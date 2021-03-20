---
title: A szervezeten belüli Power BI vizualizáció Azure Maps kezelése | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan kezelheti az Microsoft Azure Maps Power BI vizualizációt a szervezeten belül.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 2f7372d522c02eb86f1cc6470cefa9cb299605a7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86261853"
---
# <a name="manage-the-azure-maps-visual-within-your-organization"></a>A Azure Maps vizualizáció kezelése a szervezeten belül

A Power BI a tervezők és a bérlői rendszergazdák számára lehetővé teszi a Azure Maps vizualizáció használatának kezelését. Az alábbiakban megtalálhatja az egyes szerepkörökben elvégezhető lépéseket.

### <a name="designer-options"></a>Tervezői lehetőségek

Power BI Desktop a tervezők letilthatják a Azure Maps vizualizációt a biztonság lapon. Válassza  &gt; **a fájl beállításai és beállítások** lehetőséget, majd válassza a **Beállítások** &gt; **előnézet funkciók** lehetőséget. Ha le van tiltva, akkor a Azure Maps alapértelmezés szerint nem töltődik be.  
  
![Power BI beállítások panel, amely a Azure Maps vizualizáció előzetes verziójának funkcióit jeleníti meg](media/power-bi-visual/preview-options-panel.png)

## <a name="tenant-admin-options"></a>Bérlői rendszergazdai beállítások

A PowerBI.com-ben a bérlői rendszergazdák kikapcsolhatják a Azure Maps vizualizációt az összes felhasználó számára. Válassza a **Beállítások** &gt; **felügyeleti** **portál** &gt; **bérlői beállítások** lehetőséget. Ha le van tiltva, Power BI a vizualizációk ablaktáblán már nem jeleníti meg a Azure Maps vizualizációt.

> [!div class="mx-imgBorder"]
> ![A Azure Maps vizualizáció bérlői beállításait bemutató Power BI felügyeleti portál](media/power-bi-visual/tenant-admin-settings.png)

## <a name="next-steps"></a>Következő lépések

További információ a Azure Maps Power BI vizualizációról:

> [!div class="nextstepaction"]
> [A Azure Maps rétegeinek ismertetése Power BI vizualizációban](power-bi-visual-understanding-layers.md)
