---
title: Állapot nélküli csomópont-típusok telepítése Service Fabric fürtben
description: Ismerje meg, hogyan hozhat létre és helyezhet üzembe állapot nélküli csomópont-típusokat az Azure Service Fabric-fürtben.
author: peterpogorski
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: pepogors
ms.openlocfilehash: 74680f7b56ad98851e2839b53c1f9e92b6c6c23a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030009"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types"></a>Azure Service Fabric-fürt üzembe helyezése csak állapot nélküli csomópont-típusokkal
Service Fabric a csomópontok típusai feltételezik, hogy bizonyos időpontban az állapot-nyilvántartó szolgáltatások a csomópontokra helyezhetők. Az állapot nélküli csomópontok típusai kipihenhetik ezt a feltételezést a csomópontok típusához, így a csomópont típusa más funkciók használatát teszi lehetővé, például gyorsabb horizontális Felskálázási műveleteket, az automatikus operációsrendszer-frissítések támogatását a bronz tartósságon, és több mint 100 csomópontra méretezheti egyetlen virtuálisgép-méretezési csoporton belül.

* Az elsődleges csomópontok típusa nem állítható állapot nélkülire.
* Az állapot nélküli csomópontok típusai csak a bronz tartóssági szinteken támogatottak
* Az állapot nélküli csomópontok típusai csak Service Fabric futtatókörnyezet 7.1.409 vagy újabb verziójában támogatottak.


A sablonok elérhetők: [Service Fabric állapot nélküli csomópont-típusok sablonja](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Állapot nélküli csomópont-típusok engedélyezése Service Fabric fürtben
Ha egy vagy több csomópontot állapot nélküliként szeretne beállítani a fürt erőforrásaiban, állítsa a **isStateless** tulajdonságot **true (igaz**) értékre. Ha állapot nélküli csomópont-típusokkal rendelkező Service Fabric-fürtöt telepít, ne feledje, hogy a fürterőforrás egyetlen elsődleges csomópont-típussal rendelkezik.

* A Service Fabric fürterőforrás-apiVersion "2020-12-01-Preview" vagy magasabb értékűnek kell lennie.

```json
{
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "isStateless": false, // Primary Node Types cannot be stateless
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": false,
        "isStateless": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    }    
    ],
}
```

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>Virtuálisgép-méretezési csoport konfigurálása állapot nélküli csomópontok típusaihoz
Az állapot nélküli csomópontok típusának engedélyezéséhez a következő módon kell konfigurálnia az alapul szolgáló virtuálisgép-méretezési csoport erőforrásait:

* A Value  **singlePlacementGroup** tulajdonság, amelyet **false** értékre kell állítani, ha több mint 100 virtuális gépre kell méreteznie.
* A méretezési csoport **upgradeMode** a **Rolling** értékre kell beállítani.
* A működés közbeni frissítési üzemmódhoz az alkalmazás állapotának vagy a beállított állapotának beállítása szükséges. Az állapot nélküli csomópont-típusok alapértelmezett konfigurációjának beállítása az alábbi módon ajánlott. Miután telepítette az alkalmazásokat a csomópont típusára, az állapot-mintavételi/állapotfigyelő portok módosíthatók az alkalmazás állapotának figyeléséhez.

>[!NOTE]
> Ha az automatikus skálázást állapot nélküli nodetypes használja, a leskálázási művelet után a csomópont állapota nem törlődik automatikusan. A NodeState az autoskálázás során való kitakarításához a [Service Fabric autoscale Helper](https://github.com/Azure/service-fabric-autoscale-helper) használata javasolt.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Rolling",
          "automaticOSUpgradePolicy": {
            "enableAutomaticOSUpgrade": true
          }
        },
        "platformFaultDomainCount": 5
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Bronze",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
        },
        "typeHandlerVersion": "1.0"
    }
    },
    {
        "type": "extensions",
        "name": "HealthExtension",
        "properties": {
            "publisher": "Microsoft.ManagedServices",
            "type": "ApplicationHealthWindows",
            "autoUpgradeMinorVersion": true,
            "typeHandlerVersion": "1.0",
            "settings": {
            "protocol": "tcp",
            "port": "19000"
            }
            }
        },
    ]
}
```

## <a name="configuring-stateless-node-types-with-multiple-availability-zones"></a>Állapot nélküli csomópont-típusok konfigurálása több Availability Zones
A több rendelkezésre állási zónán átívelő állapot nélküli NodeType konfigurálásához kövesse [a dokumentációt](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set), valamint a következő néhány módosítást:

* **SinglePlacementGroup** beállítása: **hamis** , ha több elhelyezési csoportra van szükség az engedélyezéshez.
* Set  **upgradeMode** : a fentiekben leírtak szerint adja **meg az alkalmazás**   állapotának kiterjesztését/állapotát.
* A **platformFaultDomainCount** : **5** beállítása a virtuálisgép-méretezési csoport számára.

>[!NOTE]
> A fürtben konfigurált VMSSZonalUpgradeMode függetlenül a virtuálisgép-méretezési csoport frissítései mindig egymás után egy rendelkezésre állási zónában történnek a több zónára kiterjedő állapot nélküli NodeType, mivel az a működés közbeni frissítési módot használja.

Hivatkozásként tekintse meg a [sablont](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure) az állapot nélküli csomópont-típusok több Availability Zones való konfigurálásához

## <a name="networking-requirements"></a>Hálózati követelmények
### <a name="public-ip-and-load-balancer-resource"></a>Nyilvános IP-cím és Load Balancer erőforrás
Ha a méretezést több mint 100 virtuális gépre szeretné engedélyezni egy virtuálisgép-méretezési csoport erőforrásán, a terheléselosztó és az adott virtuálisgép-méretezési csoport által hivatkozott IP-erőforrásnak egyaránt *szabványos* SKU-t kell használnia. Az SKU tulajdonság nélküli terheléselosztó vagy IP-erőforrás létrehozásakor létrejön egy alapszintű SKU, amely nem támogatja több mint 100 virtuális gép méretezését. A standard SKU Load Balancer alapértelmezés szerint letiltja a kívülről érkező összes forgalmat. a külső forgalom engedélyezéséhez telepíteni kell egy NSG az alhálózatra.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> A nyilvános IP-címek és a terheléselosztó erőforrásainak helybeni módosítása nem lehetséges. 

### <a name="virtual-machine-scale-set-nat-rules"></a>Virtuálisgép-méretezési csoport NAT-szabályai
A terheléselosztó bejövő NAT-szabályainak meg kell egyezniük a virtuálisgép-méretezési csoport NAT-készletével. Minden virtuálisgép-méretezési csoportnak rendelkeznie kell egy egyedi bejövő NAT-készlettel.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Szabványos SKU Load Balancer kimenő szabályok
A standard Load Balancer és a standard nyilvános IP-címek új képességeket és különböző viselkedéseket vezetnek be a kimenő kapcsolatokhoz az alapszintű SKU-hoz képest. Ha standard SKU-kal dolgozik a kimenő kapcsolaton, explicit módon meg kell határoznia a standard nyilvános IP-címeket vagy a standard nyilvános Load Balancer. További információ: [Kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md) és [Azure standard Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> A standard sablon egy olyan NSG hivatkozik, amely alapértelmezés szerint engedélyezi az összes kimenő forgalmat. A bejövő forgalom a Service Fabric felügyeleti műveletekhez szükséges portokra korlátozódik. A NSG-szabályok módosíthatók a követelmények teljesítése érdekében.

>[!NOTE]
> A standard SKU-SLB használó Service Fabric fürtöknek biztosítaniuk kell, hogy minden csomópont-típushoz olyan szabály legyen, amely engedélyezi a kimenő forgalmat az 443-as porton. Ez a fürt beállításának befejezéséhez szükséges, és az ilyen szabályok nélküli központi telepítések sikertelenek lesznek.



## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>Migrálás állapot nélküli csomópont-típusok használatára a fürtben
Az összes áttelepítési forgatókönyvhöz hozzá kell adni egy új állapot nélküli csomópont-típust. A meglévő csomópont-típus nem telepíthető át csak állapotra.

Ha olyan fürtöt szeretne áttelepíteni, amely egy alapszintű SKU-val Load Balancer és IP-címet használ, először létre kell hoznia egy teljesen új Load Balancer és IP-erőforrást a szabványos SKU használatával. Ezeket az erőforrásokat helyben nem lehet frissíteni.

Az új LB-t és IP-címet a használni kívánt új állapot nélküli csomópont-típusokra kell hivatkozni. A fenti példában egy új virtuálisgép-méretezési csoport erőforrásai lesznek hozzáadva az állapot nélküli csomópontok típusaihoz. Ezek a virtuálisgép-méretezési csoportok az újonnan létrehozott LB-re és az IP-re hivatkoznak, és állapot nélküli csomópont-típusként vannak megjelölve a Service Fabric-fürterőforrás számára.

A kezdéshez hozzá kell adnia az új erőforrásokat a meglévő Resource Manager-sablonhoz. Ezek az erőforrások a következők:
* A standard SKU-t használó nyilvános IP-erőforrás.
* A standard SKU-t használó Load Balancer erőforrás.
* Az alhálózat által hivatkozott NSG, amelyben üzembe helyezi a virtuálisgép-méretezési csoportokat.

Az erőforrások telepítésének befejezése után megkezdheti a csomópontok letiltását az eredeti fürtből eltávolítani kívánt csomópont-típusból.

## <a name="next-steps"></a>Következő lépések 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Csomóponttípusok és virtuálisgép-méretezési csoportok](service-fabric-cluster-nodetypes.md)

