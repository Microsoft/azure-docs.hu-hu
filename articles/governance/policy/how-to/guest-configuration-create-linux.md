---
title: Vendégkonfigurációs szabályzatok létrehozása Linux rendszeren
description: Megtudhatja, hogyan hozhat létre Azure Policy vendég-konfigurációs házirendet Linux rendszerhez.
ms.date: 08/17/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 352c8b1936c38c9b5f706ac88bd4fd06e008b892
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99525347"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Vendégkonfigurációs szabályzatok létrehozása Linux rendszeren

Az egyéni szabályzatok létrehozása előtt olvassa el a [Azure Policy vendég konfigurációjának](../concepts/guest-configuration.md)áttekintését ismertető cikket.
 
A Windows rendszerhez készült vendég-konfigurációs szabályzatok létrehozásával kapcsolatos további tudnivalókért tekintse meg a [Windows rendszerhez készült vendég-konfigurációs szabályzatok](./guest-configuration-create.md) létrehozásának oldalát.

A Linux naplózásakor a Vendégkonfiguráció a [Chef InSpecet](https://www.inspec.io/) használja. Az InSpec-profil határozza meg a gép kívánt állapotát. Ha a konfiguráció kiértékelése meghiúsul, a rendszer elindítja a **auditIfNotExists** , és a gép **nem megfelelőnek** minősül.

[Azure Policy vendég konfiguráció](../concepts/guest-configuration.md) csak a gépeken belüli beállítások naplózására használható. A gépeken belüli beállítások szervizelése még nem érhető el.

A következő műveletek végrehajtásával hozhatja létre saját konfigurációját egy Azure-beli vagy nem Azure-beli gép állapotának ellenőrzéséhez.

> [!IMPORTANT]
> A Azure Government és az Azure China Environments szolgáltatásban a vendég-konfigurációval rendelkező egyéni házirend-definíciók előzetes verzióként használhatók.
>
> A naplózás Azure-beli virtuális gépeken történő végrehajtásához szükség van a Vendégkonfiguráció bővítményre. Ha a bővítményt a Linux rendszerű gépek skáláján szeretné üzembe helyezni, rendelje hozzá a következő szabályzat-definíciót: `Deploy prerequisites to enable Guest Configuration Policy on Linux VMs`
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
> A konfigurációk fordítása Linux rendszeren nem támogatott.

### <a name="base-requirements"></a>Alapszintű követelmények

Azok az operációs rendszerek, amelyeken telepítve van a modul:

- Linux
- macOS
- Windows

> [!NOTE]
> A parancsmaghoz a (z `Test-GuestConfigurationPackage` ) 1,0-es OpenSSL-verzióra van szükség, mert egy a következőtől függ:. Ez hibát okoz az OpenSSL 1,1-es vagy újabb verziójával rendelkező környezetekben.
>
> A parancsmag futtatása `Test-GuestConfigurationPackage` csak a Windows for Guest konfigurációs modul 2.1.0 verziója esetén támogatott.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Vendég konfigurációs összetevők és szabályzat Linux rendszerhez

Még Linux-környezetekben is a vendég konfigurációja a kívánt állapot konfigurációját használja nyelvi absztrakcióként. A megvalósítás natív kódból (C++) alapul, így nincs szükség a PowerShell betöltésére. Ehhez azonban szükség van egy konfigurációs MOF-re, amely leírja a környezet részleteit.
A DSC burkolóként viselkedik a művelet végrehajtásához, a paraméterek meghatározásához, valamint a szolgáltatáshoz való visszatéréshez. A DSC kis ismerete szükséges, ha egyéni inspec-tartalommal dolgozik.

#### <a name="configuration-requirements"></a>Konfigurációs követelmények

Az egyéni konfiguráció nevének mindenütt konzisztensnek kell lennie. A Content csomag. zip fájljának nevét, a MOF-fájlban található konfiguráció nevét, valamint a Azure Resource Manager sablonban (ARM-sablon) lévő vendég-hozzárendelés nevét meg kell egyeznie.

A PowerShell-parancsmagok segítséget nyújtanak a csomag létrehozásában.
Nincs szükség gyökér szintű mappára vagy a Version mappára.
A csomag formátumának. zip formátumúnak kell lennie. a nem lehet nagyobb, mint a teljes 100 MB-os méret a kibontott értéknél.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Egyéni vendég konfigurációs konfiguráció Linuxon

A Linuxon a vendég konfigurációja az `ChefInSpecResource` erőforrás használatával biztosítja a motor számára az [inspec-profil](https://www.inspec.io/docs/reference/profiles/)nevét. A **név** az egyetlen szükséges erőforrás-tulajdonság. Hozzon létre egy YaML-fájlt és egy Ruby-parancsfájlt az alább részletezett módon.

Először hozza létre az inspec által használt YaML-fájlt. A fájl alapvető információkat biztosít a környezetről. Alább látható egy példa:

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Mentse ezt a fájlt a `inspec.yml` `linux-path` Project-címtár nevű mappájába.

Ezután hozza létre a Ruby-fájlt a számítógép naplózásához használt inspec nyelvi absztrakcióval.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Mentse ezt a fájlt `linux-path.rb` egy nevű új mappába `controls` a `linux-path` címtárban.

Végül hozzon létre egy konfigurációt, importálja a **PSDesiredStateConfiguration** erőforrás-modult, és fordítsa le a konfigurációt.

```powershell
# import PSDesiredStateConfiguration module
import-module PSDesiredStateConfiguration

# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
AuditFilePathExists -out ./Config
```

Mentse ezt a fájlt `config.ps1` a Project mappában található néven. Futtassa a PowerShellben a terminálon történő végrehajtással `./config.ps1` . A rendszer létrehoz egy új MOF-fájlt.

A `Node AuditFilePathExists` parancs nem szükséges technikailag, de az alapértelmezett helyett egy nevű fájlt hoz létre `AuditFilePathExists.mof` `localhost.mof` . Ha a. MOF-fájlnevet követi, a konfiguráció megkönnyíti a sok fájl rendszerezését nagy léptékű működés esetén.

Most már az alábbiak szerint kell lennie egy projekt struktúrájának:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        inspec.yml
        / controls
            linux-path.rb 
```

A támogató fájlokat össze kell csomagolni. A kitöltött csomagot a vendég konfigurációja használja a Azure Policy definíciók létrehozásához.

A `New-GuestConfigurationPackage` parancsmag létrehozza a csomagot. A parancsmag paraméterei `New-GuestConfigurationPackage` Linux-tartalom létrehozásakor:

- **Name**: vendég konfigurációs csomag neve.
- **Konfiguráció**: a lefordított konfigurációs dokumentum teljes elérési útja.
- **Elérési út**: kimeneti mappa elérési útja. Ezt a paramétert nem kötelező megadni. Ha nincs megadva, a csomag az aktuális könyvtárban jön létre.
- **ChefInspecProfilePath**: az inspec-profil teljes elérési útja. Ez a paraméter csak akkor támogatott, ha tartalmat hoz létre a Linux rendszerű naplózáshoz.

A következő parancs futtatásával hozzon létre egy csomagot az előző lépésben megadott konfiguráció használatával:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

A konfigurációs csomag létrehozása után, de az Azure-ba való közzététel előtt tesztelheti a csomagot a munkaállomás vagy a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD) környezetből. A GuestConfiguration parancsmag `Test-GuestConfigurationPackage` ugyanazt az ügynököt tartalmazza a fejlesztői környezetben, mint amelyet az Azure-gépeken belül használ. Ezzel a megoldással helyileg is elvégezheti az integrációs tesztelést, mielőtt kiadná a számlázott felhőalapú környezeteket.

Mivel az ügynök ténylegesen kiértékeli a helyi környezetet, a legtöbb esetben a test-parancsmagot ugyanarra az operációsrendszer-platformra kell futtatnia, mint a naplózást.

A parancsmag paraméterei `Test-GuestConfigurationPackage` :

- **Név**: a vendég konfigurációs szabályzatának neve.
- **Paraméter**: szórótábla formátumban megadott házirend-paraméterek.
- **Elérési út**: a vendég konfigurációs csomag teljes elérési útja.

Futtassa a következő parancsot az előző lépésben létrehozott csomag teszteléséhez:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

A parancsmag a PowerShell-folyamatból is támogatja a bemenetet. A parancsmag kimenetének átadása `New-GuestConfigurationPackage` a `Test-GuestConfigurationPackage` parancsmagnak.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefInspecProfilePath './' | Test-GuestConfigurationPackage
```

A következő lépés a fájl közzététele az Azure Blob Storageban. A parancshoz `Publish-GuestConfigurationPackage` a `Az.Storage` modul szükséges.

A parancsmag paraméterei `Publish-GuestConfigurationPackage` :

- **Elérési út**: a közzétenni kívánt csomag helye
- **ResourceGroupName**: az erőforráscsoport neve, ahol a Storage-fiók található
- **StorageAccountName**: annak a Storage-fióknak a neve, amelyben közzé kell tenni a csomagot
- **StorageContainerName**: (default: *guestconfiguration*) a Storage-fiókban lévő tároló neve
- **Kényszerített**: felülírja a meglévő csomagot a Storage-fiókban ugyanazzal a névvel

Az alábbi példa közzéteszi a csomagot egy "guestconfiguration" nevű tárolási tárolóban.

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditFilePathExists/AuditFilePathExists.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

A következő fájlokat hozza létre `New-GuestConfigurationPolicy` :

- **auditIfNotExists.jsbekapcsolva**

A parancsmag kimenete egy olyan objektumot ad vissza, amely a házirend-fájlok kezdeményezésének megjelenítendő nevét és elérési útját tartalmazza.

Végül tegye közzé a szabályzat-definíciókat a `Publish-GuestConfigurationPolicy` parancsmag használatával. A parancsmag csak a **path** paraméterrel rendelkezik, amely a által létrehozott JSON-fájlok helyére mutat `New-GuestConfigurationPolicy` .

A közzétételi parancs futtatásához hozzá kell férnie a szabályzatok létrehozásához az Azure-ban. A konkrét engedélyezési követelmények dokumentálva vannak a [Azure Policy áttekintés](../overview.md) oldalon. A legjobb beépített szerepkör az erőforrás- **házirend közreműködője**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path './policies'
```

 A `Publish-GuestConfigurationPolicy` parancsmag fogadja az elérési utat a PowerShell-folyamatból. Ez a funkció azt jelenti, hogy létrehozhatja a házirend-fájlokat, és közzéteheti őket egyetlen vezetékes parancsban.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

Az Azure-ban létrehozott szabályzattal az utolsó lépés a definíció társítása. Lásd: a definíció társítása a [portál](../assign-policy-portal.md), az [Azure CLI](../assign-policy-azurecli.md)és a [Azure PowerShell](../assign-policy-powershell.md)használatával.

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Paraméterek használata az egyéni vendég-konfigurációs házirendekben

A vendég konfiguráció futási időben támogatja a konfiguráció felülírási tulajdonságait. Ez a funkció azt jelenti, hogy a csomagban lévő MOF-fájlban lévő értékeket nem kell statikusnak tekinteni. A felülbírálási értékek a Azure Policyon keresztül érhetők el, és nem befolyásolják a konfigurációk létrehozási vagy fordítási módját.

Az inspec paraméterrel jellemzően a paramétereket a rendszer a futtatókörnyezetben vagy az attribútumokat használó kódokként kezeli. A vendég konfigurációja elhomályosítja ezt a folyamatot, így a rendszer a szabályzat hozzárendelésekor megadhatja a bemenetet. A rendszer automatikusan létrehoz egy attribútum-fájlt a gépen. Nem kell létrehoznia és felvennie egy fájlt a projektben. A Linux audit projekthez két lépéssel adhat hozzá paramétereket.

Adja meg azt a Ruby-fájlban található bemenetet, amelyen a számítógépen naplózni szeretné a naplót. Alább látható egy példa.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Adja hozzá a konfigurációban a **AttributesYmlContent** tulajdonságot bármilyen karakterlánccal, értékeként.
A vendég konfigurációs ügynök automatikusan létrehozza a YAML-fájlt, amelyet az inspec az attribútumok tárolására használ. Lásd az alábbi példát.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "fromParameter"
        }
    }
}
```

Fordítsa újra a MOF-fájlt a jelen dokumentumban szereplő példák alapján.

A parancsmagok `New-GuestConfigurationPolicy` `Test-GuestConfigurationPolicyPackage` tartalmazzák a **paraméter** nevű paramétert. Ez a paraméter egy szórótábla tartalmaz, amely tartalmazza az egyes paraméterek összes részletét, és automatikusan létrehozza az egyes Azure Policy-definíciók létrehozásához használt fájlok összes szükséges részét.

Az alábbi példa egy házirend-definíciót hoz létre egy fájl elérési útjának naplózásához, ahol a felhasználó a házirend-hozzárendelés időpontjában megadja az elérési utat.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = 'File path to be audited.'      # Policy parameter description (optional)
        ResourceType = 'ChefInSpecResource'           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = 'AttributesYmlContent' # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

$uri = 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D'

New-GuestConfigurationPolicy -ContentUri $uri `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameter $PolicyParameterInfo `
    -Platform 'Linux' `
    -Version 1.0.0
```


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

### <a name="filtering-guest-configuration-policies-using-tags"></a>Vendég konfigurációs szabályzatok szűrése címkék használatával

A parancsmagok által a vendég konfigurációs modulban létrehozott házirendek opcionálisan tartalmazhatnak szűrőket a címkékhez. A **-tag** paraméter a `New-GuestConfigurationPolicy` támogatja az egyéni címkéket tartalmazó szórótáblában tömbjét. A címkék hozzá lesznek adva a `If` szabályzat-definíció szakaszához, és nem módosíthatók házirend-hozzárendeléssel.

Az alábbi példa egy olyan szabályzat-definíció kódrészletét adja meg, amely a címkék szűrésére szolgál.

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
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>Nem kötelező: a vendég konfigurációs csomagjainak aláírása

A vendég konfigurációja egyéni házirendek SHA256 kivonatot használnak a házirend-csomag érvényesítéséhez.
Igény szerint az ügyfelek is használhatnak tanúsítványokat a csomagok aláírására és a vendég konfigurációs bővítmény kényszerítésére, hogy csak az aláírt tartalmat engedélyezzék.

Ennek a forgatókönyvnek az engedélyezéséhez két lépést kell végrehajtania. Futtassa a parancsmagot a tartalomindex aláírásához, és fűzze hozzá a címkét azokhoz a gépekhez, amelyeknek az aláírásához kód szükséges.

Az aláírás-ellenőrzési funkció használatához futtassa a `Protect-GuestConfigurationPackage` parancsmagot a csomag aláírásához a közzététel előtt. Ehhez a parancsmaghoz "kód aláírása" tanúsítvány szükséges.

A parancsmag paraméterei `Protect-GuestConfigurationPackage` :

- **Elérési út**: a vendég konfigurációs csomag teljes elérési útja.
- **PublicGpgKeyPath**: nyilvános GPG-kulcs elérési útja. Ez a paraméter csak a Linux-tartalmak aláírása esetén támogatott.

A Linux rendszerű gépekhez használható GPG-kulcsok létrehozásának jó referenciája a GitHubon található cikk, [új GPG-kulcs](https://help.github.com/en/articles/generating-a-new-gpg-key)létrehozása.

A GuestConfiguration-ügynök elvárja, hogy a tanúsítvány nyilvános kulcsa jelen legyen a Linux rendszerű gépek elérési útjában `/usr/local/share/ca-certificates/extra` . Ahhoz, hogy a csomópont ellenőrizze az aláírt tartalmat, az egyéni házirend alkalmazása előtt telepítse a tanúsítvány nyilvános kulcsát a gépre. Ez a folyamat a virtuális gépen belüli bármely technikával vagy Azure Policy használatával végezhető el. [Itt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)található egy példa erre a sablonra.
Az Key Vault hozzáférési szabályzatnak lehetővé kell tennie a számítási erőforrás-szolgáltató számára a tanúsítványok elérését a központi telepítések során. A részletes lépésekért lásd: [Key Vault beállítása virtuális gépekhez a Azure Resource Managerban](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

A tartalom közzététele után fűzze hozzá a nevet és az értéket tartalmazó címkét az `GuestConfigPolicyCertificateValidation` `enabled` összes olyan virtuális géphez, amelynél szükség van a kód aláírására. Tekintse meg azokat a [mintákat](../samples/built-in-policies.md#tags) , amelyekkel a címkék a Azure Policy használatával méretezhetők. A címke betartása után a parancsmag használatával generált szabályzat-definíció `New-GuestConfigurationPolicy` engedélyezi a követelményt a vendég konfigurációs bővítményen keresztül.

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a virtuális gépek a [vendég konfigurációjával](../concepts/guest-configuration.md)való naplózásáról.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](./programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](./get-compliance-data.md).
