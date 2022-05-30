# 📄 Blazor Guidelines 📄

A set of coding guidelines for Microsoft Blazor projects, design principles and layout rules for improving the overall quality of your code development.

This part of the document is mainly based...

1.	On our experience to write the simplest and best performing code possible.
2.	On [how to create and use Razor components in Blazor apps](https://docs.microsoft.com/en-us/aspnet/core/blazor/components) documentation.
3.	On [Microsoft Coding Conventions](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions).

## 🔸Component Name

- Component file paths use **Pascal Case**. Paths indicate typical folder locations. For example, `Pages/ProductDetailPage.razor` indicates that the `ProductDetailPage` component has a file name of `ProductDetailPage.razor` and resides in the `Pages` folder of the app.

- Component file paths for routable components match their URLs with hyphens appearing for spaces between words in a component's route template. For example, a `ProductDetailPage` component with a route template of `@page "/product-detail"` is requested in a browser at the relative URL `/product-detail`.

- A component's name uses Pascal case. For example, `ProductDetailPage.razor` is valid.

## 🔸Component Structure

Organize files and components in a folder structure like this. This makes it easy to find the code related to a page, without having to browse the entire file explorer. Try, as much as possible, to respect the [SOLID](https://en.wikipedia.org/wiki/SOLID) principles. Mainly by creating autonomous and extensible components: inject the smallest possible service or parameter, manage all the possibilities offered by the component. For example, a data modification page should display the data, check their values and save the data at the end of the process.

- Group pages and dialog boxes of the **same module** in a sub-folder of **Pages** folder, with this module name (pluralized). For example: `Pages\Assets\`.
- Suffix pages with **Page.razor** and dialog boxes with **Dialog.razor**. Name the page name in the singular. For example: `\Pages\Assets\AssetMainPage.razor` and `\Pages\Assets\AssetEditDialog.razor`.
- Put the **parameters** of a dialog box in a class `[Name]DialogParameters`.
And save it into the file `[Name]Dialog.razor.Parameters.cs`, to use the "**Nested files**" representation in Visual Studio. See the example `AssetCloneDialog.razor.Parameters.cs` below.
- The code behind a dialog box (for example, to save data) must be in this class. For example, in the **OnCloseAsync** method (or equivalent method) of this class. Don’t write logic dialog box features in the main call window (except to refresh the main screen).
- Common and project shared components (between multiple modules) are stored in a folder **Components**, and are suffixed by `Component`.

For example:
```
|-- App.razor
|-- Components
|   |-- TimeComponent.razor
|   |-- TimeComponent.razor.cs
|-- Pages
|   |-- Calendars
|   |   |-- CalendarMainPage.razor
|   |   |-- CalendarMainPage.razor.cs
|   |   |-- CalendarMainPage.razor.css
|   |   |-- CalendarMainPage.razor.js
|   |   |-- CalendarEditDialog.razor
|   |   |-- CalendarEditDialog.razor.cs
|   |   |-- CalendarEditDialog.razor
|   |   |-- CalendarEditDialog.razor.Parameters.cs
```

VSCode and Visual Studio have a File nesting feature to regroup visually each item of same page or dialog. In VSCode, set this settings: `"explorer.fileNesting.patterns": { "$(capture).razor, $(capture).razor.*"`.


## 🔸C# code in the .razor.cs

From our experience, we have chosen not to use the ```@code``` element in files with the ```.razor``` extension. 
C# code should be in the ```.razor.cs``` file provided for this purpose.

Discussion via issue Discussion via [issue #2](https://github.com/Apps72/BlazorGuidelines/issues/2)

## 🔸Add injection via Code Behind

The .razor is only used for the design part and the layout of this HTML design (via loops and Razor tests).
Put all the code in the "Code Behind" file, including properties injected (using private accessor). 
All attributes are above of the property/method.

```csharp
[Inject]
private IMemoryCache MemoryCache { get; set; }
```

Discussion via issue Discussion via [issue #1](https://github.com/Apps72/BlazorGuidelines/issues/1)

## 🔸Send HTML as soon as possible

For example, if you have a component that is linked to a sub-property, **it is better to set default values for all linked properties**, instead of testing the initial variable. 

```razor
// DON'T USE
@if (MyData != null)
{
    <input type="text" value="MyData.Firstname">
}
```

```razor
// PREFERS
<input type="text" value="MyData.Firstname">

@code 
{
     MyData MyData { get; set; } = new MyData();
}
```

This allows Blazor to send the HTML code to the client who can display it. 
Then Blazor, via SignalR, will update the content of the component that is already drawn.
Otherwise, an empty page is displayed to the user, while the whole component is known... 
which is not ergonomically pleasant for the user.

Th easy way to do that, is to bind to empty objects... including all sub properties must be defined.

Discussion via [issue #11](https://github.com/Apps72/BlazorGuidelines/issues/11)

## 🔸Abuse of CSS isolation

It is preferable to use as much as possible a css / .razor.

```
MyPage.razor
  \-- MyPage.razor.cs
  \-- MyPage.razor.css
```

Discussion via [issue #4](https://github.com/Apps72/BlazorGuidelines/issues/4)

## 🔸JavaScript collocated with a component

[Collocation of JavaScript files](https://docs.microsoft.com/en-us/aspnet/core/blazor/javascript-interoperability/?view=aspnetcore-6.0#load-a-script-from-an-external-javascript-file-js-collocated-with-a-component) for pages and Razor components is a convenient way to organize scripts in an app: it's easier to centralize and find the JS code associated to a component. It does not pollute the client with global functions. And the component will load this code only when needed.
Save the JavaScript code in a .js file below the component.

1.	Create a `[Component].razor.js` file with your JavaScript code.

```javascript
export function myModule_myJsMethod() {
    ...
}
```

2.	Add the following constant and two properties.

```csharp
private const string JAVASCRIPT_FILE = "./Pages/[Path]/[Component].razor.js";
		  
[Inject]
private IJSRuntime JsRuntime { get; set; } = default!;

private IJSObjectReference JsModule { get; set; } = default!;
```

3.	Call the JavaScript function, from your C# code, as follows.

```csharp
if (JsModule == null)
{
  JsModule = await JsRuntime.InvokeAsync<IJSObjectReference>("import", JAVASCRIPT_FILE);
}
await JsModule.InvokeVoidAsync("myModule_myJsMethod");
```

## 🔸Methods and properties

The variables and properties must be organized in the following order.

1.	Private **variables** (using readonly if possible) and constants
2.	The **[Inject]** properties
3.	**[Parameter]** properties
4.	**Public** methods and properties
5.	**Internal** and **Protected** methods/properties
6.	**Private** methods/properties

Place the **attributes** (Inject, Parameter, …) above the property.
```csharp
[Inject]
public IJSRuntime JsRuntime { get; set; } = default!;
```

Since the project has been defined as accepting nullables, it is recommended to define non-null variables and properties with a `default!` value. To avoid having a warning, at compile time, assign this value by default, only to properties that do not accept null values. For properties that accept null values, don't forget to define them with the `?` (including simple types or objects like `string?`, `int?` or `object?`.
```csharp
public IConnectionManager Connection { get; set; } = default!;
```




# ❔How it works and how to contribute?

## 🔹This project is discussion based

In order to put values on this guidelines, you need to create an issue and a context to allow understand your lines. 

When it's validated by the community, you can then create a pull request to add values on this project.

## 🔹Owners

This project is owned by : 

* Adrien Clerbois @AClerbois
* Denis Voituron @Dvoituron
* Christophe Peugnet @Tossnet

## 🔹Code of Conduct
This project has adopted the code of conduct defined by the Contributor Covenant to clarify expected behavior in our community. For more information, see the [Code of Conduct.](https://github.com/Apps72/BlazorGuidelines/blob/main/CODE_OF_CONDUCT.md)
