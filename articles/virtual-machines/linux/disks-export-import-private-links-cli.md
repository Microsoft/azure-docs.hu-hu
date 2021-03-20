---
title: Azure CLI – a felügyelt lemezekre vonatkozó importálási/exportálási hozzáférés korlátozása privát hivatkozásokkal
description: Privát hivatkozások engedélyezése a felügyelt lemezekhez az Azure CLI-vel. Lehetővé teszi, hogy biztonságosan exportálja és importálja a lemezeket csak a virtuális hálózaton belül.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 68b96401fc4fc6ea8916664978dbd4c094d9e5b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684525"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links"></a>Azure CLI – a felügyelt lemezek importálási/exportálási hozzáférésének korlátozása privát hivatkozásokkal

A [privát végpontok](../../private-link/private-endpoint-overview.md) használatával korlátozhatja a felügyelt lemezek exportálását és importálását, valamint az adatok biztonságos elérését az Azure-beli virtuális hálózatban lévő ügyfelektől származó [privát kapcsolaton](../../private-link/private-link-overview.md) keresztül. A magánhálózati végpont IP-címet használ a Managed Disks szolgáltatáshoz tartozó virtuális hálózati címtartomány alapján. A virtuális hálózaton és a felügyelt lemezeken lévő ügyfelek közötti hálózati forgalom csak a virtuális hálózaton keresztül halad át, a Microsoft gerinc hálózatán pedig egy privát kapcsolaton keresztül, ami kiküszöböli a nyilvános internetről való kitettséget.

Ha privát hivatkozásokat szeretne használni a felügyelt lemezek exportálásához/importálásához, először hozzon létre egy lemez-hozzáférési erőforrást, és csatolja azt egy adott előfizetésben található virtuális hálózathoz egy privát végpont létrehozásával. Ezután rendeljen hozzá egy lemezt vagy egy pillanatképet a lemezes hozzáférés egy példányához. Végül állítsa be a lemez NetworkAccessPolicy tulajdonságát vagy a pillanatképet a következőre: `AllowPrivate` . Ez korlátozni fogja a virtuális hálózat elérését. 

A NetworkAccessPolicy tulajdonságot beállíthatja úgy `DenyAll` , hogy megakadályozza, hogy bárki exportálja egy lemez vagy pillanatkép adatait. A NetworkAccessPolicy tulajdonság alapértelmezett értéke: `AllowAll` .

## <a name="limitations"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Jelentkezzen be az előfizetésbe, és állítsa be a változókat

```azurecli-interactive
subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Lemezes hozzáférés létrehozása az Azure CLI-vel
```azurecli
az disk-access create -n $diskAccessName -g $resourceGroupName -l $region

diskAccessId=$(az disk-access show -n $diskAccessName -g $resourceGroupName --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

A hálózati házirendek, például a hálózati biztonsági csoportok (NSG) nem támogatottak privát végpontok esetén. Ahhoz, hogy egy privát végpontot helyezzen üzembe egy adott alhálózaton, az adott alhálózaton explicit letiltási beállításra van szükség. 

```azurecli
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Alhálózat magánhálózati végponti házirendjeinek letiltása

Az Azure egy virtuális hálózaton belüli alhálózatra helyezi az erőforrásokat, ezért a magánhálózati végpontok hálózati házirendjeinek letiltásához frissítenie kell az alhálózatot. 

```azurecli
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Magánhálózati végpont létrehozása a lemez-hozzáférési objektumhoz

```azurecli
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>A saját DNS zóna konfigurálása

Hozzon létre egy saját DNS zónát a Storage blob tartományhoz, hozzon létre egy társítási hivatkozást a Virtual Network, és hozzon létre egy DNS-zónát, amely a privát végpontot a saját DNS zónához társítja. 

```azurecli
az network private-dns zone create --resource-group $resourceGroupName \
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```

## <a name="create-a-disk-protected-with-private-links"></a>Privát hivatkozásokkal védett lemez létrehozása
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
diskName=yourDiskName
diskSkuName=Standard_LRS
diskSizeGB=128

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az disk create -n $diskName \
-g $resourceGroupName \
-l $region \
--size-gb $diskSizeGB \
--sku $diskSkuName \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Privát hivatkozásokkal védett lemez pillanatképének létrehozása
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
sourceDiskName=yourSourceDiskForSnapshot
snapshotNameSecuredWithPL=yourSnapshotName

diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az snapshot create -n $snapshotNameSecuredWithPL \
-g $resourceGroupName \
-l $region \
--source $diskId \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="next-steps"></a>Következő lépések

- [GYIK a privát hivatkozásokról](../faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Felügyelt pillanatképek a CLI használatával történő exportálása/másolása virtuális merevlemezként egy másik régióban lévő tárfiókba](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd)