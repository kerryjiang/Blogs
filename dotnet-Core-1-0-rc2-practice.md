# .NET Core 1.0 RC2 历险之旅

文章背景：对于.NET Core大家应该并不陌生, 从它被 [宣布](https://weblogs.asp.net/scottgu/announcing-open-source-of-net-core-framework-net-core-distribution-for-linux-osx-and-free-visual-studio-community-edition) 到现在已经有1-2年的时间了，其比较重要的一个版本1.0 RC2 也即将发布。.Net Core从一个一个的测试版到现在的RC2，经历了很多个大大小小的变化。特别是在RC1到RC2的更新之中，.NET Core命令行工具（dotnet cli）从 dnx 变为 dotnet，并且废除了 DNVM 和 DNU，使得 .NET Core 的开发变得更为简单，其相关工具链也基本成型。虽然网上关于.NET Core的示例项目不在少数，而且微软官方也提供了不少示例项目，但大多针对的是.NET Core的不同版本，因此很多示例项目并不是能很容易的运行起来。所以我决定写一篇针对RC2这个版本的.NET Core入门文章并提供一些能直接运行的[示例项目](https://github.com/kerryjiang/dotnetcore-samples)。

## 下载安装 .NET Core SDK

从 dotnet cli github项目主页找到最新版的.Net Core SDK下载:

[https://github.com/dotnet/cli](https://github.com/dotnet/cli)

例如 Mac OS X的最新版的.NET Core SDK的下载地址为：

https://dotnetcli.blob.core.windows.net/dotnet/beta/Installers/Latest/dotnet-dev-osx-x64.latest.pkg

安装前请确认当前系统是否已经安装了老版本的.NET Core, 如果已经安装，请先卸载。

如在Mac OS X上已安装的话，请运行如下命令删除：

    sudo rm -rf /usr/local/share/dotnet

在Mac OS X上安装之前请先确保 openssl 已经被安装了：

    brew install openssl
   

## 开发工具 Visual Studio Code 及其 C# 插件安装

如不准备使用VSCode进行开发的话，请忽略此部分。我不确定最新版本的 Visual Studio 2015 Update 2 是否对.NET Core 1.0 RC2有很好的支持。

1. 从官方网站下载安装 VSCode

    https://code.visualstudio.com/

2. 安装VSCode C#插件

    运行VSCode, 然后使用快捷键 ⌘ + P 启动快速打开命令窗口，然后输入如下命令安装C#扩展。最新版的csharp扩展已支持 RC2 的.NET程序的调试。
    
        ext install csharp


## 使用.NET CLI (dotnet) 创建，编译和运行项目

1. 创建项目
   
   首先在控制台／Terminal下进入你要创建项目的目录，然后运行如下命令：

        dotnet new
        
   dotnet cli 创建新项目的时候支持项目类型参数-t，但当前只支持Console参数。:( 
   
   运行之后会生成两个文件
   
        project.json    -   类似于.NET Framework里的项目文件
        Program.cs      -   程序启动入口
        
   使用restore命令下载依赖
   
        dotnet restore
   
   如出现网络错误导致restore失败的情况请重试几次，貌似这种情况比较少。
   
   如发现类似下面的Warning也请不要惊慌，这是由于CLI的版本号与下载下来的.NET Core类库的版本号不一致导致的，这种情况不会影响编译和运行。
   
    warn : Dependency specified was Microsoft.NETCore.App (>= 1.0.0-rc2-3002464) but ended up with Microsoft.NETCore.App 1.0.0-rc2-3002468.

2. 编译项目

   在控制台或Terminal打开项目所在目录，运行如下命令编译：
   
        dotnet build
        
  如出现如下类似编译错误：
  
       error NU1002: The dependency Microsoft.CodeAnalysis.Common 1.2.0-beta1-20160202-02 does not support framework .NETCoreApp,Version=v1.0
       error NU1002: The dependency Microsoft.CodeAnalysis.CSharp 1.2.0-beta1-20160202-02 does not support framework .NETCoreApp,Version=v1.0
       
  出现这个问题的可能原因是NuGet上通过版本号匹配到的依赖包并不能使用，你需要做如下操作之后再重新restore一下。
  
  a. 在项目根目录新增文件 NuGet.config, 并写入以下内容：
  
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <packageSources>
                <!--To inherit the global NuGet package sources remove the <clear/>line below -->
                <clear/>
                <add key="dotnet-core" value="https://dotnet.myget.org/F/dotnet-core/api/v3/index.json"/>
                <add key="api.nuget.org" value="https://api.nuget.org/v3/index.json"/>
            </packageSources>
        </configuration>
        
  b. 打开 project.json 文件将 dependencies 节点中的 Microsoft.NETCore.App 的版本好更改为 1.0.0-rc2-*：
  
        "dependencies": {
            "Microsoft.NETCore.App": {
                "type": "platform",
                "version": "1.0.0-rc2-*"
            }
        }
        
  c. 然后再重新运行 dotnet restore 之后编译  
  
3. 运行项目

    在控制台或Terminal打开项目所在目录，运行如下命令运行：
   
        dotnet run
        
    如遇如下错误，
    
        Expected to load libhostpolicy.dylib from [/usr/local/share/dotnet/shared/Microsoft.NETCore.App/1.0.0-rc2-3002468]
        This may be because the targeted framework ["Microsoft.NETCore.App": "1.0.0-rc2-3002468"] was not found.
        
    也不要慌张，到目录 bin/Debug/netcoreapp1.0中 找到文件 *.runtimeconfig.json, 将其中 runtime 版本号修改为与本机 CLI 版本号一致即可。

        {
            "runtimeOptions": {
                "framework": {
                    "name": "Microsoft.NETCore.App",
                    "version": "1.0.0-rc2-3002485"
                }
            }
        }
        
     如本机 dotnet --version 命令返回值为 “1.0.0-rc2-002485”，则应runtime config中的版本号应替换为“1.0.0-rc2-3002485”。
     
     然后再尝试运行 dotnet run

4. 调试项目 (Visual Studio Code)

    使用 VSCode 打开项目所在文件夹之后，VSCode 会问你是否添加启用项目调试相关的文件，你选OK之后目录下会新增文件夹“.vscode”，其中会包含两个文件：
    
        launch.json
        tasks.json
        
    当你发现无法调试失败的时候，你可以到 launch.json 文件，检查启动所指向的文件是否正确：
    
        {
            "name": ".NET Core Launch (console)",
            ...
            "program": "${workspaceRoot}/bin/Debug/netcoreapp1.0/netcore.dll",
            ...
        }


## 使用 .NET Core 进行 ASP.NET MVC 开发

虽然 dotnet cli 并没有提供直接创建 web/mvc项目的选项，但是我们还是可以手动来创建 mvc 项目的。
    
1. 首先是更新 project.json 来支持 mvc:
    
        {
            "version": "1.0.0-*",
            "content": [
                "wwwroot",
                "Views"
            ],
            "compilationOptions": {
                "preserveCompilationContext": true,
                "emitEntryPoint": true,
                "debugType": "portable"
            },
            "dependencies": {
                "Microsoft.AspNetCore.Diagnostics": "1.0.0-*",
                "Microsoft.AspNetCore.Mvc": "1.0.0-*",
                "Microsoft.AspNetCore.Mvc.TagHelpers": "1.0.0-*",
                "Microsoft.AspNetCore.Server.IISIntegration": "1.0.0-*",
                "Microsoft.AspNetCore.Server.Kestrel": "1.0.0-*",
                "Microsoft.AspNetCore.StaticFiles": "1.0.0-*",
                "Microsoft.Extensions.Logging.Console": "1.0.0-*",
                "Microsoft.NETCore.App": {
                    "type": "platform",
                    "version": "1.0.0-rc2-*"
                }
            },
            "frameworks": {
                "netcoreapp1.0": {
                "imports": [
                    "portable-net45+wp80+win8+wpa81+dnxcore50"
                ]
                }
            },
            "tools": {
                "Microsoft.AspNetCore.Server.IISIntegration.Tools": {
                "version": "1.0.0-*",
                "imports": "portable-net45+wp80+win8+wpa81+dnxcore50"
                }
            },
            "scripts": {
                "postpublish": "dotnet publish-iis --publish-folder %publish:OutputPath% --framework %publish:FullTargetFramework%"
            }
        }
        
2. 在NuGet.config 文件中增加 ASP.NET 的包的下载地址，如此文件不存在请先添加：
    
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <packageSources>
                <!--To inherit the global NuGet package sources remove the <clear/> line below -->
                <clear />
                <add key="dotnet-core" value="https://dotnet.myget.org/F/dotnet-core/api/v3/index.json" />
                <add key="api.nuget.org" value="https://api.nuget.org/v3/index.json" />
                <add key="AspNetCI" value="https://www.myget.org/F/aspnetcirelease/api/v3/index.json" />
            </packageSources>
        </configuration>

3. 增加 Startup.cs 文件 然后在 Program.cs 增加启动代码：

    Startup.cs

        using Microsoft.AspNetCore.Builder;
        using Microsoft.AspNetCore.Hosting;
        using Microsoft.Extensions.DependencyInjection;
        using Microsoft.Extensions.Logging;

        namespace HelloMvc
        {
            public class Startup
            {
                public void ConfigureServices(IServiceCollection services)
                {
                    // 注册MVC相关服务到ASP.NET Core的反转控制器
                    services.AddMvc();
                }
                
                public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
                {
                    loggerFactory.AddConsole(LogLevel.Debug);
                    
                    //启用静态文件支持
                    app.UseStaticFiles();
                    
                    if (env.IsDevelopment())
                    {
                        app.UseDeveloperExceptionPage();
                    }
                    
                    // 启用 Mvc 并定义默认的路由
                    app.UseMvc(routes =>
                    {
                        routes.MapRoute(
                            name: "default",
                            template: "{controller=Home}/{action=Index}/{id?}");
                    });
                }
            }
        }
        
    Program.cs
    
        using System;
        using System.IO;
        using Microsoft.AspNetCore.Hosting;

        namespace HelloMvc
        {
            public class Program
            {
                public static void Main(string[] args)
                {
                    var host = new WebHostBuilder()
                                .UseKestrel()
                                .UseContentRoot(Directory.GetCurrentDirectory())
                                .UseDefaultHostingConfiguration(args)
                                .UseIISIntegration()
                                .UseStartup<Startup>()
                                .Build();

                    host.Run();
                }
            }
        }
 
4. MVC 其它

    ASP.NET Core中 MVC 具体的开发方法，请参考官方文档 https://docs.asp.net/en/latest/mvc/index.html 来学习使用，我在这里就不再累述了。
    完整示例可参考我在github上的示例项目：https://github.com/kerryjiang/dotnetcore-samples/tree/master/mvc

    MVC项目也可通过 dotnet run 命令运行，还可以使用VSCode进行调试。


## 在 .NET Core 中使用 EntityFramework + Sqlite

.NET Core 中的 EntityFramework 也在 RC2 也有较大的变化。包的名字从 "EntityFramework.*" 变化为 "Microsoft.EntityFrameworkCore.*"。
如需使用Sqlite的话，project.json的包依赖应该为：

    "dependencies": {
        ...
        ...
        "Microsoft.EntityFrameworkCore": "1.0.0-*",
        "Microsoft.EntityFrameworkCore.Sqlite": "1.0.0-*",
        "Microsoft.NETCore.App": {
            "type": "platform",
            "version": "1.0.0-rc2-*"
        }
    }

另外，frameworks 节点对 netcoreapp1.0 也需增加新的imports (portable-net45+win8+wp8+wpa81 和 portable-net45+win8+wp8)：

    "frameworks": {
        "netcoreapp1.0": {
            "imports": [
                "portable-net45+wp80+win8+wpa81+dnxcore50",
                "portable-net45+win8+wp8+wpa81",
                "portable-net45+win8+wp8"
            ]
        }
    }

然后在Startup.cs中注册EF相关的服务和DbContext：

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddEntityFramework()
                .AddEntityFrameworkSqlite()
                .AddDbContext<WebsiteDbContext>(
                    options => options.UseSqlite("Data Source=./mvcefsample.sqlite")); //设置链接字符串
                    
        services.AddMvc();
    }


再到Configure里面初始化数据库或者启用DbMigration:

        public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
        {
            ...
            
            using (var serviceScope = app.ApplicationServices.GetRequiredService<IServiceScopeFactory>().CreateScope())
            {
                var db = serviceScope.ServiceProvider.GetService<WebsiteDbContext>();
                
                // do db migrate automatically
                // db.Database.Migrate();
                
                if (db.Database.EnsureCreated())
                {
                    db.Database.
                    for (int i = 0; i < 10; i++)
                    {
                        var article = new Article {
                            Title = string.Format("Article {0}",  i + 1),
                            Content = string.Format("Article {0} content blabla blabla",  i + 1),
                            CreatedTime = DateTime.Now,
                            UpdatedTime = DateTime.Now
                        };
                        
                        db.Articles.Add(article);
                    }
                    db.SaveChanges();
                }
            }
        }
 
完整示例可参考我在github上的示例项目：https://github.com/kerryjiang/dotnetcore-samples/tree/master/mvc-ef



## 在 ASP.NET MVC Core 中使用 ASP.NET Identity

1. ASP.NET 中身份验证是免不了的事情，首先第一步在 project.json 中添加包依赖：

        "dependencies": {
            ...
            ...
            "Microsoft.AspNetCore.Identity.EntityFrameworkCore": "1.0.0-*",
            ...
            "Microsoft.EntityFrameworkCore": "1.0.0-*",
            "Microsoft.EntityFrameworkCore.Sqlite": "1.0.0-*",
            "Microsoft.NETCore.App": {
                "type": "platform",
                "version": "1.0.0-rc2-*"
            }
        }
    
2. 实现带有 Identity 支持的 DbContext:

        using Microsoft.EntityFrameworkCore;
        using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

        namespace MvcIdentitySample
        {
            public class ApplicationUser : IdentityUser
            {
                
            }
        
            public class WebsiteDbContext : IdentityDbContext<ApplicationUser>
            {
                public WebsiteDbContext(DbContextOptions<WebsiteDbContext> options)
                    : base(options)
                {

                }

                //public DbSet<Article> Articles { get; set; }
            }
        }

3. 然后在 Startup 中的 ConfigureSerivces 方法中注册服务：

        public void ConfigureServices(IServiceCollection services)
        {
            // register services about EF
            services.AddEntityFramework()
                    .AddEntityFrameworkSqlite()
                    .AddDbContext<WebsiteDbContext>(
                        options => options.UseSqlite("Data Source=./mvcidentitysample.sqlite"));
            
            // register services about ASP.NET Identity
            services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<WebsiteDbContext>()
                .AddDefaultTokenProviders();
            
            services.AddMvc();
        }
    
4. 再到 Startup 中的 Configure 方法中启用 Identity：

        public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
        {
            ...
            
            // the authentication must be configured before mvc
            app.UseIdentity();
            
            // To configure external authentication please see http://go.microsoft.com/fwlink/?LinkID=532715
            /*
            app.UseFacebookAuthentication(new FacebookOptions
            {
                AppId = "901611409868059",
                AppSecret = "4aa3c530297b1dcebc8860334b39668b"
            });
            */
            
            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });
        }
    
    
注意 app.UseIdentity() 这句必须放到启用 MVC 之前，否则 Identity 无法生效，这个问题折腾了我几个小时。:(
    

5. 由于 ASP.NET MVC 6 和 最新的 ASP.NET Identity 的变化，其使用方法与老版本的模版代码略有不同。

    a. 页面头部登陆状态部分页面（/Views/Shared/_LoginPartial.cshtml），由于 taghelper 的引入和 模版引擎的变化，这个页面的代码看起来会和以前有明显的不同：

        @using Microsoft.AspNetCore.Identity
        @using MvcIdentitySample
        @using MvcIdentitySample.Models
        @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
        @inject SignInManager<ApplicationUser> SignInManager
        @inject UserManager<ApplicationUser> UserManager

        @if (SignInManager.IsSignedIn(User))
        {
            <form asp-controller="Account" asp-action="LogOff" method="post" id="logoutForm" class="navbar-right">
                <ul class="nav navbar-nav navbar-right">
                    <li>
                        <a asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
                    </li>
                    <li>
                        <button type="submit" class="btn btn-link navbar-btn navbar-link">Log off</button>
                    </li>
                </ul>
            </form>
        }
        else
        {
            <ul class="nav navbar-nav navbar-right">
                <li><a asp-controller="Account" asp-action="Register">Register</a></li>
                <li><a asp-controller="Account" asp-action="Login">Log in</a></li>
            </ul>
        }
        
    b. 新增 _ViewImports.cshtml (/Views/_ViewImports.cshtml) 的使用避免了重复在多个view里面增加相同的using和其它定义：
    
        @using Microsoft.AspNetCore.Identity
        @using MvcIdentitySample
        @using MvcIdentitySample.Models
        @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
    
    这样就不用在刚才的_LoginPartial.cshtml页面里增加同样的代码了。
    


## 总结

大家通过以上介绍和示例应该可以了解到，当前使用.NET Core 来进行简单的应用开发是可行的，dot cli(dotnet) 这个工具已经比上个版本(RC1)的工具简单方便了很多，而且统一的Web开发和Console开发，CLI工具本身以后应该不会有太大的变化。因为.NET Core 1.0 RC2还在开发测试阶段，有的包可能还没有发布到NuGet上，因此而造成的找不到合适包的情况属于常见问题之一，不过只要在NuGet.config中增加了合适的Package Source，这个问题就很好解决了。所以大家如果想要把自己的项目，公司的项目迁移到.NET Core, 现在就可以开始动手了，不用再等到时间不确定的 1.0 release。

另外，本文所展示的示例代码限于篇幅与排版，无法做到十分详细，例如代码中所需要的using并未提及，还需要读者来自行添加(VS里ALT＋SHIFT＋F10, VSC里面好像只能手动点感叹号？)。

而且此文旨在尝试.NET Core的可用性，因此并未对如ASP.NET MVC 6和 EF7相关技术做深入探讨，如需了解请查看相关技术文档：

1. [ASP.NET MVC](https://docs.asp.net/en/latest/mvc/index.html)
2. [EntityFramework](http://docs.efproject.net/en/latest/)

最后再提醒大家一次，本文中所涉及的代码的完整示例项目已放到 github 上，欢迎大家fork/star/send pr:

https://github.com/kerryjiang/dotnetcore-samples
