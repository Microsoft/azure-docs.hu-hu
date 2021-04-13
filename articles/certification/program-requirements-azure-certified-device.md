---
title: Az Azure Certified eszközre vonatkozó követelmények
description: Az Azure Certified eszköz programra vonatkozó követelményei
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Azure Certified Device Certification Requirements
ms.service: certification
ms.openlocfilehash: 497ffa4b3026491d6aa95df87708b3b1f2f1619e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308285"
---
# <a name="azure-certified-device-requirements"></a>Az Azure Certified eszközre vonatkozó követelmények 
(korábbi nevén IoT Hub)

Ez a dokumentum felvázolja az eszközre jellemző képességeket, amelyek az Azure Certified-eszköz katalógusában lesznek megjelenítve. A képesség olyan egyedi eszköz attribútum, amely szoftveres implementáció vagy szoftver-és hardver-implementáció kombinációja lehet. 

## <a name="program-purpose"></a>Program célja

A Microsoft leegyszerűsíti az IoT-t és az Azure Certified Certificate minősítést, amely biztosítja, hogy minden eszköz típusa az Azure IoT Hub biztonságosan legyen kiépítve.

Az Azure Certified eszköz minősítésének ígérete a következők:

1. Eszköz-támogatási telemetria, amely együttműködik IoT Hub
2.  Eszköz-támogatási IoT Hub Device Provisioning Service (DPS) az Azure-ba való biztonságos üzembe helyezéshez IoT Hub
3.  Az eszköz támogatja a cél DPS-azonosító hatókör-átvitelének egyszerű bevitelét anélkül, hogy a felhasználónak újra kellene fordítania a beágyazott kódot.
4.  Opcionálisan érvényesít más elemeket, például a felhőt az eszköz üzeneteire, a közvetlen metódusokra és az eszközök Twin-re 

## <a name="requirements"></a>Követelmények

**Szükséges Eszközről a felhőbe: a teszt célja annak biztosítása, hogy az telemetria küldő eszközök IoT Hub**

| **Név**                | AzureCertified. D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Már elérhető                                                |
| **A következőkre vonatkozik**          | Levél eszköz/peremhálózati eszköz                                      |
| **OS**                  | Független                                                     |
| **Érvényesítés típusa**     | Automatizált                                                    |
| **Érvényesítés**          | Az eszköznek minden telemetria-sémát el kell küldenie IoT Hubba. A Microsoft biztosítja a [portál munkafolyamatát](https://certify.azure.com/) a tesztek végrehajtásához. Eszközről a felhőbe (kötelező): **1.** Ellenőrzi, hogy az eszköz küldhet-e üzenetet az AICS által felügyelt IoT Hub **2.** A felhasználónak meg kell adnia az üzenetek számát és gyakoriságát. **3.** A AICS ellenőrzi, hogy a telemetria fogadta-e a hub-példány |
| **Erőforrások**           | [Minősítési lépések](./overview.md) (az összes további erőforrással rendelkezik) |

**Szükséges DPS: a teszt célja, hogy ellenőrizze az eszköz megvalósítását, és támogassa a IoT Hub Device Provisioning Service a három igazolási módszer egyikével.**

| **Név**                | AzureCertified. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Új                                                          |
| **A következőkre vonatkozik**          | Bármely eszköz                                                   |
| **OS**                  | Független                                                     |
| **Érvényesítés típusa**     | Automatizált                                                    |
| **Érvényesítés**          | Az eszköz támogatja a cél DPS-azonosító hatókörének egyszerű bevitelét anélkül, hogy újra kellene fordítania a beágyazott kódot. A Microsoft biztosítja a [portál munkafolyamatot](https://certify.azure.com) a tesztek végrehajtásához annak ellenőrzéséhez, hogy az eszköz támogatja-e a DPS 1-et **.** A felhasználónak ki kell választania az egyik igazolási módszert (X. 509, TPM és SAS-kulcs) **2.** Az igazolási módszertől függően a felhasználónak a megfelelő műveleteket kell elvégeznie, például **a)** X. 509 tanúsítvány feltöltését a AICS felügyelt DPS b hatókörbe, a sas **-** kulcs vagy a hátirat kulcsának megvalósítása az eszközön |
| **Erőforrások**           | [Eszközök kiépítési szolgáltatásának áttekintése](../iot-dps/about-iot-dps.md) |

**[Ha megvalósítva] Felhőből eszközre: a teszt célja, hogy az üzeneteket a felhőből az eszközökre lehessen elküldeni**                                                              

| **Név**                | AzureCertified. C2D                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Már elérhető                                            |
| **A következőkre vonatkozik**          | Levél eszköz/peremhálózati eszköz                                                   |
| **OS**                  | Független                                                     |
| **Érvényesítés típusa**     | Automatizált                                                    |
| **Érvényesítés**          | Az eszköznek képesnek kell lennie arra, hogy a felhőből üzeneteket IoT Hub. A Microsoft biztosítja a [portál munkafolyamatát](https://certify.azure.com) a tesztek végrehajtásához. Felhőből eszközre (ha megvalósítva): **1.** Ellenőrzi, hogy az eszköz fogadhat-e üzenetet IoT Hub **2.** A AICS véletlenszerű üzenetet küld, és érvényesíti az eszközről az üzenet NYUGTÁján keresztül.  |
| **Erőforrások**           | **a)** a [hitelesítési lépések](./overview.md) (az összes további erőforrással) **b)** a [felhőből üzeneteket küldenek az eszközökről IoT hub](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Ha megvalósítva] Közvetlen metódusok: a teszt célja annak biztosítása, hogy az eszközök a IoT Hub és a közvetlen metódusok támogatásával működnek**

| **Név**                | AzureCertified.DirectMethods                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Már elérhető                                            |
| **A következőkre vonatkozik**          | Levél eszköz/peremhálózati eszköz                                                   |
| **OS**                  | Független                                                     |
| **Érvényesítés típusa**     | Automatizált                                                    |
| **Érvényesítés**          | Az eszköznek képesnek kell lennie a IoT Hubtól érkező parancsok kérésének fogadására és megválaszolására. A Microsoft biztosítja a [portál munkafolyamatát](https://certify.azure.com) a tesztek végrehajtásához. Közvetlen metódusok (ha megvalósítva) **1.** A felhasználónak meg kell adnia a közvetlen metódus adattartalmát. **2.** A AICS ellenőrzi, hogy a rendszer elküldi-e a megadott adattartalom-kérést az eszköz által fogadott hub és ACK üzenetből |
| **Erőforrások**           | **a)** a [hitelesítési lépések](./overview.md) (az összes további erőforrással) **b)** [a közvetlen metódusok megismerése IoT hub](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Ha megvalósítva] Eszköz Twin tulajdonsága: a teszt célja, hogy az telemetria-t küldő eszközök IoT Hub és a IoT Hub-képességek, például a közvetlen metódusok és az eszközök Twin tulajdonságait is támogatják.**

| **Név**                                  | AzureCertified.DeviceTwin                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása**                   | Már elérhető                                            |
| **A következőkre vonatkozik**                            | Levél eszköz/peremhálózati eszköz                                                   |
| **OS**                                    | Független                                                     |
| **Érvényesítés típusa**                       | Automatizált                                                       |
| **Érvényesítés**                            | Az eszköznek minden telemetria-sémát el kell küldenie IoT Hubba. A Microsoft biztosítja a [portál munkafolyamatát](https://certify.azure.com) a tesztek végrehajtásához. Eszköz Twin tulajdonsága (ha megvalósítva) **1.** A AICS érvényesíti az írható/olvasható tulajdonságot a következő eszközön: Twin JSON **2.** A felhasználónak meg kell adnia a **3.** módosítandó JSON-adattartalmat. A AICS ellenőrzi az eszköz által fogadott IoT Hub és ACK üzenetből küldött megadott kívánt tulajdonságokat. |
| **Erőforrások**                             | **a) a** [minősítési lépések](./overview.md) (az összes további erőforrással) **b)** [a Device Twins használata IoT hub](../iot-hub/iot-hub-devguide-device-twins.md) |
