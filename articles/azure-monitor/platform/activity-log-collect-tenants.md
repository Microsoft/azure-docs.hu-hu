---
title: Azure-beli tevékenységek naplóinak begyűjtése egy Log Analytics munkaterületre az Azure-bérlők között | Microsoft Docs
description: Event Hubs és Logic Apps használatával gyűjthet adatokat az Azure-tevékenység naplójából, és elküldheti azt egy másik bérlő Azure Monitor egy Log Analytics munkaterületére.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2019
ms.openlocfilehash: 52bf8b955ef4dc9cfae7fd74fbad0df744609196
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669267"
---
# <a name="collect-azure-activity-logs-into-azure-monitor-across-azure-active-directory-tenants-legacy"></a>Azure-beli tevékenységek naplóinak gyűjtése a Azure Monitor Azure Active Directory-bérlők között (örökölt)

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan konfigurálható az Azure-beli tevékenységek naplója az Azure-beli bérlők között Log Analytics munkaterületen.  Mostantól a tevékenység naplóját begyűjtheti egy Log Analytics munkaterületre egy, az erőforrás-naplók összegyűjtéséhez hasonló diagnosztikai beállítás használatával. Lásd: [Az Azure-Tevékenységnaplók összegyűjtése és elemzése log Analytics munkaterületen Azure monitor](activity-log-collect.md).


Ez a cikk a Logic Apps Azure Log Analytics adatgyűjtő összekötőjét használó Azure Azure Monitor-beli tevékenységek naplóinak egy Log Analytics munkaterületre való gyűjtésének módszerét ismerteti. A cikkben ismertetett eljárást akkor érdemes használni, ha egy másik Azure Active Directory bérlő munkaterületére kell naplókat küldenie. Például ha felügyelt szolgáltatást kínál, előfordulhat, hogy tevékenységnaplókat szeretne gyűjteni az egyik ügyfél előfizetéséből, és a saját előfizetése Log Analytics-munkaterületén szeretné tárolni azokat.

Ha a Log Analytics munkaterület ugyanabban az Azure-előfizetésben van, vagy egy másik előfizetésben, de ugyanabban a Azure Active Directoryban, akkor az Azure-Tevékenységnaplók összegyűjtéséhez használja az Azure-beli [log Analytics munkaterület összegyűjtése és elemzése](activity-log-collect.md) című témakör lépéseit Azure monitor.

## <a name="overview"></a>Áttekintés

A forgatókönyvben alkalmazott stratégia az, hogy az Azure-tevékenységnapló eseményeket küld egy [eseményközpontnak](../../event-hubs/event-hubs-about.md), ahol egy [logikai alkalmazás](../../logic-apps/logic-apps-overview.md) továbbítja azokat a Log Analytics-munkaterületnek. 

![a tevékenység naplójából Log Analytics munkaterületre irányuló adatforgalom képe](media/collect-activity-logs-subscriptions/data-flow-overview.png)

A módszer többek között a következő előnyöket kínálja:
- Kis késéssel jár, mivel a rendszer az eseményközpontba streameli az Azure-tevékenységnaplót.  Ekkor a rendszer elindítja a logikai alkalmazást, és a munkaterületre könyveli azokat. 
- Csak minimális mennyiségű kódra van szükség, és nem kell kiszolgálói infrastruktúrát üzembe helyezni.

A cikk a következőket mutatja be:
1. Eseményközpont létrehozása. 
2. Tevékenységnaplók eseményközpontba való exportálása az Azure-tevékenységnapló exportálási profiljának használatával.
3. Hozzon létre egy logikai alkalmazást az Event hub-ról való olvasáshoz, és küldjön eseményeket Log Analytics munkaterületre.

## <a name="requirements"></a>Követelmények
A forgatókönyvben használt Azure-erőforrások az alábbi követelményekkel rendelkeznek.

- Az eseményközpont névterének nem muszáj ugyanabban az előfizetésben lennie, mint a naplókat kibocsátó előfizetésnek. A beállítást konfiguráló felhasználónak megfelelő hozzáférési engedélyekkel kell rendelkeznie mindkét előfizetéshez. Ha több előfizetéssel is rendelkezik egy Azure Active Directoryban, az összes előfizetés tevékenységnaplóját elküldheti ugyanabba az eseményközpontba.
- A logikai alkalmazás más előfizetésben is lehet, mint az eseményközpont, és nem kell ugyanabban az Azure Active Directoryban lennie. A logikai alkalmazás az eseményközpont megosztott elérési kulcsával olvas az eseményközpontból.
- A Log Analytics-munkaterületnek nem kell ugyanabban az előfizetésben és Azure Active Directoryban lennie, mint a logikai alkalmazásnak, de az egyszerűség kedvéért javasoljuk, hogy azonos előfizetésben legyenek. A logikai alkalmazás a Log Analytics munkaterület AZONOSÍTÓjának és kulcsának használatával küldi el a munkaterületet.



## <a name="step-1---create-an-event-hub"></a>1\. lépés – Eseményközpont létrehozása

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../../event-hubs/event-hubs-create.md) to create your event hub. -->

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **Event Hubs** elemet.

   ![Piactér – új eseményközpont](media/collect-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. A **Névtér létrehozása** területen adjon meg egy új névteret, vagy válasszon ki egy már meglévőt. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.

   ![az eseményközpont létrehozása párbeszédpanel képe](media/collect-activity-logs-subscriptions/create-event-hub1.png)

4. Válasszon ki egy tarifacsomagot (Basic vagy Standard), egy Azure-előfizetést, egy erőforráscsoportot és az új erőforrás helyét.  A névtér létrehozásához kattintson a **Létrehozás** parancsra. Lehet, hogy néhány percet várnia kell, amíg a rendszer teljes mértékben kiépíti az erőforrásokat.
6. A listában kattintson az imént létrehozott névtérre.
7. Válassza a **Megosztott elérési szabályzatok** lehetőséget, majd kattintson a **RootManageSharedAccessKey** elemre.

   ![az eseményközpont megosztott elérési szabályzatainak képe](media/collect-activity-logs-subscriptions/create-event-hub7.png)
   
8. Kattintson a másolás gombra, hogy a **RootManageSharedAccessKey** kapcsolati sztringet a vágólapra másolja. 

   ![az eseményközpont megosztott elérési kulcsának képe](media/collect-activity-logs-subscriptions/create-event-hub8.png)

9. Őrizze meg az eseményközpont nevét és az eseményközpont elsődleges vagy másodlagos kapcsolati sztringjét egy ideiglenes helyen, például a Jegyzettömbben. A logikai alkalmazásnak szüksége van ezekre az értékekre.  Az eseményközpont kapcsolati sztringjének használhatja a **RootManageSharedAccessKey** kapcsolati karakterláncot, vagy létrehozhat egy másikat.  A használt kapcsolati sztringnek az `Endpoint=sb://` elemmel kell kezdődnie, és egy **Kezelés** hozzáférési szabályzatot tartalmazó szabályzathoz kell tartoznia.


## <a name="step-2---export-activity-logs-to-event-hub"></a>2\. lépés – Tevékenységnaplók exportálása az eseményközpontba

A tevékenységnapló streamelésének engedélyezéséhez válasszon ki egy eseményközpont-névteret, valamint egy megosztott elérési szabályzatot a névtér számára. Az első új tevékenységnapló-esemény bekövetkeztekor létre fog jönni egy eseményközpont abban a névtérben. 

Olyan eseményközpont-névteret is használhat, amely más előfizetésben van, mint a naplókat kibocsátó előfizetés, az előfizetéseknek viszont ugyanabban az Azure Active Directoryban kell lenniük. A beállítást konfiguráló felhasználónak a megfelelő szerepköralapú hozzáférés-vezérléssel kell rendelkeznie ahhoz, hogy mindkét előfizetéshez hozzáférhessen. 

1. Az Azure Portalon válassza a **Figyelés** > **Tevékenységnapló** elemet.
3. Kattintson a lap tetején található **Exportálás** gombra.

   ![az Azure Monitor képe navigáció közben](media/collect-activity-logs-subscriptions/activity-log-blade.png)

4. Válassza ki azt az **előfizetést**, amelyből exportálni szeretne, majd kattintson **Az összes kijelölése** elemre a **Régiók** legördülő menüben, hogy bármelyik régió erőforrásaihoz választhasson eseményeket. Jelölje be az **Exportálás eseményközpontba** jelölőnégyzetet.
7. Kattintson a **Szolgáltatásbusz-névtér** lehetőségre, majd válassza ki az eseményközpontot tartalmazó **előfizetést**, az **eseményközpont-névteret** és az **eseményközpont szabályzatának nevét**.

    ![az exportálás az eseményközpontba oldal képe](media/collect-activity-logs-subscriptions/export-activity-log2.png)

11. A beállítások mentéséhez kattintson az **OK**, majd a **Mentés** gombra. A rendszer azonnal alkalmazni fogja a beállításokat az előfizetésére.

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>3\. lépés – Logikai alkalmazás létrehozása

Miután a tevékenység naplókat ír az Event hubhoz, létrehoz egy logikai alkalmazást, amely összegyűjti a naplókat az Event hub-ból, és beírja azokat a Log Analytics munkaterületre.

A logikai alkalmazás a következőket tartalmazza:
- Egy [Eseményközpont-összekötő](https://docs.microsoft.com/connectors/eventhubs/) eseményindítót az eseményközpontból való olvasáshoz.
- Egy [JSON elemzése műveletet](../../logic-apps/logic-apps-content-type.md) a JSON-események kinyeréséhez.
- Egy [Összeállítás műveletet](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) a JSON objektummá való átalakításához.
- Az [adatösszekötő log Analytics küldése](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) az adatLog Analytics munkaterületre való közzétételhez.

   ![eseményközpont-eseményindító Logic Appsben való hozzáadásának képe](media/collect-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>A logikai alkalmazás követelményei
A logikai alkalmazás létrehozása előtt győződjön meg arról, hogy rendelkezik az alábbi információkkal az előző lépésekből:
- Eseményközpont neve
- Az eseményközpont kapcsolati sztringje (az elsődleges vagy a másodlagos) az eseményközpont-névtérhez.
- A Log Analytics-munkaterület azonosítója
- A Log Analytics megosztott kulcsa

Az eseményközpont nevének és a kapcsolati sztringjének lekéréséhez kövesse [Az Event Hubs névtérengedélyeinek ellenőrzése és a kapcsolati karakterlánc megkeresése](../../connectors/connectors-create-api-azure-event-hubs.md#permissions-connection-string) című cikk lépéseit.


### <a name="create-a-new-blank-logic-app"></a>Új üres logikai alkalmazás létrehozása

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Vállalati integráció** > **Logikai alkalmazás** elemet.

    ![Piactér – új logikai alkalmazás](media/collect-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. Adja meg a beállításokat az alábbi táblázatban:

    ![Logikai alkalmazás létrehozása](media/collect-activity-logs-subscriptions/create-logic-app.png)

   |Beállítás | Leírás  |
   |:---|:---|
   | Name (Név)           | A logikai alkalmazás egyedi neve. |
   | Előfizetést   | Válassza ki azt az Azure-előfizetést, amely a logikai alkalmazást tartalmazni fogja. |
   | Erőforráscsoport | Válasszon ki egy meglévő Azure-erőforráscsoportot, vagy hozzon létre egy újat a logikai alkalmazás számára. |
   | Hely       | Válassza ki az adatközpont-régiót a logikai alkalmazás üzembe helyezéséhez. |
   | Log Analytics  | Válassza ki, hogy szeretné-e naplózni a logikai alkalmazás egyes futtatásainak állapotát egy Log Analytics munkaterületen.  |

    
3. Kattintson a **Létrehozás** gombra. Amikor megjelenik a **Sikeres üzembe helyezés** értesítés, kattintson az **Erőforrás megnyitása** lehetőségre a logikai alkalmazás megnyitásához.

4. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet. 

A Logic Apps Designerben most az elérhető összekötők és azok eseményindítói jelennek meg, amelyek a logikai alkalmazás indítására használhatók.

<!-- Learn [how to create a logic app](../../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>Eseményközpont-eseményindító hozzáadása

1. A Logic App Designer keresőmezőjébe írja be szűrőként az *event hubs* kifejezést. Válassza ki a következő eseményindítót: **Event Hubs – Amikor események válnak elérhetővé az eseményközpontban**.

   ![eseményközpont-eseményindító Logic Appsben való hozzáadásának képe](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. Amikor a rendszer hitelesítő adatokat kér, csatlakozzon az Event Hubs-névtérhez. Írja be a kapcsolat nevét, majd a kimásolt kapcsolati sztringet.  Kattintson a **Létrehozás** gombra.

   ![eseményközpont-kapcsolat Logic Appsben való hozzáadásának képe](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. A kapcsolat létrehozása után szerkessze az eseményindító beállításait. Első lépésként válassza ki az **insights-operational-logs** elemet az **Eseményközpont neve** legördülő menüből.

   ![Amikor események válnak elérhetővé párbeszédpanel](media/collect-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. Bontsa ki a **Speciális beállítások megjelenítése** elemet, és módosítsa a **Tartalom típusa** beállítást a következőre: *alkalmazás/json*

   ![Eseményközpont konfigurálása párbeszédpanel](media/collect-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>JSON elemzése művelet hozzáadása

Az eseményközpont kimenete JSON-hasznosadatokat tartalmaz, egy rekordokból álló tömbbel. A [JSON-elemzés](../../logic-apps/logic-apps-content-type.md) művelettel csak a rekordok tömbjét lehet kibontani log Analytics munkaterületre történő küldéshez.

1. Kattintson az **Új lépés** > **Művelet hozzáadása** elemre
2. A keresőmezőbe írja be szűrőként a *json elemezése* kifejezést. Válassza ki a következő műveletet: **Adatműveletek – JSON elemzése**.

   ![JSON elemzése művelet hozzáadása a Logic Appsben](media/collect-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. Kattintson a **Tartalom** mezőre, majd válassza a *Törzs* lehetőséget.

4. Másolja és illessze be az alábbi sémát a **Séma** mezőbe.  A séma megegyezik az eseményközpont műveletének kimenetével.  

   ![JSON elemzése párbeszédpanel](media/collect-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> Ha hasznosadat-mintára van szüksége, kattintson a **Futtatás** elemre, és tekintse meg az eseményközpont **Nyers kimenetét**.  Ezt a kimenetet később felhasználhatja a séma létrehozásához a **JSON elemzése** tevékenység **Séma létrehozása hasznosadat-minta használatával** lehetőségével.

### <a name="add-compose-action"></a>Összeállítás műveletet hozzáadása
Az [Összeállítás](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) művelet a JSON-kimenet segítségével létrehoz egy objektumot, amelyet a Log Analytics művelet használni tud.

1. Kattintson az **Új lépés** > **Művelet hozzáadása** elemre
2. Írja be szűrőként az *összeállítás* kifejezést, majd válassza ki az **Adatműveletek – Összeállítás** műveletet.

    ![Összeállítás műveletet hozzáadása](media/collect-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. Kattintson a **Bemenetek** mezőre, és válassza a **JSON elemzése** tevékenység alatti **Törzs** lehetőséget.


### <a name="add-log-analytics-send-data-action"></a>Log Analytics adatküldési művelet hozzáadása
Az [Azure log Analytics adatgyűjtő](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) művelet végrehajtja az objektumot az összeállítási műveletből, és elküldi azt egy log Analytics munkaterületre.

1. Kattintson az **Új lépés** > **Művelet hozzáadása** elemre
2. Írja be szűrőként a *log analytics* kifejezést, majd válassza ki az **Azure Log Analytics Data Collector – Adatküldés** műveletet.

   ![Naplóelemzési adatküldési művelet hozzáadása a Logic Appsben](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. Írja be a kapcsolat nevét, majd illessze be a Log Analytics-munkaterülethez tartozó **munkaterület-azonosítót** és **munkaterületkulcsot**.  Kattintson a **Létrehozás** gombra.

   ![naplóelemzési kapcsolat hozzáadása a Logic Appsben](media/collect-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. Miután létrehozta a kapcsolatot, szerkessze az alábbi táblázatban szereplő beállításokat. 

    ![Az adatküldési művelet konfigurálása](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |Beállítás        | Érték           | Leírás  |
   |---------------|---------------------------|--------------|
   |JSON-kérelem törzse  | Az **Összeállítás** művelet **kimenete** | Lekéri a rekordokat az Összeállítás művelet törzséből. |
   | Egyéni napló neve | AzureActivity | A Log Analytics munkaterületen létrehozandó egyéni napló tábla neve, amely az importált adatmennyiséget tárolni fogja. |
   | Time-generated-field | time | Ne válassza ki a **time** JSON-mezőt, csak írja be a „time” szót. Ha kiválasztja a JSON-mezőt, a tervező egy **For each** iterációba helyezi az *Adatküldés* műveletet, amely nem felel meg a szándékainak. |




10. Kattintson a **Mentés** gombra a logikai alkalmazás módosításainak mentéséhez.

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>4\. lépés – A logikai alkalmazás tesztelése és hibaelhárítása
Ha elkészült a munkafolyamat, a tervezőben való teszteléssel ellenőrizheti, hogy hiba nélkül működik-e.

A Logic App Designerben kattintson a **Futtatás** gombra a logikai alkalmazás teszteléséhez. A logikai alkalmazásban minden lépésnél egy állapotikon látható, amelyeknél a zöld körön belüli fehér pipa jelzi a sikert.

   ![A logikai alkalmazás tesztelése](media/collect-activity-logs-subscriptions/test-logic-app.png)

Ha meg szeretné tekinteni az egyes lépések részletes adatait, kattintson a lépés nevére annak kibontásához. Kattintson a **Nyers bemenetek megjelenítése** és a **Nyers kimenetek megjelenítése** lehetőségekre a lépéseknél fogadott és elküldött adatok további információinak megtekintéséhez.

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>5\. lépés – Azure-tevékenységnapló megtekintése a Log Analyticsben
Az utolsó lépés a Log Analytics-munkaterület ellenőrzése, hogy meggyőződjön arról, hogy az adatok gyűjtése a várakozásoknak megfelelően történik.

1. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. Válassza ki a munkaterületet a Log Analytics-munkaterületek listájából.
3.  Válassza a **Naplóbeli keresés** csempét, és a Naplóbeli keresés panel lekérdezési mezőjében adja meg a `AzureActivity_CL` típust, majd nyomja le az Enter billentyűt, vagy kattintson a lekérdezési mezőtől jobbra található Keresés gombra. Ha az egyéni napló neve nem *AzureActivity*, írja be a választott nevet és fűzze hozzá a `_CL` tagot.

>[!NOTE]
> Amikor a rendszer első alkalommal új egyéni naplót kap a Log Analytics munkaterületre, akkor akár egy óráig is eltarthat, amíg az egyéni napló kereshetővé válik.

>[!NOTE]
> A rendszer egy egyéni táblába írja a tevékenységnaplókat, és azok nem jelennek meg a [tevékenységnapló-megoldásban](./activity-log-collect.md).


![A logikai alkalmazás tesztelése](media/collect-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben létrehozott egy logikai alkalmazást, amely az Azure-beli tevékenységek naplóit olvassa be az Event hub-ból, és elküldi őket a Log Analytics munkaterületre elemzés céljából. Ha többet szeretne megtudni a munkaterület adatainak megjelenítéséről, beleértve az irányítópultok létrehozását, tekintse át az információk megjelenítésére szolgáló oktatóanyagot.

> [!div class="nextstepaction"]
> [Naplókeresési adatok vizualizációja – oktatóanyag](./../../azure-monitor/learn/tutorial-logs-dashboards.md)
