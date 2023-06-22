# 【实践记录】服务器安装 oh-my-zsh

文章编写于 2022年04月17日

```shell
touch ~/.bash_aliases
sudo apt install -y zsh
git clone https://ghproxy.com/https://github.com/ohmyzsh/ohmyzsh.git ~/.oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
sed -i 's/ZSH_THEME="robbyrussell"/#ZSH_THEME="robbyrussell"\nZSH_THEME="kafeitu"/' ~/.zshrc
printf "\nemulate sh -c 'source ~/.bash_aliases'\n" | tee -a ~/.zprofile
printf "\nemulate sh -c 'source ~/.profile'\n" | tee -a ~/.zprofile
chown -R $(whoami):$(whoami) ~/.oh-my-zsh
chown $(whoami):$(whoami) ~/.zshrc
chown $(whoami):$(whoami) ~/.zprofile
which zsh && chsh -s $(which zsh) $(whoami)
```