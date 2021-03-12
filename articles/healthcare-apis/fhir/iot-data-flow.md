---
title: 'Fogalmak: az Azure IoT-összekötő FHIR (előzetes verzió) funkciójának adatáramlása az Azure API-hoz FHIR'
description: Ismerje meg az Azure IoT-összekötőt a FHIR (előzetes verzió) adatforgalmához. A FHIR-hez készült Azure IoT-összekötő (előzetes verzió) betölti, normalizálja, csoportosítja, átalakítja és megőrzi a IoMT-adatot az Azure API-hoz a FHIR-hoz.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: dc20d4cc54824fc65eba6669eab9a1c32d7ef9eb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018624"
---
# <a name="azure-iot-connector-for-fhir-preview-data-flow"></a>Az FHIR-hez készült Azure IoT-összekötő (előzetes verzió) adatfolyama

Ez a cikk áttekintést nyújt az Azure IoT Connector adatforgalmáról a gyors egészségügyi együttműködési erőforrások (FHIR&#174;) * számára. Megismerheti az Azure IoT-összekötő különböző adatfeldolgozási szakaszait, amelyek az FHIR FHIR-alapú [megfigyelési](https://www.hl7.org/fhir/observation.html) erőforrásokra alakítják át.

![Azure IoT-összekötő a FHIR-adatfolyamhoz](media/concepts-iot-data-flow/iot-connector-data-flow.png)

A fenti ábrán az Azure IoT Connector az FHIR-hez készült általános adatforgalmai láthatók. 

Az alábbi szakaszok az Azure IoT-összekötő által az FHIR-hez érkezett kérések különböző szakaszain keresztül haladnak végig.

## <a name="ingest"></a>Betöltés
A betöltés az első olyan szakasz, ahol az eszközök az Azure IoT-Összekötőbe érkeznek az FHIR. Az eszközre vonatkozó adatfeldolgozási végpontot egy [Azure Event hub](../../event-hubs/index.yml)tárolja. Az Azure Event hub platform támogatja a nagy léptékű és az átviteli sebességet, és másodpercenként több millió üzenet fogadását és feldolgozását is lehetővé teszi. Emellett lehetővé teszi az Azure IoT Connector számára, hogy a FHIR aszinkron módon használja az üzeneteket, így nem kell megvárnia az eszközök feldolgozásának idejét.

> [!NOTE]
> Az eszközre jelenleg a JSON az egyetlen támogatott formátum.

## <a name="normalize"></a>Normalizálni
A normalizálás a következő lépés, amelyben az eszközök adatai bekerülnek a fenti Azure Event hub-ból, és az eszköz-hozzárendelési sablonok használatával dolgozzák fel őket. Ez a leképezési folyamat az eszközbeállítások normalizált sémába való átalakítását eredményezi. 

A normalizálás folyamata nem csupán az adatfeldolgozást egyszerűsíti a későbbi fázisokban, de lehetővé teszi, hogy egy bemeneti üzenetet több normalizált üzenetbe is feldolgozzon. Egy eszköz például több létfontosságú jelet is küldhet a test hőmérsékletének, a pulzusszámának, a vérnyomás és a légzési aránynak egyetlen üzenetben. Ez a bemeneti üzenet négy különálló FHIR-erőforrást hoz létre. Az egyes erőforrások különböző létfontosságú aláírást képviselnek, és a bemeneti üzenet négy különböző normalizált üzenetbe lett kiosztva.

## <a name="group"></a>Group
A csoport a következő szakasz, ahol az előző fázisban elérhető normalizált üzenetek három különböző paraméterrel vannak csoportosítva: eszköz identitása, mérték típusa és időszaka.

Az eszköz identitása és a mérési típus csoportosítása lehetővé teszi a [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) -mérési típus használatát. Ez a típus tömör módot biztosít a FHIR-ben található eszköz mérési időalapú sorozatának ábrázolására. És az időszak azt szabályozza, hogy az Azure IoT-összekötő által az FHIR számára generált megfigyelési erőforrások milyen késéssel íródnak a FHIR készült Azure API-ban.

> [!NOTE]
> Az időszak értéke alapértelmezés szerint 15 percet vesz igénybe, és nem konfigurálható előzetes verzióra.

## <a name="transform"></a>Átalakítás
Az átalakítási szakaszban csoportosított normalizált üzenetek lesznek feldolgozva FHIR-leképezési sablonokkal. A sablon típusának megfelelő üzenetek a leképezésen keresztül megadott FHIR-alapú megfigyelési erőforrásokra kerülnek át.

Ezen a ponton az [eszköz](https://www.hl7.org/fhir/device.html) erőforrása, valamint a hozzá tartozó [beteg](https://www.hl7.org/fhir/patient.html) erőforrás is BEolvasható a FHIR-kiszolgálóról az üzenetben található eszköz-azonosító használatával. Ezeket az erőforrásokat a rendszer a létrehozott megfigyelési erőforrásra mutató hivatkozásként adja hozzá.

> [!NOTE]
> A rendszer a FHIR-kiszolgáló terhelésének csökkentése érdekében az összes identitást megkeresi a gyorsítótárban. Ha több betegtel rendelkező eszközök újrafelhasználását tervezi, javasoljuk, hogy hozzon létre egy olyan virtuális eszköz-erőforrást, amely kifejezetten a betegre vonatkozik, és küldjön virtuális eszköz-azonosítót az üzenet tartalma. A virtuális eszköz szülőként társítható a tényleges eszköz erőforráshoz.

Ha a FHIR-kiszolgálón nem található eszköz-erőforrás egy adott eszköz-azonosítóhoz, akkor az eredmény a `Resolution Type` létrehozáskor beállított értéktől függ. Ha a értékre `Lookup` van állítva, akkor a rendszer figyelmen kívül hagyja az adott üzenetet, és a folyamat továbbra is feldolgozza a többi bejövő üzenetet. Ha a értékre van állítva `Create` , az Azure IoT-összekötő a FHIR-hoz létrehoz egy operációs rendszer nélküli eszközt és a beteg erőforrásait a FHIR-kiszolgálón.  

## <a name="persist"></a>Fennállnak
Miután létrehozta a megfigyelési FHIR erőforrását az átalakítási fázisban, az erőforrás a FHIR Azure API-ba kerül. Ha a FHIR-erőforrás új, akkor a rendszer a kiszolgálón hozza létre. Ha a FHIR-erőforrás már létezik, frissülni fog.

## <a name="next-steps"></a>Következő lépések

A következő lépésre kattintva megtudhatja, hogyan hozhat létre eszköz-és FHIR-leképezési sablonokat.

>[!div class="nextstepaction"]
>[Azure IoT-összekötő FHIR-leképezési sablonokhoz](iot-mapping-templates.md)

* A Azure Portal a FHIR készült Azure IoT Connector a IoT Connector (előzetes verzió) néven ismert. A FHIR a HL7 bejegyzett védjegye, és a HL7 engedélyével van használatban. 