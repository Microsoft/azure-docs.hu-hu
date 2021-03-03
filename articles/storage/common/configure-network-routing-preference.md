---
title: Hálózati útválasztási beállítás konfigurálása
titleSuffix: Azure Storage
description: Konfigurálja az Azure Storage-fiók hálózati útválasztási beállításait annak megadásához, hogy a hálózati forgalom hogyan legyen átirányítva a fiókjához az interneten keresztül.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/21/2020
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fb427de170764e5cd1fca57f9fb2d1410829e521
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700842"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Hálózati útválasztási beállítások konfigurálása az Azure Storage-hoz

Ez a cikk azt ismerteti, hogyan konfigurálhatja a hálózati útválasztási beállításokat és az adott útvonalhoz tartozó végpontokat a Storage-fiókhoz. 

A hálózati útválasztási beállítások azt határozzák meg, hogy a hálózati forgalom hogyan legyen átirányítva a fiókjához az interneten keresztül. Az útvonal-specifikus végpontok olyan új végpontok, amelyeket az Azure Storage hoz létre a Storage-fiókhoz. Ezek a végpontok az alapértelmezett útválasztási beállítások módosítása nélkül irányítják át a forgalmat a kívánt útvonalon. További információ: [hálózati útválasztási preferencia az Azure Storage](network-routing-preference.md)-hoz.

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Az alapértelmezett nyilvános végpont útválasztási beállításainak konfigurálása

Alapértelmezés szerint a Storage-fiók nyilvános végpontjának útválasztási beállításai a Microsoft globális hálózat értékre vannak beállítva. A Microsoft globális hálózat és az internetes útválasztás lehetőség közül választhat alapértelmezett útválasztási előnyként a Storage-fiók nyilvános végpontja számára. Ha többet szeretne megtudni a két típusú útválasztás közötti különbségről, tekintse meg az Azure Storage-hoz készült [hálózati útválasztási beállítások](network-routing-preference.md)című témakört. 

Az útválasztási preferencia módosítása az internetes útválasztásra:

1. Navigáljon a Storage-fiókjához a portálon.

2. A **Beállítások** területen válassza a **hálózatkezelés** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Hálózatkezelés menüpont](./media/configure-network-routing-preference/networking-option.png)

3.  A **tűzfalak és virtuális hálózatok** lapon a **hálózati útválasztás** területen módosítsa az **útválasztási** beállítások beállítást az **Internet-útválasztás** értékre.

4.  Kattintson a **Mentés** gombra.

    > [!div class="mx-imgBorder"]
    > ![internetes útválasztási beállítás](./media/configure-network-routing-preference/internet-routing-option.png)

## <a name="configure-a-route-specific-endpoint"></a>Útvonal-specifikus végpont konfigurálása

Egy útvonal-specifikus végpont is konfigurálható. Megadhatja például az alapértelmezett végpont útválasztási beállításait az *internetes útválasztás* beállításnál, majd közzétehet egy útvonal-specifikus végpontot, amely lehetővé teszi az interneten lévő ügyfelek és a Storage-fiók közötti adatforgalmat a Microsoft globális hálózatán keresztül.

1.  Navigáljon a Storage-fiókjához a portálon.

2.  A **Beállítások** területen válassza a **hálózatkezelés** lehetőséget.

3.  A **tűzfalak és virtuális hálózatok** lap **útvonal-specifikus végpontok közzététele** területén válassza az útvonal-specifikus végpont útválasztási beállításait, majd kattintson a **Mentés** gombra. Ez a beállítás csak az útvonal-specifikus végpontra van hatással. Ez a beállítás nem befolyásolja az alapértelmezett útválasztási beállítást.  

    A következő képen a kiválasztott **Microsoft hálózati útválasztási** lehetőség látható.

    > [!div class="mx-imgBorder"]
    > ![Microsoft hálózati útválasztási lehetőség](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>A végpont nevének megkeresése egy adott útválasztási végponthoz

Ha egy útvonal-specifikus végpontot konfigurált, a végpontot a Storage-fiók tulajdonságainál találja.

1.  A **Beállítások** területen válassza a **Tulajdonságok** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Tulajdonságok menü lehetőség](./media/configure-network-routing-preference/properties.png)

2.  A **Microsoft hálózati útválasztási** végpontja minden olyan szolgáltatás esetében megjelenik, amely támogatja az útválasztási beállításokat. Ez a rendszerkép a blob és a Fájlszolgáltatások végpontját jeleníti meg.

    > [!div class="mx-imgBorder"]
    > ![Microsoft hálózati útválasztási beállítás az útválasztási specifikus végpontokhoz](./media/configure-network-routing-preference/routing-url.png)


## <a name="see-also"></a>Lásd még

- [Hálózati útválasztási beállítások](network-routing-preference.md)
- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](storage-network-security.md)
- [Biztonsági javaslatok a blob Storage-hoz](../blobs/security-recommendations.md)
