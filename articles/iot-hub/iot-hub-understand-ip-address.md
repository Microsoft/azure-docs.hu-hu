---
title: Az IoT Hub-központ IP-címének | Microsoft Docs
description: Az IoT Hub IP-címének és tulajdonságainak lekérdezése. Az IoT Hub IP-címe bizonyos esetekben változhat, például vészhelyreállítás vagy regionális feladatátvétel esetén.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 7d807a15d358bd621baedbff253f0c731e43ed26
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874170"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub IP-címek

A nyilvános végpontok IP-IoT Hub rendszeresen közzé vannak téve az _AzureIoTHub_ [szolgáltatáscímkével.](../virtual-network/service-tags-overview.md)

> [!NOTE]
> A helyszíni hálózatokon belül üzembe helyezett eszközök esetében a Azure IoT Hub támogatja a VNET-kapcsolat integrációját a privát végpontokkal. További [IoT Hub lásd: A virtuális hálózat támogatása.](./virtual-network-support.md)


Ezeket az IP-címelőtagokat használhatja a IoT Hub és az eszközök vagy hálózati eszközök közötti kapcsolat szabályozásához, hogy többféle hálózatelkülönítési célokat valósíthat meg:

| Cél | Alkalmazható forgatókönyvek | Módszer |
|------|-----------|----------|
| Győződjön meg arról, hogy az eszközök és a szolgáltatások csak IoT Hub végpontokkal kommunikálnak | [Eszközről a felhőbe,](./iot-hub-devguide-messaging.md)a [felhőből](./iot-hub-devguide-messages-c2d.md) az eszközre történő [üzenetküldés,](./iot-hub-devguide-direct-methods.md)a közvetlen metódusok, az ikereszköz- és [modulikrek](./iot-hub-devguide-device-twins.md) és az [eszközstreamek](./iot-hub-device-streams-overview.md) | Az _AzureIoTHub_ és az _EventHub_ szolgáltatáscímkék használatával felderítheti az IoT Hub-t és az eseményközpont IP-címelőtagját, és ennek megfelelően konfigurálhatja az eszközök és szolgáltatások tűzfalbeállítására vonatkozó ALLOW-szabályokat; forgalom eldobás más cél IP-címekre, amelyeken nem szeretné, hogy az eszközök vagy szolgáltatások kommunikáljanak. |
| Győződjön meg arról IoT Hub hogy az eszközvégpont csak az eszközökről és a hálózati eszközökről fogad kapcsolatokat | [Az eszközről a](./iot-hub-devguide-messaging.md) [](./iot-hub-devguide-messages-c2d.md) felhőbe, a felhőből az eszközre történő [üzenetküldés,](./iot-hub-devguide-direct-methods.md)a közvetlen metódusok, az ikereszköz- és [modulikrek](./iot-hub-devguide-device-twins.md) és az [eszközstreamek](./iot-hub-device-streams-overview.md) | Az IoT Hub [IP-szűrési](iot-hub-ip-filtering.md) funkció használatával engedélyezheti az eszközökről és a hálózati eszközök IP-címeinek kapcsolatait (lásd [a korlátozások szakaszt).](#limitations-and-workarounds) | 
| Győződjön meg arról, hogy az útvonalak egyéni végpont-erőforrásai (tárfiókok, Service Bus és eseményközpontok) csak a hálózati eszközökről érjék el | [Üzenetek útválasztása](./iot-hub-devguide-messages-d2c.md) | Kövesse az erőforrás a kapcsolatok korlátozására [](../storage/common/storage-network-security.md)vonatkozó útmutatását (például tűzfalszabályok, privát kapcsolatok [vagy](../private-link/private-endpoint-overview.md) [szolgáltatásvégpontokkal);](../virtual-network/virtual-network-service-endpoints-overview.md) Az _AzureIoTHub_ szolgáltatáscímkéivel felderítheti IoT Hub IP-címelőtagokat, és ALLOW szabályokat adhat hozzá ezekhez az IP-előtagokhoz az erőforrás tűzfal-konfigurációjában (lásd a korlátozások [szakaszt).](#limitations-and-workarounds) |



## <a name="best-practices"></a>Ajánlott eljárások

* Amikor ALLOW szabályokat ad hozzá az eszközök tűzfal-konfigurációjában, a legjobb, ha a megfelelő protokollok által használt adott [portokat biztosít.](./iot-hub-devguide-protocols.md#port-numbers)

* Az IoT Hub IP-címelőtagja változhat. Ezeket a módosításokat a rendszer rendszeres időközönként közzéteszi szolgáltatáscímkék használatával, mielőtt azok életbe lépnek. Ezért fontos, hogy olyan folyamatokat dolgozzon ki, amelyek rendszeresen lekérik és használják a legújabb szolgáltatáscímkéket. Ez a folyamat a szolgáltatáscímkék felderítési [API-jának használatával automatizálható.](../virtual-network/service-tags-overview.md#service-tags-on-premises) Vegye figyelembe, hogy a szolgáltatáscímkék felderítési API-ja még előzetes verzióban érhető el, és bizonyos esetekben előfordulhat, hogy nem készíti el a címkék és IP-címek teljes listáját. Amíg a Discovery API általánosan elérhetővé nem válik, fontolja meg a szolgáltatáscímkék használatát [letölthető JSON formátumban.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Használja az *AzureIoTHubot.[ region name]* címke az IoT Hub-végpontok által egy adott régióban használt IP-előtagok azonosításához. Az adatközpont vészhelyreállításának [](iot-hub-ha-dr.md) vagy a regionális feladatátvételnek a figyelembevétele érdekében győződjön meg arról, hogy a IoT Hub földrajzi párja régiója IP-előtagjaihoz való csatlakozás is engedélyezve van.

* A tűzfalszabályok IoT Hub letilthatja az Azure CLI- és PowerShell-parancsok futtatásához szükséges kapcsolatot a IoT Hub. Ennek elkerülése érdekében ALLOW szabályokat adhat hozzá az ügyfelek IP-címelőtagjaihoz, hogy engedélyezze a PARANCSSORi felület vagy a PowerShell-ügyfelek számára, hogy kommunikáljanak a IoT Hub.  


## <a name="limitations-and-workarounds"></a>Korlátozások és megkerülő megoldások

* IoT Hub IP-szűrési funkció korlátja 100 szabály. Ez a korlát az Azure ügyfélszolgálatán keresztüli kérésekkel emelhető ki. 

* A konfigurált [IP-szűrési](iot-hub-ip-filtering.md) szabályok csak a IoT Hub IP-végpontokra vonatkoznak, az IoT Hub beépített Eseményközpont-végpontjára nem. Ha ip-szűrést is alkalmaznia kell arra az eseményközpontra, ahol az üzenetek tárolva vannak, akkor a saját eseményközpont-erőforrását is használhatja, ahol közvetlenül konfigurálhatja a kívánt IP-szűrési szabályokat. Ennek érdekében ki kell építenie egy saját Event [](./iot-hub-devguide-messages-d2c.md) Hubs-erőforrást, és be kell állítania az üzenetek útválasztását, hogy az üzeneteket erre az erőforrásra küldje az IoT Hub beépített Eseményközpontja helyett. Végül, ahogy azt a fenti táblázatban is említettük, az üzenetek útválasztási funkcióját úgy engedélyezheti, hogy a IoT Hub IP-címelőtagja és a kiépített Eseményközpont-erőforrás közötti kapcsolat is elérhető.

* A tárfiókra való útválasztáskor a IoT Hub IP-címelőtagjaiból származó forgalom csak akkor lehetséges, ha a tárfiók másik régióban van, mint IoT Hub.

## <a name="support-for-ipv6"></a>IPv6-támogatás 

Az IPv6 jelenleg nem támogatott a IoT Hub.
