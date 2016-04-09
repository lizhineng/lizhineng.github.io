---
layout: post
title: 在万网虚拟主机部署 Laravel 应用
tags: php, laravel
---

今天给客户部署 Laravel 的应用时，对方提供的是万网的虚拟主机，我们遇到虚拟主机应该如何部署呢？这里有几个地方要注意的。

## 一、设置 PHP 版本号

（撰稿时）由于 Laravel 最低的 PHP 版本支持是 PHP 5.5.9，万网默认的是 PHP 5.3，因此我们需要进去后台控制面板中的 *站点信息 > 高级环境设置 > PHP版本设置* 中修改版本号为 **PHP 5.5**。

![万网虚拟主机后台设置PHP版本](/imgs/deploying-laravel-application-on-wanwang/dlaow-001.png)

其次按照它的步骤重设一下 MySQL 数据库密码即可。

## 二、解决访问路径问题

部署的时候，如果我们直接把整个项目上传上去，程序的主页访问地址就变为 `http://www.yourdomain.com/public`，意味着我们的访问主页时要多加一个 `/public` 作为根目录，这显然不美观，这里有两个方法解决。

## 方法一：使用 .htaccess

我们在 **web 根目录**下创建一个名为 `.htaccess` 的文件，用于对访问目录进行重定位到 `./public` 目录，文件内容如下：

{% highlight htaccess %}
<IfModule mod_rewrite.c>
    <IfModule mod_negotiation.c>
        Options -MultiViews
    </IfModule>

    RewriteEngine On

    # Redirect Trailing Slashes If Not A Folder...
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^(.*)/$ /public/$1 [L,R=301]

    # Handle Front Controller...
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]
</IfModule>
{% endhighlight %}

## 方法二：改变程序的目录结构

另外一个方法是我目前使用的方法，当然选择哪个是按照自己喜好了，我们在这里看一下万网虚拟主机的目录结构。

    |- /
    |-- /htdocs
    |-- /myfolder
    |-- ...

我打算把项目核心的文件隐藏于外部访问地址外，因此，我的方法是在 `/myfolder` 处新增一个名为  `myapp` 的目录，这个名字可以随意更改，当然你也可以直接放这里。然后把项目的文件中除了 `/public` 文件夹外的所有文件，全部复制进去 `/myfolder/myapp` 中去，最后把原本 `/htdocs/public` 下的文件移动出来 web 根目录 `/htdocs` 即可，移动出来后把 `public` 目录删除。

目录就会变为：

    |- /
    |-- /htdocs
    |---- /index.php
    |---- /robots.txt
    |---- / ...           { 这里就是 public 目录下的文件}
    |-- /myfolder
    |---- /myapp
    |-------- /app
    |-------- /config
    |-------- / ...       { 这里就是 Laravel 的程序文件 }
    |-- ...

目录调整完毕后，我们还需要修改一下 web 根目录下的 `index.php` 文件（原本的 `/public/index.php` 现在的 `/htdocs/index.php`）

打开文件后找到

    require __DIR__.'/../bootstrap/autoload.php';

    $app = require_once __DIR__.'/../bootstrap/app.php';

把它们分别修改为

    require __DIR__.'/../myfolder/myapp/bootstrap/autoload.php';

    $app = require_once __DIR__.'/../myfolder/myapp/bootstrap/app.php';

如果自行改动了目录结构，则自行按照实际情况修改。其作用是告诉程序入口文件 Laravel 的核心文件在哪里。

## 三、解决万网虚拟主机不支持 AES-256-CBC 加密的问题

（撰稿时）遗憾的是，这里并没有什么很好的解决办法，我们唯有把它进行降级，使用 128 位的 AES-128-CBC 算法。

首先打开项目文件 `/config/app.php`，找到

    'cipher' => 'AES-256-CBC',

将其改为

    'cipher' => 'AES-128-CBC',

即可，但是别忘了由于加密的算法变更了，他所对应的密钥也需要更改，可以在控制台中生成一下

`php artisan key:generate`

然后把它放回 `key` 中，例如 `'key' => env('APP_KEY', 'SomeRandomString'),`

把 `SomeRandomString` 替换为刚才生成的密钥即可。

解决了这三个问题就可以在万网的虚拟主机中正常使用了，其实过程也蛮简单的。
