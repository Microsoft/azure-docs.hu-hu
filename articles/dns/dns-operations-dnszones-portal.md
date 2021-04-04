---
title: DNS-zónák kezelése a Azure DNS-Azure Portalban | Microsoft Docs
description: A DNS-zónák a Azure Portal használatával kezelhetők. Ez a cikk ismerteti, hogyan lehet frissíteni, törölni és létrehozni a DNS-zónákat Azure DNS
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 51e17812e1116f1e625685d5a818c18e25cdb2da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965799"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>DNS-zónák kezelése a Azure Portal

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Klasszikus Azure CLI](./dns-operations-dnszones-cli.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Ez a cikk bemutatja, hogyan kezelheti a DNS-zónákat a Azure Portal használatával. A DNS-zónákat a platformfüggetlen [Azure CLI](dns-operations-dnszones-cli.md) vagy az Azure [PowerShell](dns-operations-dnszones.md)használatával is kezelheti.

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. Jelentkezzen be az Azure Portalra
2. A központi menüben navigáljon az **erőforrás létrehozása > hálózatkezelés > DNS-zóna** elemre a **DNS-zóna létrehozása** panel megnyitásához.

    ![DNS-zóna](./media/dns-operations-dnszones-portal/openzone650.png)

4. A **DNS-zóna létrehozása** panelen adja meg a következő értékeket, majd kattintson a **Létrehozás** elemre:


   | **Beállítás** | **Érték** | **Részletek** |
   |---|---|---|
   |**Név**|contoso.com|A DNS-zóna neve|
   |**Előfizetés**|[Az Ön előfizetése]|Válassza ki azt az előfizetést, amelyben létre fogja hozni a DNS-zónát.|
   |**Erőforráscsoport**|**Új létrehozása:** contosoDNSRG|Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül. Az erőforráscsoportokkal kapcsolatos további információkért olvassa el [A Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) áttekintése című cikket.|
   |**Hely**|USA nyugati régiója||

> [!NOTE]
> Az erőforráscsoport az erőforráscsoport helyére vonatkozik, és nincs hatással a DNS-zónára. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

## <a name="list-dns-zones"></a>DNS-zónák listázása

A Azure Portal navigáljon a **További szolgáltatások**  >  **hálózatkezelés**  >  **DNS-zónákhoz**. A DNS-zónák a saját erőforrásai, és a nézetből megtekinthető információk, például a rekordhalmazok és a névkiszolgálók száma. Az oszlopnév- **kiszolgálók** nem az alapértelmezett nézetben vannak. A hozzáadásához kattintson az **oszlopok** elemre **, válassza a névkiszolgálók lehetőséget**, majd kattintson a **kész** gombra.

![DNS-zónák listázása](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>DNS-zóna törlése

Navigáljon egy DNS-zónához a portálon. A **DNS-zóna** panelen kattintson a **zóna törlése** elemre. Ekkor a rendszer felszólítja, hogy erősítse meg, hogy törölni szeretné a DNS-zónát. A DNS-zónák törlésével a zónában található összes rekord is törlődik.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan használhatja a DNS-zónát és-rekordokat [a Azure DNS használatának első lépéseiben a Azure Portal használatával](dns-getstarted-portal.md).