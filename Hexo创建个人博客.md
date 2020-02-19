---
title: Hexo创建个人博客
tags: hexo
abbrlink: 3fb4ccf6
date: 2020-02-13 17:31:49
---

记录一下自己学习的过程。以免时间长了忘记。
先说明一下本博客的构造:
[Hexo](https://hexo.io/zh-cn/) + [GitHub Pages](https://pages.github.com/)


#### 首先下载一些必要的软件： 
- [Node.js](http://nodejs.cn/download/),这是Hexo得以运行的基础。 
- [git](https://git-scm.com/)，在后面会用到。  
- npm，用于为Hexo安装各种插件的一个类似包管理器的东西。它安装Node.js时就已经自动安装了。  

装完之后在命令行下输入`node -v`以及`npm -v`,输出如下图所示则说明安装成功：  
{% qnimg 检测是否安装成功.jpg %}  
上述两个软件安装完之后即可通过npm安装Hexo：      
`$npm install -g hexo-cli`   
具体可参见[官方文档](https://hexo.io/zh-cn/docs/)    

#### 更改一下npm镜像源
查看npm当前的配置：`$ npm config list`
更改为淘宝镜像源：`$ npm --registry=https://registry.npm.taobao.org`

#### 本地生成静态hexo博客框架
`$ hexo init test`  
test是你新创建的文件夹名称（随意命名），用来作为博客的本地编辑库用的,建在什么地方无所谓       
进入到test文件夹，执行  
`$ npm install` (或者cnpm install)来安装hexo所需的一些依赖    

#### 把本地的博客库与自己的GitHub pages关联起来 
- 首先必须下载一个工具： 
`$ cnpm install hexo-deployer-git --save`    
- 获取git密钥和公钥使得本地git仓库跟远程GitHub关联起来：  
`$ ssh-keygen -t rsa -C "example@163.com"`  
- 打开test文件夹，编辑_config.yml（这是hexo博客的整体配置），更改最底下的内容为：  
```
deploy:
	type: git
	repo: git@github.com:example/example.github.io.git
	branch: master
```
之后执行  
`$ hexo g` :把你编辑的文件通过特定的主题转换成可供浏览器识别的静态页面文件
`$ hexo d` :把上一步生成的静态页面文件推送到GitHub上，
然后你就可以在浏览器中打开网址：
https:\//example.github.io.git浏览你自己的博客了。

#### 其他比较重要的东西：
- **文章地址链接的问题**
如果博客文章标题用的是中文，则生成的链接中会含有很长一串莫名其妙的字符，
所以通过下载下面这个插件来实现短小的永久链接：
`cnpm install hexo-abbrlink --save`  

并且在test/\_config.yml中做如下修改：
`permalink: post/:abbrlink.html` 这是文章的格式，默认是:year/:month/:day/:title/，贼不好  
然后添加：
```
abbrlink:
  alg: crc32  //这个表示算法： crc16(default) and crc32
  rep: hex    //这个表示进制： dec(default) and hex
```

- **七牛云图床的使用**
在七牛云上创建了自己的空间之后，就可以上传图片、音乐、视频等内容，
会自动生成外链，但是如果每次写一篇文章都还要先打开七牛云上传图片、复制外链、添加到文章中的话，
未免太麻烦了。所以在这里下载一个插件：
`$ cnpm install hexo-qiniu-sync --save`  

然后添加如下内容到站点中的_config.yml文件： 
```
qiniu:
  offline: false
  sync: true
  bucket: linfeitang #你自己在七牛云上创建的空间名
  access_key: #你自己的七牛云上密钥管理中的access_key
  secret_key: #你自己的七牛云上密钥管理中的secret_key
  dirPrefix: tang #上传到空间的资源的前缀，建议添加，可随意命名，但一定要先在空间中设置前缀
  urlPrefix: http://q5mimm4g7.bkt.clouddn.com/tang   #上面设置了前缀之后，这里也要带上。  
  注意：q5mimm4g7要换成你自己在七牛云上的外链域名，这串符号每个人都是不一样的。
  up_host: http://upload.qiniu.com  #默认设置，无需改动
  local_dir: qiniuimages 
  #这是你在站点里面用来存放图片的文件夹，七牛云同步也是同步这里面的内容。可随意命名，没必要跟七牛云空间名相同
  update_exist: true #是否更新已经上传过的文件(仅文件大小不同或在上次上传后进行更新的才会重新上传)
  image: 
    folder: images  #这里的images，js，css三个文件夹会自动都创建在上面你自定义的qiniuimages文件夹里面
    extend: 		#也是可以随意命名的。
  js:
    folder: js
  css:
    folder: css
```
{% qnimg 添加路径前缀.jpg %}
<sub>先在空间中设置资源前缀</sub>
{% qnimg 外链域名.jpg %}
<sub>每个人都有不一样的外链域名</sub>
然后执行:
`$ hexo g`,就会在test目录下生成qiniuimages文件夹和qiniuimages下的三个子文件夹images、js、css。
今后如果需要在文章中插入图片，就可以直接把图片放在qiniuimages/images/中，
然后通过
{% qnimg example.jpg %} 
这样的快捷的方式来引用图片了。
hexo会自动到qiniuimages/images/下寻找图片  
并且把图片的地址转换成`http://q5mimm4g7.bkt.clouddn.com/tang/example.jpg`这样的形式。 
推送完博客之后，执行：
`$ hexo qiniu sync`即可将本地图片自动同步到七牛云中啦。


另外：除了图片之外，也可以把视频、音频、js、css等文件存储到七牛云中，
从而大大加速真个网页的加载速度，以及特别总要的CDN加速。
这些怎么操作，等我了解了之后再来记录。