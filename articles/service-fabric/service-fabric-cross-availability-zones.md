---
title: Fürt üzembe helyezése Availability Zoneson keresztül
description: Ismerje meg, hogyan hozhat létre Azure Service Fabric-fürtöt a Availability Zones között.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 95ee4e5f326dd9b76645d22ff735bc36437c72fb
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870116"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Azure Service Fabric-fürt üzembe helyezése Availability Zones
Az Azure-beli Availability Zones magas rendelkezésre állású ajánlat, amely védelmet nyújt alkalmazásai és adatai számára az adatközpont hibáiból. A rendelkezésre állási zónák egy Azure-régión belüli, független energiaellátással, hűtéssel és hálózatkezeléssel ellátott egyedi fizikai helyek.

Service Fabric támogatja a különböző Availability Zonesokra kiterjedő fürtöket, ha olyan csomópont-típusokat telepít, amelyek meghatározott zónákra vannak rögzítve. Ez biztosítja az alkalmazások magas rendelkezésre állását. Azure Availability Zones csak a kiválasztott régiókban érhetők el. További információ: [Azure Availability Zones Overview (áttekintés](../availability-zones/az-overview.md)).

A sablonok elérhetők: [Service Fabric több rendelkezésre állási zóna sablonja](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Ajánlott topológia az Azure-Service Fabric fürtök elsődleges csomópont-típusához Availability Zones
A Availability Zones között elosztott Service Fabric-fürtök biztosítják a fürt állapotának magas rendelkezésre állását. Ha Service Fabric fürtöt a zónák között, a régió által támogatott összes rendelkezésre állási zónában létre kell hoznia egy elsődleges csomópont-típust. Ez egyenletesen osztja el a magok csomópontjait az egyes elsődleges csomópontok típusai között.

Az elsődleges csomópont típusához javasolt topológiához az alábbi erőforrások szükségesek:

* A fürt megbízhatósági szintje Platinum értékre van állítva.
* Három csomópontos típus van megjelölve elsődlegesként.
    * Minden csomópont-típust hozzá kell rendelni a saját virtuálisgép-méretezési csoporthoz, amely különböző zónákban található.
    * Minden virtuálisgép-méretezési csoportnak legalább öt csomóponttal kell rendelkeznie (ezüst tartósság).
* Egyetlen nyilvános IP-erőforrás szabványos SKU használatával.
* Egyetlen Load Balancer erőforrás szabványos SKU használatával.
* Az alhálózat által hivatkozott NSG, amelyben üzembe helyezi a virtuálisgép-méretezési csoportokat.

>[!NOTE]
> A virtuálisgép-méretezési csoport egyhelyes csoportok tulajdonságát igaz értékre kell állítani, mivel Service Fabric nem támogatja egyetlen virtuálisgép-méretezési csoportot, amely zónákra terjed ki.

Az Azure Service Fabric rendelkezésre állási zóna architektúrájának ábrája ![ , amely az azure Service Fabric rendelkezésre állási zóna architektúráját mutatja.][sf-architecture]

A virtuálisgép-méretezési csoportokban az FD/UD formátumait ábrázoló minta-csomópontok listája

 ![A virtuálisgép-méretezési csoportokban az FD/UD formátumokat ábrázoló minta-csomópontok listája.][sf-multi-az-nodes]

A **szolgáltatási replikák eloszlása a zónák között**: Ha a nodeTypes olyan szolgáltatás van telepítve, amely zónákra terjed ki, a replikák úgy lesznek elhelyezve, hogy azok külön zónákban legyenek kihelyezve. Ez gondoskodik arról, hogy a tartalék tartomány az egyes nodeTypes lévő csomópontokon legyen konfigurálva a zóna adataival (azaz FD = FD:/(1/1, stb.). Például: 5 replika vagy egy szolgáltatás példányai esetében a terjesztés 2-2-1, a futtatókörnyezet pedig a AZs közötti egyenlő eloszlást fogja biztosítani.

**Felhasználói szolgáltatás replikájának konfigurációja**: a rendszer a rendelkezésre állási zóna nodeTypes üzembe helyezett állapot-nyilvántartó felhasználói szolgáltatásokat konfigurálja ehhez a konfigurációhoz: a replika száma a TARGET = 9, a min = 5. Ez a konfiguráció segíti a szolgáltatást abban az esetben is, ha egy zóna leáll, mivel a 6 replika még mindig a másik két zónában fog működni. Egy ilyen forgatókönyvben az alkalmazás frissítése is megtörténik.

**Fürt ReliabilityLevel**: Ez határozza meg a fürtben lévő mag-csomópontok számát, valamint a rendszerszolgáltatások replikájának méretét is. Mivel a többhelyes rendelkezésre állási zónák beállítása nagyobb számú csomópontot tartalmaz, amelyek több zónában vannak elosztva a zónák rugalmasságának lehetővé tételéhez, a magasabb megbízhatósági érték biztosítja, hogy a csomópontok több vetőmag-csomópontot és rendszerszolgáltatás-replikákat biztosítanak, és egyenletesen oszlanak el a zónák között, így ha egy zóna meghibásodása esetén a fürt és a rendszerszolgáltatások nem A "ReliabilityLevel = Platinum" biztosítja, hogy 9 mag-csomópont legyen elosztva a fürt zónái között, és mindegyik zónában 3 magot biztosít, ezért ez a rendelkezésre állási zóna telepítésének ajánlása.

**Zóna lefelé mutató forgatókönyv**: Ha egy zóna leáll, a zónában lévő összes csomópont le lesz jelenítve. Ezen csomópontokon a szolgáltatási replikák is le lesznek kapcsolva. Mivel a többi zónában replikák vannak, a szolgáltatás továbbra is reagálni fog az elsődleges replikákkal, és a rendszer a működés közbeni zónákat is felhasználja. A szolgáltatások figyelmeztetési állapotban jelennek meg, mivel a célként megadott replika száma még nem érhető el, és mivel a virtuális gépek száma továbbra is meghaladja a minimálisan megcélzott replika méretét. Ezt követően Service Fabric Load Balancer replikákat hoz létre a munkazónákban a beállított replika-számnak megfelelően. Ezen a ponton a szolgáltatások Kifogástalan állapotba kerülnek. Ha az a zóna, amelyről nem érkezik biztonsági másolat, a terheléselosztás újra elosztja az összes szolgáltatási replikát az összes zónában.

## <a name="networking-requirements"></a>Hálózati követelmények
### <a name="public-ip-and-load-balancer-resource"></a>Nyilvános IP-cím és Load Balancer erőforrás
Ha engedélyezni szeretné a zónák tulajdonságot egy virtuálisgép-méretezési csoport erőforrásán, a terheléselosztó és az adott virtuálisgép-méretezési csoport által hivatkozott IP-erőforrásnak is *szabványos* SKU-t kell használnia. Egy terheléselosztó vagy IP-erőforrás az SKU tulajdonság nélkül való létrehozása egy alapszintű SKU-t hoz létre, amely nem támogatja a Availability Zones. A standard SKU Load Balancer alapértelmezés szerint letiltja a kívülről érkező összes forgalmat. a külső forgalom engedélyezéséhez telepíteni kell egy NSG az alhálózatra.

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
```

```json
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
> A nyilvános IP-címek és a terheléselosztó erőforrásainak helybeni módosítása nem lehetséges. Ha egy alapszintű SKU-val rendelkező meglévő erőforrásról végez áttelepítést, tekintse meg a jelen cikk áttelepítés című szakaszát.

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


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Zónák engedélyezése virtuálisgép-méretezési csoportokban
Ha egy zónát szeretne engedélyezni egy virtuálisgép-méretezési csoporton, a következő három értéket kell tartalmaznia a virtuálisgép-méretezési csoport erőforrásaiban.

* Az első érték a **Zones** tulajdonság, amely megadja, hogy melyik rendelkezésre állási zónát telepíti a rendszer a virtuálisgép-méretezési csoport számára.
* A második érték a "singlePlacementGroup" tulajdonság, amelyet igaz értékre kell beállítani.
* A harmadik érték a Service Fabric virtuálisgép-méretezési csoport bővítményének "faultDomainOverride" tulajdonsága. A tulajdonság értékének csak azt a zónát kell tartalmaznia, amelyben ez a virtuálisgép-méretezési csoport el lesz helyezve. Példa: "faultDomainOverride": "az1" a virtuálisgép-méretezési csoport összes erőforrását ugyanabba a régióba kell helyezni, mert az Azure Service Fabric-fürtök nem rendelkeznek több régiós támogatással.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
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
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Több elsődleges csomópont típusának engedélyezése a Service Fabric fürterőforrás
Ha egy vagy több csomópontot szeretne elsődlegesként beállítani egy fürt erőforrásában, állítsa a "isPrimary" tulajdonságot "true" értékre. Service Fabric-fürt Availability Zones-beli üzembe helyezése során három csomópont-típust kell tartalmaznia különálló zónákban.

```json
{
    "reliabilityLevel": "Platinum",
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
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrálás a fürt Availability Zones használatára egy alapszintű SKU Load Balancer és egy alapszintű SKU IP-cím használatával
Ha olyan fürtöt szeretne áttelepíteni, amely egy alapszintű SKU-val Load Balancer és IP-címet használ, először létre kell hoznia egy teljesen új Load Balancer és IP-erőforrást a szabványos SKU használatával. Ezeket az erőforrásokat helyben nem lehet frissíteni.

Az új LB-t és IP-címet a használni kívánt új rendelkezésre állási zónák csomópont-típusaira kell hivatkozni. A fenti példában három új virtuálisgép-méretezési csoport erőforrásai lettek hozzáadva az 1., 2. és 3. zónához. Ezek a virtuálisgép-méretezési csoportok az újonnan létrehozott LB-re és IP-re hivatkoznak, és elsődleges csomópont-típusként vannak megjelölve a Service Fabric fürterőforrás számára.

A kezdéshez hozzá kell adnia az új erőforrásokat a meglévő Resource Manager-sablonhoz. Ezek az erőforrások a következők:
* A standard SKU-t használó nyilvános IP-erőforrás.
* A standard SKU-t használó Load Balancer erőforrás.
* Az alhálózat által hivatkozott NSG, amelyben üzembe helyezi a virtuálisgép-méretezési csoportokat.
* Három csomópontos típus van megjelölve elsődlegesként.
    * Minden csomópont-típust hozzá kell rendelni a saját virtuálisgép-méretezési csoporthoz, amely különböző zónákban található.
    * Minden virtuálisgép-méretezési csoportnak legalább öt csomóponttal kell rendelkeznie (ezüst tartósság).

Ilyen erőforrások például a [minta sablonban](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)találhatók.

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Az erőforrások telepítésének befejezése után megkezdheti a csomópontok letiltását az elsődleges csomópont típusa alapján az eredeti fürtből. Mivel a csomópontok le vannak tiltva, a rendszerszolgáltatások a fenti lépésben üzembe helyezett új elsődleges csomópont-típusra lesznek áttelepítve.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Ha a csomópontok le vannak tiltva, a rendszerszolgáltatások az elsődleges csomópont-típuson futnak, amely a zónák között oszlik meg. Ezután eltávolíthatja a letiltott csomópontokat a fürtből. A csomópontok eltávolítása után eltávolíthatja az eredeti IP-címet, a Load Balancer és a virtuálisgép-méretezési csoport erőforrásait.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Ezután el kell távolítania az erőforrásokra mutató hivatkozásokat a telepített Resource Manager-sablonból.

Az utolsó lépés a DNS-név és a nyilvános IP-cím frissítését is magában foglalja.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>Előnézet Több rendelkezésre állási zóna engedélyezése egyetlen virtuálisgép-méretezési csoportba

A korábban említett megoldás egy nodeType használ az AZ alapján. A következő megoldás lehetővé teszi, hogy a felhasználók 3 az az ugyanazon nodeType legyenek telepítve.

[Itt](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)jelennek meg a teljes minta sablon.

![Azure Service Fabric rendelkezésre állási zóna architektúrája][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>Zónák konfigurálása virtuálisgép-méretezési csoportokban
A zónák virtuálisgép-méretezési csoportokon való engedélyezéséhez a következő három értéket kell tartalmaznia a virtuálisgép-méretezési csoport erőforrásaiban.

* Az első érték a **Zones** tulajdonság, amely meghatározza a virtuálisgép-méretezési csoport Availability Zones.
* A második érték a "singlePlacementGroup" tulajdonság, amelyet igaz értékre kell beállítani. **A 3 AZ-ra kiterjedő méretezési csoport legfeljebb 300 virtuális gépet képes méretezni, akár "singlePlacementGroup = true" értékkel.**
* A harmadik érték a "zoneBalance", amely biztosítja a zónák szigorú kiegyensúlyozását. Ennek a következőnek kell lennie: "true". Ez biztosítja, hogy a zónák közötti virtuális gépek eloszlása ne legyen kiegyensúlyozatlan, gondoskodjon arról, hogy ha az egyik zóna leáll, a másik két zónának elegendő virtuális géppel kell rendelkeznie annak biztosításához, hogy a fürt zavartalanul fusson. A nem kiegyensúlyozatlan virtuálisgép-eloszlású fürtök esetében előfordulhat, hogy a zónák nem maradhatnak le, mert az adott zóna a virtuális gépek többségével rendelkezhet. A kiegyensúlyozatlan virtuálisgép-eloszlás a zónák között a szolgáltatások elhelyezésével kapcsolatos problémákat is eredményez & az infrastruktúra frissítései beragadtak. További információ a [zoneBalancing](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing).
* A FaultDomain és a UpgradeDomain felülbírálásokat nem szükséges konfigurálni.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": true
    }
}
```

>[!NOTE]
> * **Service Fabric fürtöknek legalább egy elsődleges nodeType kell rendelkezniük. Az elsődleges nodeTypes DurabilityLevel Silver vagy Above értéknek kell lennie.**
> * Az az átfedő virtuálisgép-méretezési csoportnak legalább 3 rendelkezésre állási zónával kell konfigurálnia a durabilityLevel függetlenül.
> * AZ AZ átfedő virtuálisgép-méretezési csoportnak az ezüst tartósságával (vagy újabb verzióval) legalább 15 virtuális géppel kell rendelkeznie.
> * AZ AZ spanning virtuálisgép-méretezési csoport bronz tartóssággal, legalább 6 virtuális géppel kell rendelkeznie.

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Több zóna támogatásának engedélyezése a Service Fabric nodeType
A több rendelkezésre állási zóna támogatásához engedélyezni kell a Service Fabric nodeType.

* Az első érték a **multipleAvailabilityZones** , amelyet igaz értékűre kell állítani a nodeType esetében.
* A második érték a **sfZonalUpgradeMode** , és nem kötelező. Ez a tulajdonság nem módosítható, ha a fürtben már szerepel egy olyan NodeType, amely több AZ az.
  A tulajdonság a virtuális gépek logikai csoportosítását vezérli a frissítési tartományokban.
  **Ha az érték "Parallel" (párhuzamos) értékre van állítva:** A NodeType alá tartozó virtuális gépek a frissítési figyelmen kívül hagyják a zóna adatait 5 frissítési. Ez azt eredményezi, hogy az összes zónában UD0, hogy egyszerre legyen frissítve. Ez az üzembe helyezési mód gyorsabb a frissítésekhez, de nem ajánlott, mert az a SDP irányelvek szerint működik, amely azt adja meg, hogy a frissítések egyszerre csak egy zónát alkalmazzanak.
  **Ha az érték nincs megadva, vagy a "hierarchikus" értéket adja meg:** A virtuális gépek úgy lesznek csoportosítva, hogy tükrözzék a zóna szerinti eloszlást akár 15 frissítési. A 3 zóna mindegyike 5 frissítési fog rendelkezni. Ez biztosítja, hogy a frissítések go Zone Wise legyen, a következő zónára való áttérés után csak az első zónán belüli 5 frissítési befejezése után, lassan 15 frissítési (3 zóna, 5 frissítési), ami biztonságosabb a fürt és a felhasználói alkalmazás szempontjából.
  Ez a tulajdonság csak a ServiceFabric alkalmazás és a kód verziófrissítésének frissítési viselkedését határozza meg. Az alapul szolgáló virtuálisgép-méretezési csoport frissítései továbbra is párhuzamosak lesznek az AZ-ban.
  Ez a tulajdonság nem befolyásolja az UD-eloszlást olyan csomópont-típusok esetén, amelyeken nincs engedélyezve több zóna.
* A harmadik érték a **vmssZonalUpgradeMode = Parallel**. Ez egy *kötelező* tulajdonság, amelyet konfigurálni kell a fürtben, ha több AZs rendelkező nodeType van hozzáadva. Ez a tulajdonság határozza meg a virtuálisgép-méretezési csoport frissítéseinek frissítési módját, amely párhuzamosan fog történni az AZ összes időpontban.
  Most ezt a tulajdonságot csak párhuzamosra lehet beállítani.
* A Service Fabric fürterőforrás-apiVersion "2020-12-01-Preview" vagy magasabb értékűnek kell lennie.
* A fürt kódjának a következőnek kell lennie: "7.2.445" vagy újabb.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "reliabilityLevel": "Platinum",
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
> * A nyilvános IP-cím és a Load Balancer erőforrásnak a cikkben korábban ismertetett standard SKU-t kell használnia.
> * a nodeType "multipleAvailabilityZones" tulajdonsága csak a nodeType létrehozásakor adható meg, és később nem módosítható. Ezért a meglévő nodeTypes nem konfigurálhatók ezzel a tulajdonsággal.
> * Ha a "sfZonalUpgradeMode" ki van hagyva vagy "hierarchikus" értékre van állítva, a fürt és az alkalmazás központi telepítése lassabb lesz, mert a fürtben több frissítési tartomány található. Fontos, hogy megfelelően módosítsa a frissítési szabályzat időtúllépését, hogy az a 15 frissítési tartományhoz tartozó frissítési idő időtartamára legyen beépítve. Az alkalmazás és a fürt frissítési szabályzatát frissíteni kell, hogy az üzemelő példány ne lépje túl a 12hours Azure Resource Serbice üzembe helyezési időtúllépését. Ez azt jelenti, hogy a telepítés nem haladhatja meg a 12hours-t a 15UDs esetében, azaz legfeljebb 40 perc/UD lehet.
> * Állítsa be a **reliabilityLevel = Platinum** fürtöt annak biztosítására, hogy a fürt megmaradjon az egy zóna lefelé irányuló forgatókönyvben.

>[!NOTE]
> Ajánlott eljárásként javasoljuk, hogy a sfZonalUpgradeMode Hierarchikusra állítsa, vagy hagyja figyelmen kívül. Az üzembe helyezés során a virtuális gépek a kisebb mennyiségű replikát és/vagy az azok biztonságosabbá tételét érintő replikálási eloszlását fogja követni.
> Ha az üzembe helyezés sebessége prioritás, vagy csak állapot nélküli számítási feladat fut, akkor a sfZonalUpgradeMode párhuzamosan kell futnia. Ez azt eredményezi, hogy az UD Walk is párhuzamosan fog történni az AZ-ban.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>Migrálás a csomópont típusára több Availability Zones
Minden áttelepítési forgatókönyv esetében új nodeType kell hozzáadni, amely több rendelkezésre állási zónával is rendelkezik. Egy meglévő nodeType nem telepíthető át több zóna támogatásához.
A cikk [itt](./service-fabric-scale-up-primary-node-type.md) rögzíti az új nodeType hozzáadásának részletes lépéseit, valamint az új nodeType, például az IP-és LB-erőforrások hozzáadásához szükséges egyéb erőforrásokat is. Ugyanez a cikk azt is leírja, hogy most kivonja a meglévő nodeType, miután a nodeType több rendelkezésre állási zónával bővült a fürthöz.

* Áttelepítése egy alapszintű LB-t és IP-erőforrást használó nodeType: ezt a megoldást már [itt](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) tekintheti meg az az egyik csomópont-típussal. 
    Az új csomópont-típus esetében az egyetlen különbség az, hogy csak 1 virtuálisgép-méretezési csoport és 1 NodeType van az az összes az az érték helyett az az összes.
* Áttelepítés egy olyan nodeType, amely a standard SKU LB-t és IP-erőforrásokat használja a NSG-ben: kövesse a fentiekben ismertetett eljárást, amely alól nem szükséges új LB-, IP-és NSG-erőforrásokat hozzáadni, és ugyanazokat az erőforrásokat újra fel lehet használni az új nodeType.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png