---
title: Adatbázis-biztonság – Azure Cosmos DB-hez
description: Ismerje meg, hogyan nyújt az Azure Cosmos DB az adatbázis védelmét és az adatok biztonsági adataihoz.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: b82f7fed2407da86c036237a2de10363c4706d67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241136"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Az Azure Cosmos DB – biztonsági áttekintés

Ez a cikk ismerteti az adatbázis ajánlott biztonsági eljárások és segítséget nyújtanak a megelőzését, észlelését és elhárítását adatbázis biztonsági rések kialakulásához, az Azure Cosmos DB által nyújtott legfontosabb funkcióit.

## <a name="whats-new-in-azure-cosmos-db-security"></a>What's new in Azure Cosmos DB-biztonság

Titkosítás inaktív állapotban már elérhető a dokumentumok és az Azure Cosmos DB az összes Azure-régióban található biztonsági másolataihoz. Titkosítás inaktív állapotban automatikusan alkalmazza az új és meglévő ügyfelek ezekben a régiókban. Nem kell semmilyen; az azonos jelentős késés, átviteli sebesség, rendelkezésre állási kap, és funkciók, mielőtt az az előnye, hogy az adatok védelméről a titkosítás inaktív állapotban.

## <a name="how-do-i-secure-my-database"></a>Hogyan védhetem adatbázisom

Adatbiztonság egy, az ügyfél és az adatbázis-szolgáltató között megosztott felelősségre. Sokat felelőssége mennyisége az adatbázis-szolgáltatónál, attól függően eltérőek lehetnek. Ha úgy dönt, hogy egy helyszíni megoldás, meg kell adnia, hogy a hardver -, amely nem egyszerű feladat végponti fizikai biztonsági védelmet a. Ha úgy dönt, hogy egy PaaS felhőalapú adatbázis-szolgáltató például az Azure Cosmos DB, a terület hibaforrás jelentősen csökken. Az alábbi képen a Microsoft a kölcsönvett [megosztott feladatkörök a felhő-számítástechnika](https://aka.ms/sharedresponsibility) tanulmány, bemutatja, hogyan az Ön felelőssége PaaS szolgáltató, például az Azure Cosmos DB-csökken.

![Ügyfél- és adatbázis-szolgáltató feladatkörei](./media/database-security/nosql-database-security-responsibilities.png)

A fentebbi ábra bemutatja azokat magas szintű felhőbeli biztonsági összetevők, de milyen elemek kell aggódnia a kifejezetten a az adatbázis-megoldástól? És hogyan lehet összehasonlítja megoldásait egymáshoz?

Azt javasoljuk, hogy az alábbi ellenőrzőlista, amelyen adatbázis-rendszerek összehasonlítására követelmények:

- Hálózati biztonság és a tűzfal beállításai
- Felhasználói hitelesítés és a részletes felhasználói vezérlők
- Replikálhatók adatait globálisan regionális meghibásodások esetén
- Átadhatnak feladatokat egyik adatközpontból a másikba
- Helyi adatok replikálása egy adatközponton belül
- Automatikus biztonsági mentések
- A biztonsági mentésekből törölt adatok helyreállítása
- Védelme és a bizalmas adatok elkülönítése
- A támadások figyelése
- Válaszadás a támadások
- Igazodnia kell irányítási adatkorlátozás teszi geo-időkorlát adatok
- Kiszolgálók a védett adatközpontok fizikai védelme
- Minősítések

Bár nyilvánvaló, legutóbbi tűnhet [nagyméretű adatbázis kapcsolatos problémák esetén](https://thehackernews.com/2017/01/mongodb-database-security.html) emlékeztesse velünk a kapcsolatot a egyszerű, de a kritikus fontosságú az alábbi követelményeket:

- Javított kiszolgálókat, amelyek így mindig naprakészek
- Alapértelmezett/SSL-titkosítás által HTTPS
- Erős jelszót a rendszergazdai fiókok

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Hogyan Azure Cosmos DB-adatbázisomat biztonságossá

Vizsgáljuk meg a fenti lista vissza - hány biztonsági követelményekről az Azure Cosmos DB biztosítja a? Minden egyes egyetlen olyan.

Tájékozódjon részletesebben mindegyikhez.

|Biztonsági követelmény|Az Azure Cosmos DB biztonsági módszer|
|---|---|
|Hálózati biztonság|Az IP-tűzfalak használata esetén az első adatbázis biztonságossá tétele védelmi réteget. Az Azure Cosmos DB támogatja az IP-alapú hozzáférés-vezérlést a tűzfaltámogatás bejövő vezérelt házirend. Az IP-alapú hozzáférés-vezérlést a hagyományos adatbázisrendszerek által használt tűzfalszabályok hasonló, de azok ki vannak bontva, hogy az Azure Cosmos DB-adatbázisfiók gépeken vagy felhőszolgáltatásában, jóváhagyott csak érhető el. <br><br>Az Azure Cosmos DB lehetővé teszi, hogy egy adott IP-cím (168.61.48.0), egy IP-címtartomány (168.61.48.0/8) és az IP-címek és tartományok kombinációi. <br><br>Azure Cosmos DB engedélyezési listán szereplő gépekről származó összes kérelem blokkolja. Jóváhagyott gépek és felhőszolgáltatások majd érkező kéréseket kell végeznie a hitelesítési folyamat az erőforrásokhoz való hozzáférés-vezérlés kell megadni.<br><br>További információ: [Azure Cosmos DB tűzfaltámogatásáról](firewall-support.md).|
|Engedélyezés|Az Azure Cosmos DB üzenetet kivonat-alapú hitelesítési kód (HMAC) használ a hitelesítéshez használ. <br><br>A titkos kulcsát minden kérés kivonatolt, és a későbbi base-64 kódolású kivonat küldi el az egyes Azure Cosmos DB-hívások. A kérelem érvényesítéséhez, az Azure Cosmos DB szolgáltatást használja a megfelelő titkos kulcs és a Tulajdonságok létrehozni a kivonatot, majd összehasonlítja az értéket egy, a kérelmet. Ha a két érték egyezik, a művelet sikeresen jogosult-e, és a kérés feldolgozása, ellenkező esetben van egy engedélyezési hiba, és a kérelmet elutasították.<br><br>Használhat egy [főkulcs](secure-access-to-data.md#master-keys), vagy egy [erőforrás-jogkivonat](secure-access-to-data.md#resource-tokens) például egy dokumentum egy erőforrás részletes hozzáférést.<br><br>További információ: [Azure Cosmos DB-erőforrásokhoz való hozzáférés biztonságossá tétele](secure-access-to-data.md).|
|Felhasználók és engedélyek|A főkulcs a fiókot használja, hozhat létre a felhasználói és engedély erőforrások adatbázisonként. Egy erőforrás-jogkivonat társított engedély egy adatbázisban, és határozza meg, hogy a felhasználó rendelkezik-e az adatbázis egy alkalmazás-erőforrást (olvasási és írási, csak olvasható, vagy nincs hozzáférés) elérését. Alkalmazás-erőforrások közé tartoznak a tároló, dokumentumok, a mellékleteket, tárolt eljárások, eseményindítók és felhasználói függvények. Az erőforrás-jogkivonat-hitelesítés során majd használni a vagy megtagadja a hozzáférést az erőforráshoz.<br><br>További információ: [Azure Cosmos DB-erőforrásokhoz való hozzáférés biztonságossá tétele](secure-access-to-data.md).|
|Active directory-integráció (RBAC)| Adja meg, vagy a Cosmos fiókkal, adatbázis, tároló és ajánlatokat (teljesítmény) hozzáférés-vezérlés (IAM) használatával az Azure Portalon való hozzáférés korlátozása is. Az IAM szerepköralapú hozzáférés-vezérlést biztosít, és együttműködik az Active Directoryval. Használhatja a beépített szerepkörök vagy egyéni szerepkörök egyének és csoportok számára. Lásd: [Active Directory-integráció](role-based-access-control.md) cikkben további információt.|
|Globális replikálás|Az Azure Cosmos DB kínál a kulcsrakész globális disztribúciót, amely lehetővé teszi, hogy az adatok replikálása egy gombra kattintva elküldjön világméretű az Azure-adatközpontok közül. Globális replikálás lehetővé teszi, hogy globálisan méretezhető legyen, és kis késésű hozzáférést az adataihoz, a világ különböző pontjain.<br><br>Biztonsági környezetében globális replikáció biztosítja az adatok védelmet élveznek a regionális meghibásodásokkal szemben.<br><br>További információ: [Globális adatterjesztés](distribute-data-globally.md).|
|Régiónkénti feladatátvétel|Ha egynél több adatközpontban az adatok replikálása, az Azure Cosmos DB automatikusan áthalad a műveletek kell egy regionális adatközpont kapcsolat nélküli módba. A régió, amelyben a rendszer replikálja az adatokat használó feladatátvevő régiók rangsorolt listáját is létrehozhat. <br><br>További információ: [aktivált regionális feladatátvétel az Azure Cosmos DB](high-availability.md).|
|A helyi replikációt|Még egyetlen adatközpontba, az Azure Cosmos DB automatikusan replikálja az adatokat a magas rendelkezésre álláshoz, így a kiválasztott [konzisztenciaszintek](consistency-levels.md). A replikáció garantálja 99,99 %-os [rendelkezésre állási SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) minden egyrégiós és többrégiós fiókokat az összes enyhe konzisztencia, valamint 99,999 %-os olvasási rendelkezésre állás minden többrégiós adatbázisfiókhoz.|
|Automatikus online biztonsági másolatok|Az Azure Cosmos DB-adatbázisok rendszeresen biztonsági másolat, és a egy földrajzi redundáns tárolóban tárolja. <br><br>További információ: [automatikus online biztonsági mentés és visszaállítás az Azure Cosmos DB](online-backup-and-restore.md).|
|Törölt adatok helyreállításához|Az automatikus online biztonsági másolatok helyreállítani az adatokat, előfordulhat, hogy véletlenül törölt az esemény után akár KB. 30 napig használható. <br><br>További információ: [automatikus online biztonsági mentés és visszaállítás Azure Cosmos DB-vel](online-backup-and-restore.md)|
|Védelme és a bizalmas adatok elkülönítése|Minden adat a felsorolt új régiókban? most már titkosítása.<br><br>Személyes adatok és egyéb bizalmas adatokat el lehet különíteni az adott tároló és az olvasási és írási, vagy csak olvasási hozzáférés meghatározott felhasználókra korlátozható.|
|A figyelő támadások|Használatával [naplózás és a tevékenység auditnaplók](logging.md), nyomon követheti a fiók normál és rendellenes tevékenység. Milyen műveletek lettek végrehajtva az erőforrásokon, amikor a művelet történt, az állapot, a művelet, és sokkal amint az a táblázat alatti képernyőképet a műveletet kezdeményező tekintheti meg.|
|Elháríthatja a támadásokat|Azure-támogatási jelentését a potenciális támadási kell felvenni a kapcsolatot, miután egy 5-lépés incidensek megoldási folyamatának megkezdődik. Az 5. lépés – folyamat célja normál szolgáltatás biztonsága és műveletei a lehető leggyorsabban visszaállítása után problémát észlelt, és a vizsgálat elindult.<br><br>További információ: [Microsoft Azure Security Response a felhőben](https://aka.ms/securityresponsepaper).|
|Geokerítés-|Az Azure Cosmos DB biztosítja az adatszabályozást szuverén régiók (például Németország, Kína, US Gov).|
|Védett létesítményekben|Az Azure Cosmos DB adatokat az SSD-k az Azure által védett adatközpontokban tárolja.<br><br>További információ: [Microsoft globális adatközpontjairól](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|HTTPS/SSL/TLS encryption|Az összes ügyfél – szolgáltatás az Azure Cosmos DB kapcsolati SSL/TLS 1.2-es képes a rendszer. Az összes belüli adatközpontja és több adatközpont replikációk is SSL/TLS 1.2 kényszerítése.|
|Titkosítás inaktív állapotban|Az Azure Cosmos DB-be tárolt összes adat titkosítása. További információ: [Azure Cosmos DB-titkosítás inaktív állapotban](./database-encryption-at-rest.md)|
|Javított kiszolgálók|Egy felügyelt adatbázis Azure Cosmos DB szükségtelenné teszi a kezelése és a patch-kiszolgálók által elvégzett, automatikusan.|
|Erős jelszót a rendszergazdai fiókok|Hihetetlen, még akkor is meg kell ezt a követelményt említik, de néhány lemaradni a versenytársak eltérően nem lehet egy rendszergazdai fiók, jelszó nélküli rendelkezik az Azure Cosmos DB.<br><br> Alapértelmezés szerint a számlázásnak biztonsági SSL és HMAC-val titkos alapú hitelesítés használatával.|
|Biztonság és a data protection minősítések| Minősítések legfrissebb listáját lásd: a teljes [Azure megfelelőségi hely](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) és a legújabb [Azure megfelelőségi dokumentum](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) az összes tanúsítványának (Cosmos keresése). Tekintse meg a 2018. április 25. bejegyzés egy célzottabb olvasni az [Azure #CosmosDB: Biztonságos, privát, előírásoknak megfelelő, amely tartalmazza a csak 1/2 Type 2, HITRUST, PCI DSS Level 1, az ISO 27001, a HIPAA, magas szintű FedRAMP és sok más.

Az alábbi képernyőképen látható, hogyan naplózás és a tevékenység auditnaplók használhatja a fiók figyelése: ![A Tevékenységnaplók az Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>További lépések

Mesterkulcs és erőforrás-jogkivonatokat kapcsolatos további információkért lásd: [Azure Cosmos DB-adatokhoz való hozzáférés biztonságossá tétele](secure-access-to-data.md).

Naplózási naplózásával kapcsolatos további információkért lásd: [diagnosztikai naplózás az Azure Cosmos DB](logging.md).

Microsoft-minősítésekkel kapcsolatos további információkért lásd: [Azure adatvédelmi központ](https://azure.microsoft.com/support/trust-center/).