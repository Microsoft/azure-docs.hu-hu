---
title: Azure Kinect Body Tracking-kötések
description: Ismerje meg a törzs keretét, az ízületeket, a közös koordinátákat és az Azure Kinect DK közös hierarchiáját.
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, portolás, törzs, nyomon követés, közös, hierarchia, csont, csatlakozás
ms.openlocfilehash: 4cf6ac13a93d0674f9fa144abcc3153a2d7c3350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277324"
---
# <a name="azure-kinect-body-tracking-joints"></a>Azure Kinect Body Tracking-kötések

Az Azure Kinect-törzs nyomon követésével egyszerre több emberi szerv is nyomon követhető. Minden törzs tartalmaz egy azonosítót a keretek és a kinematikus csontváz közötti időbeli korrelációhoz. Az egyes keretekben észlelt szervezetek száma a használatával szerezhető be `k4abt_frame_get_num_bodies()` .

## <a name="joints"></a>Ízületek

A közös pozíció és a tájolás a globális mélységi érzékelők referenciakeretéhez viszonyított becslés. A pozíciót milliméterben kell megadni. A tájolás normalizált quaternion van kifejezve.

## <a name="joint-coordinates"></a>Közös koordináták

Az egyes közös koordináta-rendszerek pozíciója és tájolása. Minden közös koordináta-rendszer abszolút koordináta-rendszer, amely a 3D koordináta-rendszerhez képest teljes.

> [!NOTE]
> A közös koordináták tengelyes tájolással rendelkeznek. A tengely tájolása széles körben használatos a kereskedelmi avatárok, a videojáték-hajtóművek és a renderelési szoftverek használatával. A tengely tájolása leegyszerűsíti a tükrözött mozgások használatát, például a fegyverek 20 fokos növelését.

![Közös koordináták](./media/concepts/joint-coordinates.png)

## <a name="joint-hierarchy"></a>Közös hierarchia

A csontváz 32 ízületeket tartalmaz a törzs közepéről a végtagokra áramló közös hierarchiával. Minden kapcsolat (csont) a szülővel közösen összekapcsolja a fölérendelt gyermeket. Az ábra a közös helyszíneket és a kapcsolatokat mutatja be az emberi törzshöz képest.

![Közös hierarchia](./media/concepts/joint-hierarchy.png)

A következő táblázat a szabványos közös kapcsolatokat sorolja fel.

|Index |Közös név     | Szülő közös   |
|------|---------------|----------------|
| 0    |MEDENCE         | -              |
| 1    |SPINE_NAVAL    | MEDENCE         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |TÖLCSÉRNYAKMAGASSÁG           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |HAND_LEFT      | WRIST_LEFT     |
| 9    |HANDTIP_LEFT   | HAND_LEFT      |
| 10   |THUMB_LEFT     | WRIST_LEFT     |
| 11   |CLAVICLE_RIGHT | SPINE_CHEST    |
| 12   |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 13   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 14   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 15   |HAND_RIGHT     | WRIST_RIGHT    |
| 16   |HANDTIP_RIGHT  | HAND_RIGHT     |
| 17   |THUMB_RIGHT    | WRIST_RIGHT    |
| 18   |HIP_LEFT       | MEDENCE         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | MEDENCE         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | TÖLCSÉRNYAKMAGASSÁG           |
| 27   |ORR           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>Következő lépések

[Testkövetési indexleképezés](body-index-map.md)
