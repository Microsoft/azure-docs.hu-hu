---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: lajanuar
ms.openlocfilehash: 89b035397ea2050ae7e61f2a19310b6a7fb4192c
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467246"
---
Vizsgálja `"confidence"` meg az egyes kulcsok/értékek eredményének értékét a `"pageResults"` csomópont alatt. Tekintse meg a csomópontban található megbízhatósági pontszámokat is `"readResults"` , amelyek a szöveges olvasási műveletnek felelnek meg. Az olvasási eredmények megbízhatósága nem befolyásolja a kulcs/érték kinyerési eredményeinek megbízhatóságát, ezért mindkettőt ellenőriznie kell.
* Ha az olvasási művelet megbízhatósági pontszáma alacsony, próbálja meg javítani a bemeneti dokumentumok minőségét (lásd a [bemeneti követelményeket](../overview.md#input-requirements)).
* Ha a kulcs/érték kibontási műveletének megbízhatósági pontszáma alacsony, ügyeljen arra, hogy az elemzett dokumentumok ugyanolyan típusúak legyenek, mint a betanítási készletben használt dokumentumok. Ha a betanítási készlet dokumentumaiban változások vannak kialakítva, érdemes lehet különböző mappákba bontani őket, és minden egyes változathoz egy modellt betanítani.

A megcélzott megbízhatósági pontszámok a használati esettől függenek, de általában jó gyakorlat a 80%-os vagy újabb pontszám megcélzására. Az olyan érzékeny esetekben, mint például az orvosi rekordok vagy a számlázási utasítások beolvasása, 100%-os pontszám ajánlott.