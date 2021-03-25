---
title: Az aktív geo-replikáció konfigurálása a Redis-példányok Enterprise Azure cache-hez
description: Ismerje meg, hogyan replikálhatja az Azure cache-t az Azure-régiók Redis nagyvállalati példányaihoz
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 3fe3131263d3cf1984eae1692854d8d6bcd2746a
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109488"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Az aktív geo-replikáció konfigurálása a Redis-példányok Enterprise Azure cache-hez (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy aktív földrajzilag replikált Azure-gyorsítótárat a Azure Portal használatával.

Az aktív geo-replikációs csoportok két vagy több Enterprise Azure cache-t Redis példányok számára egyetlen, az Azure-régiókra kiterjedő gyorsítótárban. Az összes példány helyi elsődlegesként működik. Az alkalmazások határozzák meg, hogy mely példány (ok) használható az olvasási és írási kérésekhez.

## <a name="create-or-join-an-active-geo-replication-group"></a>Aktív geo-replikációs csoport létrehozása vagy csatlakoztatása

> [!IMPORTANT]
> Az aktív geo-replikációt engedélyezni kell a Redis Azure-gyorsítótárának létrehozásakor.
>
>

1. Az **új Redis cache** LÉTREHOZÁSI felhasználói felület **speciális** lapján válassza a **vállalat** lehetőséget a **fürtözési házirendhez**.

    ![Aktív georeplikáció konfigurálása](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. Az **aktív földrajzi replikálás** beállításához kattintson a **Konfigurálás** elemre.

1. Hozzon létre egy új replikációs csoportot az első gyorsítótár-példányhoz, vagy válasszon ki egy meglévőt a listából.

    ![Csatolási gyorsítótárak](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Kattintson a **Konfigurálás** gombra a befejezéshez.

    ![Aktív geo-replikáció konfigurálva](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Várjon, amíg az első gyorsítótár sikeresen létrejött. Ismételje meg a fenti lépéseket a Geo-replikáció csoportban található minden további gyorsítótár-példánynál.

## <a name="remove-from-an-active-geo-replication-group"></a>Eltávolítás aktív geo-replikálási csoportból

Ha egy gyorsítótár-példányt szeretne eltávolítani egy aktív geo-replikálási csoportból, egyszerűen törölje a példányt. A többi példány automatikusan újrakonfigurálja magukat.

## <a name="next-steps"></a>Következő lépések

További információ az Azure cache Redis szolgáltatásairól.

* [Azure cache a Redis szolgáltatási szintjeihez](cache-overview.md#service-tiers)
* [Magas rendelkezésre állás az Azure cache Redis](cache-high-availability.md)
