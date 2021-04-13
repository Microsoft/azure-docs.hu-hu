---
title: Magas rendelkezésre állás az Azure cache Redis
description: Ismerje meg az Azure cache Redis magas rendelkezésre állású funkcióit és lehetőségeit
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 69dec2ce00b263f3536e30ba0a5376e6d922b79c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308353"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Magas rendelkezésre állás az Azure cache Redis

A Redis készült Azure cache beépített magas rendelkezésre állást biztosít. Magas rendelkezésre állású architektúrájának célja annak biztosítása, hogy a felügyelt Redis-példányok akkor is működőképesek legyenek, ha az alapul szolgáló virtuális gépeket a tervezett vagy nem tervezett kimaradások befolyásolják. Sokkal nagyobb százalékos arányt biztosít, mint amit a Redis egyetlen virtuális gépen való üzemeltetésével érhet el.

A Redis készült Azure cache magas rendelkezésre állást biztosít a gyorsítótárhoz több, *csomópontok* nevű virtuális gép használatával. Ezeket a csomópontokat úgy konfigurálja, hogy az adatreplikáció és a feladatátvétel koordinált módon történjen. Emellett a karbantartási műveleteket, például a Redis-szoftverek javítását is elvégezheti. A standard, Premium és Enterprise szinten számos magas rendelkezésre állási lehetőség érhető el:

| Beállítás | Leírás | Rendelkezésre állás | Standard | Prémium | Enterprise |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Normál replikáció](#standard-replication)| Két csomópontos replikált konfiguráció egyetlen adatközpontban automatikus feladatátvételsel | 99,9% ( [részletek](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)megtekintése) |✔|✔|-|
| [Zónaredundancia](#zone-redundancy) | Több csomópontos replikált konfiguráció a AZs-ben automatikus feladatátvételsel | Akár 99,99% ( [részletek](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)megtekintése) |-|Előnézet|Előnézet|
| [Georeplikáció](#geo-replication) | Csatolt gyorsítótár-példányok két régióban, felhasználó által vezérelt feladatátvételsel | Akár 99,999% ( [részletek](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)megtekintése) |-|✔|Előnézet|

## <a name="standard-replication"></a>Normál replikáció

A standard vagy a prémium szint Redis tartozó Azure cache alapértelmezés szerint egy pár Redis-kiszolgálón fut. A két kiszolgáló dedikált virtuális gépeken fut. A nyílt forráskódú Redis lehetővé teszi, hogy csak egy kiszolgáló kezelje az adatírási kérelmeket. Ez a kiszolgáló az *elsődleges* csomópont, míg a másik *replika*. A kiszolgálói csomópontok kiépítését követően az Azure cache for Redis az elsődleges és a replika szerepköröket rendeli hozzájuk. Az elsődleges csomópont általában az írás és a Redis-ügyfelek olvasási kéréseinek kiszolgálására szolgál. Írási művelet esetén egy új kulcsot és egy kulcs frissítését véglegesíti a belső memóriájában, és azonnal válaszol a-ügyfélre. Aszinkron módon továbbítja a műveletet a replikának.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Adatreplikáció beállítása":::
   
>[!NOTE]
>A Redis-ügyfél általában az összes olvasási és írási kérelem esetében egy Redis gyorsítótárban kommunikál az elsődleges csomóponttal. Bizonyos Redis-ügyfelek úgy konfigurálhatók, hogy a replika csomópontról olvassák a beolvasást.
>
>

Ha egy Redis-gyorsítótár elsődleges csomópontja nem érhető el, akkor a replika az új elsődleges automatikusan lesz előléptetve. Ezt a folyamatot *feladatátvételnek* nevezzük. Ha az elsődleges csomópont gyorsan helyreállítható, a replika elég hosszú időt vár a megkezdése előtt. Feladatátvétel esetén az Azure cache for Redis kiépít egy új virtuális gépet, és a replika csomópontként csatlakoztatja a gyorsítótárhoz. A replika teljes adatszinkronizálást végez az elsődlegesen, hogy a gyorsítótár-adatok egy másik példánya legyen.

Egy elsődleges csomópont egy tervezett karbantartási tevékenység részeként kiléphet a szolgáltatásból, például a Redis szoftver vagy az operációs rendszer frissítése során. Emellett a nem tervezett események, például a mögöttes hardver-, szoftver-és hálózati hibák miatt is leállhat. A [Redis Azure cache feladatátvétele és javítása](cache-failover.md) részletes magyarázatot nyújt a Redis-feladatátvételek típusairól. A Redis készült Azure cache számos feladatátvételt hajt végre az élettartama során. A magas rendelkezésre állású architektúra úgy van kialakítva, hogy az ügyfelek számára a lehető legátlátható módon végezze el a módosításokat a gyorsítótáron belül.

>[!NOTE]
>A következő előzetes verzióként érhető el.
>
>

Emellett az Azure cache for Redis lehetővé teszi további replika-csomópontok használatát a prémium szinten. A [több replika gyorsítótára](cache-how-to-multi-replicas.md) legfeljebb három replika csomóponttal konfigurálható. A további replikák általában javítják a rugalmasságot, mert az elsődleges biztonsági másolatokat tartalmazó további csomópontok miatt. Még ha több replikával is rendelkezik, a Redis-példányhoz tartozó Azure cache-t továbbra is súlyosan befolyásolhatja az adatközpontok vagy AZ AZ-szintű leállás. A gyorsítótár rendelkezésre állását több replikával is növelheti a [zóna redundancia](#zone-redundancy)szolgáltatásával együtt.

## <a name="zone-redundancy"></a>Zónaredundancia

Az Azure cache for Redis támogatja a zóna redundáns konfigurációit a prémium és a nagyvállalati szinteken. A [zóna redundáns gyorsítótára](cache-how-to-zone-redundancy.md) a csomópontokat ugyanazon régió különböző [Azure Availability Zonesi](../availability-zones/az-overview.md) között helyezheti el. Kiküszöböli az adatközpontot vagy az kimaradást egyetlen meghibásodási pontként, és növeli a gyorsítótár teljes rendelkezésre állását.

### <a name="premium-tier"></a>Prémium szintű csomag

>[!NOTE]
>Ez előzetes verzióként érhető el.
>
>

A következő ábra szemlélteti a prémium szintű zóna redundáns konfigurációját:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Zóna redundancia beállítása":::
   
A Redis-hez készült Azure cache egy ciklikusan elosztott zónában lévő csomópontokat oszt szét a kiválasztott AZs szemben. Azt is meghatározza, hogy melyik csomópont lesz kezdetben elsődlegesként kiszolgálva.

A zóna redundáns gyorsítótára automatikus feladatátvételt biztosít. Ha az aktuális elsődleges csomópont nem érhető el, az egyik replikát átveszi a rendszer. Előfordulhat, hogy az alkalmazás magasabb gyorsítótár-válaszidőt észlel, ha az új elsődleges csomópont más az-ban található. A AZs földrajzilag el vannak különítve. Az alkalmazás és a gyorsítótár közötti fizikai távolságot az egyikről a másikra váltva változtathatja meg. Ez a módosítás az alkalmazásból a gyorsítótárba való átállási hálózati késéseket is befolyásolja. Az extra késés várhatóan a legtöbb alkalmazás elfogadható tartományán belül esik. Javasoljuk, hogy tesztelje az alkalmazást, és győződjön meg arról, hogy az jól használható-e a zóna redundáns gyorsítótárával.

### <a name="enterprise-tiers"></a>Nagyvállalati szintek

A vállalati szintű gyorsítótárak egy Redis vállalati fürtön futnak. A kvórum létrehozásához minden esetben páratlan számú kiszolgálói csomópontra van szükség. Alapértelmezés szerint három csomópontból áll, amelyek mindegyike egy dedikált virtuális gépen fut. A vállalati gyorsítótár két azonos méretű *adatcsomóponttal* és egy kisebb *kvórum csomóponttal* rendelkezik. A vállalati Flash-gyorsítótár három azonos méretű adatcsomóponttal rendelkezik. A vállalati fürt a Redis az adatpartíciókat belsőleg osztja szét. Minden partíció rendelkezik egy *elsődleges* és legalább egy *replikával*. Minden adatcsomópont egy vagy több partícióval rendelkezik. A vállalati fürt biztosítja, hogy a partíciók elsődleges és replikája soha ne legyen ugyanazon az adatcsomóponton elhelyezve. A partíciók aszinkron módon replikálják az adatokból a megfelelő replikákat.

Ha egy adatcsomópont elérhetetlenné válik, vagy egy hálózati megosztás történik, a [normál replikációban](#standard-replication) leírthoz hasonló feladatátvétel zajlik. A vállalati fürt kvórum-alapú modellt használ annak meghatározására, hogy mely túlélő csomópontok fognak részt venni egy új kvórumban. Emellett a csomópontokon belüli replika-partíciókat is támogatja, ha szükséges.

## <a name="geo-replication"></a>Georeplikáció

A [geo-replikáció](cache-how-to-geo-replication.md) egy olyan mechanizmus, amellyel két vagy több Azure cache-t Redis-példányokhoz is összekapcsolhat, jellemzően két Azure-régióra. 

### <a name="premium-tier"></a>Prémium szintű csomag

>[!NOTE]
>A prémium szintű geo-replikáció főleg a vész-helyreállításhoz lett tervezve.
>
>

A különböző prémium szintű gyorsítótár-példányok a [geo-replikáción](cache-how-to-geo-replication.md) keresztül csatlakoztathatók, így a gyorsítótár-adataikat egy másik régióba lehet biztonsági másolatot készíteni. A csatolást követően az egyik példány az elsődleges csatolt gyorsítótárként, a másik pedig másodlagos csatolt gyorsítótárként van kijelölve. Csak az elsődleges gyorsítótár fogadja az olvasási és írási kérelmeket. Az elsődleges gyorsítótárba írt adatkészletek a másodlagos gyorsítótárba lesznek replikálva. Az alkalmazás az elsődleges és másodlagos gyorsítótárak külön végpontján keresztül fér hozzá a gyorsítótárhoz. Az alkalmazásnak az összes írási kérelmet az elsődleges gyorsítótárba kell küldenie, ha több Azure-régióban van üzembe helyezve. Az elsődleges vagy másodlagos gyorsítótárból is olvasható. Általánosságban elmondható, hogy az alkalmazás számítási példányainak a legközelebbi gyorsítótárból kell olvasniuk a késés csökkentése érdekében. A két gyorsítótár-példány közötti adatátvitelt a TLS védi.

A Geo-replikáció nem biztosít automatikus feladatátvételt, mivel a további hálózati oda-és bekapcsolási idő a régiók között történik, ha az alkalmazás többi része az elsődleges régióban marad. A másodlagos gyorsítótár leválasztásával kell kezelnie és kezdeményeznie a feladatátvételt. Ez az új elsődleges példány lesz.

### <a name="enterprise-tiers"></a>Nagyvállalati szintek

>[!NOTE]
>Ez előzetes verzióként érhető el.
>
>

A vállalati rétegek a Geo-replikálás fejlettebb formáját támogatják, amelyet [aktív geo-replikálásnak](cache-how-to-active-geo-replication.md)nevezünk. Az ütközés nélküli replikált adattípusok kihasználása esetén a Redis Enterprise szoftver támogatja több gyorsítótár-példány írását, és szükség esetén gondoskodik a módosítások egyesítéséről és az ütközések feloldásáról. A különböző Azure-régiókban lévő két vagy több vállalati rétegbeli gyorsítótár-példány csatlakoztatható aktív geo-replikált gyorsítótár létrehozásához. Az ilyen gyorsítótárat használó alkalmazások a megfelelő végpontokon keresztül olvashatnak és írhatnak a földrajzilag elosztott gyorsítótár-példányokat. Az egyes számítási példányokhoz legközelebb eső értéket kell használnia, amely a legalacsonyabb késést adja. Az alkalmazásnak emellett figyelnie kell a gyorsítótár-példányokat, és váltania kell egy másik régióra, ha az egyik példány elérhetetlenné válik. További információ az aktív geo-replikáció működéséről: [aktív-aktív Geo-Distriubtion (CRDTs-alapú)](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/).

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan konfigurálható az Azure cache a Redis magas rendelkezésre állási lehetőségeihez.

* [Azure cache a Redis prémium szintű szolgáltatási szintjeihez](cache-overview.md#service-tiers)
* [Replikák hozzáadása az Azure cache-hez a Redis-hez](cache-how-to-multi-replicas.md)
* [Zónák redundanciának engedélyezése az Azure cache számára a Redis](cache-how-to-zone-redundancy.md)
* [Az Azure cache geo-replikációjának beállítása a Redis](cache-how-to-geo-replication.md)
