---
title: Az Azure-számlázási és költségkezelési költségvetés forgatókönyve
description: Azure Automation használata a virtuális gépek költségvetési küszöbértékektől függő leállítására.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 1706fd69f58e4dee4a7ed963fb22cb63aec026fa
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593393"
---
# <a name="manage-costs-with-azure-budgets"></a>Költségek kezelése az Azure Budgetsszel

A költségek kézben tartása egy kritikus eleme annak, hogy a felhővel kapcsolatos befektetéseiből a lehető legtöbbet tudja kihozni. Számos forgatókönyv esetében a folyamatos üzleti tevékenységhez kritikus fontosságú a költségek láthatósága, a jelentéskészítés és a költségalapú előkészítés. [Azure Cost Management API](/rest/api/consumption/)-k egy sor API-t biztosítanak ezen forgatókönyvek mindegyikéhez. Az API-k használati adatokat biztosítanak, így lehetővé teszik a példányszintű költségek részletes megtekintését.

A költségvetések általában a költségszabályozás részeként használatosak. A költségvetések hatóköre beállítható az Azure-ban. Például leszűkítheti a költségvetési nézetet előfizetés, erőforráscsoport vagy erőforrások gyűjteménye alapján. Amellett, hogy a Budgets API használatával e-mailben értesülhet a költségvetési küszöbértékek elérésekor, az [Azure Monitor-műveletcsoportokkal](../../azure-monitor/alerts/action-groups.md) vezényelt műveleteket is elindíthat a költségvetési események eredményeként.

A nem kritikus fontosságú munkaterhelést futtató ügyfelek egyik gyakori költségvetési forgatókönyve a költségvetés havonta előrelátható, kiszámítható költségek melletti felügyelete. Ehhez a forgatókönyvhöz az Azure-környezet részét képező erőforrások költségalapú vezénylése szükséges. Ebben a forgatókönyvben az előfizetésre 1000 dolláros havi költségvetés van beállítva. Emellett az értesítési küszöbértékek úgy vannak beállítva, hogy elindítsanak néhány vezénylést. Ez a forgatókönyv 80%-os költségküszöbértékkel indul, ami leállítja az összes virtuális gépet a **Választható** erőforráscsoportban. Ezután a 100%-os költség-küszöbértéknél minden virtuálisgép-példány le lesz állítva.

Ennek a forgatókönyvnek a beállításához az oktatóanyag szakaszaiban található lépésekkel a következő műveleteket fogja elvégezni.

Az oktatóanyagban bemutatott műveletekkel lehetséges:

- Létrehozni egy Azure Automation-runbookot egy virtuális gép webhookokkal való leállításához.
- Létrehozni egy Azure Logic App-alkalmazást, amely a költségvetési küszöbérték alapján aktiválódik, és a runbookot a megfelelő paraméterekkel hívja.
- Létrehozni egy Azure Monitor-műveletcsoportot, amely úgy lesz konfigurálva, hogy a költségvetési küszöbérték teljesülése esetén aktiválja az Azure Logic Appot.
- Létrehozni az Azure-költségvetést a kívánt küszöbértékekkel, és hozzáfűzni a műveletcsoporthoz.

## <a name="create-an-azure-automation-runbook"></a>Azure Automation-runbook létrehozása

Az [Azure Automation](../../automation/automation-intro.md) egy olyan szolgáltatás, amely lehetővé teszi az erőforrás-kezelési feladatok nagy részének parancsfájllal történő végrehajtását és a feladatok ütemezett vagy igény szerinti futtatását. Ennek a forgatókönyvnek a részeként létre fog hozni egy [Azure Automation-runbookot](../../automation/automation-runbook-types.md) a virtuális gépek leállításához. A [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) (Azure V2-beli virtuális gépek leállítása) grafikus runbookot fogja használni a [katalógusból](../../automation/automation-runbook-gallery.md) a forgatókönyv létrehozásához. Ha ezt a runbookot importálja az Azure-fiókjába, majd közzéteszi, akkor a költségvetési küszöbérték elérésekor leállíthatja a virtuális gépeket.

### <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókja hitelesítő adataival.
2. Kattintson az Azure bal felső sarkában található **Erőforrás létrehozása** gombra.
3. Válassza a **Felügyeleti eszközök** > **Automation** lehetőséget.
   > [!NOTE]
   > Ha még nem rendelkezik Azure-fiókkal, akár most is létrehozhat [egy ingyenes fiókot](https://azure.microsoft.com/free/).
4. Írja be fiókadatait. Az **Azure-beli futtató fiók létrehozása** területen válassza az **Igen** lehetőséget az Azure-beli hitelesítést leegyszerűsítő beállítások automatikus engedélyezéséhez.
5. Amikor végzett, kattintson a **Létrehozás** gombra az Automation-fiók üzembe helyezésének megkezdéséhez.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>A Stop Azure V2 VMs (Azure V2-beli virtuális gépek leállítása) runbook importálása

Az [Azure Automation-runbook](../../automation/automation-runbook-types.md) használatával importálja a [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) (Azure V2-beli virtuális gépek leállítása) grafikus runbookot a katalógusból.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókja hitelesítő adataival.
1. Nyissa meg az Automation-fiókját a **Minden szolgáltatás**, majd az  > **Automation-fiókok** lehetőség kiválasztásával. Ezután válassza ki az Automation-fiókját.
1. Válassza a **Folyamatok automatizálása** területen lévő **Runbook-katalógus** lehetőséget.
1. Állítsa a **Katalógusforrást** a **Script Center** elemre, majd kattintson az **OK** gombra.
1. Keresse meg és jelölje ki a [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) (Azure V2-beli virtuális gépek leállítása) katalóguselemet az Azure portalon.
1. Kattintson az **Importálás** gombra az **Importálás** terület megjelenítéséhez, és kattintson az **OK** gombra. Megjelenik a runbook áttekintési területe.
1. Miután a runbook végrehajtotta az importálási folyamatot, válassza a **Szerkesztés** lehetőséget a grafikus runbook szerkesztése és közzététele lehetőség megjelenítéséhez.  
    ![Azure – Grafikus runbook szerkesztése](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
1. A runbook közzétételéhez válassza a **Közzététel** lehetőséget, és ha a rendszer rákérdez, kattintson az **Igen** gombra. Amikor közzétesz egy runbookot, felülírja a közzétett verziót a piszkozattal. Ebben az esetben még nincs közzétett verzió, mivel még csak most hozta létre a runbookot.
    A runbook közzétételével kapcsolatos további információkért lásd a [Grafikus runbook létrehozása](../../automation/learn/automation-tutorial-runbook-graphical.md) című témakört.

## <a name="create-webhooks-for-the-runbook"></a>Webhookok létrehozása a runbookhoz

A [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) (Azure V2-beli virtuális gépek leállítása) grafikai runbook használatával egyetlen HTTP-kérésen keresztül két webhookot hoz létre a runbook elindításához az Azure Automationben. Az első webhook a runbookot a 80%-os költségvetési küszöbértéken hívja meg paraméterként az erőforráscsoport nevével, így lehetővé teszi a választható virtuális gépek leállítását. Ezután (100%-nál) a második webhook meghívja a runbookot paraméter nélkül, ami leállítja az összes többi VM-példányt.

1. Az [Azure Portal](https://portal.azure.com/) **Runbookok** oldalán válassza a **StopAzureV2Vm** runbookot, hogy megjelenjen a runbook áttekintési területe.
1. Válassza az oldal tetején található **Webhook** lehetőséget a **Webhook hozzáadása** terület megnyitásához.
1. Ezután az **Új webhook létrehozása** elemre kattintva nyissa meg az **Új webhook létrehozása** területet.
1. Írja be a webhooknál a **Név** lehetőséghez, hogy **Választható**. Az **Engedélyezve** tulajdonságnál **Igen** legyen beállítva. Nem kell módosítania a **Lejárat** értékét. A webhookra vonatkozó további információért lásd a [Webhook tulajdonságai](../../automation/automation-webhooks.md#webhook-properties) szakaszt.
1. A webhook URL-címének másolásához kattintson a másolás ikonra az URL-érték mellett.
   > [!IMPORTANT]
   > Mentse a **Választható** elnevezésű webhook URL-címét egy biztonságos helyre. Ezt az URL-t az oktatóanyag későbbi szakaszában használni fogja. Biztonsági okokból a webhook létrehozása után nem tekintheti meg és nem kérheti le újra az URL-címet.
1. Az új webhook létrehozásához kattintson az **OK** gombra.
1. A runbook paraméterértékeinek megtekintéséhez válassza a **Paraméterek és futtatási beállítások konfigurálása** lehetőséget.
   > [!NOTE]
   > Kötelező paraméterekkel rendelkező runbook esetén csak akkor lehet a webhookot létrehozni, ha az értékeket is megadja.
1. Kattintson az **OK** gombra a webhook paraméterértékeinek elfogadásához.
1. A webhook létrehozásához kattintson a **Létrehozás** gombra.
1. Kövesse az alábbi lépéseket egy második, **Teljes** nevű webhook létrehozásához.
    > [!IMPORTANT]
    > Ne felejtse menteni mindkét URL-t, hogy oktatóanyag későbbi szakaszában használhassa őket. Biztonsági okokból a webhook létrehozása után nem tekintheti meg és nem kérheti le újra az URL-címet.

Most már két konfigurált webhookkal rendelkezik, amelyek a mentett URL-címekkel érhetők el.

![Webhookok – Választható és Teljes](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Ezzel elvégezte az Azure Automation beállítását. Egy egyszerű Postman-teszttel ellenőrizheti, hogy a webhookok működnek-e. Következő lépésként létre kell hoznia a Logic Appot a vezényléshez.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Azure Logic App létrehozása vezényléshez

A Logic Appsszel munkafolyamatként hozhat létre, ütemezhet és automatizálhat folyamatokat, lehetővé téve az alkalmazások, adatok, rendszerek és szolgáltatások integrációját egy adott vállalaton vagy szervezeten belül. Ebben a forgatókönyvben a létrehozott [logikai alkalmazás](../../logic-apps/index.yml) valamivel többet fog tenni, mint csak létrehozott Automation-webhook meghívása.

A költségvetés beállítható úgy, hogy egy adott küszöbérték teljesülése esetén értesítést indítson. Több küszöbértéket is megadhat, amelyekről értesítést kaphat, és a logikai alkalmazás a teljesített küszöbértéktől függően képes különböző műveleteket elvégezni. Ebben a példában egy olyan forgatókönyvet állíthat be, amely több értesítést küld. Az első értesítés a költségvetés 80%-ának elérésénél, a második pedig a költségvetés 100%-ának elérésénél érkezik. Az erőforráscsoport virtuális gépei a logikai alkalmazás használatával lesznek leállítva. Első lépésként a **Választható** küszöbérték 80%-os lesz, majd a második küszöbérték elérésénél az előfizetésben lévő összes virtuális gép le lesz állítva.

A logikai alkalmazások lehetővé teszik egy mintaséma biztosítását a HTTP-eseményindítóhoz, de ehhez be kell állítania a **Content-Type** fejlécet. Mivel a műveletcsoport nem rendelkezik egyéni fejlécekkel a webhookhoz, a hasznos adatokat külön lépésben kell elemezni. Az **Elemzés** műveletet fogja használni, és megad a műveletnek egy hasznosadat-mintát.

### <a name="create-the-logic-app"></a>A logikai alkalmazás létrehozása

A logikai alkalmazás több művelet fog végrehajtani. A következő lista tartalmazza a logikai alkalmazás által végrehajtott magas szintű műveleteket:

- HTTP-kérés fogadásának felismerése
- Az átadott JSON-adatok elemzése az elért küszöbérték megállapításához
- Feltételes utasítás használatával győződjön meg arról, hogy a küszöbérték elérte-e a költségvetési tartomány legalább 80%-át, de nem nagyobb vagy egyenlő, mint 100%.
  - Ha elérte a küszöbértéket, küldjön egy HTTP POST-ot a **Választható** nevű webhook használatával. Ez a művelet leállítja a „Választható” csoportban lévő virtuális gépeket.
- Feltételes utasítás használatával győződjön meg arról, hogy a küszöbérték elérte vagy túllépte-e a költségvetési érték 100%-át.
  - Ha elérte a küszöbértéket, küldjön egy HTTP POST-ot a **Teljes** nevű webhook használatával. Ez a művelet leállítja az összes hátralévő virtuális gépet.

A fenti lépéseket kivitelező logikai alkalmazás létrehozásához az alábbi lépéseket kell elvégezni:

1. Az [Azure Portalon](https://portal.azure.com/) válassza az **Erőforrás létrehozása** > **Integráció** > **Logikai alkalmazás** lehetőséget.  
    ![Azure – A Logic Apps-erőforrás kiválasztása](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
1. A **Logic Apps-alkalmazás létrehozása** területen adja meg a logikai alkalmazás létrehozásához szükséges paramétereket, majd válassza a **Rögzítés az irányítópulton** lehetőséget, és kattintson a **Létrehozás** elemre.  
    ![Azure – Logic Apps-alkalmazás létrehozása](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Miután az Azure üzembe helyezte a logikai alkalmazást, megnyílik a **Logic Apps Designer** panel, és egy bemutató videót és a gyakran használt eseményindítókat tartalmazó területet jelenít meg.

### <a name="add-a-trigger"></a>Eseményindító hozzáadása

Mindegyik logikai alkalmazásnak egy eseményindítóval kell indulnia, amelyet egy adott esemény vagy adott feltételek teljesülése aktivál. A Logic Apps-motor az eseményindító minden elindulásakor létrehoz egy logikaialkalmazás-példányt, amely elindítja és futtatja a munkafolyamatot. A műveletek mindazon lépések, amelyek az eseményindító aktiválása után végbemennek.

1. A **Logic Apps Designer** terület **Sablonok** szakaszában válassza az **Üres logikai alkalmazás** lehetőséget.
1. Adjon hozzá egy új [eseményindítót](../../logic-apps/logic-apps-overview.md#logic-app-concepts) úgy, hogy a **Logic Apps Designer** keresőmezőjébe beírja a „http kérés” kulcsszavakat, és kiválasztja a **Kérés – HTTP-kérés fogadásakor** nevű eseményindítót.  
    ![Azure – Logic Apps-alkalmazás – HTTP-eseményindító](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
1. Válassza az **Új lépés** > **Művelet hozzáadása** lehetőséget.  
    ![Azure – Új lépés – Művelet hozzáadása](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
1. A **Logic Apps Designer** keresőmezőjébe írja be a „JSON elemzése” kulcsszavakat, és válassza ki **Adatműveletek – JSON elemzése** [műveletet](../../logic-apps/logic-apps-overview.md#logic-app-concepts).  
    ![Azure – Logic Apps-alkalmazás – JSON elemzése művelet hozzáadása](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
1. Nevezze el „hasznos adat”-nak a **Tartalom** mezőben a JSON hasznos adat elemzéshez használt tartalmat, vagy használja a „Szövegtörzs” címkét a dinamikus tartalomból.
1. Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget a **JSON elemzése** mezőben.  
    ![Azure – Logic Apps-alkalmazás – Séma létrehozása minta JSON-adatok használatával](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
1. A következő JSON hasznos adat mintát illessze be a szövegmezőbe: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`
   A szövegmező a következőképp fog kinézni:  
    ![Azure – Logic Apps-alkalmazás – JSON hasznos adat minta](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
1. Válassza a **Done** (Kész) lehetőséget.

### <a name="add-the-first-conditional-action"></a>Az első feltételes művelet hozzáadása

Feltételes utasítás használatával győződjön meg arról, hogy a küszöbérték elérte-e a költségvetési tartomány legalább 80%-át, de nem nagyobb vagy egyenlő, mint 100%. Ha elérte a küszöbértéket, küldjön egy HTTP POST-ot a **Választható** nevű webhook használatával. Ez a művelet leállítja a **Választható** csoportban lévő virtuális gépeket.

1. Válassza az **Új lépés** > **Feltétel hozzáadása** lehetőséget.  
    ![Azure – Logic Apps-alkalmazás – Feltétel hozzáadása](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
1. A **Feltétel** mezőben válassza a `Choose a value` szövegmezőt az elérhető értékek megjelenítéséhez.  
    ![Azure – Logic Apps-alkalmazás – Feltétel mező](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)
1. Válassza a **Kifejezés** lehetőséget a lista tetején, és írja be a következő kifejezést a kifejezésszerkesztőbe: `float()`  
    ![Azure – Logic Apps-alkalmazás – Lebegőpontos kifejezés](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)
1. Válassza a **Dinamikus tartalom** lehetőséget, vigye a kurzort a () zárójelek közé, és a listából válassza a **NotificationThresholdAmount** lehetőséget a teljes kifejezés feltöltéséhez.
   A kifejezés a következő lesz:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
1. A kifejezés beállításához kattintson az **OK** gombra.
1. A **Feltétel** legördülő listából válassza a **nagyobb vagy egyenlő, mint** lehetőséget.
1. A feltétel **Válasszon egy értéket** mezőjébe írja be a következőt: `.8`.  
    ![Képernyőkép a Feltétel párbeszédpanelről, a kiválasztott értékekkel.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)
1. A Feltétel mezőben válassza a **Hozzáadás** > **Sor hozzáadása** lehetőséget a feltétel további részének hozzáadásához.
1. A **Feltétel** mezőben válassza a `Choose a value` szöveget tartalmazó szövegmezőt.
1. Válassza a **Kifejezés** lehetőséget a lista tetején, és írja be a következő kifejezést a kifejezésszerkesztőbe: `float()`
1. Válassza a **Dinamikus tartalom** lehetőséget, vigye a kurzort a () zárójelek közé, és a listából válassza a **NotificationThresholdAmount** lehetőséget a teljes kifejezés feltöltéséhez.
1. A kifejezés beállításához kattintson az **OK** gombra.
1. A **Feltétel** legördülő listából válassza a **Kevesebb, mint** lehetőséget.
1. A feltétel **Válasszon egy értéket** mezőjébe írja be a következőt: `1`.  
    ![Képernyőkép a Feltétel párbeszédpanelről, két feltétellel.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)
1. A **Ha igaz** mezőben válassza a **Művelet hozzáadása** lehetőséget. Egy HTTP POST-műveletet fog hozzáadni, amely kikapcsolja a választható virtuális gépeket.  
    ![Azure – Logic Apps-alkalmazás – Művelet hozzáadása](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)
1. A HTTP-művelet megkereséséhez adja meg a **HTTP** kulcsszót, majd válassza a **HTTP – HTTP** műveletet.  
    ![Azure – Logic Apps-alkalmazás – HTTP-művelet hozzáadása](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)
1. A **Metódus** beállításhoz válassza a **POST** értéket.
1. **Uri** értékként írja be az oktatóanyag korábbi szakaszában létrehozott **Választható** nevű webhook URL-címét.  
    ![Azure – Logic Apps-alkalmazás – HTTP művelet URI-je](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)
1. A **Ha igaz** mezőben válassza a **Művelet hozzáadása** lehetőséget. Egy e-mail-műveletet fog hozzáadni, amely értesíti a címzettet arról, hogy a választható virtuális gépek le lettek állítva.
1. Keressen rá az „e-mail küldése” kifejezésre, és válassza az Ön e-mail-szolgáltatásához tartozó *e-mail küldése* műveletet.  
    ![Azure – Logic Apps-alkalmazás – E-mail küldése művelet](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    Személyes Microsoft-fiókok esetében válassza az **Outlook.com** lehetőséget. Munkahelyi vagy iskolai Azure-fiókok esetében válassza az **Office 365 Outlook** lehetőséget. Ha még nem rendelkezik kapcsolattal, a rendszer arra kéri, hogy jelentkezzen be az e-mail-fiókjába. A Logic Apps létrehoz egy kapcsolatot az e-mail-fiókjával.
   A Logic Apps számára engedélyeznie kell az e-mail-információkhoz való hozzáférést.  
    ![Azure – Logic Apps-alkalmazás – Hozzáférési értesítés](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)
1. Adja hozzá az e-mailhez a **Címzett**, a **Tárgy** és a **Szövegtörzs** szövegét, amely értesíti a címzettet arról, hogy a választható virtuális gépek le lettek állítva. A **BudgetName** és a **NotificationThresholdAmount** dinamikus tartalmat használva töltse fel a tárgy és a szövegtörzs mezőket. 
    ![Azure – Logic Apps-alkalmazás – E-mail részletei](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Adja hozzá a második feltételes műveletet

Feltételes utasítás használatával győződjön meg arról, hogy a küszöbérték elérte vagy túllépte-e a költségvetési érték 100%-át. Ha elérte a küszöbértéket, küldjön egy HTTP POST-ot a **Teljes** nevű webhook használatával. Ez a művelet leállítja az összes hátralévő virtuális gépet.

1. Válassza az **Új lépés** > **Feltétel hozzáadása** lehetőséget.  
    ![Képernyőkép a Ha igaz párbeszédpanelről, amelyen a Művelet hozzáadása lehetőség van kiemelve.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)
1. A **Feltétel** mezőben válassza a `Choose a value` szövegmezőt az elérhető értékek megjelenítéséhez.
1. Válassza a **Kifejezés** lehetőséget a lista tetején, és írja be a következő kifejezést a kifejezésszerkesztőbe: `float()`
1. Válassza a **Dinamikus tartalom** lehetőséget, vigye a kurzort a () zárójelek közé, és a listából válassza a **NotificationThresholdAmount** lehetőséget a teljes kifejezés feltöltéséhez.
   A kifejezés a következőre hasonlít:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
1. A kifejezés beállításához kattintson az **OK** gombra.
1. A **Feltétel** legördülő listából válassza a **nagyobb vagy egyenlő, mint** lehetőséget.
1. A feltétel **Válasszon egy értéket** mezőjébe írja be a következőt: `1`.  
    ![Azure – Logic Apps-alkalmazás – Feltétel beállítása](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)
1. A **Ha igaz** mezőben válassza a **Művelet hozzáadása** lehetőséget. Egy HTTP POST-műveletet fog hozzáadni, amely leállítja az összes fennmaradó virtuális gépet.  
    ![Képernyőkép a Ha igaz párbeszédpanelről, ahol hozzáadhatók HTTP POST-műveletek.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)
1. A HTTP-művelet megkereséséhez adja meg a **HTTP** kulcsszót, majd válassza a **HTTP – HTTP** műveletet.
1. A **Metódus** beállításhoz válassza a **POST** értéket.
1. **Uri** értékként írja be az oktatóanyag korábbi szakaszában létrehozott **Teljes** elnevezésű webhook URL-címét.  
    ![Képernyőkép a HTTP párbeszédpanelről, ahol megadható az URL-cím érték.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)
1. A **Ha igaz** mezőben válassza a **Művelet hozzáadása** lehetőséget. Egy e-mail-műveletet fog hozzáadni, amely értesíti a címzettet arról, hogy a fennmaradó virtuális gépek le lettek állítva.
1. Keressen rá az „e-mail küldése” kifejezésre, és válassza az Ön e-mail-szolgáltatásához tartozó *e-mail küldése* műveletet.
1. Adja hozzá az e-mailhez a **Címzett**, a **Tárgy** és a **Szövegtörzs** szövegét, amely értesíti a címzettet arról, hogy a választható virtuális gépek le lettek állítva. A **BudgetName** és a **NotificationThresholdAmount** dinamikus tartalmat használva töltse fel a tárgy és a szövegtörzs mezőket.  
    ![Azure – Logic Apps-alkalmazás – E-mail küldésének részletei](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)
1. A **Logic App Designer** terület tetején kattintson a **Mentés** gombra.

### <a name="logic-app-summary"></a>Logic App összefoglalása

Amikor elkészült, a logikai alkalmazás így néz ki. A legalapvetőbb forgatókönyvekben, ahol nincs szükség küszöbérték-alapú vezénylésre, közvetlenül meghívhatja az Automation-szkriptet a **Monitorból**, és kihagyhatja a **logikai alkalmazás** lépését.

![Azure – Logic App – Teljes áttekintés](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

A logikai alkalmazás mentésekor létrejött egy URL-cím, amelyet meghívhat. Ezt az URL-címet fogja használni az oktatóanyag következő szakaszában.

## <a name="create-an-azure-monitor-action-group"></a>Azure Monitor-műveletcsoport létrehozása

Egy műveletcsoport az Ön által meghatározott értesítési preferenciák gyűjteménye. Egy riasztás aktiválásakor egy adott műveletcsoport értesítést kaphat a riasztásról. Az Azure-riasztás proaktív módon, meghatározott feltételek alapján küld egy értesítést, és lehetőséget biztosít a beavatkozásra. A riasztási szabályok több forrásból, például metrikákból és naplókból származó adatokat használnak.

A műveletcsoportok képezik az egyetlen végpontot, amelyet integrál a költségvetéssel. Több csatornán is beállíthat értesítéseket, de ebben a forgatókönyvben az oktatóanyag korábbi szakaszában létrehozott logikai alkalmazásra fog összpontosítani.

### <a name="create-an-action-group-in-azure-monitor"></a>Műveletcsoport létrehozása az Azure Monitorban

Amikor létrehozza a műveletcsoportot, arra a logikai alkalmazásra fog mutatni, amelyet az oktatóanyag korábbi szakaszában létrehozott.

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és válassza a **Minden szolgáltatás** > **Monitor** elemet.
1. Válassza a **Riasztások**, majd a **Műveletek kezelése** lehetőséget.
1. A **Műveletcsoportok** területen válassza a **Műveletcsoport hozzáadása** pontot.
1. Adja hozzá és ellenőrizze a következő elemeket:
    - Műveletcsoport neve
    - Rövid név
    - Előfizetés
    - Erőforráscsoport  
    ![Azure – Logic App – Műveletcsoport hozzáadása](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)
1. A **Műveletcsoport hozzáadása** panelen adjon hozzá egy LogicApp-műveletet. A művelet neve legyen **Budget-BudgetLA**. A **Logikai alkalmazás** panelen válassza ki az **Előfizetés**, majd az **Erőforráscsoport** elemet. Ezután válassza ki azt a **logikai alkalmazást**, amelyet az oktatóanyag korábbi szakaszában létrehozott.
1. A logikai alkalmazás beállításához kattintson az **OK** gombra. Ezután a **Műveletcsoport hozzáadása** panelen kattintson az **OK** gombra a műveletcsoport létrehozásához.

A költségvetés hatékony vezényléséhez szükséges összes támogató összetevőt beállította. Nincs más tennivalója, mint létrehozni, és a létrehozott műveletcsoport használatára beállítani a költségvetést.

## <a name="create-the-azure-budget"></a>Azure-költségvetés létrehozása

Az Azure Portalon a [Költségvetés funkció](../costs/tutorial-acm-create-budgets.md) használatával hozhat létre költségvetést a Cost Managementben. Vagy REST API-k, PowerShell-parancsmagok vagy a CLI használatával is létrehozhat költségvetést. A következő eljárás a tartományi REST API-t használja. A REST API meghívásához szüksége lesz egy engedélyezési jogkivonatra. Engedélyezési jogkivonat létrehozásához használhatja az [ARMClient](https://github.com/projectkudu/ARMClient) projektet. Az **ARMClient** lehetővé teszi, hogy hitelesítse magát az Azure Resource Manageren, és megkapja az API-k meghívásához szükséges jogkivonatot.

### <a name="create-an-authentication-token"></a>Engedélyezési jogkivonat létrehozása

1. A GitHubon lépjen az [ARMClient](https://github.com/projectkudu/ARMClient) projekthez.
1. Saját példány létrehozásához klónozza az adattárat.
1. Nyissa meg és buildelje a projektet a Visual Studióban.
1. Sikeres buildelés után a végrehajtható fájlnak a *\bin\debug* mappában kell lennie.
1. Futtassa az ARMClientet. Nyisson meg egy parancssort, és a projekt gyökérkönyvtárából lépjen a *\bin\debug* mappához.
1. A bejelentkezéshez és a hitelesítéshez írja be az alábbi parancsot a parancssorba:<br>
    `ARMClient login prod`
1. Másolja ki az **előfizetés GUID-azonosítóját** a kimenetből.
1. Az engedélyezési jogkivonat vágólapra másolásához írja be az alábbi parancsokat a parancssorba, de győződjön meg arról, hogy a fenti lépés során kimásolt előfizetés-azonosítót adja meg: <br>
    `ARMClient token <subscription GUID from previous step>`

    A fenti lépés elvégzése után az alábbiakat fogja látni:<br>
    **Sikeresen a vágólapra másolta a jogkivonatot.**
1. Mentse a jogkivonatot, mert az oktatóanyag következő szakaszában szükség lesz rá.

### <a name="create-the-budget"></a>A költségvetés létrehozása

A következő lépésként konfigurálni fogja a **Postmant**, hogy az Azure Consumption REST API-k meghívásával létrehozza a költségvetést. A Postman egy API-fejlesztési környezet. Környezeti és gyűjteményfájlokat importál a Postmanbe. A gyűjtemény az Azure-használati REST API-kat meghívó HTTP-kérelmek csoportosított definícióit tartalmazza. A környezeti fájl azokat a változókat tartalmazza, amelyeket a gyűjtemény használ.

1. A REST API-k végrehajtásához töltse le és nyissa meg a [Postman REST-ügyfelet](https://www.getpostman.com/).
1. Hozzon létre egy új kérelmet a Postmanben.  
    ![Postman – új kérelem létrehozása](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)
1. Mentse az új kérelmet gyűjteményként, hogy az új kérelmen ne legyen semmi.  
    ![Postman – új kérelem mentése](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)
1. A kérelmet módosítsa `Get` műveletről `Put` műveletre.
1. Az URL-t módosítsa a `{subscriptionId}` kicserélésével az oktatóanyag előző szakaszában használt **előfizetés-azonosítóra**. Továbbá módosítsa az URL-t úgy, hogy a `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31` értéke „SampleBudget” legyen
1. A Postmanben válassza a **Fejlécek** lapot.
1. „Engedélyezés” néven adjon hozzá egy új **Kulcsot**.
1. Az **Értéket** állítsa az előző szakasz végén az ArmClient használatával létrehozott jogkivonatra.
1. Válassza a **Szövegtörzs** fület a Postmanben.
1. Válassza ki a **formázatlan** gomb lehetőséget.
1. A szövegmezőbe illessze be az alábbi költségvetésdefiníció-mintát, azonban a `subscriptionID`, `resourcegroupname` és `actiongroupname` paramétert előtte le kell cserélnie a saját előfizetés-azonosítójára, erőforráscsoportjának egyedi nevére, valamint az URL-címben és a kérelem törzsében létrehozott műveletcsoport nevére:

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {},
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [],
                    "contactRoles": [],
                    "contactGroups": [
                        "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                },
               "Actual_EqualTo_100_Percent": {
                       "operator": "EqualTo",
                       "threshold": 100,
                       "contactGroups": [
                           "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                       ]
                   }
               }
            }
        }
    ```
1. Küldje el a kérelmet a **Küldés** gomb lenyomásával.

Most már a [Budgets API](/rest/api/consumption/budgets) meghívásához szükséges összes elemmel rendelkezik. A Budgets API referenciája további részleteket tartalmaz az adott kérelmekkel kapcsolatban, beleértve az alábbiakat:

- **budgetName** – Több költségvetés is támogatott.  A költségvetés nevének egyedinek kell lennie.
- **category** – **Cost** vagy **Usage** kell, hogy legyen. Az API a költség és a használat kategóriájú költségvetéseket is támogatja.
- **timeGrain** – Havi, negyedéves vagy éves költségvetés. Az időszak végén a mennyiség visszaáll az alaphelyzetre.
- **filters** – A szűrők lehetővé teszik, hogy a költségvetést a kijelölt hatókörön belüli erőforrások egy adott készletére szűkítse. Egy szűrő lehet például az előfizetési szint költségvetéséhez tartozó erőforráscsoportok gyűjteménye.
- **Értesítések** – Meghatározza az értesítés részleteit és küszöbértékeit. Több küszöbértéket is beállíthat, és megadhat egy e-mail-címet vagy egy műveletcsoportot az értesítés fogadásához.

## <a name="summary"></a>Összefoglalás

Ebben az oktatóanyagban a következőkkel ismerkedett meg:

- Hogyan hozhat létre Azure Automation-runbookokat a virtuális gépek leállítására.
- Hogyan hozhat létre olyan Azure Logic app-alkalmazást, amely a költségvetési küszöbértékek alapján aktiválódik, és hogyan hívhatja meg a kapcsolódó, megfelelő paraméterekkel rendelkező runbookot.
- Hogyan hozhat létre olyan Azure Monitor-műveletcsoportot, amely úgy lett konfigurálva, hogy a költségvetési küszöbérték elérésekor aktiválja az Azure Logic Apps-alkalmazást.
- Hogyan hozhatja az Azure-költségvetést a kívánt küszöbértékekkel, és fűzheti hozzá azt a műveletcsoporthoz.

Most már rendelkezik egy teljesen működőképes költségvetéssel az előfizetéshez, amely leállítja a virtuális gépeket, amikor eléri a beállított költségvetési küszöbértékeket.

## <a name="next-steps"></a>További lépések

- További információkért az Azure számlázási forgatókönyvekről lásd: [A számlázás és költségkezelés automatizálási forgatókönyvei](cost-management-automation-scenarios.md).