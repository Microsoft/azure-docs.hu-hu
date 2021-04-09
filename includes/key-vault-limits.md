---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: c2548b1669366564809ed2fde725cb3399922a29
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803602"
---
Azure Key Vault a szolgáltatás két erőforrástípust támogat: a tárolókat és a felügyelt HSM. A következő két szakasz ismerteti a szolgáltatásra vonatkozó korlátozásokat.

### <a name="resource-type-vault"></a>Erőforrás típusa: tároló

Ez a szakasz az erőforrástípus szolgáltatási korlátait mutatja be `vaults` .

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Legfontosabb tranzakciók (a maximálisan engedélyezett tranzakciók 10 másodpercen belül, tároló régiónként<sup>1</sup>):

|Kulcs típusa|HSM-kulcs<br>Kulcs létrehozása|HSM-kulcs<br>Minden egyéb tranzakció|Szoftver kulcsa<br>Kulcs létrehozása|Szoftver kulcsa<br>Minden egyéb tranzakció|
|:---|---:|---:|---:|---:|
|RSA 2 048 – bites|5|1,000|10|2000|
|RSA 3 072 – bites|5|250|10|500|
|RSA 4 096 – bites|5|125|10|250|
|ECC P-256|5|1,000|10|2000|
|ECC P-384|5|1,000|10|2000|
|ECC P-521|5|1,000|10|2000|
|ECC-SECP256K1|5|1,000|10|2000|
||||||

> [!NOTE]
> Az előző táblázatban azt láthatjuk, hogy az RSA 2 048 bites szoftverek kulcsai esetében 10 másodpercenként 2 000 GET tranzakció engedélyezett. Az RSA 2 048 bites HSM-kulcsok esetében 10 másodpercenként 1 000 GET tranzakció engedélyezett.
>
> A szabályozási küszöbértékek súlyozottak, és a kényszerítés az összegen alapul. Például az előző táblázatban látható módon, amikor az RSA HSM-kulcsok beolvasási műveleteit hajtja végre, a rendszer nyolc alkalommal drágább a 4 096 bites kulcsok és a 2 048 bites kulcsok összehasonlítására. Ennek oka, hogy 1000/125 = 8.
>
> Egy adott 10 másodperces intervallumban az Azure Key Vault-ügyfél csak a következő műveletek *egyikét* hajthatja végre, mielőtt megtapasztalja a `429` szabályozási HTTP-állapotkódot:
> - 2 000 RSA 2 048-bites szoftver – kulcs lekérése tranzakciók
> - 1 000 RSA 2 048-bit HSM – kulcs lekérése tranzakció
> - 125 RSA 4 096-bit HSM – kulcs lekérése tranzakció
> - 124 RSA 4 096-bites HSM-Key GET tranzakciók és 8 RSA 2 048-bites HSM – kulcs-lekérési tranzakciók

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Titkok, felügyelt Storage-fiókok kulcsai és a tár tranzakciói:

| Tranzakció típusa | A maximálisan engedélyezett tranzakciók 10 másodpercen belül, a tároló régiónként<sup>1</sup> |
| --- | --- |
| Minden tranzakció |2000 |

További információ a korlátozásoknak a határértékek túllépése esetén történő kezeléséről: [Azure Key Vault szabályozási útmutató](../articles/key-vault/general/overview-throttling.md).

<sup>1</sup> az összes tranzakciótípus esetében az előfizetésre vonatkozó korlát a Key Vault-korlátnál ötször van. Például HSM – az egyes előfizetésekhez tartozó egyéb tranzakciók 10 másodpercen belül 5 000 tranzakcióra korlátozódnak.

#### <a name="backup-keys-secrets-certificates"></a>Biztonsági mentési kulcsok, titkok, tanúsítványok

A Key Vault-objektumok (például titkos kulcsok, kulcsok vagy tanúsítványok) biztonsági mentésekor a biztonsági mentési művelet titkosított blobként tölti le az objektumot. Ezt a blobot nem lehet visszafejteni az Azure-on kívül. Ennek a blobnak a felhasználható adatainak lekéréséhez vissza kell állítania a blobot egy, az Azure-előfizetésben és az Azure-földrajzban található kulcstartóban

| Tranzakció típusa | A Key Vault-objektumok maximálisan engedélyezett verziói |
| --- | --- |
| Egyéni kulcs biztonsági mentése, titkos kód, certfiicate |500 |

> [!NOTE]
> Hiba történt a kulcs-, titkos vagy tanúsítvány-objektumnak a fentinél újabb verziókkal történő biztonsági mentésére tett kísérlet során. A kulcsok, titkos kódok vagy tanúsítványok korábbi verzióit nem lehet törölni. 

#### <a name="azure-private-link-integration"></a>Azure Private link-integráció

> [!NOTE]
> Az előfizetések által engedélyezett privát végpontokkal rendelkező kulcstartók száma állítható korlát. Az alább látható korlát az alapértelmezett korlát. Ha korlátozni szeretné a szolgáltatás korlátozását, küldjön e-mailt a címre akv-privatelink@microsoft.com . Ezeket a kéréseket eseti alapon kell jóváhagyni.

| Erőforrás | Korlát |
| -------- | -----:|
| Privát végpontok a Key vaultban | 64 |
| Privát végpontok által előfizetett kulcstartók | 400 |

### <a name="resource-type-managed-hsm-preview"></a>Erőforrás típusa: felügyelt HSM (előzetes verzió)

Ez a szakasz az erőforrástípus szolgáltatási korlátait mutatja be `managed HSM` .

#### <a name="object-limits"></a>Objektum korlátai

|Elem|Korlátok|
|----|------:|
HSM-példányok száma/előfizetés régiónként|1 (az előzetes verzió ideje alatt)
Kulcsok száma HSM-készletben|5000
Verziók száma kulcs szerint|100
Egyéni szerepkör-definíciók száma HSM-ben|50
Szerepkör-hozzárendelések száma a HSM-hatókörben|50
Szerepkör-hozzárendelések száma az egyes kulcsok hatókörében|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Felügyeleti műveletek tranzakciós korlátai (műveletek másodpercenkénti száma HSM-példányon)
|Művelet |Műveletek másodpercenkénti száma|
|----|------:|
Minden RBAC művelet<br/>(tartalmazza az összes szifilisz-műveletet a szerepkör-definíciók és a szerepkör-hozzárendelések esetében)|5
Teljes HSM biztonsági mentés/visszaállítás<br/>(csak egy egyidejű biztonsági mentési vagy visszaállítási művelet támogatott a HSM-példányok esetében)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Titkosítási műveletek tranzakciós korlátai (műveletek másodpercenkénti száma HSM-példányon)

- Mindegyik felügyelt HSM-példány 3 elosztott terhelésű HSM partíciót alkot. Az átviteli sebesség korlátai az egyes partíciók számára lefoglalt mögöttes hardver-kapacitás függvényei. Az alábbi táblázatokban a maximális átviteli sebesség látható legalább egy rendelkezésre álló partícióval. A tényleges átviteli sebesség akár 3x-nál nagyobb is lehet, ha mind a 3 partíció elérhető.
- Az átviteli sebesség korlátai azt feltételezik, hogy egyetlen kulcsot használ a maximális átviteli sebesség eléréséhez. Ha például egyetlen RSA-2048 kulcsot használ, a maximális átviteli sebesség a 1100-es aláírási művelet lesz. Ha a 1100 különböző kulcsokat használ másodpercenként 1 tranzakcióval, akkor nem lesznek képesek ugyanazt az átviteli sebességet elérni.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>RSA-kulcs műveletei (műveletek másodpercenkénti száma/HSM-példány)

|Művelet|2048 bites|3072 bites|4096 bites|
|--|--:|--:|--:|
Kulcs létrehozása|1| 1| 1
Kulcs törlése (Soft-delete)|10|10|10 
Kulcs kiürítése|10|10|10 
Biztonsági mentési kulcs|10|10|10 
Kulcs visszaállítása|10|10|10 
Legfontosabb információk beolvasása|1100|1100|1100
Titkosítás|10000|10000|6000
Visszafejtés|1100|360|160
Wrap|10000|10000|6000
Kicsomagolása|1100|360|160
Előjel|1100|360|160
Ellenőrzés|10000|10000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>EK-kulcsú műveletek (műveletek másodpercenkénti száma/HSM-példány)

Ez a táblázat a műveletek másodpercenkénti számát írja le az egyes görbék típusaihoz.

|Művelet|P-256|P – 256K|P-384|P-521|
|---|---:|---:|---:|---:|
Kulcs létrehozása| 1| 1| 1| 1
Kulcs törlése (Soft-delete)|10|10|10|10
Kulcs kiürítése|10|10|10|10
Biztonsági mentési kulcs|10|10|10|10
Kulcs visszaállítása|10|10|10|10
Legfontosabb információk beolvasása|1100|1100|1100|1100
Előjel|260|260|165|56
Ellenőrzés|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>AES-kulcsok műveletei (műveletek másodpercenkénti száma/HSM-példány)
- A titkosítási és visszafejtési műveletek feltételezik, hogy a 4KB-csomagok mérete.
- A titkosításra és visszafejtésre vonatkozó átviteli korlátok az AES-CBC és az AES-GCM algoritmusokra vonatkoznak.
- A becsomagolás/kicsomagolás átviteli sebességének korlátai az AES-KW algoritmusra vonatkoznak.

|Művelet|128 bites|192 bites|256 bites|
|--|--:|--:|--:|
Kulcs létrehozása|1| 1| 1
Kulcs törlése (Soft-delete)|10|10|10
Kulcs kiürítése|10|10|10
Biztonsági mentési kulcs|10|10|10
Kulcs visszaállítása|10|10|10
Legfontosabb információk beolvasása|1100|1100|1100
Titkosítás|8000|8000 |8000 
Visszafejtés|8000|8000|8000
Wrap|9000|9000|9000
Kicsomagolása|9000|9000|9000

