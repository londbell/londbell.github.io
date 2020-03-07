---
title: CentOS下的运维记录
date: 2020-03-07 16:48:35
tags:
    - php
    - devops
    - nginx
    - cent os
---

我的服务器维护能力也就是个半吊子水平，还是把一些日常案例记下来，以后方便维护：

1. `Cent OS`使用`Mariadb-sever`，而不是`MySQL`:[CentOS7.2安装mariadb-server,解决Failed to start mysqld.service: Unit not found][1]
2. 非源码编译升级PHP：[yum安装PHP升级到7.2版本][2]
3. [在CentOS6.4 yum 线上升级 php5.3.3 至 PHP5.6 .24][3]
4. [如何在CentOS 7上升级到PHP 7][4]
5. 这篇有个sh脚本教你备份，强烈推荐 [用 yum 把服务器的 php 升级到 7][5]
6. [Centos7升级和安装PHP][6]
7. [php-fpm监听socket类型-解决Connection refused][7]
8. [connect() to unix:/tmp/php-fpm.sock failed (2: No such file or directory)][8]
9. [Nginx常见502错误][9]
10. [centos7.2yum安装php70w.x86_64][10]
11. [CentOS7添加epel源和remi源支持PHP7.1][11]
12. [Linux文件权限与属性详解 之 一般权限][12]
13. [Discuz!教程之启用HTTPS后解决各处遗留http://网址问题][13]
14. [CentOS7启动、停止MySQL][14]
15. [one-tab聚合][15]

``` shell
    echo "yum remove -y \\">removephp.sh
    yum list installed | grep php |sed      "s/^\(php\\S*\).*$/\\1 \\\\/" >> removephp.sh
    echo "yum install \\">installphp7.sh
    yum list installed | grep php |sed "s/^\(php\)\(\\S*\).*$/\\170w\\2 \\\\/" >>installphp7.sh
    yum list installed | grep php |sed "s/^\(php\)\(\\S*\)\..*$/yum search \\170w\\2 |grep 'No Matches found'/" |sh
    # Warning: No matches found for: php70w-pecl-xhprof No Matches found
```

  [1]: https://blog.csdn.net/yyyyu3/article/details/80090668
  [2]: https://blog.csdn.net/qq_31725371/article/details/83033884
  [3]: https://blog.csdn.net/xfcy1990/article/details/52851510
  [4]: https://cloud.tencent.com/developer/article/1356524
  [5]: https://cloud.tencent.com/developer/article/10057:
  [6]: https://www.cnblogs.com/biaopei/p/7730464.html
  [7]: https://blog.csdn.net/ty_hf/article/details/78483057
  [8]: https://stackoverflow.com/questions/35989291/connect-to-unix-tmp-php-fpm-sock-failed-2-no-such-file-or-directory
  [9]: https://www.cnblogs.com/zhangyin6985/p/6047333.html
  [10]: http://www.511yj.com/centos-yum-php.html
  [11]: https://blog.csdn.net/zhengbin9/article/details/82745758
  [12]: https://www.cnblogs.com/Jimmy1988/p/7243699.html
  [13]: https://blog.csdn.net/lih062624/article/details/77320739
  [14]: https://blog.csdn.net/plg17/article/details/78300416
  [15]: https://www.one-tab.com/page/6mh8vNGYTf2K3oaJA09XLA