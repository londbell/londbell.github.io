---
title: discuz@Nginx的mime type问题
date: 2020-03-07 15:44:17
tags:
    - php
    - discuz
    - nginx
    - javascript
    - mime
    - content-type
---

## 起因

最近帮人维护`discuz`站点，由于垃圾dz架构和设计实在是太奇葩，实在是令人头疼。

<!-- more -->

比如说dz的手机模板是展示`portal`的首页，这个模板的作者就用了这种方式来获取首页list信息流:

``` html
<!--{template common/header}-->
<!--{block/586}-->
<script type="text/javascript" src="http://www.xxxxxx.com/api.php?mod=js&bid=587"></script>
<!--{template common/footer}-->
```

这种弱智写法可能是为了配合dz这个弱智框架....

这段玩意的理想输出应该是：

``` javascript
document.write('<div class="bus_newslist mt20 pd10">content list.......</div>');
```

但是实际上根本不会展示，`chrome`会直接报错：

``` plain
because its MIME type ('text/html') is not executable, and strict MIME type
```

## 问题原因

原因是这个`script`的响应`Header content-type`是`text/html`，而`Header X-Content-Type-Options`的设置`nosniff`，所以浏览器拒绝了脚本的执行。

参考[对于“Refused to execute script from ” because its MIME type (‘text/html’) is not executable, and strict MIME type checking is enabled.”问题的解决办法][1]，在返回前设置一下header就好了：

``` php
header("content-type:text/javascript; charset=uft-8");
```

由于我们访问的url后缀是`api.php?mod=xxxxxx`，我不会写php，连蒙带猜确定下这文件位于根目录下，内容是：

``` php
<?php

/**
 *      [Discuz!] (C)2001-2099 Comsenz Inc.
 *      This is NOT a freeware, use is subject to license terms
 *
 *      $Id: api.php 33591 2013-07-12 06:39:49Z andyzheng $
 */

define('IN_API', true);
define('CURSCRIPT', 'api');

$modarray = array('js' => 'javascript/javascript', 'ad' => 'javascript/advertisement');

$mod = !empty($_GET['mod']) ? $_GET['mod'] : '';
if(empty($mod) || !in_array($mod, array('js', 'ad'))) {
    exit('Access Denied');
}

require_once './api/'.$modarray[$mod].'.php';

function loadcore() {
    global $_G;
xrequire_once './source/class/class_core.php';

    $discuz = C::app();
    $discuz->init_cron = false;
    $discuz->init_session = false;
    $discuz->init();
}

?>
```

大意就是根据`mod`参数来调用`/api/`下的实际php文件，在这里我们使用的文件应该是`/api/javascript/javascript.php`，于是我们增添一行来，修改为：

``` php
<?php
//失效，会被nginx覆盖
header('Content-Type: application/javascript');

//生效
header('Expires: '.gmdate('D, d M Y H:i:s', time() + 60).' GMT');

//瞎定义一个header-key-value都没问题...
//header("xixi:xixi")

if(!defined('IN_API')) {
    exit('document.write(\'Access Denied\')');
}

loadcore();

include_once libfile('function/block');

loadcache('blockclass');
$bid = intval($_GET['bid']);
block_get_batch($bid);
$data = block_fetch_content($bid, true);

$search = "/(href|src)\=(\"|')(?![fhtps]+\:)(.*?)\\2/i";
$replace = "\\1=\\2$_G[siteurl]\\3\\2";
$data = preg_replace($search, $replace, $data);

echo 'document.write(\''.preg_replace("/\r\n|\n|\r/", '\n', addcslashes($data, "'\\")).'\');';

?>
```

我在注释里已经写了，这种写法不会生效，测试了好久才发现原因，因为`nginx`里有这个配置：

``` etc
    include       /etc/nginx/mime.types;
```

`mime.types`文件已经针对不同文件做了不同header content-type配置，由于我们请求的是个`php`文件，会因为被`php-fpm`接管，最后`nginx`认为这是个`html`文件，会用`text/html`覆盖我们的设置。所以我们再怎么改都没用......纯属浪费时间。

Dz官方也有类似的问题，[Content-Type错误导致验证码无法加载][2]和[uc通信一直提示链接中，chrome 报错 because its MIME type text html is not executable and strict MIME type checking is enabled. ][3]中，官方也推荐使用`header("content-type:application/x-javascript;");`来解决，但这样是修不好的。

只能从`nginx`这边下手，参考[nginx配置content-type问题][4]和[只能针对目录重设 Nginx 的 Content-Type 吗？][5]通过正则表达式方式可以解决，但我实在懒，直接把`nginx`里面的`X-Content-Type-Options`关闭完事:

``` shell
#   add_header X-Content-Type-Options "nosniff";
#   直接注释了...
```

问题结束。

  [1]: http://www.luoxiao123.cn/content-option-nosniff-mime-bug.html
  [2]: https://gitee.com/ComsenzDiscuz/DiscuzX/issues/IKKGE
  [3]: https://gitee.com/ComsenzDiscuz/DiscuzX/issues/ID8TQ
  [4]: https://segmentfault.com/q/1010000009266686/a-1020000009267783
  [5]: https://www.v2ex.com/t/451862
