---
title: Válassza ki a megfelelő PostgreSQL-kiszolgáló lehetőséget az Azure-ban
description: Útmutatást nyújt a PostgreSQL-kiszolgáló megfelelő beállításának kiválasztásához az üzemelő példányokhoz.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: db80ae66761e49ce90f25a22f0e74cf8a1300796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98797227"
---
# <a name="choose-the-right-postgresql-server-option-in-azure"></a>Válassza ki a megfelelő PostgreSQL-kiszolgáló lehetőséget az Azure-ban

Az Azure-ban a PostgreSQL-kiszolgáló munkaterhelései egy üzemeltetett virtuálisgép-infrastruktúrában (IaaS) vagy üzemeltetett szolgáltatásként (Péter) is futhatnak. A Péter több üzembe helyezési lehetőséggel rendelkezik, amelyek mindegyike több szolgáltatási réteggel rendelkezik. Ha a IaaS és a Péter közötti választást választja, el kell döntenie, hogy szeretné-e kezelni az adatbázist, a javítások alkalmazásával és a biztonsági másolatok készítésével, vagy ha ezeket a műveleteket az Azure-ba szeretné delegálni.

A döntés meghozatalakor vegye figyelembe a következő három lehetőséget a Pásti-ben vagy az Azure-beli virtuális gépeken (IaaS) való futtatáskor
- [Azure Database for PostgreSQL, egyetlen kiszolgáló](./overview-single-server.md)
- [Azure Database for PostgreSQL, rugalmas kiszolgáló](./flexible-server/overview.md)
- [Azure Database for PostgreSQL nagy kapacitású (Citus)](hyperscale/index.yml)

A PostgreSQL az Azure-beli **virtuális gépeken** lehetőség a IaaS iparág kategóriájára esik. Ezzel a szolgáltatással a PostgreSQL-kiszolgálót egy teljes körűen felügyelt virtuális gépen futtathatja az Azure Cloud platformon. A PostgreSQL legújabb verzióit és kiadásait IaaS virtuális gépre lehet telepíteni. A Azure Database for PostgreSQL legjelentősebb különbsége, hogy az Azure-beli virtuális gépeken a PostgreSQL felügyeli az adatbázis-működtető szolgáltatást. Ez a szabályozás azonban a virtuális gépek és számos adatbázis-felügyeleti (DBA) feladat felügyeletének a felelősségi körébe tartozik. Ezen feladatok közé tartozik az adatbázis-kiszolgálók, az adatbázis-helyreállítás és a magas rendelkezésre állású kialakítás fenntartása és javítása.

A fenti lehetőségek közötti fő különbségek a következő táblázatban láthatók:

| **Attribútum** | **Postgres az Azure-beli virtuális gépeken** | **PostgreSQL mint Péter** |
| ----- | ----- | ----- |
| <B> Rendelkezésre állási SLA |-99,99% rendelkezésre állási csoportokkal <br> -99,95% egyetlen virtuális géppel | -Egyetlen kiszolgáló – 99,99% <br> -Rugalmas kiszolgáló – előzetes verzióban nem érhető el <br> -Nagy kapacitású (Citus) – 99,95% (ha a magas rendelkezésre állás engedélyezve van)|
| <B> Az operációs rendszer és a PostgreSQL javítása | – Ügyfél által felügyelt | – Egyetlen kiszolgáló – automatikus <br> -Rugalmas kiszolgáló – automatikus és opcionális ügyfél által felügyelt ablak <br> -Nagy kapacitású (Citus) – automatikus |
| <B> Magas rendelkezésre állás | – A magas rendelkezésre állást biztosító ügyfelek tervezője, implementálása, tesztelése és karbantartása. A képességek lehetnek fürtözés, replikálás stb. | – Önálló kiszolgáló: beépített <br> -Rugalmas kiszolgáló: beépített <br> -Nagy kapacitású (Citus): beépített készenléti állapottal |
| <B> Zóna redundancia | – Az Azure-beli virtuális gépek különböző rendelkezésre állási zónákban való futtatásra állíthatók be. Helyszíni megoldás esetén az ügyfeleknek saját másodlagos adatközpontot kell létrehozniuk, kezelnie és karbantartani. | – Egyetlen kiszolgáló: nem <br> -Rugalmas kiszolgáló: igen <br> -Nagy kapacitású (Citus): nem |
| <B> Hibrid forgatókönyv | – Ügyfél által felügyelt |-Egyetlen kiszolgáló: olvasási replika <br> -Rugalmas kiszolgáló: az előzetes verzióban nem érhető el <br> -Nagy kapacitású (Citus): nem |
| <B> Biztonsági mentés és visszaállítás | – Ügyfél által felügyelt | – Önálló kiszolgáló: beépített helyi és földrajzi felhasználói konfigurációval <br> -Rugalmas kiszolgáló: beépített felhasználói konfiguráció a zóna-redundáns tároláshoz <br> -Nagy kapacitású (Citus): beépített |
| <B> Adatbázis-műveletek figyelése | – Ügyfél által felügyelt | – Egyetlen kiszolgáló, rugalmas kiszolgáló és nagy kapacitású (Citus): az összes ajánlat lehetővé teszi az ügyfeleknek, hogy riasztásokat állítsanak be az adatbázis-műveletre, és a küszöbértékek elérésekor fellépnek. |
| <B> Komplex veszélyforrások elleni védelem | – Az ügyfeleknek saját maguknak kell elkészíteniük ezt a védelmet. |– Egyetlen kiszolgáló: igen <br> -Rugalmas kiszolgáló: az előzetes verzióban nem érhető el <br> -Nagy kapacitású (Citus): nem |
| <B> Vész-helyreállítás | – Ügyfél által felügyelt | -Egyetlen kiszolgáló: térinformatikai redundáns biztonsági mentés és földrajzi olvasási replika <br> -Rugalmas kiszolgáló: az előzetes verzióban nem érhető el <br> -Nagy kapacitású (Citus): nem |
| <B> Intelligens teljesítmény | – Ügyfél által felügyelt | – Egyetlen kiszolgáló: igen <br> -Rugalmas kiszolgáló: az előzetes verzióban nem érhető el <br> -Nagy kapacitású (Citus): nem |

## <a name="total-cost-of-ownership-tco"></a>Teljes tulajdonlási költség (TCO)

A TCO gyakran az elsődleges szempont, amely meghatározza az adatbázisok üzemeltetésének legjobb megoldását. Ez akkor is igaz, ha kevés pénzzel vagy csapattal rendelkezik egy olyan vállalatnál, amely szűk költségvetési megkötések keretében működik. Ez a szakasz a számlázási és licencelési alapismereteket ismerteti az Azure-ban, mivel azok az Azure-beli virtuális gépek Azure Database for PostgreSQL és PostgreSQL-re vonatkoznak

## <a name="billing"></a>Számlázás

Azure Database for PostgreSQL jelenleg számos különböző szinten érhető el szolgáltatásként, és az erőforrásokra eltérő árak érvényesek. Az összes erőforrás számlázása óránként, rögzített sebességgel történik. A jelenleg támogatott szolgáltatási csomagokra, számítási méretekre és tárolási mennyiségekre vonatkozó legfrissebb információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/postgresql/server/) , amelyekkel a szolgáltatási szintek és a számítási méretek dinamikusan állíthatók be, hogy megfeleljenek az alkalmazás eltérő átviteli igényeinek. A kimenő internetes forgalom díját a normál [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/)alapján számítjuk fel.

A Azure Database for PostgreSQL a Microsoft automatikusan konfigurálja, kijavításokat végez, és frissíti az adatbázis szoftverét. Ezek az automatizált műveletek csökkentik az adminisztrációs költségeket. Emellett a Azure Database for PostgreSQL [automatikus biztonsági mentési kapcsolati]() képességekkel rendelkezik. Ezeknek a képességeknek köszönhetően jelentős költségmegtakarítást érhet el, különösen akkor, ha nagy számú adatbázissal rendelkezik. Ezzel szemben az Azure-beli virtuális gépeken elérhető PostgreSQL-mel bármelyik PostgreSQL-verziót kiválaszthatja és futtathatja. Azonban fizetnie kell a kiépített virtuális gépen, az adattárolási költségekkel, a biztonsági mentéssel, a figyelési és a naplózási szolgáltatással, valamint a használt PostgreSQL-licenccel kapcsolatos költségekkel (ha van ilyen).

A Azure Database for PostgreSQL beépített magas rendelkezésre állást biztosít bármilyen csomópont szintű megszakításhoz, miközben továbbra is fenntartja a szolgáltatás 99,99%-os SLA-garanciáját. A virtuális gépeken belüli magas rendelkezésre állás érdekében azonban a magas rendelkezésre állási lehetőségeket, például a PostgreSQL-adatbázison elérhető [adatfolyam-replikálást](https://www.postgresql.org/docs/12/warm-standby.html#STREAMING-REPLICATION) kell használni. A támogatott magas rendelkezésre állási lehetőség használata nem biztosít további SLA-t. Ugyanakkor további költségekkel és adminisztratív terheléssel több mint 99,99%-os adatbázis-elérhetőséget érhet el.

A díjszabással kapcsolatos további információkért tekintse meg a következő cikkeket:
- [Díjszabás Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/)
- [Virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Felügyelet

Sok vállalat esetében a felhőalapú szolgáltatásba való áttérésre vonatkozó döntés annyit jelent, mint az adminisztráció bonyolultságának kiszervezése.

A IaaS-mel a Microsoft:

- A mögöttes infrastruktúra felügyelete.
- Automatizált javítást biztosít a mögöttes hardverekhez és operációs rendszerekhez

A Microsoft:

- A mögöttes infrastruktúra felügyelete.
- Automatizált javítást biztosít a mögöttes hardverek, az operációs rendszer és az adatbázis-motor számára.
- Az adatbázis magas rendelkezésre állását kezeli.
- A automatikusan végrehajtja a biztonsági mentéseket, és az összes adat replikálásával vész-helyreállítást biztosít.
- Alapértelmezés szerint az inaktív és a mozgásban lévő adatok titkosítása.
- Figyeli a kiszolgálót, és funkciókat biztosít a lekérdezési teljesítményhez és a teljesítménnyel kapcsolatos ajánlásokhoz.

A Azure Database for PostgreSQL használatával továbbra is felügyelheti az adatbázist. Azonban már nincs szükség az adatbázismotor, az operációs rendszer vagy a hardver felügyeletére. Példák a továbbra is felügyelhető elemekre:

- Adatbázisok
- Bejelentkezés
- Index finombeállítása
- Lekérdezés finomhangolása
- Naplózás
- Biztonság

Emellett a magas rendelkezésre állás konfigurálása egy másik adatközponthoz minimálisan szükséges a konfiguráció vagy a felügyelet nélkül.

- Az Azure-beli virtuális gépeken futó PostgreSQL-sel teljes mértékben szabályozhatja az operációs rendszert és a PostgreSQL-kiszolgáló példányának konfigurációját. A virtuális géppel eldöntheti, hogy mikor frissítse vagy frissítse az operációs rendszert és az adatbázis szoftverét, és hogy milyen javításokat kell alkalmaznia. Azt is eldöntheti, hogy mikor telepítsen további szoftvereket, például vírusirtó alkalmazást. Bizonyos automatizált funkciók jelentősen leegyszerűsítik a javítást, a biztonsági mentést és a magas rendelkezésre állást. A virtuális gép méretét, a lemezek számát és a tárolási konfigurációkat is szabályozhatja. További információ: [virtuális gépek és felhőalapú szolgáltatások mérete az Azure](../virtual-machines/sizes.md)-ban.

## <a name="time-to-move-to-azure-postgresql-service-paas"></a>Az Azure PostgreSQL szolgáltatásba való áttérés ideje (Péter)

- A Azure Database for PostgreSQL a megfelelő megoldás a felhőben tervezett alkalmazások számára, ha a fejlesztő és az új megoldások piacra jutásának gyors időpontja kritikus fontosságú. A DBA-hez hasonló programozott funkciókkal a szolgáltatás alkalmas a Felhőbeli építészek és fejlesztők számára, mivel csökkenti a mögöttes operációs rendszer és adatbázis felügyeletének szükségességét.

- Ha el szeretné kerülni az új helyszíni hardver beszerzésének idejét és költségét, a PostgreSQL az Azure-beli virtuális gépeken a megfelelő megoldás olyan alkalmazásokhoz, amelyek a szolgáltatás által nem támogatott, vagy a mögöttes operációs rendszer elérését igénylik a PostgreSQL motor részletes szabályozására és testreszabására.

## <a name="next-steps"></a>Következő lépések

- Lásd: az Azure Database for [PostgreSQL díjszabása](https://azure.microsoft.com/pricing/details/postgresql/server/).
- Első lépésként hozza létre első kiszolgálóját.