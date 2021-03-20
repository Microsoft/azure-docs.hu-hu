---
title: A Azure Automation grafikus runbook SDK használata (előzetes verzió)
description: Ez a cikk azt ismerteti, hogyan használható a Azure Automation grafikus runbook SDK (előzetes verzió).
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 969e60cd08a65adb1dd731aa7c6c3f9872e288fd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83835036"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk-preview"></a>A Azure Automation grafikus runbook SDK használata (előzetes verzió)

A [grafikus runbookok](automation-graphical-authoring-intro.md) segítségével kezelheti a mögöttes Windows PowerShell-vagy PowerShell munkafolyamat-kód bonyolultságát. A Microsoft Azure Automation grafikus authoring SDK lehetővé teszi, hogy a fejlesztők grafikus runbookok hozzanak létre és szerkesszenek a Azure Automation használatával. Ez a cikk a grafikus runbook kód alapján történő létrehozásának alapvető lépéseit ismerteti.

## <a name="prerequisites"></a>Előfeltételek

Importálja a `Orchestrator.GraphRunbook.Model.dll` csomagot úgy, hogy letölti az [SDK](https://www.microsoft.com/download/details.aspx?id=50734)-t.

## <a name="create-a-runbook-object-instance"></a>Runbook objektum példányának létrehozása

Hivatkozzon a `Orchestrator.GraphRunbook.Model` szerelvényre, és hozza létre a osztály egy példányát `Orchestrator.GraphRunbook.Model.GraphRunbook` :

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Runbook paraméterek hozzáadása

Objektumok példányainak példánya `Orchestrator.GraphRunbook.Model.Parameter` és hozzáadása a runbook:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Tevékenységek és hivatkozások hozzáadása

Hozza létre a megfelelő típusú tevékenységeket, és adja hozzá őket a runbook:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

A tevékenységeket a következő osztályok használják a `Orchestrator.GraphRunbook.Model` névtérben:.

|Osztály  |Tevékenység  |
|---------|---------|
|CommandActivity     | Meghívja a PowerShell-parancsot (parancsmag, függvény stb.).        |
|InvokeRunbookActivity     | Egy másik runbook-sort hív meg.        |
|JunctionActivity     | Megvárja, amíg az összes bejövő ág befejeződik.        |
|WorkflowScriptActivity     | Végrehajtja a PowerShell vagy a PowerShell munkafolyamat-kód (a runbook típusától függően) egy blokkját a runbook környezetében. Ez egy hatékony eszköz, de nem túlzott: a felhasználói felület ezt a szkriptet fogja megjeleníteni szövegként. a végrehajtó motor a megadott blokkot fekete dobozként kezeli, és nem próbálja meg elemezni a tartalmát, kivéve az alapszintű szintaxis-ellenőrzések során. Ha csak egyetlen PowerShell-parancsot kell meghívnia, inkább a CommandActivity-t.        |

> [!NOTE]
> A megadott osztályokból ne származtatja saját tevékenységeit. Azure Automation nem használhatja az runbookok egyéni tevékenység-típusokkal.

Meg kell adnia `CommandActivity` és `InvokeRunbookActivity` paramétereket kell megadni az érték-leíróként, nem közvetlen értékekként. Az értékek leírók határozzák meg, hogyan kell létrehozni a tényleges paramétereket. Jelenleg a következő érték-leírókat kell megadnia:


|Leíró  |Definíció  |
|---------|---------|
|ConstantValueDescriptor     | Egy rögzített konstans értékre hivatkozik.        |
|RunbookParameterValueDescriptor     | Név alapján egy runbook paraméterre hivatkozik.        |
|ActivityOutputValueDescriptor     | Egy felsőbb rétegbeli tevékenység kimenetére hivatkozik, amely lehetővé teszi, hogy az egyik tevékenység "fizessen elő" egy másik tevékenység által létrehozott adatokat.        |
|AutomationVariableValueDescriptor     | Egy Automation változóra hivatkozik név szerint.         |
|AutomationCredentialValueDescriptor     | Az Automation-tanúsítvány objektumára hivatkozik név szerint.        |
|AutomationConnectionValueDescriptor     | Egy Automation-kapcsolódási eszközre hivatkozik név szerint.        |
|PowerShellExpressionValueDescriptor     | Egy szabad formátumú PowerShell-kifejezést ad meg, amely csak a tevékenység meghívása előtt lesz kiértékelve.  <br/>Ez egy hatékony eszköz, de nem túlzott: a felhasználói felület ezt a kifejezést szövegként fogja megjeleníteni. a végrehajtó motor a megadott blokkot fekete dobozként kezeli, és nem próbálja meg elemezni a tartalmát, kivéve az alapszintű szintaxis-ellenőrzések során. Ha lehetséges, inkább pontosabb értéket adjon meg.      |

> [!NOTE]
> A megadott osztályokból ne származtatja a saját értékeit. Azure Automation nem használhatja a runbookok egyéni értékű leíró típusokkal.

A tevékenységek összekapcsolására és a runbook való hozzáadására szolgáló hivatkozások példánya:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>A runbook mentése fájlba

`Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer`Runbook szerializálása karakterlánccá:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Ezt a karakterláncot a **. graphrunbook** kiterjesztésű fájlba mentheti. A megfelelő runbook importálhatók a Azure Automationba.
A szerializált formátum változhat a későbbi verzióiban `Orchestrator.GraphRunbook.Model.dll` . Megígérjük a visszamenőleges kompatibilitást: bármely `Orchestrator.GraphRunbook.Model.dll` újabb verzióban deszerializálható bármely, egy régebbi verziójával szerializált runbook. A továbbítási kompatibilitás nem garantált: egy újabb verzióval szerializált runbook nem lehet a régebbi verziókban deszerializálható.

## <a name="next-steps"></a>Következő lépések

További információ: [grafikus runbookok készítése Azure Automationban](automation-graphical-authoring-intro.md).