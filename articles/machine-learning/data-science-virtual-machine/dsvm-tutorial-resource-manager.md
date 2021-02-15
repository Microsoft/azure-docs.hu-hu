---
title: 'Gyors útmutató: Data Science VM – Resource Manager-sablon létrehozása'
titleSuffix: Azure Data Science Virtual Machine
description: Ebben a rövid útmutatóban egy Azure Resource Manager sablonnal végezheti el a Data Science Virtual Machine gyors üzembe helyezését
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 06/10/2020
ms.service: data-science-vm
ms.topic: quickstart
ms.openlocfilehash: 7feacec9f0e78749f17359a9080411b6d9696136
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518215"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-an-arm-template"></a>Gyors útmutató: Ubuntu-Data Science Virtual Machine létrehozása ARM-sablonnal

Ez a rövid útmutató bemutatja, hogyan hozhat létre Ubuntu 18,04 Data Science Virtual Machine Azure Resource Manager sablonnal (ARM-sablon használatával). Az adatelemzési Virtual Machines az adatelemzési és gépi tanulási keretrendszerek és eszközök által előre feltöltött felhőalapú virtuális gépek. GPU-alapú számítási erőforrásokon való üzembe helyezéskor az összes eszköz és könyvtár a GPU használatára van konfigurálva.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-ubuntu-DSVM-GPU-or-CPU%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/services/machine-learning/).

* Ha a jelen dokumentumban a CLI-parancsokat a **helyi környezetből** szeretné használni, szüksége lesz az [Azure CLI](/cli/azure/install-azure-cli)-re.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/) közül származik.

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json":::

A következő erőforrások vannak definiálva a sablonban:

* [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft. Network/nyilvános IP](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. számítási/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): felhőalapú virtuális gép létrehozása. Ebben a sablonban a virtuális gép az Ubuntu 18,04-as verzióját futtató Data Science Virtual Machine van konfigurálva.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Ha az Azure CLI-vel szeretné használni a sablont, jelentkezzen be, és válassza ki az előfizetését (lásd: [Bejelentkezés az Azure CLI-vel](/cli/azure/authenticate-azure-cli)). Majd futtassa ezt:

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey &&
echo "Press [ENTER] to continue ..." &&
read
```

A fenti parancs futtatásakor írja be a következőket:

1. Azon erőforráscsoport neve, amelyet létre szeretne hozni a DSVM és a kapcsolódó erőforrások tárolásához.
1. Az Azure-beli hely, ahol az üzembe helyezést el szeretné végezni.
1. A használni kívánt hitelesítési típus (adja meg a karakterláncot `password` vagy `sshPublicKey` ).
1. A rendszergazdai fiók bejelentkezési neve (ez az érték nem lehet `admin` ).
1. A fiók jelszavának vagy nyilvános SSH-kulcsának értéke.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A Data Science Virtual Machine megtekintéséhez:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com)
1. Jelentkezzen be.
1. Válassza ki az imént létrehozott erőforráscsoportot.

Ekkor megjelenik az erőforráscsoport adatai:

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Képernyőkép egy DSVM tartalmazó alapszintű erőforráscsoporthoz":::

Kattintson a virtuális gép erőforrására, hogy megnyissa az információ lapját. Itt információkat találhat a virtuális gépen, beleértve a kapcsolat részleteit is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem szeretné használni ezt a virtuális gépet, törölje azt. Mivel a DSVM más erőforrásokhoz, például egy Storage-fiókhoz van társítva, valószínűleg törölni szeretné a teljes létrehozott erőforráscsoportot. A portálon törölheti az erőforráscsoportot, ha a **Törlés** gombra kattint, és megerősíti a szolgáltatást. Vagy törölheti is az erőforráscsoportot a parancssori felületről a következővel:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Data Science Virtual Machine egy ARM-sablonból.

> [!div class="nextstepaction"]
> [Minta programok & ML-útmutató](dsvm-samples-and-walkthroughs.md)
