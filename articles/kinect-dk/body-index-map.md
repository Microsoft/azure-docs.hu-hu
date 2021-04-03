---
title: Azure Kinect Body index-Térkép
description: Megtudhatja, hogyan kérdezheti le a Body Tracking index-térképet az Azure Kinect DK-ben.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, portolás, törzs, nyomon követés, index, szegmentálás, Térkép
ms.openlocfilehash: 8c128e59cef515aafb4f59794f0f644b90fd625d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277554"
---
# <a name="azure-kinect-body-tracking-index-map"></a>Az Azure Kinect Body Tracking index térképe

A szövegtörzs-index leképezése tartalmazza az egyes törzsekhez tartozó példány-szegmentálási térképet a részletes kamera rögzítése során. Mindegyik képpont a mélység vagy az IR-rendszerkép megfelelő képpontjára mutat. Az egyes képpontok értéke azt jelöli, hogy a képpont melyik törzshöz tartozik. Ez lehet háttér (érték `K4ABT_BODY_INDEX_MAP_BACKGROUND` ) vagy az észlelt index `k4abt_body_t` .

![Szövegtörzs-index leképezése – példa](./media/concepts/body-index-map.png)

>[!NOTE]
> A törzs indexe eltér a törzs azonosítótól. A törzs azonosítóját lekérdezheti egy adott törzs-indexből az API: [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92)metódus meghívásával.


## <a name="using-body-index-map"></a>A Body index térképe

A rendszer a törzs indexének leképezését tárolja, `k4a_image_t` és a mélységgel vagy az IR-képpel megegyező felbontással rendelkezik. Mindegyik képpont egy 8 bites érték. A hívásával lekérdezhető `k4abt_frame_t` `k4abt_frame_get_body_index_map` . A fejlesztő feladata, hogy meghívja a törzs index-térképének memóriáját `k4a_image_release()` .

## <a name="next-steps"></a>Következő lépések

[Az első testkövetési alkalmazás összeállítása](build-first-body-app.md)
