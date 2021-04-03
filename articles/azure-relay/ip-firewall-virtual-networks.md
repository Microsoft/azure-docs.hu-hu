---
title: IP-tűzfal konfigurálása Azure Relay névtérhez
description: Ez a cikk azt ismerteti, hogyan használhatók a tűzfalszabályok az adott IP-címekről Azure Relay névterekhez való csatlakozás engedélyezéséhez.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ad8feed5df49dcc4503226a5fae50195bb9d48aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91999508"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>IP-tűzfal konfigurálása Azure Relay névtérhez
Alapértelmezés szerint a továbbítási névterek elérhetők az internetről, feltéve, hogy a kérelem érvényes hitelesítést és engedélyezést tartalmaz. Az IP-tűzfallal a [CIDR (osztály nélküli Inter-Domain útválasztás)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel tovább korlátozhatja az IPv4-címek vagy az IPv4-címtartományok körét.

Ez a funkció olyan helyzetekben hasznos, amikor a Azure Relay csak bizonyos jól ismert helyekről lehet elérhető. A tűzfalszabályok lehetővé teszik a szabályok konfigurálását az adott IPv4-címekből származó forgalom fogadásához. Ha például az [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services)használatával használ továbbítót, létrehozhat egy **tűzfalszabályet** , amely lehetővé teszi, hogy csak a helyszíni infrastruktúra IP-címeiről érkező forgalmat engedélyezze. 


> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. 


## <a name="enable-ip-firewall-rules"></a>IP-tűzfalszabályok engedélyezése
Az IP-tűzfalszabályok a névtér szintjén lesznek alkalmazva. Ezért a szabályok az ügyfelek összes kapcsolatára érvényesek bármely támogatott protokoll használatával. A névtérben engedélyezett IP-szabálynak nem megfelelő IP-címről érkező kapcsolódási kísérleteket a rendszer nem engedélyezettként fogadja el. A válasz nem említi az IP-szabályt. Az IP-szűrési szabályok sorrendben lesznek alkalmazva, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletet.

### <a name="use-azure-portal"></a>Az Azure Portal használata
Ebből a szakaszból megtudhatja, hogyan használhatja a Azure Portal IP-tűzfalszabályok létrehozására a névtérhez. 

1. Navigáljon a **továbbítási névtérhez** a [Azure Portal](https://portal.azure.com).
2. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget. Ha az **összes hálózat** lehetőséget választja a  **hozzáférés engedélyezése** részben, a továbbítási névtér bármely IP-címről fogad kapcsolatokat. Ez a beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt. 

    ![Képernyőfelvétel: a hálózat lap, amelyen a minden hálózat lehetőség be van jelölve.](./media/ip-firewall/all-networks-selected.png)
1. Ha korlátozni szeretné a hozzáférést bizonyos hálózatokra és IP-címekre, válassza a **kiválasztott hálózatok** lehetőséget. A **tűzfal** szakaszban kövesse az alábbi lépéseket:
    1. Válassza az **ügyfél IP-címének hozzáadása** lehetőséget, hogy a jelenlegi ügyfél IP-címe hozzáférjen a névtérhez. 
    2. A **címtartomány** mezőben adjon meg egy adott IPv4-címeket vagy IPv4-CÍMTARTOMÁNYT a CIDR-jelölésben. 

        ![Tűzfal – az összes hálózat lehetőség ki van választva](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra az eszköztáron. Várjon néhány percet, hogy a megerősítés megjelenjen a portál értesítésein.


### <a name="use-resource-manager-template"></a>Resource Manager-sablon használata
A következő Resource Manager-sablon lehetővé teszi az IP-szűrési szabály hozzáadását egy meglévő továbbítási névtérhez.

A sablon egy paramétert vesz igénybe: **ipMask**, amely egyetlen IPv4-cím vagy IP-cím CIDR jelölésű. A CIDR 70.37.104.0/24 jelölése például 256 a 70.37.104.0 és a 70.37.104.255 közötti IPv4-címeket jelöli, és 24 a tartomány jelentős előtagjának számát jelzi.

> [!NOTE]
> Habár a megtagadási szabályok nem lehetségesek, a Azure Resource Manager sablon az **"engedélyezés"** értékre van állítva, amely nem korlátozza a kapcsolatokat.
> Virtual Network vagy tűzfalakra vonatkozó szabályok végrehajtásakor módosítania kell a ***"defaultAction"***
> 
> a
> ```json
> "defaultAction": "Allow"
> ```
> felhasználóként a(z)
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.Relay/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
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
          "virtualNetworkRules": [],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

A sablon üzembe helyezéséhez kövesse az [Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)utasításait.



## <a name="next-steps"></a>Következő lépések
A hálózati biztonsággal kapcsolatos egyéb funkciókkal kapcsolatos további tudnivalókért lásd: [hálózati biztonság](network-security.md).


<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
