---
title: Társítások telepítése felügyelt alkalmazáshoz házirend használatával
description: Ismerje meg, hogyan telepíthet társításokat felügyelt alkalmazásokhoz Azure Policy szolgáltatás használatával.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650941"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Felügyelt alkalmazáshoz tartozó társítások telepítése Azure Policy használatával

Az Azure-szabályzatok segítségével társításokat helyezhet üzembe az erőforrások felügyelt alkalmazásokhoz való társításához. Ebben a cikkben egy olyan beépített szabályzatot ismertetünk, amely a társításokat telepíti, és hogyan használhatja ezt a házirendet.

## <a name="built-in-policy-to-deploy-associations"></a>Beépített szabályzat a társítások telepítéséhez

A felügyelt alkalmazásokhoz tartozó társítások üzembe helyezése egy beépített szabályzat, amely az erőforrások felügyelt alkalmazásokhoz társítására használható. A szabályzat három paramétert fogad el:

- Felügyelt alkalmazás azonosítója – ez az azonosító annak a felügyelt alkalmazásnak az erőforrás-azonosítója, amelyhez az erőforrásokat társítani kell.
- Társítandó erőforrástípusok – ezek az erőforrástípusok a felügyelt alkalmazáshoz társítandó erőforrástípusok listája. Több erőforrástípust is hozzárendelhet egy felügyelt alkalmazáshoz ugyanazzal a házirenddel.
- Társítás neve előtag – ez a karakterlánc a létrehozandó társítási erőforrás neveként hozzáadandó előtag. Az alapértelmezett érték a "DeployedByPolicy".

A szabályzat DeployIfNotExists-értékelést használ. Azt követően fut, hogy az erőforrás-szolgáltató a kiválasztott erőforrástípus (ok) hoz létre vagy frissít erőforrás-kérelmet, és a kiértékelés sikeres állapotkódot adott vissza. Ezt követően a társítási erőforrás üzembe helyezése sablon-telepítés használatával történik.
További információ a társításokról: [Azure Custom Providers erőforrás](../custom-providers/concepts-resource-onboarding.md) bevezetése

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>A beépített társítási szabályzat használata 

### <a name="prerequisites"></a>Előfeltételek
Ha a felügyelt alkalmazásnak engedélyre van szüksége az előfizetéshez, a művelet végrehajtásához a társítási erőforrás házirend-telepítése nem fog működni az engedélyek megadása nélkül.

### <a name="policy-assignment"></a>Szabályzat-hozzárendelés
A beépített házirend használatához hozzon létre egy szabályzat-hozzárendelést, és rendelje hozzá a felügyelt alkalmazás-házirendhez tartozó központi telepítéseket. Miután a szabályzat sikeresen hozzá lett rendelve, a szabályzat azonosítja a nem megfelelő erőforrásokat, és nem telepíti a társítást ezekhez az erőforrásokhoz.

![Beépített szabályzat kiosztása](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Segítség kérése

Ha kérdése van az Azure-beli egyéni erőforrás-szolgáltatók fejlesztésével kapcsolatban, próbálja meg megkérdezni őket [stack Overflowon](https://stackoverflow.com/questions/tagged/azure-custom-providers). Egy hasonló kérdés megválaszolása már megtörtént, ezért először A feladás előtt érdemes megnéznie. A címke hozzáadásával ```azure-custom-providers``` gyors választ kaphat!

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtanulta, hogyan telepíthet a beépített szabályzatot a társítások üzembe helyezésére. További információt a következő cikkekben talál:

- [Fogalmak: Azure Custom Providers erőforrás-előkészítés](../custom-providers/concepts-resource-onboarding.md)
- [Oktatóanyag: erőforrás-előkészítés egyéni szolgáltatókkal](../custom-providers/tutorial-resource-onboarding.md)
- [Oktatóanyag: egyéni műveletek és erőforrások létrehozása az Azure-ban](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [Gyors útmutató: egyéni erőforrás-szolgáltató létrehozása és egyéni erőforrások üzembe helyezése](../custom-providers/create-custom-provider.md)
- [Útmutató: egyéni műveletek hozzáadása egy Azure-REST APIhoz](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [Útmutató: egyéni erőforrások hozzáadása Azure-REST APIhoz](../custom-providers/custom-providers-resources-endpoint-how-to.md)
