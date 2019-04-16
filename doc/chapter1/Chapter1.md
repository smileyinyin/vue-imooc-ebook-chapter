# Chapter 1 - 运行完整的源代码

本章的目标是帮助大家在本地搭建开发环境，并运行完整代码

## 1. 安装Node.js、npm和Vue CLI 3.0
### 1.1 nvm安装
nvm是Node.js版本管理工具，可以非常方便的下载和切换Node.js版本，分为两个版本：
- macOS和Linux版本点击[这里](https://github.com/creationix/nvm)
- Windows版本点击[这里](https://github.com/coreybutler/nvm-windows)

macOS和Linux版本指令方法如下：
```shell
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```
或
```shell
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```
安装时必须在当前用户根路径下存在`.bash_profile`文件（该文件的用途是在当用户登录后，加载相应的环境变量），如果不存在该文件，可以手动创建一个，再重新执行上述安装脚本
```shell
touch ~/.bash_profile
```
安装成功后会在`.bash_profile`文件中写入以下内容，以便我们可以直接使用nvm指令
```shell
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This l    oads nvm bash_completion
```
我们可以重新打开终端令环境变量生效，或使用如下指令令环境变量立即生效
```shell
source ~/.bash_profile
```
nvm卸载方法很简单，只需要执行如下指令就可以了。我们在`.bash_profile`中定义了环境变量`$NVM_DIR`为`$HOME/.nvm`，`$HOME`指向用户根目录，`$HOME/.nvm`表示nvm的安装路径，所以直接删除即可
```shell
$ rm -rf "$NVM_DIR"
```
执行上述指令后，还要将`.bash_profile`文件中安装nvm时写入的内容删除即可

### 1.2 Node.js和npm安装
安装nvm后我们就可以很方便的管理Node.js版本，安装最新的Node.js版本（安装Node.js的同时会自动安装npm）：
```shell
nvm install node
```
安装指定版本Node.js（如安装10.10.0版本）：
```shell
nvm install 10.10.0
```
查看本地已经安装的Node.js版本
```shell
nvm ls
```
查看所有可用的Node.js版本
```shell
nvm ls-remote
```
切换到最新的Node.js版本
```shell
nvm use node
```
切换到指定版本的Node.js（如切换到10.10.0版本）
```shell
nvm use 10.10.0
```
验证Node.js安装是否成功
```shell
$ node -v

v10.10.0
```
验证npm安装是否成功
```shell
$ npm -v

6.4.1
```

### 1.3 安装Vue CLI 3.0环境
Vue CLI 3.0官网地址点击[这里](https://cli.vuejs.org)，注意安装Vue CLI 3.0必须安装Node.js 8.9.或更高版本，通过以下指令安装：
```shell
npm install -g @vue/cli
```
检查Vue CLI 3.0是否安装成功
```shell
$ vue --version

3.0.5
```

## 2. 下载源码
通过git下载完整源码
```shell
git clone https://git.imooc.com/coding-285/vue-imooc-ebook.git
``` 
下载后进入源码目录，安装依赖包
```shell
cd vue-imooc-ebook
npm install
```
 
## 3. 搭建静态资源服务器
### 3.1 安装Nginx
- Windows版Nginx下载地址点击[这里](http://nginx.org/en/download.html)
- macOS版Nginx需要通过brew进行安装
```shell
brew install nginx
```
### 3.2 Nginx配置（以macOS为例）
查看nginx配置文件
```shell
vim /usr/local/etc/nginx/nginx.conf
```
- 修改user，将user修改为当前登录用户名，如：当前登录用户为sam，则修改如下：
```
user  sam owner;
```
- 在http对象下再增加一个server
```
http {
  server {
    listen  80;
    server_name  localhost;
    root  /Users/sam/upload;
    autoindex  on;
    location / {
      add_header  Access-Control-Allow-Origin *;
      index  index.html index.htm;
    }
  }
}
```
这里配置项的具体含义如下：
- listen：监听端口号，最新的macOS系统不允许设置80端口，可以选择8000以上的端口号
- server_name：指定虚拟主机的名称
- root：资源文件的根路径，必须指向一个文件夹，该路径为资源文件存放的位置，例如用户访问：`localhost/book/a.epub`，实际访问的资源路径为：`/Users/sam/upload/book/a.epub`
- autoindex：是否打开目录浏览功能，如果打开，当我们访问`localhost/`时，就可以在网页上查看`/Users/sam/upload`目录下的所有文件
- location：路由规则匹配，`location /`表示匹配所有路由
    - add_header：表示在响应头中添加指定内容，这里添加`Access-Control-Allow-Origin`为`*`是为了解决前端跨域问题，如果不配置这个选项，前端请求资源时会出现跨域错误，希望详细了解跨域原理的同学可以点击[这里](http://www.ruanyifeng.com/blog/2016/04/cors.html)学习
    - index：指定访问根路径时默认访问的资源文件，比如我们访问`localhost/`，实际Nginx会找到`/Users/sam/upload/index.html`或`/Users/sam/upload/index.htm`进行返回

### 3.3 下载资源包
- 通过百度网盘下载资源包，点击[这里](https://pan.baidu.com/s/1x2N7vl8nd2x6x7FnlQH3Cg)进行下载，提取码: ksjv，下载"慕课网-实战微信读书-资源文件.zip"
- 下载成功后进行解压
- 解压后打开文件夹，将其中的epub和book 2个文件夹拷贝到`/Users/sam/upload`（即Nginx配置文件中root指定的文件夹）路径下
    - epub中包含了电子书文件
    - book中包含了资源文件，如封面、字体、主题样式等
    
## 4. 运行源码
### 4.1 配置环境变量
进入源码目录，打开`.env.development`
```shell
cd vue-imooc-ebook
vim .env.development
```

`.env.development`配置文件内容如下：
```shell
VUE_APP_EPUB_URL=http://47.99.166.157/epub
VUE_APP_EPUB_OPF_URL=http://47.99.166.157/epub2
VUE_APP_RES_URL=http://47.99.166.157/book/res
VUE_APP_BASE_URL=http://47.99.166.157:3000
VUE_APP_VOICE_URL=http://47.99.166.157:3000
VUE_APP_BOOK_URL=http://47.99.166.157:3000
```
我们将静态资源文件路径替换为本地Nginx路径，注意要使用IP地址，不要使用localhost，否则只能在本机访问，而无法通过手机扫码无法访问，如：Nginx服务器的地址为：http://192.168.31.243，则需要将`.env.development`配置文件改为：
```shell
VUE_APP_EPUB_URL=http://192.168.31.243/epub
VUE_APP_EPUB_OPF_URL=http://192.168.31.243/epub2
VUE_APP_RES_URL=http://192.168.31.243/book/res
VUE_APP_BASE_URL=http://47.99.166.157:3000
VUE_APP_VOICE_URL=http://47.99.166.157:3000
VUE_APP_BOOK_URL=http://47.99.166.157:3000
```
这里另外三个地址`VUE_APP_BASE_URL`、`VUE_APP_VOICE_URL`、`VUE_APP_BOOK_URL`指向演示系统的API，这里可以暂时不做修改，我们会在项目发布一章中为大家详细讲解如何开发一个简单的电子书后台，为前端提供API，实现前后端分离

### 4.2 运行项目
通过如下指令运行项目
```
cnpm run dev
```
运行成功后提示如下：
```
 DONE  Compiled successfully in 3950ms                                      22:25:51

 
  App running at:
  - Local:   http://localhost:8080/ 
  - Network: http://192.168.31.243:8080/

  Note that the development build is not optimized.
  To create a production build, run npm run build.
```
此时我们就可以通过`http://192.168.31.243:8080`访问站点了
