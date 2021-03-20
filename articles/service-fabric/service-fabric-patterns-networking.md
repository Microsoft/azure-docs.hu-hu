---
title: Az Azure Service Fabric hálózatkezelési mintái
description: Ismerteti a Service Fabric általános hálózati mintáit, valamint azt, hogyan lehet fürtöt létrehozni az Azure hálózatkezelési funkciói segítségével.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 20bd5e931307725016c3e2ad69dae91214b2caab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87421467"
---
# <a name="service-fabric-networking-patterns"></a>Service Fabric hálózati minták
Az Azure Service Fabric-fürtöt más Azure hálózati szolgáltatásokkal is integrálhatja. Ebben a cikkben bemutatjuk, hogyan hozhat létre olyan fürtöket, amelyek a következő szolgáltatásokat használják:

- [Meglévő virtuális hálózat vagy alhálózat](#existingvnet)
- [Statikus nyilvános IP-cím](#staticpublicip)
- [Csak belső terheléselosztó](#internallb)
- [Belső és külső terheléselosztó](#internalexternallb)

Service Fabric a standard virtuálisgép-méretezési csoportokban fut. A virtuálisgép-méretezési csoportokban használható bármely funkció Service Fabric-fürttel is használható. A virtuálisgép-méretezési csoportokhoz és a Service Fabric Azure Resource Manager sablonjainak hálózati fejezetei azonosak. Egy meglévő virtuális hálózatra való üzembe helyezés után egyszerűen beépíthet más hálózati szolgáltatásokat, például az Azure ExpressRoute, az Azure VPN Gateway, a hálózati biztonsági csoportot és a virtuális hálózati társítást.

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>A fürt lekérdezésének engedélyezése a Service Fabric erőforrás-szolgáltató számára

A Service Fabric az egyik aspektusban található egyéb hálózatkezelési funkcióktól egyedi. A [Azure Portal](https://portal.azure.com) belsőleg a Service Fabric erőforrás-szolgáltató használatával hívja meg a fürtöt, hogy információkat kapjon a csomópontokról és az alkalmazásokról. A Service Fabric erőforrás-szolgáltatóhoz nyilvánosan elérhető bejövő hozzáférésre van szükség a HTTP-átjáró portján (alapértelmezés szerint a 19080-es port) a felügyeleti végponton. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) a felügyeleti végpont használatával kezeli a fürtöt. A Service Fabric erőforrás-szolgáltató ezt a portot használja a fürt adatainak lekérdezéséhez, hogy megjelenjen a Azure Portal. 

Ha a 19080-es port nem érhető el a Service Fabric erőforrás-szolgáltatótól, akkor a portálon *nem található* üzenet, például a csomópontok és az alkalmazások listája üresen jelenik meg. Ha szeretné megtekinteni a fürtöt a Azure Portalban, a terheléselosztó számára közzé kell tenni egy nyilvános IP-címet, és a hálózati biztonsági csoportnak engedélyeznie kell a bejövő port 19080-as forgalmát. Ha a telepítés nem felel meg a követelményeknek, a Azure Portal nem jeleníti meg a fürt állapotát.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Sablonok

Az összes Service Fabric-sablon a [githubon](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking)található. A sablonokat a következő PowerShell-parancsokkal telepítheti. Ha a meglévő Azure Virtual Network-sablont vagy a statikus nyilvános IP-sablont telepíti, először olvassa el a jelen cikk [kezdeti beállítás](#initialsetup) szakaszát.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Kezdeti beállítás

### <a name="existing-virtual-network"></a>Meglévő virtuális hálózat

Az alábbi példában egy ExistingRG-vnet nevű meglévő virtuális hálózattal kezdjük a **ExistingRG** erőforráscsoporthoz. Az alhálózat neve alapértelmezett. Ezek az alapértelmezett erőforrások akkor jönnek létre, amikor a Azure Portal használatával hozza létre a szabványos virtuális gépet (VM). A virtuális hálózatot és az alhálózatot a virtuális gép létrehozása nélkül is létrehozhatja, de a fürt meglévő virtuális hálózathoz való hozzáadásának fő célja, hogy hálózati kapcsolatot biztosítson más virtuális gépekhez. A virtuális gép létrehozása jó példa arra, hogy egy meglévő virtuális hálózatot általában hogyan használják. Ha a Service Fabric-fürt csak belső terheléselosztó használata nyilvános IP-cím nélkül, akkor a virtuális gépet és annak nyilvános IP-címét biztonságos *Jump Box*-ként használhatja.

### <a name="static-public-ip-address"></a>Statikus nyilvános IP-cím

A statikus nyilvános IP-címek általában egy dedikált erőforrás, amelyet a rendszer külön felügyel a virtuális gépről vagy a hozzájuk rendelt virtuális gépektől. Dedikált hálózati erőforráscsoporthoz van kiépítve (a Service Fabric fürterőforrás-csoporton belül). Hozzon létre egy staticIP1 nevű statikus nyilvános IP-címet ugyanabban a ExistingRG-erőforráscsoporthoz, vagy a Azure Portal vagy a PowerShell használatával:

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Service Fabric sablon

A cikkben szereplő példákban a Service Fabric template.jst használjuk. A sablon létrehozása előtt a szokásos portál varázsló segítségével töltheti le a sablont a portálról. Használhatja az egyik [minta sablont](https://github.com/Azure-Samples/service-fabric-cluster-templates)is, például a [biztonságos, öt csomópontos Service Fabric fürtöt](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Meglévő virtuális hálózat vagy alhálózat

1. Módosítsa az alhálózati paramétert a meglévő alhálózat nevére, majd vegyen fel két új paramétert a meglévő virtuális hálózatra való hivatkozáshoz:

    ```json
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

   Emellett a "virtualNetworkName" nevű paramétert is megadhatja, így nem fogja kérni a virtuális hálózat nevének megadását a Azure Portal fürt üzembe helyezés paneljén.

2. A Megjegyzés ki `nicPrefixOverride` attribútuma `Microsoft.Compute/virtualMachineScaleSets` , mivel meglévő alhálózatot használ, és ezt a változót az 1. lépésben letiltotta.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Módosítsa a `vnetID` változót úgy, hogy az a meglévő virtuális hálózatra mutasson:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Távolítsa el `Microsoft.Network/virtualNetworks` az erőforrásait, hogy az Azure ne hozzon létre új virtuális hálózatot:

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
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
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. Jegyezze fel a virtuális hálózatot a (z) `dependsOn` attribútumával `Microsoft.Compute/virtualMachineScaleSets` , így nem függ új virtuális hálózat létrehozásával:

    ```json
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. A sablon üzembe helyezése:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Az üzembe helyezés után a virtuális hálózatnak tartalmaznia kell az új méretezési csoport virtuális gépeket. A virtuálisgép-méretezési csoport csomópontjának a meglévő virtuális hálózatot és alhálózatot kell megjelenítenie. RDP protokoll (RDP) használatával is elérheti a virtuális hálózatban már meglévő virtuális GÉPET, és pingelheti az új méretezési csoportbeli virtuális gépeket:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Egy másik példaként tekintse [meg az egyiket, amely nem jellemző a Service Fabricra](https://github.com/gbowerman/azure-myriad/tree/main/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statikus nyilvános IP-cím

1. Paraméterek hozzáadása a meglévő statikus IP-erőforráscsoport, a név és a teljes tartománynév (FQDN) nevéhez:

    ```json
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Távolítsa el a `dnsName` paramétert. (A statikus IP-címnek már van egy.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Adjon hozzá egy változót a meglévő statikus IP-címhez való hivatkozáshoz:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Távolítsa el `Microsoft.Network/publicIPAddresses` az erőforrásait, hogy az Azure ne hozzon létre új IP-címet:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Jegyezze fel az IP-címet a (z) `dependsOn` attribútumból `Microsoft.Network/loadBalancers` , így nem függ új IP-cím létrehozásával:

    ```json
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. Az `Microsoft.Network/loadBalancers` erőforrásban módosítsa a `publicIPAddress` elemét, `frontendIPConfigurations` hogy az újonnan létrehozott helyett a meglévő statikus IP-címet hivatkozzon:

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. Az `Microsoft.ServiceFabric/clusters` erőforrásban váltson a `managementEndpoint` statikus IP-cím DNS teljes tartománynevére. Ha biztonságos fürtöt használ, győződjön meg róla, hogy a *http://* a *https://* értékre módosítja. (Vegye figyelembe, hogy ez a lépés csak Service Fabric fürtökre vonatkozik. Ha virtuálisgép-méretezési készletet használ, ugorja át ezt a lépést.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. A sablon üzembe helyezése:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Az üzembe helyezést követően láthatja, hogy a terheléselosztó a másik erőforráscsoport nyilvános statikus IP-címéhez van kötve. A Service Fabric ügyfél-kapcsolódási végpont és [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) végpont a statikus IP-cím DNS teljes tartománynevére mutat.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Csak belső terheléselosztó

Ez a forgatókönyv a külső Load balancert az alapértelmezett Service Fabric sablonban cseréli le egy csak belső terheléselosztó esetén. Tekintse meg a [cikk korábbi részében ismertetett](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) következményeket a Azure Portal és a Service Fabric erőforrás-szolgáltató vonatkozásában.

1. Távolítsa el a `dnsName` paramétert. (Nincs szükség.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Ha statikus kiosztási módszert használ, statikus IP-cím paramétert is hozzáadhat. Ha dinamikus kiosztási módszert használ, ezt a lépést nem kell végrehajtania.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Távolítsa el `Microsoft.Network/publicIPAddresses` az erőforrásait, hogy az Azure ne hozzon létre új IP-címet:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Távolítsa el a (z) IP-cím `dependsOn` attribútumát `Microsoft.Network/loadBalancers` , így az új IP-cím létrehozása nem függ. Adja hozzá a virtuális hálózat `dependsOn` attribútumot, mert a terheléselosztó mostantól a virtuális hálózat alhálózatán múlik:

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Módosítsa a terheléselosztó `frontendIPConfigurations` beállítását a használatával `publicIPAddress` , egy alhálózat és a használatával `privateIPAddress` . `privateIPAddress` egy előre definiált statikus belső IP-címet használ. Ha dinamikus IP-címet szeretne használni, távolítsa el az `privateIPAddress` elemet, majd váltson `privateIPAllocationMethod` **dinamikusra**.

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. Az `Microsoft.ServiceFabric/clusters` erőforrásban váltson `managementEndpoint` úgy, hogy a belső terheléselosztó-címnek mutasson. Ha biztonságos fürtöt használ, győződjön meg róla, hogy a *http://* a *https://* értékre módosítja. (Vegye figyelembe, hogy ez a lépés csak Service Fabric fürtökre vonatkozik. Ha virtuálisgép-méretezési készletet használ, ugorja át ezt a lépést.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. A sablon üzembe helyezése:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Az üzembe helyezés után a terheléselosztó a magánhálózati statikus 10.0.0.250 IP-címét használja. Ha ugyanazon a virtuális hálózaton van egy másik gép, a belső [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) -végpontra léphet. Vegye figyelembe, hogy a terheléselosztó mögött található egyik csomóponthoz csatlakozik.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Belső és külső terheléselosztó

Ebben az esetben a meglévő egycsomópontos külső terheléselosztó típust kell kezdenie, és hozzá kell adnia egy belső terheléselosztó-t ugyanahhoz a csomópont-típushoz. A háttér-címkészlet csatolt háttér-portot csak egyetlen terheléselosztó számára lehet hozzárendelni. Válassza ki, hogy melyik terheléselosztó fogja tartalmazni az alkalmazás portjait, és hogy melyik terheléselosztó fogja a felügyeleti végpontokat (19000-es és 19080-as portok). Ha a felügyeleti végpontokat a belső terheléselosztó fölé helyezi, vegye figyelembe a [cikkben korábban](#allowing-the-service-fabric-resource-provider-to-query-your-cluster)tárgyalt Service Fabric erőforrás-szolgáltatói korlátozásokat. Az általunk használt példában a felügyeleti végpontok a külső terheléselosztó alatt maradnak. Hozzáadhat egy 80-es porthoz tartozó portot is, és elhelyezheti a belső terheléselosztó számára.

Egy két csomópontot tartalmazó fürtben az egyik csomópont típusa a külső terheléselosztó. A másik csomópont típusa a belső terheléselosztó. Ha két csomópontos típusú fürtöt szeretne használni, a portálon létrehozott kétcsomópontos sablon (amely két terheléselosztó esetén is elérhető), a második terheléselosztó belső terheléselosztó értékre vált. További információ: [csak belső](#internallb) terheléselosztó szakasz.

1. Adja hozzá a statikus belső terheléselosztó IP-címének paraméterét. (Dinamikus IP-cím használatával kapcsolatos megjegyzésekért lásd a cikk korábbi szakaszait.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Adjon hozzá egy Application port 80 paramétert.

3. Ha a meglévő hálózati változók belső verzióit szeretné hozzáadni, másolja és illessze be őket, és adja hozzá a "-int" nevet a következőhöz:

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Ha az 80-es Application porton alapuló, portál által generált sablonnal kezdődik, az alapértelmezett portál sablon hozzáadja a AppPort1 (80-es port) a külső terheléselosztó számára. Ebben az esetben távolítsa el a AppPort1 a külső terheléselosztó és a mintavételek közül `loadBalancingRules` , így hozzáadhatja azt a belső terheléselosztó számára:

    ```json
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. Adjon hozzá egy második `Microsoft.Network/loadBalancers` erőforrást. A [belső terheléselosztási szakaszban létrehozott](#internallb) belső terheléselosztó hasonlónak tűnik, de az "-int" terheléselosztó változókat használja, és csak az 80-es alkalmazás-portot implementálja. Ez a művelet eltávolítja `inboundNatPools` az RDP-végpontokat a nyilvános terheléselosztó esetében is. Ha az RDP-t a belső terheléselosztó esetében szeretné használni, váltson át `inboundNatPools` a külső terheléselosztó erre a belső Load balancerre:

    ```json
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. Az `networkProfile` erőforrás esetében `Microsoft.Compute/virtualMachineScaleSets` adja hozzá a belső háttér-címkészletet:

    ```json
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. A sablon üzembe helyezése:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Az üzembe helyezést követően két terheléselosztó látható az erőforráscsoporthoz. Ha megkeresi a terheléselosztó szolgáltatásait, megtekintheti a nyilvános IP-címhez hozzárendelt nyilvános IP-címet és felügyeleti végpontokat (19000-es és 19080-as portok). A belső terheléselosztó számára hozzárendelt statikus belső IP-címet és alkalmazás-végpontot (80-as portot) is láthatja. Mindkét terheléselosztó ugyanazt a virtuálisgép-méretezési készletet használja.

## <a name="notes-for-production-workloads"></a>Az éles számítási feladatokra vonatkozó megjegyzések

A fenti GitHub-sablonok úgy vannak kialakítva, hogy működjenek az alapértelmezett SKU-val az Azure standard Load Balancer (SLB) és az alapszintű SKU használatával. Ez a SLB nem rendelkezik SLA-val, ezért az éles számítási feladatokhoz a szabványos SKU-t kell használni. Erről további információt az [Azure standard Load Balancer áttekintése](../load-balancer/load-balancer-overview.md)című témakörben talál. A SLB standard SKU-t használó összes Service Fabric fürtnek biztosítania kell, hogy minden csomópont-típushoz olyan szabály legyen, amely engedélyezi a kimenő forgalmat az 443-as porton. Ez a fürt beállításának befejezéséhez szükséges, és az ilyen szabályok nélküli központi telepítések sikertelenek lesznek. A "belső only" terheléselosztó fenti példájában egy további külső terheléselosztó hozzáadása szükséges a sablonhoz egy olyan szabállyal, amely engedélyezi a kimenő forgalmat a 443-es porton.

## <a name="next-steps"></a>Következő lépések
[Fürt létrehozása](service-fabric-cluster-creation-via-arm.md)

Az üzembe helyezést követően két terheléselosztó látható az erőforráscsoporthoz. Ha megkeresi a terheléselosztó szolgáltatásait, megtekintheti a nyilvános IP-címhez hozzárendelt nyilvános IP-címet és felügyeleti végpontokat (19000-es és 19080-as portok). A belső terheléselosztó számára hozzárendelt statikus belső IP-címet és alkalmazás-végpontot (80-as portot) is láthatja. Mindkét terheléselosztó ugyanazt a virtuálisgép-méretezési készletet használja.
