---
title: A Service Fabric felügyelt fürt konfigurálása (előzetes verzió)
description: Megtudhatja, hogyan konfigurálhatja a Service Fabric felügyelt fürtöt automatikus operációsrendszer-frissítésekhez, NSG-szabályokhoz és egyebekhez.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 90ba5057f06cf8841e278b8d921d812286459c49
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642497"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Service Fabric felügyelt fürt (előzetes verzió) konfigurációs beállításai

A fürt létrehozásakor a [Service Fabric felügyelt fürt SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus) -jának kiválasztásán kívül számos más módszert is konfigurálhat. Az aktuális előzetes verzióban a következőket teheti:

* Virtuálisgép- [méretezési csoport kiterjesztésének](how-to-managed-cluster-vmss-extension.md) hozzáadása csomópont-típushoz
* Az [operációs rendszer automatikus frissítéseinek](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) engedélyezése a csomópontok számára
* Az [operációs rendszer és az adatlemez titkosításának](how-to-enable-managed-cluster-disk-encryption.md) engedélyezése a csomópontokon
* Tudnivalók a [hálózati konfigurációkról](how-to-managed-cluster-configuration.md#networking-configurations)
* [Felügyelt identitás](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) konfigurálása a csomópont-típusoknál

## <a name="networking-configurations"></a>Hálózati konfigurációk

Service Fabric felügyelt fürtök alapértelmezett hálózati konfigurációval jönnek létre. Ez a konfiguráció kötelező szabályokat tartalmaz a fürt alapvető működéséhez, és néhány választható szabályt, amely megkönnyíti az ügyfelek konfigurálását.

Az alapértelmezett hálózati konfiguráción túl módosíthatja a hálózati szabályokat, hogy azok megfeleljenek a forgatókönyv igényeinek. 

### <a name="nsg-rules-guidance"></a>Útmutató a NSG-szabályokhoz

* Service Fabric felügyelt fürtök az alapvető funkcionalitás érdekében fenntartják a 0 és 999 közötti NSG-szabály prioritási tartományát. Nem hozhat létre olyan egyéni NSG-szabályokat, amelyek prioritási értéke kisebb, mint 1000. 
* Service Fabric felügyelt fürtök fenntartják a 3001 – 4000 prioritási tartományt a választható NSG-szabályok létrehozásához. Ezeket a szabályokat a rendszer automatikusan hozzáadja a konfigurációk gyors és egyszerű létrehozásához. Ezeket a szabályokat a 1000 – 3000 prioritási tartományba tartozó egyéni NSG-szabályok hozzáadásával bírálhatja felül. 
* Az egyéni NSG-szabályoknak a 1000 és 3000 közötti tartományba kell tartoznia. 


### <a name="apply-nsg-rules"></a>NSG-szabályok alkalmazása

A klasszikus (nem felügyelt) Service Fabric fürtök esetén külön *Microsoft. Network/networkSecurityGroups* -erőforrást kell deklarálnia és felügyelni ahhoz, hogy [a fürtre alkalmazni lehessen a hálózati biztonsági csoport (NSG) szabályait](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-nsg-cluster-65-node-3-nodetype). Service Fabric felügyelt fürtök lehetővé teszik a NSG-szabályok közvetlen hozzárendelését a telepítési sablon fürt erőforrásán belül.

NSG-szabályok hozzárendeléséhez használja a *Microsoft. ServiceFabric/managedclusters* erőforrás (vagy újabb verzió) [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) tulajdonságát `2021-01-01-preview` . Például:

```json
            "apiVersion": "2021-01-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

### <a name="rdp-ports"></a>RDP-portok 

Service Fabric felügyelt fürtök alapértelmezés szerint nem engedélyezik az RDP-portok elérését. Az RDP-portok az interneten való megnyitásához a következő tulajdonságot kell megnyitnia egy Service Fabric felügyelt fürterőforrás-erőforráson. 

```json
"allowRDPAccess": true 
```

Ha a allowRDPAccess tulajdonság értéke TRUE (igaz), a rendszer a következő NSG-szabályt adja hozzá a fürt üzembe helyezéséhez.  

```json
{
    "name": "SFMC_AllowRdpPort", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

### <a name="clientconnection-and-httpgatewayconnection-ports"></a>ClientConnection és HttpGatewayConnection portok 

**NSG szabály: SFMC_AllowServiceFabricGatewayToSFRP** A rendszer egy alapértelmezett NSG szabályt ad hozzá, amely lehetővé teszi a Service Fabric erőforrás-szolgáltató számára a fürt clientConnectionPort és httpGatewayConnectionPort elérését. Ez a szabály engedélyezi a portokhoz való hozzáférést a "ServiceFabric" szolgáltatási címkén keresztül.

>[!NOTE]
>Ez a szabály mindig fel van véve, és nem bírálható felül. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayToSFRP", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.", 
        "protocol": "TCP", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "ServiceFabric", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 500, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

**NSG szabály: SFMC_AllowServiceFabricGatewayPorts** Ez egy opcionális NSG-szabály, amely engedélyezi a hozzáférést a clientConnectionPort, és httpGatewayPort az internetről. Ez a szabály lehetővé teszi az ügyfelek számára az SFX elérését, a PowerShell használatával csatlakozhat a fürthöz, és Service Fabric fürt API-végpontokat használ a on kívülről. 

>[!NOTE]
>Ez a szabály nem lesz hozzáadva, ha van olyan egyéni szabály, amely azonos hozzáférési, irány-és protokoll-értékekkel rendelkezik ugyanahhoz a porthoz. Ezt a szabályt egyéni NSG szabályokkal bírálhatja felül. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayPorts", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.", 
        "protocol": "tcp", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "*", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 3001, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

### <a name="load-balancer-ports"></a>Terheléselosztó portjai 

Service Fabric felügyelt fürtök létrehoznak egy NSG-szabályt az alapértelmezett prioritási tartományban az "loadBalancingRules" szakaszban konfigurált összes LB-porthoz a ManagedCluster tulajdonságai alatt. Ez a szabály az internetről érkező bejövő forgalomhoz tartozó LB-portokat nyitja meg.  

>[!NOTE]
>Ez a szabály a választható prioritási tartományba kerül, és felülbírálható az egyéni NSG-szabályok hozzáadásával. 

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*", 
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

### <a name="load-balancer-probes"></a>Load Balancer-tesztek

Service Fabric felügyelt fürtök automatikusan létrehoznak terheléselosztó-teszteket a háló átjáró portjaihoz, valamint a felügyelt fürt tulajdonságainak "loadBalancingRules" szakaszában konfigurált összes portot.

```json
{ 
  "value": [ 
    { 
        "name": "FabricTcpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19000, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>"
                } 
            ] 
        }, 
        "type": "Microsoft.Network/loadBalancers/probes" 
    }, 
    { 
        "name": "FabricHttpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>" 
                } 
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes" 
    },
    {
        "name": "probe1_tcp_8080", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 8080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
            { 
                "id": "<>" 
            } 
        ] 
      }, 
      "type": "Microsoft.Network/loadBalancers/probes" 
    } 
  ] 
} 
```

## <a name="enable-automatic-os-image-upgrades"></a>Az operációs rendszer rendszerképének automatikus frissítésének engedélyezése

Dönthet úgy is, hogy engedélyezi az operációs rendszer rendszerképének automatikus frissítését a felügyelt fürtcsomópontok futtatására szolgáló virtuális gépeken. Bár a virtuálisgép-méretezési csoport erőforrásai az Ön nevében Service Fabric felügyelt fürtökkel vannak kezelve, lehetővé teszi az operációs rendszer rendszerképének automatikus frissítését a fürtcsomópontok számára. A [klasszikus Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) -fürtökhöz hasonlóan a felügyelt fürtcsomópontok nem frissülnek alapértelmezés szerint, hogy megakadályozza a fürt nem szándékos megszakadását.

Az operációs rendszer automatikus frissítéseinek engedélyezése:

* Használja a `2021-01-01-preview` *Microsoft. ServiceFabric/managedclusters* és a *Microsoft. ServiceFabric/managedclusters/nodetypes* erőforrások (vagy újabb) verzióját.
* A fürt tulajdonságának beállítása `enableAutoOSUpgrade` *true (igaz* ) értékre
* A fürt nodeTypes erőforrás-tulajdonságának beállítása a `vmImageVersion` *legújabb* értékre

Például:

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Ha engedélyezve van, Service Fabric megkezdi a felügyelt fürt operációsrendszer-rendszerkép-verzióinak lekérdezését és nyomon követését. Ha új operációsrendszer-verzió érhető el, a fürtcsomópont-típusok (a virtuálisgép-méretezési csoportok) frissítése egyszerre történik. Service Fabric futtatókörnyezet frissítése csak a fürt csomópont operációsrendszer-rendszerképének frissítését követően végezhető el.

Ha egy frissítés meghiúsul, Service Fabric 24 óra elteltével újra próbálkozik, legfeljebb három újrapróbálkozás után. A klasszikus (nem felügyelt) Service Fabric frissítésekhez hasonlóan a nem megfelelő állapotú alkalmazások vagy csomópontok is letilthatják az operációs rendszer rendszerképének frissítését.

További információ a képek frissítéséről: [az operációs rendszer rendszerképének automatikus frissítése az Azure virtuálisgép-méretezési csoportokkal](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="next-steps"></a>Következő lépések

[Sablonra mutató hivatkozás]

[Felügyelt fürt áttekintése]
