---
title: Szolgáltatás-végponti házirendek konfigurálása – Azure HDInsight
description: Ismerje meg, hogyan konfigurálhatja a virtuális hálózat szolgáltatás-végponti házirendjeit az Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: 992cd994f96b5637d5afd91bccfecde8704d2886
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940627"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>Virtuális hálózati szolgáltatás-végponti szabályzatok konfigurálása az Azure HDInsight

Ez a cikk azt ismerteti, hogyan valósítható meg a szolgáltatás-végponti szabályzatok virtuális hálózatokon az Azure HDInsight használatával.

## <a name="background"></a>Háttér

Az Azure HDInsight lehetővé teszi fürtök létrehozását a saját virtuális hálózatában. Ha engedélyeznie kell a kimenő forgalmat a virtuális hálózatról más Azure-szolgáltatásokra, például a Storage-fiókokra, létrehozhat [szolgáltatási végponti házirendeket](../virtual-network/virtual-network-service-endpoint-policies-overview.md). A Azure Portalon keresztül létrehozott szolgáltatási végponti házirendek lehetővé teszik, hogy egyetlen fiókhoz, az előfizetéshez tartozó összes fiókhoz vagy egy erőforráscsoport fiókjaihoz hozzon létre házirendet.

Felügyelt szolgáltatásként azonban az Azure HDInsight az egyes régiókban lévő egyes fürtökről gyűjt adatokat és naplófájlokat. Ahhoz, hogy ezek az adatok elérjék a HDInsight a virtuális hálózatról, létre kell hoznia olyan szolgáltatás-végponti házirendeket, amelyek engedélyezik a kimenő forgalmat az Azure HDInsight által kezelt adatgyűjtési pontokra.

## <a name="service-endpoint-policies-for-hdinsight"></a>A HDInsight szolgáltatási végpontjának házirendjei

Ezek a szolgáltatás-végponti házirendek a következő funkciókat támogatják:

- Naplók és telemetria gyűjteménye a fürt létrehozásához, a feladatok végrehajtásához és a platform műveleteihez, például a skálázáshoz.
- Virtuális merevlemezek (VHD-k) csatolása az újonnan létrehozott fürtcsomópontok számára a fürtben lévő szoftverek és könyvtárak kiépítéséhez.

Ha a szolgáltatás-végponti szabályzatok nem jönnek létre az adatáramlás engedélyezéséhez, a fürt létrehozása meghiúsulhat, és az Azure HDInsight nem tud támogatást nyújtani a fürtökhöz.

## <a name="create-service-endpoint-policies-for-hdinsight"></a>Szolgáltatás-végponti szabályzatok létrehozása a HDInsight

Új fürtök létrehozása előtt győződjön meg arról, hogy a megfelelő szolgáltatási végponti házirendek vannak csatlakoztatva a virtuális hálózathoz. Ellenkező esetben előfordulhat, hogy a fürt létrehozása meghiúsul, vagy hibát okoz.

A szükséges szolgáltatási végponti szabályzatok létrehozásához használja a következő eljárást:

1. Döntse el, hogy melyik régióban hozza létre a HDInsight-fürtöt.
1. Keresse meg ezt a régiót a [szolgáltatás-végponti házirend erőforrásainak listájában](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json), amely az összes erőforráscsoportot megadja a HDInsight felügyeleti tárolási fiókjaihoz.
1. Válassza ki a régióhoz tartozó erőforráscsoportok listáját. Az alábbi erőforrásokra példa `Canada Central` látható:

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG"
    ],
    ```

1. Szúrja be az erőforráscsoportok listáját az Azure CLI-ben vagy Azure PowerShellban írt telepítési parancsfájlba.

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    Ha a szolgáltatás-végponti házirendet a PowerShell használatával szeretné beállítani, használja a következő kódrészletet.
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```

## <a name="next-steps"></a>Következő lépések

* [Azure HDInsight virtuális hálózati architektúra](hdinsight-virtual-network-architecture.md)
* [Hálózati virtuális berendezés konfigurálása](./network-virtual-appliance.md)
