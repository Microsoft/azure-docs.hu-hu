---
title: A megfelelő központi telepítési típus kiválasztása – Azure Database for MariaDB
description: Ez a cikk azokat a tényezőket ismerteti, amelyeket figyelembe kell vennie, mielőtt üzembe helyezi a Azure Database for MariaDB infrastruktúra-szolgáltatásként (IaaS) vagy a szolgáltatásként szolgáló platformként (Pásti).
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d8885e374142b3d916803fc472ae18351ca6d470
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664518"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Válassza ki a megfelelő MariaDB-kiszolgáló lehetőséget az Azure-ban

Az Azure-ban a MariaDB-kiszolgáló munkaterhelései egy üzemeltetett virtuálisgép-infrastruktúrában (IaaS) vagy üzemeltetett szolgáltatásként (Pásti) is futhatnak. A Péter több üzembe helyezési lehetőséggel rendelkezik, és az egyes üzembe helyezési lehetőségeken belül vannak szolgáltatási szintek. Ha a IaaS és a Péter közötti választást választja, el kell döntenie, hogy szeretné-e kezelni az adatbázist, a javítások alkalmazásával és a biztonsági másolatok készítésével, vagy ha ezeket a műveleteket az Azure-ba szeretné delegálni.

A döntés meghozatalakor vegye figyelembe a következő két lehetőséget:

- **Azure Database for MariaDB:** Ez a lehetőség egy teljes körűen felügyelt MariaDB-adatbázismotor, amely a MariaDB Community Edition stabil verziójára épül. Ez az Azure-beli felhőalapú platformon üzemelő, szolgáltatásként (DBaaS) elérhető, a (z) és a

  Az Azure-beli MariaDB felügyelt példányaival olyan beépített funkciókat használhat, amelyek egyébként nagy konfigurációt igényelnek, ha a MariaDB-kiszolgáló vagy egy Azure-beli virtuális gépen van.

  A MariaDB szolgáltatásként való használatakor a megszakadás és a vertikális felskálázás érdekében a megszakítás nélkül kell fizetnie. Az önálló MariaDB-kiszolgálótól eltérően a Azure Database for MariaDB olyan további funkciókat is tartalmaz, mint például a beépített magas rendelkezésre állás, az intelligencia és a felügyelet.

- **MariaDB az Azure-beli virtuális gépeken:** Ez a lehetőség a IaaS iparági kategóriába tartozik. Ezzel a szolgáltatással a MariaDB-kiszolgálót egy teljes körűen felügyelt virtuális gépen futtathatja az Azure Cloud platformon. A MariaDB legújabb verzióit és kiadásait IaaS virtuális gépre lehet telepíteni.

  Azure Database for MariaDB az Azure-beli virtuális gépek MariaDB legjelentősebb eltérése biztosítja az adatbázismotor vezérlését. Ez a szabályozás azonban a virtuális gépek és számos adatbázis-felügyeleti (DBA) feladat felügyeletének a felelősségi körébe tartozik. Ezen feladatok közé tartozik az adatbázis-kiszolgálók, az adatbázis-helyreállítás és a magas rendelkezésre állású kialakítás fenntartása és javítása.

A fenti lehetőségek közötti fő különbségek a következő táblázatban láthatók:

| Attribútum          | Azure Database for MariaDB | MariaDB az Azure-beli virtuális gépeken    |
|:-------------------|:-----------------------------|:--------------------|
| Szolgáltatói szerződés (SLA)                | A 99,99%-os rendelkezésre állást biztosító SLA-t kínál| Akár 99,95%-os rendelkezésre állást biztosít két vagy több példányban ugyanabban a rendelkezésre állási csoporton belül.<br/><br/>99,9%-os rendelkezésre állás egyetlen példányos virtuális géppel Premium Storage használatával.<br/><br/>99,99% a több rendelkezésre állási csoport több példányával rendelkező Availability Zones használatával.<br/><br/>Lásd a [Virtual Machines SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)-t. |
| Operációs rendszer javítása        | Automatikus  | Ügyfelek által felügyelt |
| MariaDB-javítás     | Automatikus  | Ügyfelek által felügyelt |
| Magas rendelkezésre állás | A magas rendelkezésre állású (HA) modell olyan beépített feladatátvételi mechanizmusokon alapul, amelyekben csomópont szintű megszakítás történik. Ilyen esetekben a szolgáltatás automatikusan létrehoz egy új példányt, és csatolja a tárolót ehhez a példányhoz. | Az ügyfelek a magas rendelkezésre állás kialakítását, tesztelését és karbantartását is elismerik. A képességek lehetnek többek között a feladatátvételi fürtszolgáltatás, a folyamatos replikálás, a naplózás vagy a tranzakciós replikáció.|
| Zónaredundancia | Jelenleg nem támogatott | Az Azure-beli virtuális gépek különböző rendelkezésre állási zónákban való futtatásra állíthatók be. Helyszíni megoldás esetén az ügyfeleknek saját másodlagos adatközpontot kell létrehozniuk, kezelnie és karbantartani.|
| Hibrid forgatókönyvek | A [felhőbe irányuló replikálás](concepts-data-in-replication.md)segítségével egy külső MariaDB-kiszolgáló adatait szinkronizálhatja a Azure Database for MariaDB szolgáltatásba. A külső kiszolgáló lehet helyszíni, virtuális gépek vagy más felhőalapú szolgáltatók által üzemeltetett adatbázis-szolgáltatás.<br/><br/> A [replika olvasása](concepts-read-replicas.md) funkcióval egy Azure Database for MariaDB forráskiszolgáló adatait akár öt írásvédett replika kiszolgálóra is replikálhatja. A replikák vagy ugyanabban az Azure-régióban vagy régiókban találhatók. Az írásvédett replikák aszinkron módon frissülnek a BinLog replikációs technológiával.<br/><br/>A régiók közötti olvasási replikálás jelenleg nyilvános előzetes verzióban érhető el.| Ügyfelek által felügyelt
| Biztonsági mentés és helyreállítás | A automatikusan létrehozza a [kiszolgáló biztonsági másolatait](concepts-backup.md#backups) , és tárolja azokat a helyileg redundáns vagy földrajzilag redundáns, felhasználó által konfigurált tárolóban. A szolgáltatás teljes, differenciális és tranzakciónapló-alapú biztonsági mentéseket tart | Ügyfelek által felügyelt |
| Adatbázis-műveletek figyelése | A lehetővé teszi az ügyfeleknek, hogy [riasztásokat állítsanak be](concepts-monitoring.md) az adatbázis-műveletre, és a küszöbértékek elérésekor járjanak. | Ügyfelek által felügyelt |
| Advanced Threat Protection | [Fejlett veszélyforrások elleni védelmet](howto-database-threat-protection-portal.md)nyújt. Ez a védelem olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.<br/><br/>A komplex veszélyforrások elleni védelem jelenleg nyilvános előzetes verzióban érhető el.| Az ügyfeleknek saját maguknak kell elkészíteniük ezt a védelmet.
| Vészhelyreállítás | Az automatizált biztonsági mentéseket a felhasználó által konfigurált [helyileg redundáns vagy földrajzilag redundáns tárolóban](howto-restore-server-portal.md)tárolja. A biztonsági mentések egy adott időpontban is visszaállíthatják a kiszolgálót. A megőrzési időtartam 7 és 35 nap között van. A visszaállítás a Azure Portal használatával valósul meg. | Az ügyfelek teljes körűen kezelik. A felelősség magában foglalja az ütemezési, tesztelési, archiválási, tárolási és adatmegőrzési feladatokat. Egy másik lehetőség egy Azure Recovery Services-tároló használata az Azure-beli virtuális gépek és adatbázisok biztonsági mentésére a virtuális gépeken. Ez a beállítás előzetes verzióban érhető el. |
| Teljesítménnyel kapcsolatos javaslatok | [Teljesítményre vonatkozó javaslatokat](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) biztosít az ügyfeleknek a rendszer által létrehozott használati naplófájlok alapján. A javaslatok segítenek optimalizálni a munkaterheléseket.<br/><br/>A teljesítménnyel kapcsolatos javaslatok jelenleg nyilvános előzetes verzióban érhetők el. | Ügyfelek által felügyelt |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>A Pásti vagy a IaaS kiválasztásának üzleti motivációi

A MariaDB-adatbázisok üzemeltetéséhez számos tényező befolyásolhatja a saját döntését, hogy kiválassza a Péter vagy a IaaS lehetőséget.

### <a name="cost"></a>Költségek

A korlátozott finanszírozás gyakran az elsődleges szempont, amely meghatározza az adatbázisok üzemeltetésének legjobb megoldását. Ez akkor is igaz, ha kevés pénzzel vagy csapattal rendelkezik egy olyan vállalatnál, amely szűk költségvetési megkötések keretében működik. Ez a szakasz a számlázási és licencelési alapismereteket ismerteti az Azure-ban, mivel azok az Azure-beli virtuális gépek Azure Database for MariaDB és MariaDB vonatkoznak.

#### <a name="billing"></a>Számlázás

Azure Database for MariaDB jelenleg számos különböző szinten érhető el szolgáltatásként, és az erőforrásokra eltérő árak érvényesek. Az összes erőforrás számlázása óránként, rögzített sebességgel történik. A jelenleg támogatott szolgáltatási rétegekkel, számítási méretekkel és tárolási mennyiségekkel kapcsolatos legfrissebb információkért lásd: [virtuális mag-alapú vásárlási modell](concepts-pricing-tiers.md). A szolgáltatási szintek és a számítási méretek dinamikusan állíthatók be úgy, hogy megfeleljenek az alkalmazás eltérő átviteli igényeinek. A kimenő internetes forgalom díját a normál [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/)alapján számítjuk fel.

A Azure Database for MariaDB a Microsoft automatikusan konfigurálja, kijavításokat végez, és frissíti az adatbázis szoftverét. Ezek az automatizált műveletek csökkentik az adminisztrációs költségeket. Emellett a Azure Database for MariaDB [beépített biztonsági mentési](concepts-backup.md) képességekkel rendelkezik. Ezeknek a képességeknek köszönhetően jelentős költségmegtakarítást érhet el, különösen akkor, ha nagy számú adatbázissal rendelkezik. Ezzel szemben az Azure-beli virtuális gépeken futó MariaDB-mel bármely MariaDB-verziót kiválaszthat és futtathat. Függetlenül attól, hogy milyen MariaDB-verziót használ, fizetnie kell a kiépített virtuális gépen, valamint a felhasznált MariaDB-licencek díját.

A Azure Database for MariaDB beépített magas rendelkezésre állást biztosít bármilyen csomópont szintű megszakításhoz, miközben továbbra is fenntartja a szolgáltatás 99,99%-os SLA-garanciáját. A virtuális gépeken belüli magas rendelkezésre állás érdekében azonban az ügyfeleknek a magas rendelkezésre állási lehetőségeket, például a MariaDB-adatbázison elérhető [MariaDB-replikációt](https://mariadb.com/kb/en/library/setting-up-replication/) kell használniuk. A támogatott magas rendelkezésre állási lehetőség használata nem biztosít további SLA-t. Ugyanakkor további költségekkel és adminisztratív terheléssel több mint 99,99%-os adatbázis-elérhetőséget érhet el.

A díjszabással kapcsolatos további információkért tekintse meg a következő cikkeket:
* [Díjszabás Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Felügyelet

Sok vállalat esetében a felhőalapú szolgáltatásba való áttérésre vonatkozó döntés annyit jelent, mint az adminisztráció bonyolultságának kiszervezése. A IaaS és a Pásti, a Microsoft:

- A mögöttes infrastruktúra felügyelete.
- A automatikusan replikálja az összes adatforrást a vész-helyreállítás biztosításához.
- Az adatbázis szoftverének konfigurálása és frissítése.
- Kezeli a terheléselosztást.
- Transzparens feladatátvételt hajt végre, ha kiszolgálóhiba történt.

A következő lista az egyes lehetőségek felügyeleti szempontjait ismerteti:

* A Azure Database for MariaDB használatával továbbra is felügyelheti az adatbázist. Azonban már nincs szükség az adatbázismotor, az operációs rendszer vagy a hardver felügyeletére. Példák a továbbra is felügyelhető elemekre:

  - Adatbázisok
  - Bejelentkezés
  - Index finombeállítása
  - Lekérdezés finomhangolása
  - Naplózás
  - Biztonság

  Emellett a magas rendelkezésre állás konfigurálása egy másik adatközponthoz minimálisan szükséges a konfiguráció vagy a felügyelet nélkül.

* Az Azure-beli virtuális gépeken futó MariaDB teljes mértékben szabályozhatja az operációs rendszert és a MariaDB-kiszolgáló példányának konfigurációját. A virtuális géppel eldöntheti, hogy mikor frissítse vagy frissítse az operációs rendszert és az adatbázis szoftverét. Azt is eldöntheti, hogy mikor telepítsen további szoftvereket, például vírusirtó alkalmazást. Bizonyos automatizált funkciók jelentősen leegyszerűsítik a javítást, a biztonsági mentést és a magas rendelkezésre állást. A virtuális gép méretét, a lemezek számát és a tárolási konfigurációkat is szabályozhatja. További információ: [virtuális gépek és felhőalapú szolgáltatások mérete az Azure](../virtual-machines/sizes.md)-ban.

### <a name="time-to-move-to-azure"></a>Az Azure-ba való áttérés ideje

* A Azure Database for MariaDB a megfelelő megoldás a felhőben tervezett alkalmazások számára, ha a fejlesztő és az új megoldások piacra jutásának gyors időpontja kritikus fontosságú. A DBA-hez hasonló programozott funkciókkal a szolgáltatás alkalmas a Felhőbeli építészek és fejlesztők számára, mivel csökkenti a mögöttes operációs rendszer és adatbázis felügyeletének szükségességét.

* Ha el szeretné kerülni az új helyszíni hardver beszerzésének idejét és költségét, a MariaDB az Azure-beli virtuális gépeken a megfelelő megoldás olyan alkalmazások esetében, amelyek MariaDB-adatbázist igényelnek, vagy Windows vagy Linux rendszerű MariaDB-funkciókhoz férnek hozzá. Ez a megoldás alkalmas a meglévő helyszíni alkalmazások és adatbázisok az Azure-ba való áttelepítésére is, olyan esetekben, amikor a Azure Database for MariaDB gyenge illeszkedést biztosít.

  Mivel nincs szükség a bemutató, az alkalmazás és az adatrétegek módosítására, időt és költségvetést takaríthat meg a meglévő megoldás újratervezése során. Ehelyett a megoldások Azure-ba való áttelepítésére és az Azure-platform által igényelt teljesítmény-optimalizálásokra koncentrálhat.

## <a name="next-steps"></a>Következő lépések

* Lásd: [Azure Database for MariaDB díjszabása](https://azure.microsoft.com/pricing/details/MariaDB/).
* Első lépésként [hozza létre első kiszolgálóját](quickstart-create-mariadb-server-database-using-azure-portal.md).
