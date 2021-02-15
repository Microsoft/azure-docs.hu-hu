---
title: Ajánlott eljárások Azure Functions
description: A Azure Functions ajánlott eljárásainak és mintáinak megismerése.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5783f8092a6435b43ab8720df18cc5200e390d46
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378247"
---
# <a name="best-practices-for-performance-and-reliability-of-azure-functions"></a>Ajánlott eljárások a Azure Functions teljesítményéhez és megbízhatóságához

Ez a cikk útmutatást nyújt a [kiszolgáló](https://azure.microsoft.com/solutions/serverless/) nélküli Function-alkalmazások teljesítményének és megbízhatóságának javításához.  

Az alábbiakban az ajánlott eljárások azt ismertetik, hogyan hozhat létre és alakíthat ki kiszolgáló nélküli megoldásokat Azure Functions használatával.

## <a name="avoid-long-running-functions"></a>A hosszan futó függvények elkerülése

A nagyméretű, hosszan futó függvények váratlan időtúllépési problémákhoz vezethetnek. Ha többet szeretne megtudni egy adott üzemeltetési csomag időtúllépéséről, tekintse meg a [Function app timeout időtartamát](functions-scale.md#timeout).

A függvények sok Node.js függőség miatt nagy méretűek lehetnek. A függőségek importálása nagyobb betöltési időt is okozhat, ami váratlan időtúllépéseket eredményezhet. A függőségeket explicit módon és implicit módon kell betölteni. A kód által betöltött egyetlen modul a saját további moduljait is betöltheti.

Amikor csak lehetséges, a nagyméretű függvények újrabontása kisebb functions-készletekbe, amelyek együtt működnek, és a válaszokat gyorsan adják vissza. Előfordulhat például, hogy egy webhook vagy egy HTTP trigger függvény egy bizonyos időkorláton belül visszaigazolási választ kér. gyakori, hogy a webhookok azonnali választ igényelnek. A HTTP-trigger hasznos adatait átadhatja egy, a várólista-trigger függvény által feldolgozandó várólistába. Ezzel a megközelítéssel késleltetheti a tényleges munkát, és azonnali választ adhat vissza.

## <a name="cross-function-communication"></a>Cross Function kommunikáció

A [Durable functions](durable/durable-functions-overview.md) és a [Azure Logic apps](../logic-apps/logic-apps-overview.md) az állapot-váltások és a több függvény közötti kommunikáció kezelésére készült.

Ha nem használ Durable Functions vagy Logic Apps több funkcióval való integráláshoz, érdemes a tárolási várólistákat használni a több funkciós kommunikációhoz. Ennek fő oka, hogy a tárolási várólisták olcsóbbak, és sokkal könnyebben használhatók, mint a többi tárolási lehetőség.

A tárolási várólistán lévő egyes üzenetek mérete 64 KB-ra van korlátozva. Ha nagyobb üzeneteket kell átadnia a függvények között, egy Azure Service Bus üzenetsor használható a standard szinten 256 KB-ig, a prémium szinten legfeljebb 1 MB-ra.

Service Bus témakörök akkor hasznosak, ha a feldolgozás előtt üzenet-szűrésre van szükség.

Az Event hubok hasznosak a nagy mennyiségű kommunikáció támogatásához.

## <a name="write-functions-to-be-stateless"></a>Az írási függvények állapot nélküliek lesznek

A függvények állapot nélküliek és idempotens, ha lehetséges. Társítson minden szükséges állapotinformációt az adataihoz. Például egy feldolgozandó megrendelés valószínűleg egy társított taggal fog rendelkezni `state` . Egy függvény az adott állapot alapján feldolgozhat egy rendelést, miközben maga a függvény állapota változatlan marad.

A idempotens functions használata különösen ajánlott időzítő eseményindítókkal. Ha például olyan dolog van, amely naponta egyszer kell futnia, írja meg, hogy a nap folyamán bármikor fusson ugyanazzal az eredménnyel. A függvény akkor léphet ki, ha egy adott nap nem működik. Ha egy korábbi Futtatás nem fejeződött be, a következő futtatásnak kell megadnia, ahol abbahagyta.

## <a name="write-defensive-functions"></a>Védelmi függvények írása

Tegyük fel, hogy a függvényt bármikor felmerülhet. Tervezze meg a függvényeket úgy, hogy a következő végrehajtás során egy korábbi sikertelen pontról folytassanak. Vegyünk egy olyan forgatókönyvet, amely a következő műveleteket igényli:

1. Egy adatbázisban lévő 10 000-sorok lekérdezése.
2. Hozzon létre egy üzenetsor-üzenetet az egyes sorokhoz a sor további feldolgozásához.

Attól függően, hogy milyen összetett a rendszer, lehetséges, hogy: a kapcsolódó alsóbb szintű szolgáltatások rosszul viselkednek, a hálózati kimaradások, vagy a kvóta korlátai is megszülettek. Ezek mindegyike bármikor hatással lehet a függvényre. Meg kell terveznie a feladatait.

Hogyan reagál a kód, ha hiba lép fel az elemek 5 000-es beszúrását követően a feldolgozáshoz? Egy megadott készlet elemeinek nyomon követése. Ellenkező esetben a következő alkalommal is beszúrhatja őket. Ez a kettős Beszúrás komoly hatással lehet a munkahelyi folyamatra, így [a függvények idempotens](functions-idempotent.md). 

Ha egy üzenetsor-elem már fel lett dolgozva, a függvény nem lehet op.

Használja ki a Azure Functions platformon használt összetevőkhöz már megadott védelmi mértékeket. Tekintse meg például az [Azure Storage üzenetsor-eseményindítók és-kötések](functions-bindings-storage-queue-trigger.md#poison-messages)dokumentációjában található **méreg üzenetsor-üzeneteinek kezelését** ismertető részt.

## <a name="function-organization-best-practices"></a>Az ajánlott eljárások a szervezeti feladathoz

A megoldás részeként többféle funkciót fejleszthet és tehet közzé. Ezek a függvények gyakran egyetlen Function-alkalmazásba vannak egyesítve, de külön Function-alkalmazásokban is futhatnak. A Premium és a dedikált (App Service) üzemeltetési csomagokban több Function-alkalmazás is megoszthatja ugyanazokat az erőforrásokat, ha ugyanazon a csomagban fut. A függvények és függvények alkalmazásainak csoportosítása hatással lehet a teljes megoldás teljesítményére, méretezésére, konfigurálására, üzembe helyezésére és biztonságára. Nincsenek szabályok, amelyek minden forgatókönyvre érvényesek, ezért vegye figyelembe az ebben a szakaszban található információkat a függvények tervezésekor és fejlesztésekor.

### <a name="organize-functions-for-performance-and-scaling"></a>Függvények szervezése a teljesítményhez és a skálázáshoz

Minden létrehozott függvényhez tartozik egy memória-lábnyom. Habár ez a helyigény általában kicsi, és a Function alkalmazásban túl sok függvény is képes az alkalmazás lassabb indítására az új példányokon. Ez azt is jelenti, hogy a Function app teljes memóriahasználat magasabb lehet. Nehéz megmondani, hogy hány függvényt kell egyetlen alkalmazásban megadni, amely az adott számítási feladattól függ. Ha azonban a függvény nagy mennyiségű adat tárolására van szüksége a memóriában, érdemes lehet kevesebb funkciót felvenni egyetlen alkalmazásban.

Ha több Function alkalmazást is futtat egyetlen prémium szintű csomagban vagy dedikált (App Service) csomagban, ezek az alkalmazások mind együtt méretezhetők. Ha van egy olyan Function-alkalmazás, amely sokkal nagyobb memóriát igényel, mint a többi, akkor az alkalmazás által üzembe helyezett összes példányon aránytalanul nagy mennyiségű memória-erőforrást használ. Mivel ez kevesebb memóriát is igénybe vehet a többi alkalmazás számára az egyes példányokon, érdemes lehet egy nagy memóriát használó Function alkalmazást futtatni, például a saját külön üzemeltetési tervében.

> [!NOTE]
> A használati [terv](./functions-scale.md)használatakor javasoljuk, hogy minden alkalmazást a saját csomagjában helyezze el, mivel az alkalmazások egymástól függetlenül méretezhetők.

Gondolja át, hogy a függvényeket különböző betöltési profilokkal kívánja-e csoportosítani. Ha például olyan függvényt használ, amely több ezer üzenetsor-üzenetet dolgoz fel, és egy másikat, amely csak alkalmanként fordul elő, de nagy memória-követelményekkel rendelkezik, érdemes lehet külön Function-alkalmazásokban telepíteni őket, hogy saját erőforrásaikat kapjanak, és egymástól függetlenül méretezhetők.

### <a name="organize-functions-for-configuration-and-deployment"></a>Függvények rendszerezése konfiguráláshoz és üzembe helyezéshez

A Function apps tartalmaz egy `host.json` fájlt, amely a függvények és a Azure functions futtatókörnyezet speciális viselkedésének konfigurálására szolgál. A fájl módosításai az `host.json` alkalmazásban található összes függvényre érvényesek. Ha vannak olyan függvények, amelyek egyéni konfigurációt igényelnek, érdemes áthelyezni őket a saját Function alkalmazásba.

A helyi projekt összes funkciója együtt települ az Azure-beli Function alkalmazásban található fájlok készletével. Előfordulhat, hogy külön kell telepítenie az egyes függvényeket, vagy olyan szolgáltatásokat kell használnia, mint például az [üzembe helyezési](./functions-deployment-slots.md) pontok, amelyek nem mások. Ilyen esetekben ezeket a függvényeket (külön programkód-projektekben) a különböző Function apps-alkalmazásokba kell telepítenie.

### <a name="organize-functions-by-privilege"></a>Függvények rendszerezése jogosultság alapján

A (z) alkalmazás beállításaiban tárolt kapcsolatok és egyéb hitelesítő adatok lehetővé teszi, hogy a Function alkalmazás összes funkciója ugyanazokat az engedélyeket adja meg a társított erőforrásban. Érdemes lehet minimalizálni az adott hitelesítő adatokhoz hozzáféréssel rendelkező függvények számát azáltal, hogy áthelyezi azokat a függvényeket, amelyek nem használják ezeket a hitelesítő adatokat egy különálló Function alkalmazásnak. Mindig használhat olyan technikákat, mint például a függvények [láncolása](/learn/modules/chain-azure-functions-data-using-bindings/) , hogy az adatok áthaladnak a függvények között különböző Function Apps-alkalmazásokban.  

## <a name="scalability-best-practices"></a>Méretezhetőség – ajánlott eljárások

Számos tényező befolyásolja a Function app-méretezési példányait. A részletek a [függvények skálázására](functions-scale.md)szolgáló dokumentációban találhatók.  Az alábbiakban néhány ajánlott eljárás használható a functions-alkalmazások optimális méretezhetőségének biztosításához.

### <a name="share-and-manage-connections"></a>Kapcsolatok megosztása és kezelése

Ha lehetséges, a külső erőforrásokhoz való kapcsolódást újra fel kell használni. Lásd: [kapcsolatok kezelése Azure Functionsban](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>A Storage-fiókok megosztásának elkerülése

Egy Function-alkalmazás létrehozásakor hozzá kell rendelnie egy Storage-fiókhoz. A Storage-fiók kapcsolatai a [AzureWebJobsStorage alkalmazás-beállításban](./functions-app-settings.md#azurewebjobsstorage)maradnak.

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Ne keverje a tesztet és a termelési kódot ugyanabban a Function alkalmazásban

Functions-megosztási erőforrásokon belüli függvények. A memória például meg van osztva. Ha éles környezetben használ egy Function alkalmazást, ne adjon hozzá teszttel kapcsolatos függvényeket és erőforrásokat. Váratlan terhelést okozhat a termelési kód végrehajtása során.

Ügyeljen arra, hogy milyen terhelést kell betöltenie az üzemi függvények alkalmazásaiba. A memória átlaga az alkalmazás egyes funkciói között.

Ha a megosztott szerelvény több .NET-függvényben is szerepel, akkor egy közös megosztott mappában kell elhelyeznie. Ellenkező esetben előfordulhat, hogy véletlenül ugyanazon bináris fájl több verzióját helyezi üzembe, amely eltérően viselkedik a függvények között.

Ne használja a részletes naplózást az éles kódban, amely negatív hatással van a teljesítményre.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Aszinkron kód használata, de ne blokkolja a hívásokat

Az aszinkron programozás ajánlott eljárás, különösen az I/O-műveletek blokkolása esetén.

A C# nyelvben mindig Kerülje a `Result` tulajdonság vagy a hívás `Wait` metódusának hivatkozását egy `Task` példányon. Ez a módszer a szál kimerülését eredményezheti.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Több munkavégző folyamat használata

Alapértelmezés szerint a függvények minden gazdagép-példánya egyetlen munkavégző folyamatot használ. A teljesítmény javítása érdekében, különösen az egyszálas futtatókörnyezetek, például a Python esetében, a [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) használatával növelheti a munkavégző folyamatok számát (legfeljebb 10). Azure Functions ezt követően megpróbál egyenletesen terjeszteni egyidejű függvényeket a feldolgozók között.

A FUNCTIONS_WORKER_PROCESS_COUNT minden olyan gazdagépre vonatkozik, amelyet a functions hoz létre, amikor az alkalmazás az igények kielégítése érdekében felskálázást végez.

### <a name="receive-messages-in-batch-whenever-possible"></a>Üzenetek fogadása kötegben, amikor csak lehetséges

Egyes eseményindítók, például az Event hub lehetővé teszik egy köteg üzenet fogadását egyetlen meghíváskor.  A kötegelt üzenetek sokkal jobb teljesítményt biztosítanak.  A (z) `host.json` [host.jsa dokumentációban](functions-host-json.md) részletesen beállíthatja a Batch maximális méretét.

A C# függvények esetében a típust erősen gépelt tömbre módosíthatja.  Például `EventData sensorEvent` a metódus aláírása helyett lehet `EventData[] sensorEvent` .  Más nyelvek esetében explicit módon be kell állítania a kardinális tulajdonságot a-ben, hogy `function.json` az `many` [itt látható módon](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10)engedélyezze a kötegelt feldolgozást.

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>A gazdagép viselkedésének konfigurálása a Egyidejűség jobb kezelésére

A `host.json` Function alkalmazásban található fájl lehetővé teszi a gazdagép-futtatókörnyezet és az aktiválási viselkedés konfigurációját.  A Batch-viselkedésen kívül számos eseményindító esetében is kezelheti a párhuzamosságot. Az ilyen beállításokban szereplő értékek gyakran módosítják az egyes példányok megfelelő méretezését a meghívott függvények igényei szerint.

A host.jsfájljának beállításai az alkalmazáson belüli összes függvényre érvényesek a függvény *egyetlen példányán* belül. Ha például két HTTP-függvényt és kérést tartalmazó Function alkalmazást használ [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) , a http-triggerre irányuló kérelem a közös 25 egyidejű kérelemre is beleszámít.  Ha a függvény alkalmazása 10 példányra van méretezve, a tíz függvény hatékonyan engedélyezi a 250 egyidejű kérelmeket (10 példány * 25 egyidejű kérelem/példány). 

További gazdagép-konfigurációs beállítások találhatók a [host.json Configuration cikkben](functions-host-json.md).

## <a name="next-steps"></a>Következő lépések

További információkat találhat az alábbi forrásokban:

* [Kapcsolatok kezelése a Azure Functionsban](manage-connections.md)
* [Azure App Service ajánlott eljárások](../app-service/app-service-best-practices.md)
