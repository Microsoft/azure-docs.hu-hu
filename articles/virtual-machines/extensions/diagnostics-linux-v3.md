---
title: Azure számítás – Linux diagnosztikai bővítmény 3,0
description: Az Azure Linux diagnosztikai bővítmény (LAD) 3,0 konfigurálása az Azure-ban futó Linux rendszerű virtuális gépek metrikáinak és naplózási eseményeinek összegyűjtéséhez.
services: virtual-machines-linux
author: axayjo
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh
ms.openlocfilehash: 88f8d62a6321deae8a46d32441625c5960547694
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99982103"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>A Linux diagnosztikai bővítmény 3,0 használata a metrikák és naplók figyeléséhez

Ez a dokumentum a Linux diagnosztikai bővítmény 3,0-es és újabb verzióját ismerteti.

> [!IMPORTANT]
> A 2,3-es és régebbi verzióval kapcsolatos információkért tekintse meg [ezt a dokumentumot](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

## <a name="introduction"></a>Bevezetés

A Linux diagnosztikai bővítmény segítségével a felhasználók figyelheti Microsoft Azure-on futó Linux rendszerű virtuális gépek állapotát. A következő képességekkel rendelkezik:

* Összegyűjti a rendszerteljesítményi mérőszámokat a virtuális gépről, és egy kijelölt Storage-fiókban tárolja őket egy adott táblában.
* Beolvassa a naplózási eseményeket a syslog-ből, és a kijelölt Storage-fiók egy adott táblájába tárolja azokat.
* Lehetővé teszi a felhasználók számára a gyűjtött és feltöltött adatmérőszámok testreszabását.
* Lehetővé teszi a felhasználók számára a syslog-létesítmények és a gyűjtött és feltöltött események súlyossági szintjeinek testreszabását.
* Lehetővé teszi a felhasználóknak a megadott naplófájlok feltöltését egy kijelölt Storage-táblába.
* A lehetővé teszi metrikák és naplózási események küldését tetszőleges EventHub-végpontokra és JSON-formátumú blobokra a kijelölt Storage-fiókban.

Ez a bővítmény az Azure üzembe helyezési modelljeivel is működik.

## <a name="installing-the-extension-in-your-vm"></a>A bővítmény telepítése a virtuális gépre

Ezt a bővítményt a Azure PowerShell-parancsmagok, az Azure CLI-parancsfájlok, az ARM-sablonok vagy a Azure Portal használatával engedélyezheti. További információ: [bővítmények szolgáltatásai](features-linux.md).

>[!NOTE]
>A diagnosztikai virtuálisgép-bővítmény egyes összetevőit a [log Analytics VM-bővítmény](./oms-linux.md)is tartalmazza. Az architektúra miatt ütközések merülhetnek fel, ha mindkét bővítmény ugyanabban az ARM-sablonban van létrehozva. A telepítési idejű ütközések elkerülése érdekében használja az [ `dependsOn` irányelvet](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) annak biztosítására, hogy a bővítmények egymás után legyenek telepítve. A bővítmények mindkét sorrendben telepíthetők.

Ezek a telepítési utasítások és egy [letölthető minta konfiguráció](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) , amely a Lad 3,0-et konfigurálja:

* rögzítheti és tárolhatja ugyanazokat a mérőszámokat, mint a LAD 2,3;
* rögzítse a fájlrendszer metrikáinak hasznos készletét, amely az új a LAD 3,0;
* a LAD 2,3; által engedélyezett alapértelmezett syslog-gyűjtemény rögzítése
* a virtuális gépek metrikáinak ábrázolására és riasztására szolgáló Azure Portal használatának engedélyezése.

A letölthető konfiguráció csupán egy példa; módosítsa a saját igényeinek megfelelően.

### <a name="supported-linux-distributions"></a>Támogatott Linux-disztribúciók

A Linux diagnosztikai bővítmény a következő disztribúciókat és verziókat támogatja. A disztribúciók és verziók listája csak az Azure által támogatott linuxos gyártói rendszerképekre vonatkozik. A harmadik féltől származó BYOL-és BYOS-lemezképek, például a készülékek, általában nem támogatottak a Linux diagnosztikai bővítményben.

A csak a főverziókat (például a Debian 7-et) felsoroló disztribúció minden másodlagos verzió esetében is támogatott. Ha meg van adva egy adott alverzió, akkor csak az adott verziót támogatja a rendszer. Ha a "+" utótaggal rendelkezik, a rendszer támogatja a megadott verziónál nagyobb vagy annál kisebb alverziókat.

Támogatott disztribúciók és verziók:

- Ubuntu 18,04, 16,04, 14,04
- CentOS 7, 6.5 +
- Oracle Linux 7, 6.4 +
- OpenSUSE 13.1 +
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- RHEL 7, 6,7 +

### <a name="prerequisites"></a>Előfeltételek

* Az **Azure Linux Agent 2.2.0-es vagy újabb verziója**. A legtöbb Azure-beli virtuális gép Linux-katalógusa tartalmaz 2.2.7 vagy újabb verziót. A futtatásával erősítse meg a `/usr/sbin/waagent -version` virtuális gépre telepített verziót. Ha a virtuális gép a vendég ügynök egy régebbi verzióját futtatja, a frissítéshez kövesse az [alábbi utasításokat](./update-linux-agent.md) .
* **Azure CLI**-vel. [Állítsa be az Azure CLI](/cli/azure/install-azure-cli) -környezetet a gépen.
* A wget parancs, ha még nem tette meg: Futtatás `sudo apt-get install wget` .
* Meglévő Azure-előfizetés és meglévő általános célú Storage-fiók az adattároláshoz.  Az általános célú Storage-fiókok támogatják a Table Storage-t, amelyhez szükség van.  A blob Storage-fiók nem fog működni.
* Python 2

### <a name="python-requirement"></a>Python-követelmény

A Linux diagnosztikai bővítményhez a Python 2 szükséges. Ha a virtuális gép olyan disztribúciót használ, amely alapértelmezés szerint nem tartalmazza a Python 2-et, akkor telepítenie kell azt. A következő minta parancsok a Python 2 különböző disztribúciókban való telepítését teszik ki.    

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE `zypper install -y python2`

A python2 végrehajtható fájljának aliasnak kell lennie a *Pythonhoz*. Az alábbi két módszer használható az alias beállításához:

1. Futtassa a következő parancsot a meglévő aliasok eltávolításához.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. A következő parancs futtatásával hozza létre az aliast.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Minta telepítése

> [!NOTE]
> Bármelyik minta esetében a Futtatás előtt adja meg az első szakaszban szereplő változók helyes értékeit. 

A példákban letöltött minta-konfiguráció szabványos adatokat gyűjt, és a táblázatos tárolóba küldi őket. A minta konfigurációjának és tartalmának URL-címe változhat. A legtöbb esetben le kell töltenie a portál beállítások JSON-fájljának másolatát, és testre kell szabnia az igényeinek megfelelően, majd minden olyan sablonnal vagy automatizálással rendelkezik, amely a konfigurációs fájl saját verzióját használja, és nem tölti le az adott URL-címet.

#### <a name="azure-cli-sample"></a>Azure CLI-minta

```azurecli
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-30-extension-on-the-virtual-machine-scale-set-instance"></a>Azure CLI-minta a LAD 3,0-bővítmény telepítéséhez a virtuálisgép-méretezési csoport példányán

```azurecli
#Set your Azure VMSS diagnostic variables correctly below
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VMSS resource ID. Replace storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>PowerShell-minta

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>A bővítmény beállításainak frissítése

Miután módosította a védett vagy a nyilvános beállításokat, telepítse őket a virtuális gépre ugyanazon parancs futtatásával. Ha bármilyen változás történt a beállításokban, a rendszer elküldi a frissített beállításokat a bővítménynek. A LAD újra betölti a konfigurációt, és újraindul.

### <a name="migration-from-previous-versions-of-the-extension"></a>Áttelepítés a bővítmény korábbi verzióiból

A bővítmény legújabb verziója **3,0**. A **régi verziók (2. x) elavultak, és a 2018. július 31-ig vagy azt követően nem** tehetők közzé.

> [!IMPORTANT]
> Ez a bővítmény bevezeti a bővítmény konfigurációjának feltörésének változásait. Egy ilyen változás történt a bővítmény biztonságának javítása érdekében; Ennek eredményeképpen a 2. x verzióra visszamenőleges kompatibilitás nem tartható karban. Emellett a bővítmény közzétevője nem azonos a 2. x verzió közzétevője verziójával.
>
> Ha 2. x verzióról kíván áttérni a bővítmény ezen új verziójára, el kell távolítania a régi bővítményt (a régi közzétevő neve alatt), majd telepítenie kell a bővítmény 3. verzióját.

Javaslatok

* Telepítse a bővítményt, ha engedélyezve van az alverzió automatikus frissítése.
  * A klasszikus üzembe helyezési modellben a virtuális gépeken a "3. *" verziót kell megadni, ha a bővítményt az Azure XPLAT CLI vagy a PowerShell használatával telepíti.
  * Azure Resource Manager üzembe helyezési modell virtuális gépeken a virtuális gép központi telepítési sablonjában a "autoUpgradeMinorVersion": true "értéket kell tartalmaznia.
* Új/eltérő Storage-fiók használata a LAD 3,0-hez. Több kis inkompatibilitás van a LAD 2,3 és a LAD 3,0 között, amelyek megosztanak egy fiókot:
  * A LAD 3,0 egy másik nevű táblában tárolja a syslog-eseményeket.
  * A metrikák counterSpecifier karakterláncai `builtin` eltérnek a 3,0.

## <a name="protected-settings"></a>Védett beállítások

A konfigurációs adatok ezen készlete olyan bizalmas információkat tartalmaz, amelyeket védeni kell a nyilvános nézetből, például a tárolási hitelesítő adatokat. Ezeket a beállításokat a kiterjesztés titkosított formában továbbítja és tárolja.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name | Érték
---- | -----
storageAccountName | Annak a Storage-fióknak a neve, amelybe az adatkiterjesztést írta.
storageAccountEndPoint | választható A felhőt azonosító végpont, amelyben a Storage-fiók létezik. Ha ez a beállítás nem érhető el, a LAD alapértelmezett értéke az Azure nyilvános felhő `https://core.windows.net` . Ha Azure Germany-, Azure Government-vagy Azure China-beli Storage-fiókot szeretne használni, ennek megfelelően állítsa be ezt az értéket.
storageAccountSasToken | Egy [fiók sas-jogkivonata](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) a blob és Table Services ( `ss='bt'` ) számára, amely a tárolók és objektumok () számára érvényes `srt='co'` , amely a hozzáadási, létrehozási, listázási, frissítési és írási engedélyeket ( `sp='acluw'` ) biztosítja. Ne *foglalja bele* a kezdő kérdőjelet (?).
mdsdHttpProxy | választható A HTTP-proxy azon adatai, amelyek lehetővé teszik a bővítménynek a megadott Storage-fiókhoz és végponthoz való csatlakozást.
sinksConfig | választható Azon alternatív célhelyek részletei, amelyekhez mérőszámokat és eseményeket lehet továbbítani. A bővítmény által támogatott adattárolók konkrét részletei az alábbi szakaszokban találhatók.

Ha SAS-tokent szeretne beolvasni egy Resource Manager-sablonon belül, használja a **listAccountSas** függvényt. Példa a sablonra: [példa a függvények listázására](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

A szükséges SAS-tokent egyszerűen létrehozhatja a Azure Portalon keresztül.

1. Válassza ki azt az általános célú Storage-fiókot, amelyhez a bővítményt írni szeretné
1. A bal oldali menü beállítások részében válassza a "közös hozzáférésű aláírás" lehetőséget.
1. Tegye meg a megfelelő szakaszokat az előzőekben leírtak szerint
1. Kattintson az "SAS előállítása" gombra.

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Képernyőfelvétel: a közös hozzáférésű aláírás oldal, amely az S A S-t tartalmazza.":::

Másolja a generált SAS-t a storageAccountSasToken mezőbe; eltávolítja a kezdő kérdőjelet ("?").

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

Ez a választható szakasz azokat a további célhelyeket határozza meg, amelyekhez a bővítmény elküldi az általa gyűjtött adatokat. A "fogadó" tömb tartalmaz egy objektumot minden további adatfogadóhoz. A "type" attribútum meghatározza az objektum többi attribútumát.

Elem | Érték
------- | -----
name | A bővítmény konfigurációjában máshol a fogadóhoz való hivatkozáshoz használt karakterlánc.
típus | A definiált fogadó típusa. Meghatározza az ilyen típusú példányok egyéb értékeit (ha vannak ilyenek).

A Linux diagnosztikai bővítmény 3,0-es verziója két fogadó típust támogat: EventHub és JsonBlob.

#### <a name="the-eventhub-sink"></a>A EventHub fogadó

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

A "sas URL" bejegyzés tartalmazza a teljes URL-címet, beleértve az SAS-tokent is, az Event hub számára, amelyre közzé kell tenni az adott adat. A LAD-nek szüksége van egy olyan házirendre, amely engedélyezi a küldési jogcímet. Példa:

* Hozzon létre egy nevű Event Hubs névteret `contosohub`
* Hozzon létre egy Event hubot a nevű névtérben. `syslogmsgs`
* Hozzon létre egy megosztott hozzáférési szabályzatot az nevű esemény-hubhoz `writer` , amely engedélyezi a jogcím küldését.

Ha a SAS-t a 2018-as éjféli UTC szerint hozta létre, akkor a sas URL értéke a következő lehet:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

További információ a Event Hubs SAS-jogkivonatokkal kapcsolatos információk létrehozásáról és beolvasásáról: [Ez a weblap](/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="the-jsonblob-sink"></a>A JsonBlob fogadó

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Az JsonBlob-fogadóba irányított adattárolók tárolása a blobokban történik az Azure Storage-ban. A LAD minden példánya minden órában létrehoz egy blobot minden egyes fogadó nevénél. Az egyes Blobok mindig az objektum szintaktikai érvényes JSON-tömbjét tartalmazzák. Az új bejegyzések a tömbhöz való atomi hozzáadásával lettek hozzáadva. A Blobok tárolása a fogadóval megegyező nevű tárolóban történik. A blob-tárolók neveihez tartozó Azure Storage-szabályok a JsonBlob-mosdók nevére érvényesek: 3 és 63 kisbetűs alfanumerikus ASCII-karakter vagy kötőjel.

## <a name="public-settings"></a>Nyilvános beállítások

Ez a struktúra különböző beállításokat tartalmaz, amelyek a bővítmény által gyűjtött adatokat vezérlik. Az egyes beállítások megadása nem kötelező. Ha megadja `ladCfg` , azt is meg kell adnia `StorageAccount` .

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Elem | Érték
------- | -----
StorageAccount | Annak a Storage-fióknak a neve, amelybe az adatkiterjesztést írta. A [védett beállításokban](#protected-settings)megadott névnek meg kell egyeznie.
mdsdHttpProxy | választható Ugyanaz, mint a [védett beállításokban](#protected-settings). A nyilvános értéket felülbírálja a magánhálózati érték, ha be van állítva. Helyezzen el egy titkos kulcsot (például egy jelszót) tartalmazó proxybeállításokat a [védett beállításokban](#protected-settings).

A többi elemet a következő szakaszokban részletesen ismertetjük.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Ez a választható struktúra szabályozza a metrikák és naplók összegyűjtését az Azure metrika szolgáltatásba és más adatnyelők számára történő kézbesítéshez. A `performanceCounters` vagy a vagy a mindkettőt kell megadnia `syslogEvents` . Meg kell adnia a `metrics` struktúrát.

Elem | Érték
------- | -----
eventVolume | választható A Storage-táblán belül létrehozott partíciók számának szabályozása. A (,) vagy a () egyikének kell lennie `"Large"` `"Medium"` `"Small"` . Ha nincs megadva, az alapértelmezett érték: `"Medium"` .
sampleRateInSeconds | választható A nyers (nem aggregált) mérőszámok gyűjteménye közötti alapértelmezett időköz. A legkisebb támogatott mintavételi sebesség 15 másodperc. Ha nincs megadva, az alapértelmezett érték: `15` .

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Elem | Érték
------- | -----
resourceId | A virtuális gép vagy a virtuálisgép-méretezési csoport Azure Resource Manager erőforrás-azonosítója, amelyre a virtuális gép tartozik. Ezt a beállítást akkor is meg kell adni, ha a konfigurációban bármilyen JsonBlob-fogadó használatban van.
scheduledTransferPeriod | Az összesített metrikák kiszámításának és az Azure-mérőszámokra való átadásának gyakorisága 8601-as Időintervallumként kifejezve. A legkisebb átvitel időtartama 60 másodperc, azaz PT1M. Legalább egy scheduledTransferPeriod meg kell adnia.

A performanceCounters szakaszban megadott mérőszámok mintáit 15 másodpercenként vagy a számlálóhoz explicit módon meghatározott mintavételi sebességgel gyűjti a rendszer. Ha több scheduledTransferPeriod-frekvencia is megjelenik (ahogy a példában is látható), az egyes összesítéseket egymástól függetlenül számítjuk ki.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Ez a választható szakasz szabályozza a metrikák gyűjteményét. A nyers mintákat minden [scheduledTransferPeriod](#metrics) összesíti az alábbi értékek előállításához:

* középérték
* minimum
* maximum
* utolsó összegyűjtött érték
* az Összesítés kiszámításához használt nyers minták száma

Elem | Érték
------- | -----
fogadóként | választható Egy vesszővel elválasztott lista azoknak a nyelőknek a neveiről, amelyekhez a LAD összesített metrikai eredményeket küld. Minden összesített metrika közzé lesz téve az egyes felsorolt fogadók számára. Lásd: [sinksConfig](#sinksconfig). Példa: `"EHsink1, myjsonsink"`.
típus | A metrika tényleges szolgáltatóját azonosítja.
osztály | A "Counter" kifejezéssel együtt a szolgáltató névterében lévő adott mérőszámot azonosítja.
számláló | A "class" kifejezéssel együtt a szolgáltató névterében lévő adott mérőszámot azonosítja.
counterSpecifier | Az Azure-metrikák névterében lévő adott mérőszámot azonosítja.
feltétel | választható Kiválasztja az objektum egy adott példányát, amelyre a metrika vonatkozik, vagy kiválasztja az összesítést az adott objektum összes példánya között. További információ: `builtin` metrika-definíciók.
Mintavételi | Az 8601-es intervallum, amely meghatározza, hogy a rendszer milyen mértékben gyűjtsön nyers mintákat a metrikához. Ha nincs beállítva, a gyűjtési időközt a [sampleRateInSeconds](#ladcfg)érték határozza meg. A legrövidebb támogatott mintavételi sebesség 15 másodperc (PT15S).
egység | A következő karakterláncok egyikének kell lennie: "Count", "Bytes", "Seconds", "százalék", "CountPerSecond", "BytesPerSecond", "ezredmásodperc". Meghatározza a metrika egységét. Az összegyűjtött adatok felhasználói a begyűjtött adatértékeket az egységnek megfelelően elvárják. A mező figyelmen kívül hagyja ezt a mezőt.
displayName | A címkét (a társított területi beállítás által megadott nyelven) az Azure-Mérőszámokban lévő adatokhoz kell csatolni. A mező figyelmen kívül hagyja ezt a mezőt.

A counterSpecifier tetszőleges azonosító. A metrikák felhasználói, például az Azure Portal ábrázolási és riasztási funkció, a counterSpecifier használja "kulcsként", amely egy metrika vagy egy metrika egy példányát azonosítja. A `builtin` metrikák esetében ajánlott olyan counterSpecifier-értékeket használni, amelyek a következővel kezdődnek: `/builtin/` . Ha egy metrika egy adott példányát gyűjti be, javasoljuk, hogy a példány azonosítóját csatolja a counterSpecifier értékhez. Néhány példa:

* `/builtin/Processor/PercentIdleTime` – Az összes vCPU átlagos üresjárati idő
* `/builtin/Disk/FreeSpace(/mnt)` – Szabad terület a/mnt fájlrendszer számára
* `/builtin/Disk/FreeSpace` – Az összes csatlakoztatott fájlrendszer esetében átlagosan szabad terület

Sem a LAD, sem a Azure Portal a counterSpecifier értéket várja a mintázatnak megfelelően. Konzisztensnek kell lennie a counterSpecifier értékeinek összeállításában.

Ha megadja `performanceCounters` , a Lad mindig az Azure Storage-ban lévő táblába ír egy adatot. A JSON-blobokban és/vagy Event Hubsban is megadhatja ugyanazokat az adatfájlokat, de nem tilthatja le az adattárolást egy táblába. A diagnosztikai bővítmény minden olyan példánya, amely ugyanazt a Storage-fióknevet és-végpontot használja, hozzá kell adnia a metrikákat és a naplókat ugyanahhoz a táblához. Ha túl sok virtuális gép ír ugyanarra a táblázatos partícióra, az Azure képes a partíción lévő írások szabályozására. A eventVolume beállítás azt eredményezi, hogy a bejegyzések 1 (kis), 10 (közepes) vagy 100 (nagyméretű) különböző partíciók között oszlanak el. A "közepes" érték általában elegendő ahhoz, hogy a forgalom ne legyen szabályozva. A Azure Portal Azure mérőszámok funkciója a táblázat adatait használja a diagramok létrehozásához vagy riasztások elindításához. A tábla neve a következő sztringek összefűzése:

* `WADMetrics`
* A táblázatban tárolt összesített értékek "scheduledTransferPeriod"
* `P10DV2S`
* Egy "ÉÉÉÉHHNN" formátumú dátum, amely 10 naponta változik

Ilyenek például `WADMetricsPT1HP10DV2S20170410` a és a `WADMetricsPT1MP10DV2S20170609` .

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Ez a választható szakasz a naplózási események a syslog-ből történő gyűjtését vezérli. Ha a szakasz nincs megadva, a rendszer egyáltalán nem rögzíti a syslog-eseményeket.

A syslogEventConfiguration-gyűjtemény egyetlen bejegyzést tartalmaz minden olyan syslog-létesítményhez, amely az összes fontos eszközt felvette. Ha a minSeverity "nincs" egy adott létesítmény esetében, vagy ha a létesítmény egyáltalán nem jelenik meg az elemben, akkor a rendszer nem rögzíti az adott létesítményből származó eseményeket.

Elem | Érték
------- | -----
fogadóként | Vesszővel tagolt lista azoknak a fogadóknak a neveiről, amelyeken az egyes naplózási események közzé vannak téve. A syslogEventConfiguration korlátozásait egyeztető összes naplózási eseményt közzéteszi az egyes felsorolt fogadók. Példa: "EHforsyslog"
kódban | A syslog-létesítmény neve (például "LOG \_ User" vagy "log \_ LOCAL0"). A teljes listához tekintse meg a [syslog man oldalának](http://man7.org/linux/man-pages/man3/syslog.3.html) "létesítmény" szakaszát.
minSeverity | Syslog súlyossági szintje (például "LOG \_ err" vagy "log \_ info"). Tekintse meg a [syslog man oldalának](http://man7.org/linux/man-pages/man3/syslog.3.html) "szint" szakaszát a teljes listához. A bővítmény rögzíti a létesítménynek a megadott szinten vagy felett eljuttatott eseményeket.

Ha megadja `syslogEvents` , a Lad mindig az Azure Storage-ban lévő táblába ír egy adatot. A JSON-blobokban és/vagy Event Hubsban is megadhatja ugyanazokat az adatfájlokat, de nem tilthatja le az adattárolást egy táblába. Az ehhez a táblához tartozó particionálási viselkedés megegyezik a című témakörben leírtak szerint `performanceCounters` . A tábla neve a következő sztringek összefűzése:

* `LinuxSyslog`
* Egy "ÉÉÉÉHHNN" formátumú dátum, amely 10 naponta változik

Ilyenek például `LinuxSyslog20170410` a és a `LinuxSyslog20170609` .

### <a name="perfcfg"></a>perfCfg

Ez a választható szakasz vezérli a [tetszőleges típusú](https://github.com/Microsoft/omi) adatlekérdezések végrehajtását.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Elem | Érték
------- | -----
névtér | választható Az a-t tartalmazó-névtér, amelyen belül a lekérdezés végrehajtása történik. Ha nincs megadva, az alapértelmezett érték a [System Center platformfüggetlen szolgáltatók](https://github.com/Microsoft/SCXcore)által megvalósított "root/SCX".
lekérdezés | A végrehajtandó a kipróbálható adatlekérdezés.
tábla | választható Az Azure Storage-tábla a kijelölt Storage-fiókban (lásd a [védett beállításokat](#protected-settings)).
frequency | választható A lekérdezés végrehajtása közötti másodpercek száma. Az alapértelmezett érték 300 (5 perc); a minimális érték 15 másodperc.
fogadóként | választható A további mosdók neveinek vesszővel tagolt listája, amelybe a nyers minta metrikájának eredményeit közzé kell tenni. A nyers minták összesítését a bővítmény vagy az Azure-metrika számítja ki.

Meg kell adni a "Table" vagy a "mosogató", vagy mindkettőt.

### <a name="filelogs"></a>fileLogs

A naplófájlok rögzítését vezérli. A LAD rögzíti az új szövegsorok írását a fájlba, és a táblázat soraiba és/vagy bármely megadott mosogatóba (JsonBlob vagy EventHub) írja azokat.

> [!NOTE]
> a fileLogs a LAD nevű alösszetevő rögzíti `omsagent` . A fileLogs összegyűjtéséhez győződjön meg arról, hogy a `omsagent` felhasználó rendelkezik olvasási engedéllyel a megadott fájlokhoz, valamint a fájl elérési útjának összes könyvtárára vonatkozó engedélyek végrehajtásához. Ezt úgy is megtekintheti, hogy a `sudo su omsagent -c 'cat /path/to/file'` Lad telepítése után fut.

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Elem | Érték
------- | -----
file | A figyelni és rögzíteni kívánt naplófájl teljes elérési útja. Az elérési útnak egyetlen fájlt kell megadnia; nem lehet könyvtárat átnevezni, és nem tartalmazhat helyettesítő karaktereket. A "omsagent" felhasználói fióknak olvasási hozzáféréssel kell rendelkeznie a fájl elérési útjához.
tábla | választható Az Azure Storage-tábla a kijelölt Storage-fiókban (a védett konfigurációban megadott módon), amelybe a fájl "farok" új sorai íródnak.
fogadóként | választható Vesszővel tagolt lista azoknak a további mosogatóknak a neveiről, amelyeknek a naplózási sorai elküldése megtörténjen.

Meg kell adni a "Table" vagy a "mosogató", vagy mindkettőt.

## <a name="metrics-supported-by-the-builtin-provider"></a>A beépített szolgáltató által támogatott metrikák

A beépített metrikai szolgáltató a felhasználók széles köréhez legérdekesebb mérőszámok forrása. Ezek a metrikák öt átfogó osztályba sorolhatók:

* Processzor
* Memória
* Network (Hálózat)
* Fájlrendszer
* Lemez

### <a name="builtin-metrics-for-the-processor-class"></a>a processzor osztályának beépített metrikái

A metrikák processzor-osztálya információt nyújt a virtuális gép processzor-használatáról. A százalékok összesítése során az eredmény az összes processzor átlaga. Két vCPU virtuális gépen, ha egy vCPU 100%-ban foglalt, míg a másik a 100%-os üresjáratban volt, a jelentett PercentIdleTime a következő lesz: 50. Ha az egyes vCPU ugyanazon időszakra vonatkozóan 50%-ban foglalt, akkor a jelentett eredmény a következő lesz: 50. Egy négy vCPU virtuális gépen egy vCPU 100%-os foglalt és a többi tétlen, a jelentett PercentIdleTime a következő lesz: 75.

számláló | Értelmezés
------- | -------
PercentIdleTime | Az összesítési időszakban a kernel üresjárati ciklusát végrehajtó munkafolyamatok százalékos aránya
PercentProcessorTime | Nem tétlen szálat végrehajtó idő százalékos aránya
PercentIOWaitTime | Az i/o-műveletek befejezésére várakozási idő százalékos aránya
PercentInterruptTime | Hardver-/szoftver-megszakítások és-DPC végrehajtási idejének százalékos aránya (késleltetett eljárási hívások)
PercentUserTime | A nem tétlen idő az összesítési időszakban, a felhasználó által a normál prioritásban eltöltött idő százalékos aránya
PercentNiceTime | A nem tétlen időpontig a lecsökkentett (Nizza) prioritásban eltöltött százalékarány
PercentPrivilegedTime | A nem üresjárati idő, a rendszerjogosultságú (kernel) módban eltöltött százalék

Az első négy számláló összege 100%. Az utolsó három számláló a 100%-ot is összegzi; a PercentProcessorTime, a PercentIOWaitTime és a PercentInterruptTime összegét osztják meg.

Ha egyetlen mérőszámot szeretne beolvasni az összes processzor között, állítsa be a következőt: `"condition": "IsAggregate=TRUE"` . Egy adott processzor metrikájának beszerzéséhez, például egy négy vCPU virtuális gép második logikai processzorához, állítsa be a következőt: `"condition": "Name=\\"1\\""` . A logikai processzorok száma a tartományban van `[0..n-1]` .

### <a name="builtin-metrics-for-the-memory-class"></a>beépített metrikák a memória osztályhoz

A metrikák memória osztálya információt nyújt a memória kihasználtságáról, a lapozásról és a cseréről.

számláló | Értelmezés
------- | -------
AvailableMemory | Rendelkezésre álló fizikai memória a MiB-ben
PercentAvailableMemory | Rendelkezésre álló fizikai memória a teljes memória százalékában
UsedMemory | Használatban lévő fizikai memória (MiB)
PercentUsedMemory | Használatban lévő fizikai memória a teljes memória százalékában
PagesPerSec | Lapozófájlok összesen (olvasás/írás)
PagesReadPerSec | Az áruházból beolvasott lapok (fájl, programfájl, leképezett fájl stb.)
PagesWrittenPerSec | Az áruházba írt lapok (fájl cseréje, leképezett fájl stb.)
AvailableSwap | Nem használt lapozófájl (MiB)
PercentAvailableSwap | Fel nem használt swap-terület a teljes swap százalékaként
UsedSwap | Használatban lévő swap-terület (MiB)
PercentUsedSwap | Használatban lévő swap-terület a teljes swap százalékaként

A metrikák ezen osztálya csak egyetlen példánnyal rendelkezik. A "Condition" attribútum nem rendelkezik hasznos beállításokkal, és el kell hagyni.

### <a name="builtin-metrics-for-the-network-class"></a>a hálózati osztály beépített metrikái

A metrikák hálózati osztálya a rendszerindítás óta információt nyújt az egyes hálózati adapterek hálózati tevékenységéről. A LAD nem tesz elérhetővé sávszélesség-metrikákat, amelyek a gazdagép metrikái alapján kérhetők le.

számláló | Értelmezés
------- | -------
BytesTransmitted | A rendszerindítás óta elindított bájtok száma összesen
BytesReceived | A rendszerindítás óta fogadott bájtok összesen
BytesTotal | A rendszerindítás óta küldött vagy fogadott bájtok összesen
PacketsTransmitted | A rendszerindítás óta küldött csomagok összesen
PacketsReceived | A rendszerindítás óta fogadott csomagok összesen
TotalRxErrors | Fogadási hibák száma a rendszerindítás óta
TotalTxErrors | Küldési hibák száma a rendszerindítás óta
TotalCollisions | A hálózati portok által a rendszerindítás óta jelentett ütközések száma

 Bár ez az osztály példányt tartalmaz, a LAD nem támogatja az összes hálózati eszközre összesített hálózati metrikák rögzítését. Egy adott csatoló (például ETH0) metrikáinak beszerzéséhez állítsa be a következőt: `"condition": "InstanceID=\\"eth0\\""` .

### <a name="builtin-metrics-for-the-filesystem-class"></a>a fájlrendszer osztályának beépített metrikái

A metrikák fájlrendszer-osztálya információt nyújt a fájlrendszer használatáról. Az abszolút és a százalékos értékek a jelentések szerint a szokásos felhasználó számára (nem root) jelennek meg.

számláló | Értelmezés
------- | -------
FreeSpace | Rendelkezésre álló lemezterület bájtban
UsedSpace | Felhasznált lemezterület bájtban
PercentFreeSpace | Szabad terület százalékos aránya
PercentUsedSpace | Felhasznált terület százalékos aránya
PercentFreeInodes | A fel nem használt inode százaléka
PercentUsedInodes | Az összes fájlrendszerben összefoglalt lefoglalt (használatban lévő) inode százalékaránya
BytesReadPerSecond | Olvasott bájtok másodpercenként
BytesWrittenPerSecond | Másodpercenként írt bájtok száma
BytesPerSecond | Olvasott vagy írt bájtok másodpercenként
ReadsPerSecond | Olvasási műveletek másodpercenként
WritesPerSecond | Írási műveletek másodpercenként
TransfersPerSecond | Olvasási vagy írási műveletek másodpercenként

A rendszer az összes fájlrendszer összesített értékeit a beállítással szerezheti be `"condition": "IsAggregate=True"` . Az adott csatlakoztatott fájlrendszer, például a "/mnt" értékeit a beállítással lehet beolvasni `"condition": 'Name="/mnt"'` . 

> [!NOTE]
> Ha JSON helyett a Azure Portal használja, a helyes feltétel mező űrlap a name = '/mnt '.

### <a name="builtin-metrics-for-the-disk-class"></a>beépített metrikák a lemez osztályhoz

A metrikák lemezes osztálya információt nyújt a lemezes eszközök használatáról. Ezek a statisztikák a teljes meghajtóra vonatkoznak. Ha több fájlrendszer van egy eszközön, az eszköz számlálói hatékonyan, összesítve lesznek az összesre.

számláló | Értelmezés
------- | -------
ReadsPerSecond | Olvasási műveletek másodpercenként
WritesPerSecond | Írási műveletek másodpercenként
TransfersPerSecond | Műveletek másodpercenkénti száma
AverageReadTime | Olvasási művelet átlagos másodpercenkénti száma
AverageWriteTime | Írási művelet átlagos másodpercenkénti száma
AverageTransferTime | Művelet átlagos másodpercenkénti száma
AverageDiskQueueLength | A várólistán lévő lemezes műveletek átlagos száma
ReadBytesPerSecond | Olvasott bájtok másodpercenkénti száma
WriteBytesPerSecond | A másodpercenként írt bájtok száma
BytesPerSecond | Olvasott vagy írt bájtok másodpercenkénti száma

Az összes lemez összesített értékei a beállítás alapján szerezhetők be `"condition": "IsAggregate=True"` . Egy adott eszközre vonatkozó információk (például/dev/sdf1) lekéréséhez állítsa be a következőt: `"condition": "Name=\\"/dev/sdf1\\""` .

## <a name="installing-and-configuring-lad-30"></a>A LAD 3,0 telepítése és konfigurálása

### <a name="azure-cli"></a>Azure CLI

Ha azt feltételezi, hogy a védett beállítások szerepelnek a ProtectedSettings.jsfájlban, és a nyilvános konfigurációs adatok PublicSettings.jsbe van kapcsolva, futtassa ezt a parancsot:

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

A parancs feltételezi, hogy az Azure CLI Azure Resource Management üzemmódját használja. A klasszikus üzembe helyezési modell (ASM) virtuális gépei konfigurálásához váltson az "ASM" módra ( `azure config mode asm` ), és hagyja ki az erőforráscsoport nevét a parancsban. További információkért lásd a [többplatformos CLI dokumentációját](/cli/azure/authenticate-azure-cli).

### <a name="powershell"></a>PowerShell

Feltételezve, hogy a védett beállítások szerepelnek a `$protectedSettings` változóban, és a nyilvános konfigurációs adatok szerepelnek a `$publicSettings` változóban, futtassa a következő parancsot:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="an-example-lad-30-configuration"></a>Példa LAD 3,0-konfigurációra

Az előző definíciók alapján Íme egy példa a 3,0-es, néhány magyarázattal ellátott bővítmény-konfigurációra. Ha alkalmazni szeretné a mintát az esetére, használja a saját Storage-fiók nevét, a fiók SAS-tokenjét és a EventHubs SAS-tokeneket.

> [!NOTE]
> Attól függően, hogy az Azure CLI vagy a PowerShell használatával telepíti a LAD-t, a nyilvános és védett beállítások biztosításának módszere eltérő lesz. Ha az Azure CLI-t használja, mentse a következő beállításokat ProtectedSettings.jsbe és PublicSettings.jsbe a parancsot a fenti minta paranccsal való használatra. Ha a PowerShellt használja, mentse a beállításokat a és a parancs `$protectedSettings` `$publicSettings` futtatásával `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Védett beállítások

A következő védett beállítások konfigurálása:

* egy Storage-fiók
* egy megfelelő fiók SAS-jogkivonata
* több mosogató (JsonBlob vagy EventHubs SAS-tokenekkel)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>Nyilvános beállítások

Ezek a nyilvános beállítások a következőt okozzák:

* Százalék-processzor-idő és felhasznált lemezterület-metrikák feltöltése a `WADMetrics*` táblába
* Üzenetek feltöltése a syslog-létesítményből a "user" és a "fontosság" információkkal a `LinuxSyslog*` táblába
* Nyers PercentProcessorTime-és PercentIdleTime-lekérdezési eredmények feltöltése a nevesített `LinuxCPU` táblázatba
* A fájlban lévő hozzáfűzött sorok feltöltése `/var/log/myladtestlog` a `MyLadTestLog` táblába

A rendszer minden esetben feltölti az adatfájlokat a következőre:

* Azure Blob Storage (a tároló neve a JsonBlob-fogadóban definiált)
* EventHubs-végpont (a EventHubs-fogadóban megadott módon)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

A `resourceId` konfigurációnak meg kell egyeznie a virtuális gép vagy a virtuálisgép-méretezési csoport nevével.

* Az Azure platform metrikái a diagramok és a riasztások megismerik annak a virtuális gépnek a resourceId, amelyen dolgozik. A rendszer a resourceId a keresési kulcs használatával keresi meg a virtuális gép adatait.
* Ha az Azure automatikus méretezést használja, az automatikus skálázási konfiguráció resourceId meg kell egyeznie a LAD által használt resourceId.
* A resourceId a LAD által írt JsonBlobs nevére épül.

## <a name="view-your-data"></a>Adatok megtekintése

A Azure Portal használatával megtekintheti a teljesítményadatokat, vagy beállíthatja a riasztásokat:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Képernyőfelvétel: az Azure Portal, amely a felhasznált lemezterületet mutatja a metrika kiválasztott és az eredményül kapott diagramon.":::

Az `performanceCounters` adattárolást mindig egy Azure Storage-táblában tárolja a rendszer. Az Azure Storage API-jai számos nyelven és platformon elérhetők.

A JsonBlob elküldhető adattárolók a [védett beállítások](#protected-settings)nevű Storage-fiókban található blobokban vannak tárolva. A Blobok adatai bármely Azure Blob Storage API használatával felhasználhatók.

Emellett használhatja ezeket a felhasználói felületi eszközöket az Azure Storage-ban tárolt adatok eléréséhez:

* A Visual Studio Server Explorer.
* [A képernyőképen a Azure Storage Explorer tárolók és táblák láthatók.](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer")

A Microsoft Azure Storage Explorer-munkamenet ezen pillanatképe a generált Azure Storage-táblákat és-tárolókat mutatja be egy, a teszt virtuális gépen megfelelően konfigurált, LAD 3,0-es bővítménnyel. A rendszerkép nem egyezik pontosan a [LAD 3,0-konfigurációval](#an-example-lad-30-configuration).

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="A képernyőképen a Azure Storage Explorer látható.":::


A EventHubs-végponton közzétett üzenetek felhasználásának megismeréséhez tekintse meg a vonatkozó [EventHubs dokumentációját](../../event-hubs/event-hubs-about.md) .

## <a name="next-steps"></a>Következő lépések

* Metrikai riasztások létrehozása [Azure monitorban](../../azure-monitor/platform/alerts-classic-portal.md) a begyűjtött mérőszámokhoz.
* [Figyelési diagramok](../../azure-monitor/platform/data-platform.md) létrehozása a mérőszámokhoz.
* Megtudhatja, hogyan [hozhat létre virtuálisgép-méretezési készletet](../linux/tutorial-create-vmss.md) a metrikák használatával az automatikus skálázás szabályozásához.
