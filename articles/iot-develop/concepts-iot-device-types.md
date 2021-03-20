---
title: Az Azure IoT-eszközök típusának áttekintése
description: Ismerje meg az Azure IoT által támogatott különböző típusú eszközöket és az elérhető eszközöket.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: aa99594fe3de98635e37d15beebf015f15dc4f64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654374"
---
# <a name="overview-of-azure-iot-device-types"></a>Az Azure IoT-eszközök típusának áttekintése
A IoT-eszközök széles választékban léteznek a hardveres platformok között. Kicsi, 8 bites MCU az asztali számítógépeken található legújabb x86-os processzorokra. Számos változó a IoT-eszközre kiválasztható hardver döntésére vonatkozik, és ez a cikk a legfontosabb különbségeket ismerteti.

## <a name="key-hardware-differentiators"></a>Key Hardware differenciálók
A hardver kiválasztásakor néhány fontos tényező a Cost, az energiafogyasztás, a Hálózatkezelés és a rendelkezésre álló bemenetek és kimenetek.

* **COST:** A végtermék tömeges előállítása általában kisebb, olcsóbb eszközöket használ. A kompromisszum azonban az, hogy az eszköz fejlesztése drágább lehet, mivel a magas korlátozott eszköz. A fejlesztési költségeket az összes előállított eszköz között eloszthatja, így az egységenkénti fejlesztési díj alacsony lesz.

* **Teljesítmény:** Az eszköz által felhasznált energia mennyisége fontos, ha az eszköz akkumulátorokat használ, és nem csatlakozik a Power Grid hálózathoz. A MCU gyakran alacsonyabb energiaellátási forgatókönyvekhez készültek, és jobb választás lehet az akkumulátorok élettartamának kibővítéséhez.

* **Hálózati hozzáférés:** Az eszközök felhőalapú szolgáltatásokhoz való csatlakoztatásának számos módja van. Ethernet, Wi-Fi és mobil, valamint néhány elérhető lehetőség. A választott kapcsolattípus attól függ, hogy az eszköz hol van telepítve, és hogyan használják azt. Például a mobil a nagy lefedettség miatt vonzó megoldás lehet, azonban a nagy forgalmú eszközök esetében költséges lehet. A vezetékes Ethernet olcsóbb adatköltséget biztosít, de a kevésbé hordozható.

* **Bemenetek és kimenetek:** Az eszközön elérhető bemenetek és kimenetek közvetlenül érintik az eszközök működési képességeit. A mikrokontroller általában sok I/O-funkcióval rendelkezik, amelyek közvetlenül a chipbe vannak építve, és az érzékelők széles választékát biztosítják közvetlenül a kapcsolódáshoz.

## <a name="microcontrollers-vs-microprocessors"></a>Mikroprocesszorok vs mikroprocesszorok
A IoT-eszközök két széles kategóriába, mikroprocesszorba (MCU) és mikroprocesszorba (MPUs) is elkülöníthetők.

A **MCU** kevésbé költségesek és egyszerűbbek, mint a MPUs. Az MCU számos funkciót tartalmaz, például a memóriát, a csatolókat és az I/O-t a zsetonon belül. A MPU ezt a funkciót a zsetonokat támogató összetevőkből fogja kirajzolni. Az MCU gyakran egy valós idejű operációs rendszert (RTOS) használ, vagy operációs rendszer nélküli (nem OS) operációs rendszert futtat, és valós idejű választ ad a külső eseményekre, és nagyon determinisztikus a válaszokat.

A **MPUs** általában olyan általános célú operációs rendszert futtatnak, mint például a Windows, a Linux vagy a MacOSX, amely nem determinisztikus valós idejű választ biztosít. Általában nincs garancia arra, hogy egy feladat befejeződik. 

:::image type="content" border="false" source="media/concepts-iot-device-types/mcu-vs-mpu.png" alt-text="MCU vs MPU":::

Alább látható egy táblázat, amely az MCU és egy MPU-alapú rendszer közötti különbségek meghatározását mutatja be:

||Mikrokontrollerek (MCU)|Mikroprocesszor (MPU)|
|-|-|-|
|**CPU**| Less | Kibontás |
|**RAM**| Less | Kibontás |
|**Flash**| Less | Kibontás |
|**OS**| Nem vagy RTOS | Általános célú |
|**Fejlesztési nehézség**| Nehezebb |  Könnyebben |
|**Energiafogyasztás**| Alacsonyabb | Magasabb |
|**Költségek**| Alacsonyabb | Magasabb |
|**Determinisztikus**| Yes | Nem – kivételek|
|**Eszköz mérete**| Kisebb | Nagyobb |
