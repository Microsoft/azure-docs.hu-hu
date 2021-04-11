---
title: Vendégkonfigurációs szabályzatok létrehozása Windows rendszeren
description: Megtudhatja, hogyan hozhat létre Azure Policy vendég-konfigurációs házirendet a Windows rendszerhez.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 72772743eba23ea7c2a93f5037ac84b671256a66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104887699"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Vendégkonfigurációs szabályzatok létrehozása Windows rendszeren

Az egyéni házirend-definíciók létrehozása előtt érdemes beolvasni a koncepcionális áttekintési információkat az oldalon [Azure Policy vendég konfigurációjában](../concepts/guest-configuration.md).
 
A Linux rendszerhez készült vendég-konfigurációs szabályzatok létrehozásával kapcsolatos további tudnivalókért tekintse meg a [Linux rendszerhez készült vendég-konfigurációs szabályzatok létrehozása](./guest-configuration-create-linux.md) című oldalt.

A Windows naplózásakor a Vendégkonfiguráció egy [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) erőforrásmodul használatával hozza létre a konfigurációs fájlt. A DSC-konfiguráció határozza meg a gép kívánt állapotát. Ha a konfiguráció kiértékelése meghiúsul, a rendszer elindítja a **auditIfNotExists** , és a gép **nem megfelelőnek** minősül.

[Azure Policy vendég konfiguráció](../concepts/guest-configuration.md) csak a gépeken belüli beállítások naplózására használható. A gépeken belüli beállítások szervizelése még nem érhető el.

A következő műveletek végrehajtásával hozhatja létre saját konfigurációját egy Azure-beli vagy nem Azure-beli gép állapotának ellenőrzéséhez.

> [!IMPORTANT]
> A Azure Government és az Azure China Environments szolgáltatásban a vendég-konfigurációval rendelkező egyéni házirend-definíciók előzetes verzióként használhatók.
>
> A naplózás Azure-beli virtuális gépeken történő végrehajtásához szükség van a Vendégkonfiguráció bővítményre.
> Ha a bővítményt az összes Windows rendszerű gépen szeretné üzembe helyezni, rendelje hozzá a következő szabályzat-definíciókat: `Deploy prerequisites to enable Guest Configuration Policy on Windows VMs`
> 
> Ne használja a titkokat vagy a bizalmas információkat az egyéni tartalom csomagjaiban.

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

### <a name="get-targetresource-requirements"></a>Get-TargetResource követelmények

A függvény `Get-TargetResource` speciális követelményekkel rendelkezik a Windows kívánt állapotának konfigurálásához nem szükséges vendég-konfigurációhoz.

- A visszaadott szórótábla tartalmaznia kell **egy nevű** tulajdonságot.
- A Reason tulajdonságnak tömbnek kell lennie.
- A tömb minden elemének olyan szórótábla kell lennie, amelynek **kóddal** és **kifejezéssel** ellátott kulcsokkal kell rendelkeznie.

A szolgáltatás az okok tulajdonságát arra használja, hogy egységesítse, hogyan jelennek meg az információk, amikor egy gép nem felel meg az előírásoknak. Az egyes elemek oka az lehet, hogy az erőforrás nem megfelelő. A tulajdonság egy tömb, mert egy erőforrás több okból nem felel meg a megfelelőségnek.

A szolgáltatás a tulajdonságok **kódját** és a **kifejezést** is elvárta. Egyéni erőforrás létrehozásakor állítsa be azt a szöveget (jellemzően StdOut), amelyet az erőforrás nem felel meg a **kifejezés** értékének. A **kód** meghatározott formázási követelményekkel rendelkezik, így a jelentéskészítés egyértelműen megjelenítheti az erőforrással kapcsolatos információkat a naplózás végrehajtásához. Ez a megoldás a vendég konfigurációját bővíthetővé teszi. Bármely parancs futtatható, ha a kimenet karakterlánc-értékként adható vissza a **kifejezés** tulajdonsághoz.

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

Ha az erőforráshoz szükséges tulajdonságok vannak, akkor azokat `Get-TargetResource` a osztállyal párhuzamosan is vissza kell adni `reasons` . Ha `reasons` nem tartalmazza a szolgáltatást, a szolgáltatás tartalmazza a "catch-all" viselkedést, amely összehasonlítja a bemeneteket `Get-TargetResource` és a által visszaadott értékeket `Get-TargetResource` , és részletes összehasonlítást biztosít a következővel: `reasons` .

### <a name="configuration-requirements"></a>Konfigurációs követelmények

Az egyéni konfiguráció nevének mindenütt konzisztensnek kell lennie. A Content csomag. zip fájljának nevét, a MOF-fájlban található konfiguráció nevét, valamint a Azure Resource Manager sablonban (ARM-sablon) lévő vendég-hozzárendelés nevét meg kell egyeznie.

### <a name="policy-requirements"></a>Házirend-követelmények

A házirend `metadata` -definíció szakasznak tartalmaznia kell két tulajdonságot a vendég konfigurációs szolgáltatás számára a vendég-konfigurációs hozzárendelések kiosztásának és jelentéskészítésének automatizálásához. A `category` tulajdonságot "Guest Configuration" értékre kell beállítani, és a nevű szakasznak `Guest Configuration` tartalmaznia kell a vendég-konfiguráció hozzárendelésével kapcsolatos információkat. A `New-GuestConfigurationPolicy` parancsmag automatikusan létrehozza ezt a szöveget.
Tekintse meg az oldalon található lépésenkénti útmutatót.

Az alábbi példa bemutatja a `metadata` szakaszt.

```json
    "metadata": {
      "category": "Guest Configuration",
      "guestConfiguration": {
        "name": "test",
        "version": "1.0.0",
        "contentType": "Custom",
        "contentUri": "CUSTOM-URI-HERE",
        "contentHash": "CUSTOM-HASH-VALUE-HERE",
        "configurationParameter": {}
      }
    },
```

### <a name="scaffolding-a-guest-configuration-project"></a>Vendég konfigurációs projekt állványzata

Azok a fejlesztők, akik szeretnék felgyorsítani az első lépéseket, és a mintakód használatával dolgozhatnak egy, a **Guest Configuration Project** nevű közösségi projektben. A projekt egy sablont telepít a [vakolat](https://github.com/powershell/plaster) PowerShell-modulhoz. Ezzel az eszközzel a projekt beépíthető, beleértve a munkakonfigurációt és a minta erőforrást, valamint a projekt ellenőrzéséhez szükséges, a tevékenység ellenőrzésére szolgáló feldolgozatlan [teszteket](https://github.com/pester/pester) . A sablon a Visual Studio Code feladatait is tartalmazza, amelyekkel automatizálható a vendég-konfigurációs csomag kiépítése és érvényesítése. További információ: GitHub Project [Guest Configuration Project](https://github.com/microsoft/guestconfigurationproject).

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
A csomag formátumának. zip-fájlnak kell lennie, és nem lehet nagyobb a teljes 100 MB-os méretnél, ha tömörítetlen.

### <a name="storing-guest-configuration-artifacts"></a>Vendég konfigurációs összetevők tárolása

A. zip-csomagot a felügyelt virtuális gépek által elérhető helyen kell tárolni.
Ilyenek például a GitHub-adattárak, az Azure-Tárházak vagy az Azure Storage. Ha nem szeretné, hogy a csomag nyilvános legyen, az URL-címben egy [sas-tokent](../../../storage/common/storage-sas-overview.md) is hozzáadhat. A magánhálózati számítógépekhez [szolgáltatási végpontot](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) is alkalmazhat, bár ez a konfiguráció csak a csomag elérésére és a szolgáltatással való kommunikációra vonatkozik.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Lépésről lépésre, egyéni vendég-konfiguráció naplózási szabályzatának létrehozása Windows rendszerhez

A beállítások naplózásához hozzon létre egy DSC-konfigurációt. A következő PowerShell-parancsfájl egy **AuditBitLocker** nevű konfigurációt hoz létre, importálja a **PsDscResources** erőforrás-modult, és az `Service` erőforrás használatával naplózza a futó szolgáltatást. A konfigurációs parancsfájl futtatható Windows vagy macOS rendszerű gépről.

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
AuditBitLocker
```

Futtassa ezt a parancsfájlt egy PowerShell-terminálon, vagy mentse a fájlt a `config.ps1` Project mappában található néven.
Futtassa a PowerShellben a terminálon történő végrehajtással `./config.ps1` . Létrejön egy új MOF-fájl.

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
  -Configuration './AuditBitlocker/AuditBitlocker.mof'
```

A konfigurációs csomag létrehozása után, de az Azure-ba való közzététel előtt tesztelheti a csomagot a munkaállomás vagy a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD) környezetből. A GuestConfiguration parancsmag `Test-GuestConfigurationPackage` ugyanazt az ügynököt tartalmazza a fejlesztői környezetben, mint amelyet az Azure-gépeken belül használ. Ezzel a megoldással helyileg végezheti el az integrációs tesztelést, mielőtt kiadná a számlázott felhőalapú környezeteket.

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
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./AuditBitlocker/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

A következő lépés a fájl közzététele az Azure Blob Storageban. A Storage-fiókhoz nem tartoznak speciális követelmények, de érdemes a fájlt a gépek közelében lévő régióban üzemeltetni. Ha nincs Storage-fiókja, használja a következő példát. Az alábbi parancsoknak, beleértve `Publish-GuestConfigurationPackage` a modult, szükségesek `Az.Storage` .

```azurepowershell-interactive
# Creates a new resource group, storage account, and container
New-AzResourceGroup -name myResourceGroupName -Location WestUS
New-AzStorageAccount -ResourceGroupName myResourceGroupName -Name myStorageAccountName -SkuName 'Standard_LRS' -Location 'WestUs' | New-AzStorageContainer -Name guestconfiguration -Permission Blob
```

A parancsmag paraméterei `Publish-GuestConfigurationPackage` :

- **Elérési út**: a közzétenni kívánt csomag helye
- **ResourceGroupName**: az erőforráscsoport neve, ahol a Storage-fiók található
- **StorageAccountName**: annak a Storage-fióknak a neve, amelyben közzé kell tenni a csomagot
- **StorageContainerName**: (default: *guestconfiguration*) a Storage-fiókban lévő tároló neve
- **Kényszerített**: felülírja a meglévő csomagot a Storage-fiókban ugyanazzal a névvel

Az alábbi példa közzéteszi a csomagot egy "guestconfiguration" nevű tárolási tárolóban.

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
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

Az Azure-ban létrehozott szabályzattal az utolsó lépés a definíció társítása. Lásd: a definíció társítása a [portál](../assign-policy-portal.md), az [Azure CLI](../assign-policy-azurecli.md)és a [Azure PowerShell](../assign-policy-powershell.md)használatával.

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

A parancsmagok `New-GuestConfigurationPolicy` `Test-GuestConfigurationPolicyPackage` tartalmazzák a **paraméter** nevű paramétert. Ez a paraméter egy szórótábla-definíciót vesz fel, amely tartalmazza az egyes paraméterek részleteit, és létrehozza a Azure Policy-definícióhoz használt minden fájl szükséges részeit.

Az alábbi példa egy szabályzat-definíciót hoz létre egy szolgáltatás naplózásához, ahol a felhasználó a szabályzat-hozzárendelés időpontjában kiválasztja a listából.

```azurepowershell-interactive
# This DSC Resource text:
Service 'UserSelectedNameExample'
      {
          Name = 'ParameterValue'
          Ensure = 'Present'
          State = 'Running'
      }

# Would require the following hashtable:
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'UserSelectedNameExample'                                   # DSC configuration resource id (mandatory)
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

## <a name="policy-lifecycle"></a>Szabályzat életciklusa

Ha a szabályzat frissítését szeretné kibocsátani, végezze el a módosítást a vendég konfigurációs csomag és a Azure Policy definíció részleteit is.

> [!NOTE]
> A `version` vendég konfiguráció-hozzárendelés tulajdonsága csak a Microsoft által üzemeltetett csomagokat gyakorolja. Az egyéni tartalom verziószámozásának ajánlott eljárása, hogy tartalmazza a verziót a fájl nevében.

Először is, ha fut `New-GuestConfigurationPackage` , adja meg a csomag nevét, amely egyedivé teszi az előző verzióktól. Megadhat egy verziószámot is a névben, például: `PackageName_1.0.0` .
Az ebben a példában szereplő számot csak a csomag egyedivé tételéhez használja a rendszer, és nem határozza meg, hogy a csomagnak újabb vagy régebbinek kell lennie, mint a többi csomagnak.

Másodszor, frissítse a parancsmaghoz használt paramétereket az `New-GuestConfigurationPolicy` alábbi magyarázatok mindegyikével.

- **Verzió**: a parancsmag futtatásakor meg `New-GuestConfigurationPolicy` kell adnia a jelenleg közzétett verziónál nagyobb verziószámot.
- **contentUri**: a parancsmag futtatásakor meg `New-GuestConfigurationPolicy` kell adnia egy URI-t a csomag helyéhez. A fájl nevében szereplő csomag verziószáma biztosítja, hogy a tulajdonság értéke az egyes kiadásokban is megváltozik.
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

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a virtuális gépek a [vendég konfigurációjával](../concepts/guest-configuration.md)való naplózásáról.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](./programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](./get-compliance-data.md).
