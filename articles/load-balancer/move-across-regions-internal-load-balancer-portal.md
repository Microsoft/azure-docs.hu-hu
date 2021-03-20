---
title: Azure belső Load Balancer áthelyezése egy másik Azure-régióba az Azure Portal használatával
description: Azure Resource Manager sablon használatával áthelyezheti az Azure belső Load Balancer egyik Azure-régióból a másikba a Azure Portal
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 68a2cb6926cb41956711a9e3c15d21c250d27f0b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94698489"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Az Azure belső Load Balancer áthelyezése másik régióba a Azure Portal használatával

Különböző helyzetekben érdemes áthelyezni a meglévő belső terheléselosztó egyik régióból a másikba. Előfordulhat például, hogy egy belső terheléselosztó-t szeretne létrehozni ugyanazzal a konfigurációval a teszteléshez. A vész-helyreállítási tervezés részeként érdemes áthelyezni egy belső terheléselosztó egy másik régióba is.

Az Azure belső terheléselosztó nem helyezhető át egyik régióból a másikba. A belső terheléselosztó meglévő konfigurációjának és virtuális hálózatának exportálásához azonban Azure Resource Manager sablont is használhat.  Ezt követően az erőforrást egy másik régióban is elvégezheti, ha a terheléselosztó és a virtuális hálózat sablonba való exportálásával módosítja a paramétereket, hogy azok megfeleljenek a célként megadott régiónak, majd telepítse a sablonokat az új régióba.  A Resource Managerrel és a sablonokkal kapcsolatos további információkért tekintse meg a rövid útmutató [: Azure Resource Manager sablonok létrehozása és telepítése a Azure Portal használatával](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)című témakört.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure belső terheléselosztó abban az Azure-régióban található, amelyről át kívánja helyezni.

- Az Azure belső terheléselosztó nem helyezhetők át a régiók között.  Az új Load balancert a célként megadott régióban lévő erőforrásokhoz kell rendelnie.

- A belső terheléselosztó konfigurációjának exportálásához és egy olyan sablon üzembe helyezéséhez, amely egy belső terheléselosztó egy másik régióban való létrehozásához szükséges, szüksége lesz a hálózati közreműködő szerepkörre vagy magasabbra.

- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez az elrendezés magában foglalja, de nem korlátozódik a terheléselosztó, a hálózati biztonsági csoportok, a virtuális gépek és a virtuális hálózatok számára.

- Ellenőrizze, hogy az Azure-előfizetése lehetővé teszi-e belső terheléselosztó létrehozását a használt célcsoportban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Ellenőrizze, hogy az előfizetése rendelkezik-e elegendő erőforrással a folyamathoz tartozó terheléselosztó hozzáadásának támogatásához.  Tekintse meg az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Előkészítés és áthelyezés
A következő lépések bemutatják, hogyan készítse elő a belső terheléselosztó az áthelyezéshez Resource Manager-sablonnal, és a belső terheléselosztó konfigurációját helyezze át a cél régióba a Azure Portal használatával.  Ennek a folyamatnak a részeként a belső terheléselosztó virtuális hálózati konfigurációját is tartalmaznia kell, és először a belső Load Balancer áthelyezése előtt kell elvégezni.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>A virtuális hálózati sablon exportálása és üzembe helyezése a Azure Portal

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com)  >  **erőforráscsoporthoz**.
2. Keresse meg a forrás virtuális hálózatot tartalmazó erőforráscsoportot, és kattintson rá.
3. Válassza > **Beállítások**  >  **Exportálás sablon** lehetőséget.
4. A **sablon exportálása** panelen válassza a **telepítés** lehetőséget.
5. Kattintson a **sablon**  >  **szerkesztése paraméterek** lehetőségre, hogy megnyissa a **parameters.js** fájlt az online szerkesztőben.
6. A virtuális hálózat nevének paraméterének szerkesztéséhez módosítsa a **Value** tulajdonságot a **Paraméterek** alatt:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. Módosítsa a forrás virtuális hálózat nevének értékét a szerkesztőben a cél VNET kívánt névre. Győződjön meg arról, hogy a nevet idézőjelek közé adja.

8. Kattintson a **Mentés** gombra a szerkesztőben.

9. Kattintson **a sablon**  >  **szerkesztése** gombra a **template.js** fájl megnyitásához az online szerkesztőben.

10. Ha módosítani szeretné a VNET áthelyezni kívánt célpontot, módosítsa a **Location (hely** ) tulajdonságot az erőforrások területen.

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

11. A régióbeli hely kódjának beszerzéséhez tekintse meg az [Azure-helyeket](https://azure.microsoft.com/global-infrastructure/locations/).  A régió kódja a régió neve szóközök nélkül, **Közép-USA**  =  **CentralUS**.

12. Ha ezt a lehetőséget választja, a **template.js** további paramétereket is megadhat, és a követelményektől függően választható:

    * **Címterület** – a VNET a Mentés előtt módosítható, ha módosítja az **erőforrások**  >  **addressSpace** szakaszt, és módosítja a **addressPrefixes** tulajdonságot a fájl **template.jsjában** :

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Alhálózat** – az alhálózat neve és az alhálózati címtartomány módosítható, illetve a **template.js** fájl **alhálózatai** szakaszának módosításával is felvehető. Az alhálózat neve módosítható a **Name** tulajdonság megváltoztatásával. Az alhálózati címtartomány módosítható úgy, hogy módosítja a **addressPrefix** tulajdonságot a fájl **template.jsjában** :

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         A fájl **template.js** a címben szereplő előtag módosításához két helyen kell szerkeszteni, a fent felsorolt szakaszt és az alább felsorolt **típusok** szakaszt.  Módosítsa a **addressPrefix** tulajdonságot úgy, hogy az megfeleljen a fentieknek:

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

13. Kattintson a **Save (Mentés** ) gombra az online szerkesztőben.

14. Az   >  alapértékek **előfizetése** lehetőségre kattintva válassza ki azt az előfizetést, amelyben a cél VNET telepíteni fogja.

15. Az alapszintű erőforráscsoport **elemre** kattintva  >   válassza ki azt az erőforráscsoportot, amelyben a cél VNET telepíteni fogja.  Az **új létrehozása** lehetőségre kattintva létrehozhat egy új erőforráscsoportot a cél VNET.  Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő VNET forrásoldali erőforráscsoport.

16. Győződjön   >  meg arról, hogy az alapértékek **helye** arra a célhelyre van beállítva, ahol a VNET telepíteni kívánja.

17. Ellenőrizze a **Beállítások** területen, hogy a név megegyezik-e a fenti Parameters Editorban megadott névvel.

18. Jelölje be a jelölőnégyzetet a **feltételek és KIkötések** területen.

19. A cél virtuális hálózat üzembe helyezéséhez kattintson a **vásárlás** gombra.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>A belső terheléselosztó sablon exportálása és üzembe helyezése Azure PowerShell

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com)  >  **erőforráscsoporthoz**.
2. Keresse meg a forrás belső Load balancert tartalmazó erőforráscsoportot, és kattintson rá.
3. Válassza > **Beállítások**  >  **Exportálás sablon** lehetőséget.
4. A **sablon exportálása** panelen válassza a **telepítés** lehetőséget.
5. Kattintson a **sablon**  >  **szerkesztése paraméterek** lehetőségre, hogy megnyissa a **parameters.js** fájlt az online szerkesztőben.

6. A belső terheléselosztó nevének a paraméterének szerkesztéséhez módosítsa a forrás belső terheléselosztó **neve tulajdonságát** a cél belső terheléselosztó nevére, győződjön meg arról, hogy a név idézőjelek közé esik:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. A fentiekben áthelyezett cél virtuális hálózat értékének szerkesztéséhez először be kell szereznie az erőforrás-azonosítót, majd be kell másolnia és beillesztenie a fájl **parameters.js** . Az azonosító beszerzése:

    1. Jelentkezzen be a [Azure Portal](https://portal.azure.com)  >  **erőforráscsoporthoz** egy másik böngésző lapon vagy ablakban.
    2. Keresse meg azt a célként megadott erőforráscsoportot, amely az áthelyezett virtuális hálózatot tartalmazza a fenti lépések alapján, majd kattintson rá.
    3. Válassza a > **Beállítások**  >  **Tulajdonságok** lehetőséget.
    4. A jobb oldali panelen jelölje ki az erőforrás- **azonosítót** , és másolja a vágólapra.  Azt is megteheti, hogy a **Másolás a vágólapra** gombra kattint az **erőforrás-azonosító** elérési útjának jobb oldalán.
    5. Illessze be az erőforrás-azonosítót a **defaultValue** tulajdonságba a más böngészőablakban vagy lapon megnyitott **Paraméterek szerkesztése** szerkesztőben:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Kattintson a **Save (Mentés** ) gombra az online szerkesztőben.

7. Kattintson **a sablon**  >  **szerkesztése** gombra a **template.js** fájl megnyitásához az online szerkesztőben.
8. Ha módosítani szeretné a belső terheléselosztó konfigurációját áthelyező célhelyet, módosítsa a **Location (hely** ) tulajdonságot a **template.js** fájljában található **erőforrások** területen:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

9.  A régióbeli hely kódjának beszerzéséhez tekintse meg az [Azure-helyeket](https://azure.microsoft.com/global-infrastructure/locations/).  A régió kódja a régió neve szóközök nélkül, **Közép-USA**  =  **CentralUS**.

10. A sablon egyéb paramétereit is módosíthatja, és a követelményektől függően választható:

    * **SKU** – megváltoztathatja a belső terheléselosztó SKU-jának konfigurációját a standard értékről az alapszintű vagy az alapszintű értékre, ha módosítja az **SKU**  >  **Name** tulajdonságot a fájl **template.jsjában** :

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Az alapszintű és standard SKU-terheléselosztó közötti különbségekről az [Azure standard Load Balancer áttekintése](./load-balancer-overview.md) című témakörben olvashat bővebben.

    * Terheléselosztási **szabályok** – a konfigurációban terheléselosztási szabályokat adhat hozzá vagy távolíthat el, ha bejegyzéseket ad hozzá a **loadBalancingRules** szakaszhoz, vagy eltávolítja azokat a fájlon **template.js** :

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
       További információ a terheléselosztási szabályokról: [Mi az Azure Load Balancer?](./load-balancer-overview.md)

    * Mintavétel **– hozzáadhat** vagy eltávolíthat egy mintavételt a terheléselosztó számára a konfigurációban úgy, hogy bejegyzéseket ad hozzá vagy távolít el a **template.js** fájljának mintavételek **szakaszához** :

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
       Azure Load Balancer állapot-mintavételsel kapcsolatos további információkért lásd: [Load Balancer Health Szondák](./load-balancer-custom-probe-overview.md)

    * **Bejövő NAT-szabályok** – a Load BALANCER bejövő NAT-szabályait hozzáadhatja vagy eltávolíthatja a **template.js** fájljának **inboundNatRules** szakaszának bejegyzéseinek hozzáadásával vagy eltávolításával:

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
        Egy bejövő NAT-szabály hozzáadásának vagy eltávolításának befejezéséhez a szabálynak jelen kell lennie, vagy el kell távolítania a **template.js** fájl végén található **Type (típus** ) tulajdonsággal:

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
        További információ a bejövő NAT-szabályokról: [Mi az Azure Load Balancer?](./load-balancer-overview.md)

12. Kattintson a **Save (Mentés** ) gombra az online szerkesztőben.

13.   >  Az alapértékek **előfizetése** lehetőségre kattintva válassza ki azt az előfizetést, ahol a cél belső terheléselosztó üzembe lesz helyezve.

15. Az alapszintű erőforráscsoport **elemre** kattintva  >   kiválaszthatja azt az erőforráscsoportot, amelyben a cél terheléselosztó telepítve lesz.  Az **új létrehozása** lehetőségre kattintva létrehozhat egy új erőforráscsoportot a cél belső terheléselosztó számára, vagy kiválaszthatja a virtuális hálózat felett létrehozott meglévő erőforráscsoportot.  Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő forrás belső terheléselosztó forrás-erőforráscsoport.

16. Győződjön   >  meg arról, hogy az alapértékek **helye** arra a célhelyre van beállítva, ahol a belső terheléselosztó üzembe helyezését szeretné.

17. Ellenőrizze a **Beállítások** területen, hogy a név megegyezik-e a fenti Parameters Editorban megadott névvel.  Ellenőrizze, hogy az erőforrás-azonosítók ki vannak-e töltve a konfigurációban lévő virtuális hálózatok esetében.

18. Jelölje be a jelölőnégyzetet a **feltételek és KIkötések** területen.

19. A cél virtuális hálózat üzembe helyezéséhez kattintson a **vásárlás** gombra.

## <a name="discard"></a>Elvetés

Ha el szeretné vetni a cél virtuális hálózatot és a belső terheléselosztó-t, törölje a célként szolgáló virtuális hálózatot és a belső Load balancert tartalmazó erőforráscsoportot.  Ehhez válassza ki az erőforráscsoportot az irányítópulton a portálon, és válassza a **Törlés** lehetőséget az Áttekintés oldal tetején.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások elvégzéséhez és a virtuális hálózat és a belső terheléselosztó áthelyezésének befejezéséhez törölje a forrásként szolgáló virtuális hálózatot és a belső terheléselosztó vagy erőforráscsoportot. Ehhez válassza ki a virtuális hálózatot és a belső terheléselosztó vagy erőforráscsoport elemet az irányítópulton a portálon, és válassza a **Törlés** lehetőséget az egyes oldalak tetején.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy belső Azure Load balancert helyezett át egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](../site-recovery/azure-to-azure-tutorial-migrate.md)