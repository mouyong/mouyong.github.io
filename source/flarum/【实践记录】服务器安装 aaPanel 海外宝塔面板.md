# 【实践记录】服务器安装 aaPanel 海外宝塔面板

文章编写于 2022年04月17日

```shell
# 更新 ubuntu apt 软件源
sudo apt update -y
# 切换到 root 用户
sudo su
# 安装宝塔 aaPanel
wget -O install.sh http://www.aapanel.com/script/install-ubuntu_6.0_en.sh && sudo bash install.sh
# 清空面板入口
sed -i "s/\/.*/\//" /www/server/panel/data/admin_path.pl
# 停用宝塔
bt stop

# 启用宝塔，并查看宝塔的登录信息
bt restart && bt default

# 需要安装的环境
nginx php80 php72 mysql redis ftp phpmyadmin
# 需要安装的软件
Linux Tools|Log cleanup|Supervisor|Fail2ban Manager|Nginx free firewall|Docker Manager

# 首页显示的软件
nginx|php80|mysql|redis|Log cleanup|Supervisor|Nginx free firewall|Docker Manager

# 设置 ACL 权限，授予 www 用户对 /www/wwwroot 目录的相关权限
sudo apt install acl
sudo setfacl -R -m g:www:r-x -m u:www:rwx /www/wwwroot
sudo setfacl -Rd -m g:www:r-x -m u:www:rwx /www/wwwroot

# 增加环境变量，允许 root 用户执行 composer
export COMPOSER_ALLOW_SUPERUSER=1

# 云厂商安全组放行以下端口
7800|888|80|443|20|21|22|23|8080|3306|6379

```