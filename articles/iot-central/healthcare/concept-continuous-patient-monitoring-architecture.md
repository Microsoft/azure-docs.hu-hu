---
title: Folyamatos fekvőbeteg-figyelési architektúra az Azure IoT Centralban | Microsoft Docs
description: Oktatóanyag – Ismerje meg a folyamatos beteg monitorozási megoldás architektúráját.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 093e069fa7a3eab06c61f796421a3253d9b92cb1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727498"
---
# <a name="continuous-patient-monitoring-architecture"></a>A folyamatos betegfigyelés architektúrája

Ez a cikk a **folyamatos beteg monitorozási** alkalmazás sablonjában létrehozott megoldások architektúráját ismerteti:

A folyamatos fekvőbeteg-figyelési megoldások a megadott alkalmazás-sablonnal, valamint az alább ismertetett architektúra használatával használhatók.

:::image type="content" source="media/cpm-architecture.png" alt-text="A folyamatos betegfigyelés architektúrája":::

## <a name="details"></a>Részletek

Ez a szakasz részletesebben ismerteti az architektúra-diagram egyes részeit:

### <a name="bluetooth-low-energy-ble-medical-devices"></a>Bluetooth alacsony energiafogyasztású (egypontos) orvostechnikai eszközök

Az egészségügyi IoT-megoldásokban használt számos orvosi eszköz a készülék. Ezek az eszközök nem tudnak közvetlenül kommunikálni a felhővel, és átjáróval kell használniuk az adatcserét a felhőalapú megoldással. Ez az architektúra egy mobiltelefon-alkalmazást használ átjáróként.

### <a name="mobile-phone-gateway"></a>Mobile Phone-átjáró

A mobiltelefonos alkalmazás elsődleges funkciója az, hogy az orvostechnikai eszközökből származó adatokat gyűjtsön, és IoT Central kommunikáljon. Az alkalmazás a betegeket az eszköz beállításán keresztül is irányítja, és lehetővé teszi a személyes állapotadatok megtekintését. Más megoldások is használhatnak táblaszámítógép-átjárót vagy statikus átjárót egy kórházi helyiségben. Az Android és az iOS rendszerhez készült nyílt forráskódú minta Mobile-alkalmazás kiindulási pontként használható az alkalmazások fejlesztéséhez. További információért lásd a [IoT Central folyamatos beteg monitorozása Mobile alkalmazást](/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>Exportálás az Azure API-ba FHIR&reg;

Az Azure IoT Central HIPAA-kompatibilis és HITRUST &reg; tanúsítvánnyal rendelkezik. A [FHIR készült Azure API](../../healthcare-apis/overview.md)használatával más szolgáltatásokhoz is küldhet beteg-egészségügyi adataikat. A FHIR készült Azure API egy szabványon alapuló API a klinikai egészségügyi adatkezeléshez. A [FHIR készült Azure IoT-összekötő](../../healthcare-apis/iot-fhir-portal-quickstart.md) lehetővé teszi, hogy a FHIR Azure API-ját folyamatos adatexportálási célként használja IoT Central.

### <a name="machine-learning"></a>Gépi tanulás

A gépi tanulási modellek segítségével FHIR-adataival elemzési és támogatási döntéseket hozhat a Care csapata számára. További információt az [Azure Machine learning dokumentációjában](../../machine-learning/index.yml)talál.

### <a name="provider-dashboard"></a>Szolgáltatói irányítópult

Az Azure API-val FHIR-adatokkal egy beteg-elemzési irányítópultot hozhat létre, vagy integrálhatja azt közvetlenül egy Care Teams által használt elektronikus orvosi rekordba. A Care Teams az irányítópultot használva segíti a betegeket, és azonosíthatja a korai előrejelző jeleinek romlását. További tudnivalókat a Power BI- [szolgáltatói irányítópult létrehozása](tutorial-health-data-triage.md) című oktatóanyagban talál.

## <a name="next-steps"></a>Következő lépések

A következő lépés az, hogy [megtudja, hogyan helyezhet üzembe egy folyamatos beteg monitorozási alkalmazás sablonját](tutorial-continuous-patient-monitoring.md).