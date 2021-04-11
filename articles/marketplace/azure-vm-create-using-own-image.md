---
title: Azure-beli virtuális gépek ajánlatának létrehozása az Azure Marketplace-en saját rendszerkép használatával
description: Ismerje meg, hogyan tehet közzé egy virtuálisgép-ajánlatot az Azure Marketplace-en saját rendszerkép használatával.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 4711ea76af83594ec529cfda13a308fbe6646398
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200467"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Virtuális gép létrehozása saját rendszerkép használatával

Ez a cikk bemutatja, hogyan hozhat létre és helyezhet üzembe egy felhasználó által biztosított virtuális gépet (VM) tartalmazó lemezképet.

> [!NOTE]
> Az eljárás megkezdése előtt tekintse át az Azure-beli virtuális gépekre vonatkozó [technikai követelményeket](marketplace-virtual-machines.md#technical-requirements) , beleértve a virtuális merevlemez (VHD) követelményeit.

Ha ehelyett egy jóváhagyott alaprendszerképet szeretne használni, kövesse a virtuálisgép- [rendszerkép létrehozása jóváhagyott alapból](azure-vm-create-using-approved-base.md)című témakör utasításait.

## <a name="configure-the-vm"></a>A virtuális gép konfigurálása

Ez a szakasz azt ismerteti, hogyan lehet méretezni, frissíteni és általánosítani egy Azure-beli virtuális gépet. Ezek a lépések szükségesek ahhoz, hogy előkészítse a virtuális gépet az Azure Marketplace-en való üzembe helyezéshez.

### <a name="size-the-vhds"></a>A virtuális merevlemezek mérete

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>A legújabb frissítések telepítése

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>Több biztonsági ellenőrzés végrehajtása

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Egyéni konfiguráció és ütemezett feladatok végrehajtása

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>A rendszerkép általánosítása

Az Azure Marketplace-en lévő összes lemezképet általános módon újrafelhasználhatónak kell lennie. Ennek eléréséhez általánosítva kell lennie az operációs rendszer virtuális merevlemezének, egy olyan műveletnek, amely eltávolítja az összes példány-specifikus azonosítót és a szoftver illesztőprogramjait egy virtuális gépről.

## <a name="bring-your-image-into-azure"></a>Rendszerkép beolvasása az Azure-ba

A rendszerképet háromféleképpen lehet az Azure-ba hozni:

1. Töltse fel a virtuális merevlemezt egy megosztott képtárba (SIG).
1. Töltse fel a VHD-t egy Azure Storage-fiókba.
1. A virtuális merevlemez kibontása felügyelt rendszerképből (rendszerkép-építési szolgáltatások használata esetén).

A következő három szakasz ezeket a beállításokat ismerteti.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>1. lehetőség: a virtuális merevlemez feltöltése megosztott rendszerkép-gyűjteményként

1. Töltse fel a VHD (ka) t a Storage-fiókba.
2. A Azure Portal keresse meg az **egyéni sablon üzembe helyezése** kifejezést.
3. Válassza **a saját sablon létrehozása lehetőséget a szerkesztőben**.
4. Másolja a következő Azure Resource Manager (ARM) sablont.

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

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="Minta kód képernyő a virtuális géphez.":::

1. Kattintson a **Mentés** gombra.
1. Az ebben a táblázatban szereplő paraméterek segítségével hajtsa végre az alábbi képernyőn látható mezőket.

| Paraméterek | Leírás |
| --- | --- |
| sourceStorageAccountResourceId | Azon forrás-tárolási fiók erőforrás-azonosítója, amelyben a blob VHD található.<br><br>Az erőforrás-azonosító beszerzéséhez nyissa meg a **Storage-fiókját** **Azure Portalon**, válassza a **Tulajdonságok menüpontot**, és másolja a **ResourceId** értéket. |
| sourceBlobUri | Az operációsrendszer-lemez VHD-blobjának blob URI-ja (a megadott Storage-fiókban kell lennie).<br><br>A blob URL-címének beszerzéséhez nyissa meg a **Storage-fiókját** **Azure Portalon**, nyissa meg a **blobot**, és másolja az **URL-címet** . |
| sourceBlobDataDisk0Uri | Az adatlemez VHD-blobjának blob URI-ja (a megadott Storage-fiókban kell lennie). Ha nem rendelkezik adatlemezzel, távolítsa el ezt a paramétert a sablonból.<br><br>A blob URL-címének beszerzéséhez nyissa meg a **Storage-fiókját** **Azure Portalon**, nyissa meg a **blobot**, és másolja az **URL-címet** . |
| sourceBlobDataDisk1Uri | További adatlemez VHD-blobjának blob URI-ja (a megadott Storage-fiókban kell lennie). Ha nem rendelkezik további adatlemezzel, távolítsa el ezt a paramétert a sablonból.<br><br>A blob URL-címének beszerzéséhez nyissa meg a **Storage-fiókját** **Azure Portalon**, nyissa meg a **blobot**, és másolja az **URL-címet** . |
| galleryName | A megosztott Képtár neve |
| galleryImageDefinitionName | A rendszerkép definíciójának neve |
| galleryImageVersionName | A létrehozandó rendszerkép-verzió neve, ebben a formátumban: `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="Megjeleníti az egyéni központi telepítési ablakot.":::

8. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejeződése után válassza a **Létrehozás** lehetőséget.

> [!TIP]
> A kiadói fióknak tulajdonosi hozzáféréssel kell rendelkeznie a SIG-rendszerkép közzétételéhez. Ha szükséges, kövesse az alábbi lépéseket a hozzáférés biztosításához:
>
> 1. Nyissa meg a megosztott rendszerkép-katalógust (SIG).
> 2. A bal oldali panelen válassza a **hozzáférés-vezérlés** (iam) lehetőséget.
> 3. Válassza a **Hozzáadás** lehetőséget, majd **adja hozzá a szerepkör-hozzárendelést**.
> 4. A **szerepkör** területen válassza a **tulajdonos** lehetőséget.
> 5. A **hozzáférésének hozzárendeléséhez** válassza a **felhasználó, csoport vagy egyszerű szolgáltatásnév** lehetőséget.
> 6. Adja meg a képet közzétevő személy Azure-beli e-mail-címét.
> 7. Kattintson a **Mentés** gombra.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="Megjelenik a szerepkör-hozzárendelés hozzáadása ablak.":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>2. lehetőség: a virtuális merevlemez feltöltése egy Storage-fiókba

Konfigurálja és készítse elő a virtuális gépet, amelyet a [Windows VHD vagy VHDX előkészítése az Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) -ba való feltöltéshez, illetve [linuxos virtuális merevlemez létrehozásához és feltöltéséhez](../virtual-machines/linux/create-upload-generic.md)című cikkben ismertetett módon kell feltölteni.

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>3. lehetőség: a virtuális merevlemez kibontása a felügyelt rendszerképből (rendszerkép-építési szolgáltatások használata esetén)

Ha olyan rendszerkép-építési szolgáltatást használ, mint a [csomagoló](https://www.packer.io/), előfordulhat, hogy ki kell bontania a virtuális merevlemezt a rendszerképből. Erre nincs közvetlen mód. Létre kell hoznia egy virtuális gépet, és ki kell bontania a virtuális merevlemezt a virtuálisgép-lemezről.

## <a name="create-the-vm-on-the-azure-portal"></a>A virtuális gép létrehozása a Azure Portal

Az alábbi lépéseket követve hozza létre az alapszintű VM-rendszerképet a [Azure Portal](https://ms.portal.azure.com/).

1. Jelentkezzen be az [Azure Portalra](https://ms.portal.azure.com/).
2. Válassza a **Virtuális gépek** lehetőséget.
3. Válassza a **+ Hozzáadás** lehetőséget a **virtuális gép létrehozása** képernyő megnyitásához.
4. Válassza ki a lemezképet a legördülő listából, vagy válassza az **összes nyilvános és privát rendszerkép tallózása** lehetőséget a rendelkezésre álló virtuálisgép-lemezképek kereséséhez vagy tallózásához.
5. A 2. **generációs** virtuális gépek létrehozásához nyissa meg a **speciális** lapot, és válassza a **2. generációs** lehetőséget.

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Válassza az 1. gen vagy a 2. lehetőséget.":::

6. Válassza ki a telepítendő virtuális gép méretét.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Válasszon egy ajánlott virtuálisgép-méretet a kiválasztott képhez.":::

7. Adja meg a virtuális gép létrehozásához szükséges egyéb adatokat.
8. Válassza a **felülvizsgálat + létrehozás** lehetőséget a választási lehetőségek áttekintéséhez. Amikor megjelenik az **érvényesítési** üzenet, válassza a **Létrehozás** lehetőséget.

Az Azure megkezdi a megadott virtuális gép üzembe helyezését. Az előrehaladás nyomon követéséhez válassza a bal oldali menü **Virtual Machines** lapját. Miután létrehozta a virtuális gép változásait a **futtatásra**.

## <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

A Windows vagy [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) [rendszerű](../virtual-machines/windows/connect-logon.md) virtuális géphez való kapcsolódáshoz tekintse meg az alábbi dokumentációt.

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Következő lépések

- [Tesztelje a virtuálisgép-rendszerképet](azure-vm-image-test.md) , és győződjön meg arról, hogy az megfelel az Azure Marketplace közzétételi követelményeinek. Ez nem kötelező.
- Ha nem szeretné tesztelni a virtuálisgép-rendszerképet, jelentkezzen be a [partner Centerben](https://partner.microsoft.com/) , és tegye közzé a SIG-rendszerképet (#1).
- Ha követte a #2 vagy #3 lehetőséget, akkor [létrehozza az SAS URI](azure-vm-get-sas-uri.md)-t.
- Ha nehézségekbe ütközött az új Azure-alapú virtuális merevlemez létrehozása során, tekintse meg [Az Azure Marketplace-hez készült virtuális gépekkel kapcsolatos gyakori kérdéseket](azure-vm-create-faq.md).
