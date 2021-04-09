---
title: VHD-fájl feltöltése a Azure DevTest Labsba a AzCopy használatával | Microsoft Docs
description: Ez a cikk bemutatja, hogyan tölthetők fel a AzCopy parancssori segédprogram a VHD-fájloknak Azure DevTest Labs-beli Lab Storage-fiókjába való feltöltéséhez.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1d8ede0f78726b04ac862a00b559b8d42c3ed1cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88650774"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>VHD-fájl feltöltése a labor Storage-fiókjába a AzCopy használatával

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs a VHD-fájlok használhatók a virtuális gépek kiépítéséhez használt egyéni rendszerképek létrehozására. A következő lépések végigvezetik a AzCopy parancssori segédprogram használatával, amellyel feltölthető egy VHD-fájl egy labor Storage-fiókjába. A VHD-fájl feltöltése után a [következő lépések szakasz](#next-steps) azokat a cikkeket sorolja fel, amelyek bemutatják, hogyan hozhat létre egyéni rendszerképet a feltöltött VHD-fájlból. További információ az Azure-beli lemezekről és virtuális merevlemezekről: [Bevezetés a Managed Disks](../virtual-machines/managed-disks-overview.md) szolgáltatásba

> [!NOTE] 
>  
> A AzCopy egy csak Windows-alapú parancssori segédprogram.

## <a name="step-by-step-instructions"></a>Lépésenkénti utasítások

A következő lépések végigvezetik a VHD-fájlok Azure DevTest Labs [AzCopy](https://aka.ms/downloadazcopy)használatával történő feltöltésének lépésein. 

1. Szerezze be a labor Storage-fiókjának nevét a Azure Portal használatával:

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza a **minden szolgáltatás** lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.

1. A laborok listájából válassza ki a kívánt labort.  

1. A labor paneljén válassza a **Konfigurálás** lehetőséget. 

1. A labor- **konfiguráció** panelen válassza az **Egyéni lemezképek (VHD-k)** lehetőséget.

1. Az **Egyéni lemezképek** panelen válassza a **+ Hozzáadás** lehetőséget. 

1. Az **Egyéni rendszerkép** panelen válassza a **VHD** elemet.

1. A **VHD** panelen válassza **a virtuális merevlemez feltöltése a PowerShell használatával** lehetőséget.

    ![VHD feltöltése a PowerShell használatával](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. A **rendszerkép feltöltése a PowerShell panel használatával** megjeleníti az **Add-AzureVhd** parancsmag hívását. Az első paraméter (*cél*) a blob-tároló URI-ját tartalmazza (*feltöltések*) a következő formátumban:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Jegyezze fel a teljes URI-t, mivel azt a későbbi lépésekben használják.

1. Töltse fel a VHD-fájlt a AzCopy használatával:
 
1. [Töltse le és telepítse a AzCopy legújabb verzióját](https://aka.ms/downloadazcopy).

1. Nyisson meg egy parancssorablakot, és navigáljon a AzCopy telepítési könyvtárába. Igény szerint hozzáadhatja a AzCopy telepítési helyét a rendszer elérési útjához. Alapértelmezés szerint a AzCopy a következő könyvtárba van telepítve:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. A Storage-fiók kulcsa és a blob Container URI-ja használatával futtassa a következő parancsot a parancssorban. A *vhdFileName* értékének idézőjelek közé kell esnie. A VHD-fájlok feltöltésének folyamata a VHD-fájl méretétől és a kapcsolatok sebességétől függően hosszadalmas lehet.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Következő lépések

- [Egyéni rendszerkép létrehozása Azure DevTest Labs egy VHD-fájlból a Azure Portal használatával](devtest-lab-create-template.md)
- [Egyéni rendszerkép létrehozása Azure DevTest Labs VHD-fájlból a PowerShell használatával](devtest-lab-create-custom-image-from-vhd-using-powershell.md)