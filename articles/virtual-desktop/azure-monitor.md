---
title: A Windows rendszerű virtuális asztali monitor figyelése – Azure
description: A Azure Monitor használata a Windows rendszerű virtuális asztali gépekhez.
author: Heidilohr
ms.topic: how-to
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1b93e0c0d61eaa390eda66da2a583a4f429ddd79
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709548"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment"></a>Az üzembe helyezés figyelése a Windows rendszerű virtuális asztali Azure Monitor használatával

A Windows rendszerű virtuális asztali Azure Monitor egy olyan irányítópult, amely az informatikai szakemberek számára megkönnyíti a Windows rendszerű virtuális asztali környezetek megértését Azure Monitor munkafüzetek számára. Ebből a témakörből megtudhatja, hogyan állíthatja be Azure Monitor a Windows rendszerű virtuális asztal számára a Windows rendszerű virtuális asztali környezetek figyeléséhez.

## <a name="requirements"></a>Követelmények

Mielőtt megkezdené a Windows rendszerű virtuális asztali Azure Monitor használatát, be kell állítania a következő dolgokat:

- Az összes Windows rendszerű virtuális asztali környezetnek a Azure Resource Manager rendszerrel kompatibilis Windows-alapú virtuális asztal legújabb kiadásán kell alapulnia.
- Legalább egy konfigurált Log Analytics munkaterület. A Windows rendszerű virtuális asztali munkamenet-gazdagépek kijelölt Log Analytics munkaterületének használatával biztosíthatja, hogy a teljesítményszámlálók és az események csak a Windows rendszerű virtuális asztali környezetben lévő munkamenet-gazdagépekről legyenek gyűjtve.
- Az adatgyűjtés engedélyezése a Log Analytics munkaterületen a következő dolgokhoz:
    - Diagnosztika a Windows rendszerű virtuális asztali környezetből
    - Ajánlott teljesítményszámlálók a Windows rendszerű virtuális asztali munkamenet-gazdagépekről
    - Ajánlott Windows-eseménynaplók a Windows rendszerű virtuális asztali munkamenet-gazdagépekről

 Az ebben a cikkben ismertetett adatbeállítási folyamat az egyetlen, amelyet figyelnie kell a Windows rendszerű virtuális asztalon. A költségek megtakarítása érdekében letilthatja az adatokat küldő összes többi elemet a Log Analytics munkaterületen.

A Windows rendszerű virtuális asztali környezethez tartozó Azure Monitor figyeléséhez a következő olvasási hozzáférési engedélyekre is szüksége lesz:

- Olvasási hozzáférés a Windows rendszerű virtuális asztali erőforrásokkal rendelkező Azure-előfizetésekhez
- Olvasási hozzáférés az előfizetés azon csoportjaihoz, amelyek a Windows rendszerű virtuális asztali munkamenet-gazdagépeket tárolják
- Olvasási hozzáférés a Log Analytics munkaterülethez vagy munkaterületekhez

>[!NOTE]
> Az olvasási hozzáférés csak a rendszergazdák számára teszi lehetővé az adatmegjelenítést. A Windows rendszerű virtuális asztali portál erőforrásainak kezeléséhez eltérő engedélyekre van szükségük.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>A Windows rendszerű virtuális asztali számítógép megnyitása Azure Monitor

A Windows rendszerű virtuális asztali Azure Monitor a következő módszerek egyikével nyitható meg:

- Nyissa meg a [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks).
- Keresse meg **és válassza ki** a **Windows rendszerű virtuális asztal** elemet a Azure Portal, majd válassza az eredmények lehetőséget.
- Keresse meg és válassza ki **Azure monitor** a Azure Portal. Az Áttekintés területen válassza az **ininsight hub** **lehetőséget, majd válassza a** **Windows virtuális asztal** lehetőséget.
Miután megnyitotta a lapot, adja meg a figyelni kívánt környezet **előfizetését**, **erőforráscsoportját**, **állomásnevét** és **időtartományát** .

>[!NOTE]
>A Windows rendszerű virtuális asztali szolgáltatás jelenleg csak egy előfizetés, erőforráscsoport és címkészlet figyelését támogatja. Ha nem találja a figyelni kívánt környezetet, tekintse meg a [hibaelhárítási dokumentációt](troubleshoot-azure-monitor.md) az ismert funkciókra vonatkozó kérések és problémák megoldásához.

## <a name="log-analytics-settings"></a>Log Analytics beállítások

A Windows rendszerű virtuális asztalok Azure Monitor használatának megkezdéséhez legalább egy Log Analytics-munkaterületre szüksége lesz. A Windows rendszerű virtuális asztali munkamenet-gazdagépek kijelölt Log Analytics munkaterületének használatával biztosíthatja, hogy a teljesítményszámlálók és az események csak a Windows rendszerű virtuális asztali környezetből származó űrlapos munkamenet-gazdagépeket gyűjtsék. Ha már beállított egy munkaterületet, ugorjon előre a [konfigurációs munkafüzet használatával történő beállításhoz](#set-up-using-the-configuration-workbook). A beállításhoz lásd: [log Analytics munkaterület létrehozása a Azure Portalban](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>A Log Analytics szabványos adattárolási díjait fogja alkalmazni. A kezdéshez azt javasoljuk, hogy válassza az utólagos elszámolású modellt, és állítsa be az üzembe helyezés méretezését, és vegyen fel több adatmennyiséget. További információ: [Azure monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="set-up-using-the-configuration-workbook"></a>Beállítás a konfigurációs munkafüzet használatával

Ha első alkalommal nyit Azure Monitor a Windows rendszerű virtuális asztali számítógépeken, be kell állítania Azure Monitor a Windows rendszerű virtuális asztali környezethez. Az erőforrások konfigurálása:

1. Nyissa meg a Windows rendszerű virtuális asztali Azure Monitor a [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)-on található Azure Portal, majd válassza a **konfigurációs munkafüzet** lehetőséget.
2. Válassza ki az **előfizetés**, az **erőforráscsoport** és az **alkalmazáskészlet** területen konfigurálni kívánt környezetet.

A konfigurációs munkafüzet beállítja a figyelési környezetet, és lehetővé teszi a konfiguráció ellenőrzését a telepítési folyamat befejezése után. Fontos, hogy ellenőrizze a konfigurációt, ha az irányítópulton lévő elemek nem jelennek meg megfelelően, vagy ha a termékcsoport olyan frissítéseket tesz közzé, amelyek új beállításokat igényelnek.

### <a name="resource-diagnostic-settings"></a>Erőforrás-diagnosztikai beállítások

A Windows rendszerű virtuális asztali infrastruktúrával kapcsolatos információk gyűjtéséhez engedélyeznie kell több diagnosztikai beállítást a Windows rendszerű virtuális asztali gazdagépeken és munkaterületeken (ez a Windows virtuális asztal munkaterülete, nem az Log Analytics-munkaterület). További információ a gazdagép-készletekről, a munkaterületekről és az egyéb Windows rendszerű virtuális asztali erőforrás-objektumokról: [környezeti útmutató](environment-setup.md).

További információt a Windows rendszerű virtuális asztali diagnosztika és a támogatott diagnosztikai táblázatok a [Windows rendszerű virtuális asztali diagnosztika küldése log Analyticsre](diagnostics-log-analytics.md)című témakörben olvashat.

Az erőforrás-diagnosztikai beállítások beállítása a konfigurációs munkafüzetben: 

1. Válassza ki a konfigurációs munkafüzet **erőforrás-diagnosztikai beállítások** lapját. 
2. Válassza **log Analytics munkaterületet** a Windows rendszerű virtuális asztali diagnosztika elküldéséhez. 

#### <a name="host-pool-diagnostic-settings"></a>Gazdagépkészlet diagnosztikai beállításai

A gazdagép-diagnosztika beállítása a konfigurációs munkafüzet erőforrás diagnosztikai beállítások szakaszának használatával:

1. Az **alkalmazáskészlet** területen ellenőrizze, hogy engedélyezve van-e a Windows rendszerű virtuális asztali diagnosztika. Ha nem, a rendszer hibaüzenetet jelenít meg, amely szerint a kiválasztott gazdagéphez nem található létező diagnosztikai konfiguráció. A következő támogatott diagnosztikai táblákat kell engedélyeznie:

    - Checkpoint
    - Hiba
    - Kezelés
    - Kapcsolat
    - Gazdagép regisztrációja
    - AgentHealthStatus
    
    >[!NOTE]
    > Ha nem látja a hibaüzenetet, nem kell végrehajtania a 2 – 4. lépést.

2. Válassza az **alkalmazáskészlet konfigurálása** lehetőséget.
3. Válassza az **Üzembe helyezés** lehetőséget.
4. Frissítse a konfigurációs munkafüzetet.

#### <a name="workspace-diagnostic-settings"></a>Munkaterület diagnosztikai beállításai 

A munkaterület-diagnosztika beállítása a konfigurációs munkafüzet erőforrás-diagnosztikai beállítások szakaszának használatával:

 1. A **munkaterület** területen ellenőrizze, hogy engedélyezve van-e a Windows rendszerű virtuális asztali diagnosztika a Windows rendszerű virtuális asztal munkaterületen. Ha nem, hibaüzenet jelenik meg, amely szerint a kiválasztott munkaterülethez nem található létező diagnosztikai konfiguráció. A következő támogatott diagnosztikai táblákat kell engedélyeznie:
 
    - Checkpoint
    - Hiba
    - Kezelés
    - Adatcsatorna
    
    >[!NOTE]
    > Ha nem látja a hibaüzenetet, nem kell végrehajtania a 2-4. lépést.

2. Válassza a **munkaterület konfigurálása** lehetőséget.
3. Válassza az **Üzembe helyezés** lehetőséget.
4. Frissítse a konfigurációs munkafüzetet.

### <a name="session-host-data-settings"></a>Munkamenet-gazdagép adatbeállításai

Ha információkat szeretne gyűjteni a Windows rendszerű virtuális asztali munkamenet-gazdagépekről, telepítenie kell a Log Analytics ügynököt a gazdagép összes munkamenet-gazdagépére, győződjön meg arról, hogy a munkamenet-gazdagépek elküldve Log Analytics munkaterületre, és konfigurálja a Log Analytics-ügynök beállításait a Teljesítményadatok és a Windows-eseménynaplók összegyűjtéséhez.

A munkamenet-gazdagépek adatküldési Log Analytics munkaterületének nem kell megegyeznie a diagnosztikai adatküldéshez. Ha a Windows rendszerű virtuális asztali környezeten kívüli Azure-munkamenetek is vannak, javasoljuk, hogy a Windows rendszerű virtuális asztali munkamenet-gazdagépek számára kijelölt Log Analytics munkaterülettel rendelkezzen. 

Az Log Analytics munkaterület beállítása, amelyben a munkamenet-gazdagép adatait össze szeretné gyűjteni: 

1. Válassza ki a **munkamenet-gazdagép adatbeállítások** lapját a konfigurációs munkafüzetben. 
2. Válassza ki azt a **log Analytics munkaterületet** , amelyhez munkamenet-gazdagépet szeretne küldeni. 

#### <a name="session-hosts"></a>Munkamenet-gazdagépek

Telepítenie kell a Log Analytics ügynököt a gazdagép összes munkamenet-gazdagépére, és el kell küldenie azokat az adott gazdagépekről a kiválasztott Log Analytics-munkaterületre. Ha a Log Analytics nincs konfigurálva a gazdagép összes munkamenet-gazdagépéhez, megjelenik egy **munkamenet** -gazdagépek szakasz a **munkamenet-állomás adatbeállításainak** tetején a "néhány gazdagép a gazdagépen, amely nem küld adatokat a kiválasztott log Analytics munkaterületre."

>[!NOTE]
> Ha nem látja a **munkamenet-gazdagépek** szakaszt vagy hibaüzenetet, a rendszer minden munkamenet-gazdagépet helyesen állít be. Ugorjon előre a [munkaterület](#workspace-performance-counters)-teljesítményszámlálók útmutatásának beállításához.

A fennmaradó munkamenet-gazdagépek beállítása a konfigurációs munkafüzet használatával:

1. Válassza **a gazdagépek hozzáadása munkaterülethez** lehetőséget. 
2. Frissítse a konfigurációs munkafüzetet.

>[!NOTE]
>A Log Analytics-bővítmény telepítéséhez a gazdagépnek futnia kell. Ha az automatikus központi telepítés nem működik, manuálisan is telepítheti a bővítményt egy gazdagépre. A bővítmény manuális telepítésével kapcsolatos további információkért lásd: [log Analytics virtuálisgép-bővítmény a Windowshoz](../virtual-machines/extensions/oms-windows.md).

#### <a name="workspace-performance-counters"></a>Munkaterület teljesítményszámlálói

A teljesítményadatok a munkamenet-gazdagépekről való összegyűjtéséhez és a Log Analytics munkaterületre való elküldéséhez engedélyeznie kell az adott teljesítményszámlálók számára.

Ha már engedélyezve vannak a teljesítményszámlálók, és el szeretné távolítani őket, kövesse a [teljesítményszámlálók konfigurálása](../azure-monitor/agents/data-sources-performance-counters.md)című témakör útmutatását. A teljesítményszámlálók ugyanabban a helyen adhatók hozzá és távolíthatók el.

Teljesítményszámlálók beállítása a konfigurációs munkafüzet használatával: 

1. A konfigurációs munkafüzet **munkaterület teljesítményszámlálói** területén tekintse meg a **konfigurált számlálókat** , és tekintse meg a már engedélyezett számlálókat a log Analytics munkaterületre való küldéshez. Ellenőrizze a **hiányzó számlálókat** , és győződjön meg arról, hogy engedélyezte az összes szükséges számlálót.
2. Ha hiányoznak a számlálók, válassza a **teljesítményszámlálók konfigurálása** lehetőséget.
3. Válassza a **konfiguráció alkalmazása** lehetőséget.
4. Frissítse a konfigurációs munkafüzetet.
5. Győződjön meg arról, hogy az összes szükséges számláló engedélyezve van a **hiányzó számlálók** listájának ellenőrzésével. 

#### <a name="configure-windows-event-logs"></a>Windows-eseménynaplók konfigurálása

Az egyes Windows-eseménynaplókat is engedélyeznie kell a hibák, figyelmeztetések és információk összegyűjtéséhez a munkamenet-gazdagépekről, és elküldeni őket a Log Analytics munkaterületre.

Ha már engedélyezte a Windows-eseménynaplókat, és el szeretné távolítani őket, kövesse a [Windows-eseménynaplók konfigurálása](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)című témakör útmutatását.  Windows-eseménynaplókat is hozzáadhat és eltávolíthat ugyanazon a helyen.

Windows-eseménynaplók beállítása a konfigurációs munkafüzet használatával:

1. A **Windows-eseménynaplók konfigurálása** területen ellenőrizze, hogy **vannak** -e olyan eseménynaplók, amelyekkel már engedélyezte az log Analytics munkaterületre való küldést. Ellenőrizze, hogy vannak-e a **hiányzó eseménynaplók** , és győződjön meg arról, hogy engedélyezte az összes Windows eseménynaplót.
2. Ha hiányoznak a Windows-eseménynaplók, válassza az **események konfigurálása** lehetőséget.
3. Válassza az **Üzembe helyezés** lehetőséget.
4. Frissítse a konfigurációs munkafüzetet.
5. Győződjön meg arról, hogy a szükséges Windows-eseménynaplók engedélyezve vannak a **hiányzó eseménynaplók** listájának ellenőrzésével. 

>[!NOTE]
>Ha az automatikus esemény üzembe helyezése sikertelen, válassza az **ügynök konfigurációjának megnyitása** lehetőséget a konfigurációs munkafüzetben a hiányzó Windows-eseménynaplók manuális hozzáadásához. 

## <a name="optional-configure-alerts"></a>Nem kötelező: riasztások konfigurálása

A Windows rendszerű virtuális asztali Azure Monitor lehetővé teszi a kiválasztott előfizetésen belüli Azure Monitor riasztások figyelését a Windows rendszerű virtuális asztali adatai környezetében. Azure Monitor a riasztások az Azure-előfizetések választható funkciói, és külön kell beállítani azokat a Windows rendszerű virtuális asztali Azure Monitor. A Azure Monitor riasztások keretrendszer használatával egyéni riasztásokat állíthat be a Windows rendszerű virtuális asztali eseményekre, diagnosztikare és erőforrásokra vonatkozóan. A Azure Monitor riasztásokkal kapcsolatos további információkért lásd: [az eseményekre való reagálás Azure monitor riasztásokkal](../azure-monitor/alerts/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Diagnosztikai és használati adatok

A Microsoft a Azure Monitor szolgáltatás használatával automatikusan gyűjti a használati és teljesítményadatokat. A Microsoft ezeket az adatmennyiségeket használja a szolgáltatás minőségének, biztonságának és integritásának javítására.

A pontos és hatékony hibaelhárítási képességek biztosításához az összegyűjtött adatok tartalmazzák a portál munkamenet-AZONOSÍTÓját, Azure Active Directory felhasználói AZONOSÍTÓját és a portál lap nevét, ahol az esemény történt. A Microsoft nem gyűjt neveket, címeket és más kapcsolattartási adatokat.

További információ az adatok gyűjtéséről és használatáról: a [Microsoft Online Services adatvédelmi nyilatkozata](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>A szolgáltatás által gyűjtött személyes adatok megtekintésének és törlésének megismeréséhez tekintse meg [a GDPR kapcsolatos Azure-beli adattulajdonosi kérelmeket](/microsoft-365/compliance/gdpr-dsr-azure). A GDPR kapcsolatos további információkért tekintse meg a [szolgáltatás-megbízhatósági portál GDPR című szakaszát](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Következő lépések

Most, hogy konfigurálta Azure Monitor a Windows rendszerű virtuális asztali környezethez, az alábbiakban néhány olyan erőforrást talál, amelyek segíthetnek a környezet figyelésének megkezdésében:

- Tekintse meg a [szószedetet](azure-monitor-glossary.md) , és ismerkedjen meg a Windows rendszerű virtuális asztalok Azure Monitorával kapcsolatos feltételekkel és fogalmakkal.
- Ha problémába ütközik, tekintse meg a Súgó és az ismert problémák [hibaelhárítási útmutatóját](troubleshoot-azure-monitor.md) .
