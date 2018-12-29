## pacman
- pacman Si
可以浏览已装软件的可选依赖
- pacman -Syu 
安装软件可以检查并升级系统，若没有u，可能某软件包不再出现在官方库，可能有依赖问题
- `[使用正则表达式安装](https://wiki.archlinux.org/index.php/Pacman/Tips_and_tricks_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E5.B7.A7.E7.94.A8_Bash_.E8.AF.AD.E6.B3.95)`

- pacman -S extra/package-name
可能一个软件包在不同软件仓库中有不同版本
- pacman -S plasma-{desktop,nm}
安装名称相似的包
- pacman -S gnome
安装包组，其中有大量的包，会提示标号。可以使用`1-10 15`表示1到10以及15的包。`^5-8 ^2`表示除了5-8以及2之外的包
- pacman -Sg gnome
查看哪些包属于gnome组
- 删除软件包
  1. pacman -R package_name 仅删除单个软件包，保留其依赖
  2. pacman -Rs package_name 删除软件包以及其所有未被其他已安装软件包使用的依赖
  3. pacman -Rsc package_name 删除软件包以及所有依赖该软件包的程序
  4. pacman -Rdd package_name 删除软件包，但不删除依赖该软件包的其他程序
  5. pacman -Rn package_name 正常pacman在删除某些程序时会被备份重要配置文件，在其后面加上\*.pacsave扩展名 -n可以避免备份这些文件
  6. 注意pacman不会删除软件自己创建的文件

## iptables


## 安装fcitx中文输入法
pacman -S fcitx fcitx-googlepinyin fcitx-configtool fcitx-gtk2 fcitx-gtk3 fcitx-qt4
启动fcitx-configtool添加中文输入法

在/etc/environment中加入:
```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"

```

## vim-fcitx安装  (使用PKGBUILD)
pacman -S core/binutils    //为了使用makepkg
git clone https://aur.archlinux.org/vim-fcitx.git
cd vim-fcitx
makepkg
sudo pacman -U vim-fcitx-1.2.5-1-any.pkg.tar.xz

## pacman 出现密钥错误的一种不推荐解决办法
将/etc/pacman.conf中Siglever = 后面全都改成Never  即让它不检查

## shadowsocks
pacman -S shadowsocks
在/etc/shadowsocks/下配置config.json文件
ss-local -c /etc/shadowsocks/config.json 以该文件为配置开启shadowsock

systemctl start shadowsocks@config    (config为/etc/shadowsocks/config.json)

google-chrome-unstable --proxy-server=socks5://127.0.0.1:1080

## tim
sudo vim /etc/pacman.conf
去掉multilib注释
sudo pacman -Syu
sudo pacman -S deepin-wine-tim

## 开启vpn-pptp
sudo pacman -S networkmanager-pptp pptpclient networkmanager
sudo pacman -S networkmanager-openvpn
systemctl restart NetworkManager

## 安装百度云 tim wechat
yaourt bcloud
pacman -S deepin-wine-tim
yaourt wechat

## cmake使用指定gcc
在cmake之前, 使用以下指令指定gcc版本
``` 
export CC=/usr/bin/gcc-7
export CXX=/usr/bin/g++-7
```
