---
title: Virtuális gép Azure Resource Manager sablonjának megtekintése és használata
description: Megtudhatja, hogyan használhatja a Azure Resource Manager sablont egy virtuális gépről más virtuális gépek létrehozásához
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fb7cb38dfb2c1664017f0e342fefecdd464df45f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696013"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Virtuális gépek létrehozása Azure Resource Manager sablon használatával 

Ha virtuális gépet (VM) hoz létre a DevTest Labs szolgáltatásban a [Azure Portalon](https://go.microsoft.com/fwlink/p/?LinkID=525040)keresztül, megtekintheti a Azure Resource Manager sablont a virtuális gép mentése előtt. A sablon ezután felhasználható úgy, hogy több, azonos beállításokkal rendelkező laboratóriumi virtuális gépet hozzon létre.

Ez a cikk a több virtuális gépre kiterjedő és a különálló VM Resource Manager-sablonokat ismerteti, és bemutatja, hogyan tekintheti meg és mentheti a sablonokat a virtuális gépek létrehozásakor.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Multi-VM és Single-VM Resource Manager-sablonok
A DevTest Labs-ben kétféleképpen hozhatók létre virtuális gépek a Resource Manager-sablonok használatával: a Microsoft. segédösszetevője/Labs/virtualmachines erőforrás kiépítése, vagy a Microsoft. számítási/virtualmachines erőforrás kiépítése. Mindegyiket különböző forgatókönyvekben használják, és eltérő engedélyekkel kell rendelkezniük.

- A Microsoft. segédösszetevője/Labs/virtualmachines típusú erőforrást használó Resource Manager-sablonok (ahogy a sablon "Resource" tulajdonságában vannak deklarálva) egyéni Tesztkörnyezet-virtuális gépeket is létrehozhatnak. Minden virtuális gép ekkor egyetlen elemként jelenik meg a DevTest Labs virtuális gépek listájában:

   ![Képernyőkép, amely a virtuális gépek listáját jeleníti meg egyetlen elemként a DevTest Labs virtuális gépek listájában.](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Ezt a típusú Resource Manager-sablont a **New-AzResourceGroupDeployment Azure PowerShell-** paranccsal vagy az Azure CLI-parancs az **Deployment Group Create** parancsával lehet kiépíteni. Rendszergazdai engedélyekre van szükség, így a DevTest Labs felhasználói szerepkörhöz hozzárendelt felhasználók nem hajthatják végre a telepítést. 

- A Microsoft. számítási/virtualmachines erőforrástípus használó Resource Manager-sablonok több virtuális gépet is kiépíthetnek egyetlen környezetként a DevTest Labs virtuális gépek listájában:

   ![A virtuális gépek listája egyetlen elemként a DevTest Labs Virtual Machines listában](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Az ugyanabban a környezetben lévő virtuális gépeket együtt lehet kezelni, és ugyanazt az életciklust használják. A DevTest Labs-felhasználói szerepkörrel rendelkező felhasználók létrehozhatnak környezeteket a sablonok használatával, feltéve, hogy a rendszergazda úgy konfigurálta a labort, hogy a módszert használja.

A cikk további része a Microsoft. segédösszetevője/Labs/virtualmachines szolgáltatást használó Resource Manager-sablonokat ismerteti. Ezeket a laboratóriumi rendszergazdák használják a laboratóriumi virtuális gépek létrehozásának automatizálására (például a megkövetelhető virtuális gépekre) vagy a aranyszínű képek generálására (például a rendszerkép-előállítóra).

[Azure Resource Manager sablonok létrehozásával kapcsolatos ajánlott eljárások](../azure-resource-manager/templates/template-best-practices.md) számos útmutatást és javaslatot nyújtanak a megbízható és könnyen használható Azure Resource Manager sablonok létrehozásához.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Virtuális gép Resource Manager-sablonjának megtekintése és mentése
1. A virtuális gép létrehozásának megkezdéséhez kövesse az első virtuális gép [tesztkörnyezetben való létrehozásához](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) szükséges lépéseket.
1. Adja meg a virtuális gép szükséges adatait, és adja hozzá az ehhez a virtuális GÉPHEZ használni kívánt összetevőket.
1. Váltson a **Speciális beállítások** lapra. 
1. A beállítások konfigurálása ablak alján kattintson az ARM- **sablon megtekintése** elemre.
1. Másolja ki és mentse el a Resource Manager-sablont később egy másik virtuális gép létrehozásához.

   ![A későbbi használatra menteni kívánt Resource Manager-sablon](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

A Resource Manager-sablon mentése után frissítenie kell a sablon paraméterek szakaszát, mielőtt használni lehetne. Létrehozhat egy parameter.js, amely a tényleges Resource Manager-sablonon kívül csak a paramétereket testreszabja. 

![Paraméterek testreszabása JSON-fájl használatával](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

A Resource Manager-sablon most már készen áll a [virtuális gép létrehozására](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Lejárati dátum beállítása
Olyan forgatókönyvek esetén, mint például a képzés, a bemutatók és a próbaverziók, érdemes lehet virtuális gépeket létrehozni, és azokat automatikusan törölni rögzített időtartam után, hogy ne kelljen szükségtelen költségeket fizetnie. A virtuális gép **expirationDate** tulajdonságának megadásával létrehozhat egy lejárati dátummal rendelkező Tesztkörnyezet-virtuális gépet. Tekintse meg ugyanezt a Resource Manager-sablont a [GitHub-tárházban](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration).



### <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [hozhat létre több virtuális gépre kiterjedő környezeteket Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md).
* [Resource Manager-sablon üzembe helyezése virtuális gép létrehozásához](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Ismerkedjen meg a DevTest Labs automationhez készült Resource Manager-sablonokkal a [nyilvános DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates)-tárházában.
