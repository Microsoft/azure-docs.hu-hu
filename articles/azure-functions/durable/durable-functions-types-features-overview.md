---
title: Függvények típusai az Azure Durable Functions
description: Ismerje meg, hogy milyen típusú függvények és szerepkörök működnek a Durable Functions Azure Functions-ben való összehangolása során.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84697707"
---
# <a name="durable-functions-types-and-features"></a>Durable Functions típusok és szolgáltatások

Durable Functions a [Azure functions](../functions-overview.md)kiterjesztése. A függvények végrehajtásának állapot-nyilvántartó előkészítéséhez Durable Functions is használhatja. A tartós Function app egy olyan megoldás, amely különböző Azure-függvényekből áll. A függvények a különböző szerepköröket egy tartós függvény-előkészítés során is lejátszhatják. 

Jelenleg a Azure Functions: Activity, a Orchestrator, az Entity és az Client szolgáltatásban négy tartós függvény szerepel. A szakasz további részében további részleteket talál a megszervezésben részt vevő függvények típusairól.

## <a name="orchestrator-functions"></a>Orchestrator függvények

A Orchestrator függvények leírják a műveletek végrehajtását, valamint a műveletek végrehajtásának sorrendjét. A Orchestrator függvények leírják a kód (C# vagy JavaScript) előkészítését, ahogyan az [Durable functions alkalmazási mintákban](durable-functions-overview.md#application-patterns)látható. Az előkészítés számos különböző típusú műveletet tartalmazhat, többek között a [tevékenységi funkciókat](#activity-functions), az [alfolyamatokat](durable-functions-orchestrations.md#sub-orchestrations), [a külső eseményekre, a](durable-functions-orchestrations.md#external-events) [http](durable-functions-http-features.md)-re és az [időzítőre](durable-functions-orchestrations.md#durable-timers)való várakozást. A Orchestrator függvények is kommunikálhatnak az [Entity functions](#entity-functions)szolgáltatással.

> [!NOTE]
> A Orchestrator függvények egyszerű kóddal vannak írva, de szigorú követelmények vonatkoznak a kód írására. Pontosabban, a Orchestrator függvény kódjának *determinisztikus* kell lennie. Ha nem sikerül követni ezeket a szabályokra vonatkozó követelményeket, a Orchestrator functions nem tud megfelelően futni. A követelményekkel és azok megoldásával kapcsolatos részletes információkat a [kód megkötései](durable-functions-code-constraints.md) témakörben találhat.

A Orchestrator functions és azok funkcióinak részletes ismertetését lásd a [tartós](durable-functions-orchestrations.md) előkészítéssel foglalkozó cikkben.

## <a name="activity-functions"></a>Activity függvények

A tevékenységi funkciók az alapszintű Munkaegységek tartós függvények előkészítésében. A Activity functions a folyamat során feldolgozható függvények és feladatok. Létrehozhat például egy Orchestrator függvényt egy megrendelés feldolgozásához. A feladatok közé tartozik a leltár ellenőrzése, az ügyfél töltése és a szállítás létrehozása. Minden feladat külön tevékenységi függvény lenne. Ezek a tevékenység-függvények sorosan, párhuzamosan vagy a kettő kombinációjában is végrehajthatók.

A Orchestrator függvényektől eltérően a tevékenységi funkciók nem korlátozódnak a bennük elvégezhető feladatok típusára. A tevékenység-függvények gyakran használatosak hálózati hívások kezdeményezéséhez vagy a CPU-igényes műveletek futtatásához. Egy tevékenységi függvény visszatérhet az Orchestrator függvénynek is. A tartós feladatok keretrendszere garantálja, hogy a rendszer minden ún. tevékenységi függvényt *legalább egyszer* végrehajt egy előkészítési művelet végrehajtása során.

> [!NOTE]
> Mivel a tevékenységi funkciók csak *legalább egyszer* garantálják a végrehajtást, javasoljuk, hogy ha lehetséges, végezze el a tevékenység függvényének logikai *idempotens* .

Aktivitási [trigger](durable-functions-bindings.md#activity-trigger) használata tevékenységi függvény definiálásához. A .NET függvények paraméterként kapják meg a `DurableActivityContext` paramétereket. Azt is megteheti, hogy a triggert bármely más JSON-serializeable objektumhoz köti, hogy beadja a függvény bemeneteit. A JavaScriptben az `<activity trigger binding name>` [ `context.bindings` objektum](../functions-reference-node.md#bindings)tulajdonságán keresztül férhet hozzá egy bemenethez. A tevékenység-függvények csak egyetlen értéket adhatnak át nekik. Több érték továbbításához rekordok, tömböket vagy összetett típusokat kell használnia.

> [!NOTE]
> A tevékenység függvény csak Orchestrator függvényből aktiválható.

## <a name="entity-functions"></a>Entitás-függvények

Az Entity functions meghatározza a kis mennyiségű állapot olvasására és frissítésére vonatkozó műveleteket. Gyakran hivatkozunk ezekre az állapot-nyilvántartó entitásokra *tartós entitásként*. A Orchestrator függvényekhez hasonlóan az Entity functions is egy speciális trigger típussal, az *entitások triggerével* működik. Az ügyfél functions vagy a Orchestrator functions használatával is meghívhatók. A Orchestrator függvényektől eltérően az Entity functions nem rendelkezik konkrét kód megkötésekkel. Az Entity functions emellett explicit módon kezeli az állapotot, nem pedig implicit módon jelképezi az állapotot a vezérlési folyamaton keresztül.

> [!NOTE]
> Az Entity functions és a kapcsolódó funkciók csak Durable Functions 2,0-es és újabb verziókban érhetők el.

További információ az Entity functions szolgáltatásról: [tartós entitások](durable-functions-entities.md) cikk.

## <a name="client-functions"></a>Ügyfél-függvények

A Orchestrator függvényeket egy előkészítési [trigger kötése](durable-functions-bindings.md#orchestration-trigger) váltja ki, és az Entity functions egy [entitás-trigger kötést](durable-functions-bindings.md#entity-trigger)indít el. Mindkét eseményindító úgy működik, [hogy a várólistán lévő](durable-functions-task-hubs.md)lévő üzenetekre reagál. Ezeknek az üzeneteknek az elsődleges módja egy [Orchestrator-ügyfél kötésének](durable-functions-bindings.md#orchestration-client) vagy egy [entitás-ügyfél kötésének](durable-functions-bindings.md#entity-client) használata az *ügyfél-függvényen* belül. A nem Orchestrator függvények lehetnek *ügyféloldali függvények*. Aktiválhatja például a Orchestrator egy HTTP által aktivált függvényből, egy Azure Event hub által aktivált függvényt stb. Mi teszi a függvényt az *ügyfél funkció* a tartós ügyfél kimeneti kötésének használatára.

> [!NOTE]
> Más típusú függvényektől eltérően a Orchestrator és az Entity függvények nem indíthatók el közvetlenül az Azure Portal gombjain keresztül. Ha a Orchestrator vagy az Entity függvényt szeretné tesztelni az Azure Portalon, ehelyett olyan *ügyféloldali függvényt* kell futtatnia, amely elindítja a Orchestrator vagy az Entity függvényt a megvalósításának részeként. A legegyszerűbb tesztelési funkciókhoz *manuális trigger* -függvény használata javasolt.

A Orchestrator vagy az Entity functions kiváltásán túl a *tartós ügyfél* -kötés használható a futtatási feladatokkal és az entitásokkal való interakcióra is. Például lekérdezheti, leállíthatja és megszakíthatja az eseményeket. További információ a munkafolyamatok és az entitások kezeléséről: [példányok kezelése](durable-functions-instance-management.md) .

## <a name="next-steps"></a>Következő lépések

Első lépésként hozzon létre egy tartós függvényt [C#](durable-functions-create-first-csharp.md) vagy [JavaScript](quickstart-js-vscode.md)nyelven.

> [!div class="nextstepaction"]
> [További információ a Durable Functions](durable-functions-orchestrations.md)