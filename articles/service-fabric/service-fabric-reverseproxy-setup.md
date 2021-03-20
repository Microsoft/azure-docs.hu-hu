---
title: Azure Service Fabric fordított proxy beállítása
description: Megtudhatja, hogyan állíthatja be és konfigurálhatja a fordított proxy szolgáltatást egy Azure Service Fabric alkalmazáshoz.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: f8a9025a50b2815f0e6030e7baf317b261c8c462
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86256344"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Fordított proxy beállítása és konfigurálása az Azure-ban Service Fabric
A fordított proxy egy opcionális Azure Service Fabric szolgáltatás, amely segít a Service Fabric-fürtökön futó, a http-végpontokkal rendelkező más szolgáltatásokkal való kommunikációban. További információ: [fordított proxy az Azure Service Fabricban](service-fabric-reverseproxy.md). Ez a cikk bemutatja, hogyan állíthatja be és konfigurálhatja a fordított proxykat a fürtben. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Fordított proxy engedélyezése Azure Portal használatával

A Azure Portal lehetőséget biztosít a fordított proxy engedélyezésére új Service Fabric-fürt létrehozásakor. Meglévő fürt nem frissíthető fordított proxy használatára a portálon. 

Ha [Azure Portal használatával hoz létre fürtöt](./service-fabric-cluster-creation-via-portal.md), a fordított proxy konfigurálásához tegye a következőket:

1. A **2. lépés: fürtkonfiguráció** **csomópont típusa konfiguráció** területén válassza a **fordított proxy engedélyezése** lehetőséget.

   ![Fordított proxy engedélyezése a portálon](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. Választható A biztonságos fordított proxy konfigurálásához TLS/SSL-tanúsítványt kell konfigurálnia. A **3. lépés: biztonság**, a **fürt biztonsági beállításainak konfigurálása** elemnél, a **konfiguráció típusa** területen válassza az **Egyéni** lehetőséget. Ezután a **fordított proxy SSL-tanúsítványa** alatt válassza az **SSL-tanúsítvány belefoglalása fordított proxyhoz** lehetőséget, és adja meg a tanúsítvány adatait.

   ![Biztonságos fordított proxy konfigurálása a portálon](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Ha úgy dönt, hogy nem konfigurálja a fordított proxyt egy tanúsítvánnyal a fürt létrehozásakor, később a fürt erőforráscsoporthoz tartozó Resource Manager-sablonnal végezheti el. További információ: [fordított proxy engedélyezése Azure Resource Manager-sablonok használatával](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Fordított proxy engedélyezése Azure Resource Manager-sablonok használatával

Az Azure-beli fürtök esetében a Azure Resource Manager sablonnal engedélyezheti a fordított proxyt Service Fabric. A fordított proxyt engedélyezheti a fürt létrehozásakor, vagy engedélyezheti, ha később frissíti a fürtöt. 

Egy új fürthöz [létrehozhat egy egyéni Resource Manager-sablont](service-fabric-cluster-creation-via-arm.md) , vagy használhat egy minta sablont is. 

Olyan Resource Manager-sablonokat talál, amelyek segítségével biztonságos fordított proxyt konfigurálhat egy Azure-fürthöz a GitHubon található [biztonságos fordított proxy-sablonokban](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample) . A biztonságos fordított proxy tanúsítványokkal történő konfigurálásához és a tanúsítványok átváltásának kezeléséhez tekintse meg a [https fordított proxy konfigurálása biztonságos fürtben](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) című témakört a readme fájlban.

Meglévő fürt esetén a [Azure Portal](../azure-resource-manager/templates/export-template-portal.md), a [PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)vagy az [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md)használatával exportálhatja a fürt erőforráscsoport Resource Manager-sablonját.

A Resource Manager-sablonokkal a következő lépéseket követve engedélyezheti a fordított proxyt:

1. Definiáljon egy portot a fordított proxyhoz a sablon [Paraméterek szakaszában](../azure-resource-manager/templates/template-syntax.md) .

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Adja meg a portot a [**Microsoft. ServiceFabric/Clusters**](/azure/templates/microsoft.servicefabric/clusters) [erőforrástípus szakaszban](../azure-resource-manager/templates/template-syntax.md)található összes NodeType-objektumhoz.

    A portot a paraméter neve, reverseProxyEndpointPort azonosítja.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. A fordított proxy portján a TLS/SSL-tanúsítványok konfigurálásához adja hozzá a tanúsítványt a ***reverseProxyCertificate** _ tulajdonsághoz a _ *Microsoft. ServiceFabric/Clusters* *  [erőforrástípus szakaszban](../azure-resource-manager/templates/template-syntax.md).

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>A fürt tanúsítványának eltérő fordított proxy-tanúsítvány támogatása
 Ha a fordított proxy tanúsítványa eltér a fürtöt tároló tanúsítványtól, akkor a korábban megadott tanúsítványt telepíteni kell a virtuális gépre, és hozzá kell adni a hozzáférés-vezérlési listához (ACL), hogy Service Fabric el tudja érni. Ezt a [**Microsoft. számítási/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets) [erőforrástípus szakaszban](../azure-resource-manager/templates/template-syntax.md)teheti meg. A telepítéshez adja hozzá a tanúsítványt a osProfile. A sablon bővítmény szakasza frissíteni tudja a tanúsítványt az ACL-ben.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Ha a fürttől eltérő tanúsítványokat használ a fordított proxy meglévő fürtön való engedélyezéséhez, telepítse a fordított proxy-tanúsítványt, és frissítse a fürt ACL-listáját a fordított proxy engedélyezése előtt. Fejezze be a [Azure Resource Manager sablon](service-fabric-cluster-creation-via-arm.md) központi telepítését a korábban említett beállításokkal, mielőtt elkezdené a központi telepítést, hogy engedélyezze a fordított proxy használatát a 1-3. lépésben.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Fordított proxy engedélyezése önálló fürtökön

Önálló fürtök esetében engedélyezze a fordított proxyt a fájl ClusterConfig.js. A fordított proxyt engedélyezheti a fürt létrehozásakor vagy egy meglévő fürt konfigurációjának frissítésével. Ha többet szeretne megtudni a fájlok ClusterConfig.jsján elérhető beállításokról, tekintse meg a [különálló fürt beállításai](./service-fabric-cluster-manifest.md)című témakört.

A következő lépések bemutatják, hogy milyen beállításokkal engedélyezheti a fordított proxyt, és ha szükséges, a fordított proxyt X. 509 tanúsítvánnyal is biztonságossá teheti. 

1. A fordított proxy engedélyezéséhez állítsa be a **reverseProxyEndpointPort** értékét a fürt konfigurációjának **Tulajdonságok** területén. A következő JSON azt mutatja be, hogy a fordított proxy végpontjának portja 19081-ra van állítva a "NodeType0" típusú csomópontok esetében:

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. Választható Biztonságos fordított proxy esetén a **Tulajdonságok** területen konfigurálja a tanúsítványt a **Biztonság** szakaszban. 
   - Fejlesztési vagy tesztelési környezetben használhatja a **ReverseProxyCertificate** beállítást:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - Éles környezetben a **ReverseProxyCertificateCommonNames** beállítás ajánlott:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   Ha többet szeretne megtudni a tanúsítványok önálló fürthöz való konfigurálásáról és kezeléséről, valamint részletesebben tájékozódhat a fordított proxy védelméhez használt tanúsítványok konfigurálásáról, tekintse meg a [X509-alapú biztonság](./service-fabric-windows-cluster-x509-security.md)című témakört.

Miután módosította a ClusterConfig.jsfájlt a fordított proxy engedélyezéséhez, kövesse a [fürtkonfiguráció frissítése](service-fabric-cluster-config-upgrade-windows-server.md) a fürt módosításainak leküldéséhez című témakör útmutatását.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Fordított proxy kicserélése nyilvános porton keresztül Azure Load Balancer

A fordított proxy Azure-fürtön kívüli kezeléséhez állítson be Azure Load Balancer szabályokat és egy Azure Health-mintavételt a fordított proxy porthoz. Ezeket a lépéseket a fürt létrehozása után bármikor elvégezheti a Azure Portal vagy a Resource Manager-sablon használatával. 

> [!WARNING]
> Ha a fordított proxy portját Load Balancerban konfigurálja, a fürtben lévő összes olyan szolgáltatás, amely a HTTP-végpontot teszi elérhetővé, a fürtön kívülről is címezhető. Ez azt jelenti, hogy a belső használatra szánt szolgáltatásokhoz egy meghatározott kártevő felhasználó is felderíthető. Ez potenciálisan súlyos biztonsági réseket jelenthet, amelyeket kihasználhat. például:
>
> * Egy rosszindulatú felhasználó egy olyan belső szolgáltatás ismételt meghívásával indíthat el szolgáltatásmegtagadási támadást, amely nem rendelkezik megfelelően megerősített támadási felülettel.
> * Előfordulhat, hogy egy rosszindulatú felhasználó helytelenül formázott csomagokat kézbesít egy belső szolgáltatásnak, ami nem szándékolt viselkedést eredményez.
> * A belsőnek szánt szolgáltatás olyan magánjellegű vagy bizalmas adatokat ad vissza, amelyeket nem lehet a fürtön kívüli szolgáltatásoknak kitenni, így ez a bizalmas információ rosszindulatú felhasználónak is kikerül. 
>
> Győződjön meg arról, hogy teljes mértékben megértette és enyhítse a fürt és a rajta futó alkalmazások potenciális biztonsági következményeit, mielőtt a fordított proxy nyilvános portját tenné. 
>

Ha a fordított proxyt nyilvánosan szeretné kiterjeszteni egy önálló fürthöz, a művelet módja a fürtöt működtető rendszertől függ, és a jelen cikk hatókörén kívül esik. Az előző figyelmeztetés arról, hogy a fordított proxy nyilvánosan, de továbbra is érvényes.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Fordított proxy elérhetővé tétele Azure Portal használatával 

1. A Azure Portal kattintson a fürthöz tartozó erőforráscsoporthoz, majd a fürthöz tartozó terheléselosztó elemre.
2. Ha hozzá szeretne adni egy állapot-mintavételt a fordított proxyhoz, a terheléselosztó ablak bal oldali ablaktábláján, a **Beállítások** területen kattintson az **állapot**-mintavételek elemre. Ezután kattintson az **Add (Hozzáadás** ) gombra az állapot-mintavételi ablak tetején, és adja meg a fordított proxykiszolgáló részleteit, majd kattintson **az OK** gombra. Alapértelmezés szerint a fordított proxy portja 19081, hacsak nem módosította a fürt létrehozásakor.

   ![Fordított proxy állapot-mintavételének konfigurálása](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Ha Load Balancer szabályt szeretne hozzáadni a fordított proxy portjának megjelenítéséhez, a terheléselosztó ablak bal oldali ablaktábláján, a **Beállítások** területen kattintson a terheléselosztási **szabályok** elemre. Ezután kattintson a **Hozzáadás** gombra a terheléselosztási szabályok ablak tetején, és adja meg a fordított proxy portszámának részleteit. Győződjön meg arról, hogy **a port értékét arra** a portra állítja be, amelyben a fordított proxy elérhetővé válik, a **háttér port** értéke a fordított proxy engedélyezésekor beállított portra, az **állapot** mintavételi értéke pedig az előző lépésben konfigurált állapot-mintavételi értékre. Szükség szerint adja meg a többi mezőt, majd kattintson **az OK** gombra.

   ![Terheléselosztó szabály konfigurálása fordított proxyhoz](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Fordított proxy elérhetővé tétele Resource Manager-sablonok használatával

A következő JSON ugyanarra a sablonra hivatkozik, amely a [fordított proxy engedélyezése Azure Resource Manager-sablonok](#enable-reverse-proxy-via-azure-resource-manager-templates)használatával. A dokumentum adott szakaszát a Resource Manager-sablonok létrehozásával vagy egy meglévő fürthöz tartozó sablon exportálásával kapcsolatban tekintheti meg.  A módosítások a [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers) [erőforrástípus szakaszon](../azure-resource-manager/templates/template-syntax.md)történnek.

```json
{
    "apiVersion": "[variables('lbApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    ...
    ...
    "loadBalancingRules": [
        ...
        {
            "name": "LBSFReverseProxyRule",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('SFReverseProxyPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"
                },
                "frontendPort": "[parameters('SFReverseProxyPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                },
                "protocol": "tcp"
            }
        }
    ],
    "probes": [
        ...
        {
            "name": "SFReverseProxyProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port":     "[parameters('SFReverseProxyPort')]",
                "protocol": "tcp"
            }
        }  
    ]
}
```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Fordított proxy viselkedésének testreszabása a háló beállításaival

A fordított proxy viselkedését az Azure-ban üzemeltetett fürtök Resource Manager-sablonjában, illetve az önálló fürtökön található fájl ClusterConfig.jsjában szabhatja testre. A fordított proxy viselkedését vezérlő beállítások a fürt **tulajdonságai** szakaszban található **fabricSettings** szakasz [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszában találhatók. 

Beállíthatja például a **DefaultHttpRequestTimeout** értékét úgy, hogy a fordított proxyra irányuló kérések időtúllépését 180 másodpercre állítsa a következő JSON-ként:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

További információ az Azure-fürtökhöz tartozó Fabric-beállítások frissítéséről: [fürtkonfiguráció testreszabása Resource Manager-sablonok használatával](service-fabric-cluster-config-upgrade-azure.md). Önálló fürtök esetén tekintse [meg a fürt beállításainak testreszabása önálló fürtökhöz](service-fabric-cluster-config-upgrade-windows-server.md)című témakört. 

A rendszer több háló-beállítást használ a fordított proxy és a szolgáltatások közötti biztonságos kommunikáció kialakításához. Ezen beállítások részletes ismertetését lásd: [Csatlakozás biztonságos szolgáltatáshoz a fordított proxyval](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Következő lépések
* [A biztonságos HTTP-szolgáltatás továbbításának beállítása a fordított proxyval](service-fabric-reverseproxy-configure-secure-communication.md)
* A fordított proxy konfigurációs beállításaival kapcsolatban lásd: [ApplicationGateway/http szakasz, Service Fabric a fürt beállításainak testreszabása](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
