---
title: Azure IoT Hub klasszikus IP-szűrő (elavult) | Microsoft Docs
description: Frissítés a klasszikus IP-szűrőkről és az előnyökről
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jlian
ms.openlocfilehash: 6f326bafb311acedc48c5a349c78f1cd6bcebc87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661154"
---
# <a name="iot-hub-classic-ip-filter-and-how-to-upgrade"></a>Klasszikus IP-szűrés IoT Hub és frissítés 

A IoT Hub frissített IP-szűrője védi a beépített végpontot, és alapértelmezés szerint biztonságos. Habár a változtatások soha nem változnak, a frissített IP-szűrő fokozott biztonsági modellje nem kompatibilis a klasszikus IP-szűrőkkel, ezért bejelentjük a nyugdíjazást. További információ az [új, frissített](#whats-new) IP-szűrőről: Újdonságok és [IoT hub IP-szűrők](iot-hub-ip-filtering.md).

A szolgáltatás megszakadásának elkerülése érdekében az áttelepítési határidő előtt végre kell hajtania az irányított frissítést, ekkor a rendszer automatikusan végrehajtja a frissítést. További információ az áttelepítési idővonalról: [Azure Update](https://aka.ms/ipfilterv2azupdate).

## <a name="how-to-upgrade"></a>A frissítés lépései

1.  Látogasson el Azure Portal
2.  Keresse meg az IoT-központot.
3.  A bal oldali menüben válassza a **hálózatkezelés** lehetőséget.
4.  Ekkor megjelenik egy szalagcím, amely arra kéri, hogy frissítse az IP-szűrőt az új modellre. Válassza az **Igen** lehetőséget a folytatáshoz.
    :::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-upgrade-banner.png" alt-text="A klasszikus IP-szűrőről való frissítésre szolgáló szalagcím-kérést ábrázoló kép":::
5.  Mivel az új IP-szűrő alapértelmezés szerint blokkolja az összes IP-címet, a frissítés eltávolítja az egyéni megtagadási szabályokat, de a Mentés előtt lehetősége van áttekinteni őket. Körültekintően tekintse át a szabályokat, és győződjön meg róla, hogy azok működnek.
6.  A frissítés befejezéséhez kövesse az utasításokat.

## <a name="whats-new"></a>Újdonságok

### <a name="secure-by-default"></a>Alapértelmezés szerint biztonságos

A klasszikus IP-szűrő implicit módon lehetővé teszi, hogy az összes IP-cím alapértelmezés szerint a IoT Hubhoz kapcsolódjon, ami nem igazodik jól a leggyakoribb hálózati biztonsági forgatókönyvekhez. Általában csak megbízható IP-címekkel lehet csatlakozni az IoT hubhoz, és minden mást el kell utasítania. Ennek a célnak a klasszikus IP-szűrővel való elérése érdekében ez egy többlépéses folyamat. Ha például csak a-ból érkező forgalmat szeretné fogadni, a következőt `192.168.100.0/22` kell tennie:

1. Egyetlen *engedélyezési* szabály konfigurálása a következőhöz: `192.168.100.0/22` .
1. Más *blokkolási* szabály konfigurálása a következőhöz: `0.0.0.0/0` (az összes tiltása szabály)
1. Győződjön meg arról, hogy a szabályok megfelelően vannak rendezve, és az engedélyezési szabály a blokkolási szabály felett van rendezve.

A gyakorlatban ez a többlépéses folyamat zavart okoz. A felhasználók nem konfigurálták az "összes letiltása" szabályt, vagy nem megfelelően rendezték a szabályokat, ami nem kívánt expozíciót eredményezett. 

Az új IP-szűrő alapértelmezés szerint blokkolja az összes IP-címet. Csak a explicit módon felvett IP-címtartományok csatlakozhatnak IoT Hubhoz. A fenti példában a 2. és 3. lépés nem szükséges többé. Ez az új viselkedés leegyszerűsíti a konfigurációt, és a [Biztonság alapértelmezés szerint alapelve](https://wikipedia.org/wiki/Secure_by_default).

### <a name="protect-the-built-in-event-hub-compatible-endpoint"></a>A beépített Event hub-kompatibilis végpontok elleni védelem

A klasszikus IP-szűrő nem alkalmazható a beépített végpontra. Ez a korlátozás azt jelenti, hogy az összes konfigurált szabály (blokk) blokkolása esetén `0.0.0.0/0` a beépített végpont bármely IP-címről továbbra is elérhető.

Az új IP-szűrő lehetőséget biztosít a szabályok alkalmazására a beépített végpontra, ami csökkenti a hálózati biztonsági fenyegetésekkel szembeni kitettséget.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-built-in-endpoint.png" alt-text="Az a kép, amely a beépített végpontra alkalmazandó váltógomb megjelenítését mutatja":::

> [!NOTE]
> Ez a lehetőség nem érhető el az ingyenes (F1) IoT hubok számára. Ha az IP-szűrési szabályokat a beépített végpontra szeretné alkalmazni, használjon fizetős IoT hubot.

### <a name="api-impact"></a>API-hatás

A frissített IP-szűrő IoT Hub Resource API-ban érhető el `2020-08-31` (valamint `2020-08-31-preview` ) és újabb verziókban. A klasszikus IP-szűrő minden API-verzióban továbbra is elérhető, de az áttelepítés határideje mellett egy jövőbeli API-verzióban is el lesz távolítva. További információ az áttelepítési idővonalról: [Azure Update](https://aka.ms/ipfilterv2azupdate).

## <a name="tip-try-the-changes-before-they-apply"></a>Tipp: próbálja ki a módosításokat az alkalmazása előtt

Mivel az új IP-szűrő alapértelmezés szerint blokkolja az összes IP-címet, az egyes Blokkolási szabályok már nem kompatibilisek. Ezért az irányított frissítési folyamat eltávolítja ezeket az egyéni blokk-szabályokat. 

A klasszikus IP-szűrővel való módosításának kipróbálása:

1. Látogasson el az IoT hub **hálózatkezelés** lapjára
1. Jegyezze fel a meglévő IP-szűrő (klasszikus) konfigurációját arra az esetre, ha vissza kívánja állítani
1. A szabályok a **blokkolással** lehetőség mellett válassza ki a Kuka ikont az eltávolításához.
1. Adjon hozzá egy másik szabályt alul a (z) `0.0.0.0/0` , és válassza a **Letiltás** lehetőséget.
1. Válassza a **Mentés** lehetőséget

Ez a konfiguráció azt utánozza, hogy az új IP-szűrő hogyan viselkedik a klasszikus verzióról való frissítés után. Az egyik kivétel a beépített Endpoint Protection, amely nem lehetséges klasszikus IP-szűrő használatával próbálkozni. Azonban ez a funkció nem kötelező, így nem kell használnia, ha úgy gondolja, hogy valamit megszakít.

## <a name="tip-check-diagnostic-logs-for-all-ip-connections-to-your-iot-hub"></a>Tipp: a IoT hub-hoz tartozó összes IP-kapcsolat diagnosztikai naplóinak megkeresése

A zökkenőmentes átállás érdekében ellenőrizze a diagnosztikai naplókat a kapcsolatok kategóriában. Keresse meg a `maskedIpAddress` tulajdonságot, és ellenőrizze, hogy a tartományok a várt módon szerepelnek-e. Ne feledje: az új IP-szűrő letiltja az összes olyan IP-címet, amelyet explicit módon nem adtak hozzá.

## <a name="iot-hub-classic-ip-filter-documentation-retired"></a>IoT Hub klasszikus IP-szűrési dokumentáció (kivont)

> [!IMPORTANT]
> Alább látható a klasszikus IP-szűrő eredeti dokumentációja, amely megszűnik.

A biztonság az Azure IoT Hub-alapú IoT-megoldások fontos aspektusa. A biztonsági konfiguráció részeként olykor explicit módon meg kell adni, hogy mely IP-címekről kapcsolódhatnak az eszközök. Az *IP-szűrési* funkció lehetővé teszi, hogy szabályokat konfiguráljon a megadott IPv4-címekről érkező forgalom elutasítására vagy fogadására.

### <a name="when-to-use"></a>A következő esetekben használja

A IoT Hub végpontok bizonyos IP-címekhez való letiltásához két konkrét használati eset van:

* Az IoT hub csak a megadott IP-címtartományból érkező forgalmat fogadja, és minden mást visszautasít. Például az IoT hub és az [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) használatával privát kapcsolatokat hozhat létre az IoT hub és a helyszíni infrastruktúra között.

* El kell utasítania azokat az IP-címekről érkező forgalmat, amelyeket az IoT hub rendszergazdája gyanúsnak talált.

### <a name="how-filter-rules-are-applied"></a>A szűrési szabályok alkalmazásának módja

Az IP-szűrési szabályok a IoT Hub szolgáltatási szinten lesznek alkalmazva. Ezért az IP-szűrési szabályok az eszközök és a háttérbeli alkalmazások összes kapcsolatára érvényesek bármely támogatott protokoll használatával. Azonban az ügyfelek közvetlenül a [beépített Event hub-kompatibilis végpontról](iot-hub-devguide-messages-read-builtin.md) (nem a IoT hub kapcsolati karakterláncon keresztül) olvassák az IP-szűrési szabályokat. 

Minden olyan IP-címről érkező kapcsolódási kísérlet, amely megfelel az IoT hub elutasító IP-szabályának, a rendszer jogosulatlanul 401 állapotkódot és leírást kap. A válaszüzenet nem említi az IP-szabályt. Az IP-címek elutasítása megakadályozhatja, hogy más Azure-szolgáltatások, például az Azure Stream Analytics, az Azure Virtual Machines vagy a Azure Portal Device Explorer a IoT hub használatával.

> [!NOTE]
> Ha Azure Stream Analytics (ASA) protokollt kell használnia egy olyan IoT-hubhoz érkező üzenetek olvasásához, amelyeken engedélyezve van az IP-szűrő, akkor az IoT hub Event hub-kompatibilis nevét és végpontját használva manuálisan adhat hozzá [Event Hubs stream-bemenetet](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) az ASA-ban.

### <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a IoT hub-portálon található **IP-szűrő** rács üres. Ez az alapértelmezett beállítás azt jelenti, hogy a hub bármely IP-címről fogad kapcsolatokat. Ez az alapértelmezett beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

Az IP-szűrési beállítások lapon válassza a **hálózatkezelés**, a **nyilvános hozzáférés**, majd a **kijelölt IP-címtartományok** elemet:

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-default.png" alt-text="Alapértelmezett IP-szűrési beállítások IoT Hub":::

### <a name="add-or-edit-an-ip-filter-rule"></a>IP-szűrési szabály hozzáadása vagy szerkesztése

IP-szűrési szabály hozzáadásához válassza a **+ IP-szűrési szabály hozzáadása** lehetőséget.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-add-rule.png" alt-text="IP-szűrési szabály hozzáadása egy IoT hubhoz":::

Az **IP-szűrési szabály hozzáadása** lehetőség kiválasztása után töltse ki a mezőket.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-after-selecting-add.png" alt-text="Az IP-szűrési szabály hozzáadása lehetőség kiválasztása után":::

* Adjon **nevet** az IP-szűrési szabálynak. A névnek egyedi alfanumerikus sztringnek kell lennie, amely nem különbözteti meg a kis- és nagybetűket, és legfeljebb 128 karakterből áll. Csak az ASCII 7 bites alfanumerikus karakterei és a `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` elfogadottak.

* Adjon meg egy egyedi IPv4-címet vagy egy IP-címblokkot CIDR-jelölésrendszer használatával. A CIDR-jelölés szerint például a 192.168.100.0/22 a 192.168.100.0 és a 192.168.103.255 közötti 1024 IPv4-címet jelöli.

* Állítsa be az **Engedélyezés** vagy a **Letiltás** lehetőséget a szűrési szabály **művelet** értékeként.

A mezők kitöltése után kattintson a **Mentés** gombra a szabály mentéséhez. Riasztás fog megjelenni, amely tájékoztatja, hogy a frissítés folyamatban van.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-save-new-rule.png" alt-text="IP-szűrési szabály mentéséről szóló értesítés":::

Amikor eléri a maximális 10 IP-szűrési szabályt, a **Hozzáadás** lehetőség le lesz tiltva.

Meglévő szabály szerkesztéséhez jelölje ki a módosítani kívánt adatokat, végezze el a módosítást, majd válassza a **Mentés** lehetőséget a módosítás mentéséhez.

### <a name="delete-an-ip-filter-rule"></a>IP-szűrési szabály törlése

IP-szűrési szabály törléséhez válassza az adott sorban található kuka ikont, majd a **Mentés** lehetőséget. Ezzel törli a szabályt, és menti a módosítást.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-delete-rule.png" alt-text="IoT Hub IP-szűrési szabály törlése":::

### <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>IP-szűrők beolvasása és frissítése az Azure CLI-vel

A IoT Hub IP-szűrőinek lekérhető és frissíthető az [Azure CLI](/cli/azure/)-n keresztül.

A IoT Hub aktuális IP-szűrőinek lekéréséhez futtassa a következőt:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Ez egy JSON-objektumot ad vissza, amelyben a meglévő IP-szűrők a kulcs alatt vannak felsorolva `properties.ipFilterRules` :

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Ha új IP-szűrőt szeretne hozzáadni a IoT Hubhoz, futtassa a következőt:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

A IoT Hub meglévő IP-szűrőjének eltávolításához futtassa a következőt:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Vegye figyelembe, hogy az `<ipFilterIndexToRemove>` IP-szűrők sorrendjét meg kell egyeznie a IoT hub `properties.ipFilterRules` .

### <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>IP-szűrők beolvasása és frissítése Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A IoT Hub IP-szűrőinek lekérhető és beállítható [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

### <a name="update-ip-filter-rules-using-rest"></a>IP-szűrési szabályok frissítése a REST használatával

A IoT Hub IP-szűrőjét az Azure erőforrás-szolgáltató REST-végpontjának használatával is lekérheti és módosíthatja. Lásd: `properties.ipFilterRules` a [createorupdate metódusban](/rest/api/iothub/iothubresource/createorupdate).

### <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály kiértékelése

A rendszer az IP-szűrési szabályokat sorrendben alkalmazza, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletét.

Ha például fogadni szeretné a 192.168.100.0/22 tartomány címeit, minden mást viszont elutasítana, a táblázat első szabályának el kell fogadnia a 192.168.100.0/22 címtartományt. A következő szabálynak el kell utasítania minden címet a 0.0.0.0/0 tartomány használatával.

Az IP-szűrési szabályok sorrendjét a sorok elején lévő három, függőlegesen elhelyezett pontra kattintva, húzással módosíthatja.

Az IP-szűrési szabályok új sorrendjének mentéséhez kattintson a **Mentés** gombra.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-rule-order.png" alt-text="IoT Hub IP-szűrési szabályok sorrendjének módosítása":::

## <a name="next-steps"></a>Következő lépések

A IoT Hub képességeinek további megismeréséhez lásd:

* [IP-szűrők használata](iot-hub-ip-filtering.md)