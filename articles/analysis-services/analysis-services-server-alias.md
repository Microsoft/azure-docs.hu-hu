---
title: Alias-kiszolgálók Azure Analysis Services neve | Microsoft Docs
description: A kiszolgálónév-aliasok létrehozását és használatát ismerteti.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ea618ecb29451650cbb01e9c95d263f42d406555
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146350"
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

Ebben a példában egy ASP.NET Web Forms alkalmazást hoz létre a Visual Studióban. A mesterlap-hivatkozás és a felhasználói vezérlő el lesz távolítva az alapértelmezett. aspx lapról. Az alapértelmezett. aspx fájl tartalma egyszerűen a következő oldal direktíva:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

A Default.aspx.cs Page_Load eseménye a Response. write () metódust használja a Azure Analysis Services-kiszolgáló nevének visszaadásához.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Lásd még:

[Ügyféloldali kódtárak](analysis-services-data-providers.md)   
[Csatlakozási Power BI Desktop](analysis-services-connect-pbi.md)
