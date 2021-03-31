---
title: Webes API-k létrehozása & REST API-kkal Azure Logic Apps
description: Webes API-k & REST API-k segítségével az API-k, szolgáltatások vagy rendszerek meghívásához a rendszer-integrációhoz Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/26/2017
ms.openlocfilehash: 3fe98160cc10eb3607b8309a9a263d63380dcfb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89073216"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Egyéni API-k létrehozása, melyeket hívhat Azure Logic Apps

Habár Azure Logic Apps [több száz összekötőt](../connectors/apis-list.md) kínál, amelyeket a Logic app-munkafolyamatokban használhat, előfordulhat, hogy olyan API-kat, rendszereket és szolgáltatásokat szeretne hívni, amelyek nem érhetők el összekötőként. Létrehozhat saját API-kat, amelyek a Logic Appsben használható műveleteket és eseményindítókat biztosítanak. Az alábbi egyéb okok miatt érdemes lehet saját API-kat létrehozni a Logic apps-munkafolyamatokból:

* Terjessze ki a jelenlegi rendszerintegrációs és adatintegrációs munkafolyamatokat.
* Segítheti az ügyfeleket a szolgáltatásban a szakmai vagy személyes feladatok kezelésében.
* Bővítse a szolgáltatás elérhetőségét, a felderíthetőség és a használatot.

Az összekötők alapvetően olyan webes API-k, amelyek REST-t használnak a csatlakoztatott interfészekhez, a dokumentációhoz a [hencegő metaadatok formátumát](https://swagger.io/specification/) , valamint a JSON-t adatcsere-formátumként. Mivel az összekötők olyan REST API-k, amelyek HTTP-végpontokon keresztül kommunikálnak, bármilyen nyelvet, például .NET, Java, Python vagy Node.js használhat az összekötők létrehozásához. Az API-kat [Azure app Service](../app-service/overview.md), egy szolgáltatásként nyújtott platformon is üzemeltetheti, amely az API-k egyik legjobb, legegyszerűbb és leginkább méretezhető módját kínálja. 

Ahhoz, hogy egyéni API-k működjenek a logikai alkalmazásokkal, az API olyan [*műveleteket*](./logic-apps-overview.md#logic-app-concepts) is biztosít, amelyek adott feladatokat hajtanak végre a Logic apps-munkafolyamatokban. Az API olyan [*eseményindítóként*](./logic-apps-overview.md#logic-app-concepts) is működhet, amely elindítja a Logic app-munkafolyamatot, amikor új vagy egy esemény megfelel egy megadott feltételnek. Ez a témakör azokat a gyakori mintákat ismerteti, amelyekkel az API-k által biztosított viselkedés alapján műveleteket és eseményindítókat hozhat létre az API-ban.

Az API-kat üzemeltetheti [Azure app Serviceon](../app-service/overview.md), egy szolgáltatásként nyújtott platformon (Pásti), amely kiválóan méretezhető, egyszerű API-üzemeltetést biztosít.

> [!TIP] 
> Habár az API-kat webalkalmazásként is üzembe helyezheti, érdemes lehet API-alkalmazásokként üzembe helyeznie az API-kat, amely megkönnyíti a feladatok elvégzését a felhőben és a helyszínen lévő API-k létrehozásakor, üzemeltetése és felhasználása során. Nem kell módosítania az API-kat, csak telepítse a kódot egy API-alkalmazásba. Például megtudhatja, hogyan hozhat létre az alábbi nyelvekkel létrehozott API-alkalmazásokat: 
> 
> * [ASP.net](../app-service/quickstart-dotnetcore.md). 
> * [Java](../app-service/quickstart-java.md)
> * [Node.js](../app-service/quickstart-nodejs.md)
> * [PHP](../app-service/quickstart-php.md)
> * [Python](../app-service/quickstart-python.md)
> * [Ruby](../app-service/quickstart-ruby.md)
>
> A Logic apps-hoz készült API-alkalmazások mintáinak megtekintéséhez látogasson el a [Azure Logic apps GitHub-tárházba](https://github.com/logicappsio).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Miben különböznek az egyéni API-k az egyéni összekötők?

Az egyéni API-k és az [Egyéni összekötők](../logic-apps/custom-connector-overview.md) olyan webes API-k, amelyek Rest-t használnak a csatlakoztatott felületek számára, a dokumentációhoz tartozó [hencegés metaadatainak formátuma](https://swagger.io/specification/) , valamint a JSON adatcsere formátuma Mivel ezek az API-k és összekötők olyan REST API-k, amelyek HTTP-végpontokon keresztül kommunikálnak, az egyéni API-k és összekötők létrehozásához bármilyen nyelvet használhat, például .NET, Java, Python vagy Node.js.

Az egyéni API-k segítségével olyan API-kat hívhat meg, amelyek nem összekötők, és olyan végpontokat biztosítanak, amelyek HTTP + hencegés, Azure API Management vagy App Services használatával hívhatók meg. Az egyéni összekötők úgy működnek, mint az egyéni API-k, de ezek az attribútumok is a következők:

* Regisztrálva van Logic Apps-összekötő erőforrásként az Azure-ban.
* Ikonokat jelenít meg a Microsoft által felügyelt összekötők mellett a Logic Apps Designerben.
* Csak a szerzők és a logikai alkalmazások azon felhasználói számára érhető el, akik azonos Azure Active Directory Bérlővel és Azure-előfizetéssel rendelkeznek abban a régióban, ahol a logikai alkalmazásokat telepítették.

A Microsoft minősítéshez regisztrált összekötőket is kijelölheti. Ezzel a folyamattal ellenőrizhető, hogy a regisztrált összekötők megfelelnek-e a nyilvános használat feltételeinek, és elérhetővé teszik ezeket az összekötőket a Power automatizáló és a Microsoft Power apps felhasználói számára.

További információ az egyéni összekötők használatáról: 

* [Egyéni összekötők áttekintése](../logic-apps/custom-connector-overview.md)
* [Egyéni összekötők létrehozása webes API-kkal](/connectors/custom-connectors/create-web-api-connector)
* [Egyéni összekötők regisztrálása Azure Logic Apps](/connectors/custom-connectors/)

## <a name="helpful-tools"></a>Hasznos eszközök

Az egyéni API a Logic apps szolgáltatásban a legjobban működik, ha az API-nak van egy [hencegő dokumentuma](https://swagger.io/specification/) is, amely LEÍRJA az API műveleteit és paramétereit.
Számos könyvtár, például a [swashbuckle](https://github.com/domaindrivendev/Swashbuckle), automatikusan létrehozhatja a hencegő fájlt. Ha meg szeretné adni a hencegő fájlt a megjelenítendő nevek, a tulajdonságok típusai és így tovább, akkor a [TRex](https://github.com/nihaue/TRex) is használhatja, hogy a hencegő fájl jól működjön a Logic apps szolgáltatással.

<a name="actions"></a>

## <a name="action-patterns"></a>Műveleti minták

Ahhoz, hogy a Logic apps feladatokat végezzen, az egyéni API-nak [*műveleteket*](./logic-apps-overview.md#logic-app-concepts)kell megadnia. Az API minden művelete egy műveletre van leképezve. Az alapszintű művelet egy olyan vezérlő, amely fogadja a HTTP-kérelmeket, és HTTP-válaszokat ad vissza. Így például egy logikai alkalmazás HTTP-kérelmet küld a webalkalmazásnak vagy az API-alkalmazásnak. Az alkalmazás ezután egy HTTP-választ ad vissza, a logikai alkalmazás által feldolgozható tartalommal együtt.

Szabványos művelet esetén HTTP-kérési módszert írhat az API-ban, és leírhatja ezt a metódust egy hencegő fájlban. Ezután meghívhatja az API-t közvetlenül egy [http-művelet](../connectors/connectors-native-http.md) vagy egy [http + hencegés](../connectors/connectors-native-http-swagger.md) művelet használatával. Alapértelmezés szerint a válaszokat vissza kell adni a [kérelem időtúllépési korlátján](./logic-apps-limits-and-config.md)belül. 

![Szabványos műveleti minta](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Ahhoz, hogy egy logikai alkalmazás várjon, amíg az API befejezi a már futó feladatokat, az API követheti az [aszinkron lekérdezési mintát](#async-pattern) vagy a jelen témakörben ismertetett [aszinkron webhook-mintát](#webhook-actions) . Az olyan analógia esetében, amely segít a minták különböző viselkedésének megjelenítésében, Képzelje el, hogy az egyéni süteményeket egy pékségből rendeli el. A lekérdezési minta tükrözi azt a viselkedést, amelyben 20 percenként meghívja a pékséget, hogy meggyőződjön arról, hogy a torta készen áll-e. A webhook-minta tükrözi azt a viselkedést, amelyben a pékség megkéri a telefonszámát, hogy meghívja Önt, amikor elkészült a torta.

Példákért látogasson el a [Logic apps GitHub-tárházba](https://github.com/logicappsio). Emellett további információ a [műveletek használati méréséről](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Hosszan futó feladatok végrehajtása a lekérdezési műveleti mintával

Ahhoz, hogy az API olyan feladatokat végezzen el, amelyek a [kérelem időtúllépési korlátján](./logic-apps-limits-and-config.md)túl futnak, használhatja az aszinkron lekérdezési mintát. Ez a minta rendelkezik az API-val egy különálló szálon, de aktív kapcsolódást tart fenn a Logic Apps motorral. Így a logikai alkalmazás nem időtúllépést jelent, vagy folytathatja a munkafolyamat következő lépését, mielőtt az API befejezi a munkát.

Az általános minta:

1. Győződjön meg arról, hogy a motor tudja, hogy az API elfogadta a kérést, és megkezdte a munkát.
2. Ha a motor a feladat állapotára vonatkozó további kérelmeket tesz elérhetővé, akkor a motornak tudnia kell, hogy az API befejezi-e a feladatot.
3. A megfelelő adatokat visszaküldi a motornak, hogy a logikai alkalmazás munkafolyamata továbbra is folytatódjon.

<a name="bakery-polling-action"></a> Most alkalmazza az előző pékség analógjét a lekérdezési mintára, és Képzelje el, hogy meghívja a pékséget, és rendeljen hozzá egy egyéni tortát a szállításhoz. A Cake elkészítésének folyamata időt vesz igénybe, és nem szeretné megvárni a telefont, miközben a pékség a tortán dolgozik. A pékség megerősíti a rendelését, és 20 percenként hívja meg a Cake állapotát. 20 perc elteltével meghívja a pékséget, de arról tájékoztat, hogy a Cake nem lett elvégezve, és egy másik 20 percen belül meg kell hívnia. Ez a folyamat addig folytatódik, amíg meg nem hívja a hívást, és a pékség azt jelzi, hogy a megrendelés készen áll, és elküldi a tortát. 

Tehát a lekérdezési mintát vissza kell képeznie. A pékség az egyéni API-t jelöli, a Cake-ügyfél pedig a Logic Apps motort képviseli. Amikor a motor meghívja az API-t egy kéréssel, az API megerősíti a kérést, és az időintervallummal válaszol, amikor a motor ellenőrzi a feladatok állapotát. A motor továbbra is ellenőrzi a feladatok állapotát, amíg az API nem válaszol a feladatokra, és visszaadja az adatait a logikai alkalmazásnak, amely ezután folytatja a munkafolyamatot. 

![Lekérdezési művelet mintája](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Az alábbi lépésekben ismertetjük az API által követendő lépéseket az API szemszögéből:

1. Ha az API HTTP-kérést kap a munka megkezdéséhez, azonnal küldjön vissza egy HTTP `202 ACCEPTED` -választ a `location` lépés későbbi részében ismertetett fejléccel. Ez a válasz lehetővé teszi, hogy a Logic Apps motor tudja, hogy az API megkapta a kérést, elfogadta a kérelem hasznos adatait (adatbevitel), és most dolgozza fel a feldolgozást. 
   
   A `202 ACCEPTED` válasznak tartalmaznia kell a következő fejléceket:
   
   * *Kötelező*: egy `location` fejléc, amely megadja az URL-cím abszolút elérési útját, ahol a Logic apps motor megtekintheti az API-feladatok állapotát

   * Nem *kötelező*: `retry-after` az a fejléc, amely meghatározza, hogy a motor hány másodpercig várjon a `location` feladatok állapotának URL-címének ellenőrzése előtt. 

     Alapértelmezés szerint a motor 20 másodpercenként ellenőrzi. Eltérő intervallum megadásához adja meg a `retry-after` fejlécet és a másodpercek számát a következő lekérdezésig.

2. A megadott idő elteltével a Logic Apps motor lekérdezi az `location` URL-címet a feladatok állapotának vizsgálatához. Az API-nak ezeket az ellenőrzéseket kell végrehajtania, és vissza kell juttatnia a válaszokat:
   
   * Ha a feladatot elvégezte, adja vissza a HTTP `200 OK` -választ, valamint a válasz adattartalmát (a következő lépés bemenete).

   * Ha a feladatok feldolgozása még folyamatban van, egy másik HTTP `202 ACCEPTED` -választ ad vissza, de az eredeti választal megegyező fejlécekkel.

Ha az API ezt a mintát követi, semmit nem kell tennie a logikai alkalmazás munkafolyamatának definíciójában a feladatok állapotának ellenőrzésének folytatásához. Ha a motor HTTP- `202 ACCEPTED` választ kap, és érvényes `location` fejlécet kap, a motor tiszteletben tartja az aszinkron mintát, és addig ellenőrzi a `location` fejlécet, amíg az API nem 202-es választ ad vissza.

> [!TIP]
> Példa aszinkron minta esetén tekintse át ezt az [aszinkron vezérlő-választ a githubon](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Hosszan futó feladatok végrehajtása a webhook műveleti mintázatával

Alternatív megoldásként használhatja a webhook mintát a hosszan futó feladatokhoz és az aszinkron feldolgozáshoz. Ennek a mintának a logikai alkalmazás szüneteltetése, és várnia kell a "visszahívás" lehetőséget az API-ról a munkafolyamat folytatásához a feldolgozás befejezéséhez. Ez a visszahívás egy olyan HTTP-bejegyzés, amely egy adott esemény bekövetkezésekor küld üzenetet egy URL-címre. 

<a name="bakery-webhook-action"></a> Most alkalmazza az előző sütőipari-típust a webhook-mintára, és Képzelje el, hogy meghívja a pékséget, és rendeljen hozzá egy egyéni tortát a szállításhoz. A Cake elkészítésének folyamata időt vesz igénybe, és nem szeretné megvárni a telefont, miközben a pékség a tortán dolgozik. A pékség megerősíti a rendelését, de ezúttal megadja a telefonszámát, hogy meghívja Önt, amikor elkészült a torta. Ezúttal a pékség azt jelzi, hogy mikor áll készen a megrendelés, és elküldi a tortáját.

Ha ezt a webhook-mintázatot visszaképezi, a pékség az egyéni API-t jelöli, a Cake-ügyfél pedig a Logic Apps motort jelképezi. A motor meghívja az API-t egy kéréssel, és tartalmazza a "visszahívás" URL-címet.
A feladatok elvégzése után az API az URL-cím használatával értesíti a motort, és visszaadja az adatait a logikai alkalmazásnak, amely ezután folytatja a munkafolyamatot. 

Ehhez a mintához állítson be két végpontot a vezérlőn: `subscribe` és `unsubscribe`

*  `subscribe` végpont: Ha a végrehajtás eléri az API műveletét a munkafolyamatban, a Logic Apps motor meghívja a `subscribe` végpontot. Ez a lépés azt eredményezi, hogy a logikai alkalmazás létrehoz egy visszahívási URL-címet, amelyet az API tárol, majd megvárja, amíg az API visszahívást végez a munka befejezésekor. Az API ezután visszahívja a HTTP-BEJEGYZÉST az URL-címre, és továbbítja a visszaadott tartalmat és fejléceket a logikai alkalmazás bemenetének.

* `unsubscribe` végpont: Ha a logikai alkalmazás futása meg lett szakítva, a Logic Apps motor meghívja a `unsubscribe` végpontot. Az API ezután törölheti a visszahívási URL-címet, és szükség szerint leállíthatja a folyamatokat.

![Webhook műveleti minta](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

A Logic app Designer jelenleg nem támogatja a webhook-végpontok felfedését a hencegő használatával. Tehát ehhez a mintához hozzá kell adnia egy [ **webhook** -műveletet](../connectors/connectors-native-webhook.md) , és meg kell adnia a kérelem URL-címét, fejléceit és törzsét. Lásd még: [munkafolyamat-műveletek és eseményindítók](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Példa webhook-minta esetén tekintse át ezt a [webhook trigger-mintát a githubon](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

Íme néhány további tipp és Megjegyzés:

* A visszahívási URL-cím átadásához `@listCallbackUrl()` szükség szerint használhatja a munkafolyamat-függvényt az előző mezők bármelyikén.

* Ha a logikai alkalmazás és az előfizetett szolgáltatás tulajdonosa is van, nem kell meghívnia a `unsubscribe` végpontot a visszahívási URL-cím meghívása után. Ellenkező esetben a Logic Apps futtatókörnyezetnek meg kell hívnia a `unsubscribe` végpontot arra, hogy jelezze, ne kelljen több hívást várnia, és hogy az erőforrást a kiszolgáló oldalán is meg lehessen tisztítani.

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Trigger mintázatai

Az egyéni API olyan [*eseményindítóként*](./logic-apps-overview.md#logic-app-concepts) működhet, amely egy logikai alkalmazást indít el, ha új vagy egy esemény megfelel egy adott feltételnek. Ez az aktiválás vagy a szolgáltatás végpontján lévő új adatértékek vagy események esetében lehet rendszeresen, vagy megvárni és figyelni. Ha az új vagy az esemény megfelel a megadott feltételnek, az eseményindító elindítja és elindítja a logikai alkalmazást, amely figyeli az eseményindítót. A Logic apps ily módon történő elindításához az API a [*lekérdezési triggert*](#polling-triggers) vagy a [*webhook trigger*](#webhook-triggers) mintázatát követheti. Ezek a minták a [lekérdezési műveletekhez](#async-pattern) és a [webhook-műveletekhez](#webhook-actions)hasonlók. Emellett további információ a [triggerek használati méréséről](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Új adatértékek vagy események keresése rendszeresen a lekérdezési trigger mintázatával

A *lekérdezési trigger* az ebben a témakörben korábban ismertetett [lekérdezési művelethez](#async-pattern) hasonlóan működik. A Logic Apps motor rendszeres időközönként meghívja és ellenőrzi az indító végpontot új adatként vagy eseményekhez. Ha a motor új adatait vagy a megadott feltételnek megfelelő eseményt talál, akkor az eseményindító tüzet vált. Ezután a motor létrehoz egy logikai alkalmazás-példányt, amely bemenetként dolgozza fel az adatokat. 

![Lekérdezési trigger mintája](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Minden lekérdezési kérelem művelet-végrehajtásnak számít, még akkor is, ha nem jön létre logikai alkalmazás példánya. Ha nem szeretné, hogy ugyanazokat az adatfeldolgozást többször is feldolgozza, az triggernek törölnie kell a logikai alkalmazásba már olvasott és továbbított adatait.

Az alábbi, az API perspektívájában ismertetett lekérdezési triggerek konkrét lépései:

| Új adathalmazt vagy eseményt talált?  | API-válasz | 
| ------------------------- | ------------ |
| Találat | HTTP `200 OK` -állapot visszaadása a válasz hasznos adatával (a következő lépés bemenete). <br/>Ez a válasz létrehoz egy Logic app-példányt, és elindítja a munkafolyamatot. | 
| Nem található | Egy `202 ACCEPTED` `location` fejlécet és egy fejlécet tartalmazó http-állapotot ad vissza `retry-after` . <br/>Eseményindítók esetén a `location` fejlécnek tartalmaznia kell egy `triggerState` lekérdezési paramétert is, amely általában egy "Timestamp". Az API ezzel az azonosítóval nyomon követheti a logikai alkalmazás aktiválásának utolsó időpontját. | 
||| 

Ha például rendszeresen szeretné ellenőrizni a szolgáltatást az új fájlokhoz, létrehozhat egy lekérdezési triggert, amely a következő viselkedésekkel rendelkezik:

| A kérelem tartalmazza `triggerState` ? | API-válasz | 
| -------------------------------- | -------------| 
| No | Egy HTTP `202 ACCEPTED` -állapotot, valamint egy fejlécet ad vissza, amely `location` `triggerState` az aktuális időpontra, az `retry-after` intervallum pedig 15 másodpercre van állítva. | 
| Yes | Győződjön meg arról, hogy a szolgáltatásban a `DateTime` for () után hozzáadott fájlok szerepelnek `triggerState` . | 
||| 

| Talált fájlok száma | API-válasz | 
| --------------------- | -------------| 
| Egyetlen fájl | Adja vissza a HTTP- `200 OK` állapotot és a tartalom hasznos adatait, frissítse a értéket a `triggerState` `DateTime` visszaadott fájlhoz, és állítsa be az `retry-after` intervallumot 15 másodpercre. | 
| Több fájl | Egyszerre csak egy fájlt ad vissza, egy HTTP- `200 OK` állapotot, `triggerState` a frissítést, és az `retry-after` intervallumot 0 másodpercre állítja be. </br>Ezek a lépések lehetővé teszik, hogy a motor tudja, hogy további adatok állnak rendelkezésre, és hogy a motornak azonnal el kell kérnie az adatok kérését a fejléc URL-címéről `location` . | 
| Nincsenek fájlok | HTTP- `202 ACCEPTED` állapotot ad vissza, ne módosítsa `triggerState` , és állítsa be az `retry-after` intervallumot 15 másodpercre. | 
||| 

> [!TIP]
> A lekérdezési trigger mintájának vizsgálatához tekintse át ezt a [lekérdezés-indító vezérlő mintát a githubon](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Várjon, és figyelje az új adattípusokat és eseményeket a webhook trigger-mintázatával

A webhook-trigger egy *leküldéses trigger* , amely a szolgáltatás végpontján megvárja és figyeli az új adatait vagy eseményeit. Ha az új adatok vagy az események megfelelnek a megadott feltételnek, az eseményindító elindít egy Logic app-példányt, majd bemenetként dolgozza fel az adatokat.
A webhook-eseményindítók ugyanúgy működnek, mint az ebben a témakörben korábban ismertetett [webhook-műveletek](#webhook-actions) , és a `subscribe` és a `unsubscribe` végpontokkal vannak beállítva. 

* `subscribe` végpont: amikor webhook-triggert ad hozzá és ment a logikai alkalmazásban, a Logic Apps motor meghívja a `subscribe` végpontot. Ez a lépés azt eredményezi, hogy a logikai alkalmazás létrehoz egy visszahívási URL-címet, amelyet az API tárol. Új vagy a megadott feltételnek megfelelő esemény esetén az API a HTTP-POSTával hívja vissza az URL-címet. A tartalom és a fejléc a logikai alkalmazás bemenetként adja át a tartalmat.

* `unsubscribe` végpont: Ha a webhook-trigger vagy a teljes logikai alkalmazás törölve lett, a Logic Apps motor meghívja a `unsubscribe` végpontot. Az API ezután törölheti a visszahívási URL-címet, és szükség szerint leállíthatja a folyamatokat.

![Webhook-trigger mintája](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

A Logic app Designer jelenleg nem támogatja a webhook-végpontok felfedését a hencegő használatával. Tehát ehhez a mintához hozzá kell adnia egy [ **webhook** -triggert](../connectors/connectors-native-webhook.md) , és meg kell adnia a kérelem URL-címét, fejléceit és törzsét. Lásd még: [HTTPWebhook trigger](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Egy példa webhook-minta esetén tekintse át ezt a [webhook-indító vezérlő mintát a githubon](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

Íme néhány további tipp és Megjegyzés:

* A visszahívási URL-cím átadásához `@listCallbackUrl()` szükség szerint használhatja a munkafolyamat-függvényt az előző mezők bármelyikén.

* Ha nem szeretné, hogy ugyanazokat az adatfeldolgozást többször is feldolgozza, az triggernek törölnie kell a logikai alkalmazásba már olvasott és továbbított adatait.

* Ha a logikai alkalmazás és az előfizetett szolgáltatás tulajdonosa is van, nem kell meghívnia a `unsubscribe` végpontot a visszahívási URL-cím meghívása után. Ellenkező esetben a Logic Apps futtatókörnyezetnek meg kell hívnia a `unsubscribe` végpontot arra, hogy jelezze, ne kelljen több hívást várnia, és hogy az erőforrást a kiszolgáló oldalán is meg lehessen tisztítani.

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>Az API-k hívásai biztonságának növelése a Logic Apps használatával

Az egyéni API-k létrehozása után állítsa be az API-k hitelesítését, hogy biztonságosan meghívja őket a Logic apps szolgáltatásból. Ismerje meg [, hogyan javíthatja az egyéni API-kon érkező hívások biztonságát a Logic apps szolgáltatásban](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>API-k üzembe helyezése és meghívása

A hitelesítés beállítása után állítsa be az API-k üzembe helyezését. Ismerje meg, [Hogyan helyezhet üzembe és hívhat meg egyéni API-kat a Logic apps szolgáltatásból](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Egyéni API-k közzététele az Azure-ban

Ahhoz, hogy az egyéni API-k elérhetők legyenek az Azure más Logic Apps felhasználói számára, hozzá kell adnia a biztonságot, és regisztrálnia kell őket Logic app-összekötőként. További információért lásd az [egyéni összekötők áttekintését](../logic-apps/custom-connector-overview.md). 

Ahhoz, hogy az egyéni API-kat elérhetővé tegye a Logic Apps, a Power automatizáló és a Microsoft Power apps összes felhasználója számára, hozzá kell adnia a biztonságot, regisztrálnia kell az API-kat Logic app-összekötőként, és jelölnie kell az összekötőket a [Microsoft Azure Certified programhoz](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/) 

## <a name="get-support"></a>Támogatás kérése

* Az egyéni API-kkal kapcsolatos speciális segítségért forduljon a következőhöz: [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com) .

* Ha kérdése van, látogasson el a [Microsoft Q&a Azure Logic apps vonatkozó kérdés oldalára](/answers/topics/azure-logic-apps.html).

* Ha szeretne segíteni a Logic Apps fejlesztésében, szavazzon vagy küldje el javaslatait a [Logic Apps felhasználói visszajelzések oldalon](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Következő lépések

* [Hibák és kivételek kezelése](../logic-apps/logic-apps-exception-handling.md)
* [Logikai alkalmazások hívása, triggere vagy beágyazása HTTP-végpontokkal](../logic-apps/logic-apps-http-endpoint.md)
* [Műveletek és eseményindítók használatának mérése](../logic-apps/logic-apps-pricing.md)
