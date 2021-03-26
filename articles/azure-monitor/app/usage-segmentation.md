---
title: Felhasználók, munkamenetek és események elemzése az Azure Application Insights
description: A webalkalmazás felhasználóinak demográfiai elemzése.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 6eb91734afac81e103cebea48865793fa687ad71
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024829"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Felhasználók, munkamenetek és események elemzése Application Insights

Megtudhatja, hogy mikor használják a felhasználók a webalkalmazást, mely oldalakat érdeklik leginkább, hol találhatók a felhasználók, és milyen böngészőket és operációs rendszereket használnak. Az üzleti és a használati telemetria elemzése az [Azure Application Insights](./app-insights-overview.md)használatával.

![Képernyőkép Application Insights felhasználókról](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Bevezetés

Ha még nem látja az adatokat a Application Insights-portálon található felhasználók, munkamenetek vagy események paneleken, [Ismerkedjen meg a használati eszközök használatának első lépéseivel](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>A felhasználók, munkamenetek és események szegmentálására szolgáló eszköz

A használati pengék három szemszögből ugyanazt az eszközt használják a webalkalmazásból származó telemetria és-kockákra három perspektívából. Az adatok szűrésével és felosztásával feltárhatja a különböző lapok és szolgáltatások relatív használatát.

* **Felhasználók eszköz**: hány ember használta az alkalmazást és annak funkcióit.  A felhasználókat a böngésző cookie-jai tárolt névtelen azonosítók használatával számoljuk el. A különböző böngészőket vagy számítógépeket használó egyetlen személy több felhasználónak is számít.
* **Munkamenetek eszköz**: a felhasználói tevékenység hány munkamenete tartalmazta az alkalmazás egyes lapjait és funkcióit. A munkamenetek a felhasználói tétlenség fél órája vagy 24 órányi folyamatos használat után számítanak.
* **Events Tool (események) eszköz**: az alkalmazás egyes oldalai és funkciói milyen gyakran használatosak. Az oldal nézet akkor számít, ha egy böngésző betölt egy oldalt az alkalmazásból, ha már van ilyen [eszköz](./javascript.md). 

    Egy egyéni esemény az alkalmazásban előforduló egyik előfordulást jelképezi, gyakran egy felhasználói interakciót, például egy kattintással vagy egy feladat befejezését. Az alkalmazásban programkódot szúrhat be [Egyéni események létrehozásához](./api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Bizonyos felhasználók lekérdezése

A felhasználók eszköz tetején található lekérdezési beállítások módosításával megismerheti a felhasználók különböző csoportjait:

* Megjelenítés: válassza ki az elemezni kívánt felhasználók kohorszát.
* Ki használt: válassza az egyéni események és oldalletöltések lehetőséget.
* Ideje: válasszon időtartományt.
* Szerint: válassza ki, hogyan szeretné felvenni az adatgyűjtőt egy adott időtartam vagy egy másik tulajdonság (például böngésző vagy város) alapján.
* Felosztási szempont: válasszon ki egy tulajdonságot, amellyel feloszthatja vagy szegmentálhatja az adatgyűjtést. 
* Szűrők hozzáadása: a lekérdezés korlátozása bizonyos felhasználóknak, munkameneteknek vagy eseményeknek a tulajdonságaik (például böngésző vagy város) alapján. 
 
## <a name="saving-and-sharing-reports"></a>Jelentések mentése és megosztása 
A felhasználók jelentéseit mentheti saját maga is a Saját jelentések szakaszban, vagy megoszthatja mindenki mással, hogy hozzáférjen ehhez a Application Insights erőforráshoz a megosztott jelentések szakaszban.

Felhasználókra, munkamenetekre vagy eseményekre vonatkozó jelentésre mutató hivatkozás megosztása; kattintson az eszköztár **megosztás** elemére, majd másolja a hivatkozást.

A felhasználók, munkamenetek vagy események jelentésében tárolt adatmásolatok megosztása; kattintson a **megosztás** lehetőségre az eszköztáron, majd kattintson a **Word ikonra** egy olyan Word-dokumentum létrehozásához, amely az adott adattal rendelkezik. Vagy kattintson a fő diagram fölötti **szó ikonra** .

## <a name="meet-your-users"></a>Ismerkedjen meg a felhasználókkal

A **felhasználók** betartása szakasz az aktuális lekérdezéssel megegyező öt minta felhasználó adatait jeleníti meg. Az összesítések mellett figyelembe kell venni és meg kell vizsgálni az egyének viselkedését, és betekintést nyerhet arról, hogy a felhasználók miként használják ténylegesen az alkalmazást.

## <a name="next-steps"></a>Következő lépések

- A használati tapasztalatok engedélyezéséhez kezdjen el [Egyéni eseményeket](./api-custom-events-metrics.md#trackevent) vagy [oldalletöltések](./api-custom-events-metrics.md#page-views)küldését.
- Ha már elküldte az egyéni eseményeket vagy a lapok nézeteit, tekintse meg a használati eszközöket, amelyekkel megismerheti, hogy a felhasználók miként használják a szolgáltatást.
    - [Tölcsérek](usage-funnels.md)
    - [Visszatartás](usage-retention.md)
    - [Felhasználókövetés](usage-flows.md)
    - [Munkafüzetek](../visualize/workbooks-overview.md)
    - [Felhasználói környezet hozzáadása](./usage-overview.md)