---
title: VHD-fájl feltöltése Azure DevTest Labs PowerShell használatával | Microsoft Docs
description: Ez a cikk egy olyan bemutatót tartalmaz, amely bemutatja, hogyan tölthet fel egy VHD-fájlt Azure DevTest Labs a PowerShell használatával.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2b393b886a50f60a918690ee2a5583f9623dbe39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88650757"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>VHD-fájl feltöltése a labor Storage-fiókjába a PowerShell használatával

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs a VHD-fájlok használhatók a virtuális gépek kiépítéséhez használt egyéni rendszerképek létrehozására. Az alábbi lépések végigvezetik a PowerShell használatával egy VHD-fájlnak a labor Storage-fiókjába való feltöltéséhez. A VHD-fájl feltöltése után a [következő lépések szakasz](#next-steps) azokat a cikkeket sorolja fel, amelyek bemutatják, hogyan hozhat létre egyéni rendszerképet a feltöltött VHD-fájlból. További információ az Azure-beli lemezekről és virtuális merevlemezekről: [Bevezetés a Managed Disks](../virtual-machines/managed-disks-overview.md) szolgáltatásba

## <a name="step-by-step-instructions"></a>Lépésenkénti utasítások

A következő lépések végigvezetik a VHD-fájlok Azure DevTest Labs a PowerShell használatával történő feltöltésének lépésein. 

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza a **minden szolgáltatás** lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.

1. A laborok listájából válassza ki a kívánt labort.  

1. A labor paneljén válassza a **Konfigurálás** lehetőséget. 

1. A labor- **konfiguráció** panelen válassza az **Egyéni lemezképek (VHD-k)** lehetőséget.

1. Az **Egyéni lemezképek** panelen válassza a **+ Hozzáadás** lehetőséget. 

1. Az **Egyéni rendszerkép** panelen válassza a **VHD** elemet.

1. A **VHD** panelen válassza **a virtuális merevlemez feltöltése a PowerShell használatával** lehetőséget.

    ![VHD feltöltése a PowerShell használatával](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. A **rendszerkép feltöltése a PowerShell használatával** panelen másolja a generált PowerShell-szkriptet egy szövegszerkesztőbe.

1. Módosítsa az **Add-AzureVhd** parancsmag **LocalFilePath** paraméterét úgy, hogy a feltölteni kívánt vhd-fájl helyére mutasson.

1. A PowerShell-parancssorban futtassa az **Add-AzureVhd** parancsmagot (a módosított **LocalFilePath** paraméterrel).

> [!WARNING] 
> 
> A VHD-fájlok feltöltésének folyamata a VHD-fájl méretétől és a kapcsolatok sebességétől függően hosszadalmas lehet.

## <a name="next-steps"></a>Következő lépések

- [Egyéni rendszerkép létrehozása Azure DevTest Labs egy VHD-fájlból a Azure Portal használatával](devtest-lab-create-template.md)
- [Egyéni rendszerkép létrehozása Azure DevTest Labs VHD-fájlból a PowerShell használatával](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
