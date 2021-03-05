---
title: Az Azure bejárati prémium csatlakoztatása a Storage-fiók forrásához privát hivatkozással
titleSuffix: Azure Private Link
description: Megtudhatja, hogyan csatlakoztatható az Azure bejárati prémium a Storage-fiókhoz magántulajdonban.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/04/2021
ms.author: tyao
ms.openlocfilehash: 885582481e3783bdd4fbad40a24499f42a40ce24
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193448"
---
# <a name="connect-azure-front-door-premium-to-a-storage-account-origin-with-private-link"></a>Az Azure bejárati prémium csatlakoztatása a Storage-fiók forrásához privát hivatkozással

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure-beli bevezető prémium SKU-t, hogy az Azure Private link Service-en keresztül csatlakozhasson a Storage-fiók forrásához.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-private-link-to-a-storage-account-in-azure-front-door-premium"></a>Privát hivatkozás engedélyezése a Storage-fiókhoz az Azure bejárati Premiumban
 
Ebben a szakaszban a Private link Service-t egy Azure-beli bejárati ajtó magánhálózaton létrehozott privát végpontra képezi le. 

1. Az Azure bejárati prémium profiljának *Beállítások* területén válassza a **Origin groups** elemet.

1. Válassza ki a forrás csoportot, amely tartalmazza azt a Storage-fiók forrását, amelyhez engedélyezni szeretné a magánhálózati hivatkozást.

1. Válassza a **+ forrás hozzáadása lehetőséget** egy új Storage-fiók forrásának hozzáadásához, vagy válasszon egy korábban létrehozott Storage-fiók forrását a listából.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-storage-account.png" alt-text="Képernyőkép: privát hivatkozás engedélyezése egy Storage-fiókra.":::

1. Az **Azure-erőforrások kiválasztásához** válassza **a saját címtárban** lehetőséget. Válassza ki vagy adja meg a következő beállításokat annak a helynek a konfigurálásához, amelyet az Azure bejárati prémium szintűhez szeretne kötni.

    | Beállítás | Érték |
    | ------- | ----- |
    | Region | Válassza ki azt a régiót, amelyik azonos vagy a legközelebb van a forráshoz. |
    | Erőforrás típusa | Válassza a **Microsoft. Storage/storageAccounts** elemet. |
    | Erőforrás | Válassza ki a tárfiókot. |
    | Cél alerőforrás | A *blob* vagy a *web* lehetőséget választhatja. |
    | Kérelem üzenete | Testreszabhatja az üzenetet, vagy kiválaszthatja az alapértelmezett értéket. |

1. Ezután válassza a **Hozzáadás** lehetőséget a konfiguráció mentéséhez.

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-the-storage-account"></a>Az Azure bejárati ajtó Premium Private Endpoint-kapcsolatainak jóváhagyása a Storage-fiókból

1. Nyissa meg azt a Storage-fiókot, amelyet az utolsó szakaszban a privát hivatkozással konfigurál. Válassza a **hálózatkezelés** lehetőséget a **Beállítások** területen.

1. A **hálózatkezelés** területen válassza a **privát végponti kapcsolatok** elemet. 

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/storage-account-configure-endpoint.png" alt-text="Képernyőkép a webes alkalmazás hálózati beállításairól.":::

1. Válassza ki a *függőben lévő* privát végpontra vonatkozó kérést az Azure bejárati Premiumból, majd válassza a **jóváhagyás** lehetőséget

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-pending-approval.png" alt-text="Képernyőkép a függőben lévő Storage privát végponti kérelméről.":::

1. A jóváhagyást követően az alábbi képernyőképhez hasonlóan kell kinéznie. A kapcsolat teljes körű létrehozása eltarthat néhány percig. Mostantól elérhetővé teheti a Storage-fiókját az Azure bejárati Premiumból.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="A jóváhagyott tárolási végpontra vonatkozó kérelem képernyőképe.":::

## <a name="next-steps"></a>Következő lépések

Ismerje meg a [Storage-fiókkal rendelkező Private link Service-t](../../storage/common/storage-private-endpoints.md).
