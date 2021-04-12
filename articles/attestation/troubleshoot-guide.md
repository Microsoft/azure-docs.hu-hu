---
title: Azure Attestation – Hibaelhárítási útmutató
description: Hibaelhárítási útmutató a gyakran megfigyelt problémákhoz
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ae3e12c11f194b3efcc149382dc952bd74d38b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97704316"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Microsoft Azure igazolás hibaelhárítási útmutatója

Az Azure-igazolásban történt hibakezelés a [Microsoft REST API irányelvek](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses)követésével valósul meg. Az Azure igazolási API-k által visszaadott hibaüzenet a HTTP-állapotkódot, valamint a "code" és az "Message" névvel rendelkező név/érték párokat tartalmazza. A "code" érték az ember által olvasható, és a hiba típusának jelzése. Az "üzenet" érték a felhasználót kívánja támogatni, és a hiba részleteit jeleníti meg.

Ha a probléma nem szerepel ebben a cikkben, Azure-támogatási kérelmet is beküldhet az [Azure támogatási oldalára](https://azure.microsoft.com/support/options/).

Az alábbiakban néhány példát láthat az Azure-igazolás által visszaadott hibákra:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP – 401: jogosulatlan kivétel

### <a name="http-status-code"></a>HTTP-állapotkód
401

**Hibakód** Jogosulatlan

**Példák a forgatókönyvekre**
  - Igazolási hiba, ha a felhasználó nincs hozzárendelve igazolási olvasó szerepkörrel
  - Nem sikerült kezelni az igazolási házirendeket, mert a felhasználó nincs hozzárendelve a megfelelő szerepkörökhöz
  - Nem lehet felügyelni a tanúsítvány-előállítók felügyeletét, mert a felhasználó nincs hozzárendelve a megfelelő szerepkörökhöz

Az a felhasználó, aki olvasói szerepkörrel próbálta módosítani az igazolási szabályzatot a PowerShellben 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Hibaelhárítási lépések**

Az igazolási házirendek/szabályzat-aláírók megtekintéséhez az Azure AD-felhasználónak a "műveletek" engedélyre van szüksége:
- Microsoft. igazolás/attestationProviders/igazolás/olvasás

  Ez az engedély hozzárendelhető egy AD-felhasználóhoz egy szerepkör, például a "tulajdonos" (helyettesítő engedélyek) vagy az "olvasó" (helyettesítő engedélyek) vagy az "igazolási olvasó" (csak az Azure-igazolásra vonatkozó egyedi engedélyek) használatával.

A szabályzat-aláírók hozzáadásához vagy törléséhez, illetve a házirendek konfigurálásához az Azure AD-felhasználónak a következő engedélyekkel kell rendelkeznie a műveletekhez:
- Microsoft. igazolás/attestationProviders/igazolás/írás
- Microsoft. igazolás/attestationProviders/igazolás/törlés

  Ezek az engedélyek egy AD-felhasználóhoz társíthatók, például a "tulajdonos" (helyettesítő engedélyek), a "közreműködő" (helyettesítő engedélyek) vagy az "igazolás közreműködője" (csak az Azure-hitelesítésre vonatkozó egyedi engedélyek).

Az ügyfelek dönthetnek úgy, hogy az alapértelmezett szolgáltatót használják az igazoláshoz, vagy egyéni szabályzatokkal hoznak létre saját szolgáltatókat. Ha igazolási kéréseket szeretne küldeni az egyéni igazolási szolgáltatók számára, "tulajdonos" (helyettesítő jogosultságok) vagy "olvasó" (helyettesítő engedélyek) vagy "igazolási olvasó" szerepkörre van szükség a felhasználó számára. Az alapértelmezett szolgáltatók bármely Azure AD-felhasználó számára elérhetők.

A szerepkörök a PowerShellben való ellenőrzéséhez futtassa az alábbi parancsot:

a. Indítsa el a PowerShellt, és jelentkezzen be az Azure-ba a "kapcsolat-AzAccount" parancsmag használatával

b. Az Azure-szerepkör-hozzárendelés beállításainak ellenőrzése


  ```powershell
  $c = Get-AzContext
  Get-AzRoleAssignment -ResourceGroupName $attestationResourceGroup -ResourceName $attestationProvider -ResourceType Microsoft.Attestation/attestationProviders -SignInName $c.Account.Id
  ```

  Ennek nagyjából a következőképpen kell kinéznie:

  ```
  RoleAssignmentId   :/subscriptions/subscriptionId/providers/Microsoft.Authorization/roleAssignments/roleAssignmentId
  
  Scope              : /subscriptions/subscriptionId
  
  DisplayName        : displayName
  
  SignInName         : signInName
  
  RoleDefinitionName : Reader
  
  RoleDefinitionId   : roleDefinitionId
  
  ObjectId           : objectid
  
  ObjectType         : User
  
  CanDelegate        : False
 
  ```

c. Ha nem találja a megfelelő szerepkör-hozzárendelést a listában, kövesse az [itt](../role-based-access-control/role-assignments-powershell.md) található utasításokat.

## <a name="2-http--400-errors"></a>2. HTTP – 400 hibák

### <a name="http-status-code"></a>HTTP-állapotkód
400

A kérések a 400-as visszaküldésének különböző okai lehetnek. Az alábbiakban néhány példát láthat az Azure igazolási API-k által visszaadott hibákra:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. Igazolási hiba a házirend-kiértékelési hibák miatt

Az igazolási házirend engedélyezési szabályokat és kiállítási szabályokat tartalmaz. Az enklávék bizonyítékait a rendszer az engedélyezési szabályok alapján értékeli ki. A kiállítási szabályok határozzák meg az igazolási jogkivonat részét képező jogcímeket. Ha az enklávéban lévő jogcímek nem felelnek meg az engedélyezési szabályoknak, a rendszer igazolja a szabályzatok kiértékelésének hibáját. 

**Hibakód** PolicyEvaluationError

**Példák a forgatókönyvekre** Ha az enklávé-idézőjelben lévő jogcímek nem egyeznek az igazolási házirend engedélyezési szabályaival

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Hibaelhárítási lépések** A felhasználók a SGX ENKLÁVÉHOZ-igazolási házirend alapján kiértékelik az enklávé-tanúsítványt, mielőtt ugyanezt konfigurálja.

Küldési kérelem küldése az API-nak a "draftPolicyForAttestation" paraméterben található házirend szövegének megadásával. A AttestSgxEnclave API ezt a házirend-dokumentumot fogja használni a tanúsító hívás során, így a használat előtt tesztelheti az igazolási házirendeket. A mező jelenléte esetén generált igazolási jogkivonat nem lesz biztonságos.

Lásd a [tanúsítványokra vonatkozó példákat](./policy-examples.md)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Érvénytelen bevitel miatti igazolási hiba

**Hibakód** InvalidParameter

**Példák a forgatókönyvekre** Érvénytelen bevitel miatti SGX ENKLÁVÉHOZ-igazolási hiba. Néhány példa a hibaüzenetekre:
- A megadott idézőjel érvénytelen az árajánlat-biztosítékban található hiba miatt. 
- A megadott idézőjel érvénytelen, mert az az eszköz, amelyen az árajánlat létrejött, nem felel meg az Azure-beli alapkövetelményeknek
- A megadott idézőjel érvénytelen, mert a Cache Service PCK által megadott TCBInfo vagy QEID érvénytelen volt.

**Hibaelhárítási lépések**

Microsoft Azure igazolás támogatja az Intel SDK és az Open enklávé SDK által generált SGX ENKLÁVÉHOZ idézetek igazolását.

Tekintse [meg az igazolást az Open](/samples/browse/?expanded=azure&terms=attestation) enklávé SDK/Intel SDK használatával

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. Érvénytelen tanúsítványlánc-hiba történt a házirend/házirend-aláíró feltöltése során

**Hibakód** InvalidParameter

**Példák a forgatókönyvekre** Az aláírt házirend konfigurálása vagy a házirend-aláíró hozzáadása/törlése, amely érvénytelen tanúsítványlánc alá van írva (például ha a főtanúsítvány alapszintű megkötések bővítménye nem a tulajdonos típusára van beállítva = CA)

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Hibaelhárítási lépések** A főtanúsítványt CA által kiállítottként kell megjelölni (az X. 509 alapszintű megkötések), máskülönben a rendszer nem számít érvényes tanúsítványnak. 

Győződjön meg arról, hogy a főtanúsítvány alapszintű megkötések bővítménye úgy van beállítva, hogy jelezze, hogy a tulajdonos típusa = CA

Máskülönben a tanúsítványlánc érvénytelennek tekintendő.

Lásd: [házirend-aláíró](./policy-signer-examples.md) és [házirend](./policy-examples.md) -példák 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. Házirend-aláíró hibájának hozzáadása/törlése

**Hibakód** InvalidOperation

**Példák a forgatókönyvekre**

Ha a felhasználó a "Maa-policyCertificate" jogcím nélkül tölt fel JWS

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

**Hibaelhárítási lépések** Új házirend-aláíró tanúsítvány hozzáadásához/törléséhez használja az RFC7519 JSON Web Token (JWT) kifejezést az "x-MS-policyCertificate" nevű jogcím használatával. A jogcím értéke egy RFC7517 JSON-webkulcs, amely tartalmazza a hozzáadni kívánt tanúsítványt. A JWT a szolgáltatóhoz tartozó érvényes házirend-aláíró tanúsítványok titkos kulcsával kell aláírni. Lásd: [házirend-aláíró példák](./policy-signer-examples.md).

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Igazolási házirend konfigurációs hibája

**Hibakód** PolicyParsingError

**Példák a forgatókönyvekre** Helytelen szintaxissal megadott házirend (például hiányzó pontosvessző)/valid JWT-szabályzat)

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

**Hibakód** InvalidOperation

**Példák a forgatókönyvekre** Érvénytelen tartalom van megadva (például feltöltési házirend/aláíratlan szabályzat, ha házirend-aláírás szükséges)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Hibaelhárítási lépések** Győződjön meg arról, hogy a házirend szöveges formátumban UTF-8 kódolású.

Ha házirend-aláírásra van szükség, a hitelesítő házirendet csak RFC7519 JSON Web Token (JWT) formátumban kell konfigurálni. Ha a szabályzat aláírása nem szükséges, a házirend szöveg-vagy JWT formátumban is konfigurálható.

A szabályzatok JWT formátumban való konfigurálásához használja a JWT kifejezést a "AttestationPolicy" nevű jogcím használatával. A jogcím értéke a Base64URL kódolt verziója. Ha az igazolási szolgáltató házirend-aláíró tanúsítványokkal van konfigurálva, a JWT a szolgáltatóhoz társított érvényes házirend-aláíró tanúsítványok titkos kulcsával kell aláírnia. 

Ha egy házirendet szöveges formátumban szeretne konfigurálni, akkor közvetlenül adja meg a házirend szövegét.

A PowerShellben adja meg a PolicyFormat as JWT, hogy JWT formátumban konfigurálja a szabályzatot. Az alapértelmezett házirend-formátum a Text (szöveg).

Lásd a tanúsítványokra [vonatkozó példákat](./policy-examples.md) és az [igazolási szabályzatok létrehozási módját](./author-sign-policy.md) . 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. az az igazolás telepítési problémái a PowerShellben

Nem lehet telepíteni az az vagy az az. igazolási modulokat a PowerShellben

### <a name="error"></a>Hiba

Figyelmeztetés: nem sikerült feloldani a (z) " https://www.powershellgallery.com/api/v2 " PackageManagement\Install-Package: nem található egyezés a megadott keresési feltételekhez és a modul nevéhez.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

PowerShell-galéria elavult Transport Layer Security (TLS) 1,0-es és 1,1-es verziójú. 

A TLS 1,2 vagy újabb verzió használata javasolt. 

A PowerShell-galéria folytatásához futtassa a következő parancsot a Install-Module parancsok előtt.

**[Net. ServicePointManager]:: SecurityProtocol = [net. SecurityProtocolType]:: Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. házirend-hozzáférési/konfigurációs problémák a PowerShellben

A felhasználó hozzárendelve a megfelelő szerepkörökhöz. Azonban a hitelesítési házirendek a PowerShellen keresztüli kezelése során az engedélyezési problémákkal szembesülnek.

### <a name="error"></a>Hiba
Az objektumazonosító-azonosítójú ügyfél &lt; &gt;  nem rendelkezik a művelet végrehajtásához szükséges engedéllyel a Microsoft. Authorization/RoleAssignments/write (hatókör: "subcriptions/ &lt; subscriptionId &gt; resourcegroups/secure_enclave_poc/Providers/Microsoft.Authorization/RoleAssignments/ &lt; role assignmentId") &gt; vagy a hatókör érvénytelen. Ha a hozzáférés a közelmúltban lett megadva, frissítse a hitelesítő adatait

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Az igazolási műveletek támogatásához szükséges minimális verzió az az alábbi: 

 **Az 4.5.0** 
 
 **Az. accounts 1.9.2**
 
 **Az. igazolási 0.1.8** 

Futtassa az alábbi parancsot az összes modul telepített verziójának ellenőrzéséhez 

```powershell
Get-InstalledModule 
```

Ha a verziók nem felelnek meg a minimális követelménynek, futtassa Update-Module parancsokat

például:-Update-Module-Name az. igazolás
