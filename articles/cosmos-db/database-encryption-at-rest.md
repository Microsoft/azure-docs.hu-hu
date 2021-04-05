---
title: Titkosítás inaktív állapotban Azure Cosmos DB
description: Ismerje meg, hogyan biztosítja a Azure Cosmos DB az inaktív adatok titkosítását és megvalósítását.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: seodec18
ms.openlocfilehash: abe4bb3a53e3177857cb8041fd255ec916985054
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93089865"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Adatok titkosítása az Azure Cosmos DB-ben 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A inaktív adatok titkosítása olyan kifejezés, amely általában a nem felejtő tárolóeszközökön, például SSD-meghajtókon és merevlemez-meghajtókon (HDD-k) lévő adatok titkosítására utal. A Cosmos DB az elsődleges adatbázisait az SSD-meghajtókon tárolja. Az adathordozó mellékleteit és biztonsági másolatait az Azure Blob Storage tárolja, amelyet általában a HDD-k készítenek. A inaktív adatok titkosítása Cosmos DB esetén az összes adatbázisa, adathordozó-melléklete és biztonsági mentése titkosítva van. Az adatok most már titkosítva vannak az átvitelben (a hálózaton keresztül) és a nyugalmi állapotban (nem felejtő tárolóban), így teljes körű titkosítást biztosítanak.

A Azure Cosmos DB nagyon könnyen használható. Mivel a Azure Cosmos DBban tárolt összes felhasználói adatok titkosítva vannak, és a átvitel során nem kell semmilyen műveletet végrehajtania. A másik lehetőség az, hogy a titkosítás nyugalmi állapotban van, alapértelmezés szerint "on". Nincsenek olyan vezérlők, amelyekkel ki-vagy bekapcsolhatja. A Azure Cosmos DB AES-256 titkosítást használ minden olyan régióban, ahol a fiók fut. Ezt a funkciót a [rendelkezésre állási és teljesítménybeli SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)-kat továbbra is teljesítjük. Az Azure Cosmos-fiókban tárolt adatai automatikusan és zökkenőmentesen titkosítva vannak a Microsoft által kezelt kulcsokkal (szolgáltatás által felügyelt kulcsok). Lehetőség van arra is, hogy egy második titkosítási réteget adjon hozzá a saját kulcsaihoz az [ügyfél által felügyelt kulcsok](how-to-setup-cmk.md) című cikkben leírtak szerint.

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Inaktív titkosítás megvalósítása Azure Cosmos DB

A REST-alapú titkosítás számos biztonsági technológiával valósul meg, többek között a biztonságos kulcsú tárolási rendszerek, a titkosított hálózatok és a titkosítási API-k használatával. Az adatvisszafejtési és-feldolgozási rendszerek a kulcsokat kezelő rendszerekkel kommunikálnak. Az ábrán látható, hogy a titkosított adatok tárolása és a kulcsok kezelése elkülönül. 

:::image type="content" source="./media/database-encryption-at-rest/design-diagram.png" alt-text="Tervezési diagram" border="false":::

A felhasználói kérések alapszintű folyamatábrája a következő:
- A felhasználói adatbázis fiókja készen áll, és a tárolási kulcsok beolvasása a kezelési szolgáltatás erőforrás-szolgáltatójának kérelme alapján történik.
- A felhasználó HTTPS/biztonságos átvitelen keresztül hoz létre Cosmos DB kapcsolatát. (Az SDK-k elvonták a részleteket.)
- A felhasználó egy JSON-dokumentumot küld a korábban létrehozott biztonságos kapcsolaton keresztül.
- A JSON-dokumentum indexelve van, kivéve, ha a felhasználó kikapcsolta az indexelést.
- A JSON-dokumentumot és az indexet is a biztonságos tárolóba írja a rendszer.
- Időnként az adatok beolvasása a biztonságos tárolóból történik, és biztonsági másolat készül az Azure-beli titkosított blob-tárolóba.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>K: Mennyibe kerül az Azure Storage szolgáltatás, ha Storage Service Encryption engedélyezve van?
A: nincs további díj.

### <a name="q-who-manages-the-encryption-keys"></a>K: kik kezelik a titkosítási kulcsokat?
A: a kulcsokat a Microsoft felügyeli.

### <a name="q-how-often-are-encryption-keys-rotated"></a>K: milyen gyakran forognak a titkosítási kulcsok?
A: a Microsoft a titkosítási kulcs elforgatására vonatkozó belső irányelveket tartalmaz, amelyek Cosmos DB következnek. Az adott irányelvek nincsenek közzétéve. A Microsoft közzéteszi a [biztonsági fejlesztési életciklust (SDL)](https://www.microsoft.com/sdl/default.aspx), amelyet a belső útmutatások egy részhalmazának tekintenek, és hasznos gyakorlati tanácsokat tartalmaz a fejlesztők számára.

### <a name="q-can-i-use-my-own-encryption-keys"></a>K: használhatom a saját titkosítási kulcsokat?
A: igen, ez a funkció mostantól elérhető az új Azure Cosmos DB fiókokhoz, és ezt a fiók létrehozásakor kell elvégezni. További információért olvassa el az [ügyfél által felügyelt kulcsok](./how-to-setup-cmk.md) című dokumentumot.

### <a name="q-what-regions-have-encryption-turned-on"></a>K: mely régiókban van engedélyezve a titkosítás?
A: minden Azure Cosmos DB régióban van a titkosítás bekapcsolva az összes felhasználói adattal.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>K: a titkosítás befolyásolja a teljesítmény késését és az átviteli sebességet?
A: a teljesítmény SLA-ban nem befolyásolja a teljesítményt, és nem változik meg, hogy az összes meglévő és új fiók esetében engedélyezve van a titkosítás a REST-ben. A legújabb garanciák megtekintéséhez további információt az [SLA Cosmos db](https://azure.microsoft.com/support/legal/sla/cosmos-db) oldalon talál.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>K: a helyi emulátor támogatja a titkosítást a REST-ben?
A: az emulátor egy önálló fejlesztési/tesztelési eszköz, és nem használja a felügyelt Cosmos DB szolgáltatás által használt kulcskezelő szolgáltatásokat. Javasoljuk, hogy engedélyezze a BitLockert olyan meghajtókon, amelyeken érzékeny Emulator-tesztelési adatokat tárol. Az [emulátor támogatja az alapértelmezett adatkönyvtár módosítását](local-emulator.md) , valamint egy jól ismert hely használatát.

## <a name="next-steps"></a>Következő lépések

* Kiválaszthatja, hogy egy második titkosítási réteget is hozzáadhat a saját kulcsaihoz, és további információt az [ügyfél által felügyelt kulcsok](how-to-setup-cmk.md) című cikkben talál.
* A Cosmos DB biztonság és a legújabb Újdonságok áttekintését lásd: az [Azure Cosmos Database biztonsága](database-security.md).
* További információ a Microsoft-tanúsítványokról: [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/).