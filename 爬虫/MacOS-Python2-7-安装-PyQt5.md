最近项目需要安装PyQt5。因为依赖许多的系统库，像Sip、Qt等，最新想到的是用Homebrew安装
```
brew install pyqt5
```
发现Homebrew会先安装python3，然后安装PyQt5和它依赖的库。

python3的话还可以用pip3安装。但是需要先用Homebrew安装Sip、Qt
我平时是一般用

python configure.py --sip-incdir=/usr/local/include/
