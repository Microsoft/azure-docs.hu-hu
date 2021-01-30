---
title: Fürtcsomópontok frissítése az Azure Managed Disks használatára
description: A következőképpen frissíthet egy meglévő Service Fabric-fürtöt az Azure Managed Disks használatára a fürt minimális vagy leállása nélkül.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: c374c4536309a13abcf8c882b041a9c5357878e5
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090654"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Fürtcsomópontok frissítése az Azure Managed Disks használatára

Az [Azure Managed](../virtual-machines/managed-disks-overview.md) Disks az ajánlott lemezes tárolási ajánlat az Azure Virtual Machines szolgáltatásban az adattároláshoz. A Service Fabric számítási feladatok rugalmasságát a felügyelt lemezek használatára szolgáló virtuálisgép-méretezési csoportok frissítésével növelheti. A következőképpen frissíthet egy meglévő Service Fabric-fürtöt az Azure Managed Disks használatára a fürt minimális vagy leállása nélkül.

Service Fabric fürtcsomópont a felügyelt lemezek használatára való frissítésének általános stratégiája a következő:

1. Helyezzen üzembe egy, az adott csomópont típusú, másképpen duplikált virtuálisgép-méretezési készletet, de a virtuálisgép- [](/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) `osDisk` méretezési csoport telepítési sablonjának szakaszához hozzáadott managedDisk objektummal. Az új méretezési csoportnak ugyanahhoz a terheléselosztó/IP-címhez kell tartoznia, mint az eredeti, így az ügyfelek nem tapasztalnak szolgáltatás-kimaradást az áttelepítés során.

2. Ha az eredeti és a frissített méretezési csoportok is futnak egymás mellett, tiltsa le az eredeti csomópont-példányokat egy időben, hogy a rendszerszolgáltatások (vagy az állapot-nyilvántartó szolgáltatások replikái) át legyenek telepítve az új méretezési csoportba.

3. Ellenőrizze, hogy a fürt és az új csomópontok állapota Kifogástalan-e, majd távolítsa el a törölt csomópontok eredeti méretezési csoportját és csomópontjának állapotát.

Ez a cikk végigvezeti egy példa-fürt elsődleges csomópont-típusának a felügyelt lemezek használatára való frissítésének lépésein, és a fürt leállásának elkerülésével (lásd az alábbi megjegyzést). A példaként szolgáló tesztelési fürt kezdeti állapota egy [ezüst tartósságú](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)csomópontból áll, amely egyetlen, öt csomóponttal rendelkező méretezési csoporttal rendelkezik.

> [!NOTE]
> Egy alapszintű SKU Load Balancer korlátai megakadályozzák a további méretezési csoport hozzáadását. Javasoljuk, hogy Ehelyett használja a standard SKU Load balancert. További információ: [a két SKU összevetése](../load-balancer/skus.md).

> [!CAUTION]
> Ezt az eljárást csak akkor fogja tapasztalni, ha a fürt DNS-függőségeivel rendelkezik (például [Service Fabric Explorerhoz](service-fabric-visualizing-your-cluster.md)való hozzáféréskor). Az [előtér-szolgáltatásokra vonatkozó ajánlott eljárás](/azure/architecture/microservices/design/gateway) az, hogy a csomópontok típusai előtt valamilyen [terheléselosztó](/azure/architecture/guide/technology-choices/load-balancing-overview) legyen elérhető, hogy leállás nélkül lehessen lecserélni a csomópontokat.

Az alábbi [sablonok és parancsmagok](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) a frissítési forgatókönyv elvégzéséhez használni kívánt Azure Resource Manager. A sablon módosításait a [frissített méretezési csoport üzembe helyezése az alábbi elsődleges csomópont-típushoz című](#deploy-an-upgraded-scale-set-for-the-primary-node-type)  részben találja.

## <a name="set-up-the-test-cluster"></a>A tesztelési fürt beállítása

Állítsa be a kezdeti Service Fabric tesztelési fürtöt. Először [töltse le](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) a forgatókönyv végrehajtásához használni kívánt Azure Resource Manager-mintákat.

Ezután jelentkezzen be az Azure-fiókjába.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

A következő parancsok végigvezetik egy új önaláírt tanúsítvány létrehozásán és a tesztelési fürt üzembe helyezésén. Ha már rendelkezik egy használni kívánt tanúsítvánnyal, ugorjon [a meglévő tanúsítvány használata a fürt üzembe helyezéséhez](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Önaláírt tanúsítvány létrehozása és a fürt üzembe helyezése

Először rendelje hozzá az Service Fabric-fürt üzembe helyezéséhez szükséges változókat. Módosítsa a, a, a és a értékét az `resourceGroupName`  `certSubjectName` `parameterFilePath` `templateFilePath` adott fiókhoz és környezethez:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> A parancs futtatása előtt győződjön meg arról, hogy a `certOutputFolder` hely létezik a helyi gépen, mielőtt új Service Fabric fürtöt szeretne üzembe helyezni.

Ezután nyissa meg a [*Initial-1NodeType-UnmanagedDisks.parameters.js*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) fájlt, és módosítsa a és a értékét, `clusterName` hogy azok `dnsName` megfeleljenek a PowerShellben megadott dinamikus értékeknek, és mentse a módosításokat.

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

A művelet visszaküldi a tanúsítvány ujjlenyomatát, amelyet az [új fürthöz való kapcsolódáshoz](#connect-to-the-new-cluster-and-check-health-status) fog használni, és megtekintheti annak állapotát. (Hagyja ki a következő szakaszt, amely a fürt üzembe helyezésének alternatív megközelítése.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Meglévő tanúsítvány használata a fürt üzembe helyezéséhez

A tesztelési fürt üzembe helyezéséhez meglévő Azure Key Vault tanúsítványt is használhat. Ehhez meg kell [szereznie a Key Vault és a tanúsítvány ujjlenyomatára mutató hivatkozásokat](#obtain-your-key-vault-references) .

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Nyissa meg a [*Initial-1NodeType-UnmanagedDisks.parameters.js*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) fájlt, és módosítsa a és a értékeit `clusterName` `dnsName` valami egyedire.

Végül jelöljön ki egy erőforráscsoport-nevet a fürthöz, és állítsa be `templateFilePath` a `parameterFilePath` *kezdeti 1NodeType-UnmanagedDisks* fájlok és helyeit:

> [!NOTE]
> A kijelölt erőforráscsoport már léteznie kell, és ugyanabban a régióban kell lennie, mint a Key Vault.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Végül futtassa a következő parancsot a kezdeti tesztelési fürt üzembe helyezéséhez:

```powershell
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

Kapcsolódjon a fürthöz, és győződjön meg arról, hogy mind az öt csomópontja kifogástalan állapotban van (cserélje `clusterName` `thumb` le a fürt változóit):

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

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Továbbfejlesztett méretezési csoport üzembe helyezése az elsődleges csomópont típusaként

A csomópontok frissítéséhez vagy *vertikális méretezéséhez* telepíteni kell a csomópont típusú virtuálisgép-méretezési csoport egy másolatát, amely egyébként azonos az eredeti méretezési csoporttal (többek között a következőre való hivatkozással, `nodeTypeRef` `subnet` és), kivéve, `loadBalancerBackendAddressPools` hogy az tartalmazza a kívánt frissítést/módosításokat, valamint a saját külön alhálózatát és a bejövő NAT-címkészletet. Mivel elsődleges csomópont-típust frissítünk, az új méretezési csoport elsődlegesként () lesz megjelölve `isPrimary: true` , ugyanúgy, mint az eredeti méretezési csoport. (A nem elsődleges csomópont típusú frissítésekhez egyszerűen hagyja ki ezt.)

Az egyszerűség kedvéért a szükséges módosítások már az *upgrade-1NodeType-2ScaleSets-ManagedDisks* [sablonban](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) és a [Parameters](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json) fájlokban is megtörténtek.

A következő szakaszokban részletesen ismertetjük a sablon változásait. Ha szeretné, kihagyhatja a magyarázatot, és folytathatja a [frissítési eljárás következő lépését](#obtain-your-key-vault-references).

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>A fürt sablonjának frissítése a frissített méretezési csoporttal

Az alábbi, az eredeti fürt központi telepítési sablonjának szakasz – szakasz szerinti módosításai az elsődleges csomópont típusához tartozó frissített méretezési csoport hozzáadására szolgálnak.

#### <a name="parameters"></a>Paraméterek

Paraméterek hozzáadása az új méretezési csoport példányának nevéhez, darabszámához és méretéhez. Vegye figyelembe, hogy az `vmNodeType1Name` új méretezési csoport egyedi, míg a darabszám és a méret érték megegyezik az eredeti méretezési csoporttal.

**Sablonfájl**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**Parameters fájl**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>Változók

A központi telepítési sablon `variables` szakaszban adja meg az új méretezési csoport bejövő NAT-címkészlet bejegyzését.

**Sablonfájl**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Források

A központi telepítési sablon *erőforrásai* szakaszban adja hozzá az új virtuálisgép-méretezési készletet, szem előtt tartva ezeket a dolgokat:

* Az új méretezési csoport ugyanarra a csomópont-típusra hivatkozik, mint az eredeti:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* Az új méretezési csoport ugyanarra a terheléselosztó háttérbeli címére és alhálózatra hivatkozik (de egy másik terheléselosztó bejövő NAT-készletet használ):

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* Az eredeti méretezési csoporthoz hasonlóan az új méretezési csoport is elsődleges csomópont-típusként van megjelölve. (A nem elsődleges csomópontok típusának frissítésekor hagyja ki ezt a módosítást.)

    ```json
    "isPrimary": true,
    ```

* Az eredeti méretezési csoporttól eltérően az új méretezési csoport a felügyelt lemezek használatára van frissítve.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

Miután végrehajtotta a sablon és a paraméterek fájljaiban történt összes változást, folytassa a következő szakasszal a Key Vault referenciáinak beolvasásához és a frissítések fürtön való telepítéséhez.

### <a name="obtain-your-key-vault-references"></a>A Key Vault-referenciák beszerzése

A frissített konfiguráció üzembe helyezéséhez először szerezzen be több hivatkozást a Key Vault tárolt fürt-tanúsítványra. Az értékek megkeresésének legegyszerűbb módja a Azure Portal. A következők szükségesek:

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

Módosítsa a `parameterFilePath` és `templateFilePath` szükség szerint, majd futtassa a következő parancsot:

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Ha az üzembe helyezés befejeződött, ellenőrizze újra a fürt állapotát, és győződjön meg arról, hogy az összes tíz csomópont (az eredeti és öt az új méretezési csoporton) kifogástalan állapotú.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>A vetőmag-csomópontok migrálása az új méretezési csoportba

Most már készen áll az eredeti méretezési csoport csomópontjainak letiltására. Mivel ezek a csomópontok le lesznek tiltva, a rendszerszolgáltatások és a vetőmag-csomópontok átkerülnek az új méretezési csoport virtuális gépei felé, mert az elsődleges csomópont-típusként is meg van jelölve.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

A Service Fabric Explorer segítségével figyelheti a magok áttelepítését az új méretezési csoportba, és az eredeti méretezési csoport csomópontjainak a *letiltott* állapotba való *letiltásának* folyamatát.

![A letiltott csomópontok állapotának megjelenítése Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Eltarthat egy ideig, amíg az eredeti méretezési csoport összes csomópontján el nem végzi a letiltási műveletet. Az adatkonzisztencia garantálása érdekében egyszerre csak egy mag-csomópont módosítható. Minden egyes mag-csomópont változásához szükség van egy fürt frissítésére; így a magok csomópontjának cseréje két fürt frissítését igényli (egyet a csomópontok hozzáadásához és eltávolításához). A példában szereplő öt mag-csomópont frissítése tíz fürt frissítését eredményezi.

## <a name="remove-the-original-scale-set"></a>Az eredeti méretezési csoport eltávolítása

A letiltási művelet befejezése után távolítsa el a méretezési csoportját.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

Service Fabric Explorer az eltávolított csomópontok (és így a *fürt állapota*) mostantól *hibás* állapotban jelennek meg.

![A hibás állapotú letiltott csomópontok megjelenítése Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Távolítsa el az elavult csomópontokat a Service Fabric-fürtből a fürt állapotának *OK* értékre való visszaállításához.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Service Fabric Explorer a hibás állapotú csomópontok eltávolításakor](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta, hogyan frissíthet egy Service Fabric-fürt virtuálisgép-méretezési csoportjait a felügyelt lemezek használatára, miközben elkerüli a szolgáltatás leállását a folyamat során. A kapcsolódó témakörökkel kapcsolatos további információkért tekintse meg az alábbi forrásokat.

Az alábbiak végrehajtásának módját ismerheti meg:

* [Service Fabric-fürt elsődleges csomóponttípusának vertikális felskálázása](service-fabric-scale-up-primary-node-type.md)

* [Méretezési csoport sablonjának átalakítása felügyelt lemezek használatára](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Service Fabric csomópont típusának eltávolítása](service-fabric-how-to-remove-node-type.md)

Lásd még:

* [Minta: fürtcsomópontok frissítése az Azure Managed Disks használatára](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade)

* [Vertikális skálázási megfontolások](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)