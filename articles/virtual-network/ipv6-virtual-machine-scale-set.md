---
title: Virtuálisgép-méretezési csoportok üzembe helyezése IPv6-ban az Azure-ban
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan helyezhet üzembe virtuálisgép-méretezési csoportokat IPv6-ban egy Azure-beli virtuális hálózaton.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f969d7edc22c9c36481ca42449193af5f8c7b0d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84709997"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Virtuálisgép-méretezési csoportok üzembe helyezése IPv6-ban az Azure-ban

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy kettős verem (IPv4 + IPv6) virtuálisgép-méretezési csoportját egy Azure-beli virtuális hálózatban lévő, kettős veremből álló külső terheléselosztó használatával. Az IPv6-kompatibilis virtuálisgép-méretezési csoport létrehozásának folyamata majdnem azonos az [itt](ipv6-configure-standard-load-balancer-template-json.md)ismertetett egyéni virtuális gépek létrehozási folyamatával. Az egyes virtuális gépekhez hasonló lépésekkel kezdheti meg a lépéseket:
1.    IPv4-és IPv6-alapú nyilvános IP-címek létrehozása.
2.    Hozzon létre egy Dual stack Load balancert.  
3.    Hozzon létre hálózati biztonsági csoport (NSG) szabályait.  

Az egyetlen virtuális gépről eltérő lépés a hálózati adapter (NIC) olyan konfigurációját hozza létre, amely a virtuálisgép-méretezési csoport erőforrását használja: networkProfile/Networkinterfaceconfigurations szakaszához. A JSON-struktúra hasonló az egyes virtuális gépekhez használt Microsoft. Network/networkInterfaces objektumhoz, a hálózati adapter és az IPv4-IpConfiguration elsődleges illesztőfelületként való beállításával, az **"elsődleges": true**  attribútummal, az alábbi példában látható módon:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Minta virtuálisgép-méretezési csoport sablonja JSON

A kettős verem (IPv4 + IPv6) virtuálisgép-méretezési csoportjának üzembe helyezéséhez hozzon létre egy kettős verem külső Load Balancer és a Virtual Network View minta sablont [itt](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/).
## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure Virtual Networks IPv6-támogatásáról, tekintse meg a [Mi az az Azure-beli ipv6 Virtual Network?](ipv6-overview.md)című témakört.
