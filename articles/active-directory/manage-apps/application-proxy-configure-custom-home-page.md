---
title: Közzétett alkalmazások egyéni kezdőlapja – Azure AD Application Proxy
description: Az Azure AD Application Proxy-összekötők alapjait ismerteti
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ebef93455414c114fb60df9af8ebc629769a33e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254428"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Egyéni Kezdőlap beállítása a közzétett alkalmazásokhoz az Azure AD Application Proxy használatával

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy alkalmazást egy felhasználó egyéni kezdőlapra való irányításához. Ha alkalmazásproxy-alkalmazással tesz közzé egy alkalmazást, belső URL-címet kell beállítania, de néha ez nem az a lap, amelyet a felhasználónak először látnia kell. Állítson be egy egyéni kezdőlapot, hogy a felhasználó megkapja a jobb oldalt, amikor hozzáférnek az alkalmazáshoz. A felhasználó megtekintheti a beállított egyéni kezdőlapot, függetlenül attól, hogy a Azure Active Directory saját alkalmazások vagy a Microsoft 365 app Launcher alkalmazásból férnek hozzá az alkalmazáshoz.

Amikor egy felhasználó elindítja az alkalmazást, a rendszer alapértelmezés szerint a közzétett alkalmazás gyökértartomány URL-címére irányítja. A Kezdőlap általában a Kezdőlap URL-címeként van beállítva. Az Azure AD PowerShell-modullal definiálhat egy egyéni Kezdőlap URL-címet, ha azt szeretné, hogy egy alkalmazás egy adott oldalon az alkalmazáson belül egy adott lapra szálljon.

Íme egy forgatókönyv, amely leírja, hogy a vállalat miért állította be az egyéni kezdőlapot:

- A vállalati hálózaton belül a felhasználók `https://ExpenseApp/login/login.aspx` bejelentkezhetnek, és hozzáférhetnek az alkalmazáshoz.
- Mivel más eszközökkel (például képekkel) rendelkeznek, amelyeket az alkalmazásproxy a mappa struktúrájának legfelső szintjén kell elérnie, az alkalmazást a `https://ExpenseApp` belső URL-címként teheti közzé.
- Az alapértelmezett külső URL-cím `https://ExpenseApp-contoso.msappproxy.net` :, amely nem végez külső felhasználót a bejelentkezési oldalon.
- `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx`Ehelyett a Kezdőlap URL-címét szeretné beállítani, így a külső felhasználók először a bejelentkezési oldalt láthatják.

> [!NOTE]
> Ha hozzáférést ad a felhasználóknak a közzétett alkalmazásokhoz, az alkalmazások megjelennek a [saját alkalmazások](../user-help/my-apps-portal-end-user-access.md) és az [Office 365 app Launcher](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)alkalmazásban.

## <a name="before-you-start"></a>Előkészületek

A Kezdőlap URL-címének beállítása előtt vegye figyelembe a következő követelményeket:

- A megadott elérési útnak a gyökértartomány URL-címéhez tartozó altartomány elérési útnak kell lennie.

  Ha például a gyökér-tartomány URL-címe `https://apps.contoso.com/app1/` , a konfigurált Kezdőlap URL-címének a következővel kell kezdődnie: `https://apps.contoso.com/app1/` .

- Ha módosítja a közzétett alkalmazást, a módosítás a Kezdőlap URL-címének értékét is visszaállíthatja. Ha a jövőben frissíti az alkalmazást, újra kell ellenőriznie, és szükség esetén frissítenie kell a Kezdőlap URL-címét.

A Kezdőlap URL-címét a Azure Portal vagy a PowerShell használatával is beállíthatja.

## <a name="change-the-home-page-in-the-azure-portal"></a>A Kezdőlap módosítása a Azure Portalban

Ha módosítani szeretné az alkalmazás kezdőlapjának URL-címét az Azure AD-portálon, kövesse az alábbi lépéseket:

1. Jelentkezzen be rendszergazdaként a [Azure Portalba](https://portal.azure.com/) .
1. Válassza a **Azure Active Directory**, majd a **Alkalmazásregisztrációk** lehetőséget. Megjelenik a regisztrált alkalmazások listája.
1. Válassza ki az alkalmazást a listából. Megjelenik a regisztrált alkalmazás részleteit megjelenítő oldal.
1. A **kezelés** területen válassza a **branding (védjegyezés**) lehetőséget.
1. Frissítse a **Kezdőlap URL-címét**  az új elérési úttal.

   ![A Kezdőlap URL-címét tartalmazó regisztrált alkalmazás védjegyezési lapja](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Kattintson a **Mentés** gombra.

## <a name="change-the-home-page-with-powershell"></a>Kezdőlap módosítása a PowerShell-lel

Egy alkalmazás kezdőlapjának a PowerShell használatával történő konfigurálásához a következőket kell tennie:

1. Telepítse az Azure AD PowerShell-modult.
1. Az alkalmazás ObjectId-értékének megkeresése.
1. Frissítse az alkalmazás kezdőlapjának URL-címét a PowerShell-parancsokkal.

### <a name="install-the-azure-ad-powershell-module"></a>Az Azure AD PowerShell-modul telepítése

Mielőtt egyéni Kezdőlap URL-címet definiál a PowerShell használatával, telepítse az Azure AD PowerShell-modult. A csomagot letöltheti a [PowerShell-Galéria](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), amely a Graph API végpontot használja.

A csomag telepítéséhez kövesse az alábbi lépéseket:

1. Nyisson meg egy szabványos PowerShell-ablakot, és futtassa a következő parancsot:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Ha nem rendszergazdaként futtatja a parancsot, használja a `-scope currentuser` kapcsolót.

1. A telepítés során a Nuget.org két csomag telepítéséhez válassza az **Y** lehetőséget. Mindkét csomagot kötelező megadni.

### <a name="find-the-objectid-of-the-app"></a>Az alkalmazás ObjectId megkeresése

Az alkalmazás ObjectId úgy érheti el, hogy a megjelenített név vagy a Kezdőlap alapján keresi az alkalmazást.

1. Ugyanebben a PowerShell-ablakban importálja az Azure AD-modult.

   ```powershell
   Import-Module AzureAD
   ```

1. Jelentkezzen be az Azure AD-modulba bérlői rendszergazdaként.

   ```powershell
   Connect-AzureAD
   ```

1. Keresse meg az alkalmazást. Ez a példa a PowerShell használatával keresi meg a ObjectId az alkalmazás megjelenített névvel való keresésével `SharePoint` .

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Az itt láthatóhoz hasonló eredményt kell kapnia. Másolja a ObjectId GUID azonosítót a következő szakaszban használt értékre.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Azt is megteheti, hogy lekéri az összes alkalmazás listáját, megkeresi az alkalmazás listáját egy adott megjelenítendő névvel vagy kezdőlaptal, majd az alkalmazás ObjectId másolja az alkalmazást.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>A Kezdőlap URL-címének frissítése

Hozza létre a Kezdőlap URL-címét, és frissítse az alkalmazást az adott értékkel. Folytassa ugyanazt a PowerShell-ablakot, vagy ha új PowerShell-ablakot használ, jelentkezzen be ismét az Azure AD-modulba a használatával `Connect-AzureAD` . Ez után kövesse az alábbi lépéseket:

1. Hozzon létre egy változót, amely az előző szakaszban másolt ObjectId értéket fogja tárolni. (Cserélje le az ebben a SharePoint-példában használt ObjectId-értéket az alkalmazás ObjectId értékével.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. A következő parancs futtatásával ellenőrizze, hogy rendelkezik-e a megfelelő alkalmazással. A kimenetnek azonosnak kell lennie az előző szakaszban látott kimenettel ([Keresse meg az alkalmazás ObjectId](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Hozzon létre egy üres alkalmazásobjektum-objektumot, amely a kívánt módosításokat fogja tárolni.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Állítsa be a Kezdőlap URL-címét a kívánt értékre. Az értéknek a közzétett alkalmazás altartományának elérési útjának kell lennie. Ha például megváltoztatja a Kezdőlap URL-címét a rendszerre `https://sharepoint-iddemo.msappproxy.net/` `https://sharepoint-iddemo.msappproxy.net/hybrid/` , az alkalmazás felhasználói közvetlenül az egyéni kezdőlapra kerülnek.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. A Kezdőlap frissítésének elkészítése.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. A módosítás sikerességének ellenőrzéséhez futtassa újra a 2. lépésből a következő parancsot.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Példánkban a kimenetnek ekkor a következőképpen kell megjelennie:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Indítsa újra az alkalmazást annak megerősítéséhez, hogy a Kezdőlap az elvárt módon jelenik meg az első képernyőn.

> [!NOTE]
> Előfordulhat, hogy az alkalmazáson végzett módosítások visszaállítják a Kezdőlap URL-címét. Ha a Kezdőlap URL-címe alaphelyzetbe áll, a szakasz lépéseit megismételve állíthatja vissza.

## <a name="next-steps"></a>Következő lépések

- [Távoli hozzáférés engedélyezése a SharePointhoz az Azure AD-alkalmazásproxy segítségével](application-proxy-integrate-with-sharepoint-server.md)
- [Oktatóanyag: helyi alkalmazás hozzáadása a távoli eléréshez az alkalmazásproxy használatával Azure Active Directory](application-proxy-add-on-premises-application.md)
