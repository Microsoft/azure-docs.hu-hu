---
title: Azure-beli virtuálisgép-ajánlat létrehozása Azure Marketplace rendszerkép használatával
description: Megtudhatja, hogyan tehet közzé virtuálisgép-ajánlatot Azure Marketplace rendszerkép használatával.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: 47fe7b42b68ae42f74a74e5fc69c8d1041d3bf8d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727120"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Virtuális gép létrehozása saját rendszerkép használatával

Ez a cikk azt ismerteti, hogyan hozhat létre és helyezhet üzembe felhasználó által megadott virtuálisgép-rendszerképeket.

> [!NOTE]
> Mielőtt elkezdené ezt az [](marketplace-virtual-machines.md#technical-requirements) eljárást, tekintse át az Azure-beli virtuális gépekre vonatkozó ajánlatok műszaki követelményeit, beleértve a virtuális merevlemezre (VHD) vonatkozó követelményeket.

Ha ehelyett jóváhagyott alapként használt rendszerképet használ, kövesse a Virtuálisgép-rendszerkép létrehozása [jóváhagyott alapból útmutatót.](azure-vm-create-using-approved-base.md)

## <a name="configure-the-vm"></a>A virtuális gép konfigurálása

Ez a szakasz az Azure-beli virtuális gépek méretének, frissítésének és általánosizálásának a módszerét ismerteti. Ezek a lépések szükségesek a virtuális gép előkészítéséhez a virtuális gép Azure Marketplace.

### <a name="size-the-vhds"></a>A virtuális merevlemezek mérete

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>A legújabb frissítések telepítése

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>További biztonsági ellenőrzések végrehajtása

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Egyéni konfiguráció és ütemezett feladatok végrehajtása

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>A rendszerkép általánosizálása

A rendszerképnek Azure Marketplace általános módon újrahasználhatónak kell lennie. Ehhez az operációs rendszer virtuális merevlemezét általánosított művelettel kell elérni, amely eltávolítja az összes példányspecifikus azonosítót és szoftverillesztőt a virtuális gépről.

## <a name="bring-your-image-into-azure"></a>Rendszerkép behozása az Azure-ba

> [!NOTE]
> A SIG-t tartalmazó Azure-előfizetésnek a közzétevői fiókkal azonos bérlőn kell lennie a közzétételhez. Emellett a közzétevői fióknak legalább Közreműködői hozzáféréssel kell lennie a SIG-t tartalmazó előfizetéshez.

A rendszerképet háromféleképpen hozhatja be az Azure-ba:

1. Töltse fel a vhd-t egy Shared Image Gallery (SIG).
1. Töltse fel a VHD-t egy Azure Storage-fiókba.
1. Bontsa ki a vhd-t egy felügyelt rendszerképből (ha rendszerkép-építési szolgáltatásokat használ).

A következő három szakasz ezeket a lehetőségeket ismerteti.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>1. lehetőség: Töltse fel a virtuális merevlemezt Shared Image Gallery

1. Vhd(k) feltöltése a Tárfiókba.
2. A Azure Portal keresse meg **a Deploy a custom template (Egyéni sablon üzembe helyezése) et.**
3. Válassza **a Build your own template (Saját sablon létrehozása a szerkesztőben) lehetőséget.**
4. Másolja ki a következő Azure Resource Manager (ARM) sablont.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sourceStorageAccountResourceId": {
          "type": "string",
          "metadata": {
            "description": "Resource ID of the source storage account that the blob vhd resides in."
          }
        },
        "sourceBlobUri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk0Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk1Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "galleryName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Shared Image Gallery."
          }
        },
        "galleryImageDefinitionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Definition."
          }
        },
        "galleryImageVersionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Version - should follow <MajorVersion>.<MinorVersion>.<Patch>."
          }
        }
      },
      "resources": [
        {
          "type": "Microsoft.Compute/galleries/images/versions",
          "name": "[concat(parameters('galleryName'), '/', parameters('galleryImageDefinitionName'), '/', parameters('galleryImageVersionName'))]",
          "apiVersion": "2020-09-30",
          "location": "[resourceGroup().location]",
          "properties": {
            "storageProfile": {
              "osDiskImage": {
                "source": {
                  "id": "[parameters('sourceStorageAccountResourceId')]",
                  "uri": "[parameters('sourceBlobUri')]"
                }
              },
    
              "dataDiskImages": [
                {
                  "lun": 0,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk0Uri')]"
                  }
                },
                {
                  "lun": 1,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk1Uri')]"
                  }
                }
              ]
            }
          }
        }
      ]
    }
    
    ```

5. Illessze be a sablont a szerkesztőbe.

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="Mintakódképernyő virtuális géphez.":::

1. Kattintson a **Mentés** gombra.
1. A táblázatban található paraméterekkel töltse ki a következő képernyő mezőit.

| Paraméterek | Description |
| --- | --- |
| sourceStorageAccountResourceId | Annak a forrás tárfióknak az erőforrás-azonosítója, amelyben a blob vhd található.<br><br>Az erőforrás-azonosítót a  tárfiókban, a Azure Portal **a** **Tulajdonságok** oldalon, és másolja ki a **ResourceID** értékét. |
| sourceBlobUri | Az operációsrendszer-lemez VHD-blobja blob URI-ját (a megadott tárfiókban kell lennie).<br><br>A blob URL-címének le Azure Portal a **storage-fiókjához,** majd a **blobhoz,** és másolja ki **az URL-értéket.** |
| sourceBlobDataDisk0Uri | Az adatlemez VHD-blobja blob URI-ját (a megadott tárfiókban kell lennie). Ha nem tartalmaz adatlemezt, távolítsa el ezt a paramétert a sablonból.<br><br>A blob URL-címének  le Azure Portal a Storage-fiókjához, majd a **blobhoz,** és másolja ki az **URL-címet.** |
| sourceBlobDataDisk1Uri | A további adatlemez vhd-blobja blob URI-ját (a megadott tárfiókban kell lennie). Ha nincs további adatlemeze, távolítsa el ezt a paramétert a sablonból.<br><br>A blob URL-címének  le Azure Portal a Storage-fiókjához, majd a **blobhoz,** és másolja ki az **URL-címet.** |
| galleryName (katalógusnév) | A Shared Image Gallery |
| galleryImageDefinitionName | A képdefiníció neve |
| galleryImageVersionName | A létrehozni kívánt rendszerképverzió neve a következő formátumban: `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="Megjeleníti az egyéni üzembe helyezési ablakot.":::

8. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejezése után válassza a **Létrehozás lehetőséget.**

> [!TIP]
> A közzétevői fióknak "Tulajdonos" hozzáféréssel kell lennie a SIG-rendszerkép közzétételéhez. Szükség esetén kövesse az alábbi lépéseket a hozzáférés megadásához:
>
> 1. Ugrás a Shared Image Gallery (SIG).
> 2. A **bal oldali panelen** válassza a Hozzáférés-vezérlés (IAM) lehetőséget.
> 3. Válassza **a Hozzáadás,** majd a **Szerepkör-hozzárendelés hozzáadása lehetőséget.**
> 4. A **Szerepkör mezőben** válassza a **Tulajdonos lehetőséget.**
> 5. A **Hozzáférés hozzárendelése a szolgáltatáshoz beállításnál** válassza a **Felhasználó, csoport vagy szolgáltatásnév lehetőséget.**
> 6. Adja meg a rendszerképet közzétenő személy Azure-e-mail-címét.
> 7. Kattintson a **Mentés** gombra.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="Megjelenik a Szerepkör-hozzárendelés hozzáadása ablak.":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>2. lehetőség: A VHD feltöltése egy tárfiókba

Konfigurálja és készítse elő a virtuális gépet a feltöltéshez [a Prepare a Windows VHD or VHDX to upload to Azure (Windows VHD vagy VHDX](../virtual-machines/windows/prepare-for-upload-vhd-image.md) előkészítése az Azure-ba való feltöltéshez) vagy Create and Upload a Linux VHD (Linux rendszerű virtuális merevlemez létrehozása és [feltöltése) dokumentumban leírtak szerint.](../virtual-machines/linux/create-upload-generic.md)

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>3. lehetőség: Virtuális merevlemez kinyerása felügyelt rendszerképből (ha rendszerkép-építési szolgáltatásokat használ)

Ha olyan rendszerkép-építési szolgáltatást használ, mint a [Packer,](https://www.packer.io/)előfordulhat, hogy ki kell csomagolni a virtuális merevlemezt a lemezképből. Erre nincs közvetlen mód. Létre kell hoznia egy virtuális gépet, és ki kell bonta a virtuális merevlemezt a virtuálisgép-lemezről.

## <a name="create-the-vm-on-the-azure-portal"></a>Hozza létre a virtuális gépet a Azure Portal

Kövesse az alábbi lépéseket az alap virtuálisgép-rendszerkép létrehozásához a [Azure Portal.](https://ms.portal.azure.com/)

1. Jelentkezzen be az [Azure Portalra](https://ms.portal.azure.com/).
2. Válassza a **Virtuális gépek** lehetőséget.
3. Válassza **a + Hozzáadás** lehetőséget a Virtuális gép létrehozása képernyő **megnyitásához.**
4. Válassza ki a rendszerképet  a legördülő listából, vagy válassza az Összes nyilvános és privát rendszerkép tallózása lehetőséget az összes elérhető virtuálisgép-rendszerkép keresésére vagy tallózásra.
5. **2. generációs** virtuális gép létrehozásához kattintson a Speciális **lapra,** és válassza a **Gen 2** lehetőséget.

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Válassza a Gen 1 vagy Gen 2 lehetőséget.":::

6. Válassza ki az üzembe helyezni kívánt virtuális gép méretét.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Válasszon egy javasolt virtuálisgép-méretet a kiválasztott rendszerképhez.":::

7. Adja meg a virtuális gép létrehozásához szükséges egyéb adatokat.
8. Válassza **az Áttekintés + létrehozás lehetőséget** a választási lehetőségek áttekintéshez. Amikor **megjelenik az Ellenőrzésen** áteső üzenet, válassza a **Létrehozás lehetőséget.**

Az Azure elkezdi kiépítni a megadott virtuális gépet. A folyamat előrehaladásának nyomon követéséhez válassza **Virtual Machines** fület a bal oldali menüben. A létrehozása után a Virtuális gép állapota Fut **állapotra változik.**

## <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

A Windows vagy [Linux](../virtual-machines/windows/connect-logon.md) rendszerű virtuális géphez való csatlakozáshoz tekintse meg a következő [dokumentációt.](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm)

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Következő lépések

- [Tesztelje a virtuálisgép-rendszerképet,](azure-vm-image-test.md) hogy az megfeleljen Azure Marketplace követelményeknek. Ez nem kötelező.
- Ha nem szeretné tesztelni a virtuálisgép-rendszerképet, jelentkezzen be az Partnerközpont, és tegye közzé a SIG-rendszerképet (#1). [](https://partner.microsoft.com/)
- Ha követte a #2 vagy #3, hozza létre a [SAS URI-t.](azure-vm-get-sas-uri.md)
- Ha nehézségekbe ütközött az új Azure-alapú virtuális merevlemez létrehozása során, tekintse meg a virtuális gépekkel kapcsolatos gyakori [kérdéseket a Azure Marketplace.](azure-vm-create-faq.md)
