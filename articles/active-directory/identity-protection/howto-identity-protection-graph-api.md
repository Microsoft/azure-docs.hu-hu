---
title: Microsoft Graph PowerShell SDK és Azure Active Directory Identity Protection
description: Ismerje meg, hogyan lehet lekérdezni Microsoft Graph kockázati észleléseket és a kapcsolódó információkat Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2db8cfe652c0fca4b68b00d846e345c1b60cd05d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880236"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Active Directory Identity Protection és az Microsoft Graph PowerShell SDK

Microsoft Graph a Microsoft Unified API-végpontja és a [Azure Active Directory Identity Protection](./overview-identity-protection.md) API-k otthona. Ez a cikk bemutatja, hogyan használhatja a [Microsoft Graph POWERSHELL SDK](/graph/powershell/get-started) -t a kockázatos felhasználói részletek a PowerShell használatával történő beszerzéséhez. Azok a szervezetek, amelyek közvetlenül a Microsoft Graph API-kat szeretnék lekérdezni, a cikkből megtudhatják, hogyan [azonosíthatja és javíthatja a kockázatokat Microsoft Graph API](/graph/tutorial-riskdetection-api) -k használatával, hogy megkezdődjön


## <a name="connect-to-microsoft-graph"></a>Kapcsolódás Microsoft Graphhoz

Az Identity Protection-adatok elérésének négy lépése van Microsoft Graph:

- [Tanúsítvány létrehozása](#create-a-certificate)
- [Új alkalmazás regisztrációjának létrehozása](#create-a-new-app-registration)
- [API-engedélyek konfigurálása](#configure-api-permissions)
- [Érvényes hitelesítő adat konfigurálása](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>Tanúsítvány létrehozása

Éles környezetben az éles hitelesítésszolgáltató tanúsítványát kellene használnia, de ebben a példában egy önaláírt tanúsítványt fogunk használni. Hozza létre és exportálja a tanúsítványt a következő PowerShell-parancsok használatával.

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>Új alkalmazás regisztrációjának létrehozása

1. A Azure Portal tallózással keresse meg **Azure Active Directory**  >  **Alkalmazásregisztrációk**.
1. Válassza az **új regisztráció** lehetőséget.
1. A **Létrehozás** oldalon hajtsa végre a következő lépéseket:
   1. A **név** szövegmezőbe írja be az alkalmazás nevét (például: Azure ad kockázati észlelési API).
   1. A **támogatott fiókok típusai** területen válassza ki az API-kat használó fiókok típusát.
   1. Válassza a **Regisztráció** lehetőséget.
1. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** és a **könyvtár (bérlő) azonosítóját** , mert később szüksége lesz ezekre az elemekre.

### <a name="configure-api-permissions"></a>API-engedélyek konfigurálása

Ebben a példában olyan alkalmazás-engedélyeket konfigurálunk, amelyek lehetővé teszik, hogy ezt a mintát felügyelet nélkül lehessen használni. Ha engedélyeket ad a bejelentkezett felhasználónak, válassza a delegált engedélyek lehetőséget. A különböző engedélyekkel kapcsolatos további információkért tekintse meg a cikk, [engedélyek és hozzájárulás a Microsoft Identity platformon](../develop/v2-permissions-and-consent.md#permission-types)című cikket.

1. A létrehozott **alkalmazásból** válassza az **API-engedélyek** lehetőséget.
1. A **konfigurált engedélyek** lapon, a felső eszköztáron kattintson az **engedély hozzáadása** lehetőségre.
1. Az **API-hozzáférés hozzáadása** lapon kattintson **az API kiválasztása** lehetőségre.
1. Az **API kiválasztása** lapon válassza a **Microsoft Graph** lehetőséget, majd kattintson a **kiválasztás** gombra.
1. Az **API-engedélyek kérése** oldalon: 
   1. Válassza ki az **alkalmazás engedélyeit**.
   1. Jelölje be a és a melletti jelölőnégyzeteket `IdentityRiskEvent.Read.All` `IdentityRiskyUser.Read.All` .
   1. Válassza az **engedélyek hozzáadása** lehetőséget.
1. Válassza **a rendszergazdai jóváhagyás megadása a tartományhoz** lehetőséget. 

### <a name="configure-a-valid-credential"></a>Érvényes hitelesítő adat konfigurálása

1. A létrehozott **alkalmazásból** válassza a **tanúsítványok & Secrets** elemet.
1. A **tanúsítványok** területen válassza a **tanúsítvány feltöltése** lehetőséget.
   1. Válassza ki a korábban exportált tanúsítványt a megnyíló ablakból.
   1. Válassza a **Hozzáadás** lehetőséget.
1. Jegyezze fel a tanúsítvány **ujjlenyomatát** , mert erre az információra szüksége lesz a következő lépésben.

## <a name="list-risky-users-using-powershell"></a>Kockázatos felhasználók listázása a PowerShell használatával

A Microsoft Graph lekérdezésének engedélyezéséhez a parancsot használva telepítenie kell a `Microsoft.Graph` modult a PowerShell-ablakban `Install-Module Microsoft.Graph` .

Módosítsa a következő változókat az előző lépésekben létrehozott információk belefoglalásához, majd futtassa őket egészként a kockázatos felhasználói részletek a PowerShell használatával történő beszerzéséhez.

```powershell
$ClientID       = "<your client ID here>"        # Application (client) ID gathered when creating the app registration
$tenantdomain   = "<your tenant domain here>"    # Directory (tenant) ID gathered when creating the app registration
$Thumbprint     = "<your client secret here>"    # Certificate thumbprint gathered when configuring your credential

Select-MgProfile -Name "beta"
  
Connect-MgGraph -ClientId $ClientID -TenantId $tenantdomain -CertificateThumbprint $Thumbprint

Get-MgRiskyUser -All
```

## <a name="next-steps"></a>Következő lépések

- [Ismerkedés a Microsoft Graph PowerShell SDK-val](/graph/powershell/get-started)
- [Oktatóanyag: kockázatok azonosítása és szervizelése Microsoft Graph API-k használatával](/graph/tutorial-riskdetection-api)
- [A Microsoft Graph áttekintése](https://developer.microsoft.com/graph/docs)
- [Hozzáférés kérése felhasználó nélkül](/graph/auth-v2-service)
- [Azure AD Identity Protection szolgáltatás gyökerének](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
