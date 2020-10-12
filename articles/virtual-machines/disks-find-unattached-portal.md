---
title: A nem csatlakoztatott Azure-lemezek azonosítása – Azure Portal
description: Az Azure által felügyelt és nem felügyelt (VHD/oldal Blobok) lemezek a Azure Portal használatával történő megkeresése.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 06/01/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6632d65fa07788e35b24c2f957e713f824f6b091
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87542738"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks---azure-portal"></a>Nem csatlakoztatott Azure felügyelt és nem felügyelt lemezek keresése és törlése – Azure Portal

Amikor töröl egy virtuális gépet (VM) az Azure-ban, alapértelmezés szerint a virtuális géphez csatolt összes lemez nem törlődik. Ez segít megakadályozni az adatvesztést a virtuális gépek véletlen törlése miatt. A virtuális gép törlése után továbbra is fizetnie kell a nem csatlakoztatott lemezekre. Ebből a cikkből megtudhatja, hogyan keresheti meg és törölheti a nem csatolt lemezeket a Azure Portal használatával, és csökkentheti a szükségtelen költségeket.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Felügyelt lemezek: nem csatolt lemezek keresése és törlése

Ha nem csatlakoztatott felügyelt lemezekkel rendelkezik, és már nincs szüksége rájuk az adatokra, a következő folyamat ismerteti, hogyan keresheti meg őket a Azure Portal:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. **Lemezek**keresése és kiválasztása.

    A **lemezek** panelen megjelenik az összes lemez listája. **-** A **tulajdonos** oszlopban a "" értékkel rendelkező lemezek nem csatolt lemezek.

    [![Képernyőfelvétel a Managed Disks (felügyelt lemezek) panelről, ha egy lemez a tulajdonos oszlopban van, akkor az egy nem csatlakoztatott lemez.](media/disks-find-unattached-portal/managed-disk-unattached-owner.png)](media/disks-find-unattached-portal/managed-disk-owner-unattached.png#lightbox)

1. Válassza ki a törölni kívánt nem csatolt lemezt, amely megnyitja a lemez paneljét.
1. A lemez paneljén ellenőrizheti, hogy a lemez állapota nincs-e csatlakoztatva, majd válassza a **Törlés**lehetőséget.

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="Képernyőkép az egyes felügyelt lemezek panelről. Ez a panel a lemez állapotában nem csatoltként jelenik meg, ha nincs csatlakoztatva. Ezt a lemezt akkor törölheti, ha már nem kell megőriznie az adatvédelmet":::

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nem felügyelt lemezek: nem csatolt lemezek keresése és törlése

A nem felügyelt lemezek az [Azure Storage-fiókokban](../storage/common/storage-account-overview.md) [blobként](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) tárolt VHD-fájlok.

Ha olyan nem felügyelt lemezekkel rendelkezik, amelyek nem csatlakoznak egy virtuális géphez, a továbbiakban nincs szüksége rájuk az adatokra, és törölni szeretné őket, a következő folyamat ismerteti, hogyan teheti meg ezt a Azure Portal:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Keresse meg és válassza a **lemezek (klasszikus)** lehetőséget.

    Megjelenik az összes nem felügyelt lemez listája. **-** A **csatolt** oszlophoz tartozó "" nevű lemez nem csatolt lemez.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="Képernyőkép az egyes felügyelt lemezek panelről. Ez a panel a lemez állapotában nem csatoltként jelenik meg, ha nincs csatlakoztatva. Ezt a lemezt akkor törölheti, ha már nem kell megőriznie az adatvédelmet":::

1. Válassza ki a törölni kívánt, nem csatolt lemezt, amely megjeleníti a lemez paneljét.

1. A lemez paneljén ellenőrizheti, hogy nincs-e csatlakoztatva, mert a **csatolva** marad **-** .

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="Képernyőkép az egyes felügyelt lemezek panelről. Ez a panel a lemez állapotában nem csatoltként jelenik meg, ha nincs csatlakoztatva. Ezt a lemezt akkor törölheti, ha már nem kell megőriznie az adatvédelmet":::

1. Válassza a **Törlés** elemet.

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="Képernyőkép az egyes felügyelt lemezek panelről. Ez a panel a lemez állapotában nem csatoltként jelenik meg, ha nincs csatlakoztatva. Ezt a lemezt akkor törölheti, ha már nem kell megőriznie az adatvédelmet":::

## <a name="next-steps"></a>Következő lépések

Ha szeretné automatikusan megkeresni és törölni a nem csatlakoztatott Storage-fiókokat, tekintse meg a [CLI](linux/find-unattached-disks.md) -vagy [PowerShell](windows/find-unattached-disks.md) -cikkeket.

További információ: Storage- [fiók törlése](../storage/common/storage-account-create.md#delete-a-storage-account) és [árva lemezek azonosítása a PowerShell használatával](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell)
