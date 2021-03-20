---
title: Alkalmazásproxy cookie-beállításai – Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) hozzáféréssel és munkamenet-cookie-kkal rendelkezik a helyszíni alkalmazások alkalmazás-proxyn keresztüli eléréséhez. Ebből a cikkből megtudhatja, hogyan használhatja és konfigurálhatja a cookie-beállításokat.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 01/16/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62afe97b44f45bc0b7aa12b33b6a65dd94ecf095
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99252202"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Cookie-beállítások a helyszíni alkalmazások eléréséhez Azure Active Directory

Azure Active Directory (Azure AD) hozzáféréssel és munkamenet-cookie-kkal rendelkezik a helyszíni alkalmazások alkalmazás-proxyn keresztüli eléréséhez. Ismerje meg, hogyan használhatja az alkalmazásproxy cookie-beállításait. 

## <a name="what-are-the-cookie-settings"></a>Mik a cookie-beállítások?

Az [alkalmazásproxy](application-proxy.md) a következő hozzáférési és munkamenet-cookie-beállításokat használja.

| Cookie-beállítás | Alapértelmezett | Description | Javaslatok |
| -------------- | ------- | ----------- | --------------- |
| HTTP-Only cookie használata | **Nem** | **Igen** , lehetővé teszi, hogy az alkalmazásproxy tartalmazza a HTTPOnly jelzőt a http-válasz fejlécekben. Ez a jelző további biztonsági előnyöket biztosít, például megakadályozza az ügyféloldali parancsfájlok (CSS) használatát a cookie-k másolásával vagy módosításával.<br></br><br></br>Mielőtt támogatta a HTTP-Only beállítást, az alkalmazásproxy titkosított és továbbított sütiket egy biztonságos TLS-csatornán keresztül a módosítás elleni védelem érdekében. | A további biztonsági előnyök miatt használja az **Igen** lehetőséget.<br></br><br></br>**Nem** használható olyan ügyfelek vagy felhasználói ügynökök számára, akiknek hozzáférésre van szükségük a munkamenet cookie-hoz. Például a **nem** érték használata olyan RDP-vagy MTSC-ügyfél esetén, amely az alkalmazásproxy használatával csatlakozik egy távoli asztali átjáró-kiszolgálóhoz.|
| Biztonságos cookie használata | **Nem** | **Igen** , lehetővé teszi, hogy az alkalmazásproxy tartalmazza a biztonságos jelölőt a http-válasz fejlécekben. A biztonságos cookie-k biztonságosabbá teszi a cookie-kat egy TLS-védelemmel ellátott csatornán keresztül, például a HTTPS-en keresztül. Ez megakadályozza, hogy a cookie-k a cookie-k egyszerű szövegben való továbbítása miatt ne legyenek megfigyelhetők a jogosulatlan felektől. | A további biztonsági előnyök miatt használja az **Igen** lehetőséget.|
| Állandó cookie használata | **Nem** | **Igen** , az Application proxy lehetővé teszi, hogy a böngésző bezárásakor ne járjon le a hozzáférési cookie-k. Az adatmegőrzés addig tart, amíg a hozzáférési jogkivonat le nem jár, vagy amíg a felhasználó manuálisan nem törli az állandó cookie-kat. | A **nem** használható a felhasználók hitelesítésének megtartásához kapcsolódó biztonsági kockázat miatt.<br></br><br></br>Javasoljuk, hogy csak az **Igen értéket** használja olyan régebbi alkalmazásokhoz, amelyek nem oszthatják meg a sütiket a folyamatok között. Az állandó cookie-k használata helyett érdemes frissíteni az alkalmazást úgy, hogy kezelni tudja a különböző folyamatok közötti megosztási cookie-kat. Előfordulhat például, hogy állandó cookie-k szükségesek ahhoz, hogy a felhasználók egy SharePoint-webhelyről nyissák meg az Office-dokumentumokat a Explorer nézetben. Állandó cookie-k nélkül ez a művelet meghiúsulhat, ha a hozzáférési cookie-k nem vannak megosztva a böngésző, a Explorer folyamat és az Office-folyamat között. |

## <a name="samesite-cookies"></a>SameSite-cookie-k
A Chrome 80-es és újabb verzióiban a Chromium-t kihasználó böngészőkben a [SameSite](https://web.dev/samesite-cookies-explained) attribútumot nem megadó cookie-k úgy lesznek kezelve, mintha a **SameSite = LAX** értékre lettek beállítva. A SameSite attribútum azt deklarálja, hogy a cookie-k hogyan korlátozhatók egy adott hely környezetére. Ha a LAX értékre van állítva, a cookie-t csak ugyanarra a helyre irányuló kérésekre vagy legfelső szintű navigálásra kell elküldeni. Az alkalmazásproxy azonban megköveteli, hogy ezek a cookie-k megmaradjanak a harmadik féltől származó környezetben annak érdekében, hogy a felhasználók a munkamenet során megfelelően bejelentkezve maradjanak. Ennek köszönhetően az Application proxy hozzáférési és munkamenet-cookie-jai frissülnek, hogy elkerülje a változás negatív hatását. A frissítések a következők:

* A **SameSite** attribútum beállítása **none** értékre. Ez lehetővé teszi, hogy az alkalmazásproxy-hozzáférés és a munkamenetek cookie-jai megfelelően legyenek elküldve a harmadik féltől származó környezetben.
* A **biztonságos cookie** -beállítás beállításával az **Igen** beállítást használhatja alapértelmezettként. A Chrome emellett megköveteli, hogy a cookie-k megadják a biztonságos jelzőt, vagy elutasítva lesznek. Ez a módosítás az Application proxyn keresztül közzétett összes meglévő alkalmazásra érvényes lesz. Vegye figyelembe, hogy az alkalmazásproxy-hozzáférési cookie-k mindig biztonságosak, és csak HTTPS protokollal továbbíthatók. Ez a módosítás csak a munkamenet-cookie-kra vonatkozik.

Az alkalmazásproxy-cookie-k változásai az alábbi néhány héttel a Chrome 80 kiadási dátum előtt jelennek meg.

Továbbá, ha a háttérbeli alkalmazás olyan cookie-kkal rendelkezik, amelyeknek harmadik féltől származó kontextusban is elérhetőnek kell lenniük, explicit módon engedélyeznie kell az alkalmazást, hogy az SameSite = none beállítást használja a cookie-k használatához. Az alkalmazásproxy lefordítja az Set-Cookie fejlécet az URL-CÍMEKre, és tiszteletben tartja a háttér-alkalmazás által beállított cookie-k beállításait.



## <a name="set-the-cookie-settings---azure-portal"></a>A cookie-beállítások beállítása – Azure Portal
A cookie-beállítások beállítása a Azure Portal használatával:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Navigáljon **Azure Active Directory**   >  **vállalati alkalmazások**   >  **minden alkalmazás** elemre.
3. Válassza ki azt az alkalmazást, amelyhez engedélyezni kívánja a cookie-beállítást.
4. Kattintson a **alkalmazásproxy** elemre.
5. A **További beállítások** területen állítsa a cookie beállítást **Igen** vagy **nem** értékre.
6. A módosítások alkalmazásához kattintson a **Mentés** gombra. 

## <a name="view-current-cookie-settings---powershell"></a>Aktuális cookie-beállítások megtekintése – PowerShell

Az alkalmazás aktuális cookie-beállításainak megtekintéséhez használja ezt a PowerShell-parancsot:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Cookie-beállítások beállítása – PowerShell

A következő PowerShell-parancsokban az ```<ObjectId>``` alkalmazás ObjectId. 

**Csak HTTP cookie** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Biztonságos cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Állandó cookie-k**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
