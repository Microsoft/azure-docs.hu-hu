---
title: Azure Monitor-ügynök áttekintése
description: A Azure Monitor ügynök (AMA) áttekintése, amely a virtuális gépek vendég operációs rendszerének figyelési adatait gyűjti.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: 0487e5bd8109c02605e785e74d45589dd5fde5b9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039597"
---
# <a name="azure-monitor-agent-overview-preview"></a>Azure Monitor-ügynök áttekintése (előzetes verzió)
A Azure Monitor ügynök (AMA) figyeli a virtuális gépek vendég operációs rendszerének figyelési adatait, és a Azure Monitor számára biztosítja azt. Ez a cikk áttekintést nyújt a Azure Monitor-ügynökről, beleértve a telepítésének módját és az adatgyűjtés konfigurálását.

## <a name="relationship-to-other-agents"></a>Kapcsolat más ügynökökkel
A Azure Monitor ügynök a következő, jelenleg a Azure Monitor által használt ügynököket váltja fel, hogy a vendég adatait a virtuális gépekről gyűjtsön:

- [Log Analytics ügynök](./log-analytics-agent.md) – adatokat küld log Analytics munkaterületre, és támogatja a virtuális gépek elemzését és figyelési megoldásait.
- [Diagnosztikai bővítmény](./diagnostics-extension-overview.md) – adatokat küld Azure monitor metrikák (csak Windows), az Azure Event Hubs és az Azure Storage szolgáltatásba.
- A [Grafi ügynök](../essentials/collect-custom-metrics-linux-telegraf.md) – adatokat küld Azure monitor metrikáknak (csak Linux).

A funkció egyetlen ügynökbe való konszolidálásán kívül a Azure Monitor ügynök a következő előnyöket biztosítja a meglévő ügynököknél:

- A figyelés hatóköre. Központilag konfigurálhatja a gyűjteményt különböző típusú virtuális gépek különböző csoportjaihoz.  
- Linux rendszerű többszörös vezérlés: a linuxos virtuális gépekről több munkaterületre küldheti az adatait.
- Windows-események szűrése: az XPATH-lekérdezésekkel szűrheti, hogy mely Windows-események legyenek gyűjtve.
- Továbbfejlesztett bővítmények kezelése: Azure Monitor-ügynök egy új módszert használ a bővíthetőség kezelésére, amely átláthatóbbá és ellenőrizhető, mint a felügyeleti csomagok és a Linux beépülő modulok az aktuális Log Analytics-ügynökökben.

### <a name="changes-in-data-collection"></a>Az adatgyűjtés változásai
A meglévő ügynökök adatgyűjtésének meghatározására szolgáló módszerek különböznek egymástól, és mindegyiknek van olyan kihívása, amely Azure Monitor ügynökkel foglalkozik.

- Log Analytics ügynök Log Analytics munkaterületről kapja meg a konfigurációt. Ez könnyen központilag konfigurálható, de nehéz meghatározni a különböző virtuális gépek független definícióit. Csak Log Analytics munkaterületre tud küldeni.

- A diagnosztikai bővítmény minden virtuális géphez rendelkezik konfigurációval. Ez egyszerűen meghatározható a különböző virtuális gépek független definíciói, de a központilag könnyen kezelhető. Az adatok csak Azure Monitor metrikák, Azure Event Hubs vagy Azure Storage szolgáltatásba küldhetők. A Linux-ügynökök esetében a nyílt forráskódú, a Azure Monitor metrikák számára szükséges, hogy az adatok elküldjenek adatokat.

Azure Monitor ügynök az [adatgyűjtési szabályok (DCR)](data-collection-rule-overview.md) használatával konfigurálja az egyes ügynököktől gyűjtött adatokat. Az adatgyűjtési szabályok lehetővé teszik a gyűjtési beállítások kezelhetőségét, miközben továbbra is engedélyezik az egyedi, hatókörön belüli konfigurációkat a számítógépek részhalmaza számára. Függetlenek a munkaterülettől és függetlenek a virtuális gépről, amely lehetővé teszi, hogy egyszer legyenek meghatározva és újra felhasználhatók legyenek a gépek és a környezetek között. Lásd: [az adatgyűjtés konfigurálása a Azure monitor-ügynökhöz (előzetes verzió)](data-collection-rule-azure-monitor-agent.md).

## <a name="should-i-switch-to-azure-monitor-agent"></a>Váltson Azure Monitor ügynökre?
Azure Monitor ügynök párhuzamosan [használható a Azure monitor általánosan elérhető ügynökökkel](agents-overview.md), de érdemes lehet a virtuális gépeket a Azure monitor-ügynök nyilvános előzetes verziójában áttérni a jelenlegi ügynökökre. A meghatározás során vegye figyelembe a következő tényezőket.

- **Környezeti követelmények.** Azure Monitor ügynök a jelenlegi ügynököknél korlátozottabb számú támogatott operációs rendszert, környezetet és hálózatkezelési követelményt tartalmaz. A jövőbeli környezetek támogatása, például az új operációsrendszer-verziók és a hálózati követelmények típusai valószínűleg csak Azure Monitor ügynökben lesznek biztosítva. Érdemes felmérni, hogy Azure Monitor ügynök támogatja-e a környezetét. Ha nem, akkor az aktuális ügynökkel kell maradnia. Ha Azure Monitor ügynök támogatja a jelenlegi környezetét, érdemes áttérnie rá.
- **Nyilvános előzetes kockázati tűréshatár.** Noha Azure Monitor ügynök alapos tesztelés alatt áll a jelenleg támogatott forgatókönyvek esetében, az ügynök még nyilvános előzetes verzióban érhető el. A verziók frissítései és a funkcionalitás fejlesztése gyakran előfordul, és hibákat okozhat. Fel kell mérnie, hogy az ügynökben milyen kockázattal jár a virtuális gépeken az adatgyűjtés leállítása. Ha az adatgyűjtési hézag nem lesz jelentős hatással a szolgáltatásaira, folytassa Azure Monitor ügynökkel. Ha alacsony toleranciával rendelkezik az instabilitáshoz, akkor az általánosan elérhető ügynökökkel maradjon, amíg Azure Monitor ügynök el nem éri ezt az állapotot.
- **Aktuális és új szolgáltatásokra vonatkozó követelmények.** Azure Monitor ügynök számos új képességet vezet be, például szűrést, hatókört és többsoros vezérlést, de még nem paritásos a jelenlegi ügynökökkel, például az egyéni naplók gyűjtésével és a megoldásokkal való integrációval. Azure Monitor legtöbb új funkciója csak Azure Monitor ügynökkel érhető el, így az idő múlásával a további funkciók csak az új ügynökben lesznek elérhetők. Érdemes figyelembe vennie, hogy Azure Monitor ügynök rendelkezik-e a szükséges funkciókkal, és hogy vannak-e olyan szolgáltatások, amelyeket átmenetileg elvégezhet anélkül, hogy más fontos funkciókat kellene beszereznie az új ügynökhöz. Ha Azure Monitor ügynök rendelkezik az összes szükséges alapvető képességgel, érdemes áttérnie rá. Ha kritikus fontosságú funkciókra van szükség, akkor folytassa az aktuális ügynökkel, amíg Azure Monitor ügynök eléri a paritást.
- **Az újraműködésre vonatkozó tolerancia.** Ha új környezetet állít be olyan erőforrásokkal, mint például a telepítési parancsfájlok és a bevezetési sablonok, érdemes megfontolnia, hogy képes-e újradolgozni, ha Azure Monitor ügynök általánosan elérhetővé válik. Ha az újraműködésre vonatkozó erőfeszítés minimális, a jelenlegi ügynökökkel együtt marad. Ha jelentős mennyiségű munkát fog igénybe venni, érdemes lehet új környezetet beállítani az új ügynökkel. A Azure Monitor ügynöknek általánosan elérhetőnek kell lennie, és az Log Analytics ügynökök számára közzé kell tenni az elavult dátumot a 2021-es verzióban. A jelenlegi ügynökök több évig is támogatottak lesznek, ha az elavultság megkezdődik.



## <a name="current-limitations"></a>Aktuális korlátozások
A Azure Monitor ügynök nyilvános előzetes verziója a következő korlátozásokat alkalmazza:

- A Azure Monitor ügynök nem támogatja a megoldásokat és az adatellenőrzéseket, például a virtuális gépeket és a Azure Security Center. Az egyetlen jelenleg támogatott forgatókönyv az adatok gyűjtése a konfigurált adatgyűjtési szabályok használatával. 
- Az adatgyűjtési szabályokat ugyanabban a régióban kell létrehozni, mint a célhelyként használt Log Analytics munkaterület.
- Az Azure Virtual Machines, a virtuálisgép-méretezési csoportok és az Azure arc-kompatibilis kiszolgálók jelenleg támogatottak. Az Azure Kubernetes szolgáltatás és más számítási erőforrástípusok jelenleg nem támogatottak.
- A virtuális gépnek hozzá kell férnie a következő HTTPS-végpontokhoz:
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>Együttélés más ügynökökkel
A Azure Monitor ügynök együtt létezhet a meglévő ügynökökkel, így az értékelés vagy az áttelepítés során továbbra is használhatja meglévő funkcióit. Ez különösen fontos a meglévő megoldások támogatásának nyilvános előzetes verziójában. Ügyeljen arra, hogy a duplikált adatok összegyűjtése óta ez a lekérdezési eredmények eldöntése, valamint az adatfeldolgozás és-megőrzés további díját eredményezheti.

A VM-elemzések például a Log Analytics ügynök használatával küldenek teljesítményadatokat egy Log Analytics munkaterületre. A munkaterületet úgy is beállíthatja, hogy a Windows-eseményeket és a syslog-eseményeket összegyűjtse az ügynököktől. Ha telepíti a Azure Monitor-ügynököt, és létrehoz egy adatgyűjtési szabályt ugyanezen események és teljesítményadatok esetében, a rendszer duplikált adatértéket eredményez.


## <a name="costs"></a>Költségek
Azure Monitor ügynöknek nincs díja, de a betöltött adatokért díjat számítunk fel. Az adatgyűjtéssel és-megőrzéssel, valamint az ügyfél-metrikákkal kapcsolatos részletekért tekintse Log Analytics meg [Azure monitor díjszabását](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="data-sources-and-destinations"></a>Adatforrások és célhelyek
A következő táblázat felsorolja, hogy milyen típusú adatok gyűjthetők össze az Azure Monitor ügynökkel az adatgyűjtési szabályok segítségével, és hogy hol küldhetik el ezeket az adatokat. Tekintse meg a [Azure monitor által figyelt adatokat?](../monitor-reference.md) az elemzések, megoldások és egyéb olyan megoldások listáját, amelyek a Azure monitor-ügynököt használják más típusú adatok gyűjtésére.


A Azure Monitor ügynök adatokat küld Azure Monitor metrikák vagy egy Log Analytics munkaterületre, amely támogatja Azure Monitor naplókat.

| Adatforrás | Célhelyek | Leírás |
|:---|:---|:---|
| Teljesítmény        | Azure Monitor-metrikák<br>Log Analytics-munkaterület | Az operációs rendszer és a számítási feladatok különböző szempontjainak teljesítményét mérő numerikus értékek. |
| Windows-eseménynaplók | Log Analytics-munkaterület | A Windows eseménynaplózási rendszernek eljuttatott információk. |
| Rendszernapló             | Log Analytics-munkaterület | A Linux-eseménynaplózási rendszernek eljuttatott információk. |


## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
A Azure Monitor ügynök által jelenleg támogatott Windows és Linux operációsrendszer-verziók listáját a [támogatott operációs rendszerek](agents-overview.md#supported-operating-systems) című részben tekintheti meg.



## <a name="security"></a>Biztonság
A Azure Monitor ügynöknek nincs szüksége kulcsra, de a [rendszerhez rendelt felügyelt identitásra](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)van szükség. Az ügynök telepítése előtt minden virtuális gépen engedélyezve kell lennie egy rendszerhez rendelt felügyelt identitásnak.

## <a name="networking"></a>Hálózatkezelés
Az Azure Monitor ügynök támogatja az Azure-szolgáltatás címkéit (a AzureMonitor és a AzureResourceManager címkét is kötelező megadni), de még nem működik Azure Monitor magánhálózati vagy közvetlen proxykkal.


## <a name="next-steps"></a>Következő lépések

- [Telepítse Azure monitor Agent ügynököt](azure-monitor-agent-install.md) Windows és Linux rendszerű virtuális gépekre.
- [Hozzon létre egy adatgyűjtési szabályt](data-collection-rule-azure-monitor-agent.md) , amely adatokat gyűjt az ügynöktől, és elküldi azt a Azure monitornak.