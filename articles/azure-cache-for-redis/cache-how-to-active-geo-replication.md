---
title: Az aktív geo-replikáció konfigurálása a Redis-példányok Enterprise Azure cache-hez
description: Ismerje meg, hogyan replikálhatja az Azure cache-t az Azure-régiók Redis nagyvállalati példányaihoz
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: fe777c3aa7b314dc56a42cc64712d18281a6ea7d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121167"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Az aktív geo-replikáció konfigurálása a Redis-példányok Enterprise Azure cache-hez (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy aktív földrajzilag replikált Azure-gyorsítótárat a Azure Portal használatával.

Az aktív geo-replikációs csoportok két vagy több Enterprise Azure cache-t Redis példányok számára egyetlen, az Azure-régiókra kiterjedő gyorsítótárban. Az összes példány helyi elsődlegesként működik. Az alkalmazások határozzák meg, hogy mely példány (ok) használható az olvasási és írási kérésekhez.

## <a name="create-or-join-an-active-geo-replication-group"></a>Aktív geo-replikációs csoport létrehozása vagy csatlakoztatása

> [!IMPORTANT]
> Az aktív geo-replikációt engedélyezni kell a Redis Azure-gyorsítótárának létrehozásakor.
>
>

1. Az **új Redis cache** LÉTREHOZÁSI felhasználói felületén kattintson a **Konfigurálás** elemre az **aktív geo-replikálás** beállításához a **speciális** lapon.

    ![Aktív georeplikáció konfigurálása](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. Hozzon létre egy új replikációs csoportot az első gyorsítótár-példányhoz, vagy válasszon ki egy meglévőt a listából.

    ![Csatolási gyorsítótárak](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Kattintson a **Konfigurálás** gombra a befejezéshez.

    ![Aktív geo-replikáció konfigurálva](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Ismételje meg a fenti lépéseket a Geo-replikáció csoportban található minden további gyorsítótár-példánynál.

## <a name="remove-from-an-active-geo-replication-group"></a>Eltávolítás aktív geo-replikálási csoportból

Ha egy gyorsítótár-példányt szeretne eltávolítani egy aktív geo-replikálási csoportból, egyszerűen törölje a példányt. A többi példány automatikusan újrakonfigurálja magukat.

## <a name="next-steps"></a>Következő lépések

További információ az Azure cache Redis szolgáltatásairól.

* [Azure cache a Redis szolgáltatási szintjeihez](cache-overview.md#service-tiers)
* [Magas rendelkezésre állás az Azure cache Redis](cache-high-availability.md)
