# docker ubuntu

try to use zsh inside a docker ubuntu on windows, because I don't like cmd/powershell on windows

## start

```sh
docker run -itd -v D:/ubuntu-home:/root/share --name=elegant-ubuntu -p 8000:8000 -p 8001:8001 -p 8002:8002 ubuntu
```

## first enter ubuntu in container

```sh
docker exec -it elegant-ubuntu bash
```

## install zsh

```bash
apt update
apt install zsh
```
## install oh-my-zsh

```zsh
apt install curl
apt install git
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## Config oh-my-zsh

### theme

```zsh
apt install vim
vim .zshrc
```

```zsh
ZSH_THEME=agnoster
```

### Fix encoding issue

```zsh
vim .zshrc
```
```zsh
export LC_ALL=C.UTF-8
export LANG=C.UTF-8
```
```zsh
apt-get install fonts-powerline
```
```zsh
mkdir -p ~/.config/fontconfig/
cd ~/.config/fontconfig/
vim conf.d
```
```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
  <selectfont>
    <acceptfont>
      <pattern>
        <patelt name="family"><string>terminess powerline</string></patelt>
      </pattern>
    </acceptfont>
  </selectfont>
</fontconfig>
```
```zsh
fc-cache -vf
```
