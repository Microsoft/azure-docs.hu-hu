---
title: IoT Plug and Play minősítési követelmények
description: IoT Plug and Play minősítési programra vonatkozó követelmények
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: IoT Plug and Play Certification Requirements
ms.service: certification
ms.openlocfilehash: b26fab6f8b92e3cb996f545f1f6201d32b1eaced
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310512"
---
# <a name="iot-plug-and-play-certification-requirements"></a>IoT Plug and Play minősítési követelmények

Ez a dokumentum az Azure IoT-eszközök katalógusában szereplő, az eszközre jellemző képességeket ismerteti. A képesség olyan egyedi eszköz attribútum, amely szoftveres implementáció vagy szoftver-és hardver-implementáció kombinációja lehet.

## <a name="program-purpose"></a>Program célja

A IoT Plug and Play előzetes verziója lehetővé teszi a megoldás-építők számára, hogy manuális konfiguráció nélkül integrálják az intelligens eszközöket a megoldásaikkal. A IoT-Plug and Play középpontjában a egy eszköz modellje, amelyet az eszköz használ a képességeinek egy IoT Plug and Play-kompatibilis alkalmazásba való reklámozásához. Ez a modell elemek csoportjaként van strukturálva: telemetria, Properties és commands.

A IoT Plug and Play minősítésének ígérete a következők:

1.  A definiált eszközök és felületek megfelelnek a [digitális kettős definíciós nyelvnek](https://github.com/Azure/opendigitaltwins-dtdl) .  
2.  Az azonosító hatókör tulajdonjogának biztonságos kiépítés és egyszerű átvitele az eszközök kiépítési szolgáltatásaiban
3.  Egyszerű integráció az Azure IoT-alapú megoldásokkal a [digitális Twin API](../iot-pnp/concepts-digital-twin.md)  -k használatával: Azure IoT hub és Azure IoT Central
4.  Érvényesített termék igazsága hitelesített eszközökön

## <a name="requirements"></a>Követelmények

**Szükséges Eszközről a felhőbe: a teszt célja annak biztosítása, hogy az telemetria küldő eszközök IoT Hub**

| **Név**                | IoTPnP. D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Már elérhető                                                |
| **A következőkre vonatkozik**          | Levél eszköz/peremhálózati eszköz                                      |
| **OS**                  | Független                                                     |
| **Érvényesítés típusa**     | Automatizált                                                    |
| **Érvényesítés**          | Az eszköznek minden telemetria-sémát el kell küldenie IoT Hubba. A Microsoft biztosítja a [portál munkafolyamatát](https://certify.azure.com) a tesztek végrehajtásához. Eszközről a felhőbe (kötelező): **1.** Ellenőrzi, hogy az eszköz küldhet-e üzenetet az AICS által felügyelt IoT Hub **2.** A felhasználónak meg kell adnia az üzenetek számát és gyakoriságát. **3.** A AICS ellenőrzi, hogy a telemetria fogadta-e a hub-példány |
| **Erőforrások**           | [Minősítési lépések](./overview.md) (az összes további erőforrással rendelkezik) |

**Szükséges DPS: a teszt célja, hogy ellenőrizze az eszköz megvalósítását, és támogassa a IoT Hub Device Provisioning Service a három igazolási módszer egyikével.**

| **Név**                | IoTPnP. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Már elérhető                                                |
| **A következőkre vonatkozik**          | Bármely eszköz                                                   |
| **OS**                  | Független                                                     |
| **Érvényesítés típusa**     | Automatizált                                                    |
| **Érvényesítés**          | Az eszköznek a DPS-azonosító hatókör tulajdonjogának egyszerű átvitelét kell megvalósítani anélkül, hogy újra kellene fordítania a beágyazott kódot. A Microsoft biztosítja a [portál munkafolyamatot](https://certify.azure.com) a tesztek végrehajtásához annak ellenőrzéséhez, hogy az eszköz támogatja-e a DPS 1-et **.** A felhasználónak ki kell választania az egyik igazolási módszert (X. 509, TPM és SAS-kulcs) **2.** Az igazolási módszertől függően a felhasználónak a megfelelő műveleteket kell elvégeznie, például **a)** X. 509 tanúsítvány feltöltését a AICS felügyelt DPS b hatókörbe, a sas **-** kulcs vagy a hátirat kulcsának megvalósítása az eszközön |
| **Erőforrások**           | **a)** [Device kiépítési szolgáltatás áttekintése](../iot-dps/about-iot-dps.md), **b)** [minta konfigurációs fájl a DPS-azonosító hatókör-átviteléhez](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview-pnp/serializer/samples/devicetwin_simplesample) |

**Szükséges DTDL v2: a teszt célja, hogy a definiált eszközök és felületek megfeleljenek a digitális Twins Definition Language v2-nek.**                                                              

| **Név**                | IoTPnP.DTDL                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Már elérhető                                                |
| **A következőkre vonatkozik**          | Bármely eszköz                                                   |
| **OS**                  | Független                                                     |
| **Érvényesítés típusa**     | Automatizált                                                    |
| **Érvényesítés**          | A [portál munkafolyamat](https://certify.azure.com) -ellenőrzése: **1.** Modell AZONOSÍTÓjának bejelentése, valamint annak biztosítása, hogy az eszköz csatlakoztatva legyen a MQTT vagy a MQTT-en keresztül a WebSockets 2-es protokollon keresztül **.** A modellek megfelelnek a DTDL v2 3 verziójának **.** A telemetria, a tulajdonságok és a parancsok megfelelően vannak implementálva, és a IoT Hub digitális Twin és az eszközön található Twin eszközök között működnek |
| **Erőforrások**           | [Nyilvános előzetes frissítés frissítései](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**Szükséges Az eszközök modelljei a nyilvános modell adattárában vannak közzétéve**

| **Név**                | IoTPnP.ModelRepo                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Már elérhető                                                |
| **A következőkre vonatkozik**          | Bármely eszköz                                                   |
| **OS**                  | Független                                                     |
| **Érvényesítés típusa**     | Automatizált                                                    |
| **Érvényesítés**          | Az összes eszköz modelljét közzé kell tenni a nyilvános tárházban. Az eszközök modelljeit az **1.** nyilvános adattárban elérhető modelleken keresztül oldják fel. A tanúsítvány elküldése előtt a felhasználónak manuálisan kell közzétennie a modelleket a nyilvános adattárba. **2.** Vegye figyelembe, hogy a modellek közzétételekor nem változtathatók meg. Javasoljuk, hogy csak akkor tegye közzé a közzétételt, ha a modellek és a beágyazott eszközök kódja véglegesítve van. * 1 * 1 * a felhasználónak kapcsolatba kell lépnie a Microsoft ügyfélszolgálatával, hogy visszavonja a modelleket, amint közzé lett téve a **3-as** A [portál munkafolyamata](https://certify.azure.com) ellenőrzi a nyilvános tárház modelljeinek létezését, ha az eszköz a tanúsítási szolgáltatáshoz csatlakozik |
| **Erőforrások**           | [Modelladattár](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**Szükséges Fizikai eszköz ellenőrzése a GSG használatával**

| **Név**                                  | IoTPnP.Physicaldevice                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása**                   | Már elérhető                                                |
| **A következőkre vonatkozik**                            | Bármely eszköz                                                   |
| **OS**                                    | Független                                                     |
| **Érvényesítés típusa**                       | Kézi                                                       |
| **Érvényesítés**                            | A partnereknek kapcsolatba kell lépniük a Microsoft Contact ( [iotcert@microsoft.com](mailto:iotcert@microsoft.com) ) megoldással, hogy a fizikai eszközön további érvényesítéseket végezzenek. A COVID-19 helyzet miatt a fizikai eszközök ellenőrzésének különféle módjait vizsgáljuk meg anélkül, hogy az eszközt a Microsoft felé szállítjuk. |
| **Erőforrások**                             | A részletek később érhetők el                                 |
| **Azure ajánlott**       | N/A    |

**[Ha megvalósítva] Eszköz-információs felület: a teszt célja, hogy az eszköz információs felületének ellenőrzése megfelelően legyen implementálva az eszköz kódjában.**

| **Név**                | IoTPnP.DeviceInfoInterface                                   |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Már elérhető                                                |
| **A következőkre vonatkozik**          | Bármely eszköz                                                   |
| **OS**                  | Független                                                     |
| **Érvényesítés típusa**     | Automatizált                                                    |
| **Érvényesítés**          | A [portál munkafolyamata](https://certify.azure.com) ellenőrzi, hogy az eszköz kódja implementálja-e az eszköz info Interface **1.** Ellenőrzi, hogy az eszköz kódja kibocsátja-e az értékeket IoT Hub 2 értékre **.** Ellenőrzi, hogy az interfész implementálva van-e a DCM-ben (ez a megvalósítás a DTDL v2-ben változik) **3.** Az ellenőrzések tulajdonságai nem írhatók (csak olvasható) **4.** Ellenőrzi, hogy a séma típusa sztring és/vagy hosszú, és nem null értékű-e. |
| **Erőforrások**           | [Microsoft által definiált felület](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |
| **Azure ajánlott**  | N/A                                                          |

**[Ha megvalósítva] Felhőből eszközre: a teszt célja, hogy az üzeneteket a felhőből az eszközökre lehessen elküldeni**

| **Név**                | IoTPnP. C2D                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Már elérhető                                                |
| **A következőkre vonatkozik**          | Levél eszköz/peremhálózati eszköz                                      |
| **OS**                  | Független                                                     |
| **Érvényesítés típusa**     | Automatizált                                                    |
| **Érvényesítés**          | Az eszköznek képesnek kell lennie arra, hogy a felhőből üzeneteket IoT Hub. A Microsoft biztosítja a [portál munkafolyamatát](https://certify.azure.com) a tesztek végrehajtásához. Felhőből eszközre (ha megvalósítva): **1.** Ellenőrzi, hogy az eszköz fogadhat-e üzenetet IoT Hub **2.** A AICS véletlenszerű üzenetet küld, és érvényesíti az eszközről az üzenet NYUGTÁján keresztül. |
| **Erőforrások**           | **1.** a [minősítés lépései](./overview.md) (a további erőforrásokkal rendelkezik), **2.** [Felhő küldése az eszköz üzeneteinek IoT Hub](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Ha megvalósítva] Közvetlen metódusok: a teszt célja annak biztosítása, hogy az eszközök a IoT Hub és a közvetlen metódusok támogatásával működnek**

| **Név**                | IoTPnP.DirectMethods                                     |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Már elérhető                                                |
| **A következőkre vonatkozik**          | Levél eszköz/peremhálózati eszköz                                      |
| **OS**                  | Független                                                     |
| **Érvényesítés típusa**     | Automatizált                                                    |
| **Érvényesítés**          | Az eszköznek képesnek kell lennie a IoT Hubtól érkező parancsok kérésének fogadására és megválaszolására. A Microsoft biztosítja a [portál munkafolyamatát](https://certify.azure.com) a tesztek végrehajtásához. Közvetlen metódusok (ha megvalósítva): **1.** A felhasználónak meg kell adnia a közvetlen metódus adattartalmát. **2.** A AICS ellenőrzi, hogy a rendszer elküldi-e a megadott adattartalom-kérést az eszköz által fogadott hub és ACK üzenetből |
| **Erőforrások**           | **1.** a [minősítés lépései](./overview.md) (a további erőforrásokkal rendelkezik), **2.** [A közvetlen módszerek megismerése IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Ha megvalósítva] Eszköz Twin tulajdonsága: a teszt célja, hogy az telemetria-t küldő eszközök IoT Hub és a IoT Hub-képességek, például a közvetlen metódusok és az eszközök Twin tulajdonságait is támogatják.**

| **Név**                | IoTPnP.DeviceTwin                                        |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Már elérhető                                                |
| **A következőkre vonatkozik**          | Levél eszköz/peremhálózati eszköz                                      |
| **OS**                  | Független                                                     |
| **Érvényesítés típusa**     | Automatizált                                                    |
| **Érvényesítés**          | Az eszköznek minden telemetria-sémát el kell küldenie IoT Hubba. A Microsoft biztosítja a [portál munkafolyamatát](https://certify.azure.com) a tesztek végrehajtásához. Eszköz Twin tulajdonsága (ha megvalósítva): **1.** A AICS érvényesíti az írható/olvasható tulajdonságot a következő eszközön: Twin JSON **2.** A felhasználónak meg kell adnia a **3.** módosítandó JSON-adattartalmat. A AICS ellenőrzi az eszköz által fogadott IoT Hub és ACK üzenetből küldött megadott kívánt tulajdonságokat. |
| **Erőforrások**           | **1.** a [minősítés lépései](./overview.md) (a további erőforrásokkal rendelkezik), **2.** [Az eszközök ikrek használata IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) |
