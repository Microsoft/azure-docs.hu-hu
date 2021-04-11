---
title: A VM v2 (előzetes verzió) indítási/leállítási funkciójának eltávolítása – áttekintés
description: Ez a cikk azt ismerteti, Hogyan távolítható el a VM v2 (előzetes verzió) szolgáltatás indítása/leállítása funkció.
services: azure-functions
ms.subservice: ''
ms.date: 03/30/2021
ms.topic: conceptual
ms.openlocfilehash: b4308be8f7494c1cb6f6b4839fc5a2e9717668e3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111803"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>Virtuális gépek indítása és leállítása v2 (előzetes verzió)

Miután engedélyezte az Azure-beli virtuális gépek futó állapotának kezeléséhez szükséges Start/Stop VM v2 (előzetes verzió) szolgáltatást, dönthet úgy, hogy leállítja az használatát. A szolgáltatás eltávolításához törölje az alábbi erőforrások tárolására kijelölt erőforráscsoportot a Start/Stop VM v2 (előzetes verzió) támogatásához:

- A Azure Functions alkalmazások
- Ütemtervek Azure Logic Apps
- Az Application Insights-példány
- Azure Storage-fiók

## <a name="delete-the-dedicated-resource-group"></a>A dedikált erőforráscsoport törlése

Az erőforráscsoport törléséhez kövesse az [Azure Resource Manager erőforráscsoport és az erőforrás-törlés](../../azure-resource-manager/management/delete-resource-group.md) című cikkben ismertetett lépéseket.

## <a name="next-steps"></a>Következő lépések

A szolgáltatás újratelepítéséhez tekintse meg a [Start/Stop v2 (előzetes verzió) üzembe helyezése](deploy.md) című témakört.