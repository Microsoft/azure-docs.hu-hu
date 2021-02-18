---
title: Az Azure bejárati prémium csatlakoztatása saját forráshoz privát hivatkozással
description: Megtudhatja, hogyan csatlakoztathatja az Azure bejárati prémiumot a saját forrásához a Azure Portal használatával.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 3015a0560171b61aaab05e27b369d9ca531e81c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098882"
---
# <a name="connect-azure-front-door-premium-to-your-origin-with-private-link"></a>Az Azure bejárati prémium csatlakoztatása saját forráshoz privát hivatkozással

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure-beli bevezető prémium SKU-t az Azure Private link Service használatával a virtuális hálózatban üzemeltetett alkalmazásokhoz való csatlakozáshoz.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Hozzon létre egy [privát hivatkozási](../../private-link/create-private-link-service-portal.md) szolgáltatást a forrásként szolgáló webkiszolgálók számára.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-private-endpoint-in-azure-front-door-service"></a>Privát végpont engedélyezése az Azure bejárati ajtó szolgáltatásában

Ebben a szakaszban az Azure Private link Service-t az Azure bevezető prémium SKU magánhálózaton létrehozott privát végpontra fogja képezni. 

1. Az Azure bejárati prémium profiljának *Beállítások* területén válassza a **Origin groups** elemet.

1. Válassza ki azt a származási csoportot, amely tartalmazza azt a forrást, amely számára engedélyezni kívánja a saját hivatkozását.

1. Válassza a **+ forrás hozzáadása lehetőséget** egy új forrás hozzáadásához, vagy válasszon egy korábban létrehozott forrást a listából. Ezután jelölje be a **privát kapcsolati szolgáltatás engedélyezésére** szolgáló jelölőnégyzetet.

    :::image type="content" source="../media/how-to-enable-private-link/front-door-private-endpoint-private-link.png" alt-text="Képernyőkép a privát hivatkozás engedélyezéséről a forrás hozzáadása lapon.":::

1. Az **Azure-erőforrások kiválasztásához** válassza **a saját címtárban** lehetőséget. Válassza ki vagy adja meg a következő beállítást annak az erőforrásnak a konfigurálásához, amelyet az Azure bejárati Premium használatával szeretne csatlakozni a privát kapcsolattal.
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Region | Válassza ki azt a régiót, amelyik azonos vagy a legközelebb van a forráshoz. |
    | Erőforrás típusa | Válassza a **Microsoft. Network/privateLinkServices** lehetőséget. |
    | Erőforrás | Válassza a **myPrivateLinkService** lehetőséget. |
    | Cél alerőforrás | Ezt a mezőt hagyja üresen. |
    | Kérelem üzenete | Testreszabhatja az üzenetet, vagy kiválaszthatja az alapértelmezett üzenetet. |

## <a name="next-steps"></a>Következő lépések

Ismerje meg az [Azure bejárati prémium privát hivatkozását](concept-private-link.md).
