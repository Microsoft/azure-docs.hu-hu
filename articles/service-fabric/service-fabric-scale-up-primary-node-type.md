---
title: Azure Service Fabric elsődleges csomópont-típus vertikális felskálázása
description: A Service Fabric-fürt vertikális méretezéséhez adjon hozzá egy új csomópont-típust, és távolítsa el az előzőt.
ms.date: 12/11/2020
ms.author: pepogors
ms.topic: how-to
ms.openlocfilehash: 325ece761481077171a670c52e9d98071237601a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251179"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Service Fabric-fürt elsődleges csomóponttípusának vertikális felskálázása

Ez a cikk azt ismerteti, hogyan lehet felskálázást végezni egy Service Fabric-fürt elsődleges csomópont-típusát minimális állásidővel. Service Fabric fürtcsomópontok típusának frissítésére vonatkozó általános stratégia a következő:

1. Adjon hozzá egy új csomópont-típust a Service Fabric-fürthöz, amelyet a frissített (vagy módosított) virtuálisgép-méretezési csoport SKU és konfiguráció alapján támogat. Ez a lépés egy új terheléselosztó, alhálózat és nyilvános IP-cím beállítását is magában foglalja a méretezési csoport számára.

1. Ha az eredeti és a frissített méretezési csoportok is futnak egymás mellett, tiltsa le az eredeti csomópont-példányokat egy időben, hogy a rendszerszolgáltatások (vagy az állapot-nyilvántartó szolgáltatások replikái) át legyenek telepítve az új méretezési csoportba.

1. Ellenőrizze, hogy a fürt és az új csomópontok állapota Kifogástalan-e, majd távolítsa el az eredeti méretezési készletet (és a kapcsolódó erőforrásokat) és a csomópont állapotát a törölt csomópontok számára.

Az alábbi lépés végigvezeti a virtuális gépek méretének és operációs rendszerének a megadásával, amely az [ezüst tartósságú](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), egyetlen, öt csomóponttal rendelkező méretezési csoporttal támogatott Az elsődleges csomópont típusát fogjuk frissíteni:

- A virtuális gép méretétől *Standard_D2_V2* a *standard D4_V2ig*, és
- A virtuális gép operációs rendszerének *Windows server 2016 Datacenter tárolókkal* a *Windows Server 2019 Datacenter tárolókkal*.

> [!WARNING]
> Az eljárás üzemi fürtön való megkísérlése előtt javasoljuk, hogy tanulmányozza a minta sablonokat, és ellenőrizze a folyamatot egy tesztelési fürtön. Előfordulhat, hogy a fürt rövid ideig nem érhető el.
>
> Ha a fürt állapota nem kifogástalan, ne próbálkozzon az elsődleges csomópont típusú vertikális Felskálázási eljárással, mivel ez a művelet csak a fürt destabilizálására szolgál.

Az alábbi lépésről lépésre haladó Azure-telepítési sablonokat fogjuk használni a minta-frissítési forgatókönyv elvégzéséhez: https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade

## <a name="set-up-the-test-cluster"></a>A tesztelési fürt beállítása

Állítsa be a kezdeti Service Fabric tesztelési fürtöt. Először [töltse le](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) a forgatókönyv végrehajtásához használni kívánt Azure Resource Manager-mintákat.

Ezután jelentkezzen be az Azure-fiókjába.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Ezután nyissa meg a [*parameters.js*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/parameters.json) fájlt, és frissítse a értékét a következőre: `clusterName` valami egyedi (az Azure-on belül).

A következő parancsok végigvezetik egy új önaláírt tanúsítvány létrehozásán és a tesztelési fürt üzembe helyezésén. Ha már rendelkezik egy használni kívánt tanúsítvánnyal, ugorjon [a meglévő tanúsítvány használata a fürt üzembe helyezéséhez](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Önaláírt tanúsítvány létrehozása és a fürt üzembe helyezése

Először rendelje hozzá az Service Fabric-fürt üzembe helyezéséhez szükséges változókat. Módosítsa a, a, a és a értékét az `resourceGroupName`  `certSubjectName` `parameterFilePath` `templateFilePath` adott fiókhoz és környezethez:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$parameterFilePath = "C:\parameters.json"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
```

> [!NOTE]
> A parancs futtatása előtt győződjön meg arról, hogy a `certOutputFolder` hely létezik a helyi gépen, mielőtt új Service Fabric fürtöt szeretne üzembe helyezni.

Ezután telepítse a Service Fabric test-fürtöt:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Miután az üzembe helyezés befejeződött, keresse meg a *. pfx* -fájlt a `$certPfx` helyi gépen, és importálja a tanúsítványtárolóba:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

A művelet visszaküldi a tanúsítvány ujjlenyomatát, amellyel mostantól [csatlakozhat az új fürthöz](#connect-to-the-new-cluster-and-check-health-status) , és megtekintheti annak állapotát. (Hagyja ki a következő szakaszt, amely a fürt üzembe helyezésének alternatív megközelítése.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Meglévő tanúsítvány használata a fürt üzembe helyezéséhez

Másik lehetőségként meglévő Azure Key Vault tanúsítvány használatával telepítheti a tesztelési fürtöt. Ehhez meg kell [szereznie a Key Vault és a tanúsítvány ujjlenyomatára mutató hivatkozásokat](#obtain-your-key-vault-references) .

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Ezután jelöljön ki egy erőforráscsoport-nevet a fürthöz, és állítsa be a `templateFilePath` és a `parameterFilePath` helyet:

> [!NOTE]
> A kijelölt erőforráscsoport már léteznie kell, és ugyanabban a régióban kell lennie, mint a Key Vault.

```powershell
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
$parameterFilePath = "C:\parameters.json"
```

Végül futtassa a következő parancsot a kezdeti tesztelési fürt üzembe helyezéséhez:

```powershell
# Deploy the initial test cluster
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Kapcsolódás az új fürthöz és az állapot állapotának ellenõrzése

Kapcsolódjon a fürthöz, és győződjön meg arról, hogy mind az öt csomópontja kifogástalan állapotban van (a `clusterName` és a `thumb` változókat a saját értékeire cseréli):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Ezzel készen áll a frissítési eljárás megkezdésére.

## <a name="deploy-a-new-primary-node-type-with-upgraded-scale-set"></a>Új elsődleges csomópont-típus üzembe helyezése frissített méretezési csoporttal

A csomópontok típusának (vertikális skálázás) frissítéséhez először egy új, a méretezési csoport és a támogató erőforrások által támogatott csomópont-típust kell üzembe helyezni. Az új méretezési csoport elsődlegesként () lesz megjelölve `isPrimary: true` , ugyanúgy, mint az eredeti méretezési csoport (kivéve, ha nem elsődleges csomópont típusú frissítést végez). A következő szakaszban létrehozott erőforrások végső soron a fürt új elsődleges csomópont-típusa lesz, és a rendszer törli az eredeti elsődleges csomópont típusú erőforrásokat.

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>A fürt sablonjának frissítése a frissített méretezési csoporttal

Az alábbi, az eredeti fürt központi telepítési sablonjának szakasz – szakasz szerinti módosításaival új elsődleges csomópont-típust és támogató erőforrásokat adhat hozzá.

Az ehhez a lépéshez szükséges módosítások már megtörténtek a sablonban lévő [*Step1-AddPrimaryNodeType.jsban*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step1-AddPrimaryNodeType.json) , és a következők részletesen ismertetik ezeket a módosításokat. Ha szeretné, kihagyhatja a magyarázatot, és folytathatja a [Key Vault hivatkozásainak beszerzését](#obtain-your-key-vault-references) és [a frissített sablon üzembe helyezését](#deploy-the-updated-template) , amely új elsődleges csomópont-típust ad hozzá a fürthöz.

> [!Note]
> Győződjön meg arról, hogy az eredeti csomópont típusa, a méretezési csoport, a terheléselosztó, a nyilvános IP-cím és az eredeti elsődleges csomópont típusú alhálózat alapján egyedi neveket használ, mivel ezeket az erőforrásokat a folyamat egy későbbi lépése fogja törölni.

#### <a name="create-a-new-subnet-in-the-existing-virtual-network"></a>Új alhálózat létrehozása a meglévő virtuális hálózaton

```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```

#### <a name="create-a-new-public-ip-with-a-unique-domainnamelabel"></a>Új nyilvános IP-cím létrehozása egyedi Domainnamelabel értékkel

```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt1')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```

#### <a name="create-a-new-load-balancer-for-the-public-ip"></a>Új terheléselosztó létrehozása a nyilvános IP-címhez

```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```

#### <a name="create-a-new-virtual-machine-scale-set-with-upgraded-vm-and-os-skus"></a>Új virtuálisgép-méretezési csoport létrehozása (frissített virtuális géppel és operációs rendszerű SKU-val)

Csomópont típusa ref

```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

Virtuális gép termékváltozata

```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

OPERÁCIÓS RENDSZER SKU

```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

Továbbá győződjön meg arról, hogy a számítási feladathoz szükséges további bővítmények is elérhetők.

#### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Új elsődleges csomópont-típus hozzáadása a fürthöz

Most, hogy az új csomópont típusa (vmNodeType1Name) rendelkezik a saját nevével, alhálózatával, IP-címével, terheléselosztó és méretezési csoporttal, a többi változót újra felhasználhatja az eredeti csomópont típusától (például, `nt0applicationEndPort` `nt0applicationStartPort` , és `nt0fabricTcpGatewayPort` ):

```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```

Miután végrehajtotta a sablon és a paraméterek fájljaiban történt összes változást, folytassa a következő szakasszal a Key Vault referenciáinak beolvasásához és a frissítések fürtön való telepítéséhez.

### <a name="obtain-your-key-vault-references"></a>A Key Vault-referenciák beszerzése

A frissített konfiguráció üzembe helyezéséhez több hivatkozásra lesz szüksége a Key Vault tárolt fürt tanúsítványára. Az értékek megkeresésének legegyszerűbb módja a Azure Portal. A következők szükségesek:

* **A fürt tanúsítványának Key Vault URL-címe.** A Azure Portal Key Vault válassza a **tanúsítványok**  >  *a kívánt tanúsítvány*  >  **titkos azonosítóját**:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **A fürt tanúsítványának ujjlenyomata.** (Ha [a kezdeti fürthöz kapcsolódott az](#connect-to-the-new-cluster-and-check-health-status) állapotának vizsgálatához, valószínűleg már ezt is megteheti.) Ugyanabból a tanúsítványból (**a tanúsítvány**  >  *a kívánt tanúsítvánnyal*) Azure Portal másolja az **X. 509 SHA-1 ujjlenyomatot (hexadecimális)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **A Key Vault erőforrás-azonosítója.** A Azure Portal Key Vault válassza a **Tulajdonságok**  >  **erőforrás-azonosító** elemet:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>A frissített sablon üzembe helyezése

Módosítsa az `templateFilePath` igény szerint, és futtassa a következő parancsot:

```powershell
# Deploy the new node type and its resources
$templateFilePath = "C:\Step1-AddPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Amikor az üzembe helyezés befejeződik, ellenőrizze újra a fürt állapotát, és győződjön meg arról, hogy a csomópontok minden csomópontja kifogástalan állapotú.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-node-type"></a>A vetőmag-csomópontok migrálása az új csomópont-típusba

Most már készen áll arra, hogy az eredeti csomópont típusát nem elsődlegesként frissítse, és megkezdje a csomópontok letiltását. Mivel a csomópontok le vannak tiltva, a fürt rendszerszolgáltatásai és a magok csomópontjai áttelepülnek az új méretezési csoportba.

### <a name="unmark-the-original-node-type-as-primary"></a>Az eredeti csomópont típusának megjelölése elsődlegesként

Először távolítsa el a `isPrimary` megjelölést a sablonból az eredeti csomópont-típusból.

```json
{
    "isPrimary": false,
}
```

Ezután telepítse a sablont a frissítéssel. Ez elindítja a vetőmag-csomópontok áttelepítését az új méretezési csoportba.

```powershell
$templateFilePath = "C:\Step2-UnmarkOriginalPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!Note]
> Eltarthat egy ideig a magok csomópontjának az új méretezési csoportba való áttelepítésének befejezéséhez. Az adatkonzisztencia garantálása érdekében egyszerre csak egy mag-csomópont módosítható. Minden egyes mag-csomópont változásához szükség van egy fürt frissítésére; így a magok csomópontjának cseréje két fürt frissítését igényli (egyet a csomópontok hozzáadásához és eltávolításához). A példában szereplő öt mag-csomópont frissítése tíz fürt frissítését eredményezi.

A Service Fabric Explorer használatával figyelheti a vetőmag-csomópontok áttelepítését az új méretezési csoportba. Az eredeti csomópont típusú (nt0vm) csomópontok mindegyikének *false értékűnek* kell lennie a **mag csomópont** oszlopban, és az új csomópont típusa (nt1vm) *igaz* lesz.

### <a name="disable-the-nodes-in-the-original-node-type-scale-set"></a>Csomópontok letiltása az eredeti csomópont típusú méretezési csoportokban

Miután az összes vetőmag-csomópontot áttelepítette az új méretezési csoportba, letilthatja az eredeti méretezési csoport csomópontjait.

```powershell
# Disable the nodes in the original scale set.
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```

A Service Fabric Explorer használatával figyelheti az eredeti méretezési csoport csomópontjainak a *letiltott* állapotba való *letiltásának* folyamatát.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status.png" alt-text="A letiltott csomópontok állapotának megjelenítése Service Fabric Explorer":::

Az ezüst és az arany tartóssága érdekében egyes csomópontok letiltott állapotba kerülnek, míg mások *letiltási* állapotban maradhatnak. A Service Fabric Explorer a csomópontok **részletek** lapján a Letiltás állapotot. Ha a *EnsurePartitionQuorem* (az infrastruktúra-szolgáltatási partíciók Kvórumának biztosítása) *függőben lévő biztonsági ellenőrzését* jeleníti meg, akkor biztonságos a folytatás.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status-disabling.png" alt-text="Folytathatja az adatgyűjtés leállítását és a &quot;letiltás&quot; állapotba ragadt csomópontok eltávolítását, ha a &quot;EnsurePartitionQuorum&quot; függőben lévő biztonsági ellenőrzését is megjeleníti.":::

Ha a fürt bronz tartósságú, várjon, amíg az összes csomópont le nem éri a *letiltott* állapotot.

### <a name="stop-data-on-the-disabled-nodes"></a>A letiltott csomópontokon lévő adatsorok leállítása

Most leállíthatja az adatvesztést a letiltott csomópontokon.

```powershell
# Stop data on the disabled nodes.
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```

## <a name="remove-the-original-node-type-and-cleanup-its-resources"></a>Távolítsa el az eredeti csomópont típusát, és törölje erőforrásait

Készen áll az eredeti csomópont-típus és a hozzá tartozó erőforrások eltávolítására a vertikális skálázási eljárás megkötése érdekében.

### <a name="remove-the-original-scale-set"></a>Az eredeti méretezési csoport eltávolítása

Először távolítsa el a csomópont-típus biztonsági méretezési csoportját.

```powershell
$scaleSetName = "nt0vm"
$scaleSetResourceType = "Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```

### <a name="delete-the-original-ip-and-load-balancer-resources"></a>Az eredeti IP-és terheléselosztó-erőforrások törlése

Most már törölheti az eredeti IP-címet és a Load Balancer erőforrásait. Ebben a lépésben a DNS-nevet is frissíti.

> [!Note]
> Ez a lépés nem kötelező, ha már használja a *standard* SKU nyilvános IP-címet és a Load balancert. Ebben az esetben több méretezési csoportot vagy csomópont-típust is használhat ugyanahhoz a terheléselosztó alá.

Futtassa az alábbi parancsokat, és `$lbname` szükség szerint módosítsa az értéket.

```powershell
# Delete the original IP and load balancer resources
$lbName = "LB-sftestupgrade-nt0vm"
$lbResourceType = "Microsoft.Network/loadBalancers"
$ipResourceType = "Microsoft.Network/publicIPAddresses"
$oldPublicIpName = "PublicIP-LB-FE-nt0vm"
$newPublicIpName = "PublicIP-LB-FE-nt1vm"

$oldPrimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldPrimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldPrimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbName -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

### <a name="remove-node-state-from-the-original-node-type"></a>Csomópont állapotának eltávolítása az eredeti csomópont-típusból

Az eredeti csomópont típusú csomópontok mostantól *hibát* jeleznek **az állapotukban.** Távolítsa el a csomópont állapotát a fürtből.

```powershell
# Remove state of the obsolete nodes from the cluster
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Removing node state..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```

A Service Fabric Explorernak most csak az új csomópont-típus (nt1vm) öt csomópontját kell tükröznie, az *OK* állapotának megfelelő értékekkel. A fürt állapotának állapota továbbra is *hibát* jelez. A következő lépésekkel frissítjük a sablont, hogy az tükrözze a legújabb módosításokat és az újbóli üzembe helyezést.

### <a name="update-the-deployment-template-to-reflect-the-newly-scaled-up-primary-node-type"></a>A központi telepítési sablon frissítése az újonnan felméretezett elsődleges csomópont típusának megfelelően

Az ehhez a lépéshez szükséges módosítások már megtörténtek a sablonban lévő [*Step3-CleanupOriginalPrimaryNodeType.jsban*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step3-CleanupOriginalPrimaryNodeType.json) , és a következő szakaszokban részletesen ismertetjük a sablon változásait. Ha szeretné, kihagyhatja a magyarázatot, és folytathatja a [frissített sablon üzembe helyezését](#deploy-the-finalized-template) és az oktatóanyag elvégzését.

#### <a name="update-the-cluster-management-endpoint"></a>A fürt felügyeleti végpontjának frissítése

Frissítse a fürtöt `managementEndpoint` a központi telepítési sablonban, hogy az új IP-re hivatkozzon (a *vmNodeType0Name* *vmNodeType1Name*-vel való frissítésével).

```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```

#### <a name="remove-the-original-node-type-reference"></a>Az eredeti csomópont-típus hivatkozásának eltávolítása

Távolítsa el az eredeti csomópont-típus hivatkozását a Service Fabric erőforrásból a központi telepítési sablonban:

```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```

#### <a name="configure-health-policies-to-ignore-existing-errors"></a>Állapotházirendek konfigurálása a meglévő hibák figyelmen kívül hagyásához

Csak ezüst és magasabb tartósságú fürtök esetén frissítse a sablonban lévő fürterőforrás-erőforrást, és konfigurálja az állapotfigyelő házirendeket az alkalmazás állapotának figyelmen kívül hagyásával úgy, hogy az `fabric:/System` alább megadott módon hozzáadja a *applicationDeltaHealthPolicies* a fürterőforrás-tulajdonságok területen. Az alábbi szabályzat figyelmen kívül hagyja a meglévő hibákat, de nem engedélyezi az új állapottal kapcsolatos hibákat.

```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```

#### <a name="remove-supporting-resources-for-the-original-node-type"></a>Az eredeti csomópont-típus támogatási erőforrásainak eltávolítása

Távolítsa el az eredeti csomópont-típushoz kapcsolódó összes egyéb erőforrást az ARM-sablonból és a Parameters fájlból. Törölje a következőket:

```json
    "vmImagePublisher": {
      "value": "MicrosoftWindowsServer"
    },
    "vmImageOffer": {
      "value": "WindowsServer"
    },
    "vmImageSku": {
      "value": "2016-Datacenter-with-Containers"
    },
    "vmImageVersion": {
      "value": "latest"
    },
```

#### <a name="deploy-the-finalized-template"></a>A véglegesített sablon üzembe helyezése

Végezetül telepítse a módosított Azure Resource Manager sablont.

```powershell
# Deploy the updated template file
$templateFilePath = "C:\Step3-CleanupOriginalPrimaryNodeType"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!NOTE]
> Ez a lépés eltarthat egy ideig, általában akár két óráig is.

A frissítés a beállításokat a *InfrastructureService* fogja módosítani; Ezért szükség van egy csomópont újraindítására. Ebben az esetben a rendszer figyelmen kívül hagyja a *forceRestart* . A paraméter `upgradeReplicaSetCheckTimeout` Megadja azt a maximális időtartamot, ameddig Service Fabric a partíció biztonságos állapotba kerül, ha még nem biztonságos állapotban van. Miután a biztonsági ellenőrzés egy csomóponton lévő összes partícióra kiterjed, Service Fabric folytatja a frissítést a csomóponton. A paraméter értéke `upgradeTimeout` 6 órára csökkenthető, de a maximális biztonság érdekében 12 órát kell használni.

Miután az üzembe helyezés befejeződött, ellenőrizze Azure Portal, hogy a Service Fabric erőforrás állapota *kész*. Ellenőrizze, hogy el tudja-e érni az új Service Fabric Explorer végpontot, a **fürt állapotának állapota** *rendben* van-e, és hogy a telepített alkalmazások megfelelően működnek-e.

Ezzel a beállítással vertikálisan méretezhető a fürt elsődleges csomópontjának típusa!

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [adhat hozzá csomópont-típust fürthöz](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Az [alkalmazások méretezhetőségének](service-fabric-concepts-scalability.md)megismerése.
* [Azure-fürt méretezése vagy](service-fabric-tutorial-scale-cluster.md)kibontása.
* Az [Azure-fürtöket programozott módon méretezheti](service-fabric-cluster-programmatic-scaling.md) a Fluent Azure számítási SDK használatával.
* [Önálló fürt méretezése vagy](service-fabric-cluster-windows-server-add-remove-nodes.md)kibontása.
