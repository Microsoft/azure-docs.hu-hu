---
title: Azure Storage Explorer használata az Azure Managed Disks kezeléséhez
description: Megtudhatja, hogyan tölthet fel, tölthet le és telepíthet át Azure-beli felügyelt lemezeket a régiók között, és hogyan hozhat létre egy felügyelt lemezről készült pillanatképet a Azure Storage Explorer használatával.
author: roygara
ms.author: rogarana
ms.date: 09/25/2019
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 9dfce7b76eed5bfc9f4979c0e3041b6c65c28422
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88749379"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>Azure Storage Explorer használata az Azure Managed Disks kezeléséhez

Storage Explorer 1.10.0 lehetővé teszi a felhasználók számára a felügyelt lemezek feltöltését, letöltését és másolását, valamint Pillanatképek készítését. Ezen további képességek miatt a Storage Explorer segítségével áttelepítheti az adatait a helyszínről az Azure-ba, és áttelepítheti az adatait az Azure-régiók között.

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez a következőkre lesz szüksége:
- Azure-előfizetés
- Egy vagy több Azure Managed Disks
- A [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) legújabb verziója

## <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez

Ha a Storage Explorer nem csatlakozik az Azure-hoz, nem fogja tudni használni az erőforrásokat az erőforrások kezeléséhez. Ez a szakasz az Azure-fiókhoz való csatlakozást veszi át, így Storage Explorer használatával felügyelheti az erőforrásokat.

1. Indítsa el Azure Storage Explorer, és kattintson a bal oldali **beépülő** modul ikonra.

    ![Kattintson a beépülő modul ikonjára](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Válassza **Az Azure-fiók hozzáadása** lehetőséget, majd kattintson a **tovább** gombra.

    ![Azure-fiók hozzáadása](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. Az **Azure bejelentkezés** párbeszédpanelen adja meg az Azure-beli hitelesítő adatait.

    ![Azure bejelentkezési párbeszédpanel](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Válassza ki az előfizetést a listából, majd kattintson az **Alkalmaz** gombra.

    ![Az előfizetés kiválasztása](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Felügyelt lemez feltöltése helyszíni VHD-ből

1. A bal oldali ablaktáblán bontsa ki a **lemezek** elemet, majd válassza ki azt az erőforráscsoportot, amelybe fel szeretné tölteni a lemezt.

    ![1. erőforráscsoport kiválasztása](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Válassza a **Feltöltés** lehetőséget.

    ![Feltöltés kiválasztása](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. A **virtuális merevlemez feltöltése** lapon adja meg a forrás VHD-jét, a lemez nevét, az operációs rendszer típusát, a lemezt, ahová fel kívánja tölteni a lemezt, valamint a fiók típusát. Egyes régiókban a rendelkezésre állási zónák támogatottak, az adott régiókban választhat egy tetszőleges zónát.
1. Válassza a **Létrehozás** lehetőséget a lemez feltöltésének megkezdéséhez.

    ![VHD-fájl feltöltése párbeszédpanel](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. A feltöltés állapota mostantól megjelenik a **tevékenységekben**.

    ![Feltöltés állapota](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Ha a feltöltés befejeződött, és a jobb oldali ablaktáblában nem jelenik meg a lemez, válassza a **frissítés** lehetőséget.

## <a name="download-a-managed-disk"></a>Felügyelt lemez letöltése

A következő lépések azt ismertetik, hogyan tölthető le egy felügyelt lemez egy helyszíni virtuális merevlemezre. A lemez állapotát le kell **kapcsolni** a letöltéshez, ezért nem tölthető le egy **csatlakoztatott** lemez.

1. Ha még nincs kibontva, a bal oldali ablaktáblán bontsa ki a **lemezek** csomópontot, és válassza ki azt az erőforráscsoportot, amelyről le szeretné tölteni a lemezt.

    ![1. erőforráscsoport kiválasztása](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. A jobb oldali ablaktáblán válassza ki a letölteni kívánt lemezt.
1. Válassza a **Letöltés** lehetőséget, majd válassza ki, hová szeretné menteni a lemezt.

    ![Felügyelt lemez letöltése](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Válassza a **Mentés** lehetőséget, és a lemez letöltése megkezdődik. A letöltés állapota a **tevékenységekben** fog megjelenni.

    ![Letöltés állapota](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Felügyelt lemez másolása

A Storage Explorer segítségével a különböző régiókban lévő, vagy azokon belül található, többhelyes lemezek másolhatók. Lemez másolása:

1. A bal oldali **lemezek** legördülő listából válassza ki azt az erőforráscsoportot, amely a másolni kívánt lemezt tartalmazza.

    ![1. erőforráscsoport kiválasztása](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. A jobb oldali ablaktáblán válassza ki a másolni kívánt lemezt, majd válassza a **Másolás** lehetőséget.

    ![Felügyelt lemez másolása](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. A bal oldali panelen válassza ki azt az erőforráscsoportot, amelyben be szeretné illeszteni a lemezt.

    ![Válassza ki a 2. erőforráscsoportot](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. A jobb oldali ablaktáblán kattintson a **Beillesztés** elemre.

    ![Felügyelt lemez beillesztése](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. A **lemez beillesztése** párbeszédpanelen adja meg az értékeket. Megadhat egy rendelkezésre állási zónát is a támogatott régiókban.

    ![Lemez beillesztése párbeszédpanel](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Válassza a **Beillesztés** lehetőséget, és a lemez a másolás megkezdése után az állapot megjelenik a **tevékenységek** területen.

    ![Beillesztési állapot másolása](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Pillanatkép létrehozása

1. A bal oldali **lemezek** legördülő listából válassza ki azt az erőforráscsoportot, amely a pillanatképet tartalmazó lemezt tartalmazza.

    ![1. erőforráscsoport kiválasztása](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. A jobb oldalon válassza ki azt a lemezt, amelyről pillanatképet szeretne készíteni, majd válassza a **pillanatkép létrehozása** lehetőséget.

    ![Pillanatkép létrehozása](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. A **pillanatkép létrehozása** lapon adja meg a pillanatkép nevét, valamint azt az erőforráscsoportot, amelyben létre kívánja hozni. Ezután kattintson a **Létrehozás** elemre.

    ![Pillanatkép létrehozása párbeszédpanel](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. A pillanatkép létrehozása után a **tevékenységek** területen a **Megnyitás a portálon** lehetőségre kattintva megtekintheti a pillanatképet a Azure Portal.

    ![Pillanatkép megnyitása a portálon](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Következő lépések


Megtudhatja, hogyan [hozhat létre virtuális gépet virtuális merevlemezről a Azure Portal használatával](windows/create-vm-specialized-portal.md).

Megtudhatja, hogyan [csatolhat felügyelt adatlemezt egy Windows rendszerű virtuális géphez a Azure Portal használatával](windows/attach-managed-disk-portal.md).