---
title: API szerkesztése az Azure Portal használatával  | Microsoft Docs
description: Ismerje meg, hogyan szerkesztheti az API-t a API Management (APIM) használatával. Hozzáadhat, törölhet vagy átnevezhet műveleteket a APIM-példányban, vagy szerkesztheti az API-k hencegő felületét.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 1c4e64251390936e8a63ee904ec69f173cac6114
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93146705"
---
# <a name="edit-an-api"></a>API szerkesztése

Ez az oktatóanyag bemutatja, hogyan szerkeszthet egy API-t az API Management (APIM) szolgáltatással. 

+ Ez műveletek az APIM-példányon történő hozzáadásával, törlésével és átnevezésével történik. 
+ Az API swaggerjét is szerkesztheti.

## <a name="prerequisites"></a>Előfeltételek

+ [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
+ [Az első API importálása és közzététele](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>API szerkesztése az APIM szolgáltatásban

![Képernyőfelvétel: az API APIM való szerkesztésének folyamata.](./media/edit-api/edit-api001.png)

1. Kattintson az **API-k** lapfülre.
2. Válassza ki az egyik korábban importált API-t.
3. Válassza ki a **Tervezés** fület.
4. Válassza ki a szerkeszteni kívánt műveletet.
5. A művelet átnevezéséhez az **Előtér** ablakban kattintson a **ceruza** ikonra.

## <a name="update-the-swagger"></a>Swagger frissítése

A háttérrendszer API-ját frissítheti az Azure Portalról. Ehhez tegye a következőket:

1. Válassza ki a **Minden művelet** lehetőséget.
2. Az **Előtér** ablakban kattintson a ceruza ikonra.

    ![Képernyőkép, amely kiemeli a ceruza ikont a előtér-képernyőn.](./media/edit-api/edit-api002.png)

    Megjelenik az API swaggerje.

    ![API szerkesztése](./media/edit-api/edit-api003.png)

3. Frissítse a swaggert.
4. Kattintson a **Mentés** gombra.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [APIM szabályzat mintái](./policy-reference.md) 
>  [Közzétett API átalakítása és védelemmel](transform-api.md) való ellátása