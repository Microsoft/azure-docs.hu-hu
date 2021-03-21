---
title: Üzembe helyezés meglévő virtuális hálózaton
description: Ismerteti, hogyan lehet engedélyezni a felügyelt alkalmazás felhasználóit egy meglévő virtuális hálózat kiválasztásához. A virtuális hálózat a felügyelt alkalmazáson kívül is lehet.
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: fa5e59b96aada06c2dd486094d9be6a52c79e43e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84261288"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>Meglévő virtuális hálózat használata Azure Managed Applications

Ez a cikk bemutatja, hogyan határozhat meg egy olyan Azure által felügyelt alkalmazást, amely integrálható egy meglévő virtuális hálózattal a fogyasztó előfizetésében. A felügyelt alkalmazás lehetővé teszi a fogyasztó számára, hogy hozzon létre egy új virtuális hálózatot, vagy használjon egy meglévőt. A meglévő virtuális hálózat a felügyelt erőforráscsoporthoz is elvégezhető.

## <a name="main-template"></a>Fő sablon

Először nézzük meg a fájl **mainTemplate.jsét** . Alább látható a teljes sablon a virtuális gépek és a hozzájuk tartozó erőforrások üzembe helyezéséhez. Később alaposabban megvizsgálhatja a sablon azon részeit, amelyek a meglévő virtuális hálózatok használatával kapcsolatosak.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

Figyelje meg, hogy a virtuális hálózat [feltételesen van üzembe helyezve](../templates/conditional-resource-deployment.md). A fogyasztó egy paraméter értékét adja meg, amely azt jelzi, hogy új vagy meglévő virtuális hálózatot használ-e. Ha a fogyasztó új virtuális hálózatot választ ki, a rendszer telepíti az erőforrást. Ellenkező esetben a rendszer kihagyja az erőforrást az üzembe helyezés során.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

A virtuális hálózat AZONOSÍTÓjának változója két tulajdonsággal rendelkezik. Az egyik tulajdonság az erőforrás-azonosítót adja vissza új virtuális hálózat telepítésekor. A másik tulajdonság az erőforrás-azonosítót adja vissza egy meglévő virtuális hálózat használatakor. A meglévő virtuális hálózat erőforrás-azonosítója tartalmazza a virtuális hálózatot tartalmazó erőforráscsoport nevét.

Az alhálózati azonosítót a virtuális hálózat AZONOSÍTÓjának értéke alapján kell kiépíteni. Ez az érték megegyezik a felhasználók kiválasztásával.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

A hálózati adapter az alhálózati azonosító változóra van beállítva.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>Felhasználói felület definíciója

Most nézzük meg a fájl **createUiDefinition.js** . A teljes fájl:

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

A fájl egy virtuális hálózati elemet tartalmaz.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

Ez az elem lehetővé teszi, hogy a fogyasztó válasszon ki egy új vagy egy meglévő virtuális hálózatot.

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="Új vagy meglévő virtuális hálózat":::

A kimenetekben szerepel egy érték, amely jelzi, hogy a fogyasztó új vagy meglévő virtuális hálózatot jelölt ki. A felügyelt identitás értéke is van.

> [!NOTE]
> A felügyelt identitás kimeneti értékének **managedIdentity** kell lennie.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a felhasználói felület definíciós fájljának létrehozásáról, tekintse meg az [ Azure felügyelt alkalmazás létrehozási felületénekCreateUiDefinition.jsát](create-uidefinition-overview.md)ismertető témakört.
