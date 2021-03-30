---
title: Hálózati módok konfigurálása a Container Serviceshez
description: Ismerje meg, hogyan állíthatja be az Azure Service Fabric által támogatott különböző hálózati módokat.
author: athinanthny
ms.topic: conceptual
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: e6174f35bd54b3ca0b2c5240a663369350b30ce8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86241896"
---
# <a name="service-fabric-container-networking-modes"></a>Service Fabric tároló hálózatkezelési módjai

A Container Services Azure Service Fabric-fürtje alapértelmezés szerint **NAT** hálózati módot használ. Ha több tároló szolgáltatás is figyel ugyanazon a porton, és a NAT-mód is használatban van, a telepítési hibák fordulhatnak elő. Ha több, ugyanazon a porton figyelt tároló-szolgáltatást szeretne támogatni, Service Fabric **nyitott** hálózatkezelési módot kínál (5,7-es és újabb verziók). Nyitott módban minden egyes Container Service belső, dinamikusan hozzárendelt IP-címmel rendelkezik, amely támogatja több, ugyanazon a porton figyelt szolgáltatást.  

Ha egy tároló szolgáltatás statikus végponttal rendelkezik a szolgáltatás jegyzékfájljában, akkor az üzembe helyezési hibák nélkül létrehozhat és törölhet új szolgáltatásokat Megnyitási mód használatával. Ugyanazt a Docker-compose. YML fájlt is használhatja statikus port-hozzárendelésekkel több szolgáltatás létrehozásához.

Amikor egy tároló szolgáltatás újraindítja vagy áthelyezi a fürt egy másik csomópontjára, az IP-cím megváltozik. Ezért nem ajánlott a dinamikusan hozzárendelt IP-cím használatával felderíteni a Container Services szolgáltatást. A szolgáltatás felderítéséhez csak a Service Fabric elnevezési szolgáltatás vagy a DNS-szolgáltatást kell használnia. 

>[!WARNING]
>Az Azure összesen 65 356 IP-címet tesz lehetővé virtuális hálózatonként. A csomópontok számának és a tároló szolgáltatás példányainak (amelyek nyílt üzemmódot használnak) összege nem haladhatja meg a virtuális hálózaton belüli 65 356 IP-címeket. Nagy sűrűségű helyzetekben javasolt a NAT hálózati mód használata. Emellett más függőségek, például a terheléselosztó más [korlátozásokat](../azure-resource-manager/management/azure-subscription-service-limits.md) is figyelembe vesznek. Egy csomóponton jelenleg akár 50 IP-cím lett tesztelve és bizonyítottan stabil. 
>

## <a name="set-up-open-networking-mode"></a>Nyitott hálózati mód beállítása

1. A Azure Resource Manager sablon beállítása. A fürterőforrás **fabricSettings** szakaszában engedélyezze a DNS-szolgáltatást és az IP-szolgáltatót: 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```
    
2. A virtuálisgép-méretezési csoport erőforrásának hálózati profil szakaszának beállítása. Ez lehetővé teszi több IP-cím konfigurálását a fürt mindegyik csomópontján. A következő példa egy Windows/Linux Service Fabric-fürthöz egy csomóponton öt IP-címet állít be. Az egyes csomópontokon öt szolgáltatási példány is figyelheti a portot. Ahhoz, hogy az öt IP-cím elérhető legyen a Azure Load Balancer, regisztrálja az öt IP-címet a Azure Load Balancer háttér-címkészlet az alábbi ábrán látható módon.  A változókat a sablon tetejéhez is hozzá kell adnia a változók szakaszban.

    Adja hozzá ezt a szakaszt a változókhoz:

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    ```
    
    Adja hozzá ezt a szakaszt a virtuálisgép-méretezési csoport erőforrásához:

    ```json   
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 
3. Csak Windows-fürtök esetén állítson be egy olyan Azure hálózati biztonsági csoport (NSG) szabályt, amely az UDP/53 portot nyitja meg a virtuális hálózat számára a következő értékekkel:

   |Beállítás |Érték |
   | --- | --- |
   |Prioritás |2000 |
   |Name |Custom_Dns  |
   |Forrás |VirtualNetwork |
   |Destination (Cél) | VirtualNetwork |
   |Szolgáltatás | DNS (UDP/53) |
   |Művelet | Engedélyezés  |

4. A hálózati mód megadása az alkalmazás jegyzékfájljában az egyes szolgáltatásokhoz: `<NetworkConfig NetworkType="Open">` . A hálózati mód **megnyitásakor** a szolgáltatás dedikált IP-címet kap. Ha nincs megadva mód, a szolgáltatás alapértelmezett értéke **NAT** mód. A következő manifest-példában a `NodeContainerServicePackage1` és a `NodeContainerServicePackage2` szolgáltatások mindegyike ugyanazon a porton figyel (mindkét szolgáltatás figyeli a szolgáltatást `Endpoint1` ). Ha meg van adva a hálózati mód, a `PortBinding` konfigurációk nem adhatók meg.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    A Windows-fürtökön belül különböző hálózati módokat is összekeverheti és egyeztetheti egy alkalmazásban. Egyes szolgáltatások használhatnak nyitott üzemmódot, míg mások NAT-módot használnak. Ha egy szolgáltatás NAT mód használatára van konfigurálva, akkor a szolgáltatás által figyelt portnak egyedinek kell lennie.

    >[!NOTE]
    >Linux-fürtökön a különböző szolgáltatások hálózati módjainak keverése nem támogatott. 
    >

5. Ha a **megnyitási** mód van kiválasztva, a szolgáltatási jegyzékfájl **végpont** -definíciójának explicit módon a végpontnak megfelelő programkódra kell mutatnia, még akkor is, ha a szolgáltatáscsomag csak egy csomaggal rendelkezik. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. A Windows rendszerben a virtuális gépek újraindításakor a rendszer újra létrehozza a nyitott hálózatot. Ennek célja, hogy enyhítse a mögöttes problémát a hálózati veremben. Az alapértelmezett viselkedés a hálózat újbóli létrehozása. Ha ezt a viselkedést ki kell kapcsolni, a következő konfigurációt is használhatja, amelyet egy konfiguráció-frissítés követ.

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>Következő lépések
* [A Service Fabric-alkalmazásmodell megismerése](service-fabric-application-model.md)
* [További információ a Service Fabric Service manifest-erőforrásokról](./service-fabric-service-manifest-resources.md)
* [Windows-tároló üzembe helyezése Service Fabric Windows Server 2016 rendszeren](service-fabric-get-started-containers.md)
* [Docker-tároló üzembe helyezése Linuxon Service Fabric](service-fabric-get-started-containers-linux.md)
