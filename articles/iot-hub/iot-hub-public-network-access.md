---
title: Nyilvános hálózati hozzáférés kezelése az Azure IoT hub-hoz
description: Az IoT hub nyilvános hálózati hozzáférésének letiltására és engedélyezésére vonatkozó dokumentáció
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 539e420cb9085fad10ea3972ba0e9e5ffb9d0622
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419762"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>A IoT hub nyilvános hálózati hozzáférésének kezelése

A nyilvános hálózati hozzáférés letiltásával korlátozhatja a hozzáférést a [VNet IoT hub-beli privát végponthoz](virtual-network-support.md). Ehhez használja a Azure Portal vagy az API-t `publicNetworkAccess` . 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Nyilvános hálózati hozzáférés kikapcsolása Azure Portal használatával

1. Látogasson el [Azure Portal](https://portal.azure.com)
2. Keresse meg az IoT-központot.
3. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget.
4. A "nyilvános hálózati hozzáférés engedélyezése" területen válassza a **Letiltva** lehetőséget.
5. Kattintson a **Mentés** gombra.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="A nyilvános hálózati hozzáférés kikapcsolását Azure Portal bemutató kép" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

A nyilvános hálózati hozzáférés bekapcsolásához válassza a **minden hálózat**, majd a **Mentés** lehetőséget.

## <a name="accessing-the-iot-hub-after-disabling-public-network-access"></a>A IoT Hub elérése a nyilvános hálózati hozzáférés letiltása után

Ha a nyilvános hálózati hozzáférés le van tiltva, a IoT Hub csak [Az Azure Private link használatával érhető el az VNet privát végpontján](virtual-network-support.md)keresztül. Ez a korlátozás magában foglalja a Azure Portalon keresztüli hozzáférését, mert az IoT Hub szolgáltatáshoz tartozó API-hívások közvetlenül a böngészőjében, a hitelesítő adataival lesznek elérhetők. 

## <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>Végpontok, IP-címek és portok IoT Hub a nyilvános hálózati hozzáférés letiltása után

IoT Hub egy több-bérlős platform-szolgáltatás (Péter), így a különböző ügyfelek ugyanazt a számítási, hálózati és tárolási hardveres erőforrásokat használják. A IoT Hub állomásneve egy nyilvános végpontra mutat, amely nyilvánosan irányítható IP-címmel rendelkezik az interneten keresztül. A különböző ügyfelek megoszthatják ezt a IoT Hub nyilvános végpontot, és IoT a nagy területű hálózatokon és a helyszíni hálózatokon található eszközöket. 

A nyilvános hálózati hozzáférés letiltása egy adott IoT hub-erőforráson történik, az elkülönítés biztosítása érdekében. Annak érdekében, hogy a szolgáltatás a nyilvános elérési úttal rendelkező más ügyfelek erőforrásai számára is aktív maradjon, a nyilvános végpont feloldható marad, az IP-címek felderíthetők, és a portok nyitva maradnak. Ez nem okoz problémát, mert a Microsoft több biztonsági réteget integrál a bérlők közötti teljes elkülönítés biztosításához. További információ: elkülönítés az [Azure nyilvános felhőben](../security/fundamentals/isolation-choices.md#tenant-level-isolation).

## <a name="ip-filter"></a>IP-szűrő 

Ha a nyilvános hálózati hozzáférés le van tiltva, az összes [IP-szűrési](iot-hub-ip-filtering.md) szabályt figyelmen kívül hagyja a rendszer. Ennek az az oka, hogy a nyilvános internetről érkező összes IP-cím le van tiltva. Az IP-szűrő használatához használja a **kiválasztott IP-címtartományok** lehetőséget.

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Hibajavítás beépített Event hub-kompatibilis végponttal

Hiba történt a IoT Hub, ahol a [beépített Event hub-kompatibilis végpont](iot-hub-devguide-messages-read-builtin.md) továbbra is elérhető lesz nyilvános interneten keresztül, ha a nyilvános hálózati hozzáférés a IoT hub le van tiltva. Ha többet szeretne megtudni erről a hibáról, tekintse meg a [nyilvános hálózati hozzáférés letiltása IoT hub letiltja a hozzáférést a beépített Event hub-végponthoz](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).
