---
title: Linuxos virtuális gép létrehozása az Azure-ban sablon alapján
description: Linux rendszerű virtuális gép létrehozása Resource Manager-sablonból az Azure CLI használatával
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: a4e1bf56df52717255d2bae0a38186335d922ff1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554683"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Linuxos virtuális gép létrehozása Azure Resource Manager-sablonokkal

Megtudhatja, hogyan hozhat létre linuxos virtuális gépet (VM) egy Azure Resource Manager sablonnal és az Azure parancssori felülettel az Azure Cloud Shell használatával. Windows rendszerű virtuális gép létrehozásához tekintse meg [a Windows rendszerű virtuális gép Resource Manager-sablonból](../windows/ps-template.md)történő létrehozását ismertető témakört.

Egy másik lehetőség a sablon üzembe helyezése a Azure Portal. A sablon megnyitásához a portálon válassza az **üzembe helyezés az Azure** -ban gombot.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-sshkey%2Fazuredeploy.json)

## <a name="templates-overview"></a>Sablonok – Áttekintés

Azure Resource Manager a sablonok JSON-fájlok, amelyek meghatározzák az Azure-megoldás infrastruktúráját és konfigurációját. A sablonok segítségével a megoldás a teljes életciklusa során ismételten üzembe helyezhető, és az erőforrások üzembe helyezése biztosan konzisztens lesz. Ha többet szeretne megtudni a sablon formátumáról és a létrehozásáról, tekintse meg [a rövid útmutató: Azure Resource Manager sablonok létrehozása és telepítése a Azure Portal használatával](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)című témakört. Az erőforrástípusok JSON-szintaxisának megtekintéséért lásd [az Azure Resource Manager-sablonokban az erőforrások meghatározásával kapcsolatos](/azure/templates/microsoft.compute/allversions) témakört.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Az Azure-beli virtuális gépek létrehozása általában két lépést tartalmaz:

1. Hozzon létre egy erőforráscsoportot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuális gép létrejötte előtt létre kell hozni.
1. Virtuális gépet hoz létre.

Az alábbi példa egy virtuális gépet hoz létre egy [Azure Gyorsindítás sablonból](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Ehhez a központi telepítéshez csak SSH-hitelesítés engedélyezett. Ha a rendszer kéri, adja meg saját nyilvános SSH-kulcsának értékét, például a *~/.ssh/id_rsa. pub* tartalmát. Ha létre kell hoznia egy SSH-kulcspárt, tekintse meg a Linux rendszerű [virtuális gépekhez készült SSH-kulcspár létrehozása és használata az Azure-ban](mac-create-ssh-keys.md)című témakört. A sablon egy másolata:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

A CLI-szkript futtatásához válassza a **kipróbálás** lehetőséget az Azure Cloud Shell megnyitásához. A szkript beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a **Beillesztés** parancsot:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

Az utolsó Azure CLI-parancs az újonnan létrehozott virtuális gép nyilvános IP-címét jeleníti meg. A virtuális géphez való kapcsolódáshoz a nyilvános IP-címnek kell lennie. Tekintse meg a jelen cikk következő szakaszát.

Az előző példában egy GitHubban tárolt sablont adott meg. Letölthet vagy létrehozhat egy sablont is, és megadhatja a helyi elérési utat a `--template-file` paraméterrel.

Néhány további információforrás:

- A Resource Manager-sablonok fejlesztéséről a [Azure Resource Manager dokumentációjában](../../azure-resource-manager/index.yml)talál további információt.
- Az Azure-beli virtuális gépek sémáinak megtekintéséhez lásd: [Azure-sablonok referenciája](/azure/templates/microsoft.compute/allversions).
- A virtuálisgép-sablonok további mintáinak megtekintéséhez tekintse meg az [Azure Gyorsindítás sablonjait](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Ezt követően SSH-t használhat a virtuális géphez a szokásos módon. Adja meg saját nyilvános IP-címét az előző parancsból:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Következő lépések

Ebben a példában egy alapszintű linuxos virtuális gépet hozott létre. További Resource Manager-sablonok, amelyek alkalmazás-keretrendszereket tartalmaznak, vagy összetettebb környezeteket hoznak létre, Böngésszen az [Azure Gyorsindítás sablonjain](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

A sablonok létrehozásával kapcsolatos további tudnivalókért tekintse meg a telepített erőforrások típusának JSON-szintaxisát és tulajdonságait:

- [Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft. Network/nyilvános IP](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft. számítás/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
