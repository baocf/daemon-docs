

1.linux 访问 https 证书问题

```shell
[root@boss-test-dev001-jydx ~]# curl -v https://mobile.mycard520.com.tw

- About to connect() to mobile.mycard520.com.tw port 443 (#0)
- Trying 220.130.127.122... connected
- Connected to mobile.mycard520.com.tw (220.130.127.122) port 443 (#0)
- Initializing NSS with certpath: sql:/etc/pki/nssdb
- CAfile: /etc/pki/tls/certs/ca-bundle.crt
  CApath: none
- Certificate is signed by an untrusted issuer: 'CN=TWCA Secure SSL Certification Authority,OU=Secure SSL Sub-CA,O=TAIWAN-CA,C=TW'
- NSS error -8172
- Closing connection #0
- Peer certificate cannot be authenticated with known CA certificates
  curl: (60) Peer certificate cannot be authenticated with known CA certificates
  More details here: http://curl.haxx.se/docs/sslcerts.html

curl performs SSL certificate verification by default, using a "bundle"
 of Certificate Authority (CA) public keys (CA certs). If the default
 bundle file isn't adequate, you can specify an alternate file
 using the --cacert option.
If this HTTPS server uses a certificate signed by a CA represented in
 the bundle, the certificate verification probably failed due to a
 problem with the certificate (it might be expired, or the name might
 not match the domain name in the URL).
If you'd like to turn off curl's verification of the certificate, use
 the -k (or --insecure) option.
```



2.现有 证书twca.cer 需要添加到 linux 证书信任列表

相关证书转换参见:http://netkiller.github.io/cryptography/openssl/format.html

#转换格式 .cer 到 .pem
```openssl x509 -inform der -in twca.cer  -out twca.pem```

#追加到信任列表
```cat twca.pem >> /etc/pki/tls/certs/ca-bundle.crt```



3.添加后访问成功

```shell
[root@boss-test-dev01 certs]# curl -v https://mobile.mycard520.com.tw/billing/RECEIVE.aspx

- About to connect() to mobile.mycard520.com.tw port 443 (#0)
- Trying 220.130.127.122... connected
- Connected to mobile.mycard520.com.tw (220.130.127.122) port 443 (#0)
- Initializing NSS with certpath: sql:/etc/pki/nssdb
- CAfile: /etc/pki/tls/certs/ca-bundle.crt
  CApath: none
- SSL connection using SSL_RSA_WITH_RC4_128_MD5
- Server certificate:
- subject: CN=mobile.mycard520.com.tw,OU=IT,O=Soft-World International Corporation,L=Kaohsiung,ST=Taiwan,C=TW
- start date: Apr 29 03:21:58 2015 GMT
- expire date: Nov 12 15:59:59 2017 GMT
- common name: mobile.mycard520.com.tw
- issuer: CN=TWCA Secure SSL Certification Authority,OU=Secure SSL Sub-CA,O=TAIWAN-CA,C=TW

> GET /billing/RECEIVE.aspx HTTP/1.1
> User-Agent: curl/7.19.7 (x86_64-redhat-linux-gnu) libcurl/7.19.7 NSS/3.13.1.0 zlib/1.2.3 libidn/1.18 libssh2/1.2.2
> Host: mobile.mycard520.com.tw
> Accept: */*
>
> < HTTP/1.1 200 OK
> < Cache-Control: private
> < Content-Type: application/json;charset=utf-8
> < Date: Tue, 16 Jun 2015 02:52:04 GMT
> < Content-Length: 18
> < Set-Cookie: NSC_Npcjmf-XfcQ*80=ffffffffaf181f5345525d5f4f58455e445a4a423660;path=/;httponly
> < 

- Connection #0 to host mobile.mycard520.com.tw left intact
- Closing connection #0
  { "ResultCode":0
```



4.对于java中如果使用 HttpClient 访问https 可以使用以下命令导入到jre中的作为信任证书

```
keytool -import -keystore "/usr/local/jdk/jre/lib/security/cacerts"  -storepass changeit -keypass changeit -alias twca -file twca.cer
```

过程中提示是否信任:输入yes即可

```Trust this certificate? [no]: ```
配置过程如下:

```shell
[root@boss-test-dev01 ~]# keytool -import -keystore "/usr/local/jdk/jre/lib/security/cacerts"  -storepass changeit -keypass changeit -alias twca -file /root/twca.cer
Owner: CN=TWCA Root Certification Authority, OU=Root CA, O=TAIWAN-CA, C=TW
Issuer: CN=TWCA Root Certification Authority, OU=Root CA, O=TAIWAN-CA, C=TW
Serial number: 1
Valid from: Thu Aug 28 15:24:33 CST 2008 until: Tue Dec 31 23:59:59 CST 2030
Certificate fingerprints:
         MD5:  AA:08:8F:F6:F9:7B:B7:F2:B1:A7:1E:9B:EA:EA:BD:79
         SHA1: CF:9E:87:6D:D3:EB:FC:42:26:97:A3:B5:A3:7A:A0:76:A9:06:23:48
         Signature algorithm name: SHA1withRSA
         Version: 3

Extensions: 

#1: ObjectId: 2.5.29.15 Criticality=true
KeyUsage [
  Key_CertSign
  Crl_Sign
]

#2: ObjectId: 2.5.29.19 Criticality=true
BasicConstraints:[
  CA:true
  PathLen:2147483647
]

#3: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 6A 38 5B 26 8D DE 8B 5A   F2 4F 7A 54 83 19 18 E3  j8[&...Z.OzT....
0010: 08 35 A6 BA                                        .5..
]
]

Trust this certificate? [no]:  yes
Certificate was added to keystore
```





### 5.1 生成根密钥

```
`# cd /etc/pki/CA/# openssl genrsa -out private/cakey.pem 2048`
```

为了安全起见，修改cakey.pem私钥文件权限为600或400，也可以使用子shell生成`( umask 077; openssl genrsa -out private/cakey.pem 2048 )`，下面不再重复。



### 5.2生成根证书

使用req命令生成自签证书：

```
`# openssl req -new -x509 -key private/cakey.pem -out cacert.pem`
```

会提示输入一些内容，因为是私有的，所以可以随便输入（之前修改的openssl.cnf会在这里呈现），最好记住能与后面保持一致。上面的自签证书`cacert.pem`应该生成在`/etc/pki/CA`下。

### 5.3为我们的nginx web服务器生成ssl密钥

以上都是在CA服务器上做的操作，而且只需进行一次��现在转到nginx服务器上执行：

```
`# cd /etc/nginx/ssl# openssl genrsa -out nginx.key 2048`
```

 

这里测试的时候CA中心与要申请证书的服务器是同一个。

### 5.4  为nginx生成证书签署请求

```
`# openssl req -new -key nginx.key -out nginx.csr...Country Name (2 letter code) [AU]:CNState or Province Name (full name) [Some-State]:GDLocality Name (eg, city) []:SZOrganization Name (eg, company) [Internet Widgits Pty Ltd]:COMPANYOrganizational Unit Name (eg, section) []:IT_SECTIONCommon Name (e.g. server FQDN or YOUR name) []:your.domain.comEmail Address []:Please enter the following 'extra' attributesto be sent with your certificate requestA challenge password []:An optional company name []:...`
```

同样会提示输入一些内容，其它随便，除了`Commone Name`一定要是你要授予证书的服务器域名或主机名，challenge password不填。

### 5.5 私有CA根据请求来签署证书

接下来要把上一步生成的证书请求csr文件，发到CA服务器上，在CA上执行：

```
`# openssl ca -in nginx.csr -out nginx.crt另外在极少数情况下，上面的命令生成的证书不能识别，试试下面的命令：# openssl x509 -req -in server.csr -CA /etc/pki/CA/cacert.pem -CAkey /etc/pki/CA/private/cakey.pem -CAcreateserial -out server.crt`
```

 

上面签发过程其实默认使用了`-cert cacert.pem -keyfile cakey.pem`，这两个文件就是前两步生成的位于`/etc/pki/CA`下的根密钥和根证书。将生成的crt证书发回nginx服务器使用。

到此我们已经拥有了建立ssl安全连接所需要的所有文件，并且服务器的crt和key都位于配置的目录下，剩下的是如何使用证书的问题。





### 5.6 为linux系统添加根证书

这一步不是必须的，一般出现在开发测试环境中，而且具体的应用程序应该提供添加证书的方法。

`curl`工具可以在linux上模拟发送请求，但当它去访问https加密网站时就会提示如下信息：

```
`# curl https://sean:sean@registry.domain.com:8000/curl: (60) Peer certificate cannot be authenticated with known CA certificatesMore details here: http://curl.haxx.se/docs/sslcerts.htmlcurl performs SSL certificate verification by default, using a "bundle" of Certificate Authority (CA) public keys (CA certs). If the default bundle file isn't adequate, you can specify an alternate file using the --cacert option.If this HTTPS server uses a certificate signed by a CA represented in the bundle, the certificate verification probably failed due to a problem with the certificate (it might be expired, or the name might not match the domain name in the URL).If you'd like to turn off curl's verification of the certificate, use the -k (or --insecure) option.`
```

 

提示上面的信息说明curl在linux的证书信任集里没有找到根证书，你可以使用`curl --insecure`来不验证证书的可靠性，这只能保证数据是加密传输的但无法保证对方是我们要访问的服务。使用`curl --cacert cacert.pem`可以手动指定根证书路径。我们也可以把根证书添加到系统（CentOS 5,6）默认的bundle：

```
`# cp /etc/pki/tls/certs/ca-bundle.crt{,.bak}    备份以防出错# cat /etc/pki/CA/cacert.pem >> /etc/pki/tls/certs/ca-bundle.crt# curl https://sean:sean@registry.domain.com:8000"docker-registry server (dev) (v0.8.1)"`
```

 

### 5.7 nginx

在nginx配置文件（可能是`/etc/nginx/sites-available/default`）的server指令下添加：

```
`ssl on;ssl_certificate /etc/nginx/ssl/nginx.crt;ssl_certificate_key /etc/nginx/ssl/nginx.key;`
```

同时注意 server_name 与证书申请时的 Common Name 要相同，打开443端口。当然关于web服务器加密还有其他配置内容，如只对部分URL加密，对URL重定向实现强制https访问，请参考其他资料。

## 5.8 关于证书申请

注意，如果对于一般的应用，管理员只需生成“证书请求”（后缀大多为.csr），它包含你的名字和公钥，然后把这份请求交给诸如verisign等有CA服务公司（当然，连同几百美金），你的证书请求经验证后，CA用它的私钥签名，形成正式的证书发还给你。管理员再在web server上导入这个证书就行了。如果你不想花那笔钱，或者想了解一下原理，可以自己做CA。从ca的角度讲，你需要CA的私钥和公钥。从想要证书的服务器角度将，需要把服务器的证书请求交给CA。

如果你要自己做CA，别忘了客户端需要导入CA的证书（CA的证书是自签名的，导入它意味着你“信任”这个CA签署的证书）。而商业CA的一般不用，因为它们已经内置在你的浏览器中了。