---
title: 'Rövid útmutató: privát link szolgáltatás létrehozása az Azure Private linkben'
description: Ebben a rövid útmutatóban egy Azure Resource Manager sablont (ARM-sablont) használ egy privát kapcsolati szolgáltatás létrehozásához.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: 34993ad3d3d0494f89bd264a8b7194f52129ad7c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555057"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-arm-template"></a>Rövid útmutató: privát hivatkozási szolgáltatás létrehozása ARM-sablonnal

Ebben a rövid útmutatóban egy Azure Resource Manager sablont (ARM-sablont) használ egy privát kapcsolati szolgáltatás létrehozásához.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ezt a rövid útmutatót a [Azure Portal](create-private-link-service-portal.md), [Azure PowerShell](create-private-link-service-powershell.md)vagy az [Azure CLI](create-private-link-service-cli.md)használatával is elvégezheti.

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Aktív előfizetéssel rendelkező Azure-fiókra van szüksége. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>A sablon áttekintése

Ez a sablon létrehoz egy magánhálózati kapcsolati szolgáltatást.

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-privatelink-service/) közül származik.

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json":::

Több Azure-erőforrás van definiálva a sablonban:

- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): minden virtuális géphez egyetlen virtuális hálózat van.
- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): az a terheléselosztó, amely a szolgáltatást futtató virtuális gépeket teszi elérhetővé.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): két hálózati adapter létezik, egyet az egyes virtuális gépekhez.
- [**Microsoft. számítási/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): két virtuális gép van, amelyik a szolgáltatást üzemelteti, és amely a privát végponttal létesített kapcsolatokat teszteli.
- [**Microsoft. számítás/virtualMachines/Extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions): a webkiszolgálót telepítő bővítmény.
- [**Microsoft. Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices): a privát kapcsolati szolgáltatás, amellyel elérhetővé teheti a szolgáltatást.
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicIpAddresses): két nyilvános IP-cím létezik, egyet az egyes virtuális gépekhez.
- [**Microsoft. Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints): a szolgáltatás eléréséhez használt privát végpont.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az ARM-sablon üzembe helyezése az Azure-ban:

1. Az Azure-ba való bejelentkezéshez és a sablon megnyitásához válassza az **üzembe helyezés az Azure**-ban lehetőséget. A sablon létrehoz egy virtuális gépet, a standard Load balancert, a privát kapcsolati szolgáltatást, a privát végpontot, a hálózatot és a virtuális gépet az ellenőrzéshez.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. Válassza ki vagy hozza létre az erőforráscsoportot.
3. Írja be a virtuális gép rendszergazdai felhasználónevét és jelszavát.
4. Olvassa el a feltételek és kikötések utasítást. Ha elfogadja, jelölje be az Elfogadom a vásárláskor **megjelenő feltételeket és kikötéseket**  >  .

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

> [!NOTE]
> Az ARM-sablon egyedi nevet hoz létre a (z)<b>{UniqueID}</b> virtuális gép myConsumerVm. Helyettesítse be a generált értéket **{UniqueID}** értékre.

### <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Kapcsolódjon a _(z) {UniqueID}_ virtuális gép myConsumerVm az internetről a következőképpen:

1.  A portál keresősáv mezőjébe írja be a _myConsumerVm {UniqueID}_ értéket.

2.  Válassza a **Kapcsolódás** lehetőséget. Megnyílik **a virtuális géphez való kapcsolódás** .

3.  Válassza az **RDP-fájl letöltése** lehetőséget. Az Azure létrehoz egy RDP protokoll (_. rdp_) fájlt, és letölti a számítógépre.

4.  Nyissa meg a letöltött .rdp fájlt.

    a. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    b. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.
    
    > [!NOTE]
    > Előfordulhat, hogy a   >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania **egy másik fiók használatával**.

5.  Válassza az **OK** lehetőséget.

6.  A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás** lehetőséget.

7.  A virtuális gép asztalának megjelenése után csökkentse a helyi asztalra való visszalépést.

### <a name="access-the-http-service-privately-from-the-vm"></a>A http-szolgáltatás elérése a virtuális gépről

A következő lépésekkel csatlakozhat a http-szolgáltatáshoz a virtuális gépről a privát végpont használatával.

1.  Ugrás a _(távoli asztal) {UniqueID} myConsumerVm_.
2.  Nyisson meg egy böngészőt, és adja meg a magánhálózati végpont címe: `http://10.0.0.5/` .
3.  Megjelenik az alapértelmezett IIS-oldal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a privát kapcsolat szolgáltatással létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a Private link Service-t és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések


A privát végpontot támogató szolgáltatásokkal kapcsolatos további információkért lásd:
> [!div class="nextstepaction"]
> [Privát kapcsolat elérhetősége](private-link-overview.md#availability)
