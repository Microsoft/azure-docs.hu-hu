---
title: Fürt üzembe helyezése több Availability Zones
description: Megtudhatja, hogyan hozhat létre Azure Service Fabric fürtöt a Availability Zones.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: pepogors
ms.openlocfilehash: 9cc2a9d189e7a781dc6ba64a65af022150392485
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727761"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Azure-beli Service Fabric fürt üzembe helyezése a Availability Zones
Availability Zones Azure-ban egy magas rendelkezésre állású ajánlat, amely megvédi az alkalmazásokat és az adatokat az adatközpontok meghibásodásaitól. A rendelkezésre állási zóna egy egyedi fizikai hely, amely független áramellátással, hűtéssel és hálózattal rendelkezik egy Azure-régióban.

Service Fabric a különböző zónákra Availability Zones fürtök a meghatározott zónákhoz rögzített csomóponttípusok üzembe helyezésával. Ez biztosítja az alkalmazások magas rendelkezésre állását. Azure Availability Zones csak bizonyos régiókban érhetők el. További információkért lásd a [Azure Availability Zones áttekintését.](../availability-zones/az-overview.md)

Mintasablonok érhetők el: [Service Fabric rendelkezésre állási zónák közötti sablon létrehozása](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Az Azure-beli fürtök elsődleges csomóponttípusának Service Fabric topológiája, amely több csomópontra is Availability Zones
A Service Fabric fürtök között elosztott Availability Zones biztosítja a fürt állapotának magas rendelkezésre állását. Ha több zónára Service Fabric fürtöt, létre kell hoznia egy elsődleges csomóponttípust a régió által támogatott összes rendelkezésre állási zónában. Ez egyenletesen osztja el a magcsomópontokat az egyes elsődleges csomóponttípusok között.

Az elsődleges csomóponttípushoz ajánlott topológiához az alábbi erőforrásokra van szükség:

* A fürt megbízhatósági szintje a Következőre van állítva: <
* Három elsődlegesként megjelölt csomóponttípus.
    * Minden csomóponttípust a saját, különböző zónákban található virtuálisgép-méretezési csoportjára kell leképezni.
    * Minden virtuálisgép-méretezési csoportnak legalább öt csomóponttal kell lennie (ezüst tartósság).
* Egyetlen, Standard termékváltozatot használó nyilvános IP-erőforrás.
* Egyetlen Load Balancer standard termékváltozatot használó erőforrás.
* Egy NSG, amelyre az az alhálózat hivatkozik, amelyben a virtuálisgép-méretezési készleteket üzembe helyeznie kell.

>[!NOTE]
> A virtuálisgép-méretezési csoport egyetlen elhelyezési csoport tulajdonságát true (igaz) értékre kell állítani.

Az Azure Service Fabric rendelkezésre állási zóna architektúráját bemutató diagram, amely az Azure Service Fabric rendelkezésre állási zóna ![ architektúráját mutatja be.][sf-architecture]

Minta csomópontlista, amely a zónákat tartalmazó virtuálisgép-méretezési csoport FD-/UD-formátumát ábrázolja

 ![Minta csomópontlista, amely a zónákat tartalmazó virtuálisgép-méretezési csoport FD-/UD-formátumát ábrázolja.][sf-multi-az-nodes]

**Szolgáltatásreplikák** eloszlása zónák között: Amikor egy szolgáltatást a zónákat átszűkító nodeType-ekben helyeznek üzembe, a replikák úgy vannak elhelyezve, hogy külön zónákba kerülnek. Ez azért van így, mert a tartalék tartomány az egyes nodeTypes csomópontokon található, és a zónainformációk (pl. FD = fd:/zone1/1 stb.). Például: egy szolgáltatás 5 replikája vagy példánya esetén az elosztás 2–2-1 lesz, és a futásidő megpróbálja biztosítani az egyenlő elosztást az AZ-ben.

**Felhasználói szolgáltatásreplikák** konfigurációja: A rendelkezésre állási zónák közötti csomóponttípusokon üzembe helyezett állapot-szolgáltatásokat a következő konfigurációval kell konfigurálni: replikaszám cél = 9, min = 5. Ez a konfiguráció abban segít, hogy a szolgáltatás akkor is működik, ha az egyik zóna leáll, mivel 6 replika továbbra is működik a másik két zónában. Ilyen esetben az alkalmazásfrissítés is végig fog menni.

**Fürt megbízhatósági szintje:** Ez határozza meg a fürt magcsomópontjainak számát és a rendszerszolgáltatások replikaméretét. Mivel a rendelkezésre állási zónák közötti beállítás több csomóponttal rendelkezik, amelyek zónák között oszlnak el a zóna rugalmasságának biztosításához, a magasabb megbízhatósági érték biztosítja, hogy a csomópontok száma több magcsomópontot és rendszerszolgáltatás-replikát tartalmaz, és egyenletesen oszlik el a zónák között, így a zóna meghibásodása esetén a fürt és a rendszerszolgáltatások nem lesznek akadálytalanok. A "ReliabilityLevel = The" biztosítja, hogy a fürt zónái között 9 magcsomópont legyen 3-as elosztással az egyes zónákban, ezért ez a rendelkezésre állási zónák közötti beállítás ajánlott.

**Zóna down forgatókönyve:** Ha egy zóna leáll, a zónában lévő összes csomópont lefelé megjelenik. Az ezeken a csomópontokon lévő szolgáltatásreplikák szintén leállnak. Mivel a többi zónában replikák is vannak, a szolgáltatás továbbra is válaszol, és az elsődleges replikák a működő zónákba vesznek át. A szolgáltatások figyelmeztetési állapotban jelennek meg, mivel a célreplikák száma még nem érhető el, és mivel a virtuális gépek száma továbbra is több, mint a célreplikák minimális mérete. Ezt követően Service Fabric terheléselosztási rendszer a munkazónákban a konfigurált célreplikák számával egyező replikákat fog elhozni. Ezen a ponton a szolgáltatások kifogástalannak fognak megjelenni. Amikor a lefelé lévő zóna visszatér a terheléselosztáshoz, az összes szolgáltatásreplikát ismét egyenletesen el fogja terjeszteni az összes zónában.

## <a name="networking-requirements"></a>Hálózati követelmények
### <a name="public-ip-and-load-balancer-resource"></a>Nyilvános IP-cím és Load Balancer erőforrás
A zónatulajdonság virtuálisgép-méretezési csoport erőforráson való engedélyezéséhez az adott virtuálisgép-méretezési csoport  által hivatkozott terheléselosztási és IP-erőforrásnak egyaránt standard termékváltozatot kell használnia. Ha a termékváltozat tulajdonsága nélkül hoz létre terheléselosztást vagy IP-erőforrást, az alapszintű termékváltozatot hoz létre, amely nem támogatja a Availability Zones. A Standard termékváltozatú terheléselosztás alapértelmezés szerint minden kívülről származó forgalmat blokkol; A külső forgalom lehetővé helyezéséhez egy NSG-t kell üzembe helyezni az alhálózaton.

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
> A nyilvános IP- és terheléselosztási erőforrásokon nem lehetséges a termékváltozat módosítása. Ha olyan meglévő erőforrásokról migrál, amelyek alapszintű termékváltozatmal rendelkezik, tekintse meg a cikk migrálási szakaszát.

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
standard Load Balancer és a standard nyilvános IP-cím új képességeket és különböző viselkedésmódokat vezet be a kimenő kapcsolatokhoz az alapszintű SKUs-okkal szemben. Ha kimenő kapcsolatot szeretne a Standard SKUs-okkal való munka során, explicit módon meg kell határoznia azt standard nyilvános IP-címekkel vagy standard nyilvános Load Balancer. További információ: [Kimenő kapcsolatok és](../load-balancer/load-balancer-outbound-connections.md) [Azure standard Load Balancer.](../load-balancer/load-balancer-overview.md)

>[!NOTE]
> A standard sablon egy NSG-re hivatkozik, amely alapértelmezés szerint engedélyezi az összes kimenő forgalmat. A bejövő forgalom a felügyeleti műveletekhez szükséges portokra Service Fabric korlátozva. Az NSG-szabályok a követelményeknek megfelelően módosíthatók.

>[!NOTE]
> Minden Service Fabric fürtnek, amely standard termékváltozatú SLB-t használ, gondoskodnia kell arról, hogy minden csomóponttípushoz legyen egy szabály, amely engedélyezi a kimenő forgalmat a 443-as porton. Ez a fürt beállításának befejezéséhez szükséges, és az ilyen szabályok nélküli üzembe helyezés sikertelen lesz.


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Zónák engedélyezése virtuálisgép-méretezési csoporton
Zóna engedélyezéséhez a virtuálisgép-méretezési csoportban a következő három értéket kell szerepelnie a virtuálisgép-méretezési csoport erőforrásában.

* Az első érték a **zones** tulajdonság, amely meghatározza, hogy a virtuálisgép-méretezési csoport melyik rendelkezésre állási zónában legyen üzembe állítva.
* A második érték a "singlePlacementGroup" tulajdonság, amelyet true (igaz) értékre kell állítani.
* A harmadik érték a virtuálisgép-méretezési Service Fabric bővítmény "faultDomainOverride" tulajdonsága. A tulajdonság értékének csak azt a zónát kell tartalmaznia, amelyben a virtuálisgép-méretezési készlet el lesz helyezve. Például: "faultDomainOverride": "az1" Minden virtuálisgép-méretezési csoport erőforrásait ugyanabban a régióban kell elhelyezni, mivel az Azure Service Fabric-fürtök nem támogatják a régiók közötti támogatást.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Több elsődleges csomóponttípus engedélyezése Service Fabric fürterőforrásban
Ha egy fürterőforrásban elsődlegesként egy vagy több csomóponttípust is beállít, állítsa az "isPrimary" tulajdonságot "true" (igaz) értékre. Amikor üzembe helyez egy Service Fabric fürtöt a Availability Zones, három csomóponttípusnak kell lennie különböző zónákban.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Áttelepítés fürtről Availability Zones alapszintű termékváltozat és alapszintű termékváltozat IP Load Balancer használatával
Egy olyan fürt áttelepítéséhez, amely alapszintű termékváltozatú Load Balancer és IP-címet használt, először létre kell hoznia egy teljesen új Load Balancer ip-erőforrást a standard termékváltozat használatával. Ezeket az erőforrásokat nem lehet a helyén frissíteni.

Az új LB-re és IP-címre hivatkozni kell az új, rendelkezésre állási zónák közötti csomóponttípusokban, amelyekre használni szeretne. A fenti példában három új virtuálisgép-méretezési csoport erőforrást adtunk hozzá az 1., 2. és 3. zónához. Ezek a virtuálisgép-méretezési csoportok az újonnan létrehozott terheléselosztásra és IP-címre hivatkoznak, és elsődleges csomóponttípusként vannak megjelölve a fürterőforrás Service Fabric között.

Először hozzá kell adni az új erőforrásokat a meglévő Resource Manager sablonhoz. Ezek az erőforrások a következők:
* Egy standard termékváltozatot használó nyilvános IP-erőforrás.
* Egy Load Balancer standard termékváltozatot használó erőforrás.
* Az alhálózat által hivatkozott NSG, amelyen a virtuálisgép-méretezési készleteket üzembe helyezheti.
* Három csomóponttípus elsődlegesként megjelölve.
    * Minden csomóponttípust a saját, különböző zónákban található virtuálisgép-méretezési csoportjára kell leképezni.
    * Minden virtuálisgép-méretezési csoportnak legalább öt csomóponttal kell lennie (ezüst tartósság).

Ezekre az erőforrásokra a mintasablonban [talál példát.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Miután az erőforrások üzembe helyezése befejeződött, megkezdheti az elsődleges csomóponttípus csomópontjainak letiltását az eredeti fürtből. Mivel a csomópontok le vannak tiltva, a rendszerszolgáltatások át lesznek telepítve a fenti lépésben üzembe helyezett új elsődleges csomóponttípusra.

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

Ha az összes csomópont le van tiltva, a rendszerszolgáltatások az elsődleges csomóponttípuson fognak futni, amely több zónában működik. Ezután eltávolíthatja a letiltott csomópontokat a fürtből. A csomópontok eltávolítása után eltávolíthatja az eredeti IP-címet, Load Balancer virtuálisgép-méretezési csoport erőforrásait.

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

Ezután távolítsa el az ezekre az erőforrásokra mutató hivatkozásokat az Resource Manager üzembe helyezett sablonból.

Az utolsó lépés a DNS-név és a nyilvános IP-cím frissítése.

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

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>(Előzetes verzió) Több rendelkezésre állási zóna engedélyezése egyetlen virtuálisgép-méretezési csoportban

A korábban említett megoldás az AZ-hez egy nodeType típust használ. Az alábbi megoldással a felhasználók 3 AZ-t helyezhetnek üzembe ugyanabban a nodeType-ban.

**Mivel ez a funkció jelenleg előzetes verzióban érhető el, éles környezetben nem támogatott.**

A teljes mintasablon itt [van.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)

![Az Azure Service Fabric rendelkezésre állási zóna architektúrája][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>Zónák konfigurálása virtuálisgép-méretezési csoporton
A zónák virtuálisgép-méretezési csoporton való engedélyezéséhez a következő három értéket kell szerepelnie a virtuálisgép-méretezési csoport erőforrásában.

* Az első érték a **zones** tulajdonság, amely meghatározza a Availability Zones virtuálisgép-méretezési csoportban lévő virtuálisgép-méretezési csoportban található adatokat.
* A második érték a "singlePlacementGroup" tulajdonság, amelyet true (igaz) értékre kell állítani. **A 3 AZ-re kiható méretezési csoport akár 300 virtuális gépre is felskálálhat, még a "singlePlacementGroup = true" érték esetén is.**
* A harmadik érték a "zoneBalance", amely szigorú zónaelosztást biztosít. Ennek "true" (igaz) értéknek kell lennie. Ez biztosítja, hogy a zónák közötti virtuálisgép-elosztás ne legyen kiegyensúlyozva, így ha az egyik zóna leáll, a másik két zóna elegendő virtuális gépekkel rendelkezik annak biztosításához, hogy a fürt ne szakadjon meg. Előfordulhat, hogy a kiegyensúlyozatlan virtuálisgép-elosztású fürtök nem élik át a zóna leállását, mivel előfordulhat, hogy a virtuális gépek többsége a zónában van. A zónák közötti kiegyensúlyozatlan virtuálisgép-elosztás szolgáltatáselhelyezéssel kapcsolatos problémákhoz vezet, & infrastruktúrafrissítések elakadnak. További információ [a zoneBalancing -ről.](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing)
* A FaultDomain és az UpgradeDomain felülbírálásokat nem szükséges konfigurálni.

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
> * **Service Fabric fürtnek legalább egy Elsődleges csomóponttípussal kell lennie. Az Elsődleges nodeTypes tartóssági szintje legyen Silver vagy magasabb.**
> * A virtuálisgép-méretezési csoportra kiterjedő AZ-t legalább 3 rendelkezésre állási zónával kell konfigurálni, a tartóssági szinttől függetlenül.
> * Az AZ silver (vagy magasabb) tartósságú virtuálisgép-méretezési készletben legalább 15 virtuális gépnek kell lennie.
> * Az AZ bronz tartósságú virtuálisgép-méretezési készletben legalább 6 virtuális gépnek kell lennie.

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Több zóna támogatása a nodeType Service Fabric engedélyezése
A Service Fabric nodeType típust engedélyezni kell több rendelkezésre állási zóna támogatásához.

* Az első érték **a multipleAvailabilityZones,** amelyet true értékre kell állítani a nodeType esetében.
* A második érték **az sfZonalUpgradeMode,** és nem kötelező. Ez a tulajdonság nem módosítható, ha a fürtben már létezik több AZ-hez rendelkező csomóponttípus.
  A tulajdonság a frissítési tartományokban található virtuális gépek logikai csoportosítását szabályozza.
  **Ha az érték "Parallel" (Párhuzamos) értékre van állítva:** A nodetype alatt található virtuális gépek UD-kbe lesznek csoportosítva, a zónainformációkat 5 UD-ben figyelmen kívül hagyva. Ez azt eredményezi, hogy az UD0 minden zónában egyszerre lesz frissítve. Ez a telepítési mód gyorsabb a frissítésekhez, de nem ajánlott, mivel az SDP irányelvei szerint a frissítéseket egyszerre csak egy zónában kell alkalmazni.
  **Ha az érték nincs megadva, vagy "Hierarchikus" értékre van állítva:** A virtuális gépek úgy lesznek csoportosítva, hogy legfeljebb 15 virtuális gép zónabeli eloszlását tükrözzék. A 3 zóna mindegyikének 5 UD-jük lesz. Ez biztosítja, hogy a frissítések zóna szerint haladnak, és csak azután váltson a következő zónára, hogy az első zónában 5, lassan, 15 UD-n belül (3 zóna, 5 UD) halad át, ami biztonságosabb a fürt és a felhasználói alkalmazás szempontjából.
  Ez a tulajdonság csak a ServiceFabric-alkalmazások és -kódfrissítések frissítési viselkedését határozza meg. A mögöttes virtuálisgép-méretezési készlet frissítései továbbra is párhuzamosak lesznek az ÖSSZES AZ-ban.
  Ez a tulajdonság nincs hatással az olyan csomóponttípusok UD-elosztására, amelyeken nincs engedélyezve több zóna.
* A harmadik érték a **vmssZonalUpgradeMode = Parallel**. Ezt a *tulajdonságot kötelező konfigurálni* a fürtben, ha egy több AZ-sel rendelkező nodeType van hozzáadva. Ez a tulajdonság határozza meg a virtuálisgép-méretezési készlet frissítésének frissítési módját, amely párhuzamosan fog történni minden AZ-hez egyszerre.
  Jelenleg ez a tulajdonság csak párhuzamosra beállítható.
* Az Service Fabric apiVersion fürterőforrásnak "2020-12-01-preview" vagy újabb verziójúnak kell lennie.
* A fürt kódverziója "7.2.445" vagy újabb legyen.

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
> * A nyilvános IP-Load Balancer erőforrásoknak a cikkben korábban leírtak szerint a Standard termékváltozatot kell használniuk.
> * A nodeType "multipleAvailabilityZones" tulajdonsága csak a nodeType létrehozásakor határozható meg, és később nem módosítható. Ezért a meglévő nodeTypes nem konfigurálható ezzel a tulajdonsággal.
> * Ha az "sfZonalUpgradeMode" nincs megadva vagy "Hierarchikus" lesz, a fürt és az alkalmazások üzembe helyezése lassabb lesz, mivel több frissítési tartomány található a fürtben. Fontos megfelelően módosítani a frissítési szabályzat időtúllépéseit, hogy azok a 15 frissítési tartomány frissítési idejét tartalmazzák. Mind az alkalmazásra, mind a fürtre vonatkozó frissítési szabályzatot frissíteni kell, hogy az üzemelő példány ne haladja meg az Azure-erőforrás-szolgáltatás üzembehelyi időkorlátját (12 óra). Ez azt jelenti, hogy az üzembe helyezésnek 15UD-hez nem szabad 12 órán túlinak lennie, azaz ne vegyen fel 40 percnél többet.
> * Állítsa be a fürt **megbízhatósági szintjét = Tol,** hogy a fürt ne maradjon le az egyzónás forgatókönyvben.

>[!NOTE]
> Az ajánlott eljárás szerint az sfZonalUpgradeMode beállítása Hierarchikus vagy kihagyva. Az üzembe helyezés a virtuális gépek zónaális eloszlását követi, ami kisebb mennyiségű replikát és/vagy példányt befolyásol, ami biztonságosabbá teszi őket.
> Az sfZonalUpgradeMode (SfZonalUpgradeMode) beállításnál a Parallel (Párhuzamos) érték használható, ha az üzembe helyezési sebesség prioritású, vagy csak az állapot nélküli számítási feladat fut több AZ-hez használt csomóponttípuson. Ez azt eredményezi, hogy az UD-ről az összes AZ-ben párhuzamosan történik.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>Migrálás a csomóponttípusba több Availability Zones
Minden migrálási forgatókönyvhöz hozzá kell adni egy új nodeType típust, amely több rendelkezésre állási zónát támogat. Egy meglévő nodeType nem migrálható több zóna támogatásához.
Az itt [található cikk](./service-fabric-scale-up-primary-node-type.md) az új nodeType hozzáadásának részletes lépéseit, valamint az új nodeType típushoz szükséges egyéb erőforrások, például az IP- és LB-erőforrások hozzáadásának lépéseit rögzíti. Ugyanez a cikk azt is bemutatja, hogyan lehet kivezetni a meglévő nodeType típust, miután a fürthöz hozzáadta a több rendelkezésre állási zónával rendelkező nodeType típust.

* Migrálás alapszintű LB- és IP-erőforrásokat használó [](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) nodeType típusról: Ez már le van írva az AZ-nként egy csomóponttípusú megoldás esetében. 
    Az új csomóponttípus esetében az egyetlen különbség az, hogy minden AZ-hez csak 1 virtuálisgép-méretezési készlet és 1 csomóponttípus tartozik, nem pedig minden egyes AZ-hez.
* Áttelepítés a Standard termékváltozatú LB-t és IP-erőforrásokat NSG-n használó nodeType-ból: Kövesse a fent leírt eljárást azzal a kivétellel, hogy nincs szükség új LB-, IP- és NSG-erőforrások hozzáadására, és ugyanazok az erőforrások újra felhasználhatók az új nodeType típusban.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png
