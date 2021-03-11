---
title: 'Gyors útmutató: Ubuntu Linux virtuális gép létrehozása Resource Manager-sablonnal'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Linux rendszerű virtuális gépet Resource Manager-sablonok használatával
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: 97a9d3632cb0e78b899844b1aaa84a030c6da23e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549617"
---
# <a name="quickstart-create-an-ubuntu-linux-virtual-machine-using-an-arm-template"></a>Gyors útmutató: Ubuntu Linux virtuális gép létrehozása ARM-sablonnal

Ez a rövid útmutató bemutatja, hogyan használható egy Azure Resource Manager sablon (ARM-sablon) egy Ubuntu Linux virtuális gép (VM) üzembe helyezéséhez az Azure-ban.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) közül származik.

:::code language="json" source="~/quickstart-templates/101-vm-simple-linux/azuredeploy.json":::


Több erőforrás van definiálva a sablonban:

- [**Microsoft. Network/virtualNetworks/Subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): hozzon létre egy alhálózatot.
- [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): hozzon létre egy Storage-fiókot.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): hozzon létre egy hálózati adaptert.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): hozzon létre egy hálózati biztonsági csoportot.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): hozzon létre egy virtuális hálózatot.
- [**Microsoft. Network/nyilvános IP**](/azure/templates/Microsoft.Network/publicIPAddresses): hozzon létre egy nyilvános IP-címet.
- [**Microsoft. számítási/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): hozzon létre egy virtuális gépet.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy kulcstartót és egy titkos kulcsot.

    [![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket. Használja az alapértelmezett értékeket, ha vannak ilyenek.

    - **Előfizetés**: válasszon ki egy Azure-előfizetést.
    - **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új létrehozása** lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK** gombra.
    - **Hely**: válasszon ki egy helyet.  Például: **USA középső régiója**.
    - **Rendszergazdai Felhasználónév**: adjon meg egy felhasználónevet, például az *Azureus* nevet.
    - **Hitelesítés típusa**: választhat, hogy SSH-kulcsot vagy jelszót használ-e.
    - **Rendszergazdai jelszó vagy kulcs** attól függően, hogy mit választott a hitelesítési típushoz:
        - Ha a **jelszó** lehetőséget választja, a jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](faq.md#what-are-the-password-requirements-when-creating-a-vm).
        - Ha a **sshPublicKey** lehetőséget választja, illessze be a nyilvános kulcs tartalmát.
    - **DNS-címke előtagja**: adjon meg egy egyedi azonosítót, amelyet a DNS-címke részeként kíván használni.
    - **Ubuntu operációs rendszer verziója**: válassza ki, hogy melyik Ubuntu-verziót szeretné futtatni a virtuális gépen.
    - **Hely**: az alapértelmezett érték ugyanaz a hely, mint az erőforráscsoport, ha már létezik.
    - **Virtuális gép mérete**: válassza ki a virtuális géphez használandó [méretet](../sizes.md) .
    - **Virtual Network neve**: a vNet használandó név.
    - **Alhálózat neve**: a virtuális gép által használandó alhálózat neve.
    - **Hálózati biztonsági csoport neve**: a NSG neve.
1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejezése után a **Létrehozás** gombra kattintva hozhatja létre és helyezheti üzembe a virtuális gépet.


Az Azure Portalon helyezhető üzembe a sablon. A Azure Portalon kívül használhatja az Azure CLI-t, a Azure PowerShellt és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A Azure Portal segítségével megkeresheti a virtuális gépet és a létrehozott más erőforrásokat. Az üzembe helyezés befejezése után válassza az **Ugrás az erőforráscsoporthoz** lehetőséget a virtuális gép és egyéb erőforrások megtekintéséhez.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli a virtuális gépet és az erőforráscsoport összes erőforrását. 

1. Válassza ki az **erőforráscsoportot**.
1. Az erőforráscsoport lapján válassza a **Törlés** lehetőséget.
1. Ha a rendszer kéri, írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet egy ARM-sablon használatával. Ha bővebb információra van szüksége az Azure-beli virtuális gépekkel kapcsolatban, lépjen tovább a Linux rendszerű virtuális gépekről szóló oktatóanyagra.


> [!div class="nextstepaction"]
> [Azure-beli Linux rendszerű virtuális gépek – oktatóanyag](./tutorial-manage-vm.md)