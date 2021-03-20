---
title: A Windows rendszerű virtuális asztali monitor előzetes verziójának használata – Azure
description: A Azure Monitor használata a Windows rendszerű virtuális asztali gépekhez.
author: Heidilohr
ms.topic: how-to
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e9da1071686dafa003a5a49d0864b77644493344
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594455"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>Az üzembe helyezés figyelése a Windows rendszerű virtuális asztali Azure Monitor használatával (előzetes verzió)

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali Azure Monitor jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Windows rendszerű virtuális asztali Azure Monitor (előzetes verzió) egy olyan irányítópult, amely Azure Monitor-munkafüzetekre épül, és az informatikai szakemberek megismerhetik a Windows rendszerű virtuális asztali környezeteket. Ebből a témakörből megtudhatja, hogyan állíthatja be Azure Monitor a Windows rendszerű virtuális asztal számára a Windows rendszerű virtuális asztali környezetek figyeléséhez.

## <a name="requirements"></a>Követelmények

Mielőtt megkezdené a Windows rendszerű virtuális asztali Azure Monitor használatát, be kell állítania a következő dolgokat:

- Az összes Windows rendszerű virtuális asztali környezetnek a Azure Resource Manager rendszerrel kompatibilis Windows-alapú virtuális asztal legújabb kiadásán kell alapulnia.

- Legalább egy konfigurált Log Analytics munkaterület.

- Az adatgyűjtés engedélyezése a Log Analytics munkaterületen a következő dolgokhoz:
    - A szükséges teljesítményszámlálók
    - A Windows rendszerű virtuális asztali Azure Monitorban használt teljesítményszámlálók és események
    - A diagnosztikai eszközről származó adatok a környezetben található összes objektumra vonatkozóan.
    - A környezetben figyelni kívánt virtuális gépek (VM-EK).

A Windows rendszerű virtuális asztali környezethez tartozó Azure Monitor figyeléséhez a következő olvasási hozzáférési engedélyekre is szüksége lesz:

- Olvasási hozzáférés az erőforráscsoporthoz, ahol a környezet erőforrásai találhatók.

- Olvasási hozzáférés azon erőforráscsoport (ok) hoz, ahol a környezet munkamenet-gazdagépei találhatók

>[!NOTE]
> Az olvasási hozzáférés csak a rendszergazdák számára teszi lehetővé az adatmegjelenítést. A Windows rendszerű virtuális asztali portál erőforrásainak kezeléséhez eltérő engedélyekre van szükségük.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>A Windows rendszerű virtuális asztali számítógép megnyitása Azure Monitor

A Windows rendszerű virtuális asztali Azure Monitor a következő módszerek egyikével nyitható meg:

- Nyissa meg a [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks).

- Keresse meg **és válassza ki** a **Windows rendszerű virtuális asztal** elemet a Azure Portal, majd válassza az eredmények lehetőséget.

- Keresse meg és válassza ki **Azure monitor** a Azure Portal. Az **Áttekintés területen válassza** az adatellenőrzési **központ** **lehetőséget,** majd a **Windows rendszerű virtuális asztal** területen nyissa meg az irányítópultot a Azure monitor oldalon.

Miután megnyitotta Azure Monitor a Windows rendszerű virtuális asztal megnyitásához, jelölje be az **előfizetés**, az **erőforráscsoport**, a **címkészlet** és az **időtartomány** címkével ellátott jelölőnégyzetek egyikét a figyelni kívánt környezet alapján.

>[!NOTE]
>A Windows rendszerű virtuális asztali szolgáltatás jelenleg csak egy előfizetés, erőforráscsoport és címkészlet figyelését támogatja. Ha nem találja a figyelni kívánt környezetet, tekintse meg a [hibaelhárítási dokumentációt](troubleshoot-azure-monitor.md) az ismert funkciókra vonatkozó kérések és problémák megoldásához.

## <a name="set-up-with-the-configuration-workbook"></a>Beállítás a konfigurációs munkafüzettel

Ha első alkalommal nyit Azure Monitor a Windows rendszerű virtuális asztalhoz, konfigurálnia kell Azure Monitor a Windows rendszerű virtuális asztali erőforrásokhoz. Az erőforrások konfigurálása:

1. Nyissa meg a munkafüzetet a Azure Portalban.
2. Válassza **a konfigurációs munkafüzet megnyitása** lehetőséget.

A konfigurációs munkafüzet beállítja a figyelési környezetet, és lehetővé teszi a konfiguráció ellenőrzését a telepítési folyamat befejezése után. Fontos, hogy ellenőrizze a konfigurációt, ha az irányítópulton található elemek nem jelennek meg megfelelően, vagy ha a termékcsoport további adatpontokat igénylő frissítéseket tesz közzé.

## <a name="host-pool-diagnostic-settings"></a>Gazdagépkészlet diagnosztikai beállításai

A szolgáltatást támogató Windowsos virtuális asztali környezetben lévő összes objektumon engedélyeznie kell Azure Monitor diagnosztikai beállításait.

1. Nyissa meg Azure Monitor a Windows rendszerű virtuális asztalnál a következő helyen: [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), majd válassza a **konfigurációs munkafüzet** elemet.

2. Válasszon ki egy, az **előfizetés**, az **erőforráscsoport** és az **alkalmazáskészlet** területen figyelni kívánt környezetet.

3. Ellenőrizze, hogy engedélyezve van-e a Windows rendszerű virtuális asztali diagnosztika a gazdagép-készlet **diagnosztikai beállításai** területen. Ha nem, a rendszer hibaüzenetet jelenít meg, amely szerint a kiválasztott gazdagéphez nem található létező diagnosztikai konfiguráció. 
   
   A következő táblákat kell engedélyezni:

    - Checkpoint
    - Hiba
    - Kezelés
    - Kapcsolat
    - Gazdagép regisztrációja

    >[!NOTE]
    > Ha nem látja a hibaüzenetet, nem kell végrehajtania a 4. lépést.

4. Válassza az **alkalmazáskészlet konfigurálása** lehetőséget.

5. Válassza az **Üzembe helyezés** lehetőséget.

6. Frissítse a munkafüzetet.

Ha többet szeretne megtudni arról, hogyan engedélyezheti a diagnosztika szolgáltatást a Windows rendszerű virtuális asztali környezetben lévő összes objektumon, vagy elérheti a Log Analytics munkaterületet a [Windows rendszerű virtuális asztali diagnosztika küldése a log Analytics](diagnostics-log-analytics.md).

## <a name="configure-log-analytics"></a>A Log Analytics konfigurálása

A Windows rendszerű virtuális asztalok Azure Monitor használatának megkezdéséhez szüksége lesz legalább egy Log Analytics-munkaterületre, hogy adatokat gyűjtsön a figyelni kívánt környezetből, és megadja azt a munkafüzetnek. Ha már van egy beállított beállítása, ugorjon előre a [teljesítményszámlálók beállítása](#set-up-performance-counters)elemre. Ha új Log Analytics munkaterületet szeretne beállítani a Windows rendszerű virtuális asztali környezetet tartalmazó Azure-előfizetéshez, tekintse meg a [log Analytics munkaterület létrehozása a Azure Portalben](../azure-monitor/logs/quick-create-workspace.md)című témakört.

>[!NOTE]
>A Log Analytics szabványos adattárolási díjait fogja alkalmazni. A kezdéshez azt javasoljuk, hogy válassza az utólagos elszámolású modellt, és állítsa be az üzembe helyezés méretezését, és vegyen fel több adatmennyiséget. További információ: [Azure monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="set-up-performance-counters"></a>Teljesítményszámlálók beállítása

A Log Analytics munkaterületen a megfelelő mintavételi intervallumban engedélyeznie kell a gyűjteményhez megadott teljesítményszámlálókat. Ezek a teljesítményszámlálók csak a Windows virtuális asztal figyeléséhez szükséges számlálók. Letilthatja az összes többiet a költségek megtakarítása érdekében.

Ha már engedélyezve vannak a teljesítményszámlálók, és el szeretné távolítani őket, kövesse a teljesítményszámlálók [konfigurálása](../azure-monitor/agents/data-sources-performance-counters.md) a teljesítményszámlálók újrakonfigurálásához című témakör útmutatását. Míg a cikk leírja, hogyan adhat hozzá számlálókat, de ugyanazon a helyen is eltávolíthatja őket.

Ha még nem állított be teljesítményszámlálókat, a következő módon konfigurálhatja őket a Windows rendszerű virtuális asztali Azure Monitorhoz:

1. Nyissa meg a [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), majd válassza ki a **konfigurációs munkafüzetet** az ablak alján.

2. A **log Analytics konfigurálása** területen válassza ki az előfizetéséhez beállított munkaterületet.

3. A **munkaterület teljesítményszámlálói** esetében megjelenik a figyeléshez szükséges számlálók listája. A lista jobb oldalán tekintse meg a **hiányzó számlálók** listán szereplő elemeket, hogy engedélyezze a számlálók figyelését a munkaterületen.

4. Válassza a **teljesítményszámlálók konfigurálása** lehetőséget.

5. Válassza a **konfiguráció alkalmazása** lehetőséget.

6. Frissítse a konfigurációs munkafüzetet, és folytassa a környezet beállítását.

Új teljesítményszámlálókat is hozzáadhat a kezdeti konfigurálás után, amikor a szolgáltatás frissül, és új figyelési eszközöket igényel. A **hiányzó számlálók** listában kiválasztva ellenőrizheti, hogy az összes szükséges számláló engedélyezve van-e.

>[!NOTE]
>A bemeneti késleltetési teljesítményszámlálók csak a Windows 10 RS5 és újabb, illetve a Windows Server 2019-es és újabb verzióival kompatibilisek.

További információ a gyűjteményhez még nem engedélyezett teljesítményszámlálók manuális hozzáadásáról: [teljesítményszámlálók konfigurálása](../azure-monitor/agents/data-sources-performance-counters.md).

### <a name="set-up-windows-events"></a>Windows-események beállítása

Ezután engedélyeznie kell bizonyos Windows-eseményeket a gyűjteményhez a Log Analytics munkaterületen. Az ebben a szakaszban ismertetett események az egyetlenek, Azure Monitor a Windows rendszerű virtuális asztali igényekhez. Letilthatja az összes többiet a költségek megtakarítása érdekében.

Windows-események beállítása:

1. Ha a Windows-események már engedélyezve vannak, és el szeretné távolítani őket, távolítsa el a nem kívánt eseményeket, mielőtt a konfigurációs munkafüzetet használja a figyeléshez szükséges beállítás engedélyezéséhez.

2. Nyissa meg a Azure Monitor a Windows rendszerű virtuális asztali gépen a [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)címen, majd válassza a **konfigurációs munkafüzet** elemet az ablak alján.

3. A **Windows-események konfigurációjában** a figyeléshez szükséges Windows-események listája látható. A lista jobb oldalán a **hiányzó események** lista látható, ahol megtalálhatja a munkaterülethez jelenleg nem engedélyezett kötelező események nevét és esemény-típusait. Jegyezze fel ezeket a neveket később.

4. Válassza a **munkaterület-konfiguráció megnyitása** lehetőséget.

5. Válassza **az** adatelemet.

6. Válassza a **Windows-eseménynaplók** lehetőséget.

7. Adja hozzá a hiányzó események nevét a 3. lépésben és a szükséges eseménytípus mindegyikhez.

8. Frissítse a konfigurációs munkafüzetet, és folytassa a környezet beállítását.

Új Windows-eseményeket adhat hozzá a kezdeti konfigurálás után, ha a figyelési eszköz frissítései új események engedélyezését igénylik. Győződjön meg arról, hogy az összes szükséges esemény engedélyezve van, lépjen vissza a **hiányzó események** listájára, és engedélyezze az ott látható hiányzó eseményeket.

## <a name="install-the-log-analytics-agent-on-all-hosts"></a>A Log Analytics-ügynök telepítése minden gazdagépre

Végezetül telepítenie kell a Log Analytics ügynököt a gazdagép összes gazdagépére, hogy az adatok a gazdagépekről a kiválasztott munkaterületre legyenek küldve.

A Log Analytics-ügynök telepítése:

1. Nyissa meg a Azure Monitor a Windows rendszerű virtuális asztali gépen a [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)címen, majd válassza a **konfigurációs munkafüzet** elemet az ablak alján.

2. Ha a Log Analytics nincs konfigurálva a gazdagép összes gazdagépén, hibaüzenet jelenik meg a Log Analytics konfigurációs szakasz alján, a következő üzenettel: "a gazdagép egyes gazdagépei nem küldenek adatokat a kijelölt Log Analytics munkaterületre." Válassza a **gazdagépek hozzáadása munkaterülethez** lehetőséget a kiválasztott gazdagépek hozzáadásához. Ha nem látja a hibaüzenetet, itt állíthatja le.

3. Frissítse a konfigurációs munkafüzetet.

>[!NOTE]
>A Log Analytics-bővítmény telepítéséhez a gazdagépnek futnia kell. Ha az automatikus központi telepítés sikertelen a gazdagépen, a bővítményt mindig manuálisan is telepítheti egy gazdagépre. A bővítmény manuális telepítésével kapcsolatos további információkért lásd: [log Analytics virtuálisgép-bővítmény a Windowshoz](../virtual-machines/extensions/oms-windows.md).

## <a name="optional-configure-alerts"></a>Nem kötelező: riasztások konfigurálása

A Windows rendszerű virtuális asztali számítógép Azure Monitor konfigurálható úgy, hogy értesítést kapjon, ha a kiválasztott előfizetésen belül bármilyen súlyos Azure Monitor riasztás történik. Ehhez kövesse a [válasz az eseményekre Azure monitor riasztásokkal](../azure-monitor/alerts/tutorial-response.md)című témakör utasításait.

## <a name="diagnostic-and-usage-data"></a>Diagnosztikai és használati adatok

A Microsoft a Azure Monitor szolgáltatás használatával automatikusan gyűjti a használati és teljesítményadatokat. A Microsoft ezeket az adatmennyiségeket használja a szolgáltatás minőségének, biztonságának és integritásának javítására.

A pontos és hatékony hibaelhárítási képességek biztosításához az összegyűjtött adatok tartalmazzák a portál munkamenet-AZONOSÍTÓját, Azure Active Directory felhasználói AZONOSÍTÓját és a portál lap nevét, ahol az esemény történt. A Microsoft nem gyűjt neveket, címeket és más kapcsolattartási adatokat.

További információ az adatok gyűjtéséről és használatáról: a [Microsoft Online Services adatvédelmi nyilatkozata](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>A szolgáltatás által gyűjtött személyes adatok megtekintésének és törlésének megismeréséhez tekintse meg [a GDPR kapcsolatos Azure-beli adattulajdonosi kérelmeket](/microsoft-365/compliance/gdpr-dsr-azure). A GDPR kapcsolatos további információkért tekintse meg a [szolgáltatás-megbízhatósági portál GDPR című szakaszát](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Következő lépések

Most, hogy konfigurálta a Windows rendszerű virtuális asztali Azure Portal, néhány olyan erőforrást tartalmaz, amelyek segíthetnek a következőkben:

- Tekintse meg a [szószedetet](azure-monitor-glossary.md) , és ismerkedjen meg a Windows rendszerű virtuális asztalok Azure Monitorával kapcsolatos feltételekkel és fogalmakkal.
- Ha probléma merül fel, segítségért tekintse meg a [hibaelhárítási útmutatót](troubleshoot-azure-monitor.md) .