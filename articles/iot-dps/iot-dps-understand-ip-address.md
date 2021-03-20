---
title: A IoT Device kiépítési szolgáltatás (DPS) példányának IP-címének megértése | Microsoft Docs
description: Ismerje meg, hogyan kérdezheti le a IoT-eszköz kiépítési szolgáltatásának (DPS) címeit és tulajdonságait. A DPS-példány IP-címe változhat bizonyos esetekben, például a vész-helyreállítási vagy a regionális feladatátvétel során.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "79284927"
---
# <a name="iot-hub-dps-ip-addresses"></a>IoT Hub DPS IP-címei

Egy IoT Hub Device Provisioning Service (DPS) nyilvános végpontjának IP-címének előtagjait rendszeresen közzéteszi a _AzureIoTHub_ [szolgáltatás címkéjén](../virtual-network/service-tags-overview.md). Ezeket az IP-cím előtagokat használhatja a IoT DPS-példányok és eszközök vagy hálózati eszközök közötti kapcsolat vezérléséhez a különböző hálózati elkülönítési célok megvalósítása érdekében:

| Cél | Módszer |
|------|----------|
| Gondoskodjon arról, hogy az eszközök és szolgáltatások csak IoT Hub DPS-végpontokkal kommunikáljanak | IoT Hub DPS-példányok felderítéséhez használja a _AzureIoTHub_ szolgáltatás címkéjét. Konfigurálja az eszközök és szolgáltatások tűzfalának engedélyezési szabályait az IP-címek előtagjainak megfelelően. Konfigurálja a szabályokat úgy, hogy eldobják a forgalmat más célként megadott IP-címekre, és nem szeretné, hogy az eszközök és szolgáltatások kommunikáljanak a szolgáltatással. |
| Győződjön meg róla, hogy IoT Hub DPS-végpontja csak az eszközökről és a hálózati eszközökről kap kapcsolatokat | A IoT DPS [IP-szűrő funkció](iot-dps-ip-filtering.md) használatával hozzon létre szűrési szabályokat az eszközhöz és a DPS szolgáltatás API-khoz. Ezekkel a szűrési szabályokkal engedélyezheti a kapcsolódást csak az eszközökről és a hálózati eszközök IP-címeiről (lásd: [korlátozások](#limitations-and-workarounds) szakasz). | 




## <a name="best-practices"></a>Ajánlott eljárások

* Az eszközök tűzfal-konfigurációjának engedélyezési szabályok hozzáadásakor a legmegfelelőbb a [megfelelő protokollok által használt portok](../iot-hub/iot-hub-devguide-protocols.md#port-numbers)biztosításához.

* A IoT DPS-példányok IP-címének előtagjainak módosítása változhat. A módosításokat a rendszer rendszeresen közzéteszi a szolgáltatáson keresztül, mielőtt érvénybe lépnek. Ezért fontos, hogy folyamatokat fejlesszen ki a legújabb szolgáltatási címkék rendszeres beolvasására és használatára. Ez a folyamat automatizálható a [Service Tags Discovery API](../virtual-network/service-tags-overview.md#service-tags-on-premises)használatával. A szolgáltatás címkék felderítésének API-je még előzetes verzióban érhető el, és bizonyos esetekben előfordulhat, hogy nem hozza létre a címkék és az IP-címek teljes listáját. Amíg a felderítési API általánosan elérhetővé válik, érdemes lehet a [szolgáltatás címkéit letölthető JSON formátumban](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)használni. 

* Használja a *AzureIoTHub. [ régió neve]* címke egy adott régióban a DPS-végpontok által használt IP-előtagok azonosításához. Ha az adatközpont vész-helyreállítási vagy [regionális feladatátvételi](../iot-hub/iot-hub-ha-dr.md)szolgáltatását szeretné használni, győződjön meg arról, hogy a DPS-példány földrajzi párja RÉGIÓJÁNAK IP-előtagjaihoz való kapcsolódás is engedélyezve van.

* Egy DPS-példány tűzfalszabályok beállítása letilthatja az Azure CLI és a PowerShell-parancsok futtatásához szükséges kapcsolatokat. A kapcsolódási problémák elkerülése érdekében hozzáadhat engedélyezési szabályokat az ügyfelek IP-címeihez, hogy engedélyezze újra a parancssori felületet vagy a PowerShell-ügyfelet a DPS-példánnyal való kommunikációhoz.  


## <a name="limitations-and-workarounds"></a>Korlátozások és megkerülő megoldások

* A DPS IP-szűrő funkció 100-as korláttal rendelkezik. Ezt a korlátot és az Azure-ügyfélszolgálaton keresztüli kérésekkel lehet kiemelni. 

* A konfigurált [IP-szűrési szabályok](iot-dps-ip-filtering.md) csak a DPS-végpontokon érvényesek, nem a csatolt IoT hub-végpontokon. A csatolt IoT hubok IP-szűrését külön kell konfigurálni. További információ: [IoT hub IP-szűrési szabályok](../iot-hub/iot-hub-ip-filtering.md).

## <a name="support-for-ipv6"></a>IPv6-támogatás 

Az IPv6 jelenleg IoT Hub vagy DPS rendszeren nem támogatott.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az IP-címekről a DPS használatával kapcsolatban, tekintse meg a következőt:

* [IP-szűrés konfigurálása](iot-dps-ip-filtering.md)
