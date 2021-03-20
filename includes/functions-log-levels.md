---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 72588ccfd9ad00305cbdeaf8132999731d86b9b1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92164912"
---
A naplózási *szint* minden naplóhoz hozzá van rendelve. Az érték egy egész szám, amely a relatív fontosságot jelzi:

|Naplózási szint    |Code| Description |
|------------|---|--------------|
|Nyomkövetés       | 0 |A legrészletesebb üzeneteket tartalmazó naplók. Ezek az üzenetek bizalmas alkalmazási adatokat is tartalmazhatnak. Ezek az üzenetek alapértelmezés szerint le vannak tiltva, és soha nem lehet engedélyezni éles környezetben.|
|Hibakeresés       | 1 | A fejlesztés során az interaktív vizsgálathoz használt naplók. Ezeknek a naplóknak elsősorban a hibakereséshez hasznos információkkal kell rendelkezniük, és nem tartalmazhatnak hosszú távú értéket. |
|Tájékoztatás | 2 | Az alkalmazás általános folyamatát nyomon követő naplók. Ezeknek a naplóknak hosszú távú értékkel kell rendelkezniük. |
|Figyelmeztetés     | 3 | A rendellenes vagy váratlan eseményt kiemelő naplók az alkalmazás folyamatában, de egyébként nem okozzák az alkalmazás futtatásának leállását. |
|Hiba       | 4 | Azokat a naplókat jeleníti meg, amelyek akkor jelennek meg, ha egy hiba miatt leáll a jelenlegi végrehajtás folyamata. Ezeknek a hibáknak az aktuális tevékenység hibáját kell jelezniük, nem pedig az egész alkalmazásra vonatkozó hibát. |
|Kritikus    | 5 | A helyreállíthatatlan alkalmazások vagy rendszerösszeomlások leírását vagy az azonnali beavatkozást igénylő katasztrofális hibát jelző naplók. |
|Nincsenek        | 6 | Letiltja a naplózást a megadott kategóriához. |

A [ *host.jsa* fájl](../articles/azure-functions/functions-host-json.md) konfigurációjában meghatározza, hogy Mennyibe kerül a functions alkalmazás a Application Insights.  
