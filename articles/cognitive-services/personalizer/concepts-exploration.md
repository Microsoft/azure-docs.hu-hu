---
title: Feltárás – személyre szabás
titleSuffix: Azure Cognitive Services
description: A feltárással a személyre szabottan folytathatja a jó eredmények megvalósítását, még a felhasználói viselkedés változásaival is. A feltárási beállítások kiválasztása üzleti döntés a felhasználói interakciók arányos arányáról a modell tökéletesítése érdekében.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 00b9cc881d9414e957d7503bb6156095bb821c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "87128148"
---
# <a name="exploration-and-exploitation"></a>Feltárás és kiaknázás

A feltárással a személyre szabottan folytathatja a jó eredmények megvalósítását, még a felhasználói viselkedés változásaival is.

Ha a megszemélyesítő hívást kap, egy olyan RewardActionID ad vissza, amely a következők egyikét adja meg:
* A az aktuális Machine learning-modell alapján a legvalószínűbb felhasználói viselkedésnek megfelelő kiaknázást használja.
* A feltárást használ, amely nem felel meg a rangsorban a legnagyobb valószínűséggel rendelkező műveletnek.

A személyre szabott funkció jelenleg egy *epszilon mohó* nevű algoritmust használ a megismeréshez. 

## <a name="choosing-an-exploration-setting"></a>Feltárási beállítás kiválasztása

A Azure Portal **Configuration** (személyre szabás) lapján konfigurálhatja a felderítéshez használandó forgalom százalékos arányát. Ez a beállítás határozza meg a feltárást végző rangsorolt hívások százalékos arányát. 

A személyre szabhatja, hogy a rangsorban megjelenő valószínűséggel megvizsgálja vagy kihasználja ezt a valószínűséget. Ez különbözik az egyes olyan/B keretrendszerek működésének, amelyek adott felhasználói azonosítók kezelését zárolják.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Ajánlott eljárások a feltárási beállítások kiválasztásához

A feltárási beállítások kiválasztása üzleti döntés a felhasználói interakciók arányos arányáról a modell tökéletesítése érdekében. 

A nulla beállítás a személyre szabás számos előnyét cáfolja. Ezzel a beállítással a személyre szabás nem használ felhasználói interakciókat a jobb felhasználói interakciók felderítése érdekében. Ez az érték a stagnálás, a drift és a végső teljesítmény modellezését eredményezi.

A túl magas beállítás nem fogja megtagadni a felhasználói viselkedésből való tanulás előnyeit. Ha 100%-ra állítja be az állandó véletlenszerű működést, és a felhasználók által megszerzett bármilyen viselkedés nem befolyásolja az eredményt.

Fontos, hogy ne módosítsa az alkalmazás viselkedését attól függően, hogy látható-e a személyre szabás vagy a kihasználás. Ez olyan torzulásokat eredményezne, amelyek végső soron csökkentik a lehetséges teljesítményt.

## <a name="next-steps"></a>Következő lépések

[Megerősítő tanulás](concepts-reinforcement-learning.md) 