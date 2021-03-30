---
title: Az Azure-beli adatmegosztás díjszabásának ismertetése
description: Ismerje meg, hogyan működik az Azure-beli adatmegosztás díjszabása
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1c2c58e206a70a3801c712df3b5582409712d3c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88136927"
---
# <a name="understand-azure-data-share-pricing"></a>Az Azure-beli adatmegosztás díjszabásának ismertetése

A pillanatkép-alapú megosztáshoz az Azure-beli adatmegosztási díjak az adatkészlet pillanatképének és a pillanatképek végrehajtásának díjai. Ez a cikk azt ismerteti, hogyan lehet megbecsülni az adatkészlet pillanatképét és a pillanatképek végrehajtását a pillanatkép-előzmények információi Jelenleg az adatszolgáltató számlázása az adatkészlet-pillanatkép és a pillanatkép-végrehajtás után történik.

## <a name="dataset-snapshot"></a>Adatkészlet pillanatképe

Az adatkészlet pillanatképe az adatkészlet a forrásról a célhelyre való áthelyezésének művelete. Ha pillanatképet készít egy megosztáshoz, a megosztáson belüli egyes adatkészletek pillanatképe egy adatkészlet-pillanatkép-művelet. Az adatkészlet-Pillanatképek listájának megtekintéséhez kövesse az alábbi lépéseket. 

1. A Azure Portalban navigáljon az adatmegosztási erőforráshoz.

1. Válasszon egy megosztást az elküldött megosztásból vagy a kapott megosztásból.

1. Kattintson a **History (előzmények** ) lapra.

1. Kattintson a pillanatkép kezdő időpontjára.
 
    ![Pillanatkép-előzmények](./media/concepts/concepts-pricing/pricing-snapshot-history.png "Pillanatkép-előzmények") 

1. Az adatkészlet-Pillanatképek műveleteinek listájának megtekintése. Minden sor elem egy adatkészlet-pillanatkép-műveletnek felel meg. Ebben a példában két adatkészlet-pillanatkép található.

    ![Adatkészlet-pillanatkép művelete](./media/concepts/concepts-pricing/pricing-dataset-snapshot.png "Adatkészlet-pillanatkép művelete")

## <a name="snapshot-execution"></a>Pillanatkép-végrehajtás

A pillanatkép-végrehajtás magában foglalja az adatkészletnek a forrásról a célhelyre való áthelyezéséhez szükséges erőforrásokat. Az egyes adatkészlet-pillanatkép-műveletekhez a pillanatkép-végrehajtás kiszámítása a pillanatkép időtartamának szorzata virtuális mag történik. A díjakat a percek alapján kell felkerekíteni. A virtuális mag száma dinamikusan van kiválasztva a forrás-cél pár és az adatminta alapján. Az alábbi lépéseket követve megtekintheti a pillanatkép kezdési idejét, a befejezési időt és a virtuális mag az adatkészlet-Pillanatképek műveletéhez.

1. A Azure Portalban navigáljon az adatmegosztási erőforráshoz.

1. Válasszon egy megosztást az elküldött megosztásból vagy a kapott megosztásból.

1. Kattintson a **History (előzmények** ) lapra.

1. Kattintson a pillanatkép kezdő időpontjára.

    ![Pillanatkép-előzmények](./media/concepts/concepts-pricing/pricing-snapshot-history.png "Pillanatkép-előzmények") 

1. Kattintson az adatkészlet-pillanatkép művelet állapotára.

    ![Adatkészlet pillanatképének állapota](./media/concepts/concepts-pricing/pricing-snapshot-status.png "Adatkészlet pillanatképének állapota")

1. Megtekintheti az adatkészlet-pillanatkép műveletéhez használt kezdési időt, befejezési időt és virtuális mag. 

    ![Pillanatkép-végrehajtás](./media/concepts/concepts-pricing/pricing-snapshot-execution.png "Pillanatkép-végrehajtás")

## <a name="next-steps"></a>Következő lépések

- A legfrissebb díjszabási információk beszerzése – [Az Azure-adatmegosztás díjszabása](https://azure.microsoft.com/pricing/details/data-share/)
- Az Azure díjszabási kalkulátor használata a Cost- [Azure díjszabási kalkulátorának](https://azure.microsoft.com/pricing/calculator/) becsléséhez
