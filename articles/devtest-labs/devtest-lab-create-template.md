---
title: Azure DevTest Labs egyéni rendszerkép létrehozása VHD-fájlból | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egyéni rendszerképeket Azure DevTest Labs egy VHD-fájlból a Azure Portal használatával
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: cac812a9c38fc1dedfd31659a626b122f9527e63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87289400"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Egyéni rendszerkép létrehozása VHD-fájlból

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Lépésenkénti utasítások

A következő lépések végigvezetik egy egyéni rendszerkép létrehozásán egy VHD-fájlból a Azure Portal használatával:

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza a **minden szolgáltatás** lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.

1. A laborok listájából válassza ki a kívánt labort.  

1. A labor fő ablaktábláján válassza a **konfiguráció és szabályzatok** lehetőséget. 

1. A **konfiguráció és házirendek** panelen válassza az **Egyéni lemezképek** lehetőséget.

1. Az **Egyéni lemezképek** panelen válassza a **+ Hozzáadás** lehetőséget.

    ![Egyéni rendszerkép hozzáadása](./media/devtest-lab-create-template/add-custom-image.png)

1. Adja meg az egyéni rendszerkép nevét. Ez a név jelenik meg az alaplemezképek listájában a virtuális gép létrehozásakor.

1. Adja meg az egyéni rendszerkép leírását. Ez a leírás az alaplemezképek listájában jelenik meg a virtuális gép létrehozásakor.

1. Az **operációs rendszer típusa** beállításnál válassza a **Windows** vagy a **Linux** lehetőséget.

    - Ha a **Windows** lehetőséget választja, adja meg azt a jelölőnégyzetet, hogy a *Sysprep* a számítógépen fut-e. 
    - Ha a **Linux** lehetőséget választja, adja meg azt a jelölőnégyzetet, hogy a *kiépítés* le lett-e futtatva a gépen. 

1. Válassza ki a **virtuális merevlemezt** a legördülő menüből. Ez az új egyéni rendszerkép létrehozásához használt VHD. Ha szükséges, válassza a **virtuális merevlemez feltöltését a PowerShell használatával**.

1. Megadhatja a csomag nevét, a csomag ajánlatát, és megtervezheti a közzétevőt, ha az egyéni rendszerkép létrehozásához használt rendszerkép nem a Microsoft által közzétett, licencelt rendszerkép.

   - **Csomag neve:** Adja meg annak a piactér-rendszerképnek (SKU) a nevét, amelyből ez az egyéni rendszerkép létrejött 
   - **Csomag ajánlata:** Adja meg annak a piactér-rendszerképnek a termékét (ajánlatát), amelyről az egyéni rendszerkép létrejött 
   - **Közzétevő megtervezése:** Adja meg annak a piactér-rendszerképnek a közzétevőjét, amelyről az egyéni rendszerkép létrejött

   > [!NOTE]
   > Ha az egyéni rendszerkép létrehozásához használt rendszerkép **nem** egy licenccel rendelkező rendszerkép, akkor ezek a mezők üresek, és a választása esetén kitölthetők. Ha a rendszerkép **egy licencelt** rendszerkép, akkor a rendszer automatikusan kitölti a mezőket a csomag adataival. Ha ebben az esetben megpróbálja módosítani őket, figyelmeztető üzenet jelenik meg.
   >
   >

1. Az egyéni rendszerkép létrehozásához kattintson **az OK gombra** .

Néhány perc elteltével létrejön az egyéni rendszerkép, amely a labor Storage-fiókjában tárolódik. Ha egy tesztkörnyezet egy új virtuális gépet szeretne létrehozni, a lemezkép az alaplemezképek listájában érhető el.

![Az alapképek listájában elérhető egyéni rendszerkép](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni lemezképek vagy képletek?](./devtest-lab-faq.md#blog-post)
- [Egyéni rendszerképek másolása Azure DevTest Labs között](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Következő lépések

- [Virtuális gép hozzáadása a laborhoz](./devtest-lab-add-vm.md)
