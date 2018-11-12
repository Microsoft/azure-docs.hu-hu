---
title: Előrejelezheti állapotát, és a vezetési szokásokat – Azure |} A Microsoft Docs
description: A Cortana Intelligence képességeit használatával valós idejű és prediktív elemzéseket az állapotát és a vezetési szokásokat.
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.openlocfilehash: 0594b9b40fdf8647f99946a31dbb47a95c9694ac
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300353"
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Járműtelemetria-Telemetria elemzési megoldás forgatókönyve

A szuperszámítógépek kikerültek a labor létrehozása, és a garázsok ma már egyenesen. Ezeket most már folyamatban van a legmodernebb tulajdonságkészletét számtalan érzékelők tartalmazó kerül. Érzékelőktől lehetővé teheti a őket nyomon követése és több millió eseményt másodpercenként. Szerint 2020-ra a legtöbb járművek fog csatlakozni az internethez. Koppintson az adatok rengeteg be kínál a nagyobb biztonság, megbízhatóság, és így jobban vezetési élmény. A Microsoft-ben Ez a Cortana Intelligence valóra kelnek.

Cortana Intelligence egy teljesen felügyelt, big data és a speciális analitikai csomag, amely segítségével az adatok átalakítása intelligens műveletekké. A Cortana Intelligence Járműtelemetriai analitikai Megoldássablonját bemutatja, hogyan autó autókereskedők, autó és biztosítási cégek képesek beszerezni a valós idejű és prediktív elemzéseket az állapotát és a vezetési szokásokat.

A megoldás megvalósítása a egy [lambdaarchitektúrát](https://en.wikipedia.org/wiki/Lambda_architecture), amely megjeleníti az összes kihasználja a Cortana Intelligence platform valós idejű kötegelt feldolgozást.

## <a name="architecture"></a>Architektúra
Ez az ábra mutatja be a jármű-Telemetria Analytics megoldás architektúrája:

![A megoldás architektúrájának ábrája](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


Ez a megoldás a Cortana Intelligence következő összetevőket tartalmazza, és bemutatja az integráció:

* **Az Azure Event Hubs** jármű-telemetria-eseményeinek millió feltölti az Azure-bA.
* **Az Azure Stream Analytics** állapotát valós idejű elemzéseket nyújt, és továbbra is fennáll az adatokat pedig hosszú távú tárolással a részletesebb kötegelt feldolgozáshoz.
* **Az Azure Machine Learning** valós időben észleli a rendellenességeket, és használja a kötegelt feldolgozás révén prediktív előrejelzéseket kínál.
* **Az Azure HDInsight** alakítja át a nagy mennyiségű adat.
* **Az Azure Data Factory** kezeli a vezénylési, ütemezését, erőforrás-kezelés és a kötegelt feldolgozási folyamat figyelését.
* **Power bi-ban** biztosít a megoldás egy informatív irányítópulton a valós idejű adatok és prediktív elemzési vizualizációt.

Ez a megoldás két különböző adatforrásokhoz fér hozzá: 

* **Járműtelemetria jelek és diagnosztikai szimulált**: bocsát ki diagnosztikai adatokat, és azt jelzi, hogy felel meg a jármű és a vezetési minta egy adott időpontban állapotát jármű-telematika szimulátort időben. 
* **Járműtelemetria katalógus**: A referencia-adatkészlet VIN számok képez le modellek.

## <a name="next-steps"></a>További lépések

Ismerje meg a megoldás további, lásd: [jármű-Telemetria elemzési megoldás forgatókönyve: részletes ismerje meg, a megoldás](cortana-analytics-playbook-vehicle-telemetry-deep-dive.md).

A Power BI-jelentések és irányítópultok a megoldás konfigurálása kapcsolatban lásd: [jármű Telemetriai Analytics megoldás sablon Power BI-irányítópult beállítási útmutatásai](cortana-analytics-playbook-vehicle-telemetry-powerbi.md).
