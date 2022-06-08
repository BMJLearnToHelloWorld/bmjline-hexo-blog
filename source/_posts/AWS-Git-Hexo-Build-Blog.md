---
title: AWS+Git+Hexo搭建博客
description: 个人认为，这篇才能算得上是真正意义上的第一篇博客吧！XD 写完了这篇博客，也算是又回顾了一遍自己搭建博客的过程，也对Markdown**有了一定的了解。原来写一篇博客会这么累，要这么久。。。XD
date: 2018-11-12 23:31:10
tags: 
- intro
- aws
- git
- hexo
categories: 
- technology
---

## 概要

### 要素

[AWS：Amazon Web Services 亚马逊云](https://aws.amazon.com)

[Git：版本控制系统](https://git-scm.com/)

[Hexo：支持Markdown的博客框架](https://hexo.io/)

### 实现流程

首先，需要在**AWS**上申请一台服务器（EC2），在服务器上搭建**Git仓库**，创建**Hook**（钩子）并配置将**Git仓库**里的内容部署到制定路径下。

然后，在服务器上安装**[Nginx](http://nginx.org/)**（HTTP和反向代理服务器），通过配置监听**80端口**，将访问路径转发到指定目录下（Hook所部署配置的路径）。

接着，在本地用**[node](https://nodejs.org/en/)**的**npm**（包管理工具）命令安装**Hexo**，并配置其发布方式（**Git**）及发布路径（**AWS**）。

最后，在本地完成博客编写，通过**hexo deploy**命令部署到**AWS**上。

<br/>

----------

## 搭建

### 服务端

#### 创建连接服务器

如果你是第一次使用AWS服务器，只需注册AWS账号，并绑定**支持外币的信用卡**，就可以获得最普通的服务器（单核，1G内存，8G容量）一年的免费使用权（AWS会冻结你信用卡里的1美元一个月，之后会自动解除冻结）。（怎么搞的自己像在做广告一样。。。）

我选择的是**Ubuntu16.04**的**EC2**，创建了一个**Instance**，在创建时你将会创建并获得一个**.pem私钥**，只需用**[PuTTY](https://putty.org/)**将私钥**.pem文件**生成为**.ppk文件**之后，用**SSH**方式，通过服务器的**公网IP（Public IP）**和之前生成的**.ppk私钥**就可以连上自己的服务器了**（用户名默认为ubuntu）**。

为了安全起见，你可以使用 **`sudo adduser 用户名`** 来创建一个**新用户**，这样的话，当你赋予此用户某些权限，他将可以用于**Git**上传部署等，就不需要使用权限更大的**ubuntu**或者**root**了。一会儿也会详细再说到。

----------

#### 创建用户
<pre>
sudo adduser git
</pre>

这样就会在 **`/home/`** 的目录下生成一个以**git**命名的文件夹。（创建过程中，会让你输入名称密码等，全部直接按回车即可。）

----------

#### 创建Git仓库

首先**安装Git**：
<pre>
sudo apt-get update
sudo apt-get install git
</pre>

然后**配置Git**用户名和账号：
<pre>
git config --global user.name "yourname"
git config --global user.email "youremail"
</pre>

再**更新Git**到最新稳定版：
<pre>
sudo add-apt-repository ppa:git-core/ppa
sudo apt-get update
sudo apt-get install git
</pre>

完成之后可以通过 **`git --version`** 来**查看Git**当前安装的版本。

接下来就可以通过 **`git`** 命令来创建**git仓库**和**钩子Hook**了。

----------

选择服务器的一个路径来作为**git仓库Repository**，存放通过git上传部署的发布内容，可以根据自己的喜好来建立**repo**，建完之后修改文件夹的**读写权限**（**775**的话，其他的用户是没有写的权限的）：
<pre>
cd /usr/local/
sudo mkdir hexo/repo/
chmod 775 -R /usr/local/hexo/repo/
cd /usr/local/hexo/repo
sudo git init --bare blog.git
</pre>

这样就生成了一个**空的仓库**，上传的文件就会存放在里面。

----------

现在，我们需要在仓库中配置**钩子Hook**，其作用就是每一次在仓库接收到文件的时候，就会将文件部署更新到规定的路径下，这样我们就不回去访问或者修改repo中的文件了，而是访问部署的那个路径下的文件内容：
<pre>
cd /usr/local/hexo/repo/blog.git/hooks
vi post-receive
</pre>

然后在这个文件中输入以下内容：
<pre>
#!/bin/sh
git --work-tree=/usr/local/hexo/bmjline --git-dir=/var/local/hexo/repo/blog.git checkout -f
</pre>

其中 **`/usr/local/hexo/bmjline`** 是部署的路径，也就是正真的外部去访问的路径。

貌似这个路径下还没有建这个文件夹，现在我们就去建一个：
<pre>
cd /usr/local/hexo/
sudo mkdir bmjline
</pre>

----------

#### 安装Nginx

<pre>
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:nginx/stable 
sudo apt-get update
sudo apt-get install nginx
</pre>

按照别人写的方式，这样就能安装最新稳定版的**Nginx**了。

接下来就是配置**nginx**的**config**文件了，在我自己配置的时候发现，Linux下的Nginx目录结构和**.conf**文件内容和Windows上的有些区别。

当我打开 **`/etc/nginx`** 目录下的**nginx.conf**文件时，里面并没有可以配置**server**的地方，后来研究了一下发现，配置**server**的地方在 **`/etc/nginx/sites-enabled`** 目录下的**default**文件。

于是我就修改了该文件的一些内容：
<pre>
vi /etc/nginx/sites-enabled/default
</pre>

将里面的server部分做如下修改：
<pre>
server {
	listen 80 default_server;
	listen [::]:80 default_server;

	root /usr/local/hexo/bmjline;
	index index.html index.htm index.nginx-debian.html;

	server_name www.bmjline.com;
}
</pre>

解释一下这些内容， **`listen 80 default_server;`** 和 **`listen [::]:80 default_server;`** 就是让**nginx**监听服务器的**80端口**，当外部有请求时，nginx会将**root**目录，也就是 **`/usr/local/hexo/bmjline`** 返回，返回的内容（**index**）指定的是该目录下的 **index.html** 或者 **index.htm** 或者 **index.nginx-debian.html** 。最后，我的服务器的名称是**www.bmjline.com**。

----------

#### 修改用户权限

我想只让某个用户有操作blog的权限，这样做感觉会更加安全，那么我们就用到了之前创建好的**git用户**。

首先将之前为了blog所创建的文件夹和git仓库的拥有者做一下修改（目前因为是用sudo创建的，所有拥有者为root）：
<pre>
cd /usr/local/hexo/repo/
sudo chown -R git:git blog.git
cd /usr/local/hexo/
sudo chown -R git:git repo
sudo chown -R git:git bmjline
cd /usr/local/
sudo chown -R git:git hexo
</pre>

创建git这个用户的目的在于能够使用它来进行git和部署，但是我们不希望它能够像ubuntu那样通过**SSH**登录操作。那么我们组要进行以下修改：
<pre>
vi /etc/passwd
</pre>

我们找到**git**用户，一般会在最后一行：
<pre>
git:x:1001:1001:,,,:/home/git:/bin/bash
</pre>

将其修改为：
<pre>
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
</pre>

----------

#### 生成 & 验证keys

*这一步可以放在***搭建完本地环境后，发布之前***进行操作。*

在**Windows**下用**cmd**运行：
<pre>
ssh-keygen
</pre>

Windows会问你是否使用默认路径，直接回车即可。输入邮箱之后完成。当生成完**key**之后你可以按照**cmd**上显示的路径寻找到默认生成路径。一般默认路径为 **`C:\Users\用户名\.ssh\`** 打开复制目录下文件 **`id_rsa.pub`** 的内容。

来到服务器端：
<pre>
cd /home/git/
sudo mkdir .ssh/
vi /home/bmj/.ssh/authorized_keys
</pre>

将刚才复制的**公钥**黏贴进去。保存退出。

修改一下 **`.ssh`** 和 **`authorized_keys`** 文件的权限：
<pre>
cd /home/git/
chmod 700 .ssh
chmod 600 .ssh/authorized_keys
</pre>

----------

在**Windows**本地环境，打开**cmd**测试是否可以通过验证：
<pre>
ssh -T git@www.bmjline.com
</pre>

当没有报错，并且最后显示**closed**时，完成！

----------

### 本地环境

#### 安装Node.js

本地环境我用的操作系统是**Windows**

[Node.js Download](https://nodejs.org/en/download/)

安装完成后，可以到**安装路径根目录下**输入：
<pre>
node -v
</pre>

当显示版本号时，说明安装成功，接下来就可以使用 **`npm`** 命令来安装其他的内容了。

----------

#### 安装[Hexo](https://hexo.io/)

首先需要**全局安装Hexo**，直接**cmd**运行：
<pre>
npm install -g hexo-cli
</pre>

在自己的workspace目录里新建一个文件夹，我们的Hexo框架就安装在这个文件夹里。

来到这个文件夹的**根目录**下，运行：
<pre>
hexo init
</pre>

安装完成之后，还需要在**根目录下**安装**两个插件**，分别用来做**git部署**和**起本地服务**的：
<pre>
npm install hexo-deployer-git --save
npm install hexo-server --save
</pre>

----------

#### 创建博客运行本地环境

创建自己的**第一篇博客**：
<pre>
hexo new "My First Blog"
</pre>

当语句执行成功，便会在 **`source/_post/`** 路径下生成 **`My-First-Blog.md`** 的文件，你可以打开它来**编辑**你的第一篇博客。

当需要**起本地环境**时，直接运行以下**三条命令**，便可以同时达到**清缓存，编译，运行**三个操作：
<pre>
hexo clean
hexo generator
hexo server
</pre>

打开浏览器访问 **[http://localhost:4000](http://localhost:4000)**

----------

#### Hexo配置

打开**blog根目录**下的 **`_config.yml`** 进行修改添加：
<pre>
deploy:
  type: git
  repo: git@www.bmjline.com:/usr/local/hexo/repo/blog.git
  branch: master
</pre>

里面有很多的**配置**可以根据自己的实际情况进行**修改和添加**，现在为了发布我们只修改上面这块内容。

应该很清楚能看明白，我们需要通过**git**将博客**push**到服务器的**git仓库**中，用户为**git用户**。

<br/>

----------

## 发布

*在这之前别忘了生成公钥并验证哦！*

到目前为止，**服务端**和**本地环境**都已经搭建完成，可以准备发布了。

**本地环境**博客**根目录**打开**cmd**运行：
<pre>
hexo deploy
</pre>

~~全绿就对了！~~

现在你可以通过**服务器的公网IP地址**访问看看，看到你的博客了吗？XD

<br/>

----------

## 总结

个人认为，这篇才能算得上是**真正意义上的第一篇博客**吧！XD

写完了这篇博客，也算是又回顾了一遍自己搭建博客的过程，也对**Markdown**有了一定的了解。原来写一篇博客会这么累，要这么久。。。XD

这里只是讲到了博客的搭建，还有更多定制化的内容，比如修改主题等。你大可以访问**[Hexo的官网](https://hexo.io/)**进行**探索**。

你也可以购买**自己的域名**，像我这样，并用**服务器的公网IP**对域名进行**解析**，你就可以通过域名访问你的博客了！

搭建的过程我也是根据**别人的博客**一点点**摸索**才完成的，完成之后才慢慢理解一些工具的用途和意义，这也告诉了我，**实践是多么重要**，尤其是对于刚工作一年多的我来说，我很高兴自己能够写下**第一篇**不是很短的博客！

*感谢你能够看到有这个网站，有这篇博客。如果发现文中有写的不正确的地方或者难以理解的地方都没关系。多多研究，完成它的方法会有很多！*
<br/>

----------

谢谢你愿意看到最后，我们下一篇博客见。。。


![TO BE CONTINUED...](https://storage.bmjline.com/blog/tobecontinuedjojo.png)
<br/>
