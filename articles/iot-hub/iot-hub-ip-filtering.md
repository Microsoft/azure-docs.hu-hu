---
title: Azure IoT Hub IP-szűrők | Microsoft Docs
description: Az IP-szűrés használata az adott IP-címek és az Azure IoT hub közötti kapcsolatok engedélyezéséhez.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: jlian
ms.openlocfilehash: 62292a2ee51f8e1838e9cf3376367a02964cad47
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418742"
---
# <a name="use-ip-filters"></a>IP-szűrők használata

A biztonság az Azure IoT Hub-alapú IoT-megoldások fontos aspektusa. A biztonsági konfiguráció részeként olykor explicit módon meg kell adni, hogy mely IP-címekről kapcsolódhatnak az eszközök. Az *IP-szűrési* funkció lehetővé teszi, hogy szabályokat konfiguráljon a megadott IPv4-címekről érkező forgalom elutasítására vagy fogadására.

## <a name="when-to-use"></a>A következő esetekben használja

Az IP-szűrő használatával csak a megadott IP-címtartományból érkező forgalmat fogadhatja el, és minden mást visszautasíthat. Például az IoT hub és az [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) használatával privát kapcsolatokat hozhat létre az IoT hub és a helyszíni infrastruktúra között.

## <a name="default-setting"></a>Alapértelmezett beállítás

Az IP-szűrési beállítások lapon válassza a **hálózatkezelés**, a **nyilvános hozzáférés**, majd a **kijelölt IP-címtartományok** elemet:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="Alapértelmezett IP-szűrési beállítások IoT Hub":::

Alapértelmezés szerint a IoT hub-portálon található **IP-szűrő** rács üres. Ez az alapértelmezett beállítás azt jelenti, hogy a hub blokkolja az összes IP-címről érkező kapcsolatokat. Ez az alapértelmezett beállítás egyenértékű egy olyan szabállyal, amely letiltja az `0.0.0.0/0` IP-címtartományt.

## <a name="add-or-edit-an-ip-filter-rule"></a>IP-szűrési szabály hozzáadása vagy szerkesztése

IP-szűrési szabály hozzáadásához válassza a **+ IP-szűrési szabály hozzáadása** lehetőséget.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="IP-szűrési szabály hozzáadása egy IoT hubhoz":::

Az **IP-szűrési szabály hozzáadása** lehetőség kiválasztása után töltse ki a mezőket.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Az IP-szűrési szabály hozzáadása lehetőség kiválasztása után":::

* Adjon **nevet** az IP-szűrési szabálynak. A névnek egyedi, kis-és nagybetűket megkülönböztető, alfanumerikus sztringnek kell lennie, legfeljebb 128 karakter hosszú lehet. Csak az ASCII 7 bites alfanumerikus karakterei és a `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` elfogadottak.

* Adjon meg egy egyedi IPv4-címet vagy egy IP-címblokkot CIDR-jelölésrendszer használatával. A CIDR-jelölés szerint például a 192.168.100.0/22 a 192.168.100.0 és a 192.168.103.255 közötti 1024 IPv4-címet jelöli.

A mezők kitöltése után kattintson a **Mentés** gombra a szabály mentéséhez. Riasztás fog megjelenni, amely tájékoztatja, hogy a frissítés folyamatban van.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="IP-szűrési szabály mentéséről szóló értesítés":::

Amikor eléri a maximális 10 IP-szűrési szabályt, a **Hozzáadás** lehetőség le lesz tiltva.

Meglévő szabály szerkesztéséhez jelölje ki a módosítani kívánt adatokat, végezze el a módosítást, majd válassza a **Mentés** lehetőséget a módosítás mentéséhez.

## <a name="delete-an-ip-filter-rule"></a>IP-szűrési szabály törlése

IP-szűrési szabály törléséhez válassza az adott sorban található kuka ikont, majd a **Mentés** lehetőséget. Ezzel törli a szabályt, és menti a módosítást.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="IoT Hub IP-szűrési szabály törlése":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>IP-szűrési szabályok alkalmazása a beépített Event hub-kompatibilis végpontra

Ha az IP-szűrési szabályokat a beépített Event hub-kompatibilis végpontra szeretné alkalmazni, jelölje be az **IP-szűrők alkalmazása a beépített végpontra** jelölőnégyzetet, majd kattintson a **Save (Mentés**) gombra.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="A beépített végpont és a Mentés váltógomb megjelenítésére szolgáló kép":::

> [!NOTE]
> Ez a lehetőség nem érhető el az ingyenes (F1) IoT hubok számára. Ha az IP-szűrési szabályokat a beépített végpontra szeretné alkalmazni, használjon fizetős IoT hubot.

Ha engedélyezi ezt a beállítást, az IP-szűrési szabályok replikálódnak a beépített végpontra, így csak a megbízható IP-címtartományok férhetnek hozzá.

Ha letiltja ezt a beállítást, a beépített végpont minden IP-cím számára elérhető. Ez a viselkedés akkor lehet hasznos, ha a végpontot olyan szolgáltatásokkal szeretné beolvasni, amelyek olyan IP-címeket módosítanak, mint a Azure Stream Analytics. 

## <a name="how-filter-rules-are-applied"></a>A szűrési szabályok alkalmazásának módja

Az IP-szűrési szabályok a IoT Hub szolgáltatási szinten lesznek alkalmazva. Ezért az IP-szűrési szabályok az eszközök és a háttérbeli alkalmazások összes kapcsolatára érvényesek bármely támogatott protokoll használatával. Azt is megadhatja, hogy a [beépített Event hub-kompatibilis végpont](iot-hub-devguide-messages-read-builtin.md) (a IoT hub kapcsolati karakterláncon keresztül nem) ehhez a szabályokhoz van-e kötve. 

A explicit módon nem engedélyezett IP-címről érkező kapcsolódási kísérletek jogosulatlan 401-as állapotkódot és-leírást kapnak. A válaszüzenet nem említi az IP-szabályt. Az IP-címek elutasítása megakadályozhatja, hogy más Azure-szolgáltatások, például az Azure Stream Analytics, az Azure Virtual Machines vagy a Azure Portal Device Explorer a IoT hub használatával.

> [!NOTE]
> Ha Azure Stream Analytics (ASA) szolgáltatást kell használnia egy olyan IoT hub üzeneteinek olvasásához, amelyen engedélyezve van az IP-szűrő, **Tiltsa le** az **IP-szűrők alkalmazása a beépített végpontra** beállítást, majd az IoT hub Event hub-kompatibilis nevét és végpontját használva manuálisan vegyen fel egy [Event Hubs stream-bemenetet](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) az ASA-ba.

### <a name="ordering"></a>Rendezés

Az IP-szűrési szabályok *engedélyezik* a szabályokat, és a rendelés nélkül is alkalmazhatók. Csak az Ön által hozzáadott IP-címek csatlakozhatnak IoT Hubhoz. 

Ha például el szeretné fogadni a címeket a tartományban, és el `192.168.100.0/22` kell utasítania minden mást, akkor csak egy szabályt kell hozzáadnia a rácshoz címtartomány használatával `192.168.100.0/22` .

### <a name="azure-portal"></a>Azure Portal 

Az IP-szűrési szabályok akkor is érvényesek, ha a IoT Hubt Azure Portalon keresztül használja. Ennek az az oka, hogy az IoT Hub szolgáltatáshoz való API-hívások közvetlenül a böngészőjében, a hitelesítő adataival vannak ellátva, amelyek konzisztensek a többi Azure-szolgáltatással. Ha az IP-szűrő engedélyezve van a Azure Portal használatával való IoT Hub eléréséhez, adja hozzá a számítógép IP-címét az engedélyezési listához. 

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>IP-szűrők beolvasása és frissítése az Azure CLI-vel

A IoT Hub IP-szűrőinek lekérhető és frissíthető az [Azure CLI](/cli/azure/)-n keresztül.

A IoT Hub aktuális IP-szűrőinek lekéréséhez futtassa a következőt:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Ez egy JSON-objektumot ad vissza, amelyben a meglévő IP-szűrők a kulcs alatt vannak felsorolva `properties.networkRuleSets` :

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

Ha új IP-szűrőt szeretne hozzáadni a IoT Hubhoz, futtassa a következőt:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

A IoT Hub meglévő IP-szűrőjének eltávolításához futtassa a következőt:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

Itt `<ipFilterIndexToRemove>` meg kell egyeznie az IP-szűrők sorrendjével a IoT hub `properties.networkRuleSets.ipRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>IP-szűrők beolvasása és frissítése Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A IoT Hub IP-szűrőinek lekérhető és beállítható [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>IP-szűrési szabályok frissítése a REST használatával


A IoT Hub IP-szűrőjét az Azure erőforrás-szolgáltató REST-végpontjának használatával is lekérheti és módosíthatja. Lásd: `properties.networkRuleSets` a [createorupdate metódusban](/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-classic-retirement"></a>IP-szűrő (klasszikus) nyugdíjazás

A klasszikus IP-szűrő ki lett vonva. További információ: [IoT hub klasszikus IP-szűrő és a frissítés](iot-hub-ip-filter-classic.md).

## <a name="next-steps"></a>Következő lépések

A IoT Hub képességeinek további megismeréséhez lásd:

* [IoT Hub metrikák](./monitor-iot-hub.md)
* [IoT Hub a magánhálózati és felügyelt identitású virtuális hálózatok támogatása](virtual-network-support.md)
* [A IoT hub nyilvános hálózati hozzáférésének kezelése](iot-hub-public-network-access.md)
* [Az IoT Hub monitorozása](monitor-iot-hub.md)
