---
title: Vendégkonfigurációs szabályzatok létrehozása Windows rendszeren
description: Megtudhatja, hogyan hozhat létre Azure Policy vendég-konfigurációs házirendet a Windows rendszerhez.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 4ee0c9d1912338235e53eb287bfc86a14b75cc97
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547664"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Vendégkonfigurációs szabályzatok létrehozása Windows rendszeren

Az egyéni házirend-definíciók létrehozása előtt érdemes beolvasni a koncepcionális áttekintési információkat az oldalon [Azure Policy vendég konfigurációjában](../concepts/guest-configuration.md).
 
A Linux rendszerhez készült vendég-konfigurációs szabályzatok létrehozásával kapcsolatos további tudnivalókért tekintse meg a [Linux rendszerhez készült vendég-konfigurációs szabályzatok létrehozása](./guest-configuration-create-linux.md) című oldalt.

A Windows naplózásakor a Vendégkonfiguráció egy [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) erőforrásmodul használatával hozza létre a konfigurációs fájlt. A DSC-konfiguráció határozza meg a gép kívánt állapotát. Ha a konfiguráció kiértékelése meghiúsul, a rendszer elindítja a **auditIfNotExists** , és a gép **nem megfelelőnek**minősül.

[Azure Policy vendég konfiguráció](../concepts/guest-configuration.md) csak a gépeken belüli beállítások naplózására használható. A gépeken belüli beállítások szervizelése még nem érhető el.

A következő műveletek végrehajtásával hozhatja létre saját konfigurációját egy Azure-beli vagy nem Azure-beli gép állapotának ellenőrzéséhez.

> [!IMPORTANT]
> A vendég-konfigurációval rendelkező egyéni házirendek előzetes verziójú funkciók.
>
> A naplózás Azure-beli virtuális gépeken történő végrehajtásához szükség van a Vendégkonfiguráció bővítményre.
> Ha a bővítményt az összes Windows rendszerű gépen szeretné üzembe helyezni, rendelje hozzá a következő szabályzat-definíciókat:
>   - [Telepítse az előfeltételeket, hogy engedélyezze a vendég-konfigurációs házirendet a Windows rendszerű virtuális gépeken.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

## <a name="install-the-powershell-module"></a>A PowerShell-modul telepítése

A vendég konfigurációs modul automatizálja az egyéni tartalom létrehozásának folyamatát, többek között a következőket:

- Vendég konfigurációs tartalom (. zip) létrehozása
- Az összetevő automatizált tesztelése
- Házirend-definíció létrehozása
- A szabályzat közzététele

A modul Windows, macOS vagy Linux rendszerű gépen telepíthető, amely a PowerShell 6,2-as vagy újabb verzióját futtatja helyileg, vagy [Azure Cloud Shell](https://shell.azure.com)vagy a [Azure PowerShell Core Docker-rendszerkép](https://hub.docker.com/r/azuresdk/azure-powershell-core)használatával.

> [!NOTE]
> A konfigurációk fordítása még nem támogatott Linux rendszeren.

### <a name="base-requirements"></a>Alapszintű követelmények

Azok az operációs rendszerek, amelyeken telepítve van a modul:

- Linux
- macOS
- Windows

A vendég konfigurációs erőforrás-modulhoz a következő szoftverek szükségesek:

- PowerShell 6,2 vagy újabb. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 vagy újabb. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/azure/install-az-ps).
  - Csak az az modulok "az. accounts" és az "az. Resources" megadása szükséges.

### <a name="install-the-module"></a>A modul telepítése

A **GuestConfiguration** modul telepítése a PowerShellben:

1. A PowerShell-parancssorból futtassa a következő parancsot:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Annak ellenőrzése, hogy a modul importálása megtörtént-e:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Vendég konfigurációs összetevők és szabályzat Windows rendszerhez

A vendég konfigurációja a PowerShell kívánt állapotának konfigurációját használja nyelvi absztrakcióként a Windows rendszerbe való naplózással kapcsolatos íráshoz. Az ügynök betölti a PowerShell 6,2 önálló példányát, így a PowerShell DSC használata nem ütközik a Windows PowerShell 5,1-ben, és nincs szükség a PowerShell 6,2 vagy újabb verziójának előzetes telepítésére.

A DSC-fogalmak és a terminológia áttekintését lásd: a [POWERSHELL DSC áttekintése](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>A vendég konfigurációs moduljai eltérnek a Windows PowerShell DSC moduljaitól

Ha a vendég konfigurációja naplóz egy gépet, az események sorozatából különböznek, mint a Windows PowerShell DSC-ben.

1. Az ügynök először futtatja `Test-TargetResource` annak megállapítására, hogy a konfiguráció helyes állapotban van-e.
1. A függvény által visszaadott logikai érték határozza meg, hogy a vendég-hozzárendelés Azure Resource Manager állapotának megfelelőnek vagy nem megfelelőnek kell lennie.
1. A szolgáltató `Get-TargetResource` úgy fut, hogy az egyes beállítások aktuális állapotát adja vissza, így a részletek mind arról szólnak, hogy a gép miért nem megfelelő, és hogy a jelenlegi állapot megfelelő-e.

Az Azure Policyban található paramétereknek _karakterlánc_ típusúnak kell lenniük a vendég konfigurációs hozzárendeléseinek. Nem lehet paramétereken keresztül átadni a tömböket, még akkor is, ha a DSC-erőforrás támogatja a tömböket.

### <a name="get-targetresource-requirements"></a>A Get-TargetResource követelményei

A függvény `Get-TargetResource` speciális követelményekkel rendelkezik a Windows kívánt állapotának konfigurálásához nem szükséges vendég-konfigurációhoz.

- A visszaadott szórótábla tartalmaznia kell **egy nevű**tulajdonságot.
- A Reason tulajdonságnak tömbnek kell lennie.
- A tömb minden elemének olyan szórótábla kell lennie, amelynek **kóddal** és **kifejezéssel**ellátott kulcsokkal kell rendelkeznie.

A szolgáltatás az okok tulajdonságát arra használja, hogy egységesítse, hogyan jelennek meg az információk, amikor egy gép nem felel meg az előírásoknak. Az egyes elemek oka az lehet, hogy az erőforrás nem megfelelő. A tulajdonság egy tömb, mert egy erőforrás több okból nem felel meg a megfelelőségnek.

A szolgáltatás a tulajdonságok **kódját** és a **kifejezést** is elvárta. Egyéni erőforrás létrehozásakor állítsa be azt a szöveget (jellemzően StdOut), amelyet az erőforrás nem felel meg a **kifejezés**értékének. A **kód** meghatározott formázási követelményekkel rendelkezik, így a jelentéskészítés egyértelműen megjelenítheti az erőforrással kapcsolatos információkat a naplózás végrehajtásához. Ez a megoldás a vendég konfigurációját bővíthetővé teszi. Bármely parancs futtatható, ha a kimenet karakterlánc-értékként adható vissza a **kifejezés** tulajdonsághoz.

- **Code** (string) (karakterlánc): az erőforrás neve, ismétlődő, majd egy rövid név, amely nem tartalmazhat szóközt azonosítóként az ok miatt. Ez a három érték csak kettősponttal tagolható szóközök nélkül.
  - Példa erre `registry:registry:keynotpresent`
- **Kifejezés** (karakterlánc): ember által olvasható szöveg, amely elmagyarázza, hogy a beállítás miért nem megfelelő.
  - Példa erre `The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

Az okok tulajdonságot beágyazott osztályként hozzá kell adni az erőforráshoz tartozó MOF-sémához.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>Konfigurációs követelmények

Az egyéni konfiguráció nevének mindenütt konzisztensnek kell lennie. A Content csomag. zip fájljának nevét, a MOF-fájlban található konfiguráció nevét, valamint a Azure Resource Manager sablonban (ARM-sablon) lévő vendég-hozzárendelés nevét meg kell egyeznie.

### <a name="scaffolding-a-guest-configuration-project"></a>Vendég konfigurációs projekt állványzata

Azok a fejlesztők, akik szeretnék felgyorsítani az első lépéseket, és a mintakód használatával dolgozhatnak egy, a **Guest Configuration Project**nevű közösségi projektben. A projekt egy sablont telepít a [vakolat](https://github.com/powershell/plaster) PowerShell-modulhoz. Ezzel az eszközzel a projekt beépíthető, beleértve a munkakonfigurációt és a minta erőforrást, valamint a projekt ellenőrzéséhez szükséges, a tevékenység ellenőrzésére szolgáló feldolgozatlan [teszteket](https://github.com/pester/pester) . A sablon a Visual Studio Code feladatait is tartalmazza, amelyekkel automatizálható a vendég-konfigurációs csomag kiépítése és érvényesítése. További információ: GitHub Project [Guest Configuration Project](https://github.com/microsoft/guestconfigurationproject).

A konfigurációk általános használatáról további információt a [konfiguráció írása, fordítása és alkalmazása](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)című témakörben talál.

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Vendég konfigurációs összetevő várt tartalma

A kitöltött csomagot a vendég konfigurációja használja a Azure Policy definíciók létrehozásához. A csomag a következőkből áll:

- A lefordított DSC-konfiguráció MOF-ként
- Modulok mappa
  - GuestConfiguration modul
  - DscNativeResources modul
  - Windows A MOF által igényelt DSC-erőforrás-modulok

A PowerShell-parancsmagok segítséget nyújtanak a csomag létrehozásában.
Nincs szükség gyökér szintű mappára vagy a Version mappára.
A csomag formátumának. zip formátumúnak kell lennie.

### <a name="storing-guest-configuration-artifacts"></a>Vendég konfigurációs összetevők tárolása

A. zip-csomagot a felügyelt virtuális gépek által elérhető helyen kell tárolni.
Ilyenek például a GitHub-adattárak, az Azure-Tárházak vagy az Azure Storage. Ha nem szeretné, hogy a csomag nyilvános legyen, az URL-címben egy [sas-tokent](../../../storage/common/storage-sas-overview.md) is hozzáadhat. A magánhálózati számítógépekhez [szolgáltatási végpontot](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) is alkalmazhat, bár ez a konfiguráció csak a csomag elérésére és a szolgáltatással való kommunikációra vonatkozik.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Lépésről lépésre, egyéni vendég-konfiguráció naplózási szabályzatának létrehozása Windows rendszerhez

A beállítások naplózásához hozzon létre egy DSC-konfigurációt. A következő PowerShell-parancsfájl egy **AuditBitLocker**nevű konfigurációt hoz létre, importálja a **PsDscResources** erőforrás-modult, és az `Service` erőforrás használatával naplózza a futó szolgáltatást. A konfigurációs parancsfájl futtatható Windows vagy macOS rendszerű gépről.

```powershell
# Add PSDscResources module to environment
Install-Module 'PSDscResources'

# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker ./Config
```

Mentse ezt a fájlt `config.ps1` a Project mappában található néven. Futtassa a PowerShellben a terminálon történő végrehajtással `./config.ps1` . Létrejön egy új MOF-fájl.

A `Node AuditBitlocker` parancs nem szükséges technikailag, de az alapértelmezett helyett egy nevű fájlt hoz létre `AuditBitlocker.mof` `localhost.mof` . Ha a. MOF-fájlnevet követi, a konfiguráció megkönnyíti a sok fájl rendszerezését nagy léptékű működés esetén.

A MOF lefordítása után a támogató fájlokat össze kell csomagolni. A kitöltött csomagot a vendég konfigurációja használja a Azure Policy definíciók létrehozásához.

A `New-GuestConfigurationPackage` parancsmag létrehozza a csomagot. A konfigurációhoz szükséges moduloknak elérhetőnek kell lenniük a-ben `$Env:PSModulePath` . A parancsmag paraméterei `New-GuestConfigurationPackage` Windows-tartalom létrehozásakor:

- **Name**: vendég konfigurációs csomag neve.
- **Konfiguráció**: LEfordított DSC-konfigurációs dokumentum teljes elérési útja.
- **Elérési út**: kimeneti mappa elérési útja. Ezt a paramétert nem kötelező megadni. Ha nincs megadva, a csomag az aktuális könyvtárban jön létre.

A következő parancs futtatásával hozzon létre egy csomagot az előző lépésben megadott konfiguráció használatával:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

A konfigurációs csomag létrehozása után, de az Azure-ba való közzététel előtt tesztelheti a csomagot a munkaállomás vagy CI/CD-környezetből. A GuestConfiguration parancsmag `Test-GuestConfigurationPackage` ugyanazt az ügynököt tartalmazza a fejlesztői környezetben, mint amelyet az Azure-gépeken belül használ. Ezzel a megoldással helyileg végezheti el az integrációs tesztelést, mielőtt kiadná a számlázott felhőalapú környezeteket.

Mivel az ügynök ténylegesen kiértékeli a helyi környezetet, a legtöbb esetben a test-parancsmagot ugyanarra az operációsrendszer-platformra kell futtatnia, mint a naplózást. A teszt csak a tartalmi csomagban található modulokat használja.

A parancsmag paraméterei `Test-GuestConfigurationPackage` :

- **Név**: a vendég konfigurációs szabályzatának neve.
- **Paraméter**: szórótábla formátumban megadott házirend-paraméterek.
- **Elérési út**: a vendég konfigurációs csomag teljes elérési útja.

Futtassa a következő parancsot az előző lépésben létrehozott csomag teszteléséhez:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

A parancsmag a PowerShell-folyamatból is támogatja a bemenetet. A parancsmag kimenetének átadása `New-GuestConfigurationPackage` a `Test-GuestConfigurationPackage` parancsmagnak.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

A következő lépés a fájl közzététele a blob Storage-ban. Az alábbi szkript a feladat automatizálásához használható függvényt tartalmaz. A függvényben használt parancsokhoz a `publish` `Az.Storage` modul szükséges.

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

Miután létrehozta és feltöltötte a vendég konfigurációhoz tartozó egyéni házirend-csomagot, hozza létre a vendég-konfigurációs házirend definícióját. A `New-GuestConfigurationPolicy` parancsmag egy egyéni házirend-csomagot vesz fel, és létrehoz egy házirend-definíciót.

A parancsmag paraméterei `New-GuestConfigurationPolicy` :

- **ContentUri**: nyilvános http (s) URI a vendég konfigurációs tartalomkezelő csomaghoz.
- **DisplayName**: házirend megjelenítendő neve.
- **Leírás**: szabályzat leírása.
- **Paraméter**: szórótábla formátumban megadott házirend-paraméterek.
- **Verzió**: szabályzat verziója.
- **Elérési út**: a célhely elérési útja, ahol a szabályzat-definíciók létrejönnek
- **Platform**: cél platform (Windows/Linux) a vendég konfigurációs házirendhez és a tartalmi csomaghoz.
- A **címke** egy vagy több címkét rendel hozzá a szabályzat-definícióhoz
- **Kategória** – a szabályzat-definícióban a kategória metaadatainak mezőjének beállítása

A következő példa egy egyéni házirend-csomagból egy megadott elérési úton hozza létre a házirend-definíciókat:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

A következő fájlokat hozza létre `New-GuestConfigurationPolicy` :

- **auditIfNotExists.jsbekapcsolva**
- **deployIfNotExists.jsbekapcsolva**
- **Initiative.jsbekapcsolva**

A parancsmag kimenete egy olyan objektumot ad vissza, amely a házirend-fájlok kezdeményezésének megjelenítendő nevét és elérési útját tartalmazza.

Végül tegye közzé a szabályzat-definíciókat a `Publish-GuestConfigurationPolicy` parancsmag használatával. A parancsmag csak a **path** paraméterrel rendelkezik, amely a által létrehozott JSON-fájlok helyére mutat `New-GuestConfigurationPolicy` .

A közzétételi parancs futtatásához hozzá kell férnie a szabályzatok létrehozásához az Azure-ban. A konkrét engedélyezési követelmények dokumentálva vannak a [Azure Policy áttekintés](../overview.md) oldalon. A legjobb beépített szerepkör az erőforrás- **házirend közreműködője**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

A `Publish-GuestConfigurationPolicy` parancsmag fogadja az elérési utat a PowerShell-folyamatból. Ez a funkció azt jelenti, hogy létrehozhatja a házirend-fájlokat, és közzéteheti őket egyetlen vezetékes parancsban.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Az Azure-ban létrehozott szabályzattal az utolsó lépés a kezdeményezés kiosztása. Lásd: a kezdeményezés társítása a [portál](../assign-policy-portal.md), az [Azure CLI](../assign-policy-azurecli.md)és a [Azure PowerShell](../assign-policy-powershell.md)használatával.

> [!IMPORTANT]
> A _AuditIfNotExists_ -és _DeployIfNotExists_ -házirendeket egyesítő kezdeményezéssel **mindig** hozzá kell rendelni a vendég-konfigurációs házirendeket. Ha csak a _AuditIfNotExists_ szabályzat van hozzárendelve, az előfeltételek nincsenek telepítve, és a házirend mindig azt mutatja, hogy a "0" kiszolgálók megfelelőek.

Egy szabályzat-definíció _DeployIfNotExists_ -effektussal való hozzárendeléséhez további hozzáférési szint szükséges. A legalacsonyabb jogosultság megadásához létrehozhat egy egyéni szerepkör-definíciót, amely kibővíti az **erőforrás-házirend közreműködőjét**. Az alábbi példa létrehoz egy erőforrás- **házirend közreműködője** nevű szerepkört a _Microsoft. Authorization/roleAssignments/Write_további engedélyeivel.

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="filtering-guest-configuration-policies-using-tags"></a>Vendég konfigurációs szabályzatok szűrése címkék használatával

A parancsmagok által a vendég konfigurációs modulban létrehozott szabályzat-definíciók tartalmazhatják a címkék szűrőit is. A **tag** paraméter a `New-GuestConfigurationPolicy` támogatja az egyéni címkéket tartalmazó szórótáblában tömbjét. A címkék hozzáadódnak a `If` házirend-definíció szakaszához, és nem módosíthatók házirend-hozzárendeléssel.

A következő példa egy olyan szabályzat-definíció kódrészletét adja meg, amely a címkékre vonatkozó szűrőket mutatja.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content
    }
  ]
}
```

### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>Paraméterek használata az egyéni vendég konfigurációs házirend-definíciókban

A vendég konfiguráció futási időben támogatja a konfiguráció felülírási tulajdonságait. Ez a funkció azt jelenti, hogy a csomagban lévő MOF-fájlban lévő értékeket nem kell statikusnak tekinteni. A felülbírálási értékek a Azure Policyon keresztül érhetők el, és nem befolyásolják a konfigurációk létrehozási vagy fordítási módját.

A parancsmagok `New-GuestConfigurationPolicy` `Test-GuestConfigurationPolicyPackage` tartalmazzák a **paraméter**nevű paramétert. Ez a paraméter egy szórótábla-definíciót vesz fel, amely tartalmazza az egyes paraméterek részleteit, és létrehozza a Azure Policy-definícióhoz használt minden fájl szükséges részeit.

Az alábbi példa egy szabályzat-definíciót hoz létre egy szolgáltatás naplózásához, ahol a felhasználó a szabályzat-hozzárendelés időpontjában kiválasztja a listából.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="extending-guest-configuration-with-third-party-tools"></a>A vendég konfiguráció kiterjesztése külső gyártótól származó eszközökkel

A vendég konfigurációhoz tartozó összetevő-csomagok kiterjeszthetők a külső gyártótól származó eszközökre is.
A vendég konfiguráció kibővítéséhez két összetevő fejlesztésére van szükség.

- A harmadik féltől származó eszköz kezelésével kapcsolatos összes tevékenységet kezelő kívánt állapot-konfigurációs erőforrás
  - Telepítés
  - Invoke
  - Kimenet konvertálása
- Az eszköz natív felhasználásának megfelelő formátumú tartalma

A DSC-erőforráshoz egyéni fejlesztés szükséges, ha még nem létezik közösségi megoldás.
A közösségi megoldások felderíthető, ha megkeresi a [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22)címke PowerShell-Galéria.

> [!Note]
> A vendég konfiguráció bővíthetősége a "saját licenc használata" forgatókönyv. A használat előtt győződjön meg arról, hogy a harmadik féltől származó eszközök használati feltételei teljesülnek.

Miután telepítette a DSC-erőforrást a fejlesztői környezetbe, a **FilesToInclude** paraméterrel beillesztheti a tartalmat a `New-GuestConfigurationPackage` harmadik féltől származó platformra a tartalmi összetevőben.

### <a name="step-by-step-creating-a-content-artifact-that-uses-third-party-tools"></a>Lépésről lépésre, külső gyártótól származó eszközöket használó tartalmi összetevő létrehozása

Csak a `New-GuestConfigurationPackage` parancsmag szükséges a DSC-tartalom összetevőinek lépésenkénti útmutatójában. Ebben a példában a modul használatával `gcInSpec` kiterjesztheti a vendég konfigurációját a Windows rendszerű gépek naplózására a inspec platform használatával, nem pedig a Linuxon használt beépített modul helyett. A közösségi modul [nyílt forráskódú projektként van megőrizve a githubban](https://github.com/microsoft/gcinspec).

Telepítse a szükséges modulokat a fejlesztési környezetben:

```azurepowershell-interactive
# Update PowerShellGet if needed to allow installing PreRelease versions of modules
Install-Module PowerShellGet -Force

# Install GuestConfiguration module prerelease version
Install-Module GuestConfiguration -allowprerelease

# Install commmunity supported gcInSpec module
Install-Module gcInSpec
```

Először hozza létre az inspec által használt YaML-fájlt. A fájl alapvető információkat biztosít a környezetről. Alább látható egy példa:

```YaML
name: wmi_service
title: Verify WMI service is running
maintainer: Microsoft Corporation
summary: Validates that the Windows Service 'winmgmt' is running
copyright: Microsoft Corporation
license: MIT
version: 1.0.0
supports:
  - os-family: windows
```

Mentse a nevű fájlt `wmi_service.yml` egy nevű mappában a `wmi_service` projekt könyvtárába.

Ezután hozza létre a Ruby-fájlt a számítógép naplózásához használt inspec nyelvi absztrakcióval.

```Ruby
control 'wmi_service' do
  impact 1.0
  title 'Verify windows service: winmgmt'
  desc 'Validates that the service, is installed, enabled, and running'

  describe service('winmgmt') do
    it { should be_installed }
    it { should be_enabled }
    it { should be_running }
  end
end

```

Mentse ezt a fájlt `wmi_service.rb` egy nevű új mappába `controls` a `wmi_service` címtárban.

Végül hozzon létre egy konfigurációt, importálja a **GuestConfiguration** erőforrás-modult, és használja az `gcInSpec` erőforrást az inspec-profil nevének megadásához.

```powershell
# Define the configuration and import GuestConfiguration
Configuration wmi_service
{
    Import-DSCResource -Module @{ModuleName = 'gcInSpec'; ModuleVersion = '2.1.0'}
    node 'wmi_service'
    {
        gcInSpec wmi_service
        {
            InSpecProfileName       = 'wmi_service'
            InSpecVersion           = '3.9.3'
            WindowsServerVersion    = '2016'
        }
    }
}

# Compile the configuration to create the MOF files
wmi_service -out ./Config
```

Most már az alábbiak szerint kell lennie egy projekt struktúrájának:

```file
/ wmi_service
    / Config
        wmi_service.mof
    / wmi_service
        wmi_service.yml
        / controls
            wmi_service.rb 
```

A támogató fájlokat össze kell csomagolni. A kitöltött csomagot a vendég konfigurációja használja a Azure Policy definíciók létrehozásához.

A `New-GuestConfigurationPackage` parancsmag létrehozza a csomagot. Harmadik féltől származó tartalom esetén használja a **FilesToInclude** paramétert a csomaghoz tartozó inspec-tartalom hozzáadásához. Nem kell megadnia a **ChefProfilePath** a Linux-csomagokhoz.

- **Name**: vendég konfigurációs csomag neve.
- **Konfiguráció**: a lefordított konfigurációs dokumentum teljes elérési útja.
- **Elérési út**: kimeneti mappa elérési útja. Ezt a paramétert nem kötelező megadni. Ha nincs megadva, a csomag az aktuális könyvtárban jön létre.
- **FilesoInclude**: az inspec-profil teljes elérési útja.

A következő parancs futtatásával hozzon létre egy csomagot az előző lépésben megadott konfiguráció használatával:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'wmi_service' `
  -Configuration './Config/wmi_service.mof' `
  -FilesToInclude './wmi_service'  `
  -Path './package' 
```

## <a name="policy-lifecycle"></a>Szabályzat életciklusa

Ha a szabályzat frissítését szeretné kibocsátani, két, figyelmet igénylő mező szükséges.

- **Verzió**: a parancsmag futtatásakor meg `New-GuestConfigurationPolicy` kell adnia a jelenleg közzétett verziónál nagyobb verziószámot. A tulajdonság frissíti a vendég konfiguráció-hozzárendelés verzióját, hogy az ügynök felismeri a frissített csomagot.
- **contentHash**: ezt a tulajdonságot a parancsmag automatikusan frissíti `New-GuestConfigurationPolicy` . Ez a csomag által létrehozott kivonatoló érték `New-GuestConfigurationPackage` . A tulajdonságnak megfelelőnek kell lennie a `.zip` közzétett fájlhoz. Ha csak a **contentUri** tulajdonság frissül, a bővítmény nem fogadja el a csomag tartalmát.

Egy frissített csomag kiadásának legegyszerűbb módja, ha megismétli a jelen cikkben ismertetett folyamatot, és megadja a verziószámot. Ez a folyamat garantálja az összes tulajdonság megfelelő frissítését.

## <a name="optional-signing-guest-configuration-packages"></a>Nem kötelező: a vendég konfigurációs csomagjainak aláírása

A vendég konfigurációja egyéni házirendek SHA256 kivonatot használnak a házirend-csomag érvényesítéséhez.
Igény szerint az ügyfelek is használhatnak tanúsítványokat a csomagok aláírására és a vendég konfigurációs bővítmény kényszerítésére, hogy csak az aláírt tartalmat engedélyezzék.

Ennek a forgatókönyvnek az engedélyezéséhez két lépést kell végrehajtania. Futtassa a parancsmagot a tartalomindex aláírásához, és fűzze hozzá a címkét azokhoz a gépekhez, amelyeknek az aláírásához kód szükséges.

Az aláírás-ellenőrzési funkció használatához futtassa a `Protect-GuestConfigurationPackage` parancsmagot a csomag aláírásához a közzététel előtt. Ehhez a parancsmaghoz "kód aláírása" tanúsítvány szükséges.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

A parancsmag paraméterei `Protect-GuestConfigurationPackage` :

- **Elérési út**: a vendég konfigurációs csomag teljes elérési útja.
- **Tanúsítvány**: kód aláírására szolgáló tanúsítvány a csomag aláírásához. Ez a paraméter csak Windows-tartalmak aláírása esetén támogatott.

A GuestConfiguration-ügynök elvárja, hogy a tanúsítvány nyilvános kulcsa a Windows rendszerű gépeken és a Linux-gépek elérési útjában legyen jelen a "megbízható legfelső szintű hitelesítésszolgáltatók" szolgáltatásban `/usr/local/share/ca-certificates/extra` . Ahhoz, hogy a csomópont ellenőrizze az aláírt tartalmat, az egyéni házirend alkalmazása előtt telepítse a tanúsítvány nyilvános kulcsát a gépre. Ez a folyamat a virtuális gépen belüli bármely technikával vagy Azure Policy használatával végezhető el. [Itt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)található egy példa erre a sablonra.
Az Key Vault hozzáférési szabályzatnak lehetővé kell tennie a számítási erőforrás-szolgáltató számára a tanúsítványok elérését a központi telepítések során. A részletes lépésekért lásd: [Key Vault beállítása virtuális gépekhez a Azure Resource Managerban](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Az alábbi példa a nyilvános kulcs exportálását írja elő egy aláíró tanúsítványból a gépre való importáláshoz.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

A tartalom közzététele után fűzze hozzá a nevet és az értéket tartalmazó címkét az `GuestConfigPolicyCertificateValidation` `enabled` összes olyan virtuális géphez, amelynél szükség van a kód aláírására. Tekintse meg azokat a [mintákat](../samples/built-in-policies.md#tags) , amelyekkel a címkék a Azure Policy használatával méretezhetők. A címke betartása után a parancsmag használatával generált szabályzat-definíció `New-GuestConfigurationPolicy` engedélyezi a követelményt a vendég konfigurációs bővítményen keresztül.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>A vendég-konfigurációs házirend hozzárendeléseinek hibaelhárítása (előzetes verzió)

Egy eszköz előzetes verzióban érhető el, amely segítséget nyújt az Azure Policy vendég konfigurációs hozzárendeléseinek hibaelhárításában. Az eszköz előzetes verzióban érhető el, és közzé lett téve a PowerShell-galéria mint modul neve [vendég konfigurációs hibakereső](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Az eszköz parancsmagokkal kapcsolatos további információkért használja a PowerShell Get-Help parancsát a beépített útmutatás megjelenítéséhez. Mivel az eszköz gyakori frissítéseket kap, ez a legjobb módszer a legfrissebb információk beszerzésére.

## <a name="next-steps"></a>További lépések

- Tudnivalók a virtuális gépek a [vendég konfigurációjával](../concepts/guest-configuration.md)való naplózásáról.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](./programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](./get-compliance-data.md).
