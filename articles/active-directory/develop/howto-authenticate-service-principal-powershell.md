---
title: Azure-alkalmazás identitásának létrehozása (PowerShell) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre az Azure PowerShell segítségével Azure Active Directory-alkalmazást és -szolgáltatásnevet, és miként adhat annak hozzáférést az erőforrásokhoz szerepköralapú hozzáférés-vezérléssel. Bemutatja az alkalmazás tanúsítvánnyal való hitelesítését.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev , devx-track-azurepowershell
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.date: 06/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.openlocfilehash: b07679c6dd5e03131fde1439213783893971a088
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581822"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Útmutató: egyszerű szolgáltatásnév létrehozása a Azure PowerShell használatával

Ha olyan alkalmazása van, amelynek erőforrásokhoz kell hozzáférnie, létrehozhat egy identitást az alkalmazásnak, és hitelesítheti az alkalmazást annak saját hitelesítő adataival. Ezt az identitást szolgáltatásnévnek nevezzük. Ez a megközelítés lehetővé teszi az alábbiakat:

* A saját engedélyeitől eltérő engedélyek hozzárendelése az alkalmazásidentitáshoz. Ezek az engedélyek jellemzően csak azt engedélyezik, amire az alkalmazásnak szüksége van.
* Tanúsítvány használata hitelesítéshez szkriptek felügyelet nélküli futtatásakor.

> [!IMPORTANT]
> Egyszerű szolgáltatásnév létrehozása helyett érdemes felügyelt identitásokat használni az Azure-erőforrásokhoz az alkalmazás identitásához. Ha a kód olyan szolgáltatáson fut, amely támogatja a felügyelt identitásokat, és a Azure Active Directory (Azure AD) hitelesítést támogató erőforrásokhoz fér hozzá, a felügyelt identitások jobb megoldást biztosítanak Önnek. Ha többet szeretne megtudni az Azure-erőforrások felügyelt identitásáról, beleértve a jelenleg támogatott szolgáltatásokat, tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai?](../managed-identities-azure-resources/overview.md)című témakört.

Ez a cikk bemutatja, hogy hogyan hozhat létre egy szolgáltatásnevet tanúsítvánnyal történő hitelesítéshez. A jelszót használó szolgáltatásnevek beállításáról a következő témakörben tájékozódhat: [Azure-beli szolgáltatásnév létrehozása az Azure PowerShell használatával](/powershell/azure/create-azure-service-principal-azureps).

A cikk lépéseinek követéséhez a PowerShell [legújabb verzióját](/powershell/azure/install-az-ps) kell használnia.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Szükséges engedélyek

A cikk elvégzéséhez megfelelő engedélyekkel kell rendelkeznie az Azure AD-ben és az Azure-előfizetésben. Pontosabban létre kell hoznia egy alkalmazást az Azure AD-ben, és hozzá kell rendelnie a szolgáltatásnevet egy szerepkörhöz.

A legegyszerűbben a portálon ellenőrizheti, hogy rendelkezik-e megfelelő jogosultságokkal. Lásd: [Szükséges engedélyek ellenőrzése](howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="assign-the-application-to-a-role"></a>Az alkalmazás társítása szerepkörhöz
Az előfizetés erőforrásainak eléréséhez hozzá kell rendelnie az alkalmazást egy szerepkörhöz. Döntse el, melyik szerepkör kínálja a megfelelő engedélyeket az alkalmazáshoz. Az elérhető szerepkörökről az [Azure beépített szerepköreivel](../../role-based-access-control/built-in-roles.md)foglalkozó témakörben olvashat bővebben.

Megadhatja a hatókört az előfizetés, az erőforráscsoport vagy az erőforrás szintjén. Az engedélyek a hatókör alacsonyabb szintjein vannak örökölve. Ha például hozzáad egy alkalmazást az erőforráscsoport *olvasó* szerepköréhez, az azt jelenti, hogy elolvashatja az erőforráscsoportot és a benne található összes erőforrást. Ha engedélyezni szeretné, hogy az alkalmazás olyan műveleteket hajtson végre, mint például az újraindítás, a példányok elindítása és leállítása, válassza ki a *közreműködő* szerepkört.

## <a name="create-service-principal-with-self-signed-certificate"></a>Szolgáltatásnév létrehozása önaláírt tanúsítvánnyal

Az alábbi példa egy egyszerű forgatókönyvet követ. A [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) használatával létrehoz egy önaláírt tanúsítvánnyal rendelkező szolgáltatásnevet, és a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) használatával rendeli hozzá az [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört az egyszerű szolgáltatáshoz. A szerepkör-hozzárendelés hatóköre az aktuális Azure-előfizetés. Másik előfizetés kiválasztásához használja a [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext).

> [!NOTE]
> A New-SelfSignedCertificate parancsmag és a PKI modul jelenleg nem támogatott a PowerShell Core-ban. 

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $sp.ApplicationId
```

A példa 20 másodpercig alvó állapotba lép, hogy az új szolgáltatásnév az Azure AD-ben való propagálása némi időt hagyjon. Ha a szkriptje nem vár eleget, akkor a következő hibát fogja látni: „A(z) {ID} résztvevő nem létezik a(z) {DIR-ID} címtárban”. A hiba megoldásához várjon egy percet, majd futtassa újra a **New-AzRoleAssignment** parancsot.

A szerepkör-hozzárendelés hatókörét beállíthatja egy adott erőforráscsoportra a **ResourceGroupName** paraméter használatával. Egy adott erőforrásra is beállíthatja a hatókört a **ResourceType** és a **ResourceName** paraméter együttes használatával. 

Ha **nem rendelkezik Windows 10 vagy Windows Server 2016 operációs** rendszerrel, töltse le az [önaláírt tanúsítvány-generátort](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) a Microsoft Script Center webhelyről. Bontsa ki a fájl tartalmát, és importálja a szükséges parancsmagot.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

A szkriptben helyettesítse be az alábbi két sort a tanúsítvány generálásához.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Tanúsítvány biztosítása automatizált PowerShell-szkripttel

Ha egyszerű szolgáltatásként jelentkezik be, adja meg az AD-alkalmazás címtárának bérlői AZONOSÍTÓját. A bérlő az Azure AD egy példánya.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

$Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Szolgáltatásnév létrehozása hitelesítésszolgáltatótól származó tanúsítvánnyal

Az alábbi példa egy hitelesítésszolgáltatótól származó tanúsítvánnyal hoz létre egy szolgáltatásnevet. A hozzárendelés hatóköre a megadott Azure-előfizetés. Hozzáadja az egyszerű szolgáltatást az [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkörhöz. Ha hiba történik a szerepkör-hozzárendelés során, a szkript megismétli a hozzárendelést.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }

 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Tanúsítvány biztosítása automatizált PowerShell-szkripttel
Ha egyszerű szolgáltatásként jelentkezik be, adja meg az AD-alkalmazás címtárának bérlői AZONOSÍTÓját. A bérlő az Azure AD egy példánya.

```powershell
Param (

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Az alkalmazásazonosító és a bérlőazonosító nem bizalmas adat, ezért beágyazhatja közvetlenül a szkriptjébe. A bérlőazonosító megállapításához használja a következőt:

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

Az alkalmazásazonosító megállapításához használja a következőt:

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Hitelesítő adatok módosítása

Ha módosítani szeretné egy AD-alkalmazás hitelesítő adatait, vagy a biztonsági sérülés vagy a hitelesítő adatok lejárata miatt, használja a [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) és a [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential) parancsmagokat.

Ha egy alkalmazás összes hitelesítő adatát szeretné eltávolítani, használja a következőt:

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

Ha tanúsítványértéket kíván hozzáadni, hozzon létre egy önaláírt tanúsítványt a cikkben ismertetett módon. Ezután használja a következőt:

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Hibakeresés

A szolgáltatásnév létrehozásakor az alábbi hibákba ütközhet:

* **„Authentication_Unauthorized”** vagy **„No subscription found in the context.”** (Nem található előfizetés a környezetben.) – Ez a hiba akkor jelenik meg, ha a fiókja nem rendelkezik a [szükséges engedélyekkel](#required-permissions) az Azure ad-ben az alkalmazás regisztrálásához. Ez a hiba általában akkor jelenik meg, ha csak a Azure Active Directory rendszergazdai felhasználói regisztrálhatnak alkalmazásokat, és a fiókja nem rendszergazda. Kérje meg a rendszergazdát, hogy rendeljen hozzá egy rendszergazdai szerepkört, vagy engedélyezze a felhasználóknak az alkalmazások regisztrálását.

* A (z) "nincs engedélye a (z **)" Microsoft. Authorization/roleAssignments/Write "művelet végrehajtására a (z)"/Subscriptions/{GUID} "hatókörben."** – ezt a hibaüzenetet akkor látja, ha a fiók nem rendelkezik megfelelő engedélyekkel ahhoz, hogy egy szerepkört társítson az identitáshoz. Kérje meg az előfizetés-rendszergazdáját, hogy adja hozzá Önt a Felhasználói hozzáférés rendszergazdája szerepkörhöz.

## <a name="next-steps"></a>Következő lépések

* A jelszót használó szolgáltatásnevek beállításáról a következő témakörben tájékozódhat: [Azure-beli szolgáltatásnév létrehozása az Azure PowerShell használatával](/powershell/azure/create-azure-service-principal-azureps).
* Az alkalmazásokról és a szolgáltatásnevekről bővebben az [Alkalmazásobjektumok és egyszerű szolgáltatási objektumok](app-objects-and-service-principals.md) című cikkben olvashat.
* Az Azure AD-hitelesítéssel kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-hez](./authentication-vs-authorization.md).
* További információ az alkalmazás-regisztrációk **Microsoft Graph** használatával történő használatáról: [alkalmazások](/graph/api/resources/application) API-referenciája.
