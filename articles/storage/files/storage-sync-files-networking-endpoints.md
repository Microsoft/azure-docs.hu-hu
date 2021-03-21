---
title: Azure File Sync hálózati végpontok konfigurálása | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja Azure File Sync hálózati végpontokat.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 5/11/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 97ccbd0858a7b85c4b5d1e460f67416d8139e49a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218604"
---
# <a name="configuring-azure-file-sync-network-endpoints"></a>Az Azure File Sync hálózati végpontjainak konfigurálása
Azure Files és Azure File Sync két fő típusú végpontot biztosít az Azure-fájlmegosztás eléréséhez: 
- Nyilvános végpontok, amelyek nyilvános IP-címmel rendelkeznek, és bárhonnan elérhetők a világ bármely pontján.
- Saját végpontok, amelyek egy virtuális hálózaton belül találhatók, és rendelkeznek magánhálózati IP-címmel a virtuális hálózat címterület területén belülről.

Mind a Azure Files, mind a Azure File Sync, az Azure felügyeleti objektumai, a Storage-fiók és a Storage Sync szolgáltatás egyaránt szabályozza a nyilvános és a privát végpontokat is. A Storage-fiók egy olyan felügyeleti szerkezet, amely egy megosztott tárolót jelöl, amelyben több fájlmegosztást is üzembe helyezhet, valamint más tárolási erőforrásokat, például blob-tárolókat vagy várólistákat. A Storage Sync szolgáltatás olyan felügyeleti megoldás, amely a regisztrált kiszolgálókat jelöli, amelyek olyan Windows-fájlkiszolgálók, amelyeken a Azure File Sync és a szinkronizálási kapcsolat topológiája van meghatározva. 

Ez a cikk azt ismerteti, hogyan konfigurálható a hálózati végpontok mind a Azure Files, mind a Azure File Sync. Ha többet szeretne megtudni arról, hogyan konfigurálhatja a hálózatkezelési végpontokat az Azure-fájlmegosztás közvetlen eléréséhez ahelyett, hogy a helyszíni Azure File Sync használatával gyorsítótárazza a szolgáltatást, tekintse meg a [Azure Files hálózati végpontok konfigurálása](storage-files-networking-endpoints.md)című témakört.

Javasoljuk, hogy olvassa el a [Azure file Sync hálózati megfontolásokat](storage-sync-files-networking-overview.md) az útmutató elolvasása előtt.

## <a name="prerequisites"></a>Előfeltételek 
Ez a cikk azt feltételezi, hogy:
- Azure-előfizetéssel rendelkezik. Ha még nem rendelkezik előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.
- Már létrehozott egy Azure-fájlmegosztást egy olyan Storage-fiókban, amelyhez a helyszíni rendszerből szeretne csatlakozni. Az Azure-fájlmegosztás létrehozásáról az [Azure-fájlmegosztás létrehozása](storage-how-to-create-file-share.md)című témakörben olvashat bővebben.
- Már létrehozott egy Storage Sync szolgáltatást, és regisztrálta azt a Windows-fájlkiszolgálón. A Azure File Sync telepítésének megismeréséhez tekintse meg a [Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md)című témakört.

Továbbá:
- Ha Azure PowerShell szeretne használni, [telepítse a legújabb verziót](/powershell/azure/install-az-ps).
- Ha az Azure CLI-t szeretné használni, [telepítse a legújabb verziót](/cli/azure/install-azure-cli).

## <a name="create-the-private-endpoints"></a>Saját végpontok létrehozása
Ha egy Azure-erőforráshoz hoz létre privát végpontot, a rendszer a következő erőforrásokat telepíti:

- **Privát végpont**: egy Azure-erőforrás, amely a Storage-fiók vagy a Storage Sync szolgáltatás privát végpontját jelöli. Ezt olyan erőforrásként tekintheti meg, amely összekapcsolja az Azure-erőforrást és egy hálózati adaptert.
- **Hálózati adapter (NIC)**: a megadott virtuális hálózaton/alhálózaton belüli magánhálózati IP-címet fenntartó hálózati adapter. Ez ugyanaz az erőforrás, amelyet a rendszer a virtuális gépek telepítésekor üzembe helyez, azonban a virtuális géphez való hozzárendelés helyett a magánhálózati végpont tulajdonosa.
- **Privát DNS-zóna**: Ha korábban még nem telepített privát végpontot ehhez a virtuális hálózathoz, a rendszer új magánhálózati DNS-zónát helyez üzembe a virtuális hálózathoz. A DNS-rekord is létrejön az Azure-erőforráshoz ebben a DNS-zónában. Ha már telepített egy privát végpontot ebben a virtuális hálózaton, az Azure-erőforráshoz tartozó új rekord lesz hozzáadva a meglévő DNS-zónához. A DNS-zónák üzembe helyezése nem kötelező, azonban kifejezetten ajánlott a DNS-kezelés szükséges egyszerűsítése.

> [!Note]  
> Ez a cikk az Azure nyilvános régióinak, a `core.windows.net` Storage-fiókoknak és `afs.azure.net` a Storage Sync Services DNS-utótagját használja. Ez a kommentár az Azure szuverén felhőkre is vonatkozik, például az Azure US government-felhőre, és csak a megfelelő utótagokat helyettesíti a környezetében.

### <a name="create-the-storage-account-private-endpoint"></a>A Storage-fiók magánhálózati végpontjának létrehozása
# <a name="portal"></a>[Portál](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

Ha a virtuális hálózatán belül van egy virtuális gép, vagy ha konfigurálta a DNS-továbbítást a [Azure Files DNS](storage-files-networking-dns.md)-továbbítás beállítása című témakörben leírtak szerint, akkor tesztelheti, hogy a saját végpontja helyesen van-e beállítva. Ehhez futtassa a következő parancsokat a powershellből, a parancssorból vagy a terminálból (Windows, Linux vagy MacOS rendszeren működik). `<storage-account-name>`A nevet a megfelelő Storage-fiók nevével kell helyettesíteni:

```console
nslookup <storage-account-name>.file.core.windows.net
```

Ha minden sikeresen működött, a következő kimenetnek kell megjelennie, ahol a a `192.168.0.5` virtuális hálózat privát végpontjának magánhálózati IP-címe (a Windowshoz megjelenített kimenet):

```output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

Ha a virtuális hálózatán belül van egy virtuális gép, vagy ha konfigurálta a DNS-továbbítást a [Azure Files DNS](storage-files-networking-dns.md)-továbbításának beállítása című témakörben leírtak szerint, ellenőrizheti, hogy a saját végpontja helyesen van-e beállítva a következő parancsokkal:

```powershell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Ha minden sikeresen működött, a következő kimenetnek kell megjelennie, ahol a a `192.168.0.5` virtuális hálózat privát végpontjának magánhálózati IP-címe:

```output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]

Ha a virtuális hálózatán belül van egy virtuális gép, vagy ha konfigurálta a DNS-továbbítást a [Azure Files DNS](storage-files-networking-dns.md)-továbbításának beállítása című témakörben leírtak szerint, ellenőrizheti, hogy a saját végpontja helyesen van-e beállítva a következő parancsokkal:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Ha minden sikeresen működött, a következő kimenetnek kell megjelennie, ahol a a `192.168.0.5` virtuális hálózat privát végpontjának magánhálózati IP-címe:

```output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

### <a name="create-the-storage-sync-service-private-endpoint"></a>A Storage Sync szolgáltatás magánhálózati végpontjának létrehozása
> [!Important]  
> Ha privát végpontokat szeretne használni a Storage Sync Service-erőforráson, akkor a Azure File Sync Agent 10,1-es vagy újabb verzióját kell használnia. A 10,1 előtti ügynök-verziók nem támogatják a Storage Sync szolgáltatás magánhálózati végpontját. Az összes korábbi ügynök-verzió támogatja a Storage-fiók erőforrásához tartozó magánhálózati végpontokat.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Navigáljon a **privát hivatkozás központhoz** , és írja be a Azure Portal felső részén található keresési sávra a *privát hivatkozást* . A privát kapcsolati központ tartalomjegyzékében válassza a **privát végpontok** lehetőséget, majd a **+ Hozzáadás** elemet egy új privát végpont létrehozásához.

[![A privát kapcsolati központ képernyőképe](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png)](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png#lightbox)

Az eredményül kapott varázslónak több lapja is van.

Az **alapvető beállítások** panelen válassza ki a saját végpontja számára a kívánt erőforráscsoportot, nevet és régiót. Ezek bármilyen módon igénybe vehetők, de a Storage Sync szolgáltatásnak egyáltalán nem kell megegyeznie, bár a magánhálózati végpontot ugyanabban a régióban kell létrehoznia, mint a virtuális hálózatot, amelyben létre kívánja hozni a privát végpontot.

![A privát végpont létrehozása szakasz alapjai szakaszának képernyőképe](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-1.png)

Az **erőforrás** panelen válassza a **saját címtárban található Azure-erőforráshoz való kapcsolódáshoz** használandó választógombot. Az **erőforrástípus** területen válassza a **Microsoft. StorageSync/storageSyncServices** elemet az erőforrás típushoz. 

A **konfiguráció** panelen kiválaszthatja azt a virtuális hálózatot és alhálózatot, amelyhez hozzá szeretné adni a privát végpontot. Válassza ki ugyanazt a virtuális hálózatot, mint amelyet a fenti Storage-fiókhoz használt. A konfiguráció panelen a magánhálózati DNS-zóna létrehozásával/frissítésével kapcsolatos információk is szerepelnek.

A privát végpont létrehozásához kattintson a **felülvizsgálat + létrehozás** gombra.

A következő parancsok a PowerShellből való futtatásával ellenőrizheti, hogy a saját végpontja megfelelően van-e beállítva. 

```powershell
$privateEndpointResourceGroupName = "<your-private-endpoint-resource-group>"
$privateEndpointName = "<your-private-endpoint-name>"

Get-AzPrivateEndpoint `
        -ResourceGroupName $privateEndpointResourceGroupName `
        -Name $privateEndpointName `
        -ErrorAction Stop | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty Id | `
    ForEach-Object { Get-AzNetworkInterface -ResourceId $_ } | `
    Select-Object -ExpandProperty IpConfigurations | `
    Select-Object -ExpandProperty PrivateLinkConnectionProperties | `
    Select-Object -ExpandProperty Fqdns | `
    ForEach-Object { Resolve-DnsName -Name $_ } | `
    Format-List
```

Ha minden megfelelően működött, a következő kimenetnek kell megjelennie, ahol a, a, a `192.168.1.4` `192.168.1.5` és a `192.168.1.6` `192.168.1.7` privát végponthoz rendelt magánhálózati IP-címek:

```output
Name     : mysssmanagement.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : mysssmanagement.westus2.privatelink.afs.azure.net


Name       : mysssmanagement.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.4

Name     : myssssyncp.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : myssssyncp.westus2.privatelink.afs.azure.net


Name       : myssssyncp.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.5

Name     : myssssyncs.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : myssssyncs.westus2.privatelink.afs.azure.net


Name       : myssssyncs.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.6

Name     : mysssmonitoring.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : mysssmonitoring.westus2.privatelink.afs.azure.net


Name       : mysssmonitoring.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.7

```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Ha privát végpontot szeretne létrehozni a Storage Sync szolgáltatáshoz, először be kell szereznie a Storage Sync szolgáltatásra mutató hivatkozást. Ne felejtse el lecserélni a `<storage-sync-service-resource-group>` `<storage-sync-service>` környezetet a megfelelő értékekre. A következő PowerShell-parancsok feltételezik, hogy az Ön által használt virtuális hálózati adatok már korábban is fel vannak töltve. 

```powershell
$storageSyncServiceResourceGroupName = "<storage-sync-service-resource-group>"
$storageSyncServiceName = "<storage-sync-service>"

$storageSyncService = Get-AzStorageSyncService `
        -ResourceGroupName $storageSyncServiceResourceGroupName `
        -Name $storageSyncServiceName `
        -ErrorAction SilentlyContinue

if ($null -eq $storageSyncService) {
    $errorMessage = "Storage Sync Service $storageSyncServiceName not found "
    $errorMessage += "in resource group $storageSyncServiceResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}
```

Privát végpont létrehozásához létre kell hoznia egy magánhálózati kapcsolati szolgáltatást a Storage Sync szolgáltatáshoz. A privát kapcsolat kapcsolata egy bemenet a privát végpont létrehozásához.

```powershell 
# Disable private endpoint network policies
$subnet.PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork = $virtualNetwork | `
    Set-AzVirtualNetwork -ErrorAction Stop

# Create a private link service connection to the storage account.
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name "$storageSyncServiceName-Connection" `
        -PrivateLinkServiceId $storageSyncService.ResourceId `
        -GroupId "Afs" `
        -ErrorAction Stop

# Create a new private endpoint.
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $storageSyncServiceResourceGroupName `
        -Name "$storageSyncServiceName-PrivateEndpoint" `
        -Location $virtualNetwork.Location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -ErrorAction Stop
```

Az Azure privát DNS-zónák létrehozása lehetővé teszi a tárolási szinkronizálási szolgáltatás (például:) állomásneve számára a `mysssmanagement.westus2.afs.azure.net` virtuális hálózatban lévő Storage Sync szolgáltatás megfelelő magánhálózati IP-címeinek feloldását. Bár a privát végpontok létrehozásának perspektívája nem kötelező, explicit módon szükséges ahhoz, hogy a Azure File Sync ügynök hozzáférhessen a Storage Sync szolgáltatáshoz. 

```powershell
# Get the desired Storage Sync Service suffix (afs.azure.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
$azureEnvironment = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

switch($azureEnvironment) {
    "AzureCloud" {
        $storageSyncSuffix = "afs.azure.net"
    }

    "AzureUSGovernment" {
        $storageSyncSuffix = "afs.azure.us"
    }
    
    default {
        Write-Error 
                -Message "The Azure environment $_ is not currently supported by Azure File Sync." `
                -ErrorAction Stop
    }
}

# For public cloud, this will generate the following DNS suffix:
# privatelink.afs.azure.net
$dnsZoneName = "privatelink.$storageSyncSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
$dnsZone = Get-AzPrivateDnsZone | `
    Where-Object { $_.Name -eq $dnsZoneName } | `
    Where-Object {
        $privateDnsLink = Get-AzPrivateDnsVirtualNetworkLink `
                -ResourceGroupName $_.ResourceGroupName `
                -ZoneName $_.Name `
                -ErrorAction SilentlyContinue
        
        $privateDnsLink.VirtualNetworkId -eq $virtualNetwork.Id
    }

if ($null -eq $dnsZone) {
    # No matching DNS zone attached to virtual network, so create new one.
    $dnsZone = New-AzPrivateDnsZone `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsZoneName `
            -ErrorAction Stop

    $privateDnsLink = New-AzPrivateDnsVirtualNetworkLink `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name "$virtualNetworkName-DnsLink" `
            -VirtualNetworkId $virtualNetwork.Id `
            -ErrorAction Stop
}
```
Most, hogy már rendelkezik a saját DNS-zónára mutató hivatkozással, létre kell hoznia egy rekordot a Storage Sync szolgáltatáshoz.

```powershell 
$privateEndpointIpFqdnMappings = $privateEndpoint | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty Id | `
    ForEach-Object { Get-AzNetworkInterface -ResourceId $_ } | `
    Select-Object -ExpandProperty IpConfigurations | `
    ForEach-Object { 
        $privateIpAddress = $_.PrivateIpAddress; 
        $_ | `
            Select-Object -ExpandProperty PrivateLinkConnectionProperties | `
            Select-Object -ExpandProperty Fqdns | `
            Select-Object `
                @{ 
                    Name = "PrivateIpAddress"; 
                    Expression = { $privateIpAddress } 
                }, `
                @{ 
                    Name = "FQDN"; 
                    Expression = { $_ } 
                } 
    }

foreach($ipFqdn in $privateEndpointIpFqdnMappings) {
    $privateDnsRecordConfig = New-AzPrivateDnsRecordConfig `
        -IPv4Address $ipFqdn.PrivateIpAddress
    
    $dnsEntry = $ipFqdn.FQDN.Substring(0, 
        $ipFqdn.FQDN.IndexOf(".", $ipFqdn.FQDN.IndexOf(".") + 1))

    New-AzPrivateDnsRecordSet `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsEntry `
            -RecordType A `
            -ZoneName $dnsZoneName `
            -Ttl 600 `
            -PrivateDnsRecords $privateDnsRecordConfig `
            -ErrorAction Stop | `
        Out-Null
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Ha privát végpontot szeretne létrehozni a Storage Sync szolgáltatáshoz, először be kell szereznie a Storage Sync szolgáltatásra mutató hivatkozást. Ne felejtse el lecserélni a `<storage-sync-service-resource-group>` `<storage-sync-service>` környezetet a megfelelő értékekre. A következő CLI-parancsok feltételezik, hogy az Ön által használt virtuális hálózati adatok már korábban is fel vannak töltve. 

```bash
storageSyncServiceResourceGroupName="<storage-sync-service-resource-group>"
storageSyncServiceName="<storage-sync-service>"

storageSyncService=$(az resource show \
        --resource-group $storageSyncServiceResourceGroupName \
        --name $storageSyncServiceName \
        --resource-type "Microsoft.StorageSync/storageSyncServices" \
        --query "id" | \
    tr -d '"')

storageSyncServiceRegion=$(az resource show \
        --resource-group $storageSyncServiceResourceGroupName \
        --name $storageSyncServiceName \
        --resource-type "Microsoft.StorageSync/storageSyncServices" \
        --query "location" | \
    tr -d '"')
```

Privát végpont létrehozásához először győződjön meg arról, hogy az alhálózat magánhálózati végpontjának hálózati házirendje le van tiltva. Ezután létrehozhat egy privát végpontot a parancs használatával `az network private-endpoint create` .

```bash
# Disable private endpoint network policies
az network vnet subnet update \
        --ids $subnet \
        --disable-private-endpoint-network-policies \
        --output none

# Get virtual network location
region=$(az network vnet show \
        --ids $virtualNetwork \
        --query "location" | \
    tr -d '"')

# Create a private endpoint
privateEndpoint=$(az network private-endpoint create \
        --resource-group $storageSyncServiceResourceGroupName \
        --name "$storageSyncServiceName-PrivateEndpoint" \
        --location $region \
        --subnet $subnet \
        --private-connection-resource-id $storageSyncService \
        --group-id "Afs" \
        --connection-name "$storageSyncServiceName-Connection" \
        --query "id" | \
    tr -d '"')
```

Az Azure privát DNS-zónák létrehozása lehetővé teszi a tárolási szinkronizálási szolgáltatás (például:) állomásneve számára a `mysssmanagement.westus2.afs.azure.net` virtuális hálózatban lévő Storage Sync szolgáltatás megfelelő magánhálózati IP-címeinek feloldását. Bár a privát végpontok létrehozásának perspektívája nem kötelező, explicit módon szükséges ahhoz, hogy a Azure File Sync ügynök hozzáférhessen a Storage Sync szolgáltatáshoz. 

```bash
# Get the desired storage account suffix (afs.azure.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
azureEnvironment=$(az cloud show \
        --query "name" |
    tr -d '"')

storageSyncSuffix=""
if [ $azureEnvironment == "AzureCloud" ]
then
    storageSyncSuffix="afs.azure.net"
elif [ $azureEnvironment == "AzureUSGovernment" ]
then
    storageSyncSuffix="afs.azure.us"
else
    echo "Unsupported Azure environment $azureEnvironment."
fi

# For public cloud, this will generate the following DNS suffix:
# privatelinke.afs.azure.net.
dnsZoneName="privatelink.$storageSyncSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
possibleDnsZones=""
possibleDnsZones=$(az network private-dns zone list \
        --query "[?name == '$dnsZoneName'].id" \
        --output tsv)

dnsZone=""
possibleDnsZone=""
for possibleDnsZone in $possibleDnsZones
do
    possibleResourceGroupName=$(az resource show \
            --ids $possibleDnsZone \
            --query "resourceGroup" | \
        tr -d '"')
    
    link=$(az network private-dns link vnet list \
            --resource-group $possibleResourceGroupName \
            --zone-name $dnsZoneName \
            --query "[?virtualNetwork.id == '$virtualNetwork'].id" \
            --output tsv)
    
    if [ -z $link ]
    then
        echo "1" > /dev/null
    else 
        dnsZoneResourceGroup=$possibleResourceGroupName
        dnsZone=$possibleDnsZone
        break
    fi  
done

if [ -z $dnsZone ]
then
    # No matching DNS zone attached to virtual network, so create a new one
    dnsZone=$(az network private-dns zone create \
            --resource-group $virtualNetworkResourceGroupName \
            --name $dnsZoneName \
            --query "id" | \
        tr -d '"')
    
    az network private-dns link vnet create \
            --resource-group $virtualNetworkResourceGroupName \
            --zone-name $dnsZoneName \
            --name "$virtualNetworkName-DnsLink" \
            --virtual-network $virtualNetwork \
            --registration-enabled false \
            --output none
    
    dnsZoneResourceGroup=$virtualNetworkResourceGroupName
fi
```

Most, hogy már rendelkezik a saját DNS-zónára mutató hivatkozással, létre kell hoznia egy rekordot a Storage Sync szolgáltatáshoz.

```bash
privateEndpointNIC=$(az network private-endpoint show \
        --ids $privateEndpoint \
        --query "networkInterfaces[0].id" | \
    tr -d '"')

privateIpAddresses=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[].privateIpAddress" \
        --output tsv) 

hostNames=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[].privateLinkConnectionProperties.fqdns[]" \
        --output tsv)

i=0
for privateIpAddress in $privateIpAddresses
do
    j=0
    targetHostName=""
    for hostName in $hostNames
    do
        if [ $i == $j ]
        then
            targetHostName=$hostName
            break
        fi

        j=$(expr $j + 1)
    done

    endpointName=$(echo $targetHostName | \
        cut -c1-$(expr $(expr index $targetHostName ".") - 1))

    az network private-dns record-set a create \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --name "$endpointName.$storageSyncServiceRegion" \
        --output none
    
    az network private-dns record-set a add-record \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --record-set-name "$endpointName.$storageSyncServiceRegion" \
        --ipv4-address $privateIpAddress \
        --output none

    i=$(expr $i + 1)
done
```
---

## <a name="restrict-access-to-the-public-endpoints"></a>A nyilvános végpontokhoz való hozzáférés korlátozása
A Storage-fiók és a Storage Sync Services nyilvános végpontokra is korlátozhatja a hozzáférést. A nyilvános végponthoz való hozzáférés korlátozása további biztonságot nyújt annak biztosításával, hogy a hálózati csomagok csak a jóváhagyott helyekről legyenek elfogadva. 

### <a name="restrict-access-to-the-storage-account-public-endpoint"></a>A Storage-fiók nyilvános végponthoz való hozzáférés korlátozása
A nyilvános végponthoz való hozzáférés korlátozását a Storage-fiók tűzfala beállítások használatával végezheti el. Általánosságban elmondható, hogy egy Storage-fiókhoz a legtöbb tűzfal-házirend korlátozza a hálózati hozzáférést egy vagy több virtuális hálózathoz. A Storage-fiókokhoz való hozzáférés korlátozásának két módja van:

- [Hozzon létre egy vagy több privát végpontot a Storage-fiókhoz](#create-the-storage-account-private-endpoint) , és tiltsa le a nyilvános végponthoz való hozzáférést. Ez biztosítja, hogy csak a kívánt virtuális hálózatokból származó forgalom férhessenek hozzá az Azure-fájlmegosztás számára a Storage-fiókon belül.
- Korlátozza a nyilvános végpontot egy vagy több virtuális hálózatra. Ez a *szolgáltatás végpontok* nevű virtuális hálózat funkciójának használatával működik. Ha egy szolgáltatási végponton keresztül korlátozza a forgalmat egy Storage-fiókra, akkor továbbra is a nyilvános IP-cím használatával fér hozzá a Storage-fiókhoz.

#### <a name="disable-access-to-the-storage-account-public-endpoint"></a>A Storage-fiók nyilvános végponthoz való hozzáférés letiltása
Ha a nyilvános végponthoz való hozzáférés le van tiltva, a Storage-fiók továbbra is elérhető a saját végpontján keresztül. Ellenkező esetben a rendszer elutasítja a Storage-fiók nyilvános végpontjának érvényes kérelmeit. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-storage-account-public-endpoint-to-specific-virtual-networks"></a>A Storage-fiók nyilvános végpontjának megadott virtuális hálózatokra való hozzáférésének korlátozása
Ha a Storage-fiókot adott virtuális hálózatokra korlátozza, a megadott virtuális hálózatokról érkező kéréseket engedélyez a nyilvános végpontnak. Ez a *szolgáltatás végpontok* nevű virtuális hálózat funkciójának használatával működik. Ez privát végpontokkal vagy anélkül is használható.

# <a name="portal"></a>[Portál](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

### <a name="disable-access-to-the-storage-sync-service-public-endpoint"></a>A Storage Sync szolgáltatás nyilvános végponthoz való hozzáférés letiltása
Azure File Sync lehetővé teszi, hogy csak privát végpontokon keresztül korlátozza az adott virtuális hálózatokhoz való hozzáférést; A Azure File Sync nem támogatja a szolgáltatási végpontok számára a nyilvános végponthoz adott virtuális hálózatokhoz való hozzáférés korlátozását. Ez azt jelenti, hogy a Storage Sync szolgáltatás nyilvános végpontjának két állapota engedélyezve van és le van tiltva.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Ez nem lehetséges a Azure Portalon keresztül. Az Azure PowerShell lapon megtudhatja, hogyan tilthatja le a Storage Sync szolgáltatás nyilvános végpontját. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Ha le szeretné tiltani a Storage Sync szolgáltatás nyilvános végpontjának elérését, a `incomingTrafficPolicy` Storage Sync szolgáltatás tulajdonságát a következőre állítja be: `AllowVirtualNetworksOnly` . Ha engedélyezni szeretné a Storage Sync szolgáltatás nyilvános végpontjának elérését, állítsa `incomingTrafficPolicy` a helyet a következőre: `AllowAllTraffic` . Ne felejtse el lecserélni `<storage-sync-service-resource-group>` és `<storage-sync-service>` .

```powershell
$storageSyncServiceResourceGroupName = "<storage-sync-service-resource-group>"
$storageSyncServiceName = "<storage-sync-service>"

Set-AzStorageSyncService `
    -ResourceGroupName $storageSyncServiceResourceGroupName `
    -Name $storageSyncServiceName `
    -IncomingTrafficPolicy AllowVirtualNetworksOnly
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Az Azure CLI nem támogatja a tulajdonság beállítását `incomingTrafficPolicy` a Storage Sync szolgáltatásban. Az Azure PowerShell lapon megtudhatja, hogyan tilthatja le a Storage Sync szolgáltatás nyilvános végpontját.

---

## <a name="azure-policy"></a>Azure Policy
Azure Policy segíti a szervezeti szabványok betartatását és a szabványok megfelelőségének értékelését a nagy méretekben. Azure Files és Azure File Sync számos hasznos naplózási és szervizelési hálózati házirendet tesz elérhetővé, amelyek segítségével figyelheti és automatizálhatja az üzembe helyezést.

A szabályzatok naplózzák a környezetet, és riasztást küldenek arról, ha a Storage-fiókok vagy a Storage Sync Services a definiált viselkedéstől eltérnek. Ha például egy nyilvános végpont engedélyezve van, ha a házirend úgy van beállítva, hogy a nyilvános végpontok le legyenek tiltva. A házirendek módosítása/üzembe helyezése további lépéseket hajt végre, és proaktív módon módosítja egy erőforrást (például a Storage Sync szolgáltatást), vagy üzembe helyezi az erőforrásokat (például privát végpontokat) a szabályzatokhoz való igazításhoz.

A következő, előre meghatározott házirendek érhetők el Azure Files és Azure File Sync számára:

| Művelet | Szolgáltatás | Feltétel | Házirend neve |
|-|-|-|-|
| Naplózás | Azure Files | A Storage-fiók nyilvános végpontja engedélyezve van. További információért lásd: [hozzáférés letiltása a Storage-fiók nyilvános végpontja](#disable-access-to-the-storage-account-public-endpoint) számára. | A Storage-fiókoknak korlátoznia kell a hálózati hozzáférést |
| Naplózás | Azure File Sync | A Storage Sync szolgáltatás nyilvános végpontja engedélyezve van. További információért lásd: [hozzáférés letiltása a Storage Sync szolgáltatás nyilvános végpontja](#disable-access-to-the-storage-sync-service-public-endpoint) számára. | A nyilvános hálózati hozzáférést le kell tiltani Azure File Sync |
| Naplózás | Azure Files | A Storage-fióknak legalább egy privát végpontra szüksége van. További információért lásd [a Storage-fiók magánhálózati végpontjának létrehozása](#create-the-storage-account-private-endpoint) című témakört. | A Storage-fióknak magánhálózati kapcsolatot kell használnia |
| Naplózás | Azure File Sync | A Storage Sync szolgáltatásnak legalább egy magánhálózati végpontra van szüksége. További információért lásd [a Storage Sync szolgáltatás privát végpontjának létrehozása](#create-the-storage-sync-service-private-endpoint) című témakört. | Azure File Sync a magánhálózati hivatkozást kell használnia |
| Módosítás | Azure File Sync | Tiltsa le a Storage Sync szolgáltatás nyilvános végpontját. | Módosítás – Azure File Sync konfigurálása a nyilvános hálózati hozzáférés letiltásához |
| Üzembe helyezés | Azure File Sync | Helyezzen üzembe egy magánhálózati végpontot a Storage Sync szolgáltatáshoz. | Azure File Sync konfigurálása magánhálózati végpontokkal |
| Üzembe helyezés | Azure File Sync | Helyezzen üzembe egy rekordot a privatelink.afs.azure.net DNS-zónában. | Azure File Sync konfigurálása privát DNS-zónák használatára |

### <a name="set-up-a-private-endpoint-deployment-policy"></a>Privát végpont telepítési szabályzatának beállítása
A privát végpontok központi telepítési szabályzatának beállításához lépjen a [Azure Portalra](https://portal.azure.com/), és keressen rá **a szabályzat kifejezésre.** A Azure Policy központnak legfelső szintű eredménynek kell lennie. A szabályzati központ tartalomjegyzékében navigáljon az **authoring**  >  -**definíciók** elemre. Az eredményül kapott **definíciók** ablaktábla az összes Azure-szolgáltatáson belül tartalmazza az előre meghatározott szabályzatokat. Az adott házirend megkereséséhez válassza ki a **tárolási** kategóriát a kategória szűrőben, vagy keressen rá a **Azure file Sync konfigurálása magánhálózati végpontokkal** lehetőségre. Válassza a **...** és a **hozzárendelés** lehetőséget, hogy új szabályzatot hozzon létre a definícióból.

A **házirend kiosztása** varázsló **alapjai** panelje lehetővé teszi a hatókör, az erőforrás vagy az erőforráscsoport kizárási listájának beállítását, valamint egy felhasználóbarát név megadását, amely megkönnyíti a szabályzatok megkülönböztetését. Ezeket nem kell módosítania a szabályzat működéséhez, de ha módosításokat kíván végezni. Kattintson a **tovább** gombra a **Paraméterek** lapra való továbblépés előtt. 

A **Parameters (paraméterek** ) panelen kattintson a **...** elemre a **privateEndpointSubnetId** legördülő lista mellett, és válassza ki azt a virtuális hálózatot és alhálózatot, ahol a Storage Sync szolgáltatás erőforrásaihoz tartozó magánhálózati végpontokat telepíteni kell. Az eredményül kapott varázsló több másodpercig is eltarthat a rendelkezésre álló virtuális hálózatok betöltéséhez az előfizetésben. Válassza ki a környezetének megfelelő virtuális hálózatot/alhálózatot, majd kattintson a **kiválasztás** gombra. Kattintson a **Next (tovább** ) gombra a **szervizelési** panelre való továbblépés előtt.

Ahhoz, hogy a magánhálózati végpont telepítve legyen, ha a Storage Sync szolgáltatás magánhálózati végpont nélkül van azonosítva **, a szervizelés lapon be** kell jelölnie a **szervizelés létrehozása feladatot** . Végül válassza a **felülvizsgálat + létrehozás** lehetőséget a szabályzat-hozzárendelés áttekintéséhez és **létrehozásához** .

Az eredményül kapott szabályzat-hozzárendelést rendszeres időközönként végrehajtja a rendszer, és előfordulhat, hogy a létrehozása után nem azonnal fut.

## <a name="see-also"></a>Lásd még
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)