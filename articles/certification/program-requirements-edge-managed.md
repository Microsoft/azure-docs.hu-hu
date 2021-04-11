---
title: Peremhálózati felügyelt minősítési követelmények
description: Az Edge által felügyelt minősítési program követelményei
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Managed Certification Requirements
ms.service: certification
ms.openlocfilehash: 744f81d0544a765f60793ece1aa539c6c37e39cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969263"
---
# <a name="azure-certification-edge-managed"></a>Azure minősítési peremhálózati felügyelt 

Ez a dokumentum felvázolja az eszközre jellemző képességeket, amelyek az Azure Certified-eszköz katalógusában lesznek megjelenítve. A képesség olyan egyedi Device attribútum, amely az eszköz leírására szolgál. 

## <a name="program-purpose"></a>Program célja

Edge által felügyelt minősítés, egy növekményes tanúsítvány, amely az Azure Certified minősítésű eszköz minősítésén túl van. Az Edge által felügyelt eszköz az Azure-beli csatlakoztatott eszközökre vonatkozó eszközkezelés-szabványokra koncentrál, és ellenőrzi a modul üzembe helyezésének és felügyeletének IoT Edge futtatókörnyezeti kompatibilitását. (Korábban ez a program IoT Edge minősítési programként lett meghatározva.) 

Az Edge által felügyelt tanúsítvány ellenőrzi IoT Edge futtatókörnyezet kompatibilitását a modulok üzembe helyezéséhez és kezeléséhez. Ez a program megbízhatóságot biztosít az Azure Connected IoT-eszközök felügyeletében.

## <a name="requirements"></a>Követelmények

Az Edge által felügyelt minősítés megköveteli, hogy az [Azure Certified eszköz alapprogramjának](.\program-requirements-azure-certified-device.md)minden követelménye megfeleljen.

**DPS: a teszt célja, hogy ellenőrizze az eszköz megvalósítását, és támogassa a IoT Hub Device Provisioning Service a három igazolási módszer egyikével.**

| **Név**                | AzureReady. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Ignite (előzetes verzió)                                                |
| **A következőkre vonatkozik**          | Bármely eszköz                                      |
| **OS**                  | Független                                                     |
| **Érvényesítés típusa**     | Automatizált                                                    |
| **Érvényesítés**          | A AICS ellenőrzi az eszköz kódját támogató DPS-t. **1.** A felhasználónak ki kell választania az igazolási módszerek egyikét (X. 509, TPM és SAS-kulcs). **2.** Az igazolási módszertől függően a felhasználónak a megfelelő műveleteket kell elvégeznie, például **a)** az X. 509 tanúsítvány feltöltése a AICS FELÜGYELt DPS-hatókörbe **b)** az SAS-kulcs vagy a hátirat kulcs bevezetését az eszközre. **3.** Ezután a felhasználó a "kapcsolódás" gombra kattintva csatlakozhat a AICS Managed IoT Hubhoz a DPS használatával                                                    |
| **Erőforrások**           |                                                      |
| **Azure ajánlott:**     | N/A                                                    |

## <a name="iot-edge"></a>IoT Edge

**Az Edge-futtatókörnyezet létezik: a teszt célja annak biztosítása, hogy az eszköz IoT Edge futtatókörnyezetet ($edgehub és $edgeagent) megfelelően működjön.**

| **Név**                | EdgeManaged.EdgeRT                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Már elérhető                                                          |
| **A következőkre vonatkozik**          | IoT Edge-eszköz                                                   |
| **OS**                  | [Tier1 és szint operációs rendszer](../iot-edge/support.md)                                                     |
| **Érvényesítés típusa**     | Automatizált                                                    |
| **Érvényesítés**          | A AICS érvényesíti a telepített IoT Edge RT üzembe helyezési képességét. **1.** A felhasználónak meg kell adnia adott operációs rendszert (az operációs rendszer nem szerepel a Tier1/2 listán) **2.** A AICS létrehozza a config. YAML és üzembe helyezi a kanonikus [szimulált Temp Sensor Edge-modul](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-iot.simulated-temperature-sensor?tab=Overview) **3.** A AICS ellenőrzi, hogy a Docker-kompatibilis tároló alrendszer (Moby) telepítve van-e a **4.** eszközön. A teszt eredménye a szimulált Temp Sensor Edge-modul és a Docker-kompatibilis tároló alrendszer működésének sikeres üzembe helyezése alapján van meghatározva.                                                    |
| **Erőforrások**           | **a)** [AICS blog](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [hitelesítési lépései](./overview.md) (az összes további erőforrással), **c)** [követelmények](./program-requirements-azure-certified-device.md) |
| **Azure ajánlott:**     | N/A                                                    |

### <a name="capability-template"></a>Képesség sablonja:

**IoT Edge egyszerű beállítás: a teszt célja annak biztosítása, hogy IoT Edge eszköz könnyen beállítható és ellenőrizhető legyen, IoT Edge futtatókörnyezet a fizikai eszköz ellenőrzésekor előre telepítve van-e**

| **Név**                | EdgeManaged.PhysicalDevice                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Cél rendelkezésre állása** | Most elérhető (jelenleg a COVID-19 miatt)                                            |
| **A következőkre vonatkozik**          | IoT Edge-eszköz                                                   |
| **OS**                  | [Tier1 és szint operációs rendszer](../iot-edge/support.md)                                                     |
| **Érvényesítés típusa**     | Kézi/tesztkörnyezet ellenőrizve                                                    |
| **Érvényesítés**          | Az OEM-nek a fizikai eszközt IoT-felügyeletre (HCL) kell szállítani. A HCL manuális ellenőrzést végez a fizikai eszközön a következő ellenőrzés céljából: **1.** A EdgeRT a következőt használja: Moby alrendszer (engedélyezett újraelosztás verziója). Nem Docker **2.** Válassza ki a legújabb Edge-modult az Edge üzembe helyezési képességének ellenőrzéséhez.                                                     |
| **Erőforrások**           | **a)** [AICS blog](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [](./overview.md) [követelményeinek](./program-requirements-azure-certified-device.md) , **c** |
| **Azure ajánlott:**     | N/A                                                    |