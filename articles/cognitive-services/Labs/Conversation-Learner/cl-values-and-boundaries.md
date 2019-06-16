---
title: Beszélgetés Learner alapértelmezett konfiguráció – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg az alapértelmezett Beszélgetéstanuló konfigurációt.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: ebdc1e1c100329e95bd19359408cb138d233b1c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66385445"
---
# <a name="default-values-and-boundaries"></a>Alapértelmezett értékek és határok

Ez a dokumentum ismerteti az alapértelmezett konfiguráció Beszélgetéstanuló és a kulcs a szolgáltatás határain.

## <a name="default-configuration"></a>Alapértelmezett konfiguráció

Paraméter | Alapértelmezett érték
--- | --- 
Alapértelmezett munkamenet időkorlátja | 30 perc

## <a name="boundaries"></a>Határok

Paraméter | Korlát
--- | --- 
Szerzői API, maximális HTTP hívások / hó | 5M
Szerzői API, maximális HTTP-hívások | 25
Munkamenet API-t, a maximális HTTP-hívások / hó | 500 000
Munkamenet API-t, a maximális HTTP-hívások / másodperc | 10
Egyéni (nem programozott) entitás modell / maximális száma | Lásd: [LUIS határok doc](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries); a gyakorlatban, tényleges szám valamivel kisebb is lehet.
Egyes modellek előre összeállított entitások maximális száma | Lásd: [LUIS határok doc](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries)
Entitások (összesen) egyes modellek maximális száma | 100
Egyes modellek műveletek maximális száma | 32
Egyes modellek train párbeszédpanelek maximális száma | 1000
Felhasználók maximális száma fordulat / train párbeszédpanel | 100
Egyes modellek log párbeszédpanelek maximális száma | Nincs előre beállított korlát, de a napló párbeszédpanelek csak megmaradnak megsemmisülne fix időszakra.  A beszélgetés Learner felhasználói felület ezenkívül megjelenítése a 100 log párbeszédpanelek egyszerre. 
Modellek felhasználónkénti maximális száma | Nincs előre beállított korlát
Nem várakozási soros műveletek maximális száma | 5 (*)

(*) 5 egymást követő nem várakozási művelet után minden nem várakozási művelet maszkolva, és Beszélgetéstanuló elérhető várakozási műveletek közül fog választani.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beszélgetéstanuló használatának első lépései](./quickstart.md)
