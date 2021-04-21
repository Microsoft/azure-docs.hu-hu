---
title: Azure Attestation – Hibaelhárítási útmutató
description: A gyakorian megfigyelt problémákhoz vezető hibabeeső útmutató
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9d3e34bee3d0f1420b379638389e6fad0a2fed60
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831563"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Microsoft Azure igazolás hibaelhárítási útmutatója

A hibakezelés a Azure Attestation microsoftos irányelvek [REST API van megvalósítva.](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses) Az API-k által Azure Attestation válasz HTTP-állapotkódot és név/érték párokat tartalmaz a "code" és "message" névvel. A "kód" értéke olvasható, és a hiba típusát jelzi. Az "üzenet" érték segít a felhasználónak, és tartalmazza a hiba részleteit.

Ha a probléma nem található ebben a cikkben, elküldhet egy kérést Azure-támogatás a következő Azure-támogatás [oldalon:](https://azure.microsoft.com/support/options/).

Az alábbiakban néhány példát talál a Azure Attestation:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP–401: Jogosulatlan kivétel

### <a name="http-status-code"></a>HTTP-állapotkód
401

**Hibakód** Jogosulatlan

**Példaforgatókönyvek**
  - Nem lehet kezelni az igazolási szabályzatokat, mert a felhasználó nincs hozzárendelve a megfelelő szerepkörökkel
  - Nem lehet kezelni az igazolási szabályzat aláíróit, mert a felhasználó nincs hozzárendelve a megfelelő szerepkörökkel

Olvasó szerepkörrel felfelhasználó, aki egy igazolási szabályzatot próbál szerkeszteni a PowerShellben 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Hibaelhárítási lépések**

A szabályzatok kezeléséhez egy Azure AD-felhasználónak a következő engedélyekre van szüksége a "Műveletekhez":
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  Ezeknek a műveleteknek a végrehajtásához az Azure AD-felhasználónak "Igazolási közreműködő" szerepkörben kell lennie az igazolási szolgáltatón. Ezek az engedélyek olyan szerepkörökkel is öröklhetőek, mint a "Tulajdonos" (helyettesítő karakterek engedélyei), a "Közreműködő" (helyettesítő karakterek engedélyei) az előfizetésen/erőforráscsoporton.  

A szabályzatok olvasásához egy Azure AD-felhasználónak a következő engedélyre van szüksége a "Műveletek" művelethez:
- Microsoft.Attestation/attestationProviders/attestation/read

  A művelet végrehajtásához az Azure AD-felhasználónak "Igazolásolvasó" szerepkörre van szükség az igazolásszolgáltatón. Az olvasási engedély olyan szerepkörökkel is örökölhető, mint például az "Olvasó" (helyettesítő karakteres engedélyek) az előfizetésen/erőforráscsoporton.  

A szerepkörök PowerShellben való ellenőrzéséhez futtassa az alábbi lépéseket:

a. Indítsa el a PowerShellt, és jelentkezzen be az Azure-ba a Connect-AzAccount parancsmaggal

b. Az [azure-beli](../role-based-access-control/role-assignments-list-powershell.md) szerepkör-hozzárendelés az igazolási szolgáltatón való ellenőrzéséhez tekintse meg az itt

c. Ha nem talál megfelelő szerepkör-hozzárendelést, kövesse az itt található [utasításokat](../role-based-access-control/role-assignments-powershell.md)

## <a name="2-http--400-errors"></a>2. HTTP – 400-as hibák

### <a name="http-status-code"></a>HTTP-állapotkód
400

A kérések több okból is visszaadhatnak 400-as igénylést. Az alábbiakban néhány példát talál az API-k által Azure Attestation hibákra:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. Igazolási hiba a szabályzatértékelési hibák miatt

Az igazolási szabályzat engedélyezési szabályokat és kiállítási szabályokat tartalmaz. Az enklávé-bizonyítékok kiértékelése az engedélyezési szabályok alapján történik. A kiállítási szabályok határozzák meg az igazolási jogkivonatban szerepeltetni kell a jogcímeket. Ha az enklávéban talált jogcímek nem felelnek meg az engedélyezési szabályoknak, az attest hívások szabályzatértékelési hibát fognak visszaadni. 

**Hibakód** PolicyEvaluationError

**Példaforgatókönyvek** Ha az enklávéban a jogcímek nem egyeznek az igazolási szabályzat engedélyezési szabályaival

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Hibaelhárítási lépések** A felhasználók az SGX-igazolási szabályzatok alapján értékelhetik az enklávé-bizonyítékokat, mielőtt ugyanezt konfigurálják.

Küldjön kérést az attest API-nak úgy, hogy a szabályzat szövegét a "draftPolicyForAttestation" paraméterben megadva. Az AttestSgxEnclave API ezt a szabályzatdokumentumot fogja használni az igazolási hívás során, és ezzel tesztelheti az igazolási szabályzatokat azok használata előtt. A mezőben létrehozott igazolási jogkivonat nem biztonságos.

Példák [az igazolási szabályzatra](./policy-examples.md)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Igazolási hiba érvénytelen bemenet miatt

**Hibakód** InvalidParameter (Érvénytelen paraméter)

**Példaforgatókönyvek** Az SGX-igazolás érvénytelen bemenet miatt nem sikerült. Íme néhány példa a hibaüzenetek használatára:
- A megadott ajánlat érvénytelen volt egy, az árajánlatot ékeső hiba miatt 
- A megadott ajánlat érvénytelen volt, mert az az eszköz, amelyen az ajánlat létre lett hozva, nem felel meg az Azure alapkonfiguráció követelményeinek
- A megadott idézőjel érvénytelen volt, mert a PCK Cache Szolgáltatás által biztosított TCBInfo vagy QEID érvénytelen volt

**Hibaelhárítási lépések**

Microsoft Azure-igazolás támogatja az Intel SDK és az Open Enclave SDK által generált SGX-ajánlatok igazolását.

Tekintse meg [az](/samples/browse/?expanded=azure&terms=attestation) Open Enclave SDK/Intel SDK használatával való igazolás végrehajtásához szükséges kódmintákat

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. Érvénytelen tanúsítványlánchiba a szabályzat/szabályzat-aláíró feltöltésekor

**Hibakód** InvalidParameter (Érvénytelen paraméter)

**Példaforgatókönyvek** Konfigurálja az aláírt házirendet, vagy adja hozzá/törölje a házirend-aláírót, amely érvénytelen tanúsítványlánccal van aláírva (például ha a főtanúsítvány Alapszintű korlátozások bővítménye nem a Tulajdonos típusa = CA beállításra van beállítva)

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Hibaelhárítási lépések** A főtanúsítványt hitelesítésszolgáltató által kiállítottként kell megjelölve (az X.509 alapszintű megkötései), különben nem tekinthető érvényes tanúsítványnak. 

Győződjön meg arról, hogy a főtanúsítvány Alapszintű korlátozások bővítménye úgy van beállítva, hogy a Tulajdonos típusa = CA legyen

Ha nem, akkor a tanúsítványlánc érvénytelennek minősül.

Lásd [a szabályzat-aláírókra és](./policy-signer-examples.md) [szabályzatpépéékra vonatkozó példákat](./policy-examples.md) 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. Szabályzat-aláíró hozzáadása/törlése – hiba

**Hibakód** InvalidOperation (Érvénytelen működés)

**Példaforgatókönyvek**

Amikor a felhasználó "maa-policyCertificate" jogcím nélkül tölt fel JWS-t

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

Ha a felhasználó nem tölt fel JWS formátumú tanúsítványt

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**Hibaelhárítási lépések** Új szabályzat-aláíró tanúsítvány hozzáadásához/törléséhez használja az RFC7519 JSON-webtoken (JWT) parancsot egy "x-ms-policyCertificate" nevű jogcímhez. A jogcím értéke egy RFC7517 JSON-webkulcs, amely tartalmazza a hozzáadni kívánt tanúsítványt. A JWT-t a szolgáltatóhoz társított érvényes házirend-aláíró tanúsítványok titkos kulcsával kell aláírni. Lásd [a szabályzat-aláíró példákat.](./policy-signer-examples.md)

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Igazolási szabályzat konfigurációs hibája

**Hibakód** PolicyParsingError

**Példaforgatókönyvek** Helytelen szintaxissal megadott szabályzat (például hiányzó pontosvessző)/érvényes JWT-szabályzat)

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Hibakód** InvalidOperation (Érvénytelen működés)

**Példaforgatókönyvek** Érvénytelen tartalom (például feltöltési szabályzat/aláíratlan szabályzat, ha a szabályzat aláírása szükséges)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Hibaelhárítási lépések** Győződjön meg arról, hogy a szövegformátumú szabályzat UTF-8 kódolású.

Ha házirend-aláírásra van szükség, az igazolási házirendet csak RFC7519 JSON-webtoken (JWT) formátumban kell konfigurálni. Ha a szabályzat aláírása nem kötelező, a házirend szöveg- vagy JWT-formátumban is konfigurálható.

A szabályzat JWT-formátumban való konfiguráláshoz használja a JWT-t egy "AttestationPolicy" nevű jogcímhez. A jogcím értéke a szabályzat szövegének Base64URL kódolású verziója. Ha az igazolási szolgáltató házirend-aláíró tanúsítványokkal van konfigurálva, a JWT-t a szolgáltatóhoz társított bármely érvényes házirend-aláíró tanúsítvány titkos kulcsával alá kell írni. 

Ha szövegformátumban konfigurál egy szabályzatot, adja meg közvetlenül a szabályzat szövegét.

A PowerShellben adja meg a PolicyFormatet JWT-ként a szabályzat JWT-formátumban való konfiguráláshoz. Az alapértelmezett szabályzatformátum a Szöveg.

Lásd az igazolási szabályzat [példái és](./policy-examples.md) az [igazolási szabályzatok írásának mikéntje című cikkeket.](./author-sign-policy.md) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. Az Az.Attestation telepítési problémái a PowerShellben

Nem lehet telepíteni az Az vagy az Az.Attestation modulokat a PowerShellben

### <a name="error"></a>Hiba

FIGYELMEZTETÉS: Nem sikerült feloldani a PackageManagement\Install-Package csomagforrást: Nem található egyezés a megadott keresési feltételhez és https://www.powershellgallery.com/api/v2 a modul nevéhez

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

PowerShell-galéria (TLS) Transport Layer Security 1.0-s és 1.1-es verziói elavultak. 

A TLS 1.2-es vagy újabb verziója ajánlott. 

Ha továbbra is használni PowerShell-galéria, futtassa a következő parancsot a Install-Module parancsai előtt

**[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. Szabályzat-hozzáférési/konfigurációs problémák a PowerShellben

A megfelelő szerepkörökkel rendelkező felhasználó. De hitelesítési problémákba kell szembesülni az igazolási szabályzatok PowerShell-en keresztüli kezelése során.

### <a name="error"></a>Hiba
Az objektumazonosító objektumazonosítóval (Id) nem rendelkezik engedéllyel a &lt; &gt;  Microsoft.Authorization/roleassignments/write művelet végrehajtásához a subcriptions/ &lt; subscriptionId &gt; resourcegroups/secure_enclave_poc/providers/Microsoft.Authorization/roleassignments/ &lt; role assignmentId hatókörben, vagy a hatókör &gt; érvénytelen. Ha nemrég kapott hozzáférést, frissítse hitelesítő adatait

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Az igazolási műveletek támogatásához szükséges Az modulok minimális verziója a következő: 

 **Az Az 4.5.0** 
 
 **Az.Accounts 1.9.2**
 
 **Az.Attestation 0.1.8** 

Futtassa az alábbi parancsot az összes Az modul telepített verziójának ellenőrzéséhez 

```powershell
Get-InstalledModule 
```

Ha a verziók nem egyeznek meg a minimális követelménnyel, futtassa a Update-Module parancsokat

például: - Update-Module -Name Az.Attestation
