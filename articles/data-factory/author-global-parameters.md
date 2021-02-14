---
title: Globális paraméterek
description: Globális paraméterek beállítása az egyes Azure Data Factory környezetekhez
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 08/31/2020
ms.openlocfilehash: fb9439bc37fcecf1cb5299a09916ebe21c5bc1cb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393819"
---
# <a name="global-parameters-in-azure-data-factory"></a>Globális paraméterek a Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A globális paraméterek állandók egy olyan adatelőállítón keresztül, amelyet bármely kifejezésben felhasználhat egy folyamat. Akkor hasznosak, ha több, azonos paraméterekkel és értékekkel rendelkező folyamattal rendelkezik. Ha a folyamatos integrációs és üzembe helyezési folyamat (CI/CD) használatával népszerűsít egy adatelőállítót, felülbírálhatja ezeket a paramétereket az egyes környezetekben. 

## <a name="creating-global-parameters"></a>Globális paraméterek létrehozása

Globális paraméter létrehozásához nyissa meg a *kezelés* szakasz *globális paraméterek* lapját. Válassza az **új** lehetőséget a létrehozási oldal megnyitásához – NAV.

![Képernyőfelvétel: a globális paraméterek létrehozásához kiválasztott új gomb kiemelése.](media/author-global-parameters/create-global-parameter-1.png)

A Side-NAV lapon adjon meg egy nevet, válasszon ki egy adattípust, és adja meg a paraméter értékét.

![Képernyőkép, amely azt mutatja, hogy hol adja hozzá a nevet, az adattípust és az értéket az új globális paraméterhez.](media/author-global-parameters/create-global-parameter-2.png)

A globális paraméter létrehozása után a paraméter nevére kattintva szerkesztheti. Ha egyszerre több paramétert szeretne módosítani, válassza az **összes szerkesztése** lehetőséget.

![Globális paraméterek létrehozása](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>Globális paraméterek használata egy folyamaton belül

A globális paraméterek bármelyik [folyamat kifejezésében](control-flow-expression-language-functions.md)használhatók. Ha egy folyamat egy másik erőforrásra hivatkozik, például egy adatkészletre vagy adatfolyamatra, a globális paraméter értékét átadhatja az erőforrás paramétereinek használatával. A globális paraméterek a következőképpen vannak hivatkozva: `pipeline().globalParameters.<parameterName>` .

![Globális paraméterek használata](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> Globális paraméterek a CI/CD-ben

A globális paraméterek kétféleképpen integrálhatók a folyamatos integrációs és üzembe helyezési megoldásban:

* Globális paraméterek belefoglalása az ARM-sablonba
* Globális paraméterek üzembe helyezése PowerShell-parancsfájl használatával

A legtöbb felhasználási eset esetében javasoljuk, hogy globális paramétereket tartalmazzon az ARM-sablonban. Ez natív módon integrálható a [CI/CD doc](continuous-integration-deployment.md)-ban ismertetett megoldással. Alapértelmezés szerint a globális paraméterek az ARM-sablon paraméterként lesznek hozzáadva, mivel azok gyakran változnak a környezetből a környezetbe. A felügyeleti központban engedélyezheti a globális paraméterek felvételét az ARM-sablonban.

![Belefoglalás az ARM-sablonba](media/author-global-parameters/include-arm-template.png)

Ha globális paramétereket ad hozzá az ARM-sablonhoz, egy olyan gyári szintű beállítást ad hozzá, amely felülírja a más gyári szintű beállításokat, például az ügyfél által felügyelt kulcsot vagy a git-konfigurációt más környezetekben. Ha ezek a beállítások egy emelt szintű környezetben, például a ellenőrzését vagy a PRODon vannak engedélyezve, akkor jobb, ha egy PowerShell-parancsfájllal telepíti a globális paramétereket az alábbi lépésekben leírtak szerint.

### <a name="deploying-using-powershell"></a>Üzembe helyezés a PowerShell használatával

A következő lépések azt ismertetik, hogyan helyezhetők üzembe globális paraméterek a PowerShell használatával. Ez akkor hasznos, ha a cél-előállító gyári szintű beállítással, például az ügyfél által felügyelt kulccsal rendelkezik.

Ha egy gyárat tesz közzé, vagy globális paraméterekkel exportál egy ARM-sablont, a *globalParameters* nevű mappát egyyour-factory-name_GlobalParameters.jsnevű fájllal hozza létre *a* rendszer. Ez a fájl egy JSON-objektum, amely a közzétett gyár összes globális paraméter-típusát és értékét tartalmazza.

![Globális paraméterek közzététele](media/author-global-parameters/global-parameters-adf-publish.png)

Ha egy új környezetbe (például a TEST vagy a PROD) végzi az üzembe helyezést, ajánlott létrehozni a globális Parameters fájl másolatát, és felül kell írnia a megfelelő környezeti jellemző értékeket. Az eredeti globális paraméterek ismételt közzétételekor a rendszer felülírja a fájlt, de a másik környezet másolata érintetlen marad.

Ha például egy "ADF-DEV" nevű gyárat és egy "Environment" nevű, karakterlánc típusú globális paramétert ad meg a "dev" értékkel, akkor a rendszer a következő nevű fájlt teszi közzé: *ADF-DEV_GlobalParameters.js* . Ha egy "ADF_TEST" nevű tesztelési gyárba helyezi üzembe, hozzon létre egy másolatot a JSON-fájlról (például: ADF-TEST_GlobalParameters.js), és cserélje le a paramétereket a környezeti specifikus értékekre. A "környezet" paraméternek lehet "test" értéke. 

![Globális paraméterek üzembe helyezése](media/author-global-parameters/powershell-task.png)

Az alábbi PowerShell-szkripttel előléptetheti a globális paramétereket a további környezetekhez. Vegyen fel egy Azure PowerShell DevOps feladatot az ARM-Template deployment előtt. Az DevOps feladatban meg kell adnia az új Parameters fájl, a célként megadott erőforráscsoport és a cél adat-előállító helyét.

> [!NOTE]
> Globális paraméterek a PowerShell használatával történő telepítéséhez az az modul legalább 4.4.0 kell használnia.

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>Következő lépések

* A Azure Data Factory [folyamatos integrációs és üzembe helyezési folyamatának](continuous-integration-deployment.md) megismerése
* Megtudhatja, hogyan használhatja a [vezérlési folyamat kifejezésének nyelvét](control-flow-expression-language-functions.md)
