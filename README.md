@Yaoel Feng
@2016/01/04
@关于flask uwsgi demo程序

execing way: uwsgi --ini uwsgi.ini

uWSGI是一个C应用，所以需要一个C编译器以及Python开发头；
所以在基于Debain的系统中（比如ubuntu）
     apt-get install build-essential python-dev

下载uWSGI：
     pip install uwsgi
     或
     curl http://uwsgi.it/install | bash -s default /tmp/uwsgi
     （安装到/tmp/uwsgi中）
     或
     wget http://projects.unbit.it/downloads/uwsgi-latest.tar.gz
     tar zxvf uwsgi-latest.tar.gz
     cd <dir>
     make
     （build后，你将拥有一个二进制uwsgi文件，当前目录下）

增加并发性和监控
     默认情况下uWSGI是单进程单线程运行的
     我们能通过设置--processes和--threads来设置进程数和线程数
     uwsgi --http :9090 --wsgi-file foobar.py --master --processes 4 --threads 2


     监控，在生产部署时，了解运行过程是很重要的。
     stats子系统允许导出uWSGI的内部统计数据位JSON数据；
     uwsgi --http :9090 --wsgi-file foobar.py --master --processes 4 --threads 2 --stats 127.0.0.1:9191
     
在一个完整的web服务后部署
     虽然uWSGI HTTP路由是稳定高性能的，你也想要将你的应用放在一个多功能的webserver后面；
     nginx支持uwsgi
     常见的nginx配置如下：
     location / {
          include uwsgi_params;
          uwsgi_pass 127.0.0.1:3031;
     }

部署Flask
     Flask导出它的WSGI函数为“app”，所以我们要指引uWSGI使用app。
     uwsgi --socket 127.0.0.1:3000 --wsgi-file myflaskapp.py --callable app --processes 4 --threads 2 --stats 127.0.0.1:9191


other：
     --enable-threads   or enable-threads = true     使能线程功能
     virtualenv = <path>          指定虚拟环境路径
     使用uid和gid来避免使用root权限运行
          uid = foo
          gid = bar

