---
title: Laravel的架构概念
date: 2017-08-29 06:45:34
tags:
categories: PHP
---
本篇博客从整体的角度来介绍Laravel框架是如何工作的。

<!-- more -->

#### 请求的生命周期(Request Lifecycle)

所有请求的入口点是`public/index.php`文件，所有的请求都会被服务器配置定向到这个文件。`index.php`文件不包含太多代码，相反，它只是用来加载框架的剩余部分。

`index.php`文件加载Composer产生的autoloader定义，接着从`bootstrap/app.php`脚本中获取Laravel应用实例。Laravel执行的第一个动作是创建应用(即服务容器)的实例。

请求要么发送给HTTP kernel，要么发送给console kernel，取决于进入应用的请求类型。这两个kernel作为所有请求必须经过的中心点。HTTP kernel位于`app/Http/Kernel.php`。

HTTP kernel继承自`Illuminate\Foundation\Http\Kernel`类。该类定义了一组启动加载器(bootstrapper)，在请求执行之前会被运行，这些启动加载器r会配置错误处理、配置日志打印、检测应用环境以及执行请求处理之前需要做的其它任务。

HTTP kernel也定义了一系列HTTP中间件，所有的请求被应用处理前必须要通过这些中间件。这些中间件会读写session，验证CSRF token等。

HTTP kernel的handle方法签名非常简单：接收Request，返回Response。将kernel当作是代表整个应用的黑盒，向它传递HTTP请求，它会返回HTTP 响应。

最重要的启动加载器是加载应用的服务提供者(service provider)。应用的所有服务提供者都配置在`config/app.php`文件的`providers`数组中。首先，所有的提供者都会调用`register`方法，一旦所有的服务提供者被注册后，`boot`方法就会被调用。

服务提供者负责启动框架的组件，例如数据库、队列、验证、路由组件等。

一旦应用被启动以及所有的服务提供者被注册，`Request`就会被交给路由器来调度。路由器会将请求调度给路由或者控制器，当然，也会运行特定路由的中间件。

服务提供者才是Laravel应用的关键。应用实例被创建，服务提供者被注册，请求被传递，这样就启动了应用。

#### 服务容器(Service Container)：绑定和解析

服务容器用来管理类的依赖项和执行依赖项注入。依赖项注入：通过构造器或setter方法来将依赖项注入到类中。

几乎所有的绑定(将依赖项类绑定到服务容器中)都是在服务提供者中被注册的。

在服务提供者中，通过`$this->app`属性来获取容器。可以通过`bind`方法来注册绑定，传递类名或接口名，以及会返回类实例的闭包。

		$this->app->bind('HelpSpot\API', function ($app) {
    		return new HelpSpot\API($app->make('HttpClient'));
		});

`singleton`方法将类或接口绑定到容器中，并且只会被解析一次。

		$this->app->singleton('HelpSpot\API', function ($app) {
    		return new HelpSpot\API($app->make('HttpClient'));
		});

`instance`方法用来将存在的对象实例绑定到容器中。

		$api = new HelpSpot\API(new HttpClient);

		$this->app->instance('HelpSpot\API', $api);

有时候类会接收一些注入的类，但也会需要原始值类型，比如正数，这时候可以使用上下文绑定来注入任何值。

		$this->app->when('App\Http\Controllers\UserController')
          ->needs('$variableName')
          ->give($value);

服务容器能够将接口绑定到具体的实现类上：

		$this->app->bind(
    		'App\Contracts\EventPusher',
    		'App\Services\RedisEventPusher'
		);

如果两个类需要相同的接口，但是又希望每个类注入不同的实现，这时候可以使用上下文绑定(Filesytem是接口)：

		$this->app->when(PhotoController::class)
          ->needs(Filesystem::class)
          ->give(function () {
              return Storage::disk('local');
          });

		$this->app->when(VideoController::class)
          ->needs(Filesystem::class)
          ->give(function () {
              return Storage::disk('s3');
          });

有时候，需要解析某种类别(实现相同接口)的所有绑定的类，可以使用`tag`方法来给类分配标签。

		$this->app->bind('SpeedReport', function () {
		});

		$this->app->bind('MemoryReport', function () {
		});

		$this->app->tag(['SpeedReport', 'MemoryReport'], 'reports');
		
		//通过tagged方法来解析
		$this->app->bind('ReportAggregator', function ($app) {
    		return new ReportAggregator($app->tagged('reports'));
		});

使用`make`方法来从容器中解析类的实例，该方法接收希望解析的类名或接口名。

		$api = $this->app->make('HelpSpot\API');

如果所在的代码位置无法获取`$app`变量，则使用全局的`resolve`函数：

		$api = resolve('HelpSpot\API');

如果类的依赖项无法通过容器解析，可以通过将依赖项作为关联数组传递到`makeWith`方法中。

		$api = $this->app->makeWith('HelpSpot\API', ['id' => 1]);

服务容器每次解析对象时，都会发出事件，可以使用`resolving`方法来监听该事件：

		$this->app->resolving(function ($object, $app) {
    		// Called when container resolves object of any type...
		});

		$this->app->resolving(HelpSpot\API::class, function ($api, $app) {
    		// Called when container resolves objects of type "HelpSpot\API"...
		});

#### 服务提供者(Service Provider)

服务提供者是Laravel应用启动加载(bootstrap：注册服务容器绑定、事件监听、中间件、路由等)的中心位置。

`config/app.php`文件包含`providers`数组，这些服务提供者类会被加载。当然，有许多是`deferred`提供者，意味着它们只有在需要的时候才会加载。

所有的服务提供者都继承自`Illuminate\Support\ServiceProvider`。大多数的服务提供者都包含`register`和`boot`方法。在`register`方法中，只应该执行绑定到服务容器的操作，不要执行注册事件监听器、路由等其它操作。

		class RiakServiceProvider extends ServiceProvider{
    		public function register(){
        		$this->app->singleton(Connection::class, function ($app) {
           			 return new Connection(config('riak'));
        		});
    		}
		}

`boot`方法会在其它所有服务提供者被注册后被调用，就是说可以获取其它所有注册的服务。

		class ComposerServiceProvider extends ServiceProvider{
    		public function boot(){
        		view()->composer('view', function () {
            	//
        		});
    		}
		}

所有的服务提供者都是注册在`config/app.php`配置文件中，该文件包含`providers`数组。默认，Laravel核心的服务提供者都被列在该数组中。

如果服务提供者只注册服务容器的绑定，可以选择延迟它的注册，直到需要的时候再注册。为了延迟服务提供者的加载，可以将`defer`属性设置为`true`和定义`provides`方法，该方法应该返回由服务提供者注册的服务容器绑定。

		class RiakServiceProvider extends ServiceProvider{

    		protected $defer = true;

    		public function register(){
        		$this->app->singleton(Connection::class, function ($app) {
            	return new Connection($app['config']['riak']);});
    		}

    		public function provides(){
        		return [Connection::class];
    		}

		}

#### Facade：服务容器中的类的静态接口

Laravel的Facade作为服务容器中底层类的静态代理，所有的facade都定义在`Illuminate\Support\Facades`命名空间中。

```

use Illuminate\Support\Facades\Cache;

Route::get('/cache', function () {
    return Cache::get('key');
});

```

除了facade外，Laravel还包含了一系列辅助(helper)函数，用来执行常见的任务：产生视图，发射事件，调度工作，发送响应。许多辅助函数和其对应的facade执行相同的功能。

```

return View::make('profile');

return view('profile');

```

#### Contract：一系列接口，用来定义核心服务

每个contract都会有对应的实现。

Laravel的facade和辅助函数提供了使用Laravel服务的简单方式，而不必type-hint和从服务容器中解析。

