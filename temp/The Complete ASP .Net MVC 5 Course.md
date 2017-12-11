# The Complete ASP .Net MVC 5 Course

## Useful Visual Studio Extensions

* Productivity Power Tools 2013
* Web Essentials 2013.5
* ReSharper (not free but worth it)

## Shortcuts

* `Ctrl` + `M` + `M`: collapse and expand
* `Ctrl` + `Shift` + `B`: build
* `Ctrl` + `F5`: build and open in browser
* `ctor` + `Tab`: creates the default constructor
* `prop` + `Tab`: creates a new property

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

* in the following code, entity framework is not going to query the database when the  is executed. 

```C#
    public ViewResult Index()
    {
      var customers = _context.Customers;

      return View(customers);
    }
```








