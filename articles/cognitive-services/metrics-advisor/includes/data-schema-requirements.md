---
title: Az Adatséma követelményei
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 4c55c25621df1925b6ed6c374d8af88551eb1e46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96231429"
---
A metrikai tanácsadó a Time Series anomália észlelésének, diagnosztizálásának és elemzésének egyik szolgáltatása. AI-alapú szolgáltatásként az adatait a felhasznált modell betanítására használják. A szolgáltatás az összesített adatokat tartalmazó táblákat a következő oszlopokkal fogadja el:

* **Mérték** (kötelező): egy vagy több numerikus értékeket tartalmazó oszlop.
* **Időbélyeg** (nem kötelező): nulla vagy egy oszlop, amelynek típusa `DateTime` vagy `String` . Ha ez az oszlop nincs beállítva, az időbélyegző az egyes betöltési időszakok kezdő időpontjaként van beállítva. Az időbélyeg formázása: `yyyy-MM-ddTHH:mm:ssZ` . 
  * Az **időbélyegnek meg kell egyeznie a metrika részletességével. A napi metrika például biztosítja az óra, a perc és a másodperc értékét a következőként megjelölt `00:00:00` időbélyegen:**.
* **Dimenzió** (nem kötelező): az oszlopok bármilyen adattípus lehet. Legyen körültekintő, ha nagy mennyiségű oszlopot és értéket használ, hogy megakadályozza a dimenziók feldolgozásának túlzott számát.

> [!Note]
> Minden metrika esetében csak egy időbélyeg lehet, amely egy dimenzió kombinációnak felel meg. Összesítheti az adatokat a bevezetést megelőzően, vagy a lekérdezés használatával megadhatja a betöltött adatokat.