


### 安装brew

```bash
#install
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
#uninstall
sudo ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
```
可能遇到问题，如
>Error:
  homebrew-core is a shallow clone.
  homebrew-cask is a shallow clone.
To `brew update`, first run:
  git -C /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core fetch --unshallow
  git -C /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask fetch --unshallow

根据提示操作即可
```
git -C /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core fetch --unshallow
git -C /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask fetch --unshallow
```

另，可能会有权限的问题
```bash
sudo chown -R `whoami` /usr/local/Homebrew/
sudo chown -R $(whoami) $(brew --prefix)/*
sudo mkdir /usr/local/Frameworks
sudo chown -R `whoami` /usr/local/Frameworks/
```
