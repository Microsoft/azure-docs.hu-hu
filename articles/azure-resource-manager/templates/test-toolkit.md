---
title: ARM-sablon tesztelési eszközkészlete
description: Útmutatás a Azure Resource Manager sablon (ARM-sablon) tesztelési eszközkészletének futtatásához a sablonban. Az eszközkészlet segítségével megtekintheti, hogy végrehajtotta-e a javasolt eljárásokat.
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: e5ad0b6dca7718166517b52148fbc6dd49f38869
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674012"
---
# <a name="use-arm-template-test-toolkit"></a>ARM-sablon tesztelési eszközkészletének használata

A [Azure Resource Manager-sablon (ARM-sablon) tesztelési eszközkészlete](https://aka.ms/arm-ttk) ellenőrzi, hogy a sablon ajánlott eljárásokat használ-e. Ha a sablon nem felel meg az ajánlott eljárásoknak, a figyelmeztetések listáját adja vissza a javasolt módosításokkal. A test Toolkit használatával megtudhatja, hogyan kerülheti el a sablonok fejlesztésével kapcsolatos gyakori problémákat.

A test Toolkit az [alapértelmezett tesztek készletét](test-cases.md)tartalmazza. Ezek a tesztek javaslatok, de nem szükségesek. Eldöntheti, hogy mely tesztek relevánsak a céljaihoz, és hogy milyen tesztek futnak.

Ez a cikk bemutatja, hogyan futtathatja a tesztelési eszközkészletet, és hogyan adhat hozzá vagy távolíthat el teszteket. Az alapértelmezett tesztek leírását lásd: [eszközkészlet-tesztelési esetek](test-cases.md).

Az eszközkészlet PowerShell-parancsfájlok egy készlete, amely a PowerShell vagy a parancssori felület egyik parancsának használatával futtatható.

## <a name="install-on-windows"></a>Telepítés Windows rendszeren

1. Ha még nincs telepítve a PowerShell, [telepítse a PowerShellt Windows rendszeren](/powershell/scripting/install/installing-powershell-core-on-windows).

1. [Töltse le a test Toolkit legújabb. zip fájlját](https://aka.ms/arm-ttk-latest) , és bontsa ki.

1. Indítsa el a PowerShellt.

1. Navigáljon ahhoz a mappához, ahová kibontotta a tesztelési eszközkészletet. A mappában navigáljon a **ARM-TTK** mappára.

1. Ha a [végrehajtási házirend](/powershell/module/microsoft.powershell.core/about/about_execution_policies) blokkolja az internetről származó parancsfájlokat, fel kell oldania a parancsfájlok feloldását. Győződjön meg arról, hogy az **ARM-TTK** mappában van.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importálja a modult.

   ```powershell
   Import-Module .\arm-ttk.psd1
   ```

1. A tesztek futtatásához használja a következő parancsot:

   ```powershell
   Test-AzTemplate -TemplatePath \path\to\template
   ```

## <a name="install-on-linux"></a>Telepítés Linux rendszeren

1. Ha még nincs telepítve a PowerShell, [telepítse a PowerShellt Linux rendszeren](/powershell/scripting/install/installing-powershell-core-on-linux).

1. [Töltse le a test Toolkit legújabb. zip fájlját](https://aka.ms/arm-ttk-latest) , és bontsa ki.

1. Indítsa el a PowerShellt.

   ```bash
   pwsh
   ```

1. Navigáljon ahhoz a mappához, ahová kibontotta a tesztelési eszközkészletet. A mappában navigáljon a **ARM-TTK** mappára.

1. Ha a [végrehajtási házirend](/powershell/module/microsoft.powershell.core/about/about_execution_policies) blokkolja az internetről származó parancsfájlokat, fel kell oldania a parancsfájlok feloldását. Győződjön meg arról, hogy az **ARM-TTK** mappában van.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importálja a modult.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. A tesztek futtatásához használja a következő parancsot:

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="install-on-macos"></a>Telepítés macOS rendszeren

1. Ha még nincs telepítve a PowerShell, [telepítse a PowerShellt MacOS rendszeren](/powershell/scripting/install/installing-powershell-core-on-macos).

1. `coreutils` telepítése:

   ```bash
   brew install coreutils
   ```

1. [Töltse le a test Toolkit legújabb. zip fájlját](https://aka.ms/arm-ttk-latest) , és bontsa ki.

1. Indítsa el a PowerShellt.

   ```bash
   pwsh
   ```

1. Navigáljon ahhoz a mappához, ahová kibontotta a tesztelési eszközkészletet. A mappában navigáljon a **ARM-TTK** mappára.

1. Ha a [végrehajtási házirend](/powershell/module/microsoft.powershell.core/about/about_execution_policies) blokkolja az internetről származó parancsfájlokat, fel kell oldania a parancsfájlok feloldását. Győződjön meg arról, hogy az **ARM-TTK** mappában van.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importálja a modult.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. A tesztek futtatásához használja a következő parancsot:

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="result-format"></a>Az eredmény formátuma

Az átadott tesztek **zöld** színnel és a **[+]** előtaggal jelennek meg.

A sikertelen tesztek **piros színnel** jelennek meg, és a **[-]** előtaggal vannak ellátva.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="teszt eredményeinek megtekintése":::

A szöveges eredmények a következők:

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>Paraméterek tesztelése

Ha megadja a **-TemplatePath** paramétert, az eszközkészlet az azuredeploy.json vagy maintemplate.jsnevű sablonban megjelenő mappában található. Először a sablont teszteli, majd az összes többi sablont teszteli a mappában és annak almappáiban. A többi sablon csatolt sablonként van tesztelve. Ha az elérési út tartalmaz egy [CreateUiDefinition.js](../managed-applications/create-uidefinition-overview.md)nevű fájlt, akkor a felhasználói felületi definícióhoz kapcsolódó teszteket futtat.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

A mappában található egy fájl teszteléséhez adja hozzá a **-file** paramétert. A mappának azonban továbbra is rendelkeznie kell egy azuredeploy.jsvagy maintemplate.jsnevű fő sablonnal.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

Alapértelmezés szerint az összes teszt le van futtatva. Az egyedi tesztek futtatásához használja a **-test** paramétert. Adja meg a teszt nevét. A nevekért lásd: az [eszközkészlet tesztelési esetei](test-cases.md).

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Tesztek testreszabása

ARM-sablonok esetében az eszközkészlet a **\arm-ttk\testcases\deploymentTemplate** mappában található összes tesztet futtatja. Ha végleg el szeretné távolítani a tesztet, törölje a fájlt a mappából.

A [CreateUiDefinition](../managed-applications/create-uidefinition-overview.md) -fájlok esetében a **\arm-ttk\testcases\CreateUiDefinition** mappában futtatja az összes tesztet.

Saját teszt hozzáadásához hozzon létre egy fájlt a következő elnevezési konvencióval: **Your-Custom-Test-Name.test.ps1**.

A teszt a sablont objektum-paraméterként vagy karakterlánc-paraméterként kérdezheti le. Általában az egyiket vagy a másikat kell használnia, de mindkettőt használhatja.

Akkor használja az Object paramétert, ha a sablon egy szakaszát be kell szereznie, és meg kell ismételni a tulajdonságait. Az Object paramétert használó teszt formátuma a következő:

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

A sablon objektum a következő tulajdonságokkal rendelkezik:

* $schema
* contentVersion
* parameters
* változók
* resources
* kimenetek

Például lekérheti a paraméterek gyűjteményét a következővel: `$TemplateObject.parameters` .

Ha a teljes sablonon sztring műveletet kell végrehajtania, használja a string paramétert. A string paramétert használó teszt formátuma a következő:

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

Futtathat például egy reguláris kifejezést a string paraméterrel, hogy megtudja, van-e egy adott szintaxis használata.

Ha többet szeretne megtudni a teszt végrehajtásáról, tekintse meg a mappában található többi tesztet.

## <a name="integrate-with-azure-pipelines"></a>Integrálás az Azure-folyamatokkal

A tesztelési eszközkészletet hozzáadhatja az Azure-folyamathoz. Egy folyamattal a sablon minden frissítésekor futtathatja a tesztet, vagy futtathatja azt a telepítési folyamat részeként.

A tesztelési eszközkészletnek a folyamathoz való hozzáadásának legegyszerűbb módja a harmadik féltől származó bővítmények használata. A következő két bővítmény érhető el:

* [ARM TTK tesztek futtatása](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [ARM-sablon tesztelése](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

Emellett saját feladatokat is megvalósíthat. Az alábbi példa bemutatja, hogyan töltheti le a tesztelési eszközkészletet.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

A következő példa bemutatja, hogyan futtathatja a teszteket.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>Következő lépések

- Az alapértelmezett tesztek megismeréséhez tekintse meg az [ARM-sablon tesztelési eszközkészletének alapértelmezett tesztelési eseteit](test-cases.md).
- A tesztelési eszközkészlettel foglalkozó Microsoft Learn modul esetében tekintse meg az [előzetes verzió módosításait és az Azure-erőforrások érvényesítését a mi és az ARM template test Toolkit használatával](/learn/modules/arm-template-test/).
