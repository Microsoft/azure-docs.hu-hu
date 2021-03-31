---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96018363"
---
Jegyezze fel a **valószínűségi küszöb** csúszkát a **teljesítmény** lap bal oldali paneljén. Ez az a megbízhatósági szint, amelyet az előrejelzésnek megfelelőnek kell lennie ahhoz, hogy helyesnek lehessen tekinteni (a pontosság és a visszahívás kiszámítása céljából). 

Ha magas valószínűségi küszöbértékkel értelmezi az előrejelzési hívásokat, a rendszer általában nagy pontossággal adja vissza az eredményeket az &mdash; észlelt besorolások visszahívásának rovására, de sok továbbra is észrevétlen marad. Az alacsony valószínűségi küszöbérték a &mdash; tényleges besorolások túlnyomó többségét észleli, de a készleten belül még több hamis pozitív érték van. Ezt szem előtt tartva a valószínűségi küszöbértéket a projekt konkrét igényeinek megfelelően kell beállítania. Később, amikor az ügyfél oldalán az előrejelzési eredményeket kapja, ugyanazt a valószínűségi küszöbértéket kell használnia, mint az itt használt értéket.