---
title: Felhasználó által definiált függvények Azure Stream Analytics
description: Ez a cikk áttekintést nyújt a Azure Stream Analytics felhasználó által definiált függvényekről.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: c671d3989fa46fa7546ba042b9132e19d80265a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020502"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Felhasználó által definiált függvények Azure Stream Analytics

A Azure Stream Analytics SQL-szerű lekérdezési nyelve megkönnyíti a valós idejű elemzési logikák megvalósítását a folyamatos átviteli értékeken. Stream Analytics további rugalmasságot biztosít a lekérdezésben meghívott egyéni függvények révén. A következő mintakód egy nevű UDF `sampleFunction` , amely egy paramétert fogad el, minden bemeneti rekord a feladatot fogadja, és az eredményt a kimenetre írja `sampleResult` .

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Függvények típusai

A Azure Stream Analytics a következő négyféle típusú függvényt támogatja: 

* Felhasználói JavaScript-függvények 
* Felhasználó által definiált JavaScript-összesítések 
* C# felhasználó által definiált függvények (a Visual Studióval) 
* Azure Machine Learning 

Ezek a függvények olyan forgatókönyvekhez használhatók, mint például a valós idejű pontozás gépi tanulási modellek, karakterlánc-manipulációk, összetett matematikai számítások, kódolás és dekódolási adatok használatával. 

## <a name="limitations"></a>Korlátozások

A felhasználó által definiált függvények állapot nélküliek, és a visszatérési érték csak skaláris érték lehet. Ezekből a felhasználó által definiált függvényekből nem hívhat külső REST-végpontokat, mivel ez valószínűleg hatással lesz a feladatok teljesítményére. 

Azure Stream Analytics nem tartja nyilván az összes függvényt, és eredményként visszaadott eredményt. Az ismételhetőség garantálása érdekében – például a régebbi időbélyeg-feladatának újbóli futtatása esetén – a függvények nem használhatók, például a vagy a `Date.GetData()` függvény `Math.random()` , mivel ezek a függvények nem adják vissza ugyanazt az eredményt az egyes hívásokhoz.  

## <a name="resource-logs"></a>Erőforrásnaplók

A futásidejű hibák végzetesnek minősülnek, és tevékenység-és erőforrás-naplókon keresztül vannak felszínben. Azt javasoljuk, hogy a függvény minden kivételt és hibát kezelje, és érvényes eredményt ad vissza a lekérdezéshez. Ezzel megakadályozhatja, hogy a feladata [sikertelen állapotba](job-states.md)kerüljön.  

## <a name="exception-handling"></a>Kivételkezelés

Az adatfeldolgozás során felmerülő kivétel a Azure Stream Analyticsban lévő adatfelhasználás során felmerülő katasztrofális hiba. A felhasználó által definiált függvények nagyobb valószínűséggel okozhatnak kivételeket, és a feldolgozás leállhat. A probléma elkerüléséhez használjon egy *try-catch* blokkot a JavaScriptben vagy a C#-ban a kivételek elvégzéséhez a kód végrehajtása során. A kifogott kivételek naplózása és kezelése rendszerhiba miatt sikertelen lehet. Javasoljuk, hogy mindig az egyéni kódot egy *try-catch* blokkba csomagolja, hogy elkerülje a váratlan kivételek ledobását a feldolgozó motorra.

## <a name="next-steps"></a>Következő lépések

* [Felhasználó által definiált JavaScript-függvények Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [JavaScript felhasználó által definiált összesítések Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md)
* [A .NET szabványú, felhasználó által definiált függvények fejlesztése Azure Stream Analytics feladatokhoz](stream-analytics-edge-csharp-udf-methods.md)
* [Azure Stream Analytics integrálása Azure Machine Learning](machine-learning-udf.md)
