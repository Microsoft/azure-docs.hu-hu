---
title: Azure külső terheléselosztó áthelyezése másik Azure-régióba a Azure Portal használatával
description: A Azure Portal használatával helyezzen át egy külső terheléselosztó egyik Azure-régióból a másikba egy Azure Resource Manager sablonnal.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f83ff3d1d03354daef3466c1f48eaa505e378634
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94693749"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Külső Load Balancer áthelyezése másik régióba a Azure Portal használatával

Különböző helyzetekben érdemes áthelyezni egy külső terheléselosztó egyik régióból a másikba. Előfordulhat például, hogy egy másik külső terheléselosztó-t szeretne létrehozni ugyanazzal a konfigurációval a teszteléshez. Előfordulhat, hogy a vész-helyreállítási tervezés részeként másik régióba is át szeretné helyezni a külső terheléselosztó-t.

A szöveges értelemben nem helyezhető át egy külső Azure Load Balancer egyik régióból a másikba. Azonban használhat egy Azure Resource Manager sablont egy külső terheléselosztó meglévő konfigurációjának és nyilvános IP-címének exportálására. Ezt követően az erőforrást egy másik régióban is elvégezheti, ha a terheléselosztó és a nyilvános IP-címet egy sablonba exportálja, módosítja a paramétereket, hogy azok megfeleljenek a célként megadott régiónak, majd üzembe helyezi a sablont az új régióban. A Resource Managerrel és a sablonokkal kapcsolatos további információkért lásd: [erőforráscsoportok exportálása sablonokba](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure külső terheléselosztó abban az Azure-régióban található, amelyről át kívánja helyezni.

- Az Azure külső terheléselosztó nem helyezhető át a régiók között. Az új Load balancert a célként megadott régióban lévő erőforrásokhoz kell rendelnie.

- Külső terheléselosztó konfigurációjának exportálásához és sablon üzembe helyezéséhez egy másik régióban található külső terheléselosztó létrehozásához hozzá kell rendelnie a hálózati közreműködő szerepkört vagy magasabb szintűt.

- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez az elrendezés tartalmaz, de nem korlátozódik a terheléselosztó, a hálózati biztonsági csoportok, a nyilvános IP-címek és a virtuális hálózatok számára.

- Ellenőrizze, hogy az Azure-előfizetése lehetővé teszi-e külső terheléselosztó létrehozását a célként megadott régióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetése elegendő erőforrással rendelkezik a terheléselosztó hozzáadásának támogatásához. Tekintse meg a következőt: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

## <a name="prepare-and-move"></a>Előkészítés és áthelyezés
Az alábbi eljárások azt mutatják be, hogyan készítse elő a külső terheléselosztó az áthelyezéshez egy Resource Manager-sablonnal, és a külső terheléselosztó konfigurációját helyezze át a célként megadott régióba a Azure Portal használatával. Először exportálnia kell a külső Load Balancer nyilvános IP-konfigurációját.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>A nyilvános IP-sablon exportálása és a nyilvános IP-cím üzembe helyezése a portálról

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.
2. Keresse meg azt az erőforráscsoportot, amely a forrás nyilvános IP-címet tartalmazza, majd jelölje ki.
3. Válassza a **Beállítások**  >  **Exportálás sablon** lehetőséget.
4. Válassza a **telepítés** lehetőséget a **sablon exportálása** lehetőség alatt.
5. Válassza a **sablon**  >  **szerkesztése paraméterek** lehetőséget, hogy megnyissa a parameters.jsfájlt az online szerkesztőben.
8. A nyilvános IP-cím paraméterének szerkesztéséhez módosítsa az **Value** tulajdonságot a forrás nyilvános IP-címe **paraméterei** alatt a célként megadott nyilvános IP-cím nevére. Tegye a nevet idézőjelek közé.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Válassza a **Mentés** lehetőséget a szerkesztőben.

9.  Válassza **a sablon**  >  **szerkesztése** lehetőséget a template.jsfájl megnyitásához az online szerkesztőben.

10. Annak a célcsoportnak a szerkesztéséhez, amelybe a nyilvános IP-cím át lesz helyezve, módosítsa a **Location (hely** ) tulajdonságot az **erőforrások** területen.

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```
  
    A régióbeli hely kódjának beolvasásához lásd: [Azure-helyek](https://azure.microsoft.com/global-infrastructure/locations/). A régió kódja a régió neve, szóközök nélkül. Például az USA középső régiójának kódja **CentralUS**.
    
12. A sablon egyéb paramétereit is módosíthatja, ha a követelményektől függően a következőt kívánja használni:

    * **SKU**. A konfigurációban lévő nyilvános IP-cím SKU-jának megváltoztatásához a standard és az alapszint közötti, illetve az alapszintű és a standard közötti értékre módosítja a **Name (név** ) tulajdonságot a template.jsfájljának **SKU** elemében

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Az alapszintű és a standard SKU nyilvános IP-címei közötti különbségekkel kapcsolatos információkért lásd: [nyilvános IP-cím létrehozása, módosítása vagy törlése](../virtual-network/virtual-network-public-ip-address.md).

    * **Nyilvános IP-kiosztási módszer** és **Üresjárati időkorlát**. A nyilvános IP-kiosztási módszert módosíthatja úgy, hogy a **publicIPAllocationMethod** tulajdonságot **dinamikusról** **statikusra** vagy **statikusról** **dinamikusra** módosítja. A tétlen időtúllépést úgy változtathatja meg, hogy a **idleTimeoutInMinutes** tulajdonságot a kívánt értékre módosítja. Az alapértelmezett érték **4**.

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        További információ a kiosztási módszerekről és a tétlen időtúllépési értékekről: [nyilvános IP-cím létrehozása, módosítása vagy törlése](../virtual-network/virtual-network-public-ip-address.md).

 
13. Válassza a **Mentés** lehetőséget az online szerkesztőben.

14. Válassza az alapszintű  >  **előfizetés** lehetőséget annak az előfizetésnek a kiválasztásához, amelyben a célként szolgáló nyilvános IP-címet telepíteni fogja.

15. Válassza az alapszintű erőforráscsoport **lehetőséget azon**  >   erőforráscsoport kiválasztásához, ahol a célként megadott nyilvános IP-címet telepíteni fogja. Az **új létrehozása** lehetőség kiválasztásával létrehozhat egy új erőforráscsoportot a cél nyilvános IP-címhez. Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő forrás nyilvános IP-cím forrásoldali erőforráscsoport.

16. Győződjön meg arról, hogy az **alapvető beállítások**  >  **helye** arra a célhelyre van beállítva, ahol a nyilvános IP-címet telepíteni szeretné.

17. A **Beállítások** területen ellenőrizze, hogy a név egyezik-e a korábban a paraméterek szerkesztőjében megadott névvel.

18. Jelölje be a **feltételek és KIkötések** jelölőnégyzetet.

19. A cél nyilvános IP-cím telepítéséhez válassza a **vásárlás** lehetőséget.

20. Ha van olyan nyilvános IP-címe, amelyet a kimenő NAT-hoz a terheléselosztó átmozgatásakor használ, ismételje meg az előző lépéseket a második kimenő nyilvános IP-cím exportálásához és üzembe helyezéséhez a megcélzott régióban.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>A külső terheléselosztó sablonjának exportálása és a terheléselosztó üzembe helyezése a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.
2. Keresse meg azt az erőforráscsoportot, amely a forrás külső terheléselosztó elemet tartalmazza, majd jelölje ki.
3. Válassza a **Beállítások**  >  **Exportálás sablon** lehetőséget.
4. Válassza a **telepítés** lehetőséget a **sablon exportálása** lehetőség alatt.
5. Válassza a **sablon**  >  **szerkesztése paraméterek** lehetőséget, hogy megnyissa a parameters.jsfájlt az online szerkesztőben.

5. A külső terheléselosztó nevének a paraméterének szerkesztéséhez módosítsa a forrás külső terheléselosztó név **Value (érték** ) tulajdonságát a cél külső terheléselosztó nevére. Tegye a nevet idézőjelek közé.

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Az előző lépésekben áthelyezett cél nyilvános IP-cím értékének szerkesztéséhez először be kell szereznie az erőforrás-azonosítót, majd be kell illesztenie a fájl parameters.jsba. Az azonosító beszerzése:

    1. Egy másik böngésző lapon vagy ablakban jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza az **erőforráscsoportok** lehetőséget.
    2. Keresse meg azt a célként megadott erőforráscsoportot, amely az előző lépésekben áthelyezett nyilvános IP-címet tartalmazza. Válassza ki.
    3. Válassza a **Beállítások** > **Tulajdonságok** lehetőséget.
    4. A jobb oldali panelen jelölje ki az erőforrás- **azonosítót** , és másolja a vágólapra. Azt is megteheti, hogy kijelöli a **Másolás a vágólapra** lehetőséget az **erőforrás-azonosító** elérési útjának jobb oldalán.
    5. Illessze be az erőforrás-azonosítót a más böngészőablakban vagy lapon megnyitott **Parameters** Editor **Value (érték** ) tulajdonságba:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Válassza a **Mentés** lehetőséget az online szerkesztőben.


7.  Ha a terheléselosztó kimenő NAT-és kimenő szabályait konfigurálta, akkor a fájl harmadik bejegyzése jelenik meg a kimenő nyilvános IP-cím külső AZONOSÍTÓJÁHOZ. A kimenő nyilvános IP-cím AZONOSÍTÓjának beszerzéséhez ismételje meg a **megcélzott régió** előző lépéseit. Illessze be az azonosítót a parameters.jsfájlba:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  Válassza **a sablon**  >  **szerkesztése** lehetőséget a template.jsfájl megnyitásához az online szerkesztőben.
9.  Ha módosítani szeretné a külső terheléselosztó konfigurációját áthelyezni kívánt célpontot, módosítsa a Location ( **hely** ) tulajdonságot a template.jsfájljában található **erőforrások** területen:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. A régióbeli hely kódjának beolvasásához lásd: [Azure-helyek](https://azure.microsoft.com/global-infrastructure/locations/). A régió kódja a régió neve, szóközök nélkül. Például az USA középső régiójának kódja **CentralUS**.

11. A sablon egyéb paramétereit is módosíthatja, ha a követelményektől függően a következőt kívánja használni:

    * **SKU**. Módosítsa a külső terheléselosztó SKU-jának konfigurációját a standard és az alap közötti értékre, illetve az alapszintű a standard értékre úgy, hogy a **Name (név** ) tulajdonságot a fájl template.js **SKU** elemében módosítja:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Az alapszintű és standard SKU-terheléselosztó közötti különbségekről az [Azure standard Load Balancer áttekintése](./load-balancer-overview.md)című témakörben olvashat bővebben.

    * Terheléselosztási **szabályok**. A konfigurációban terheléselosztási szabályokat adhat hozzá vagy távolíthat el a template.jsfájljának **loadBalancingRules** szakaszában található bejegyzések hozzáadásával vagy eltávolításával:

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       További információ a terheléselosztási szabályokról: [Mi az Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Mintavételek.** A Load Balancerhez tartozó mintavételt hozzáadhatja vagy eltávolíthatja a konfigurációban, ha bejegyzéseket ad hozzá vagy távolít **el a template.js** fájljának mintavételek szakaszában:

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       További információ: [Load Balancer Health probs](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **Bejövő NAT-szabályok**. A terheléselosztó bejövő NAT-szabályait hozzáadhatja vagy eltávolíthatja a template.jsfájljának **inboundNatRules** szakaszában található bejegyzések hozzáadásával vagy eltávolításával:

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Egy bejövő NAT-szabály hozzáadásának vagy eltávolításának befejezéséhez a szabálynak jelen kell lennie, vagy el kell távolítania a template.jsfájl végén található **Type (típus** ) tulajdonsággal:

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        A bejövő NAT-szabályokról a [Mi az Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)című témakörben olvashat bővebben.

    * **Kimenő szabályok**. A konfigurációban adhat hozzá vagy távolíthat el kimenő szabályokat a template.jsfájljának **outboundRules** tulajdonságának szerkesztésével:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         További információ: [Load Balancer kimenő szabályok](./load-balancer-outbound-connections.md#outboundrules).

12. Válassza a **Mentés** lehetőséget az online szerkesztőben.

13. Válassza az alapszintű  >  **előfizetés** lehetőséget annak az előfizetésnek a kiválasztásához, ahol a cél külső terheléselosztó üzembe lesz helyezve.

15. Válassza az alapszintű erőforráscsoport **lehetőséget azon**  >   erőforráscsoport kiválasztásához, ahol a cél terheléselosztó üzembe lesz helyezve. Az **új létrehozása** lehetőség kiválasztásával létrehozhat egy új erőforráscsoportot a célként kijelölt külső terheléselosztó számára. Vagy kiválaszthatja azt a meglévő erőforráscsoportot, amelyet korábban hozott létre a nyilvános IP-címhez. Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő forrás külső terheléselosztó forrás-erőforráscsoport.

16. Győződjön meg arról, hogy az **alapvető beállítások**  >  **helye** arra a célhelyre van beállítva, ahol a külső terheléselosztó üzembe helyezését szeretné.

17. A **Beállítások** területen ellenőrizze, hogy a név megegyezik-e a paraméterek szerkesztőjében korábban megadott névvel. Győződjön meg arról, hogy az erőforrás-azonosítók fel vannak töltve a konfigurációban lévő nyilvános IP-címekre.

18. Jelölje be a **feltételek és KIkötések** jelölőnégyzetet.

19. A cél nyilvános IP-cím telepítéséhez válassza a **vásárlás** lehetőséget.

## <a name="discard"></a>Elvetés

Ha el szeretné vetni a cél nyilvános IP-címet és a külső terheléselosztó-t, törölje az azokat tartalmazó erőforráscsoportot. Ehhez válassza ki az erőforráscsoportot az irányítópulton a portálon, majd válassza a **Törlés** lehetőséget az Áttekintés oldal tetején.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások elvégzéséhez és a nyilvános IP-cím és a külső terheléselosztó áthelyezésének befejezéséhez törölje a forrásként szolgáló nyilvános IP-címet és a külső terheléselosztó vagy erőforráscsoportot. Ehhez válassza ki az erőforráscsoportot az irányítópulton a portálon, majd válassza a **Törlés** lehetőséget az egyes lapok tetején.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy külső Azure Load balancert helyezett át egyik régióból a másikba, és megtisztította a forrás erőforrásait. Ha többet szeretne megtudni a régiók és a vész-helyreállítási erőforrások közötti áthelyezésről az Azure-ban, tekintse meg a következőket:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](../site-recovery/azure-to-azure-tutorial-migrate.md)