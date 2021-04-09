---
title: A Azure Service Bus IP-tűzfalszabályok konfigurálása
description: A tűzfalszabályok használata az adott IP-címekről Azure Service Bus való csatlakozás engedélyezéséhez.
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: e73f566533cb2357653f7f584ec9ca77333c0a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100560859"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-ip-addresses-or-ranges"></a>Azure Service Bus névtér elérésének engedélyezése adott IP-címekről vagy tartományokból
Alapértelmezés szerint a Service Bus névterek az internetről érhetők el, feltéve, hogy a kérés érvényes hitelesítéssel és engedélyezéssel rendelkezik. Az IP-tűzfallal a [CIDR (osztály nélküli Inter-Domain útválasztás)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel tovább korlátozhatja az IPv4-címek vagy az IPv4-címtartományok körét.

Ez a funkció olyan helyzetekben hasznos, amikor a Azure Service Bus csak bizonyos jól ismert helyekről lehet elérhető. A tűzfalszabályok lehetővé teszik a szabályok konfigurálását az adott IPv4-címekből származó forgalom fogadásához. Ha például az [Azure Express Route][express-route]Service Bust használja, létrehozhat egy **tűzfalszabályet** , amely lehetővé teszi, hogy csak a helyszíni infrastruktúra IP-címeiről vagy egy vállalati NAT-átjáró címéről érkező forgalom legyen. 

> [!IMPORTANT]
> - A tűzfalak és a virtuális hálózatok csak a **prémium** szintű Service Bus támogatottak. Ha a **Premier** szintre való frissítés nem egy lehetőség, javasoljuk, hogy a közös hozzáférésű aláírás (SAS) tokenjét biztonságos módon őrizze meg, és csak a jogosult felhasználók férhessenek hozzá. További információ az SAS-hitelesítéssel kapcsolatban: [hitelesítés és engedélyezés](service-bus-authentication-and-authorization.md#shared-access-signature).
> - Legalább egy IP-szabályt vagy virtuális hálózati szabályt meg kell adni a névtérhez, hogy csak a virtuális hálózat megadott IP-címeiről vagy alhálózatáról engedélyezze a forgalmat. Ha nincsenek IP-és virtuális hálózati szabályok, a névtér a nyilvános interneten keresztül érhető el (a hozzáférési kulccsal).  

## <a name="ip-firewall-rules"></a>IP-tűzfalszabályok
Az IP-tűzfalszabályok a Service Bus névtér szintjén lesznek alkalmazva. Ezért a szabályok az ügyfelek összes kapcsolatára érvényesek bármely támogatott protokoll használatával. Olyan IP-címről érkező csatlakozási kísérletek, amely nem felel meg a Service Bus névtérben lévő engedélyezett IP-szabálynak, a rendszer nem engedélyezettként fogadja el. A válasz nem említi az IP-szabályt. Az IP-szűrési szabályok sorrendben lesznek alkalmazva, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletet.

A tűzfalszabályok bevezetésével megakadályozhatja, hogy más Azure-szolgáltatások a Service Bus használatával kommunikálnak. Kivételként engedélyezheti a hozzáférést bizonyos megbízható szolgáltatásoktól Service Bus erőforrásaihoz, még akkor is, ha az IP-szűrés engedélyezve van. A megbízható szolgáltatások listáját lásd: [megbízható szolgáltatások](#trusted-microsoft-services). 

A következő Microsoft-szolgáltatások szükségesek virtuális hálózaton
- Azure App Service
- Azure Functions

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ebből a szakaszból megtudhatja, hogyan használhatja a Azure Portal IP-tűzfalszabályok létrehozásához egy Service Bus névtérhez. 

1. Navigáljon a **Service Bus névtérhez** a [Azure Portal](https://portal.azure.com).
2. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget a **Beállítások** területen.  

    > [!NOTE]
    > A **hálózatkezelés** lap csak a **prémium** szintű névterek esetében jelenik meg.  
    
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Hálózatkezelés lap – alapértelmezett" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Ha a **minden hálózat** lehetőséget választja, akkor a Service Bus névtér bármely IP-címről fogad kapcsolatokat. Ez az alapértelmezett beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt. 

    ![Képernyőkép a Azure Portal hálózatkezelési oldalról. A tűzfalak és virtuális hálózatok lapon a hozzáférés engedélyezése az összes hálózatról lehetőség van kiválasztva.](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Ha csak a megadott IP-címről szeretne hozzáférést engedélyezni, válassza a **kiválasztott hálózatok** lehetőséget, ha még nincs kiválasztva. A **tűzfal** szakaszban kövesse az alábbi lépéseket:
    1. Válassza az **ügyfél IP-címének hozzáadása** lehetőséget, hogy a jelenlegi ügyfél IP-címe hozzáférjen a névtérhez. 
    2. A **címtartomány** mezőben adjon meg egy adott IPv4-címeket vagy IPv4-CÍMTARTOMÁNYT a CIDR-jelölésben. 
    3. Itt adhatja meg, hogy szeretné- **e engedélyezni a megbízható Microsoft-szolgáltatások számára a tűzfal megkerülését**. 

        >[!WARNING]
        > Ha a **kiválasztott hálózatok** lehetőséget választja, és nem ad hozzá legalább egy IP-tűzfalszabály vagy virtuális hálózat ezen a lapon, a névtér a nyilvános interneten keresztül érhető el (a hozzáférési kulcs használatával).    

        ![Képernyőkép a Azure Portal hálózatkezelési oldalról. A kijelölt hálózatokból való hozzáférés engedélyezése lehetőség ki van választva, és a tűzfal szakasz ki van emelve.](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra az eszköztáron. Várjon néhány percet, hogy a megerősítés megjelenjen a portál értesítésein.

    > [!NOTE]
    > Ha korlátozni szeretné a hozzáférést az adott virtuális hálózatokhoz, tekintse meg a [hozzáférés engedélyezése adott hálózatokból](service-bus-service-endpoints.md)című témakört.

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Resource Manager-sablon használata
Ebben a szakaszban egy példa Azure Resource Manager sablonnal, amely egy virtuális hálózatot és egy tűzfalszabály hozzáadását is felveszi egy meglévő Service Bus-névtérbe.

a **ipMask** egy IPv4-cím vagy IP-cím CIDR-jelölésű blokkja. A CIDR 70.37.104.0/24 jelölése például 256 a 70.37.104.0 és a 70.37.104.255 közötti IPv4-címeket jelöli, és 24 a tartomány jelentős előtagjának számát jelzi.

Virtuális hálózati vagy tűzfalszabályok hozzáadásakor állítsa be a értékét a következőre `defaultAction` : `Deny` .


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
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
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
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
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
> Ha nincsenek IP-és virtuális hálózati szabályok, akkor az összes forgalom a névtérbe kerül, még akkor is, ha be van állítva `defaultAction` `deny` . A névtér a nyilvános interneten keresztül érhető el (a hozzáférési kulccsal). Legalább egy IP-szabályt vagy virtuális hálózati szabályt meg kell adni a névtérhez, hogy csak a virtuális hálózat megadott IP-címeiről vagy alhálózatáról engedélyezze a forgalmat.  


## <a name="next-steps"></a>Következő lépések

Az Azure-beli virtuális hálózatokhoz való Service Bus hozzáférésének korlátozásához tekintse meg a következő hivatkozást:

- [Service Bus Virtual Network szolgáltatási végpontok][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  ../expressroute/expressroute-faqs.md#supported-services