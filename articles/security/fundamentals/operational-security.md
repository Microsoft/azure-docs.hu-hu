---
title: Azure működési biztonság | Microsoft Docs
description: Ismerkedjen meg a naplók és a szolgáltatásai Microsoft Azure figyelésével, valamint a működésének módjával. Ehhez tekintse meg az áttekintést.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 7d71820db3d58931f2fcd8d18441534ad36183c2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711994"
---
# <a name="azure-operational-security"></a>Azure-beli működési biztonság
## <a name="introduction"></a>Introduction (Bevezetés)

### <a name="overview"></a>Áttekintés
Tudjuk, hogy a biztonság az egyik a felhőben, és mennyire fontos, hogy pontos és kellő időben tájékozódjon az Azure biztonságáról. Az Azure alkalmazásokhoz és szolgáltatásokhoz való használatának egyik legjobb oka az, hogy kihasználja az elérhető biztonsági eszközök és képességek széles körét. Ezek az eszközök és képességek segítik a biztonságos megoldások létrehozását a biztonságos Azure platformon. A Windows Azure-nak biztosítania kell az ügyféladatok titkosságát, integritását és rendelkezésre állását, ugyanakkor az átlátható elszámoltathatóságot is lehetővé teszi.

Annak érdekében, hogy az ügyfelek jobban megértsék a Microsoft Azureon belül megvalósított biztonsági vezérlők tömbjét az ügyfél és a Microsoft működési perspektívái alapján, ez a tanulmány az "Azure Operational Security" című tanulmányt ismerteti, amely átfogó képet nyújt a Windows Azure-ban elérhető működési biztonságról.

### <a name="azure-platform"></a>Azure-platform
Az Azure egy nyilvános felhőalapú szolgáltatási platform, amely az operációs rendszerek, a programozási nyelvek, a keretrendszerek, az eszközök, az adatbázisok és az eszközök széles választékát támogatja. Linux-tárolókat is futtathat a Docker-integrációval; JavaScript-, Python-, .NET-, PHP-, Java-és Node.js-alkalmazásokat hozhat létre. az iOS-, Android-és Windows-eszközökön is létrehozhatók háttérrendszer. Az Azure Cloud Service több millió fejlesztőt és informatikai szakembereket is támogat.

Ha a-ra épít, vagy áttelepíti az IT-eszközöket, egy nyilvános felhőalapú szolgáltatóra támaszkodik, amely a szervezet képességei alapján gondoskodik az alkalmazások és adatok védelméről a szolgáltatásokkal és az általuk biztosított vezérlőkkel a felhőalapú eszközök biztonságának kezeléséhez.

Az Azure infrastruktúráját úgy alakították ki, hogy a létesítményből egyszerre több millió ügyfelet üzemeltető alkalmazások számára biztosítson egy megbízható alapot, amely alapján a vállalatok megfelelnek a biztonsági követelményeknek. Emellett az Azure a konfigurálható biztonsági beállítások széles körét biztosítja, és lehetővé teszi, hogy a biztonság testre szabható legyen, hogy megfeleljen a szervezete üzembe helyezésének egyedi követelményeinek. Ez a dokumentum segít megérteni, hogy az Azure biztonsági képességei hogyan segíthetnek a követelmények teljesítésében.

### <a name="abstract"></a>Abstract
Az Azure Operational Security a felhasználók számára elérhető szolgáltatásokat, vezérlőket és szolgáltatásokat jelenti a Microsoft Azureban tárolt adatok, alkalmazások és egyéb eszközök védelmére. Az Azure Operational Security olyan keretrendszerre épül, amely magában foglalja a Microsoft számára egyedi, a Microsoft biztonsági fejlesztési életciklus (SDL), a Microsoft Security Response Center program, valamint a kiberbiztonsági fenyegetések tájképének részletes ismerete révén szerzett ismereteket.

Ez a tanulmány a Microsoft Azure-beli működési biztonságának megközelítését ismerteti a Microsoft Azure Cloud platformon belül, és a következő szolgáltatásokat nyújtja:
1.  [Azure Monitor](../../azure-monitor/index.yml)

2.  [Azure Security Center](../../security-center/security-center-introduction.md)

3.  [Azure Monitor](../../azure-monitor/overview.md)

4.  [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)

5.  [Azure Storage-elemzés](/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)


## <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure figyelési naplók

A hibrid felhő számára az IT-felügyeleti megoldás a Microsoft Azure figyelési naplói. Önmagában vagy a meglévő System Center-telepítés kiterjesztéséhez Azure Monitor naplók lehetővé teszi az infrastruktúra felhőalapú felügyeletének maximális rugalmasságát és szabályozását.

![Azure Monitor-naplók](./media/operational-security/azure-operational-security-fig1.png)

A Azure Monitor-naplók segítségével bármilyen Felhőbeli példányt kezelhet, beleértve a helyszíni, az Azure, az AWS, a Windows Server, a Linux, a VMware és a OpenStack szolgáltatást, a versenyképes megoldásoknál alacsonyabb áron. A Felhőbeli első világra épülő Azure Monitor naplók új megközelítést biztosítanak a vállalat számára, amely a leggyorsabb és leghatékonyabb módja az új üzleti kihívásoknak, valamint az új munkaterhelések, alkalmazások és felhőalapú környezetek bevezetésének.

### <a name="azure-monitor-services"></a>Azure Monitor szolgáltatások

Azure Monitor naplók alapvető funkcióit az Azure-ban futó szolgáltatások biztosítják. Mindegyik szolgáltatás egy adott felügyeleti funkciót biztosít, és a szolgáltatások kombinálásával különféle felügyeleti forgatókönyvek valósíthatók meg.

| Szolgáltatás  | Leírás|
| :------------- | :-------------|
| Azure Monitor-naplók | Figyeli és elemzi a különféle erőforrások, köztük a fizikai és virtuális gépek rendelkezésre állását és teljesítményét. |
|Automation | Automatizálja a manuális folyamatokat, és érvényesíti a fizikai és virtuális gépekre vonatkozóan megadott konfigurációkat. |
| Backup | Biztonsági mentés és visszaállítás kritikus fontosságú adatként. |
| Site Recovery | Biztosítja a kritikus fontosságú alkalmazások magas rendelkezésre állását. |

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

A [Azure monitor-naplók](https://azure.microsoft.com/documentation/services/log-analytics) figyelik a felügyeleti szolgáltatásokat azáltal, hogy a felügyelt erőforrások adatait egy központi tárházba gyűjtik. Ezek az adatok lehetnek események, teljesítményadatok vagy az API segítségével biztosított egyéni adatok. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz.


Ez a módszer lehetővé teszi különböző forrásokból származó adatok összesítését, így az Azure-szolgáltatásokból származó adatok egyesíthetők a meglévő helyszíni környezettel. Továbbá egyértelműen elkülöníti az adatok gyűjtését az adatokon végzett műveletektől, így az összes művelet végrehajtható a különféle adatokon.


![Diagram, amely különböző forrásokból származó adatok összevonását mutatja be, így az Azure-szolgáltatásokból származó adatok egyesíthetők a meglévő helyszíni környezettel.](./media/operational-security/azure-operational-security-fig2.png)

A Azure Monitor szolgáltatás biztonságosan kezeli a felhőalapú adatait az alábbi módszerek használatával:
-   az adatelkülönítés
-   adatmegőrzés
-   fizikai biztonság
-   incidensek kezelése
-   betartásának
-   biztonsági szabványok tanúsítványai

### <a name="azure-backup"></a>Azure Backup

[Azure Backup](https://azure.microsoft.com/documentation/services/backup) biztosítja az adatok biztonsági mentési és visszaállítási szolgáltatásait, és a Azure monitor csomag részét képezi a termékek és szolgáltatások számára.
Védelmet biztosít az alkalmazásadatok számára, valamint évekig megőrzi őket minden tőkebefektetés nélkül és minimális működési költségek mellett. Képes biztonsági másolatot készíteni a fizikai és a virtuális Windows-kiszolgálókról az alkalmazások számítási feladataihoz, például a SQL Server és a SharePoint rendszerhez. A [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) is használhatja a védett és az Azure-ba való replikáláshoz a redundancia és a hosszú távú tárolás céljából.


Az Azure Backup védett adatainak tárolása egy meghatározott földrajzi régióban elhelyezkedő biztonságimásolat-tárolóban történik. A rendszer replikálja az adatmennyiséget ugyanazon a régión belül, és a tár típusától függően más régióba is replikálhatja a rugalmasságot.

### <a name="management-solutions"></a>Felügyeleti megoldások
A [Azure monitor](../../security-center/security-center-introduction.md) a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a helyszíni és a Felhőbeli infrastruktúra kezelésében és biztonságában.


A [felügyeleti megoldások](../../azure-monitor/insights/solutions.md) előre csomagolt logikai egységek, amelyek egy vagy több Azure monitor-szolgáltatás használatával egy adott felügyeleti forgatókönyvet implementálnak. A Microsofttól és partnereitől különböző megoldások érhetők el, amelyeket egyszerűen hozzáadhat az Azure-előfizetéshez, hogy növelje Azure Monitorbeli beruházásának értékét. Partnerként saját megoldásokat hozhat létre az alkalmazások és szolgáltatások támogatásához, és biztosíthatja azokat a felhasználóknak az Azure Marketplace-en vagy a gyors üzembe helyezési sablonokon keresztül.


![Felügyeleti megoldások](./media/operational-security/azure-operational-security-fig4.png)

Egy olyan megoldás jó példája, amely több szolgáltatást használ, hogy további funkciókkal lássa el a [Update Management megoldást](../../automation/update-management/overview.md). Ez a megoldás a Windows és Linux rendszerhez készült [Azure monitor naplók](../../azure-monitor/logs/log-query-overview.md) ügynökével gyűjti össze az egyes ügynökök szükséges frissítéseivel kapcsolatos információkat. Ezt az adatot a Azure Monitor naplók tárházba írja, ahol elemezheti azt egy befoglalt irányítópulttal.

Központi telepítés létrehozásakor a rendszer a szükséges frissítések telepítéséhez a [Azure Automation](../../automation/automation-intro.md) runbookok használja. A teljes folyamatot a portálon felügyelheti, és nem kell foglalkoznia a mögöttes részletekkel.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center segíti az Azure-erőforrások megóvását. Integrált biztonsági monitorozást és házirend-kezelést biztosít az Azure-előfizetések között. A szolgáltatáson belül lehetősége van arra, hogy a szabályzatokat ne csak az Azure-előfizetésekhez, hanem az [erőforráscsoportokhoz](../../azure-resource-manager/management/overview.md#resource-groups)is definiálja, így részletesebben is megadható.

### <a name="security-policies-and-recommendations"></a>Biztonsági szabályzatok és javaslatok

A biztonsági szabályzat határozza meg azoknak a vezérlőelemeknek a körét, amelyeket a rendszer az adott előfizetésen vagy erőforráscsoporton belüli erőforrásokhoz javasol.

A Security Centerben a vállalat biztonsági elvárásainak és az alkalmazások típusának vagy az adatok érzékenységének megfelelően állíthatja be a szabályzatokat.

![Biztonsági szabályzatok és javaslatok](./media/operational-security/azure-operational-security-fig5.png)


Az előfizetési szinten engedélyezett házirendek automatikusan propagálják az előfizetésben lévő összes erőforrás-csoportot, ahogy az a jobb oldali diagramban látható:


### <a name="data-collection"></a>Adatgyűjtés

A Security Center adatokat gyűjt a virtuális gépekről a biztonsági állapotuk értékeléséhez, a biztonsági javaslatok létrehozásához és a fenyegetésekkel kapcsolatos riasztásokhoz. Amikor az első hozzáférési Security Center, az adatgyűjtés az előfizetésben lévő összes virtuális gépen engedélyezve van. Az adatgyűjtés ugyan ajánlott, de le is állíthatja azt a Security Center szabályzatában található adatgyűjtési lehetőség kikapcsolásával.

### <a name="data-sources"></a>Adatforrások

- Az Azure Security Center az alábbi forrásokból kapott adatok elemzésével biztosítja a biztonsági állapot áttekinthetőségét, azonosítja a biztonsági réseket, javításokat javasolj, valamint észleli az aktív fenyegetéseket:

-   Azure Services: az adott erőforrás-szolgáltatóval kommunikálva üzembe helyezett Azure-szolgáltatások konfigurációjával kapcsolatos információkat használja.

- Hálózati forgalom: a hálózati forgalom metaadataiból vett mintát használja a Microsoft infrastruktúrájából, például a forrás és a cél IP-címét és portját, a csomagméretet és a hálózati protokollt.

-   Partnermegoldások: Az integrált partnermegoldásoktól, például tűzfalaktól és kártevőirtó megoldásoktól érkező biztonsági riasztásokat használja.

-   Saját virtuális gépek: Konfigurációs és biztonsági eseményekkel kapcsolatos információkat használ (például Windows-esemény- és vizsgálati naplókat, IIS-naplókat, rendszernapló-üzeneteket és a virtuális gépekről származó összeomlási memóriaképeket).

### <a name="data-protection"></a>Adatvédelem

Az Azure Security Center biztonsággal kapcsolatos adatokat gyűjt és dolgoz fel (például konfigurációs információkat, metaadatokat, eseménynaplókat, összeomlási memóriaképeket és még sok mást), hogy segítsen az ügyfeleknek a fenyegetések megelőzésében, észlelésében és elhárításában. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig.

-   **Az adatok elkülönítése**: az adatok logikailag elkülönítve vannak tárolva a szolgáltatás egyes összetevőiben. Az összes adat szervezet szerint van megcímkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes.

-   **Adathozzáférés**: a biztonsági javaslatok és a lehetséges biztonsági fenyegetések kivizsgálása érdekében a Microsoft munkatársai hozzáférhetnek az Azure-szolgáltatások által összegyűjtött vagy elemzett információkhoz, beleértve az összeomlási memóriaképek fájljait, feldolgozzák a létrehozási eseményeket, a VM-lemezek pillanatképeit és összetevőit, amelyek véletlenül a virtuális gépekről származó ügyféladatokat vagy személyes adatokat is tartalmazhatnak. Betartjuk a [Microsoft Online Services használati feltételeit és adatvédelmi nyilatkozatát](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), amely szerint a Microsoft nem használja fel az ügyféladatokat, vagy semmilyen hirdetési vagy hasonló kereskedelmi célra információt származtat.

-   **Az adatok felhasználása**: a Microsoft a különböző bérlőknél észlelt mintákat és fenyegetésre vonatkozó intelligenciát használ a megelőzési és észlelési funkcióihoz, és ezt az [adatvédelmi nyilatkozatában](https://www.microsoft.com/en-us/privacystatement/OnlineServices/) ismertetett adatvédelmi kötelezettségeinek megfelelően teszi.

### <a name="data-location"></a>Az adatok tárolási helye

Az Azure Security Center ideiglenes másolatokat gyűjt az összeomlási memóriaképek fájljairól, és elemzi ezeket a biztonsági réseket kihasználó támadások és a sikeres feltörések nyomai után kutatva. Az Azure Security Center az elemzéseket ugyanazon a földrajzi helyen végzi el, ahol a munkaterület található, és az elemzés után törli az ideiglenes másolatokat. A gépösszetevők tárolása központilag történik, ugyanabban a régióban, ahol a virtuális gép is található.

-   **A Storage-fiókok**: minden olyan régióhoz meg van adva egy Storage-fiók, amelyben a virtuális gépek futnak. Ez lehetővé teszi, hogy abban a régióban tárolja az adatait, amelyben az a virtuális gép található, amelyről az adatok gyűjtése történik.

-   **Az Azure Security Center tárhelye**: A biztonsági riasztásokkal (beleértve a partnerriasztásokat), javaslatokkal és biztonsági állapottal kapcsolatos információk tárolása jelenleg az Egyesült Államokban, központilag történik. Ez az információ magában foglalhat az Ön virtuális gépeiről összegyűjtött kapcsolódó konfigurációs adatokat és biztonsági eseményeket szükség szerint annak érdekében, hogy megjeleníthessük Önnek a biztonsági riasztásokat, a javaslatokat vagy a biztonsági állapot adatait.


## <a name="azure-monitor"></a>Azure Monitor

A [Azure monitor naplózza a biztonsági](../../security-center/security-center-monitoring.md) és auditálási megoldást, amely lehetővé teszi, hogy aktívan figyelje az összes erőforrást, ami csökkentheti a biztonsági incidensek hatását. Azure Monitor naplókat Security and Audit az erőforrások figyelésére használható biztonsági tartományokkal. A biztonsági tartomány gyors hozzáférést biztosít a beállításokhoz a biztonsági monitorozáshoz a következő tartományokban található további részletek:

-   Kártevőfelmérés
-   Frissítések felmérése
-   Identitás és hozzáférés.

A Azure Monitor az adott típusú erőforrásokra mutató tájékoztatókat biztosít. Vizualizációkat, lekérdezéseket, útválasztást, riasztásokat, automatikus méretezést és automatizálást biztosít az Azure-infrastruktúra (műveletnapló) és az egyes Azure-erőforrások (diagnosztikai naplók) esetében egyaránt.

![Azure Monitor](./media/operational-security/azure-operational-security-fig6.png)


A Felhőbeli alkalmazások számos mozgó részből összetettek. A figyelési szolgáltatással biztosítható, hogy az alkalmazás kifogástalan állapotban maradjon. Emellett segít elhárítani a lehetséges problémákat vagy a múltbeli hibák elhárítását.

Emellett a figyelési adatok segítségével mélyebb elemzéseket nyerhet az alkalmazásról. Ezzel az ismerettel javíthatja az alkalmazások teljesítményét vagy karbantartását, vagy automatizálhatja azokat a műveleteket, amelyek egyébként manuális beavatkozást igényelnek.

### <a name="azure-activity-log"></a>Azure-tevékenységnapló


Ez egy olyan napló, amely betekintést nyújt az előfizetése erőforrásaiban végrehajtott műveletekre. A műveletnapló korábban "naplók" vagy "operatív naplók" néven ismert, mivel az előfizetések vezérlési sík eseményeiről számolt be.

![Azure-tevékenységnapló](./media/operational-security/azure-operational-security-fig7.png)

A műveletnapló segítségével meghatározhatja a "mit, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az előfizetésében lévő erőforrásokon. Emellett megismerheti a művelet állapotát és az egyéb kapcsolódó tulajdonságokat is. A műveletnapló nem tartalmaz olvasási (GET) műveleteket vagy műveleteket a klasszikus modellt használó erőforrásokhoz.

### <a name="azure-diagnostic-logs"></a>Azure-beli diagnosztikai naplók

Ezeket a naplókat egy erőforrás bocsátja ki, és az adott erőforrás működésével kapcsolatos gazdag és gyakori információkat biztosít. A naplók tartalma az erőforrástípus alapján változik.

A Windows-események rendszernaplói például a virtuális gépek és a Blobok, a táblák és a várólista naplóinak egyik kategóriája a tárolási fiókok diagnosztikai naplóinak kategóriája.

A diagnosztikai naplók eltérnek a [tevékenység naplójától (korábbi nevén naplózási napló vagy operatív napló)](../../azure-monitor/essentials/platform-logs-overview.md). A tevékenység naplója betekintést nyújt az előfizetése erőforrásaiban végrehajtott műveletekre. A diagnosztikai naplókban az erőforrás által végrehajtott műveletekkel kapcsolatos információk találhatók meg.

### <a name="metrics"></a>Mérőszámok

A Azure Monitor lehetővé teszi a telemetria használatát az Azure-beli számítási feladatok teljesítményének és állapotának megismeréséhez. Az Azure telemetria-adatok legfontosabb típusa a legtöbb Azure-erőforrás által kibocsátott mérőszámok (más néven teljesítményszámlálók). A Azure Monitor számos módszert biztosít ezen [mérőszámok](../../azure-monitor/data-platform.md) konfigurálásához és felhasználásához a figyeléshez és a hibaelhárításhoz. A metrikák értékes telemetria, és lehetővé teszik a következő feladatok elvégzését:

-   **Nyomon követheti az erőforrás teljesítményét** (például egy virtuális gépet, webhelyet vagy logikai alkalmazást), ha kirajzolja a mérőszámait egy portál diagramon, és a diagramot egy irányítópultra rögzíti.

-   **Értesítést kap arról,** hogy az erőforrás teljesítménye hatással van, ha egy metrika egy bizonyos küszöbértéket keresztez.

-   **Konfiguráljon automatizált műveleteket**, például egy erőforrás automatikus méretezését vagy egy runbook égetését, ha egy metrika egy bizonyos küszöbértéket keresztez.

-   **Speciális elemzési** vagy jelentéskészítési műveletek végrehajtása az erőforrás teljesítmény-vagy használati trendjeivel.

-   Az erőforrás teljesítmény-vagy állapotadatok **archiválása** megfelelőségi vagy naplózási célokra.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Ez a képesség az Azure-ban, amely lehetővé teszi a diagnosztikai adatgyűjtést egy üzembe helyezett alkalmazáson belül. A diagnosztikai bővítményt különböző forrásokból is használhatja. Jelenleg támogatott az [Azure Cloud Service webes és feldolgozói szerepkörei](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), az [Azure Virtual Machines](../../virtual-machines/windows/overview.md) a Microsoft Windows rendszert futtatja, és [Service Fabric](../../azure-monitor/agents/diagnostics-extension-overview.md). Más Azure-szolgáltatások saját külön diagnosztikát is rendelkeznek.

## <a name="azure-network-watcher"></a>Azure Network Watcher

A hálózati biztonság naplózása létfontosságú a hálózati sebezhetőségek észleléséhez, valamint az informatikai biztonsági és szabályozási irányítási modell megfelelőségének biztosításához. A biztonsági csoport nézettel beolvashatók a konfigurált hálózati biztonsági csoport és biztonsági szabályok, valamint az érvényes biztonsági szabályok. Az alkalmazott szabályok listájának segítségével meghatározhatja a megnyíló portokat, és kiderítheti a hálózati sebezhetőséget.

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) egy regionális szolgáltatás, amely lehetővé teszi, hogy az Azure-ban hálózati szinten figyelje és diagnosztizálja a feltételeket. A Network Watcher elérhető hálózati diagnosztikai és vizualizációs eszközök segítségével megismerheti, diagnosztizálhatja és elemezheti a hálózatát az Azure-ban. Ez a szolgáltatás magában foglalja a csomagok rögzítését, a következő ugrást, az IP-forgalom ellenőrzését, a biztonsági csoport nézetét, a NSG folyamat naplóit. A forgatókönyvek szintjének figyelése lehetővé teszi a hálózati erőforrások teljes körű megtekintését az egyes hálózati erőforrások figyelésével szemben.

![Azure Network Watcher](./media/operational-security/azure-operational-security-fig8.png)

A Network Watcher jelenleg a következő képességekkel rendelkezik:

-   **<a href="/azure/network-watcher/network-watcher-monitoring-overview">Naplók</a>**– a hálózatok konfigurációjának részeként végrehajtott műveletek naplózása történik. Ezek a naplók a Azure Portalban tekinthetők meg, vagy a Microsoft-eszközök, például Power BI vagy külső gyártótól származó eszközök használatával kérhetők le. A naplók a portálon, a PowerShellen, a CLI-n és a REST API-n keresztül érhetők el. A naplókkal kapcsolatos további információkért lásd: műveletek naplózása a Resource Managerrel. A naplók az összes hálózati erőforráson végzett műveletekhez érhetők el.


-   **<a href="/azure/network-watcher/network-watcher-ip-flow-verify-overview">IP-forgalom ellenőrzése</a>** – ellenőrzi, hogy egy csomag engedélyezett vagy megtagadva a flow-információk 5 rekordos csomagjainak paraméterei (cél IP-címe, forrás IP-címe, célport, forrásport és protokoll) alapján. Ha a csomagot egy hálózati biztonsági csoport megtagadja, a rendszer visszaadja a csomagot és a hálózati biztonsági csoportot, amely megtagadta a csomagot.

-   **<a href="/azure/network-watcher/network-watcher-next-hop-overview">Következő ugrás</a>** – meghatározza a következő ugrást az Azure hálózati hálóban átirányított csomagok számára, lehetővé téve a helytelenül konfigurált, felhasználó által megadott útvonalak diagnosztizálását.

-   **<a href="/azure/network-watcher/network-watcher-security-group-view-overview">Biztonsági csoport nézet</a>** – a virtuális gépen alkalmazott hatályos és alkalmazott biztonsági szabályok beolvasása.

-   **<a href="/azure/network-watcher/network-watcher-nsg-flow-logging-overview">NSG flow naplózás</a>** – a hálózati biztonsági csoportok folyamatábrái lehetővé teszik a csoport biztonsági szabályai által engedélyezett vagy megtagadott forgalomhoz kapcsolódó naplók rögzítését. A folyamatot 5 rekordos információ határozza meg – forrás IP-cím, célként megadott IP-cím, forrásport, célport és protokoll.

## <a name="azure-storage-analytics"></a>Azure Storage Analytics

A [Storage Analytics](/rest/api/storageservices/fileservices/storage-analytics) olyan metrikákat tárolhat, amelyek összesített tranzakciós statisztikát és kapacitási adatokat tartalmaznak a tárolási szolgáltatásokra irányuló kérések esetén. A tranzakciókat az API működési szintjén és a tárolási szolgáltatás szintjén is jelenteni kell, a kapacitást pedig a Storage szolgáltatás szintjén kell jelenteni. A metrikák adatai a tárolási szolgáltatások használatának elemzésére, a tárolási szolgáltatással kapcsolatos kérések diagnosztizálására, valamint a szolgáltatást használó alkalmazások teljesítményének javítására használhatók.

[Azure Storage Analytics](/rest/api/storageservices/fileservices/storage-analytics) naplózást végez, és metrikai adatokat biztosít egy Storage-fiókhoz. Ezeket az adatokat felhasználhatja a kérelmek nyomon követéséhez, a használati trendek elemzéséhez és a Storage-fiókkal kapcsolatos problémák diagnosztizálásához. Storage Analytics naplózás elérhető a [blob, a üzenetsor és a Table Services](../../storage/common/storage-introduction.md)számára. A Storage Analytics naplózza a tárolási szolgáltatásoknak elküldött sikeres és sikertelen kérelmek részletes információit.

Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kéréseket a rendszer a lehető legjobb módon naplózza. A naplóbejegyzések csak akkor jönnek létre, ha a szolgáltatás-végpontra irányuló kérések történnek. Ha például egy Storage-fiók egy tevékenységgel rendelkezik a blob-végponton, de nem a tábla vagy a várólista végpontjában, akkor csak a Blob servicehoz tartozó naplók jönnek létre.

Storage Analytics használatához külön kell engedélyeznie a figyelni kívánt szolgáltatásokhoz. Engedélyezheti a [Azure Portal](https://portal.azure.com/); További információ: [Storage-fiók figyelése a Azure Portalban](../../storage/common/manage-storage-analytics-logs.md). Storage Analytics programozott módon is engedélyezheti a REST API vagy az ügyféloldali kódtár használatával. A szolgáltatás tulajdonságainak beállítása művelettel engedélyezheti a Storage Analytics egyenként az egyes szolgáltatásokhoz.

Az összesített adatokat egy jól ismert blobban (naplózáshoz) és jól ismert táblákban (a metrikák esetében) tárolják, amelyek a Blob service és az Table service API-k használatával érhetők el.

A Storage Analytics 20 TB-os korláttal rendelkezik a Storage-fiók teljes korlátján kívüli tárolt adat mennyiségétől függően. Az összes napló egy $logs nevű tárolóban található [blokk blobokban](../../storage/common/storage-analytics.md) van tárolva, amelyek automatikusan létrejönnek, ha a Storage Analytics engedélyezve van egy Storage-fiókhoz.

A Storage Analytics által végrehajtott következő műveletek számlázandóek:

-   Kérelmek a Blobok létrehozásához a naplózáshoz
-   A táblázat entitások metrikák létrehozásához szükséges kérelmek.

> [!Note]
> A számlázási és adatmegőrzési szabályzatokkal kapcsolatos további információkért lásd: [Storage Analytics és számlázás](/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Az optimális teljesítmény érdekében korlátozni szeretné a virtuális géphez csatlakoztatott, magas kihasználtságú lemezek számát a lehetséges szabályozás elkerülése érdekében. Ha az összes lemez nem magas kihasználtságú, a Storage-fiók több lemezt is támogat.

> [!Note]
> A Storage-fiók korlátaival kapcsolatos további információkért lásd a [standard szintű Storage-fiókok méretezhetőségi céljait](../../storage/common/scalability-targets-standard-account.md)ismertető témakört.


A rendszer naplózza a következő típusú hitelesített és névtelen kérelmeket.

| Hitelesített  | Névtelen|
| :------------- | :-------------|
| Sikeres kérések | Sikeres kérések |
|Sikertelen kérések, köztük az időtúllépések, torlódások, valamint a hálózati, hitelesítési és egyéb hibák | Közös hozzáférésű aláírást (SAS) használó kérelmek, beleértve a sikertelen és sikeres kérelmeket |
| Közös hozzáférésű aláírást (SAS) használó kérelmek, beleértve a sikertelen és sikeres kérelmeket |Ügyfél- és kiszolgálóoldali időtúllépési hibák |
|   Elemzési adatokhoz kapcsolódó kérelmek |    304-es (Nincs módosítva) hibakóddal ellátott sikertelen GET-kérések |
| A Storage Analytics saját maga által kezdeményezett kérelmeket (például a naplózás létrehozását vagy törlését) nem naplózza a rendszer. A naplózott adatok teljes listáját az [Storage Analytics naplózott műveletek és állapotüzenetek](/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) , valamint a [Storage Analytics naplózási formátum](/rest/api/storageservices/fileservices/storage-analytics-log-format) témakörök ismertetik. | Az összes többi sikertelen névtelen kérelmet nem naplózza a rendszer. A naplózott adatok teljes listáját az [Storage Analytics naplózott műveletek és állapotüzenetek](/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) , valamint a [Storage Analytics naplózási formátuma](/rest/api/storageservices/fileservices/storage-analytics-log-format)ismerteti. |

## <a name="azure-active-directory"></a>Azure Active Directory

Az Azure AD számos Identitáskezelés-felügyeleti funkciót tartalmaz, többek között a többtényezős hitelesítést, az eszközök regisztrálását, az önkiszolgáló jelszavas felügyeletet, az önkiszolgáló csoportok felügyeletét, a privilegizált fiókok felügyeletét, a szerepköralapú hozzáférés-vezérlést, az alkalmazások használatának figyelését, a részletes naplózást, valamint a biztonsági monitorozást és riasztásokat.

-   Az alkalmazások biztonságának javítása az Azure AD többtényezős hitelesítéssel és a feltételes hozzáféréssel.

-   Az alkalmazások használatának figyelése és az üzleti adatok védelme a fejlett fenyegetésekkel és a biztonsági jelentéskészítéssel és figyeléssel.

Az Azure Active Directory (Azure AD) biztonsági, naplózási és tevékenységjelentéseket biztosít a címtárához. [A Azure Active Directory audit jelentés](../../active-directory/reports-monitoring/overview-reports.md) segíti az ügyfeleket abban, hogy azonosítsák a Azure Active Directoryban bekövetkezett privilegizált műveleteket. A Kiemelt jogosultságú műveletek közé tartoznak a jogosultságszint-emelési változások (például a szerepkörök létrehozása vagy a jelszó alaphelyzetbe állítása), a házirendek konfigurációjának módosítása (például a jelszóházirend) vagy a címtár konfigurációjának módosítása (például a tartományi összevonási beállítások változásai).

A jelentések az esemény neve, a műveletet végrehajtó színész, a módosítás által érintett cél erőforrás, valamint a dátum és idő (UTC) alapján adják meg a naplózási rekordot. Az ügyfelek lekérhetik Azure Active Directory naplózási események listáját a [Azure Portal](https://portal.azure.com/)segítségével, a [naplók megtekintése](../../active-directory/reports-monitoring/overview-reports.md)című témakörben leírtak szerint. A benne foglalt jelentések listája:

| Biztonsági jelentések  | Tevékenységjelentések| Naplózási jelentések |
| :------------- | :-------------| :-------------|
|Bejelentkezések ismeretlen forrásokról | Alkalmazáshasználat: összegzés | Címtárnaplózási jelentés |
|Több hibát követő bejelentkezések | Alkalmazáshasználat: részletes |   |
|Bejelentkezések különböző földrajzi régiókból | Alkalmazás irányítópultja |  |
|Bejelentkezések gyanús tevékenységeket mutató IP-címekkel |Alkalmazás-kiépítési hibák |  |
|Rendszertelen bejelentkezési tevékenység |Egyéni felhasználói eszközök |  |
|Bejelentkezések potenciálisan fertőzött eszközökről |Egyéni felhasználói tevékenység |   |
|Rendellenes bejelentkezési tevékenységet mutató felhasználók |Csoporttevékenység-jelentés |   |
| |Jelszó-visszaállítási regisztrációs tevékenységjelentés |   |
| |Jelszó-visszaállítási tevékenység |   |



Ezeknek a jelentéseknek az adatai hasznosak lehetnek az alkalmazások, például a SIEM-rendszerek, a naplózási és az üzleti intelligencia-eszközök számára. Az Azure AD Reporting [API](../../active-directory/reports-monitoring/concept-reporting-api.md) -k egy REST-alapú API-kon keresztül programozott hozzáférést biztosítanak az adatokhoz. Ezeket az API-kat különböző programozási nyelveken és eszközökön hívhatja.

Az Azure AD-naplózási jelentésben szereplő események 180 napig őrződnek meg.

> [!Note]
> További információ a jelentések megőrzéséről: [Azure Active Directory jelentési adatmegőrzési szabályzatok](../../active-directory/reports-monitoring/reference-reports-data-retention.md).

Azon ügyfelek számára, akik a [naplózási események](../../active-directory/reports-monitoring/concept-audit-logs.md) hosszabb megőrzési időtartamokra való tárolását érdeklik, a JELENTÉSKÉSZÍTÉSi API-val rendszeresen lehívhatja a naplózási eseményeket egy különálló adattárba.

## <a name="summary"></a>Összefoglalás

Ez a cikk az adatvédelmet és az adatok védelmét, valamint a szervezet informatikai infrastruktúrájának kezelését segítő szoftvereket és szolgáltatásokat nyújt. A Microsoft felismeri, hogy amikor másoknak bízzák meg az adatvédelmet, a megbízhatóság szigorú biztonságot igényel. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig. Az adatok biztonságossá tétele és védelme a Microsoft legfontosabb prioritása.

Ez a cikk ismerteti

-   Az adatok gyűjtése, feldolgozása és biztonságossá tétele a Azure Monitor Suite-ban.

-   Gyorsan elemezheti az eseményeket több adatforráson belül. Azonosítsa a biztonságot, és Ismerje meg a fenyegetések és a támadások hatókörét és hatását, hogy enyhítse a biztonsági rések sérülését.

-   Azonosítsa a támadási mintákat a kimenő kártékony IP-forgalom és a kártékony veszélyforrások típusának megjelenítésével. Ismerje meg a teljes környezet biztonsági állapotát a platformtól függetlenül.

-   Rögzítse a biztonsági vagy megfelelőségi ellenőrzéshez szükséges összes naplózási és esemény-adatmennyiséget. A biztonsági audit teljes, kereshető és exportálható napló-és esemény-adatkészlettel való ellátásához szükséges idő és erőforrások levágása.

<ul>
<li>A biztonsággal kapcsolatos események, naplózási és szabálysértési elemzések összegyűjtése az eszközök alapos nyomon követéséhez:</li>
<ul>
<li>Biztonsági helyzet</li>
<li>Jelentős probléma</li>
<li>Összefoglalási fenyegetések</li>
</ul>
</ul>

## <a name="next-steps"></a>Következő lépések

- [Tervezési és működési biztonság](https://www.microsoft.com/trustcenter/security/designopsecurity)

A Microsoft megtervezi a szolgáltatásait és szoftvereit a biztonsággal kapcsolatban, így biztosítva, hogy a felhőalapú infrastruktúrája rugalmasan és védelmet nyújtson a támadásokkal szemben.

- [Naplók Azure Monitor | Biztonsági & megfelelőség](https://www.microsoft.com/cloud-platform/security-and-compliance)

A Microsoft biztonsági adatai és elemzése segítségével intelligensebb és hatékony veszélyforrások észlelését végezheti el.

- [Azure Security Center tervezés és műveletek](../../security-center/security-center-planning-and-operations-guide.md) Lépések és feladatok összessége, amelyek segítségével optimalizálhatja Security Center használatát a szervezet biztonsági követelményei és a felhőalapú felügyeleti modell alapján.