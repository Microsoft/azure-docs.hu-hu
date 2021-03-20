---
title: Hibák diagnosztizálása az Azure AD-hez csatlakoztatott szolgáltatással (Visual Studio)
description: Az Active Directory csatlakoztatott szolgáltatás inkompatibilis hitelesítési típust észlelt
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 7b437e3117540719c8c0adc5701ac1a5e934340b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88114472"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Hibák diagnosztizálása a Azure Active Directory csatlakoztatott szolgáltatással

A korábbi hitelesítési kód észlelése közben a Azure Active Directory csatlakoztatott szolgáltatás inkompatibilis hitelesítési típust észlelt.

A projekt korábbi hitelesítési kódjának megfelelő észleléséhez újra kell építeni a projektet. Ha ezt a hibát látja, és nem rendelkezik korábbi hitelesítési kóddal a projektben, építse újra, majd próbálkozzon újra.

## <a name="project-types"></a>Projektek típusai

A csatlakoztatott szolgáltatás ellenőrzi, hogy milyen típusú projektet fejleszt, így a megfelelő hitelesítési logikát beillesztheti a projektbe. Ha van olyan vezérlő, amely a `ApiController` projektből származik, a projekt egy WebAPI projektnek tekintendő. Ha csak a projektből származtatott vezérlők találhatók `MVC.Controller` , akkor a projekt MVC-projektnek tekintendő. A csatlakoztatott szolgáltatás nem támogatja a más típusú projekteket.

## <a name="compatible-authentication-code"></a>Kompatibilis hitelesítési kód

A csatlakoztatott szolgáltatás a korábban konfigurált vagy a szolgáltatással kompatibilis hitelesítési beállításokat is ellenőrzi. Ha minden beállítás megtalálható, akkor a rendszer újbóli beosztású esetnek tekinti, és a csatlakoztatott szolgáltatás megnyitja a beállításokat.  Ha csak néhány beállítás van jelen, akkor ez egy hiba esetének minősül.

Egy MVC-projektben a csatlakoztatott szolgáltatás a következő beállítások bármelyikét ellenőrzi, ami a szolgáltatás korábbi használatából ered:

```xml
<add key="ida:ClientId" value="" />
<add key="ida:Tenant" value="" />
<add key="ida:AADInstance" value="" />
<add key="ida:PostLogoutRedirectUri" value="" />
```

Emellett a csatlakoztatott szolgáltatás a következő beállítások bármelyikét ellenőrzi egy webes API-projektben, amely a szolgáltatás korábbi használatából ered:

```xml
<add key="ida:ClientId" value="" />
<add key="ida:Tenant" value="" />
<add key="ida:Audience" value="" />
```

## <a name="incompatible-authentication-code"></a>Inkompatibilis hitelesítési kód

Végül a csatlakoztatott szolgáltatás megpróbálja észlelni a Visual Studio korábbi verzióival konfigurált hitelesítési kód verzióit. Ha ezt a hibát kapta, az azt jelenti, hogy a projekt inkompatibilis hitelesítési típust tartalmaz. A csatlakoztatott szolgáltatás a következő típusú hitelesítést észleli a Visual Studio korábbi verzióiból:

* Windows-hitelesítés
* Egyéni felhasználói fiókok
* Szervezeti fiókok

Az MVC-projektekben a Windows-hitelesítés észleléséhez a csatlakoztatott `authentication` elem a fájlban található elemet keresi `web.config` .

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

A Windows-hitelesítés webes API-projektben való észleléséhez a csatlakoztatott szolgáltatás a `IISExpressWindowsAuthentication` projekt fájljában keresi az `.csproj` elemet:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Az egyes felhasználói fiókok hitelesítésének észleléséhez a csatlakoztatott szolgáltatás megkeresi a csomag elemét a `packages.config` fájlban.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

A szervezeti fiók hitelesítésének régi formáját a csatlakoztatott szolgáltatás a következő elemre keresi `web.config` :

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

A hitelesítés típusának módosításához távolítsa el a nem kompatibilis hitelesítési típust, és próbálkozzon újra a csatlakoztatott szolgáltatás hozzáadásával.

További információ: [hitelesítési forgatókönyvek az Azure ad-hez](./authentication-vs-authorization.md).