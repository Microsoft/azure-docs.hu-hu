---
title: Az FHIR-hez készült Azure IoT-összekötő (előzetes verzió) metrikáinak megjelenítése és konfigurálása
description: Ez a cikk bemutatja, hogyan jelenítheti meg és konfigurálhatja az Azure IoT Connectort a FHIR (előzetes verzió) mérőszámokhoz.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 831c6df3f50bfff9b411660d9efc4cd78ee5e8d9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019383"
---
# <a name="display-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Az FHIR-hez készült Azure IoT-összekötő (előzetes verzió) metrikáinak megjelenítése és konfigurálása 

Ebből a cikkből megtudhatja, hogyan jelenítheti meg és konfigurálhatja az Azure IoT Connectort a gyors egészségügyi együttműködési erőforrások (FHIR&#174;) * mérőszámok számára.

> [!TIP]
> A metrikai adatok exportálásának beállításához kövesse az [Azure IoT-összekötő exportálása a FHIR (előzetes verzió) mérőszámait diagnosztikai beállításokon keresztül](iot-metrics-diagnostics-export.md)című témakör útmutatását.

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>Metrikák megjelenítése az Azure IoT-összekötőhöz a FHIR (előzetes verzió)

1. Jelentkezzen be a Azure Portalba, majd válassza ki az Azure API-t a FHIR szolgáltatáshoz. 

2. A bal oldali ablaktáblán válassza a **metrikák** lehetőséget. 

3. Válassza az **IoT-összekötő** fület.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="Képernyőkép az &quot;IoT-összekötő&quot; panelről, amely a bejövő és a normalizált üzenetek számát jeleníti meg." lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. A metrikák megtekintéséhez válasszon ki egy IoT-összekötőt. Például négy IoT-összekötő (*1. összekötő*, *2*. összekötő és így tovább) társítva van ehhez az Azure API for FHIR szolgáltatáshoz.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="Képernyőkép az &quot;IoT-összekötő&quot; panelről, amely a IoT-összekötő 1., 2., 3. és 4. lapjait jeleníti meg." lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. Válassza ki a megjeleníteni kívánt IoT-összekötő metrikájának időtartamát (például **1 óra**, **24 óra**, **7 nap** vagy **Egyéni**). Az **Egyéni** lapon megadhatja az IoT-összekötő metrikáinak megjelenítéséhez megadott idő/dátum kombinációkat.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="Képernyőkép az &quot;IoT-összekötő&quot; panelről, amely egy &quot;1 órás&quot; időszakos grafikont jelenít meg az 1-es összekötőhöz." lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>Metrikus típusok az Azure IoT-összekötőhöz a FHIR (előzetes verzió) 

> [!TIP]
> Ha többet szeretne megtudni a FHIR készült Azure IoT Connector adatforgalmáról, tekintse meg az [Azure IoT Connector for FHIR (előzetes verzió) adatfolyamát](iot-data-flow.md) és [Az Azure IOT Connector for FHIR (előzetes verzió) hibaelhárítási útmutatóját](iot-troubleshoot-guide.md) a hibaüzenetekkel és javításokkal kapcsolatos további információkért.

A megjeleníthető IoT-összekötő metrikái a következő táblázatban láthatók:

|Metrika típusa|Metrika célja| 
|-----------|--------------|
|Bejövő üzenetek száma|Megjeleníti a fogadott nyers bejövő üzenetek (például az eszköz eseményei) számát.|
|Normalizált üzenetek száma|A normalizált üzenetek számát jeleníti meg.|
|Üzenetszöveg-csoportok száma|Azon csoportok számát jeleníti meg, amelyek a kijelölt időablakban összesítve vannak.|
|Átlagos normalizált szakasz késése|A normalizált szakasz átlagos késését jeleníti meg. A normalizált szakasz a nyers bejövő üzenetekre vonatkozó normalizálás elvégzését végzi.|
|Csoportosítási szakasz átlagos késése|Megjeleníti a csoport fázisának átlagos késését. A csoportkör a normalizált üzenetek pufferelését, összesítését és csoportosítását végzi.| 
|Hibák száma összesen|A hibák összesített számát jeleníti meg.| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>A FHIR (előzetes verzió) metrikáinak Azure IoT-összekötőre való összpontosítása és konfigurálása

Ebben a példában a **Bejövő üzenetek** metrikájának számát fogjuk összpontosítani.

1. Válassza ki azt az időpontot, amelyre összpontosítani kíván.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="Képernyőkép a &quot;beérkező üzenetek száma&quot; metrika panelről, amely egyetlen időpontot jelöl ki a vonalas diagramon." lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. A **beérkező üzenetek száma** ablaktáblán tovább testreszabhatja a metrikát a **metrika hozzáadása**, a **szűrő hozzáadása** vagy a **felosztás alkalmazása** lehetőség kiválasztásával. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="Képernyőfelvétel a &quot;beérkező üzenetek száma&quot; metrika panelről, a &quot;metrika hozzáadása&quot;, a &quot;szűrő hozzáadása&quot; és a &quot;felosztás alkalmazása&quot; gomb kiemelése." lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Összegzés 
A figyeléshez és a hibaelhárításhoz elengedhetetlen, hogy az adatközpont metrikái hozzáférjenek. A FHIR készült Azure IoT Connector a mérőszámokon keresztül segíti ezeket a műveleteket. 

## <a name="next-steps"></a>Következő lépések

Válaszok az Azure IoT Connector FHIR szolgáltatással kapcsolatos gyakori kérdéseire.

>[!div class="nextstepaction"]
>[Azure IoT-összekötő FHIR – gyakori kérdések](fhir-faq.md)

* A Azure Portal a FHIR készült Azure IoT Connector a IoT Connector (előzetes verzió) néven ismert. A FHIR a HL7 bejegyzett védjegye, és a HL7 engedélyével van használatban. 
