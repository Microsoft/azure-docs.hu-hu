---
title: Intelligens észlelés – meghibásodási rendellenességek, Application Insights | Microsoft Docs
description: Riasztást küld a webalkalmazásnak küldött sikertelen kérelmek arányának szokatlan változásairól, és diagnosztikai elemzéseket is biztosít. Nincs szükség konfigurációra.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: 0f4de3aaba4acf86df37048134089326196e87ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587543"
---
# <a name="smart-detection---failure-anomalies"></a>Intelligens észlelés – hibák rendellenességei
[Application Insights](./app-insights-overview.md) automatikusan riasztást küld a közel valós időben, ha a webalkalmazása rendellenes növekedést tapasztal a sikertelen kérések arányában. Ez a művelet szokatlanul megnövekszik a HTTP-kérelmek vagy a sikertelenként jelentett függőségi hívások gyakorisága. A kérelmek esetében a sikertelen kérelmek általában 400-as vagy magasabb szintű hibakódokkal rendelkeznek. A probléma osztályozásának és diagnosztizálásának elősegítése érdekében a riasztás részleteiben a hibák és a kapcsolódó alkalmazásadatok jellemzőinek elemzése szerepel. További diagnosztizálásra a Application Insights portálra mutató hivatkozások is rendelkezésre állnak. A szolgáltatásnak nincs szüksége beállításra és konfigurációra, mivel gépi tanulási algoritmusokat használ a normál meghibásodási arány előrejelzéséhez.

Ez a funkció bármely, a felhőben vagy a saját kiszolgálókon üzemeltetett webalkalmazáshoz használható, amely alkalmazás-vagy függőségi adatait eredményezi. Ha például olyan feldolgozói szerepkörrel rendelkezik, amely meghívja a [TrackRequest ()](./api-custom-events-metrics.md#trackrequest) vagy a [TrackDependency ()](./api-custom-events-metrics.md#trackdependency)metódust.

Miután beállította [Application Insights a projekthez](./app-insights-overview.md), és ha az alkalmazás egy bizonyos minimális adatmennyiséget hoz létre, a hibák rendellenes észlelése 24 órát vesz igénybe az alkalmazás normál működésének megismerése előtt, és riasztásokat küldhet.

Íme egy példa a riasztásra:

:::image type="content" source="./media/proactive-failure-diagnostics/013.png" alt-text="Minta intelligens észlelési riasztás, amely a fürt elemzését mutatja a hiba miatt." lightbox="./media/proactive-failure-diagnostics/013.png":::

A riasztás részletei a következőket közlik:

* A hiba aránya a normál alkalmazás működéséhez képest.
* Hány felhasználót érintenek, hogy tudja, mennyit kell aggódnia.
* A hibákhoz tartozó jellemző minta. Ebben a példában egy adott válasz kódja, a kérelem neve (művelet) és az alkalmazás verziója szerepel. Ezzel azonnal megtudhatja, hol kezdjen el keresni a kódban. Más lehetőségek lehetnek egy adott böngésző vagy ügyféloldali operációs rendszer.
* A jellemző hibákkal társított kivételek, naplók és függőségi hibák (adatbázisok vagy más külső összetevők).
* Közvetlenül a kapcsolódó keresésekre mutató hivatkozásokat Application Insights.

## <a name="benefits-of-smart-detection"></a>Az intelligens észlelés előnyei
A szokásos [metrikus riasztások](../alerts/alerts-log.md) jelzik, hogy probléma lehet. Az intelligens észlelés azonban elindítja a diagnosztikai munkát, és sok olyan elemzést hajt végre, amelyet egyébként el kellene végeznie. Az eredményeket szépen csomagolja, és segít a probléma gyökerének gyors megszerzésében.

## <a name="how-it-works"></a>Működés
Az intelligens észlelés figyeli az alkalmazástól kapott adatokat, és különösen a hibák sebességét. Ez a szabály számolja a kérelmek számát, amelyekben a `Successful request` tulajdonság hamis, valamint azon függőségi hívások száma, amelyek esetében a `Successful call` tulajdonság hamis. Alapértelmezés szerint a kérelmek esetében `Successful request == (resultCode < 400)` (hacsak nem írt egyéni kódot a saját [TrackRequest](./api-custom-events-metrics.md#trackrequest) -hívások [szűréséhez](./api-filtering-sampling.md#filtering) vagy létrehozásához). 

Az alkalmazás teljesítménye jellemző viselkedési mintázatot tartalmaz. Bizonyos kérelmek vagy függőségi hívások hajlamosabbak a többitől eltérő hibákra; a teljes meghibásodási arány pedig a terhelés növekedésével járhat. Az intelligens észlelés a gépi tanulás használatával keresi meg ezeket a rendellenességeket.

Ahogy az adatok bekerülnek Application Insights a webalkalmazásból, az intelligens észlelés összehasonlítja a jelenlegi viselkedést az elmúlt néhány napban látható mintákkal. Ha a korábbi teljesítményhez képest rendellenesen megnőtt a meghibásodási arány, a rendszer egy elemzést indít el.

Az elemzések indításakor a szolgáltatás a sikertelen kérelemhez tartozó fürt elemzését hajtja végre, hogy megpróbálja azonosítani a hibákat jellemző értékek mintáját. 

A fenti példában az elemzés felderítette, hogy a legtöbb hiba egy adott eredmény kódjára, a kérelem nevére, a kiszolgáló URL-címére és a szerepkör-példányra vonatkozik. 

Ha a szolgáltatás ezekkel a hívásokkal van kialakítva, az elemző kivételt és függőségi hibát keres az általa azonosított fürt kéréseivel kapcsolatban, valamint egy példát a kérelmekhez társított nyomkövetési naplókra.

Az eredményül kapott elemzést riasztásként küldi el a rendszer, ha nem konfigurálta.

A [manuálisan beállított riasztásokhoz](../alerts/alerts-log.md)hasonlóan megtekintheti a kilőtt riasztás állapotát, amely feloldható, ha a probléma kijavítva van. Adja meg a riasztási szabályokat a Application Insights erőforrás riasztások lapján. Más riasztásokkal ellentétben azonban nem kell beállítania vagy konfigurálnia az intelligens észlelést. Ha szeretné, letilthatja vagy módosíthatja a cél e-mail-címeit.

### <a name="alert-logic-details"></a>Riasztási logika részletei

A riasztásokat a szabadalmaztatott gépi tanulási algoritmus váltja ki, így nem tudjuk megosztani a pontos megvalósítás részleteit. Ezzel megértettük, hogy néha többet kell tudnia az alapul szolgáló logikai működéséről. Az elsődleges tényezők, amelyekkel megállapítható, hogy a riasztást ki kell-e indítani: 

* A kérelmek/függőségek sikertelen százalékos arányának elemzése 20 percen belül egy gördülő időablakban.
* Az elmúlt 20 perc hibájának az elmúlt 40 percben és az elmúlt hét napban való összehasonlítása, valamint olyan jelentős eltérések keresése, amelyek meghaladják a szórást.
* A minimális meghibásodási arányra vonatkozó adaptív korlát használata, amely az alkalmazás kérelmek/függőségek mennyisége alapján változik.
* Létezik olyan logika, amely képes automatikusan feloldani a kilőtt riasztások figyelő feltételét, ha a probléma már nem észlelhető 8-24 órán keresztül.
  Megjegyzés: az aktuális tervben. egy értesítés vagy művelet nem lesz elküldve egy intelligens észlelési riasztás feloldásakor. Megtekintheti, hogy az intelligens észlelési riasztás megoldódott-e a Azure Portalban.

## <a name="configure-alerts"></a>Riasztások konfigurálása

Az intelligens észlelési riasztási szabályt letilthatja a portálról vagy Azure Resource Manager használatával ([lásd a sablon példáját](./proactive-arm-config.md)).

Ez a riasztási szabály egy "Application Insights intelligens észlelés" nevű társított [műveleti csoporttal](../alerts/action-groups.md) jön létre, amely e-mail-és webhook-műveleteket tartalmaz, és kiterjeszthető további műveletek elindítására, ha a riasztás tüzet okoz.

> [!NOTE]
> Az ebből a riasztási szabályból küldött e-mail-értesítéseket a rendszer alapértelmezés szerint az előfizetés figyelési olvasójának és a közreműködő szerepkörök figyeléséhez társított felhasználók számára küldi el. Erről további információt [itt](./proactive-email-notification.md)talál.
> A riasztási szabály által küldött értesítések a [Common Alert sémát](../alerts/alerts-common-schema.md)követik.
>

Nyissa meg a riasztások lapot. A meghibásodási rendellenességek riasztási szabályai szerepelnek a manuálisan beállított riasztásokkal együtt, és láthatja, hogy jelenleg a riasztási állapotban van-e.

:::image type="content" source="./media/proactive-failure-diagnostics/021.png" alt-text="A Application Insights erőforrás lapon kattintson a riasztások csempére, majd a riasztási szabályok kezelése lehetőségre." lightbox="./media/proactive-failure-diagnostics/021.png":::

A beállításhoz kattintson a riasztásra.

:::image type="content" source="./media/proactive-failure-diagnostics/032.png" alt-text="Szabály konfigurációs képernyője." lightbox="./media/proactive-failure-diagnostics/032.png":::

Figyelje meg, hogy letilthatja vagy törölheti a hiba rendellenességét jelző riasztási szabályt, de nem hozhat létre egy másikat ugyanazon a Application Insights erőforráson.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Hiba – példa a riasztások webhook-adatforgalmára

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [
                [
                    "2019-10-30T05:20:00Z",
                    0
                ],
                [
                    "2019-10-30T05:40:00Z",
                    100
                ],
                [
                    "2019-10-30T06:00:00Z",
                    0
                ],
                [
                    "2019-10-30T06:20:00Z",
                    0
                ],
                [
                    "2019-10-30T06:40:00Z",
                    100
                ],
                [
                    "2019-10-30T07:00:00Z",
                    0
                ],
                [
                    "2019-10-30T07:20:00Z",
                    0
                ],
                [
                    "2019-10-30T07:40:00Z",
                    100
                ],
                [
                    "2019-10-30T08:00:00Z",
                    0
                ],
                [
                    "2019-10-30T08:20:00Z",
                    0
                ],
                [
                    "2019-10-30T08:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:00:00Z",
                    0
                ],
                [
                    "2019-10-30T17:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:00:00Z",
                    0
                ],
                [
                    "2019-10-30T09:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:40:00Z",
                    100
                ],
                [
                    "2019-10-30T10:00:00Z",
                    0
                ],
                [
                    "2019-10-30T10:20:00Z",
                    0
                ],
                [
                    "2019-10-30T10:40:00Z",
                    100
                ],
                [
                    "2019-10-30T11:00:00Z",
                    0
                ],
                [
                    "2019-10-30T11:20:00Z",
                    0
                ],
                [
                    "2019-10-30T11:40:00Z",
                    100
                ],
                [
                    "2019-10-30T12:00:00Z",
                    0
                ],
                [
                    "2019-10-30T12:20:00Z",
                    0
                ],
                [
                    "2019-10-30T12:40:00Z",
                    100
                ],
                [
                    "2019-10-30T13:00:00Z",
                    0
                ],
                [
                    "2019-10-30T13:20:00Z",
                    0
                ],
                [
                    "2019-10-30T13:40:00Z",
                    100
                ],
                [
                    "2019-10-30T14:00:00Z",
                    0
                ],
                [
                    "2019-10-30T14:20:00Z",
                    0
                ],
                [
                    "2019-10-30T14:40:00Z",
                    100
                ],
                [
                    "2019-10-30T15:00:00Z",
                    0
                ],
                [
                    "2019-10-30T15:20:00Z",
                    0
                ],
                [
                    "2019-10-30T15:40:00Z",
                    100
                ],
                [
                    "2019-10-30T16:00:00Z",
                    0
                ],
                [
                    "2019-10-30T16:20:00Z",
                    0
                ],
                [
                    "2019-10-30T16:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:30:00Z",
                    50
                ]
            ],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [
                {
                    "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
                }
            ],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [
                {
                    "rootJsonNode": null,
                    "sectionName": null,
                    "displayControls": [
                        {
                            "property": "DetectionSummary",
                            "displayName": "What was detected?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "FormattedOccurenceTime",
                            "displayName": "When did this occur?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "DetectedFailureRate",
                            "displayName": "Detected failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "NormalFailureRate",
                            "displayName": "Normal failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "chartType": "Line",
                            "xAxisType": "Date",
                            "yAxisType": "Percentage",
                            "xAxisName": "",
                            "yAxisName": "",
                            "property": "FailureRateChart",
                            "displayName": "Failure rate over last 12 hours",
                            "type": "Chart",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "defaultLoad": true,
                            "displayConfig": [
                                {
                                    "rootJsonNode": null,
                                    "sectionName": null,
                                    "displayControls": [
                                        {
                                            "showHeader": false,
                                            "columns": [
                                                {
                                                    "property": "Name",
                                                    "displayName": "Name"
                                                },
                                                {
                                                    "property": "Value",
                                                    "displayName": "Value"
                                                }
                                            ],
                                            "property": "tables[0].rows[0][0]",
                                            "displayName": "All of the failed requests had these characteristics:",
                                            "type": "Table",
                                            "isOptional": false,
                                            "isPropertySerialized": true
                                        }
                                    ]
                                }
                            ],
                            "property": "ArmSystemEventsRequest",
                            "displayName": "",
                            "type": "ARMRequest",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "showHeader": false,
                            "columns": [
                                {
                                    "property": "Link",
                                    "displayName": "Link"
                                }
                            ],
                            "property": "LinksTable",
                            "displayName": "Links",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": false
                        }
                    ]
                }
            ]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Riasztás osztályozása és diagnosztizálása

A riasztás azt jelzi, hogy a sikertelen kérelmek arányának rendellenes növekedése észlelhető. Valószínű, hogy probléma merült fel az alkalmazással vagy a környezettel kapcsolatban.

A további vizsgálathoz kattintson az "összes adat megtekintése Application Insights" elemre. az ezen a lapon található hivatkozások a megfelelő kérelmekre, kivételekre, függőségekre vagy nyomokra szűrt [keresési oldalra](./diagnostic-search.md) mutatnak. 

A [Azure Portal](https://portal.azure.com)is megnyithatja, navigáljon az alkalmazás Application Insights erőforrásához, és nyissa meg a hibák lapot.

A "hibák diagnosztizálása" gombra kattintva további részleteket tudhat meg, és elháríthatja a problémát.

:::image type="content" source="./media/proactive-failure-diagnostics/051.png" alt-text="Diagnosztikai keresés." lightbox="./media/proactive-failure-diagnostics/051.png#lightbox":::

A kérések és a felhasználók számának százalékos aránya alapján eldöntheti, hogy milyen sürgős a probléma. A fenti példában a 78,5%-os meghibásodási arány a normál 2,2%-os arányt hasonlítja össze, ami azt jelzi, hogy valami rossz történik. Másfelől azonban csak 46 felhasználót érintettek. Ha ez volt az alkalmazása, azt is megtudhatja, hogy mennyire súlyos ez a lehetőség.

Sok esetben a probléma gyorsan diagnosztizálható a kérelem nevétől, a kivételtől, a függőségi hibáktól és a megadott nyomkövetési adatoktól.

Ebben a példában kivétel történt a SQL Database miatt, mert elérte a kérelmekre vonatkozó korlátot.

:::image type="content" source="./media/proactive-failure-diagnostics/052.png" alt-text="Sikertelen kérelem részletei." lightbox="./media/proactive-failure-diagnostics/052.png":::

## <a name="review-recent-alerts"></a>Legutóbbi riasztások áttekintése

A legutóbbi kilőtt riasztások megtekintéséhez kattintson a **riasztások** elemre a Application Insights erőforrás lapon:

:::image type="content" source="./media/proactive-failure-diagnostics/070.png" alt-text="Riasztások összegzése" lightbox="./media/proactive-failure-diagnostics/070.png":::

## <a name="whats-the-difference-"></a>Mi a különbség...
A meghibásodási rendellenességek intelligens észlelése kiegészíti a Application Insights hasonló, de különböző funkcióit.

* a [metrikai riasztásokat](../alerts/alerts-log.md) Ön állítja be, és a metrikák széles skáláját figyeli, például a CPU-kihasználtságot, a kérelmek sebességét, az oldal betöltési idejét stb. Ezekkel a riasztással figyelmeztetheti Önt, például ha további erőforrásokat kell hozzáadnia. Ezzel szemben a meghibásodási rendellenességek intelligens észlelése a kritikus mérőszámok kis tartományát fedi le (jelenleg csak a sikertelen kérelmek arányát), amely úgy lett kialakítva, hogy közel valós időben értesítse Önt, amint a webalkalmazás sikertelen kérelmének aránya a webalkalmazás normál viselkedéséhez képest növekszik. A metrikus riasztásokkal ellentétben az intelligens észlelés automatikusan beállítja és frissíti a küszöbértékeket a viselkedés változásakor. Az intelligens észlelés is elindítja a diagnosztikai munkát, így időt takaríthat meg a problémák megoldásában.

* A [teljesítménybeli rendellenességek intelligens észlelése](proactive-performance-diagnostics.md) a gépi intelligenciával észleli a mérőszámokban szokatlan mintákat, és nincs szükség konfigurációra. A meghibásodási rendellenességek intelligens észlelése miatt azonban a teljesítménybeli rendellenességek intelligens észlelésének célja, hogy megkeresse a használat sokrétű, esetlegesen kiszolgált szegmenseit, például egy adott böngésző adott lapjain. Az elemzés naponta történik, és ha bármilyen eredmény található, valószínűleg sokkal kevésbé sürgős, mint a riasztás. Ezzel szemben a hibákra vonatkozó rendellenességek elemzése folyamatosan történik a bejövő alkalmazásadatok esetében, és perceken belül értesítést kap, ha a kiszolgáló meghibásodási aránya nagyobb a vártnál.

## <a name="if-you-receive-a-smart-detection-alert"></a>Ha intelligens észlelési riasztást kap
*Miért kaptam ezt a riasztást?*

* A sikertelen kérelmek arányának rendellenes emelkedését észlelte a korábbi időszak normál alapértékéhez képest. A hibák és a kapcsolódó alkalmazásadatok elemzése után úgy gondoljuk, hogy probléma merült fel.

*Az értesítésben feltétlenül probléma merült fel?*

* Megpróbálunk riasztást kapni az alkalmazások megszakadásával vagy romlásával kapcsolatban, de csak teljes mértékben tisztában lehet az alkalmazásra és a felhasználókra gyakorolt hatásokkal.

*Így néz ki az alkalmazás adatai?*

* Nem. A szolgáltatás teljesen automatikus. Csak Ön kapja meg az értesítéseket. Az adatai [magánjellegűek](./data-retention-privacy.md).

*Elő kell fizetnünk erre a riasztásra?*

* Nem. Minden kérelem-adatokat küldő alkalmazás rendelkezik az intelligens észlelési riasztási szabállyal.

*Lemondható vagy lekérhetem a munkatársaknak küldött értesítéseket?*

* Igen, a riasztási szabályok területen kattintson az intelligens észlelési szabályra a konfigurálásához. Letilthatja a riasztást, vagy megváltoztathatja a riasztás címzettjeit.

*Elfelejtettem az e-mailt. Hol találhatók az értesítések a portálon?*

* A tevékenység naplófájljaiban. Az Azure-ban nyissa meg a Application Insights erőforrást az alkalmazáshoz, majd válassza a tevékenységek naplóit.

*Néhány riasztás az ismert problémákról szól, és nem szeretném fogadni őket.*

* Használhatja a [riasztás műveleti szabályok](../alerts/alerts-action-rules.md) elnyomása funkciót.

## <a name="next-steps"></a>Következő lépések
Ezek a diagnosztikai eszközök segítenek az alkalmazás adatainak vizsgálatában:

* [Metrika-kezelő](../essentials/metrics-charts.md)
* [Keresési ablak](./diagnostic-search.md)
* [Analitika – hatékony lekérdezési nyelv](../logs/log-analytics-tutorial.md)

Az intelligens észlelések automatikusak. De lehet, hogy néhány riasztást szeretne beállítani?

* [Manuálisan konfigurált metrikai riasztások](../alerts/alerts-log.md)
* [Rendelkezésre állási webes tesztek](./monitor-web-app-availability.md)