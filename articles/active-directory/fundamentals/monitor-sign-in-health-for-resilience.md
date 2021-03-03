---
title: Az alkalmazás bejelentkezési állapotának figyelése a rugalmasság érdekében Azure Active Directory
description: Lekérdezések és értesítések létrehozása az alkalmazások bejelentkezési állapotának figyeléséhez.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/10/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad99c8d319a22f8b5388838b9d537de2f610478a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650991"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>Az alkalmazás bejelentkezési állapotának figyelése a rugalmasság érdekében

Az infrastruktúra rugalmasságának növeléséhez állítsa be az alkalmazás bejelentkezési állapotának figyelését a kritikus fontosságú alkalmazásokhoz, hogy riasztást kapjon, ha érintett incidens következik be. Ebben az esetben a bejelentkezési állapot munkafüzet alapján konfigurálhatja a riasztásokat. 

Ez a munkafüzet lehetővé teszi a rendszergazdák számára, hogy figyeljenek a bérlőben lévő alkalmazásokhoz tartozó hitelesítési kérelmeket. A következő főbb képességeket biztosítja:

* Konfigurálja úgy a munkafüzetet, hogy az összes vagy az egyes alkalmazásokat közel valós idejű adattal figyelje.

* A riasztások konfigurálásával értesítheti a hitelesítési minták változását, így megvizsgálhatja és végrehajthatja a műveleteket.

* Összehasonlíthatja a trendeket egy adott időszakban, például hetente, a munkafüzet alapértelmezett beállításával.

> [!NOTE]
> Ha szeretné megtekinteni az összes rendelkezésre álló munkafüzetet és az azok használatára vonatkozó előfeltételeket, tekintse meg, [hogyan használhatók Azure monitor munkafüzetek a jelentésekhez](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

Egy érintett esemény során két dolog fordulhat elő:

* Előfordulhat, hogy egy alkalmazáshoz tartozó bejelentkezések száma meredeken, mert a felhasználók nem tudnak bejelentkezni.

* A sikertelen bejelentkezések száma növelhető. 

Ez a cikk végigvezeti a bejelentkezési állapot munkafüzetének beállításán, hogy figyelje a felhasználók bejelentkezésének megszakadását.

## <a name="prerequisites"></a>Előfeltételek 

* Egy Azure AD-bérlő.

* Egy globális rendszergazdai vagy biztonsági rendszergazdai szerepkörrel rendelkező felhasználó az Azure AD-bérlőhöz.

* Log Analytics munkaterület az Azure-előfizetésben, amely naplók küldését Azure Monitor naplókba. 

   * Megtudhatja, hogyan [hozhat létre log Analytics munkaterületet](../../azure-monitor/logs/quick-create-workspace.md)

* Azure Monitor naplókba integrált Azure AD-naplók

   * Ismerje meg, hogyan [integrálhatja az Azure ad bejelentkezési naplóit Azure monitor streambe.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

 

## <a name="configure-the-app-sign-in-health-workbook"></a>Az alkalmazás bejelentkezési állapotának konfigurálása munkafüzetben 

A munkafüzetek eléréséhez nyissa meg a **Azure Portal**, válassza a **Azure Active Directory** lehetőséget, majd válassza a **munkafüzetek** elemet.

A munkafüzeteket a használat, a feltételes hozzáférés és a hibakeresés alatt tekintheti meg. Az alkalmazás bejelentkezési állapota munkafüzet a használat szakaszban jelenik meg.

Ha a munkafüzetet használja, akkor az a legutóbb módosított munkafüzetek szakaszban jelenhet meg.

![Képernyőfelvétel: a Azure Portal munkafüzeteit ábrázoló gyűjtemény.](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


Az alkalmazás-bejelentkezési állapot munkafüzet lehetővé teszi, hogy megjelenítse, mi történik a bejelentkezéssel. 

Alapértelmezés szerint a munkafüzet két gráfot mutat be. Ezek a grafikonok összehasonlítják, hogy mi történik az alkalmazás (ok) ban, és egy héttel ezelőtt Ugyanez az időszak. A kék vonalak aktuálisak, a narancssárga vonalak pedig az előző héten láthatók.

![A bejelentkezési állapot ábráit ábrázoló képernyőkép.](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**Az első gráf óradíjas használat (sikeres felhasználók száma)**. A sikeres felhasználók aktuális számának összevetése egy tipikus használati időszakra segít a használatot megkövetelő használati idő kimutatása. A sikeres használat arányának csökkenése segíthet észlelni a teljesítmény-és kihasználtsági problémákat, ha a meghibásodási arány nem lehetséges. Ha például a felhasználók nem tudják elérni az alkalmazást, hogy megpróbálják bejelentkezni, nem lesznek hibák, csak a használat csökkenése. Az ehhez az adathalmazhoz tartozó minta-lekérdezés a következő szakaszban található.

A második gráf óránkénti meghibásodási arányt jelez. A meghibásodási arányban megjelenő tüske a hitelesítési mechanizmusokkal kapcsolatos problémát jelezhet. A hibák aránya csak akkor mérhető, ha a felhasználók megpróbálják hitelesíteni magukat. Ha a felhasználók nem tudnak hozzáférni a kísérlethez, a hibák nem fognak megjelenni.

Beállíthat egy riasztást, amely egy adott csoportot értesíti, ha a használati vagy a meghibásodási arány meghaladja a megadott küszöbértéket. Az ehhez az adathalmazhoz tartozó minta-lekérdezés a következő szakaszban található.

 ## <a name="configure-the-query-and-alerts"></a>A lekérdezés és a riasztások konfigurálása

Riasztási szabályokat hozhat létre a Azure Monitorban, és rendszeres időközönként automatikusan futtathatja a mentett lekérdezéseket vagy az egyéni naplók kereséseit.

Az alábbi útmutatást követve hozhat létre e-mail-riasztásokat a diagramok által megjelenített lekérdezések alapján. Az alábbi minta-parancsfájlok e-mailben értesítést küldenek, ha

* a sikeres használat 90%-kal csökken az előző szakasz óránkénti használati gráfja alapján. 

* a meghibásodási arány 90%-kal nő ugyanazon az órára két nappal ezelőtt, ahogy az előző szakaszban az óránkénti meghibásodások aránya gráfban. 

 Az alapul szolgáló lekérdezés konfigurálásához és a riasztások beállításához hajtsa végre az alábbi lépéseket. A minta lekérdezést a konfiguráció alapjaként fogja használni. A szakasz végén megjelenik a lekérdezési struktúra magyarázata.

További információ a naplók létrehozásával, megtekintésével és kezelésével kapcsolatban Azure Monitor lásd: a [naplók kezelése riasztások](../../azure-monitor/alerts/alerts-log.md).

 
1. A munkafüzetben válassza a **Szerkesztés** lehetőséget, majd válassza a **lekérdezés ikont** közvetlenül a gráf jobb oldalán.   

   [![A munkafüzet szerkesztését bemutató képernyőkép.](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   Megnyílik a lekérdezési napló.

  [![A lekérdezési naplót ábrázoló képernyőfelvétel.](./media/monitor-sign-in-health-for-resilience/query-log.png)](/media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. Másolja a következő minta-parancsfájlok egyikét egy új Kusto-lekérdezéshez.

**Kusto-lekérdezés a használat eldobásához**

```Kusto

let thisWeek = SigninLogs

| where TimeGenerated > ago(1h)

| project TimeGenerated, AppDisplayName, UserPrincipalName

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize users = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

let lastWeek = SigninLogs

| where TimeGenerated between((ago(1h) - totimespan(2d))..(now() - totimespan(2d)))

| project TimeGenerated, AppDisplayName, UserPrincipalName

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize usersPriorWeek = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

thisWeek

| join

(

 lastWeek

)

on rn

| project TimeGenerated, users, usersPriorWeek, difference = abs(users - usersPriorWeek), max = max_of(users, usersPriorWeek)

| where (difference * 2.0) / max > 0.9

```

 

**Kusto-lekérdezés a hibák arányának növeléséhez**


```kusto

let thisWeek = SigninLogs

| where TimeGenerated > ago(1 h)

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h)

| project TimeGenerated, failureRate = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rn = row_number();

let lastWeek = SigninLogs

| where TimeGenerated between((ago(1 h) - totimespan(2d))..(ago(1h) - totimespan(2d)))

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h)

| project TimeGenerated, failureRatePriorWeek = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rn = row_number();

thisWeek

| join (lastWeek) on rn

| project TimeGenerated, failureRate, failureRatePriorWeek

| where abs(failureRate – failureRatePriorWeek) > **THRESHOLD VALUE**

```

3. Illessze be a lekérdezést az ablakba, és válassza a **Futtatás** lehetőséget. Győződjön meg arról, hogy az alábbi képen látható befejezett üzenet jelenik meg, majd az üzenet alá kerül.

   [![A lekérdezési eredmények futtatását bemutató képernyőkép.](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. Jelölje ki a lekérdezést, és válassza az + **új riasztási szabály** lehetőséget. 
 
   [![Az új riasztási szabály képernyőjét bemutató képernyőkép.](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. Riasztási feltételek konfigurálása. A feltétel szakaszban válassza ki a hivatkozást, **amikor az egyéni naplók átlagos keresési értéke nagyobb, mint a logikai definíciók száma**. A jel logikájának konfigurálása panelen görgessen a riasztási logikához.

   [![A riasztások konfigurálása képernyőt ábrázoló képernyőfelvétel.](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **Küszöbérték**: 0. Ez az érték minden eredményről riasztást küld.

   * **Próbaidőszak (perc)**: 60. Ez az érték egy órányi időt keres

   * **Gyakoriság (perc)**: 60. Ez az érték óránként egyszer állítja be az értékelési időszakot az előző órában.

   * Válassza a **Kész** lehetőséget.

6. A **műveletek** szakaszban adja meg a következő beállításokat:  

    [![A riasztási szabály létrehozása lapot ábrázoló képernyőfelvétel](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * A **műveletek** területen válassza a **műveleti csoport kiválasztása** lehetőséget, és adja hozzá azt a csoportot, amelyhez értesítést szeretne kapni a riasztásokról.

   * A **műveletek testreszabása** területen válassza az **e-mail riasztások** lehetőséget.

   * Adja hozzá a **tárgy sort**.

7. A **riasztási szabály részletei** szakaszban adja meg a következő beállításokat:

   * Adjon hozzá egy leíró nevet és egy leírást.

   * Válassza ki azt az **erőforráscsoportot** , amelyhez hozzá szeretné adni a riasztást.

   * Válassza ki a riasztás alapértelmezett **súlyosságát** .

   * A **létrehozáskor válassza a riasztási szabály engedélyezése** lehetőséget, ha azt szeretné, hogy azonnal elérhető legyen, máskülönben válassza a **riasztások mellőzése** lehetőséget.

8. Válassza a **Riasztási szabály létrehozása** lehetőséget.

9. Válassza a **Mentés** lehetőséget, adja meg a lekérdezés nevét, majd **mentse lekérdezésként egy riasztási kategóriával**. Ezután válassza a **Mentés** újra lehetőséget.  

   [![A lekérdezés mentése gombot ábrázoló képernyőkép.](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)



### <a name="refine-your-queries-and-alerts"></a>A lekérdezések és a riasztások pontosítása
A maximális hatékonyság érdekében módosítsa a lekérdezéseket és a riasztásokat.

* Ügyeljen arra, hogy tesztelje a riasztásokat.

* Módosítsa a riasztások érzékenységét és gyakoriságát, hogy fontos értesítéseket kapjon. A rendszergazdák inszenzibilizációt kaphatnak a riasztásokhoz, ha túl sok, és hiányzik valami fontos. 

* Győződjön meg arról, hogy az e-mailek, amelyekről a riasztások beérkeznek a rendszergazda e-mail-ügyfelei az engedélyezett küldők listájára kerülnek. Ellenkező esetben előfordulhat, hogy az e-mail-ügyfélprogramban levélszemét-szűrő miatt nem lehet értesítéseket kihagyni. 

* A Azure Monitor riasztások lekérdezése csak az elmúlt 48 óra eredményét tartalmazza. [Ez a tervezés aktuális korlátozása](https://github.com/MicrosoftDocs/azure-docs/issues/22637).

## <a name="create-processes-to-manage-alerts"></a>Folyamatok létrehozása a riasztások kezeléséhez

A lekérdezés és a riasztások beállítása után üzleti folyamatokat hozhat létre a riasztások kezeléséhez.

* Ki fogja figyelni a munkafüzetet, és Mikor?
* Ha a rendszer riasztást generál, ki vizsgálja ki?

* Mik a kommunikációs igények? Ki hozza létre a kommunikációt, és kik kapják meg őket?

* Ha áramkimaradás történik, milyen üzleti folyamatokat kell elindítani?

## <a name="next-steps"></a>Következő lépések

[További információ a munkafüzetekről](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

 

 

