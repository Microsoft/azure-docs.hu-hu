---
title: Az FHIR-hez készült Azure IoT-összekötő (előzetes verzió) metrikáinak exportálása a diagnosztikai beállításokon keresztül
description: Ez a cikk bemutatja, hogyan exportálhatja az Azure IoT Connectort a FHIR (előzetes verzió) Mérőszámokhoz a diagnosztikai beállítások segítségével
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 3b350de81e07f30f4f0f105c4e833a3b4450e558
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019413"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>Az FHIR-hez készült Azure IoT-összekötő (előzetes verzió) metrikáinak exportálása a diagnosztikai beállításokon keresztül

Ebből a cikkből megtudhatja, hogyan exportálhatja az Azure IoT Connectort a gyors egészségügyi együttműködési erőforrások (FHIR&#174;) * mérőszámok naplóiba. A metrikák naplózását lehetővé tevő szolgáltatás a Azure Portal [**diagnosztikai beállításai**](../../azure-monitor/essentials/diagnostic-settings.md) . 

> [!TIP]
> A naplózási naplózás beállításához kövesse az [Azure API diagnosztikai naplózásának engedélyezése az FHIR és az Azure IoT connectorhoz](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) című témakör útmutatását.

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>Metrikák naplózásának engedélyezése a FHIR készült Azure IoT-összekötőhöz (előzetes verzió)
1. A FHIR Azure IoT-összekötő metrikáinak naplózásának engedélyezéséhez válassza ki az Azure API-t a FHIR szolgáltatáshoz a Azure Portal 

2. Navigáljon a **diagnosztikai beállításokhoz** 

3. Válassza a **+ diagnosztikai beállítás hozzáadása** lehetőséget

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Adjon meg egy nevet a **diagnosztikai beállítások neve** párbeszédpanelen.

5. Válassza ki a diagnosztikai naplók eléréséhez használni kívánt módszert:

    1. Archiválás vagy manuális ellenőrzés a **Storage-fiókba** . A használni kívánt Storage-fióknak már létre kell hoznia.
    2. **Adatfolyam küldése az Event hub** -nak egy harmadik féltől származó szolgáltatás vagy egyéni analitikai megoldás betöltéséhez. Ennek a lépésnek a konfigurálásához létre kell hoznia egy Event hub-névteret és egy Event hub-házirendet.
    3. **Stream a Azure Monitor log Analytics** munkaterületére. Ennek a lehetőségnek a kiválasztásához létre kell hoznia a naplók Analytics-munkaterületet.

6. A FHIR Azure IoT-összekötőhöz tartozó **hibák, forgalom és késés** kiválasztása.  Válassza ki a FHIR Azure API-hoz rögzíteni kívánt további metrikai kategóriákat.

7. Válassza a **Mentés** lehetőséget

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Akár 15 percet is igénybe vehet, hogy az első mérőszámok naplói megjelenjenek az Ön által választott adattárban.  
 
A diagnosztikai naplók használatával kapcsolatos további információkért tekintse meg az [Azure Resource log dokumentációját](../../azure-monitor/essentials/platform-logs-overview.md) .

## <a name="conclusion"></a>Összegzés 
A metrikák naplóihoz való hozzáférés elengedhetetlen a figyeléshez és a hibaelhárításhoz.  A FHIR készült Azure IoT Connector lehetővé teszi ezeket a műveleteket a metrikák naplóin keresztül. 

## <a name="next-steps"></a>Következő lépések

Tekintse meg a FHIR készült Azure IoT-összekötővel kapcsolatos gyakori kérdéseket.

>[!div class="nextstepaction"]
>[Azure IoT-összekötő FHIR – gyakori kérdések](fhir-faq.md)

* A Azure Portal a FHIR készült Azure IoT Connector a IoT Connector (előzetes verzió) néven ismert. A FHIR a HL7 bejegyzett védjegye, és a HL7 engedélyével van használatban.