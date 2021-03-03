---
title: Service Fabric felügyelt fürtök hálózati beállításainak konfigurálása (előzetes verzió)
description: Megtudhatja, hogyan konfigurálhatja Service Fabric felügyelt fürtöt a NSG-szabályokhoz, az RDP-portok eléréséhez, a terheléselosztási szabályokhoz és egyebekhez.
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: e17251523c0720665c4c6f5b7811304eebc9923e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745913"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters-preview"></a>Service Fabric felügyelt fürtök hálózati beállításainak konfigurálása (előzetes verzió)

Service Fabric felügyelt fürtök alapértelmezett hálózati konfigurációval jönnek létre. Ez a konfiguráció kötelező szabályokat tartalmaz a fürt alapvető működéséhez, és néhány választható szabályt, amely megkönnyíti az ügyfelek konfigurálását.

Az alapértelmezett hálózati konfiguráción túl módosíthatja a hálózati szabályokat, hogy azok megfeleljenek a forgatókönyv igényeinek.

## <a name="nsg-rules-guidance"></a>Útmutató a NSG-szabályokhoz

Vegye figyelembe ezeket a szempontokat, amikor új NSG-szabályokat hoz létre a felügyelt fürthöz.

* Service Fabric felügyelt fürtök az alapvető funkcionalitás érdekében fenntartják a 0 és 999 közötti NSG-szabály prioritási tartományát. Nem hozhat létre olyan egyéni NSG-szabályokat, amelyek prioritási értéke kisebb, mint 1000.
* Service Fabric felügyelt fürtök fenntartják a 3001 – 4000 prioritási tartományt a választható NSG-szabályok létrehozásához. Ezeket a szabályokat a rendszer automatikusan hozzáadja a konfigurációk gyors és egyszerű létrehozásához. Ezeket a szabályokat a 1000 – 3000 prioritási tartományba tartozó egyéni NSG-szabályok hozzáadásával bírálhatja felül.
* Az egyéni NSG-szabályoknak a 1000 és 3000 közötti tartományba kell tartoznia.

## <a name="apply-nsg-rules"></a>NSG-szabályok alkalmazása

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

## <a name="rdp-ports"></a>RDP-portok

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

## <a name="clientconnection-and-httpgatewayconnection-ports"></a>ClientConnection és HttpGatewayConnection portok

### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>NSG szabály: SFMC_AllowServiceFabricGatewayToSFRP

A rendszer egy alapértelmezett NSG szabályt ad hozzá, amely lehetővé teszi a Service Fabric erőforrás-szolgáltató számára a fürt clientConnectionPort és httpGatewayConnectionPort elérését. Ez a szabály engedélyezi a portokhoz való hozzáférést a "ServiceFabric" szolgáltatási címkén keresztül.

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

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>NSG szabály: SFMC_AllowServiceFabricGatewayPorts

Ez egy opcionális NSG-szabály, amely engedélyezi a hozzáférést a clientConnectionPort, és httpGatewayPort az internetről. Ez a szabály lehetővé teszi az ügyfelek számára az SFX elérését, a PowerShell használatával csatlakozhat a fürthöz, és Service Fabric fürt API-végpontokat használ a on kívülről. 

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

## <a name="load-balancer-ports"></a>Terheléselosztó portjai

Service Fabric felügyelt fürtök létrehoznak egy NSG-szabályt az alapértelmezett prioritási tartományban az összes olyan terheléselosztó-(LB-) porthoz, amelyet a *ManagedCluster* tulajdonságok szakaszának "loadBalancingRules" szakaszában konfiguráltak. Ez a szabály az internetről érkező bejövő forgalomhoz tartozó LB-portokat nyitja meg.  

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

## <a name="load-balancer-probes"></a>Load Balancer-tesztek

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

## <a name="next-steps"></a>Következő lépések

[Service Fabric felügyelt fürt konfigurációs beállításai](how-to-managed-cluster-configuration.md)

[Service Fabric felügyelt fürtök áttekintése](overview-managed-cluster.md)
