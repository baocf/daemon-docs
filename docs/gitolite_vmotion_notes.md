useradd git
su git
git clone https://github.com/sitaramc/gitolite
mkdir -p $HOME/bin
gitolite/install -to $HOME/bin
  安装 perl-Data-Dumper.x86_64 （因为执行 gitolite/install 时的异常 Can't locate Data/Dumper.pm in @INC）
$HOME/bin/gitolite setup -pk daemon_newest_win10.pub

git clone gitolite-admin

commit key & conf files.

cp all repos dir  new path

$HOME/bin/gitolite setup -pk daemon_newest_win10.pub