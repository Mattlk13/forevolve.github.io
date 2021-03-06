---
title: 'DataAnnotations Localization using ASP.NET Core 2 and ForEvolve.AspNetCore.Localization'
subtitle: 'The basics'
date: 2017-12-29 00:00:00 -0500
post-img: '//cdn.forevolve.com/blog/images/articles-header/2017-12-00-data-annotation-localization-using-asp-net-core.jpg'
unsplash-credit: Photo by Christine Roy on Unsplash
lang: en
categories: en/articles
tags:
    - Asp.Net Core
    - MVC
    - C#
    - Localization
    - DataAnnotations
    - ForEvolve Framework
proficiency-level: Intermediate
updates:
    - { date: 2017-12-29, description: 'Initial article.' }
    - { date: 2018-10-03, description: 'Update the "The NuGet package" section to use nuget.org instead of the CI feed.' }
---

In the past few months, I worked on a few projects, some are more long terms than others but all in all, I ended up adding a few features to my toolbox: the ForEvolve Framework.

This article will focus on `ForEvolve.AspNetCore.Localization`. I will show you how you can enable localization of `DataAnnotations` error messages with 3 lines of code. All the usual Asp.Net Core 2 localization plumbing is also done for you.<!--more-->

## The other tools

_If you can't wait to read about localization, you can skip this part and jump right to "The NuGet package" section below..._

The long terms projects that I am building are:

1. An identity server using IdentityServer4; I am a little tired of writing authentication code.
1. Some Web APIs (and ultimately some UIs), using microservices design patterns.

Due to those projects, I added a few testing utilities in a new project that I named `ForEvolve.XUnit`. This package is not part of the meta-package since it's only test stuff. I also added some HTTP testing helpers and some Identity mocks (pre-linked together). This is a really early release but feel free to take a look at it on [GitHub](https://github.com/ForEvolve/ForEvolve-Framework/tree/master/src/ForEvolve.XUnit) if you want.

In my APIs design, I have raw endpoints, one or more Gateway, and multiple user interfaces. To communicate between the Gateway and the raw endpoints, I need a client. I am still in the process of building that client, and once I am done, I will try to extract a base HTTP client "framework" out of it to make it easier to future projects. Doing this leads me to build a programmable test server with expected responses and status code allowing easy testing of the client.

This is not yet done and, until merged, is located in the [base-http-test-server](https://github.com/ForEvolve/ForEvolve-Framework/tree/feature/base-http-test-server) branch on GitHub.

If you have any interest in using the test server or the base HTTP client, feel free to manifest yourself, and I will try to find a way to ask for your inputs.
The more brains, the more chances of improving a design!

## The NuGet package

First, you need to install the `ForEvolve.AspNetCore.Localization` NuGet package by typing `dotnet add package ForEvolve.AspNetCore.Localization` or `Install-Package ForEvolve.AspNetCore.Localization`. It is also included in the `ForEvolve.App` meta-package.

For more information about my NuGet packages, please visit [GitHub](https://github.com/ForEvolve/ForEvolve-Framework) or [NuGet](https://www.nuget.org/profiles/ForEvolve).

### Source code

See GitHub for the [source code](https://github.com/ForEvolve/ForEvolve.AspNetCore.Localization), the supported languages and the supported attributes.

## The code

Before beginning, I've built 2 samples MVC applications available on [GitHub](https://github.com/ForEvolve/ForEvolve.Blog.Samples/tree/master/Localization%20-%20Samples). They both are a basic contact form.

-   **01-Basic**: This project only localizes error messages.
-   **02-Basic with Resources**: This project localizes error messages and uses the default resources directory to localize `DisplayAttribute` as well.

Both projects use a similar `ContactViewModel` class and the same views (mostly auto-generated by VS).

```csharp
public class ContactViewModel
{
    [Required]
    [MaxLength(20)]
    [Display(Name = "First name")]
    public string FirstName { get; set; }

    [Required]
    [MaxLength(20)]
    [Display(Name = "Last name")]
    public string LastName { get; set; }

    [Required]
    [EmailAddress]
    [Display(Name = "Email")]
    public string Email { get; set; }

    [Url]
    [Display(Name = "Web site")]
    public string WebUri { get; set; }

    [Required]
    [MaxLength(50)]
    [Display(Name = "Subject")]
    public string Subject { get; set; }

    [Required]
    [MaxLength(2000)]
    [Display(Name = "Message")]
    public string Message { get; set; }
}
```

```csharp
@model AspNetCore.Localization.Models.ContactViewModel

@{
    ViewData["Title"] = "Contact";
}

<h2>Contact</h2>
<p>
    <a href="?culture=fr">Français</a> |
    <a href="?culture=en">English</a>
</p>
<hr />
<div class="row">
    <div class="col-md-4">
        <form asp-action="Contact">
            <div asp-validation-summary="ModelOnly" class="text-danger"></div>
            <div class="form-group">
                <label asp-for="FirstName" class="control-label"></label>
                <input asp-for="FirstName" class="form-control" />
                <span asp-validation-for="FirstName" class="text-danger"></span>
            </div>
            <div class="form-group">
                <label asp-for="LastName" class="control-label"></label>
                <input asp-for="LastName" class="form-control" />
                <span asp-validation-for="LastName" class="text-danger"></span>
            </div>
            <div class="form-group">
                <label asp-for="Email" class="control-label"></label>
                <input asp-for="Email" class="form-control" />
                <span asp-validation-for="Email" class="text-danger"></span>
            </div>
            <div class="form-group">
                <label asp-for="WebUri" class="control-label"></label>
                <input asp-for="WebUri" class="form-control" />
                <span asp-validation-for="WebUri" class="text-danger"></span>
            </div>
            <div class="form-group">
                <label asp-for="Subject" class="control-label"></label>
                <input asp-for="Subject" class="form-control" />
                <span asp-validation-for="Subject" class="text-danger"></span>
            </div>
            <div class="form-group">
                <label asp-for="Message" class="control-label"></label>
                <textarea asp-for="Message" class="form-control"></textarea>
                <span asp-validation-for="Message" class="text-danger"></span>
            </div>
            <div class="form-group">
                <a asp-action="Index" class="btn btn-default">Cancel</a>
                <input type="submit" value="Send" class="btn btn-primary" />
            </div>
        </form>
    </div>
</div>

@section Scripts {
    @{await Html.RenderPartialAsync("_ValidationScriptsPartial");}
}
```

## Enabling ForEvolve.AspNetCore.Localization

In a new or an existing ASP.NET MVC project, in `Startup.cs` add those 3 lines of code:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Localization & options
    services.AddForEvolveLocalization();

    // ...

    // MVC
    services
        .AddMvc()
        .AddForEvolveMvcLocalization();
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Localization
    app.UseForEvolveRequestLocalization();

    //...
}
```

And voila, localization of `DataAttributes` should be working.

### 01-Basic

If you run the sample application `01-Basic`, navigate to the contact page and click the "Send" button (leaving the form empty), you should see that all messages are in English.

If you click on the "Français" link and submit the form again, you should see that all messages are now in French.

The links make use of Asp.Net Core `QueryStringRequestCultureProvider` that assign the `culture=fr` querystring as the current culture (and ui-culture).
See [Globalization and localization in ASP.NET Core - Localization middleware](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/localization#localization-middleware) for more info about built-in middleware.

---

> As you may have noticed, the labels are not translated.

---

## Translating DisplayAttribute

`ForEvolve.AspNetCore.Localization` only affect error messages, not `DisplayAttribute`.
As for any other projects, to do that, you will need to create resources files and translate the strings you want.

However, by default, `ForEvolve.AspNetCore.Localization` set the default `ResourcesPath` to `"Resources"`.

This is what the second sample does, localize the `ContactViewModel` class. To achieve the same result, you must:

1. Create the `Resources` directory.
1. `ContactViewModel.cs` is located in the `Models` directory, so you also need to create the `Resources\Models` directory.
1. Now we need to create the resources file: `Resources\Models\ContactViewModel.fr.resx`.
1. Realy important here, you need to change the "Access Modifier" from "Internal" to "No code generation" ![Change the "Access Modifier" from "Internal" to "No code generation"](//cdn.forevolve.com/blog/images/2017/vs-change-resz-access-modifier.png)

Once you have a resource file, you can add translated name/value pairs to it. The name represents the string to be translated while the value represents its translated value.

### 02-Basic with Resources

If you run the sample application `02-Basic with Resources`, navigate to the contact page and click the "Send" button (leaving the form empty), you should see that all messages are in English.

If you click on the "Français" link, you should see that all labels have been translated into French.
At this point, if you submit the form, you should see that all error messages are also in French.

## Going further

Enabling `ForEvolve.AspNetCore.Localization` adds all Asp.Net Core localization features, including `ViewLocalization`.

You can configure everything in the `AddForEvolveLocalization()` extension method call. See [README => How to use => Code break down](https://github.com/ForEvolve/ForEvolve.AspNetCore.Localization#code-break-down) for more some examples.

## How to contribute a translation

Since I only know French and English, I can't translate messages into more languages, so contributions are very welcome.

It should take only a few minutes to translate error messages to a specific language.

If you are interested, please take a look at [How to contribute a translation](https://github.com/ForEvolve/ForEvolve.AspNetCore.Localization#how-to-contribute-a-translation).

## The end

I hope you enjoyed this little overview of `ForEvolve.AspNetCore.Localization`. If you have questions or ideas, feel free to leave me a comment.

Happy new coding year!
