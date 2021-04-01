---
title: Csomópont-végpontok konfigurálása Azure Batch készletben
description: Az SSH-vagy RDP-portok elérésének konfigurálása vagy letiltása egy Azure Batch készlet számítási csomópontjain.
ms.topic: how-to
ms.date: 02/13/2018
ms.openlocfilehash: 4e7df7da539be75ef1befdff4b4e1fe5244c1702
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109306"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>A Azure Batch-készletben lévő számítási csomópontok távoli elérésének konfigurálása vagy letiltása

Alapértelmezés szerint a Batch lehetővé teszi, hogy a hálózati kapcsolattal rendelkező [csomópontok](/rest/api/batchservice/computenode/adduser) a Batch-készletben lévő számítási csomóponthoz külsőleg csatlakozzanak. Egy felhasználó például a 3389-as porton Távoli asztal (RDP) használatával kapcsolódhat egy számítási csomóponthoz egy Windows-készletben. Hasonlóképpen, alapértelmezés szerint a felhasználók a biztonságos rendszerhéj (SSH) használatával kapcsolódhatnak a 22-es porton a Linux-készletben lévő számítási csomóponthoz. 

A környezetében előfordulhat, hogy korlátozni vagy le kell tiltania ezeket az alapértelmezett külső hozzáférési beállításokat. Ezeket a beállításokat a Batch API-k használatával módosíthatja a [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) tulajdonság beállításához. 

## <a name="about-the-pool-endpoint-configuration"></a>A készlet végpont-konfigurációjának ismertetése
A végpont-konfiguráció a előtér-portok egy vagy több hálózati címfordítási [(NAT) készletét](/rest/api/batchservice/pool/add#inboundnatpool) tartalmazza. (Ne keverje össze a NAT-készletet a számítási csomópontok batch-készletével.) Az egyes NAT-készleteket úgy állíthatja be, hogy felülbírálja a készlet számítási csomópontjain lévő alapértelmezett kapcsolódási beállításokat. 

Minden NAT-készlet konfigurációja tartalmaz egy vagy több [hálózati biztonsági csoport (NSG) szabályt](/rest/api/batchservice/pool/add#networksecuritygrouprule). Minden NSG-szabály engedélyezi vagy megtagadja a végpontra irányuló bizonyos hálózati forgalmat. Dönthet úgy, hogy engedélyezi vagy megtagadja az összes forgalmat, a [szolgáltatási címke](../virtual-network/network-security-groups-overview.md#service-tags) által azonosított forgalmat (például az "internetet") vagy adott IP-címekről vagy alhálózatokról érkező forgalmat.

### <a name="considerations"></a>Megfontolandó szempontok
* A készlet végpontjának konfigurációja a készlet [hálózati konfigurációjának](/rest/api/batchservice/pool/add#networkconfiguration)részét képezi. A hálózati konfiguráció opcionálisan tartalmazhat olyan beállításokat, amelyekkel csatlakoztathatja a készletet egy Azure-beli [virtuális hálózathoz](batch-virtual-network.md). Ha a készletet egy virtuális hálózatban állítja be, létrehozhat olyan NSG-szabályokat, amelyek a virtuális hálózatban a címek beállításait használják.
* NAT-készlet konfigurálásakor több NSG szabályt is beállíthat. A szabályok prioritási sorrendben vannak bejelölve. Amint talál egy érvényes szabályt, nem vizsgálja, hogy a többi szabálynak megfelel-e a forgalom.


## <a name="example-deny-all-rdp-traffic"></a>Példa: az összes RDP-forgalom tiltása

A következő C# kódrészlet bemutatja, hogyan konfigurálhatja az RDP-végpontot egy Windows-készlet számítási csomópontjain az összes hálózati forgalom megtagadásához. A végpont a *60000-60099* tartományba tartozó portok egy frontend-készletét használja. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Példa: az internetről érkező összes SSH-forgalom tiltása

A következő Python-kódrészlet bemutatja, hogyan konfigurálhatja az SSH-végpontot a Linux-készletben lévő számítási csomópontokon az összes internetes forgalom megtagadásához. A végpont a *4000-4100* tartományba tartozó portok egy frontend-készletét használja. 

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                    source_address_prefix='Internet'
                )
            ]
        )
        ]
    )
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Példa: egy adott IP-címről érkező RDP-forgalom engedélyezése

A következő C# kódrészlet bemutatja, hogyan konfigurálhatja az RDP-végpontot egy Windows-készlet számítási csomópontjain úgy, hogy csak az IP- *198.51.100.7* engedélyezze az RDP-hozzáférést. A második NSG szabály megtagadja a forgalmat, amely nem felel meg az IP-címnek.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Példa: egy adott alhálózat SSH-forgalmának engedélyezése

A következő Python-kódrészlet bemutatja, hogyan konfigurálhatja az SSH-végpontot a Linux-készletben lévő számítási csomópontokon, hogy csak a *192.168.1.0/24* alhálózatról engedélyezze a hozzáférést. A második NSG szabály megtagadja a forgalmat, amely nem felel meg az alhálózatnak.

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access='allow',
                    source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                    priority=175,
                    access='deny',
                    source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- További információ az Azure-beli NSG-szabályokról: [hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/network-security-groups-overview.md).