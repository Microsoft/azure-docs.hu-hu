---
title: Azure Functions Prémium csomag létrehozása a portálon
description: Megtudhatja, hogyan hozhat létre a prémium csomaggal futó Function-alkalmazást a Azure Portal használatával.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 9cab67f096665c9333fa40bcb790896fcbebd8d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676586"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>Prémium csomagú Function-alkalmazás létrehozása a Azure Portal

Azure Functions egy skálázható, prémium szintű csomagot kínál, amely virtuális hálózati kapcsolatot biztosít, és nem tartalmaz hideg indítást és prémium szintű hardvert. További információ: [Azure functions Premium csomag](functions-premium-plan.md). 

Ebből a cikkből megtudhatja, hogyan hozhat létre egy Function-alkalmazást a prémium csomagban a Azure Portal használatával. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése, skálázása és megosztása érdekében.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Ezen a ponton létrehozhat függvényeket az új Function alkalmazásban. Ezek a függvények kihasználhatják a [Prémium csomag](functions-premium-plan.md)előnyeit.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [HTTP által aktivált függvény hozzáadása] (./functions-get-started.md
