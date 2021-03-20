---
title: Egyszerű szolgáltatások biztonságossá tétele a Azure Active Directoryban
description: Egyszerű szolgáltatások keresése, értékelése és védelme.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bab8e8c6dfb944e496c636d53217e63175be9fbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587848"
---
# <a name="securing-service-principals"></a>Az egyszerű szolgáltatások biztonságossá tétele

Egy Azure Active Directory (Azure AD) [egyszerű szolgáltatásnév](../develop/app-objects-and-service-principals.md) egy alkalmazás-objektum helyi ábrázolása egyetlen bérlőben vagy címtárban.  A funkció az alkalmazás példányának identitása. Az egyszerű szolgáltatások határozzák meg, hogy ki férhet hozzá az alkalmazáshoz, és milyen erőforrásokhoz férhet hozzá. A rendszer minden olyan bérlőn létrehoz egy szolgáltatásnevet, amelyben az alkalmazás használatban van, és hivatkozik a globálisan egyedi alkalmazásobjektum-objektumra. A bérlő gondoskodik az egyszerű szolgáltatás bejelentkezéséről és az erőforrásokhoz való hozzáférésről.  

### <a name="tenant-service-principal-relationships"></a>Bérlői szolgáltatások – egyszerű kapcsolatok
Egy egybérlős alkalmazás csak egy egyszerű szolgáltatásnevet tartalmaz a saját bérlőben. A több-bérlős webalkalmazások vagy API-k minden bérlőhöz igényelnek szolgáltatásnevet. Egy egyszerű szolgáltatásnév akkor jön létre, amikor az adott bérlő felhasználója beleegyezett az alkalmazás vagy az API használatára. Ez a beleegyezés egy-a-többhöz kapcsolatot hoz létre a több-bérlős alkalmazás és a hozzá tartozó egyszerű szolgáltatások között.

A több-bérlős alkalmazások egyetlen bérlőn belül vannak, és úgy tervezték, hogy más bérlők példányain is legyenek. A legtöbb szoftveres (SaaS) alkalmazás több-bérlős használatra készült. Az egyszerű szolgáltatásokkal biztosíthatja, hogy az alkalmazás és a felhasználók számára egyaránt megfelelő biztonsági helyzet legyen a bérlői és a több-bérlős használati esetekben.

## <a name="applicationid-and-objectid"></a>ApplicationID és ObjectID

Egy adott alkalmazás példányának két különböző tulajdonsága van: a ApplicationID (más néven ClientID) és a ObjectID.

> [!NOTE] 
> Előfordulhat, hogy az alkalmazás és a szolgáltatásnév feltételeit szinonimaként használják, amikor lazán hivatkozik egy alkalmazásra a hitelesítéssel kapcsolatos feladatokkal összefüggésben. Az Azure AD-ben azonban két különböző ábrázolási alkalmazás található.
 

A ApplicationID a globális alkalmazást jelöli, és megegyezik a bérlők összes alkalmazási példánya esetében. A ObjectID egy alkalmazás-objektum egyedi értéke, amely az egyszerű szolgáltatásnevet jelöli. A felhasználókhoz, csoportokhoz és egyéb erőforrásokhoz hasonlóan a ObjectID is segít egyedileg azonosítani az Azure AD-beli alkalmazás-példányokat.

További információ erről a témakörről: az [alkalmazás-és szolgáltatásnév kapcsolata](../develop/app-objects-and-service-principals.md).

A Azure PowerShell, az Azure CLI, a Microsoft Graph, a Azure Portal és más eszközök használatával is létrehozhat egy alkalmazást és a szolgáltatás egyszerű objektumát (ObjectID). 

![Képernyőfelvétel: új alkalmazás regisztrálása, az alkalmazás azonosítója és az objektumazonosító mezők kiemelve.](./media/securing-service-accounts/secure-principal-image-1.png)

## <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Az egyszerű szolgáltatásoknak két mechanizmusa van a hitelesítéshez – Ügyféltanúsítványok és ügyfél-titkos kulcsok használatával. 

![ Képernyőfelvétel az új alkalmazás lapról, amely a tanúsítványok és az ügyfél titkos területeinek kiemelését mutatja.](./media/securing-service-accounts/secure-principal-certificates.png)

A tanúsítványok biztonságosabbak: ha lehetséges, használjon ügyféltanúsítványt. Az ügyfél-titkoktól eltérően az Ügyféltanúsítványok nem ágyazhatók be véletlenül programkódba. A tanúsítvány-és titkok kezeléséhez Azure Key Vault használata, ha lehetséges, hogy a hardveres biztonsági modulok által védett kulcsokkal Titkosítsa a következő eszközöket:

* hitelesítési kulcsok

* Storage-fiók kulcsainak

* adattitkosítási kulcsok

* . pfx fájlok

* jelszavak 

További információ a Azure Key Vaultről és használatáról a tanúsítványok és a titkos kódok kezeléséhez: [Tudnivalók a Azure Key Vaultról](../../key-vault/general/overview.md) és a [Key Vault hozzáférési szabályzat hozzárendeléséről a Azure Portal használatával](../../key-vault/general/assign-access-policy-portal.md). 

 ### <a name="challenges-and-mitigations"></a>Kihívások és enyhítések
Az alábbi táblázat az egyszerű szolgáltatások használata során felmerülő kihívásokat mutatja be.


| Problémák| Kezelési lehetőségek |
| - | - |
| A Kiemelt szerepkörökhöz rendelt egyszerű szolgáltatásnév hozzáférési felülvizsgálatai.| Ez a funkció előzetes verzióban érhető el, és még nem széles körben elérhető. |
| Az egyszerű szolgáltatásnév hozzáférésének felülvizsgálata| Az erőforrás hozzáférés-vezérlési listájának manuális ellenőrzése a Azure Portal használatával. |
| Engedéllyel rendelkező egyszerű szolgáltatások| Automation-szolgáltatásfiókok vagy-egyszerű szolgáltatások létrehozásakor csak a feladathoz szükséges engedélyeket adja meg. Értékelje ki a meglévő egyszerű szolgáltatásokat, és ellenőrizze, hogy lehet-e csökkenteni a jogosultságokat. |
|Az egyszerű szolgáltatásnév hitelesítő adatainak vagy hitelesítési módszereinek módosítása |Használja a bizalmas műveletek jelentés munkafüzetét, amely segíthet a probléma enyhítésében. [Tekintse meg a jelen blogbejegyzésben található magyarázatot](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718).|

## <a name="find-accounts-using-service-principals"></a>Fiókok keresése egyszerű szolgáltatásnév használatával
A következő parancsok futtatásával megkeresheti az egyszerű szolgáltatásokat használó fiókokat.

Az Azure parancssori felület használata


`az ad sp list`

A PowerShell használata

`Get-AzureADServicePrincipal -All:$true` 


További információ: [Get-azureadserviceprincipal parancsmagot](/powershell/module/azuread/get-azureadserviceprincipal)

## <a name="assess-service-principal-security"></a>Az egyszerű szolgáltatás biztonságának felmérése

Az egyszerű szolgáltatások biztonságának felméréséhez győződjön meg arról, hogy kiértékeli a jogosultságokat és a hitelesítő adatokat.

A következő információk segítségével csökkentheti a lehetséges kihívásokat.
|Problémák | Kezelési lehetőségek|
| - | - |
| A több-bérlős alkalmazásnak eljuttatott felhasználó észlelése és a tiltott beleegyezési támogatások észlelése egy több-bérlős alkalmazás számára | Futtassa a következő PowerShell-t a több-bérlős alkalmazások kereséséhez.<br>`Get-AzureADServicePrincipal -All:$true ? {$_.Tags -eq WindowsAzureActiveDirectoryIntegratedApp"}`<br>Felhasználói engedély letiltása. <br>Hitelesített közzétevők felhasználói jóváhagyásának engedélyezése a kijelölt engedélyekhez (ajánlott) <br> A feltételes hozzáférés használatával blokkolhatja az egyszerű szolgáltatásokat a nem megbízható helyekről. Konfigurálja azokat a felhasználói környezetben, és a jogkivonatokat az egyszerű szolgáltatásnév elindításához kell használni.|
|Egy parancsfájlban rögzített közös titkos kulcs használata egy egyszerű szolgáltatásnév használatával.|Tanúsítvány vagy Azure Key Vault használata.|
|A tanúsítványt vagy a titkos kulcsot használó követés| Figyelje meg az egyszerű szolgáltatás bejelentkezési naplóit az Azure AD bejelentkezési naplói segítségével.|
Az egyszerű szolgáltatásokhoz való bejelentkezést nem lehet feltételes hozzáféréssel kezelni.| A bejelentkezések figyelése az Azure AD bejelentkezési naplói használatával
| Az alapértelmezett Azure RBAC szerepkör a közreműködő. |Értékelje ki az igényeket, és alkalmazza a szerepkört a lehető legkevesebb jogosultsággal az igény kielégítéséhez.|

## <a name="move-from-a-user-account-to-a-service-principal"></a>Áthelyezés felhasználói fiókból egy egyszerű szolgáltatásba  
Ha Azure-beli felhasználói fiókot használ szolgáltatásnévként, akkor kiértékelheti, hogy áthelyezhető-e egy [felügyelt identitásra](../../app-service/overview-managed-identity.md?tabs=dotnet) vagy egy egyszerű szolgáltatásra. Ha nem tud felügyelt identitást használni, hozzon létre egy egyszerű szolgáltatásnevet, amely a szükséges feladatok futtatásához elég engedélyekkel és hatókörrel rendelkezik. Egyszerű szolgáltatást [egy alkalmazás regisztrálásával](../develop/howto-create-service-principal-portal.md)vagy a [PowerShell](../develop/howto-authenticate-service-principal-powershell.md)-lel hozhat létre.

Microsoft Graph használatakor tekintse meg az adott API dokumentációját, például [ebben a példában](/powershell/azure/create-azure-service-principal-azureps), és győződjön meg arról, hogy az alkalmazáshoz tartozó engedély típusa támogatott.

## <a name="next-steps"></a>Következő lépések

**További információ az egyszerű szolgáltatásokról:**

[Egyszerű szolgáltatás létrehozása](../develop/howto-create-service-principal-portal.md)

 [Egyszerű szolgáltatás-bejelentkezések figyelése](../reports-monitoring/concept-sign-ins.md#sign-ins-report)

**További információ a szolgáltatásfiókok biztonságossá tételéről:**

[Az Azure Service-fiókok bemutatása](service-accounts-introduction-azure.md)

[Felügyelt identitások biztonságossá tétele](service-accounts-managed-identities.md)

[Az Azure-szolgáltatásfiókok szabályozása](service-accounts-governing-azure.md)

[A helyszíni szolgáltatásfiókok bemutatása](service-accounts-on-premises.md)