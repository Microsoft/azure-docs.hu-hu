---
title: Emberi interakció és időtúllépés a Durable Functions-ben – Azure
description: Ismerje meg, hogyan kezelheti az emberi interakciókat és időtúllépéseket a Azure Functions Durable Functions bővítményében.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: dd7f8416b2f4520ec8e94c8608f753f7412afc4d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627372"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Emberi interakció Durable Functions – telefonos ellenőrzési minta

Ez a példa azt mutatja be, hogyan hozhat létre olyan [Durable functions](durable-functions-overview.md) -előkészítést, amely emberi beavatkozást is magában foglal. Amikor egy valós személy részt vesz egy automatizált folyamatban, a folyamatnak képesnek kell lennie értesítéseket küldeni a személynek, és aszinkron módon fogadnia a válaszokat. Azt is lehetővé kell tenni, hogy a személy nem érhető el. (Ez utóbbi részben az időtúllépések fontosak lesznek.)

Ez a példa egy SMS-alapú telefonos ellenőrzési rendszert valósít meg. Az ilyen típusú folyamatokat gyakran használják az ügyfél telefonszámának vagy a többtényezős hitelesítés (MFA) ellenőrzéséhez. Ez egy nagy teljesítményű példa, mivel a teljes implementáció pár kis függvény használatával történik. Nincs szükség külső adattárra, például egy adatbázisra.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>A forgatókönyv áttekintése

A telefonos ellenőrzés segítségével ellenőrizheti, hogy az alkalmazás végfelhasználói nem levélszemétek-e, és hogy kik azok, akik azt mondják. A többtényezős hitelesítés gyakori használati eset a felhasználói fiókok támadók általi védelméhez. A saját telefonos ellenőrzés megvalósításának kihívása az, hogy a rendszer **állapot-nyilvántartó interakciót** igényel egy emberi lényrel. A végfelhasználók általában bizonyos kódokat (például egy 4 számjegyű számot) adnak meg, és **ésszerű** időn belül válaszolni kell rájuk.

A szokásos Azure Functions állapot nélküliek (mint a más platformokon futó más Felhőbeli végpontok), ezért az ilyen típusú interakciók az adatbázisban vagy valamilyen más állandó tárolóban kifejezetten az állapot felügyeletét vonják maguk után. Emellett az interakciót több, egymással koordinálható függvénybe kell bontani. Például szükség van legalább egy függvényre a kód kiválasztásához, a megtartáshoz és a felhasználó telefonjára való elküldéséhez. Emellett szükség van legalább egy másik függvényre, hogy választ kapjon a felhasználótól, és valahogy vissza kell képeznie az eredeti függvény hívására, hogy el lehessen végezni a kód érvényesítését. Az időkorlát szintén fontos szempont a biztonság biztosításához. Meglehetősen összetett, gyorsan elvégezhető.

A forgatókönyv összetettsége nagy mértékben csökkent a Durable Functions használatakor. Ahogy az ebben a példában is látható, egy Orchestrator-függvény egyszerűen, külső adattárak nélkül kezelheti az állapot-nyilvántartó interakciókat. Mivel a Orchestrator függvények *tartósak*, az interaktív folyamatok is nagyon megbízhatóak.

## <a name="configuring-twilio-integration"></a>Twilio-integráció konfigurálása

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>A függvények

Ez a cikk végigvezeti a minta alkalmazás következő funkcióiról:

* `E4_SmsPhoneVerification`: Egy [Orchestrator-függvény](durable-functions-bindings.md#orchestration-trigger) , amely elvégzi a telefonos ellenőrzési folyamatot, beleértve az időtúllépések és az újrapróbálkozások kezelését.
* `E4_SendSmsChallenge`: Olyan [tevékenységi függvény](durable-functions-bindings.md#activity-trigger) , amely szöveges üzenetben küld egy kódot.

> [!NOTE]
> A `HttpStart` [minta alkalmazásban és a](#prerequisites) rövid útmutatóban szereplő függvény a Orchestrator függvényt kiváltó előkészítési [ügyfélként](durable-functions-bindings.md#orchestration-client) működik.

### <a name="e4_smsphoneverification-orchestrator-function"></a>E4_SmsPhoneVerification Orchestrator függvény

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> Előfordulhat, hogy először nem nyilvánvaló, de ez a Orchestrator nem sérti a determinisztikus-előkészítési [megkötést](durable-functions-code-constraints.md). Ez azért determinisztikus, mert a `CurrentUtcDateTime` tulajdonság az időzítő lejárati idejének kiszámítására szolgál, és ugyanezt az értéket adja vissza a Orchestrator-kód ezen pontján lévő összes visszajátszás esetében. Ez a viselkedés fontos annak biztosítása érdekében, hogy ugyanaz az `winner` eredmény legyen minden ismétlődő hívástól `Task.WhenAny` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A **E4_SmsPhoneVerification** függvény a standard *function.jst* használja a Orchestrator függvényekhez.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

Itt látható a függvényt megvalósító kód:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> Előfordulhat, hogy először nem nyilvánvaló, de ez a Orchestrator nem sérti a determinisztikus-előkészítési [megkötést](durable-functions-code-constraints.md). Ez azért determinisztikus, mert a `currentUtcDateTime` tulajdonság az időzítő lejárati idejének kiszámítására szolgál, és ugyanezt az értéket adja vissza a Orchestrator-kód ezen pontján lévő összes visszajátszás esetében. Ez a viselkedés fontos annak biztosítása érdekében, hogy ugyanaz az `winner` eredmény legyen minden ismétlődő hívástól `context.df.Task.any` .

# <a name="python"></a>[Python](#tab/python)

A **E4_SmsPhoneVerification** függvény a standard *function.jst* használja a Orchestrator függvényekhez.

[!code-json[Main](~/samples-durable-functions-python/samples/human_interaction/E4_SmsPhoneVerification/function.json)]

Itt látható a függvényt megvalósító kód:

[!code-python[Main](~/samples-durable-functions-python/samples/human_interaction/E4_SmsPhoneVerification/\_\_init\_\_.py)]

> [!NOTE]
> Előfordulhat, hogy először nem nyilvánvaló, de ez a Orchestrator nem sérti a determinisztikus-előkészítési [megkötést](durable-functions-code-constraints.md). Ez azért determinisztikus, mert a `currentUtcDateTime` tulajdonság az időzítő lejárati idejének kiszámítására szolgál, és ugyanezt az értéket adja vissza a Orchestrator-kód ezen pontján lévő összes visszajátszás esetében. Ez a viselkedés fontos annak biztosítása érdekében, hogy ugyanaz az `winner` eredmény legyen minden ismétlődő hívástól `context.df.Task.any` .

---

Az indítás után a Orchestrator függvény a következő műveleteket végzi el:

1. Beolvas egy telefonszámot, amelyre az SMS-értesítést *küldi* .
2. Meghívja a **E4_SendSmsChallenget** , hogy SMS-üzenetet küldjön a felhasználónak, és visszaadja a várt 4 számjegyű Challenge-kódot.
3. Egy tartós időzítőt hoz létre, amely az aktuális időpontból 90 másodpercet indít el.
4. Az időzítővel párhuzamosan vár egy **SmsChallengeResponse** eseményt a felhasználótól.

A felhasználó egy négyjegyű kóddal rendelkező SMS-üzenetet kap. Az ellenőrzési folyamat elvégzéséhez 90 másodperc van, hogy ugyanazt a négyjegyű kódot küldje vissza a Orchestrator függvény példányának. Ha nem a megfelelő kódot küldi el, a rendszer további három kísérletet tesz arra, hogy jobbat kapjon (ugyanazon a 90-másodperces ablakban).

> [!WARNING]
> Fontos [megszakítani az időzítőt](durable-functions-timers.md) , ha már nincs rájuk szükség a lejárathoz, ahogy a fenti példában a kérdéses válasz elfogadásakor is.

## <a name="e4_sendsmschallenge-activity-function"></a>E4_SendSmsChallenge Activity függvény

A **E4_SendSmsChallenge** függvény a Twilio kötés használatával küldi el az SMS-üzenetet a négyjegyű kóddal a végfelhasználónak.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> A mintakód futtatásához telepítenie kell a `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget csomagot.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A *function.jsa* következő módon van definiálva:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

Itt a kód generálja a négyjegyű hibakódot, és elküldi az SMS-üzenetet:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

# <a name="python"></a>[Python](#tab/python)

A *function.jsa* következő módon van definiálva:

[!code-json[Main](~/samples-durable-functions-python/samples/human_interaction/SendSMSChallenge/function.json)]

Itt a kód generálja a négyjegyű hibakódot, és elküldi az SMS-üzenetet:

[!code-python[Main](~/samples-durable-functions-python/samples/human_interaction/SendSMSChallenge/\_\_init\_\_.py)]

---

## <a name="run-the-sample"></a>Minta futtatása

A mintában szereplő HTTP-triggerű függvények használatával a következő HTTP POST-kérelem elküldésével elindíthatja a koordinálást:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

A Orchestrator függvény a megadott telefonszámot fogadja, és azonnal SMS-üzenetet küld egy véletlenszerűen generált 4 számjegyű ellenőrző kóddal &mdash; , például *2168*. A függvény ezután 90 másodpercet vár a válaszra.

A kóddal való válaszadáshoz használhatja a [ `RaiseEventAsync` (.net) vagy a `raiseEvent` (JavaScript)](durable-functions-instance-management.md) függvényt egy másik függvényen belül, vagy meghívhatja a fenti 202-válaszban hivatkozott **sendEventUrl** http post webhookot, az `{eventName}` esemény nevére cserélve `SmsChallengeResponse` :

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Ha ezt az időzítő lejárata előtt küldi el, a rendszer befejeződik, és a `output` mező értéke `true` , amely sikeres ellenőrzést jelez.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Ha engedélyezte az időzítő érvényességét, vagy ha négyszer adja meg a kódot, akkor lekérdezheti az állapotot, és megtekintheti az előkészítési `false` függvény kimenetét, ami azt jelzi, hogy a telefon ellenőrzése nem sikerült.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="next-steps"></a>Következő lépések

Ez a példa a Durable Functions, különösen és API-k fejlett képességeit mutatja `WaitForExternalEvent` be `CreateTimer` . Megismerte, hogy ezek hogyan kombinálhatók a szolgáltatással `Task.WaitAny` egy megbízható időtúllépési rendszer megvalósításához, ami gyakran hasznos a valós emberekkel való interakcióhoz. Ha többet szeretne megtudni a Durable Functions használatáról, tekintse meg az egyes témakörök részletes lefedettségét biztosító cikkek sorozatát.

> [!div class="nextstepaction"]
> [Ugrás a sorozat első cikkére](durable-functions-bindings.md)
