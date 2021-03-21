---
title: A log Analytics varázsló konfigurálása az Azure AD-ben | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a log Analytics szolgáltatást.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: markvi
author: MarkusVi
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca84fa57cb3a26337038275d1b7491154915c90e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574375"
---
# <a name="tutorial-configure-the-log-analytics-wizard"></a>Oktatóanyag: a log Analytics varázsló konfigurálása


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Log Analytics-munkaterület konfigurálása a naplózási és bejelentkezési naplókhoz
> * Lekérdezések futtatása a Kusto lekérdezési nyelvének (KQL) használatával
> * Riasztási szabály létrehozása, amely riasztásokat küld egy adott fiók használatakor
> * Egyéni munkafüzet létrehozása a rövid útmutató sablon használatával
> * Lekérdezés hozzáadása meglévő munkafüzet-sablonhoz

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-előfizetés legalább egy P1 licenccel rendelkező rendszergazdával. Ha nem rendelkezik Azure-előfizetéssel, [regisztrálhat az ingyenes próbaverzióra](https://azure.microsoft.com/free/).

- Egy Azure AD-bérlő.

- Egy felhasználó, aki az adott Azure AD-bérlő globális vagy biztonsági rendszergazdája.


Ismerkedjen meg a következő cikkekkel:

- [Oktatóanyag: erőforrás-naplók összegyűjtése és elemzése Azure-erőforrásból](../../azure-monitor/essentials/tutorial-resource-logs.md)

- [A tevékenységek naplóinak integrálása a Log Analytics](./howto-integrate-activity-logs-with-log-analytics.md)

- [Vészhelyzeti hozzáférési fiók kezelése az Azure AD-ben](../roles/security-emergency-access.md)

- [KQL– rövid áttekintés](/azure/data-explorer/kql-quick-reference)

- [Munkafüzetek Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md)



## <a name="configure-a-workspace"></a>Munkaterület konfigurálása 

Ez az eljárás azt ismerteti, hogyan konfigurálható a log Analytics-munkaterület a naplózási és bejelentkezési naplókhoz.
A log Analytics-munkaterület konfigurálása két fő lépésből áll:
 
1. Log Analytics-munkaterület létrehozása
2. Diagnosztikai beállítások beállítása

**Munkaterület konfigurálása:** 


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként.

2. Keresse meg a **log Analytics-munkaterületeket**.

    ![Erőforrás-szolgáltatások és dokumentumok keresése](./media/tutorial-log-analytics-wizard/search-services.png)

3. A log Analytics-munkaterületek lapon kattintson a **Hozzáadás** gombra.

    ![A képernyőfelvétel a log Analytics-munkaterületek lapon látható Hozzáadás gombra mutat.](./media/tutorial-log-analytics-wizard/add.png)

4.  A **log Analytics-munkaterület létrehozása** lapon hajtsa végre a következő lépéseket:

    ![Log Analytics-munkaterület létrehozása](./media/tutorial-log-analytics-wizard/create-log-analytics-workspace.png)

    1. Válassza ki előfizetését.

    2. Válasszon ki egy erőforráscsoportot.
 
    3. A **név** szövegmezőbe írja be a nevet (például: MytestWorkspace1).

    4. Válassza ki a régiót.

5. Kattintson a **Felülvizsgálat + létrehozás** elemre.

    ![Áttekintés és létrehozás](./media/tutorial-log-analytics-wizard/review-create.png)

6. Kattintson a **Létrehozás** gombra, és várja meg, amíg a telepítés sikeres lesz. Előfordulhat, hogy frissítenie kell a lapot az új munkaterület megtekintéséhez.

    ![Létrehozás](./media/tutorial-log-analytics-wizard/create-workspace.png)

7. Keresse meg az **Azure Active Directoryt**.

    ![A képernyőképen az Azure Search Azure Active Directory látható.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

8. A **figyelés** szakaszban kattintson a **diagnosztikai beállítások** elemre.

    ![A képernyőképen a figyelésből kiválasztott diagnosztikai beállítások láthatók.](./media/tutorial-log-analytics-wizard/diagnostic-settings.png)

9. A **diagnosztikai beállítások** lapon kattintson a **diagnosztikai beállítás hozzáadása** elemre.

    ![Diagnosztikai beállítás hozzáadása](./media/tutorial-log-analytics-wizard/add-diagnostic-setting.png)

10. A **diagnosztikai beállítások** lapon végezze el a következő lépéseket:

    ![Diagnosztikai beállítások kiválasztása](./media/tutorial-log-analytics-wizard/select-diagnostics-settings.png)

    1. A **Kategória részletei** területen válassza a **AuditLogs** és a **SigninLogs** lehetőséget.

    2. A **célhely részletei** területen válassza a **Küldés log Analyticsba** lehetőséget, majd válassza ki az új log Analytics-munkaterületet. 
   
    3. Kattintson a **Mentés** gombra. 

## <a name="run-queries"></a>Lekérdezések futtatása  

Ez az eljárás bemutatja, hogyan futtathat lekérdezéseket a **Kusto lekérdezési nyelv (KQL)** használatával.


**Lekérdezés futtatása:**


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként.

2. Keresse meg az **Azure Active Directoryt**.

    ![A képernyőképen az Azure Search Azure Active Directory látható.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. A **figyelés** szakaszban kattintson a **naplók** elemre.

4. A **naplók** lapon kattintson az első **lépések** elemre.

5. A **Keresés* szövegmezőbe írja be a lekérdezést.

6. Kattintson a **Futtatás** elemre.  


### <a name="kql-query-examples"></a>KQL-lekérdezési példák

Vegyen fel 10 véletlenszerű bejegyzést a bemeneti adatokból:

`SigninLogs | take 10`

Ellenőrizze, hogy a feltételes hozzáférés sikeres volt-e

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus` 


Megszámolja, hogy hány siker történt

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus | count`


A sikeres bejelentkezések összesített száma a felhasználó által naponta:

`SigninLogs | where ConditionalAccessStatus == "success" | summarize SuccessfulSign-ins = count() by UserDisplayName, bin(TimeGenerated, 1d)` 


Megtekintheti, hogy a felhasználó hányszor hajtja egy bizonyos műveletet adott időszakban:

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | summarize count() by OperationName, Identity`


A művelet nevének kimutatására szolgáló eredmény

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | project OperationName, Identity | evaluate pivot(OperationName)`


Egyesítse a naplózási és bejelentkezési naplókat a belső illesztés használatával:

`AuditLogs |where OperationName contains "Add User" |extend UserPrincipalName = tostring(TargetResources[0].userPrincipalName) | |project TimeGenerated, UserPrincipalName |join kind = inner (SigninLogs) on UserPrincipalName |summarize arg_min(TimeGenerated, *) by UserPrincipalName |extend SigninDate = TimeGenerated` 


A Signs-modulok számának megtekintése az ügyfélalkalmazás típusa szerint:

`SigninLogs | summarize count() by ClientAppUsed`

A bejelentkezések napi száma:

`SigninLogs | summarize NumberOfEntries=count() by bin(TimeGenerated, 1d)`

5 véletlenszerű bejegyzést és projektet kell megtekintenie az eredményekben megjeleníteni kívánt oszlopokból:

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `


Az első 5-öt csökkenő sorrendbe rendezheti, és megtekintheti a megjeleníteni kívánt oszlopokat.

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `

Hozzon létre egy új oszlopot úgy, hogy összekapcsolja az értékeket két másik oszloppal:

`SigninLogs | limit 10 | extend RiskUser = strcat(RiskDetail, "-", Identity) | project RiskUser, ClientAppUsed`




## <a name="create-an-alert-rule"></a>Riasztási szabály létrehozása  

Ez az eljárás bemutatja, hogyan küldhet riasztásokat a breakglass-fiók használatakor.

**Riasztási szabály létrehozása:**


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként.

2. Keresse meg az **Azure Active Directoryt**.

    ![A képernyőképen az Azure Search Azure Active Directory látható.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. A **figyelés** szakaszban kattintson a **naplók** elemre.

4. A **naplók** lapon kattintson az első **lépések** elemre.

5. A **Keresés** szövegmezőbe írja be a következőt: `SigninLogs |where UserDisplayName contains "BreakGlass" | project UserDisplayName`

6. Kattintson a **Futtatás** elemre.  

7. Az eszköztáron kattintson az **új riasztási szabály** elemre.

    ![Új riasztási szabály](./media/tutorial-log-analytics-wizard/new-alert-rule.png)

8. A **riasztási szabály létrehozása** lapon ellenőrizze, hogy a hatókör helyes-e.

9. A **feltétel** területen kattintson a következőre: **Ha az egyéni naplók átlagos keresési értéke nagyobb a <logic undefined> darabszámnál**

    ![Alapértelmezett feltétel](./media/tutorial-log-analytics-wizard/default-condition.png)

10. A **jel logikai beállítása** lap **riasztás logikája** szakaszában hajtsa végre a következő lépéseket:

    ![Riasztási logika](./media/tutorial-log-analytics-wizard/alert-logic.png)

    1. **A alapján** válassza ki **az eredmények számát**.

    2. As **operátorként** válassza a **nagyobb, mint** lehetőséget.

    3. **Küszöbértékként** válassza a **0** értéket. 

11. A **jel logikai beállítása** oldalon, a **kiértékelt alapján** szakaszban hajtsa végre a következő lépéseket:

    ![Értékelés alapja](./media/tutorial-log-analytics-wizard/evaluated-based-on.png)

    1. Az **időtartam (perc)** mezőben válassza az **5** lehetőséget.

    2. **Gyakoriság (perc)**, válassza az **5** lehetőséget.

    3. Kattintson a **Kész** gombra. 

12. A **művelet csoportban** kattintson a **műveleti csoport kiválasztása** elemre. 

    ![Műveletcsoport](./media/tutorial-log-analytics-wizard/action-group.png)

13. A **riasztási szabályhoz csatolni kívánt műveleti csoport kiválasztása** lapon kattintson a **műveleti csoport létrehozása** elemre. 

    ![Műveletcsoport létrehozása](./media/tutorial-log-analytics-wizard/create-action-group.png)

14. A **műveleti csoport létrehozása** oldalon hajtsa végre a következő lépéseket:

    ![Példány adatai](./media/tutorial-log-analytics-wizard/instance-details.png)

    1. A **műveleti csoport neve** szövegmezőbe írja be a **saját műveleti csoport** értéket.

    2. A **megjelenítendő név** szövegmezőbe írja be a **saját művelet** értéket.

    3. Kattintson a **Felülvizsgálat + létrehozás** elemre. 

    4. Kattintson a **Létrehozás** lehetőségre.


15. A **művelet testreszabása** területen hajtsa végre a következő lépéseket:

    ![Műveletek testreszabása](./media/tutorial-log-analytics-wizard/customize-actions.png)

    1. Válassza ki az **e-mail tárgyát**.

    2. A **Tárgy** mezőbe írja be a következőt: `Breakglass account has been used`

16. A **riasztási szabály részletei** területen hajtsa végre a következő lépéseket:

    ![Riasztási szabály részletei](./media/tutorial-log-analytics-wizard/alert-rule-details.png)

    1. A **riasztási szabály neve** szövegmezőbe írja be a következőt: `Breakglass account`

    2. A **Leírás** szövegmezőbe írja be a következőt: `Your emergency access account has been used`

17. Kattintson a **Riasztási szabály létrehozása** lehetőségre.   


## <a name="create-a-custom-workbook"></a>Egyéni munkafüzet létrehozása

Ez az eljárás bemutatja, hogyan hozhat létre egy új munkafüzetet a gyors üzembe helyezési sablonnal.




1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként.

2. Keresse meg az **Azure Active Directoryt**.

    ![A képernyőképen az Azure Search Azure Active Directory látható.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. A **figyelés** szakaszban kattintson a **munkafüzetek** elemre.

    ![Képernyőfelvétel: a Azure Portal menü figyelés elemét jeleníti meg a kiválasztott munkafüzetekkel.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. A rövid **útmutató szakaszban kattintson** az **üres** gombra.

    ![Első lépések](./media/tutorial-log-analytics-wizard/quick-start.png)

5. Kattintson a **Hozzáadás** parancsra.

    ![Munkafüzet hozzáadása](./media/tutorial-log-analytics-wizard/add-workbook.png)

6. Kattintson a **szöveg hozzáadása** gombra.

    ![Szöveg hozzáadása](./media/tutorial-log-analytics-wizard/add-text.png)


7. A szövegmezőbe írja be a következőt: `# Client apps used in the past week` , majd kattintson a **Szerkesztés kész** lehetőségre.

    ![Munkafüzet szövege](./media/tutorial-log-analytics-wizard/workbook-text.png)

8. Az új munkafüzetben kattintson a **Hozzáadás**, majd a **lekérdezés hozzáadása** lehetőségre.

    ![Lekérdezés hozzáadása](./media/tutorial-log-analytics-wizard/add-query.png)

9. A lekérdezés szövegmezőbe írja be a következőt: `SigninLogs | where TimeGenerated > ago(7d) | project TimeGenerated, UserDisplayName, ClientAppUsed | summarize count() by ClientAppUsed`

10. Kattintson a **lekérdezés futtatása** gombra.

    ![A képernyőképen a lekérdezés futtatása gomb látható.](./media/tutorial-log-analytics-wizard/run-workbook-query.png)

11. Az eszköztár **vizualizáció** területén kattintson a **tortadiagram** elemre.

    ![Tortadiagram](./media/tutorial-log-analytics-wizard/pie-chart.png)

12. Kattintson a **Szerkesztés kész** gombra.

    ![Szerkesztés kész](./media/tutorial-log-analytics-wizard/done-workbook-editing.png)



## <a name="add-a-query-to-a-workbook-template"></a>Lekérdezés hozzáadása munkafüzet-sablonhoz  

Ez az eljárás bemutatja, hogyan adhat hozzá egy lekérdezést egy meglévő munkafüzet-sablonhoz. A példa egy olyan lekérdezésen alapul, amely a feltételes hozzáférés sikerességének eloszlását mutatja a hibákhoz.


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként.

2. Keresse meg az **Azure Active Directoryt**.

    ![A képernyőképen az Azure Search Azure Active Directory látható.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. A **figyelés** szakaszban kattintson a **munkafüzetek** elemre.

    ![A képernyőképen a kiválasztott munkafüzeteket tartalmazó menüben látható a figyelés.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. A **feltételes hozzáférés** szakaszban kattintson a **feltételes hozzáférésű információ és jelentéskészítés** elemre.

    ![A képernyőképen a feltételes hozzáférési ismeretek és a jelentéskészítés lehetőség látható.](./media/tutorial-log-analytics-wizard/conditional-access-template.png)

5. Az eszköztáron kattintson a **Szerkesztés** elemre.

    ![A képernyőképen a Szerkesztés gomb látható.](./media/tutorial-log-analytics-wizard/edit-workbook-template.png)

6. Az eszköztáron kattintson a három pontra, majd **adja hozzá**, majd **adja hozzá a lekérdezést**.

    ![Munkafüzet-lekérdezés hozzáadása](./media/tutorial-log-analytics-wizard/add-custom-workbook-query.png)

7. A lekérdezés szövegmezőbe írja be a következőt: `SigninLogs | where TimeGenerated > ago(20d) | where ConditionalAccessPolicies != "[]" | summarize dcount(UserDisplayName) by bin(TimeGenerated, 1d), ConditionalAccessStatus`

8. Kattintson a **lekérdezés futtatása** gombra.

    ![A lekérdezés futtatásához a képernyőképen megjelenik a lekérdezés futtatása gomb.](./media/tutorial-log-analytics-wizard/run-workbook-insights-query.png)

9. Kattintson az **időtartomány** elemre, majd válassza **a beállítás a lekérdezésben** lehetőséget.

10. Kattintson a **vizualizáció** elemre, majd válassza a **sávdiagram** lehetőséget. 

11. Kattintson a **Speciális beállítások** elemre, és írja be a következőt: `Conditional Access status over the last 20 days` , majd kattintson a **Szerkesztés kész** lehetőségre. 

    ![Diagram címének beállítása](./media/tutorial-log-analytics-wizard/set-chart-title.png)








## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan kezelheti az eszközök identitásait a Azure Portal használatával.
> [!div class="nextstepaction"]
> [Figyelés](overview-monitoring.md)