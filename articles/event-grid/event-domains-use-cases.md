---
title: Esemény-tartományokhoz tartozó esetek használata Azure Event Grid
description: Ez a cikk néhány olyan használati esetet ismertet, amelyek az Azure Event Grid-beli Event-tartományok használatát ismertetik.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 00318fc78053ed55e3599c329746d89d2eee4f99
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204345"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Esemény-tartományokhoz tartozó esetek használata Azure Event Grid
Ez a cikk néhány olyan használati esetet ismertet, amelyek az Azure Event Grid-beli Event-tartományok használatát ismertetik. 

## <a name="use-case-1"></a>1. eset használata 
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>2. eset használata
Rendszertémakörök használata esetén legfeljebb 500 esemény-előfizetésre van lehetőség. Ha több mint 500 esemény-előfizetésre van szüksége egy rendszertémakörhöz, használhat tartományokat. 

Tegyük fel, hogy létrehozott egy rendszertémakört egy Azure-Blob Storagehoz, és több mint 500 előfizetést kell létrehoznia a témakörben, de ez a korlátozás miatt nem lehetséges (500 előfizetés/témakör). Ebben az esetben használhatja az alábbi, az Event domaint használó megoldást. 

1. Hozzon létre egy tartományt, amely akár 100 000 témakört is képes támogatni, és az egyes tartományok témakör 500 esemény-előfizetéssel rendelkezhet. Ez a modell 500 * 100 000 esemény-előfizetést biztosít. 
1. Azure Function-előfizetés létrehozása az Azure Storage System témakörben. Ha a függvény események fogadását fogadja az Azure Storage-ból, az képes a megfelelő tartományba tartozó témakörökre bővíteni és közzétenni az eseményeket. A függvény például elemezheti a blob fájlnevét a cél tartomány meghatározásához, és közzéteheti az eseményt a tartományon belüli témakörben. 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="AzureEvent Grid-tartományok – 2. eset":::


## <a name="next-steps"></a>Következő lépések
Az események tartományának beállításával, a témakörök létrehozásával, az esemény-előfizetések létrehozásával és az események közzétételével kapcsolatos további tudnivalókért lásd: események [tartományának kezelése](./how-to-event-domains.md).
