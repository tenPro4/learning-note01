- [Project Link](#project-link)
- [Test Database](#test-database)
- [基础](#基础)
- [Test Units](#test-units)
  - [构筑网页应用测试环境](#构筑网页应用测试环境)
    - [没有任何配置改写配置的版本](#没有任何配置改写配置的版本)
    - [复写一些配置的版本](#复写一些配置的版本)
    - [多层Fixture 类](#多层fixture-类)
    - [Fixture Collection](#fixture-collection)
  - [Intergration Testing](#intergration-testing)
    - [例子1](#例子1)
    - [例子2](#例子2)
  - [Controller Testing](#controller-testing)
  - [API Testing](#api-testing)
- [File Upload Testing](#file-upload-testing)
  - [Test On Life Cycle](#test-on-life-cycle)
  - [Test On API](#test-on-api)

# Project Link
Click here to get [reference project](https://github.com/tenPro4/NetCoreUnitTestBasic)

# Test Database

**要点**
- 测试数据库时，必须避免使用真数据，除非仅仅制作`读取`数据测试
- 推荐使用记忆数据或sqlite作测试
- 本次示范使用记忆数据库

1. 创建DbContextFactory 类
```csharp
public class DbContextFactory
{
    public static AppDbContext Create(string databaseName)
    {
        var options = new DbContextOptionsBuilder<AppDbContext>()
            .UseInMemoryDatabase(databaseName)  //使用记忆数据库测试
            .Options;

        return new AppDbContext(options);
    }
}
```

1. 创建测试类
```csharp
[Fact]
public void SavesCounterToDatabase()
{
    var counter = new Counter { Name = "New Counter" };
    using (var ctx = DbContextFactory.Create(nameof(SavesCounterToDatabase)))
    {
        ctx.Counters.Add(counter);
        ctx.SaveChanges();  //输入测试
    }

    using(var ctx = DbContextFactory.Create(nameof(SavesCounterToDatabase)))
    {
        var savedCounter = ctx.Counters.Single();
        Assert.Equal(counter.Name, savedCounter.Name);  //检测输入测试正确性以及读取测试
    }
}
```

>>**注意**
>> 测试可在Top Nav中的Test -> Run All Test
>> 或者Test -> Test Explorer 中的界面进行特定测试

# 基础

**一个简单的加方法类**
```csharp
public class CounterManager 
{
    public int Plus(int n1,int n2)
    {
        return n1+n2;
    }
}
```

**测试类**
```csharp

    [Fact]
    public void Test1()
    {
        var total = new CounterManager().Plus(1,2)
        Assert.Equal(3,total );
    }


    //you can stimulate params as many as you wish
    [Theory]
    [InlineData(1,2,3)]
    [InlineData(33,30,63)]
    public void Test2(int n1,int n2,int expected)
    {
        var total = new CounterManager().Plus(n1,n2)

        Assert.Equal(expected, total);
    }

    [Theory]
    [InlineData([])]
    public void Test2(List<int> ns)
    {
        Assert.Empty(ns) //verify if is empty list
    }
```

**Other common verify methods**
- Assert.Contains(expected, ICollection<string>)
- Assert.False(boolean) : verify value is false
- Assert.True(boolean) : verify value is true
- Assert.Single(List<T>) : verify list only contain single object
- Assert.NotNull<T>(object<T>)
- Assert.StartsWith(expected,input)
- Assert.EndsWith(expected,input)
- Assert.Matches(expression,input)

# Test Units

**要点**
- 测试单元会因项目的构造有所不同，通常来说会有:Repository, Servercice, Controller
- 测试单元时，通常不会直接使用该单元内的方法，因为逻辑中可能包含数据写入。
- 除非，在测试单元的时候改写覆盖数据库的配置，使其数据库的读写逻辑仅发生在短暂性的数据库
- 或者，使用**Moq**改写单元的方法
- 该范例使用网页应用作为测试对象，因此测试前需要构筑虚拟网页应用的测试环境

## 构筑网页应用测试环境

### 没有任何配置改写配置的版本
```csharp
public class TestFixture<TStartup> : IDisposable where TStartup : class
{
    public readonly TestServer Server;
    private readonly HttpClient Client;


    public TestFixture()
    {
        var builder = new WebHostBuilder()
            .UseStartup<TStartup>();

        Server = new TestServer(builder);
        Client = new HttpClient();
    }


    public void Dispose()
    {
        Client.Dispose();
        Server.Dispose();
    }
}
```

接着只需要在测试类上引用Fixture类即可
```csharp
 public class CourseServiceTest:IClassFixture<TestFixture<Startup>>
 {}
```

### 复写一些配置的版本
```csharp
 public class TestFixture
 {
     public TestServer TestServer;

     public HttpClient Client { get; }
     public TestFixture()
     {
         var builder = new WebHostBuilder()
              .ConfigureAppConfiguration((hostContext, configApp) =>
              {
                  configApp.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true);

                  var env = hostContext.HostingEnvironment;

                  configApp.AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);
              })
              .UseStartup<StartupTest>();

         TestServer = new TestServer(builder);
         Client = TestServer.CreateClient();
     }
 }
```

### 多层Fixture 类
- 本范例主要体现多层复杂的fixture类应用
- 本范例以测试**JWT**为主

**可以在一个Fixture类的基础上再次叠加其他Fixture 类**
```csharp
 public class BaseClassFixture : IClassFixture<TestFixture>     //<--- 该fixture为第二范例的类
 {
     protected readonly HttpClient Client;
     protected readonly TestServer TestServer;
     public BaseClassFixture(TestFixture fixture)
     {
         Client = fixture.Client;
         TestServer = fixture.TestServer;
     }

     protected virtual void SetupAdminClaimsViaHeaders()
     {
         using (var scope = TestServer.Services.CreateScope())
         {
             var configuration = scope.ServiceProvider.GetRequiredService<AppSettings>();
             Client.SetAdminClaimsViaHeaders(configuration);
         }
     }
 }
```

**延展HttpClient的方法以及虚拟Jwt的配置**
```csharp
public static class HttpClientExtensions
{
    public static void SetAdminClaimsViaHeaders(this HttpClient client, AppSettings setting)
    {
        var claims = new[]
        {
            new Claim(JwtClaimTypes.Subject, Guid.NewGuid().ToString()),
            new Claim(JwtClaimTypes.Name, Guid.NewGuid().ToString())
        };

        var authSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(setting.Key));

        var token = new JwtSecurityToken(
            claims: claims,
            audience: "NuxtApp",
            issuer: "StationMonitorAPI",
            expires: DateTime.Now.AddDays(30),
            signingCredentials: new SigningCredentials(authSigningKey, SecurityAlgorithms.HmacSha256));
        var t = new JwtSecurityTokenHandler().WriteToken(token);
        client.DefaultRequestHeaders.Add(AuthenticatedTestRequestMiddleware.TestAuthorizationHeader, t);
    }
}

public class AuthenticatedTestRequestMiddleware
{
    private readonly RequestDelegate _next;
    public static readonly string TestAuthorizationHeader = "FakeAuthorization";
    public AuthenticatedTestRequestMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext context)
    {
        if (context.Request.Headers.Keys.Contains(TestAuthorizationHeader))
        {
            var token = context.Request.Headers[TestAuthorizationHeader].Single();
            var jwt = new JwtSecurityToken(token);
            var claimsIdentity = new ClaimsIdentity(jwt.Claims, JwtBearerDefaults.AuthenticationScheme, JwtClaimTypes.Name, JwtClaimTypes.Role);
            context.User = new ClaimsPrincipal(claimsIdentity);
        }

        await _next(context);
    }
}
```

**测试JWT类**
```csharp
 public class APITest1 : BaseClassFixture
 {
     public APITest1(TestFixture fixture) : base(fixture)
     {
     }

     [Fact]
     public async Task GetMapping()
     {
         //set jwt token for auth
         SetupAdminClaimsViaHeaders();

         var response = await Client.GetAsync("/api/home");

         //assert
         response.EnsureSuccessStatusCode();
         response.StatusCode.Should().Be(HttpStatusCode.OK);
     }
 }
```

以上版本改写了appsetting.json的文件以及使用**StartupTest** instead of **Startup**
改写测试环境的配置的主要好处是生产和测试的一些配置参数可能会不同，例如数据库连接，上传路径等

### Fixture Collection
可以将多个fixture类合成一个集合类

```csharp
public class DatabaseFixture : IDisposable
{
    public DatabaseFixture()
    {
        // Initialize the database connection
    }

    public void Dispose()
    {
        // Clean up the database connection
    }
}

public class ConfigurationFixture : IDisposable
{
    public ConfigurationFixture()
    {
        // Initialize the configuration
    }

    public void Dispose()
    {
        // Clean up the configuration
    }
}
```

**定义 Collection Fixture**
```csharp
public class MyCollectionFixture
{
    public DatabaseFixture DatabaseFixture { get; private set; }
    public ConfigurationFixture ConfigurationFixture { get; private set; }

    public MyCollectionFixture()
    {
        DatabaseFixture = new DatabaseFixture();
        ConfigurationFixture = new ConfigurationFixture();
    }
}
```

**标记Collection**
```csharp
[CollectionDefinition("MyTestCollection")]
public class MyTestCollection : ICollectionFixture<MyCollectionFixture>
{
    // This class has no code, and is never created. Its purpose is simply
    // to be the place to apply [CollectionDefinition] and all the
    // ICollectionFixture<> interfaces.
}
```

**测试类**
```csharp
[Collection("MyTestCollection")]
public class MyTests1
{
    MyCollectionFixture _fixture;

    public MyTests1(MyCollectionFixture fixture)
    {
        _fixture = fixture;
    }

    [Fact]
    public void Test1()
    {
        // Use _fixture.DatabaseFixture and _fixture.ConfigurationFixture
        Assert.NotNull(_fixture.DatabaseFixture);
        Assert.NotNull(_fixture.ConfigurationFixture);
    }
}

[Collection("MyTestCollection")]
public class MyTests2 //: IClassFixture<>
{
    MyCollectionFixture _fixture;

    public MyTests2(MyCollectionFixture fixture)
    {
        _fixture = fixture;
    }

    [Fact]
    public void Test2()
    {
        // Use _fixture.DatabaseFixture and _fixture.ConfigurationFixture
        Assert.NotNull(_fixture.DatabaseFixture);
        Assert.NotNull(_fixture.ConfigurationFixture);
    }
}
```


## Intergration Testing

**要点**
- 使用Mock来复写方法的内容

### 例子1
**方法类**
```csharp
public class BookManager 
{
    public class Book
    {
        public int Id {get;set;}
        public int Page {get;set;}
        public string Title {get;set;}
    }

    public class AveragePageResponse : Book
    {
        public float Percentage {get;set;}
    }

    public ICollection<AveragePageResponse> PercentagePageForBooks(ICollection<Book> books)
    {
       var total = books.Sum(x => x.Page);

       return books.Select(x => new AveragePageResponse{
        Id = x.Id,
        Page = x.Page,
        Title = x.Title,
        Percentage = x.Page/total
       }).ToList();
    }

    public void JustFun(string message)
    {
        Console.WriteLine(message)
    }
}

public interface IBookRepo
{
    ICollection<Book> GetBooks(List<int> ids);
    Book AddBook(Book book);
}

public class BookService
{
    private readonly _bookManager;
    private readonly _iBookRepo;

    public BookService(_bookManager bm,_iBookRepo repo){
        _bookManager =bm;
        _iBookRepo = repo;
    }

    public ICollection<AveragePageResponse> GetBookPagePercentage(ICollection<int> ids)
    {
        var books = _iBookRepo.GetBooks(ids);

        var percentages = _bookManager.PercentagePageForBooks(books);

        _bookManager.JustFun("Test Whether got call");

        return percentages.OrderBy(x => x.Id);
    }

    public Book AddNewBook(Book book)
    {
        _iBookRepo.AddBook(book);

        return book;
    }
}
```

**测试类**
```csharp
public class BookManagerTest
{
    private readonly Mock<BookManager> _mockBookManager = new Mock<BookManager>();
    private readonly Mock<IBookRepo> _mockBookRepo = new Mock<IBookRepo>();

    [Fact]
    public void Test1()
    {
        var bookIds = new List<int>{1,2};
        var book1 = new Book{ Id = 1,title="b1",Page=50};
        var book2 = new Book{ Id = 2,title="b2",Page=50};

        var books = new List<Book>{book1,book2};

        var expectedResult1 = new AveragePageResponse{ Id = 1,title="b1",Page=50, Percentage=0.5};
        var expectedResult2 = new AveragePageResponse{ Id = 2,title="b2",Page=50, Percentage=0.5};

        var expectedPercentageResults = new List<AveragePageResponse>{expectedResult1,expectedResult2};

        //使用setup复写方法,这样可避免输入的测试数据进入可能包含数据写入的逻辑
        _mockBookRepo.Setup(x => x.GetBooks(bookIds)).Return(books);
        _mockBookManager.Setup(x => x.PercentagePageForBooks(bookIds)).Return(expectedPercentageResults);

        var service = new BookService(_mockBookManager.Object,_mockBookRepo.Object);

        var getResults = service.GetBookPagePercentage(bookIds);

        foreach(var expectedPercentageResult in expectedPercentageResults)
        {
            Asserts.Contains(expectedPercentageResult.title,getResults.Select(x => x.title))
        }

        Asserts.Equal(expectedResult1,getResults[0]);

        //检测JustFun方法是否有被至少呼叫一次
        _mockBookManager.Verify(x => x.JustFun("Any Custom Message"),Times.Once);
        //or can use It.IsAny<T>
        //_mockBookManager.Verify(x => x.JustFun(It.IsAny<string>()),Times.Once);

        //复写iBookRepo 的AddBook方法
        //强调Callback方法
        //Callback是将返回的值进行再次处理
        //这里的则意味着，AddBook成功后返回Book object，然后将该object 加入books 参数。
        _mockBookRepo.Setup(x => x.AddBook(It.IsAny<Book>()))
        .Callback((Book book) => books.Add(book));

        var book3 = new Book{ Id = 3,title="b3",Page=50};

        service.AddNewBook(book3);

        Assert.Equal(3,books.Count());
    }
}
```

**解释：**
`BookService`的构成需要引用`BookManager`以及`IBookRepo`。
然后在`测试类`中使用`Setup`方法改写了各自的方法。
因此当`BookService`使用对应的方法时，它不会进入方法真正的逻辑，而是直接返回指定的数值。

### 例子2
该范例主要强调可将所有复写都放在constructor
```csharp
public class BookManagerTest:IClassFixture<TestFixture<Startup>>
{
    private readonly Mock<BookManager> _mockBookManager = new Mock<BookManager>();
    private readonly Mock<IBookRepo> _mockBookRepo = new Mock<IBookRepo>();
    private IBookService Service { get; }

    public List<Book> books = new List<Book>();

    public BookManagerTest(TestFixture<Startup> fixture)
    {
        _mockBookRepo.Setup(x => x.GetBooks(bookIds)).Return(books);
        _mockBookManager.Setup(x => x.PercentagePageForBooks(bookIds)).Return(expectedPercentageResults);

        _mockBookRepo.Setup(x => x.AddBook(It.IsAny<Book>()))
        .Callback((Book book) => books.Add(book));

        Service = new BookService(_mockBookManager.Object,_mockBookRepo.Object);
    }

    [Fact]
    public void Test1()
    {
        var bookIds = new List<int>{1,2};
        var book1 = new Book{ Id = 1,title="b1",Page=50};
        var book2 = new Book{ Id = 2,title="b2",Page=50};

        books.Add(book1);
        books.Add(book2);

        var expectedResult1 = new AveragePageResponse{ Id = 1,title="b1",Page=50, Percentage=0.5};
        var expectedResult2 = new AveragePageResponse{ Id = 2,title="b2",Page=50, Percentage=0.5};

        var expectedPercentageResults = new List<AveragePageResponse>{expectedResult1,expectedResult2};

        var getResults = Service.GetBookPagePercentage(bookIds);

        foreach(var expectedPercentageResult in expectedPercentageResults)
        {
            Asserts.Contains(expectedPercentageResult.title,getResults.Select(x => x.title))
        }

        Asserts.Equal(expectedResult1,getResults[0]);

        //检测JustFun方法是否有被至少呼叫一次
        _mockBookManager.Verify(x => x.JustFun("Any Custom Message"),Times.Once);
        //or can use It.IsAny<T>
        //_mockBookManager.Verify(x => x.JustFun(It.IsAny<string>()),Times.Once);

        var book3 = new Book{ Id = 3,title="b3",Page=50};

        Service.AddNewBook(book3);

        Assert.Equal(3,books.Count());
    }
}
```

## Controller Testing
```csharp
public class BookControllerTests : IClassFixture<TestFixture>
{
    private readonly Mock<IBookRepo> _mockBookRepo = new Mock<IBookRepo>();
    private IBookService Service { get; }
    private BookController Controller {get;}

     public BookManagerTest(TestFixture fixture)
     {
        //doing mock setup here

        Service = new BookService(_mockBookRepo.Object,...);
        Controller = new BookController(_mockBookRepo.Object,...);
     }

     [Theory]
     [InlineData(1, "b1", 50)]
     public async TestAdd(int id,string title,int page)
     {
        var newBook = new Book{
            Id = id,
            Title = title,
            Page = page
        };

        var createdBook = await Controller.Create(newBook);

        Assert.NotNull(createdBook);

        _mockBookRepo.Verify(x => x.AddBook(It.Any<Book>()),Times.Once);
     }
}
```

## API Testing
测试API需要先构筑网页应用的测试环境
```csharp
public class TestFixture:WebApplicationFactory<Startup>
{
     protected override void ConfigureWebHost(IWebHostBuilder builder){
        base.ConfigureWebHost(builder);
        //you can do override here for test env
     }
}
```

可在在fixture类上引用`IAsyncLifetime`是测试周期能够在一定程度上控制
例如，创建周期时可以上传一些照片到测试目录，然后在结束周期时将所有测试照片删除。
```csharp
public class TestFixture:WebApplicationFactory<Startup>,IAsyncLifetime
{
     protected override void ConfigureWebHost(IWebHostBuilder builder){
        base.ConfigureWebHost(builder);
        //you can do override here for test env
     }

     public async Task InitializeAsync()
    {
        //something here
    }

     public Task DisposeAsync()
    {
        //something here

        return Task.CompletedTask;
    }
}
```

**测试类**
```csharp
public class ApiTest:IClassFixture<TestFixture>
{
    private readonly TestFixture _fixture;

    public ApiTest(TestFixture fixture)
    {
        _fixture = fixture;
    }

    [Fact]
    public async Task Test1()
    {
        var client = _fixture.CreateClient();
        var result = await client.GetAsync("/api/polls");
        Assert.Equal(HttpStatusCode.OK,result.StatusCode);
        Assert.NotNull(result);
    }
}
```

# File Upload Testing
## Test On Life Cycle

**要点**
- 需要先构筑网页应用的测试环境，例如改写上传文件的路径，避免和生产环境的路径冲突
- 可应用生命周期，例如，创建周期创建上传文档，结束周期删除该文档包括其中全部的文件。

**构筑测试环境**
```csharp
public class TestFixture:WebApplicationFactory<Startup>,IAsyncLifetime
{
    public Stream TestFile { get; private set; }
    private string _cleanupPath;
     protected override void ConfigureWebHost(IWebHostBuilder builder){
        base.ConfigureWebHost(builder);
        //you can do override here for test env

        builder.ConfigureServices(services =>
        {
            //change upload path for test env
             services.Configure<FileSettings>(fs =>
            {
                fs.Path = "test_images";
            });


            //get upload path for the purpose of deleted at the end of testing
            var serviceProvider = services.BuildServiceProvider();
            using var scope = serviceProvider.CreateScope();
            var env = scope.ServiceProvider.GetRequiredService<IWebHostEnvironment>();
            _cleanupPath = Path.Combine(env.WebRootPath, "test_images");
        })
     }

     public async Task InitializeAsync()
    {
        //upload any image to test directory
        TestFile = new MemoryStream();
        var fileStream = File.OpenRead("base.png");
        await fileStream.CopyToAsync(memoryStream);
        fileStream.Close();
    }

     public Task DisposeAsync()
    {
        var directoryInfo = new DirectoryInfo(_cleanupPath);
        if (directoryInfo.Exists)
        {
            foreach (var file in directoryInfo.GetFiles())
            {
                file.Delete();
            }

            Directory.Delete(_cleanupPath);
        }

        return Task.CompletedTask;
    }
}
```

## Test On API
```csharp
[Fact]
public async Task SavesFileToDisk()
{
    var client = _fixture.CreateClient();

    MultipartFormDataContent form = new();

    form.Add(new StreamContent(_fixture.TestFile), "file", "base.png");
    var response = await client.PostAsync("/api/polls/upFile", form);

    Assert.Equal(HttpStatusCode.OK, response.StatusCode);

    var fileResponse = await client.GetAsync("/test_images/base.png");
    Assert.Equal(HttpStatusCode.OK, fileResponse.StatusCode);
}
```