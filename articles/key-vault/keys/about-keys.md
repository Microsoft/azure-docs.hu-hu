---
title: A kulcsok ismertetése – Azure Key Vault
description: A kulcsok Azure Key Vault REST-felületének és fejlesztői adatainak áttekintése.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: 3c4bb61217c7b972220a55a4837c2b3db980f2ca
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095992"
---
# <a name="about-keys"></a>Információ a kulcsokról

Azure Key Vault két típusú erőforrást biztosít a titkosítási kulcsok tárolásához és kezeléséhez. A tárolók támogatják a szoftveres védelemmel ellátott és HSM-védelemmel ellátott (hardveres biztonsági modul) kulcsokat. A felügyelt HSM csak a HSM-védelemmel ellátott kulcsokat támogatják. 

|Erőforrás típusa|Kulcsfontosságú védelmi módszerek|Adatsík-végpont alap URL-címe|
|--|--|--|
| **Kulcstartók** | Szoftveres védelemmel ellátott<br/><br/>és<br/><br/>HSM által védett (prémium SKU-val)</li></ul> | https://{Vault-Name}. Vault. Azure. net |
| * * Felügyelt HSM * * | HSM által védett | https://{HSM-Name}. managedhsm. Azure. net |
||||

- **Tárolók – a** tárolók alacsony költséghatékonyságú, könnyen telepíthető, több-bérlős, zóna-rugalmas (ahol elérhető), nagy teljesítményű kulcskezelő megoldás, amely a leggyakoribb felhőalapú alkalmazási forgatókönyvekhez megfelelő.
- A **felügyelt HSM** által FELÜGYELt HSM egybérlős, rugalmas (ahol elérhető), magasan elérhető HSM biztosít a titkosítási kulcsok tárolásához és kezeléséhez. A leginkább megfelelő alkalmazások és használati forgatókönyvek esetében, amelyek nagy értékű kulcsokat kezelnek. Emellett segít a legszigorúbb biztonsági, megfelelőségi és szabályozási követelményeknek való megfelelésben. 

> [!NOTE]
> A tárolók lehetővé teszik a titkosítási kulcsok mellett különféle típusú objektumok, például a titkok, a tanúsítványok és a Storage-fiókok kulcsának tárolását és kezelését is.

A Key Vaultban található titkosítási kulcsok JSON webkulcs [JWK] objektumként jelennek meg. A JavaScript Object Notation (JSON) és a JavaScript Object aláírási és titkosítási (JOSE) specifikációi a következők:

-   [JSON-webkulcs (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON webes titkosítás (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON webes algoritmusok (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON webes aláírás (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Az alap JWK-/JWA-specifikációk a Azure Key Vault és a felügyelt HSM-implementációk egyedi alaptípusának engedélyezésére is kiterjednek. 

HSM-védelemmel ellátott kulcsok (más néven HSM-kulcsok) feldolgozása egy HSM-ben (hardveres biztonsági modul) történik, és mindig az HSM védelmi határa marad. 

- A tárolók az **FIPS 140-2 2. szintű** hitelesített HSM védik a HSM-kulcsok a megosztott HSM háttér-infrastruktúrában való ellátásához. 
- A Managed HSM **FIPS 140-2 3. szintű** hitelesített HSM-modult használ a kulcsok védeleméhez. Mindegyik HSM-készlet egy elkülönített, egybérlős példány, amely saját [biztonsági tartománnyal](../managed-hsm/security-domain.md) rendelkezik, és teljes titkosítási elkülönítést biztosít minden más HSM, amely azonos hardveres infrastruktúrát használ.

Ezek a kulcsok egyetlen bérlős HSM-készletekben vannak védve. Az RSA, az EC és a szimmetrikus kulcs importálható puha formában, vagy egy támogatott HSM-eszközről is exportálható. A HSM-készletekben is létrehozhat kulcsokat. Amikor HSM-kulcsokat importál a [BYOK (saját kulcs használata) specifikációban](../keys/byok-specification.md)ismertetett módszerrel, az lehetővé teszi a biztonságos szállítási kulcsra vonatkozó anyagok kezelését a felügyelt HSM-készletekbe. 

A földrajzi határokra vonatkozó további információkért lásd: [Microsoft Azure Adatvédelmi központ](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>A kulcsok típusai és védelmi módszerei

Key Vault támogatja az RSA-és az EK-kulcsokat. A felügyelt HSM támogatja az RSA, az EC és a szimmetrikus kulcsokat. 

### <a name="hsm-protected-keys"></a>HSM-védett kulcsok

|Kulcs típusa|Tárolók (csak prémium SKU)|Felügyelt HSM|
|--|--|--|
|**EC-HSM**: elliptikus görbe kulcsa | Támogatott | Támogatott|
|**RSA-HSM**: RSA-kulcs|Támogatott|Támogatott|
|**TOT – HSM**: szimmetrikus kulcs|Nem támogatott|Támogatott|
|||

### <a name="software-protected-keys"></a>Szoftveres védelemmel ellátott kulcsok

|Kulcs típusa|Kulcstartók|Felügyelt HSM|
|--|--|--|
**RSA**: "szoftveres védelemmel ellátott" RSA-kulcs|Támogatott|Nem támogatott
**EC**: "szoftveres védelemmel ellátott" elliptikus görbe kulcsa|Támogatott|Nem támogatott
|||

### <a name="compliance"></a>Megfelelőség

|Kulcs típusa és célhelye|Megfelelőség|
|---|---|
|Szoftveres védelemmel ellátott kulcsok a tárolókban (prémium & standard SKU-ban) | FIPS 140-2 1. szint|
|HSM által védett kulcsok a tárolókban (prémium SKU)| 2. szintű FIPS 140-2|
|HSM által védett kulcsok a felügyelt HSM-ben|FIPS 140-2 3. szint|
|||



A kulcs típusait, algoritmusait, műveleteit, attribútumait és címkéit részletesen lásd: [kulcshasználat, algoritmusok és műveletek](about-keys-details.md) .

## <a name="next-steps"></a>Következő lépések
- [Tudnivalók a Key Vaultról](../general/overview.md)
- [A Managed HSM ismertetése](../managed-hsm/overview.md)
- [Információ a titkos kulcsokról](../secrets/about-secrets.md)
- [Információ a tanúsítványokról](../certificates/about-certificates.md)
- [Key Vault REST API áttekintése](../general/about-keys-secrets-certificates.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
