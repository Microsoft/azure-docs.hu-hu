---
title: Virtuálisgép-méretezési csoport létrehozása és titkosítása Azure Resource Manager-sablonokkal
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és titkosíthat virtuálisgép-méretezési készleteket Azure Resource Manager sablonok használatával
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 4284e94f8d8d0effd160c5048f54fcbede417e38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86129751"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Virtuálisgép-méretezési csoportok titkosítása Azure Resource Manager

A Linux rendszerű virtuálisgép-méretezési csoportok titkosítása vagy visszafejtése Azure Resource Manager-sablonok használatával végezhető el.

## <a name="deploying-templates"></a>Sablonok üzembe helyezése

Először válassza ki azt a sablont, amely megfelel a forgatókönyvnek.

- [Lemez titkosításának engedélyezése futó linuxos virtuálisgép-méretezési csoporton](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Lemez titkosításának engedélyezése egy futó Windowsos virtuálisgép-méretezési csoporton](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Linux rendszerű virtuális gépek virtuálisgép-méretezési csoportjának üzembe helyezése Jumpbox és a titkosítás engedélyezése linuxos virtuálisgép-méretezési csoportokban](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Windows rendszerű virtuális gépek virtuálisgép-méretezési csoportjának üzembe helyezése Jumpbox és a titkosítás engedélyezése a Windowsos virtuálisgép-méretezési csoportokban](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Lemez titkosításának letiltása egy futó linuxos virtuálisgép-méretezési csoporton](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Lemez titkosításának letiltása egy futó Windowsos virtuálisgép-méretezési csoporton](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Ez után kövesse az alábbi lépéseket:

1. Kattintson az **Üzembe helyezés az Azure-ban** lehetőségre.
2. Adja meg a kötelező mezőket, majd fogadja el a feltételeket és a kikötéseket.
3. A sablon üzembe helyezéséhez kattintson a **vásárlás** elemre.

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption a virtuálisgép-méretezési csoportokhoz](disk-encryption-overview.md)
- [Virtuálisgép-méretezési csoportok titkosítása az Azure CLI használatával](disk-encryption-cli.md)
- [Virtuálisgép-méretezési csoportok titkosítása a Azure PowerShell használatával](disk-encryption-powershell.md)
- [Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md)
- [A Azure Disk Encryption használata a virtuálisgép-méretezési csoport bővítményének előkészítésével](disk-encryption-extension-sequencing.md)
