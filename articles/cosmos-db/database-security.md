---
title: Adatbázis biztonsága – Azure Cosmos DB
description: Ismerje meg, hogyan biztosítja az Azure Cosmos DB az adatbázis védelmét és az adatbiztonságot az adatai számára.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: mjbrown
ms.openlocfilehash: 19b4c8466e88159839ce1f43a5ba282b1bb3ec9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94636926"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Biztonság az Azure Cosmos DB-ben – Áttekintés
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Ez a cikk az Azure Cosmos DB által az adatbázis feltörésének megakadályozása, észlelése és kezelése érdekében biztosított adatbázis-biztonsági ajánlott eljárásokat és fő funkciókat írja le.

## <a name="whats-new-in-azure-cosmos-db-security"></a>A Azure Cosmos DB biztonság újdonságai

A REST-alapú titkosítás mostantól elérhető az összes Azure-régióban Azure Cosmos DB tárolt dokumentumok és biztonsági másolatok számára. A REST-titkosítást a rendszer automatikusan alkalmazza az új és a meglévő ügyfelekre is ezekben a régiókban. Semmit nem kell konfigurálnia; és ugyanolyan nagy késést, teljesítményt, rendelkezésre állást és funkcionalitást kap, mint korábban, hogy az adatok biztonsága és biztonsága biztonságos legyen a REST-titkosítással.

## <a name="how-do-i-secure-my-database"></a>Az adatbázis biztonságának Hogyan

Az adatbiztonság az Ön, az ügyfél és az adatbázis-szolgáltató között megosztott felelősség. Attól függően, hogy melyik adatbázis-szolgáltatót választja, az Ön által viselt felelősség mértéke változhat. Ha helyszíni megoldást választ, meg kell adnia mindent a végpontok elleni védelemtől a hardver fizikai biztonsága érdekében – ami nem egyszerű feladat. Ha úgy dönt, hogy egy olyan, a felhőben tárolt, a Azure Cosmos DBt is kiválasztó, a területe jelentősen csökken Az alábbi, a Microsoft által a [felhőalapú számítástechnikai feladatokkal kapcsolatos közös felelősséggel](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) foglalkozó rendszerkép azt mutatja be, hogyan csökken a felelőssége egy olyan Pásti-szolgáltatóval, mint a Azure Cosmos db.

:::image type="content" source="./media/database-security/nosql-database-security-responsibilities.png" alt-text="Az ügyfél-és adatbázis-szolgáltató feladatai":::

Az előző ábrán a magas szintű Felhőbeli biztonsági összetevők láthatók, de milyen elemek szükségesek az adatbázis-megoldáshoz? És hogyan lehet összehasonlítani egymással a megoldásokat?

A következő, az adatbázis-rendszerek összehasonlítására szolgáló követelmények listáját ajánljuk:

- Hálózati biztonság és tűzfal beállításai
- Felhasználói hitelesítés és részletes felhasználói vezérlők
- Az adatreplikálás globálisan a regionális hibákra
- Az egyik adatközpontból a másikba való feladatátvétel lehetősége
- Helyi adatreplikálás egy adatközponton belül
- Automatikus biztonsági mentések
- Törölt adatok visszaállítása biztonsági másolatokból
- Bizalmas adatok védelme és elkülönítése
- Támadások figyelése
- Válaszadás a támadásokra
- Az adatkezelési korlátozások betartásának lehetősége geo-kerítésre
- A védett adatközpontokban található kiszolgálók fizikai védelme
- Tanúsítványok

És bár nyilvánvalónak tűnhet, a közelmúltban [nagy léptékű adatbázis-megsértések](https://thehackernews.com/2017/01/mongodb-database-security.html) emlékeztetnek minket az alábbi követelmények egyszerű, de kritikus jelentőségére:

- A javított kiszolgálók naprakészen tartanak
- HTTPS alapértelmezett/TLS titkosítással
- Erős jelszavakkal rendelkező rendszergazdai fiókok

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Hogyan Azure Cosmos DB az adatbázis biztonsága

Nézzünk vissza az előző listára – hány biztonsági követelményt Azure Cosmos DB biztosítanak? Minden egy.

Ismerkedjen meg minden részlettel.

|Biztonsági követelmény|Azure Cosmos DB biztonsági megközelítése|
|---|---|
|Hálózati biztonság|Az adatbázis biztonságossá tételéhez az IP-tűzfal használata az első védelmi réteg. Azure Cosmos DB támogatja a házirend által vezérelt IP-alapú hozzáférés-vezérlést a bejövő tűzfalak támogatásához. Az IP-alapú hozzáférés-vezérlések a hagyományos adatbázis-rendszerek által használt tűzfalszabályok hasonlóak, de kibővülnek, így az Azure Cosmos adatbázis-fiók csak jóváhagyott gépekből vagy felhőalapú szolgáltatásokból érhető el. További információt [Azure Cosmos db tűzfal támogatási](how-to-configure-firewall.md) cikkében talál.<br><br>Azure Cosmos DB lehetővé teszi egy adott IP-cím (168.61.48.0), egy IP-címtartomány (168.61.48.0/8), valamint az IP-címek és tartományok kombinációjának engedélyezését. <br><br>Azure Cosmos DB letiltja az ezen az engedélyezési listán kívüli gépekről származó összes kérelmet. A jóváhagyott gépektől és a Cloud servicestől érkező kéréseket a hitelesítési folyamatnak kell elvégeznie, hogy hozzáférés-vezérlést kapjon az erőforrásokhoz.<br><br> A [virtuális hálózati szolgáltatás címkéi](../virtual-network/service-tags-overview.md) a hálózati elkülönítés eléréséhez és a Azure Cosmos db-erőforrások általános internetről való ellátásához használhatók. A szolgáltatási címkéket adott IP-címek helyett használhatja biztonsági szabályok létrehozásakor. A szolgáltatási címke nevének (például AzureCosmosDB) megadásával a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát.|
|Engedélyezés|A Azure Cosmos DB kivonat-alapú üzenethitelesítő kódot (HMAC) használ a hitelesítéshez. <br><br>Minden kérelmet a titkos fiók kulcsa alapján kell kivonattal ellátni, és a rendszer a következő Base-64 kódolású kivonatot küldi el a Azure Cosmos DB összes hívásával. A kérelem érvényesítéséhez a Azure Cosmos DB szolgáltatás a helyes titkos kulcsot és tulajdonságokat használja a kivonat létrehozásához, majd összehasonlítja az értéket a kérelemben szereplővel. Ha a két érték egyezik, a művelet sikeresen engedélyezve lesz, és a rendszer feldolgozza a kérést, ellenkező esetben a rendszer elutasítja a kérelmet.<br><br>Használhat egy [elsődleges kulcsot](#primary-keys)vagy egy [erőforrás-tokent](secure-access-to-data.md#resource-tokens) , amely lehetővé teszi a részletes hozzáférést egy erőforráshoz, például egy dokumentumhoz.<br><br>További információ a [Azure Cosmos db erőforrásaihoz való hozzáférés biztonságossá tételéről](secure-access-to-data.md).|
|Felhasználók és engedélyek|A fiók elsődleges kulcsának használatával létrehozhat egy adatbázison felhasználói erőforrásokat és engedélyek erőforrásait. Az erőforrás-token egy adatbázisban lévő engedélyhez van társítva, és meghatározza, hogy a felhasználó rendelkezik-e hozzáféréssel (írható-olvasható, írásvédett vagy nincs hozzáférés) az adatbázisban lévő alkalmazás-erőforráshoz. Az alkalmazás erőforrásai közé tartozik a tároló, a dokumentumok, a mellékletek, a tárolt eljárások, az eseményindítók és a UDF. Az erőforrás-tokent a rendszer a hitelesítés során használja az erőforráshoz való hozzáférés megadásához vagy megtagadásához.<br><br>További információ a [Azure Cosmos db erőforrásaihoz való hozzáférés biztonságossá tételéről](secure-access-to-data.md).|
|Active Directory-integráció (Azure RBAC)| Az Azure Portal hozzáférés-vezérlés (IAM) használatával is megadhatja vagy korlátozhatja a Cosmos-fiók, az adatbázis, a tároló és az ajánlatok elérését. A IAM a szerepköralapú hozzáférés-vezérlést és a Active Directory integrációját biztosítja. Használhat beépített szerepköröket vagy egyéni szerepköröket egyéni felhasználókhoz és csoportokhoz. További információ: [Active Directory Integration](role-based-access-control.md) article.|
|Globális replikálás|A Azure Cosmos DB kulcsrakész globális terjesztést kínál, amely lehetővé teszi az adatai replikálását az Azure egyik globális adatközpontja számára egy gombnyomással. A globális replikáció lehetővé teszi, hogy globálisan méretezhető legyen, és alacsony késésű hozzáférést biztosítson az adataihoz világszerte.<br><br>A biztonság kontextusában a globális replikáció biztosítja a regionális hibákkal szembeni adatvédelmet.<br><br>További információ: [Globális adatterjesztés](distribute-data-globally.md).|
|Régiónkénti feladatátvétel|Ha több adatközpontban replikálta az adatait, Azure Cosmos DB automatikusan legörgeti a műveleteket, ha a regionális adatközpont offline állapotba kerül. Létrehozhatja a feladatátvételi régiók rangsorolt listáját azon régiók használatával, amelyekben az adatait replikálja. <br><br>További információ a [regionális feladatátvételekről Azure Cosmos DBban](high-availability.md).|
|Helyi replikálás|A Azure Cosmos DB még egy adatközponton belül is automatikusan replikálja az adatok magas rendelkezésre állását, így biztosítva a [konzisztencia-szintek](consistency-levels.md)választékát. Ez a replikálás garantálja a 99,99%-os [rendelkezésre állási SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) -t az összes egyrégiós fiókhoz és az összes többrégiós fiókhoz, amely nyugodt konzisztenciát és 99,999%-os olvasási rendelkezésre állást biztosít az összes több régiós adatbázis|
|Automatizált online biztonsági másolatok|Az Azure Cosmos-adatbázisok rendszeresen készülnek, és egy geo redundáns tárolóban tárolódnak. <br><br>További információ: [automatikus online biztonsági mentés és visszaállítás Azure Cosmos DBsal](online-backup-and-restore.md).|
|Törölt adatértékek visszaállítása|Az automatizált online biztonsági másolatok segítségével helyreállíthatja az esetlegesen véletlenül törölt adatokat az eseményt követő 30 napon belül. <br><br>További információ: [automatikus online biztonsági mentés és visszaállítás Azure Cosmos db](online-backup-and-restore.md)|
|Bizalmas adatok védelme és elkülönítése|Az Újdonságok listájában felsorolt régiók összes adatai mostantól titkosítva van.<br><br>A személyes adatok és az egyéb bizalmas adatok elkülöníthetők bizonyos tárolók és írási és olvasási hozzáférés esetén, vagy csak olvasási hozzáféréssel rendelkezhetnek bizonyos felhasználóknak.|
|Támadások figyelése|A naplózási [és a tevékenységi naplók](./monitor-cosmos-db.md)használatával figyelheti a fiókját a normál és rendellenes tevékenységekhez. Megtekintheti, hogy milyen műveleteket hajtottak végre az erőforrásokon, ki kezdeményezte a műveletet, a művelet bekövetkeztekor, a művelet állapotát, és sokkal többet, ahogy az a táblázatot követő képernyőképen is látható.|
|Válaszadás a támadásokra|Miután felvette a kapcsolatot az Azure-támogatással, hogy jelentsen egy lehetséges támadást, egy 5 lépésből álló incidens-válasz indult el. Az 5 lépésből álló folyamat célja, hogy a probléma észlelése és a vizsgálat elindítása után a lehető leggyorsabban visszaállítsa a normál szolgáltatás biztonsági és üzemeltetési műveleteit.<br><br>További információ: [Microsoft Azure biztonsági válasz a felhőben](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).|
|Földrajzi kerítés|Azure Cosmos DB biztosítja az adatkezelést a szuverén régiók számára (például Németország, Kína, US Gov).|
|Védett létesítmények|A Azure Cosmos DBban tárolt adategységeket az Azure védett adatközpontjai SSD-k tárolják.<br><br>További információ a [Microsoft globális adatközpontokban](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|HTTPS/SSL/TLS titkosítás|A Azure Cosmos DB összes kapcsolata támogatja a HTTPS protokollt. A Azure Cosmos DB a TLS 1,2-et is támogatja.<br>Egy minimális TLS-verzió kiszolgálóoldali kikényszeríthető. Ehhez vegye fel a kapcsolatot [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) .|
|Titkosítás inaktív állapotban|A Azure Cosmos DB tárolt összes adatforgalom inaktív állapotban van. További információ a [Azure Cosmos db inaktív titkosításról](./database-encryption-at-rest.md)|
|Javított kiszolgálók|Felügyelt adatbázisként a Azure Cosmos DB szükségtelenné teszi a kiszolgálók felügyeletét és javítását, ami automatikusan történik.|
|Erős jelszavakkal rendelkező rendszergazdai fiókok|Nehéz elhinni, hogy még meg is kell említeni ezt a követelményt, de a versenytársaktól eltérően nem lehet rendszergazdai fiókja, amely nem rendelkezik jelszóval a Azure Cosmos DBban.<br><br> A TLS-vel és a HMAC-alapú titkos hitelesítéssel történő biztonság alapértelmezés szerint besütött.|
|Biztonsági és adatvédelmi tanúsítványok| A minősítések legnaprakészebb listája a teljes [Azure-megfelelőségi webhelyet](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) , valamint a legújabb [Azure-megfelelőségi dokumentumot](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) tartalmazza minden minősítéssel (a Cosmos kifejezés keresése). A koncentráltabb olvasásért tekintse meg 2018 az [Azure #CosmosDB: Secure, Private, kompatibilis, SOCS 1/2 Type 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP magas és sok más.

A következő képernyőképen látható, hogyan használhatók a naplózási naplózási és a tevékenységi naplók a fiók figyeléséhez: :::image type="content" source="./media/database-security/nosql-database-security-application-logging.png" alt-text="Azure Cosmos db":::

<a id="primary-keys"></a>

## <a name="primary-keys"></a>Elsődleges kulcsok

Az elsődleges kulcsok hozzáférést biztosítanak az adatbázis-fiókhoz tartozó összes felügyeleti erőforráshoz. Elsődleges kulcsok:

- Hozzáférés biztosítása a fiókokhoz, adatbázisokhoz, felhasználókhoz és engedélyekhez. 
- Nem használható a tárolók és a dokumentumok részletes hozzáférésének biztosítására.
- A fiók létrehozása során jönnek létre.
- Bármikor újra létrehozhatók.

Minden fiók két elsődleges kulcsot tartalmaz: egy elsődleges és egy másodlagos kulcsot. A kettős kulcsok célja, hogy újragenerálja vagy leállítsa a kulcsokat, és folyamatos hozzáférést biztosítson fiókjához és adataihoz.

A Cosmos DB fiók két elsődleges kulcsa mellett két írásvédett kulcs is van. Ezek a csak olvasási jogosultsággal rendelkező kulcsok csak olvasási műveleteket engedélyeznek a fiókon. A csak olvasási jogosultsággal rendelkező kulcsok nem biztosítanak hozzáférést az olvasási engedélyek erőforrásaihoz.

Az elsődleges, a másodlagos, az írásvédett és az írható-olvasható elsődleges kulcsok lekérhető és újragenerálható a Azure Portal használatával. Útmutatásért lásd: [hozzáférési kulcsok megtekintése, másolása és újragenerálása](manage-with-cli.md#regenerate-account-key).

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Hozzáférés-vezérlés (IAM) a Azure Portal – NoSQL adatbázis biztonságának bemutatása":::

## <a name="next-steps"></a>Következő lépések

További információ az elsődleges kulcsokról és az erőforrás-jogkivonatokról: a [Azure Cosmos db adatokhoz való hozzáférés biztonságossá tétele](secure-access-to-data.md).

További információ a naplózásról: [Azure Cosmos db diagnosztikai naplózás](./monitor-cosmos-db.md).

További információ a Microsoft-tanúsítványokról: [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/).