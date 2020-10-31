---
title: Magas rendelkezésre állás a Azure Cosmos DBban
description: Ez a cikk azt ismerteti, hogy a Azure Cosmos DB hogyan biztosít magas rendelkezésre állást
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 2fb8b24d5d44ced8f9e363008354acf5bc2fde40
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081875"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Hogyan biztosítja a Azure Cosmos DB magas rendelkezésre állást
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB két elsődleges módon biztosít magas rendelkezésre állást. Először Azure Cosmos DB a Cosmos-fiókon belül konfigurált régiók között replikálja az adathalmazt. Másodszor Azure Cosmos DB a régión belül 4 replikát tart fenn.

A Azure Cosmos DB egy globálisan elosztott adatbázis-szolgáltatás, amely az Azure egyik alapszolgáltatása. Alapértelmezés szerint minden olyan régióban elérhető, [ahol az Azure elérhető](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). Tetszőleges számú Azure-régiót társíthat az Azure Cosmos-fiókjához, és az adatai automatikusan és transzparens módon replikálódnak. Bármikor hozzáadhat vagy eltávolíthat egy régiót az Azure Cosmos-fiókjához. A Cosmos DB az ügyfelek számára elérhető öt különböző Azure Cloud-környezetben elérhető:

* **Azure nyilvános** felhő, amely globálisan elérhető.

* Az **Azure China 21Vianet** a Microsoft és a 21Vianet közötti egyedi partneri kapcsolaton keresztül érhető el, amely az ország egyik legnagyobb internetszolgáltatója Kínában.

* Az **Azure Germany** egy adatkezelői modell keretében nyújt szolgáltatásokat, amely biztosítja, hogy az ügyféladatok Németországban maradnak a T-Systems International GmbH, a Deutsche Telekom leányvállalata, a német adatkezelő nevében.

* **Azure Government** a Egyesült Államok négy régiójában érhető el az USA kormányzati szervei és azok partnerei számára.

* A **védelmi minisztérium (DoD) Azure Government** a Egyesült Államok két régiójában érhető el az USA védelmi Minisztériuma számára.

Egy adott régión belül Azure Cosmos DB négy példányban tárolja adatait replikaként a fizikai partíciókon, ahogy az alábbi képen is látható:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Fizikai particionálás" border="false":::

* Az Azure Cosmos-tárolókban lévő adatkeretek [horizontálisan particionálva](partitioning-overview.md)vannak.

* A partíciók készlete több replika-készlet gyűjteménye. Minden egyes régión belül minden partíciót egy replikával véd, és a replikák többsége által véglegesített összes írás replikálva és tartósan. A replikák több mint 10-20 tartalék tartományba vannak elosztva.

* Az összes régió összes partíciója replikálódik. Mindegyik régió egy Azure Cosmos-tároló összes adatpartícióját tartalmazza, és írási és olvasási műveleteket is képes fogadni.  

Ha az Azure Cosmos-fiókját *N* Azure-régióban osztják el, az összes adatnak legalább *N* x 4 példánya lesz. Ha több mint 2 régióban rendelkezik Azure Cosmos-fiókkal, az alkalmazás rendelkezésre állását növeli, és a kapcsolódó régiókban kis késleltetést biztosít.

## <a name="slas-for-availability"></a>SLA-kat a rendelkezésre álláshoz

Globálisan elosztott adatbázisként Azure Cosmos DB átfogó SLA-kat biztosít, amelyek az átviteli sebességre, a esetek 99% percentilis, a konzisztencia és a magas rendelkezésre állásra vonatkozó késést foglalnak magukban. Az alábbi táblázat az egyes és a többrégiós fiókok Azure Cosmos DB által biztosított magas rendelkezésre állási garanciákat mutatja be. A magas rendelkezésre állás érdekében mindig konfigurálja az Azure Cosmos-fiókokat több írási régióval.

|Művelettípus  | Egyetlen régió |Több régió (egyrégiós írások)|Többrégiós (több régiós írások) |
|---------|---------|---------|-------|
|Írások    | 99,99    |99,99   |99,999|
|Olvasások     | 99,99    |99,999  |99,999|

> [!NOTE]
> A gyakorlatban a kötött elavulás, munkamenet, konzisztens előtag és végleges konzisztencia-modell tényleges írási rendelkezésre állása jelentősen meghaladja a közzétett SLA-kat. Az összes konzisztencia tényleges olvasási rendelkezésre állása jelentősen meghaladja a közzétett SLA-kat.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Magas rendelkezésre állás a Azure Cosmos DB a regionális kimaradások esetén

A regionális leállás ritka eseteiben Azure Cosmos DB biztosítja, hogy az adatbázis mindig rendelkezésre álljon. Az alábbi részletek az Azure Cosmos-fiók konfigurációjától függően a leállás során Azure Cosmos DB viselkedést rögzítik:

* Ha Azure Cosmos DB, az írási művelet elfogadását megelőzően az ügyfél számára az adatok tartósan a régióban lévő replikák kvóruma végzi el, amely elfogadja az írási műveleteket. További részletek: [konzisztencia-szintek és átviteli sebesség](./consistency-levels.md#consistency-levels-and-throughput)

* A több írható régióval konfigurált többrégiós fiókok esetében az írások és olvasások egyaránt nagyon elérhetők lesznek. A rendszer a regionális feladatátvételeket észleli és kezeli a Azure Cosmos DB-ügyfélen. Emellett azonnali, és nem igényelnek semmilyen változást az alkalmazásban.

* Az egyrégiós fiókok a regionális leállás után elveszíthetik a rendelkezésre állást. A magas rendelkezésre állás biztosítása érdekében mindig ajánlott **legalább két régiót** (lehetőleg legalább két írási régiót) beállítani az Azure Cosmos-fiókkal.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Többrégiós fiókok egyetlen írási régióval (írási régió kimaradása)

* Az írási régió meghibásodása során az Azure Cosmos-fiók automatikusan előléptet egy másodlagos régiót az új elsődleges írási régiónak, ha az **automatikus feladatátvétel engedélyezése** az Azure Cosmos-fiókban be van állítva. Ha ez a beállítás engedélyezve van, a feladatátvétel egy másik régióba kerül a megadott régió-prioritás sorrendjében.

* Ha a korábban érintett régió újra online állapotba került, az [ütközések csatornán](how-to-manage-conflicts.md#read-from-conflict-feed)keresztül elérhetővé tett írási és olvasási műveletek a régió hibája miatt nem replikálódnak. Az alkalmazások elolvashatják az ütközések csatornáját, elhárítják az alkalmazás-specifikus logikán alapuló ütközéseket, és szükség szerint visszaírják a frissített információt az Azure Cosmos-tárolóba.

* A korábban érintett írási régió helyreállítása után a rendszer automatikusan elérhetővé válik olvasási régióként. Az írási régióként visszaválthat a visszaállított régióra. A régiókat a [PowerShell, az Azure CLI vagy a Azure Portal](how-to-manage-database-account.md#manual-failover)használatával állíthatja át. Az írási régió és az alkalmazás továbbra is rendelkezésre áll, amíg az **adatvesztés és a rendelkezésre állás még nem** érhető el.

> [!IMPORTANT]
> Határozottan javasoljuk, hogy az **automatikus feladatátvétel engedélyezéséhez** konfigurálja az éles számítási feladatokhoz használt Azure Cosmos-fiókokat. A manuális feladatátvételhez kapcsolat szükséges a másodlagos és az elsődleges írási régió között a konzisztencia-ellenőrzés elvégzéséhez, hogy a feladatátvétel során ne legyen adatvesztés. Ha az elsődleges régió nem érhető el, ez a konzisztencia-ellenőrzés nem hajtható végre, és a manuális feladatátvétel nem fog sikerülni, ami a regionális leállás időtartamára való írási rendelkezésre állás elvesztését eredményezi.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Többrégiós fiókok egyetlen írási régióval (olvasási régió kimaradása)

* Az olvasási régió meghibásodása esetén az Azure Cosmos-fiókok bármely konzisztencia-szintet, vagy a három vagy több olvasási régióval való erős konzisztencia esetén is magas rendelkezésre állást biztosítanak az olvasáshoz és íráshoz.

* Az Azure Cosmos-fiókok erős konzisztencia használatával két vagy kevesebb olvasási régióval rendelkeznek (amely magában foglalja az olvasási & írási régiót is), az olvasási régió meghibásodása során elveszítik az írási írási rendelkezésre állást.

* Az érintett régiót a rendszer automatikusan leválasztja, és offline állapotba állítja. A [Azure Cosmos db SDK](sql-api-sdk-dotnet.md) -k átirányítják az olvasási hívásokat a következő elérhető régióba az előnyben részesített régiók listájában.

* Ha a régiók rangsorolt listáján egyetlen régió sem érhető el, a beérkező kérések automatikusan az aktuális írási régióra váltanak vissza.

* Az olvasási régió meghibásodásának kezeléséhez nincs szükség módosításra az alkalmazás kódjában. Amikor az érintett olvasási régió újra online állapotba kerül, automatikusan szinkronizál az aktuális írási régióval, és ismét elérhető lesz az olvasási kérések kiszolgálásához.

* A további olvasásokat a szolgáltatás a helyreállt régiókhoz irányítja át anélkül, hogy módosítania kellene az alkalmazáskódot. Egy korábban sikertelen régió feladatátvétele és újracsatlakozása során a konzisztencia-garanciák továbbra is tiszteletben maradnak Azure Cosmos DB.

* Még egy ritka és szerencsétlen eseményen is, amikor az Azure-régió véglegesen behajthatatlan, nincs adatvesztés, ha a többrégiós Azure Cosmos-fiók *erős* konzisztencia-konfigurációval van konfigurálva. Ha tartósan letiltott írási régiót használ, egy többrégiós Azure Cosmos-fiók, amely a határértékek konzisztenciájával van konfigurálva, a lehetséges adatvesztési időszak az elavult ( *k* vagy *T* ) értékre van korlátozva, ahol k = 100000 frissítés és T = 5 perc. A munkamenet, a konzisztens előtag és a végleges konzisztencia-szintek esetében a lehetséges adatvesztési időszak legfeljebb 15 percet vesz igénybe. További információ a Azure Cosmos DB RTO és RPO céljairól: a [konzisztencia szintjei és az adatok tartóssága](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>Rendelkezésre állási zóna támogatása

A régiók közötti rugalmasság mellett mostantól engedélyezheti a **zónák redundanciát** az Azure Cosmos-adatbázishoz társítandó régió kiválasztásakor.

A rendelkezésre állási zónák támogatásával Azure Cosmos DB biztosítja, hogy a replikák egy adott régióban több zónába kerüljenek, hogy magas rendelkezésre állást és rugalmasságot biztosítson a zónákon belüli meghibásodások során. Ebben a konfigurációban nem változnak a késés és a többi SLA. Egyetlen zóna meghibásodása esetén a Zone redundancia teljes körű adattartósságot biztosít a RPO = 0 és a rendelkezésre állás RTO = 0 segítségével való elérhetősége esetén.

A zóna-redundancia a [többrégiós írási funkciók replikálásának](how-to-multi-master.md) *kiegészítő* funkciója. A zónák redundancia önmagában nem lehet a regionális rugalmasság elérésére támaszkodni. Ha például regionális kimaradások vagy kis késleltetésű hozzáférés van a régiók között, azt javasoljuk, hogy több írási régióval is rendelkezzen a zóna redundancia mellett.

Ha többrégiós írásokat konfigurál az Azure Cosmos-fiókhoz, külön díj nélkül is dönthet a zóna-redundancia szolgáltatásban. Ellenkező esetben tekintse meg az alábbi megjegyzést a zóna redundancia támogatásának díjszabását illetően. Az Azure Cosmos-fiók meglévő régiójába engedélyezheti a zóna redundanciát, ha eltávolítja a régiót, és újból hozzáadja a zóna redundancia beállítással.

Ez a funkció a következő helyen érhető el: *Egyesült Királyság déli régiója, Délkelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, USA középső régiója, Nyugat-Európa, Nyugat-USA 2, Kelet-Japán, Észak-Európa, Közép-Franciaország, Kelet-Ausztrália, 2. keleti* régió

A következő táblázat összefoglalja a különböző fiókok konfigurációinak magas rendelkezésre állási képességét:

|KPI  |Egyetlen régió Availability Zones nélkül (nem AZ)  |Egyetlen régió Availability Zones (AZ)  |Multi-region writes with Availability Zones (AZ, 2 Regions) – a javasolt beállítás |
|---------|---------|---------|---------|
|Rendelkezésre állási SLA írása | 99.99% | 99.99% | 99.999% |
|Rendelkezésre állási SLA olvasása  | 99.99% | 99.99% | 99.999% |
|Ár | Egyetlen régió számlázási díja | Az önálló régió rendelkezésre állási zónájának számlázási sebessége | Többrégiós számlázási díj |
|Zónák hibái – adatvesztés | Adatvesztés | Nincs adatvesztés | Nincs adatvesztés |
|Zónák hibái – rendelkezésre állás | Rendelkezésre állás elvesztése | Nincs rendelkezésre állási veszteség | Nincs rendelkezésre állási veszteség |
|Olvasási késés | Régiók közötti régió | Régiók közötti régió | Alacsony |
|Írási késés | Régiók közötti régió | Régiók közötti régió | Alacsony |
|Regionális leállás – adatvesztés | Adatvesztés |  Adatvesztés | Adatvesztés <br/><br/> Ha a kötött elavulás konzisztenciáját több írási régióval és több régióval együtt használja, az adatvesztés a fiókon beállított korláton belülre korlátozódik. <br /><br />A regionális leállás során elkerülhető az adatvesztés azáltal, hogy erős konzisztenciát konfigurál több régióval. Ez a lehetőség olyan kompromisszumokat tartalmaz, amelyek befolyásolják a rendelkezésre állást és a teljesítményt. Csak az egyrégiós írásokhoz konfigurált fiókokon konfigurálható. |
|Regionális leállás – rendelkezésre állás | Rendelkezésre állás elvesztése | Rendelkezésre állás elvesztése | Nincs rendelkezésre állási veszteség |
|Átviteli sebesség | X RU/s kiosztott átviteli sebesség | X RU/s kiosztott átviteli sebesség * 1,25 | 2X RU/s kiosztott átviteli sebesség <br/><br/> Ennek a konfigurációs módnak kétszer kell megfelelnie az átviteli sebességnek, ha egyetlen régióhoz képest Availability Zones van, mert két régió van. |

> [!NOTE]
> Ha engedélyezni szeretné a rendelkezésre állási zónák támogatását egy többrégiós Azure Cosmos-fiókhoz, a fióknak engedélyezve kell lennie a többrégiós írásoknak.

A zóna redundancia engedélyezhető, ha új vagy meglévő Azure Cosmos-fiókokhoz ad hozzá régiót. Ha engedélyezni szeretné a zóna redundanciát az Azure Cosmos-fiókjában, állítsa be a `isZoneRedundant` jelölőt `true` egy adott helyre. Ezt a jelzőt a Locations (helyszínek) tulajdonságon belül állíthatja be. A következő PowerShell-kódrészlet például lehetővé teszi a zóna redundanciát a "Délkelet-ázsiai" régióban:

Availability Zones a használatával engedélyezhető:

* [Azure Portalra](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure CLI](manage-with-cli.md#add-or-remove-regions)

* [Azure Resource Manager sablonok](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Magasan elérhető alkalmazások fejlesztése

* Tekintse át az [Azure Cosmos SDK](troubleshoot-sdk-availability.md) -k várható viselkedését ezekben az eseményekben, valamint azokat a konfigurációkat, amelyek hatással vannak rá.

* A magas írási és olvasási rendelkezésre állás biztosítása érdekében konfigurálja az Azure Cosmos-fiókot úgy, hogy legalább két, több írási régióval rendelkező régióra legyen kiterjedhet. Ez a konfiguráció biztosítja a legmagasabb rendelkezésre állást, a legalacsonyabb késést és a legjobb skálázhatóságot a SLA-kat támogató olvasások és írások számára. További információ: [Az Azure Cosmos-fiók konfigurálása több írási régióval](tutorial-global-distribution-sql-api.md).

* Az egyírásos régióval konfigurált többrégiós Azure Cosmos-fiókok esetében az [Azure CLI vagy a Azure Portal használatával engedélyezze az automatikus feladatátvételt](how-to-manage-database-account.md#automatic-failover). Miután engedélyezte az automatikus feladatátvételt, ha regionális katasztrófa van, Cosmos DB automatikusan feladatátvételt hajt végre a fiókjában.  

* Még ha az Azure Cosmos-fiókja is nagyon elérhető, előfordulhat, hogy az alkalmazás nem megfelelően van kialakítva, hogy továbbra is elérhető legyen. Az alkalmazás végpontok közötti magas rendelkezésre állásának teszteléséhez az alkalmazás tesztelési vagy vész-helyreállítási (DR) gyakorlatának részeként átmenetileg tiltsa le a fiók automatikus feladatátvételét, a [PowerShell, az Azure CLI vagy a Azure Portal használatával indítsa el a manuális feladatátvételt](how-to-manage-database-account.md#manual-failover), majd figyelje az alkalmazás feladatátvételét. Ha elkészült, visszatérhet az elsődleges régióhoz, és visszaállíthatja a fiók automatikus feladatátvételét.

* Egy globálisan elosztott adatbázis-környezeten belül közvetlen kapcsolat áll fenn a konzisztencia szintje és az adattartósság között egy adott régióra kiterjedő leállás esetén. Az üzletmenet-folytonossági terv kidolgozása során meg kell ismernie a maximális elfogadható időtartamot, mielőtt az alkalmazás teljesen helyreállít egy zavaró esemény után. Az alkalmazás teljes helyreállításához szükséges idő a helyreállítási időre vonatkozó célkitűzés (RTO). Azt is meg kell ismernie, hogy a legutóbbi adatfrissítések maximális időtartama alatt az alkalmazás elveszítheti a zavaró események utáni helyreállítást. Az adatfrissítés-vesztés megengedhető időkorlátja a helyreállítási időkorlát (RPO). A Azure Cosmos DB RPO és RTO lásd: a [konzisztencia szintjei és az adattartósság](./consistency-levels.md#rto)

## <a name="next-steps"></a>Következő lépések

Ezután olvassa el a következő cikkeket:

* [Rendelkezésre állási és teljesítménybeli kompromisszumok különböző konzisztencia-szintekhez](./consistency-levels.md)

* [Kiosztott átviteli sebesség globális méretezése](./request-units.md)

* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)

* [Konzisztenciaszintek az Azure Cosmos DB-ben](consistency-levels.md)

* [Cosmos-fiók konfigurálása több írási régióval](how-to-multi-master.md)

* [Az SDK működése több régiós környezetben](troubleshoot-sdk-availability.md)