---
title: Runbook bemeneti paramétereinek konfigurálása a Azure Automation
description: Ez a cikk azt mutatja be, hogyan konfigurálhatja a runbook bemeneti paramétereit, amelyek lehetővé teszik az adatok továbbadása a runbooknak az elindításkor.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 95a6cc87471fcf2209d452f90e1e5f52cae122c5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831293"
---
# <a name="configure-runbook-input-parameters"></a>Runbook bemeneti paramétereinek konfigurálása

A Runbook bemeneti paraméterei növelik a runbook rugalmasságát, mert lehetővé teszik az adatoknak való továbbadást az started ( started) után. Ezek a paraméterek lehetővé teszik, hogy a Runbook-műveletek adott forgatókönyvekre és környezetekre vonatkoznak. Ez a cikk bemutatja a bemeneti paraméterek konfigurációját és használatát a runbookban.

A PowerShell, a PowerShell-munkafolyamat, a grafikus és a Python-runbookok bemeneti paramétereit konfigurálhatja. Egy runbook több, különböző adattípusú paramétert is tartalmazhat, vagy egyáltalán nem. A bemeneti paraméterek kötelezőek vagy választhatók is, és az alapértelmezett értékeket a választható paraméterekhez is használhatja.

Amikor elindítja a runbookot, értékeket rendelhet hozzá a bemeneti paraméterekhez. A runbookot elindíthatja a Azure Portal, egy webszolgáltatásból vagy a PowerShellből. Gyermek runbookként is elindíthat egyet, amely egy másik runbook beágyazott neve.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Bemeneti paraméterek konfigurálása PowerShell-runbookok esetén

A PowerShell- és a PowerShell-Azure Automation a következő tulajdonságokkal definiált bemeneti paramétereket támogatják. 

| **Tulajdonság** | **Leírás** |
|:--- |:--- |
| Típus |Kötelező. A paraméterértékhez várt adattípus. Bármely .NET-típus érvényes. |
| Name |Kötelező. A paraméter neve. Ennek a névnek egyedinek kell lennie a runbookon belül, betűvel kell kezdődnie, és csak betűket, számokat vagy aláhúzásjeleket tartalmazhat. |
| Kötelező |Választható. Logikai érték, amely meghatározza, hogy a paraméterhez szükség van-e értékre. Ha true (Igaz) értékre van állítva, a runbook elindultakor meg kell adni egy értéket. Ha false (Hamis) értékre van állítva, az érték megadása nem kötelező. Ha nem ad meg értéket a tulajdonsághoz, a PowerShell alapértelmezés szerint opcionálisnak tekinti a bemeneti `Mandatory` paramétert. |
| Alapértelmezett érték |Választható. A paraméterhez használt érték, ha a runbook indításakor nem ad meg bemeneti értéket. A runbook bármilyen paraméterhez beállíthatja az alapértelmezett értéket. |

Windows PowerShell a bemeneti paraméterek több attribútumát támogatja, mint a fent felsoroltak, például az érvényesítést, az aliasokat és a paraméterkészleteket. A Azure Automation azonban jelenleg csak a felsorolt bemeneti paramétertulajdonságokat támogatja.

Példaként vessünk egy pillantást egy paraméterdefinícióra egy PowerShell-munkafolyamati runbookban. Ez a definíció az alábbi általános formát követi, amelyben több paraméter vesszővel van elválasztva.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Most konfiguráljuk egy Olyan PowerShell-munkafolyamati runbook bemeneti paramétereit, amely a virtuális gépek adatait adja vissza, akár egyetlen virtuális gépet, akár az erőforráscsoporton belüli összes virtuális gépet. Ez a runbook két paramétert használ, ahogyan az alábbi képernyőképen látható: a virtuális gép neve ( ) és az `VMName` erőforráscsoport neve ( `resourceGroupName` ).

![Automation PowerShell-munkafolyamat](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Ebben a paraméterdefinícióban a bemeneti paraméterek sztring típusú egyszerű paraméterek.

Vegye figyelembe, hogy a PowerShell- és a PowerShell-munkafolyamat-runbookok minden egyszerű típust és összetett típust támogatnak, például a vagy a bemeneti `Object` `PSCredential` paramétereket. Ha a runbook objektumbemeneti paraméterrel rendelkezik, név-érték párokkal egy PowerShell-kivonattáblát kell használnia egy érték átadhatóként. Egy runbookban például a következő paraméter van megadva.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Ebben az esetben a következő értéket használhatja a paraméternek.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Ha nem ad át értéket egy nem kötelező alapértelmezett sztringparaméternek, a paraméter értéke null helyett üres sztring lesz.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Bemeneti paraméterek konfigurálása grafikus runbookban

A grafikus runbook bemeneti paramétereinek konfigurálására hozzunk létre egy runbookot, amely a virtuális gépek adatait jeleníti meg, akár egyetlen virtuális gépet, akár az erőforráscsoporton belüli összes virtuális gépet. Részletekért lásd: [Az első grafikus runbookom.](./learn/automation-tutorial-runbook-graphical.md)

A grafikus runbookok a következő fő Runbook-tevékenységeket használják:

* Az Azure-beli hitelesítéshez használt Azure-beli futtatási fiók konfigurálása. 
* Egy [Get-AzVM-parancsmag](/powershell/module/az.compute/get-azvm) definíciója a virtuális gép tulajdonságainak lekért érdekében.
* A [Write-Output tevékenység használata](/powershell/module/microsoft.powershell.utility/write-output) a virtuális gépek nevének kiírásához. 

A tevékenység két bemenetet határoz meg: a virtuális gép nevét és `Get-AzVM` az erőforráscsoport nevét. Mivel ezek a nevek a Runbook minden indításakor eltérőek, bemeneti paramétereket kell hozzáadnia a runbookhoz a bemenetek elfogadásához. További [információ: Grafikus tartalom Azure Automation.](automation-graphical-authoring-intro.md)

A bemeneti paraméterek konfiguráláshoz kövesse az alábbi lépéseket.

1. Válassza ki a grafikus runbookot a Runbookok lapon, majd kattintson a **Szerkesztés gombra.**
2. A grafikus szerkesztőben kattintson az **Input and output** (Bemenet és kimenet) gombra, majd az Add input (Bemenet **hozzáadása)** gombra a Runbook Input Parameter (Forgatókönyv bemeneti paramétere) panel megnyitásához.

   ![Automation grafikus runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. A Bemenet és kimenet vezérlő megjeleníti a runbookhoz definiált bemeneti paraméterek listáját. Itt hozzáadhat egy új bemeneti paramétert, vagy szerkesztheti egy meglévő bemeneti paraméter konfigurációját. Ha új paramétert szeretne hozzáadni  a runbookhoz, kattintson a Bemenet hozzáadása elemre a **Runbook** bemeneti paraméter paneljének megnyitásához, ahol konfigurálhatja a paramétereket a Grafikus tartalom létrehozása című [Azure Automation.](automation-graphical-authoring-intro.md)

    ![Új bemenet hozzáadása](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Hozzon létre két paramétert a tevékenység által használt alábbi `Get-AzVM` tulajdonságokkal, majd kattintson az OK **gombra.**

   * 1. paraméter:
        * **Name (Név)**  --  **VMName**
        * **Típus** – Sztring
        * **Kötelező**  --  **Nem**

   * 2. paraméter:
        * **Név**  --  **resourceGroupName (erőforráscsoport neve)**
        * **Típus** – Sztring
        * **Kötelező**  --  **Nem**
        * **Alapértelmezett érték**  --  **Egyéni**
        * Egyéni alapértelmezett érték – A virtuális gépeket tartalmazó erőforráscsoport neve

5. Tekintse meg a paramétereket a Bemenet és kimenet vezérlőben. 
6. Kattintson **ismét az OK** gombra, majd a Mentés **gombra.**
7. A **runbook közzétételéhez** kattintson a Publish (Közzététel) gombra.

### <a name="configure-input-parameters-in-python-runbooks"></a>Bemeneti paraméterek konfigurálása Python-runbookok esetén

A PowerShelltől, a PowerShell-munkafolyamattól és a grafikus runbooktól eltérően a Python-runbookok nem venek fel nevesedt paramétereket. A Runbook-szerkesztő az összes bemeneti paramétert argumentumértékek tömbjeként elemezi. A tömb eléréséhez importálja a modult a `sys` Python-szkriptbe, majd használja a `sys.argv` tömböt. Fontos megjegyezni, hogy a tömb első eleme a szkript `sys.argv[0]` neve. Ezért az első tényleges bemeneti paraméter a `sys.argv[1]` .

A bemeneti paraméterek Python-runbookban való használatára vonatkozó példáért lásd: Az első [Python-runbookom](./learn/automation-tutorial-runbook-textual-python2.md)a Azure Automation.

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Értékek hozzárendelése a runbookok bemeneti paramétereihez

Ez a szakasz több módszert ismertet az értékek a runbookok bemeneti paramétereinek való továbbadása érdekében. A paraméterértékeket a következőkor rendelheti hozzá:

* [Runbook indítása](#start-a-runbook-and-assign-parameters)
* [Runbook tesztelése](#test-a-runbook-and-assign-parameters)
* [Ütemezés csatolása a runbookhoz](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Webhook létrehozása a runbookhoz](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Runbook futtatása és paraméterek hozzárendelése

A runbookok számos módon elindíthatóak: az Azure Portal-ban, webhookkal, PowerShell-parancsmagokkal, a REST API vagy az SDK-val. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Közzétett runbook futtatása a Azure Portal és paraméterek hozzárendelése

Amikor [elindítja a runbookot](start-runbooks.md#start-a-runbook-with-the-azure-portal) a Azure Portal megnyílik a **Runbook** futtatása panel, és megadhatja a létrehozott paraméterek értékeit.

![A portál használatának első kezdése](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

A bemeneti mező alatti címkében láthatja a paraméterattribútumok meghatározására beállított tulajdonságokat, például kötelező vagy választható, típus, alapértelmezett érték. A paraméter neve melletti súgóballon a paraméterbemeneti értékekkel kapcsolatos döntések meghozatalához szükséges fő információkat is meghatározza. 

> [!NOTE]
> A sztringparaméterek támogatják a Sztring típusú üres értékeket. A `[EmptyString]` bemeneti paraméter mezőbe beírása egy üres sztringet ad át a paraméternek. Emellett a sztringparaméterek nem támogatják a Null értéket. Ha nem ad át értéket egy sztringparaméternek, a PowerShell null értékként értelmezi azt.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Közzétett runbook futtatása PowerShell-parancsmagok használatával és paraméterek hozzárendelése

* **Azure Resource Manager parancsmagok:** A [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook)használatával elindíthat egy erőforráscsoportban létrehozott Automation-runbookot.

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Klasszikus Azure üzembe helyezési modell parancsmagok:** A [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure.service/start-azureautomationrunbook)használatával elindíthat egy alapértelmezett erőforráscsoportban létrehozott Automation-runbookot.
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Amikor PowerShell-parancsmagokkal indít el egy runbookot, a rendszer létrehoz egy alapértelmezett paramétert `MicrosoftApplicationManagementStartedBy` (). `PowerShell` Ezt a paramétert a Feladat részletei panelen tudja megtekinteni.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Runbook futtatása SDK-val és paraméterek hozzárendelése

* **Azure Resource Manager metódus:** A runbookokat egy programozási nyelv SDK-ján keresztül lehet elindítani. Az alábbiakban egy C#-kódrészlet látható, amely egy runbooknak az Automation-fiókban való futtatásához ad vissza. Az összes kódot megtekintheti a [GitHub-adattárunkban.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Klasszikus Azure-beli üzembe helyezési modell metódusa:** A runbookok egy programozási nyelv SDK-ját használva indíthatóak el. Az alábbiakban egy C#-kódrészlet látható egy Runbook automation-fiókban való futtatásához. Az összes kódot megtekintheti a [GitHub-adattárban.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   A metódus indításhoz hozzon létre egy szótárt a runbook paramétereinek és `VMName`  `resourceGroupName` értékeinek tárolására. Ezután indítsa el a runbookot. Az alábbi C#-kódrészlet a fent definiált metódus hívását mutatja be.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Runbook futtatása a REST API és paraméterek hozzárendelése

A runbook-feladat a Azure Automation REST API a metódussal hozható létre és indíthat el a következő kérelem `PUT` URI-val: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

A kérelem URI-ján cserélje le a következő paramétereket:

* `subscriptionId`: Az Azure-előfizetés azonosítója.  
* `resourceGroupName`: Az Automation-fiók erőforráscsoportja neve.
* `automationAccountName`: A megadott felhőszolgáltatásban üzemeltetett Automation-fiók neve.  
* `jobName`: A feladat GUID-ja. A PowerShellBEN a GUID azonosítók a használatával használhatja `[GUID]::NewGuid().ToString()*` a következőt: .

Ha paramétereket ad át a runbook-feladatnak, használja a kérelem törzsét. A következő, JSON formátumban megadott információkat tartalmazza:

* Runbook neve: Kötelező. Az elindított feladat runbookneve.  
* Runbook-paraméterek: Nem kötelező. A paraméterlista szótára (név, érték) formátumban, ahol a név Sztring típusú, az érték pedig bármilyen érvényes JSON-érték lehet.

Ha a korábban létrehozott **Get-AzureVMTextual** runbookot paraméterként szeretné elindítani, használja a következő JSON-formátumot a `VMName` kérelem `resourceGroupName` törzseként.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

A 201-es HTTP-állapotkódot ad vissza, ha a feladat sikeresen létrejött. A válaszfejlécekkel és a válasz törzsével kapcsolatos további információkért [lásd: Runbook-feladat](/rest/api/automation/job/create)létrehozása a REST API.

### <a name="test-a-runbook-and-assign-parameters"></a>Runbook tesztelése és paraméterek hozzárendelése

Amikor a [teszt lehetőséggel](./manage-runbooks.md) teszteli a runbook piszkozatverzióját, megnyílik a Teszt oldal. Ezen az oldalon konfigurálhatja a létrehozott paraméterek értékeit.

![Paraméterek tesztelése és hozzárendelése](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Ütemezés csatolása runbookhoz és paraméterek hozzárendelése

Ütemezést [kapcsolhat a](./shared-resources/schedules.md) runbookhoz, hogy a runbook egy adott időpontban indul el. A bemeneti paramétereket az ütemezés létrehozásakor rendelheti hozzá, és a runbook ezeket az értékeket használja, amikor az ütemezés szerint indul el. Az ütemezést addig nem mentheti, amíg az összes kötelező paraméterérték meg nem van adva.

![Paraméterek ütemezése és hozzárendelése](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Webhook létrehozása runbookhoz és paraméterek hozzárendelése

Létrehozhat egy [webhookot](automation-webhooks.md) a runbookhoz, és konfigurálhatja a runbook bemeneti paramétereit. Nem mentheti a webhookot, amíg meg nem ad minden kötelező paraméterértéket.

![Webhook létrehozása és paraméterek hozzárendelése](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Amikor webhookkal hajt végre egy runbookot, a rendszer elküldi az előre definiált bemeneti paramétert és az Ön által meghatározott bemeneti `[WebhookData](automation-webhooks.md)` paramétereket. 

![WebhookData paraméter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>JSON-objektum átadása runbooknak

Hasznos lehet egy JSON-fájlban tárolni a runbooknak átadni kívánt adatokat. Létrehozhat például egy JSON-fájlt, amely a runbooknak átadni kívánt összes paramétert tartalmazza. Ehhez konvertálja a JSON-kódot sztringgé, majd alakítsa át a sztringet Egy PowerShell-objektumká, mielőtt továbbadja a runbooknak.

Ez a szakasz egy olyan példát használ, amelyben egy PowerShell-szkript a [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) hívásával indít el egy PowerShell-runbookot, és a JSON-fájl tartalmát a runbooknak továbbadja. A PowerShell-runbook úgy elindít egy Azure-beli virtuális gépet, hogy lekérte a virtuális gép paramétereit a JSON-objektumból.

### <a name="create-the-json-file"></a>A JSON-fájl létrehozása

Írja be az alábbi kódot egy szövegfájlba, és mentsetest.js **a** helyi számítógép egyik útjára.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>A runbook létrehozása

Hozzon létre egy új **PowerShell-runbookot Test-Json névvel** a Azure Automation. Lásd: [Az első PowerShell-runbookom.](./learn/automation-tutorial-runbook-textual-powershell.md)

A JSON-adatok elfogadásához a runbooknak egy objektumot kell bemeneti paraméterként fogadnia. A runbook ezután használhatja a JSON-fájlban meghatározott tulajdonságokat.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Mentse és tegye közzé ezt a runbookot az Automation-fiókjában.

### <a name="call-the-runbook-from-powershell"></a>A runbook hívása a PowerShellből

Most már meg tudja hívni a runbookot a helyi gépről az Azure PowerShell. 

1. Jelentkezzen be az Azure-ba az itt látható módon. Ezt követően meg kell adnia Azure-beli hitelesítő adatait.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >A PowerShell-runbookok és `Add-AzAccount` `Add-AzureRMAccount` a a `Connect-AzAccount` aliasai. Vegye figyelembe, hogy ezek az aliasok nem érhetők el grafikus runbookok számára. A grafikus runbookok csak saját magát `Connect-AzAccount` használhatja.

1. Szerezze be a mentett JSON-fájl tartalmát, és alakítsa át sztringgé. `JsonPath` A azt az elérési utat jelzi, ahová a JSON-fájlt mentette.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Konvertálja a sztring tartalmát `$json` Egy PowerShell-objektumká.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Hozzon létre egy kivonattáblát a `Start-AzAutomationRunbook` paramétereihez. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Figyelje meg, hogy a értékét a JSON-fájlból származó értékeket tartalmazó `Parameters` PowerShell-objektumra kell beértékekkel.
1. Indítsa el a runbookot.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Következő lépések

* A szöveges runbookok előkészítéséhez lásd: [Szöveges runbookok](automation-edit-textual-runbook.md)szerkesztése a Azure Automation.
* Grafikus runbook előkészítéséhez lásd: [Grafikus runbookok írása](automation-graphical-authoring-intro.md)a Azure Automation.
