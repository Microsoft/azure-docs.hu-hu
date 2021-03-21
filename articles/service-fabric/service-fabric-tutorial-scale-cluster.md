---
title: Service Fabric-fürt skálázása az Azure-ban
description: Ebből az oktatóanyagból megtudhatja, hogyan méretezhető egy Service Fabric-fürt az Azure-ban és a-ben, és hogyan tisztítható fel a megmaradt erőforrások.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cdc7ba8d6c83ae72ffb8f1afae3954b3a46dc6ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788010"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Oktatóanyag: Service Fabric-fürt skálázása az Azure-ban

Ez az oktatóanyag egy sorozat harmadik része, amely bemutatja, hogyan méretezheti a meglévő fürtöt és a-t. Az oktatóanyag végére elsajátíthatja a fürtök skálázásának és a hátramaradt erőforrások eltávolításának módját.  Az Azure-ban futó fürtök méretezésével kapcsolatos további információkért olvassa el [Service Fabric fürtök méretezését](service-fabric-cluster-scaling.md)ismertető témakört.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Csomópontok hozzáadása és eltávolítása (vertikális felskálázás és méretezés a-ben)
> * Csomópont-típusok hozzáadása és eltávolítása (vertikális felskálázás és méretezés a-ben)
> * Csomópont-erőforrások bővítése (vertikális felskálázás)

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Biztonságos Windows- [fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) létrehozása az Azure-ban sablon használatával
> * [Fürt figyelése](service-fabric-tutorial-monitor-cluster.md)
> * Fürt horizontális fel- és leskálázása
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * [Fürt törlése](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Telepítse [Azure PowerShell](/powershell/azure/install-az-ps) vagy az [Azure CLI](/cli/azure/install-azure-cli)-t.
* Biztonságos Windows- [fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) létrehozása az Azure-ban

## <a name="important-considerations-and-guidelines"></a>Fontos szempontok és irányelvek

Az alkalmazások számítási feladatainak időbeli változása idővel megváltoznak, a meglévő szolgáltatásainak több (vagy kevesebb) erőforrásra van szüksége?  Csomópontok [hozzáadásával vagy eltávolításával](#add-nodes-to-or-remove-nodes-from-a-node-type) növelheti vagy csökkentheti a fürt erőforrásait.

Hozzá kell adnia több mint 100 csomópontot a fürthöz?  Egyetlen Service Fabric csomópont típusa/méretezési csoport legfeljebb 100 csomópontot/virtuális gépet tartalmazhat.  A fürt 100 csomóponton túli méretezéséhez [adjon hozzá további csomópont-típusokat](#add-nodes-to-or-remove-nodes-from-a-node-type).

Az alkalmazása több szolgáltatással rendelkezik, és ezek közül bármelyiknek nyilvánosnak vagy internetkapcsolatnak kell lennie?  A tipikus alkalmazások olyan előtér-átjáró szolgáltatást tartalmaznak, amely egy ügyféltől érkező adatokat fogad, valamint egy vagy több háttér-szolgáltatást, amely az előtér-szolgáltatásokkal kommunikál. Ebben az esetben javasoljuk, hogy [legalább két csomópont-típust adjon hozzá](#add-nodes-to-or-remove-nodes-from-a-node-type) a fürthöz.  

A szolgáltatásai eltérő infrastrukturális igényekkel rendelkeznek, például nagyobb RAM-mal vagy magasabb CPU-ciklusokkal? Az alkalmazás például egy előtér-szolgáltatást és egy háttér-szolgáltatást tartalmaz. Az előtér-szolgáltatás a kisebb méretű virtuális gépeken (például a D2-ben) is futtatható, amelyeken a portok nyitva vannak az interneten. A háttérben futó szolgáltatás azonban nagy számítási igényű, és nagyobb méretű virtuális gépeken (például D4, D6, D15) kell futnia, amelyek nem az internetkapcsolattal rendelkeznek. Ebben az esetben javasoljuk, hogy [két vagy több csomópont-típust adjon hozzá](#add-nodes-to-or-remove-nodes-from-a-node-type) a fürthöz. Ez lehetővé teszi, hogy az egyes csomópont-típusok különböző tulajdonságokkal rendelkezzenek, például az internetkapcsolat vagy a virtuális gép méretével. A virtuális gépek száma egymástól függetlenül méretezhető.

Egy Azure-fürt skálázásakor tartsa szem előtt a következő irányelveket:

* Egyetlen Service Fabric csomópont típusa/méretezési csoport legfeljebb 100 csomópontot/virtuális gépet tartalmazhat.  A fürt 100 csomóponton túli méretezéséhez adjon hozzá további csomópont-típusokat.
* Az éles munkaterheléseket futtató elsődleges csomópontok esetében az arany vagy ezüst [tartóssági szintnek][durability] kell lennie, és mindig legalább öt csomópontnak kell lennie.
* Az állapot-nyilvántartó munkaterheléseket futtató nem elsődleges csomópontok esetében mindig legalább öt csomópontnak kell futnia.
* Az állapot nélküli éles környezetben futó munkaterheléseket futtató nem elsődleges csomópontok esetében mindig legalább két csomópontnak kell futnia.
* Az arany vagy ezüst [tartóssági szintjének][durability] minden csomópont-típusának mindig öt vagy több csomóponttal kell rendelkeznie.
* Ha a (csomópontok eltávolítása a-ből) elsődleges csomópont típusúra történik, soha ne csökkentse a példányok számát a [megbízhatósági szinthez][reliability] szükségesnél kisebb értékre.

További információért olvassa el a [fürt kapacitása című útmutatót](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Erőforráscsoport sablonjának exportálása

A biztonságos Windows- [fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) létrehozása és az erőforráscsoport sikeres beállítása után exportálja az erőforráscsoport Resource Manager-sablonját. A sablon exportálása lehetővé teszi a fürt és erőforrásai jövőbeli üzembe helyezésének automatizálását, mivel a sablon tartalmazza az összes teljes infrastruktúrát.  További információ a sablonok exportálásáról [: Azure Resource Manager erőforráscsoportok kezelése a Azure Portal használatával](../azure-resource-manager/management/manage-resource-groups-portal.md).

1. A [Azure Portal](https://portal.azure.com)nyissa meg a fürtöt tartalmazó erőforráscsoportot (**sfclustertutorialgroup**, ha ezt az oktatóanyagot követi). 

2. A bal oldali ablaktáblán válassza a **központi telepítések** lehetőséget, vagy válassza ki a **központi telepítések** területen található hivatkozást. 

3. Válassza ki a legutóbbi sikeres telepítést a listából.

4. A bal oldali ablaktáblán válassza a **sablon** lehetőséget, majd válassza a **Letöltés** lehetőséget a sablon zip-fájlként való exportálásához.  Mentse a sablont és a paramétereket a helyi számítógépre.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Csomópontok hozzáadása vagy eltávolítása csomópontok közül

A be-és kiskálázás, illetve a horizontális skálázás a fürtben lévő csomópontok számát módosítja. A be-és kiskálázáskor további virtuálisgép-példányokat ad hozzá a méretezési csoporthoz. Ezek a példányok lesznek a Service Fabric által használt csomópontok. A Service Fabric tudja, ha a méretezési csoport új példányokkal bővül (felskálázással), és automatikusan ennek megfelelően jár el. A fürtöt bármikor méretezheti, még akkor is, ha a munkaterhelések futnak a fürtön.

### <a name="update-the-template"></a>A sablon frissítése

[Exportálja a sablont és a paramétereket tartalmazó fájlt](#export-the-template-for-the-resource-group) az erőforráscsoporthoz a legutóbbi központi telepítéshez.  Nyissa meg a *parameters.js* fájlt.  Ha az oktatóanyagban a [sablon][template] alapján telepítette a fürtöt, három csomópont-típus található a fürtben, és három paramétert kell beállítania a csomópontok számának megadásához az egyes csomópontok esetében: *nt0InstanceCount*, *nt1InstanceCount* és *nt2InstanceCount*.  A *nt1InstanceCount* paraméter például beállítja a példányszámot a második csomópont típusához, és beállítja a társított virtuálisgép-méretezési csoportba tartozó virtuális gépek számát.

Így a *nt1InstanceCount* értékének frissítésével módosíthatja a csomópontok számát a második csomópont típusában.  Ne feledje, hogy a csomópont-típusok több mint 100 csomópontra nem méretezhetők.  Az állapot-nyilvántartó munkaterheléseket futtató nem elsődleges csomópontok esetében mindig legalább öt csomópontnak kell futnia. Az állapot nélküli éles környezetben futó munkaterheléseket futtató nem elsődleges csomópontok esetében mindig legalább két csomópontnak kell futnia.

Ha méretezést végez, távolítsa el a csomópontokat a ból, a bronz [tartóssági szint][durability] csomópont-típusát [manuálisan el kell távolítania a csomópontok állapotát](service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  Az ezüst és az arany tartóssági szinten ezeket a lépéseket a platform automatikusan végrehajtja.

### <a name="deploy-the-updated-template"></a>A frissített sablon üzembe helyezése
Mentse a fájlokon a *template.js* és *parameters.js* módosításait.  A frissített sablon üzembe helyezéséhez futtassa a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Vagy az alábbi Azure CLI-paranccsal:
```azurecli
az deployment group create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Csomópont típusának hozzáadása a fürthöz

Minden, az Azure-ban futó Service Fabric-fürtben definiált csomópont-típus [külön virtuálisgép-méretezési csoportként](service-fabric-cluster-nodetypes.md)van beállítva. Ezután mindegyik csomópont-típust külön lehet kezelni. Az egyes csomópont-típusok egymástól függetlenül méretezhetők, különböző portokat nyitnak meg, és különböző kapacitási metrikákat használhatnak. Külön is megváltoztathatja az egyes fürtcsomópontokon futó operációs rendszerbeli SKU-t, de vegye figyelembe, hogy a minta fürtben nem lehet a Windows és a Linux együttes használata. Egyetlen csomópont típusú/méretezési csoport legfeljebb 100 csomópontot tartalmazhat.  További csomópont-típusok/méretezési csoportok hozzáadásával vízszintesen méretezheti a fürtöt több mint 100 csomópontra. A fürtöt bármikor méretezheti, még akkor is, ha a munkaterhelések futnak a fürtön.

### <a name="update-the-template"></a>A sablon frissítése

[Exportálja a sablont és a paramétereket tartalmazó fájlt](#export-the-template-for-the-resource-group) az erőforráscsoporthoz a legutóbbi központi telepítéshez.  Nyissa meg a *parameters.js* fájlt.  Ha az oktatóanyagban a [sablon][template] alapján telepítette a fürtöt, három csomópont-típus található a fürtben.  Ebben a szakaszban egy Resource Manager-sablon frissítésével és telepítésével adhat hozzá egy negyedik csomópont-típust. 

Az új csomópont típusa mellett hozzá kell adnia a társított virtuálisgép-méretezési csoportot is (amely a virtuális hálózat különálló alhálózatán fut) és a hálózati biztonsági csoporttal együtt.  Dönthet úgy is, hogy új vagy meglévő nyilvános IP-címet és Azure Load Balancer-erőforrásokat ad hozzá az új méretezési csoporthoz.  Az új csomópont típusa az ezüst és a "Standard_D2_V2" méretének [tartóssági szintje][durability] .

Adja hozzá a következő új paramétereket a fájl *template.js* :
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

Adja hozzá a következő új változókat a fájl *template.js* :
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

A *template.js* fájlon adjon hozzá egy új alhálózatot a virtuális hálózati erőforráshoz:
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

A fájl *template.js* új nyilvános IP-cím és terheléselosztó erőforrások hozzáadása:
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
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
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

A fájl *template.jsban* vegyen fel új hálózati biztonsági csoportot és virtuálisgép-méretezési csoport erőforrásait.  A virtuálisgép-méretezési csoport Service Fabric bővítmény tulajdonságaiban található NodeTypeRef tulajdonság a méretezési csoportra képezi le a megadott csomópont típusát.

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "ServiceFabricNode",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                            },
                            "publisher": "Microsoft.Azure.ServiceFabric",
                            "settings": {
                                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                "storageAccountEndPoint": "https://core.windows.net/"
                            },
                            "publisher": "Microsoft.Azure.Diagnostics",
                            "settings": {
                                "WadCfg": {
                                    "DiagnosticMonitorConfiguration": {
                                        "overallQuotaInMB": "50000",
                                        "EtwProviders": {
                                            "EtwEventSourceProviderConfiguration": [
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Actors",
                                                    "scheduledTransferKeywordFilter": "1",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableActorEventTable"
                                                    }
                                                },
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Services",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                    }
                                                }
                                            ],
                                            "EtwManifestProviderConfiguration": [
                                                {
                                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                    "scheduledTransferLogLevelFilter": "Information",
                                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricSystemEventTable"
                                                    }
                                                }
                                            ]
                                        }
                                    }
                                },
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
                                        }
                                    }
                                }
                            ],
                            "primary": true
                        }
                    }
                ]
            },
            "osProfile": {
                "adminPassword": "[parameters('adminPassword')]",
                "adminUsername": "[parameters('adminUsername')]",
                "computernamePrefix": "[variables('vmNodeType3Name')]",
                "secrets": [
                    {
                        "sourceVault": {
                            "id": "[parameters('sourceVaultValue')]"
                        },
                        "vaultCertificates": [
                            {
                                "certificateStore": "[parameters('certificateStoreValue')]",
                                "certificateUrl": "[parameters('certificateUrlValue')]"
                            }
                        ]
                    }
                ]
            },
            "storageProfile": {
                "imageReference": {
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
                },
                "osDisk": {
                    "caching": "ReadOnly",
                    "createOption": "FromImage",
                    "managedDisk": {
                        "storageAccountType": "[parameters('storageAccountType')]"
                    }
                }
            }
        }
    },
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

A *template.js* fájlon frissítse a fürterőforrás-t, és adjon hozzá egy új csomópont-típust:
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

Adja hozzá a következő új paramétereket és értékeket a fájl *parameters.js* :
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>A frissített sablon üzembe helyezése
Mentse a fájlokon a *template.js* és *parameters.js* módosításait.  A frissített sablon üzembe helyezéséhez futtassa a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Vagy az alábbi Azure CLI-paranccsal:
```azurecli
az deployment group create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Csomópont típusának eltávolítása a fürtből
Service Fabric-fürt létrehozása után vízszintesen méretezheti a fürtöt egy csomópont-típus (virtuálisgép-méretezési csoport) és annak összes csomópontjának eltávolításával. A fürtöt bármikor méretezheti, még akkor is, ha a munkaterhelések futnak a fürtön. A fürt skálázása esetén az alkalmazások is automatikusan méretezhetők.

> [!WARNING]
> A csomópontok üzemi fürtből való eltávolításához a Remove-AzServiceFabricNodeType használata nem ajánlott, hogy gyakori módon lehessen használni. Ez egy veszélyes parancs, mivel törli a virtuálisgép-méretezési csoport erőforrását a csomópont típusa mögött. 

A csomópont típusának eltávolításához futtassa a [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) parancsmagot.  A csomópont típusának ezüst vagy arany [tartóssági szintűnek][durability]  kell lennie, a parancsmag törli a csomópont-típushoz társított méretezési készletet, és eltarthat egy ideig.  Ezután futtassa a [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) parancsmagot az összes eltávolítani kívánt csomóponton, amely törli a csomópont állapotát, és eltávolítja a csomópontokat a fürtből. Ha vannak szolgáltatások a csomópontokon, akkor a szolgáltatások először egy másik csomópontra kerülnek. Ha a Fürtfelügyelő nem talál csomópontot a replika/szolgáltatás számára, a művelet késleltetve vagy Letiltva lesz.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>Csomópont-erőforrások bővítése 
Service Fabric-fürt létrehozása után függőlegesen méretezheti a fürt csomópontjának típusát (módosítsa a csomópontok erőforrásait), vagy frissítse a csomópont típusú virtuális gépek operációs rendszerét úgy, hogy lecseréli az eredeti csomópont típusát egy új csomópont-típusra (frissített VM SKU vagy operációsrendszer-rendszerkép használatával). További részletekért lásd: [Azure Service Fabric csomópont-típus vertikális Felskálázása](service-fabric-scale-up-primary-node-type.md).

> [!IMPORTANT]
> Soha ne próbálkozzon a virtuális gép SKU-jának vagy operációsrendszer-rendszerképének helyben történő módosításával, amely veszélyes művelet, és nem támogatott.

Ha ez nem lehetséges, létrehozhat egy új fürtöt, és [visszaállíthatja az alkalmazás állapotát](service-fabric-reliable-services-backup-restore.md) (ha van ilyen) a régi fürtből. Nem kell visszaállítani a rendszerszolgáltatások állapotát; a rendszer akkor hozza létre újra, amikor az új fürtre telepíti az alkalmazásait. Ha csak az állapot nélküli alkalmazásokat futtatta a fürtön, akkor csak az alkalmazásait telepíti az új fürtre, nincs szükség a visszaállításra.

### <a name="update-the-template"></a>A sablon frissítése

[Exportálja a sablont és a paramétereket tartalmazó fájlt](#export-the-template-for-the-resource-group) az erőforráscsoporthoz a legutóbbi központi telepítéshez.  Nyissa meg a *parameters.js* fájlt.  Ha az oktatóanyagban a [sablon][template] alapján telepítette a fürtöt, három csomópont-típus található a fürtben.  

A második csomópont típusú virtuális gépek mérete a *vmNodeType1Size* paraméterben van beállítva.  Módosítsa a *vmNodeType1Size* paraméter értékét Standard_D2_V2ról [Standard_D3_V2ra](../virtual-machines/dv2-dsv2-series.md), ami megduplázza az egyes virtuálisgép-példányok erőforrásait.

Mindhárom csomóponthoz tartozó virtuálisgép-SKU a *vmImageSku* paraméterben van beállítva.  Ismét meg kell adni a csomópontok virtuálisgép-SKU-jának módosítását körültekintően, és az elsődleges csomópont típusa nem ajánlott.

### <a name="deploy-the-updated-template"></a>A frissített sablon üzembe helyezése
Mentse a fájlokon a *template.js* és *parameters.js* módosításait.  A frissített sablon üzembe helyezéséhez futtassa a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Vagy az alábbi Azure CLI-paranccsal:
```azurecli
az deployment group create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Csomópontok hozzáadása és eltávolítása (vertikális felskálázás és méretezés a-ben)
> * Csomópont-típusok hozzáadása és eltávolítása (vertikális felskálázás és méretezés a-ben)
> * Csomópont-erőforrások bővítése (vertikális felskálázás)

Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan frissíthetők a fürtök futtatókörnyezetei.
> [!div class="nextstepaction"]
> [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
