---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: 9ecfcff00e6f44f5c739513c063baaa3fa02a3db
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753296"
---
Azure Key Vault szolgáltatás két erőforrástípust támogat: a tárolókat és a felügyelt HSM-eket. A következő két szakasz az egyes szolgáltatásokra vonatkozó korlátozásokat ismerteti.

### <a name="resource-type-vault"></a>Erőforrás típusa: tároló

Ez a szakasz a erőforrástípus szolgáltatási korlátait `vaults` ismerteti.

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Kulcstranzakciók (a tranzakciók maximális száma 10 másodpercben, tárolónként régiónként<sup>1):</sup>

|Kulcs típusa|HSM-kulcs<br>CREATE kulcs|HSM-kulcs<br>Minden egyéb tranzakció|Szoftverkulcs<br>CREATE kulcs|Szoftverkulcs<br>Minden egyéb tranzakció|
|:---|---:|---:|---:|---:|
|RSA 2048 bites|5|1,000|10|2000|
|RSA 3072 bites|5|250|10|500|
|RSA 4096 bites|5|125|10|250|
|ECC P-256|5|1,000|10|2000|
|ECC P-384|5|1,000|10|2000|
|ECC P-521|5|1,000|10|2000|
|ECC SECP256K1|5|1,000|10|2000|
||||||

> [!NOTE]
> Az előző táblázatban látható, hogy az RSA 2048 bites szoftverkulcsok esetében 2000 GET tranzakció engedélyezett 10 másodpercenként. Az RSA 2048 bites HSM-kulcsok esetében 10 másodpercenként 1000 GET tranzakció engedélyezett.
>
> A szabályozási küszöbértékek súlyozottak, és a kényszerítés az összegükön van. Az előző táblázatban látható módon például, ha GET műveleteket végez az RSA HSM-kulcsokon, nyolcszor drágább a 4096 bites kulcsok használata a 2048 bites kulcsokhoz képest. Ennek az az oka, hogy 1000/125 = 8.
>
> Egy adott 10 másodperces időszakban a Azure Key Vault-ügyfél csak az alábbi műveletek egyikét tudja megtenni, mielőtt szabályozási  `429` HTTP-állapotkódot kap:
> - 2000 RSA 2048 bites szoftverkulcs GET-tranzakciók
> - 1000 RSA 2048 bites HSM-kulcs GET-tranzakciók
> - 125 RSA 4096 bites HSM-kulcs GET-tranzakciók
> - 124 RSA 4096 bites HSM-kulcs GET-tranzakciók és 8 RSA 2048 bites HSM-kulcs GET-tranzakciók

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Titkos kulcsok, felügyelt tárfiókkulcsok és tárolótranzakciók:

| Tranzakciók típusa | Tranzakciók maximális száma 10 másodpercben, tárolónként és régiónként<sup>1</sup> |
| --- | --- |
| Minden tranzakció |2000 |

További információ a szabályozás kezelésről ezen korlátok túllépése esetén: Azure Key Vault [útmutatás.](../articles/key-vault/general/overview-throttling.md)

<sup>1</sup> Minden tranzakciótípus előfizetésre vonatkozó korlátja kulcstartó-korlátonként ötször. Az előfizetésenkénti HSM-egyéb tranzakciók például legfeljebb 5000 tranzakciót korlátoznak előfizetésenként 10 másodperc alatt.

#### <a name="backup-keys-secrets-certificates"></a>Biztonsági másolati kulcsok, titkos kulcsok, tanúsítványok

Amikor biztonsági másolatot készít egy Key Vault-objektumról, például egy titkos kulcsról, kulcsról vagy tanúsítványról, a biztonsági mentési művelet titkosított blobként fogja letölteni az objektumot. Ezt a blobot nem lehet visszafejteni az Azure-n kívül. Ha ebből a blobból használható adatokat keres, vissza kell állítania a blobot egy kulcstartóba ugyanabban az Azure-előfizetésben és azure földrajzi helyen

| Tranzakciók típusa | A Key Vault-objektumok engedélyezett maximális verziói |
| --- | --- |
| Biztonsági másolat készítése az egyes kulcsokról, titkos kulcsokról és tanúsítványokról |500 |

> [!NOTE]
> Ha a korlátnál több verziójú kulcsot, titkos kulcsot vagy tanúsítványobjektumot próbál meg biztonsági másolatot készíteni, az hibát eredményez. Kulcs, titkos kulcs vagy tanúsítvány korábbi verziói nem törölhetők. 

#### <a name="azure-private-link-integration"></a>Azure Private Link integráció

> [!NOTE]
> Az előfizetésenként engedélyezett privát végpontokkal rendelkező kulcstartók száma módosítható korlát. Az alábbi korlát az alapértelmezett korlát. Ha korlátemelést szeretne kérni a szolgáltatáshoz, hozzon létre egy támogatási kérést, amelyet eseti alapon értékelünk ki.

| Erőforrás | Korlát |
| -------- | -----:|
| Privát végpontok kulcstartónként | 64 |
| Titkos végpontokkal és előfizetésenként elérhető kulcstartók | 400 |

### <a name="resource-type-managed-hsm-preview"></a>Erőforrás típusa: Managed HSM (előzetes verzió)

Ez a szakasz a erőforrástípus szolgáltatási korlátait `managed HSM` ismerteti.

#### <a name="object-limits"></a>Objektumkorlátok

|Elem|Korlátok|
|----|------:|
HSM-példányok száma előfizetésenként és régiónként|1 (az előzetes verzióban)
Kulcsok száma HSM-készletenként|5000
Verziók száma kulcsonként|100
Egyéni szerepkör-definíciók száma HSM-enként|50
Szerepkör-hozzárendelések száma HSM-hatókörben|50
Szerepkör-hozzárendelések száma az egyes kulcshatókörökben|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>A felügyeleti műveletek tranzakciós korlátai (a műveletek száma másodpercenként HSM-példányonként)
|Művelet |Műveletek száma másodpercenként|
|----|------:|
Minden RBAC-művelet<br/>(tartalmazza az összes CRUD-műveletet a szerepkör-definíciókhoz és a szerepkör-hozzárendeléshez)|5
HSM teljes biztonsági mentése/visszaállítása<br/>(A HSM-példányonként csak egy egyidejű biztonsági mentési vagy visszaállítási művelet támogatott)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>A titkosítási műveletek tranzakciós korlátai (a műveletek száma másodpercenként HSM-példányonként)

- Minden felügyelt HSM-példány 3 elosztott terhelésű HSM-partíciót tartalmaz. Az átviteli sebességkorlátok az egyes partíciókhoz lefoglalt mögöttes hardverkapacitás függvényei. Az alábbi táblázatokban a maximális átviteli sebesség látható, legalább egy elérhető partícióval. A tényleges átviteli sebesség akár 3x magasabb is lehet, ha mind a 3 partíció elérhető.
- A feljegyzett átviteli sebességkorlátok azt feltételezik, hogy a rendszer egyetlen kulcsot használ a maximális átviteli sebesség eléréséhez. Ha például egyetlen RSA-2048 kulcsot használ, a maximális átviteli sebesség 1100 aláírási művelet lesz. Ha 1100 különböző kulcsot használ másodpercenként 1 tranzakcióval, nem fogják tudni ugyanazt az átviteli sebességet elérni.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>RSA-kulcsműveletek (műveletek száma másodpercenként HSM-példányonként)

|Művelet|2048 bites|3072 bites|4096 bites|
|--|--:|--:|--:|
Kulcs létrehozása|1| 1| 1
Kulcs törlése (soft-delete)|10|10|10 
Kulcs végleges végleges kiürítése|10|10|10 
Biztonsági mentési kulcs|10|10|10 
Kulcs visszaállítása|10|10|10 
Kulcsinformációk lekérte|1100|1100|1100
Titkosítás|10000|10000|6000
Visszafejtés|1100|360|160
Betakar|10000|10000|6000
Kiírás|1100|360|160
Előjel|1100|360|160
Ellenőrzés|10000|10000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>EC-kulcsműveletek (műveletek száma másodpercenként HSM-példányonként)

Ez a táblázat az egyes görbetípusonkénti műveletek másodpercenkénti számát ismerteti.

|Művelet|P-256|P-256K|P-384|P-521|
|---|---:|---:|---:|---:|
Kulcs létrehozása| 1| 1| 1| 1
Kulcs törlése (soft-delete)|10|10|10|10
Kulcs végleges végleges kiürítése|10|10|10|10
Biztonsági mentési kulcs|10|10|10|10
Kulcs visszaállítása|10|10|10|10
Kulcsinformációk lekérte|1100|1100|1100|1100
Előjel|260|260|165|56
Ellenőrzés|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>AES-kulcsműveletek (műveletek száma másodpercenként HSM-példányonként)
- A titkosítási és visszafejtési műveletek 4 KB-os csomagméretet feltételeznek.
- A Titkosítás/Visszafejtés átviteli sebességkorlátai az AES-CBC és az AES-GCM algoritmusra vonatkoznak.
- A Wrap/Unwrap átviteli sebességkorlátai az AES-WRAP algoritmusra vonatkoznak.

|Művelet|128 bites|192 bites|256 bites|
|--|--:|--:|--:|
Kulcs létrehozása|1| 1| 1
Kulcs törlése (soft-delete)|10|10|10
Kulcs végleges végleges kiürítésén|10|10|10
Biztonsági mentési kulcs|10|10|10
Kulcs visszaállítása|10|10|10
Kulcsinformációk lekérte|1100|1100|1100
Titkosítás|8000|8000 |8000 
Visszafejtés|8000|8000|8000
Betakar|9000|9000|9000
Kicsomagzás|9000|9000|9000

