# Blazor Guidelines

A set of coding guidelines for Microsoft Blazor projects, design principles and layout rules for improving the overall quality of your code development.

This project is based on a simple idea : 

> Blazor is relatively new and with the community experience, we could together create Blazor guidelines.

## - C# code in the .razor.cs

From our experience, we have chosen not to use the ```@code``` element in files with the ```.razor``` extension. 
C# code should be in the ```.razor.cs``` file provided for this purpose.

> Discussion via issue #2

## - Send HTML as soon as possible

For example, if you have a component that is linked to a sub-property, **it is better to set default values for all linked properties**, instead of testing the initial variable. 

```html
// DON'T USE
@if (MyData != null)
{
    <input type="text" value="MyData.Firstname">
}
```

```html
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

> Discussion via issue #11

# How it works and how to contribute?

**This project is discussion based**

In order to put values on this guidelines, you need to create an issue and a context to allow understand your lines. 

When it's validated by the community, you can then create a pull request to add values on this project.

## Owners

This project is owned by : 

* Adrien Clerbois @AClerbois
* Denis Voituron @Dvoituron
* Christophe Peugnet @Tossnet

## Code of Conduct
This project has adopted the code of conduct defined by the Contributor Covenant to clarify expected behavior in our community. For more information, see the [Code of Conduct.](https://github.com/Apps72/BlazorGuidelines/blob/main/CODE_OF_CONDUCT.md)
