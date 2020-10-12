---
title: Alias-kiszolgálók Azure Analysis Services neve | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Azure Analysis Services kiszolgáló neve aliasokat. A felhasználók ezt követően a kiszolgáló neve helyett egy rövidebb aliast is tudnak csatlakozni a kiszolgálóhoz.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 435649c5431ff14461245fee88cebe4a2c571663
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85361438"
---
# <a name="alias-server-names"></a>Alias-kiszolgálók nevei

Kiszolgálónév-alias használatával a felhasználók a kiszolgáló neve helyett rövidebb *aliassal* csatlakozhatnak a Azure Analysis Services-kiszolgálóhoz. Ügyfélalkalmazás esetén az alias a **link://** protokoll formátumát használó végpontként van megadva. A végpont ezután a valódi kiszolgálónevet adja vissza a kapcsolódáshoz.

Az alias-kiszolgálók nevei a következőkre jók:

- Modellek migrálása a kiszolgálók között anélkül, hogy ez hatással lenne a felhasználókra. 
- A felhasználók könnyen megjegyezhető névvel láthatják el a kiszolgálókat. 
- A felhasználókat a nap különböző pontjain lévő különböző kiszolgálókra irányíthatja. 
- A különböző régiókban lévő felhasználókat a földrajzilag közelebbi példányokra irányíthatja, például az Azure Traffic Manager használatakor. 

Minden olyan HTTPS-végpont, amely érvényes Azure Analysis Services kiszolgáló nevét adja vissza, aliasként szolgálhat. A végpontnak támogatnia kell a HTTPS protokollt a 443-as porton keresztül, és a port nem adható meg az URI-ban.

![Alias hivatkozási formátum használatával](media/analysis-services-alias/aas-alias-browser.png)

Az ügyfélről való csatlakozáskor az alias-kiszolgáló nevét **link://** protokoll formátumban kell megadni. Power BI Desktop például:

![Power BI Desktop-kapcsolatok](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Alias létrehozása

Alias-végpont létrehozásához bármely olyan metódust használhat, amely érvényes Azure Analysis Services-kiszolgálónevet ad vissza. Például egy Azure Blob Storage fájlra mutató hivatkozás, amely a valódi kiszolgálónevet tartalmazza, vagy ASP.NET Web Forms alkalmazást hoz létre és tesz közzé.

Ebben a példában egy ASP.NET Web Forms alkalmazást hoz létre a Visual Studióban. Az oldal hivatkozása és a felhasználói vezérlő el lesz távolítva az alapértelmezett. aspx lapról. Az alapértelmezett. aspx fájl tartalma egyszerűen a következő oldal direktíva:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

A Default.aspx.cs Page_Load esemény a Response. write () metódust használja a Azure Analysis Services-kiszolgáló nevének visszaadásához.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Lásd még

[Ügyféloldali kódtárak](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current)   
[Csatlakozás a Power BI Desktopból](analysis-services-connect-pbi.md)
