---
title: Hálózati Azure File Sync végpontok konfigurálása | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja Azure File Sync hálózati végpontokat.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: b89e0f8209c0165b71eef3d143ea1a84ea8bf64e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796855"
---
# <a name="configuring-azure-file-sync-network-endpoints"></a>Az Azure File Sync hálózati végpontjainak konfigurálása
Azure Files és Azure File Sync két fő végponttípust biztosítanak az Azure-fájlmegosztások eléréséhez: 
- Nyilvános végpontok, amelyek nyilvános IP-címmel és a világon bárhonnan elérhetők.
- Privát végpontok, amelyek egy virtuális hálózaton belül léteznek, és az adott virtuális hálózat címterületének magánhálózati IP-címével vannak.

A nyilvános Azure Files a Azure File Sync a nyilvános és a privát végpontokat egyaránt az Azure felügyeleti objektumok, a tárfiók és a Társzinkronizálási szolgáltatás vezérelik. A tárfiók egy olyan felügyeleti szerkezet, amely egy megosztott tárolókészletet képvisel, amelyben több fájlmegosztást, valamint más tárolási erőforrásokat, például blobtárolókat vagy üzenetsorokat helyezhet üzembe. A Storage Sync Service egy felügyeleti szerkezet, amely a regisztrált kiszolgálókat jelöli, amelyek a Azure File Sync-val létrehozott megbízhatósági kapcsolattal és szinkronizálási csoportokkal, amelyek meghatározzák a szinkronizálási kapcsolat topológiáját. 

Ez a cikk arra összpontosít, hogyan konfigurálhatja a hálózati végpontokat a Azure Files és Azure File Sync. Ha többet szeretne megtudni arról, hogyan konfigurálhat hálózati végpontokat az Azure-fájlmegosztások közvetlen eléréséhez ahelyett, hogy helyszíni gyorsítótárazást Azure File Sync a Azure Files hálózati végpontokkal. [](../files/storage-files-networking-endpoints.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

Javasoljuk, [hogy Azure File Sync,](file-sync-networking-overview.md) mielőtt elolvassa az útmutatót, olvassa el a hálózati szempontokat.

## <a name="prerequisites"></a>Előfeltételek 
Ez a cikk a következőt feltételezi:
- Azure-előfizetéssel rendelkezik. Ha még nem rendelkezik előfizetéssel, kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) fiókot.
- Már létrehozott egy Azure-fájlmegosztást egy tárfiókban, amelyhez a helyszínről szeretne csatlakozni. Az Azure-fájlmegosztások létrehozásáról az [Azure-fájlmegosztások létrehozása cikkből olvashat.](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
- Már létrehozott egy szinkronizálási szolgáltatást, és regisztrálta vele a Windows-fájlkiszolgálót. A virtuális gép üzembe helyezésének [Azure File Sync: Deploying Azure File Sync](file-sync-deployment-guide.md).

Továbbá:
- Ha az alkalmazást szeretné Azure PowerShell, [telepítse a legújabb verziót.](/powershell/azure/install-az-ps)
- Ha az Azure CLI-t szeretné használni, [telepítse a legújabb verziót.](/cli/azure/install-azure-cli)

## <a name="create-the-private-endpoints"></a>A privát végpontok létrehozása
Amikor privát végpontot hoz létre egy Azure-erőforráshoz, a rendszer a következő erőforrásokat telepíti:

- **Privát végpont:** Olyan Azure-erőforrás, amely a tárfiók privát végpontját vagy a Storage Sync Service-t képviseli. Ez egy olyan erőforrás, amely összeköti az Azure-erőforrást és egy hálózati adaptert.
- **Hálózati adapter (NIC):** A magánhálózati IP-címet fenntartó hálózati adapter a megadott virtuális hálózaton/alhálózaton belül. Ez pontosan ugyanaz az erőforrás, amelyet a virtuális gép üzembe helyezésekor helyez üzembe a rendszer, de ahelyett, hogy hozzá lenne rendelve egy virtuális géphez, annak a privát végpont a tulajdonosa.
- **Privát DNS-zóna:** Ha még soha nem helyezett üzembe privát végpontot ehhez a virtuális hálózathoz, a virtuális hálózathoz egy új privát DNS-zóna lesz üzembe helyezni. Az ebben a DNS-zónában található Azure-erőforráshoz is létrejön egy "A" DNS-rekord. Ha már üzembe helyezett egy privát végpontot ebben a virtuális hálózatban, az Azure-erőforrás új A rekordja lesz hozzáadva a meglévő DNS-zónához. A DNS-zóna üzembe helyezése nem kötelező, de erősen ajánlott a DNS-kezelés leegyszerűsítése érdekében.

> [!Note]  
> Ez a cikk a nyilvános Azure-régiók, a tárfiókok és a `core.windows.net` `afs.azure.net` Storage Sync Services DNS-utótagját használja. Ez a megjegyzés az Olyan Azure szuverén felhőkre is vonatkozik, mint az Azure US Government-felhő – csak helyettesítse be a környezetének megfelelő utótagokat.

### <a name="create-the-storage-account-private-endpoint"></a>A tárfiók privát végpontjának létrehozása
# <a name="portal"></a>[Portál](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

Ha virtuális géppel rendelkezik a virtuális hálózaton belül, vagy konfigurálta a DNS-továbbítást a DNS-továbbítás konfigurálása a [Azure Files-hoz](../files/storage-files-networking-dns.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)című résznek megfelelően, a következő parancsok PowerShellből, parancssorból vagy terminálból (Windows, Linux vagy macOS rendszeren) való futtatásával tesztelheti, hogy a privát végpont megfelelően lett-e beállítva. Cserélje le a `<storage-account-name>` helyére a megfelelő tárfióknevet:

```console
nslookup <storage-account-name>.file.core.windows.net
```

Ha minden sikeresen működött, a következő kimenetnek kell látsza lennie, ahol a a virtuális hálózat privát végpontjának magánhálózati `192.168.0.5` IP-címe (a Windowshoz látható kimenet):

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

Ha virtuális géppel rendelkezik a virtuális hálózaton belül, vagy konfigurálta a DNS-továbbítást a [DNS-továbbítás](../files/storage-files-networking-dns.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)Azure Files-hoz való konfigurálásával kapcsolatos leírásban leírtak szerint, a következő parancsokkal tesztelheti, hogy a privát végpont megfelelően lett-e beállítva:

```powershell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Ha minden sikeresen működött, a következő kimenetnek kell látsza lennie, ahol a a virtuális hálózat privát végpontjának magánhálózati `192.168.0.5` IP-címe:

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

Ha virtuális géppel rendelkezik a virtuális hálózaton belül, vagy konfigurálta a DNS-továbbítást a [DNS-továbbítás](../files/storage-files-networking-dns.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)Azure Files-hoz való konfigurálásával kapcsolatos leírásban leírtak szerint, a következő parancsokkal tesztelheti, hogy a privát végpont megfelelően lett-e beállítva:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Ha minden sikeresen működött, a következő kimenetnek kell látsza lennie, ahol a a virtuális hálózat privát végpontjának magánhálózati `192.168.0.5` IP-címe:

```output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

### <a name="create-the-storage-sync-service-private-endpoint"></a>A Társzinkronizálási szolgáltatás privát végpontjának létrehozása
> [!Important]  
> Ha privát végpontokat használ a Storage Sync Service-erőforráson, a Azure File Sync 10.1-es vagy újabb verzióját kell használnia. A 10.1-es előtti ügynökverziók nem támogatják a privát végpontokat a Storage Sync Service-ben. Az összes korábbi ügynökverzió támogatja a privát végpontokat a tárfiók-erőforráson.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Lépjen a **Private Link-központ** a Private Link  tetején található keresősávba begépelt Azure Portal. Az új privát végpont létrehozásához a Private Link-központ válassza a Privát végpontok, majd a **+** Hozzáadás lehetőséget.

[![A privát kapcsolati központ képernyőképe](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png)](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png#lightbox)

Az eredményül kapott varázsló több lapot is tartalmaz.

Az Alapvető **beállítások panelen** válassza ki a kívánt erőforráscsoportot, nevet és régiót a privát végponthoz. Ezek bármilyenek lehetnek, de nem kell semmilyen módon egyezniük a társzinkronizálási szolgáltatással, bár a privát végpontot ugyanabban a régióban kell létrehoznia, mint a virtuális hálózatot, ahol létre szeretné hozni a privát végpontot.

![Képernyőkép a Privát végpont létrehozása szakasz Alapvető funkciók szakaszról](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-1.png)

Az Erőforrás **panelen** válassza a Csatlakozás a címtárban lévő Azure-erőforráshoz **választógombot.** Az Erőforrás **típusa alatt** válassza a **Microsoft.StorageSync/storageSyncServices** erőforrástípust. 

A **Konfiguráció** panelen kiválaszthatja azt a virtuális hálózatot és alhálózatot, amelybe hozzá szeretné adni a privát végpontot. Válassza ki ugyanazt a virtuális hálózatot, mint amit a fenti tárfiókhoz használt. A Konfiguráció panel a privát DNS-zóna létrehozására/frissítésére vonatkozó információkat is tartalmazza.

Kattintson **az Áttekintés + létrehozás gombra** a privát végpont létrehozásához.

A következő parancsok PowerShellből való futtatásával tesztelheti, hogy a privát végpont megfelelően lett-e beállítva. 

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

Ha minden megfelelően működött, a következő kimenetnek kell látsza lennie, ahol a , , és a a privát végponthoz rendelt magánhálózati `192.168.1.4` `192.168.1.5` `192.168.1.6` `192.168.1.7` IP-címek:

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
Ha privát végpontot hoz létre a Storage Sync Service-hez, először be kell szereznie a Storage Sync Service-hez való hivatkozást. Ne felejtse el `<storage-sync-service-resource-group>` lecserélni a és `<storage-sync-service>` a értékeket a környezetének megfelelő értékekre. A következő PowerShell-parancsok feltételezik, hogy a használatával már ki vannak töltve a fenti virtuális hálózat adatai. 

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

Privát végpont létrehozásához létre kell hoznia egy privát kapcsolati szolgáltatáskapcsolatot a Társzinkronizálási szolgáltatással. A privát kapcsolati kapcsolat a privát végpont létrehozásának bemenete.

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

Az Azure privát DNS-zóna létrehozása lehetővé teszi a Társzinkronizálási szolgáltatás (például ) állomásneveit, hogy feloldják a virtuális hálózaton belüli szinkronizálási szolgáltatás megfelelő magánhálózati `mysssmanagement.westus2.afs.azure.net` IP-címeit. Bár a privát végpont létrehozása szempontjából nem kötelező, explicit módon szükséges, hogy a Azure File Sync-ügynök hozzáférjen a Storage Sync Service-hez. 

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
Most, hogy már van hivatkozása a privát DNS-zónára, létre kell hoznia egy A rekordot a Storage Sync Service-hez.

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
Ahhoz, hogy privát végpontot hoz létre a Storage Sync Service-hez, először be kell szereznie egy hivatkozást a Storage Sync Service-hez. Ne felejtse el `<storage-sync-service-resource-group>` lecserélni a és `<storage-sync-service>` a értékeket a környezetének megfelelő értékekre. A következő CLI-parancsok feltételezik, hogy a használatával már ki vannak töltve a virtuális hálózat fenti adatai. 

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

Privát végpont létrehozásához először meg kell győződnie arról, hogy az alhálózat privát végpont hálózati szabályzata le van tiltva. Ezután létrehozhat egy privát végpontot az `az network private-endpoint create` paranccsal.

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

Az Azure privát DNS-zóna létrehozása lehetővé teszi a Társzinkronizálási szolgáltatás (például ) állomásneveit, hogy feloldják a virtuális hálózaton belüli szinkronizálási szolgáltatás megfelelő magánhálózati `mysssmanagement.westus2.afs.azure.net` IP-címeit. Bár a privát végpont létrehozása szempontjából nem kötelező, explicit módon szükséges, hogy a Azure File Sync-ügynök hozzáférjen a Storage Sync Service-hez. 

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

Most, hogy már van hivatkozása a privát DNS-zónára, létre kell hoznia egy A rekordot a Storage Sync Service-hez.

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

## <a name="restrict-access-to-the-public-endpoints"></a>A nyilvános végpontok elérésének korlátozása
Korlátozhatja a tárfiók és a szinkronizálási szolgáltatások nyilvános végpontjaihoz való hozzáférést. A nyilvános végponthoz való hozzáférés korlátozása további biztonságot nyújt annak biztosításával, hogy a hálózati csomagok csak jóváhagyott helyekről fogadhatóak el. 

### <a name="restrict-access-to-the-storage-account-public-endpoint"></a>A tárfiók nyilvános végponthoz való hozzáférésének korlátozása
A nyilvános végpontra vonatkozó hozzáférési korlátozásokat a tárfiók tűzfalbeállításokkal lehet elérni. Általánosságban elmondható, hogy a tárfiókok legtöbb tűzfal-szabályzata egy vagy több virtuális hálózathoz korlátozza a hálózati hozzáférést. Két módszer létezik a tárfiókhoz való hozzáférés virtuális hálózatra való korlátozására:

- [Hozzon létre egy vagy több privát végpontot a tárfiókhoz, és](#create-the-storage-account-private-endpoint) tiltsa le a nyilvános végponthoz való hozzáférést. Ez biztosítja, hogy csak a kívánt virtuális hálózatokról származó forgalom fér hozzá a tárfiókon belüli Azure-fájlmegosztáshoz.
- Korlátozza a nyilvános végpontot egy vagy több virtuális hálózatra. Ez a virtuális hálózat szolgáltatásvégpontnak nevezett *képességével működik.* Ha egy szolgáltatásvégponton keresztül egy tárfiókra korlátozza a forgalmat, akkor is a nyilvános IP-címen keresztül fér hozzá a tárfiókhoz.

#### <a name="disable-access-to-the-storage-account-public-endpoint"></a>A tárfiók nyilvános végponthoz való hozzáférés letiltása
Ha a nyilvános végponthoz való hozzáférés le van tiltva, a tárfiók továbbra is elérhető a privát végpontjaikon keresztül. Ellenkező esetben a rendszer elutasítja a tárfiók nyilvános végpontjára vonatkozó érvényes kérelmeket. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-storage-account-public-endpoint-to-specific-virtual-networks"></a>A tárfiók nyilvános végponthoz való hozzáférés korlátozása adott virtuális hálózatokra
Ha a tárfiókot adott virtuális hálózatokra korlátozza, a nyilvános végpontra vonatkozó kéréseket a megadott virtuális hálózatokon belül engedélyezi. Ez a virtuális hálózat szolgáltatásvégpontnak nevezett *képességével működik.* Ez privát végpontokkal vagy anélkül is használható.

# <a name="portal"></a>[Portál](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

### <a name="disable-access-to-the-storage-sync-service-public-endpoint"></a>A Társzinkronizálási szolgáltatás nyilvános végponthoz való hozzáférés letiltása
Azure File Sync lehetővé teszi, hogy csak privát végpontok használatával korlátozza az adott virtuális hálózatokhoz való hozzáférést; Azure File Sync nem támogatja a szolgáltatásvégpontokat a nyilvános végponthoz való hozzáférés adott virtuális hálózatokra való korlátozásához. Ez azt jelenti, hogy a Társzinkronizálási szolgáltatás nyilvános végpontjának két állama engedélyezve van és le van tiltva.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Ez nem lehetséges a Azure Portal. Válassza a Azure PowerShell fület a Storage Sync Service nyilvános végpont letiltására vonatkozó utasításokért. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A Társzinkronizálási szolgáltatás nyilvános végpontjának hozzáférésének letiltásához a Tárolószinkronizálási szolgáltatás tulajdonságát a következőre `incomingTrafficPolicy` állítjuk: `AllowVirtualNetworksOnly` . Ha engedélyezni szeretné a storage sync service nyilvános végpontjának hozzáférését, állítsa a `incomingTrafficPolicy` `AllowAllTraffic` következőre: . Ne felejtse el lecserélni a `<storage-sync-service-resource-group>` és a `<storage-sync-service>` helyére.

```powershell
$storageSyncServiceResourceGroupName = "<storage-sync-service-resource-group>"
$storageSyncServiceName = "<storage-sync-service>"

Set-AzStorageSyncService `
    -ResourceGroupName $storageSyncServiceResourceGroupName `
    -Name $storageSyncServiceName `
    -IncomingTrafficPolicy AllowVirtualNetworksOnly
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Az Azure CLI nem támogatja a tulajdonság `incomingTrafficPolicy` beállítását a Társzinkronizálási szolgáltatásban. Válassza a Azure PowerShell fület a Storage Sync Service nyilvános végpont letiltására vonatkozó utasításokért.

---

## <a name="azure-policy"></a>Azure Policy
Azure Policy segít kikényszeríteni a szervezeti szabványokat, és felmérni a szabványoknak való megfelelőséget nagy léptékben. Azure Files és Azure File Sync számos hasznos naplózási és szervizelési hálózati szabályzatot is elérhetővé teszi, amelyek segítségével figyelheti és automatizálhatja az üzembe helyezést.

A szabályzatok naplózják a környezetet, és riasztást küldnek, ha a tárfiókok vagy a Storage Sync Services eltérnek a meghatározott viselkedéstől. Ha például egy nyilvános végpont engedélyezve van a szabályzat beállításakor, a nyilvános végpontok le vannak tiltva. A szabályzatok módosítása/üzembe helyezése egy lépéssel tovább tart, és proaktív módon módosít egy erőforrást (például a Storage Sync Service-t), vagy erőforrásokat (például privát végpontokat) helyez üzembe a szabályzatok igazítása érdekében.

A következő előre definiált szabályzatok érhetők el a Azure Files és Azure File Sync:

| Művelet | Szolgáltatás | Feltétel | Házirend neve |
|-|-|-|-|
| Naplózás | Azure Files | A tárfiók nyilvános végpontja engedélyezve van. További információ: A tárfiók [nyilvános végponthoz való hozzáférésének](#disable-access-to-the-storage-account-public-endpoint) letiltása. | A tárfióknak korlátoznia kell a hálózati hozzáférést |
| Naplózás | Azure File Sync | A Storage Sync Service nyilvános végpontja engedélyezve van. További [információ: A Társzinkronizálási](#disable-access-to-the-storage-sync-service-public-endpoint) szolgáltatás nyilvános végponthoz való hozzáférésének letiltása. | A nyilvános hálózati hozzáférést le kell tiltani a Azure File Sync |
| Naplózás | Azure Files | A tárfióknak legalább egy privát végpontra van szüksége. További információ: Create the storage account private endpoint (A [tárfiók privát végpontjának](#create-the-storage-account-private-endpoint) létrehozása). | A tárfióknak privát kapcsolati kapcsolatot kell használnia |
| Naplózás | Azure File Sync | A Társzinkronizálási szolgáltatásnak legalább egy privát végpontra van szüksége. További [információ: Create the Storage Sync Service private endpoint (A Társzinkronizálási](#create-the-storage-sync-service-private-endpoint) szolgáltatás privát végpontjának létrehozása). | Azure File Sync kapcsolat használata |
| Módosítás | Azure File Sync | Tiltsa le a Társzinkronizálási szolgáltatás nyilvános végpontját. | Módosítás – A Azure File Sync konfigurálása a nyilvános hálózati hozzáférés letiltásához |
| Üzembe helyezés | Azure File Sync | Privát végpont üzembe helyezése a Társzinkronizálási szolgáltatáshoz. | Konfigurálás Azure File Sync privát végpontokkal |
| Üzembe helyezés | Azure File Sync | Egy A rekord üzembe helyezése a DNS privatelink.afs.azure.net számára. | A Azure File Sync konfigurálása privát DNS-zónák használatára |

### <a name="set-up-a-private-endpoint-deployment-policy"></a>Privát végpont üzembe helyezési szabályzatának beállítása
Privátvégpont-telepítési házirend beállításhoz keresse meg a [Azure Portal,](https://portal.azure.com/)és keressen rá a Szabályzat **kifejezésre.** A Azure Policy középpontnak kell a legjobb eredménynek lennie. A **Szabályzatközpont tartalomjegyzékében** lépjen a Szerzői  >   definíciók lapra. Az eredményül **kapott Definíciók** panel az összes Azure-szolgáltatás előre definiált szabályzatát tartalmazza. Az adott szabályzat megkereséhez  válassza ki a Tárolási kategóriát a kategóriaszűrőben, vagy keressen rá a Configure Azure File Sync with private endpoints (Tároló konfigurálása privát végpontokkal) **kifejezésre.** Új szabályzat **definícióból való** létrehozásához válassza a **...** és a Hozzárendelés lehetőséget.

A **Szabályzat hozzárendelése** varázsló Alapszintű panelje lehetővé teszi hatókör, erőforrás vagy erőforráscsoport kizárási listájának beállítását, valamint a szabályzat megkülönböztetést segítő rövid nevének beállítását.  Ezeket nem kell módosítania ahhoz, hogy a szabályzat működjön, de ha módosításokat szeretne tenni, igen. Kattintson **a Tovább** gombra a Paraméterek **lapra lépéshez.** 

A **Paraméterek panelen** válassza a **privateEndpointSubnetId** legördülő lista melletti **...** lehetőséget, és válassza ki azt a virtuális hálózatot és alhálózatot, ahol a Storage Sync Service-erőforrások privát végpontjai üzembe lesznek helyezni. Az eredményül kapott varázsló néhány másodperc alatt betölti az előfizetésben elérhető virtuális hálózatokat. Válassza ki a környezetének megfelelő virtuális hálózatot/alhálózatot, majd kattintson a **Kijelölés gombra.** Válassza **a Tovább** lehetőséget a **Szervizelés panelre lépéshez.**

Ahhoz, hogy a privát végpontot akkor telepítse a rendszer, ha egy privát végpont nélküli szinkronizálási szolgáltatást azonosít, ki kell **választania a** Szervizelési feladat létrehozása feladatot a **Szervizelés lapon.** Végül válassza az **Áttekintés + létrehozás** lehetőséget a szabályzat-hozzárendelés áttekintéshez, majd a **Létrehozás** gombra a létrehozásához.

Az eredményül kapott szabályzat-hozzárendelést a rendszer rendszeres időközönként végrehajtja, és előfordulhat, hogy a létrehozás után nem fog azonnal futni.

## <a name="see-also"></a>Lásd még
- [Az Azure File Sync üzembe helyezésének megtervezése](file-sync-planning.md)
- [Az Azure File Sync üzembe helyezése](file-sync-deployment-guide.md)
