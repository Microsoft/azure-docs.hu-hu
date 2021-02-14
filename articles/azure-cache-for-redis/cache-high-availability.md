---
title: Magas rendelkezésre állás az Azure cache Redis
description: Ismerje meg az Azure cache Redis magas rendelkezésre állású funkcióit és lehetőségeit
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: d9c8f5dd8b2647756087ce6f36ff3a25b2aaaadc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387971"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Magas rendelkezésre állás az Azure cache Redis

A Redis készült Azure cache beépített magas rendelkezésre állást biztosít. Magas rendelkezésre állású architektúrájának célja annak biztosítása, hogy a felügyelt Redis-példányok akkor is működőképesek legyenek, ha az alapul szolgáló virtuális gépeket a tervezett vagy nem tervezett kimaradások befolyásolják. Sokkal nagyobb százalékos arányt biztosít, mint amit a Redis egyetlen virtuális gépen való üzemeltetésével érhet el.

A Redis készült Azure cache magas rendelkezésre állást biztosít a gyorsítótárhoz több, *csomópontok* nevű virtuális gép használatával. Ezeket a csomópontokat úgy konfigurálja, hogy az adatreplikáció és a feladatátvétel koordinált módon történjen. Emellett a karbantartási műveleteket, például a Redis-szoftverek javítását is elvégezheti. A standard, Premium és Enterprise szinten számos magas rendelkezésre állási lehetőség érhető el:

| Beállítás | Leírás | Rendelkezésre állás | Standard | Prémium | Enterprise |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Normál replikáció](#standard-replication)| Két csomópontos replikált konfiguráció egyetlen adatközpontban automatikus feladatátvételsel | 99.9% |✔|✔|-|
| [Zónaredundancia](#zone-redundancy) | Több csomópontos replikált konfiguráció a AZs-ben automatikus feladatátvételsel | 99,95% (prémium szint), 99,99% (nagyvállalati szint) |-|Előnézet|Előnézet|
| [Georeplikáció](#geo-replication) | Csatolt gyorsítótár-példányok két régióban, felhasználó által vezérelt feladatátvételsel | 99,9% (prémium szint, egyetlen régió) |-|✔|-|

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

Emellett az Azure cache for Redis lehetővé teszi további replika-csomópontok használatát a prémium szinten. A [több replika gyorsítótára](cache-how-to-multi-replicas.md) legfeljebb három replika csomóponttal konfigurálható. A további replikák általában javítják a rugalmasságot, mert az elsődleges biztonsági másolatokat tartalmazó további csomópontok miatt. Még ha több replikával is rendelkezik, a Redis-példányhoz tartozó Azure cache-t továbbra is súlyosan befolyásolhatja az adatközpontok vagy AZ AZ egészet érintő leállás. A gyorsítótár rendelkezésre állását több replikával is növelheti a [zóna redundancia](#zone-redundancy)szolgáltatásával együtt.

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

### <a name="enterprise-and-enterprise-flash-tiers"></a>Nagyvállalati és vállalati Flash-rétegek

A vállalati szintű gyorsítótárak egy Redis vállalati fürtön futnak. A kvórum létrehozásához minden esetben páratlan számú kiszolgálói csomópontra van szükség. Alapértelmezés szerint három csomópontból áll, amelyek mindegyike egy dedikált virtuális gépen fut. A vállalati gyorsítótár két azonos méretű *adatcsomóponttal* és egy kisebb *kvórum csomóponttal* rendelkezik. A vállalati Flash-gyorsítótár három azonos méretű adatcsomóponttal rendelkezik. A vállalati fürt a Redis az adatpartíciókat belsőleg osztja szét. Minden partíció rendelkezik egy *elsődleges* és legalább egy *replikával*. Minden adatcsomópont egy vagy több partícióval rendelkezik. A vállalati fürt biztosítja, hogy a partíciók elsődleges és replikája soha ne legyen ugyanazon az adatcsomóponton elhelyezve. A partíciók aszinkron módon replikálják az adatokból a megfelelő replikákat.

Ha egy adatcsomópont elérhetetlenné válik, vagy egy hálózati megosztás történik, a [normál replikációban](#standard-replication) leírthoz hasonló feladatátvétel zajlik. A vállalati fürt kvórum-alapú modellt használ annak meghatározására, hogy mely túlélő csomópontok fognak részt venni egy új kvórumban. Emellett a csomópontokon belüli replika-partíciókat is támogatja, ha szükséges.

## <a name="geo-replication"></a>Georeplikáció

A [geo-replikáció](cache-how-to-geo-replication.md) két Azure-gyorsítótár Redis-példányokhoz való összekapcsolására szolgáló mechanizmus, amely jellemzően két Azure-régióra kiterjed. Az egyik gyorsítótár elsődleges csatolt gyorsítótárként van kiválasztva, a másik pedig másodlagos csatolt gyorsítótárként. Csak az elsődleges csatolt gyorsítótár fogadja el az olvasási és írási kérelmeket. Az elsődleges gyorsítótárba írt adatforgalom a másodlagos csatolt gyorsítótárba replikálódik. A másodlagos csatolt gyorsítótár az olvasási kérések kiszolgálására szolgál. Az elsődleges és a másodlagos gyorsítótár példányai közötti adatátvitelt a TLS védi.

A Geo-replikáció főleg a vész-helyreállításhoz lett tervezve. Lehetővé teszi a gyorsítótár-adataik biztonsági mentését egy másik régióba. Alapértelmezés szerint az alkalmazás az elsődleges régióból ír és olvas. Opcionálisan beállítható úgy is, hogy a másodlagos régióból olvasson. A Geo-replikáció nem biztosít automatikus feladatátvételt, mert a régiók között a hozzáadott hálózati késés miatti probléma az, hogy az alkalmazás többi része az elsődleges régióban marad. A másodlagos gyorsítótár leválasztásával kell kezelnie és kezdeményeznie a feladatátvételt. Ez az új elsődleges példány lesz.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan konfigurálható az Azure cache a Redis magas rendelkezésre állási lehetőségeihez.

* [Azure cache a Redis prémium szintű szolgáltatási szintjeihez](cache-overview.md#service-tiers)
* [Replikák hozzáadása az Azure cache-hez a Redis-hez](cache-how-to-multi-replicas.md)
* [Zónák redundanciának engedélyezése az Azure cache számára a Redis](cache-how-to-zone-redundancy.md)
* [Az Azure cache geo-replikációjának beállítása a Redis](cache-how-to-geo-replication.md)