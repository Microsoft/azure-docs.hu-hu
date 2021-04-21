---
title: Azure Key Vault kulcsok, titkos kulcsok és tanúsítványok áttekintése
description: A rest Azure Key Vault és a kulcsok, titkos kulcsok és tanúsítványok fejlesztői részleteinek áttekintése.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: e3eb77d15c288c93298da6dd79a76565e5d67f96
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749941"
---
# <a name="azure-key-vault-keys-secrets-and-certificates-overview"></a>Azure Key Vault kulcsok, titkos kulcsok és tanúsítványok áttekintése

Azure Key Vault lehetővé Microsoft Azure, hogy az alkalmazások és a felhasználók többféle titkos/kulcs típusú adatot tároljanak és használjanak. Key Vault erőforrás-szolgáltató két erőforrástípust támogat: tárolókat és felügyelt HSM-eket.

## <a name="dns-suffixes-for-base-url"></a>DNS-utótagok az alap URL-címhez
 Az alábbi táblázat az adatsík végpontja által különböző felhőkörnyezetekben használt tárolókhoz és felügyelt HSM-készletekhez használt alap URL DNS-utótagot mutatja.

Felhőalapú környezet | Tárolók DNS-utótagja | DNS-utótag felügyelt HSM-hez
---|---|---
Azure Cloud | .vault.azure.net | .managedhsm.azure.net
Azure China Cloud | .vault.azure.cn | Nem támogatott
Azure US Government | .vault.usgovcloudapi.net | Nem támogatott
Azure German Cloud | .vault.microsoftazure.de | Nem támogatott
|||


## <a name="object-types"></a>Objektumtípusok
 Az alábbi táblázat az objektumtípusokat és azok utótagját mutatja az alap URL-címben.

Objektumtípus|URL-utótag|Kulcstartók|Felügyelt HSM-készletek
--|--|--|--
**Titkosítási kulcsok**||
HSM-védett kulcsok|/keys|Támogatott|Támogatott
Szoftveres védelem alatt található kulcsok|/keys|Támogatott|Nem támogatott
**Egyéb objektumtípusok**||
Titkos kulcsok|/secrets|Támogatott|Nem támogatott
Tanúsítványok|/certificates|Támogatott|Nem támogatott
Tárfiókkulcsok|/storage|Támogatott|Nem támogatott
|||
- **Titkosítási kulcsok:** Több kulcstípust és algoritmust támogat, és lehetővé teszi a szoftveres és A HSM által védett kulcsok használatát. További információ: [Tudnivalók a kulcsokról.](../keys/about-keys.md)
- **Titkos** kulcsok: A titkos kulcsok, például jelszavak és adatbázis-kapcsolati sztringek biztonságos tárolását biztosítja. További információ: [Információk a titkos kulcsokról.](../secrets/about-secrets.md)
- **Tanúsítványok:** Támogatja a kulcsokra és titkos kulcsokra épülő tanúsítványokat, és egy automatikus megújítási funkciót ad hozzá. További információ: Tudnivalók [a tanúsítványokról.](../certificates/about-certificates.md)
- **Azure Storage-fiókkulcsok:** Kezelheti az Azure Storage-fiók kulcsait. Belsőleg a Key Vault kilistálhatja (szinkronizálhatja) a kulcsokat egy Azure Storage-fiókkal, és rendszeres időközönként újragenerálhatja (elforgathatja). További információ: [Tárfiókkulcsok kezelése a Key Vault.](../secrets/overview-storage-keys.md)

További általános információk a Key Vault: [About Azure Key Vault](overview.md). A Managed HSM-készletekkel kapcsolatos további információkért lásd: Mi Azure Key Vault [Managed HSM?](../managed-hsm/overview.md)


## <a name="data-types"></a>Adattípusok

A kulcsokra, titkosításra és aláírásra vonatkozó adattípusokért tekintse meg a RÉSZLETES SPECIFIKÁCIÓKAT.  

-   **algoritmus** – egy kulcsművelet támogatott algoritmusa, például RSA1_5  
-   **ciphertext-value** – Base64URL használatával kódolt titkosítási szöveges oktettek  
-   **digest-value** – egy kivonatoló algoritmus kimenete Base64URL használatával kódolva  
-   **kulcs-típus** – a támogatott kulcstípusok egyike, például RSA (Rivest-Shamir-Adleman).  
-   **plaintext-value** – egyszerű szöveges oktettek, Base64URL használatával kódolva  
-   **signature-value** – egy aláírási algoritmus kimenete Base64URL használatával kódolva  
-   **base64URL** – Base64URL [RFC4648] kódolású bináris érték  
-   **logikai érték** – igaz vagy hamis  
-   **Identitás** – identitás a Azure Active Directory (AAD).  
-   **IntDate** – JSON decimális érték, amely az 1970-01-01T0:0:0Z UTC időponttól a megadott UTC-dátum/idő időpontig megadott másodpercek számát adja meg. Az RFC3339 a dátummal/időpontokkal kapcsolatos részletes információkat tartalmaz, általában és különösen az UTC időzónával kapcsolatban.  

## <a name="objects-identifiers-and-versioning"></a>Objektumok, azonosítók és verziószámozás

A tárolókban Key Vault a rendszer verziószámmal egészül ki, amikor új objektumpéldány jön létre. Minden verzióhoz egyedi azonosító és URL-cím van rendelve. Az objektum első létrehozásakor a rendszer egyedi verzióazonosítót kap, és az objektum aktuális verziójaként lesz megjelölve. Egy új példány létrehozása ugyanazokkal az objektumnévvel egyedi verzióazonosítót ad az új objektumnak, így az lesz az aktuális verzió.  

A Key Vault egy verzió megadásával vagy az objektum aktuális verzióján lévő műveletek verziójának kihagyása által háríthatóak el. Ha például a kulcs neve , és verzió megadása nélkül hajt végre műveleteket, a rendszer a legújabb elérhető `MasterKey` verziót használja. Ha a verzióspecifikus azonosítóval végez műveleteket, a rendszer az objektum adott verzióját használja.  

### <a name="vault-name-and-object-name"></a>Tárolónév és objektumnév
Az objektumok egyedileg vannak azonosítva a Key Vault URL-cím használatával. A rendszer két objektumának URL-címe nem azonos a földrajzi helytől függetlenül. Az objektumok teljes URL-címét objektumazonosítónak nevezzük. Az URL-cím egy előtagból áll, amely azonosítja a Key Vault, az objektumtípust, a felhasználó által megadott objektum nevét és egy objektumverziót. Az Objektum neve nemérzékeny és nem módosítható. Az objektumverziót nem magában foglaló azonosítókat alapazonosítóknak nevezzük.  

További információ: [Hitelesítés, kérések és válaszok](authentication-requests-and-responses.md)

Az objektumazonosítók általános formátuma a következő (tárolótípustól függően):  

- **Tárolókhoz:**`https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

- **Felügyelt HSM-készletekhez:**`https://{hsm-name}.managedhsm.azure.net/{object-type}/{object-name}/{object-version}`  

> [!NOTE]
> Lásd: [Objektumtípus-támogatás](#object-types) az egyes tárolótípusok által támogatott objektumtípusokhoz.

Ahol:  

| Elem | Leírás |  
|-|-|  
|`vault-name` vagy `hsm-name`|A tároló vagy felügyelt HSM-készlet neve a Microsoft Azure Key Vault szolgáltatásban.<br /><br />A tárolók és a Managed HSM-készletneveket a felhasználó választja ki, és globálisan egyediek.<br /><br />A tároló nevének és a Managed HSM-készlet nevének 3–24 karakteres sztringnek kell lennie, amely csak 0–9, a–z, A–Z és -értékeket tartalmazhat.|  
|`object-type`|Az objektum típusa, "kulcsok", "titkos kulcsok" vagy "tanúsítványok".|  
|`object-name`|Az `object-name` a felhasználó által megadott neve, és egyedinek kell lennie a Key Vault. A névnek 1–127 karakteres sztringnek kell lennie, amely egy betűvel kezdődik, és csak 0–9, a-z, A-Z és -.|  
|`object-version`|Az egy rendszer által létrehozott, 32 karakterből áll sztringazonosító, amely opcionálisan egy objektum egyedi `object-version` verziójának címzére használható.|  

## <a name="next-steps"></a>Következő lépések

- [Információ a kulcsokról](../keys/about-keys.md)
- [Információ a titkos kulcsokról](../secrets/about-secrets.md)
- [Információ a tanúsítványokról](../certificates/about-certificates.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
