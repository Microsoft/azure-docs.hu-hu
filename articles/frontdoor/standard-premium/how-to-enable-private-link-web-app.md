---
title: Az Azure bejárati prémium csatlakoztatása egy webalkalmazás-forráshoz privát hivatkozással
titleSuffix: Azure Private Link
description: Ismerje meg, hogyan kapcsolódhat az Azure bejárati Premiumhoz a webapphoz.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 805c3ba0360fcffe2bfd4217c0ef625fe61e5d64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030579"
---
# <a name="connect-azure-front-door-premium-to-a-web-app-origin-with-private-link"></a>Az Azure bejárati prémium csatlakoztatása egy webalkalmazás-forráshoz privát hivatkozással

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure bevezető prémium SKU-t úgy, hogy az Azure Private link Service használatával saját webalkalmazásához kapcsolódjon.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Hozzon létre egy [privát hivatkozási](../../private-link/create-private-link-service-portal.md) szolgáltatást a forrásként szolgáló webkiszolgálók számára.

> [!Note]
> A privát végpontok nyilvános régiókban érhetők el a PremiumV2, a PremiumV3 Windows Web Apps, a Linux Web Apps és a Azure Functions Premium csomag (más néven a rugalmas Prémium csomag) számára.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-private-link-to-a-web-app-in-azure-front-door-premium"></a>Privát hivatkozás engedélyezése webalkalmazáshoz az Azure bejárati Premiumban
 
Ebben a szakaszban a Private link Service-t egy Azure-beli bejárati ajtó magánhálózaton létrehozott privát végpontra képezi le. 

1. Az Azure bejárati prémium profiljának *Beállítások* területén válassza a **Origin groups** elemet.

1. Válassza ki azt a kezdőpontot, amely tartalmazza azt a webalkalmazási forrást, amelyhez engedélyezni kívánja a saját hivatkozását.

1. Válassza a **+ forrás hozzáadása lehetőséget** egy új webalkalmazás-forrás hozzáadásához, vagy válasszon egy korábban létrehozott webalkalmazás-forrást a listából.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-web-app.png" alt-text="Képernyőkép: privát hivatkozás engedélyezése webalkalmazásra.":::

1. Az **Azure-erőforrások kiválasztásához** válassza **a saját címtárban** lehetőséget. Válassza ki vagy adja meg a következő beállításokat annak a helynek a konfigurálásához, amelyet az Azure bejárati prémium szintűhez szeretne kötni.

    | Beállítás | Érték |
    | ------- | ----- |
    | Region | Válassza ki azt a régiót, amelyik azonos vagy a legközelebb van a forráshoz. |
    | Erőforrás típusa | Válassza a **Microsoft. Web/Sites** lehetőséget. |
    | Erőforrás | Válassza a **myPrivateLinkService** lehetőséget. |
    | Cél alerőforrás | helyek |
    | Kérelem üzenete | Testreszabhatja az üzenetet, vagy kiválaszthatja az alapértelmezett értéket. |

1. Ezután válassza a **Hozzáadás** lehetőséget a konfiguráció mentéséhez.

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-web-app"></a>Az Azure bejárati ajtó Premium Private Endpoint-kapcsolatának jóváhagyása a webalkalmazásból

1. Nyissa meg a webalkalmazást, amely a saját privát hivatkozását adja meg az utolsó szakaszban. Válassza a **hálózatkezelés** lehetőséget a **Beállítások** területen.

1. A **hálózatkezelés** területen válassza **a privát végponti kapcsolatok konfigurálása** lehetőséget.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/web-app-configure-endpoint.png" alt-text="Képernyőkép a webes alkalmazás hálózati beállításairól.":::

1. Válassza ki a *függőben lévő* privát végpontra vonatkozó kérést az Azure bejárati Premiumból, majd válassza a **jóváhagyás** lehetőséget

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-pending-approval.png" alt-text="Képernyőkép a függőben lévő privát végponti kérelemről.":::

1. A jóváhagyást követően az alábbi képernyőképhez hasonlóan kell kinéznie. A kapcsolat teljes körű létrehozása eltarthat néhány percig. Mostantól elérheti a webalkalmazását az Azure bejárati Premium szolgáltatásával. A webes alkalmazás a nyilvános internetről való közvetlen hozzáférése le lesz tiltva, miután a privát végpont engedélyezve lesz.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-approved.png" alt-text="A jóváhagyott végpontra vonatkozó kérelem képernyőképe.":::

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg az [Azure-webalkalmazással való privát link Service szolgáltatással](../../app-service/networking/private-endpoint.md).
