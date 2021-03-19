---
title: Egyéni rendszerkép létrehozása VHD-fájlból Azure PowerShell használatával
description: Egyéni rendszerkép létrehozásának automatizálása Azure DevTest Labs VHD-fájlból a PowerShell használatával
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4b0712fdbec1ce23ad9e09d972e425cb7941107b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87288982"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Egyéni rendszerkép létrehozása VHD-fájlból a PowerShell használatával

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Lépésenkénti utasítások

A következő lépések végigvezetik egy egyéni rendszerkép létrehozásán egy VHD-fájlból a PowerShell használatával:

1. Egy PowerShell-parancssorban jelentkezzen be az Azure-fiókjába a következő hívással a **AzAccount** parancsmaggal.

    ```powershell
    Connect-AzAccount
    ```

1.  A **Select-AzSubscription** parancsmag meghívásával válassza ki a kívánt Azure-előfizetést. Cserélje le a következő helyőrzőt a **$subscriptionId** változóra érvényes Azure-előfizetés-azonosítóval.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Kérje le a labor objektumot a **Get-AzResource** parancsmag meghívásával. Cserélje le a következő helyőrzőket a **$labRgre** , és **$labName** változókat a környezetének megfelelő értékekkel.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  Cserélje le a **$vhdUri** változó következő helyőrzőjét a feltöltött vhd-fájl URI-fájljára. A VHD-fájl URI-JÁT a Storage-fiók blob paneljéről kérheti le a Azure Portal.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Hozza létre az egyéni rendszerképet a **New-AzResourceGroupDeployment** parancsmag használatával. Cserélje le a következő helyőrzőket a **$customImageNamere** , és **$customImageDescription** a változókat a környezete szempontjából hasznos nevekre.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>PowerShell-parancsfájl egy VHD-fájlból származó egyéni rendszerkép létrehozásához

A következő PowerShell-szkripttel létrehozhat egy VHD-fájlból származó egyéni rendszerképet. Cserélje le a helyőrzőket (szögletes zárójelekkel kezdődik és végződik) az igényeinek megfelelő értékekkel.

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni lemezképek vagy képletek?](./devtest-lab-faq.md#blog-post)
- [Egyéni rendszerképek másolása Azure DevTest Labs között](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Következő lépések

- [Virtuális gép hozzáadása a laborhoz](devtest-lab-add-vm.md)
