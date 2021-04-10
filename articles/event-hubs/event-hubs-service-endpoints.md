---
title: Virtual Network szolgáltatási végpontok – Azure Event Hubs | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan adhat hozzá Microsoft. EventHub szolgáltatási végpontot egy virtuális hálózathoz.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: f7f0f3ff480018c9bfc5d9c6f34cf7e2935f8d6a
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959959"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-virtual-networks"></a>Azure Event Hubs-névterek elérésének engedélyezése adott virtuális hálózatokból 

Event Hubs és [Virtual Network (VNet) szolgáltatás-végpontok][vnet-sep] integrációja lehetővé teszi az üzenetkezelési funkciók biztonságos elérését olyan munkaterhelések esetén, mint például a virtuális hálózatokhoz kötött virtuális gépek, és a hálózati forgalom elérési útja mindkét végén védett. 

Ha úgy van konfigurálva, hogy legalább egy virtuális hálózati alhálózat szolgáltatási végpontra legyen kötve, a megfelelő Event Hubs névtér többé nem fogadja el a forgalmat bárhonnan, de a virtuális hálózatokban engedélyezett alhálózatokat. A virtuális hálózat szempontjából a Event Hubs névterek egy szolgáltatás-végponthoz kötése egy elkülönített hálózati alagutat állít be a virtuális hálózat alhálózatáról az üzenetküldési szolgáltatásba. 

Az eredmény az alhálózathoz és a megfelelő Event Hubs névtérhez kötött munkaterhelések közötti privát és elkülönített kapcsolat, annak ellenére, hogy az üzenetküldési szolgáltatás végpontjának megfigyelhető hálózati címe egy nyilvános IP-tartományban van. Kivételt jelent ez a viselkedés. A szolgáltatás végpontjának engedélyezése alapértelmezés szerint engedélyezi a `denyall` szabályt a virtuális hálózathoz társított [IP-tűzfalon](event-hubs-ip-filtering.md) . Adott IP-címeket adhat hozzá az IP-tűzfalon az Event hub nyilvános végponthoz való hozzáférés engedélyezéséhez. 

## <a name="important-points"></a>Fontos pontok
- Ez a funkció mind a **standard** , mind a **dedikált** szint esetében támogatott. Az alapszintű **csomag** nem támogatja.
- Ha engedélyezi a virtuális hálózatok számára a Event Hubs névteret, alapértelmezés szerint letiltja a bejövő kérelmeket, hacsak a kérelmek nem az engedélyezett virtuális hálózatokról származó szolgáltatásból származnak. A letiltott kérések közé tartoznak a más Azure-szolgáltatások, a Azure Portal, a naplózási és a metrikai szolgáltatások, valamint így tovább. Kivételként engedélyezheti a hozzáférést bizonyos **megbízható szolgáltatásoktól** Event Hubs erőforrásaihoz, még akkor is, ha a virtuális hálózatok engedélyezve vannak. A megbízható szolgáltatások listáját lásd: [megbízható szolgáltatások](#trusted-microsoft-services).
- **Legalább egy IP-szabályt vagy virtuális hálózati szabályt** meg kell adni a névtérhez, hogy csak a virtuális hálózat megadott IP-címeiről vagy alhálózatáról engedélyezze a forgalmat. Ha nincsenek IP-és virtuális hálózati szabályok, a névtér a nyilvános interneten keresztül érhető el (a hozzáférési kulccsal).  

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>A VNet-integráció által engedélyezett speciális biztonsági forgatókönyvek 

Azok a megoldások, amelyek feszes és compartmentalized biztonságot igényelnek, és ahol a virtuális hálózati alhálózatok biztosítják a compartmentalized szolgáltatások közötti szegmentálást, továbbra is szükség van kommunikációs útvonalakra az ezekben a rekeszekben található szolgáltatások között.

A rekeszek közötti közvetlen IP-útvonal, beleértve a TCP/IP protokollon keresztüli HTTPS-t is, a hálózati réteg biztonsági réseinak kiaknázásának kockázatát hordozza. Az üzenetkezelési szolgáltatások olyan szigetelt kommunikációs útvonalakat biztosítanak, amelyekben a felek közötti átmenet során az üzenetek lemezre is írhatók. Az ugyanahhoz a Event Hubs-példányhoz kötött két különálló virtuális hálózatban a munkaterhelések hatékonyan és megbízhatóan kommunikálhatnak az üzeneteken keresztül, miközben a hálózat elkülönítési határának megfelelő integritása megmarad.
 
Ez azt jelenti, hogy a biztonsági szempontból bizalmas Felhőbeli megoldások nem csupán az Azure piacvezető megbízható és skálázható üzenetkezelési képességeihez férnek hozzá, de mostantól az üzenetküldés használatával kommunikációs útvonalakat hozhatnak létre a biztonságos Megoldási rekeszek között, amelyek eleve biztonságosabbak, mint bármely egyenrangú kommunikációs mód, beleértve a HTTPS-t és más TLS-védelemmel ellátott szoftvercsatorna-protokollokat is.

## <a name="bind-event-hubs-to-virtual-networks"></a>Esemény-hubok kötése virtuális hálózatokhoz

A **virtuális hálózati szabályok** a tűzfal biztonsági funkciója, amely azt szabályozza, hogy az Azure Event Hubs-névtér fogadjon-e kapcsolatokat egy adott virtuális hálózati alhálózatból.

Egy Event Hubs névtér egy virtuális hálózathoz kötése kétlépéses folyamat. Először létre kell hoznia egy **virtuális hálózati szolgáltatás végpontját** egy virtuális hálózat alhálózatán, és engedélyeznie kell azt a **Microsoft. EventHub** számára a [szolgáltatási végpont áttekintése című][vnet-sep] cikkben leírtak szerint. A szolgáltatás-végpont hozzáadása után a Event Hubs névteret egy **virtuális hálózati szabállyal** kell kötnie.

A virtuális hálózati szabály a Event Hubs névtér egy virtuális hálózati alhálózattal való társítása. Amíg a szabály létezik, az alhálózathoz kötött összes munkaterhelés hozzáférést kap a Event Hubs névtérhez. Event Hubs maga soha nem hoz létre kimenő kapcsolatokat, nem kell elérnie a hozzáférést, ezért a szabály engedélyezésével soha nem kap hozzáférést az alhálózathoz.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ez a szakasz bemutatja, hogyan használható a Azure Portal virtuális hálózati szolgáltatásbeli végpont hozzáadására. A hozzáférés korlátozásához integrálnia kell a virtuális hálózati szolgáltatás végpontját ehhez a Event Hubs névtérhez.

1. Navigáljon a **Event Hubs névtérhez** a [Azure Portal](https://portal.azure.com).
4. A bal oldali menü **Beállítások** területén válassza a **hálózatkezelés** lehetőséget. A **hálózatkezelés** lap csak a **standard** vagy a **dedikált** névtér esetében jelenik meg. 

    > [!WARNING]
    > Ha a **kiválasztott hálózatok** lehetőséget választja, és nem ad hozzá legalább egy IP-tűzfalszabály vagy virtuális hálózat ezen a lapon, a névtér a **nyilvános interneten** keresztül érhető el (a hozzáférési kulcs használatával). 

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Hálózatok lap – kiválasztott hálózatok lehetőség" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Ha a **minden hálózat** lehetőséget választja, az Event hub bármely IP-címről fogad kapcsolatokat (a hozzáférési kulccsal). Ez a beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt. 

    ![Tűzfal – az összes hálózat lehetőség ki van választva](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Ha az adott hálózatokhoz való hozzáférést szeretné korlátozni, válassza a **kijelölt hálózatok** lehetőséget a lap tetején, ha még nincs kiválasztva.
2. A lap **Virtual Network** szakaszában válassza a **+ meglévő virtuális hálózat hozzáadása** _ elemet. Válassza az _ *+ új virtuális hálózat létrehozása** lehetőséget, ha új VNet szeretne létrehozni. 

    ![meglévő virtuális hálózat hozzáadása](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)

    >[!WARNING]
    > Ha a **kiválasztott hálózatok** lehetőséget választja, és nem ad hozzá legalább egy IP-tűzfalszabály vagy virtuális hálózat ezen a lapon, a névtér a nyilvános interneten keresztül érhető el (a hozzáférési kulcs használatával).
3. Jelölje ki a virtuális hálózatot a virtuális hálózatok listájából, majd válassza ki az **alhálózatot**. Engedélyeznie kell a szolgáltatás végpontját, mielőtt hozzáadja a virtuális hálózatot a listához. Ha a szolgáltatási végpont nincs engedélyezve, akkor a portál felszólítja, hogy engedélyezze.
   
   ![alhálózat kiválasztása](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Az alhálózat szolgáltatási végpontjának engedélyezése után a következő sikeres üzenetnek kell megjelennie: **Microsoft. EventHub**. A hálózat hozzáadásához kattintson a lap alján található **Hozzáadás** gombra. 

    ![alhálózat kiválasztása és végpont engedélyezése](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Ha nem tudja engedélyezni a szolgáltatási végpontot, a Resource Manager-sablonnal figyelmen kívül hagyhatja a hiányzó virtuális hálózati szolgáltatás végpontját. Ez a funkció nem érhető el a portálon.
5. Itt adhatja meg, hogy szeretné- **e engedélyezni a megbízható Microsoft-szolgáltatások számára a tűzfal megkerülését**. További részletekért lásd a [megbízható Microsoft-szolgáltatások](#trusted-microsoft-services) című témakört. 
6. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra az eszköztáron. Várjon néhány percet, hogy a megerősítés megjelenjen a portál értesítésein.

    ![Hálózat mentése](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)

    > [!NOTE]
    > A megadott IP-címekre vagy tartományokra való hozzáférés korlátozásához tekintse meg a [hozzáférés engedélyezése adott IP-címekről vagy tartományokról](event-hubs-ip-filtering.md)című témakört.

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Resource Manager-sablon használata
A következő példa Resource Manager-sablon egy virtuális hálózati szabályt vesz fel egy meglévő Event Hubs névtérbe. A hálózati szabály a virtuális hálózatban lévő alhálózat AZONOSÍTÓját határozza meg. 

Az azonosító a virtuális hálózat alhálózatának teljes erőforrás-kezelői útvonala. Például `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` egy virtuális hálózat alapértelmezett alhálózata esetében.

Virtuális hálózati vagy tűzfalszabályok hozzáadásakor állítsa be a értékét a következőre `defaultAction` : `Deny` .


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
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
                    "service": "Microsoft.EventHub"
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
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
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
- [Azure Event Hubs IP-szűrés][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
