# Adding Pages

Using the same project from the [how to start guide](how-to-start.md). Let's add two pages to it. One hello page and a page that can get your name from the URL.

## Creating the View

To begin, create a `Hello` folder inside the `src/SaturnSample` folder.

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

One of the dependency required is [Giraffe View Engine](https://github.com/giraffe-fsharp/Giraffe/blob/master/DOCUMENTATION.md#giraffe-view-engine). This will allow your project to define html within your function. The `index` function will result in the following html code.

```html
<div>
    <h2>Hello from Saturn!</h2>
</div>
```

## Creating the controller

Create a `HelloController.fs` file inside the `Hello` folder.

The `index` function tell us what the html will be but we still need to tell Saturn to return it as an html page. We also need to tell Saturn what where the page is

Insert the following into the file.

```fsharp
namespace Hello

open Saturn
open Giraffe.ResponseWriters

module Controller =
    let indexAction =
        htmlView (Views.index)

    let helloView = scope {
        get "/" indexAction
    }
```

The `indexAction` tell Saturn to create an html page using the `index` function inside "HelloViews.fs"

`helloView` let Saturn know that the page is located at the root.

## Adding it to Router.fs

After setting up the route. You need to update the project with the new route. Inside "Router.fs", add the following to the inside of the `browserRouter` function

```fsharp
forward "/hello" HelloView
```

This means that when we navigate to [http://localhost:8085/hello](http://localhost:8085/hello). The `helloView` function will determine what page to load there. Looking inside the `helloView` function, we said that `indexAction` is called at the root. In conclusion, the page will be located at [http://localhost:8085/hello/](http://localhost:8085/hello/). (Note the "/" at the end)

Now run the program and go to [http://localhost:8085/hello/](http://localhost:8085/hello/) and you will see a page saying "Hello from Saturn!"


## Adding the 2 new files into SaturnSample.fsproj as below
`  <ItemGroup>
    <Compile Include="Database.fs" />
    <Compile Include="Config.fs" />
    <Compile Include="Hello\HelloViews.fs" />
    <Compile Include="Hello\HelloController.fs" />
`
## Sending parameter to your page

What if you want the page to display your name?

One way to retrieve your name is to get it from the route. So when you go to [http://localhost:8085/hello/{yourname}](http://localhost:8085/hello/{yourname}) with {yourname} being your actual name, it will grab your name which can then be use to display it on the page.

To begin, add a new view in your `HelloViews.fs`

```fsharp
  let index2 (name : string) =
    div [] [
        h2 [] [rawText ("Hello " + name + "!")]
    ]
```

This function require passing in the name to be displayed. The name will be retrieved from the route.

Add the following to the `HelloController.fs` file below the `helloView` handler.

```fsharp
let index2Action name=
    htmlView (Hello.Views.index2 name)
```

Now to set up the route. Add the following to the `HelloView` handler.

```fsharp
getf "/%s" index2Action
```

"%s" is a format string. This let Saturn knows to save whatever you type in that spot. Since we want to save a name, we want to save it as a string so we use `%s`.

There are other format string for different types.

| Format Char | Type |
| ----------- | ---- |
| `%b` | `bool` |
| `%c` | `char` |
| `%s` | `string` |
| `%i` | `int` |
| `%d` | `int64` |
| `%f` | `float`/`double` |
| `%O` | `Guid` |

Now run the program and go to [http://localhost:8085/hello/{yourname}](http://localhost:8085/hello/{yourname}) and replace `{yourname}` with your name to see a page that will greet you.
