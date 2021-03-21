---
title: Eseményvezérelt skálázás Azure Functions
description: Ismerteti a használati terv és a Prémium csomag Function apps méretezési viselkedését.
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: 8aca1ab6629f95ef9162e1247434bd3189d5a7d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97937649"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Eseményvezérelt skálázás Azure Functions

A használat és a prémium csomagok esetében Azure Functions a processzor-és memória-erőforrásokat a functions gazdagép további példányainak hozzáadásával. A példányok száma a függvényt kiváltó események számánál van meghatározva. 

A functions gazdagép összes példánya a használati tervben legfeljebb 1,5 GB memóriával és egy PROCESSZORral rendelkezik.  A gazdagép egy példánya a teljes Function alkalmazás, ami azt jelenti, hogy a Function app-ban található összes függvény egy adott példányon belül található, és egy időben méretezhető. Az azonos fogyasztási tervvel rendelkező alkalmazások egymástól függetlenül méretezhetők.  A Prémium csomag esetében a csomag mérete határozza meg az adott példányon lévő összes alkalmazás rendelkezésre álló memóriáját és PROCESSZORát.  

A függvény kódjának fájljai a függvény fő Storage-fiókján Azure Files megosztásokon tárolódnak. Ha törli a Function alkalmazás fő Storage-fiókját, a rendszer törli a függvény kódjának fájljait, és nem állítható helyre.

## <a name="runtime-scaling"></a>Futtatókörnyezet skálázása

A Azure Functions a *skálázási vezérlő* nevű összetevővel figyeli az események sebességét, és meghatározza, hogy a méretezést ki kell-e bővíteni. A skálázási vezérlő heurisztikus adattípust használ az egyes triggerekhez. Ha például egy Azure üzenetsor-tárolási triggert használ, az a várólista hossza és a legrégebbi üzenetsor-üzenet kora alapján méretezhető.

A Azure Functions méretezési egysége a Function alkalmazás. A Function alkalmazás skálázásakor a rendszer további erőforrásokat foglal le a Azure Functions gazdagép több példányának futtatásához. Fordítva, ahogy a számítási igény csökken, a skálázási vezérlő eltávolítja a Function Host-példányokat. A példányok száma végül "skálázás" értékre nulla, ha egyetlen függvény sem fut a Function alkalmazásban.

![Vezérlő-figyelési események méretezése és példányok létrehozása](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>Hidegindító

Azt követően, hogy a függvény alkalmazása több percig is tétlen volt, a platform méretezheti a példányok számát, amelyeken az alkalmazás nullára fut. A következő kérelem a nullára való skálázás késleltetését adja meg. Ezt a késést a rendszer _hideg indításnak_ nevezzük. A Function alkalmazás által igényelt függőségek száma hatással lehet a hideg kezdési időpontra. A hidegebb indítás több problémát jelent a szinkron műveletek, például a HTTP-eseményindítók esetében, amelyeknek választ kell visszaadniuk. Ha a ritkán használt funkciók hatással vannak a függvényekre, érdemes lehet egy prémium csomagon vagy egy dedikált csomagban futtatni, ahol az **Always On** beállítás engedélyezve van.   

## <a name="understanding-scaling-behaviors"></a>A skálázási viselkedés ismertetése

A skálázás több tényezőn is változhat, és a kiválasztott trigger és nyelv alapján különbözőképpen méretezhető. Van néhány bonyolult méretezési mód az alábbiakkal kapcsolatban:

* **Példányok maximális száma:** Egyetlen Function-alkalmazás csak legfeljebb 200 példányra méretezhető. Egyetlen példány egyszerre több üzenetet vagy kérelmet is feldolgozhat, így az egyidejű végrehajtások száma nem megengedett.  [A maximális méretet megadhatja](#limit-scale-out) , ha szükséges.
* **Új példányok aránya:** HTTP-eseményindítók esetén az új példányok lefoglalása legfeljebb egyszer, másodpercenként történik. A nem HTTP-triggerek esetében az új példányok lefoglalása legfeljebb 30 másodpercenként történik. A skálázás gyorsabb, ha [prémium](functions-premium-plan.md)szintű csomagban fut.
* **Méretezés hatékonysága:** Service Bus eseményindítók esetében a leghatékonyabb skálázáshoz használja az erőforrások _kezelése_ jogosultságokat. A _figyelési_ jogosultságok használata esetén a skálázás nem annyira pontos, mert a várólista hossza nem használható a skálázási döntések tájékoztatására. Ha többet szeretne megtudni a Service Bus hozzáférési házirendekben található jogosultságok beállításáról, tekintse meg a [megosztott hozzáférés engedélyezési házirendjét](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies). Az Event hub-eseményindítók esetében lásd a [méretezési útmutatót](functions-bindings-event-hubs-trigger.md#scaling) a dokumentációban. 

## <a name="limit-scale-out"></a>Felskálázás korlátozása

Előfordulhat, hogy korlátozni szeretné a felskálázáshoz használt alkalmazások maximális számát.  Ez leggyakrabban olyan esetekben fordul elő, amikor egy alsóbb rétegbeli összetevő, például egy adatbázis korlátozott átviteli sebességgel rendelkezik.  Alapértelmezés szerint a használati terv függvények több mint 200 példányra vannak kibővítve, és a prémium szintű csomag funkciói akár több mint 100 példányra is kiméretezhetők.  Az érték módosításával egy adott alkalmazásnál alacsonyabb maximális értéket adhat meg `functionAppScaleLimit` .  A `functionAppScaleLimit` beállítható a `0` vagy a `null` korlátozás nélkül, vagy egy érvényes érték `1` és az alkalmazás maximális értéke.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>Ajánlott eljárások és minták méretezhető alkalmazásokhoz

A függvények alkalmazásának számos aspektusa van, amely hatással van a méretezés módjára, beleértve a gazdagép konfigurációját, a futásidejű lábnyomot és az erőforrás-hatékonyságot.  További információ: a [teljesítmény szempontjairól szóló cikk méretezhetőségi szakasza](functions-best-practices.md#scalability-best-practices). Azt is figyelembe kell vennie, hogy a kapcsolatok hogyan viselkednek, mint a Function app Scales. További információ: a [kapcsolatok kezelése a Azure Functionsban](manage-connections.md).

A Python és a Node.js méretezésével kapcsolatos további információkért lásd: [Azure functions Python fejlesztői útmutató – skálázás és Egyidejűség](functions-reference-python.md#scaling-and-performance) és [Azure functions Node.js fejlesztői útmutató – skálázás és Egyidejűség](functions-reference-node.md#scaling-and-concurrency).

## <a name="billing-model"></a>Számlázási modell

A különböző csomagokra vonatkozó számlázást részletesen ismertetjük a [Azure functions díjszabási oldalán](https://azure.microsoft.com/pricing/details/functions/). A függvény az alkalmazás szintjén összesíti a használatot, és csak a függvény kódjának végrehajtásához tartozó időt számítja ki. A számlázási egységek a következők:

* **Erőforrás-használat GB-ban (GB-s)**. A függvény alkalmazásban található összes függvényhez a memória mérete és a végrehajtási idő kombinációja számítja ki. 
* **Végrehajtások**. Minden alkalommal, amikor egy függvény egy esemény eseményindítóra válaszol.

Hasznos lekérdezések és információk arról, hogyan értelmezhető a használati számla a [Számlázási GYIK](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)szolgáltatásban.

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>Következő lépések

+ [Üzemeltetési lehetőségek Azure Functions](functions-scale.md)

