---
title: Egyéni összetevők létrehozása a DevTest Labs virtuális géphez | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre olyan összetevőket, amelyekkel a virtuális gép üzembe helyezése után a Azure DevTest Labs használatával telepítheti és állíthatja be az alkalmazásokat.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85acfcc3811e671e58fadab08a23951778e1323d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88270682"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Egyéni összetevők létrehozása a DevTest Labs virtuális géphez

Tekintse meg az alábbi videót a cikkben ismertetett lépések áttekintéséhez:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Áttekintés
Az alkalmazások üzembe helyezéséhez és beállításához a virtuális gép kiépítése után *is használhatja az* összetevőket. Az összetevők egy összetevő-definíciós fájlból és más parancsfájlokból állnak, amelyek egy git-tárház mappában találhatók. Az összetevő-definíciós fájlok JSON-ből és kifejezésből állnak, amelyek segítségével megadható, hogy mit szeretne telepíteni a virtuális gépre. Megadhatja például egy összetevő nevét, a futtatandó parancsot, valamint a parancs futtatásakor elérhető paramétereket. Az összetevő-definíciós fájlban lévő egyéb parancsfájlokat név szerint is megtekintheti.

## <a name="artifact-definition-file-format"></a>Összetevő-definíció fájlformátuma
A következő példa egy definíciós fájl alapszerkezetét alkotó szakaszt mutatja be:

```json
  {
    "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
    "title": "",
    "description": "",
    "iconUri": "",
    "targetOsType": "",
    "parameters": {
      "<parameterName>": {
        "type": "",
        "displayName": "",
        "description": ""
      }
    },
    "runCommand": {
      "commandToExecute": ""
    }
  }
```

| Elem neve | Kötelező? | Description |
| --- | --- | --- |
| $schema |No |A JSON-séma fájljának helye. A JSON-sémafájl segítségével tesztelheti a definíciós fájl érvényességét. |
| cím |Yes |A laborban megjelenő összetevő neve. |
| leírás |Yes |A laborban megjelenő összetevő leírása. |
| iconUri |No |A laborban megjelenő ikon URI-ja. |
| targetOsType |Yes |Azon virtuális gép operációs rendszere, amelyen az összetevő telepítve van. A támogatott beállítások a Windows és a Linux. |
| parameters |No |Azok az értékek, amelyek akkor jelennek meg, amikor az összetevő telepítési parancsa fut a gépen. Ez segít testreszabni az összetevőt. |
| ParancsFuttatása |Yes |A virtuális gépen végrehajtott telepítési parancs. |

### <a name="artifact-parameters"></a>Összetevő paraméterei
A definíciós fájl parameters (paraméterek) részében adja meg, hogy a felhasználó mely értékeket vihet be az összetevők telepítésekor. Ezeket az értékeket az összetevő telepítési parancsában tekintheti meg.

Paraméterek definiálásához használja az alábbi struktúrát:

```json
  "parameters": {
    "<parameterName>": {
      "type": "<type-of-parameter-value>",
      "displayName": "<display-name-of-parameter>",
      "description": "<description-of-parameter>"
    }
  }
```

| Elem neve | Kötelező? | Description |
| --- | --- | --- |
| típus |Yes |A paraméter értékének típusa. Az engedélyezett típusok listáját az alábbi listában tekintheti meg. |
| displayName |Yes |A tesztkörnyezetben a felhasználó számára megjelenített paraméter neve. |
| leírás |Yes |A laborban megjelenő paraméter leírása. |

Az engedélyezett típusok a következők:

* karakterlánc (bármely érvényes JSON-karakterlánc)
* int (bármely érvényes JSON egész szám)
* bool (bármely érvényes JSON logikai érték)
* tömb (bármely érvényes JSON-tömb)

## <a name="secrets-as-secure-strings"></a>Titkos kódok biztonságos karakterláncként
Titkos kódok deklarálása biztonságos karakterláncként. Az alábbi szintaxis a biztonságos karakterlánc-paraméterek deklarálása a `parameters` **artifactfile.js** fájlján belül:

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

Az összetevő telepítési parancsához futtassa a PowerShell-parancsfájlt, amely a ConvertTo-SecureString paranccsal létrehozott biztonságos karakterláncot használja. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

A (z) és a artifact.ps1 (PowerShell-parancsfájl) artifactfile.jsteljes példáját a [githubon](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes)tekintheti meg.

Egy másik fontos szempont, hogy a rendszer nem naplózza a titkokat a konzolon, mivel a rendszer kimenetként rögzíti a felhasználói hibakeresést. 

## <a name="artifact-expressions-and-functions"></a>Összetevő-kifejezések és függvények
Az összetevő-telepítési parancs létrehozásához kifejezések és függvények használhatók.
A kifejezések szögletes zárójelekkel ([és]) vannak ellátva, és az összetevő telepítésekor lesz kiértékelve. A kifejezések bárhol szerepelhetnek egy JSON-karakterlánc értékében. A kifejezések mindig egy másik JSON-értéket adnak vissza. Ha olyan literál karakterláncot kell használnia, amely szögletes zárójelet ([) használ, két szögletes zárójelet ([[) kell használnia.
A függvényeket általában egy érték létrehozásához használjuk. A JavaScripthez hasonlóan a függvények is függvénynév-ként **(arg1, Arg2, arg3)** vannak formázva.

Az alábbi lista a gyakori függvényeket tartalmazza:

* **Parameters (parameterName)**: egy paraméter értékét adja vissza, amely az összetevő-parancs futtatásakor van megadva.
* **concat (arg1, Arg2, arg3,.....)**: több karakterlánc-értéket egyesít. Ez a függvény számos argumentumot is igénybe vehet.

Az alábbi példa bemutatja, hogyan használhatók kifejezések és függvények egy érték létrehozásához:

```json
  runCommand": {
      "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
  , ' -RawPackagesList ', parameters('packages')
  , ' -Username ', parameters('installUsername')
  , ' -Password ', parameters('installPassword'))]"
  }
```

## <a name="create-a-custom-artifact"></a>Egyéni összetevő létrehozása

1. Telepítsen egy JSON-szerkesztőt. Az összetevő-definíciós fájlokkal való együttműködéshez JSON-szerkesztőre van szükség. A [Visual Studio Code](https://code.visualstudio.com/)használatát javasoljuk, amely Windows, Linux és OS X rendszereken érhető el.
2. A definíciós fájl artifactfile.jsmintájának beolvasása. Tekintse meg az DevTest Labs csapata által létrehozott összetevőket a GitHub- [tárházban](https://github.com/Azure/azure-devtestlab). Létrehoztunk egy gazdag gyűjteményt, amely segíthet saját összetevők létrehozásában. Töltsön le egy összetevő-definíciós fájlt, és módosítsa a saját összetevőinek létrehozásához.
3. Az IntelliSense használata. Az IntelliSense használatával megtekintheti az összetevő-definíciós fájlok létrehozásához használható érvényes elemeket. Megtekintheti az egyes elemek értékeinek különböző lehetőségeit is. Ha például szerkeszti az **targetOsType** elemet, az IntelliSense két választási lehetőséget mutat be Windows vagy Linux rendszeren.
4. Tárolja az összetevőt a [DevTest Labs nyilvános git-tárházában](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) vagy [a saját git-tárházában](devtest-lab-add-artifact-repo.md). A nyilvános tárházban megtekintheti mások által megosztott összetevőket, amelyeket közvetlenül használhat, vagy testre szabhatja az igényeinek megfelelően.
   
   1. Hozzon létre egy külön könyvtárat az egyes összetevőkhöz. A könyvtár nevének meg kell egyeznie az összetevő nevével.
   2. Tárolja az összetevő-definíciós fájlt (artifactfile.js) a létrehozott könyvtárban.
   3. Az összetevő-telepítési parancsban hivatkozott parancsfájlok tárolása.
      
      Az alábbi példa bemutatja, hogyan nézhet ki egy adott összetevőt tartalmazó mappa:
      
      ![Példa az összetevő mappára](./media/devtest-lab-artifact-author/git-repo.png)
5. Ha saját tárházat használ az összetevők tárolásához, adja hozzá a tárházat a laborhoz a következő cikk utasításait követve: [a git-tárház hozzáadása](devtest-lab-add-artifact-repo.md)az összetevőkhöz és a sablonokhoz.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az összetevők hibáinak diagnosztizálása a DevTest Labs szolgáltatásban](devtest-lab-troubleshoot-artifact-failure.md)
* [Virtuális gép csatlakoztatása meglévő Active Directory tartományhoz egy Resource Manager-sablonnal a DevTest Labs szolgáltatásban](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [adhat hozzá egy git-](devtest-lab-add-artifact-repo.md)összetevőt a laborhoz.
