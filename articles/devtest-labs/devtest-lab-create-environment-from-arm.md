---
title: Több virtuális gépből álló környezetek és PaaS-erőforrások létrehozása sablonokkal
description: Megtudhatja, hogyan hozhat létre több virtuális gépből álló környezeteket és PaaS-erőforrásokat Azure DevTest Labs egy Azure Resource Manager sablonból
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: f285acffe642a85fa27792ee51ea67a57f6d35a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790112"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal

Azure DevTest Labs környezetek lehetővé teszik a felhasználók számára, hogy konzisztens módon, a laboron belül, könnyen üzembe helyeznek összetett infrastruktúrákat. A [DevTest](../azure-resource-manager/templates/template-syntax.md) Labs Azure Resource Manager erőforráskészletekkel létrehozott környezeteket sablonokkal hozhat létre. Ezek a környezetek bármilyen Azure-erőforrást tartalmazhatnak, Resource Manager sablonok hozhatnak létre.

A tesztkörnyezethez egyszerre egyszerűen adhat hozzá egy virtuális gépet [(VM)](devtest-lab-add-vm.md) a [Azure Portal.](https://portal.azure.com) Azonban az olyan forgatókönyvekhez, mint a többrétegű webalkalmazások vagy a SharePoint-farmok, egy olyan mechanizmusra van szükség, amely egyetlen lépésben több virtuális gép létrehozására is használható. A Azure Resource Manager segítségével meghatározhatja az Azure-megoldás infrastruktúráját és konfigurációját, és ismételten üzembe helyezhet több virtuális gépet konzisztens állapotban.

Azure Resource Manager sablonok a következő előnyöket is biztosítják:

- Azure Resource Manager sablonokat közvetlenül a GitHubról vagy az Azure Repos verzióvezérlő adattárból lehet betölteni.
- A felhasználók úgy hozhatnak létre környezetet, hogy kiválasztják a Azure Resource Manager-sablont a Azure Portal, ahogyan más típusú [virtuálisgép-bázisok esetében is.](devtest-lab-comparing-vm-base-image-types.md)
- Azure PaaS-erőforrásokat és IaaS virtuális gépeket is kiépíthet egy környezetben egy Azure Resource Manager sablonból.
- Nyomon követheti a tesztkörnyezetek költségeit, valamint a más típusú alapokkal létrehozott egyéni virtuális gépeket. A PaaS-erőforrások létrejönnek, és megjelennek a költségkövetésben. A virtuális gépek automatikus leállítása azonban nem vonatkozik a PaaS-erőforrásokra.

Ha többet szeretne megtudni arról, hogy milyen előnyökkel jár, ha Resource Manager több tesztkörnyezeti erőforrást telepíthet, frissíthet vagy törölhet egyetlen műveletben, tekintse meg a sablonsablonok használatának Resource Manager [előnyeit.](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)

> [!NOTE]
> Ha tesztkörnyezeti virtuális gépek létrehozásához Resource Manager sablont használ, van néhány különbség több virtuális gép vagy egyetlen virtuális gép létrehozása között. További információ: [Use a virtual machine's Azure Resource Manager template](devtest-lab-use-resource-manager-template.md)(Virtuális gép Azure Resource Manager sablonjának használata).
>

## <a name="use-devtest-labs-public-environments"></a>A DevTest Labs nyilvános környezetek használata
Azure DevTest Labs rendelkezik egy nyilvános adattárakból [Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) sablonokkal, amelyek használatával környezeteket hozhat létre anélkül, hogy önnek is csatlakoznia kell egy külső GitHub-forráshoz. Ez a nyilvános adattár hasonló az összes létrehozott tesztkörnyezethez elérhető összetevők nyilvános Azure Portal adattárhoz. A környezeti adattár lehetővé teszi, hogy gyorsan elkezdje az előre megszerző környezeti sablonokat, amelyek kevés bemeneti paramétert tartalmaznak. Ezek a sablonok zökkenőmentes első lépésekhez nyújtanak segítséget a tesztkörnyezetben található PaaS-erőforrásokhoz.

A nyilvános adattárban a DevTest Labs csapata és mások olyan gyakran használt sablonokat hoztak létre és osztottak meg, mint az Azure Web Apps, Service Fabric Cluster és egy fejlesztési SharePoint Farm-környezet. Ezeket a sablonokat használhatja közvetlenül, vagy igény szerint testre is szabhatja őket. További információ: [Nyilvános környezetek konfigurálása és használata a DevTest Labs szolgáltatásban.](devtest-lab-configure-use-public-environments.md) Miután létrehozott egy saját sablont, ebben az adattárban tárolhatja őket, és megoszthatja őket másokkal, vagy beállíthatja saját Git-adattárát.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Saját sablontárak létrehozása

A kódként és konfigurációként való konfigurálás egyik ajánlott eljárása a környezetsablonok kezelése a forráskezelőben. Azure DevTest Labs követi ezt a gyakorlatot, és az összes Azure Resource Manager sablont közvetlenül a GitHub- vagy Azure-adattárakból. Ennek eredményeképpen a teljes kiadási Resource Manager sablonokat használhat, a tesztkörnyezettől az éles környezetig.

A sablonsablonok adattárban Azure Resource Manager rendszerezéséhez számos szabályt kell követnie:

- A fősablonfájlnak nevet kellazuredeploy.js *a következőn:*.

- Ha paraméterfájlban definiált paraméterértékeket szeretne használni, a paraméterfájlnak a következőn *kellazuredeploy.parameters.jsneve.*

  A és a paraméter használatával felépítheti `_artifactsLocation` a parametersLink URI értéket, így a DevTest Labs automatikusan kezelheti a beágyazott `_artifactsLocationSasToken` sablonokat. További információ: Beágyazott Azure Resource Manager [üzembe helyezése tesztelési környezetekhez.](deploy-nested-template-environments.md)

- Metaadatokat definiálhat a sablon megjelenítendő nevének és leírásának megadásához egy fájlban,metadata.js *a következőképpen:*

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Fő Azure Resource Manager sablonfájlok](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Sablontárak hozzáadása a tesztkörnyezethez

Miután létrehozott és konfigurált egy adattárat, hozzáadhatja azt a tesztkörnyezethez az Azure Portal:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza **a Minden szolgáltatás** lehetőséget, majd a listából a **DevTest Labs** szolgáltatásokat.
1. A tesztkörnyezetek listájából válassza ki a kívánt tesztkörnyezetet.
1. A tesztkörnyezet Áttekintés **panelján** válassza a Konfiguráció **és szabályzatok lehetőséget.**

   ![Konfigurálás és szabályzatok](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. A Konfigurációs **és szabályzatbeállítások** listában válassza az **Adattárak lehetőséget.** A **nyilvános összetevő-adattár** automatikusan létrejön az összes tesztkörnyezethez, és csatlakozik a [DevTest Labs nyilvános GitHub-adattárhoz.](https://github.com/Azure/azure-devtestlab)

1. Az új Azure Resource Manager hozzáadásához válassza a Hozzáadás **lehetőséget.**

   ![Nyilvános repo](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Az **Adattárak panelen** adja meg a következő adatokat:

   - **Név:** Adja meg a tesztkörnyezetben használni használt adattár nevét.
   - **Git-klón URL-címe:** Adja meg a Git HTTPS-klón URL-címét a GitHubról vagy az Azure Reposból.
   - **Ág** (nem kötelező): Adja meg az ág nevét a sablondefiníciók Azure Resource Manager eléréséhez.
   - **Személyes hozzáférési jogkivonat:** Adja meg az adattár biztonságos eléréséhez használt személyes hozzáférési jogkivonatot.
     - A jogkivonat azure-adattárakból való lekért adataihoz a profilja alatt válassza a **Felhasználói** beállítások  >  **Biztonsági** személyes  >  **hozzáférési jogkivonatok lehetőséget.**
     - A jogkivonat a GitHubról való lekérhetők. A profilja alatt válassza a **Beállítások**  >  **Fejlesztői beállítások** Személyes hozzáférési  >  **jogkivonatok lehetőséget.**
   - **Mappa elérési útjai:** Adja meg a mappa elérési útját, amely a Git-klón URI-jának relatív elérési útja az összetevő-definíciókhoz vagy a Azure Resource Manager sablondefiníciókhoz.

1. Kattintson a **Mentés** gombra.

   ![Új adattár hozzáadása](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Miután hozzáadta a Azure Resource Manager sablont a tesztkörnyezethez, a tesztkörnyezet felhasználói a sablon használatával hozhatnak létre környezeteket.

## <a name="configure-access-rights-for-lab-users"></a>Hozzáférési jogosultságok konfigurálása tesztkörnyezet felhasználói számára

A tesztkörnyezet felhasználói **alapértelmezés** szerint Olvasó szerepkört töltenek be, így nem tudják módosítani az erőforrásokat egy környezeti erőforráscsoportban. Nem tudják például leállítani vagy elindítani az erőforrásaikat.

Ha közreműködői  szerepkört szeretné adni a tesztkörnyezet felhasználóinak, hogy szerkeszt tudják a környezeteikben található erőforrásokat, kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)a tesztkörnyezet Áttekintés paneljére, válassza a Konfiguráció **és** szabályzatok lehetőséget, majd válassza a **Labor beállításai lehetőséget.** 

1. A **Tesztkörnyezet beállításai panelen** válassza  a **Közreműködő** lehetőséget, majd válassza a Mentés lehetőséget, hogy írási engedélyeket adjon a tesztkörnyezet felhasználóinak.

   ![Tesztkörnyezet felhasználói hozzáférési jogosultságának konfigurálása](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

A következő szakasz végigvezeti a környezetek létrehozásán egy Azure Resource Manager sablonból.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Környezetek létrehozása sablonokból a Azure Portal

Miután hozzáadta Azure Resource Manager sablont a tesztkörnyezethez, a tesztkörnyezet felhasználói az alábbi Azure Portal hozhatnak létre környezeteket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza **a Minden szolgáltatás,** majd a **DevTest Labs** lehetőséget a listából.

1. A tesztkörnyezetek listájából válassza ki a kívánt tesztkörnyezetet.

1. A tesztkörnyezet oldalán válassza a Hozzáadás **lehetőséget.**

1. Az **Alap kiválasztása panelen** megjelenik a használható alapként használható rendszerképek, Azure Resource Manager sablonok vannak felsorolva. Válassza ki Azure Resource Manager kívánt sablont.

   ![Alap kiválasztása](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. A Hozzáadás **panelen** adjon meg egy **Környezeti** név értéket, amely a környezeti felhasználók számára jelenik meg.

   A Azure Resource Manager sablon határozza meg a többi bemeneti mezőt. Ha a sablon *azuredeploy.parameter.jsa fájlban* alapértelmezett értékeket határoz meg, a bemeneti mezőkben ezek az értékek határozzák meg.

   A biztonságos sztring típusú *paraméterekhez* használhatja a titkos Azure Key Vault. Ha többet szeretne megtudni a titkos kulcsok kulcstartóban való tárolásáról és a laborerőforrások létrehozásakor való használatukról, olvassa el a Titkos kulcsok tárolása a [Azure Key Vault.](devtest-lab-store-secrets-in-key-vault.md)  

   ![Panel hozzáadása](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Az alábbi paraméterértékek még akkor sem jelennek meg a bemeneti mezőkben, ha a sablon meghatározza őket. Ehelyett az űrlap üres beviteli mezőket tartalmaz, ahol a tesztkörnyezet felhasználóinak értékeket kell megadniuk a környezet létrehozásakor.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. A **környezet létrehozásához** válassza a Hozzáadás lehetőséget.

   A környezet azonnal megkezdi a kiépítést, és az állapot megjelenik a **Saját virtuális gépek listában.** A tesztkörnyezet automatikusan létrehoz egy új erőforráscsoportot a sablonban meghatározott összes Azure Resource Manager kiépítése érdekében.

1. A környezet létrehozása után válassza ki a környezetet a **Saját** virtuális gépek listában az erőforráscsoport panel megnyitásához és a környezet által kiépített összes erőforrás tallózásához.

   ![Környezeti erőforrások](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   A környezet kibontható úgy is, hogy csak a kiépített virtuális gépek listáját tekintse meg.

   ![Saját virtuális gépek listája](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. A környezetek kiválasztásával megtekintheti az elérhető műveleteket, például az összetevők alkalmazásával, adatlemezek csatolásával, az automatikus leállítási idő módosításával stb.

   ![Környezeti műveletek](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Környezet létrehozásának automatizálása a PowerShell használatával

A Azure Portal használható egy tesztkörnyezet hozzáadásához, de ha egy fejlesztési vagy tesztelési forgatókönyvnek több környezetet kell létrehoznia, az automatikus üzembe helyezés jobb felhasználói élmény.

A folytatás előtt győződjön meg arról, hogy rendelkezik egy Azure Resource Manager sablonnal, amely meghatározza a létrehozni kívánt erőforrásokat. [Adja hozzá és konfigurálja a sablont egy Git-adattárban,](#configure-your-own-template-repositories)majd adja hozzá az [adattárat a tesztkörnyezethez.](#add-template-repositories-to-the-lab)

Az alábbi példaszkprogram létrehoz egy környezetet a tesztkörnyezetben. A megjegyzések segítenek jobban megérteni a szkriptet.

1. Mentse a következő PowerShell-parancsfájlmintát a merevlemezre a *következődeployenv.ps1.*

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName ((Get-AzContext).Account).Id).Id)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. Futtassa a szkriptet a következőképpen: SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (mappa a Git-adattárban) és EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Az Azure CLI-t erőforrások üzembe helyezésére is használhatja Resource Manager sablonokkal. További információ: Erőforrások üzembe [helyezése sablonokkal Resource Manager Azure CLI használatával.](../azure-resource-manager/templates/deploy-cli.md)

> [!NOTE]
> Csak a tesztkörnyezet-tulajdonosi engedélyekkel rendelkező felhasználók hozhatnak létre virtuális gépeket Resource Manager sablonból a Azure PowerShell. Ha egy virtuálisgép-sablonnal Resource Manager automatizálni a virtuális gép létrehozását, és csak felhasználói engedélyekkel rendelkezik, használja az [az lab vm create CLI-parancsot.](/cli/azure/lab/vm#az_lab_vm_create)

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Resource Manager a DevTest Labs sablonkorlátozásai

Vegye figyelembe ezeket a korlátozásokat, amikor Resource Manager DevTest Labs-sablonokat használ:

- Nem hozhat létre képleteket vagy egyéni rendszerképeket a tesztkörnyezet virtuális gépeiből, amelyek egy sablonból Resource Manager létre.

- A legtöbb szabályzatot a rendszer nem értékeli ki, amikor üzembe Resource Manager sablonokat.

Előfordulhat például, hogy egy tesztkörnyezeti szabályzat csak öt virtuális gép létrehozására képes. A felhasználók azonban üzembe helyezhetnek egy Resource Manager, amely több tucat virtuális gépből hoz létre. A nem kiértékelt szabályzatok a következők:

  - Virtuális gépek száma felhasználónként

  - Prémium szintű virtuális gépek száma tesztkörnyezet-felhasználónként

  - Prémium szintű lemezek száma tesztkörnyezet-felhasználónként

## <a name="next-steps"></a>Következő lépések
- Miután létrehozott egy virtuális gépet, a virtuális  gép felügyeleti panelének Csatlakozás lehetőségével csatlakozhat a virtuális géphez.
- A környezet erőforrásainak megtekintéséhez és kezeléséhez válassza ki a környezetet a **tesztkörnyezet Saját virtuális gépek** listájából.
- Ismerkedés [a Azure Resource Manager sablonokkal az Azure gyorsindítási sablongyűjteményében.](https://github.com/Azure/azure-quickstart-templates)
