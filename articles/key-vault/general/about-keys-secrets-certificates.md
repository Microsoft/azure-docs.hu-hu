---
title: A kulcsok, a titkok és a tanúsítványok Azure Key Vault áttekintése
description: A kulcsok, titkok és tanúsítványok Azure Key Vault REST-felületének és fejlesztői adatainak áttekintése.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: f2a33b4011b0bb89ca17010fc787aebe8c17fc92
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183930"
---
# <a name="azure-key-vault-keys-secrets-and-certificates-overview"></a>Azure Key Vault kulcsok, titkok és tanúsítványok áttekintése

A Azure Key Vault lehetővé teszi Microsoft Azure alkalmazások és felhasználók számára, hogy különböző típusú titkos/kulcsfontosságú adattípusokat tároljanak és használjanak. Key Vault erőforrás-szolgáltató két erőforrástípust támogat: tárolókat és felügyelt HSM.

## <a name="dns-suffixes-for-base-url"></a>Az alap URL-címhez tartozó DNS-utótagok
 Az alábbi táblázat az adatsík végpont által a tárolók és a felügyelt HSM-készletek különböző felhőalapú környezetekben használt alap URL-cím DNS-utótagját mutatja be.

Felhőalapú környezet | Tárolók DNS-utótagja | A felügyelt HSM DNS-utótagja
---|---|---
Azure-felhő | . vault.azure.net | . managedhsm.azure.net
Azure China Cloud | . vault.azure.cn | Nem támogatott
Azure US Government | . vault.usgovcloudapi.net | Nem támogatott
Azure German Cloud | . vault.microsoftazure.de | Nem támogatott
|||


## <a name="object-types"></a>Objektumtípusok
 Az alábbi táblázat az objektumok típusait és utótagját mutatja az alap URL-címben.

Objektumtípus|URL-utótag|Kulcstartók|Felügyelt HSM-készletek
--|--|--|--
**Titkosítási kulcsok**||
HSM-védett kulcsok|/keys|Támogatott|Támogatott
Szoftveres védelemmel ellátott kulcsok|/keys|Támogatott|Nem támogatott
**Egyéb objektumtípusok**||
Titkos kulcsok|/secrets|Támogatott|Nem támogatott
Tanúsítványok|/certificates|Támogatott|Nem támogatott
Tárfiókkulcsok|/storage|Támogatott|Nem támogatott
|||
- **Titkosítási kulcsok**: több kulcs típust és algoritmust is támogat, valamint lehetővé teszi a szoftveres védelemmel ellátott és HSM által védett kulcsok használatát. További információ: [a kulcsok ismertetése](../keys/about-keys.md).
- **Titkok**: biztonságos tárhelyet biztosít a titkos kulcsokhoz, például jelszavakhoz és adatbázis-kapcsolatok karakterláncokhoz. További információ: [About Secrets](../secrets/about-secrets.md).
- **Tanúsítványok**: a kulcsokra és titkokra épülő tanúsítványokat támogatja, és egy automatikus megújítási funkciót ad hozzá. További információ: [Tudnivalók a tanúsítványokról](../certificates/about-certificates.md).
- **Azure Storage-fiókok kulcsai**: felügyelheti az Azure Storage-fiók kulcsait. Belsőleg Key Vault a kulcsokat egy Azure Storage-fiókkal listázhatja (szinkronizálhatja), és rendszeresen újragenerálhatja (elforgathatja) a kulcsokat. További információ: [a Storage-fiók kulcsainak kezelése a Key Vault](../secrets/overview-storage-keys.md).

További általános információk a Key Vaultről: [about Azure Key Vault](overview.md). További információ a felügyelt HSM-készletekről: mi a [Azure Key Vault Managed HSM?](../managed-hsm/overview.md)


## <a name="data-types"></a>Adattípusok

Tekintse meg a következő témakört: a kulcsok, a titkosítás és az aláírás releváns adattípusaihoz tartozó JOSE-specifikációk.  

-   **algoritmus** – a kulcs műveletének támogatott algoritmusa, például RSA1_5  
-   **rejtjelezett-érték** -titkosítatlan szöveges oktettek, Base64URL használatával kódolva  
-   **kivonatoló érték** – a Base64URL használatával kódolt kivonatoló algoritmus kimenete  
-   **kulcs-típus** – az egyik támogatott kulcs típusa, például RSA (Rivest-a-Adleman).  
-   **egyszerű szöveges érték** – egyszerű szöveges oktettek, Base64URL használatával kódolva  
-   **aláírás-érték** – az Base64URL használatával kódolt aláírási algoritmus kimenete  
-   **base64URL** – a BASE64URL [RFC4648] kódolású bináris érték  
-   **logikai** – igaz vagy hamis  
-   **Identity (identitás** ) – Azure Active Directory identitása (HRE).  
-   **IntDate** – egy JSON decimális érték, amely az 1970-01-01T0:0: 0z UTC és a megadott UTC dátum/idő közötti másodpercek számát jelöli. A dátum-és időpontokra vonatkozó részletekért, valamint az általános és az UTC RFC3339 lásd:.  

## <a name="objects-identifiers-and-versioning"></a>Objektumok, azonosítók és verziószámozás

A Key Vaultban tárolt objektumok verziószámozást kapnak, amikor egy objektum új példánya jön létre. Minden verzióhoz egyedi azonosító és URL-cím tartozik. Egy objektum első létrehozásakor a rendszer egyedi verzióazonosító-azonosítót kap, és az objektum aktuális verziójaként van megjelölve. Egy új példány ugyanazzal az objektummal való létrehozása lehetővé teszi az új objektum egyedi verziószámát, ami azt eredményezi, hogy az aktuális verzió lesz.  

A Key Vaultban lévő objektumok egy verzió megadásával vagy a verzió az objektum aktuális verziójában való kihagyásával kezelhetők. Például, ha egy kulcs a névvel van megadva `MasterKey` , és egy verzió megadása nélkül végez műveleteket, a rendszer a legújabb elérhető verziót használja. A verzió-specifikus azonosítóval végzett műveletek végrehajtása azt eredményezi, hogy a rendszer az objektum adott verzióját használja.  

### <a name="vault-name-and-object-name"></a>Tár – név és objektum neve
Az objektumok egyedileg azonosíthatók a Key Vaulton belül egy URL-cím használatával. A rendszeren nincs két objektum ugyanazzal az URL-címmel, a földrajzi helytől függetlenül. Az objektum teljes URL-címét objektumazonosítónak nevezzük. Az URL-cím egy előtagból áll, amely a Key Vault, az Objektumtípus, a felhasználó által megadott objektumnév és az objektum verziószámát azonosítja. Az Objektumnév megkülönbözteti a kis-és nagybetűket, és nem változtathatók meg. Az objektum verziószámát nem tartalmazó azonosítókat alapazonosítóknak nevezzük.  

További információ: [hitelesítés, kérések és válaszok](authentication-requests-and-responses.md)

Az objektumazonosító a következő általános formátumú (a tároló típusától függően):  

- Tárolók **esetén**:`https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

- **Felügyelt HSM-készletek esetén**: `https://{hsm-name}.managedhsm.azure.net/{object-type}/{object-name}/{object-version}`  

> [!NOTE]
> Lásd: [objektumtípus támogatása](#object-types) az egyes típusú objektumok által támogatott objektumtípusok számára.

Ahol:  

| Elem | Leírás |  
|-|-|  
|`vault-name` vagy `hsm-name`|Egy tár vagy egy felügyelt HSM-készlet neve a Microsoft Azure Key Vault szolgáltatásban.<br /><br />A tároló neveit és a felügyelt HSM-készletek nevét a felhasználó választja ki, és globálisan egyediek.<br /><br />A tár nevének és a felügyelt HSM-készlet nevének 3-24 karakterből kell állnia, amely csak 0-9, a-z, A-Z és A-.|  
|`object-type`|Az objektum típusa, "kulcsok", "titkok" vagy "tanúsítványok".|  
|`object-name`|Az a `object-name` felhasználó által megadott név, és egyedinek kell lennie egy Key Vaulton belül. A névnek 1-127 karakterből álló karakterláncnak kell lennie, betűvel kell kezdődnie, és csak 0-9, a-z, A-Z és-.|  
|`object-version`|Az egy `object-version` rendszer által generált, 32 karakterből álló karakterlánc-azonosító, amely egy objektum egyedi verziójának kezelésére szolgál.|  

## <a name="next-steps"></a>Következő lépések

- [Információ a kulcsokról](../keys/about-keys.md)
- [Információ a titkos kulcsokról](../secrets/about-secrets.md)
- [Információ a tanúsítványokról](../certificates/about-certificates.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
