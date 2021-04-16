---
title: A Azure Attestation beállítása Azure PowerShell
description: Igazolásszolgáltató beállítása és konfigurálása a Azure PowerShell.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 80f0efe0894c54d1499bbfdce2317a38dff9299d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481616"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>Rövid útmutató: A Azure Attestation beállítása Azure PowerShell

Az alábbi lépésekkel hozhat létre és konfigurálhatja az igazolásszolgáltatót a Azure PowerShell. A [Azure PowerShell](/powershell/azure/) telepítésének és futtatásának mikéntjéhez lásd a Azure PowerShell.

Vegye figyelembe, hogy a PowerShell-galéria elavult Transport Layer Security (TLS) 1.0-s és 1.1-es verziói. A TLS 1.2-es vagy újabb verziója ajánlott. Ezért a következő hibákat kaphatja:

- FIGYELMEZTETÉS: Nem sikerült feloldani a következő csomagforrást: https://www.powershellgallery.com/api/v2 '
- PackageManagement\Install-Package: Nem található egyezés a megadott keresési feltételekhez és a modul nevéhez 

Ha továbbra is használni PowerShell-galéria, futtassa a következő parancsot a Install-Module parancsai előtt

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>Az Az.Attestation PowerShell-modul telepítése

A virtuális gépre Azure PowerShell az Az.Attestation PowerShell-modult, amely parancsmagokat tartalmaz a Azure Attestation.  

### <a name="initial-installation"></a>Kezdeti telepítés

Bontsa le az összes meglévő PowerShell-ablakot.

Az "aktuális felhasználó" számára való telepítéshez indítson el egy nem emelt szintű PowerShell-ablakot, és futtassa a következőt:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

A "minden felhasználó" számára való telepítéshez indítson el egy emelt szintű PowerShell-ablakot, és futtassa a következőt:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

Zárja be az emelt szintű PowerShell-konzolt.

### <a name="update-the-installation"></a>A telepítés frissítése

Bontsa le az összes meglévő PowerShell-ablakot.

Az "aktuális felhasználó" frissítéséhez indítson el egy nem emelt szintű PowerShell-ablakot, és futtassa a következőt:

```powershell
Update-Module -Name Az.Attestation
```

A "minden felhasználó" frissítéséhez indítson el egy emelt szintű PowerShell-ablakot, és futtassa a következőt:

```powershell
Update-Module -Name Az.Attestation
```

Zárja be az emelt szintű PowerShell-konzolt.

### <a name="get-installed-modules"></a>Telepített modulok beolvasása

Az Az modulok minimális verziója, amely az igazolási műveletek támogatásához szükséges:
- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

Futtassa az alábbi parancsot az összes Az modul telepített verziójának ellenőrzéséhez 

```powershell
Get-InstalledModule
```
Ha a verziók nem egyeznek meg a minimális követelménnyel, futtassa a Update-Module parancsokat.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba a PowerShell-konzolon (emelt szintű hozzáférési jogosultságok nélkül).

```powershell
Connect-AzAccount
```

Ha szükséges, váltson arra az előfizetésre, amely a Azure Attestation.

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>A Microsoft.Attestation erőforrás-szolgáltató regisztrálása

Regisztrálja a Microsoft.Attestation erőforrás-szolgáltatót az előfizetésben. További információ az Azure-erőforrásszolgáltatókról, valamint az erőforrás-szolgáltatók konfigurálásról és kezelésről: [Azure erőforrás-szolgáltatók és -típusok.](../azure-resource-manager/management/resource-providers-and-types.md) Vegye figyelembe, hogy egy előfizetéshez csak egyszer kell regisztrálni egy erőforrás-szolgáltatót.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>A Azure Attestation

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Azure-erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az igazolási szolgáltatóhoz. Vegye figyelembe, hogy más Azure-erőforrások (beleértve az ügyfélalkalmazás-példányt is) ugyanoda az erőforráscsoportba is helyezzenek.

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

## <a name="create-and-manage-an-attestation-provider"></a>Igazolásszolgáltató létrehozása és kezelése

New-AzAttestation létrehoz egy igazolásszolgáltatót.

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

A PolicySignerCertificateFile egy olyan fájl, amely megbízható aláírókulcsokat határoz meg. Ha a PolicySignerCertificateFile paraméterhez fájlnév van megadva, az igazolási szolgáltató csak aláírt JWT formátumú házirendekkel konfigurálható. Egyéb szabályzatok szövegben vagy aláíratlan JWT formátumban konfigurálhatóak.

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

A PolicySignersCertificateFile minta esetében tekintse meg a [szabályzat-aláíró tanúsítványra vonatkozó példákat.](policy-signer-examples.md)

Get-AzAttestation lekéri az igazolásszolgáltató tulajdonságait, például az állapotot és az AttestURI-t. Jegyezze fel az AttestURI-t, mert később szüksége lesz rá.

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

A fenti parancsnak az alábbihoz hasonló kimenetet kell előállítania: 

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

Az igazolási szolgáltatók a következő parancsmag Remove-AzAttestation törölhetők.  

```powershell
Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## <a name="policy-management"></a>Szabályzatkezelés

A szabályzatok kezeléséhez egy Azure AD-felhasználónak a következő engedélyekre van szüksége a "Műveletekhez":
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

Ezeknek a műveleteknek a végrehajtásához az Azure AD-felhasználónak "Igazolási közreműködő" szerepkörben kell lennie az igazolásszolgáltatón. Ezek az engedélyek olyan szerepkörökkel is öröklhetőek, mint a "Tulajdonos" (helyettesítő karakterek engedélyei), a "Közreműködő" (helyettesítő karakterek engedélyei) az előfizetés/erőforráscsoport szintjén.  

A szabályzatok olvasásához egy Azure AD-felhasználónak a következő engedélyre van szüksége a "Műveletekhez":
- Microsoft.Attestation/attestationProviders/attestation/read

A művelet végrehajtásához az Azure AD-felhasználónak "Igazolásolvasó" szerepkörre van szükség az igazolásszolgáltatón. Az olvasási engedély olyan szerepkörökkel is örökölhető, mint például az "Olvasó" (helyettesítő karakteres engedélyek) az előfizetés/erőforráscsoport szintjén.  

Az alábbi PowerShell-parancsmagok biztosítják az igazolásszolgáltató (egyszerre egy TEE) szabályzatkezelését.

Get-AzAttestationPolicy a megadott TEE aktuális szabályzatát adja vissza. A parancsmag a szabályzat szövegében és JWT formátumában is megjeleníti a szabályzatot.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

A támogatott TEE-típusok: "SgxEnclave", "OpenEnclave" és "VbsEnclave".

Set-AttestationPolicy új szabályzatot állít be a megadott TEE-hez. A parancsmag szöveges vagy JWT formátumú házirendet fogad el, és a PolicyFormat paraméterrel vezérelhető. A PolicyFormat alapértelmezett értéke a "Text". 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

Ha a PolicySignerCertificateFile meg van állítva az igazolási szolgáltató létrehozása során, a szabályzatok csak aláírt JWT formátumban konfigurálhatóak. Az egyéb szabályzatok szövegben vagy aláíratlan JWT formátumban konfigurálhatóak.

A JWT formátumú igazolási szabályzatnak tartalmaznia kell egy "AttestationPolicy" nevű jogcímet. Az aláírt szabályzatok esetében a JWT-t a meglévő szabályzat-aláíró tanúsítványok bármelyikének megfelelő titkos kulccsal kell aláírni.

Szabályzatmintákat az igazolási szabályzat [példáiban talál.](policy-examples.md)

Reset-AzAttestationPolicy visszaállítja a szabályzatot az alapértelmezettre a megadott TEE-hez.

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>Házirend-aláíró tanúsítványok kezelése

Az alábbi PowerShell-parancsmagok az igazolási szolgáltató házirend-aláíró tanúsítványait biztosítják:

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

A szabályzat-aláíró tanúsítvány egy aláírt JWT, "maa-policyCertificate" nevű jogcímmal. A jogcím értéke egy JWK, amely tartalmazza a hozzáadható megbízható aláírókulcsot. A JWT-t a meglévő házirend-aláíró tanúsítványoknak megfelelő titkos kulccsal kell aláírni.

Vegye figyelembe, hogy a szabályzat-aláíró tanúsítványának minden szemantikai manipulációját a PowerShellen kívül kell tenni. Ami a PowerShellt illeti, ez egy egyszerű sztring.

A szabályzat-aláíró tanúsítványmintát a szabályzat-aláíró tanúsítvány [példáiban talál.](policy-signer-examples.md)

A parancsmagokkal és paramétereivel kapcsolatos további információkért lásd: Azure Attestation [PowerShell-parancsmagok](/powershell/module/az.attestation/#attestation) 

## <a name="next-steps"></a>Következő lépések

- [Igazolási szabályzat írása és aláírása](author-sign-policy.md)
- [SGX-enklávé tanúsítsa kódminták használatával](/samples/browse/?expanded=azure&terms=attestation)
