---
title: Beépített eseményindítók és műveletek Azure Logic Apps
description: Beépített eseményindítók és műveletek használatával olyan automatizált munkafolyamatokat hozhat létre, amelyek alkalmazásokat, adatokat, szolgáltatásokat és rendszereket integrálnak a munkafolyamatok vezérléséhez és az adatok kezeléséhez a Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: 045d7391c9c3c2870efddc0aed4ae7590db938d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796927"
---
# <a name="built-in-triggers-and-actions-for-logic-apps"></a>Beépített eseményindítók és műveletek Logic Apps


[A beépített eseményindítók](apis-list.md) és műveletek segítségével szabályozhatja a munkafolyamat ütemezését és szerkezetét, [](#control-workflow)saját kódot futtathat, [](#run-code-from-workflows)adatokat kezelhet vagy kezelhet, valamint egyéb feladatokat is el tud végrehajtani a munkafolyamatokban. [](#manage-or-manipulate-data) A felügyelt [összekötőktől](managed.md)eltérve számos beépített művelet nincs egy adott szolgáltatáshoz, rendszerhez vagy protokollhoz kötve. Az Ismétlődés eseményindítóval például szinte bármilyen munkafolyamatot elindíthat ütemezés szerint. Azt is kérheti, hogy a munkafolyamat a Kérés eseményindítóval várjon, amíg meg nem hívható. Az összes beépített művelet natív módon fut az Logic Apps szolgáltatásban, és a legtöbb esetben nincs szükség kapcsolat létrehozására azok használata előtt. 

Logic Apps beépített műveleteket is biztosít kisebb számú szolgáltatáshoz, rendszerhez és protokollhoz, például Azure Service Bus, Azure Functions, SQL, AS2 stb. A szám és a tartomány attól függően változik, hogy több-bérlős logikai alkalmazást vagy egybérlős logikai alkalmazást hoz létre. Néhány esetben egy beépített és egy felügyelt összekötő-verzió is elérhető. A legtöbb esetben a beépített verzió jobb teljesítményt, képességeket, díjszabást stb. biztosít. Ha például [B2B-üzeneteket](../logic-apps/logic-apps-enterprise-integration-as2.md)cserél az AS2 protokollal, válassza ki a beépített verziót, kivéve, ha nyomkövetési képességekre van szüksége, amelyek csak az (elavult) felügyelt összekötő verziójában érhetők el.

Az alábbi lista csak néhány olyan feladatot ismertet, amely a beépített eseményindítók és műveletek esetén [teljesíthető:](#understand-triggers-and-actions)

- Munkafolyamatok futtatása egyéni és speciális ütemezésekkel. Az ütemezéssel kapcsolatos további információkért tekintse át az ismétlődés viselkedését ismertető szakaszt a Logic Apps [áttekintésében.](apis-list.md#recurrence-behavior)
- A munkafolyamat struktúrájának rendszerezése és szabályozása, például hurkok és feltételek használatával.
- Dolgozhat változókat, dátumokat, adatműveleteket, tartalomátalakításokat és kötegelt műveleteket.
- Http-eseményindítók és -műveletek használatával kommunikálhat más végpontokkal.
- Kérések fogadása és megválaszolás.
- Hívja meg a saját függvényeket (Azure Functions), webalkalmazásokat (Azure App Services), API-kat (Azure API Management), egyéb Logic Apps-munkafolyamatokat, amelyek fogadhatnak kéréseket stb.

## <a name="understand-triggers-and-actions"></a>Az eseményindítók és műveletek

Logic Apps a következő beépített eseményindítókat és műveleteket biztosítja:

:::row:::
    :::column:::
        [![Ütemezés ikon a Logic Apps][schedule-icon]][schedule-doc]
        \
        \
        [**Ütemezése**][schedule-doc]
        \
        \
        [**Ismétlődés:**][schedule-recurrence-doc]Munkafolyamat aktiválása a megadott ismétlődés alapján.
        \
        \
        [**Csúszóablak:**][schedule-sliding-window-doc]Olyan munkafolyamatot aktivál, amely folyamatos adattömbökben kezeli az adatokat.
        \
        \
        [**Késleltetés:**][schedule-delay-doc]Szüneteltetheti a munkafolyamatot a megadott időtartamra.
        \
        \
        [**Késleltetés eddig:**][schedule-delay-until-doc]Szüneteltetheti a munkafolyamatot a megadott dátumig és időpontig.
    :::column-end:::
    :::column:::
        [![Batch ikon a Logic Apps][batch-icon]][batch-doc]
        \
        \
        [**Kötegelt**][batch-doc]
        \
        \
        [**Kötegelt üzenetek:**][batch-doc]Elindít egy munkafolyamatot, amely kötegekbe dolgozza fel az üzeneteket.
        \
        \
        [**Üzenetek küldése a kötegbe:**][batch-doc]Olyan meglévő munkafolyamat hívása, amely jelenleg **egy Batch-üzenet eseményindítóval** kezdődik.
    :::column-end:::
    :::column:::
        [![HTTP ikon a Logic Apps][http-icon]][http-doc]
        \
        \
        [**HTTP**][http-doc]
        \
        \
        HTTP- vagy HTTPS-végpont hívása a HTTP-eseményindító vagy művelet használatával. 
        \
        \
        Az alábbi beépített HTTP-eseményindítókat és -műveleteket is használhatja: 
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP + Webhook][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![Kérés ikon][http-request-icon]][http-request-doc]
        \
        \
        [**Kérés**][http-request-doc]
        \
        \
        [**HTTP-kérés esetén:**][http-request-doc]Várja meg egy másik munkafolyamat, alkalmazás vagy szolgáltatás kérését. Ez az eseményindító lehetővé teszi a munkafolyamat hívását anélkül, hogy ütemezetten kellene ellenőrizni vagy lekérdezni.
        \
        \
        [**Válasz:**][http-request-doc]Válasz a HTTP-kérések bekért eseményindítója által fogadott kérésre ugyanabban **a** munkafolyamatban.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure API Management ikon a Logic Apps][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Azure API Management**][azure-api-management-doc]
        \
        \
        Saját eseményindítók és műveletek meghívása az API-kban, amelyek az [Azure API Management.](../api-management/api-management-key-concepts.md) <p><p>**Megjegyzés:** Nem támogatott, ha [használatban van a](../api-management/api-management-features.md)API Management.
    :::column-end:::
    :::column:::
        [![Azure App Services ikon a Logic Apps][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [**Azure App Services**][azure-app-services-doc]
        \
        \
        Hívja meg a következőn létrehozott és Azure App Service alkalmazásokat: [API Apps](../app-service/overview.md)és Web Apps.
        \
        \
        A Swagger használata esetén az ezen alkalmazások által definiált eseményindítók és műveletek úgy jelennek meg, mint bármely más első osztályú eseményindító és művelet a Azure Logic Apps.
    :::column-end:::
    :::column:::
        [![Azure Logic Apps ikon a Logic Apps][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure Logic Apps**][nested-logic-app-doc]
        \
        \
        Más munkafolyamatokat is hívhat, amelyek a Kérés eseményindítóval **kezdődnek, a When a HTTP request is received (HTTP-kérés érkezik) névvel.**
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>Kód futtatása munkafolyamatból

Logic Apps beépített műveleteket biztosít a saját kód futtatásához a munkafolyamatban:

:::row:::
    :::column:::
        [![Azure Functions ikon a Logic Apps][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure Functions**][azure-functions-doc]
        \
        \
        Az [Azure-ban üzemeltetett függvények hívása](../azure-functions/functions-overview.md) saját kódrészletek *(C#* vagy Node.js) futtatásához a munkafolyamatban.
    :::column-end:::
    :::column:::
        [![Beágyazott kód ikon a Logic Apps][inline-code-icon]][inline-code-doc]
        \
        \
        [**Beágyazott kód**][inline-code-doc]
        \
        \
        [**JavaScript-kód végrehajtása:**][inline-code-doc]Saját beágyazott JavaScript-kódrészleteket adhat hozzá és futtathat a munkafolyamaton belül. 
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>Munkafolyamat vezérlése

Logic Apps beépített műveleteket biztosít a munkafolyamatban a műveletek strukturálása és szabályozása érdekében:

:::row:::
    :::column:::
        [![Feltétel művelet ikonja a Logic Apps][condition-icon]][condition-doc]
        \
        \
        [**Feltétel**][condition-doc]
        \
        \
        Kiértékel egy feltételt, és különböző műveleteket futtat attól függően, hogy a feltétel igaz vagy hamis.
    :::column-end:::
    :::column:::
        [![Az egyes műveletikonok a Logic Apps][for-each-icon]][for-each-doc]
        \
        \
        [**Mindegyikhez**][for-each-doc]
        \
        \
        Hajtsa végre ugyanezeket a műveleteket egy tömb minden elemében.
    :::column-end:::
    :::column:::
        [![Hatókör művelet ikonja a Logic Apps][scope-icon]][scope-doc]
        \
        \
        [**név**][scope-doc]
        \
        \
        A műveleteket *hatókörökbe* csoportosítja, amelyek saját állapotukat kapják meg, miután a hatókörben futó műveletek befejeződtek.
    :::column-end:::
    :::column:::
        [![Váltás műveletikon a Logic Apps][switch-icon]][switch-doc]
        \
        \
        [**Kapcsoló**][switch-doc]
        \
        \
        A műveleteket *esetekbe csoportosítja,* amelyekhez egyedi értékek vannak hozzárendelve, kivéve az alapértelmezett esetet. Csak azt az esetet futtassa, amelynek hozzárendelt értéke megegyezik egy kifejezés, objektum vagy jogkivonat eredményével. Ha nem létezik egyezés, futtassa az alapértelmezett esetet.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Lemondhatja a műveletet ikon a Logic Apps][terminate-icon]][terminate-doc]
        \
        \
        [**Megszünteti**][terminate-doc]
        \
        \
        Állítson le egy aktívan futó logikaialkalmazás-munkafolyamatot. 
    :::column-end:::
    :::column:::
        [![Until művelet ikon a Logic Apps][until-icon]][until-doc]
        \
        \
        [**Amíg**][until-doc]
        \
        \
        Ismételje meg a műveleteket, amíg a megadott feltétel nem igaz, vagy valamilyen állapot meg nem változott.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>Adatok kezelése vagy kezelése

Logic Apps beépített műveleteket biztosít az adatkimenetekkel és azok formátumával való munkavégzéshez:

:::row:::
    :::column:::
        [![Adatműveleti műveletek ikonja a Logic Apps][data-operations-icon]][data-operations-doc]
        \
        \
        [**Adatműveletek**][data-operations-doc]
        \
        \
        Műveletek végrehajtása adatokkal. 
        \
        \
        **Compose:** Egyetlen kimenetet hozhat létre több bemenetből, különböző típusúakból. 
        \
        \
        **CSV-tábla létrehozása:** Hozzon létre egy vesszővel elválasztott értékkel (CSV) elválasztott táblát egy JSON-objektumokat tartalmazó tömbből. 
        \
        \
        **HTML-táblázat létrehozása:** HTML-táblázat létrehozása JSON-objektumokat tartalmazó tömbből. 
        \
        \
        **Tömb szűrése:** Tömb létrehozása egy másik tömbben lévő, a feltételeknek megfelelő elemekből. 
        \
        \
        **Join**(Illesztés): Hozzon létre egy sztringet egy tömb összes elemének alapján, és válassza el ezeket az elemeket a megadott elválasztó karaktertől. 
        \
        \
        **JSON-adatok** megtekintése: Felhasználóbarát jogkivonatokat hozhat létre a tulajdonságokból és azok JSON-tartalomban található értékeiből, hogy ezeket a tulajdonságokat használni tudja a munkafolyamatban. 
        \
        \
        **Select:** Create an array with JSON objects by transforming items or values in another array and mapping those items to specified properties.
    :::column-end:::
    :::column:::
        ![Dátum és idő művelet ikon a Logic Apps][date-time-icon]
        \
        \
        **Dátum és idő**
        \
        \
        Műveletek végrehajtása időbélyegekkel.
        \
        \
        **Hozzáadás az időhez:** Adja hozzá a megadott számú egységet egy időbélyeghez. 
        \
        \
        **Időzóna konvertálása:** Időbélyegző átalakítása a forrás időzónából a cél időzónába. 
        \
        \
        **Aktuális idő:** Az aktuális időbélyeget adja vissza sztringként. 
        \
        \
        **Jövőbeli időpont lekérte:** Visszaadja az aktuális időbélyeget és a megadott időegységeket. 
        \
        \
        **Korábbi idő beázása:** Az aktuális időbélyegzőt adja vissza a megadott időegységekkel csökkentve. 
        \
        \
        **Kivonás az időből:** Időegységek kivonása időbélyegből..
    :::column-end:::
    :::column:::
        [![A változók műveletikonja a Logic Apps][variables-icon]][variables-doc]
        \
        \
        [**Változók**][variables-doc]
        \
        \
        Műveletek végrehajtása változók használatával.
        \
        \
        **Hozzáfűzés tömbváltozóhoz:** Beszúr egy értéket egy változó által tárolt tömb utolsó elemeként. 
        \
        \
        **Hozzáfűzés sztringváltozóhoz:** Beszúr egy értéket egy változó által tárolt sztring utolsó karaktereként.
        \
        \
        **Csökkentés változó:** Változó csökkentése állandó értékkel.
        \
        \
        **Növekményes** változó: Növelje a változót egy állandó értékkel. 
        \
        \
        **Változó inicializálása:** Hozzon létre egy változót, és deklarálja annak adattípusát és kezdeti értékét. 
        \
        \
        **Változó beállítása:** Rendeljen hozzá egy másik értéket egy meglévő változóhoz. 
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni API-k létrehozása, amelyek a Logic Apps](/logic-apps/logic-apps-create-api-app)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png


<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Azure-beli API Management-szolgáltatáspéldány létrehozása az API-k kezeléséhez és közzétételéhez"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Logikai alkalmazások integrálása App Service API Apps"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Logikai alkalmazások integrálása Azure Functions-függvényekkel"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Üzenetek feldolgozása csoportokban vagy kötegként"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Feltétel kiértékelése és különböző műveletek futtatása attól függően, hogy a feltétel igaz vagy hamis"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Azonos műveletek végrehajtása egy tömb minden elemében"
[http-doc]: ./connectors-native-http.md "HTTP- vagy HTTPS-végpontok hívása a logikai alkalmazásokból"
[http-request-doc]: ./connectors-native-reqres.md "HTTP-kérések fogadása a logikai alkalmazásokban"
[http-response-doc]: ./connectors-native-reqres.md "Válasz a logikai alkalmazásokTÓL származó HTTP-kérésekre"
[http-swagger-doc]: ./connectors-native-http-swagger.md "REST-végpontok hívása a logikai alkalmazásokból"
[http-webhook-doc]: ./connectors-native-webhook.md "Adott események várakozása HTTP- vagy HTTPS-végpontról"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "JavaScript-kódrészletek hozzáadása és futtatása a logikai alkalmazásokból"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Logikai alkalmazások integrálása beágyazott munkafolyamatokkal"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Tömbök kiválasztása és szűrése a Lekérdezés művelettel"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Logikai alkalmazások futtatása ütemezés alapján"
[schedule-delay-doc]: ./connectors-native-delay.md "A következő művelet futtatásának késleltetése"
[schedule-delay-until-doc]: ./connectors-native-delay.md "A következő művelet futtatásának késleltetése"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Logikai alkalmazások futtatása ismétlődő ütemezés szerint"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Olyan logikai alkalmazások futtatása, amelyek összefüggő adattömbökben kezelik az adatokat"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Csoportokba rendezheti a műveleteket, amelyek saját állapotukat kapják meg a csoportban végzett műveletek futásának befejezése után"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "A műveleteket esetekbe rendezheti, amelyekhez egyedi értékek vannak hozzárendelve. Csak azt az esetet futtassa, amelynek értéke megegyezik egy kifejezés, objektum vagy jogkivonat eredményével. Ha nem létezik egyezés, futtassa az alapértelmezett esetet"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "A logikai alkalmazás aktívan futó munkafolyamatának leállítása vagy megszakítása"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Ismételje meg a műveleteket, amíg a megadott feltétel nem teljesül, vagy valamilyen állapot meg nem változott"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Adatműveletek végrehajtása, például tömbök szűrése vagy CSV- és HTML-táblázatok létrehozása"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Műveletek végrehajtása változók használatával, például inicializálás, beállítás, növelés, növelés, növelés és hozzáfűzés sztringhez vagy tömbváltozóhoz"
