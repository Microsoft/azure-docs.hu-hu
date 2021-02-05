---
title: Végpontok közötti titkosítás engedélyezése a gazdagép-Azure Portal által felügyelt lemezek titkosításával
description: Használjon titkosítást a gazdagépen az Azure Managed Disks-Azure Portal teljes körű titkosításának engedélyezéséhez.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ba7d6d8deb2034f8b2a853cf74635687561c41ea
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573602"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Az Azure Portal használatával engedélyezheti a végpontok közötti titkosítást a gazdagépen lévő titkosítás használatával

Amikor engedélyezi a titkosítást a gazdagépen, a virtuálisgép-gazdagépen tárolt adatok titkosítva maradnak a tárolási szolgáltatásba titkosított adatforgalomban. A gazdagépen található titkosítással, valamint az egyéb felügyelt lemezes titkosítási típusokkal kapcsolatos elméleti információk:

* Linux: [titkosítás a virtuális gép adatai számára a gazdagépek végpontok közötti titkosításával](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

* Windows: [titkosítás a virtuális gép adatai számára a gazdagépen, végpontok közötti titkosítással](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Támogatott régiók

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Támogatott virtuálisgép-méretek

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a virtuális gépekhez vagy virtuálisgép-méretezési csoportokhoz titkosítást lehessen használni a gazdagépen, be kell szereznie a funkciót az előfizetésében. Küldjön egy e-mailt az encryptionAtHost@microsoft.com előfizetési azonosítókkal, hogy a szolgáltatás engedélyezve legyen az előfizetésekhez.

Jelentkezzen be a Azure Portalba a [megadott hivatkozás](https://aka.ms/diskencryptionupdates)használatával.

> [!IMPORTANT]
> A Azure Portal eléréséhez a [megadott hivatkozást](https://aka.ms/diskencryptionupdates) kell használnia. A gazdagépen lévő titkosítás jelenleg nem látható a nyilvános Azure Portal a hivatkozás használata nélkül.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Azure Key Vault és lemezes titkosítási csoport létrehozása

Ha a szolgáltatás engedélyezve van, be kell állítania egy Azure Key Vault és egy lemezes titkosítási készletet, ha még nem tette meg.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése

Egy új virtuális gépet kell üzembe helyeznie, hogy engedélyezze a titkosítást a gazdagépen, a meglévő virtuális gépeken nem engedélyezhető.

1. Keressen rá **Virtual Machines** , és válassza a **+ Hozzáadás** elemet a virtuális gép létrehozásához.
1. Hozzon létre egy új virtuális gépet, válasszon ki egy megfelelő régiót és egy támogatott VM-méretet.
1. Adja meg a többi értéket az **alapszintű** panelen, majd lépjen a **lemezek** panelre.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Képernyőfelvétel: a virtuális gépek létrehozási alapjai panel, a régió és a V M méret van kiemelve.":::

1. A **lemezek** panelen válassza az **Igen** lehetőséget a **titkosításhoz a gazdagépen**.
1. Végezze el a többi kijelölést úgy, ahogy szeretné.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="A virtuális gépek létrehozási lemezei panel képernyőképe a gazdagépen lévő titkosítás kiemelése.":::

1. Fejezze be a virtuális gép telepítési folyamatát, és válassza ki a környezetnek megfelelő beállításokat.

Már telepített egy virtuális gépet, amelyen engedélyezve van a titkosítás a gazdagépen, az összes társított lemez titkosítása a gazdagépen titkosítva történik.

## <a name="next-steps"></a>Következő lépések

[Azure Resource Manager sablon mintái](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
