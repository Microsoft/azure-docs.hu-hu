---
title: Modulok kezelése az Azure Automationben
description: Ez a cikk azt mutatja be, hogyan használhatók a PowerShell-modulok a parancsmagok runbookok és DSC-erőforrások DSC-konfigurációkban való engedélyezésére.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/01/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eaff96907b48ddc0fc92296a015ceb063149e6ec
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832751"
---
# <a name="manage-modules-in-azure-automation"></a>Modulok kezelése az Azure Automationben

Azure Automation PowerShell-modulokkal engedélyezi a parancsmagokat runbookok és DSC-erőforrások DSC-konfigurációkban. A támogatott modulok a következők:

* [Azure PowerShell Az.Automation .](/powershell/azure/new-azureps-module-az)
* [Azure PowerShell AzureRM.Automation .](/powershell/module/azurerm.automation/)
* Egyéb PowerShell-modulok.
* Belső `Orchestrator.AssetManagement.Cmdlets` modul.
* Python 2 modulok.
* Az Ön által létrehozott egyéni modulok.

Automation-fiók létrehozásakor a Azure Automation importál néhány modult. Lásd: [Alapértelmezett modulok.](#default-modules)

## <a name="sandboxes"></a>Tesztkörnyezetek

Amikor az Automation runbook- és DSC-fordítási feladatokat hajt végre, betölti a modulokat a sandboxesbe, ahol a runbookok futtathatók, és a DSC-konfigurációk lefordíthatóak. Az Automation automatikusan a DSC lekért kiszolgáló moduljaiba is elhelyezi az összes DSC-erőforrást. A gépek a DSC-konfigurációk alkalmazásakor lek tudják lekért erőforrásokat.

>[!NOTE]
>Csak a runbookok és a DSC-konfigurációk által szükséges modulokat importálja. Nem javasoljuk a gyökér Szintű Az modul importálását. Sok más modult is tartalmaz, amelyekre esetleg nincs szüksége, ami teljesítményproblémákat okozhat. Importáljon inkább egyéni modulokat, például az Az.Compute modult.

A felhőalapú védőfal legfeljebb 48 rendszerhívást támogat, és biztonsági okokból minden más hívást korlátoz. Más funkciók, például a hitelesítő adatok kezelése és bizonyos hálózatkezelések nem támogatottak a felhőalapú sandboxban.

A modulok és parancsmagok száma miatt nehéz előre tudni, hogy melyik parancsmag fog nem támogatott hívásokat kezdeményezni. Általában olyan parancsmagokkal kapcsolatos problémákat láttunk, amelyek emelt szintű hozzáférést igényelnek, paraméterként hitelesítő adatokat vagy hálózattal kapcsolatos parancsmagokat igényelnek. A teljes verem hálózati műveleteket végző parancsmagok nem támogatottak a sandboxban, beleértve az AIPService [PowerShell-modul Connect-AipService](/powershell/module/aipservice/connect-aipservice) parancsmagját és a DNSClient modul [Resolve-DnsName](/powershell/module/dnsclient/resolve-dnsname) parancsmagját.

Ezek a sandbox ismert korlátozásai. Az ajánlott áthidaló megoldás egy hibrid [runbook-feldolgozó üzembe helyezése,](../automation-hybrid-runbook-worker.md) vagy a [Azure Functions.](../../azure-functions/functions-overview.md)

## <a name="default-modules"></a>Alapértelmezett modulok

Az alábbi táblázat azokat a modulokat sorolja fel Azure Automation az Automation-fiók létrehozásakor alapértelmezés szerint importálja az adatokat. Az Automation képes importálni ezeknek a moduloknak az újabb verzióit. Az eredeti verziót azonban nem távolíthatja el az Automation-fiókból, még akkor sem, ha egy újabb verziót töröl. Vegye figyelembe, hogy ezek az alapértelmezett modulok több AzureRM-modult is tartalmaznak.

Az alapértelmezett modulok más néven globális modulok. A Azure Portal globális modultulajdonság igaz lesz  a fiók létrehozásakor importált modul megtekintésekor. 

![Képernyőkép az Azure Portal globális modultulajdonságról](../media/modules/automation-global-modules.png)

Az Automation nem importálja automatikusan a gyökér Szintű Az modult egy új vagy meglévő Automation-fiókba. A modulok használatával kapcsolatos további információkért lásd: [Migrating to Az modules (Áttelepítés az Az modulokba).](#migrate-to-az-modules)

> [!NOTE]
> Nem javasoljuk a modulok és runbookok olyan Automation-fiókokban való megváltoztatását, amelyek a szolgáltatás üzembe [helyezéséhez Start/Stop VMs during off-hours](../automation-solution-vm-management.md) használhatók.

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
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCTartománycsatlakozás | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>Az modulok

Az Az.Automation esetében a parancsmagok többsége ugyanazokkal a nevekkel rendelkezik, mint az AzureRM-modulok esetében, azzal a kivételrel, hogy az előtag a következőre `AzureRM` módosult: `Az` . Azon Az modulok listáját, amelyek nem követik ezt az elnevezési konvenciót, tekintse meg [a kivételek listáját.](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)

## <a name="internal-cmdlets"></a>Belső parancsmagok

Azure Automation támogatja a `Orchestrator.AssetManagement.Cmdlets` Windowshoz való Log Analytics-ügynök belső modulját, amely alapértelmezés szerint telepítve van. Az alábbi táblázat a belső parancsmagokat definiálja. Ezek a parancsmagok úgy vannak kialakítva, hogy a Azure PowerShell helyett a megosztott erőforrásokkal való interakcióra használjanak. A titkos kódokat titkosított változókból, hitelesítő adatokból és titkosított kapcsolatokból tudják lekérni.

>[!NOTE]
>A belső parancsmagok csak akkor érhetők el, ha runbookokat futtat az Azure-beli sandbox-környezetben vagy egy Windows rendszerű hibrid runbook-feldolgozón. 

|Név|Leírás|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Vegye figyelembe, hogy a belső parancsmagok elnevezése eltér az Az és az AzureRM parancsmagok elnevezéseitől. A belső parancsmagnevek nem tartalmaznak olyan szavakat, mint a vagy a `Azure` `Az` főnév, de használja a következőt: `Automation` . Javasoljuk, hogy az Azure-beli vagy a hibrid Runbook-feldolgozón futtatott Runbook-végrehajtás során használja az Az- vagy AzureRM-parancsmagokat. Kevesebb paramétert igényelnek, és a már futó feladat környezetében futnak.

Az Az vagy Az AzureRM-parancsmagok használatával egy runbook környezetén kívüli Automation-erőforrásokat lehet manipulálni. 

## <a name="python-modules"></a>Python-modulok

Python 2-runbookokat a Azure Automation. A Python-modulokkal kapcsolatos információkért lásd: [Python 2-csomagok kezelése a Azure Automation.](../python-packages.md)

## <a name="custom-modules"></a>Egyéni modulok

Azure Automation támogatja a runbookokkal és DSC-konfigurációval használható egyéni PowerShell-modulokat. Az egyéni modulok egyik típusa egy integrációs modul, amely opcionálisan tartalmaz egy metaadatfájlt, amely meghatározza a modul parancsmagok egyéni funkcióit. Az integrációs modul használatára egy példát a Kapcsolattípus [hozzáadása részen található.](../automation-connections.md#add-a-connection-type)

Azure Automation importálhat egy egyéni modult, hogy elérhetővé tegye a parancsmagokat. A színfalak mögött tárolja a modult, és az Azure-védőfalon használja, ahogy más modulokat is.

## <a name="migrate-to-az-modules"></a>Áttelepítés az Az modulokba

Ez a szakasz az Automation Az moduljaira való áttelepítést mutatja be. További információ: [Migrate Azure PowerShell from AzureRM to Az](/powershell/azure/migrate-from-azurerm-to-az)(Áttelepítés az AzureRM-ről az Az-be).

Nem javasoljuk az AzureRM-modulok és az Az modulok ugyanabban az Automation-fiókban való futtatását. Ha biztos benne, hogy az AzureRM-ről az Az-re szeretne migrálni, akkor a legjobb, ha teljes mértékben véglegesítést ad egy teljes migrálásra. Az Automation gyakran újra felhasználja a védőfalat az Automation-fiókban az indítási idő megtakarítása érdekében. Ha nem végez teljes moduláttelepítést, elindíthat egy csak AzureRM modulokat használó feladatot, majd elindíthat egy másik feladatot, amely csak az Az modulokat használja. A védőfal hamarosan összeomlik, és hibaüzenetet kap, amely szerint a modulok nem kompatibilisek. Ez a helyzet véletlenszerű összeomlásokat ad vissza egy adott runbookhoz vagy konfigurációhoz.

>[!NOTE]
>Amikor új Automation-fiókot hoz létre, még az Az modulokba való migrálás után is, az Automation alapértelmezés szerint telepíti az AzureRM-modulokat. Az oktatóanyag runbookja továbbra is frissítheti az AzureRM-parancsmagokkal. Ezeket a runbookokat azonban nem szabad futtatni.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Runbookok és DSC-konfigurációk tesztelése a modul migrálása előtt

Az Az modulokba való mirating előtt gondosan tesztelje az összes runbookot és DSC-konfigurációt egy külön Automation-fiókban. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Állítsa le és ne ütemezje az AzureRM modulokat használ összes runbookot

Annak érdekében, hogy ne futtatassa az AzureRM-modulokat használó meglévő runbookokat vagy DSC-konfigurációkat, le kell állítania az összes érintett runbookot és konfigurációt, és meg kell azt nem ütemeznie. Először is tekintse át külön az egyes Runbook- vagy DSC-konfigurációkat és ütemezéseket, hogy szükség esetén biztosan átütemezhető legyen az elem.

Ha készen áll az ütemezések eltávolítására, használhatja a Azure Portal vagy a [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) parancsmagot. Lásd: [Ütemezés eltávolítása.](schedules.md#remove-a-schedule)

### <a name="remove-azurerm-modules"></a>AzureRM-modulok eltávolítása

Az Az modulok importálása előtt el is távolíthatja az AzureRM modulokat. Ha azonban ezt teszi, megszakíthatja a forrásvezérlő szinkronizálását, és a továbbra is ütemezett parancsfájlok meghiúsulnak. Ha úgy dönt, hogy eltávolítja a modulokat, tekintse meg [az AzureRM eltávolítását.](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm)

### <a name="import-az-modules"></a>Az modulok importálása

Az Az modul Automation-fiókba importálása nem importálja automatikusan a modult a runbookok által használt PowerShell-munkamenetbe. A modulok importálása a PowerShell-munkamenetbe a következő helyzetekben kerül sor:

* Amikor egy runbook meghív egy parancsmagot egy modulból.
* Amikor egy runbook explicit módon importálja a modult az [Import-Module](/powershell/module/microsoft.powershell.core/import-module) parancsmaggal.
* Amikor egy runbook explicit módon importálja a modult a [using module utasítással.](/powershell/module/microsoft.powershell.core/about/about_using#module-syntax) A using utasítás az 5.0-s Windows PowerShell kezdve támogatott, és támogatja az osztályok és felsorolási típus importálását.
* Amikor egy runbook egy másik függő modult importál.

Az Az modulokat importálhatja az Automation-fiókba az Azure Portal. Ne feledje csak a szükséges Az modulokat importálni, nem minden elérhető Az modult. Mivel [az Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) a többi Az modul függősége, mindenképpen a többi modul előtt importálja ezt a modult.

1. Az Automation-fiók Megosztott erőforrások **területén válassza** a Modulok **lehetőséget.**
2. Válassza **a Browse Gallery (Katalógus tallózása) lehetőséget.**  
3. A keresősávba írja be a modul nevét `Az.Accounts` (például: ).
4. A PowerShell-modul oldalon válassza az Importálás **lehetőséget** a modul Automation-fiókba való importálásához.

    ![A modulok Automation-fiókba importálásának képernyőképe](../media/modules/import-module.png)

Ezt az importálást a fájlban [is PowerShell-galéria,](https://www.powershellgallery.com)ha rákeres az importálni a modulra. Amikor megtalálja a modult, jelölje  ki, majd válassza a Azure Automation fület. Válassza **az Üzembe helyezés a Azure Automation.**

![Képernyőkép a modulok importálásának PowerShell-galéria](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Runbookok tesztelése

Miután importálta az Az modulokat az Automation-fiókba, elkezdheti szerkeszteni a forgatókönyveket és a DSC-konfigurációkat az új modulok használatára. A runbookok új parancsmagok használatára való módosításának tesztelésének egyik módja a runbook elején található `Enable-AzureRmAlias -Scope Process` parancs használata. Ha hozzáadja ezt a parancsot a runbookhoz, a szkript módosítások nélkül futtatható.

## <a name="author-modules"></a>Modulok írása

Javasoljuk, hogy kövesse az ebben a szakaszban található szempontokat, amikor egyéni PowerShell-modult hozhat létre a Azure Automation. A modul importálásra való előkészítéséhez létre kell hoznia legalább egy .psd1, .psm1 vagy PowerShell-modul **.dll** fájlt a modulmappával azonos néven. Ezután tömöríti a modulmappát, hogy Azure Automation importálni tudja egyetlen fájlként. A **.zip csomag** nevének meg kell egy lennie a tartalmazott modul mappájának nevével.

A PowerShell-modulok írásának további információért lásd: [PowerShell-szkriptmodul írása.](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)

### <a name="version-folder"></a>Verziómappa

A PowerShell-modulok egymás mellett való verziószámozása lehetővé teszi egy modul több verziójának használatát a PowerShellben. Ez akkor lehet hasznos, ha régebbi szkripteket tesztelt, és csak egy PowerShell-modul egy bizonyos verzióján működnek, de más szkriptek ugyanazon PowerShell-modul újabb verzióját igénylik.

Ha több verziót tartalmazó PowerShell-modulokat szeretne létrehozni, hozza létre a modulmappát, majd hozzon létre egy mappát ebben a modulmappában a modul minden olyan verziójához, amely használható. A következő példában a *TestModule* nevű modul két verziót biztosít: 1.0.0 és 2.0.0.

```dos
TestModule
   1.0.0
   2.0.0
```

A verziómappákban másolja a modult tartalmazó PowerShell .psm1, .psd1 vagy PowerShell-modul **.dll** fájljait a megfelelő verziómappába. Tömörítse össze a modulmappát, Azure Automation importálni tudja egyetlen .zip-fájlként. Bár az Automation csak az importált modul legmagasabb verzióját jeleníti meg, ha a modulcsomag a modul egymás mellett futó verzióit tartalmazza, ezek mind elérhetők a runbookok vagy a DSC-konfigurációkban.  

Bár az Automation támogatja az egy csomagban található, egymás mellett verziókat tartalmazó modulokat, nem támogatja a modulok több verziójának a modulcsomagok importálható változatait. Importálhatja például az **A modult,** amely az 1. és a 2. verziót tartalmazza az Automation-fiókjába. Később frissíti az **A** modult, hogy az tartalmazza a 3. és a 4. verziót. Az Automation-fiókba való importáláskor csak a 3. és a 4. verzió használható minden runbookban vagy DSC-konfigurációban. Ha az összes verzió – 1, 2, 3 és 4 – elérhetővé kell lennie, az importált .zip fájlnak az 1., 2., 3. és 4. verziót kell tartalmaznia.

Ha ugyanazon modul különböző verzióit szeretné használni a runbookok között, mindig deklarálja a runbookban használni kívánt verziót a parancsmag használatával, és foglalja bele a `Import-Module` `-RequiredVersion <version>` paramétert. Még akkor is, ha a használni kívánt verzió a legújabb. Ennek az az oka, hogy a Runbook-feladatok ugyanabban a sandboxban futhatnak. Ha a védőfal már betöltött egy bizonyos verziószámú modult, mert a védőfal egy korábbi feladata ezt mondta, a későbbi feladatok nem töltődnek be automatikusan a modul legújabb verziójába. Ennek az az oka, hogy a verzió egy része már be van töltve a sandboxba.

DSC-erőforráshoz használja a következő parancsot egy adott verzió megadásához:

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>Súgóinformációk

A modul minden parancsmagja esetén tartalmazni kell egy összefoglalót, egy leírást és egy súgó URI-t. A PowerShellben a parancsmagokkal súgóinformációkat határozhat meg a `Get-Help` parancsmagok számára. Az alábbi példa bemutatja, hogyan definiálhat egy összefoglalót és egy súgó URI-t egy **.psm1** modulfájlban.

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
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Ha ezt az információt megmutatja, megjelenik egy súgószöveg a `Get-Help` PowerShell-konzol parancsmagján keresztül. Ez a szöveg is megjelenik a Azure Portal.

  ![Képernyőkép az integrációs modul súgóról](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Kapcsolat típusa

Ha a modul külső szolgáltatáshoz csatlakozik, definiálja a kapcsolat típusát egy [egyéni integrációs modullal.](#custom-modules) A modulban minden parancsmagnak el kell fogadnia az adott kapcsolattípus (kapcsolatobjektum) egy példányát paraméterként. A felhasználók a kapcsolati eszköz paramétereit leképezik a parancsmag megfelelő paramétereire minden alkalommal, amikor egy parancsmagot hívnak meg.

![Egyéni kapcsolat használata a Azure Portal](../media/modules/connection-create-new.png)

A következő runbook-példa egy nevű Contoso kapcsolati adateszközt használ a Contoso-erőforrások eléréséhez és a külső szolgáltatásból `ContosoConnection` való adat visszaszolgáltatáshoz. Ebben a példában a mezők egy objektum és tulajdonságára vannak leképezve, majd át vannak adhatja `UserName` `Password` a `PSCredential` parancsmagnak.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

Ennek a viselkedésnek a megközelítésének egyszerűbb és jobb módja, ha közvetlenül a kapcsolatobjektumot a parancsmagnak ad át:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

Hasonló viselkedést engedélyezhet a parancsmagok számára, ha lehetővé teszi számukra, hogy közvetlenül paraméterként fogadják el a kapcsolati objektumokat ahelyett, hogy csak a paraméterek kapcsolatmezőivel tennének. Általában mindegyikhez szükség van egy paraméterkészletre, így az Automationt nem használó felhasználók anélkül hívhatja meg a parancsmagokat, hogy egy kivonattáblát hozna létre, amely kapcsolati objektumként működik. A paraméterkészlet `UserAccount` a kapcsolatmező tulajdonságainak ad át. `ConnectionObject` A lehetővé teszi a kapcsolat egyszerű áthaladását.

### <a name="output-type"></a>Kimenet típusa

Határozza meg a modulban található összes parancsmag kimenettípusát. A parancsmagok kimeneti típusának definiálása lehetővé teszi, hogy a tervezés során az IntelliSense segítsen meghatározni a parancsmag kimeneti tulajdonságait a írás során. Ez a gyakorlat különösen hasznos a grafikus runbookok írása során, amelyek tervezés közbeni ismerete kulcsfontosságú a modul egyszerű felhasználói élményéhez.

Adja `[OutputType([<MyOutputType>])]` hozzá a `MyOutputType` következőt: , ahol az érvényes típus. További információ a `OutputType` függvényről: [A Functions OutputTypeAttribute attribútuma.](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute) Az alábbi kód egy példa a `OutputType` parancsmagok hozzáadására:

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

  ![Képernyőkép a grafikus runbook kimenettípusról](../media/modules/runbook-graphical-module-output-type.png)

  Ez a viselkedés hasonló a parancsmag kimenetének "type ahead" funkcióhoz a PowerShell integrációs szolgáltatási környezetben, futtatás nélkül.

  ![Képernyőkép a POSH IntelliSense-ről](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Parancsmag állapota

A modulban található összes parancsmagot állapot nélkülire kell tenni. Egyszerre több Runbook-feladat is futtatható ugyanabban a folyamatban és a `AppDomain` sandboxban. Ha ezen szinteken bármilyen állapot meg van osztva, a feladatok hatással lehetnek egymásra. Ez a viselkedés időszakos és nehezen diagnosztizálható problémákhoz vezethet. Alábbi példa arra, hogy mit ne tegyenek:

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

### <a name="module-dependency"></a>Modulfüggőség

Győződjön meg arról, hogy a modul teljes egészében egy olyan csomagban található, amely az xcopy használatával másolható. Az Automation-modulok a runbookok végrehajtásakor oszlnak el az Automation-védőfalra. A moduloknak az azokat futtató gazdagéptől függetlenül kell működniük.

Tömörítsen és helyezzen át egy modulcsomagot, és a szokásos módon működtetje, amikor egy másik gazdagép PowerShell-környezetbe importálja. Ahhoz, hogy ez megtörténjen, győződjön meg arról, hogy a modul nem függ a modul mappán kívüli fájloktól, amelyek tömörítve vannak a modul Automationbe való importálásakor.

A modul nem függhet a gazdagép egyedi beállításjegyzék-beállításaitól. Ilyenek például a termék telepítésekor megadott beállítások.

### <a name="module-file-paths"></a>Modulfájl elérési útjai

Győződjön meg arról, hogy a modulban lévő összes fájl elérési útja 140 karakternél rövidebb. A 140 karakternél hosszabb elérési utak problémákat okoznak a runbookok importálása során. Az Automation nem tud 140 karakternél hosszabb elérési útú fájlokat importálni a PowerShell-munkamenetbe a `Import-Module` következővel: .

## <a name="import-modules"></a>Modulok importálása

Ez a szakasz több módszert is meghatároz, amelyek segítségével modulokat importálhat Az Automation-fiókjába.

### <a name="import-modules-in-the-azure-portal"></a>Modulok importálása a Azure Portal

Modul importálása a Azure Portal:

1. Ugrás az Automation-fiókra.
2. A **Megosztott erőforrások alatt válassza** a Modulok **lehetőséget.**
3. Válassza **a Modul hozzáadása lehetőséget.**
4. Válassza ki **a modult** tartalmazó .zip fájlt.
5. Kattintson **az OK** gombra a folyamat importálásának elkezdődjön.

### <a name="import-modules-by-using-powershell"></a>Modulok importálása a PowerShell használatával

A [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) parancsmag használatával importálhat egy modult az Automation-fiókjába. A parancsmag egy modul .zip-csomagjára vonatkozó URL-címet vesz fel.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Ugyanezt a parancsmagot használhatja arra is, hogy közvetlenül importálja a modult PowerShell-galéria modulból. Ügyeljen arra, hogy a és `ModuleName` `ModuleVersion` a et a [PowerShell-galéria.](https://www.powershellgallery.com)

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Modulok importálása a PowerShell-galéria

A modulokat [PowerShell-galéria](https://www.powershellgallery.com) a katalógusból vagy az Automation-fiókjából importálhatja.

Modul importálása közvetlenül a PowerShell-galéria:

1. Keresse meg a modult az elemhez https://www.powershellgallery.com az importáláshoz.
2. A **Telepítési beállítások lapon** a **Azure Automation** válassza a Központi telepítés **a Azure Automation.** Ez a művelet megnyitja a Azure Portal. 
3. Az Importálás lapon válassza ki Automation-fiókját, majd kattintson az **OK gombra.**

![Az importálási PowerShell-galéria képernyőképe](../media/modules/powershell-gallery.png)

Ha közvetlenül az Automation-PowerShell-galéria importálni egy modult:

1. A **Megosztott erőforrások alatt válassza** a Modulok **lehetőséget.** 
2. Válassza **a Tallózás a katalógusban** lehetőséget, majd keresse meg a katalógusban a modult. 
3. Válassza ki az importálni kívánt modult, majd válassza az **Importálás lehetőséget.** 
4. Az **importálási folyamat** elkezdődjön az OK gombra.

![Képernyőkép egy PowerShell-galéria modul importálásának Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Modulok törlése

Ha problémája van egy modullal, vagy vissza kell tért a modul egy korábbi verziójára, törölheti azt az Automation-fiókjából. Az Automation-fiók létrehozásakor importált alapértelmezett [](#default-modules) modulok eredeti verziói nem törölhetők. Ha a törölni kívánt modul az egyik alapértelmezett modul újabb [verziója,](#default-modules)akkor az az Automation-fiókkal együtt telepített verzióra áll vissza. Ellenkező esetben az Automation-fiókból törölt modulok törlődnek.

### <a name="delete-modules-in-the-azure-portal"></a>Modulok törlése a Azure Portal

Modul eltávolítása a Azure Portal:

1. Ugrás az Automation-fiókra. A **Megosztott erőforrások alatt válassza** a Modulok **lehetőséget.**
2. Válassza ki az eltávolítani kívánt modult.
3. A Modul lapon válassza a **Törlés lehetőséget.** Ha ez a modul az egyik alapértelmezett [modul,](#default-modules)akkor az Automation-fiók létrehozásakor meglévő verzióra áll vissza.

### <a name="delete-modules-by-using-powershell"></a>Modulok törlése a PowerShell használatával

A modul PowerShellen keresztüli eltávolításához futtassa a következő parancsot:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Következő lépések

* A modulokkal kapcsolatos további Azure PowerShell lásd: [Első lépések a Azure PowerShell.](/powershell/azure/get-started-azureps)

* További információ a PowerShell-modulok létrehozásáról: [Writing a Windows PowerShell module](/powershell/scripting/developer/module/writing-a-windows-powershell-module)..
