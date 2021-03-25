---
title: Modulok kezelése az Azure Automationben
description: Ez a cikk azt ismerteti, hogyan használhatók a PowerShell-modulok a runbookok és a DSC-erőforrásokban lévő parancsmagok engedélyezéséhez a DSC-konfigurációkban.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/01/2021
ms.topic: conceptual
ms.openlocfilehash: 57dfc388dcba926706a695c93d2afa1956432be8
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953935"
---
# <a name="manage-modules-in-azure-automation"></a>Modulok kezelése az Azure Automationben

Azure Automation számos PowerShell-modult használ a runbookok-és DSC-erőforrásokban lévő parancsmagok engedélyezéséhez a DSC-konfigurációkban. A támogatott modulok a következők:

* [Azure PowerShell az. Automation](/powershell/azure/new-azureps-module-az).
* [Azure PowerShell AzureRM. Automation](/powershell/module/azurerm.automation/).
* Más PowerShell-modulok.
* Belső `Orchestrator.AssetManagement.Cmdlets` modul.
* Python 2 modulok.
* Az Ön által létrehozott egyéni modulok.

Automation-fiók létrehozásakor a Azure Automation alapértelmezés szerint importál egyes modulokat. Lásd: [alapértelmezett modulok](#default-modules).

## <a name="sandboxes"></a>Tesztkörnyezetek

Amikor az Automation végrehajtja a runbook és a DSC fordítási feladatait, betölti a modulokat a runbookok futtatható és a DSC-konfigurációk fordítására szolgáló sandboxba. Az Automation emellett automatikusan elhelyez minden DSC-erőforrást a modulokban a DSC lekérési kiszolgálón. A gépek lehívhatják az erőforrásokat a DSC-konfigurációk alkalmazása során.

>[!NOTE]
>Ügyeljen arra, hogy csak azokat a modulokat importálja, amelyeket a runbookok és a DSC-konfigurációk igényelnek. Nem javasoljuk a gyökér importálását az modulban. Számos más modult tartalmaz, amelyek esetleg nem szükségesek, ami teljesítménnyel kapcsolatos problémákat okozhat. Importálja az egyes modulokat, például az az. számítási helyet.

A felhőalapú sandbox legfeljebb 48 rendszerhívást támogat, és minden más hívást biztonsági okokból korlátoz. Más funkciók, például a hitelesítő adatok kezelése és egyes hálózatkezelések nem támogatottak a felhőalapú homokozóban.

A tartalmazott modulok és parancsmagok száma miatt nehéz megállapítani, hogy mely parancsmagok nem támogatott hívásokat hajtanak végre. Általánosságban olyan parancsmagokkal kapcsolatos problémák merültek fel, amelyekhez emelt szintű hozzáférés szükséges, hitelesítő adatokat kell megadni paraméterként vagy a hálózatkezeléshez kapcsolódó parancsmagokat. A teljes verem hálózati műveleteket végrehajtó parancsmagok nem támogatottak a homokozóban, beleértve a [AipService](/powershell/module/aipservice/connect-aipservice) a AipService PowerShell-modulból és a [feloldási DnsName](/powershell/module/dnsclient/resolve-dnsname) az dnsclient modulból.

Ezek az ismert korlátozások a homokozóban. Az ajánlott Áthidaló megoldás a [hibrid Runbook-feldolgozók](../automation-hybrid-runbook-worker.md) üzembe helyezése vagy [Azure functions](../../azure-functions/functions-overview.md)használata.

## <a name="default-modules"></a>Alapértelmezett modulok

A következő táblázat felsorolja az Automation-fiók létrehozásakor alapértelmezés szerint Azure Automation importálási modulokat. Az Automation képes a modulok újabb verzióinak importálására. Az eredeti verziót azonban nem távolíthatja el Automation-fiókjából, még akkor is, ha újabb verziót töröl. Vegye figyelembe, hogy ezek az alapértelmezett modulok több AzureRM-modult is tartalmaznak.

Az alapértelmezett modulokat globális moduloknak is nevezik. A Azure Portal a **globális modul** tulajdonság a fiók létrehozásakor importált modul megtekintésekor **igaz** lesz.

![Képernyőfelvétel az Azure Portal globális modul tulajdonságáról](../media/modules/automation-global-modules.png)

Az Automation nem importálja automatikusan az új vagy meglévő Automation-fiókba a gyökeret az modult. A modulok használatával kapcsolatos további információkért lásd: [áttelepítés az modulokra](#migrate-to-az-modules).

> [!NOTE]
> A [Start/Stop VMS During off-hours](../automation-solution-vm-management.md) funkció üzembe helyezéséhez használt Automation-fiókokban nem ajánlott módosítani a modulokat és a runbookok.

|Modul neve|Verzió|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Microsoft. PowerShell. Core | 0 |
| Microsoft. PowerShell. Diagnostics |  |
| Microsoft. PowerShell. Management |  |
| Microsoft. PowerShell. Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft. WSMan. Management |  |
| Orchestrator. AssetManagement. parancsmagok | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>Az modulok

Az az. Automation esetében a parancsmagok többsége ugyanazokat a neveket használja, mint a AzureRM-modulok esetében, azzal a különbséggel, hogy az előtag a következőre `AzureRM` változott: `Az` . Az azon modulok listáját, amelyek nem követik ezt az elnevezési konvenciót, tekintse meg a [kivételek listáját](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Belső parancsmagok

Azure Automation támogatja a `Orchestrator.AssetManagement.Cmdlets` Windows rendszerhez készült log Analytics Agent belső modulját, alapértelmezés szerint telepítve. A következő táblázat a belső parancsmagokat határozza meg. Ezek a parancsmagok úgy lettek kialakítva, hogy Azure PowerShell parancsmagok helyett használják a megosztott erőforrásokkal való kommunikációt. A titkos változóktól, a hitelesítő adatoktól és a titkosított kapcsolatokból származó titkokat is lekérhetik.

>[!NOTE]
>A belső parancsmagok csak akkor érhetők el, ha runbookok hajt végre az Azure-beli homokozó környezetében, vagy egy Windows hibrid Runbook-feldolgozón. 

|Név|Leírás|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Vegye figyelembe, hogy a belső parancsmagok eltérnek az az és a AzureRM parancsmagok elnevezésével. A belső parancsmagok neve nem tartalmaz olyan szavakat `Azure` `Az` , mint a főnév, de a szót használja `Automation` . Azt javasoljuk, hogy használja az az vagy a AzureRM parancsmag használatát az Azure-beli homokozóban vagy egy Windows Hybrid Runbook Worker-ben végzett runbook végrehajtása során. Kevesebb paramétert igényelnek, és a már futó feladatok kontextusában futnak.

Használja az az vagy a AzureRM parancsmagot az Automation-erőforrások runbook környezeten kívüli kezeléséhez. 

## <a name="python-modules"></a>Python-modulok

Azure Automation a Python 2 runbookok is létrehozhatja. A Python-modulok információit lásd: [a Python 2 csomagok kezelése Azure Automationban](../python-packages.md).

## <a name="custom-modules"></a>Egyéni modulok

Azure Automation támogatja a runbookok és DSC-konfigurációkhoz létrehozott egyéni PowerShell-modulokat. Az egyéni modulok egyik típusa egy integrációs modul, amely egy metaadat-fájlt tartalmaz, amely meghatározza a modul-parancsmagok egyéni funkcióit. Egy integrációs modul használatának példáját a [kapcsolattípus hozzáadása](../automation-connections.md#add-a-connection-type)című rész ismerteti.

Azure Automation importálhat egy egyéni modult, hogy elérhetők legyenek a parancsmagok. A színfalak mögött tárolja a modult, és az Azure-beli munkaterületeken használja, ugyanúgy, mint más moduloknál.

## <a name="migrate-to-az-modules"></a>Migrálás az az modulokba

Ez a szakasz azt mutatja be, hogyan lehet migrálni az az modulba az Automation-ben. További információ: [Azure PowerShell migrálása a AzureRM-ről az-](/powershell/azure/migrate-from-azurerm-to-az)ra.

Nem javasoljuk, hogy futtassa az AzureRM modulokat és az az modulokat ugyanabban az Automation-fiókban. Ha biztos abban, hogy át szeretné telepíteni a AzureRM-ről az az-ra, a legjobb megoldás, ha teljes mértékben véglegesíti a teljes áttelepítést. Az Automation gyakran újrahasznosítja az Automation-fiókon belüli munkaterületeket az indítási időpontokban való mentéshez. Ha nem hajtja végre a modul teljes áttelepítését, elindíthat egy olyan feladatot, amely csak a AzureRM modulokat használja, majd elindít egy másik feladatot, amely csak az az modulokat használja. A homokozó hamarosan összeomlik, és hibaüzenetet kap arról, hogy a modulok nem kompatibilisek. Ez a helyzet véletlenszerűen előforduló összeomlásokat eredményez bármely adott runbook vagy konfigurációnál.

>[!NOTE]
>Amikor új Automation-fiókot hoz létre, akkor is, ha az az modulba való Migrálás után is telepíti az AzureRM-modulokat. Az oktatóanyag runbookok továbbra is frissítheti az AzureRM-parancsmagokkal. Ezeket a runbookok azonban nem kell futtatnia.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>A runbookok és a DSC-konfigurációk tesztelése a modul áttelepítése előtt

Ügyeljen arra, hogy gondosan, külön Automation-fiókban tesztelje az összes runbookok és DSC-konfigurációt az az modulba való Migrálás előtt. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Az összes AzureRM-modult használó runbookok leállítása és visszaírása

Annak biztosítása érdekében, hogy ne futtasson olyan meglévő runbookok-vagy DSC-konfigurációkat, amelyek AzureRM-modulokat használnak, le kell állítania és el kell indítania az összes érintett runbookok és konfigurációt. Először is győződjön meg arról, hogy az egyes runbook-vagy DSC-konfigurációkat és azok ütemezett beállításait külön tekinti át, hogy a későbbiekben szükség esetén újraütemezze az elemeket.

Ha készen áll az ütemtervek eltávolítására, használhatja a Azure Portal vagy a [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) parancsmagot. Lásd: [az ütemterv eltávolítása](schedules.md#remove-a-schedule).

### <a name="remove-azurerm-modules"></a>AzureRM modulok eltávolítása

Az az modulok importálása előtt el lehet távolítani a AzureRM modulokat. Ha azonban ezt teszi, megszakíthatja a verziókövetés szinkronizálását, és a még sikertelenül ütemezett parancsfájlokat is okozhat. Ha úgy dönt, hogy eltávolítja a modulokat, tekintse meg a [AzureRM eltávolítása](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm)című témakört.

### <a name="import-az-modules"></a>Importálás az modulok

Az az modul az Automation-fiókba való importálása nem importálja automatikusan a modult a runbookok által használt PowerShell-munkamenetbe. A modulok a következő helyzetekben importálhatók a PowerShell-munkamenetbe:

* Amikor egy runbook meghívja a parancsmagot egy modulból.
* Amikor egy runbook explicit módon importálja a modult az [import-Module](/powershell/module/microsoft.powershell.core/import-module) parancsmaggal.
* Amikor egy runbook explicit módon importálja a modult a [using Module](/powershell/module/microsoft.powershell.core/about/about_using#module-syntax) utasítással. A using utasítás a Windows PowerShell 5,0-től kezdve támogatott, és támogatja az osztályok és az enumerálási típusok importálását.
* Ha egy runbook egy másik függő modult importál.

Az az modulokat az Automation-fiókba importálhatja a Azure Portalból. Ne feledje, hogy csak az az általa igényelt modulokat importálja, nem minden az elérhető modul. Mivel az [az. accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) a többi az modultól való függőség, ügyeljen arra, hogy a modult minden más előtt importálja.

1. Az Automation-fiókban a **megosztott erőforrások** területen válassza a **modulok** elemet.
2. Válassza a **Tallózás** katalógus lehetőséget.  
3. A keresősáv mezőben adja meg a modul nevét (például: `Az.Accounts` ).
4. A PowerShell-modul lapon válassza az **Importálás** lehetőséget, hogy importálja a modult az Automation-fiókjába.

    ![Képernyőfelvétel a modulok automatizálási fiókba való importálásáról](../media/modules/import-module.png)

Ezt az importálást a [PowerShell-Galéria](https://www.powershellgallery.com)keresztül is végrehajthatja, ha az importálni kívánt modulra keres rá. Ha megtalálta a modult, jelölje ki, majd kattintson a **Azure Automation** fülre. Válassza **a Azure Automation üzembe helyezés** lehetőséget.

![Képernyőfelvétel a modulok közvetlen importálásáról PowerShell-galéria](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>A runbookok tesztelése

Miután importálta az az modulokat az Automation-fiókba, megkezdheti a runbookok és a DSC-konfigurációk szerkesztését az új modulok használatához. A runbook módosításának az új parancsmagok használatára való tesztelésének egyik módja a `Enable-AzureRmAlias -Scope Process` runbook elején található parancs használata. Ha hozzáadja ezt a parancsot a runbook, a parancsfájl módosítása nélkül is futtatható.

## <a name="author-modules"></a>Szerzői modulok

Javasoljuk, hogy kövesse az ebben a szakaszban ismertetett szempontokat, amikor egyéni PowerShell-modult hoz létre Azure Automationban való használatra. A modul importálásra való előkészítéséhez létre kell hoznia legalább egy. psd1,. psm1 vagy PowerShell Module **. dll** fájlt, amelynek a neve megegyezik a Module mappával. Ezután elvégezheti a modul mappájának kitömörítését, hogy Azure Automation egyetlen fájlként importálja. A **. zip** -csomag nevének meg kell egyeznie a tárolt modul mappájának nevével.

Ha többet szeretne megtudni a PowerShell-modulok létrehozásáról, tekintse meg [a PowerShell parancsfájl-modul írása](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)című témakört.

### <a name="version-folder"></a>Verzió mappája

A PowerShell párhuzamos modulok verziószámozása lehetővé teszi, hogy egy modul több verzióját használja a PowerShellen belül. Ez akkor lehet hasznos, ha régebbi parancsfájlok lettek tesztelve, és csak egy PowerShell-modul egy adott verziójával működnek, de más parancsfájlokhoz ugyanaz a PowerShell-modul újabb verziója szükséges.

PowerShell-modulok létrehozásához, hogy azok több verziót tartalmazzanak, hozza létre a modul mappáját, majd hozzon létre egy mappát az ebben a modulban lévő mappában a használni kívánt modul minden egyes verziójához. A következő példában egy *TestModule* nevű modul két verziót biztosít, a 1.0.0-t és a 2.0.0-t.

```dos
TestModule
   1.0.0
   2.0.0
```

Az egyes verziók mappáiban másolja a PowerShell. psm1,. psd1 vagy PowerShell modul **. dll** fájlokat, amelyek egy modult alkotnak a megfelelő verziójú mappába. Zip-fel a modul mappájába, hogy Azure Automation egyetlen. zip fájlként importálható legyen. Míg az Automation csak az importált modul legmagasabb verzióját jeleníti meg, ha a modul a modul egymás melletti verzióit tartalmazza, azok mind elérhetők a runbookok vagy a DSC-konfigurációkhoz.  

Míg az Automation támogatja az egyazon csomagon belüli párhuzamos verziókat tartalmazó modulok használatát, nem támogatja a modul több verzióját a modul-csomagok importálása során. Importálhatja például az **a modult**, amely az Automation-fiókjába az 1. és a 2. verziót tartalmazza. Később, ha az **a modult** frissíti, hogy a 3. és a 4. verziót is tartalmazza, az Automation-fiókba való importáláskor csak a 3. és 4. verzió használható a runbookok-vagy DSC-konfigurációkon belül. Ha az összes verzióra szüksége van – 1, 2, 3 és 4, hogy elérhető legyen, az importáláshoz szükséges. zip fájlnak az 1, 2, 3 és 4 verziót kell tartalmaznia.

Ha ugyanazon modul különböző verzióit fogja használni a runbookok között, mindig deklarálja a runbook használni kívánt verziót a parancsmag használatával, `Import-Module` és adja meg a paramétert `-RequiredVersion <version>` . Még akkor is, ha a használni kívánt verzió a legújabb verzió. Ennek az az oka, hogy a runbook-feladatok ugyanabban a homokozóban futhatnak. Ha a sandbox már kifejezetten betöltött egy adott verziószámú modult, mert a homokozóban egy korábbi feladat azt mondta, hogy a homokozóban lévő jövőbeli feladatok nem töltik be automatikusan a modul legújabb verzióját. Ennek az az oka, hogy egy adott verziója már be van töltve a homokozóban.

A DSC-erőforrások esetében használja az alábbi parancsot egy adott verzió megadásához:

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>Súgó információi

Adja meg a modul összes parancsmagjának szinopszisát, leírását és súgóját. A PowerShellben a parancsmag segítségével megadhatja a parancsmagok súgójának adatait `Get-Help` . Az alábbi példa bemutatja, hogyan határozhat meg egy szinopszist és egy Súgó URI-t egy **. psm1** modul-fájlban.

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Az információ megadása a `Get-Help` PowerShell-konzolon található parancsmagon keresztül jeleníti meg a Súgó szövegét. Ez a szöveg a Azure Portal is megjelenik.

  ![Az integrációs modul súgójának képernyőképe](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Kapcsolat típusa

Ha a modul egy külső szolgáltatáshoz csatlakozik, a kapcsolat típusát [Egyéni integrációs modul](#custom-modules)használatával definiálhatja. A modul minden parancsmagjának el kell fogadnia egy adott kapcsolattípus (a kapcsolatfájl) egy példányát paraméterként. A felhasználók a kapcsolódási eszköz paramétereit a parancsmaghoz tartozó paraméterekhez rendelik a parancsmag meghívásakor.

![Egyéni kapcsolatok használata a Azure Portal](../media/modules/connection-create-new.png)

Az alábbi runbook-példa egy nevű contoso-kapcsolati eszközt használ a `ContosoConnection` contoso-erőforrások eléréséhez, és a külső szolgáltatástól származó adatok visszaküldéséhez. Ebben a példában a mezők `UserName` egy objektum és tulajdonságaira vannak leképezve, `Password` `PSCredential` majd át lesznek adva a parancsmagnak.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

Ennek a viselkedésnek a könnyebb és hatékonyabb megközelítése az, ha közvetlenül átadja a kapcsolódási objektumot a parancsmagnak:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

A parancsmagok hasonló viselkedését engedélyezheti, ha lehetővé teszi, hogy közvetlenül paraméterként fogadják el a kapcsolódási objektumokat, ahelyett, hogy a paraméterekhez csak a kapcsolódási mezőket. Általában egy paramétert kell beállítania, hogy az Automationt nem használó felhasználók a szórótábla létrehozása nélkül is meghívhatják a parancsmagokat. A set paraméter a `UserAccount` kapcsolódási mező tulajdonságainak továbbítására szolgál. `ConnectionObject` lehetővé teszi a kapcsolatok közvetlen továbbítását.

### <a name="output-type"></a>Kimenet típusa

Adja meg a modul összes parancsmagjának kimeneti típusát. A parancsmagok kimeneti típusának meghatározása lehetővé teszi a tervezési idejű IntelliSense használatát a parancsmag kimeneti tulajdonságainak meghatározásához a szerzői műveletek során. Ez a gyakorlat különösen hasznos a grafikus runbook készítése során, amelynek a tervezési idejű ismerete kulcsfontosságú a modul egyszerű felhasználói élményéhez.

Hozzáadás `[OutputType([<MyOutputType>])]` , where `MyOutputType` is érvényes típus. További információ `OutputType` : [About functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). A következő kód egy példa a parancsmag hozzáadására `OutputType` :

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![A grafikus runbook kimeneti típusának képernyőképe](../media/modules/runbook-graphical-module-output-type.png)

  Ez a viselkedés hasonló a parancsmag kimenetének "type Ahead" funkcióhoz a PowerShell integrációs szolgáltatási környezetében anélkül, hogy futtatni kellene.

  ![Képernyőfelvétel az POSH IntelliSenseról](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Parancsmag állapota

Az összes parancsmagot állapotba kell tenni a modulban. Egyszerre több runbook-feladat is futtatható ugyanabban a `AppDomain` folyamatban és a homokozóban. Ha az ilyen szinteken bármilyen állapot van megosztva, a feladatok hatással lehetnek egymásra. Ez a viselkedés időszakos és nehezen diagnosztizálható problémákhoz vezethet. Íme egy példa arra, hogy mi a teendő:

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>Modul függősége

Győződjön meg arról, hogy a modul teljes mértékben megtalálható egy csomagban, amely az xcopy használatával másolható. Az Automation-modulok a runbookok végrehajtásakor vannak kiosztva az automatizálási munkaterületekre. A moduloknak az azokat futtató gazdagéptől függetlenül kell működniük.

Képesnek kell lennie arra, hogy ZIP-csomagot helyezzen el, és áthelyezze a modult, és a szokásos módon működjön, ha egy másik gazdagép PowerShell-környezetébe importálja. Ehhez győződjön meg arról, hogy a modul nem függ a modulnak az Automation szolgáltatásba való importálásakor tömörített fájlon kívüli fájloktól.

A modul nem függhet a gazdagép egyedi beállításjegyzék-beállításaitól. Ilyenek például a termék telepítésekor végrehajtott beállítások.

### <a name="module-file-paths"></a>Modul fájlelérési útjai

Győződjön meg arról, hogy a modulban lévő összes fájl 140 karakternél rövidebb elérési úttal rendelkezik. Az 140 karakternél hosszabb útvonalak a runbookok importálásával kapcsolatos problémákat okozhatnak. Az Automation nem tud olyan fájlt importálni, amelynek elérési útja 140 karakternél hosszabb a PowerShell-munkamenetben `Import-Module` .

## <a name="import-modules"></a>Modulok importálása

Ez a szakasz számos módszert határoz meg, amelyekkel modulokat importálhat az Automation-fiókjába.

### <a name="import-modules-in-the-azure-portal"></a>Modulok importálása a Azure Portal

Modul importálása a Azure Portalban:

1. Nyissa meg az Automation-fiókját.
2. A **megosztott erőforrások** területen válassza a **modulok** elemet.
3. Válassza **a modul hozzáadása** lehetőséget.
4. Válassza ki a modult tartalmazó **. zip** fájlt.
5. Kattintson **az OK** gombra az importálási folyamat megkezdéséhez.

### <a name="import-modules-by-using-powershell"></a>Modulok importálása a PowerShell használatával

A [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) parancsmag használatával egy modult importálhat az Automation-fiókjába. A parancsmag egy Module. zip-csomag URL-címét veszi át.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Ugyanezt a parancsmagot használhatja arra is, hogy közvetlenül a PowerShell-galéria importáljon egy modult. Ügyeljen arra, hogy `ModuleName` `ModuleVersion` a [PowerShell-Galéria](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Modulok importálása a PowerShell-galériaból

[PowerShell-Galéria](https://www.powershellgallery.com) modulokat közvetlenül a katalógusból vagy Automation-fiókjából is importálhatja.

Modul importálása közvetlenül a PowerShell-galériaról:

1. Keresse meg https://www.powershellgallery.com az importálni kívánt modult, és keresse meg.
2. A **telepítési beállítások** alatt, a **Azure Automation** lapon válassza a **telepítés a Azure Automation** lehetőséget. Ez a művelet megnyitja a Azure Portal. 
3. Az importálás lapon válassza ki az Automation-fiókját, és kattintson az **OK gombra**.

![A PowerShell-galéria importálási modul képernyőképe](../media/modules/powershell-gallery.png)

PowerShell-galéria modul importálása közvetlenül az Automation-fiókból:

1. A **megosztott erőforrások** területen válassza a **modulok** elemet. 
2. Válassza a **Tallózás** katalógus lehetőséget, majd keresse meg a katalógusban a modult. 
3. Válassza ki az importálni kívánt modult, és válassza az **Importálás** lehetőséget. 
4. Az importálási folyamat elindításához kattintson **az OK gombra** .

![Képernyőkép a PowerShell-galéria modul importálásáról a Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Modulok törlése

Ha problémája van egy modullal, vagy vissza kell állítania egy modul egy korábbi verzióját, törölheti azt az Automation-fiókjából. Az Automation-fiók létrehozásakor importált [alapértelmezett modulok](#default-modules) eredeti verziói nem törölhetők. Ha a törlendő modul az egyik [alapértelmezett modul](#default-modules)újabb verziója, a rendszer Visszagörgeti az Automation-fiókjával telepített verzióra. Ellenkező esetben a rendszer eltávolítja az Automation-fiókból törölt modulokat.

### <a name="delete-modules-in-the-azure-portal"></a>Modulok törlése a Azure Portal

Modul eltávolítása a Azure Portalban:

1. Nyissa meg az Automation-fiókját. A **megosztott erőforrások** területen válassza a **modulok** elemet.
2. Válassza ki az eltávolítani kívánt modult.
3. A modul lapon válassza a **Törlés** lehetőséget. Ha ez a modul az [alapértelmezett modulok](#default-modules)egyike, az visszagörget az Automation-fiók létrehozásakor létezett verzióra.

### <a name="delete-modules-by-using-powershell"></a>Modulok törlése a PowerShell használatával

Modul a PowerShell használatával történő eltávolításához futtassa a következő parancsot:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Következő lépések

* Azure PowerShell modulok használatával kapcsolatos további információkért lásd: [az Azure PowerShell](/powershell/azure/get-started-azureps)használatának első lépései.

* További információ a PowerShell-modulok létrehozásáról: [Windows PowerShell-modul írása](/powershell/scripting/developer/module/writing-a-windows-powershell-module).