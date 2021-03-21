---
title: PowerShell-modul a Azure Lab Serviceshoz | Microsoft Docs
description: Ez a cikk egy PowerShell-modulról nyújt információkat, amely segítséget nyújt az összetevők Azure Lab Servicesban való kezeléséhez.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4f990b35a41f040d34fab156d3f3d450ad7561a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94646525"
---
# <a name="azlabservices-powershell-module-preview"></a>Az.LabServices PowerShell-modul (előzetes verzió)
Az az. LabServices egy PowerShell-modul, amely leegyszerűsíti az Azure Lab Services felügyeletét. Lehetővé teszi a laboratóriumi fiókok, laborok, virtuális gépek és lemezképek létrehozását, lekérdezését, frissítését és törlését. További információ erről a modulról: az az [. LabServices Kezdőlap a githubon](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Ez a modul **előzetes** verzióban érhető el. 

## <a name="example-command"></a>Példaparancs
Az alábbi példa egy PowerShell-parancs használatával állítja le az összes futó virtuális gépet az összes laborban.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Bevezetés
1. Telepítse [Azure PowerShell](/powershell/azure/) , ha az nem létezik a gépen. 
2. Töltse le az [az. LabServices. psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) programot a gépre.
3. Importálja a modult:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Futtassa az alábbi parancsot az előfizetésben található összes Labs listázásához.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Következő lépések
Lásd az az [. LabServices kezdőlapját a githubon](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).