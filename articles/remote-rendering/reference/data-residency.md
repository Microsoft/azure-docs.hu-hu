---
title: Adattárolási hely
description: Az Azure Remote rendering használatakor az adattárolást ismerteti.
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576893"
---
# <a name="azure-remote-rendering-data-residency"></a>Azure távoli renderelési adattárolás 
Ez a cikk az adattárolási koncepciót ismerteti, valamint azt, hogy miként vonatkozik az Azure-alapú távoli renderelésre. 

## <a name="data-residency"></a>Adattárolási hely 
Az Azure távoli renderelés a felhasználó által megadott Azure Blob-tárolókat használja a Modelles tároláshoz. Ha a modell nincs használatban, a felhasználó az Azure Blob Storage régióban marad. Ha a modellt a rendereléshez használja, a rendszer a felhasználó által a renderelési munkamenet indításakor kiválasztott régióba másolja az adattípust.

## <a name="next-steps"></a>Következő lépések
Ha szeretné megismerni, hogyan lehet Azure Blob Storage-tárolót beállítani az Azure távoli rendereléshez, tekintse meg [a modell konvertálása renderelésre](../quickstarts/convert-model.md)című témakört.
