# Online-Bookstore-Web-Application-using-ASP.NET-Core
Creating an Online Bookstore Web Application using ASP.NET Core and Entity Framework involves several steps, including setting up your development environment, creating the project structure, implementing authentication, designing the database schema, and building the user interface. Below are detailed steps to guide you through the process:

### Prerequisites

1. **Visual Studio 2022**: Ensure you have Visual Studio 2022 installed with the ASP.NET and web development workload.
2. **SQL Server**: Ensure you have SQL Server installed and running on your machine.
3. **.NET Core SDK**: Ensure you have the latest version of the .NET Core SDK installed.

### Steps to Create the Online Bookstore Web Application

#### 1. Set Up the ASP.NET Core Project

1. **Open Visual Studio**:
   - Open Visual Studio and click on "Create a new project."

2. **Create New ASP.NET Core Web Application**:
   - Select "ASP.NET Core Web App (Model-View-Controller)" and click "Next."
   - Name the project `OnlineBookstore` and choose a location for your project.
   - Choose the latest .NET Core version and ensure "Authentication Type" is set to "Individual Accounts" for ASP.NET Identity. Click "Create."

#### 2. Design the Database Schema

1. **Define the Entities**:
   - **Book**: Contains properties like `BookId`, `Title`, `Author`, `Price`, `ISBN`, `Description`, and `ImageUrl`.
   - **Order**: Contains properties like `OrderId`, `UserId`, `OrderDate`, and `TotalAmount`.
   - **OrderDetail**: Contains properties like `OrderDetailId`, `OrderId`, `BookId`, and `Quantity`.

2. **Add Entity Models**:
   - Create a new folder named `Models` in your project.
   - Add C# classes for each entity (`Book`, `Order`, `OrderDetail`).

```csharp
// Book.cs
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public string Author { get; set; }
    public decimal Price { get; set; }
    public string ISBN { get; set; }
    public string Description { get; set; }
    public string ImageUrl { get; set; }
}

// Order.cs
public class Order
{
    public int OrderId { get; set; }
    public string UserId { get; set; }
    public DateTime OrderDate { get; set; }
    public decimal TotalAmount { get; set; }
    public ICollection<OrderDetail> OrderDetails { get; set; }
}

// OrderDetail.cs
public class OrderDetail
{
    public int OrderDetailId { get; set; }
    public int OrderId { get; set; }
    public Order Order { get; set; }
    public int BookId { get; set; }
    public Book Book { get; set; }
    public int Quantity { get; set; }
}
```

#### 3. Set Up Entity Framework Core

1. **Install EF Core Packages**:
   - Open the Package Manager Console (Tools > NuGet Package Manager > Package Manager Console).
   - Run the following commands:

```shell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
Install-Package Microsoft.EntityFrameworkCore.Tools
```

2. **Create the Data Context**:
   - Add a class named `ApplicationDbContext` in the `Data` folder.

```csharp
using Microsoft.EntityFrameworkCore;
using OnlineBookstore.Models;

public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    public DbSet<Book> Books { get; set; }
    public DbSet<Order> Orders { get; set; }
    public DbSet<OrderDetail> OrderDetails { get; set; }
}
```

3. **Configure the Database Connection**:
   - Open `appsettings.json` and add the connection string for SQL Server.

```json
"ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=OnlineBookstoreDB;Trusted_Connection=True;MultipleActiveResultSets=true"
}
```

4. **Register DbContext**:
   - Open `Startup.cs` or `Program.cs` (depending on the version) and configure the services.

```csharp
services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
```

5. **Create the Database**:
   - Run the following command in the Package Manager Console to create the database:

```shell
Add-Migration InitialCreate
Update-Database
```

#### 4. Implement User Authentication

1. **Set Up ASP.NET Identity**:
   - The project template already includes Identity. Ensure it's configured in `Startup.cs`.

```csharp
services.AddDefaultIdentity<IdentityUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

2. **Enable Authentication**:
   - Ensure the authentication middleware is enabled in `Startup.cs`.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

#### 5. Build the User Interface

1. **Create Controllers**:
   - Add controllers for `Books`, `Orders`, and `ShoppingCart`.

```csharp
// BooksController.cs
using Microsoft.AspNetCore.Mvc;
using OnlineBookstore.Models;

public class BooksController : Controller
{
    private readonly ApplicationDbContext _context;

    public BooksController(ApplicationDbContext context)
    {
        _context = context;
    }

    public async Task<IActionResult> Index()
    {
        return View(await _context.Books.ToListAsync());
    }

    // Other actions for Create, Edit, Delete
}
```

2. **Design Views**:
   - Use Razor syntax to create views for displaying and managing books and orders.
   - Use Bootstrap for responsive design. Ensure Bootstrap is included in the `_Layout.cshtml` file.

```html
<!-- Example: Books/Index.cshtml -->
@model IEnumerable<OnlineBookstore.Models.Book>
@{
    ViewData["Title"] = "Books";
}

<h1>Books</h1>
<div class="row">
    @foreach (var book in Model)
    {
        <div class="col-md-3">
            <div class="card mb-3">
                <img class="card-img-top" src="@book.ImageUrl" alt="@book.Title">
                <div class="card-body">
                    <h5 class="card-title">@book.Title</h5>
                    <p class="card-text">@book.Author</p>
                    <p class="card-text"><strong>@book.Price.ToString("C")</strong></p>
                </div>
            </div>
        </div>
    }
</div>
```

3. **Shopping Cart**:
   - Implement a simple shopping cart functionality to allow users to add books and manage their orders.

#### 6. Testing and Deployment

1. **Test the Application**:
   - Run the application locally to test all features, including user registration, login, book browsing, and order placement.

2. **Deploy to a Web Server**:
   - Publish the application to a web server or cloud platform like Azure.

#### 7. Version Control

1. **Initialize Git Repository**:
   - Initialize a Git repository in your project directory and commit your changes.

```shell
git init
git add .
git commit -m "Initial commit"
```

2. **Push to GitHub**:
   - Create a new repository on GitHub and push your project.

```shell
git remote add origin https://github.com/[YourUsername]/OnlineBookstore.git
git push -u origin master
```

These steps should guide you in creating a full-stack Online Bookstore Web Application using ASP.NET Core and Entity Framework. Feel free to expand on these functionalities by adding features like search, category filtering, or reviews to enhance your application.
