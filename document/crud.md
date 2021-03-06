### 框架CRUD深度完整注解
- DB::xxx()用法的详细流程   
    首先我们的路是由这样的的： 
    ![route](images/crud/19.png) 
    我们的DB使用是这样的
    ![route](images/crud/18.png)   
    我们基于这样的代码深度分析整个流程【就是一次http请求到响应的完整流程】
    如果你没有耐心的话不建议阅读本文，本文中毒过深，不适合心急的老表学习^_^    
    
    -----------------------------分割线----------------------------------------       
    
    - index.php入口文件开始  
    `require __DIR__.'/../vendor/autoload.php';`  
    没什么的，就是引入vendor目录下的自动加载文件，功能很明确就是解决整个框架类的自动加载  
    加载注册的函数，注册多个会构成队列【至于什么是队列，请自行看本方发布过的文章】  
    [队列结构](https://learnku.com/articles/30430)   
    composer的基本功能：加载第三方扩展包【git上的包大部分，也有其它的】和类的自动加载  
    vendor目录默认作为整个框架加载扩展包的目录，并且自动生成autoload.php文件用于自动加载  
    
    `$app = require_once __DIR__.'/../bootstrap/app.php';`   
    这个app.php文件会返回Application应用的实例，是个对象，现在我们进去看看它的代码   
    
    【bootstrap/app.php下的文件内容】
    ```php  
    $app = new Illuminate\Foundation\Application(
        realpath(__DIR__.'/../')
    );  
    
    这个是实例化一个类，Illuminate\Foundation是命名空间  
    其中Illuminate是根命名空间，Foundation是子命名空间，这个类名是 Application  
    这个文件是属于laravel/framework扩展包的内容，我们来看一下这个扩展包的composer.json   
    文件
 
    ```   
    laravel/framework包的信息   
    ![laravel/framework](images/crud/1.png)   
    ```json   
    {
        "name": "laravel/framework",
        "description": "The Laravel Framework.",
        "keywords": ["framework", "laravel"],
        "license": "MIT",
        "homepage": "https://laravel.com",
        "support": {
            "issues": "https://github.com/laravel/framework/issues",
            "source": "https://github.com/laravel/framework"
        },
        "authors": [
            {
                "name": "Taylor Otwell",
                "email": "taylor@laravel.com"
            }
        ],
        "require": {
            "php": ">=7.0",
            "ext-mbstring": "*",
            "ext-openssl": "*",
            "doctrine/inflector": "~1.1",
            "erusev/parsedown": "~1.7",
            "league/flysystem": "^1.0.8",
            "monolog/monolog": "~1.12",
            "mtdowling/cron-expression": "~1.0",
            "nesbot/carbon": "^1.24.1",
            "psr/container": "~1.0",
            "psr/simple-cache": "^1.0",
            "ramsey/uuid": "~3.0",
            "swiftmailer/swiftmailer": "~6.0",
            "symfony/console": "~3.3",
            "symfony/debug": "~3.3",
            "symfony/finder": "~3.3",
            "symfony/http-foundation": "~3.3",
            "symfony/http-kernel": "~3.3",
            "symfony/process": "~3.3",
            "symfony/routing": "~3.3",
            "symfony/var-dumper": "~3.3",
            "tijsverkoyen/css-to-inline-styles": "~2.2",
            "vlucas/phpdotenv": "~2.2"
        },
        "replace": {
            "illuminate/auth": "self.version",
            "illuminate/broadcasting": "self.version",
            "illuminate/bus": "self.version",
            "illuminate/cache": "self.version",
            "illuminate/config": "self.version",
            "illuminate/console": "self.version",
            "illuminate/container": "self.version",
            "illuminate/contracts": "self.version",
            "illuminate/cookie": "self.version",
            "illuminate/database": "self.version",
            "illuminate/encryption": "self.version",
            "illuminate/events": "self.version",
            "illuminate/filesystem": "self.version",
            "illuminate/hashing": "self.version",
            "illuminate/http": "self.version",
            "illuminate/log": "self.version",
            "illuminate/mail": "self.version",
            "illuminate/notifications": "self.version",
            "illuminate/pagination": "self.version",
            "illuminate/pipeline": "self.version",
            "illuminate/queue": "self.version",
            "illuminate/redis": "self.version",
            "illuminate/routing": "self.version",
            "illuminate/session": "self.version",
            "illuminate/support": "self.version",
            "illuminate/translation": "self.version",
            "illuminate/validation": "self.version",
            "illuminate/view": "self.version",
            "tightenco/collect": "<5.5.33"
        },
        "require-dev": {
            "aws/aws-sdk-php": "~3.0",
            "doctrine/dbal": "~2.5",
            "filp/whoops": "^2.1.4",
            "mockery/mockery": "~1.0",
            "orchestra/testbench-core": "3.5.*",
            "pda/pheanstalk": "~3.0",
            "phpunit/phpunit": "~6.0",
            "predis/predis": "^1.1.1",
            "symfony/css-selector": "~3.3",
            "symfony/dom-crawler": "~3.3"
        },
        "autoload": {
            "files": [
                "src/Illuminate/Foundation/helpers.php",
                "src/Illuminate/Support/helpers.php"
            ],
            "psr-4": {
                "Illuminate\\": "src/Illuminate/"
            }
        },
        "autoload-dev": {
            "files": [
                "tests/Database/stubs/MigrationCreatorFakeMigration.php"
            ],
            "psr-4": {
                "Illuminate\\Tests\\": "tests/"
            }
        },
        "extra": {
            "branch-alias": {
                "dev-master": "5.5-dev"
            }
        },
        "suggest": {
            "ext-pcntl": "Required to use all features of the queue worker.",
            "ext-posix": "Required to use all features of the queue worker.",
            "aws/aws-sdk-php": "Required to use the SQS queue driver and SES mail driver (~3.0).",
            "doctrine/dbal": "Required to rename columns and drop SQLite columns (~2.5).",
            "fzaninotto/faker": "Required to use the eloquent factory builder (~1.4).",
            "guzzlehttp/guzzle": "Required to use the Mailgun and Mandrill mail drivers and the ping methods on schedules (~6.0).",
            "laravel/tinker": "Required to use the tinker console command (~1.0).",
            "league/flysystem-aws-s3-v3": "Required to use the Flysystem S3 driver (~1.0).",
            "league/flysystem-rackspace": "Required to use the Flysystem Rackspace driver (~1.0).",
            "league/flysystem-cached-adapter": "Required to use Flysystem caching (~1.0).",
            "nexmo/client": "Required to use the Nexmo transport (~1.0).",
            "pda/pheanstalk": "Required to use the beanstalk queue driver (~3.0).",
            "predis/predis": "Required to use the redis cache and queue drivers (~1.0).",
            "pusher/pusher-php-server": "Required to use the Pusher broadcast driver (~3.0).",
            "symfony/css-selector": "Required to use some of the crawler integration testing tools (~3.3).",
            "symfony/dom-crawler": "Required to use most of the crawler integration testing tools (~3.3).",
            "symfony/psr-http-message-bridge": "Required to psr7 bridging features (~1.0)."
        },
        "config": {
            "sort-packages": true
        },
        "minimum-stability": "dev",
        "prefer-stable": true
    }

    ```   
    我们看到这个包依赖的第三方包还特别多的，symfony包也有不少，我们只关注这个包的  
    类是怎么加载的就行了。  
    ```php "autoload": {
             "files": [
                 "src/Illuminate/Foundation/helpers.php",
                 "src/Illuminate/Support/helpers.php"
             ],
             "psr-4": {
                 "Illuminate\\": "src/Illuminate/"
             }
         },```   
    这个是一个自动加载的选项配置，它的主要作用是在框架在使用本扩展包，会自动加载  
    根命名空间对应的根目录，这就是我们所说的psr-4，本扩展包的类根命名空间都是 
    Illuminate\\，如果还有子目录，就会对应子命名空间，再对具体的类文件名，当然按标准   
    之后，类名对应类文件名了，同时在使得本扩展包的相关函数时，也会自动引入files  
    选项下的文件，为什么会自动加载【麻烦你看看php手册，composer手册吧】  
    
    下面我们来看这个Application的命名空间是不是这个composer.json文件指定的  
     ```php  
     namespace Illuminate\Foundation;
     
     use Closure;
     use RuntimeException;
     use Illuminate\Support\Arr;
     use Illuminate\Support\Str;
     use Illuminate\Http\Request;
     use Illuminate\Support\Collection;
     use Illuminate\Container\Container;
     use Illuminate\Filesystem\Filesystem;
     use Illuminate\Log\LogServiceProvider;
     use Illuminate\Support\ServiceProvider;
     use Illuminate\Events\EventServiceProvider;
     use Illuminate\Routing\RoutingServiceProvider;
     use Symfony\Component\HttpKernel\HttpKernelInterface;
     use Symfony\Component\HttpKernel\Exception\HttpException;
     use Illuminate\Contracts\Http\Kernel as HttpKernelContract;
     use Illuminate\Foundation\Bootstrap\LoadEnvironmentVariables;
     use Symfony\Component\HttpFoundation\Request as SymfonyRequest;
     use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;
     use Illuminate\Contracts\Foundation\Application as ApplicationContract;
     
     class Application extends Container implements ApplicationContract, HttpKernelInterface
     ```  
     
     所以实例化Illuminate\Foundation\Application时它会自动引下  
     vendor/laravel/framework/src/Illuminate/Foundation/Application.php   
     这个类文件，这个类文件有2个基类，是Container，ApplicationContract, HttpKernelInterface
     implements后面是接口类，接口类嘛就是规范子类的方法，告诉子类继承时你必须实现我指定的方法   
     extends嘛就是说子类可以拥有基类的成员，可访问，父类也可以访问子类的成员，他们2者构成Application  
     的整体功能。 
     
     实例化Application时必然会运行它的构造函数了，所以我们来看它的构造函数   
     
     【vendor/laravel/framework/src/Illuminate/Foundation/Application.php的构造函数内容】  
     ```php  
    public function __construct($basePath = null)
        {
            if ($basePath) {
                $this->setBasePath($basePath);
            }
            $this->registerBaseBindings();
            $this->registerBaseServiceProviders();
            $this->registerCoreContainerAliases();
        } 
     ```        
     第一句代码就是判断，判断有没有给根路径，显然给了`realpath(__DIR__.'/../')` 
     这是这个项目的根绝对路径,然后我们进入该方法看看它干什么了  
     ```php  
     public function setBasePath($basePath)
         {
         
            //过滤左边的特殊字符得到路径
             $this->basePath = rtrim($basePath, '\/');
     
             $this->bindPathsInContainer();
     
             return $this;
         }
     ```                 
     我们再继续进去，看看它是怎么把框架的路径信息绑定在容器里的   
     ```php  
     protected function bindPathsInContainer()
         {
         //return $this->basePath.DIRECTORY_SEPARATOR.'app'.($path ? DIRECTORY_SEPARATOR.$path : $path);
         //拼装/app/路径返回 app是应用路径，就是框架/app目录
             $this->instance('path', $this->path());
             //返回根路径
             $this->instance('path.base', $this->basePath());
             //返回return $this->basePath.DIRECTORY_SEPARATOR.'resources'.($path ? DIRECTORY_SEPARATOR.$path : $path);
             //再拼装return $this->resourcePath().DIRECTORY_SEPARATOR.'lang';
             //返回/resources/lang目录
             $this->instance('path.lang', $this->langPath());
             //返回$this->basePath.DIRECTORY_SEPARATOR.'config'.($path ? DIRECTORY_SEPARATOR.$path : $path);
             //就是config目录
             $this->instance('path.config', $this->configPath());
             //返回return $this->basePath.DIRECTORY_SEPARATOR.'public';
             //就是public目录
             $this->instance('path.public', $this->publicPath());
             //返回return $this->storagePath ?: $this->basePath.DIRECTORY_SEPARATOR.'storage';
             //就是storage目录
             $this->instance('path.storage', $this->storagePath());
             //return ($this->databasePath ?: $this->basePath.DIRECTORY_SEPARATOR.'database').($path ? DIRECTORY_SEPARATOR.$path : $path);
             //database目录
             $this->instance('path.database', $this->databasePath());
             //return $this->basePath.DIRECTORY_SEPARATOR.'resources'.($path ? DIRECTORY_SEPARATOR.$path : $path);
             //resources资源目录
             $this->instance('path.resources', $this->resourcePath());
             //返回 return $this->basePath.DIRECTORY_SEPARATOR.'bootstrap'.($path ? DIRECTORY_SEPARATOR.$path : $path);
             //bootstrap目录 
             $this->instance('path.bootstrap', $this->bootstrapPath());
         }
     ```     
     下面我们来看instance是做什么的【看看这个工人是干什么活的】 
     ```php   
     public function instance($abstract, $instance)
         {
         //这句代码我们现在不看，刚实例化关这吊毛什么事啊
         //是不是
             $this->removeAbstractAlias($abstract);
     
        /***
        return isset($this->bindings[$abstract]) ||
                       isset($this->instances[$abstract]) ||
                       $this->isAlias($abstract);
                       return isset($this->aliases[$name]);
        ***/
        //就是判断在bindings,instances,aliases数组是否有指定的key
        //很显然暂时没有什么用
             $isBound = $this->bound($abstract);
     
     //这句在这里也没有什么鬼用
             unset($this->aliases[$abstract]);
     
             // We'll check to determine if this type has been bound before, and if it has
             // we will fire the rebound callbacks registered with the container and it
             // can be updated with consuming classes that have gotten resolved here.
             
             //哦，这句有用了，就是把app,bootstrap,config,database,public,resources
             //storage保存在这个数组里，保存在这个数组里干嘛，目前不知道
             //可能是作者瞎写着玩的，以为好玩
             $this->instances[$abstract] = $instance;
     
     //这句也没有什么用了，目前 
             if ($isBound) {
                 $this->rebound($abstract);
             }
     
             return $instance;
         }
     ```  
     至此`$this->setBasePath($basePath);`这句代码所引发的连环流程完成的功能就是将  
     框架各个目录路径保存起来，保存起来干什么啊？我不知道，可能作者觉得无聊或是好玩吧  
     毕竟大佬我们看不懂  
     接着我们继续分析代码  
     ` $this->registerBaseBindings();`   
     我们进去它是干什么飞机的，```php   
     protected function registerBaseBindings()
         {
         /**
         return static::$instance = $container;
         把自己保存在instance上，好玩吗？这样^_^
         
         **/
             static::setInstance($this);
     
              //这个函数，刚才我们分析过了哦
              //就是把app=$this保存在
              //instances[app]=$this，好玩吗
             $this->instance('app', $this);
     
              //同样的道理，存储在数组里
              
             $this->instance(Container::class, $this);
     
            //同样的道理，存起来，怕它们飞了
             $this->instance(PackageManifest::class, new PackageManifest(
                 new Filesystem, $this->basePath(), $this->getCachedPackagesPath()
             ));
             //这PackageManifest接受了3个参数，我们一一的来看这3个参数  
             //
         }
     ``   
     `new Filesystem`看看这吊毛干什么用的   
     我们先看它的composer.json文件，它是引用其它扩展包的  
     ![filesystem](images/crud/2.png)  
     【composer.json文件内容】  
     ```json  
     {
         "name": "illuminate/filesystem",//包类，没啥可说的
         "description": "The Illuminate Filesystem package.",
         "license": "MIT",
         "homepage": "https://laravel.com",
         "support": {
             "issues": "https://github.com/laravel/framework/issues",
             "source": "https://github.com/laravel/framework"
         },
         "authors": [
             {
                 "name": "Taylor Otwell",//对，就是这大佬撸的包
                 "email": "taylor@laravel.com"
             }
         ],
         "require": {
             "php": ">=7.0",
             "illuminate/contracts": "5.5.*",
             "illuminate/support": "5.5.*",
             "symfony/finder": "~3.3"//哦，还依赖了symfony的包
         },
         "autoload": {
             "psr-4": {
                 "Illuminate\\Filesystem\\": ""//该包下所有的类
                 //根命名空间对应的路径就是当前路径
             }
         },
         "extra": {
         //分支别名，干嘛用的啊
         //https://docs.phpcomposer.com/articles/aliases.html
         //手册路径放这里了哦，不要问了，再问就要打人了哦
             "branch-alias": {
                 "dev-master": "5.5-dev"
             }
         },
         "suggest": {
             "league/flysystem": "Required to use the Flysystem local and FTP drivers (~1.0).",
             "league/flysystem-aws-s3-v3": "Required to use the Flysystem S3 driver (~1.0).",
             "league/flysystem-rackspace": "Required to use the Flysystem Rackspace driver (~1.0)."
         },
         "config": {
             "sort-packages": true
         },
         "minimum-stability": "dev"
     }

     ```  
     下面我们来看这个Filesystem.php文件吧，看看这吊毛是做什么的   
     ```php   
     namespace Illuminate\Filesystem;
     //Illuminate\Filesystem 对应的路径是vendor/laravel/framework/src
     //Illuminate/Filesystem/Filesystem.php  
     
     
     use ErrorException;
     use FilesystemIterator;
     use Symfony\Component\Finder\Finder;
     use Illuminate\Support\Traits\Macroable;
     use Illuminate\Contracts\Filesystem\FileNotFoundException;
     
     //就一个类名
     class Filesystem
     {
     //PHP宏扩展【干什么用的啊，看看手册或是本人前面写过的】
         use Macroable;
     
         /**
          * Determine if a file or directory exists.
          *
          * @param  string  $path
          * @return bool
          */
         public function exists($path)
         {
             return file_exists($path);
         }
     ```  
     读读源码就知道了，用于对文件，目录进行操作的   
     第二个参数`$this->basePath()`不用解释了吧，获取根路径   
     第三个参数得到的是`return $this->bootstrapPath().'/cache/packages.php';`   
     就是bootstrap/cache/packages.php文件，其实这文件返回的是一个数组   
     返回的样子长这样  
     ![packages.php](images/crud/3.png)  
     就是aliases,providers的一个数组，干嘛用的啊，我们继续分析，后面肯定知道这吊毛是做什么的嘛   
     
     接下来我们看看这吊毛`PackageManifest`长啥样   
     ![packagemanifest](images/crud/4.png)  
     哦长这样，看看它的构造函数，构造函数肯定会运行的，所以我们看看  
     【Illuminate\Foundation\PackageManifest文件的构造函数】
     ```php  
     public function __construct(Filesystem $files, $basePath, $manifestPath)
         {
         //哦，这吊毛存储的是Filesystem类的实例
             $this->files = $files;
             //这个保存的是框架的根路径
             $this->basePath = $basePath;
             //  return $this->bootstrapPath().'/cache/packages.php';
             //这个就是保存bootstrap/cache/packages.php
             $this->manifestPath = $manifestPath;
             //这个是composer的vendor目录路径
             $this->vendorPath = $basePath.'/vendor';
         }
     ```   
     
     至此`registerBaseBindings()`的功能就是将app,Container::class,PackageManifest::class  
     对应的内容保存在instances数组里，好玩吗？  
     
     下面继续分析`$this->registerBaseServiceProviders()`  
     呐，它的内容如下  
     ```php  
     protected function registerBaseServiceProviders()
         {
             $this->register(new EventServiceProvider($this));
             $this->register(new LogServiceProvider($this));
             $this->register(new RoutingServiceProvider($this));
         }
     ```  
     来，分析这吊毛`new EventServiceProvider($this)`  
     ![eventserviceProvider](images/crud/5.png)  
     它继承了基类ServiceProvider服务提供者，基类的构造函数肯定接受参数了嘛   
     ![serviceProvider](images/crud/6.png)  这就是它的功能，下面继续分析  
     `$this->register(xxx)`  看看这家伙又是什么的  
     【register方法内容】  
     ```php  
     public function register($provider, $options = [], $force = false)
         {
             if (($registered = $this->getProvider($provider)) && ! $force) {
                 return $registered;
             }
     
             if (is_string($provider)) {
                 
                 $provider = $this->resolveProvider($provider);
             }
     
           
             if (method_exists($provider, 'register')) {
                 $provider->register();
             }
     
        
             $this->markAsRegistered($provider);
     
             if ($this->booted) {
                 $this->bootProvider($provider);
             }
     
             return $provider;
         }  
         
     ```   
     先分析这句话`$this->getProvider($provider))`        
     
     ```php 
     
           public function getProvider($provider)
          {
              return array_values($this->getProviders($provider))[0] ?? null;
          }
          
     ```  
     
     下面接着看`$this->getProvider($provider))` 看看它是做什么的  
     ```php  
     public function getProvider($provider)
         {
             return array_values($this->getProviders($provider))[0] ?? null;
         }
     ```  
     没错，返回了一个数组，关键是框架刚启动【http请求时就启动】，这个破数组能有什么什么东西啊  
     不过，我们继续看 
     ```php  
     public function getProviders($provider)
         {
             //根据传递的参数，取得指定参数对应的value返回
             $name = is_string($provider) ? $provider : get_class($provider);
     
             return Arr::where($this->serviceProviders, function ($value) use ($name) {
             //返回$value属于$name,这个name就是$provider的类名
                 return $value instanceof $name;
             });
         }
     ```  
     ```php  
     //就一个空数组，你处理毛线啊【目前没有用】
     protected $serviceProviders = [];
     ```  
     下面我们来看这个Arr这个吊东西  
     ![Arr](images/crud/8.png)  
     这是它大概的模样，下面看看它的where是干什么用的啊  
     ```php  
      public static function where($array, callable $callback)
         {
             return array_filter($array, $callback, ARRAY_FILTER_USE_BOTH);
         }
     ```  
     呐，用了这个破数组，这个数组是干嘛用的啊，我们去看手册  
     [array_filter手册](https://www.php.net/manual/zh/function.array-filter.php)  
     
     所以这个函数where就是用于从数组查找指定的内容，框架刚启动，这serviceProviders是空的  
     `if (($registered = $this->getProvider($provider)) && ! $force) {
                       return $registered;
                   }
           `  
     所以这句话目前没有什么吊用，我们继续向下看吧  
     `if (is_string($provider)) {
                       
                       $provider = $this->resolveProvider($provider);
                   }`  
     这句也不有什么用，它又不是字符串，是个对象啊  
     ` if (method_exists($provider, 'register')) {
                  $provider->register();
              }`   
              
     这句话有用了，判断这个对象是否存在指定的方法，我们去看它的方法  
     ```php  
     class EventServiceProvider extends ServiceProvider
     {
         /**
          * Register the service provider.
          *
          * @return void
          */
         public function register()
         {
             $this->app->singleton('events', function ($app) {
                 return (new Dispatcher($app))->setQueueResolver(function () use ($app) {
                     return $app->make(QueueFactoryContract::class);
                 });
             });
         }
     }
     ```  
     我们看到它app调用了singleton方法了，这就是刚才实例化就是`new EventServiceProvider($this)`  
     传递$this的原因啦，不然它调用啥  
     我们来看看singleton方法  
     ```php  
       public function singleton($abstract, $concrete = null)
         {
             $this->bind($abstract, $concrete, true);
         }
     ```  
     再来看bind方法  
     ```php  
     public function bind($abstract, $concrete = null, $shared = false)
         {
         /**
         protected function dropStaleInstances($abstract)
             {
                 unset($this->instances[$abstract], $this->aliases[$abstract]);
             }
             判断instances,aliases是否保存了
         **/
             $this->dropStaleInstances($abstract);
             if (is_null($concrete)) {
                 $concrete = $abstract;
             }
             //如果不是匿名函数类，很显然这句话我们不用看
             if (! $concrete instanceof Closure) {
                 $concrete = $this->getClosure($abstract, $concrete);
             }
             //compact把，concrete,shared构造成一个数组
             $this->bindings[$abstract] = compact('concrete', 'shared');
             /**
             /**
                     return isset($this->aliases[$name]);
                     取回类名的别名
                      **/
                     if ($this->isAlias($abstract)) {
                         //取回类的别名
                         $abstract = $this->getAlias($abstract);
                     }
             
                     return isset($this->resolved[$abstract]) ||
                            isset($this->instances[$abstract]);
             **/
             //很明显我们不用管这句话是干什么的
             if ($this->resolved($abstract)) {
                 $this->rebound($abstract);
             }
         }
     ```  
     ` $this->register(new EventServiceProvider($this));`  这句话是做什么的啊  
     就是运行EventServiceProvider->register方法  
     注册就是别名，匿名函数保存在bindings数组里  
     
     接着看`new LogServiceProvider($this)`  
     ```php  
     public function register()
         {
             $this->app->singleton('log', function () {
                 return $this->createLogger();
             });
         } 
     ```  
     
     接着看路由的服务注册  
     ```php  
     $this->register(new RoutingServiceProvider($this));  
     public function register()
         {
             /**
             将router= function(){return Router($app['events'], $app);} key,value键值
             形式保存在Application->bindings[]里
              **/
             $this->registerRouter();
             // $this->app->singleton('router', function ($app) {
                           return new Router($app['events'], $app);
                       });
                       
                       
     
             $this->registerUrlGenerator();
     
             $this->registerRedirector();
     
             $this->registerPsrRequest();
     
             $this->registerPsrResponse();
     
             $this->registerResponseFactory();
     
             $this->registerControllerDispatcher();
         }
     ```    
     
     好了，我们再来看最后一句`$this->registerCoreContainerAliases();`  
     ```php  
     public function registerCoreContainerAliases()
         {
             foreach ([
                 'app'                  => [\Illuminate\Foundation\Application::class, \Illuminate\Contracts\Container\Container::class, \Illuminate\Contracts\Foundation\Application::class,  \Psr\Container\ContainerInterface::class],
                 'auth'                 => [\Illuminate\Auth\AuthManager::class, \Illuminate\Contracts\Auth\Factory::class],
                 'auth.driver'          => [\Illuminate\Contracts\Auth\Guard::class],
                 'blade.compiler'       => [\Illuminate\View\Compilers\BladeCompiler::class],
                 'cache'                => [\Illuminate\Cache\CacheManager::class, \Illuminate\Contracts\Cache\Factory::class],
                 'cache.store'          => [\Illuminate\Cache\Repository::class, \Illuminate\Contracts\Cache\Repository::class],
                 'config'               => [\Illuminate\Config\Repository::class, \Illuminate\Contracts\Config\Repository::class],
                 'cookie'               => [\Illuminate\Cookie\CookieJar::class, \Illuminate\Contracts\Cookie\Factory::class, \Illuminate\Contracts\Cookie\QueueingFactory::class],
                 'encrypter'            => [\Illuminate\Encryption\Encrypter::class, \Illuminate\Contracts\Encryption\Encrypter::class],
                 'db'                   => [\Illuminate\Database\DatabaseManager::class],
                 'db.connection'        => [\Illuminate\Database\Connection::class, \Illuminate\Database\ConnectionInterface::class],
                 'events'               => [\Illuminate\Events\Dispatcher::class, \Illuminate\Contracts\Events\Dispatcher::class],
                 'files'                => [\Illuminate\Filesystem\Filesystem::class],
                 'filesystem'           => [\Illuminate\Filesystem\FilesystemManager::class, \Illuminate\Contracts\Filesystem\Factory::class],
                 'filesystem.disk'      => [\Illuminate\Contracts\Filesystem\Filesystem::class],
                 'filesystem.cloud'     => [\Illuminate\Contracts\Filesystem\Cloud::class],
                 'hash'                 => [\Illuminate\Contracts\Hashing\Hasher::class],
                 'translator'           => [\Illuminate\Translation\Translator::class, \Illuminate\Contracts\Translation\Translator::class],
                 'log'                  => [\Illuminate\Log\Writer::class, \Illuminate\Contracts\Logging\Log::class, \Psr\Log\LoggerInterface::class],
                 'mailer'               => [\Illuminate\Mail\Mailer::class, \Illuminate\Contracts\Mail\Mailer::class, \Illuminate\Contracts\Mail\MailQueue::class],
                 'auth.password'        => [\Illuminate\Auth\Passwords\PasswordBrokerManager::class, \Illuminate\Contracts\Auth\PasswordBrokerFactory::class],
                 'auth.password.broker' => [\Illuminate\Auth\Passwords\PasswordBroker::class, \Illuminate\Contracts\Auth\PasswordBroker::class],
                 'queue'                => [\Illuminate\Queue\QueueManager::class, \Illuminate\Contracts\Queue\Factory::class, \Illuminate\Contracts\Queue\Monitor::class],
                 'queue.connection'     => [\Illuminate\Contracts\Queue\Queue::class],
                 'queue.failer'         => [\Illuminate\Queue\Failed\FailedJobProviderInterface::class],
                 'redirect'             => [\Illuminate\Routing\Redirector::class],
                 'redis'                => [\Illuminate\Redis\RedisManager::class, \Illuminate\Contracts\Redis\Factory::class],
                 'request'              => [\Illuminate\Http\Request::class, \Symfony\Component\HttpFoundation\Request::class],
                 'router'               => [\Illuminate\Routing\Router::class, \Illuminate\Contracts\Routing\Registrar::class, \Illuminate\Contracts\Routing\BindingRegistrar::class],
                 'session'              => [\Illuminate\Session\SessionManager::class],
                 'session.store'        => [\Illuminate\Session\Store::class, \Illuminate\Contracts\Session\Session::class],
                 'url'                  => [\Illuminate\Routing\UrlGenerator::class, \Illuminate\Contracts\Routing\UrlGenerator::class],
                 'validator'            => [\Illuminate\Validation\Factory::class, \Illuminate\Contracts\Validation\Factory::class],
                 'view'                 => [\Illuminate\View\Factory::class, \Illuminate\Contracts\View\Factory::class],
             ] as $key => $aliases) {
                 foreach ($aliases as $alias) {
                     //别名  类名
                     $this->alias($key, $alias);
                     /**
                      //类名=别名
                             $this->aliases[$alias] = $abstract;
                             //aliases[\Illuminate\Foundation\Application::class]=app
                             //aliases[ \Psr\Container\ContainerInterface::class]=app
                             //aliases[\Illuminate\Contracts\Foundation\Application::class]=app
                             //aliases[\Illuminate\Contracts\Container\Container::class]=app
                             
                             //以此方式存储起来
                             //别名[]=类名
                             
                             //abstractAliases[app][]=\Illuminate\Foundation\Application::class
                             //abstractAliases[app][]= \Psr\Container\ContainerInterface::class
                             //abstractAliases[app][]=\Illuminate\Contracts\Foundation\Application::class
                             //abstractAliases[app][]=\Illuminate\Contracts\Container\Container::class
                             $this->abstractAliases[$abstract][] = $alias;
                     **/
                 }
             }
             
             $this->aliases;
             $this->abstractAliases;
         }

     ```  
     
    ===============================================================================================  
    
    经过层层分析，实例化Application完成了如下动作  
    1、框架各个目录保存，以及app，Container::class，PackageManifest::class保存在
    instances【】数组里 
    2、运行EventServiceProvider,LogServiceProvider,RoutingServiceProvider的register 
    方法并将对应的服务注册【就是保存在bindings数组里】
    3、将框架的核心类以别名[]=类名，类名=别名形式保存在aliases,abstractAliases里  
    
    经过上面的流程后，再返回$app哦  
    
    接着看app.php文件的下一句代码  
    `$app->singleton(
         /**
         针对web的绑定
     
          **/
         Illuminate\Contracts\Http\Kernel::class,
         App\Http\Kernel::class
     );`  
     
    其中 App\Http\Kernel::class会被封装成一个匿名函数，呐看下面  
    其实前面讲了
    ```php  
    if (! $concrete instanceof Closure) {
                
                $concrete = $this->getClosure($abstract, $concrete);
               
            }
    ```   
    ```php  
    protected function getClosure($abstract, $concrete)
        {
         
            return function ($container, $parameters = []) use ($abstract, $concrete) {
                if ($abstract == $concrete) {
                    return $container->build($concrete);
                }
    
                return $container->make($concrete, $parameters);
            };
        }
    ```  
    
    它们会保存在bindings[Illuminate\Contracts\Http\Kernel::class]=匿名函数()里  
    匿名函数长上面那个吊样  
    第二句，第三句  
    ```php  
    $app->singleton(
        /**
        针对cli的绑定
         **/
        Illuminate\Contracts\Console\Kernel::class,
        App\Console\Kernel::class
    );
    
    $app->singleton(
        /**
        针对异常的绑定
         **/
        Illuminate\Contracts\Debug\ExceptionHandler::class,
        App\Exceptions\Handler::class
    );
    ```  
    
    ok，它保存在数组里了，我们来看index.php的下一句代码  
    `$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);`  
    
    下面我们来看这吊毛是如何运行的吧  
    ```php 
    public function make($abstract, array $parameters = [])
        {
            $abstract = $this->getAlias($abstract);
            //判断是否是属于延迟加载类
            if (isset($this->deferredServices[$abstract]) && ! isset($this->instances[$abstract])) {
                $this->loadDeferredProvider($abstract);
            }
    
            return parent::make($abstract, $parameters);
        }
    ```  
    第一句`$this->getAlias($abstract)`  不用看，没有什么用，目前  
    第二句呢也不用看，看第三句`return parent::make($abstract, $parameters)`  
    呐，调用父类了 
    ```php  
    public function make($abstract, array $parameters = [])
        {
            /**
            根据传递的抽像类找到具体的对象返回
             **/
            return $this->resolve($abstract, $parameters);
        }
    ```  
    再来看resolve这吊毛的动作  
    ```php  
     protected function resolve($abstract, $parameters = [])
        {
           
           //这句目前没有什么用，我们不用管
            $abstract = $this->getAlias($abstract);
    
            //这吊毛也不用看
            $needsContextualBuild = ! empty($parameters) || ! is_null(
               
                $this->getContextualConcrete($abstract)
            );
    
            //这也不用看
            if (isset($this->instances[$abstract]) && ! $needsContextualBuild) {
                return $this->instances[$abstract];
            }
    
            //也不用看，根本没传递参数
            $this->with[] = $parameters;
    
            /**
            取回一个匿名函数
            **/
            $concrete = $this->getConcrete($abstract);
    
            if ($this->isBuildable($concrete, $abstract)) {
                
                $object = $this->build($concrete);
            } else {
                $object = $this->make($concrete);
            }
    
          
            foreach ($this->getExtenders($abstract) as $extender) {
                $object = $extender($object, $this);
            }
    
            if ($this->isShared($abstract) && ! $needsContextualBuild) {
                
                $this->instances[$abstract] = $object;
            }

            $this->fireResolvingCallbacks($abstract, $object);
    
            $this->resolved[$abstract] = true;
            
            array_pop($this->with);
    
            return $object;
        }

    ```  
    ```php  
    protected function getConcrete($abstract)
        {
           
            if (! is_null($concrete = $this->getContextualConcrete($abstract))) {
                return $concrete;
            }
    
               //呐，从数组里取
               //取回来的是个匿名函数
            if (isset($this->bindings[$abstract])) {
                
                return $this->bindings[$abstract]['concrete'];
            }
    
            return $abstract;
        }
    ```  
    判断是否属于匿名类 
    ```php  
    protected function isBuildable($concrete, $abstract)
        {
            return $concrete === $abstract || $concrete instanceof Closure;
        }
    ```  
    
    我们看build【】方法  
    ```php 
    public function build($concrete)
        {
        
        //运行后返回
            if ($concrete instanceof Closure) {
               
                return $concrete($this, $this->getLastParameterOverride());
            }
  
            $reflector = new ReflectionClass($concrete);

            if (! $reflector->isInstantiable()) {
                
                return $this->notInstantiable($concrete);
            }
    
            $this->buildStack[] = $concrete;
    
            $constructor = $reflector->getConstructor();
  
            if (is_null($constructor)) {
                
                return new $concrete;
            }
    
            $dependencies = $constructor->getParameters();
    
            $instances = $this->resolveDependencies(
                $dependencies
            );
           
            array_pop($this->buildStack);
    
            return $reflector->newInstanceArgs($instances);
        }

    ```  
    直接运行匿名函数了，运行这个匿名函数时，函数内容就是如下
    ```php  
    return function ($container, $parameters = []) use ($abstract, $concrete) {
                if ($abstract == $concrete) {
                    return $container->build($concrete);
                }
    
                return $container->make($concrete, $parameters);
            };
    ```  
    所以还是在运行make方法的,那么此时，我们知道这次运行的不是匿名函数了，是个类了所以会  
    进行反射实例化返回【看上面的build方法就知道了啊】  
    
    经过分析它实例化的类是  
    `App\Http\Kernel.php`文件
     
    接下来，我们来看这文件的内容  
    ```php  
    <?php
    
    namespace App\Http;
    
    use App\Http\Middleware\UserVerify;
    use Illuminate\Foundation\Http\Kernel as HttpKernel;
    
    class Kernel extends HttpKernel
    {
        /**
         * The application's global HTTP middleware stack.
         *
         * These middleware are run during every request to your application.
         *
         * @var array
         */
        protected $middleware = [
            \Illuminate\Foundation\Http\Middleware\CheckForMaintenanceMode::class,
            \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
            \App\Http\Middleware\TrimStrings::class,
            \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
            \App\Http\Middleware\TrustProxies::class,
        ];
    
        /**
         * The application's route middleware groups.
         *
         * @var array
         */
        protected $middlewareGroups = [
            'web' => [
                \App\Http\Middleware\EncryptCookies::class,
                \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
                \Illuminate\Session\Middleware\StartSession::class,
                // \Illuminate\Session\Middleware\AuthenticateSession::class,
                \Illuminate\View\Middleware\ShareErrorsFromSession::class,
                \App\Http\Middleware\VerifyCsrfToken::class,
                \Illuminate\Routing\Middleware\SubstituteBindings::class,
            ],
    
            'api' => [
                'throttle:60,1',
                'bindings',
            ],
        ];
    
        /**
         * The application's route middleware.
         *
         * These middleware may be assigned to groups or used individually.
         *
         * @var array
         */
        protected $routeMiddleware = [
            'auth' => \Illuminate\Auth\Middleware\Authenticate::class,
            'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
            'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
            'can' => \Illuminate\Auth\Middleware\Authorize::class,
            'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
            'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
            'user.verify'=>UserVerify::class
        ];
    }

    ```  
    这是子类，定义了一堆中间件数组，它继承了laravel/framework的类  
    ```php 
    namespace Illuminate\Foundation\Http;
    
    use Exception;
    use Throwable;
    use Illuminate\Routing\Router;
    use Illuminate\Routing\Pipeline;
    use Illuminate\Support\Facades\Facade;
    use Illuminate\Contracts\Debug\ExceptionHandler;
    use Illuminate\Contracts\Foundation\Application;
    use Illuminate\Contracts\Http\Kernel as KernelContract;
    use Symfony\Component\Debug\Exception\FatalThrowableError;
    
    class Kernel implements KernelContract
    ```  
    然后再来看index.php的下一句代码  
    ```php  
    $response = $kernel->handle(
        /**
        该Request类是Symfony的一个组件
        该组件的详细文档在https://symfony.com/doc/current/components/http_foundation.html
        大体功能是对PHP的超级全局变量进行了封装,$_GET,$_POST,$_HEADER,$_SERVER等封装为对象
        并组合成组合对象返回
         **/
        $request = Illuminate\Http\Request::capture()
    );
    ```  
    哦，运行handle方法了哦  
    然后我们去看看`Illuminate\Http\Request::capture`干什么飞机了    
    我们来看Illuminate\\Http\Reqeust::class类的内容【截图】 
    ```php  
    namespace Illuminate\Http;
    
    use Closure;
    use ArrayAccess;
    use RuntimeException;
    use Illuminate\Support\Arr;
    use Illuminate\Support\Str;
    use Illuminate\Support\Traits\Macroable;
    use Illuminate\Contracts\Support\Arrayable;
    use Symfony\Component\HttpFoundation\ParameterBag;
    use Symfony\Component\HttpFoundation\Request as SymfonyRequest;
    
    /**
    Request请求对象继承了Symfony组件的请求组件，并能实现数组接口式访问
     **/
     //它还实现了数组访问的接口
    class Request extends SymfonyRequest implements Arrayable, ArrayAccess
    ```  
    再来看SymfonyRequest这吊毛  
    来看图 
    ![request](images/crud/9.png)   
    
    这个类是Symfony扩展包，不是laravel/framework作者的了，又是别一个撸的包了  
    ```json 
    {
        "name": "symfony/http-foundation",//包名
        "type": "library",
        "description": "Symfony HttpFoundation Component",
        "keywords": [],
        "homepage": "https://symfony.com",//看看吧，又是另一个大佬出来了
        "license": "MIT",
        "authors": [
            {
                "name": "Fabien Potencier",//看看吧，又是另一个大佬出来了
                "email": "fabien@symfony.com"
            },
            {
                "name": "Symfony Community",
                "homepage": "https://symfony.com/contributors"
            }
        ],
        "require": {
            "php": "^5.5.9|>=7.0.8",
            "symfony/polyfill-mbstring": "~1.1",
            "symfony/polyfill-php70": "~1.6"
        },
        "require-dev": {
            "symfony/expression-language": "~2.8|~3.0|~4.0"
        },
        "autoload": {
        //自动加载的根命名空间对应的路径
            "psr-4": { "Symfony\\Component\\HttpFoundation\\": "" },
            "exclude-from-classmap": [
                "/Tests/"
            ]
        },
        "minimum-stability": "dev",
        "extra": {
            "branch-alias": {
                "dev-master": "3.4-dev"
            }
        }
    }

    ```  
    
    哦，分析到此，我们知道这Request依赖了Symfony扩展包,呐来看看呗  
    ```php  
     public static function capture()
        {
            static::enableHttpMethodParameterOverride();
    
            return static::createFromBase(SymfonyRequest::createFromGlobals());
        }
    ```  
    继续  
    ```php 
    public static function createFromGlobals()
        {
            // With the php's bug #66606, the php's built-in web server
            // stores the Content-Type and Content-Length header values in
            // HTTP_CONTENT_TYPE and HTTP_CONTENT_LENGTH fields.
            $server = $_SERVER;
            if ('cli-server' === \PHP_SAPI) {
                if (array_key_exists('HTTP_CONTENT_LENGTH', $_SERVER)) {
                    $server['CONTENT_LENGTH'] = $_SERVER['HTTP_CONTENT_LENGTH'];
                }
                if (array_key_exists('HTTP_CONTENT_TYPE', $_SERVER)) {
                    $server['CONTENT_TYPE'] = $_SERVER['HTTP_CONTENT_TYPE'];
                }
            }
    
            $request = self::createRequestFromFactory($_GET, $_POST, array(), $_COOKIE, $_FILES, $server);
    
            if (0 === strpos($request->headers->get('CONTENT_TYPE'), 'application/x-www-form-urlencoded')
                && \in_array(strtoupper($request->server->get('REQUEST_METHOD', 'GET')), array('PUT', 'DELETE', 'PATCH'))
            ) {
                parse_str($request->getContent(), $data);
                $request->request = new ParameterBag($data);
            }
    
            return $request;
        }
    ```
    继续看代码啊  
    ```php  
    private static function createRequestFromFactory(array $query = array(), array $request = array(), array $attributes = array(), array $cookies = array(), array $files = array(), array $server = array(), $content = null)
        {
            if (self::$requestFactory) {
                $request = \call_user_func(self::$requestFactory, $query, $request, $attributes, $cookies, $files, $server, $content);
    
                if (!$request instanceof self) {
                    throw new \LogicException('The Request factory must return an instance of Symfony\Component\HttpFoundation\Request.');
                }
    
                return $request;
            }
    
            return new static($query, $request, $attributes, $cookies, $files, $server, $content);
        }
    ```  
    
    ```php 
     public function __construct(array $query = array(), array $request = array(), array $attributes = array(), array $cookies = array(), array $files = array(), array $server = array(), $content = null)
        {
            $this->initialize($query, $request, $attributes, $cookies, $files, $server, $content);
        }
    ```  
    
    ```php 
    public function initialize(array $query = array(), array $request = array(), array $attributes = array(), array $cookies = array(), array $files = array(), array $server = array(), $content = null)
        {
            /**
            将PHP全局变量封装为对象式返回$_GET, $_POST, array(), $_COOKIE, $_FILES, $server
             **/
            $this->request = new ParameterBag($request);
            $this->query = new ParameterBag($query);
            $this->attributes = new ParameterBag($attributes);
            $this->cookies = new ParameterBag($cookies);
            $this->files = new FileBag($files);
            $this->server = new ServerBag($server);
            $this->headers = new HeaderBag($this->server->getHeaders());
    
            $this->content = $content;
            $this->languages = null;
            $this->charsets = null;
            $this->encodings = null;
            $this->acceptableContentTypes = null;
            $this->pathInfo = null;
            $this->requestUri = null;
            $this->baseUrl = null;
            $this->basePath = null;
            $this->method = null;
            $this->format = null;
        }
    ```  
    
    全封装成对象了  
    ```php  
    namespace Symfony\Component\HttpFoundation;
    
    /**
     * ParameterBag is a container for key/value pairs.
     *
     * @author Fabien Potencier <fabien@symfony.com>
     */
    class ParameterBag implements \IteratorAggregate, \Countable
    ```  
    ```php  
    namespace Symfony\Component\HttpFoundation;
    
    use Symfony\Component\HttpFoundation\File\UploadedFile;
    
    /**
     * FileBag is a container for uploaded files.
     *
     * @author Fabien Potencier <fabien@symfony.com>
     * @author Bulat Shakirzyanov <mallluhuct@gmail.com>
     */
    class FileBag extends ParameterBag
    ```  
    
    ```php 
    namespace Symfony\Component\HttpFoundation;
    
    /**
     * ServerBag is a container for HTTP headers from the $_SERVER variable.
     *
     * @author Fabien Potencier <fabien@symfony.com>
     * @author Bulat Shakirzyanov <mallluhuct@gmail.com>
     * @author Robert Kiss <kepten@gmail.com>
     */
    class ServerBag extends ParameterBag
    ```   
    
    ```php 
    namespace Symfony\Component\HttpFoundation;
    
    /**
     * HeaderBag is a container for HTTP headers.
     *
     * @author Fabien Potencier <fabien@symfony.com>
     */
    class HeaderBag implements \IteratorAggregate, \Countable
    ```  
    
    看到此就够了，对那些超级全局变量它做了再次封装，没什么奇怪的 
    我们来看Kernel的构造函数  
    ```php  
    public function __construct(Application $app, Router $router)
        {
            $this->app = $app;
            $this->router = $router;
    
            $router->middlewarePriority = $this->middlewarePriority;
            /**
            向路由类添加路由中间件类
            向路由类添加中间件类组
             **/
            foreach ($this->middlewareGroups as $key => $middleware) {
                $router->middlewareGroup($key, $middleware);
                //$this->middlewareGroups[$name] = $middleware;
            }
    
            foreach ($this->routeMiddleware as $key => $middleware) {
                $router->aliasMiddleware($key, $middleware);
                //$this->middleware[$name] = $class;
            }
        }
    ```  
    这简单吧，把中间件保存在路由里,ok我们终于可以看到handle方法了，来看看她 
    看看她美丽的身姿  
    ```php 
    public function handle($request)
        {
    
            try {
                /**
                Symfony组件的Request组件方法
                 **/
                $request->enableHttpMethodParameterOverride();
    
                $response = $this->sendRequestThroughRouter($request);
            } catch (Exception $e) {
                $this->reportException($e);
    
                $response = $this->renderException($request, $e);
            } catch (Throwable $e) {
                $this->reportException($e = new FatalThrowableError($e));
    
                $response = $this->renderException($request, $e);
            }
    
            $this->app['events']->dispatch(
                new Events\RequestHandled($request, $response)
            );
    
            return $response;
        }

    ```  
    第一句没啥可看的，整个来看它是使用了try catch来做，请求失败后自动捕获到异常 
    同时，后面会激活事件，events前面分析过了，要不你去看看事件服务类是怎么运行的吧  
    我可没有忘记，呐继续看代码  
    ```php  
    protected function sendRequestThroughRouter($request)
        {
            //这是保存在instances【】数组里没有问题吧，前面说了
            $this->app->instance('request', $request);
    
            Facade::clearResolvedInstance('request');
    
            /**
            循环运行本类的成员$this->$bootstrappers[]下的成员数组
             **/
            $this->bootstrap();
    
            return (new Pipeline($this->app))
                        ->send($request) //加载全局中间件
                        ->through($this->app->shouldSkipMiddleware() ? [] : $this->middleware)
                        ->then($this->dispatchToRouter());
    
            /**
            $this->dispatchToRouter() 控制器运行之后返回的响应，响应由Symfony的组件完成
             **/
        }
    ```  
    来看看`$this->bootstrap();`这句吧  
    ```php 
     public function bootstrap()
        {
        //默认protected $hasBeenBootstrapped = false;
            if (! $this->app->hasBeenBootstrapped()) {
            //运行
                $this->app->bootstrapWith($this->bootstrappers());
            }
        }
    ```  
    呐，继续看它跑  
    ```php  
    public function bootstrapWith(array $bootstrappers)
        {
        //立马设置为真，免得下次再跑
            $this->hasBeenBootstrapped = true;
    
            foreach ($bootstrappers as $bootstrapper) {
                //框架在启动的时候已经注册了【注册模式】events，最终返回Illuminate\Events\Dispatcher
                $this['events']->fire('bootstrapping: '.$bootstrapper, [$this]);
                /**
                这里会实例化框架的启动类数组并执行启动方法
                 **/
                $this->make($bootstrapper)->bootstrap($this);
    
                $this['events']->fire('bootstrapped: '.$bootstrapper, [$this]);
            }
        }
    ```  
    `$bootstrappers的内容`  
    ```php  
    protected $bootstrappers = [
            \Illuminate\Foundation\Bootstrap\LoadEnvironmentVariables::class,
            \Illuminate\Foundation\Bootstrap\LoadConfiguration::class,
            \Illuminate\Foundation\Bootstrap\HandleExceptions::class,
            \Illuminate\Foundation\Bootstrap\RegisterFacades::class,
            \Illuminate\Foundation\Bootstrap\RegisterProviders::class,
            \Illuminate\Foundation\Bootstrap\BootProviders::class,
        ];
    ```  
    看看这代码  `$this['events']->fire('bootstrapping: '.$bootstrapper, [$this]);`  
    再来看看前面讲过的events注册代码  
    ```php  
    $this->app->singleton('events', function ($app) {
                return (new Dispatcher($app))->setQueueResolver(function () use ($app) {
                    return $app->make(QueueFactoryContract::class);
                });
            });
    ```  
    放心$this['events]会找到这破函数运行的，前面说过这Application实现了ArrayAccess接口  
    下面来看 
    ```php  
     public function offsetGet($key)
        {
            return $this->make($key);
        }
    ```  
    看到了吧,套路和前面分析过的Kernel实现机制【实例化】一个套路，最终会运行对应的匿名函数  
    我们看到函数里，它实例化了一个调度器  
    来看看它的部分源码 
    ```php  
    namespace Illuminate\Events;
    
    use Exception;
    use ReflectionClass;
    use Illuminate\Support\Arr;
    use Illuminate\Support\Str;
    use Illuminate\Container\Container;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
    use Illuminate\Contracts\Events\Dispatcher as DispatcherContract;
    use Illuminate\Contracts\Broadcasting\Factory as BroadcastFactory;
    use Illuminate\Contracts\Container\Container as ContainerContract;
    
    class Dispatcher implements DispatcherContract
    {
        /**
         * The IoC container instance.
         *
         * @var \Illuminate\Contracts\Container\Container
         */
        protected $container;
    
        /**
         * The registered event listeners.
         *
         * @var array
         */
        protected $listeners = [];
    
        /**
         * The wildcard listeners.
         *
         * @var array
         */
        protected $wildcards = [];
    
        /**
         * The queue resolver instance.
         *
         * @var callable
         */
        protected $queueResolver;
    
        /**
         * Create a new event dispatcher instance.
         *
         * @param  \Illuminate\Contracts\Container\Container|null  $container
         * @return void
         */
        public function __construct(ContainerContract $container = null)
        {
            $this->container = $container ?: new Container;
        }
    ```  
    最终运行如下代码  
    ```php 
    use Illuminate\Contracts\Queue\Factory as QueueFactoryContract;
    return $app->make(QueueFactoryContract::class);
    ```  
    呐，这句话会自动去找对应的具体类 
    ![queue](images/crud/10.png)  
    最终要找的是`'queue'                => [\Illuminate\Queue\QueueManager::class, \Illuminate\Contracts\Queue\Factory::class, \Illuminate\Contracts\Queue\Monitor::class],`  
    这吊毛  
    继续看fire动作 
    `fire('bootstrapping: '.$bootstrapper, [$this])`  
    拼装后是这样的 
    bootstrapping:\Illuminate\Foundation\Bootstrap\LoadEnvironmentVariables::class  
    表示我要启动这吊毛了  
    ```php  
      public function fire($event, $payload = [], $halt = false)
        {
        //会从事件池里取对应的监听器运行
            return $this->dispatch($event, $payload, $halt);
        }
    ```  
    这个fire目前没有什么用，因为监听器根本就没有  
    估计这老外没事干写在这里，^_^ 
    继续来看`$this->make($bootstrapper)->bootstrap($this);`  
    
    环境配置类的运行  
    ```php  
    class LoadEnvironmentVariables
    {
        /**
         * Bootstrap the given application.
         *
         * @param  \Illuminate\Contracts\Foundation\Application  $app
         * @return void
         */
        public function bootstrap(Application $app)
        {
            if ($app->configurationIsCached()) {
                return;
            }
    
            $this->checkForSpecificEnvironmentFile($app);
    
            try {
                /**
                环境变量配置文件加载后的最终结果是
                // Apache environment variable exists, overwrite it
                if (function_exists('apache_getenv') && function_exists('apache_setenv') && apache_getenv($name)) {
                apache_setenv($name, $value);
                }
    
                if (function_exists('putenv')) {
                putenv("$name=$value");
                }
    
                $_ENV[$name] = $value;
                $_SERVER[$name] = $value;
                
                 **/
                (new Dotenv($app->environmentPath(), $app->environmentFile()))->load();
            } catch (InvalidPathException $e) {
                //
            }
        }

    ```  
    接下来看看这句`$this->checkForSpecificEnvironmentFile($app);`  
    内容就是下面 
    ```php  
    protected function checkForSpecificEnvironmentFile($app)
        {
            /**
            runningInConsole()检测运行方式
            
             **/
            if ($app->runningInConsole() && ($input = new ArgvInput)->hasParameterOption('--env')) {
                if ($this->setEnvironmentFilePath(
                    $app, $app->environmentFile().'.'.$input->getParameterOption('--env')
                )) {
                    return;
                }
            }
    
    //检测.env文件有没有配置选项
            if (! env('APP_ENV')) {
                return;
            }
    
    //保存env文件
            $this->setEnvironmentFilePath(
                $app, $app->environmentFile().'.'.env('APP_ENV')
            );
        }
    ```  
    再来看最后一句  
    ```php  
    protected function setEnvironmentFilePath($app, $file)
        {
            /**
            .env环境配置文件存在，则加载
             **/
            if (file_exists($app->environmentPath().'/'.$file)) {
                $app->loadEnvironmentFrom($file);
    
                return true;
            }
    
            return false;
        }
    ```  
    运行完之后就是这样   
    【Application下】
    ```php  
     public function loadEnvironmentFrom($file)
        {
            $this->environmentFile = $file;
    
            return $this;
        }
    ```  
    然后回到上面的这句话` (new Dotenv($app->environmentPath(), $app->environmentFile()))->load();`  
    
    哦，用了Dotenv，这不是laravel的内核，是别人写的啦，我们去看这扩展包的信息  
    ![dotenv](images/crud/11.png)  
    [Dotenv包的用法](https://packagist.org/packages/vlucas/phpdotenv)  
    ![dotenv](images/crud/12.png)    
    
    All of the defined variables are now accessible with the getenv method, and are available in the $_ENV and $_SERVER super-globals.  
    
    呐，人家也说清楚这个句干嘛的，读取你的.env文件就是加载喽，然后你就可以getenv函数  
    获取了，所以我们不必再详细这个了吧，看下扩展包就可以了。  
    
    下面我们看` \Illuminate\Foundation\Bootstrap\LoadConfiguration::class,`  
    
    这个配置文件的加载一个流程  
    下面看这个类的内容  
    ```php  
     public function bootstrap(Application $app)
        {
            $items = [];
    
            // First we will see if we have a cache configuration file. If we do, we'll load
            // the configuration items from that file so that it is very quick. Otherwise
            // we will need to spin through every configuration file and load them all.
            /**
            判断是否存在缓存配置文件
             **/
            if (file_exists($cached = $app->getCachedConfigPath())) {
                $items = require $cached;
    
                $loadedFromCache = true;
            }
    
            // Next we will spin through all of the configuration files in the configuration
            // directory and load each one into the repository. This will make all of the
            // options available to the developer for use in various parts of this app.
            $app->instance('config', $config = new Repository($items));
    
            /**
            加载配置目录下的所有配置文件
             **/
            if (! isset($loadedFromCache)) {
                $this->loadConfigurationFiles($app, $config);
            }
    
            // Finally, we will set the application's environment based on the configuration
            // values that were loaded. We will pass a callback which will be used to get
            // the environment in a web context where an "--env" switch is not present.
            $app->detectEnvironment(function () use ($config) {
                return $config->get('app.env', 'production');
            });
    
            /**
            $config 是Illuminate\Config\Repository 类，它支持数组形式访问
             **/
            date_default_timezone_set($config->get('app.timezone', 'UTC'));
    
            mb_internal_encoding('UTF-8');
        }
    ```  
    看第一句的内容就是下面这样，获取bootstrap/cache/config.php文件
    ```php  
     public function configurationIsCached()
        {
            return file_exists($this->getCachedConfigPath());
        }
    
        /**
         * Get the path to the configuration cache file.
         *
         * @return string
         */
        public function getCachedConfigPath()
        {
            return $this->bootstrapPath().'/cache/config.php';
        }
    ```  
    再看` $app->instance('config', $config = new Repository($items));`这简单了吧
    前面都讲了哦，就是注册一个config【保存在数组里】  
    
    再看
    ```php  
     if (! isset($loadedFromCache)) {
                $this->loadConfigurationFiles($app, $config);
            }
    ```  
    没有缓存文件话，就要加载了哦  
    ```php  
    protected function loadConfigurationFiles(Application $app, RepositoryContract $repository)
        {
            $files = $this->getConfigurationFiles($app);
    
            /**
            如果配置目录下不存在app配置文件则运行出错
             **/
            if (! isset($files['app'])) {
                throw new Exception('Unable to load the "app" configuration file.');
            }
    
            /**
            运行配置目录下的所有配置文件
             **/
            foreach ($files as $key => $path) {
                /**
                将配置文件名和文件Illuminate\Config\Repository
                最终保存在该类下的item[]数组里
                 **/
                $repository->set($key, require $path);
            }
        }
    ```  
    接着看这个方法的第一句代码  
    ```php  
    protected function getConfigurationFiles(Application $app)
        {
            $files = [];
            /**
            得到配置文件根目录
             **/
            $configPath = realpath($app->configPath());
    
            /**
            Finder类组件https://symfony.com/doc/current/components/finder.html
    
             **/
            foreach (Finder::create()->files()->name('*.php')->in($configPath) as $file) {
                $directory = $this->getNestedDirectory($file, $configPath);
    
                $files[$directory.basename($file->getRealPath(), '.php')] = $file->getRealPath();
            }
    
            ksort($files, SORT_NATURAL);
    
            return $files;
        }
    ```  
    得到配置文件的目录，没有问题吧，然后它用symfony的Finder包，我们来看看这包  
    ![finder](images/crud/13.png),下面我们去看看这包咋用的啊   
    ![finder](images/crud/14.png)  
    应该看得懂吧，这么简单的事情，是不是，循环配置文件，并得到所有的配置文件返回   
    
    然后运行如下代码  
    ```php  
     foreach ($files as $key => $path) {
                    /**
                    将配置文件名和文件Illuminate\Config\Repository
                    最终保存在该类下的item[]数组里
                     **/
                    $repository->set($key, require $path);
                }
    ```
    我们来看Repository这家伙的结构吧  
    实现了ArrayAccess接口以及它自己定义的接口，简单吧  
    ![repository](images/crud/15.png)    
    下面我们看它是怎么set进去的，`$key  `是配置文件的名称，后面路径得到的是配置文件的数组  
    
    【Repository类set方法】
    ```php  
     public function set($key, $value = null)
        {
            $keys = is_array($key) ? $key : [$key => $value];
    
            foreach ($keys as $key => $value) {
                Arr::set($this->items, $key, $value);
            }
        }
    ```  
    简单吧，就是保存在`$this->items`里，要不看看Arr::set方法【也简单啦】
    【Arr::set方法】
    ```php  
     public static function set(&$array, $key, $value)
        {
            if (is_null($key)) {
                return $array = $value;
            }
    
            $keys = explode('.', $key);
    
            while (count($keys) > 1) {
                $key = array_shift($keys);
    
                // If the key doesn't exist at this depth, we will just create an empty array
                // to hold the next value, allowing us to create the arrays to hold final
                // values at the correct depth. Then we'll keep digging into the array.
                if (! isset($array[$key]) || ! is_array($array[$key])) {
                    $array[$key] = [];
                }
    
                $array = &$array[$key];
            }
    
            $array[array_shift($keys)] = $value;
    
            return $array;
        }
    ```  
    所以到这里够清晰了吧，没有必要再看它了，总结就是
    $app【'config'】 = new Repository();
    Repository->items【配置文件名】=【配置项】就这样保存的，你服了吧  
    
    下面来看` \Illuminate\Foundation\Bootstrap\HandleExceptions::class,`这个异常类的加载流程  
    下面是它的内容  
    ```php  
     public function bootstrap(Application $app)
        {
            $this->app = $app;
    
            error_reporting(-1);
    
            set_error_handler([$this, 'handleError']);
    
            set_exception_handler([$this, 'handleException']);
    
            register_shutdown_function([$this, 'handleShutdown']);
    
            if (! $app->environment('testing')) {
                ini_set('display_errors', 'Off');
            }
        }
    ```  
    来看看第二句，它的功能是打开的所有报错 
    [reporting](https://www.php.net/manual/zh/function.error-reporting.php) 
    第三句简单吧，就是设置错误处理自定义函数，它会得到错误信息，行号，文件这些 
    [handler](https://www.php.net/manual/zh/function.set-error-handler.php)  
    接着看四句吧，就是用来设置异常自定义处理函数，它能捕获到异常信息 
    [exception](https://www.php.net/manual/zh/function.set-exception-handler.php) 
    
    第五句就是设置php运行结束后，要运行的函数，简单吧 
    [shutdown](https://www.php.net/manual/zh/function.register-shutdown-function.php)  
    
    那些函数不用看了吧，接着我们继续看  
    
    `\Illuminate\Foundation\Bootstrap\RegisterFacades::class,`  
    来看看的内容吧  
    ```php  
    public function bootstrap(Application $app)
        {
            Facade::clearResolvedInstances();
    
            /**
    
            子门面类伪装具体的类
            门面基类保存Application类，用于实现实例化子门面类对应的具体类
             **/
            Facade::setFacadeApplication($app);
    
            AliasLoader::getInstance(array_merge(
                //得到配置文件app下类别名
                $app->make('config')->get('app.aliases', []),
                //得到缓存目录下的配置别名包即bootstrap/cache/packages.php
                $app->make(PackageManifest::class)->aliases()
            ))->register();
    
            /**
            将框架的所有门面【伪装类】配置文件里配置好的
            保存在$this->aliases[]数组里
            当调用门面【伪装类】会自动触发转换为其别名返回
             **/
        }
    ```   
    第一句不用解释吧，它是个门面类【不知道为啥我喜欢叫伪装类】 
    第二句就是保存Application的实例，方便它后面搞坏事  
    第三句，先通过config【这么获取到的不用解释了吧，前面说了】，得到配置文件下的类别名数组  
    包括第三方扩展包`  $app->make(PackageManifest::class)->aliases()`这个怎么来的，前面  
    已经说过了哦【这是加载第三方扩展，想想laravel的控制台应用是怎么运行的？特别是安装第三方扩展包，特别是
    是为laravel写的扩展包时，它会运行package:discover命令并运行Illuminate\Foundation\Console\PackageDiscoverCommand
    并运行其handle方法，然后读取vendor/composer/installed.json文件的内容并写入bootstrap/cache/packages.php文件】   
    
    然后我们来看它是怎么register的 
    ```php  
     public function register()
        {
            if (! $this->registered) {
                $this->prependToLoaderStack();
    
                $this->registered = true;
            }
        }
    ``` 
    呐，它干的坏事  
    ```php  
    protected function prependToLoaderStack()
        {
            /**
            注册自动加载
             * http://php.net/manual/zh/function.spl-autoload-register.php
             **/
            spl_autoload_register([$this, 'load'], true, true);
        }
    ```  
    现在我们来看config/app.config下的alias是什么玩意 
    ![alias](images/crud/16.png)  
    
    所以当我们DB:XXX的时候，它会触发如下代码  
    ```php  
    public function load($alias)
        {
            if (static::$facadeNamespace && strpos($alias, static::$facadeNamespace) === 0) {
                $this->loadFacade($alias);
    
                return true;
            }
    
            /**
            当用户实例化或是调用门面【伪装类】代理类，会设置为别名
            'App' => Illuminate\Support\Facades\App::class,
             如果当用户以App::xxx()方式调用会会触发该类load（）方法
             运行后会设置别名为App返回
             **/
            if (isset($this->aliases[$alias])) {
                return class_alias($this->aliases[$alias], $alias);
            }
        }
    ```   
    看到了吧，给类起个绰号，下面来看这DB门面类的代码吧 
    ```php  
    namespace Illuminate\Support\Facades;
    
    /**
     * @see \Illuminate\Database\DatabaseManager
     * @see \Illuminate\Database\Connection
     */
    class DB extends Facade
    {
        /**
         * Get the registered name of the component.
         *
         * @return string
         */
        protected static function getFacadeAccessor()
        {
            return 'db';
        }
    }
    ```  
    没错你看到它继承的基类了，此时它会触发魔术方法____callStatic  
    我们去看看吧，
    ```php  
     public static function __callStatic($method, $args)
        {
            /**
            static::$resolvedInstance[$name] = static::$app[$name];
            运行后得到Application类的对象，并且调用Application[$name] 该方法会触发ArrayAccess接口并实例化当前的门面子类如Route
             **/
            $instance = static::getFacadeRoot();
    
            if (! $instance) {
                throw new RuntimeException('A facade root has not been set.');
            }
    
            return $instance->$method(...$args);
        }
    ```
    我们看第一句，看到没有，看到Application这个吊毛【它因为实现的ArrayAccess接口】具体怎么  
    跑的看我前面写的哦，我们看第二句，
    ```php  
    public static function getFacadeRoot()
        {
            /**
            得到当前调用的门面伪装类并使用Application实例化返回
             **/
            return static::resolveFacadeInstance(static::getFacadeAccessor());
        }
    ```  
    
    `static::getFacadeAccessor()`就会返回`db`字符串，没有问题吧   
    
    ```php  
     protected static function resolveFacadeInstance($name)
        {
            if (is_object($name)) {
                return $name;
            }
    
            if (isset(static::$resolvedInstance[$name])) {
                return static::$resolvedInstance[$name];
            }
    
            return static::$resolvedInstance[$name] = static::$app[$name];
        }
    ```   
    看到了吧，用static::$app【db】这样玩的，骚的很  
    那它怎么找到db对应的类呢，下面我们来看看最骚的服务提供类是怎么玩的  
   
   
   下面接着看` \Illuminate\Foundation\Bootstrap\RegisterProviders::class,` 
   注册服务提供类 
   ```php  
   namespace Illuminate\Foundation\Bootstrap;
   
   use Illuminate\Contracts\Foundation\Application;
   
   class RegisterProviders
   {
       /**
        * Bootstrap the given application.
        *
        * @param  \Illuminate\Contracts\Foundation\Application  $app
        * @return void
        */
       public function bootstrap(Application $app)
       {
           /**
           注册配置文件里配置的服务提供者类
            **/
           $app->registerConfiguredProviders();
       }
   }

   ```  
   没错它调用的是Application这玩意，下面我们去看看它的具体代码 
   ```php  
    public function registerConfiguredProviders()
       {
           $providers = Collection::make($this->config['app.providers'])
                           ->partition(function ($provider) {
 
                               return Str::startsWith($provider, 'Illuminate\\');
                           });
           $providers->splice(1, 0, [$this->make(PackageManifest::class)->providers()]);
           (new ProviderRepository($this, new Filesystem, $this->getCachedServicesPath()))
                       ->load($providers->collapse()->toArray());
       }
   ```  
   ```php  
   $providers = Collection::make($this->config['app.providers'])
                              ->partition(function ($provider) {
    
                                  return Str::startsWith($provider, 'Illuminate\\');
                              });
   ```  
   `$this->config['app.providers']` 这句简单，就是获取配置文件服务提供类数组，下面我们看这个数组  
   
   【config/app.php的服务提供器，叫服务提供者？随便】  
   ```php  
   
    'providers' => [

        /*
         * Laravel Framework Service Providers...
         */
        Illuminate\Auth\AuthServiceProvider::class,
        Illuminate\Broadcasting\BroadcastServiceProvider::class,
        Illuminate\Bus\BusServiceProvider::class,
        Illuminate\Cache\CacheServiceProvider::class,

        //控制台命令服务提供
        Illuminate\Foundation\Providers\ConsoleSupportServiceProvider::class,
        Illuminate\Cookie\CookieServiceProvider::class,
        Illuminate\Database\DatabaseServiceProvider::class,
        Illuminate\Encryption\EncryptionServiceProvider::class,
        Illuminate\Filesystem\FilesystemServiceProvider::class,
        Illuminate\Foundation\Providers\FoundationServiceProvider::class,
        Illuminate\Hashing\HashServiceProvider::class,
        Illuminate\Mail\MailServiceProvider::class,
        Illuminate\Notifications\NotificationServiceProvider::class,
        Illuminate\Pagination\PaginationServiceProvider::class,
        Illuminate\Pipeline\PipelineServiceProvider::class,
        Illuminate\Queue\QueueServiceProvider::class,
        Illuminate\Redis\RedisServiceProvider::class,
        Illuminate\Auth\Passwords\PasswordResetServiceProvider::class,
        Illuminate\Session\SessionServiceProvider::class,
        Illuminate\Translation\TranslationServiceProvider::class,
        Illuminate\Validation\ValidationServiceProvider::class,
        Illuminate\View\ViewServiceProvider::class,

        /*
         * Package Service Providers...
         */

        /*
         * Application Service Providers...
         */
        App\Providers\AppServiceProvider::class,
        App\Providers\AuthServiceProvider::class,
        // App\Providers\BroadcastServiceProvider::class,
        App\Providers\EventServiceProvider::class,

        /**
        框架在启动的时候，会运行路由服务提供类
        路由服务类，框架在启动的时候会运行该类的boot,或是register方法
        其中会把路由定义文件引入运行【运行实现，采用伪装类完成】
        路由服务模块完成了路由----》控制器调度的实现
         **/
        App\Providers\RouteServiceProvider::class,

    ],

   ```  
   下面来看`Collection`这个类，它实现这些类 
   `class Collection implements ArrayAccess, Arrayable, Countable, IteratorAggregate, Jsonable, JsonSerializable`  
   数组访问式接口，迭代器聚合接口，json序列化接口，传递进去的参数，实例化可以数组，迭代器，json序列化操作  
   
   当然也有ArrayObject接口都可以  
   下面我们继续看代码 
   ```php  
   public static function make($items = [])
       {
           return new static($items);
       }

   ```  
   没错它自己实例化自己 
   ```php  
   public function __construct($items = [])
       {
           $this->items = $this->getArrayableItems($items);
       }
   ```  
   继续 
   ```php  
   protected function getArrayableItems($items)
       {
           if (is_array($items)) {
               return $items;
           } elseif ($items instanceof self) {
               return $items->all();
           } elseif ($items instanceof Arrayable) {
               return $items->toArray();
           } elseif ($items instanceof Jsonable) {
               return json_decode($items->toJson(), true);
           } elseif ($items instanceof JsonSerializable) {
               return $items->jsonSerialize();
           } elseif ($items instanceof Traversable) {
               return iterator_to_array($items);
           }
   
           return (array) $items;
       }
   ```  
   你没有看错，它就是返回一个数组，并保存在这个成员变量里items,下面来看它的方法 
   ```php  
    public function partition($callback)
       {
           $partitions = [new static, new static];
   
           $callback = $this->valueRetriever($callback);
   
           foreach ($this->items as $key => $item) {
               $partitions[(int) ! $callback($item, $key)][$key] = $item;
           }
   
           return new static($partitions);
       }
   ```  
   先来看第二句` $callback = $this->valueRetriever($callback);`  
   继续看 
   ```php  
    protected function valueRetriever($value)
       {
           if ($this->useAsCallable($value)) {
               return $value;
           }
   
           return function ($item) use ($value) {
               return data_get($item, $value);
           };
       }
   ```  
   ```php  
    protected function useAsCallable($value)
       {
           return ! is_string($value) && is_callable($value);
       }

   ```  
   检测后自然返回匿名函数,所以我们继续看 
   ```php  
    foreach ($this->items as $key => $item) {
               $partitions[(int) ! $callback($item, $key)][$key] = $item;
           }
   ```  
   下面来看`return Str::startsWith($provider, 'Illuminate\\');` 
   
   ```php  
    public static function startsWith($haystack, $needles)
       {
           foreach ((array) $needles as $needle) {
               if ($needle !== '' && substr($haystack, 0, strlen($needle)) === (string) $needle) {
                   return true;
               }
           }
   
           return false;
       }
   ```  
   
   检测$provider是否含有'Illuminate\\'字符串，下面详细分析这句话 
   ```php  
    foreach ($this->items as $key => $item) {
    
               $partitions[(int) ! $callback($item, $key)][$key] = $item;
           }
   ```  
   `$callback($item, $key)`功能是检测$item是否含有Illuminate字符串，有时返回true非运算后为0 
   并强制转换为整数此时为$partitions【0】【0】=$item，如果后面检测的依然有这指定的字符串，就会
   继续$partitions【0】【1】=$item，后面的如果没有指定的字符串，就会构成$partitions【1】【0】=$item  
   
   所以它的功功能是把一个数组进行分类，分成含有Illuminate类的数组和不含有此名称的数组就这么简单  
   
   `$providers`所以返回如下内容    
   $providers【0】【0】= Illuminate\xxx    
   $providers【0】【1】= Illuminate\yyy      
   $providers【1】【0】= App\xx    
   $providers【1】【1】= App\yy     
   
   下面继续分析代码 
   ```php  
   $providers->splice(1, 0, [$this->make(PackageManifest::class)->providers()]);
   ```  
   来先看这句`[$this->make(PackageManifest::class)->providers()]`这个类，前面已经说过了，它对应的类是  
   ```php  
   $this->instance(PackageManifest::class, new PackageManifest(
               new Filesystem, $this->basePath(), $this->getCachedPackagesPath()
           ));
   ```  
   是吧，在实例化Application的时候这家伙早就保存在instances【PackageManifest::class】=new PackageManifest(
                                                                                 new Filesystem, $this->basePath(), $this->getCachedPackagesPath()
                                                                             )    
   数组里了，   
   `$this->getCachedPackagesPath()` 这吊毛也说过了它是`return $this->bootstrapPath().'/cache/packages.php';` 
   
   并且前面说过了，安装composer require xxx包【专门为laravel写的扩展包时】会把providers类包括在 
   bootstrap/cache/packages.php里了，
   
   所以我们继续看  
   ```php  
    public function providers()
       {
           return collect($this->getManifest())->flatMap(function ($configuration) {
               return (array) ($configuration['providers'] ?? []);
           })->filter()->all();
       }
   ```              
   我们先看collect函数里的东西， 继续看代码 
   ```php  
    protected function getManifest()
       {
       //如果bootstrap/cache/packages.php文件存在
           if (! is_null($this->manifest)) {
               return $this->manifest;
           }
   
   //不存在则将安装包即composer/installed.json写入它
           if (! file_exists($this->manifestPath)) {
               $this->build();
           }
   
   //返回该文件里的内容即数组
           return $this->manifest = file_exists($this->manifestPath) ?
               $this->files->getRequire($this->manifestPath) : [];
       }
   ```             
   然后再看collect函数 
   ```php  
   function collect($value = null)
       {
           return new Collection($value);
       }
   ```                  
   呐，就是实例化这吊毛，  下面继续看 
   ```php  
    public function flatMap(callable $callback)
       {
           return $this->map($callback)->collapse();
       }
   ```               
   
   继续看喽 
   ```php  
    public function map(callable $callback)
       {
       //获取刚才导入的数组【就是第三方扩展包提供的providers】数组
           $keys = array_keys($this->items);
   
   //运行匿名函数
   //运行的结果判断：return (array) ($configuration['providers'] ?? [])
   //判断第三主扩展包是否含有providers这吊【一般为laravel写扩展包的都会加上这玩意】
           $items = array_map($callback, $this->items, $keys);
   
           return new static(array_combine($keys, $items));
       }
   ```           
   首先我们来看bootstrap/cache/packages.php的内容是长啥样的 
   ```php  
   <?php return [
   
     'dingo/api' => [
                    'providers' => [
                        0 => 'Dingo\\Api\\Provider\\LaravelServiceProvider',
                                    ],
                    'aliases' => [
                           'API' => 'Dingo\\Api\\Facade\\API',
                    ],
     ],
     
     'fideloper/proxy' => 
     array (
       'providers' => 
       array (
         0 => 'Fideloper\\Proxy\\TrustedProxyServiceProvider',
       ),
     ),
     'laravel/tinker' => 
     array (
       'providers' => 
       array (
         0 => 'Laravel\\Tinker\\TinkerServiceProvider',
       ),
     ),
     'nesbot/carbon' => 
     array (
       'providers' => 
       array (
         0 => 'Carbon\\Laravel\\ServiceProvider',
       ),
     ),
     'overtrue/laravel-wechat' => 
     array (
       'providers' => 
       array (
         0 => 'Overtrue\\LaravelWeChat\\ServiceProvider',
       ),
       'aliases' => 
       array (
         'EasyWeChat' => 'Overtrue\\LaravelWeChat\\Facade',
       ),
     ),
     'tymon/jwt-auth' => 
     array (
       'aliases' => 
       array (
         'JWTAuth' => 'Tymon\\JWTAuth\\Facades\\JWTAuth',
         'JWTFactory' => 'Tymon\\JWTAuth\\Facades\\JWTFactory',
       ),
       'providers' => 
       array (
         0 => 'Tymon\\JWTAuth\\Providers\\LaravelServiceProvider',
       ),
     ),
   );
   ```  
   所以我们得出这代码  
   ```php  
   返回是扩展包名=[服务提供类1,服务提供类2]
   return collect($this->getManifest())->flatMap(function ($configuration) {
               return (array) ($configuration['providers'] ?? []);
           })
   ```  
   没有问题吧，很好理解的哦，后面再运行如下代码 
   ```php  
   public function filter(callable $callback = null)
       {
           if ($callback) {
               return new static(Arr::where($this->items, $callback));
           }
   
           return new static(array_filter($this->items));
       }
   ```  
   没干嘛哦，继续 
   ```php 
    public function all()
       {
           return $this->items;
       }
   ```  
   `[$this->make(PackageManifest::class)->providers()]`所以这句话返回的是第三方扩展安装包的 
   的providers数组，没有问题吧，我们继续来看 
   ```php  
   public function splice($offset, $length = null, $replacement = [])
       {
           if (func_num_args() == 1) {
               return new static(array_splice($this->items, $offset));
           }
   
           return new static(array_splice($this->items, $offset, $length, $replacement));
       }
   ```  
   先来看看这个array_splice函数的使用吧 
   [array_splic使用示例](https://www.php.net/manual/zh/function.array-splice.php)  
   本函数的功能是将config/app.php的providers数组和第三方扩展包的进行合并并保存返回Collection实例  
   
   我们继续来看代码吧 
   ```php 
   (new ProviderRepository($this, new Filesystem, $this->getCachedServicesPath()))
                       ->load($providers->collapse()->toArray()); 
   ```  
   来，我们来看这吊吧
   ```php  
   public function collapse()
       {
           return new static(Arr::collapse($this->items));
       }
   ```  
   
   呐，继续看 
   ```php  
   public static function collapse($array)
       {
           $results = [];
   
           foreach ($array as $values) {
               if ($values instanceof Collection) {
                   $values = $values->all();
               } elseif (! is_array($values)) {
                   continue;
               }
   
               $results = array_merge($results, $values);
           }
   
           return $results;
       }
   ```  
   首先说一下框架的服务提供者和第三方的服务提供者合并之后是大概这样的 
   ```php  
   $proviers = [
            0>[
                'Illuminate\xxx',
                'Illuminate\yyy'
            ],
            1=>[
                'App\xxx',
                'App\yyy'
            ],
            '第三方扩展包名'=>[
                'xxxx',
                'xxxx'
            ],
            ....
   ];
   ```  
   这个函数的功能非常简单，就是让它变成一维数组！！！让你以我们回到原来的代码看 
   ```php  
   //第一个参数是Application不用说
   //第二个参数是文件系统对象
   //第三个参数得到的是bootstrap//cache/services.php文件
   //第四个参数是config/app.php的providers数组和第三方扩展包的providers数组处理好的一维数组
   
   (new ProviderRepository($this, new Filesystem, $this->getCachedServicesPath()))
                       ->load($providers->collapse()->toArray())
   ```  
   
   接下来，我们看load动作 
   ```php  
   public function load(array $providers)
       {
           $manifest = $this->loadManifest();
           if ($this->shouldRecompile($manifest, $providers)) {
               $manifest = $this->compileManifest($providers);
           }
           foreach ($manifest['when'] as $provider => $events) {
               $this->registerLoadEvents($provider, $events);
           }
           foreach ($manifest['eager'] as $provider) {
               $this->app->register($provider);
           }
           $this->app->addDeferredServices($manifest['deferred']);
       }
   ```  
   我们来分析第一句代码` $manifest = $this->loadManifest();` 
   ```php 
   public function loadManifest()
       {
           
           if ($this->files->exists($this->manifestPath)) {
              
               $manifest = $this->files->getRequire($this->manifestPath);
   
               if ($manifest) {
                   return array_merge(['when' => []], $manifest);
               }
           }
       }
   ```  
   检测bootstrap/cache/services.php文件是否存在，并加载返回 
   继续第二句 
   ```php  
   public function shouldRecompile($manifest, $providers)
       {
       //如果bootstrap/cache/servies.php存在的话一般$manifest是为真的
       //所以如果这文件不存在，或是第三方扩展包又增加【就是你手痒了安装了新的扩展包】
           return is_null($manifest) || $manifest['providers'] != $providers;
       }
   ```  
   它就会重新再处理，我们来看看吧`$manifest = $this->compileManifest($providers);`  
   
   ```php  
   protected function compileManifest($providers)
       {
           
           $manifest = $this->freshManifest($providers);
   
           foreach ($providers as $provider) {
           
           //实例化服务提供类
               $instance = $this->createProvider($provider);
   
   //判断deferred是否为true
   //如果提供了额外的provides数组则是添加
   $mainifest['deferred']['xxx']=服务提供类名
   //如果没有提供provies数组，但是deferred=true的就调用它的when方法
   //如果不是延迟的话就是直接是
   //$mainifest['eager'][]=服务提供类
               if ($instance->isDeferred()) {
                   foreach ($instance->provides() as $service) {
                       $manifest['deferred'][$service] = $provider;
                   }
   
                   $manifest['when'][$provider] = $instance->when();
               }
   
               else {
                   $manifest['eager'][] = $provider;
               }
           }
   
           return $this->writeManifest($manifest);
       }
   ```  
   看看第一句呗 
   ```php  
   protected function freshManifest(array $providers)
       {
           return ['providers' => $providers, 'eager' => [], 'deferred' => []];
       }
   ```  
   就是组装返回这样的吊东西，分别是providers,eager【饥渴？】，deferred【延迟】 
   继续 
   ```php  
   public function createProvider($provider)
       {
           return new $provider($this->app);
       }
       没错就是实例化，实例化服务提供类
   ```  
   这里我们找几个服务提供类看看  
   ```php  
   namespace Illuminate\Foundation\Providers;
   
   use Illuminate\Support\AggregateServiceProvider;
   use Illuminate\Database\MigrationServiceProvider;
   
   class ConsoleSupportServiceProvider extends AggregateServiceProvider
   {
       /**
        * Indicates if loading of the provider is deferred.
        *
        * @var bool
        */
       protected $defer = true;
   
       /**
        * The provider class names.
        *
        * @var array
        */
       protected $providers = [
           ArtisanServiceProvider::class,
           MigrationServiceProvider::class,
           ComposerServiceProvider::class,
       ];
   }
   
   父亲类方法
   public function isDeferred()
       {
           return $this->defer;
       }

   ```  
   
   最终写入bootstrap/cache/services.php文件里 
   ```php  
    public function writeManifest($manifest)
       {
           if (! is_writable(dirname($this->manifestPath))) {
               throw new Exception('The bootstrap/cache directory must be present and writable.');
           }
   
           $this->files->put(
               $this->manifestPath, '<?php return '.var_export($manifest, true).';'
           );
   
           return array_merge(['when' => []], $manifest);
       }  
   ```  
   
   所以得出当安装了第三方扩展包时【为laravel写的扩展包】或是刚刚安装框架的时候会将服务提供类 
   写入bootstrap/cache/services.php文件里  
   ```php  
    if ($this->shouldRecompile($manifest, $providers)) {
               $manifest = $this->compileManifest($providers);
           }
   ```  
   并且服务提供类分2种情况，一种是延迟加载的类，一种是立马要运行的    
   $providers【deferred】【xxx】=服务提供类      
   $providers【when】【】=服务提供类      
   $providers【eager】【】=服务提供类     
   
   所以bootstrap/cache/servies.php文件里的内容大概长这样     
   $providers = 【    
        'providers'=>【    
             0 => 'Illuminate\\Auth\\AuthServiceProvider',    
                1 => 'Illuminate\\Broadcasting\\BroadcastServiceProvider',    
                2 => 'Illuminate\\Bus\\BusServiceProvider',    
        】,    
        'eager'=>【    
            0 => 'Illuminate\\Auth\\AuthServiceProvider',    
            1 => 'Illuminate\\Cookie\\CookieServiceProvider',   
            2 => 'Illuminate\\Database\\DatabaseServiceProvider',    
        】,    
        'deferred' => 【    
            'Illuminate\\Broadcasting\\BroadcastManager' => 'Illuminate\\Broadcasting\\BroadcastServiceProvider',    
                'Illuminate\\Contracts\\Broadcasting\\Factory' => 'Illuminate\\Broadcasting\\BroadcastServiceProvider',    
                'Illuminate\\Contracts\\Broadcasting\\Broadcaster' => 'Illuminate\\Broadcasting\\BroadcastServiceProvider',    
                'Illuminate\\Bus\\Dispatcher' => 'Illuminate\\Bus\\BusServiceProvider',     
        】,    
        'when' => 【    
         【     
            'Illuminate\\Broadcasting\\BroadcastServiceProvider' =>     
            【   
            】,     
            'Illuminate\\Bus\\BusServiceProvider' =>     
            【    
            】,   
        】    
   】;      
    
    
   下面我们来看when服务提供类的处理流程吧      
   ```php  
   foreach ($manifest['when'] as $provider => $events) {
               $this->registerLoadEvents($provider, $events);
           }
   ```  
   
   ```php  
    protected function registerLoadEvents($provider, array $events)
       {
           if (count($events) < 1) {
               return;
           }
   
           $this->app->make('events')->listen($events, function () use ($provider) {
               $this->app->register($provider);
           });
       }
   ```  
   如果当前服务提供类没有相应的事件，就不运行了哦，如果有的话【前面已经说过事件的注册流程了】
   就会把事件=服务类操作注册在事件数组里就是这样
   ` $this->listeners[$event][] = $this->makeListener($listener);`  
   $this->listeners【服务提供类的事件名称】【】=服务提供类匿名函数(){$this->app->register($provider)}  
   
   很清晰，当你去调用事件时，这个对应的匿名服务提供类才地运行，懂了吧  
   
   我们继续看代码吧 
   ```php  
    foreach ($manifest['eager'] as $provider) {
               $this->app->register($provider);
           }
   ```  
   这个就直接运行服务提供类  
   
   ```php  
   public function register($provider, $options = [], $force = false)
       {
           if (($registered = $this->getProvider($provider)) && ! $force) {
               return $registered;
           }
           if (is_string($provider)) {
               
               $provider = $this->resolveProvider($provider);
           }
           if (method_exists($provider, 'register')) {
               $provider->register();
           }
           $this->markAsRegistered($provider);
           if ($this->booted) {
               $this->bootProvider($provider);
           }
   
           return $provider;
       }
   ```  
   先看第一句吧 
   ```php  
   public function getProvider($provider)
       {
           return array_values($this->getProviders($provider))[0] ?? null;
       }
   ```  
   这一句不用看了，没什么可看的，继续 
   ```php  
   if (is_string($provider)) {
               //实例化服务提供者类
               $provider = $this->resolveProvider($provider);
           }
   ```  
   没错，实例化 
   ```php  
   public function resolveProvider($provider)
       {
           return new $provider($this);
       }
   ```  
   运行服务提供类的register方法 
   ```php  
   if (method_exists($provider, 'register')) {
               $provider->register();
           }
   ```  
   继续看 
   ```php  
   protected function markAsRegistered($provider)
       {
           $this->serviceProviders[] = $provider;
   
           $this->loadedProviders[get_class($provider)] = true;
       }
   ```  
   已经实例化过的服务提供类【就不在实例化了，就是前面我说的没什么可看的那玩意】 
   
   ```php  
    if ($this->booted) {
               $this->bootProvider($provider);
           }
   
           return $provider;
   ```  
   最后一句暂时没有必要看了，因为booted=false时，它是没有机会跑的  
   
   自此服务提供类的运行流程是      
   1、加载config/app.php的providers数组+第三方扩展包【bootstrap/cache/packages.php】的
   providers数组合并       
   2、合并后处理成为一维数组并给ProviderRepository.php类处理       
   3、它会判断bootstrap/cache/servies.php里否有东西，或是说它的内容是否对等于现在加载的providers数组 
   否则它会再写入该文件更新掉，并返回  
   同时它还会分类【分成延迟加载的服务提供类+及时加载的提供类】      
   4、运行服务提供类的register方法     
   
   5、bootstrap/cache/packages.php的内容由用户安装composer require/remove时自动更新      
   
   下面继续来看Kernel里的最后一个吧  
   ` \Illuminate\Foundation\Bootstrap\BootProviders::class,`  
   
   下面看它的样子 
   ```php  
   namespace Illuminate\Foundation\Bootstrap;
   
   use Illuminate\Contracts\Foundation\Application;
   
   class BootProviders
   {
       /**
        * Bootstrap the given application.
        *
        * @param  \Illuminate\Contracts\Foundation\Application  $app
        * @return void
        */
       public function bootstrap(Application $app)
       {
           $app->boot();
       }
   }
   ```     
   
   没错，它运行的是Application->boot()方法  
   ```php  
    public function boot()
       {
           if ($this->booted) {
               return;
           }
   
           // Once the application has booted we will also fire some "booted" callbacks
           // for any listeners that need to do work after this initial booting gets
           // finished. This is useful when ordering the boot-up processes we run.
           $this->fireAppCallbacks($this->bootingCallbacks);
   
           array_walk($this->serviceProviders, function ($p) {
               $this->bootProvider($p);
           });
   
           $this->booted = true;
   
           $this->fireAppCallbacks($this->bootedCallbacks);
       }
   ```  
   来看看这句吧，虽然目前它没有什么用 
   ```php  
    $this->fireAppCallbacks($this->bootingCallbacks);
    protected function fireAppCallbacks(array $callbacks)
        {
            foreach ($callbacks as $callback) {
                call_user_func($callback, $this);
            }
        }
   ```  
   
   不用解释了吧,继续  
   ```php  
    array_walk($this->serviceProviders, function ($p) {
               $this->bootProvider($p);
           });
   ```  
   `$this->serviceProviders`这是已经实例化的服务提供类 
   ```php  
   protected function bootProvider(ServiceProvider $provider)
       {
           if (method_exists($provider, 'boot')) {
               return $this->call([$provider, 'boot']);
           }
       }
   ```  
   运行服务提供类的boot方法，至此，Kernel的handle方法  
   ```php  
   protected function sendRequestThroughRouter($request)
       {
           /**
           将当前的请求对象进行绑定，绑定到Application类的对象下
            **/
           $this->app->instance('request', $request);
   
           Facade::clearResolvedInstance('request');
   
           /**
           循环运行本类的成员$this->$bootstrappers[]下的成员数组
            **/
           $this->bootstrap();
   
           return (new Pipeline($this->app))
                       ->send($request) //加载全局中间件
                       ->through($this->app->shouldSkipMiddleware() ? [] : $this->middleware)
                       ->then($this->dispatchToRouter());
   
           /**
           $this->dispatchToRouter() 控制器运行之后返回的响应，响应由Symfony的组件完成
            **/
       }
   ```
   ` $this->bootstrap();`的功能就全部分析完成了，下面我们继续  
   
- 路由服务的完整流程  
    前面已经详细解释过这些服务是怎么运行的，现在我们去看路由注册的原理  
    
    来我们看看它的源码  
    ```php  
    <?php
    
    namespace App\Providers;
    
    use Illuminate\Support\Facades\Route;
    use Illuminate\Foundation\Support\Providers\RouteServiceProvider as ServiceProvider;
    
    class RouteServiceProvider extends ServiceProvider
    {
        /**
         * This namespace is applied to your controller routes.
         *
         * In addition, it is set as the URL generator's root namespace.
         *
         * @var string
         */
        protected $namespace = 'App\Http\Controllers';
    
        /**
         * Define your route model bindings, pattern filters, etc.
         *
         * @return void
         */
        public function boot()
        {
            //
    
            parent::boot();
        }
    
        /**
         * Define the routes for the application.
         *
         * @return void
         */
        public function map()
        {
            $this->mapApiRoutes();
    
            $this->mapWebRoutes();
    
            //
        }
    
        /**
         * Define the "web" routes for the application.
         *
         * These routes all receive session state, CSRF protection, etc.
         *
         * @return void
         */
        protected function mapWebRoutes()
        {
            Route::middleware('web')
                 ->namespace($this->namespace)
                 ->group(base_path('routes/web.php'));
        }
    
        /**
         * Define the "api" routes for the application.
         *
         * These routes are typically stateless.
         *
         * @return void
         */
        protected function mapApiRoutes()
        {
            Route::prefix('api')
                 ->middleware('api')
                 ->namespace($this->namespace)
                 ->group(base_path('routes/api.php'));
        }
    }

    ```  
    先看boot方法【怎么运行的，前面说过了哦】    
    
    继续看它的父类方法  
    ```php  
    public function boot()
        {
            $this->setRootControllerNamespace();
    
            if ($this->app->routesAreCached()) {
                $this->loadCachedRoutes();
            } else {
                $this->loadRoutes();
    
                $this->app->booted(function () {
                    $this->app['router']->getRoutes()->refreshNameLookups();
                    $this->app['router']->getRoutes()->refreshActionLookups();
                });
            }
        }
    ```
    来来，先看看第一句`$this->setRootControllerNamespace();`  
    看看这骚毛的代码吧  
    ```php  
     protected function setRootControllerNamespace()
        {
            if (! is_null($this->namespace)) {
                $this->app[UrlGenerator::class]->setRootControllerNamespace($this->namespace);
            }
        }
    ```  
    我们看看`UrlGenerator::class`这家伙在哪里啊？前面说过的了，看看呗  
    【Application实例化自动加载的路由注册服务】 
    ` $this->register(new RoutingServiceProvider($this));` 有印象吗各位？ 
    它的内容就是这样    
    
    ```php  
     public function register()
        {
            /**
            将router= function(){return Router($app['events'], $app);} key,value键值
            形式保存在Application->bindings[]里
             **/
            $this->registerRouter();
    
            $this->registerUrlGenerator();
    
            $this->registerRedirector();
    
            $this->registerPsrRequest();
    
            $this->registerPsrResponse();
    
            $this->registerResponseFactory();
    
            $this->registerControllerDispatcher();
        }
                    'url'                  => [\Illuminate\Routing\UrlGenerator::class, \Illuminate\Contracts\Routing\UrlGenerator::class],
    ```    
    当它调用时会获得url字符，就会运行如下代码  
    ```php  
    function ($app) {
                $routes = $app['router']->getRoutes();
    
                // The URL generator needs the route collection that exists on the router.
                // Keep in mind this is an object, so we're passing by references here
                // and all the registered routes will be available to the generator.
                $app->instance('routes', $routes);
    
                $url = new UrlGenerator(
                    $routes, $app->rebinding(
                        'request', $this->requestRebinder()
                    )
                );
    
                $url->setSessionResolver(function () {
                    return $this->app['session'];
                });
    
                // If the route collection is "rebound", for example, when the routes stay
                // cached for the application, we will need to rebind the routes on the
                // URL generator instance so it has the latest version of the routes.
                $app->rebinding('routes', function ($app, $routes) {
                    $app['url']->setRoutes($routes);
                });
    
                return $url;
    ```  
    
    所以运行如下代码  
    ```php  
    /**
         * Set the root controller namespace.
         *设置路由的命名空间 【路由模块】
         * @param  string  $rootNamespace
         * @return $this
         */
        public function setRootControllerNamespace($rootNamespace)
        {
            $this->rootNamespace = $rootNamespace;
    
            return $this;
        }
    }
    ```  
    
    继续看`$this->app->routesAreCached()`   
    ```php  
    public function routesAreCached()
        {
            return $this['files']->exists($this->getCachedRoutesPath());
        }
    
        /**
         * Get the path to the routes cache file.
         *
         * @return string
         */
        public function getCachedRoutesPath()
        {
            return $this->bootstrapPath().'/cache/routes.php';
        }
    ```  
    就是检测路由缓存文件是否存在，简单，再继续看 
    `$this->loadCachedRoutes();`来看看它的代码 
    
    ```php  
     protected function loadCachedRoutes()
        {
            $this->app->booted(function () {
                require $this->app->getCachedRoutesPath();
            });
        }
    ```
    接着看 
    ```php  
     public function booted($callback)
        {
            $this->bootedCallbacks[] = $callback;
    
            if ($this->isBooted()) {
                $this->fireAppCallbacks([$callback]);
            }
        }
    ```  
    再接着来看 
    ```php  
    protected function fireAppCallbacks(array $callbacks)
        {
            foreach ($callbacks as $callback) {
                call_user_func($callback, $this);
            }
        }
    ```  
    路由文件是PHP代码文件，引入` require $this->app->getCachedRoutesPath();`即可运行  
    【自然会完成路由的注册功能】    
    所以Application->booted()就是运行传递进去的匿名函数  
    下面接着看，如果没有路由缓存文件话  
    ```php  
    $this->loadRoutes();
    ```
    
    呐，接着看，
    ```php  
    protected function loadRoutes()
        {
            if (method_exists($this, 'map')) {
                $this->app->call([$this, 'map']);
            }
        }
    ```
    这个call不用看了吧，就是运行指定类的指定方法，它非要写一堆封装，为了好玩吧  
    下面它运行app\Providers\RouteServiceProvider.php的map方法了，看看它的内容吧 
    ```php  
    public function map()
        {
            $this->mapApiRoutes();
            $this->mapWebRoutes();
    
        }
    ```  
    你没有看错，它负责web路由和api路由文件的加载【加载并运行】  
    下面我们继续看这吊毛的表演吧  
    先看web路由的加载吧  
    
    ```php  
     protected function mapWebRoutes()
        {
            Route::middleware('web')
                 ->namespace($this->namespace)
                 ->group(base_path('routes/web.php'));
        }
    ```  
    首先看到Route这家伙了,这家伙怎么加载的啊【看前面DB 1978行左右的加载流程】，这个Route是个  
    门面类【靠起绰号来使用的】   
    
    那么这个Route到底是哪个老表呢？前面已经说过在实例化Application时它就注册【保存】了 
    ```php  
    $this->app->singleton('router', function ($app) {
                return new Router($app['events'], $app);
            });
    ```  
    没错，它就是`Illuminate\Routing\Router`这吊毛，那吧既然实例化了，我们就去看看它的构造函数  
    
    构造函数 
    ```php  
    public function __construct(Dispatcher $events, Container $container = null)
        {
            $this->events = $events;//调度器
            $this->routes = new RouteCollection;//路由集合对象
            $this->container = $container ?: new Container;//应用容器
        }
    ```  
    好吧，我们返回到方法`middleware('web')`呐，中间件方法，参数是`web`，我们继续来看的内容 
    当然了这个类【 `Illuminate\Routing\Router`】并没有这个方法的处理函数，所以我们看这个类的  
    魔术方法喽  
    ```php  
     public function __call($method, $parameters)
        {
            if (static::hasMacro($method)) {
                return $this->macroCall($method, $parameters);
            }
    
            if ($method == 'middleware') {
                return (new RouteRegistrar($this))->attribute($method, is_array($parameters[0]) ? $parameters[0] : $parameters);
            }
    
            return (new RouteRegistrar($this))->attribute($method, $parameters[0]);
        }
    ```  
    第一句我们先不看【可以看看PHP如何扩展宏前面我写过一篇介绍了不重复了】 
    继续第二句，它肯定第二句了，所以我们看看它是怎么玩的啊  
    ` return (new RouteRegistrar($this))->attribute($method, is_array($parameters[0]) ? $parameters[0] : $parameters);`  
    
    首先`$this`=`Illuminate\Routing\Router`没有问题 
    $method=middleware没有问题吧 
    $parameters=web也没有问题，继续来看这个类，它实例化了嘛所以看它的构造函数   
    【Illuminate\Routing\RouteRegistrar】
    ```php  
    namespace Illuminate\Routing;
    
    use Closure;
    use BadMethodCallException;
    use Illuminate\Support\Arr;
    use InvalidArgumentException;
    
    class RouteRegistrar  
    public function __construct(Router $router)
        {
            $this->router = $router;
        }
    ```  
    
    好了，没问题，继续看它的方法 
    ```php  
    public function attribute($key, $value)
        {
            if (! in_array($key, $this->allowedAttributes)) {
                throw new InvalidArgumentException("Attribute [{$key}] does not exist.");
            }
    
            
            $this->attributes[Arr::get($this->aliases, $key, $key)] = $value;
    
            return $this;
        }
    ```  
    首先key=middleware目前 ，$value=web，我们看看数组的内容是  
    ```php  
     protected $allowedAttributes = [
            'as', 'domain', 'middleware', 'name', 'namespace', 'prefix',
        ];
    ```  
    表示路由Route【代替Illuminate\Routing\Router】它可以调用以上那些方法  
    所以继续`$this->attributes[Arr::get($this->aliases, $key, $key)] = $value;`  
    这个Arr看看呗 ，来看看它的骚操作  
    ```php  
     public static function get($array, $key, $default = null)
        {
        // return is_array($value) || $value instanceof ArrayAccess; 
        //判断不是数组或是ArrayAccess类就直接运行value方法
        //好吧，看看value方法吧
            if (! static::accessible($array)) {
                return value($default);
            }
    
    //不用解释
            if (is_null($key)) {
                return $array;
            }
    
    /**
   判断该数组是否存在指定的索引key
    **/
            if (static::exists($array, $key)) {
                return $array[$key];
            }
    
    //没有.符号的话直接返回$default喽
            if (strpos($key, '.') === false) {
                return $array[$key] ?? value($default);
            }
    
            foreach (explode('.', $key) as $segment) {
                if (static::accessible($array) && static::exists($array, $segment)) {
                    $array = $array[$segment];
                } else {
                    return value($default);
                }
            }
    
            return $array;
        }
    ```  
    看看value函数吧 
    ```php  
    function value($value)
        {
            return $value instanceof Closure ? $value() : $value;
        }
    ```  
    是匿名函数就直接运行返回，否则返回原来的内容   
    再看看呗  
    ```php  
     public static function exists($array, $key)
            {
                if ($array instanceof ArrayAccess) {
                    return $array->offsetExists($key);
                }
        
                return array_key_exists($key, $array);
            }
    ```  
    
    所以分析了半天它的结果就是` $this->attributes[Arr::get($this->aliases, $key, $key)] = $value;` 
    $this->attributes【middleware】=>web    
    然后继续看呗  
    ` ->namespace($this->namespace)`  
    同样触发这个类【Illuminate\Routing\RouteRegistrar】的魔术回调 
    ```php  
    public function __call($method, $parameters)
        {
            if (in_array($method, $this->passthru)) {
                return $this->registerRoute($method, ...$parameters);
            }
    
            if (in_array($method, $this->allowedAttributes)) {
                if ($method == 'middleware') {
                    return $this->attribute($method, is_array($parameters[0]) ? $parameters[0] : $parameters);
                }
    
                return $this->attribute($method, $parameters[0]);
            }
    
            throw new BadMethodCallException("Method [{$method}] does not exist.");
        }
    ```  
    此时$method=namespace,$parameters='App\Http\Controllers'  
    我们先看第一句吧判断数组 
    ```php  
     protected $passthru = [
            'get', 'post', 'put', 'patch', 'delete', 'options', 'any',
        ];
    ```  
    显然不成立，继续看第二句 
    ```php  
    if (in_array($method, $this->allowedAttributes)) {
                if ($method == 'middleware') {
                    return $this->attribute($method, is_array($parameters[0]) ? $parameters[0] : $parameters);
                }
    
    //最终运行这句
                return $this->attribute($method, $parameters[0]);
            }
    ```
    所以得到结果如下  
    $this->attributes【middleware】=>web   
    $this->attributes【namepspace】=>'App\Http\Controllers'  
    
    再来继续看 
    ```php  
    group(base_path('routes/web.php'))
    ```  
    
    没错它要加载routes/web.php文件了，继续看吧  
    ```php  
     public function group($callback)
        {
            $this->router->group($this->attributes, $callback);
        }
    ```     
    没错，它调用了Illuminate\Routing\Router->group()这家伙了   
    我们来看它的代码  
    ```php  
    public function group(array $attributes, $routes)
        {
            $this->updateGroupStack($attributes);
 
            $this->loadRoutes($routes);
    
            array_pop($this->groupStack);
        }
    ```  
    继续看第一句呗  
    ```php  
     protected function updateGroupStack(array $attributes)
        {
            if (! empty($this->groupStack)) {
                $attributes = RouteGroup::merge($attributes, end($this->groupStack));
            }
    
            $this->groupStack[] = $attributes;
        }
    ```  
    
    没错，它就是保存groupStack【】里，继续看第二句吧  
    `$this->loadRoutes($routes);`    
    看看它的源码吧  
    
    ```php  
     protected function loadRoutes($routes)
        {
            if ($routes instanceof Closure) {
                $routes($this);
            } else {
                $router = $this;
    
                require $routes;
            }
        }
    ```  
    如果引入的内容是匿名函数则运行，负责直接运行，此时$routes=routes/web.php文件  
    所以它是直接运行这个文件的，所以我们再在可以去看看路由文件了  
    routes/web.php文件    
    ```php  
    <?php
    
    /*
    |--------------------------------------------------------------------------
    | Web Routes
    |--------------------------------------------------------------------------
    |
    | Here is where you can register web routes for your application. These
    | routes are loaded by the RouteServiceProvider within a group which
    | contains the "web" middleware group. Now create something great!
    |
    */
    
    Route::get('/', function () {
        return view('welcome');
    });
    
    Route::redirect('/here', '/there', 301);
    Route::view('/welcome', 'welcome');
    Route::get('user/{id}', function ($id) {
        return 'User '.$id;
    });
    
    Route::get('posts/{post}/comments/{comment}', function ($postId, $commentId) {
        //
    });
    Route::get('user/{name?}', function ($name = null) {
        return $name;
    });
    
    Route::get('user/{name?}', function ($name = 'John') {
        return $name;
    });
    Route::get('user/profile', function () {
        //
    })->name('profile');
    
    Route::domain('{account}.myapp.com')->group(function () {
        Route::get('user/{id}', function ($account, $id) {
            //
        });
    });
    Route::middleware(['web'])->namespace('Api')->prefix("admin")->group(function (){
    
        Route::get("/test/{id?}","QueueController@index");
    //    Route::get("/test","TestController@index");
    //    Route::get("/test/{id}/{comment?}","TestController@index")->where(['id'=>'\d+']);
    
    });

    ```  
    
    这么多啊，我们看最后一个路由注册就行了  
    
    `Route::middleware(['web'])->namespace('Api')` 这个不需要说了吧，结果保存在  
     $this->attributes【middleware】=>web   
     $this->attributes【namepspace】=>'App\Http\Controllers' 
     $this->attributes【prefix】=>'admin'   
     当传输重复的时候   
     Arr:get前面的源码自己看 
     ```php  
     
        if (static::exists($array, $key)) {
            return $array[$key];
        }
     ```    
     
     后面的group动作没有必要说了吧，来看看它的动作 
     ```php  
     if ($routes instanceof Closure) { 
     //直接运行匿名函数，并传递参数
                 $routes($this);
             } else {
                 $router = $this;
     
                 require $routes;
             }
     ```   
     下面来看它的get方法  
     ` Route::get("/test/{id?}","QueueController@index");`       
     【Illuminate\Routing\Router】
     ```php  
     public function get($uri, $action = null)
         {
             return $this->addRoute(['GET', 'HEAD'], $uri, $action);
         }
     ```  
     
     继续看呗  
     `$methods=['GET', 'HEAD']`    
     `$uri=/test/{id?}`    
     `$action=QueueController@index`    
     ```php  
     protected function addRoute($methods, $uri, $action)
         {
             return $this->routes->add($this->createRoute($methods, $uri, $action));
         }
     ```  
     
     继续  
     
     ```php  
     protected function createRoute($methods, $uri, $action)
         {
          
             if ($this->actionReferencesController($action)) {
                 $action = $this->convertToControllerAction($action);
             }
     
             $route = $this->newRoute(
                 $methods, $this->prefix($uri), $action
             );
     
          
             if ($this->hasGroupStack()) {
                 $this->mergeGroupAttributesIntoRoute($route);
             }
     
             $this->addWhereClausesToRoute($route);
     
             return $route;
         }

     ```  
     看看第一句 
     ```php  
     protected function actionReferencesController($action)
         {
             if (! $action instanceof Closure) {
                 return is_string($action) || (isset($action['uses']) && is_string($action['uses']));
             }
     
             return false;
         }
     ```
     
     
    
    
    