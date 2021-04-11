---
title: VM-bővítményeket tartalmazó Azure-erőforráscsoportok exportálása
description: A virtuálisgép-bővítményeket tartalmazó Resource Manager-sablonok exportálása.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 12/05/2016
ms.openlocfilehash: df1ae43b2c6a74448a6782a43fb86f8f4939b13a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560004"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Virtuálisgép-bővítményeket tartalmazó erőforráscsoportok exportálása

Az Azure-erőforráscsoportok egy új Resource Manager-sablonba exportálhatók, amely aztán újra üzembe helyezhető. Az exportálási folyamat a meglévő erőforrásokat értelmezi, és egy olyan Resource Manager-sablont hoz létre, amely egy hasonló erőforráscsoport esetében van üzembe helyezett eredmény. Ha a virtuálisgép-bővítményeket tartalmazó erőforráscsoport esetében az erőforráscsoport-exportálás lehetőséget használja, több elemet is figyelembe kell venni, például a bővítmények kompatibilitását és a védett beállításokat.

Ez a dokumentum részletesen ismerteti, hogyan működik az erőforráscsoport-exportálási folyamat a virtuálisgép-bővítményekkel kapcsolatban, beleértve a támogatott bővítmények listáját, valamint a biztonságos adatok kezelésével kapcsolatos részleteket.

## <a name="supported-virtual-machine-extensions"></a>Támogatott virtuálisgép-bővítmények

Számos virtuálisgép-bővítmény érhető el. Nem minden bővítmény exportálható Resource Manager-sablonba az "Automation-parancsfájl" funkció használatával. Ha a virtuálisgép-bővítmény nem támogatott, akkor azt manuálisan kell visszahelyezni az exportált sablonba.

A következő bővítmények exportálhatók az Automation script szolgáltatással.

> Acronis Backup, Acronis Backup Linux, BG info, BMC CTM-ügynök Linux, BMC CTM-ügynök – Windows, Chef-ügyfél, egyéni parancsfájl, egyéni parancsfájl-kiterjesztés, egyéni parancsfájl Linux rendszerhez, Datadoggal Linux-ügynök, Datadoggal Windows-ügynök, Docker-bővítmény, DSC-bővítmény, Dynatrace Linux, Dynatrace Windows, HPE Security Application Defender, IaaS antimalware, IaaS diagnosztika, Linux Chef-ügyfél, Linux-diagnosztika , Site nonstop Linux Server, site nonstop Windows Server, Trend Micro DSA, Trend Micro DSA Linux, VM-hozzáférés Linux rendszerhez, VM-hozzáférés Linux rendszerhez, VM-pillanatkép, VM pillanatkép Linux

## <a name="export-the-resource-group"></a>Az erőforráscsoport exportálása

Egy erőforráscsoport újrafelhasználható sablonba való exportálásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be az Azure Portalra
2. A központi menüben kattintson az erőforráscsoportok elemre.
3. Válassza ki a cél erőforráscsoportot a listából.
4. Az erőforráscsoport panelen kattintson az Automation-parancsfájl elemre.

![Sablon exportálása](./media/export-templates/template-export.png)

A Azure Resource Manager automations parancsfájl egy Resource Manager-sablont, egy Parameters fájlt és számos példaként szolgáló telepítési parancsfájlt hoz létre, például a PowerShellt és az Azure CLI-t. Ekkor az exportált sablon a letöltés gombbal tölthető le, új sablonként hozzáadva a sablon-függvénytárhoz, vagy újból üzembe helyezhető a Deploy (üzembe helyezés) gomb használatával.

## <a name="configure-protected-settings"></a>Védett beállítások konfigurálása

Számos Azure-beli virtuálisgép-bővítmény tartalmaz egy védett beállítások konfigurációját, amely titkosítja a bizalmas adatokat, például a hitelesítő adatokat és a konfigurációs karakterláncokat. A védett beállításokat a rendszer nem exportálja az Automation-parancsfájllal. Ha szükséges, a védett beállításokat újra be kell szúrni az exportált sablonba.

### <a name="step-1---remove-template-parameter"></a>1. lépés: sablon paraméterének eltávolítása

Az erőforráscsoport exportálásakor a rendszer egyetlen sablon paramétert hoz létre, amely megadja az exportált védett beállítások értékét. Ezt a paramétert el lehet távolítani. A paraméter eltávolításához tekintse át a paraméterek listáját, és törölje a JSON-példához hasonlóan hasonlító paramétert.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>2. lépés – a védett beállítások tulajdonságainak beolvasása

Mivel minden védett beállításhoz szükséges tulajdonságok vannak megadva, ezeknek a tulajdonságoknak a listáját össze kell gyűjteni. A védett beállítások konfigurációjának minden paraméterét a [GitHub Azure Resource Manager sémájánál](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json)találja. Ez a séma csak a jelen dokumentum Áttekintés szakaszában felsorolt bővítmények paramétereinek készletét tartalmazza. 

A séma adattárában keresse meg a kívánt kiterjesztést, ebben a példában `IaaSDiagnostics` . Ha a bővítmények `protectedSettings` objektum található, jegyezze fel az egyes paramétereket. A bővítmény példájában `IaasDiagnostic` a szükséges paraméterek a következők:, `storageAccountName` `storageAccountKey` és `storageAccountEndPoint` .

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>3. lépés – a védett konfiguráció újbóli létrehozása

Az exportált sablonban keresse meg `protectedSettings` és cserélje le az exportált védett beállítás objektumot egy olyan új elemre, amely tartalmazza a szükséges kiterjesztési paramétereket és az egyes értékek értékét.

A bővítmény példájában `IaasDiagnostic` az új védett beállítási konfiguráció a következő példához hasonlóan fog kinézni:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

A végső kiterjesztési erőforrás a következő JSON-példához hasonlóan néz ki:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Ha a tulajdonságértékek megadásához a sablon paramétereit használja, ezeket létre kell hozni. Ha sablon-paramétereket hoz létre a védett beállítási értékekhez, ügyeljen arra, hogy a `SecureString` paraméter típusát használja, hogy a bizalmas értékek biztonságosak legyenek. További információ a paraméterek használatáról: [Azure Resource Manager sablonok készítése](../../azure-resource-manager/templates/template-syntax.md).

A bővítmény példájában `IaasDiagnostic` a következő paraméterek jönnek létre a Resource Manager-sablon paraméterek szakaszában.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

Ezen a ponton a sablon üzembe helyezhető bármely sablon-telepítési módszer használatával.
