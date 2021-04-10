---
title: Azure VMware-megoldás saját felhő létrehozása
description: Az Azure VMware-megoldás saját felhővel való létrehozásának lépései a Azure Portal használatával.
ms.topic: include
ms.date: 04/07/2021
ms.openlocfilehash: 6b4e5631d1a4b6c5bf56b01aba12752595ef63b8
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073754"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza **az új erőforrás létrehozása** lehetőséget. A **Keresés a piactér** szövegmezőbe mezőbe írja be `Azure VMware Solution` az **Azure VMware megoldás** elemet a listából. Az **Azure VMware megoldás** ablakban válassza a **Létrehozás** lehetőséget.

1. Az **alapvető beállítások** lapon adja meg a mezők értékeit. A következő táblázat a mezők tulajdonságait sorolja fel.

   | Mező   | Érték  |
   | ---| --- |
   | **Előfizetés** | Az üzembe helyezéshez használni kívánt előfizetés.|
   | **Erőforráscsoport** | A saját felhőalapú erőforrásainak erőforráscsoport. |
   | **Hely** | Válasszon egy helyet, például az **USA keleti** régióját.|
   | **Erőforrás neve** | Az Azure VMware-megoldás saját Felhőbeli neve. |
   | **Termékváltozat** | Válassza ki a következő SKU-értéket: AV36 |
   | **Hosts** | A saját felhőalapú fürtbe felvenni kívánt gazdagépek száma. Az alapértelmezett érték 3, amely az üzembe helyezés után növelhető vagy csökkenthető.  |
   | **Címterület** | Adja meg a CIDR-hálózat IP-címét a privát felhőhöz, például 10.175.0.0/22. |
   | **Virtual Network** | Válasszon ki egy Virtual Network, vagy hozzon létre egy újat az Azure VMware megoldás saját felhőhöz.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Az alapvető beállítások lapon adja meg a mezők értékeit." border="true":::

1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet. A következő képernyőn ellenőrizze a megadott adatokat. Ha az adatok helyesek, válassza a **Létrehozás** lehetőséget.

   > [!NOTE]
   > Ez a lépés nagyjából 3-4 órát vesz igénybe. A meglévő/azonos fürtben egyetlen csomópont hozzáadása 30-45 percen belül megtörténik.

1. Ellenőrizze, hogy a központi telepítés sikeres volt-e. Navigáljon a létrehozott erőforráscsoporthoz, és válassza ki saját felhőjét.  A telepítés befejeződése után a **sikeres** állapot jelenik meg. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Ellenőrizze, hogy a központi telepítés sikeres volt-e." border="true":::
