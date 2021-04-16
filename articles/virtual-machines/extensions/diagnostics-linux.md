---
title: Azure Compute – Linux 4.0-s diagnosztikai bővítmény
description: Az Azure Linux diagnosztikai bővítmény (STB) 4.0 konfigurálása metrikák és naplóesemények gyűjtésére az Azure-ban futó Linux rendszerű virtuális gépekről.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/05/2021
ms.openlocfilehash: 2e862915bcc524db50e7e66c969b713f729c64aa
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479644"
---
# <a name="use-the-linux-diagnostic-extension-40-to-monitor-metrics-and-logs"></a>Metrikák és naplók figyelése a Linux 4.0-s diagnosztikai bővítményével

Ez a dokumentum a Linux diagnosztikai bővítmény (BACKUP) legújabb verzióit ismerteti.

> [!IMPORTANT]
> További információ a 3.x verzióról:  [A 3.0-s Linux](./diagnostics-linux-v3.md)diagnosztikai bővítmény használata metrikák és naplók figyelése. További információ a 2.3-as és korábbi verziókról: Linux rendszerű virtuális gépek teljesítmény- és [diagnosztikai adatainak figyelése.](https://docs.microsoft.com/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)

## <a name="introduction"></a>Bevezetés

A Linux diagnosztikai bővítménye segít a felhasználónak a virtuális gépen futó Linux rendszerű virtuális gépek Microsoft Azure. A következő képességekkel rendelkezik:

* Gyűjti a virtuális gép rendszerteljesítmény-mérőszámát, és egy meghatározott táblában tárolja őket egy kijelölt tárfiókban.
* Lekéri a naplóeseményeket a syslogból, és egy adott táblában tárolja őket a kijelölt tárfiókban.
* Lehetővé teszi a felhasználók számára a gyűjtött és feltöltött adatmetrikák testreszabását.
* Lehetővé teszi, hogy a felhasználók testre szabják a rendszernapló-létesítményeket és a gyűjtött és feltöltött események súlyossági szintjeit.
* Lehetővé teszi, hogy a felhasználók megadott naplófájlokat töltsenek fel egy kijelölt tárolótáblába.
* Támogatja a metrikák és naplóesemények küldését a kijelölt tárfiók tetszőleges EventHub-végpontjaira és JSON-formátumú blobjaira.

Ez a bővítmény mindkét Azure-beli üzembe helyezési modellel működik.

## <a name="install-the-extension-on-a-vm"></a>A bővítmény telepítése virtuális gépre

Ezt a bővítményt az Azure PowerShell parancsmagok, Azure CLI-szkriptek, Azure Resource Manager sablonok (ARM-sablonok) vagy a Azure Portal. További információ: [Bővítmények és funkciók.](features-linux.md)

>[!NOTE]
>A Linux diagnosztikai virtuálisgép-bővítmény egyes összetevőit a Log Analytics virtuálisgép-bővítmény [is tartalmazza.](./oms-linux.md) Az architektúra miatt ütközések merülhetnek fel, ha mindkét bővítmény példányosul ugyanabban az ARM-sablonban. 
>
>A telepítéssel kapcsolatos ütközések [ `dependsOn` ](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) elkerülése érdekében a bővítmények egymás után való telepítéséhez használja a irányelvet. A bővítmények mindkét sorrendben telepíthetők.

A telepítési utasítások és egy letölthető mintakonfiguráció [használatával](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) konfigurálja a FOGÉK 4.0-t a következőre:

* Rögzítse és tárolja a MEGADOTT 2.3-as és 3.x-es VERZIÓk metrikákat.
* Küldjön metrikákat a Azure Monitor fogadóba a szokásos fogadóval együtt az Azure Storage-ba. Ez a funkció az ÚJ AZ 4.0-s és újabb.
* A fájlrendszer-metrikák hasznos készletének rögzítése, ahogyan az a KÖVETKEZŐben is található: A<
* Rögzítse a SYSLOG 2.3 által engedélyezett alapértelmezett syslog-gyűjteményt.
* A virtuális Azure Portal és riasztások diagramozásának és riasztásának engedélyezése.

A letölthető konfiguráció csak egy példa. Igény szerint módosíthatja.

### <a name="supported-linux-distributions"></a>Támogatott Linux-disztribúciók

A Linux diagnosztikai bővítmény számos disztribúciót és verziót támogat. A következő disztribúciók és verziók listája csak az Azure által támogatott Linux-gyártói rendszerképekre vonatkozik. A bővítmény általában nem támogatja a külső gyártótól származó BYOL- és BYOS-rendszerképeket, például a berendezéseket.

A csak a főverziókat felsoroló disztribúciók, például a Debian 7, szintén támogatottak minden alverzióhoz. Ha egy adott alverzió van megadva, csak ez a verzió támogatott. Pluszjel (+) hozzáfűzése esetén a megadott verzióval egyenlő vagy annál újabb alverziók támogatottak.

Támogatott disztribúciók és verziók:

- Ubuntu 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7+

### <a name="prerequisites"></a>Előfeltételek

* **Az Azure Linux-ügynök 2.2.0-s vagy újabb verziója.** A legtöbb Azure-beli virtuális gép Linux-katalógusának rendszerképe tartalmazza a 2.2.7-es vagy újabb verziót. A `/usr/sbin/waagent -version` virtuális gépre telepített verzió megerősítéséhez futtassa a következőt: . Ha a virtuális gép a vendégügynök egy régebbi verzióját futtatja, frissítse [a Linux-ügynököt.](./update-linux-agent.md)
* **Azure CLI**. [Állítsa be az Azure CLI-környezetet](/cli/azure/install-azure-cli) a gépen.
* **A `wget` parancs.** Ha még nem rendelkezik vele, futtassa az `sudo apt-get install wget` -t.
* **Egy Azure-előfizetés és egy általános célú tárfiók** az adatok tárolásához.  Az általános célú tárfiókok támogatják a table storage-et, ami szükséges.  A Blob Storage-fiók nem fog működni.
* **Python 2**.

### <a name="python-requirement"></a>Python-követelmény

A Linux diagnosztikai bővítményéhez Python 2 szükséges. Ha a virtuális gép olyan disztribúciót használ, amely alapértelmezés szerint nem tartalmazza a Python 2-t, telepítse. 

Az alábbi mintaparancsok különböző disztribúciókra telepítik a Python 2-t:    

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- Suse: `zypper install -y python2`

A `python2` végrehajtható fájlnak a python aliasnevűnek *kell lennie.* Az alias beállításának egyik módja a következő:

1. A meglévő aliasok eltávolításához futtassa a következő parancsot.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Futtassa a következő parancsot az alias létrehozásához.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Mintatelepítés

> [!NOTE]
> Az alábbi minták esetében a kód futtatása előtt töltse ki az első szakaszban található változók megfelelő értékeit. 

Ezekben a példákban a mintakonfiguráció standard adatok egy halmazát gyűjti össze, és elküldi őket a Table Storage-nak. A mintakonfiguráció URL-címe és tartalma változhat. 

A legtöbb esetben le kell töltenie a portál beállításI JSON-fájljának másolatát, és testre kell szabni az igényeinek megfelelően. Ezután sablonok vagy saját automatizálás használatával a konfigurációs fájl testreszabott verzióját használhatja ahelyett, hogy minden alkalommal letölti az URL-címről.

> [!NOTE]
> Ha engedélyezi az új Azure Monitor fogadót, a virtuális gépeken engedélyezve kell lennie a rendszer által hozzárendelt identitásnak a Felügyeltszolgáltatás-identitás (MSI) hitelesítési jogkivonatok létrehozásához. Ezeket a beállításokat a virtuális gép létrehozása során vagy után használhatja. 
>
> A felügyelt identitások, Azure Portal Azure CLI, PowerShell és Azure Resource Manager lásd: [Felügyelt identitások konfigurálása.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) 



#### <a name="azure-cli-sample"></a>Azure CLI-minta

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Enable system-assigned identity on the existing VM.
az vm identity assign -g $my_resource_group -n $my_linux_vm

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace the storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-40-on-a-virtual-machine-scale-set-instance"></a>Azure CLI-minta a SCALE 4.0 virtuálisgép-méretezésikészlet-példányra való telepítéséhez

```azurecli
# Set your Azure virtual machine scale set diagnostic variables. 
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Enable system-assigned identity on the existing virtual machine scale set.
az vmss identity assign -g $my_resource_group -n $my_linux_vmss

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the virtual machine scale set resource ID. Replace the storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>PowerShell-minta

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Enable system-assigned identity on an existing VM
Update-AzVM -ResourceGroupName $VMresourceGroup -VM $vm -IdentityType SystemAssigned

# Get the public settings template from GitHub and update the templated values for the storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0 
```

### <a name="update-the-extension-settings"></a>A bővítmény beállításainak frissítése

A védett vagy nyilvános beállítások módosítása után telepítse őket a virtuális gépre ugyanaz a parancs futtatásával. Ha bármely beállítás módosult, a rendszer elküldi a frissítéseket a bővítménynek. A MIND újra betölti a konfigurációt, és újraindítja magát.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Áttelepítés a bővítmény korábbi verzióiból

A bővítmény legújabb verziója *a 4.0-s verzió, amely jelenleg nyilvános előzetes verzióban érhető el.* A 3.x régebbi verziói továbbra is támogatottak. A 2.x verziók azonban 2018. július 31. óta elavultak.

> [!IMPORTANT]
> A 3.x verzióról a bővítmény legújabb verziójára való áttelepítéshez távolítsa el a régi bővítményt. Ezután telepítse a 4-es verziót, amely tartalmazza a rendszer által hozzárendelt identitás és fogadó frissített konfigurációját, hogy metrikákat Azure Monitor fogadónak.

Az új bővítmény telepítésekor engedélyezze az alverziók automatikus frissítését:
* A klasszikus üzembe helyezési modellben lévő virtuális gépeken adja meg a verziót, ha a bővítményt az Azure Xplat parancssori felület vagy `4.*` a PowerShell használatával telepíti.
* Az Azure Resource Manager modell virtuális gépei esetében foglalja bele a következőket `"autoUpgradeMinorVersion": true` a virtuálisgép-üzembehelyhelyező sablonba.

Használhatja ugyanazt a tárfiókot, mint amit a AZS3.x-hez is használt. 

## <a name="protected-settings"></a>Védett beállítások

Ez a konfigurációs információkészlet bizalmas adatokat tartalmaz, amelyek nyilvános nézettől védve lesznek. Tartalmazza például a tárolási hitelesítő adatokat. A beállításokat a bővítmény titkosított formában továbbítja és tárolja.

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
storageAccountName | Annak a tárfióknak a neve, amelybe a bővítmény adatokat ír.
storageAccountEndPoint | (Nem kötelező) A tárfiókot tároló felhőt azonosító végpont. Ha ez a beállítás nincs meg, alapértelmezés szerint a AZ AZURE nyilvános felhőt () `https://core.windows.net` használja. Ha azure germanyi, Azure Government vagy Azure China 21Vianet azure-beli tárfiókot is használnia kell, állítsa be ezt az értéket.
storageAccountSasToken (storageAccountSasToken) | Fiók [SAS-jogkivonat a](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) blob- és táblaszolgáltatásokhoz ( `ss='bt'` ). Ez a jogkivonat a tárolókra és objektumokra ( `srt='co'` ) vonatkozik. Hozzáadási, létrehozási, listához, frissítési és írási engedélyeket `sp='acluw'` () biztosít. Ne *tartalmazza* a kezdő kérdőjelet (?).
mdsdHttpProxy | (Nem kötelező) HTTP-proxyinformációk, amelyekre a bővítménynek a megadott tárfiókhoz és végponthoz kell csatlakoznia.
sinksConfig | (Nem kötelező) Az alternatív célhelyekkel kapcsolatos részletek, amelyekre a metrikák és események kézbesítve lesznek. A következő szakaszok részletesen ismertetik a bővítmény által támogatott adat fogadók adatait.

AZ ARM-sablonon belüli SAS-jogkivonatok lekért használhatja a `listAccountSas` függvényt. Példasablonért lásd a [példalista függvényét.](../../azure-resource-manager/templates/template-functions-resource.md#list-example)

A szükséges SAS-jogkivonatot a következő módon Azure Portal:

1. Válassza ki azt az általános célú tárfiókot, amelybe a bővítményt írni szeretné.
1. A bal oldali menü Beállítások menüjében **válassza** a **Közös hozzáférésű jogosultság aláírása lehetőséget.**
1. A beállításokat a korábban leírtak szerint kell tenni.
1. Válassza a **SAS létrehozása lehetőséget.**

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Képernyőkép a Közös hozzáférésű jogosultság aláírása lapról az S A S létrehozása gombbal.":::

Másolja a létrehozott SAS-t a `storageAccountSasToken` mezőbe. Távolítsa el a kezdő kérdőjelet (?).

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

A `sinksConfig` választható szakasz további célhelyeket határoz meg, amelyekre a bővítmény elküldi az összegyűjtött adatokat. A `"sink"` tömb minden további adat fogadóhoz tartalmaz egy objektumot. Az `"type"` attribútum határozza meg az objektum többi attribútumát.

Elem | Érték
------- | -----
name | A fogadóra a bővítménykonfigurációban máshol hivatkozó sztring.
típus | A definiált fogadó típusa. Az ilyen típusú példányok egyéb értékeit határozza meg (ha vannak).

A Linux diagnosztikai bővítmény 4.0 két fogadótípust támogat: `EventHub` és `JsonBlob` .

#### <a name="eventhub-sink"></a>EventHub-fogadó

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

A bejegyzés tartalmazza annak az eseményközpontnak a teljes URL-címét, beleértve az SAS-jogkivonatot is, amelyen az `"sasURL"` adatokat közzé kell tenni. A RENDSZER-nek SAS-re van szüksége a küldési jogcímet lehetővé tő szabályzat elnevezéséhez. Bemutatunk egy példát:

* Hozzon létre Event Hubs nevű `contosohub` névteret.
* Hozzon létre egy nevű eseményközpontot a `syslogmsgs` névtérben.
* Hozzon létre egy megosztott hozzáférési szabályzatot a nevű eseményközpontban, amely engedélyezi a `writer` küldési jogcímet.

Ha 2018. január 1-jén éjfélig (UTC) megfelelő SAS-t hozott létre, az érték az alábbi `sasURL` példához hasonló lehet.

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

További információ a sas-jogkivonatokkal kapcsolatos információk létrehozásáról és Event Hubs: [SAS-jogkivonat létrehozása.](/rest/api/eventhub/generate-sas-token#powershell)

#### <a name="jsonblob-sink"></a>JsonBlob-fogadó

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

A fogadóhoz irányított adatok blobban vannak tárolva `JsonBlob` az Azure Storage-ban. A BLOB minden példánya óránként létrehoz egy blobot minden fogadónévhez. Minden blob mindig tartalmaz egy szintaktikailag érvényes JSON-objektumtömböt. Az új bejegyzések atomikusan hozzá vannak adva a tömbhöz. 

A blobok tárolása egy tárolóban történt, amely ugyanazokkal a neveivel rendelkezik, mint a fogadó. A blobtárolók nevére vonatkozó Azure Storage-szabályok a fogadók `JsonBlob` nevére vonatkoznak. Ez azt jelenti, hogy a nevek 3–63 kisbetűs alfanumerikus ASCII-karakterből vagy kötőjelből állhatnak.

## <a name="public-settings"></a>Nyilvános beállítások

A nyilvános beállítások struktúrája különböző beállításblokkokat tartalmaz, amelyek a bővítmény által gyűjtött információkat szabályják. A beállítás kivételével minden `ladCfg` beállítás megadása nem kötelező. Ha a -ban adja meg a metrika- vagy syslog-gyűjteményt, akkor a értéket `ladCfg` is meg kell `StorageAccount` adnia. Meg kell adnia a `sinksConfig` elemet, amely engedélyezi a Azure Monitor FOGADÓt a METRIK 4.0-tól származó metrikákhoz.

```json
{
    "ladCfg":  { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "sinksConfig": { ... },
    "mdsdHttpProxy" : ""
}
```

Elem | Érték
------- | -----
StorageAccount | Annak a tárfióknak a neve, amelyben a bővítmény adatokat ír. A védett beállításokban megadott [névnek kell lennie.](#protected-settings)
mdsdHttpProxy | (Nem kötelező) A védett beállításokban [megadott proxy.](#protected-settings) Ha a privát érték be van állítva, felülírja a nyilvános értéket. A védett beállítások között helyezze el a titkos adatokat tartalmazó proxybeállításokat, például a [jelszót.](#protected-settings)

A következő szakaszok a többi elem részleteit ismertetik.

### <a name="ladcfg"></a>cfCfg

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

A struktúra vezérli a metrikák és naplók gyűjtését a Azure Monitor Metrics szolgáltatásba és más `ladCfg` adatgyűjtőkbe való kézbesítéshez. Adja meg vagy `performanceCounters` `syslogEvents` mindkettőt. Adja meg a `metrics` struktúrát is.

Ha nem szeretné engedélyezni a rendszernapló- vagy metrikagyűjtést, adjon meg egy üres struktúrát az `ladCfg` elemhez, az alábbi példához hasonlóan: 

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {}
    }
```

Elem | Érték
------- | -----
eventVolume (eseménykötet) | (Nem kötelező) A tárolótáblában létrehozott partíciók számát szabályozza. Az értéknek `"Large"` , `"Medium"` vagy értékűnek kell `"Small"` lennie. Ha az érték nincs megadva, az alapértelmezett érték `"Medium"` .
sampleRateInSeconds (SampleRateInSeconds) | (Nem kötelező) A nyers (nem aggregált) metrikák gyűjteménye közötti alapértelmezett időköz. A legkisebb támogatott mintaarány 15 másodperc. Ha az érték nincs megadva, az alapértelmezett érték `15` a .

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
resourceId | A Azure Resource Manager virtuális gép vagy annak a virtuálisgép-méretezési csoportnak az erőforrás-azonosítója, amelyhez a virtuális gép tartozik. Akkor is adja meg ezt a beállítást, ha a konfiguráció bármilyen `JsonBlob` fogadót használ.
scheduledTransferPeriod | Az összesített metrikák kiszámításának és a metrikákba való Azure Monitor gyakorisága. A gyakoriság az IS 8601 időintervallumban van kifejezve. A legkisebb átviteli időszak 60 másodperc, azaz PT1M. Adjon meg legalább egy `scheduledTransferPeriod` értéket.

A szakaszban megadott metrikák mintáit a rendszer 15 másodpercenként vagy a számlálóhoz explicit módon meghatározott mintavételi sebességgel `performanceCounters` gyűjti. Ha több gyakoriság is megjelenik, ahogyan az a példában is látható, minden összesítés egymástól `scheduledTransferPeriod` függetlenül lesz kiszámítva.

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

A `performanceCounters` választható szakasz a metrikák gyűjteményét szabályozza. A nyers minták összesítése mindegyikhez a [`scheduledTransferPeriod`](#metrics) következő értékek előállításához szükséges:

* Középérték
* Minimum
* Maximum
* Legutóbb gyűjtött érték
* Az összesítés kiszámításához használt nyers minták száma

Elem | Érték
------- | -----
Mosogató | (Nem kötelező) Azoknak a fogadóknak a vesszővel elválasztott listája, amelyekhez a SINK összesített metrikaeredményeket küld. Minden összesített metrika közzé vantéve minden felsorolt fogadón. Példa: `"EHsink1, myjsonsink"`. További információ: [`sinksConfig`](#sinksconfig). 
típus | A metrika tényleges szolgáltatóját azonosítja.
osztály | A és `"counter"` a együttesen azonosítja az adott metrikát a szolgáltató névterében.
számláló | A és `"class"` a együttesen azonosítja az adott metrikát a szolgáltató névterében.
counterSpecifier (számláló-specifikus) | A Metrikák névterében Azure Monitor adott metrikát azonosítja.
Feltétel | (Nem kötelező) Kiválasztja annak az objektumnak egy példányát, amelyre a metrika vonatkozik. Vagy kiválasztja az adott objektum összes példányának összesítését. 
sampleRate | Az IS 8601 intervallum, amely beállítja a nyers minták gyűjtésének sebességét ehhez a metrikhoz. Ha az érték nincs beállítva, a gyűjtemény időközét a értéke adja [`sampleRateInSeconds`](#ladcfg) meg. A legrövidebb támogatott mintaarány 15 másodperc (PT15S).
egység | Meghatározza a metrika egységét. A következő sztringek egyikének kell lennie: `"Count"` , , , , , , `"Bytes"` `"Seconds"` `"Percent"` `"CountPerSecond"` `"BytesPerSecond"` `"Millisecond"` . Az összegyűjtött adatok fogyasztói arra számítanak, hogy az összegyűjtött adatértékek megfelelnek ennek a egységnek. A IGNORE figyelmen kívül hagyja ezt a mezőt.
displayName | A metrikákban található adatokhoz csatolni Azure Monitor címkét. Ez a címke a társított területi beállítás által megadott nyelven van megadva. A IGNORE figyelmen kívül hagyja ezt a mezőt.

A `counterSpecifier` egy tetszőleges azonosító. A metrikák felhasználói, például a Azure Portal és a riasztási funkció, a "kulcsot" használják, amely egy metrikát vagy egy metrika egy `counterSpecifier` példányát azonosítja. 

A `builtin` metrikákhoz olyan értékeket `counterSpecifier` javasolunk, amelyek a következővel `/builtin/` kezdődnek: . Ha egy metrika egy adott példányát gyűjti be, csatolja a példány azonosítóját az `counterSpecifier` értékhez. Íme néhány példa:

* `/builtin/Processor/PercentIdleTime` – Üresjárati idő átlaga az összes vCPU-ben
* `/builtin/Disk/FreeSpace(/mnt)` – Szabad terület a `/mnt` fájlrendszer számára
* `/builtin/Disk/FreeSpace` – Az összes csatlakoztatott fájlrendszerben átlagosan rendelkezésre álló szabad terület

Az AZURE PORTAL és a rendszer nem számít arra, hogy `counterSpecifier` az érték bármilyen mintával egyezni fog. Konzisztensnek kell lennie az értékek `counterSpecifier` felépítésében.

A megadásakor a RENDSZER MINDIG adatokat ír `performanceCounters` egy Azure Storage-beli táblába. Ugyanezek az adatok írhatóak JSON-blobokba, Event Hubs vagy mindkettőbe. Az adatok táblában való tárolását azonban nem tilthatja le. 

Az AZONOS tárfióknevet és -végpontot használó MINDEN PÉLDÁNY ugyanoda a táblába adja hozzá a metrikákat és a naplókat. Ha túl sok virtuális gép ír ugyanerre a táblapartícióra, az Azure képes az írási szolgáltatásokat az erre a partícióra lecsökkenteni. 

A beállítás hatására a bejegyzések `eventVolume` 1 (kicsi), 10 (közepes) vagy 100 (nagy) partíció között lesznek elosztva. A közepes partíciók általában elegendőek a forgalom szabályozásának elkerüléséhez. 

A Azure Monitor Metrika szolgáltatása Azure Portal a táblázatban található adatokat használja a gráfok előállításához vagy riasztások aktiválásához. A tábla neve a következő sztringek össze van ásva:

* `WADMetrics`
* A `"scheduledTransferPeriod"` táblában tárolt összesített értékekhez
* `P10DV2S`
* Egy "YYYYMMDD" alakban megált dátum, amely 10 naponta változik

Ilyen például a `WADMetricsPT1HP10DV2S20170410` és `WADMetricsPT1MP10DV2S20170609` a .

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

A `syslogEvents` választható szakasz a naplóesemények gyűjtését szabályozza a syslogból. Ha kihagyja a szakaszt, a rendszer egyáltalán nem rögzíti a syslog-eseményeket.

A `syslogEventConfiguration` gyűjtemény minden rendszernapló-létesítményhez egy-egy bejegyzést tartalmaz. Ha a egy adott létesítményhez készült, vagy ha az adott létesítmény egyáltalán nem jelenik meg a elemben, akkor az adott létesítményből származó `minSeverity` `"NONE"` eseményeket nem rögzíti a szolgáltatás.

Elem | Érték
------- | -----
Mosogató | Azoknak a fogadóknak a vesszővel elválasztott listája, amelyeken az egyes naplóesemények közzé vannak téve. A korlátozásainak megfelelő naplóesemények mindegyik felsorolt fogadón `syslogEventConfiguration` közzé vannak téve. Például: `"EHforsyslog"`
facilityName (létesítmény neve) | A rendszernapló-létesítmény neve, például `"LOG\_USER"` vagy `"LOG\_LOCAL0"` . További információkért lásd a syslog man oldalának "létesítmény" [szakaszát.](http://man7.org/linux/man-pages/man3/syslog.3.html)
minSeverity (minimális kitartás) | A rendszernapló súlyossági szintje, például `"LOG\_ERR"` vagy `"LOG\_INFO"` . További információért tekintse meg a syslog man oldalának ["level" (szint) szakaszát.](http://man7.org/linux/man-pages/man3/syslog.3.html) A bővítmény rögzíti a létesítménybe a megadott szinten vagy felett küldött eseményeket.

A megadásakor a RENDSZER MINDIG adatokat ír `syslogEvents` egy Azure Storage-beli táblába. Ugyanezek az adatok írhatóak JSON-blobokba, Event Hubs vagy mindkettőbe. Az adatok táblában való tárolását azonban nem tilthatja le. 

A tábla particionálási viselkedése megegyezik a következővel: `performanceCounters` . A tábla neve ezeknek a sztringeknak az össze concatenációja:

* `LinuxSyslog`
* Egy "YYYYMMDD" (YYYYMMDD) formában megált dátum, amely 10 naponta változik

Ilyen például a `LinuxSyslog20170410` és `LinuxSyslog20170609` a .

### <a name="sinksconfig"></a>sinksConfig

A választható szakasz lehetővé teszi metrikák küldését a Azure Monitor fogadónak a Storage-fiók és az alapértelmezett Vendégmetrikák `sinksConfig` panel mellett.

> [!NOTE]
> A szakaszhoz engedélyezni kell a rendszer által hozzárendelt identitást a virtuális gépeken vagy `sinksConfig` a virtuálisgép-méretezési csoporton. A rendszer által hozzárendelt identitást az Azure Portal, a parancssori felület, a PowerShell vagy a Azure Resource Manager. Kövesse a [részletes utasításokat,](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) vagy tekintse meg a cikkben található korábbi telepítési mintákat. 

```json
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
```


### <a name="filelogs"></a>fileLogs (fájlnaplók)

A `fileLogs` szakasz a naplófájlok rögzítését szabályozza. A RENDSZER rögzíti az új szövegsorokat, amikor azok a fájlba vannak írva. A tábla soraiba és/vagy bármely megadott fogadóba írja őket, például és `JsonBlob` `EventHub` .

> [!NOTE]
> `fileLogs`A-t a VAGY AD alkomponense rögzíti. `omsagent` A gyűjtéséhez győződjön meg arról, hogy a felhasználó olvasási engedélyekkel rendelkezik `fileLogs` `omsagent` a megadott fájlokhoz. Emellett végrehajtási engedélyekkel kell rendelkeznie a fájl elérési útjának összes könyvtárán. A LETT telepítése után a futtatásával ellenőrizheti az `sudo su omsagent -c 'cat /path/to/file'` engedélyeket.

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
file | A figyelni és rögzített naplófájl teljes elérési útja. Az elérési út neve egyetlen fájlhoz való. Nem nevezhet el könyvtárat, és nem tartalmazhat helyettesítő karaktereket. A `omsagent` felhasználói fióknak olvasási hozzáféréssel kell lennie a fájl elérési úthoz.
tábla | (Nem kötelező) Az Azure Storage-tábla, amelybe a fájl "faroka" új sorait írja a rendszer. A táblának a kijelölt tárfiókban kell lennie a védett konfigurációban megadottak szerint. 
Mosogató | (Nem kötelező) Azon fogadók vesszővel elválasztott listája, amelyekre a rendszer naplósorokat küld.

Vagy `"table"` `"sinks"` mindkettőt meg kell adni.

## <a name="metrics-supported-by-the-builtin-provider"></a>A beépített szolgáltató által támogatott metrikák

> [!NOTE]
> A RENDSZER által támogatott alapértelmezett metrikák összesítve vannak az összes fájlrendszerben, lemezen vagy névben. Nem aggregált metrikák esetén tekintse meg a fogadómetrikák Azure Monitor új metrikákat.

A metrikaszolgáltató olyan metrikák forrása, amelyek a legtöbb felhasználó számára `builtin` érdekesek. Ezek a metrikák öt széles osztályba tartoznak:

* Processzor
* Memória
* Network (Hálózat)
* Fájlrendszer
* Lemez

### <a name="builtin-metrics-for-the-processor-class"></a>beépített metrikák a Processor osztályhoz

A metrikák processzorosztálya a virtuális gép processzorhasználati információit biztosítja. A százalékos arányok összesítése esetén az eredmény az összes CPU átlaga. 

Két virtuális gép esetén, ha az egyik vCPU 100%-ban foglalt, a másik 100%-ban tétlen, a jelentett teljesítmény `PercentIdleTime` 50. Ha minden vCPU 50%-ban foglalt ugyanabban az időszakban, a jelentett eredmény is 50. Ha egy négy vCPU-s virtuális gépen az egyik vCPU 100%-ban foglalt, a többi pedig tétlen, a jelentett teljesítmény `PercentIdleTime` 75.

Számláló | Értelmezés
------- | -------
PercentIdleTime (PercentIdleTime) | Az aggregáció ablakában a processzorok által futtatott üresjárati kernelhurok idejének százalékos aránya
PercentProcessorTime | Nem üresjárati szál futtatásának százalékos aránya
PercentIOWaitTime | Az I/O-műveletek befejezésére való várakozás százalékos aránya
PercentInterruptTime | Hardver- vagy szoftveres megszakítások és DPC-k futtatásának százalékos aránya (késleltetett eljáráshívások)
PercentUserTime (Százalékhasználóidő) | A nem üresjárati idő az aggregáció időszakában, a felhasználói módban töltött idő százalékos aránya normál prioritással
PercentNiceTime (Százalék és idő) | Nem üresjárati idő esetén az alacsonyabb (szép) prioritással töltött százalékos arány
PercentPrivilegedTime (Százalékprivilegizált idő) | A nem üresjárati idő százalékos aránya emelt szintű (kernel) módban

Az első négy számlálónak 100%-nak kell lennie. Az utolsó három számláló összege szintén 100 százalék. Ez a három számláló felosztja a `PercentProcessorTime` , `PercentIOWaitTime` és `PercentInterruptTime` összegét.

### <a name="builtin-metrics-for-the-memory-class"></a>beépített metrikák a Memória osztályhoz

A metrikák Memória osztálya információt nyújt a memóriahasználatról, a lapozásról és a felcserélésről.

számláló | Értelmezés
------- | -------
AvailableMemory (Rendelkezésre álló jegy) | Rendelkezésre álló fizikai memória a MiB-ban
PercentAvailableMemory (PercentAvailableMemory) | Rendelkezésre álló fizikai memória a teljes memória százalékában
UsedMemory | Használatban álló fizikai memória (MiB)
PercentUsedMemory (PercentUsedMemory) | Használatban álló fizikai memória a teljes memória százalékában
PagesPerSec | Teljes lapozás (olvasás/írás)
PagesReadPerSec | A háttértárból beolvasott oldalak, például a felcserélési fájl, a programfájl és a leképezett fájl
PagesWrittenPerSec | A háttértárba írt lapok, például a felcserélési fájl és a leképezett fájl
AvailableSwap (Rendelkezésre álló adatok) | Nem használt felcserélési terület (MiB)
PercentAvailableSwap | Nem használt felcserélési terület a teljes felcserélés százalékában
UsedSwap (Felhasználtwap) | Használatban van a felcserélési terület (MiB)
PercentUsedSwap | Használatban van a felcserélési terület a teljes felcserélés százalékában

A metrikák ezen osztályának csak egy példánya van. Az `"condition"` attribútum nem rendelkezik hasznos beállításokkal, ezért ki kell hagyni.

### <a name="builtin-metrics-for-the-network-class"></a>beépített metrikák a Network osztályhoz

A metrikák Hálózati osztálya az indítás óta az egyes hálózati adapterek hálózati tevékenységéről nyújt információkat. 

A RENDSZER NEM teszi elérhetővé a sávszélesség-mérőszámokat. Ezeket a metrikákat a gazdagép metrikákból is le tudja szerezni.

Számláló | Értelmezés
------- | -------
BytesTransmitted (Áttért bájtok) | Indítás óta küldött bájtok összesen
BájtokReceived | Indítás óta fogadott bájtok összesen
Bájtösszeg | Indítás óta küldött vagy fogadott bájtok összesen
PacketsTransmitted (Csomagok átküldése) | Indítás óta küldött összes csomag
PacketsReceived (Csomagokreceived) | Indítás óta fogadott összes csomag
TotalRxErrors (TotalRxErrors) | Fogadási hibák száma indítás óta
TotalTxErrors (TotalTxErrors) | Küldési hibák száma indítás óta
TotalCollisions (Összesoszlop) | A hálózati portok által jelentett ütközések száma indítás óta

### <a name="builtin-metrics-for-the-file-system-class"></a>beépített metrikák a fájlrendszerosztályhoz

A metrikák fájlrendszerosztálya a fájlrendszer használatáról nyújt információt. Az abszolút és százalékos értékek úgy vannak jelentve, ahogyan egy átlagos felhasználó (nem gyökér) számára jelenne meg.

Számláló | Értelmezés
------- | -------
FreeSpace | Rendelkezésre álló lemezterület bájtban
UsedSpace (Felhasznált tér) | Felhasznált lemezterület bájtban
PercentFreeSpace | Szabad terület százalékos aránya
PercentUsedSpace | A felhasznált terület százalékos aránya
PercentFreeInodes (PercentFreeInodes) | Nem használt indexcsomópontok (inode-k) százalékos aránya
PercentUsedInodes (Százalékbanhasznált csomópontok) | A lefoglalt (használatban lévő) inode-k százalékos aránya az összes fájlrendszerben összeadva
BytesReadPerSecond (bájtolvasási másodperc) | Másodpercenként beolvasott bájtok száma
BytesWrittenPerSecond (Bájtok felülírt másodperce) | Másodpercenként írt bájtok száma
BájtperSecond (bájt/másodperc) | Olvasási vagy írási bájtok másodpercenként
ReadsPerSecond (Olvasási másodperc) | Olvasási műveletek másodpercenként
WritesPerSecond (Írási másodperc) | Írási műveletek száma másodpercenként
TransfersPerSecond (Adatátviteli másodperc) | Olvasási vagy írási műveletek másodpercenként

### <a name="builtin-metrics-for-the-disk-class"></a>beépített metrikák a Disk osztályhoz

A metrikák Lemez osztálya a lemezeszköz-használattal kapcsolatos információkat biztosít. Ezek a statisztikák a teljes meghajtóra érvényesek. 

Ha egy eszköz több fájlrendszert használ, az eszköz számlálói hatékonyan összesítve vannak az összes fájlrendszerben.

Számláló | Értelmezés
------- | -------
ReadsPerSecond (Olvasási másodperc) | Olvasási műveletek másodpercenként
WritesPerSecond (Írási másodperc) | Írási műveletek száma másodpercenként
TransfersPerSecond | Műveletek teljes száma másodpercenként
AverageReadTime | Olvasási műveletenkénti átlagos másodpercek
AverageWriteTime (Átlagos írásidő) | Írási műveletenkénti átlagos másodpercek
AverageTransferTime (Átlagos áttűnőidő) | Műveletenkénti átlagos másodpercek
AverageDiskQueueLength (ÁtlagosdiskQueueLength) | A várólistán lévő lemezműveletek átlagos száma
ReadBytesPerSecond | Másodpercenként beolvasott bájtok száma
WriteBytesPerSecond | A másodpercenként írt bájtok száma
BájtokPerSecond | Másodpercenként olvasott vagy írt bájtok száma

## <a name="install-and-configure-lad-40"></a>A FOGY 4.0 telepítése és konfigurálása

Az AZURE CLI-ben vagy a PowerShellben telepítheti és konfigurálhatja az ESZKÖZ 4.0-t.

### <a name="azure-cli"></a>Azure CLI

Ha a védett beállítások a fájlban *vannak,* ProtectedSettings.jsa nyilvános konfigurációs adatok pedig aPublicSettings.js *a fájlban,* futtassa a következő parancsot:

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

A parancs feltételezi, hogy az Azure CLI Azure Resource Management módját használja. Ha klasszikus üzembe helyezési modellű virtuális gépekhez is konfigurálni kell a FOGAT-t, váltson "asm" módra ( ), és a parancsban ne adja meg az `azure config mode asm` erőforráscsoport nevét. 

További információt a platformfüggetlen [CLI dokumentációjában talál.](/cli/azure/authenticate-azure-cli)

### <a name="powershell"></a>PowerShell

Ha a védett beállítások a változóban vannak, a nyilvános konfigurációs adatok pedig a `$protectedSettings` `$publicSettings` változóban, futtassa a következő parancsot:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0
```

## <a name="example-lad-40-configuration"></a>Példa AZ ARRA 4.0-konfigurációra

A fenti definíciók alapján ez a szakasz egy SAMPLE 4.0 bővítménykonfigurációt és néhány magyarázatot tartalmaz. A minta saját esetére való alkalmazásához használja a saját tárfióknevét, a fiók SAS-jogkivonatát, és Event Hubs SAS-jogkivonatokat.

> [!NOTE]
> Attól függően, hogy az Azure CLI-t vagy a PowerShellt használja-e az ARRA való telepítéshez, a nyilvános és védett beállítások megadása eltérő módszerrel használható: 
>
> * Ha az Azure CLI-t használja, mentse  a következő beállításokat,ProtectedSettings.jsbe- és *PublicSettings.js* az előző mintaparancshoz. 
> * Ha a PowerShellt használja, mentse az alábbi beállításokat a és a `$protectedSettings` `$publicSettings` parancs `$protectedSettings = '{ ... }'` futtatásával.

### <a name="protected-settings"></a>Védett beállítások

A védett beállítások a következőt konfigurálják:

* Egy tárfiók.
* Egyező FIÓK SAS-jogkivonata.
* Több fogadó ( `JsonBlob` vagy `EventHub` SAS-jogkivonatokkal).

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

A nyilvános beállítások a következőt okják:

* Töltse fel a processzoridő százalékos mérőszámát és a felhasznált lemezterület metrikákat a `WADMetrics*` táblába.
* Üzenetek feltöltése a syslog-létesítményből `"user"` és a súlyosság `"info"` a `LinuxSyslog*` táblába.
* Töltse fel a fájl hozzáfűzött sorait `/var/log/myladtestlog` a `MyLadTestLog` táblába.

Az adatok minden esetben a következő adatokra is feltölthetők:

* Azure Blob Storage. A tároló neve a fogadóban `JsonBlob` meghatározott.
* Egy Event Hubs végpontot a `EventHubs` fogadóban megadottak szerint.

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
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

A konfigurációban lévő beállításnak meg kell egyeznie a virtuális gép vagy `resourceId` a virtuálisgép-méretezési készlet beállításaival.

* Az Azure platform metrikadiagramozása és riasztásai ismerik a virtuális `resourceId` gépét, amelyről dolgozik. Arra számít, hogy a keresési kulccsal fogja megtalálni a virtuális gép `resourceId` adatait.
* Azure automatikus skálázás használata esetén az automatikus méretezési konfigurációban a beállításnak meg kell egyeznie az `resourceId` `resourceId` ÁLTAL használtVAL.
* A `resourceId` be van építve a ÁLTAL írt JSON-blobok nevére.

## <a name="view-your-data"></a>Adatok megtekintése

A Azure Portal megtekintheti a teljesítményadatokat, vagy riasztásokat állíthat be:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Képernyőkép a Azure Portal. A Metrika felhasznált lemezterülete van kiválasztva. Megjelenik az eredményül kapott diagram.":::

Az `performanceCounters` adatok mindig egy Azure Storage-táblában tárolódnak. Az Azure Storage API-k számos nyelvhez és platformhoz elérhetők.

A fogadóknak küldött adatok blobban vannak tárolva a nevű tárfiókban a `JsonBlob` [védett beállítások között.](#protected-settings) A blobadatokat bármely api-ban Azure Blob Storage fel.

Az Azure Storage-ban az alábbi felhasználói felületi eszközökkel is elérheti az adatokat:

* Visual Studio Server Explorer
* [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)

Az alábbi képernyőképen egy Azure Storage Explorer a létrehozott Azure Storage-táblákat és -tárolókat egy teszt virtuális gépen, egy megfelelően konfigurált EGYES 4.0-bővítményből. A rendszerkép nem pontosan egyezik a [MINTAKT 4.0-konfigurációval.](#example-lad-40-configuration)

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Képernyőkép a Azure Storage Explorer.":::

A végpontokra közzétett üzenetek használatával kapcsolatos további Event Hubs tekintse meg a megfelelő Event Hubs [dokumentációját.](../../event-hubs/event-hubs-about.md)

## <a name="next-steps"></a>Következő lépések

* A [Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md)hozzon létre riasztásokat az összegyűjtött metrikákhoz.
* [Monitorozási diagramokat hozhat](../../azure-monitor/data-platform.md) létre a metrikákhoz.
* [Hozzon létre egy virtuálisgép-méretezési](../linux/tutorial-create-vmss.md) készletet a metrikák használatával az automatikus skálázás vezérlése érdekében.