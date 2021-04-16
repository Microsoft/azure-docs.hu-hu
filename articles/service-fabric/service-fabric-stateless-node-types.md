---
title: Csak állapot nélküli csomóponttípusok üzembe helyezése Service Fabric fürtben
description: Megtudhatja, hogyan hozhat létre és helyezhet üzembe állapot nélküli csomóponttípusokat az Azure Service Fabric fürtben.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: pepogors
ms.openlocfilehash: 68c617b6e9345910bfd913e61e227a8e6c401bbc
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576040"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types"></a>Azure-beli Service Fabric fürt üzembe helyezése csak állapot nélküli csomóponttípusokkal
Service Fabric csomóponttípusok eredendően azt feltételezik, hogy egy adott időpontban állapot-szolgáltatási szolgáltatások helyezhetők el a csomópontokon. Az állapot nélküli csomóponttípusok ezt a feltételezést a csomóponttípus esetében ékezetesek, így lehetővé téve a csomóponttípus számára más funkciók használatát, például a gyorsabb horizontális felskálásos műveleteket, a bronz tartósság automatikus operációsrendszer-frissítésének támogatását, valamint egyetlen virtuálisgép-méretezési csoport több mint 100 csomópontjára való horizontális felskálzását.

* Az elsődleges csomóponttípusok nem konfigurálhatóak állapot nélküliként
* Az állapot nélküli csomóponttípusok csak bronz tartóssági szintekkel támogatottak
* Az állapot nélküli csomóponttípusok csak a Service Fabric 7.1.409-es vagy újabb verzióiban támogatottak.


Mintasablonok érhetők el: [Service Fabric állapot nélküli csomóponttípusok sablonja](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Állapot nélküli csomóponttípusok engedélyezése Service Fabric fürtben
Ha egy fürterőforrásban egy vagy több csomóponttípust állapot nélküliként kell beállítania, állítsa az **isStateless** tulajdonságot **true (igaz) állapotra.** Állapot nélküli csomóponttípusokkal Service Fabric fürt üzembe helyezésekor ne felejtsen el legalább egy elsődleges csomóponttípust tartalmazni a fürterőforrásban.

* Az Service Fabric apiVersion fürterőforrásnak "2020-12-01-preview" vagy újabb verziójúnak kell lennie.

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

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>Virtuálisgép-méretezési készlet konfigurálása állapot nélküli csomóponttípusokhoz
Az állapot nélküli csomóponttípusok engedélyezéséhez az alapul szolgáló virtuálisgép-méretezési csoport erőforrását a következő módon kell konfigurálnia:

* A  **singlePlacementGroup** tulajdonság értéke, amelyet **false** (hamis) értékre kell állítani, ha több mint 100 virtuális gépre kell méretezni.
* A méretezési készlet **upgradeMode beállítását** Működés közbeni **beállításra kell állítani.**
* A működés közbeni frissítési módhoz konfigurálni kell az alkalmazás állapotkiterjesztését vagy állapot-mintavételét. Konfigurálja az állapot-mintavételt az állapot nélküli csomóponttípusok alapértelmezett konfigurációjában az alább javasolt módon. Miután telepítette az alkalmazásokat a csomóponttípusba, az Állapotfigyelő mintavétel/Állapot bővítmény portjai módosíthatók az alkalmazás állapotának figyelése érdekében.

>[!NOTE]
> Az automatikus skálázás állapot nélküli csomóponttípusokkal való használata esetén a leskálás művelet után a csomópont állapota nem törlődik automatikusan. Annak érdekében, hogy az automatikus skálázás során megtisztítsa a csomópontok NodeState állapotát, javasolt [Service Fabric automatikus méretezési segítővel.](https://github.com/Azure/service-fabric-autoscale-helper)

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

## <a name="configuring-stateless-node-types-with-multiple-availability-zones"></a>Állapot nélküli csomóponttípusok konfigurálása több Availability Zones
Ha több rendelkezésre állási zónára kiterjedő állapot [](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set)nélküli csomóponttípust konfigurál, kövesse az itt található dokumentációt, valamint az alábbi néhány módosítást:

* Állítsa **be a singlePlacementGroup:**  **false értéket,**  ha több elhelyezési csoportot is engedélyezni kell.
* Állítsa  **be az upgradeMode** : **Működés**   közbeni üzemmódot, és adja hozzá az Application Health bővítményt/állapot-mintavételeket a fent említettek szerint.
* A **platformFaultDomainCount:** **5 beállítása** a virtuálisgép-méretezési készlethez.

>[!NOTE]
> A fürtben konfigurált VMSSZonalUpgradeMode módtól függetlenül a virtuálisgép-méretezési csoport frissítései mindig egymás után, egymás után történnek a több zónára is vonatkozó állapot nélküli csomóponttípus esetében, mivel működés közbeni frissítési módot használ.

Referenciaként nézze meg [](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure) az állapot nélküli csomóponttípusok több csomóponttípussal való konfigurálásához Availability Zones

## <a name="networking-requirements"></a>Hálózati követelmények
### <a name="public-ip-and-load-balancer-resource"></a>Nyilvános IP-cím és Load Balancer erőforrás
Ha több mint 100 virtuális gépre szeretné engedélyezni a méretezést egy virtuálisgép-méretezési csoport erőforrásán, az adott  virtuálisgép-méretezési csoport által hivatkozott terheléselosztási és IP-erőforrásnak egyaránt standard termékváltozatot kell használnia. Ha A termékváltozat tulajdonság nélkül hoz létre egy terheléselosztási vagy IP-erőforrást, az alapszintű termékváltozatot hoz létre, amely nem támogatja a több mint 100 virtuális gépre való skálázást. A Standard termékváltozatú terheléselosztás alapértelmezés szerint minden kívülről származó forgalmat blokkol; A külső forgalom lehetővé helyezéséhez egy NSG-t kell üzembe helyezni az alhálózaton.

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
> A nyilvános IP- és terheléselosztási erőforrásokon nem lehetséges a termékváltozat módosítása. 

### <a name="virtual-machine-scale-set-nat-rules"></a>Virtuálisgép-méretezési készlet NAT-szabályai
A terheléselosztás bejövő NAT-szabályainak egyezniük kell a virtuálisgép-méretezési készlet NAT-készletével. Minden virtuálisgép-méretezési készletnek egyedi bejövő NAT-készletet kell beállítania.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standard termékváltozat Load Balancer szabályokhoz
standard Load Balancer és a standard nyilvános IP-cím új képességeket és különböző viselkedésmódokat vezet be a kimenő kapcsolatokhoz az alapszintű SKUs-okkal szemben. Ha kimenő kapcsolatot szeretne a Standard SKUs-okkal való munka során, explicit módon meg kell határoznia azt standard nyilvános IP-címekkel vagy standard nyilvános Load Balancer. További információ: [Kimenő kapcsolatok és](../load-balancer/load-balancer-outbound-connections.md) [Az Azure standard Load Balancer.](../load-balancer/load-balancer-overview.md)

>[!NOTE]
> A standard sablon egy NSG-re hivatkozik, amely alapértelmezés szerint minden kimenő forgalmat lehetővé tesz. A bejövő forgalom a felügyeleti műveletekhez szükséges portokra Service Fabric korlátozva. Az NSG-szabályok a követelményeknek megfelelően módosíthatók.

>[!NOTE]
> Minden Service Fabric fürtnek, amely standard termékváltozatú SLB-t használ, gondoskodnia kell arról, hogy minden csomóponttípushoz legyen egy szabály, amely engedélyezi a kimenő forgalmat a 443-as porton. Ez a fürt beállításának befejezéséhez szükséges, és az ilyen szabály nélküli üzembe helyezés sikertelen lesz.



## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>Áttelepítés állapot nélküli csomóponttípusok fürtben való használatával
Minden áttelepítési forgatókönyvhöz hozzá kell adni egy új, csak állapot nélküli csomóponttípust. A meglévő csomóponttípus nem migrálható csak állapot nélküliként.

Egy olyan fürt áttelepítéséhez, amely alapszintű termékváltozatú Load Balancer ip-címet használt, először létre kell hoznia egy teljesen új Load Balancer ip-erőforrást a standard termékváltozat használatával. Ezeket az erőforrásokat nem lehet a helyén frissíteni.

Az új LB-t és IP-címet a használni szeretne új állapot nélküli csomóponttípusokban kell hivatkozni. A fenti példában egy új virtuálisgép-méretezési csoportot adunk hozzá erőforrásokhoz, amelyek állapot nélküli csomóponttípusokhoz használhatók. Ezek a virtuálisgép-méretezési csoportok az újonnan létrehozott terheléselosztásra és IP-címre hivatkoznak, és állapot nélküli csomóponttípusokként vannak megjelölve az Service Fabric fürterőforrásban.

Először hozzá kell adni az új erőforrásokat a meglévő Resource Manager sablonhoz. Ezek az erőforrások a következők:
* Egy standard termékváltozatot használó nyilvános IP-cím erőforrás.
* Egy Load Balancer standard termékváltozatot használó erőforrás.
* Egy NSG, amelyre az az alhálózat hivatkozik, amelyben a virtuálisgép-méretezési készleteket üzembe helyeznie kell.

Miután az erőforrások üzembe helyezése befejeződött, megkezdheti az eredeti fürtből eltávolítani kívánt csomóponttípus csomópontjainak letiltását.

## <a name="next-steps"></a>Következő lépések 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Csomóponttípusok és virtuálisgép-méretezési csoportok](service-fabric-cluster-nodetypes.md)

