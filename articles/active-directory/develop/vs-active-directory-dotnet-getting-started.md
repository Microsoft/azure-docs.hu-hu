---
title: Ismerkedés az Azure AD-vel a .NET MVC-projektekben | Azure
description: A .NET MVC-projektek Azure Active Directory használatának első lépései az Azure AD-vel való csatlakozás vagy a Visual Studio Connected Services használatával való létrehozás után
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: 15a7c873e4d1e5c962a89b03f2a5cafc88843192
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88165464"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Első lépések Azure Active Directory (ASP.NET MVC-projektekkel)

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-dotnet-getting-started.md)
> - [mi történt](vs-active-directory-dotnet-what-happened.md)

Ez a cikk további útmutatást nyújt, miután hozzáadta Active Directory egy ASP.NET MVC-projekthez a Visual Studio **project > Connected Services** parancsával. Ha még nem adta hozzá a szolgáltatást a projekthez, bármikor megteheti.

Megtudhatja [, mi történt az MVC-projekttel?](vs-active-directory-dotnet-what-happened.md) a projektben a csatlakoztatott szolgáltatás hozzáadásakor végrehajtott módosítások.

## <a name="requiring-authentication-to-access-controllers"></a>Hitelesítés megkövetelése a hozzáférés-vezérléshez

A projekt összes vezérlőjét az `[Authorize]` attribútummal díszítették. Ez az attribútum megköveteli a felhasználó hitelesítését a vezérlők elérése előtt. Annak engedélyezéséhez, hogy a vezérlő névtelenül legyen elérhető, távolítsa el ezt az attribútumot a vezérlőből. Ha az engedélyeket részletesebben szeretné beállítani, alkalmazza az attribútumot minden olyan metódusra, amely az engedélyezést igényli a vezérlő osztályra való alkalmazása helyett.

## <a name="adding-signin--signout-controls"></a>Bejelentkezési-/kijelentkezés-vezérlők hozzáadása

A bejelentkezési/kijelentkezés vezérlőelemeknek a nézethez való hozzáadásához a `_LoginPartial.cshtml` részleges nézetet használhatja a funkciók egyik nézethez való hozzáadásához. Íme egy példa a standard nézethez hozzáadott funkciókra `_Layout.cshtml` . (Jegyezze fel az utolsó elemet a div-összecsukási osztályhoz:

```html
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
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>Következő lépések

- [Hitelesítési forgatókönyvek Azure Active Directory](./authentication-vs-authorization.md)
- [Bejelentkezés felvétele a Microsofttal egy ASP.NET-webalkalmazásba](quickstart-v2-aspnet-webapp.md)