---
title: Kapcsolódó GitHub-projektek a FHIR készült Azure API-hoz
description: Az összes nyílt forráskódú (GitHub) tárház listázása a FHIR készült Azure API-hoz.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103548018"
---
# <a name="related-github-projects"></a>Kapcsolódó GitHub-projektek

Számos nyílt forráskódú projekt található a GitHubon, amely a forráskódot és útmutatást nyújtja a szolgáltatások különféle használatra való telepítéséhez. Mindig szívesen fogadjuk a GitHub-adattárakat a funkciók és termékek megismeréséhez és kipróbálásához. 

## <a name="fhir-server"></a>FHIR-kiszolgáló
* [Microsoft/fhir-Server](https://github.com/microsoft/fhir-server/): nyílt forráskódú Fhir-kiszolgáló, amely a Fhir készült Azure API alapja.
* A legújabb kiadások megtekintéséhez tekintse meg a [kibocsátási megjegyzéseket](https://github.com/microsoft/fhir-server/releases)
* [Microsoft/fhir-Server-Samples](https://github.com/microsoft/fhir-server-samples): a minta környezet

## <a name="data-conversion--anonymization"></a>Adatátalakítási & névtelenítésével

#### <a name="fhir-converter"></a>FHIR-átalakító
* [Microsoft/FHIR – átalakító](https://github.com/microsoft/FHIR-Converter): az örökölt adatformátumok FHIR való fordítására szolgáló konvertáló segédprogram
* Integrálva van az Azure API-val a FHIR, valamint az Azure-hoz készült FHIR-kiszolgáló $convert-adatműveletek formájában
* Az OSS folyamatos fejlesztései és a FHIR-kiszolgálókhoz való folyamatos integráció
 
#### <a name="fhir-converter---vs-code-extension"></a>FHIR Converter – VS Code-bővítmény
* [Microsoft/FHIR-Tools-for-névtelenítésével](https://github.com/microsoft/FHIR-Tools-for-Anonymization): olyan eszközök összessége, amelyek segítenek az adatkezelésben (FHIR formátumban) névtelenítésével
* Integrálva van az Azure API-val a FHIR-hez, valamint az Azure-hoz készült FHIR-kiszolgáló "de azonosított exportálás" formájában.

#### <a name="fhir-tools-for-anonymization"></a>A névtelenítésével FHIR eszközei
* [Microsoft/vscode-azurehealthcareapis-Tools](https://github.com/microsoft/vscode-azurehealthcareapis-tools): egy vs Code-bővítmény, amely az Azure Healthcare API-kkal használható eszközök gyűjteményét tartalmazza
* Megjelent a Visual Studio Marketplace-en
* A FHIR-átalakítóban használandó folyékony sablonok létrehozásához használatos

## <a name="iot-connector"></a>IoT-összekötő

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integráció IoT Hub és IoT Central
* [Microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir): integráció a következővel: IoT Hub vagy IOT Central a fhir a normalizált adatnormalizálás és a fhir átalakításával
* Normalizálás: az eszközök adatainak általános formátumba való kibontása további feldolgozás céljából
* FHIR-konverzió: a normalizált és csoportosított adathalmazok FHIR vannak leképezve. A rendszer a konfigurált sablonok alapján hozza létre vagy frissíti az észrevételeket, valamint az eszközhöz és a pácienshez kapcsolódik.
* [A beszélgetési Térkép elkészítését segítő eszközök](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): a leképezési konfiguráció megjelenítése az eszköz bemeneti adatok normalizálása és a FHIR-erőforrásokra való átalakítása céljából. A fejlesztők ezzel az eszközzel szerkeszthetik és ellenőrizhetik a leképezéseket, az eszközök hozzárendelését és a FHIR-leképezést, és exportálhatók a Azure Portal a IoT-összekötőre való feltöltéshez.

#### <a name="healthkit-and-fhir-integration"></a>HealthKit és FHIR-integráció
* [Microsoft/Healthkit-on-fhir](https://github.com/microsoft/healthkit-on-fhir): egy Swift-könyvtár, amely automatizálja az Apple Healthkit-alapú adatexportálást egy Fhir-kiszolgálóra

 