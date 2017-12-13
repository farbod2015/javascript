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
* we can add a shortcut for _Package Manager Console_ by going to `Tools`>`Options`>`Environment`>`Keyboard` and assign the preferred shortcut (e.g. `Alt`+`/`+`.`) to `View.PackageManagerConsole`

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









