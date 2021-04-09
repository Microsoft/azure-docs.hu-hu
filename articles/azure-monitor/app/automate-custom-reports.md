---
title: Egyéni jelentések automatizálása Azure Application Insights-beli adatkezeléssel
description: Egyéni napi/heti/havi jelentések automatizálása Azure Application Insights-adatokat
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: 39970227fc94d3073688b23cc530462f368a6ecf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590036"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Egyéni jelentések automatizálása Azure Application Insights-beli adatkezeléssel

Az időszakos jelentések segítenek megőrizni a csapatot arról, hogy az üzleti szempontból kritikus fontosságú szolgáltatások hogyan használják. A fejlesztők, a DevOps-és SRE-csapatok, valamint a felettesei az automatizált jelentések segítségével megbízhatóan hozhatnak be bepillantást anélkül, hogy mindenkit be kellene jelentkezniük a portálra. Ezek a jelentések segítséget nyújtanak a késések, a betöltési és a meghibásodási díjak fokozatos növekedésének azonosításában is, amelyek esetleg nem indítanak riasztási szabályokat.

Minden vállalat egyedi jelentéskészítési igényeivel rendelkezik, például:

* Mérőszámok vagy egyéni metrikák adott percentilis-összesítései egy jelentésben.
* Különböző jelentések készíthetők napi, heti és havi adatösszesítéshez különböző célközönségek számára.
* Szegmentálás egyéni attribútumok, például régió vagy környezet alapján.
* Egyes AI-erőforrások csoportosítása egyetlen jelentésben, még akkor is, ha különböző előfizetésekben vagy erőforráscsoportok között lehetnek.
* Külön jelentések a szelektív célközönségnek elküldett bizalmas mérőszámokat tartalmazó jelentésekről.
* Jelentések az érintett felek számára, akik nem férhetnek hozzá a portál erőforrásaihoz.

> [!NOTE] 
> A heti Application Insights kivonatoló e-mail nem engedélyezte a testreszabást, és az alább felsorolt egyéni beállítások mellett megszűnik. Az utolsó heti kivonatoló e-mailt 2018 június 11-én küldi el a rendszer. Az alábbi beállítások egyikét konfigurálhatja hasonló egyéni jelentések beszerzéséhez (használja az alább javasolt lekérdezést).

## <a name="to-automate-custom-report-emails"></a>Egyéni jelentési e-mailek automatizálása

Programozott módon [lekérdezheti Application Insightsi](https://dev.applicationinsights.io/) adataikat, hogy egyéni jelentéseket készítsenek az ütemterv alapján. A következő lehetőségek segíthetnek a gyors kezdésben:

* [Jelentések automatizálása a Power automatizálva](../logs/logicapp-flow-connector.md)
* [Jelentések automatizálása Logic Apps](automate-with-logic-apps.md)
* A figyelési forgatókönyvben használja a "Application Insights ütemezett kivonatoló" [Azure-függvény](../../azure-functions/functions-get-started.md) sablonját. Ez a függvény a SendGrid használatával továbbítja az e-mailt. 

    ![Azure-függvény sablonja](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Minta lekérdezés heti kivonatoló e-mailhez
A következő lekérdezés több adatkészlet összekapcsolását mutatja be egy heti kivonatoló e-mailhez, például a jelentéshez. Szabja testre az igény szerint, és használja a fent felsorolt beállítások bármelyikével a heti jelentések automatizálásához.

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Ütemezett kivonatoló jelentés Application Insights

1. Hozzon létre egy Azure-függvényalkalmazás. ( _a_ Application Insights csak akkor szükséges, ha az új függvényalkalmazás Application Insights) szeretné figyelni.

   Az Azure Functions dokumentációjában megtudhatja, hogyan [hozhat létre egy Function-alkalmazást](../../azure-functions/functions-get-started.md)

2. Miután az új függvényalkalmazás befejezte az üzembe helyezést, válassza az **Ugrás az erőforráshoz** lehetőséget.

3. Válassza az **új függvény** lehetőséget.

   ![Képernyőkép létrehozása új függvény létrehozásához](./media/automate-custom-reports/new-function.png)

4. Válassza ki a **_Application Insights ütemezett kivonatoló sablont_**.

     > [!NOTE]
     > Alapértelmezés szerint a Function apps a Runtime 3. x verziójával jön létre. Az Application Insights ütemezett kivonatoló sablon használatához a Azure Functions Runtime **1. x** [verzióját kell megcéloznia](../../azure-functions/set-runtime-version.md) . A futtatókörnyezet verziójának módosításához lépjen a Configuration > Function Runtime-beállítások menüpontra. ![futtatókörnyezet képernyőképe](./media/automate-custom-reports/change-runtime-v.png)

   ![Új függvény Application Insights sablon képernyőképe](./media/automate-custom-reports/function-app-04.png)

5. Adjon meg egy megfelelő címzett e-mail-címet a jelentéshez, és válassza a **Létrehozás** lehetőséget.

   ![A Function Settings képernyőképe](./media/automate-custom-reports/scheduled-digest.png)

6. Válassza ki a **függvényalkalmazás**  >  **platform funkcióinak**  >  **konfigurációját**.

    ![Az Azure Function alkalmazás beállításainak képernyőképe](./media/automate-custom-reports/config.png)

7. Három új Alkalmazásbeállítások létrehozása megfelelő megfelelő értékekkel, ``AI_APP_ID`` ``AI_APP_KEY`` és ``SendGridAPI`` . Kattintson a **Mentés** gombra.

     ![Function Integration Interface – képernyőfelvétel](./media/automate-custom-reports/app-settings.png)
    
    (A AI_ értékek a jelentéshez használni kívánt Application Insights erőforrás API-hozzáférése alatt találhatók. Ha nem rendelkezik Application Insights API-kulccsal, lehetőség van az **API-kulcs létrehozására**.)
    
   * AI_APP_ID = alkalmazás azonosítója
   * AI_APP_KEY = API-kulcs
   * SendGridAPI = SendGrid API-kulcs

     > [!NOTE]
     > Ha nem rendelkezik SendGrid-fiókkal, létrehozhat egyet. A Azure Functions SendGrid dokumentációja [itt](../../azure-functions/functions-bindings-sendgrid.md)található. Ha csak minimális magyarázatot szeretne arról, hogyan kell beállítani a SendGrid-t, és egy API-kulcsot kell előállítania, a cikk végén található. 

8. Válassza az **integráció** lehetőséget, majd a kimenetek területen kattintson a **SendGrid ($Return)** elemre.

     ![Kimeneti képernyőfelvétel](./media/automate-custom-reports/integrate.png)

9. A **SendGridAPI kulcs alkalmazása beállításnál** válassza ki a **SendGridAPI** újonnan létrehozott alkalmazás-beállításait.

     ![függvényalkalmazás képernyőkép futtatása](./media/automate-custom-reports/sendgrid-output.png)

10. A függvényalkalmazás futtatása és tesztelése.

     ![Képernyőkép tesztelése](./media/automate-custom-reports/function-app-11.png)

11. Ellenőrizze az e-mailben, hogy az üzenet elküldése/fogadása sikeres volt-e.

     ![E-mail tárgyi sora – képernyőkép](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid az Azure-ban

Ezek a lépések csak akkor érvényesek, ha még nem rendelkezik konfigurált SendGrid-fiókkal.

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget > a **SendGrid e-mailes kézbesítésének** megkeresése > kattintson a **Létrehozás** gombra > töltse ki a SendGrid-specifikus létrehozási utasításokat.

     ![SendGrid-erőforrás létrehozása képernyőfelvétel](./media/automate-custom-reports/sendgrid.png)

2. Miután létrehozta a SendGrid-fiókokat, válassza a **kezelés** lehetőséget.

     ![A beállítások API-kulcs képernyőképe](./media/automate-custom-reports/sendgrid-manage.png)

3. Ekkor elindul a SendGrid webhelye. Válassza a **Beállítások**  >  **API-kulcsok** elemet.

     ![API-kulcs alkalmazásának létrehozása és megtekintése képernyőkép](./media/automate-custom-reports/function-app-15.png)

4. Hozzon létre egy API-kulcsot > válassza a **létrehozás & nézet** lehetőséget. (Tekintse át a SendGrid dokumentációját a korlátozott hozzáférésről annak meghatározásához, hogy az API-kulcs milyen szintű engedélyekkel rendelkezik. A teljes hozzáférés csak példaként van kiválasztva.)

   ![Teljes hozzáférés képernyőfelvétele](./media/automate-custom-reports/function-app-16.png)

5. Másolja a teljes kulcsot, ez az érték az, amire szüksége van a függvényalkalmazás beállításaiban a SendGridAPI értékeként

   ![Az API-kulcs másolása képernyőfelvétel](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Következő lépések

* További információ az [elemzési lekérdezések](../logs/get-started-queries.md)létrehozásáról.
* További információ az [Application Insights adatainak programozott lekérdezéséről](https://dev.applicationinsights.io/)
* További tudnivalók a [Logic Apps](../../logic-apps/logic-apps-overview.md) szolgáltatásról
* További információ a [Microsoft Power automatizáló](https://ms.flow.microsoft.com)szolgáltatásról.
