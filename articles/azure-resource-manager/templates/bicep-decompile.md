---
title: Sablonok konvertálása a JSON és a bicep között
description: Leírja Azure Resource Manager sablonok a bicep-ből a JSON-be és a JSON-ből a bicep-re való konvertálásának parancsait.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 6d242f5846996cd0f5b9510a1a2b9f2bf063a0c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103422187"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>ARM-sablonok konvertálása JSON és bicep között

Ez a cikk azt ismerteti, hogyan alakíthatja át Azure Resource Manager sablonokat (ARM-sablonokat) a JSON-ből a bicep és a bicep-ből a JSON-be.

Az átalakítási parancsok futtatásához telepíteni kell a [BICEP CLI](bicep-install.md) -t.

Az átalakítási parancsok olyan sablonokat hoznak létre, amelyek funkcionálisan egyenértékűek. Előfordulhat azonban, hogy nem egyeznek pontosan a megvalósításban. Sablon konvertálása JSON-ról a bicep-re, majd a JSON-re való visszatérés valószínűleg az eredeti sablonnal eltérő szintaxisú sablont eredményez. Telepítésekor a konvertált sablonok ugyanazt az eredményt adják.

## <a name="convert-from-json-to-bicep"></a>Konvertálás a JSON-ról a bicep-re

A bicep CLI parancssori felület lehetővé teszi a meglévő JSON-sablonok egy bicep-fájlba való lefordítását. A JSON-fájlok defordításához használja a következőt:

```azurecli
bicep decompile mainTemplate.json
```

Ez a parancs kiindulási pontot biztosít a bicep authoring számára. A parancs minden sablon esetében nem működik. A beágyazott sablonok jelenleg csak akkor vonhatók le, ha a belső kifejezés kiértékelési hatókörét használják. A másolási hurkokat használó sablonok nem fordíthatók le.

## <a name="convert-from-bicep-to-json"></a>Konvertálás a Bicepból a JSON-be

A bicep CLI-vel a bicep a JSON-re alakítható parancs is elérhető. JSON-fájl létrehozásához használja a következőt:

```azurecli
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>Sablon exportálása és átalakítás

Exportálhatja az erőforráscsoport sablonját, majd átadhatja azt közvetlenül a defordítási parancsnak. Az alábbi példa egy exportált sablon defordítását szemlélteti.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

[Exportálja a sablont](export-template-portal.md) a portálon keresztül.

Használja a `bicep decompile <filename>` fájlt a letöltött fájlon.

---

## <a name="side-by-side-view"></a>Párhuzamos nézet

A [bicep játszótér](https://aka.ms/bicepdemo) lehetővé teszi az egyenértékű JSON-és bicep-fájlok megtekintését egymás mellett. Kiválaszthat egy minta sablont mindkét verzió megjelenítéséhez. Vagy válassza a `Decompile` saját JSON-sablonjának feltöltését, és tekintse meg az egyenértékű bicep-fájlt.

## <a name="next-steps"></a>Következő lépések

További információ a bicep: [bicep oktatóanyag](./bicep-tutorial-create-first-bicep.md).
