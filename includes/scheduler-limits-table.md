---
title: fájl belefoglalása
description: fájl belefoglalása
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: eb13d889cb72911e2268b7538a74336befe3320b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75392375"
---
A következő táblázat ismerteti az Azure Scheduler főbb kvótáit, korlátozásait, alapértelmezéseit és szabályozásait.

| Erőforrás | Korlát leírása |
| -------- | ----------------- |
| **Feladatok mérete** | A feladatok maximális mérete 16 000. Ha egy PUT vagy egy javítási művelet a korlátnál nagyobb méretű feladatot eredményez, a rendszer a 400 hibás kérelem állapotkódot adja vissza. | 
| **Feladatok gyűjteményei** | Az Azure-előfizetések száma legfeljebb 200 000. | 
| **Feladatok gyűjtemény szerint** | Alapértelmezés szerint a feladatok maximális száma öt feladat az ingyenes és a 50-es feladatok esetében egy normál feladatban. A feladatütemezés feladatainak maximális számát módosíthatja. A feladatütemezés összes feladata a feladat-gyűjteményben beállított értékre van korlátozva. Ha a feladatok maximális kvótája helyett több feladatot próbál létrehozni, a kérelem 409-as ütközési állapotkód esetén meghiúsul. | 
| **Kezdési idő** | A "kezdési idő" legfeljebb 18 hónap lehet. |
| **Ismétlődési tartomány** | A maximális ismétlődési időtartam 18 hónap. | 
| **Gyakoriság** | Alapértelmezés szerint a maximális gyakorisági kvóta egy óra az ingyenes feladatokban, és egy perc a standard szintű webhelycsoportban. <p>A maximális gyakoriságot a feladatokhoz a maximálisnál alacsonyabb értékre teheti. A feladat-gyűjteményben lévő összes feladat csak a feladat gyűjteményében beállított értékre korlátozódik. Ha a feladathoz a maximális gyakoriságnál nagyobb gyakorisággal próbál felvenni egy feladatot, a kérelem a 409-as ütközési állapotkód miatt meghiúsul. | 
| **Törzs mérete** | Egy kérelem maximális törzsének mérete 8 192 karakter. |
| **Kérelem URL-mérete** | A kérelem URL-címének maximális mérete 2 048 karakter. |
| **Fejlécek száma** | A fejlécek maximális száma 50 fejléc. | 
| **Összesített fejléc mérete** | Az összesített fejléc maximális mérete 4 096 karakter. |
| **Időtúllépés** | A kérés időtúllépése statikus, azaz nem konfigurálható. és 60 másodperc a HTTP-műveleteknél. A már futó műveletekhez kövesse a HTTP aszinkron protokollokat. Tegyük fel például, hogy a 202 azonnal visszaküldi, de a háttérben folytatja a munkát. | 
| **Feladatelőzmények** | A feladatok előzményeiben tárolt maximális válasz törzse 2 048 bájt. |
| **Feladatok előzményeinek megőrzése** | A feladatok előzményeit legfeljebb két hónapig, vagy akár az utolsó 1 000-végrehajtásig őrzi meg a rendszer. | 
| **Befejezett és hibás feladatok megőrzése** | A befejezett és a hibás feladatok 60 napig tartanak. |
||| 

