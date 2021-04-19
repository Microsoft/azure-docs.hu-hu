---
title: Magas rendelkezésre állás Azure Cache for Redis
description: Tudnivalók a Azure Cache for Redis funkciókról és lehetőségekről
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 6c44c87221442797f063877385ac5eb7f8585850
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719096"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Magas rendelkezésre állás Azure Cache for Redis

Azure Cache for Redis beépített magas rendelkezésre állású. Magas rendelkezésre állású architektúrája célja annak biztosítása, hogy a felügyelt Redis-példány akkor is működjön, ha a mögöttes virtuális gépeket (VM-eket) a tervezett vagy nem tervezett leállások befolyásolják. Sokkal nagyobb százalékos arányt biztosít, mint amit a Redis egyetlen virtuális gépen való üzemeltetése elérhető.

Azure Cache for Redis a magas rendelkezésre állást több virtuális gép, más néven csomópontok használatával valósítja meg a gyorsítótárakhoz. Úgy konfigurálja ezeket a csomópontokat, hogy az adatreplikáció és a feladatátvétel koordinált módon történjen. Emellett olyan karbantartási műveleteket is vezényel, mint a Redis szoftverjavítása. A Standard, a Prémium és a Nagyvállalati szinten különböző magas rendelkezésre állási lehetőségek érhetők el:

| Beállítás | Leírás | Rendelkezésre állás | Standard | Prémium | Enterprise |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Standard replikáció](#standard-replication)| Kétcsomópontos replikált konfiguráció egyetlen adatközpontban automatikus feladatátvételsel | 99,9% [(részletek)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Zónaredundancia](#zone-redundancy) | Többcsomópontos replikált konfiguráció a különböző AZ-kben automatikus feladatátvételsel | Akár 99,99% [(részletek)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|Előnézet|✔|
| [Georeplikáció](#geo-replication) | Összekapcsolt gyorsítótárpéldányok két régióban, felhasználó által vezérelt feladatátvételsel | Akár 99,999% [(részletek)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|Előnézet|

## <a name="standard-replication"></a>Standard replikáció

A Azure Cache for Redis standard vagy prémium szinten található kiszolgálók alapértelmezés szerint két Redis-kiszolgálón futnak. A két kiszolgáló dedikált virtuális gépeken üzemel. A nyílt forráskódú Redis csak egy kiszolgáló számára teszi lehetővé az adatírási kérelmeket. Ez a kiszolgáló az *elsődleges csomópont,* míg a másik *replika.* Miután kiosztja a kiszolgáló-csomópontokat, Azure Cache for Redis elsődleges és replika szerepköröket rendel hozzájuk. Az elsődleges csomópont általában a Redis-ügyfelek írási és olvasási kéréseit is kiszolgálja. Írási művelet során egy új kulcsot és egy kulcsfrissítést véglegesít a belső memóriában, és azonnal válaszol az ügyfélnek. Aszinkron módon továbbítja a műveletet a replikának.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Adatreplikáció beállítása":::
   
>[!NOTE]
>A Redis-ügyfelek általában az összes olvasási és írási kéréshez kommunikálnak a Redis Cache elsődleges csomópontján. Bizonyos Redis-ügyfelek konfigurálhatók úgy, hogy a replikacsomópontból olvassanak.
>
>

Ha a Redis Cache-gyorsítótár elsődleges csomópontja nem érhető el, a replika előlépteti magát, hogy automatikusan az új elsődleges csomópont legyen. Ezt a folyamatot *feladatátvételnek nevezzük.* A replika elég hosszú ideig várakozik, mielőtt átveszi az et, hogy az elsődleges csomópont gyorsan helyreálljon. Feladatátvétel esetén a Azure Cache for Redis új virtuális gépet hoz létre, és a replikacsomópontként a gyorsítótárhoz csatlakozik. A replika teljes adatszinkronizálást hajt végre az elsődleges adatbázissal, hogy a gyorsítótár-adatok egy másik másolatával is rendelkezik.

Az elsődleges csomópontok egy tervezett karbantartási tevékenység, például a Redis szoftver vagy az operációs rendszer frissítése során leállnak. Emellett leállhat nem tervezett események, például a mögöttes hardver, szoftver vagy hálózat meghibásodása miatt is. [A redis feladatátvételi Azure Cache for Redis](cache-failover.md) a redis feladatátvételek típusairól nyújt részletes magyarázatot. A Azure Cache for Redis számos feladatátvételen megy keresztül az élettartama során. A magas rendelkezésre állású architektúra úgy lett kialakítva, hogy ezeket a módosításokat a gyorsítótárban a lehető legátláthatóbbá tegye az ügyfelek számára.

>[!NOTE]
>Az alábbiak előzetes verzióként érhetők el.
>
>

Emellett a Azure Cache for Redis további replikacsomópontokat is lehetővé tesz a Prémium szinten. Több [replikagyorsítótár](cache-how-to-multi-replicas.md) legfeljebb három replikacsomóponttal konfigurálható. A több replika általában növeli a rugalmasságot, mivel az elsődleges csomópontok további csomópontok biztonsági másolatai is vannak. Még több replika esetén is Azure Cache for Redis adatközpont- vagy AZ-szintű szolgáltatáskimaradás súlyosan befolyásolhatja a példánypéldányt. A gyorsítótár rendelkezésre állását több replika és zónaredundania együttes használatával [növelheti.](#zone-redundancy)

## <a name="zone-redundancy"></a>Zónaredundancia

Azure Cache for Redis támogatja a zónaredundáns konfigurációkat a Prémium és a Nagyvállalati szinten. A [zónaredundáns gyorsítótár](cache-how-to-zone-redundancy.md) [](../availability-zones/az-overview.md) a csomópontokat különböző Azure Availability Zones ugyanabban a régióban helyezze el. Kiküszöböli az adatközpont vagy az AZ szolgáltatáskimaradását, mivel ez egy rendszerkéssértő pont, és növeli a gyorsítótár általános rendelkezésre állását.

### <a name="premium-tier"></a>Prémium szintű csomag

>[!NOTE]
>Ez előzetes verzióként érhető el.
>
>

Az alábbi ábra a prémium szint zónaredundáns konfigurációját mutatja be:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Zónaredundania beállítása":::
   
Azure Cache for Redis a csomópontokat egy zónaredundáns gyorsítótárban, a kiválasztott AZ-hez időzített módon osztja el. Azt is meghatározza, hogy kezdetben melyik csomópont szolgál elsődleges csomópontként.

A zónaredundáns gyorsítótár automatikus feladatátvételt biztosít. Ha az aktuális elsődleges csomópont nem érhető el, az egyik replika átveszi az áramot. Az alkalmazás magasabb gyorsítótár-válaszidőt tapasztalhat, ha az új elsődleges csomópont egy másik AZ-ban található. Az AZ-k földrajzilag elkülönülnek egymástól. Az egyik AZ-ről a másikra való váltás megváltoztatja az alkalmazás és a gyorsítótár közötti fizikai távolságot. Ez a módosítás hatással van az alkalmazás és a gyorsítótár közötti adatváltási hálózati késésre. A többletkésés várhatóan a legtöbb alkalmazás esetében elfogadható tartományba esik. Javasoljuk, hogy tesztelje az alkalmazást, hogy jól teljesítsen a zónaredundáns gyorsítótárral.

### <a name="enterprise-tiers"></a>Nagyvállalati szintek

A vállalati szinten lévő gyorsítótárak Redis Enterprise-fürtön futnak. Ahhoz, hogy kvórumot hoz létre, mindig páratlan számú kiszolgálócsomópontra van szükség. Alapértelmezés szerint három csomópontból áll, amelyek mindegyikét egy dedikált virtuális gép üzemelteti. A vállalati gyorsítótár két  azonos méretű adatcsomóponttal és egy kisebb kvórumcsomóponttal *rendelkezik.* A Enterprise Flash gyorsítótár három azonos méretű adatcsomóponttal rendelkezik. A vállalati fürt belső partíciókra osztja a Redis-adatokat. Minden partíció rendelkezik egy *elsődleges és* legalább egy *replikával.* Minden adatcsomópont egy vagy több partíciót tartalmaz. A vállalati fürt gondoskodik arról, hogy a partíciók elsődleges és replikapéldányai soha ne ugyanazon az adatcsomóponton található(k) együtt. A partíciók aszinkron módon replikálják az adatokat az adatbázisból a megfelelő replikákba.

Ha egy adatcsomópont elérhetetlenné válik, vagy hálózati felosztás történik, a standard replikációban leírthoz hasonló [feladatátvétel](#standard-replication) történik. A vállalati fürt kvórumalapú modellel határozza meg, hogy mely megmaradt csomópontok vesznek részt az új kvórumban. Emellett szükség szerint az ezeken a csomópontokon belüli replikapartíciókat is előlépteti az ries partíciókra.

## <a name="geo-replication"></a>Georeplikáció

[A georeplikáció](cache-how-to-geo-replication.md) egy olyan mechanizmus, amely két vagy több virtuális Azure Cache for Redis kapcsol össze, általában két Azure-régióra kiterjedően. 

### <a name="premium-tier"></a>Prémium szintű csomag

>[!NOTE]
>A prémium szintű georeplikáció főleg vészhelyreállításhoz lett kialakítva.
>
>

Két prémium szintű gyorsítótárpéldányt [](cache-how-to-geo-replication.md) csatlakoztathat georeplikációval, így a gyorsítótárazott adatokról egy másik régióba is mentheti a biztonsági adatokat. Az összekapcsolás után az egyik példány lesz az elsődleges csatolt gyorsítótár, a másik pedig másodlagos csatolt gyorsítótár. Csak az elsődleges gyorsítótár fogadja el az olvasási és írási kéréseket. A rendszer replikálja az elsődleges gyorsítótárba írt adatokat a másodlagos gyorsítótárba. Az alkalmazások az elsődleges és másodlagos gyorsítótárak külön végpontjaival férnek hozzá a gyorsítótárhoz. Az alkalmazásnak az összes írási kérést el kell küldenie az elsődleges gyorsítótárba, ha több Azure-régióban van telepítve. Képes olvasni az elsődleges vagy a másodlagos gyorsítótárból. Általában azt szeretné, hogy az alkalmazás számítási példányai a legközelebbi gyorsítótárból olvassanak a késés csökkentése érdekében. A két gyorsítótárpéldány közötti adatátvitelt a TLS biztonságos.

A georeplikáció nem biztosít automatikus feladatátvételt a régiók közötti hálózati adatváltási idővel kapcsolatos problémák miatt, ha az alkalmazás többi része az elsődleges régióban marad. A feladatátvételt a másodlagos gyorsítótár leválasztása segítségével kell kezelnie és elindítania. Ez előlépteti az új elsődleges példányként.

### <a name="enterprise-tiers"></a>Nagyvállalati szintek

>[!NOTE]
>Ez előzetes verzióként érhető el.
>
>

A vállalati szintek támogatják a georeplikáció egy fejlettebb formáját, az aktív [georeplikációt.](cache-how-to-active-geo-replication.md) Az ütközésmentes replikált adattípusok használata esetén a Redis Enterprise szoftver támogatja a több gyorsítótárpéldányba történő írást, és gondoskodik a módosítások egyesítése és szükség esetén az ütközések feloldása között. A különböző Azure-régiókban lévő két vagy több vállalati szintű gyorsítótárpéldány aktív georeplikált gyorsítótárként összekapcsolható. Az ilyen gyorsítótárat használó alkalmazások a megfelelő végpontok használatával olvashatják és írnak a földrajzi elosztott gyorsítótárpéldányokra. Az egyes számítási példányok legközelebbi példányát kell használnia, ami a legkisebb késést biztosítja. Az alkalmazásnak emellett figyelnie kell a gyorsítótárpéldányokat, és át kell váltania egy másik régióra, ha az egyik példány elérhetetlenné válik. További információ az aktív georeplikációról: [Aktív-aktív Geo-Distriubtion (CRDT-alapú).](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/)

## <a name="next-steps"></a>Következő lépések

További információ a magas Azure Cache for Redis konfigurálásról.

* [Azure Cache for Redis Prémium szolgáltatási szintek](cache-overview.md#service-tiers)
* [Replikák hozzáadása Azure Cache for Redis](cache-how-to-multi-replicas.md)
* [Zónaredundania engedélyezése Azure Cache for Redis](cache-how-to-zone-redundancy.md)
* [Georeplikáció beállítása Azure Cache for Redis](cache-how-to-geo-replication.md)
