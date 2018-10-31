---
title: Titkosított tárfiók üzembe helyezésére szolgáló Azure Resource Manager-sablon létrehozása | Microsoft Docs
description: A titkosított tárfiók üzembe helyezésére szolgáló sablont a Visual Studio Code-dal fogja létrehozni.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/18/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a3fc3e0cc30b379c84ac0ba12f733d2db4e41587
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945790"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>Oktatóanyag: Titkosított tárfiók üzembe helyezésére szolgáló Azure Resource Manager-sablon létrehozása

Ismerje meg, hogyan keresheti meg az Azure Resource Manager-sablon befejezéséhez szükséges adatokat.

Ebben az oktatóanyagban az Azure-beli gyorsindítási sablonok egyik alapszintű sablonját fogja használni egy Azure Storage-fiók létrehozásához.  Titkosított tárfiók létrehozásához a sablon referenciadokumentációjának használatával szabhatja testre az alapszintű sablont.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon ismertetése
> * A sablon szerkesztése
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/) [Resource Manager Tools bővítménnyel](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az ebben a rövid útmutatóban használt sablon [standard szintű tárfiók létrehozása](https://azure.microsoft.com/resources/templates/101-storage-account-create/) néven található meg. A sablon egy Azure Storage-fiókhoz tartozó erőforrást határoz meg.

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. A **File** (Fájl) > **Save as** (Mentés másként) lehetőség kiválasztásával mentheti a fájlt a helyi számítógépre, **azuredeploy.json** néven.

## <a name="understand-the-schema"></a>A séma bemutatása

A VS Code-ban csukja össze a sablont a gyökérszintig. Az ekkor előálló legegyszerűbb struktúra a következő elemeket tartalmazza:

![Resource Manager-sablon – Legegyszerűbb struktúra](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* **$schema**: adja meg a sablon nyelvének verziószámát tartalmazó JSON-sémafájl helyét.
* **contentVersion**: adjon meg egy tetszőleges értéket ehhez az elemhez a sablon lényeges módosításainak dokumentálásához.
* **parameters**: adja meg az üzembe helyezéskor beállított értékeket az erőforrás üzembe helyezésének testreszabásához.
* **variables**: adja meg a sablonban JSON-töredékekként használt értékeket a sablonnyelvi kifejezések leegyszerűsítéséhez.
* **resources**: adja meg az erőforráscsoportban üzembe helyezett vagy frissített erőforrástípusokat.
* **outputs**: adja meg az üzembe helyezés után visszaadott értékeket.

## <a name="use-parameters"></a>Paraméterek használata

Paraméterek megadásával testreszabhatja az üzemelő példányt úgy, hogy az adott környezetnek megfelelő értékeket ad meg. A tárfiókhoz tartozó értékek beállításakor a sablonban meghatározott paramétereket fogja használni.

![Resource Manager-sablon – Paraméterek](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-parameters.png)

Ebben a sablonban két paraméter van meghatározva. Figyelje meg a location.defaultValue paraméterben használt sablonfüggvényt:

```json
"defaultValue": "[resourceGroup().location]",
```

A resourceGroup() függvény az aktuális erőforráscsoportot jelölő objektumot adja vissza. A sablonfüggvények listájáért lásd az [Azure Resource Manager-sablonfüggvényeket](./resource-group-template-functions.md).

A sablonban meghatározott paraméterek használata:

```json
"location": "[parameters('location')]",
"name": "[parameters('storageAccountType')]"
```

## <a name="use-variables"></a>Változók használata

A változók segítségével a sablonban használható értékeket állíthat össze. A változók segítségével leegyszerűsíthetők a sablonok.

![Resource Manager-sablon – Változók](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

Ez a sablon egy változót (*storageAccountName*) határoz meg. A definícióban a következő két sablonfüggvény használjuk:

- **concat()**: sztringek összefűzésére. További információkért lásd a [concat](./resource-group-template-functions-string.md#concat) ismertetését.
- **uniqueString()**: determinisztikus kivonatsztring a paraméterekként megadott értékek alapján történő létrehozásához. Minden Azure-tárfióknak – az Azure egészét figyelembe véve – egyedi névvel kell rendelkeznie. Ez a függvény egy egyedi sztringet állít elő. További sztringfüggvényekért lásd a [sztringfüggvények](./resource-group-template-functions-string.md) ismertetését.

A sablonban meghatározott változó használata:

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>A sablon szerkesztése

Az oktatóanyag célja, hogy meghatározza a titkosított tárfiók létrehozásához szükséges sablont.  A mintasablon csak egy alapszintű, titkosítatlan tárfiókot hoz létre. A titkosítással kapcsolatos konfiguráció megkereséséhez az Azure Storage-fiók sablonreferenciáját fogja használni.

1. Tallózással keresse meg az [Azure Templates](https://docs.microsoft.com/azure/templates/) (Azure-sablonok) elemet.
2. A **Szűrés cím szerint** területen adja meg a **tárfiókokat**.
3. Válassza a **Referencia/Sablonreferencia/Tárhely/Tárfiókok** lehetőséget a következő képernyőkép alapján:

    ![Resource Manager-sablonreferencia – tárfiók](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

4. Keresse meg a titkosítással kapcsolatos definíció információit.  

    ```json
    "encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true
            }
        }
    }
    ```
5. A Visual Studio Code-ban módosítsa úgy a sablont, hogy a végső erőforráselem a következőképpen nézzen ki:
    
    ![Resource Manager-sablon – Titkosított tárfiók – Erőforrások](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A Visual Studio Code üzembehelyezési eljárásról szóló rövid útmutatójában tekintse meg [A sablon üzembe helyezése](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) című szakaszt.

Az alábbi képernyőképen látható az újonnan létrehozott tárfiókok listázására szolgáló parancssori felületi parancs, amely azt jelzi, hogy a Blob Storage-titkosítás engedélyezve lett.

![Azure Resource Manager – Titkosított tárfiók](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag azt ismertette, hogyan használhatja a sablonreferenciát egy létező sablon testreszabására. Több tárfiókpéldány létrehozása:

> [!div class="nextstepaction"]
> [Több példány létrehozása](./resource-manager-tutorial-create-multiple-instances.md)
