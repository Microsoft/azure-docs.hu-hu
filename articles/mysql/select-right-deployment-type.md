---
title: A megfelelő központi telepítési típus kiválasztása – Azure Database for MySQL
description: Ez a cikk azokat a tényezőket ismerteti, amelyeket figyelembe kell vennie, mielőtt üzembe helyezi a Azure Database for MySQL infrastruktúra-szolgáltatásként (IaaS) vagy a szolgáltatásként szolgáló platformként (Pásti).
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 125431e6630ccfdd9e0e5d6b2a4ec5fa9b9e58fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736185"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Válassza ki a megfelelő MySQL-kiszolgáló lehetőséget az Azure-ban

Az Azure-ban a MySQL-kiszolgáló számítási feladatait üzemeltetett virtuálisgép-infrastruktúrában (IaaS) vagy üzemeltetett szolgáltatásként (Pásti) is futtathatják. A Péter több üzembe helyezési lehetőséggel rendelkezik, és az egyes üzembe helyezési lehetőségeken belül vannak szolgáltatási szintek. Ha a IaaS és a Péter közötti választást választja, el kell döntenie, hogy szeretné-e kezelni az adatbázist, a javítások alkalmazásával és a biztonsági másolatok készítésével, vagy ha ezeket a műveleteket az Azure-ba szeretné delegálni.

A döntés meghozatalakor vegye figyelembe a következő két lehetőséget:

- **Azure Database for MySQL**. Ez a lehetőség egy teljes körűen felügyelt MySQL-adatbázismotor, amely a MySQL Community Edition stabil verzióján alapul. Ez az Azure-beli felhőalapú platformon üzemelő, szolgáltatásként (DBaaS) elérhető, a (z) és a

  Ha felügyelt MySQL-példányt használ az Azure-ban, beépített funkciókkal rendelkezik, mint például az automatikus javítás, a magas rendelkezésre állás, az automatizált biztonsági másolatok, a rugalmas skálázás, a nagyvállalati szintű biztonság, a megfelelőség és az irányítás, a figyelés és a riasztás, amely egyébként a MySQL-kiszolgáló helyszíni vagy Azure-beli virtuális gépeken való használatának megkövetelése. Ha a MySQL-t szolgáltatásként használja, az utólagos elszámolású, és a vertikális felskálázást és a vertikális felskálázást is megszakíthatja.
  
  A MySQL Community Edition által működtetett [Azure Database for MySQL](overview.md)két üzembe helyezési módban érhető el:

  - Az [egyetlen kiszolgáló](single-server-overview.md) egy teljes körűen felügyelt adatbázis-szolgáltatás, amely minimális követelményeket támaszt az adatbázis testreszabásához. Az egykiszolgálós platform úgy lett kialakítva, hogy az adatbázis-kezelési funkciók, például a javítások, a biztonsági mentések, a magas rendelkezésre állás, a biztonság minimális felhasználói konfigurációval és vezérléssel kezelhető legyen. Az architektúra úgy van optimalizálva, hogy 99,99%-os rendelkezésre állást biztosítson egyetlen rendelkezésre állási zónában. Az egyetlen kiszolgáló a legjobb megoldás a Felhőbeli natív alkalmazásokhoz, amelyek az automatikus javítás kezelésére szolgálnak anélkül, hogy a javítási ütemterv és az egyéni MySQL konfigurációs beállítások részletes szabályozására lenne szükség.

  - A [rugalmas kiszolgáló (előzetes verzió)](flexible-server/overview.md) egy teljes körűen felügyelt adatbázis-szolgáltatás, amely részletesebb szabályozást és rugalmasságot biztosít az adatbázis-felügyeleti funkciók és a konfigurációs beállítások számára. A szolgáltatás általában nagyobb rugalmasságot és kiszolgáló-konfigurációt biztosít az egykiszolgálós telepítéshez képest a felhasználói követelmények alapján. A rugalmas kiszolgáló architektúrája lehetővé teszi a felhasználók számára, hogy egyetlen rendelkezésre állási zónában és több rendelkezésre állási zónában is magas rendelkezésre állást engedélyezzenek. A rugalmas kiszolgálók jobb költség-optimalizálási szabályozást is biztosítanak, amely lehetővé teszi a kiszolgáló és a feltört SKU-alkalmazások indítását és leállítását, ideális olyan számítási feladatokhoz, amelyeknek nincs szükségük teljes számítási kapacitásra.

  A rugalmas kiszolgálók legmegfelelőbbek a következőkhöz:

  - Alkalmazásfejlesztés a MySQL-motor jobb vezérléséhez és testreszabásához.
  - Zóna redundáns magas rendelkezésre állása
  - Felügyelt karbantartási időszakok

- **MySQL Azure-beli virtuális gépeken**. Ez a lehetőség a IaaS iparági kategóriába tartozik. Ezzel a szolgáltatással a MySQL-kiszolgálót a felügyelt virtuális gépeken belül futtathatja az Azure Cloud platformon. A MySQL legújabb verzióit és kiadásait telepítheti a virtuális gépre.

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>A MySQL üzembe helyezési lehetőségeinek összehasonlítása az Azure-ban

A fenti lehetőségek közötti fő különbségek a következő táblázatban láthatók:

| Attribútum          | Azure Database for MySQL<br/>Önálló kiszolgáló |Azure Database for MySQL<br/>Rugalmas kiszolgáló  |MySQL Azure-beli virtuális gépeken                      |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:---------------------------------------|
| MySQL-verzió támogatása | 5,6, 5,7 & 8,0| 5,7 & 8,0 | Bármely verzió|
| Számítási skálázás | Támogatott (a és az alapszintű csomagra való skálázás nem támogatott)| Támogatott | Támogatott|
| Tárterület mérete | 5 GiB – 16 TiB| 5 GiB – 16 TiB | 32 GiB – 32 767 GiB|
| Online tárterület skálázása | Támogatott| Támogatott| Nem támogatott|
| Automatikus tárolás skálázása | Támogatott| Előzetes verzióban nem támogatott| Nem támogatott|
| További IOPs skálázás | Nem támogatott| Támogatott| Nem támogatott|
| Hálózati kapcsolat | – Nyilvános végpontok kiszolgálói tűzfallal.<br/> – Privát hozzáférés a privát kapcsolat támogatásával.|– Nyilvános végpontok kiszolgálói tűzfallal.<br/> – Privát hozzáférés Virtual Network integrációval.| – Nyilvános végpontok kiszolgálói tűzfallal.<br/> – Privát hozzáférés a privát kapcsolat támogatásával.|
| Szolgáltatói szerződés (SLA) | 99,99%-os rendelkezésre állási SLA |Előzetes verzióban nem érhető el SLA| 99,99% a Availability Zones használatával|
| Operációs rendszer javítása| Automatikus  | Automatikus egyéni karbantartási időszak vezérlésével | Végfelhasználók által felügyelt |
| MySQL-javítás     | Automatikus  | Automatikus egyéni karbantartási időszak vezérlésével | Végfelhasználók által felügyelt |
| Magas rendelkezésre állás | Az egyetlen rendelkezésre állási zónán belül beépített HA| Beépített HA a rendelkezésre állási zónákon belül és között van | A fürtözés, a replikálás stb. használatával kezelt egyéni felügyelet|
| Zónaredundancia | Nem támogatott | Támogatott | Támogatott|
| Zóna elhelyezése | Nem támogatott | Támogatott | Támogatott|
| Hibrid forgatókönyvek | Támogatott [felhőbe irányuló replikálás](./concepts-data-in-replication.md)| Nem érhető el előzetes verzióban | Végfelhasználók által felügyelt |
| Olvasási replikák | Támogatott (legfeljebb 5 replika)| Támogatott (legfeljebb 10 replika)| Végfelhasználók által felügyelt |
| Backup | Automatizált, 7-35 napos megőrzéssel | Automatizált, 1-35 napos megőrzéssel | Végfelhasználók által felügyelt |
| Adatbázis-műveletek figyelése | Támogatott | Támogatott | Végfelhasználók által felügyelt |
| Vészhelyreállítás | A Geo-redundáns biztonsági mentési tár és a régiók közötti olvasási replikák támogatása | Előzetes verzióban nem támogatott| Egyéni felügyelt replikációs technológiákkal |
| Lekérdezési teljesítmény elemzése | Támogatott | Nem érhető el előzetes verzióban| Végfelhasználók által felügyelt |
| Fenntartott példány díjszabása | Támogatott | Nem érhető el előzetes verzióban | Támogatott |
| Azure AD-hitelesítés | Támogatott | Nem érhető el előzetes verzióban | Nem támogatott|
| Inaktív adatok titkosítása | Ügyfél által felügyelt kulcsokkal támogatott | Szolgáltatás által felügyelt kulcsokkal támogatott | Nem támogatott|
| SSL/TLS | Alapértelmezés szerint engedélyezve van a TLS v 1.2, 1,1 és 1,0 támogatással | Kikényszerítve a TLS 1.2-es verzióval | A TLS 2.0-s, 1,1-es és 1,0-es verzióval támogatott |
| A flotta kezelése | Az Azure CLI, a PowerShell, a REST és a Azure Resource Manager támogatása | Az Azure CLI, a PowerShell, a REST és a Azure Resource Manager támogatása  | Az Azure CLI, a PowerShell, a REST és a Azure Resource Manager rendszerű virtuális gépek esetében támogatott |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>A Pásti vagy a IaaS kiválasztásának üzleti motivációi

A MySQL-adatbázisok üzemeltetéséhez több tényező is befolyásolhatja a IaaS-t vagy a kiválasztását.

### <a name="cost"></a>Költségek

A költségcsökkentés gyakran az elsődleges szempont, amely meghatározza az adatbázisok üzemeltetésének legjobb megoldását. Ez akkor is igaz, ha kevés pénzzel vagy csapattal rendelkezik egy olyan vállalatnál, amely szűk költségvetési megkötések keretében működik. Ez a szakasz a számlázási és licencelési alapismereteket ismerteti az Azure-ban, mivel azok az Azure-beli virtuális gépeken a Azure Database for MySQL és a MySQL

#### <a name="billing"></a>Számlázás

Azure Database for MySQL jelenleg számos különböző szinten érhető el szolgáltatásként, és az erőforrásokra eltérő árak érvényesek. Az összes erőforrás számlázása óránként, rögzített sebességgel történik. A jelenleg támogatott szolgáltatási szintek, számítási méretek és tárolási összegek legfrissebb információit a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/mysql/)tekintheti meg. A szolgáltatási szintek és a számítási méretek dinamikusan állíthatók be úgy, hogy megfeleljenek az alkalmazás eltérő átviteli igényeinek. A kimenő internetes forgalom díját a normál [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/)alapján számítjuk fel.

A Azure Database for MySQL a Microsoft automatikusan konfigurálja, kijavításokat végez, és frissíti az adatbázis szoftverét. Ezek az automatizált műveletek csökkentik az adminisztrációs költségeket. Azure Database for MySQL a [biztonsági mentések képességei is automatizáltak](./concepts-backup.md) . Ezeknek a képességeknek köszönhetően jelentős költségmegtakarítást érhet el, különösen akkor, ha nagy számú adatbázissal rendelkezik. Ezzel szemben az Azure-beli virtuális gépeken futó MySQL-sel bármelyik MySQL-verziót kiválaszthatja és futtathatja. Függetlenül attól, hogy milyen MySQL-verziót használ, fizetnie kell a kiépített virtuális gépen, az adattárolási költségekkel, a biztonsági mentéssel, a figyeléssel és a naplózási szolgáltatással, valamint a használt MySQL-licenccel kapcsolatos költségekkel (ha van ilyen).

A Azure Database for MySQL beépített magas rendelkezésre állást biztosít bármilyen csomópont szintű megszakításhoz, miközben továbbra is fenntartja a szolgáltatás 99,99%-os SLA-garanciáját. Az adatbázisok magas rendelkezésre állása esetén azonban a virtuális gépeken a magas rendelkezésre állási lehetőségeket, például a MySQL-adatbázison elérhető [MySQL-replikációt](https://dev.mysql.com/doc/refman/8.0/en/replication.html) kell használni. A támogatott magas rendelkezésre állási lehetőség használata nem biztosít további SLA-t. Ugyanakkor további költségekkel és adminisztratív terheléssel több mint 99,99%-os adatbázis-elérhetőséget érhet el.

A díjszabással kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Díjszabás Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/)
- [Virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Felügyelet

Sok vállalat esetében a felhőalapú szolgáltatásba való áttérésre vonatkozó döntés annyit jelent, mint az adminisztráció bonyolultságának kiszervezése.

A IaaS-mel a Microsoft:

- A mögöttes infrastruktúra felügyelete.
- Automatizált javítást biztosít a mögöttes hardverek és operációs rendszerek számára.
  
A Microsoft:

- A mögöttes infrastruktúra felügyelete.
- Automatizált javítást biztosít a mögöttes hardver, az operációs rendszer és az adatbázis motorja számára.
- Az adatbázis magas rendelkezésre állását kezeli.
- A automatikusan végrehajtja a biztonsági mentéseket, és az összes adat replikálásával vész-helyreállítást biztosít.
- Alapértelmezés szerint az inaktív és a mozgásban lévő adatok titkosítása.
- Figyeli a kiszolgálót, és funkciókat biztosít a lekérdezési teljesítményhez és a teljesítménnyel kapcsolatos ajánlásokhoz

A következő lista az egyes lehetőségek felügyeleti szempontjait ismerteti:

- A Azure Database for MySQL használatával továbbra is felügyelheti az adatbázist. Azonban már nincs szükség az adatbázismotor, az operációs rendszer vagy a hardver felügyeletére. Példák a továbbra is felügyelhető elemekre:

  - Adatbázisok
  - Bejelentkezés
  - Index finombeállítása
  - Lekérdezés finomhangolása
  - Naplózás
  - Biztonság

  Emellett a magas rendelkezésre állás konfigurálása egy másik adatközponthoz minimálisan szükséges a konfiguráció vagy a felügyelet nélkül.

- Az Azure-beli virtuális gépeken futó MySQL-sel teljes mértékben szabályozhatja az operációs rendszert és a MySQL-kiszolgáló példányának konfigurációját. A virtuális géppel eldöntheti, hogy mikor frissítse vagy frissítse az operációs rendszert és az adatbázis szoftverét, és hogy milyen javításokat kell alkalmaznia. Azt is eldöntheti, hogy mikor telepítsen további szoftvereket, például vírusirtó alkalmazást. Bizonyos automatizált funkciók jelentősen leegyszerűsítik a javítást, a biztonsági mentést és a magas rendelkezésre állást. A virtuális gép méretét, a lemezek számát és a tárolási konfigurációkat is szabályozhatja. További információ: [virtuális gépek és felhőalapú szolgáltatások mérete az Azure](../virtual-machines/sizes.md)-ban.

### <a name="time-to-move-to-azure"></a>Az Azure-ba való áttérés ideje

- A Azure Database for MySQL a megfelelő megoldás a felhőben tervezett alkalmazások számára, ha a fejlesztő és az új megoldások piacra jutásának gyors időpontja kritikus fontosságú. A DBA-hez hasonló programozott funkciókkal a szolgáltatás alkalmas a Felhőbeli építészek és fejlesztők számára, mivel csökkenti a mögöttes operációs rendszer és adatbázis felügyeletének szükségességét.

- Ha el szeretné kerülni az új helyszíni hardver beszerzésének idejét és költségét, a MySQL az Azure-beli virtuális gépeken a megfelelő megoldás olyan alkalmazásokhoz, amelyek a szolgáltatás által nem támogatott, illetve a mögöttes operációs rendszer elérését igénylik a MySQL motor részletes szabályozására és testreszabására. Ez a megoldás alkalmas a meglévő helyszíni alkalmazások és adatbázisok az Azure-ba való áttelepítésére is, olyan esetekben, amikor a Azure Database for MySQL gyenge illeszkedést biztosít.

Mivel nincs szükség a bemutató, az alkalmazás és az adatrétegek módosítására, időt és költségvetést takaríthat meg a meglévő megoldás újratervezése során. Ehelyett a megoldások Azure-ba való áttelepítésére és az Azure-platform által igényelt teljesítmény-optimalizálásokra koncentrálhat.

## <a name="next-steps"></a>Következő lépések

- Lásd: [Azure Database for MySQL díjszabása](https://azure.microsoft.com/pricing/details/MySQL/).
- Első lépésként [hozza létre első kiszolgálóját](./quickstart-create-mysql-server-database-using-azure-portal.md).
