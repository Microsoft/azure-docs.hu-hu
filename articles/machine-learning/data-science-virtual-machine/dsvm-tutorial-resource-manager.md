---
title: 'Rövid útmutató: Data Science VM létrehozása – Resource Manager sablon'
titleSuffix: Azure Data Science Virtual Machine
description: Ebben a rövid útmutatóban egy Azure Resource Manager sablonnal fog gyorsan üzembe helyezni egy Data Science Virtual Machine
services: machine-learning
author: lobrien
ms.author: laobri
ms.date: 06/10/2020
ms.topic: quickstart
ms.service: data-science-vm
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 0683634223a63281ce2b42ebb02f87f9211a589e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530651"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-an-arm-template"></a>Rövid útmutató: Ubuntu-Data Science Virtual Machine ARM-sablon használatával

Ez a rövid útmutató bemutatja, hogyan hozhat létre Ubuntu 18.04-es Data Science Virtual Machine egy Azure Resource Manager sablonnal (ARM-sablonnal). Az adattudományi Virtual Machines olyan felhőalapú virtuális gépek, amelyek adattudományi és gépi tanulási keretrendszerekkel és eszközökkel előre meg vannak töltve. GPU-alapú számítási erőforrásokon való üzembe helyezéskor minden eszköz és kódtár a GPU használatára van konfigurálva.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-ubuntu-DSVM-GPU-or-CPU%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/services/machine-learning/).

* A dokumentum cli-parancsainak a helyi környezetből való használatával az [Azure CLI-nek kell használnia.](/cli/azure/install-azure-cli)

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/) közül származik.

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json":::

A sablon a következő erőforrásokat definiálja:

* [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Compute/virtualMachines:](/azure/templates/microsoft.compute/virtualmachines)Felhőalapú virtuális gép létrehozása. Ebben a sablonban a virtuális gép Ubuntu 18.04 Data Science Virtual Machine futó virtuális gépként van konfigurálva.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az Azure CLI-sablont úgy használhatja, ha bejelentkezik, és kiválasztja az előfizetését (lásd: [Bejelentkezés az Azure CLI-val).](/cli/azure/authenticate-azure-cli) Majd futtassa ezt:

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

A fenti parancs futtatásakor írja be a következőt:

1. Annak az erőforráscsoportnak a neve, amely a DSVM-et és a társított erőforrásokat tartalmazza.
1. Az azure-beli hely, ahol az üzembe helyezést el szeretné készítse.
1. A használni szeretné a hitelesítési típust (adja meg a sztringet vagy `password` `sshPublicKey` a sztringet).
1. A rendszergazdai fiók bejelentkezési neve (ez az érték nem lehet `admin` ).
1. A fiókhoz megadott jelszó vagy nyilvános SSH-kulcs értéke.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A saját Data Science Virtual Machine:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com)
1. Jelentkezzen be.
1. Válassza ki az újonnan létrehozott erőforráscsoportot.

Itt láthatja az erőforráscsoport adatait:

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Képernyőkép egy DSVM-et tartalmazó alapszintű erőforráscsoportról":::

Kattintson a Virtuális gép erőforrásra az információs lapjára való ugráshoz. Itt találhat információkat a virtuális gépről, beleértve a kapcsolati adatokat is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem szeretné használni ezt a virtuális gépet, törölje. Mivel a DSVM más erőforrásokhoz, például tárfiókhoz van társítva, valószínűleg a teljes létrehozott erőforráscsoportot törölni szeretné. Az erőforráscsoportot a portálon törölheti, ha a **Törlés** gombra kattint, és megerősíti azt. Vagy törölheti az erőforráscsoportot a cli-ről a következővel:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Data Science Virtual Machine arm-sablonból.

> [!div class="nextstepaction"]
> [Az ML& bemutatókat bemutató mintaprogramok](dsvm-samples-and-walkthroughs.md)
