---
title: Az IoT hub IP-címének megismerése | Microsoft Docs
description: Ismerje meg, hogyan kérdezheti le az IoT hub IP-címét és annak tulajdonságait. Az IoT hub IP-címe bizonyos helyzetekben változhat, például a vész-helyreállítás vagy a regionális feladatátvétel során.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 1bfae8c7afbfdc6e73dd8bb17b94e6543361e9ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83848228"
---
# <a name="iot-hub-ip-addresses"></a>IP-címek IoT Hub

IoT Hub nyilvános végpontok IP-címének előtagjait a rendszer rendszeresen közzéteszi a _AzureIoTHub_ [szolgáltatás címkéjén](../virtual-network/service-tags-overview.md).

> [!NOTE]
> A helyszíni hálózatokon belül üzembe helyezett eszközök esetében az Azure IoT Hub támogatja a VNET-kapcsolatok integrációját a privát végpontokkal. További információ: [IoT hub támogatás a VNet](./virtual-network-support.md) .


Ezeket az IP-cím előtagokat használhatja a IoT Hub és az eszközök vagy hálózati eszközök közötti kapcsolat vezérléséhez a különböző hálózati elkülönítési célok megvalósítása érdekében:

| Cél | Alkalmazható helyzetek | Módszer |
|------|-----------|----------|
| Gondoskodjon arról, hogy az eszközök és szolgáltatások csak IoT Hub-végpontokkal kommunikáljanak | [Eszközről a felhőbe](./iot-hub-devguide-messaging.md), valamint [a felhőből az eszközre irányuló](./iot-hub-devguide-messages-c2d.md) üzenetkezelés, [közvetlen metódusok](./iot-hub-devguide-direct-methods.md), [eszközök és modulok az ikrek](./iot-hub-devguide-device-twins.md) és az [eszköz streamek](./iot-hub-device-streams-overview.md) számára | A _AzureIoTHub_ és a _EventHub_ Service-címkék használatával felderítheti IoT hub és az Event hub IP-címeinek előtagjait, és konfigurálhatja az eszközök és szolgáltatások tűzfalának engedélyezési szabályait az adott IP-cím előtagjainak megfelelően; dobja el a forgalmat más cél IP-címekre, nem szeretné, hogy az eszközök és a szolgáltatások kommunikáljanak a szolgáltatással. |
| Győződjön meg arról, hogy az IoT Hub-eszköz végpontja csak az eszközökről és a hálózati eszközökről fogad kapcsolatokat | [Eszközről a felhőbe](./iot-hub-devguide-messaging.md), valamint [a felhőből az eszközre irányuló](./iot-hub-devguide-messages-c2d.md) üzenetkezelés, [közvetlen metódusok](./iot-hub-devguide-direct-methods.md), [eszközök és modulok az ikrek](./iot-hub-devguide-device-twins.md) és az [eszköz streamek](./iot-hub-device-streams-overview.md) számára | A IoT Hub [IP-szűrő funkció](iot-hub-ip-filtering.md) használatával engedélyezheti az eszközök és a hálózati eszközök IP-címeinek kapcsolatait (lásd a [korlátozások](#limitations-and-workarounds) szakaszt). | 
| Győződjön meg arról, hogy az útvonalak egyéni végpont-erőforrásai (Storage-fiókok, Service Bus és Event hubok) csak a hálózati eszközökről érhetők el | [Üzenetek útválasztása](./iot-hub-devguide-messages-d2c.md) | Kövesse a kapcsolat korlátozására szolgáló erőforrás útmutatását (például [Tűzfalszabályok](../storage/common/storage-network-security.md), [magánhálózati hivatkozások](../private-link/private-endpoint-overview.md)vagy [szolgáltatási végpontok](../virtual-network/virtual-network-service-endpoints-overview.md)használatával); a _AzureIoTHub_ szolgáltatás-címkék használatával felderítheti IoT hub IP-címek előtagjait, és engedélyezheti az adott IP-előtag engedélyezési szabályait az erőforrás tűzfal-konfigurációjában (lásd a [korlátozások](#limitations-and-workarounds) szakaszt). |



## <a name="best-practices"></a>Ajánlott eljárások

* Az eszközök tűzfal-konfigurációjának engedélyezési szabályok hozzáadásakor a legmegfelelőbb a [megfelelő protokollok által használt portok](./iot-hub-devguide-protocols.md#port-numbers)biztosításához.

* Az IoT hub IP-címének előtagjainak módosítása változhat. A módosításokat a rendszer rendszeresen közzéteszi a szolgáltatáson keresztül, mielőtt érvénybe lépnek. Ezért fontos, hogy folyamatokat fejlesszen ki a legújabb szolgáltatási címkék rendszeres beolvasására és használatára. Ez a folyamat automatizálható a [Service Tags Discovery API](../virtual-network/service-tags-overview.md#service-tags-on-premises)használatával. Vegye figyelembe, hogy a Service Tags Discovery API még előzetes verzióban érhető el, és bizonyos esetekben előfordulhat, hogy nem hozza létre a címkék és az IP-címek teljes listáját. Amíg a felderítési API általánosan elérhetővé válik, érdemes lehet a [szolgáltatás címkéit letölthető JSON formátumban](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)használni. 

* Használja a *AzureIoTHub. [ régió neve]* címkével azonosíthatja az IoT hub-végpontok által az adott régióban használt IP-előtagokat. Az adatközpontok vész-helyreállítási feladatainak elvégzéséhez, illetve a [regionális feladatátvételhez](iot-hub-ha-dr.md) a IoT hub geo-pair RÉGIÓJÁNAK IP-előtagjaihoz való kapcsolódás is engedélyezett.

* A tűzfalszabályok beállítása a IoT Hubban letilthatja az Azure CLI és a PowerShell-parancsok futtatásához szükséges kapcsolatokat a IoT Hub. Ennek elkerüléséhez engedélyezheti az ügyfelek IP-címeinek engedélyezési szabályait, hogy engedélyezze újra a CLI vagy a PowerShell-ügyfelek számára a IoT Hub való kommunikációt.  


## <a name="limitations-and-workarounds"></a>Korlátozások és megkerülő megoldások

* IoT Hub IP-szűrő funkció legfeljebb 10 szabályt tartalmaz. Ezt a korlátot és az Azure-ügyfélszolgálaton keresztüli kérésekkel lehet kiemelni. 

* A konfigurált [IP-szűrési szabályok](iot-hub-ip-filtering.md) csak a IoT hub IP-végpontokon érvényesek, nem az IoT hub beépített Event hub-végpontján. Ha azt is megköveteli, hogy az IP-szűrést alkalmazni lehessen azon az esemény-Központon, ahol az üzenetek tárolódnak, akkor ezt úgy teheti meg, hogy a saját Event hub-erőforrást fogja használni, amely közvetlenül a kívánt IP-szűrési szabályokat konfigurálja Ehhez létre kell hoznia a saját Event hub-erőforrást, és úgy kell beállítania az [üzenet-útválasztást](./iot-hub-devguide-messages-d2c.md) , hogy az IoT hub beépített Event hub helyett az adott erőforráshoz küldje az üzeneteket. Végül, ahogy az a fenti táblázatban is látható, az üzenet-útválasztási funkció engedélyezéséhez engedélyeznie kell a IoT Hub IP-címéhez való kapcsolódást a kiépített Event hub-erőforráshoz.

* A Storage-fiókhoz való útválasztás esetén a IoT Hub IP-címének előtagjairól érkező forgalom csak akkor lehetséges, ha a Storage-fiók egy másik régióban található, mint a IoT Hub.

## <a name="support-for-ipv6"></a>IPv6-támogatás 

Az IPv6 jelenleg nem támogatott IoT Hubon.
