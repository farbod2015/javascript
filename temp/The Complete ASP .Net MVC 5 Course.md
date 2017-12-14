# The Complete ASP .Net MVC 5 Course

## General Information

### Useful Visual Studio Extensions

* Productivity Power Tools 2017
* Web Essentials 2013.5
* ReSharper (not free but worth it)

### Shortcuts

* `Ctrl` + `M` + `M`: collapse and expand
* `Ctrl` + `Shift` + `B`: build
* `Ctrl` + `F5`: build and open in browser
* `ctor` + `Tab`: creates the default constructor
* `prop` + `Tab`: creates a new property
* `mvcaction4` + `Tab`: creates a new action
* we can add a shortcut for _Package Manager Console_ by going to `Tools`>`Options`>`Environment`>`Keyboard` and assign the preferred shortcut (e.g. `Alt`+`/`+`.`) to `View.PackageManagerConsole`

### Package Manager

Package managers handle the dependency to external libraries. So we can install packages and update them using package manager.

## MVC Architectural Pattern

With this architecture each component has a distinct responsibility and this results in better separation of concerns and a more maintainable application.

### Model

Application data and behavior in terms of its problem domain, and independent of the UI. It directly manages the data, logic and rules of the application. These classes have properties and methods that purely represent the application's state and rules. They are not tight to the user interface, so they can be used in different applications like a desktop or a mobile app. They are plain old CLR objects (POCO).

In our example: classes like Movie, Customer, Rental, Transaction, etc.

### View

A view can be any output representation of information, such as the HTML markup that we display to the user.

### Controller

The controller, accepts input and converts it to commands for the model or view. It is responsible for handling HTTP requests in our application.

In ASP.NET MVC we refer to methods of the controllers as actions.

### Router

Router is also a piece to this architecture which is not in the acronym MVC but it is nearly always there. When a request comes in the application a controller will be selected for handling that request; selecting the right controller is the responsibility of the router.

The router based on some rules, notes that the request with URL `/movies` should be handled by class called `MoviesController`. An action in the controller is responsible for the handling the request.

## ASP.NET MVC Fundamentals

### Action Results

`ActionResult` is the base class for all action results in ASP.NET MVC. So depending on what an action does it would return an instance of one of the classes that is derived from `ActionResult`:

|Type|Helper Method|
|-|-|
|ViewResult|View()|
|PartialViewResult|PartialView()|
|ContentResult|Content()|
|RedirectResult|Redirect()|
|RedirectToRouteResult|RedirectToAction()|
|JsonResult|Json()|
|FileResult|File()|
|HttpNotFoundResult|HttpNotFound()|
|EmptyResult||

### Action Parameters

When a request comes in the application ASP.NET MVC automatically maps requests data to parameter values for action method. So, if an action method takes a parameter the MVC framework looks for a parameter with same name in the request data. If a parameter with that name exists the framework will automatically pass the value of that parameter to the target action.

The parameter value can be 

* embedded in the URL: `/movies/edit/1`
* in the query string: `/movies/edit?id=1`
* in the form of data: `id=1`

```c#'
public ActionResult Edit(int id)
{
  return Content("id=" + id);
}
```

### Verbatim String

A verbatim string is one that does not need to be escaped, like a filename:

```cs
// regular string
string myFileName = "C:\\myfolder\\myfile.txt";

// verbatim string
string myFileName = @"C:\myfolder\myfile.txt";
```

In a regular strings, we need to use `\` to ignore escape characters, but in verbatim strings, the `@` symbol tells the complier to read the string literally and don't interpret control characters.

### Convention-based Routing

The default routing in `App_Start/RouteConfig.cs` is defined as follow:

```c#
routes.MapRoute(
    name: "Default",
    url: "{controller}/{action}/{id}",
    defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }
);
```

It works for most scenarios but there are situations where we need a rout with multiple parameters (e.g. `/movies/released/2015/04`). We can define custom routs using `MapRoute`. The order of the defined routes is important. We need to define them from most specific to most generic otherwise the more generic route will be applied to the URL. Here is an example:

```c#
routes.MapRoute(
  "MoviesByReleaseDate",                                    // Route name
  "movies/released/{year}/{month}",                         // URL with parameters
  new { controller = "Movies", action = "ByReleaseDate" },  // Parameter defaults
  new { year = @"\d{4}", month = @"\d{2}" }                 // Constraint (optional)
);
```

and here is a controller action for the above route:

```c#
// MoviesController.cs

namespace Vidly.Controllers
{
  public class MoviesController : Controller
  {
    public ActionResult ByReleaseDate(int year, int month)
    {
      return Content(year + "/" + month);
    }
  }
}
```

### Attribute Routing

The problem with convention-base routing is:

* it will create a mess in `RouteConfig.cs` for large applications with a lot routes.
* we need to go back and forth between the controller and the config file to edit the route.
* renaming action names in controllers will not automatically update the route definition in `RouteConfig.cs`.

A better way to create a custom route is to apply the route using an attribute to the corresponding action. To do attribute routing first we need to enable it in `RouteConfig.cs`:

```cs
routes.MapMvcAttributeRoutes();
```

then we can apply the route to the action as follow:

```cs
// MoviesController.cs

namespace Vidly.Controllers
{
  public class MoviesController : Controller
  {
    [Route("movies/released/{year}/{month:regex(\\d{2}):range(1, 12)}")]
    public ActionResult ByReleaseDate(int year, int month)
    {
      return Content(year + "/" + month);
    }
  }
}
```

Other attribute route constraints are: min, max, minlength, maxlength, int, float, guid, etc.

### Passing Date to Views

There are three ways to pass a model to the view:

1. Passing the model to the `View` method:

```cs
// MoviesController.cs

namespace Vidly.Controllers
{
  public class MoviesController : Controller
  {
    public ActionResult Random()
    {
      var movie = new Movie() { Name = "Shrek!" };

      return View(movie);
    }
  }
}
```

```cs
// Random.cshtml

@model Vidly.Models.Movie
@{
    ViewBag.Title = "Random";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

<h2>@Model.Name</h2>
```

1. Using `ViewData` dictionary. It is the old way and not recommended:

```cs
// MoviesController.cs

namespace Vidly.Controllers
{
  public class MoviesController : Controller
  {
    public ActionResult Random()
    {
      var movie = new Movie() { Name = "Shrek!" };

      ViewData["Movie"] = movie;

      return View();
    }
  }
}
```

```cs
// Random.cshtml

@model Vidly.Models.Movie
@{
    ViewBag.Title = "Random";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

<h2>@( ((Movie) ViewData["Movie"]).Name )</h2>
```

1. Using `ViewBag`: this approach is not recommended as well:

```cs
// MoviesController.cs

namespace Vidly.Controllers
{
  public class MoviesController : Controller
  {
    public ActionResult Random()
    {
      var movie = new Movie() { Name = "Shrek!" };

      ViewBag.Movie = movie;

      return View();
    }
  }
}
```

```cs
// Random.cshtml

@model Vidly.Models.Movie
@{
    ViewBag.Title = "Random";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

<h2>@( ((Movie) ViewBag.Movie).Name )</h2>
```

1. Using `ViewData` dictionary. It is the old way and not recommended:

```cs
// MoviesController.cs

namespace Vidly.Controllers
{
  public class MoviesController : Controller
  {
    public ActionResult Random()
    {
      var movie = new Movie() { Name = "Shrek!" };

      ViewData["Movie"] = movie;

      return View();
    }
  }
}
```

```cs
// Random.cshtml

@model Vidly.Models.Movie
@{
    ViewBag.Title = "Random";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

<h2>@( ((Movie) ViewData["Movie"]).Name )</h2>
```

**Note:** do not use `ViewData` or `ViewBag`.

### View Models

`ViewModel` in the MVC design pattern is very similar to a model. The major difference between `Model` and `ViewModel` is that we use a `ViewModel` only in rendering views. We put all our `ViewModel` classes in a `ViewModels` named folder that we create.

For example, we have a `Movie` model and a `Customer` model, but in our Random view we need to use both. So, we can create a `ViewModel` for that view that uses both models:

```cs
// RandomMovieViewModel.cs

namespace Vidly.ViewModels
{
    public class RandomMovieViewModel
    {
        public Movie Movie { get; set; }
        public List<Customer> Customers { get; set; }
    }
}
```

```cs
// Random.cshtml

@model Vidly.ViewModels.RandomMovieViewModel
@{
    ViewBag.Title = "Random";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

<h2>@Model.Movie.Name</h2>
```

### Razor View

Razor syntax is a simple programming syntax for embedding server-based code in a web page. In a web page that uses the Razor syntax, there are two kinds of content: client content and server code. Client content is the stuff you're used to in web pages: HTML markup (elements), style information such as CSS, maybe some client script such as JavaScript, and plain text.

Razor syntax lets you add server code to this client content. If there's server code in the page, the server runs that code first, before it sends the page to the browser. By running on the server, the code can perform tasks that can be a lot more complex to do using client content alone, like accessing server-based databases.

Main Razor syntax rules for C#:

* Razor code blocks are enclosed in @{ ... }
* Inline expressions (variables and functions) start with @
* Code statements end with semicolon
* Variables are declared with the var keyword
* Strings are enclosed with quotation marks
* C# code is case sensitive
* C# files have the extension .cshtml

We can use HTML tags inside and outside the Razor bocks:

```cs
// Render.cshtml

@model Vidly.ViewModels.RandomMovieViewModel
@{
    ViewBag.Title = "Random";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

@*
    This is a comment
    On multiple lines
*@

@{
    var className = Model.Customers.Count > 0 ? "popular" : null;
}

<h2 class="@className">@Model.Movie.Name</h2>

@if (Model.Customers.Count == 0)
{
    <text>No one has rented this movie before.</text>
}
else
{
    <ul>
        @foreach (var customer in Model.Customers)
        {
            <li>@customer.Name</li>
        }
    </ul>
}
```

### Partial Views

A partial view is a view that is rendered within another view. The HTML output generated by executing the partial view is rendered into the calling (or parent) view. Like views, partial views use the `.cshtml` file extension.

Partial views are an effective way of breaking up large views into smaller components. They can reduce duplication of view content and allow view elements to be reused. Common layout elements should be specified in _Layout.cshtml. Non-layout reusable content can be encapsulated into partial views.

As a convention, we prefix the name of the partial views with `_`. We load partial views into our view by `Html.Partial()` method (we can pass a model to the partial view as the second argument of the `Partial` method):

```cs
// _NavBar.cshtml

<div class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
        <div class="navbar-header">
            <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
            </button>
            @Html.ActionLink("Vidly", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
        </div>
        <div class="navbar-collapse collapse">
            <ul class="nav navbar-nav">
                <li>@Html.ActionLink("Customers", "Index", "Customers")</li>
                <li>@Html.ActionLink("Movies", "Index", "Movies")</li>
            </ul>
            @Html.Partial("_LoginPartial")
        </div>
    </div>
</div>
```

```cs
// _Layout.cshtml

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")

</head>
<body>
    @Html.Partial("_NavBar")
    <div class="container body-content">
        @RenderBody()
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - Vidly</p>
        </footer>
    </div>

    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## Working with Data

### Entity Framework

_Entity Framework_ is an **Object-Relational Mapper** (ORM or O/RM) that enables .NET developers to work with a database using .NET objects. It maps data in a relational database into objects in our applications and eliminates the need for most of the data-access code that developers usually need to write (e.g. opening a connection to a database, executing queries, closing the connection, etc.).

Entity Framework provides a class called DbContext which is a gateway to our database. A DbContext has one or more DbSets which represent tables in our database. We use _Linq_ to query these DbSets and Entity Framework will translate our Linq queries to SQL queries at runtime. It opens a connection to the database, reads the data, maps it to objects, and adds them to DbSets in our DbContext. As we add, modify, or remove objects in these DbSets, Entity Framework keeps track of these changes. When we ask it to process the changes, it will automatically generate SQL statements and executes them on our database.

There are two workflows to use Entity Framework:

1. Database First
1. Code First

### Database-first vs Code-first

**Database-first:** in this approach Database and tables are created first. Then you create entity Data Model using the created database.

**Code-first:** in this approach we will first create entity classes with properties defined in it. Entity framework will create the database and tables based on the entity classes defined. So database is generated from the code. When the dot net code is run database will get created.

#### Which approach is better?

There are few reason to favor code-first to database-first:

1. Increases productivity: we don't have to waste our time with table designers. It is much faster to write codes because we don't have to manually deploy the changes to the database.
1. Full versioning of database: we can migrate it to any version at any point in time. So, if we are maintaining an older version of our application, we can migrate the database to an earlier state.
1. Much easier to build an integration test database simple.

#### Myths about Code-first

1. _It is only useful with greenfield or new projects_. This is not true because we get the full version of our database from the moment that we switch to code-first. Plus you don't waste time creating tables and migration scripts anymore. You just build your domain classes which is much faster.
1. _It doesn't give you full control over the database_. This is not true as well.

### Code-first Migrations

In code-first workflow we start with a code. Every time we modify our domain model by adding a class or modifying one of the existing one, we create a migration and then run it on the database. We do migrations in _Package Management Console_. The first time we use migration we need to enable it:

```none
PM> enable-migrations
```

This will create a folder, Migrations, and all our migrations will be stored there.

**Note:** In code-first migration we should not add data to database manually. We should use migration to add new data.


Now we can create our migrations by `add-migration migration_name`. `migration_name` should be a name that identifies the kind of change we have made to our domain model (e.g. `InitialModel` for the first migration). We can overwrite an existing migration using `-force` switch:

```none
PM> add-migration InitialModel
PM> add-migration InitialModel -force
```

The recommended way to work with context is to define a class that derives from DbContext and exposes `DbSet` properties that represent collections of the specified entities in the context. If you are working with the EF Designer, the context will be generated for you. If you are working with Code First, you will typically write the context yourself.

```cs
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```

Once you have a context, you would query for, add (using `Add` or `Attach` methods ) or remove (using `Remove`) entities in the context through these properties. Accessing a `DbSet` property on a context object represent a starting query that returns all entities of the specified type. 

**Note:** just accessing a property will not execute the query. A query is executed when:

* It is enumerated by a `foreach` statement.
* It is enumerated by a collection operation such as `ToArray`, `ToDictionary`, or `ToList`.
* LINQ operators such as `First` or `Any` are specified in the outermost part of the query.
* The following methods are called: the `Load` extension method on a `DbSet`, `DbEntityEntry.Reload`, and `Database.ExecuteSqlCommand`.

In our example project, we can add a `DbSet` for customers to the `ApplicationDbContext` class (in `IdentityModels.cs`) which is derived from `IdentityDbContext` class that is derived from `DbContext`:

```cs
  public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
  {

    public DbSet<Customer> Customers { get; set; }

    public ApplicationDbContext()
        : base("DefaultConnection", throwIfV1Schema: false)
    {
    }

    public static ApplicationDbContext Create()
    {
      return new ApplicationDbContext();
    }
  }
```

This `DbSet` represents the `Customer` table in our database. Now that `DbContext` is aware of our `Customer` class we can create the table by doing a migration using `add-migration`.

To create or update the database with the new migrations we need to execute the following code in the Package Management Console:

```none
PM> update-database
```

This will create or update a new database file under `App_Data` folder. We can double click on the file to see the content of the database.

### Changing the Model

We should avoid big bang migration because it will increase the risk of things going wrong. Instead of making all the changes to the database in one go, we should make small changes, create a migration, and run it on the database. This allows us to better version our code (just like git).

**Note:** in Entity Framework, every entity must have a key that will be mapped to the primary key of the corresponding table in the database. By convention, this property should be called either `Id` or the name of the type plus `Id` (e.g. `CustomerId`).

A _navigation property_ is an optional property on an entity type that allows for navigation from one end of an association to the other end. Unlike other properties, navigation properties do not carry data.

For example, we can create a navigation property so that we can load the `Customer` and its `MembershipType` together. We can also add the `MembershipTypeId` as the foreign key. Even though the ID in MembershipType class is defined as `Id`, Entity Framework will recognize `MembershipTypeId` and treats it as a foreign key:

```cs
// Customer.cs

public class Customer
{
  public int Id { get; set; }
  public string Name { get; set; }
  public bool IsSubscribedToNewsletter { get; set; }
  public MembershipType MembershipType { get; set; }
  public byte MembershipTypeId { get; set; }
}
```

```cs
// MembershipType.cs

public class MembershipType
{
  public byte Id { get; set; }
  public short SignUpFee { get; set; }
  public byte DurationInMonths { get; set; }
  public byte DiscountRate { get; set; }
}
```

A navigation property definition includes the following:

* A name. (Required)
* The association that it navigates. (Required)
* The ends of the association that it navigates. (Required)

Note that navigation properties are optional on both entity types at the ends of an association. If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.

### Seeding the Database



















### Overriding Conventions

We can override default conventions by adding Data Annotations:

```C#
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.Linq;
using System.Web;

namespace Vidly.Models
{
  public class Customer
  {
    public int Id { get; set; }
    [Required]
    [StringLength(255)]
    public string Name { get; set; }
    public bool IsSubscribedToNewsletter { get; set; }
    public MembershipType MembershipType { get; set; }
    public byte MembershipTypeId { get; set; }
  }
}
```

In the above example the `Name` property is of type `string` which by default is nullable and unlimited length but by using `[Required]` we can make _not nullable_ and assign maximum length by `[StringLength(255)]`

### Querying Objects

* To work with a database we need a `DbContext` to have access to the database.

* DbContext is a disposable object so we need to properly dispose `_context` by overriding the `Dispose` method of the base controller class.

```C#
using System.Collections.Generic;
using System.Linq;
using System.Web.Mvc;
using Vidly.Models;

namespace Vidly.Controllers
{
  public class CustomersController : Controller
  {
    private ApplicationDbContext _context;
    public CustomersController()
    {
      _context = new ApplicationDbContext();
    }

    protected override void Dispose(bool disposing)
    {
      _context.Dispose();
    }
```

* in the following code, entity framework is not going to query the database when the `var customers = _context.Customers;` is executed. This is what we call deferred execution. The query is executed when we iterate over the `customers` object (e.g. in the `View`):

```C#
    // CustomersController.cs

    public ViewResult Index()
    {
      var customers = _context.Customers;

      return View(customers);
    }
```

```C#
    // Index.cshtml

    @foreach (var customer in Model) // this iterates over customer
```

* we can immediately execute the query by calling methods like `ToList` or `SingleOrDefault`.

```C#
    // CustomersController.cs

    public ViewResult Index()
    {
      var customers = _context.Customers.ToList();

      return View(customers);
    }

    public ActionResult Details(int id)
    {
      var customer = _context.Customers.SingleOrDefault(c => c.Id == id);

      if (customer == null)
        return HttpNotFound();

      return View(customer);
    }
```

### Eagerly Loading

_Eager loading_ is the process whereby a query for one type of entity also loads related entities as part of the query. Eager loading is achieved by use of the `Include` method. For example, the query below will load customers and the membership type related to each customer:

```C#
    public ViewResult Index()
    {
      var customers = _context.Customers.Include(c => c.MembershipType).ToList();

      return View(customers);
    }
```

so, in the following example `customer` also includes the membership information:

```C#
      @foreach (var customer in Model)
      {
        <tr>
          <td>@Html.ActionLink(customer.Name, "Details", "Customers", new { id = customer.Id }, null)</td>
          <td>@customer.MembershipType.DiscountRate%</td>
        </tr>
      }
```









