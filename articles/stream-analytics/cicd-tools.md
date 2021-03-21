---
title: Egy Azure Stream Analytics-feladatokhoz tartozó buildek, tesztek és központi telepítések automatizálása CI/CD-eszközök használatával
description: Ez a cikk azt ismerteti, hogyan használható a Azure Stream Analytics CI/CD-eszközök egy Azure Stream Analytics-projekt automatikus létrehozásához, teszteléséhez és üzembe helyezéséhez.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: 2dbb491e77f132daf7b432f27705eba9e3e3cd3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036962"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>Egy Azure Stream Analytics-feladatokhoz tartozó buildek, tesztek és központi telepítések automatizálása CI/CD-eszközök használatával

A Azure Stream Analytics CI/CD NPM csomag használatával automatikusan felépítheti, tesztelheti és helyezheti üzembe Azure Stream Analytics Visual Studio Code-vagy Visual Studio-projektjeit. A projektek fejlesztői eszközökkel hozhatók létre, vagy meglévő Stream Analytics-feladatokból exportálhatók. Ez a cikk azt ismerteti, hogyan használható a NPM-csomag bármilyen CI/CD-rendszerrel. Az Azure-folyamatokkal való üzembe helyezéssel kapcsolatban lásd: az [Azure DevOps használata CI/CD-folyamat létrehozásához stream Analytics feladatokhoz](set-up-cicd-pipeline.md).

## <a name="installation"></a>Telepítés

Közvetlenül is [letöltheti a csomagot](https://www.npmjs.com/package/azure-streamanalytics-cicd) , vagy telepítheti [globálisan](https://docs.npmjs.com/downloading-and-installing-packages-globally) a `npm install -g azure-streamanalytics-cicd` paranccsal. Javasoljuk, hogy használja az parancsot, amely az **Azure-folyamatok** Build folyamatának PowerShell vagy Azure CLI parancsfájl-feladatában is használható.

## <a name="build-the-project"></a>A projekt felépítése

Az **ASA-streamanalytics-vel NPM-** csomag biztosítja az eszközöket stream Analytics [Visual Studio Code-projektek](./quick-create-visual-studio-code.md) vagy [Visual Studio-projektek](stream-analytics-quick-create-vs.md)Azure Resource Manager sablonjainak létrehozásához. A NPM-csomagot Windows, macOS és Linux rendszeren is használhatja a Visual Studio Code vagy a Visual Studio telepítése nélkül.

A csomag telepítése után a következő paranccsal hozhatja létre a Stream Analytics projekteket.

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

A *Build* parancs egy kulcsszó-szintaxist jelöl ki, és kiírja a Azure Resource Manager sablont.

| Paraméter | Leírás |
|---|---|
| `-project` | A Visual Studio Code-projekthez vagy **[a projekt neve]. asaproj** for Visual Studio projekthez tartozó fájl **asaproj.jsának** abszolút elérési útja. |
| `-outputPath` | Azure Resource Manager-sablonok kimeneti mappájának elérési útja. Ha nincs megadva, a sablonok az aktuális könyvtárba lesznek helyezve. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

Stream Analytics-projekt sikeres létrehozásakor a a következő két fájlt hozza létre a kimeneti mappában:

* Sablonfájl Azure Resource Manager

   `[ProjectName].JobTemplate.json`

* Azure Resource Manager paraméter fájlja

   `[ProjectName].JobTemplate.parameters.json`

A fájl parameters.jsjában lévő alapértelmezett paraméterek a Visual Studio Code vagy a Visual Studio-projekt beállításaiból származnak. Ha egy másik környezetbe szeretne telepíteni, cserélje le a paramétereket ennek megfelelően.

Az összes hitelesítő adat alapértelmezett értékei **Null értékűek**. Az Azure-ba való üzembe helyezés előtt be kell állítania az értékeket.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

Ha Azure Data Lake Store Gen1 felügyelt identitást szeretne használni kimeneti fogadóként, az Azure-ba való üzembe helyezés előtt meg kell adnia a szolgáltatásnevet a PowerShell használatával. További információ a [ADLS Gen1 felügyelt identitással való üzembe helyezéséhez Resource Manager-sablonnal](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).

## <a name="local-run"></a>Helyi futtatás

Ha a projekt helyi bemeneti fájlokat adott meg, akkor a paranccsal helyileg futtathat egy Stream Analytics parancsfájlt `localrun` .

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Paraméter | Leírás |
|---|---|
| `-project` | A Visual Studio Code-projekt vagy **[a projekt neve]. asaproj** for Visual Studio projekthez tartozó fájl **asaproj.jsának** elérési útja. |
| `-outputPath` | A kimeneti mappa elérési útja Ha nincs megadva, a rendszer a kimeneti eredmények fájljait az aktuális könyvtárba helyezi. |
| `-customCodeZipFilePath` | A C# egyéni kód (például UDF vagy deszerializáló) zip-fájljának elérési útja, ha azok használatban vannak. Csomagolja a DLL-eket egy zip-fájlba, és határozza meg ezt az elérési utat. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> A JavaScript UDF csak Windows rendszeren működik.

## <a name="automated-test"></a>Automatizált teszt

A CI/CD NPM csomaggal konfigurálhatja és futtathatja a Stream Analytics parancsfájl automatikus tesztelését.

### <a name="add-a-test-case"></a>Tesztelési eset hozzáadása

A tesztelési eseteket egy teszt konfigurációs fájlban mutatjuk be. Első lépésként a parancs használatával `addtestcase` vegyen fel egy tesztelési esethez tartozó sablont a teszt konfigurációs fájljába. Ha a teszt konfigurációs fájl nem létezik, a rendszer alapértelmezés szerint létrehoz egyet.

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| Paraméter | Leírás |
|---|---|
| `-project` | A Visual Studio Code-projekt vagy **[a projekt neve]. asaproj** for Visual Studio projekthez tartozó fájl **asaproj.jsának** elérési útja. |
| `-testConfigPath` | A teszt konfigurációs fájljának elérési útja. Ha nincs megadva, a rendszer a **\test** a fájl **asaproj.js** aktuális könyvtára alatt keresi a fájlt, és az alapértelmezett fájlnév **testConfig.jsbe**. Ha nem létezik, új fájl jön létre. |

> [!NOTE]
> A `Script` fájl generált **testConfig.jsjának** értéke csak a kontextus biztosítására szolgál. A tesztelési logikában nem használatos. 

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

Ha a teszt konfigurációs fájl üres, a rendszer a következő tartalmat írja be a fájlba. Ellenkező esetben a rendszer egy tesztelési esetet ad hozzá a **TestCases** tömbhöz. A szükséges bemeneti konfigurációk automatikusan ki vannak töltve a bemeneti konfigurációs fájlok szerint, ha vannak ilyenek. Ellenkező esetben az alapértelmezett értékek konfigurálva vannak. A teszt futtatása előtt meg kell adni az egyes bemenetek és a várt kimenet **filepath** . Manuálisan is módosíthatja a konfigurációt.

Ha azt szeretné, hogy a teszt érvényesítése figyelmen kívül hagyja az adott kimenetet, állítsa a várt kimenet **kötelező** értékét **false** értékre.

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": [Required],
          "Required": true
        }
      ]
    }
  ]
}
```

> [!NOTE]
> Jelenleg az elem egyetlen megengedett értéke `ScriptType` `InputMock` , amely egyben az alapértelmezett érték is. Ha más értékre állítja be, a rendszer figyelmen kívül hagyja, és az alapértelmezett értéket ( `InputMock` ) használja. 

### <a name="run-a-unit-test"></a>Egység teszt futtatása

A következő parancs használatával több tesztelési eset is futtatható a projekthez. A rendszer a kimeneti mappában hozza létre a tesztelési eredmények összegzését. A folyamat kilép a **0** . kóddal az összes átadott teszt után; **-1** kivétel történt; **-2** a tesztek sikertelenek voltak.

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Paraméter | Leírás |
|---|---|
| `-project` | A Visual Studio Code-projekt vagy **[a projekt neve]. asaproj** for Visual Studio projekthez tartozó fájl **asaproj.jsának** elérési útja. |
| `-testConfigPath` | A teszt konfigurációs fájljának elérési útja. Ha nincs megadva, a rendszer a **\test** a fájl **asaproj.js** aktuális könyvtára alatt keresi a fájlt, és az alapértelmezett fájlnév **testConfig.jsbe**.
| `-outputPath` | A teszt eredményének kimeneti mappájának elérési útja Ha nincs megadva, a rendszer a kimeneti eredmények fájljait az aktuális könyvtárba helyezi. |
| `-customCodeZipFilePath` | Az egyéni kódhoz tartozó zip-fájl, például az UDF vagy a deszerializáló elérési útja, ha azok használatban vannak. |

Ha az összes teszt elkészült, a rendszer a kimeneti mappában hozza létre a teszteredmények JSON formátumban való összefoglalását. Az összegző fájl neve **testResultSummary.json**.

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

A Build-ből generált Azure Resource Manager sablon és paraméter-fájlok használatával [üzembe helyezheti a feladatot az Azure](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template)-ban.

## <a name="next-steps"></a>Következő lépések

* [Folyamatos integráció és folyamatos üzembe helyezés a Azure Stream Analytics számára](cicd-overview.md)
* [CI/CD-folyamat beállítása Stream Analytics feladatokhoz az Azure-folyamatok használatával](set-up-cicd-pipeline.md)
