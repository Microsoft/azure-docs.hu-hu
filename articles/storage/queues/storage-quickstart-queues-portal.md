---
title: 'Rövid útmutató: Azure Storage-üzenetsorok létrehozása a portálon'
description: A Azure Portal hozzon létre egy üzenetsort. Ezt követően a Azure Portal hozzáadhat egy üzenetet, megtekintheti az üzenet tulajdonságait, és leküszteti az üzenetet.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 08/13/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- mode-portal
ms.openlocfilehash: f0e7a5a514f2d68fce025ea9fb354f29fa068561
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534399"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Rövid útmutató: Üzenetsor létrehozása és üzenet hozzáadása a Azure Portal

Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Azure Portal egy üzenetsor létrehozására az Azure Storage-ban, valamint üzenetek hozzáadására és sorba helyezésre. [](https://portal.azure.com/)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Üzenetsor létrehozásához a Azure Portal kövesse az alábbi lépéseket:

1. Az Azure Portalon lépjen az új tárfiókjára.
2. A tárfiók bal oldali menüjében görgessen a bal oldali **Queue Storage,** majd válassza az **Üzenetsorok lehetőséget.**
3. Válassza a **+ Üzenetsor** gombot.
4. Adja meg az új üzenetsor nevét. Az üzenetsor nevének kisbetűkből kell állnia, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat.
6. Kattintson **az OK** gombra az üzenetsor létrehozásához.

    ![Képernyőkép az üzenetsor létrehozásáról a Azure Portal](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Üzenet hozzáadása

Ezután adjon hozzá egy üzenetet az új üzenetsorhoz. Egy üzenet legfeljebb 64 KB méretű lehet.

1. Válassza ki az új üzenetsort a tárfiókban lévő üzenetsorok listájából.
1. Válassza a **+ Üzenet hozzáadása gombot,** hogy üzenetet adjon hozzá az üzenetsorhoz. Írjon be egy üzenetet az **Üzenet szövege mezőbe.**
1. Adja meg, hogy mikor járjon le az üzenet. A Lejárat mezőben 1 másodperc és 7 nap közötti érvényes értékek adva.  Válassza **az Üzenet soha nem jár le** lehetőséget, ha azt az üzenetet jelzi, amely az üzenetsorban marad, amíg explicit módon el nem távolítják.
1. Jelezze, hogy Base64-ként kódolja-e az üzenetet. A bináris adatok kódolása ajánlott.
1. Az üzenet **hozzáadásához** kattintson az OK gombra.

    ![Üzenet üzenetsorhoz való hozzáadását bemutató képernyőkép](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Üzenet tulajdonságainak megtekintése

Miután hozzáadta az üzenetet, a Azure Portal megjeleníti az üzenetsorban lévő összes üzenet listáját. Megtekintheti az üzenet azonosítóját, tartalmát, az üzenet beszúrási idejét és az üzenet lejárati idejét. Azt is láthatja, hogy hányszor lett ez az üzenet eltoltva.

![Képernyőkép az üzenet tulajdonságairól](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Üzenet eltávolítása az üzenetsorból

Az üzenetsor elejéről az üzenetsorból az üzenetsorból lehet Azure Portal. Az üzenetek törlésekor az üzenet törlődik.

A sor eltávolítása mindig eltávolítja a legrégebbi üzenetet az üzenetsorból.

![Képernyőkép az üzenetek a portálról való elküldését bemutató képernyőfelvételről](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre üzenetsort, hogyan adhat hozzá üzenetet, hogyan lehet megtekinteni az üzenettulajdonságokat, és hogyan lehet az üzenetek várólistára Azure Portal.

> [!div class="nextstepaction"]
> [Mi az Azure Queue Storage?](storage-queues-introduction.md)
