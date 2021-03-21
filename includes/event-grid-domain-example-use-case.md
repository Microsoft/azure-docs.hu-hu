---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 03/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2459cf6c5055dcde83dccf37addc160aeeaa64ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198657"
---
Egy példa használatával a rendszer a legkönnyebben ismerteti az események tartományait. Tegyük fel, hogy a contoso építőipari gépeket futtatja, ahol a traktorokat, a kiásási berendezéseket és más nehéz gépeket gyárt. A vállalkozás működtetésének részeként valós idejű információkat küldhet az ügyfeleknek a berendezések karbantartásával, a rendszer állapotával és a szerződések frissítéseivel kapcsolatban. Mindezen információk különböző végpontokra mutatnak, beleértve az alkalmazást, az ügyfél-végpontokat és az ügyfelek által beállított egyéb infrastruktúrát.

Az esemény-tartományok lehetővé teszik a contoso építőipari gépek egyetlen eseményként való modellezését. Minden ügyfelünk a tartományon belüli témakörként jelenik meg. A hitelesítés és az engedélyezés Azure Active Directory használatával kezelhetők. Az ügyfelek előfizethetnek a témára, és megkapják a nekik küldött eseményeket. A felügyelt hozzáférés az esemény-tartományon keresztül biztosítja, hogy csak a témához férhessenek hozzá.

Emellett egyetlen végpontot is biztosít, amely az összes ügyfél-eseményt közzéteheti. Event Grid gondoskodni fog arról, hogy az egyes témakörök csak a bérlőre vonatkozó eseményekről tájékoztassanak.

:::image type="content" source="./media/event-grid-domain-example-use-case/contoso-construction-example.png" alt-text="Contoso építési példa" lightbox="./media/event-grid-domain-example-use-case/contoso-construction-example.png":::