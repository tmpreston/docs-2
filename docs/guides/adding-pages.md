# Adding Pages

Using the same project from the [how to start guide](how-to-start.md). Let us add two new pages to it. One hello page and a page that can get your name from the route.

## Creating the View

To begin, create a new folder inside your project folder in src called "Hello".
Inside the folder, create a new file called "HelloViews.fs", this file will contain the functions to create the page.
Insert the following in the file.

```fsharp
namespace Hello

open Giraffe.GiraffeViewEngine
open Saturn

module Views =
  let index =
    div [] [
        h2 [] [rawText "Hello from Saturn!"]
    ]
```

One of the dependency required is [Giraffe View Engine](https://github.com/giraffe-fsharp/Giraffe/blob/master/DOCUMENTATION.md#giraffe-view-engine). This will allow your project to define html within your function. The ``index`` function will result in the following html code.

```html
<div>
    <h2>Hello from Saturn!</h2>
</div>
```

>The index function take a HttpContext but does not do anything with it because it only creates a basic page. It is not neccessary to include it but HttpContext contains information like the connection string to your database

## Creating the route

The ``index`` function tell us what the html will be but we still need to tell Saturn to return it as an html page. We also need to tell Saturn what is the route to the page.

Go into the "Router.fs" file and add the following right after the ``defaultView`` function

```fsharp
let indexAction =
    htmlView (Hello.Views.index)

let HelloView = scope {
    get "/hello" indexAction
}
```

The ``indexAction`` tell Saturn to create an html page using the ``index`` function inside "HelloViews.fs"

``HelloView`` tell Saturn where the page will be. If you are running locally, the route will be [http://localhost:8085/hello/](http://localhost:8085/hello/)

## Adding it to Router.fs

After setting up the route. You need to update the project with the new route. Inside "Router.fs", add the following to the inside of the ``browserRouter`` function

```fsharp
forward "/hello" HelloView
```

Now run the program and go to [http://localhost:8085/hello/](http://localhost:8085/hello/) and you will see a page saying "Hello from Saturn!"

## Sending parameter to your page

What if you want the page to display your name? You need to add a new view in your ``HelloViews.fs``

```fsharp
  let show (name : string) =
    div [] [
        h2 [] [rawText ("Hello! " + name)]
    ]
```

This function require passing in the name to be displayed. The name is retrieved from the route.

Let's add a new action for this view.

```fsharp
let showAction name=
    htmlView (Hello.Views.show name)
```

Now to set up the route. Add the following to the HelloView function

```fsharp
getf "/%s" showAction
```

"%s" is a format string. This mean that whatever you type in that spot will be saved as a string for your program to use. There are other format string that can take you enter as different type.

| Format Char | Type |
| ----------- | ---- |
| `%b` | `bool` |
| `%c` | `char` |
| `%s` | `string` |
| `%i` | `int` |
| `%d` | `int64` |
| `%f` | `float`/`double` |
| `%O` | `Guid` |

Now run the program and go to [http://localhost:8085/hello/{yourname}](http://localhost:8085/hello/{yourname}) and replace "{yourname}" with your name to see a page that will greet you directly.