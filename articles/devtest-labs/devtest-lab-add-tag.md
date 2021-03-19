---
title: Címkék hozzáadása laborhoz a Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egyéni címkéket a Azure DevTest Labsban, és hogyan használhat címkéket az erőforrások kategorizálásához. Az előfizetéshez tartozó összes erőforrást megtekintheti a címkével.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a4d2b328626f0d0f096082fa88bb170894ccf3e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88270733"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Címkék hozzáadása laborhoz Azure DevTest Labs

Létrehozhat egyéni címkéket, és alkalmazhatja azokat a DevTest Labs-erőforrásokra, hogy logikailag kategorizálja az erőforrásokat. Később gyorsan és egyszerűen megtekintheti az előfizetésben lévő összes olyan erőforrást, amely rendelkezik az adott címkével. A címkék akkor hasznosak, ha az erőforrásokat a számlázáshoz vagy a felügyelethez kell szervezni.

A címkék által támogatott erőforrások közé tartoznak a következők:

* Számítási virtuális gépek
* Hálózati adapterek (NIC-k)
* IP-címek
* Terheléselosztók
* Tárfiókok
* Felügyelt lemezek

Címkéket is alkalmazhat, ha [létrehoz egy labort](devtest-lab-create-lab.md) , majd később felügyeli őket a Címkék panelen a konfiguráció és beállítások területen.

Minden címke egy **név** / **érték** párokból áll. Létrehozhat például egy *costcenter* nevű címkét, amelynek értéke *34543*. Egy címke, például ez segíthet a szervezete adott területére számlázandó laboratóriumi erőforrások későbbi azonosításában. Itt választhatja ki azokat a neveket és értékeket, amelyek alapján az előfizetését rendszerezni szeretné.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>A címkék egy meglévő laborban való kezelésének lépései

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Ha szükséges, válassza a **minden szolgáltatás** lehetőséget, majd válassza a **DevTest Labs** elemet a listából. Előfordulhat, hogy a labor már megjelenik az irányítópulton az **összes erőforrás** területen.
1. A Labs listából válassza ki azt a labort, amelyben címkéket szeretne felvenni vagy kezelni.
1. A labor **Áttekintés** területén válassza a **konfiguráció és szabályzatok** lehetőséget.

    ![Konfigurálás és házirendek gomb](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. A **kezelés** alatt a bal oldalon válassza a **címkék** lehetőséget.
1. Ha új címkét szeretne létrehozni ehhez a laborhoz, adjon meg egy **név** / **érték** párt, és válassza a **Mentés** lehetőséget. Kiválaszthat egy meglévő címkét is a listából a címkéhez társított erőforrások megtekintéséhez vagy kezeléséhez.

    ![Címkék kezelése](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> A labor szinten létrehozott címkék az összes számlázandó erőforráson keresztül áramlanak, amelyet a labor az előfizetésében felvesz. A labor szintű címkék például a labor virtuális gépek mögöttes számítási virtuális gépekre áramlanak. a címkék a Cost Management kontextusában használhatók. A költséghely-szintű címkék a Költségkönyvelés címkéi szűrőben jelennek meg.

## <a name="understanding-limitations-to-tags"></a>A címkékre vonatkozó korlátozások ismertetése

Az alábbi korlátozások érvényesek a címkékre:

* Minden egyes erőforrás vagy erőforráscsoport legfeljebb 15 címkenév/érték párral rendelkezhet. A korlátozás csak a közvetlenül az erőforráscsoportra vagy erőforrásra alkalmazott címkékre érvényes. Az erőforráscsoportok sok olyan erőforrást tartalmazhatnak, amelyek mindegyike 15 címkenév/érték párral rendelkezik.
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.

[Az Azure-erőforrások rendszerezéséhez címkék](../azure-resource-manager/management/tag-resources.md) használatával részletesebb információkat talál a címkék Azure-ban való használatáról, beleértve a címkék a PowerShell vagy az Azure CLI használatával történő kezelését is.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Következő lépések
* Az előfizetésre vonatkozó korlátozásokat és konvenciókat testre szabott szabályzatok használatával is alkalmazhatja. Az Ön által definiált szabályzatok esetében előfordulhat, hogy minden erőforrásnak egy adott címkére vonatkozó értékkel kell rendelkeznie. További információ: [szabályzatok és ütemtervek beállítása](devtest-lab-set-lab-policy.md).
* Ismerkedjen meg a [DevTest Labs Azure Resource Manager a rövid útmutató sablonjának galériájában](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
