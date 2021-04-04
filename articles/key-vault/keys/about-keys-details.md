---
title: Kulcshasználat, algoritmusok és műveletek – Azure Key Vault
description: Támogatott kulcs típusok, algoritmusok és műveletek (részletek).
services: key-vault
author: amitbapat
manager: msmbaldwin
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: ambapat
ms.openlocfilehash: 675c4f04ece322000ae0ebb44d6291c455db9397
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98133276"
---
# <a name="key-types-algorithms-and-operations"></a>Kulcstípusok, algoritmusok és műveletek

A Key Vault kétféle erőforrástípust támogat: a tárolókat és a felügyelt HSM. Mindkét erőforrás típusa különböző titkosítási kulcsokat támogat. Ha szeretné megtekinteni az egyes erőforrástípusok által támogatott típusú kulcsokat, tekintse meg a [kulcsok](about-keys.md)című témakört.

A következő táblázat a kulcstárolók és a támogatott algoritmusok összefoglalását mutatja be.

|Kulcs típusa/méretek/görbék| Titkosítás/visszafejtés<br>(Becsomagolás/kicsomagolás) | Aláírás/ellenőrzés | 
| --- | --- | --- |
|EC-P256, EC-P256K, EC-P384, EC-521|NA|ES256<br>ES256K<br>ES384<br>ES512|
|RSA 2K, 3K, 4K| RSA1_5<br>RSA – OAEP<br>RSA-OAEP-256|PS256<br>PS384<br>PS512<br>RS256<br>RS384<br>RS512<br>RSNULL| 
|AES 128 bites, 256 bites <br/>(Csak felügyelt HSM)| AES-KW<br>AES – GCM<br>AES – CBC| NA| 
|||

##  <a name="ec-algorithms"></a>EK-algoritmusok
 A következő algoritmus-azonosítók támogatottak az EC-HSM-kulcsok használatával

### <a name="curve-types"></a>Görbe típusai

-   **P-256** – a (z) p-256-es NIST-görbe, amely a [DSS FIPS pub 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)-ban van meghatározva.
-   **P-256K** – a másodperces görbe SECP256K1, amely a [2. másodperc: javasolt elliptikus görbe tartomány paramétereinek](https://www.secg.org/sec2-v2.pdf)meghatározása.
-   **P-384** – a (z) p-384-es NIST-görbe, amely a [DSS FIPS pub 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)-ban van meghatározva.
-   **P-521** – a (z) p-521-es NIST-görbe, amely a [DSS FIPS pub 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)-ban van meghatározva.

### <a name="signverify"></a>ALÁÍRÁS/ELLENŐRZÉS

-   **ES256** – ECDSA a P-256 görbével létrehozott SHA-256 kivonatokhoz és kulcsokhoz. Ez az algoritmus a következő címen érhető el: [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** – ECDSA a P-256K által létrehozott SHA-256 kivonatokhoz és kulcsokhoz. Ez az algoritmus a normalizálás függőben van.
-   **ES384** – ECDSA a P-384 görbével létrehozott SHA-384 kivonatokhoz és kulcsokhoz. Ez az algoritmus a következő címen érhető el: [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** – ECDSA a P-521 görbével létrehozott SHA-512 kivonatokhoz és kulcsokhoz. Ez az algoritmus a következő címen érhető el: [RFC7518](https://tools.ietf.org/html/rfc7518).

##  <a name="rsa-algorithms"></a>RSA-algoritmusok  
 A következő algoritmus-azonosítók támogatottak az RSA és az RSA-HSM kulcsok esetében  

### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, TITKOSÍTÁS/VISSZAFEJTÉS

-   **RSA1_5** -RSAES-PKCS1-V1_5 [RFC3447] kulcs titkosítása  
-   **RSA-OAEP** -RSAES az optimális aszimmetrikus titkosítási kitöltés (OAEP) [RFC3447] használatával az a. 2.1. szakaszban az RFC 3447 által meghatározott alapértelmezett paraméterekkel. Ezek az alapértelmezett paraméterek az SHA-1 kivonatoló függvényét használják, és a MGF1 maszk generálási funkciója az SHA-1.  
-  **RSA-OAEP-256** – RSAES optimális aszimmetrikus titkosítással az sha-256 kivonatoló funkciójával, valamint a MGF1 maszk generálási FUNKCIÓJÁVAL az sha-256 használatával

### <a name="signverify"></a>ALÁÍRÁS/ELLENŐRZÉS

-   **PS256** -RSASSA-PSS az sha-256 és a MGF1 sha-256 használatával, az [RFC7518](https://tools.ietf.org/html/rfc7518)-ben leírtak szerint.
-   **PS384** -RSASSA-PSS az sha-384 és a MGF1 sha-384 használatával, az [RFC7518](https://tools.ietf.org/html/rfc7518)-ben leírtak szerint.
-   **PS512** -RSASSA-PSS az sha-512 és a MGF1 sha-512 használatával, az [RFC7518](https://tools.ietf.org/html/rfc7518)-ben leírtak szerint.
-   **RS256** -RSASSA-PKCS-V1_5 az SHA-256 használatával. Az alkalmazás által biztosított kivonatoló értéket az SHA-256 értékkel kell kiszámítani, és 32 bájt hosszúságú kell lennie.  
-   **RS384** -RSASSA-PKCS-V1_5 az SHA-384 használatával. Az alkalmazás által biztosított kivonatoló értéket az SHA-384 értékkel kell kiszámítani, és 48 bájt hosszúságú kell lennie.  
-   **RS512** -RSASSA-PKCS-V1_5 az SHA-512 használatával. Az alkalmazás által biztosított kivonatoló értéket az SHA-512 értékkel kell kiszámítani, és 64 bájt hosszúságú kell lennie.  
-   **RSNULL** – lásd: [RFC2437](https://tools.ietf.org/html/rfc2437), speciális használati eset, hogy bizonyos TLS-forgatókönyvek engedélyezve legyenek.  

##  <a name="symmetric-key-algorithms-managed-hsm-only"></a>Szimmetrikus kulcsú algoritmusok (csak felügyelt HSM esetén)
- **AES-kW** -AES-kulcs becsomagolása ([RFC3394](https://tools.ietf.org/html/rfc3394)).
- **AES-GCM** -AES titkosítás a Galois Counter mode-ban ([NIST SP 800-38d](https://csrc.nist.gov/publications/sp800))
- **AES-CBC** -AES titkosítás titkosított blokkos láncolási módban ([NIST SP 800-38a](https://csrc.nist.gov/publications/sp800))

> [!NOTE] 
> A jelenlegi AES-GCM implementáció és a megfelelő API-k kísérleti jellegűek. A megvalósítás és az API-k jelentősen megváltozhatnak a jövőbeli iterációk során. 

##  <a name="key-operations"></a>Legfontosabb műveletek

A felügyelt HSM a következő műveleteket támogatja a legfontosabb objektumokon:  

-   **Létrehozás**: lehetővé teszi, hogy az ügyfél Key Vaultban hozzon létre egy kulcsot. A kulcs értékét Key Vault és tárolja a rendszer, és az nem jelenik meg a-ügyfél számára. Az aszimmetrikus kulcsok a Key Vaultben hozhatók létre.  
-   **Importálás**: lehetővé teszi, hogy az ügyfél egy meglévő kulcsot importáljon Key Vaultba. Az aszimmetrikus kulcsok a JWK-konstrukción belül számos különböző csomagolási módszer használatával importálhatók Key Vaultba. 
-   **Frissítés**: lehetővé teszi, hogy az ügyfél megfelelő engedélyekkel módosítsa a korábban a Key Vault belül tárolt kulcs metaadatait (fő attribútumait).  
-   **Törlés**: lehetővé teszi, hogy az ügyfél megfelelő engedélyekkel törölje a kulcsot a Key Vaultból.  
-   **Lista**: lehetővé teszi az ügyfél számára az adott Key Vault összes kulcsának listázását.  
-   **Verziók listázása**: lehetővé teszi az ügyfél számára egy adott kulcs összes verziójának listázását egy adott Key Vaultban.  
-   **Get**: lehetővé teszi, hogy az ügyfél lekérje egy adott kulcs nyilvános részeit egy Key Vault.  
-   **Biztonsági mentés**: kulcs exportálása védett űrlapon.  
-   **Restore**: importál egy előzőleg biztonsági mentésre szolgáló kulcsot.  

További tudnivalókért tekintse [meg a Key Vault REST API dokumentációjában található főbb műveleteket](/rest/api/keyvault).  

Miután létrehozta a kulcsot a Key Vaultban, a következő titkosítási műveletek végezhetők el a kulcs használatával:  

-   **Aláírás és ellenőrzés**: szigorúan ez a művelet "aláírási kivonat" vagy "kivonat ellenőrzése", mivel Key Vault nem támogatja a tartalom kivonatolását az aláírás létrehozása részeként. Az alkalmazásoknak a helyileg aláírandó adatokat kell megadniuk, majd Key Vault aláírni a kivonatot. Az aláírt kivonatok ellenőrzése olyan alkalmazások kényelmi művelete, amelyek esetleg nem férnek hozzá a [nyilvános] kulcsfontosságú anyagokhoz. Az alkalmazások legjobb teljesítményének ELLENŐRZÉSéhez helyileg kell végrehajtani a műveleteket.  
-   **Kulcs titkosítása/becsomagolása**: Key Vault tárolt kulcs egy másik kulcs, jellemzően egy szimmetrikus tartalom titkosítási kulcsa (CEK) elleni védelemre használható. Ha a kulcs Key Vault aszimmetrikus, a rendszer a kulcs titkosítását használja. Például az RSA-OAEP és a WRAPKEY/UNWRAPKEY műveletek egyenértékűek a TITKOSÍTÁSsal/VISSZAFEJTÉSsel. Ha a kulcs Key Vault szimmetrikus, a rendszer a kulcs-körbefuttatást használja. Például: AES-KW. A WRAPKEY művelet olyan alkalmazások számára használható, amelyek esetleg nem férnek hozzá a [nyilvános] kulcshoz. Az alkalmazások legjobb teljesítményéhez helyileg kell végrehajtani a WRAPKEY műveleteket.  
-   **Titkosítás és visszafejtés**: Key Vaultban tárolt egyik kulcs használható egyetlen adatblokk titkosítására vagy visszafejtésére. A blokk méretét a kulcs típusa és a kiválasztott titkosítási algoritmus határozza meg. A titkosító művelet kényelmi célokat szolgál olyan alkalmazások számára, amelyek esetleg nem férnek hozzá a [nyilvános] kulcshoz. A legjobb teljesítmény érdekében a titkosítási műveleteket helyileg kell végrehajtani.  

Habár az aszimmetrikus kulcsokat használó WRAPKEY/UNWRAPKEY feleslegesnek tűnhet (mivel a művelet a TITKOSÍTÁShoz/VISSZAFEJTÉShez egyenértékű), a különböző műveletek használata fontos. A különbségtétel a művelet szemantikai és engedélyezési elkülönítését, valamint a szolgáltatás által támogatott más típusú kulcsok egységességét biztosítja.  

A Key Vault nem támogatja az EXPORTÁLÁSi műveleteket. Ha a kulcsot kiépítik a rendszerbe, azt nem lehet kibontani, vagy a kulcs lényegét módosítani. Előfordulhat azonban, hogy a Key Vault felhasználóinak más használati esetekhez, például a törlésük után is szükségük van a kulcsra. Ebben az esetben a biztonsági mentési és VISSZAÁLLÍTÁSi műveleteket a kulcs védett formában való exportálására/importálására használhatják. A biztonsági mentési művelet által létrehozott kulcsok nem használhatók Key Vaulton kívül. Azt is megteheti, hogy az IMPORTÁLÁSi művelet több Key Vault példányon is használható.  

A felhasználók a JWK objektum key_ops tulajdonságának használatával korlátozhatják a Key Vault által támogatott titkosítási műveleteket.  

A JWK-objektumokkal kapcsolatos további információkért lásd a [JSON webkulcs (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)című témakört.  

## <a name="key-attributes"></a>Fő attribútumok

A fő elemek mellett a következő attribútumok is megadhatók. JSON-kérelemben az attribútumok kulcsszó és a kapcsos zárójelek ({"}") szükségesek, még akkor is, ha nincsenek megadva attribútumok.  

- *engedélyezve*: logikai, nem kötelező, az alapértelmezett érték **true (igaz**). Megadja, hogy a kulcs engedélyezve van-e, és használható-e titkosítási műveletekhez. Az *engedélyezett* attribútum a *NBF* és az *exp* együttes használata esetén használatos. Ha a *NBF* és az *exp* közötti művelet történik, akkor csak akkor lesz *engedélyezve* , ha a beállítás értéke **true (igaz**). Az *NBF*  /  *exp* ablakon kívüli műveletek automatikusan le lesznek tiltva, kivéve az [adott körülmények között](#date-time-controlled-operations)meghatározott műveleti típusokat.
- *NBF*: IntDate, nem kötelező, alapértelmezés szerint most. A *NBF* (nem előtte) attribútum azt az időpontot határozza meg, ameddig a kulcs nem használható a titkosítási műveletekhez, kivéve az [adott körülmények között](#date-time-controlled-operations)meghatározott műveleti típusokat. A *NBF* attribútum feldolgozásához az aktuális dátumnak és időpontnak a *NBF* attribútumban felsorolt nem korábbi dátummal és időponttal kell rendelkeznie. Előfordulhat, hogy a Key Vault némi mozgásteret biztosít, amely általában legfeljebb néhány percet vesz igénybe, hogy az óra döntse. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.  
- *exp*: IntDate, nem kötelező, az alapértelmezett érték a "Forever". Az *exp* (lejárati idő) attribútum azt a lejárati időt határozza meg, amely után a kulcs nem használható a titkosítási művelethez, kivéve az [adott körülmények között](#date-time-controlled-operations)meghatározott műveleti típusokat. Az *exp* attribútum feldolgozásához szükséges, hogy az aktuális dátumnak és időpontnak az *exp* attribútumban felsorolt lejárati dátum/idő előtt kell lennie. Előfordulhat, hogy a Key Vault némi mozgásteret biztosít, amely jellemzően nem csupán néhány percet vesz igénybe, hogy az óra döntse. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.  

További írásvédett attribútumok is szerepelnek a legfontosabb attribútumokat tartalmazó válaszokban:  

- *Létrehozva*: IntDate, nem kötelező. A *létrehozott* attribútum azt jelzi, hogy mikor jött létre a kulcs ezen verziója. Az érték null értékű az attribútum hozzáadása előtt létrehozott kulcsok esetében. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.  
- *frissítve*: IntDate, nem kötelező. A *frissített* attribútum azt jelzi, hogy a kulcs ezen verziója frissítve lett-e. Az érték null értékű azon kulcsok esetében, amelyek utolsó frissítése az attribútum hozzáadása előtt történt. Az értéknek egy IntDate értéket tartalmazó számnak kell lennie.  

A IntDate és más adattípusokkal kapcsolatos további információkért lásd: [a kulcsok, a titkok és a tanúsítványok: [adattípusok](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Dátum-idő vezérelt műveletek

A *NBF* exp ablakon kívül még nem érvényes és lejárt kulcsok  /   is használhatók a **visszafejtéshez**, a **kicsomagoláshoz** és a műveletek **ellenőrzéséhez** (nem ad vissza 403, tiltott). A még nem érvényes állapot használatának indoklása az éles használat előtt a kulcs tesztelésének engedélyezése. A lejárt állapot használatának indoklása a helyreállítási műveletek engedélyezése a kulcs érvényessége után létrehozott adatokon. Emellett letilthatja a hozzáférést egy kulcshoz Key Vault szabályzatok használatával, vagy az *engedélyezett* kulcs attribútum **hamis értékre** való frissítésével.

Az adattípusokkal kapcsolatos további információkért lásd az [adattípusokat](../general/about-keys-secrets-certificates.md#data-types)ismertető témakört.

További információ a többi lehetséges attribútumról: JSON- [webkulcs (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

## <a name="key-tags"></a>Kulcs Címkék

További alkalmazásspecifikus metaadatokat is megadhat címkék formájában. A Key Vault legfeljebb 15 címkét támogat, amelyek mindegyike 256 karakterből és 256 karakterből állhat.  

> [!NOTE] 
> A címkék a hívó által olvashatók, ha rendelkeznek a *listával* , vagy *kapnak* engedélyt erre a kulcsra.

##  <a name="key-access-control"></a>Kulcshozzáférés-vezérlés

A Key Vault által felügyelt kulcsok hozzáférés-vezérlését a kulcsok tárolójának Key Vault szintjén kell megadnia. A kulcsok hozzáférés-vezérlési házirendje különbözik az azonos Key Vault található titkos kódok hozzáférés-vezérlési házirendjétől. A felhasználók létrehozhatnak egy vagy több tárolót a kulcsok tárolására, és szükségesek a forgatókönyvek megfelelő szegmentálásának és a kulcsok kezelésének fenntartásához. A kulcsok hozzáférés-vezérlése független a titkok hozzáférés-vezérlésével.  

A következő engedélyek adhatók meg felhasználónkénti vagy egyszerű szolgáltatás alapján a tárolóban található kulcsok hozzáférés-vezérlési bejegyzésében. Ezek az engedélyek szorosan tükrözik a Key objektumon engedélyezett műveleteket.  Egy egyszerű szolgáltatáshoz való hozzáférés biztosítása a Key vaultban egy egyszeri művelet, amely minden Azure-előfizetésnél azonos marad. Azt is megteheti, hogy tetszőleges számú tanúsítványt telepít. 

- A kulcskezelő műveletekhez szükséges engedélyek
  - *Letöltés*: olvassa el a kulcs nyilvános részét, valamint annak attribútumait
  - *lista*: a Key vaultban tárolt kulcsok kulcsait vagy verzióit sorolja fel.
  - *frissítés*: a kulcsok attribútumainak frissítése
  - *Létrehozás*: új kulcsok létrehozása
  - *Importálás*: kulcs importálása Key vaultba
  - *Törlés*: a kulcs objektum törlése
  - *helyreállítás*: törölt kulcs helyreállítása
  - *biztonsági mentés*: kulcs biztonsági mentése kulcstartóban
  - *visszaállítás*: a biztonsági másolatban szereplő kulcs visszaállítása a kulcstartóba

- Titkosítási műveletekhez szükséges engedélyek
  - *Visszafejtés*: a kulcs használatával egy bájtos sorozatot nem lehet védelemmel ellátni.
  - *titkosítás*: a kulcs használatával tetszőleges bájtos sorozatot biztosíthat.
  - *unwrapKey*: a kulcs használata a burkolt szimmetrikus kulcsok feloldásához
  - *wrapKey*: a kulcs használatával egy szimmetrikus kulcs védhető
  - *ellenőrzés*: a kulcs használata a kivonatoló ellenőrzéséhez  
  - *aláírás*: kivonatok aláírásához használja a kulcsot
    
- Jogosultsági szintű műveletek engedélyei
  - *kiürítés*: törölt kulcs kiürítése (végleges törlése)

A kulcsok használatával kapcsolatos további információkért tekintse meg [a Key Vault REST API-referenciával](/rest/api/keyvault)foglalkozó témakörben található főbb műveleteket. Az engedélyek létrehozásával kapcsolatos információkért lásd: tárolók [– Létrehozás vagy frissítés](/rest/api/keyvault/vaults/createorupdate) és tárolók [– frissítési hozzáférési szabályzat](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="next-steps"></a>Következő lépések
- [Tudnivalók a Key Vaultról](../general/overview.md)
- [A Managed HSM ismertetése](../managed-hsm/overview.md)
- [Információ a titkos kulcsokról](../secrets/about-secrets.md)
- [Információ a tanúsítványokról](../certificates/about-certificates.md)
- [Key Vault REST API áttekintése](../general/about-keys-secrets-certificates.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
