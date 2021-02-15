---
title: Virtuális hálózati szolgáltatási végpontok konfigurálása Azure Service Bushoz
description: Ez a cikk azt ismerteti, hogyan adhat hozzá Microsoft. ServiceBus szolgáltatási végpontot egy virtuális hálózathoz.
ms.topic: article
ms.date: 02/12/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 6b168bbdc69f2d18a724084d9de694fa83d23dda
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516141"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-virtual-networks"></a>Azure Service Bus névtér elérésének engedélyezése adott virtuális hálózatokból
Service Bus és [Virtual Network (VNet) szolgáltatás-végpontok][vnet-sep] integrációja lehetővé teszi az üzenetkezelési funkciók biztonságos elérését olyan munkaterhelések esetén, mint a virtuális hálózatokhoz kötött virtuális gépek, és a hálózati forgalom elérési útja mindkét végén védett.

Ha úgy konfigurálták, hogy legalább egy virtuális hálózati alhálózat szolgáltatási végponthoz legyen kötve, a megfelelő Service Bus névtér többé nem fogadja el a forgalmat bárhonnan, de engedélyezett virtuális hálózat (ok) és opcionálisan meghatározott internetes IP-címek között. A virtuális hálózat szempontjából a Service Bus névtér kötése egy szolgáltatási végponthoz egy elkülönített hálózati alagutat konfigurál a virtuális hálózat alhálózatáról az üzenetküldési szolgáltatáshoz.

Az eredmény az alhálózathoz és a megfelelő Service Bus névtérhez kötött munkaterhelések közötti privát és elkülönített kapcsolat, annak ellenére, hogy az üzenetküldési szolgáltatás végpontjának megfigyelhető hálózati címe egy nyilvános IP-tartományban van.

>[!WARNING]
> A virtuális hálózatok integrálásának megvalósításával megakadályozható, hogy más Azure-szolgáltatások a Service Bus használatával kommunikálnak. Kivételként engedélyezheti a hozzáférést bizonyos megbízható szolgáltatásoktól Service Bus erőforrásaihoz, még akkor is, ha a hálózati szolgáltatás végpontjai engedélyezve vannak. A megbízható szolgáltatások listáját lásd: [megbízható szolgáltatások](#trusted-microsoft-services).
>
> A következő Microsoft-szolgáltatások szükségesek virtuális hálózaton
> - Azure App Service
> - Azure Functions

> [!IMPORTANT]
> A virtuális hálózatok csak [prémium szintű](service-bus-premium-messaging.md) Service Bus névterek esetén támogatottak. Ha a VNet szolgáltatásbeli végpontokat Service Bus használatával használja, ezeket a végpontokat nem ajánlott olyan alkalmazásokban engedélyezni, amelyek a standard és a prémium szintű Service Bus névtereket keverik. Mivel a standard szint nem támogatja a virtuális hálózatok. A végpont csak a prémium szintű névterek számára van korlátozva.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>A VNet-integráció által engedélyezett speciális biztonsági forgatókönyvek 

A szigorú és compartmentalized biztonságot igénylő megoldások, valamint a virtuális hálózatok alhálózatai biztosítják a compartmentalized szolgáltatások közötti szegmentálást, általánosságban továbbra is szükség van a kommunikációs útvonalakra az ezekben a rekeszekben található szolgáltatások között.

A rekeszek közötti közvetlen IP-útvonal, beleértve a TCP/IP protokollon keresztüli HTTPS-t is, a hálózati réteg biztonsági réseinak kiaknázásának kockázatát hordozza. Az üzenetkezelési szolgáltatások teljes mértékben szigetelt kommunikációs útvonalakat biztosítanak, ahol az üzenetek a felek közötti váltáskor lemezre is írhatók. Az ugyanahhoz a Service Bus-példányhoz kötött két különálló virtuális hálózatban a munkaterhelések hatékonyan és megbízhatóan kommunikálhatnak az üzeneteken keresztül, miközben a hálózat elkülönítési határának megfelelő integritása megmarad.
 
Ez azt jelenti, hogy a biztonsági szempontból bizalmas Felhőbeli megoldások nem csupán az Azure piacvezető megbízható és skálázható üzenetkezelési képességeihez férnek hozzá, de mostantól az üzenetküldés használatával kommunikációs útvonalakat hozhatnak létre a biztonságos Megoldási rekeszek között, amelyek eleve biztonságosabbak, mint bármely egyenrangú kommunikációs mód, beleértve a HTTPS-t és más TLS-védelemmel ellátott szoftvercsatorna-protokollokat is.

## <a name="binding-service-bus-to-virtual-networks"></a>Kötési Service Bus a virtuális hálózatokhoz

A *virtuális hálózati szabályok* a tűzfal biztonsági funkciója, amely azt szabályozza, hogy a Azure Service Bus-kiszolgáló egy adott virtuális hálózati alhálózat kapcsolatait fogadja-e.

A Service Bus névterek virtuális hálózathoz való kötése kétlépéses folyamat. Először létre kell hoznia egy **Virtual Network szolgáltatási végpontot** egy Virtual Network alhálózaton, és engedélyeznie kell azt a **Microsoft. ServiceBus** számára a [szolgáltatási végpont áttekintése című][vnet-sep]részben leírtak szerint. A szolgáltatás végpontjának hozzáadása után a Service Bus névteret egy **virtuális hálózati szabállyal** kell kötnie.

A virtuális hálózati szabály a Service Bus névtér egy virtuális hálózati alhálózattal való társítása. Amíg a szabály létezik, az alhálózathoz kötött összes munkaterhelés hozzáférést kap a Service Bus névtérhez. Service Bus maga soha nem hoz létre kimenő kapcsolatokat, nem kell elérnie a hozzáférést, ezért a szabály engedélyezésével soha nem kapnak hozzáférést az alhálózathoz.

> [!NOTE]
> Ne feledje, hogy a hálózati szolgáltatás végpontja a virtuális hálózaton futó alkalmazásokat biztosít a Service Bus névtérhez való hozzáféréshez. A virtuális hálózat vezérli a végpont elérhetőségét, de nem hajtja végre a műveleteket Service Bus entitásokon (várólisták, témakörök vagy előfizetések). Az Azure Active Directory (Azure AD) használatával engedélyezheti az alkalmazások által a névtérben és az entitásokban elvégezhető műveleteket. További információ: [alkalmazások hitelesítése és engedélyezése az Azure ad-vel Service Bus entitások eléréséhez](authenticate-application.md).


## <a name="use-azure-portal"></a>Az Azure Portal használata
Ez a szakasz bemutatja, hogyan használható a Azure Portal virtuális hálózati szolgáltatásbeli végpont hozzáadására. A hozzáférés korlátozásához integrálnia kell a virtuális hálózati szolgáltatás végpontját ehhez a Event Hubs névtérhez.

1. Navigáljon a **Service Bus névtérhez** a [Azure Portal](https://portal.azure.com).
2. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget a **Beállítások** területen.  

    > [!NOTE]
    > A **hálózatkezelés** lap csak a **prémium** szintű névterek esetében jelenik meg.  
    
    >[!WARNING]
    > Ha a **kiválasztott hálózatok** lehetőséget választja, és nem ad hozzá legalább egy IP-tűzfalszabály vagy virtuális hálózat ezen a lapon, a névtér a nyilvános interneten keresztül érhető el (a hozzáférési kulcs használatával).

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Hálózatkezelés lap – alapértelmezett" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Ha a **minden hálózat** lehetőséget választja, akkor a Service Bus névtér bármely IP-címről fogad kapcsolatokat. Ez az alapértelmezett beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt. 

    ![Tűzfal – az összes hálózat lehetőség ki van választva](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
2. Ha az adott virtuális hálózatokhoz való hozzáférést szeretné korlátozni, válassza a **kiválasztott hálózatok** lehetőséget, ha még nincs kiválasztva.
1. A lap **Virtual Network** szakaszában válassza a **+ meglévő virtuális hálózat hozzáadása** elemet. 

    ![meglévő virtuális hálózat hozzáadása](./media/service-endpoints/add-vnet-menu.png)
3. Jelölje ki a virtuális hálózatot a virtuális hálózatok listájából, majd válassza ki az **alhálózatot**. Engedélyeznie kell a szolgáltatás végpontját, mielőtt hozzáadja a virtuális hálózatot a listához. Ha a szolgáltatási végpont nincs engedélyezve, akkor a portál felszólítja, hogy engedélyezze.
   
   ![alhálózat kiválasztása](./media/service-endpoints/select-subnet.png)

4. Az alhálózat szolgáltatási végpontjának engedélyezése után a következő sikeres üzenetnek kell megjelennie: **Microsoft. ServiceBus**. A hálózat hozzáadásához kattintson a lap alján található **Hozzáadás** gombra. 

    ![alhálózat kiválasztása és végpont engedélyezése](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Ha nem tudja engedélyezni a szolgáltatási végpontot, a Resource Manager-sablonnal figyelmen kívül hagyhatja a hiányzó virtuális hálózati szolgáltatás végpontját. Ez a funkció nem érhető el a portálon.
6. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra az eszköztáron. Várjon néhány percet, amíg a megerősítés megjelenik a portál értesítéseiben. A **Save (Mentés** ) gombot le kell tiltani. 

    ![Hálózat mentése](./media/service-endpoints/save-vnet.png)

    > [!NOTE]
    > A megadott IP-címekről vagy tartományokról való hozzáférés engedélyezésével kapcsolatos utasításokért lásd: [hozzáférés engedélyezése adott IP-címekről vagy tartományokból](service-bus-ip-filtering.md).

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Resource Manager-sablon használata
A következő példa Resource Manager-sablon egy virtuális hálózati szabályt vesz fel egy meglévő Service Bus névtérbe. A hálózati szabály a virtuális hálózatban lévő alhálózat AZONOSÍTÓját határozza meg. 

Az azonosító a virtuális hálózat alhálózatának teljes erőforrás-kezelői útvonala. Például `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` egy virtuális hálózat alapértelmezett alhálózata esetében.

Virtuális hálózati vagy tűzfalszabályok hozzáadásakor állítsa be a értékét a következőre `defaultAction` : `Deny` .

Sablon:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

A sablon üzembe helyezéséhez kövesse az [Azure Resource Manager][lnk-deploy]utasításait.

> [!IMPORTANT]
> Ha nincsenek IP-és virtuális hálózati szabályok, akkor az összes forgalom a névtérbe kerül, még akkor is, ha be van állítva `defaultAction` `deny` .  A névtér a nyilvános interneten keresztül érhető el (a hozzáférési kulccsal). Legalább egy IP-szabályt vagy virtuális hálózati szabályt meg kell adni a névtérhez, hogy csak a virtuális hálózat megadott IP-címeiről vagy alhálózatáról engedélyezze a forgalmat.  

## <a name="next-steps"></a>Következő lépések

A virtuális hálózatokkal kapcsolatos további információkért tekintse meg az alábbi hivatkozásokat:

- [Azure Virtual Network szolgáltatásbeli végpontok][vnet-sep]
- [Azure Service Bus IP-szűrés][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
