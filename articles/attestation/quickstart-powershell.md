---
title: Azure-igazolás beállítása Azure PowerShell
description: Igazolási szolgáltató beállítása és konfigurálása Azure PowerShell használatával.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: cbc415411e05d6fdecee1acf2fbc02b3c170b9d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501124"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>Gyors útmutató: Azure-igazolás beállítása Azure PowerShell

Az alábbi lépések végrehajtásával hozzon létre és konfiguráljon egy igazolási szolgáltatót Azure PowerShell használatával. A Azure PowerShell telepítésével és futtatásával kapcsolatos információkért tekintse meg a [Azure PowerShell áttekintése](/powershell/azure/) című témakört.

Vegye figyelembe, hogy a PowerShell-galéria elavult Transport Layer Security (TLS) 1,0-es és 1,1-es verzióval rendelkezik. A TLS 1,2 vagy újabb verzió használata javasolt. Így a következő hibák jelenhetnek meg:

- Figyelmeztetés: nem sikerült feloldani a (z) "" csomag forrását https://www.powershellgallery.com/api/v2
- PackageManagement\Install-Package: nem található egyezés a megadott keresési feltételekhez és a modul nevéhez. 

A PowerShell-galéria folytatásához futtassa a következő parancsot a Install-Module parancsok előtt.

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>Az az. tanúsító PowerShell-modul telepítése

Az Azure PowerShell-mel rendelkező számítógépen telepítse az az. tanúsító PowerShell-modult, amely az Azure igazolási parancsmagokat tartalmazza.  

### <a name="initial-installation"></a>Kezdeti telepítés

Az összes meglévő PowerShell-ablak leállítása.

Az "aktuális felhasználó" telepítéséhez nyisson meg egy nem emelt szintű PowerShell-ablakot, és futtassa a következőt:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

A "minden felhasználó" telepítéséhez nyisson meg egy emelt szintű PowerShell-ablakot, és futtassa a következőt:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

A rendszergazda jogú PowerShell-konzol bezárásához.

### <a name="update-the-installation"></a>A telepítés frissítése

Az összes meglévő PowerShell-ablak leállítása.

A "jelenlegi felhasználó" frissítéséhez nyisson meg egy nem emelt szintű PowerShell-ablakot, és futtassa a következőt:

```powershell
Update-Module -Name Az.Attestation
```

A "minden felhasználó" frissítéséhez nyisson meg egy emelt szintű PowerShell-ablakot, és futtassa a következőt:

```powershell
Update-Module -Name Az.Attestation
```

A rendszergazda jogú PowerShell-konzol bezárásához.

### <a name="get-installed-modules"></a>Telepített modulok beolvasása

Az igazolási műveletek támogatásához szükséges az modulok minimális verziója:
- Az 4.5.0
- Az. accounts 1.9.2
- Az. igazolási 0.1.8

Futtassa az alábbi parancsot az összes modul telepített verziójának ellenőrzéséhez 

```powershell
Get-InstalledModule
```
Ha a verziók nem felelnek meg a minimális követelménynek, futtassa Update-Module parancsokat.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba a PowerShell-konzolon (emelt szintű hozzáférési jogosultságok nélkül).

```powershell
Connect-AzAccount
```

Ha szükséges, váltson az Azure-igazoláshoz használni kívánt előfizetésre.

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>A Microsoft. tanúsító erőforrás-szolgáltató regisztrálása

Regisztrálja a Microsoft. tanúsító erőforrás-szolgáltatót az előfizetésben. Az Azure erőforrás-szolgáltatókkal és az erőforrás-szolgáltatók konfigurálásával és kezelésével kapcsolatos további információkért lásd: [Azure Resource Providers és types](../azure-resource-manager/management/resource-providers-and-types.md). Vegye figyelembe, hogy az erőforrás-szolgáltató regisztrálását csak egyszer kell megadnia egy előfizetéshez.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Az Azure-igazolás regionális elérhetősége

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Azure-erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az igazolási szolgáltatóhoz. Vegye figyelembe, hogy más Azure-erőforrások (beleértve az ügyfélalkalmazás-példánnyal rendelkező virtuális gépeket is) ugyanabba az erőforráscsoporthoz helyezhetők.

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

## <a name="create-and-manage-an-attestation-provider"></a>Igazolási szolgáltató létrehozása és kezelése

New-AzAttestation létrehoz egy igazolási szolgáltatót.

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

A PolicySignerCertificateFile egy olyan fájl, amely megbízható aláíró kulcsok készletét adja meg. Ha meg van adva fájlnév a PolicySignerCertificateFile paraméterhez, az igazolási szolgáltató csak aláírt JWT formátumú szabályzatokkal konfigurálható. Az egyéb szabályzatok szövegben vagy aláíratlan JWT formátumban is konfigurálhatók.

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

A PolicySignersCertificateFile minta esetében lásd: [házirend-aláíró tanúsítvány példái](policy-signer-examples.md).

Get-AzAttestation lekéri az igazolási szolgáltató tulajdonságait, például az állapot-és AttestURI. Jegyezze fel a AttestURI, mert később szükség lesz rá.

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

A fenti parancsnak az alábbihoz hasonló kimenetet kell létrehoznia: 

```
Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
Location: MyLocation
ResourceGroupName: MyResourceGroup
Name: MyAttestationProvider
Status: Ready
TrustModel: AAD
AttestUri: https://MyAttestationProvider.us.attest.azure.net 
Tags: 
TagsTable: 
```

Az igazoló szolgáltatók a Remove-AzAttestation parancsmag használatával törölhetők.  

```powershell
Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## <a name="policy-management"></a>Szabályzatkezelés

A házirendek kezeléséhez az Azure AD-felhasználónak a következő engedélyekkel kell rendelkeznie a "Actions" esetében:
- Microsoft. igazolás/attestationProviders/igazolás/olvasás
- Microsoft. igazolás/attestationProviders/igazolás/írás
- Microsoft. igazolás/attestationProviders/igazolás/törlés

Ezek az engedélyek egy AD-felhasználóhoz társíthatók, például a "tulajdonos" (helyettesítő engedélyek), a "közreműködő" (helyettesítő engedélyek) vagy az "igazolás közreműködője" (csak az Azure-hitelesítésre vonatkozó egyedi engedélyek).  

A szabályzatok olvasásához egy Azure AD-felhasználónak a következő engedélyre van szüksége a "műveletek" művelethez:
- Microsoft. igazolás/attestationProviders/igazolás/olvasás

Ez az engedély hozzárendelhető egy AD-felhasználóhoz egy olyan szerepkörön keresztül, mint az "olvasó" (helyettesítő karakteres engedélyek) vagy az "igazolási olvasó" (csak az Azure-hitelesítésre vonatkozó egyedi engedélyek).

Az alábbi PowerShell-parancsmagok egy igazolási szolgáltató (egy póló egyszerre) házirend-kezelését biztosítják.

Get-AzAttestationPolicy a megadott TEE aktuális házirendjét adja vissza. A parancsmag a szabályzat szöveg-és JWT-formátumában jeleníti meg a szabályzatot.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

A támogatott TEE-típusok a következők: "SgxEnclave", "OpenEnclave" és "VbsEnclave".

Set-AttestationPolicy új szabályzatot állít be a megadott PÓLÓhoz. A parancsmag a szabályzatot szöveges vagy JWT formátumban fogadja el, és a PolicyFormat paraméter vezérli. A "text" a PolicyFormat alapértelmezett értéke. 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

Ha a PolicySignerCertificateFile egy igazolási szolgáltató létrehozásakor van megadva, a szabályzatok csak aláírt JWT formátumban konfigurálhatók. Az egyéb szabályzatok szövegben vagy aláíratlan JWT formátumban is konfigurálhatók.

A JWT formátumú igazolási szabályzatnak tartalmaznia kell egy "AttestationPolicy" nevű jogcímet. Az aláírt szabályzat esetében a JWT a meglévő házirend-aláíró tanúsítványoknak megfelelő titkos kulccsal kell aláírni.

A házirend-minták esetében tekintse meg az [igazolási szabályzat példáit](policy-examples.md).

Reset-AzAttestationPolicy alaphelyzetbe állítja a szabályzatot a megadott TEE alapértelmezett értékére.

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>Házirend-aláíró tanúsítványok kezelése

Az alábbi PowerShell-parancsmagok az igazolási szolgáltató házirend-aláíró tanúsítványainak kezelését biztosítják:

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

A házirend-aláíró tanúsítvány egy "Maa-policyCertificate" nevű aláírt JWT. A jogcím értéke egy olyan JWK, amely a hozzáadandó megbízható aláírási kulcsot tartalmazza. A JWT a meglévő házirend-aláíró tanúsítványoknak megfelelő titkos kulccsal kell aláírni.

Vegye figyelembe, hogy a házirend-aláíró tanúsítvány szemantikai manipulációjának a PowerShellen kívül kell történnie. A PowerShell-lel kapcsolatban ez egy egyszerű karakterlánc.

A házirend-aláíró tanúsítvány mintája: [példák a házirend-aláíró tanúsítványára](policy-signer-examples.md).

További információ a parancsmagokról és a hozzájuk tartozó paraméterekről: [Azure igazolási PowerShell-parancsmagok](/powershell/module/az.attestation/#attestation) 

## <a name="next-steps"></a>Következő lépések

- [Igazolási szabályzat létrehozása és aláírása](author-sign-policy.md)
- [SGX ENKLÁVÉHOZ enklávé igazolása kód-minták használatával](/samples/browse/?expanded=azure&terms=attestation)
