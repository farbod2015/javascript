# The Complete ASP .Net MVC 5 Course

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

## Useful Visual Studio Extensions

* Productivity Power Tools 2017
* Web Essentials 2013.5
* ReSharper (not free but worth it)

## Shortcuts

* `Ctrl` + `M` + `M`: collapse and expand
* `Ctrl` + `Shift` + `B`: build
* `Ctrl` + `F5`: build and open in browser
* `ctor` + `Tab`: creates the default constructor
* `prop` + `Tab`: creates a new property
* `mvcaction4` + `Tab`: creates a new action
* we can add a shortcut for _Package Manager Console_ by going to `Tools`>`Options`>`Environment`>`Keyboard` and assign the preferred shortcut (e.g. `Alt`+`/`+`.`) to `View.PackageManagerConsole`

## Package Manager

Package managers handle the dependency to external libraries. So we can install packages and update them using package manager.

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









## Database-first vs Code-first

...
In code-first migration we should not add data to database manually. We should use migration to add new data.
...

## Migration Commands

* `add-migration InitialModel`
* `add-migration InitialModel -force`
* `add-migration migrationName`
* `update-database`

## Navigation Property

A _navigation property_ is an optional property on an entity type that allows for navigation from one end of an association to the other end. Unlike other properties, navigation properties do not carry data.

A navigation property definition includes the following:

* A name. (Required)
* The association that it navigates. (Required)
* The ends of the association that it navigates. (Required)

Note that navigation properties are optional on both entity types at the ends of an association. If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.

## Overriding Conventions

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

## Querying Objects

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

## Eagerly Loading

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









