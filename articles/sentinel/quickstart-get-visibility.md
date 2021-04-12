---
title: 'Gyorsútmutató: Bevezetés az Azure Sentinel használatába'
description: Ebből a rövid útmutatóból megtudhatja, hogyan tekintheti meg és figyelheti meg, hogy mi történik a környezetében az Azure Sentinel használatával.
services: sentinel
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: c6c99dc08c701116fa8bc522128f625c69bab4f5
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063848"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Gyorsútmutató: Bevezetés az Azure Sentinel használatába




Ebből a rövid útmutatóból megtudhatja, hogyan lehet gyorsan megtekinteni és figyelni, hogy mi történik a környezetben az Azure Sentinel használatával. Miután csatlakoztatta az adatforrásokat az Azure Sentinelhez, azonnal megjelenítheti és elemezheti az adatait, hogy tudja, mi történik az összes csatlakoztatott adatforráson. Az Azure Sentinel olyan munkafüzeteket biztosít, amelyek biztosítják az Azure-ban már elérhető eszközök teljes körű hatékonyságát, valamint a beépített táblázatokat és diagramokat, amelyek lehetővé teszik a naplók és lekérdezések elemzését. Használhatja a beépített munkafüzeteket, vagy létrehozhat egy új munkafüzetet egyszerűen, akár teljesen, akár egy meglévő munkafüzet alapján. 

## <a name="get-visualization"></a>Vizualizáció beolvasása

A környezettel kapcsolatos események megjelenítéséhez és elemzéséhez először tekintse meg az Áttekintés irányítópultot, és kérje meg a szervezete biztonsági helyzetének a megismerését. A csempék minden elemére kattintva megtekintheti azokat a nyers adatforrásokat, amelyekről létrehozták őket. A zaj csökkentése és a riasztások számának csökkentése érdekében az Azure Sentinel fúziós technikát használ a riasztások incidensekkel való összekapcsolásához. Az **incidensek** a kapcsolódó riasztások csoportjai, amelyek együttesen megvizsgálható és elhárítható, végrehajtható incidenseket hoznak létre.

- A Azure Portal válassza az Azure Sentinel lehetőséget, majd válassza ki a figyelni kívánt munkaterületet.

  ![Azure Sentinel – áttekintés](./media/qs-get-visibility/overview.png)

- A felső eszköztáron látható, hogy hány esemény van kiválasztva a kiválasztott időszakban, és összehasonlítja azt az előző 24 órában. Az eszköztár tájékoztat ezekről az eseményekről, az aktivált riasztásokról (a kis szám az elmúlt 24 órában változik), majd elmondja Önnek, hogy hányan nyitott, folyamatban és lezárult. Ellenőrizze, hogy az események száma nem drámai növekedést vagy csökkenést eredményez-e. Ha van eldobás, lehetséges, hogy a kapcsolatok leállították a jelentést az Azure Sentinelnek. Ha nő a növekedés, előfordulhat, hogy gyanús esemény történt. Ellenőrizze, hogy vannak-e új riasztások.

   ![Azure Sentinel-számlálók](./media/qs-get-visibility/funnel.png)

Az áttekintő lap fő törzse áttekintést nyújt a munkaterület biztonsági állapotáról:

- **Események és riasztások az idő múlásával**: felsorolja az események számát, valamint azt, hogy az események hány riasztást hoztak létre. Ha olyan tüske jelenik meg, amely szokatlan, akkor riasztásokat kell látnia – ha van valami szokatlan, hogy van-e olyan esemény, amely az események között van, de nem látja a riasztásokat, aggodalomra adhat okot.

- **Lehetséges kártékony események**: Ha a rendszer forgalmat észlel a kártékonyként ismert forrásokból, az Azure Sentinel riasztást küld a térképen. Ha a narancssárga látható, akkor bejövő forgalom: valaki megpróbál hozzáférni a szervezethez egy ismert kártékony IP-címről. Ha a kimenő (vörös) tevékenységet látja, az azt jelenti, hogy a hálózatról származó adatok a szervezetből egy ismert kártékony IP-címről áramlanak.

   ![Rosszindulatú forgalom térképe](./media/qs-get-visibility/map.png)

- **Legutóbbi incidensek**: a legutóbbi incidensek, súlyosságuk és az incidenshez kapcsolódó riasztások számának megtekintése. Ha egy adott típusú riasztás hirtelen csúcsa jelenik meg, az azt jelentheti, hogy jelenleg fut aktív támadás. Ha például egy hirtelen 20 Pass-The-hash típusú eseményt használ a Microsoft Defender for Identity (korábbi nevén Azure ATP) számára, akkor lehetséges, hogy valaki jelenleg próbálkozik a támadással.

- **Adatforrások rendellenességei**: a Microsoft adatelemzői olyan modelleket hoztak létre, amelyek folyamatosan keresik az adatforrásokból származó adatokkal kapcsolatos rendellenességeket. Ha nincs rendellenesség, semmi sem jelenik meg. Ha rendellenességek észlelhetők, érdemes részletesen bemutatni, hogy mi történt. Kattintson például az Azure-tevékenység csúcsára. A **diagramra** kattintva megtekintheti, hogy mikor történt a nyárs, majd szűrheti az adott időszakban bekövetkezett tevékenységeket, hogy megtudja, mi okozta a nyársat.

   ![Rendellenes adatforrások](./media/qs-get-visibility/anomolies.png)

## <a name="use-built-in-workbooks"></a>Beépített munkafüzetek használata<a name="dashboards"></a>

A beépített munkafüzetek integrált adatokkal szolgálnak a csatlakoztatott adatforrásokból, így részletesen megismerheti az ezekben a szolgáltatásokban létrehozott eseményeket. A beépített munkafüzetek közé tartoznak az Azure AD, az Azure-tevékenység eseményei és a helyszíni adatok, amelyek a kiszolgálókról származó Windows-eseményekről, az első féltől származó riasztásokból, bármely harmadik féltől, például a tűzfal forgalmi naplóitól, az Office 365-től és a Windows-események alapján nem biztonságos protokollokból származnak. A munkafüzetek Azure Monitor munkafüzeteken alapulnak, így jobb testreszabhatóság és rugalmasság biztosítható a saját munkafüzetek tervezésekor. További információ: [munkafüzetek](../azure-monitor/visualize/workbooks-overview.md).

1. A **Beállítások** területen válassza a **munkafüzetek** elemet. A **telepítés** alatt látható az összes telepített munkafüzet. A **Mind** területen láthatja a telepíthető beépített munkafüzetek teljes katalógusát. 
2. Egy adott munkafüzet keresésével megtekintheti az egyes ajánlatok teljes listáját és leírását. 
3. Feltételezve, hogy az Azure AD-t használja az Azure Sentinel használatának megkezdéséhez, javasoljuk, hogy legalább a következő munkafüzeteket telepítse:
   - **Azure ad**: használja az alábbiak egyikét vagy mindkettőt:
       - Az **Azure ad-bejelentkezések** időben elemzik a bejelentkezéseket, hogy megjelenjenek-e anomáliák. Ez a munkafüzet sikertelen bejelentkezéseket biztosít az alkalmazásokhoz, eszközökhöz és helyekhez, így észreveszi, hogy valami szokatlan történik. Figyeljen a több sikertelen bejelentkezésre. 
       - Az **Azure ad-naplók** elemzik a rendszergazdai tevékenységeket, például a felhasználók változásait (Hozzáadás, eltávolítás stb.), a csoportok létrehozását és a módosításokat.  

   - Adjon hozzá egy munkafüzetet a tűzfalhoz. Adja meg például a Palo Alto-munkafüzetet. A munkafüzet elemzi a tűzfal forgalmát, és összekapcsolja a tűzfal adatai és a veszélyforrás események közötti összefüggéseket, és kiemeli a gyanús eseményeket az entitások között. A munkafüzetek adatokat biztosítanak a forgalom trendjeiről, és lehetővé teszik az eredmények részletezését és szűrését. 

      ![Palo Alto-irányítópult](./media/qs-get-visibility/palo-alto-week-query.png)


A munkafüzeteket testreszabhatja úgy, hogy szerkeszti a fő lekérdezési ![ lekérdezés szerkesztése gombot ](./media/qs-get-visibility/edit-query-button.png) . A gombra kattintva megnyithatja a ![ log Analytics gombot ](./media/qs-get-visibility/go-to-la-button.png) [log Analytics a lekérdezés szerkesztéséhez](../azure-monitor/logs/log-analytics-tutorial.md), és kiválaszthatja a három pontot (...), és kiválaszthatja a **csempe adatainak testreszabása** lehetőséget, amely lehetővé teszi a fő Időszűrő szerkesztését, vagy eltávolíthatja a munkafüzetből az adott csempéket.

További információ a lekérdezések használatáról [: oktatóanyag: vizualizációs adatok log Analytics](../azure-monitor/visualize/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Új csempe hozzáadása

Ha új csempét szeretne felvenni, hozzáadhatja egy meglévő munkafüzethez, vagy létrehozhatja azt, vagy egy beépített Azure Sentinel-munkafüzetet. 
1. A Log Analytics-ban hozzon létre egy csempét az [oktatóanyag: vizualizációs adatLog Analyticsokban](../azure-monitor/visualize/tutorial-logs-dashboards.md)található útmutatások segítségével. 
2. Miután létrehozta a csempét, a **PIN** elemnél válassza ki azt a munkafüzetet, amelyben a csempét meg szeretné jeleníteni.

## <a name="create-new-workbooks"></a>Új munkafüzetek létrehozása
Létrehozhat egy új munkafüzetet a semmiből, vagy használhat egy beépített munkafüzetet az új munkafüzet alapjaként.

1. Ha új munkafüzetet szeretne létrehozni a semmiből, válassza a **munkafüzetek** , majd az **+ Új munkafüzet** lehetőséget.
2. Válassza ki azt az előfizetést, amelyben létrehozta a munkafüzetet, és adjon meg egy leíró nevet. Minden munkafüzet egy olyan Azure-erőforrás, mint bármely más, és hozzárendelheti az IT-szerepköröket (Azure RBAC) annak meghatározásához és korlátozásához, hogy ki férhet hozzá. 
3. Annak lehetővé tétele, hogy megjelenjen a munkafüzetekben a vizualizációk rögzítéséhez, meg kell osztania azt. Kattintson a **megosztás** , majd a **felhasználók kezelése** lehetőségre. 
 
1. A **hozzáférés** -és **szerepkör-hozzárendeléseket** a többi Azure-erőforráshoz hasonlóan használhatja. További információ: Azure- [munkafüzetek megosztása az Azure RBAC használatával](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-workbook-examples"></a>Új munkafüzetpéldák

A következő minta lekérdezéssel összehasonlíthatja a forgalom trendjeinek hetek közötti összehasonlítását. Egyszerűen átválthatja, hogy melyik eszköz gyártója és adatforrás futtatja a lekérdezést. Ez a példa a Windows SecurityEvent használja, átválthatja a AzureActivity-on vagy a CommonSecurityLog-on való futtatásra bármely más tűzfalon.

```console
// week over week query
SecurityEvent
| where TimeGenerated > ago(14d)
| summarize count() by bin(TimeGenerated, 1d)
| extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)
```

Előfordulhat, hogy olyan lekérdezést szeretne létrehozni, amely több forrásból származó adatokkal rendelkezik. Létrehozhat egy olyan lekérdezést, amely megkeresi Azure Active Directory naplókat az imént létrehozott új felhasználók számára, majd ellenőrzi az Azure-naplókat, hogy a felhasználó elindította-e a szerepkör-hozzárendelés változásait 24 órán belül. Ez az irányítópulton a gyanús tevékenység jelenik meg:

```console
AuditLogs
| where OperationName == "Add user"
| project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
| join (AzureActivity
| where OperationName == "Create role assignment"
| project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
| project-away user1
```

Különböző munkafüzeteket hozhat létre az adatelemzést végző személy és a keresett személyek szerepköre alapján. Létrehozhat például egy olyan munkafüzetet a hálózati rendszergazdához, amely tartalmazza a tűzfal adatait. Létrehozhat olyan munkafüzeteket is, amelyek alapján gyakran szeretné megtekinteni őket, hogy vannak-e olyan dolgok, amelyeket naponta szeretne áttekinteni, és ha szeretné, hogy a rendszer óránként egyszer ellenőrizni kívánja az Azure AD-bejelentkezéseket, akkor a rendellenességek keresése érdekében minden órában érdemes megkeresni a kívánt adatokat. 

## <a name="create-new-detections"></a>Új észlelések létrehozása

Az [Azure sentinelhez csatlakoztatott adatforrások](connect-data-sources.md) észlelésének előállítása a szervezeten belüli fenyegetések kivizsgálásához.

Új észlelés létrehozásakor kihasználhatja a Microsoft biztonsági kutatói által készített beépített észleléseket, amelyek a csatlakoztatott adatforrásokhoz vannak igazítva.

Az összes beépített észlelés megtekintéséhez lépjen az **elemzés** , majd a **szabály sablonok** elemre. Ezen a lapon az Azure Sentinel beépített szabályai láthatók.

   ![A fenyegetések felderítése az Azure Sentinel használatával beépített észlelésekkel](media/tutorial-detect-built-in/view-oob-detections.png)

A beépített észlelésekkel kapcsolatos további információkért lásd az [oktatóanyag: beépített elemzések](tutorial-detect-threats-built-in.md)beszerzése című témakört.
 
## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban megtanulta, hogyan kezdheti el az Azure Sentinel használatát. Folytassa az Oktatóanyaggal a [fenyegetések észleléséhez](tutorial-detect-threats-built-in.md).
> [!div class="nextstepaction"]
> [Hozzon létre egyéni veszélyforrás-észlelési szabályokat](tutorial-detect-threats-custom.md) a fenyegetésekre adott válaszok automatizálására.
