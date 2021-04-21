---
title: A kulcsokról – Azure Key Vault
description: A rest Azure Key Vault és a kulcsok fejlesztői részleteinek áttekintése.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: b9565095a40052a940d7a7b31f0fd3a27e0e75c2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814995"
---
# <a name="about-keys"></a>Információ a kulcsokról

Azure Key Vault titkosítási kulcsok tárolására és kezelésére kétféle erőforrást kínál. A tárolók támogatják a szoftveres és A HSM által védett (hardveres biztonsági modul) kulcsokat. A felügyelt HSM-ek csak a HSM által védett kulcsokat támogatják. 

|Erőforrás típusa|Fő védelmi módszerek|Adatsík végpontjának alap URL-címe|
|--|--|--|
| **Kulcstartók** | Szoftveres védelem<br/><br/>és<br/><br/>HSM által védett (prémium termékváltozatú)</li></ul> | https://{tároló neve}.vault.azure.net |
| **Felügyelt HSM-ek** | HSM által védett | https://{hsm-name}.managedhsm.azure.net |
||||

- **Tárolók** – A tárolók alacsony költségű, könnyen üzembe helyezhető, több-bérlős, zónatitkos (ha elérhető) kulcskezelő megoldást biztosítanak, amely a leggyakoribb felhőalkalmazás-forgatókönyvekhez alkalmas.
- **Felügyelt HSM-ek** – A Managed HSM egybérlős, zónaredőképes (ha elérhető) HSM-eket biztosít a titkosítási kulcsok tárolására és kezelésére. Leginkább a nagy értékű kulcsokat kezelő alkalmazásokhoz és használati forgatókönyvekhez alkalmas. Emellett segít megfelelni a legszigorúbb biztonsági, megfelelőségi és szabályozási követelményeknek. 

> [!NOTE]
> A tárolók a kriptográfiai kulcsok mellett számos objektumtípus, például titkos kulcsok, tanúsítványok és tárfiókkulcsok tárolását és kezelését is lehetővé teszik.

A titkosítási kulcsok Key Vault [JWK] JSON-webkulcs képviselik. A JavaScript Object Notation (JSON) és a JavaScript Object Signing and Encryption (SSL) specifikációi a következőek:

-   [JSON-webkulcs (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web Encryption (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON webes algoritmusok (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON webaláírás (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Az alap JWK-/JWA-specifikációk is ki vannak bővülve, hogy az egyedi kulcstípusokat Azure Key Vault managed HSM-implementációkhoz. 

A HSM által védett kulcsokat (más néven HSM-kulcsokat) egy HSM (hardveres biztonsági modul) feldolgoz, és mindig HSM-védelmi határvonalon marad. 

- A tárolók **a FIPS 140-2 2.** szintje szerint ellenőrzött HSM-eket használják a megosztott HSM-háttérinfra infrastruktúrában található HSM-kulcsok védelméhez. 
- A Managed HSM **FIPS 140-2 Level 3** ellenőrzött HSM-modulokat használ a kulcsok védelméhez. Minden HSM-készlet egy elkülönített egybérlős példány, saját biztonsági tartománnyal, amely teljes kriptográfiai elkülönítést biztosít az ugyanazon hardverinfrafra infrastruktúrán osztozó többi HSM-től. [](../managed-hsm/security-domain.md)

Ezek a kulcsok egybérlős HSM-készletekben vannak védve. AZ RSA-, EC- és szimmetrikus kulcsokat importálhatja soft formával vagy egy támogatott HSM-eszközről való exportálással. HSM-készletekben is létrehozhat kulcsokat. Ha a [BYOK (saját](../keys/byok-specification.md)kulcs használata) specifikációban leírt módszerrel importál HSM-kulcsokat, az lehetővé teszi a biztonságos szállítási kulcsanyagokat a Managed HSM-készletekbe. 

A földrajzi határokkal kapcsolatos további információkért lásd: Microsoft Azure [Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>Kulcstípusok és védelmi módszerek

Key Vault TÁMOGATJA az RSA- és EC-kulcsokat. A Managed HSM támogatja az RSA-, EC- és szimmetrikus kulcsokat. 

### <a name="hsm-protected-keys"></a>HSM-védett kulcsok

|Kulcs típusa|Tárolók (csak Prémium termékváltozat)|Felügyelt HSM-ek|
|--|--|--|
|**EC-HSM:** Elliptikus görbe kulcs | Támogatott | Támogatott|
|**RSA-HSM:** RSA-kulcs|Támogatott|Támogatott|
|**oct-HSM:** Szimmetrikus kulcs|Nem támogatott|Támogatott|
|||

### <a name="software-protected-keys"></a>Szoftveres védelem alatt található kulcsok

|Kulcs típusa|Kulcstartók|Felügyelt HSM-ek|
|--|--|--|
**RSA:**"Szoftveres védelem" RSA-kulcs|Támogatott|Nem támogatott
**EC:**"Szoftveres védelem alatt" Három ponttal védett görbe kulcs|Támogatott|Nem támogatott
|||

### <a name="compliance"></a>Megfelelőség

|Kulcs típusa és célja|Megfelelőség|
|---|---|
|Szoftveres védelem által védett kulcsok tárolókban (Prémium & Standard SKUs) | FIPS 140-2 1. szint|
|HSM által védett kulcsok tárolókban (prémium termékváltozat)| 2. szintű FIPS 140-2|
|HSM által védett kulcsok a Managed HSM-ban|FIPS 140-2, 3. szint|
|||



Az [egyes kulcstípusokkal,](about-keys-details.md) algoritmusokkal, műveletekkel, attribútumokkal és címkékkel kapcsolatos részletekért lásd: Kulcstípusok, algoritmusok és műveletek.

## <a name="next-steps"></a>Következő lépések
- [Tudnivalók a Key Vaultról](../general/overview.md)
- [A Managed HSM ismertetése](../managed-hsm/overview.md)
- [Információ a titkos kulcsokról](../secrets/about-secrets.md)
- [Információ a tanúsítványokról](../certificates/about-certificates.md)
- [Key Vault REST API áttekintése](../general/about-keys-secrets-certificates.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
