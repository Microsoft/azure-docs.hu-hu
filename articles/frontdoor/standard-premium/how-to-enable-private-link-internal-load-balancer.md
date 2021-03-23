---
title: Az Azure bejárati prémium csatlakoztatása belső terheléselosztó-forráshoz privát hivatkozással
titleSuffix: Azure Private Link
description: Megtudhatja, hogyan csatlakoztatható az Azure bejárati prémium egy belső terheléselosztó számára.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: tyao
ms.openlocfilehash: 6876692bcf752570ecdc5d42b65da81992ad3743
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803725"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>Az Azure bejárati prémium csatlakoztatása belső terheléselosztó-forráshoz privát hivatkozással

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure bevezető Premium SKU-t úgy, hogy az Azure Private link Service használatával csatlakozhasson a belső terheléselosztó-forráshoz.

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy [privát hivatkozási szolgáltatást](../../private-link/create-private-link-service-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-private-link-to-an-internal-load-balancer"></a>Privát hivatkozás engedélyezése belső terheléselosztó számára
 
Ebben a szakaszban a Private link Service-t egy Azure-beli bejárati ajtó magánhálózaton létrehozott privát végpontra képezi le. 

1. Az Azure bejárati prémium profiljának *Beállítások* területén válassza a **Origin groups** elemet.

1. Válassza ki azt a származási csoportot, amelyhez engedélyezni szeretné a belső terheléselosztó magánhálózati hivatkozását.

1. A belső terheléselosztó forrásának hozzáadásához válassza a **+ forrás hozzáadása** elemet.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="Képernyőkép a belső Load Balancerhez való privát hivatkozás engedélyezéséről.":::

1. Az **Azure-erőforrások kiválasztásához** válassza **a saját címtárban** lehetőséget. Válassza ki vagy adja meg a következő beállításokat annak a helynek a konfigurálásához, amelyet az Azure bejárati prémium szintűhez szeretne kötni.

    | Beállítás | Érték |
    | ------- | ----- |
    | Region | Válassza ki azt a régiót, amelyik azonos vagy a legközelebb van a forráshoz. |
    | Erőforrás típusa | Válassza a **Microsoft. Network/privateLinkServices** lehetőséget. |
    | Erőforrás | Válassza ki a belső Load Balancerhez kötött privát hivatkozást. |
    | Cél alerőforrás | Hagyja üresen. |
    | Kérelem üzenete | Testreszabhatja az üzenetet, vagy kiválaszthatja az alapértelmezett értéket. |

1. Ezután válassza a **Hozzáadás** , majd a **frissítés** lehetőséget a konfiguráció mentéséhez.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Privát végponti kapcsolatok jóváhagyása a Storage-fiókból

1. Nyissa meg a Private link centert, és válassza a **Private link Services** elemet. Ezután válassza ki a saját hivatkozás nevét.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="Képernyőfelvétel a privát hivatkozások listájáról.":::

1. Válassza a **privát végponti kapcsolatok** lehetőséget a *Beállítások* területen.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="Képernyőkép a privát hivatkozások áttekintése oldalról.":::

1. Válassza ki a *függőben lévő* privát végpontra vonatkozó kérést az Azure bejárati Premiumból, majd válassza a **jóváhagyás** lehetőséget

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="Képernyőkép a privát hivatkozás függőben lévő jóváhagyásáról.":::

1. A jóváhagyást követően az alábbi képernyőképhez hasonlóan kell kinéznie. A kapcsolat teljes körű létrehozása eltarthat néhány percig. Most már hozzáférhet a belső Load Balancerhez az Azure bejárati Premiumból.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="A jóváhagyott privát kapcsolati kérelem képernyőképe.":::

## <a name="next-steps"></a>Következő lépések

További információ a [Private link Service](../../private-link/private-link-service-overview.md)-ről.
