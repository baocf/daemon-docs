# 深入理解 Laravel 中.env 文件读取

如何读取？如何保存？这是重点

## 如何读取

> 逐行正则匹配，一次不能多行。且只能定义 `bool`/`string` 类型。

`getenv`:
laravel 的 env 是通过该函数获取 env 环境变量
那么存储呢？使用了 Dotenv 包
加载如下：Dotenv\Loader 类下：

```php
/**
     * Load `.env` file in given directory.
     *
     * @return array
     */
    public function load()
    {
        $this->ensureFileIsReadable();

        $filePath = $this->filePath;
        $lines = $this->readLinesFromFile($filePath);
        foreach ($lines as $line) {
            if (!$this->isComment($line) && $this->looksLikeSetter($line)) {
                $this->setEnvironmentVariable($line);
            }
        }

        return $lines;
    }
```

说明加载 env 文件效率比较低。

顺着 `getEnvironmentVariable` 往下：

```php
public function setEnvironmentVariable($name, $value = null)
    {
        // 这里就是解析了
        list($name, $value) = $this->normaliseEnvironmentVariable($name, $value); 

        // Don't overwrite existing environment variables if we're immutable
        // Ruby's dotenv does this with `ENV[key] ||= value`.
        if ($this->immutable && $this->getEnvironmentVariable($name) !== null) {
            return;
        }

        // If PHP is running as an Apache module and an existing
        // Apache environment variable exists, overwrite it
        if (function_exists('apache_getenv') && function_exists('apache_setenv') && apache_getenv($name)) {
            apache_setenv($name, $value);
        }

        if (function_exists('putenv')) {
            putenv("$name=$value");
        }

        $_ENV[$name] = $value;
        $_SERVER[$name] = $value;
    }
```

上面的代码会注意到：`apache_getenv` 和 `apache_setenv` 和 `putenv`
对于 `apche_getenv` : [http://php.net/manual/zh/function.apache-s...](http://php.net/manual/zh/function.apache-setenv.php)

`putenv`:
添加 setting 到服务器环境变量。 环境变量仅存活于当前请求期间。 在请求结束时环境会恢复到初始状态。

优点：1、优雅 2、稳定性
先看他的优雅写法：

```php
list($name, $value) = array_map('trim', explode('=', $name, 2));
```

对于稳定性：
上面设置了 `$_ENV`、`$_SERVER`、`putenv` 这些都是为了跨平台的稳定性，如果其中一个变量不可用，就从另一个中获取。

**解析过程**

```php
/**
      *规范化给定的环境变量。
     *
      *取得开发人员传入的值和：
      * - 确保我们处理单独的名称和值，如果需要，将名称字符串分开，引号
      * - 解析变量值，
      * - 解析带有双引号的变量名，
      * - 解析嵌套变量。
     *
      * @param string $ name
      * @param string $ value
     *
      * @return array
     */

    protected function normaliseEnvironmentVariable($name, $value)
    {
        // 就是简单的 array_map('trim', explode('=', $name, 2)); 根据等号分开并且去除两边空格
        list($name, $value) = $this->splitCompoundStringIntoParts($name, $value);
        // 解析净化变量名
        list($name, $value) = $this->sanitiseVariableName($name, $value);
        // 解析净化变量值 这个是最复杂的部分
        list($name, $value) = $this->sanitiseVariableValue($name, $value);

        $value = $this->resolveNestedVariables($value);

        return array($name, $value);
    }

    protected function sanitiseVariableName($name, $value)
    {
        // 支持 export方式定义变量，把单引号和双引号去掉
        $name = trim(str_replace(array('export ', '\'', '"'), '', $name));
        return array($name, $value);
    }

    // 
    protected function sanitiseVariableValue($name, $value)
    {
        $value = trim($value);
        if (!$value) {
            return array($name, $value);
        }

        if ($this->beginsWithAQuote($value)) { // value starts with a quote
            $quote = $value[0];
            // 支持引号的方式定义环境变量
            $regexPattern = sprintf(
                '/^
                %1$s          # match a quote at the start of the value
                (             # capturing sub-pattern used
                 (?:          # we do not need to capture this
                  [^%1$s\\\\] # any character other than a quote or backslash
                  |\\\\\\\\   # or two backslashes together
                  |\\\\%1$s   # or an escaped quote e.g \"
                 )*           # as many characters that match the previous rules
                )             # end of the capturing sub-pattern
                %1$s          # and the closing quote
                .*$           # and discard any string after the closing quote
                /mx',
                $quote
            );
            $value = preg_replace($regexPattern, '$1', $value);
            $value = str_replace("\\$quote", $quote, $value);
            $value = str_replace('\\\\', '\\', $value);
        } else {
            // 没有引号 每一行的都可能有#注释需要去掉
            $parts = explode(' #', $value, 2); #类似这样得注释
            $value = trim($parts[0]); // 区井号#前面的部分作为值。这里就需要注意了

            // Unquoted values cannot contain whitespace 非引号包含的值，不能包含空格
            if (preg_match('/\s+/', $value) > 0) {
                throw new InvalidFileException('Dotenv values containing spaces must be surrounded by quotes.');
            }
        }

        return array($name, trim($value));
    }

    // 是否包含单引号或者双引号
    protected function beginsWithAQuote($value)
    {
        return strpbrk($value[0], '"\'') !== false;
    }
```



## 问题 1：

使用 `php artisan config:cache` 之后 `env` 函数不再有用，就是从 `getenv` 无法获取数据？原因？

因为 `cache` 了之后，`laravel` 就会将所有 `config` 生成缓存文件，并且判断如果缓存文件生成，就不会加载`.env` 文件，因此 env 函数就不会获取导数据啦，那么是哪里判断了？
`\Illuminate\Foundation\Bootstrap\LoadEnvironmentVariables` 类在作怪！！

```php
public function bootstrap(Application $app)
    {
        // 是否缓存config目录的配置了，缓存了就不会去加载.env
        if ($app->configurationIsCached()) {
            return;
        }
        // 检查env应该加载哪一个文件
        $this->checkForSpecificEnvironmentFile($app);

        try {
            // 这个时候才加载 .env
            (new Dotenv($app->environmentPath(), $app->environmentFile()))->load();
        } catch (InvalidPathException $e) {
            //
        }
    }
```



## 问题 2：

这个加载环境变量是什么时候加载的？
看 `Kernel` 内核代码，`laravel` 的内核分为 `console` 和 `http` 两种。我们当然看 `http` 的。
`\Illuminate\Foundation\Http\Kernel` 类

```php
protected $bootstrappers = [
        \Illuminate\Foundation\Bootstrap\LoadEnvironmentVariables::class,
        \Illuminate\Foundation\Bootstrap\LoadConfiguration::class,
        \Illuminate\Foundation\Bootstrap\HandleExceptions::class,
        \Illuminate\Foundation\Bootstrap\RegisterFacades::class,
        \Illuminate\Foundation\Bootstrap\RegisterProviders::class,
        \Illuminate\Foundation\Bootstrap\BootProviders::class,
    ];
```

可以看到，加载 env 变量是启动的第一步，然后就是加载 config 目录的文件。这些类都有一个叫 bootstrap 的方法。



## 总结：

1、`.env` 文件的变量有注释功能
2、保存在请求期间的环境变量中
3、**重点**: 在 `config:cache` 之后不会加载 `env`，因此不能再 `config` 目录以外的文件使用 `env` 函数，否则将会导致程序以超乎想象的方式运行。我相信你的程序多少会出现这个问题。
4、非引号包含的值，不能包含空格
5、配置值会去除井号`#`后面部分，保留前面部分，这就可能有问题了。比如：

```php
# 数据库密码包含井号，那咋办？
# 错误，因为会认为井号#后面的是注释
db_password=12345#%snq
#相当于：
db_password=12345

# 正确 使用引号
db_password="12345#%snq"
```