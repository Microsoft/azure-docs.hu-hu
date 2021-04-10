---
title: Azure AD B2C figyelése Azure Monitor
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan naplózhatja Azure AD B2C eseményeit Azure Monitorekkel a delegált erőforrás-kezelés használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 01/29/2021
ms.openlocfilehash: 0231f7f3882218ef88d6151488da6aa23e4cb8e4
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106687"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Azure AD B2C figyelése Azure Monitor

A Azure Monitor használatával átirányíthatja Azure Active Directory B2C (Azure AD B2C) bejelentkezési és [naplózási](view-audit-logs.md) naplókat különböző figyelési megoldásokhoz. A naplókat megőrizheti hosszú távú használatra, vagy külső biztonságiinformáció- és eseménykezelési (SIEM-) eszközökkel integrálva elemezheti a környezetet.

A naplózási eseményeket a következő módon irányíthatja át:

* Egy Azure [Storage-fiók](../storage/blobs/storage-blobs-introduction.md).
* [Log Analytics munkaterület](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) (az adatelemzéshez, az irányítópultok létrehozásához és a riasztáshoz adott eseményeken).
* Egy Azure [Event hub](../event-hubs/event-hubs-about.md) (és integrálható a splunk és a szumó logikai példányokkal).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

Ebből a cikkből megtudhatja, hogyan viheti át a naplókat egy Azure Log Analytics-munkaterületre. Ezután létrehozhat egy irányítópultot, vagy létrehozhat olyan riasztásokat, amelyek Azure AD B2C felhasználói tevékenységeken alapulnak.

> [!IMPORTANT]
> Ha Azure AD B2C naplókat különböző figyelési megoldásokra vagy tárházba kívánja átvinni, vegye figyelembe a következőket. Azure AD B2C a naplók személyes adatfájlokat tartalmaznak. Ezeket az adatfeldolgozást olyan módon kell feldolgozni, amely gondoskodik a személyes adat megfelelő biztonságáról, beleértve a jogosulatlan vagy törvénytelen feldolgozás elleni védelmet a megfelelő technikai vagy szervezeti intézkedések használatával.


## <a name="deployment-overview"></a>Az üzembe helyezés áttekintése

A Azure AD B2C [Azure Active Directory monitorozást](../active-directory/reports-monitoring/overview-monitoring.md)használ. Az Azure AD B2C-bérlőn belüli Azure Active Directory *diagnosztikai beállításainak* engedélyezéséhez az [Azure Lighthouse](../lighthouse/concepts/azure-delegated-resource-management.md) használatával [delegálhat egy erőforrást](../lighthouse/concepts/azure-delegated-resource-management.md), amely lehetővé teszi a Azure ad B2C (a **szolgáltató**) számára az Azure ad-( **ügyfél**-) erőforrás felügyeletét. Miután elvégezte a cikkben leírt lépéseket, hozzáférhet az *Azure-ad-B2C-figyelő* erőforráscsoporthoz, amely tartalmazza a [log Analytics munkaterületet](../azure-monitor/logs/quick-create-workspace.md) a **Azure ad B2C** portálon. A naplókat a Azure AD B2C a Log Analytics-munkaterületre is továbbíthatja.

A telepítés során a Azure AD B2C-címtárban lévő felhasználó vagy csoport számára engedélyezi a Log Analytics Workspace-példány konfigurálását az Azure-előfizetést tartalmazó bérlőn belül. Az engedélyezés létrehozásához üzembe kell helyeznie egy [Azure Resource Manager](../azure-resource-manager/index.yml) sablont az előfizetést tartalmazó Azure ad-bérlőn.

Az alábbi ábrán az Azure AD-ben és a Azure AD B2C-bérlők által konfigurálandó összetevők láthatók.

![Erőforráscsoport-kivetítés](./media/azure-monitor/resource-group-projection.png)

A telepítés során a Azure AD B2C bérlőjét és az Azure AD-bérlőt is konfigurálni fogja, ahol a Log Analytics munkaterület üzemeltetve lesz. A Azure AD B2C fióknak hozzá kell rendelnie a [globális rendszergazdai](../active-directory/roles/permissions-reference.md#global-administrator) szerepkört a Azure ad B2C bérlőn. A központi telepítés futtatásához használt Azure AD-fiókot az Azure AD-előfizetésben hozzá kell rendelni a [tulajdonosi](../role-based-access-control/built-in-roles.md#owner) szerepkörhöz. Fontos, hogy ellenőrizze, hogy be van-e jelentkezve a megfelelő könyvtárba az egyes lépések végrehajtásakor.

## <a name="1-create-or-choose-resource-group"></a>1. erőforráscsoport létrehozása vagy kiválasztása

Először hozzon létre vagy válasszon ki egy erőforráscsoportot, amely tartalmazza a cél Log Analytics munkaterületet, amely a Azure AD B2Ctól érkező adatok fogadására használható. A Azure Resource Manager sablon telepítésekor meg kell adnia az erőforráscsoport nevét.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely tartalmazza az **Azure ad-bérlőt**.
1. [Hozzon létre egy erőforráscsoportot](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) , vagy válasszon ki egy meglévőt. Ez a példa egy *Azure-ad-B2C-monitor* nevű erőforráscsoportot használ.

## <a name="2-create-a-log-analytics-workspace"></a>2. Log Analytics munkaterület létrehozása

A **log Analytics munkaterület** egy egyedi környezet, amely Azure monitor naplózza az adatnaplót. Ezzel a Log Analytics munkaterülettel gyűjthet adatokat az Azure AD B2C [naplókból](view-audit-logs.md), majd megjelenítheti azokat lekérdezésekkel és munkafüzetekkel, vagy riasztásokat hozhat létre.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely tartalmazza az **Azure ad-bérlőt**.
1. [Log Analytics munkaterület létrehozása](../azure-monitor/logs/quick-create-workspace.md). Ez a példa egy *AzureAdB2C* nevű log Analytics munkaterületet használ az *Azure-ad-B2C-monitor* nevű erőforráscsoporthoz.

## <a name="3-delegate-resource-management"></a>3. erőforrás-kezelés delegálása

Ebben a lépésben kiválasztja a Azure AD B2C bérlőt **szolgáltatóként**. Megadhatja azokat az engedélyeket is, amelyekhez hozzá kell rendelnie a megfelelő Azure beépített szerepköröket az Azure AD-bérlőben található csoportokhoz.

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3,1 a Azure AD B2C bérlő AZONOSÍTÓjának beolvasása

Először szerezze be a Azure AD B2C könyvtára (más néven címtár-azonosító) **bérlői azonosítóját** .

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a **Azure ad B2C** bérlőjét tartalmazza.
1. Válassza a **Azure Active Directory** lehetőséget, majd az **Áttekintés** lehetőséget.
1. Jegyezze fel a **bérlő azonosítóját**.

### <a name="32-select-a-security-group"></a>3,2 biztonsági csoport kiválasztása

Most válasszon ki egy Azure AD B2C csoportot vagy felhasználót, amelyhez engedélyt szeretne adni az előfizetést tartalmazó könyvtárban korábban létrehozott erőforráscsoporthoz.  

A felügyelet egyszerűbbé tételéhez ajánlott az Azure AD felhasználói *csoportok* használata az egyes szerepkörökhöz, ami lehetővé teszi az egyes felhasználók hozzáadását vagy eltávolítását, nem pedig közvetlenül az adott felhasználóhoz rendel hozzá engedélyeket. Ebben az útmutatóban egy biztonsági csoportot adunk hozzá.

> [!IMPORTANT]
> Az Azure AD-csoport engedélyeinek hozzáadásához a **csoport típusát** **biztonsági** értékre kell állítani. Ez a beállítás a csoport létrehozásakor van kiválasztva. További információkért lásd: [alapszintű csoport létrehozása és Tagok hozzáadása Azure Active Directory használatával](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. Ha a **Azure Active Directory** továbbra is ki van választva a **Azure ad B2C** könyvtárban, válassza a **csoportok** lehetőséget, majd válasszon ki egy csoportot. Ha nem rendelkezik meglévő csoporttal, hozzon létre egy **biztonsági** csoportot, majd vegyen fel tagokat. További információért kövesse az [alapszintű csoport létrehozása és a tagok hozzáadása a Azure Active Directory használatával](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)című szakaszt. 
1. Válassza az **Áttekintés** lehetőséget, és jegyezze fel a csoport **objektumazonosítóát**.

### <a name="33-create-an-azure-resource-manager-template"></a>3,3 Azure Resource Manager sablon létrehozása

Ezután létre fog hozni egy Azure Resource Manager sablont, amely Azure AD B2C hozzáférést biztosít a korábban létrehozott Azure AD-erőforráscsoporthoz (például *Azure-ad-B2C-monitor*). Telepítse a sablont a GitHub-mintából az **üzembe helyezés az Azure** -ban gombbal, amely megnyitja a Azure Portal, és lehetővé teszi a sablon közvetlen konfigurálását és üzembe helyezését a portálon. A fenti lépésekben ellenőrizze, hogy be van-e jelentkezve az Azure AD-bérlőbe (nem a Azure AD B2C bérlőre).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely tartalmazza az **Azure ad** -bérlőt.
3. Az **üzembe helyezés az Azure** -ban gomb használatával nyissa meg a Azure Portal, és közvetlenül a portálon telepítse a sablont. További információ: [create a Azure Resource Manager template](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template).

   [![Üzembe helyezés az Azure-ban](https://aka.ms/deploytoazurebutton)](   https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure-ad-b2c%2Fsiem%2Fmaster%2Ftemplates%2FrgDelegatedResourceManagement.json)

5. Az **Egyéni telepítés** lapon adja meg a következő adatokat:

   | Mező   | Meghatározás |
   |---------|------------|
   | Előfizetés |  Válassza ki azt az Azure-előfizetést tartalmazó könyvtárat, amelyben az *Azure-ad-B2C-monitor* erőforráscsoport létrejött. |
   | Region| Válassza ki azt a régiót, ahol az erőforrást telepíteni kívánja.  | 
   | MSP-ajánlat neve| A definíciót leíró név. Például *Azure ad B2C figyelés*.  |
   | MSP-ajánlat leírása| Az ajánlat rövid leírása. Például *engedélyezi a Azure AD B2C Azure monitorét*.|
   | Bérlői azonosító kezeli| A Azure AD B2C bérlő (más néven címtár-azonosító) **bérlői azonosítója** . |
   |Engedélyek|Adja meg az Azure AD-t, a-t és az Azure-t tartalmazó JSON-tömböt `principalId` `principalIdDisplayName` `roleDefinitionId` . A a `principalId` B2C-csoport vagy-felhasználó **objektumazonosító** , amely hozzáfér az Azure-előfizetés erőforrásaihoz. Ebben a bemutatóban a korábban feljegyzett csoport objektumazonosító-AZONOSÍTÓját kell megadnia. A `roleDefinitionId` alkalmazásban használja a *közreműködő szerepkör* [beépített szerepkör](../role-based-access-control/built-in-roles.md) értékét `b24988ac-6180-42a0-ab88-20f7382dd24c` .|
   | RG neve | Az Azure AD-bérlőben korábban létrehozott erőforráscsoport neve. Például: *Azure-ad-B2C-monitor*. |

   Az alábbi példa egy engedélyezési tömböt mutat be egy biztonsági csoporttal.

   ```json
   [
       {
           "principalId": "<Replace with group's OBJECT ID>",
           "principalIdDisplayName": "Azure AD B2C tenant administrators",
           "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
       }
   ]
   ```

A sablon üzembe helyezése után néhány percet is igénybe vehet (általában legfeljebb öt) az erőforrás-leképezés befejezéséhez. Ellenőrizheti az üzembe helyezést az Azure AD-bérlőben, és lekérheti az erőforrás-kivetítés részleteit. További információ: szolgáltatók [megtekintése és kezelése](../lighthouse/how-to/view-manage-service-providers.md).

## <a name="4-select-your-subscription"></a>4. Válassza ki az előfizetését

Miután telepítette a sablont, és néhány percet várt az erőforrás-kivetítés befejezéséhez, kövesse az alábbi lépéseket az előfizetés az Azure AD B2C-címtárhoz való hozzárendeléséhez.

1. Jelentkezzen ki a Azure Portal, ha jelenleg be van jelentkezve (Ez lehetővé teszi a munkamenet hitelesítő adatainak frissítését a következő lépésben).
2. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) a **Azure ad B2C** rendszergazdai fiókjával. Ennek a fióknak az [Erőforrás-kezelés delegálása](#3-delegate-resource-management) lépésben megadott biztonsági csoport tagjának kell lennie.
3. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán.
4. Válassza ki azt az Azure AD-címtárat, amely tartalmazza az Azure-előfizetést és az *Azure-ad-B2C-figyelő* erőforráscsoportot, amelyet Ön hozott létre.

    ![Címtár váltása](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)

1. Ellenőrizze, hogy a megfelelő könyvtárat és előfizetést jelölte-e ki. Ebben a példában az összes könyvtár és az összes előfizetés ki van választva.

    ![A címtár & előfizetés-szűrőben kiválasztott összes könyvtár](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="5-configure-diagnostic-settings"></a>5. diagnosztikai beállítások konfigurálása

A diagnosztikai beállítások határozzák meg, hogy az erőforráshoz tartozó naplókat és mérőszámokat kell-e elküldeni. A lehetséges célpontok:

- [Azure Storage-fiók](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
- [Event hub](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) -megoldások
- [Log Analytics munkaterület](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

Ebben a példában a Log Analytics munkaterületet használjuk egy irányítópult létrehozásához.

### <a name="51-create-diagnostic-settings"></a>5,1 diagnosztikai beállítások létrehozása

Készen áll a Azure Portal [diagnosztikai beállításainak létrehozására](../active-directory/reports-monitoring/overview-monitoring.md) .

Azure AD B2C tevékenység naplóinak figyelési beállításainak konfigurálása:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a Azure ad B2C rendszergazdai fiókjával. Ennek a fióknak a biztonsági csoport [kiválasztása](#32-select-a-security-group) lépésben megadott biztonsági csoport tagjának kell lennie.
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. **Azure Active Directory** kiválasztása
1. A **Monitorozás** területen kattintson a **Diagnosztikai beállítások** elemre.
1. Ha vannak meglévő beállítások az erőforráshoz, a már konfigurált beállítások listája jelenik meg. Válassza a **diagnosztikai beállítás hozzáadása** lehetőséget egy új beállítás hozzáadásához, vagy válassza a **Szerkesztés** lehetőséget egy meglévő beállítás szerkesztéséhez. Az egyes beállítások nem lehetnek többek között a célhelyek közül.

    ![Diagnosztikai beállítások ablaktábla Azure Portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Adja meg a beállítás nevét, ha még nem rendelkezik ilyennel.
1. Jelölje be az egyes célhelyek jelölőnégyzetét a naplók elküldéséhez. Válassza a **Konfigurálás** lehetőséget a beállítások megadásához **az alábbi táblázatban leírtak szerint**.
1. Válassza a **küldés log Analytics** lehetőséget, majd válassza ki a korábban létrehozott **munkaterület nevét** ( `AzureAdB2C` ).
1. Válassza a **AuditLogs** és a **SignInLogs** lehetőséget.
1. Kattintson a **Mentés** gombra.

> [!NOTE]
> Akár 15 percet is igénybe vehet, miután egy esemény kikerül [egy log Analytics munkaterületen](../azure-monitor/logs/data-ingestion-time.md). Emellett tájékozódhat [Active Directory jelentési késésekről](../active-directory/reports-monitoring/reference-reports-latencies.md)is, amelyek befolyásolhatják az adatvesztést, és fontos szerepet játszanak a jelentéskészítésben.

Ha a következő hibaüzenet jelenik meg: "a diagnosztikai beállítások beállítása Azure Monitor használatára a Azure AD B2C könyvtára számára, be kell állítania a delegált erőforrás-kezelést," győződjön meg arról, hogy a [biztonsági csoport](#32-select-a-security-group) tagja, és [válassza ki az előfizetését](#4-select-your-subscription).

## <a name="6-visualize-your-data"></a>6. az adatai megjelenítése

Most beállíthatja a Log Analytics munkaterületet az adatai megjelenítéséhez és a riasztások konfigurálásához. Ezeket a konfigurációkat az Azure AD-bérlőben és a Azure AD B2C-bérlőben is elvégezheti.

### <a name="61-create-a-query"></a>6,1 lekérdezés létrehozása

A naplók lekérdezései segítségével teljes mértékben kihasználhatja Azure Monitor naplókban összegyűjtött adatok értékét. Egy hatékony lekérdezési nyelv lehetővé teszi több táblázatból származó adatok összekapcsolását, nagy mennyiségű adat összesítését, valamint a minimális kóddal rendelkező összetett műveletek végrehajtását. Gyakorlatilag bármilyen kérdés megválaszolható és elemezhető mindaddig, amíg a támogatási adatok gyűjtése megtörtént, és tisztában van a megfelelő lekérdezés létrehozásával is. További információ: Ismerkedés [a Azure monitor-naplózási lekérdezésekkel](../azure-monitor/logs/get-started-queries.md).

1. **Log Analytics munkaterületen** válassza a **naplók** lehetőséget.
1. Illessze be a következő Kusto lekérdezési [nyelvi](/azure/data-explorer/kusto/query/) lekérdezést a lekérdezéstervező szerkesztőjébe. Ez a lekérdezés a házirend-használatot mutatja az elmúlt x napban. Az alapértelmezett időtartam 90 nap (90d). Figyelje meg, hogy a lekérdezés csak arra a műveletre összpontosít, ahol a jogkivonat/kód kiállítja a szabályzatot.

    ```kusto
    AuditLogs
    | where TimeGenerated  > ago(90d)
    | where OperationName contains "issue"
    | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
    | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
    | summarize SignInCount = count() by Policy, OperationName
    | order by SignInCount desc  nulls last
    ```

1. Válassza a **Futtatás** lehetőséget. A lekérdezés eredményei a képernyő alján jelennek meg.
1. Ha későbbi használatra szeretné menteni a lekérdezést, válassza a **Mentés** lehetőséget.

   ![Log Analytics log Editor](./media/azure-monitor/query-policy-usage.png)

1. Adja meg a következő adatokat:

    - **Név** – adja meg a lekérdezés nevét.
    - **Mentés másként** beállítás `query` .
    - **Kategória** – válassza ki `Log` .

1. Kattintson a **Mentés** gombra.

A lekérdezést úgy is módosíthatja, hogy megjelenítse az adatait a [Render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) operátor használatával.

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Log Analytics log Editor Pie](./media/azure-monitor/query-policy-usage-pie.png)

További példákért tekintse meg a Azure AD B2C [Siem GitHub](https://aka.ms/b2csiem)-tárházat.

### <a name="62-create-a-workbook"></a>6,2 munkafüzet létrehozása

A munkafüzetek Azure Portalon belül kínálnak rugalmas vásznat az adatelemzéshez és a részletgazdag vizuális jelentések létrehozásához. Lehetővé teszik, hogy több adatforrásra koppintson az Azure-ból, és egyesítse azokat egységes interaktív környezetekben. További információ: [Azure monitor munkafüzetek](../azure-monitor/visualize/workbooks-overview.md).

Az alábbi útmutatást követve hozzon létre egy új munkafüzetet egy JSON-katalógus sablon használatával. Ez a munkafüzet egy **felhasználói bepillantást** és **hitelesítési** irányítópultot biztosít Azure ad B2C bérlőnek.

1. A **log Analytics munkaterületen** válassza a **munkafüzetek** elemet.
1. Az eszköztáron kattintson az **+ új** lehetőségre egy új munkafüzet létrehozásához.
1. Az **Új munkafüzet** lapon válassza ki a **speciális szerkesztő** az **</>** eszköztáron található kapcsoló használatával.

     ![Gyűjtemény sablonja](./media/azure-monitor/wrkb-adv-editor.png)

1. Válassza ki a **gyűjtemény sablonját**.
1. Cserélje le a JSON-t a katalógus **sablonban** [Azure ad B2C alapszintű munkafüzetből](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json)származó tartalomra:
1. Alkalmazza a sablont az Apply ( **alkalmaz** ) gomb használatával.
1. A munkafüzet szerkesztésének befejezéséhez válassza az eszköztár **Szerkesztés kész** gombját.
1. Végül mentse a munkafüzetet a **Mentés** gomb használatával az eszköztárból.
1. Adjon meg egy **címet**, például *Azure ad B2C irányítópultot*.
1. Kattintson a **Mentés** gombra.

    ![A munkafüzet mentése](./media/azure-monitor/wrkb-title.png)

A munkafüzet irányítópult formájában jeleníti meg a jelentéseket.

![Munkafüzet első irányítópultja](./media/azure-monitor/wkrb-dashboard-1.png)

![Munkafüzet második irányítópultja](./media/azure-monitor/wrkb-dashboard-2.png)

![Munkafüzet harmadik irányítópultja](./media/azure-monitor/wrkb-dashboard-3.png)


## <a name="create-alerts"></a>Riasztások létrehozása

A riasztásokat riasztási szabályok hozzák létre az Azure Monitorban, és rendszeres időközönként automatikusan mentett lekérdezéseket vagy egyéni naplókereséseket futtathatnak. Riasztásokat megadott teljesítménymetrikák alapján, illetve bizonyos események létrehozásakor vagy hiányakor hozhat létre, illetve akkor, ha egy adott időtartományon belül több esemény jön létre. A riasztások például értesítést kaphatnak arról, ha a bejelentkezések átlagos száma meghaladja az adott küszöbértéket. További információt a [riasztások létrehozása](../azure-monitor/alerts/tutorial-response.md)című témakörben talál.


Az alábbi útmutatást követve létrehozhat egy új Azure-riasztást, amely [e-mailben értesítést](../azure-monitor/alerts/action-groups.md#configure-notifications) küld, amikor 25%-kal csökken az előző időszakban összehasonlított **kérelmek száma** . A riasztás 5 percenként fut le, és az elmúlt 24 órában megtekinti a Windows-t. A riasztások a Kusto lekérdezési nyelv használatával jönnek létre.


1. **Log Analytics munkaterületen** válassza a **naplók** lehetőséget. 
1. Hozzon létre egy új **Kusto-lekérdezést** az alábbi lekérdezés használatával.

    ```kusto
    let start = ago(24h);
    let end = now();
    let threshold = -25; //25% decrease in total requests.
    AuditLogs
    | serialize TimeGenerated, CorrelationId, Result
    | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated in range(start, end, 1h)
    | mvexpand TimeGenerated, TotalRequests
    | where TotalRequests > 0
    | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-M-dd [hh:mm:ss tt]')
    | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
    | order by TimeGeneratedFormatted
    | where PercentageChange <= threshold   //Trigger's alert rule if matched.
    ```

1. A lekérdezés teszteléséhez válassza a **Futtatás** lehetőséget. Az eredményeket akkor kell látnia, ha az elmúlt 24 órában több, mint 25%-os csökkenés van az összes kérelemben.
1. A fenti lekérdezésen alapuló riasztási szabály létrehozásához használja az eszköztáron az **+ új riasztási szabály** lehetőséget.
1. A **riasztási szabály létrehozása** lapon válassza a **feltétel neve** lehetőséget. 
1. A **jel logikai beállítása** lapon állítsa be a következő értékeket, majd a **kész** gomb használatával mentse a módosításokat.
    * Riasztási logika: állítsa be a **nullánál** **nagyobb** **eredmények számát** .
    * Értékelés alapja: válassza a **1440** pontot (percben) és az **5-öt** a gyakorisághoz (perc) 

    ![Riasztási szabály feltételének létrehozása](./media/azure-monitor/alert-create-rule-condition.png)

A riasztás létrehozása után nyissa meg **log Analytics munkaterületet** , és válassza a **riasztások** lehetőséget. Ez a lap megjeleníti az összes olyan riasztást, amely a **Time Range** beállítással beállított időtartam alatt aktiválódik.  

### <a name="configure-action-groups"></a>Műveleti csoportok konfigurálása

Azure Monitor és Service Health riasztások használata műveleti csoportok segítségével értesíti a felhasználókat arról, hogy riasztást váltott ki. Belefoglalhatja a hanghívások, SMS és e-mailek küldését is. vagy különböző típusú automatizált műveletek elindítása. Kövesse a [műveleti csoportok létrehozása és kezelése](../azure-monitor/alerts/action-groups.md) című témakör útmutatását a Azure Portal

Íme egy példa a riasztási értesítő e-mailekre. 

   ![E-mailes értesítés](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>Több bérlő

Ha több Azure AD B2C bérlői naplót szeretne bevezetni ugyanahhoz a Log Analytics munkaterületre (vagy Azure Storage-fiókba vagy Event hub-ba), külön üzemelő példányokra van szüksége az **MSP-ajánlat nevének** értékeivel. Győződjön meg arról, hogy a Log Analytics munkaterület ugyanabban az erőforráscsoporthoz van, mint a [Létrehozás vagy az erőforráscsoport kiválasztása](#1-create-or-choose-resource-group)elemben.

Ha több Log Analytics munkaterülettel dolgozik, a több munkaterületen dolgozó lekérdezések létrehozásához használjon több munkaterület [lekérdezését](../azure-monitor/logs/cross-workspace-query.md) . Például a következő lekérdezés két napló összeillesztését hajtja végre különböző bérlők között ugyanazon kategória alapján (például hitelesítés):

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>Az adatmegőrzési időtartam módosítása

Azure Monitor naplókat úgy tervezték, hogy a vállalati vagy az Azure-ban üzembe helyezett bármely forrásból származó nagy mennyiségű adat gyűjtését, indexelését és tárolását naponta kialakítsa és támogassa. Alapértelmezés szerint a rendszer 30 napig őrzi meg a naplókat, a megőrzési időtartam azonban akár két évre is növelhető. Megtudhatja, hogyan [kezelheti a használatot és a költségeket Azure monitor naplók használatával](../azure-monitor/logs/manage-cost-storage.md). Az árképzési csomag kiválasztása után [megváltoztathatja az adatmegőrzési időszakot](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period).

## <a name="next-steps"></a>Következő lépések

* További mintákat a Azure AD B2C [Siem galériában](https://aka.ms/b2csiem)talál. 

* A Azure Monitor diagnosztikai beállításainak hozzáadásával és konfigurálásával kapcsolatos további információkért lásd [: oktatóanyag: erőforrás-naplók összegyűjtése és elemzése az Azure-erőforrásokból](../azure-monitor/essentials/monitor-azure-resource.md).

* További információ az Azure AD-naplók esemény-központba való továbbításáról [: oktatóanyag: Stream Azure Active Directory-naplók az Azure Event hub](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)-ba.
