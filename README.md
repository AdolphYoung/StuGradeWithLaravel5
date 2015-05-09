# StuGradeWithLaravel5

一个简易的学生成绩管理,查询系统,仅供新手学习参考。QQ:215672398 有什么好的想法,或是建议,不明白的都可以来找我讨论。

## 下面,我们开始,我也是从一个新手(伸手党)走过来的,这次自己写一个教程给刚学习laravel,或者感到困惑的同学,我会尽量详细每一个重要的步骤。
* [系统的功能简介](#feature1)
* [需要的一些组件,库](#feature2)
* [一些源文件位置说明](#feature3)
* [运行环境](#feature4)
* [安装](#feature5)
* [详细学习过程](#feature6)

<a name="feature2"></a>
## 功能
* 学生端
    * 登录完善修改个人信息,查看成绩
    * 修改密码,验证邮件(待完善)
* 管理员
    * 登录能查看所有学生信息,包括新增学生,删除学生
    * 能更新学生成绩,没有修改学生信息的权限
    * 下载学生信息Excel,学生成绩Excel
    * 上传学生名单Excel,更新数据库(待完善)
    * 上传学生成绩Excel,更新数据库(待完善)

-----
<a name="feature2"></a>
## 组件链接和文档
* Laravel 5.0(这个大家都懂,最基本的)
* [Bootstrap 3.3.4](http://v3.bootcss.com/)
* gulp,前端自动化工具,(官网被墙了),推荐laravel文档中的安装gulp. [link](http://www.golaravel.com/laravel/docs/5.0/elixir/)
* [TableSorter](http://tablesorter.com/docs/) 表格排序插件
* [Laravel Excel](http://www.maatwebsite.nl/laravel-excel/docs) Excel的导出导入组件

-----
<a name="feature3"></a>
## 说明
* TableSorter ----- resources/js/jquery.tablesorter.min.js
* Bootstarp   ----- 需要引入bootstarp的font文件(不引入会出现一些问题,见下文)
* resources/js/main.js  ----- 系统功能完善js脚本(意思就是自己写的)
* resources/css/main/css ----- 同上

-----
<a name="feature4"></a>
## 环境需要

    PHP >= 5.5.0
    MCrypt PHP Extension
    SQL server(for example MySQL)

-----
<a name="feature4"></a>
## 安装步骤
* [1: 获取源代码](#step1)
* [2: Composer管理依赖关系的工具](#step2)
* [3: 配置文件](#step3)
* [4: 安装gulp工具和Laravel Elixir](#step4)
* [5: 转移数据表,填充数据](#step5)
* [6: 开始](#step6)

-----
<a name="step1"></a>
### 1: 下载源代码

    https://github.com/qq215672398/StuGradeWithLaravel5/archive/master.zip

-----
<a name="step2"></a>
### 2: Composer Install

安装[Composer](http://getcomposer.org/) 来管理PHP依赖关系的工具,详细安装参考官网,不过大部分同学这步肯定Ok了。

安装好之后,在项目根目录下执行命令:

    composer update

-----
<a name="step3"></a>
### 3: 修改配置文件

在.env文件中修改

    DB_HOST=localhost
    DB_DATABASE=dbname
    DB_USERNAME=username
    DB_PASSWORD=password

-----
<a name="step4"></a>
### 4: 安装gulp和Elixir

执行以下命令:

    npm install

-----
<a name="step5"></a>
### 5: 得到表,和默认用户数据

依次执行一下命令:

    php artisan migrate
    php artisan db:seed(要是失败的话,可能需要 composer dump-autoload)

现在执行:

    php artisan serve

打开浏览器,输入http://localhost:8000/就可以看到首页呢！

-----
<a name="step6"></a>
### 6: 登录开始

数据库有3个默认用户(当然你可以在数据库里删除,自己添加,这里只是测试)
这里的相关信息可以在 /database/seeds/UserTableSeeder.php 找到

管理员:

    username: 1234567890
    pasword: root

同学:

    username: 1210311232
    pasword: 1210311232

    username: 1210311233
    pasword: 1210311233

-----

<a name="feature6"></a>
## 开始学习
首先,讲讲的我自己的学习思路,拿到一个应用的源代码,我会先看路由,每一个路由对应着什么功能,其中有什么细节,怎么去实现,这是我最希望能学到的。在编码的时候,我个人是路由-功能去完成的,可能这是新手的方法,有大牛能指导一下能更好。所以,我从逐步从路由开始说起,一步一步的编码过程。

####建议,开两个编辑器,一个用来看前面安装好的源代码,一个用来进行下面的学习

安装好laravel,配置好环境之后,我执行了

    php artisan fresh

只留下了laravel自带的主页路由 Route::get('/', 'WelcomeController@index');

然后导入我们的数据表,填充默认数据进行测试。

打开 /database/migrations/._create_users_table.php,修改up方法

    public function up()
    {
        Schema::create('users', function(Blueprint $table)
        {
            $table->integer('id')->unique()->unsigned();  #学号,唯一,取正数
            $table->string('name');                       #姓名
            $table->string('password');                   #密码
            $table->string('phone')->default('');  #手机 默认为空(不是可以为空,值为'')
            $table->string('sex')->default('');    #性别 同上
            $table->string('email')->default('');  #邮箱 同上
            $table->string('pro_class')->default(''); #班级 同上
            $table->boolean('is_admin')->default(0);  #是否为管理员 默认为学生
            $table->rememberToken();
            $table->timestamps();
        });
    }

这里为什么这么写,首先我觉的老师新增学生时候是没有填写他信息的权限的,只能生成学号,姓名。密码,所以其他都默认为空,需要学生登录后自己去填写。

然后在User.php中修改$fillable数组

    protected $fillable = ['name', 'email', 'is_admin', 'password', 'sex', 'phone', 'pro_class'];

什么意思,我也不是很清楚,这样写了储存user到数据库,输出user属性就不会出问题,至于具体为啥,怪我没有深入,下面继续。

下面开始有任何不确定的都可以去看源代码对比

找到WelcomeController.php,可以看到两段代码:

    public function __construct()
    {
        $this->middleware('guest');
    }

    public function index()
    {
        return view('welcome');
    }

上面的构造函数,有什么作用？里面的中间件guest,在Kernel.php 中的routeMiddleware数组里面有注册,它的功能在App\Http\Middleware\RedirectIfAuthenticated.php里面可以看到。

可以理解为登录之后要是还想访问主页,就会自动跳转,跳转细节后面在说。

index方法返回的是welcome页面.

这时候,我们先构建一个基础页面模版,因为后面的每个页面都是需要继承它的.创建 master.blade.php文件.

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title> @yield('title') </title>
        <link rel="stylesheet" href="http://cdn.bootcss.com/bootstrap/3.3.4/css/bootstrap.min.css">
    </head>
    <body>
        <nav class="navbar navbar-default">
            <div class="container">
                <div class="navbar-header">
                    <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
                        <span class="sr-only">Toggle Navigation</span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                    </button>
                    @if(Auth::guest())
                        <a class="navbar-brand" href="/">学生成绩管理</a>
                    @else
                        @if (Auth::user()->is_admin)
                            <a class="navbar-brand" href="/admin">学生成绩管理</a>
                        @else
                            <a class="navbar-brand" href="/">学生成绩管理</a>
                        @endif
                    @endif

                </div>

                <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
                    <ul class="nav navbar-nav">
                        <li><a href="http://www.golaravel.com" target="__blank">Power by laravel5</a></li>
                </ul>
                    <ul class="nav navbar-nav navbar-right">
                        @if (Auth::guest())
                        @else
                            <li class="dropdown">
                                <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-expanded="false">{{ Auth::user()->name }} <span class="caret"></span></a>
                                <ul class="dropdown-menu" role="menu">
                                    <li><a href="{{ url('/logout') }}">退出</a></li>
                                </ul>
                            </li>
                        @endif
                    </ul>
                </div>
            </div>
        </nav>

        <!-- <div class="container">
            @include('flash')
        </div> -->

        @yield('content')

    <!-- script -->
    <script src="http://cdn.bootcss.com/jquery/1.11.2/jquery.min.js"></script>
    <script src="http://cdn.bootcss.com/bootstrap/3.3.4/js/bootstrap.min.js"></script>
    </body>
    </html>

@include('flash')先注释掉,这里暂时还不需要.

创建我们的welcome.blade.php,随便修饰一下(前端不行 :) )

    @extends('master')   {{-- 继承master模版 --}}

    @section('title')   {{-- 对应@yield('title') --}}
        学生成绩管理系统
    @stop

    @section('content')   {{-- 对应@yield('content') --}}
        <div class="container">
            <div class="jumbotron">
                <h2><div class="quote">{{ Inspiring::quote() }}</div></h2>
                <p>同学们登录后先修改相关资料</p>
                <p>查询分数,有疑问咨询管理员</p>
                <p><a class="btn btn-primary btn-lg" href="/login" role="button">点击登录</a></p>
            </div>
        </div>
    @stop

![Index](http://img1.ph.126.net/HmhY3w2qYDWr4RyQKdcfiQ==/6630430048955199565.jpg)

好了,首页已经完成了,来看这三个路由

    Route::get('login', [
    'middleware' => 'guest', 'as' => 'login', 'uses' => 'loginController@loginGet']);
    Route::post('login', [
    'middleware' => 'guest', 'loginController@loginPost']);
    Route::get('logout', [
    'middleware' => 'auth', 'as' => 'logout', 'uses' => 'loginController@logout']);

完成登录登出的功能, 在路由中设置中间件, 过滤一些非法请求,关于中间件,参考[官方文档](www.golaravel.com/laravel/docs/5.0/middleware/)

guest 只允许游客(没登陆的情况下)访问get路由login和post路由login,要是已经登录,就会跳转到相应页面,注意关键词响应。我们登录用户有两种,学生,和管理员,当他们在登录的情况下要想访问这两个路由,肯定会做出不同的响应。即,学生,跳转到学生主页,管理员,跳转到管理员主页.现在来看看RedirectIfAuthenticated.php

    public function handle($request, Closure $next)
    {
        if ($this->auth->check())     <!-- 用户是否登录 -->
        {
            if (!Auth::user()->is_admin) {
                return new RedirectResponse(url('/stu/home'));  <!-- 不是管理员 -->
            } else {
                return new RedirectResponse(url('/admin'));   <!-- 管理员 -->
            }

        }

        return $next($request);
    }

auth 只有登录用户才能访问(这个不知道怎么表达,我就不误人子弟),看下源码 Authenticate.php

    public function handle($request, Closure $next)
    {
        if ($this->auth->guest())    <!-- 没有登录,是游客 -->
        {
            if ($request->ajax())           <!-- 通过ajax来请求 -->
            {
                return response('Unauthorized.', 401);
            }
            else       <!-- 直接请求, 跳转到登录页 -->
            {
                return redirect()->guest('login');
            }
        }

        return $next($request);
    }

也就是说只有登录了才能登出,就是这个意思。

说完了中间件,来看看控制器。loginControlle.php

    /**
     * 返回login视图,登录页面
     */
    public function loginGet()
    {
        return view('login');
    }

    /**
     * 登录响应
     */
    public function loginPost(Request $request)
    {
        $this->validate($request, User::rules());
        $id = $request->get('id');
        $password = $request->get('password');
        if (Auth::attempt(['id' => $id, 'password' => $password], $request->get('remember'))) {
            if (!Auth::user()->is_admin) {
                return Redirect::route('stu_home');
            } else {
                return Redirect::action('Admin\AdminController@index');
            }

        } else {
            return Redirect::route('login')
                ->withInput()
                ->withErrors('学号或者密码不正确,请重试！');
        }
    }

    /**
     * 用户登出
     */
    public function logout()
    {
        if (Auth::check()) {
            Auth::logout();
        }
        return Redirect::route('login');
    }

可以使用validate验证用户输入,在laravel5中使用validate非常方便,注意第二个参数,User::rules(),
这是在User模型中一个静态方法,接着在User.php中加入静态方法。

    protected static function rules()
    {
        return [
            'id' => 'required|digits:10',   <!-- 代表必需填写,10位数字 -->
            'password' => 'required'        <!-- 必填 -->
            ];
    }

验证用户登录使用了Auth::attempt(),这是laravel中自带的验证方法,非常好用,如果验证通过,接着判断是否是管理员,然后分别跳转到不同的url.

    return Redirect::route('stu_home') -- 对应路由名为stu_home的路由
    return Redirect::action('Admin\AdminController@index') -- 对于这个index方法

登出使用的是Auth::logout().

这时候点击登录,laravel会告诉你view(login)不存在,创建login.blade.php文件

    @extends('master')

    @section('title')
        欢迎登录
    @stop

    @section('content')
    <div class="container-fluid">
        <div class="row">
            <div class="col-md-8 col-md-offset-2">
                <div class="panel panel-default">
                    <div class="panel-heading">登录</div>
                    <div class="panel-body">

                        @include('errors.list')

                        {!! Form::open(['url' => '/login', 'class' => 'form-horizontal', 'role' => 'form']) !!}
                            <div class="form-group">
                                {!! Form::label('id', '学号', ['class' => 'col-md-4 control-label']) !!}
                                <div class="col-md-6">
                                    {!! Form::text('id', old('id'), ['class' => 'form-control', 'required']) !!}
                                </div>
                            </div>
                            <div class="form-group">
                                {!! Form::label('password', '密码', ['class' => 'col-md-4 control-label']) !!}
                                <div class="col-md-6">
                                    {!! Form::password('password', ['class' => 'form-control', 'required']) !!}
                                </div>
                            </div>
                            <div class="form-group">
                                <div class="col-md-6 col-md-offset-4">
                                    <div class="checkbox">
                                        <label>
                                            <input type="checkbox" name="remember"> Remember Me
                                        </label>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group">
                                <div class="col-md-6 col-md-offset-4">
                                    {!! Form::submit('Login', ['class' => 'btn btn-primary form-control']) !!}
                                </div>
                            </div>
                        {!! Form::close() !!}
                    </div>
                </div>
            </div>
        </div>
    </div>
    @stop

这时候要是点击登录,还是会报错,因为在laravel5中Illuminate/Html组件被移了,我们可以在composer.json的required数组中加入

    "illuminate/html": "5.0"

接着执行:

    composer update

等待安装完成之后,就能看到我们的登录页面了

![Login](http://img1.ph.126.net/aBpSfUKwCB34SJXOcbz-Lg==/6630121086187793065.jpg)

