---
title: Azure Industrial IoT – áttekintés
description: Ez a cikk az ipari IoT áttekintését tartalmazza. Ez a cikk ismerteti az IIoT-ben található üzemirányítási és biztonsági összetevőket.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 940391d26b5a8455fef01c8094cd08e05ab51290
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787927"
---
# <a name="what-is-industrial-iot-iiot"></a>Mi az az ipari IoT (IIoT)?

A IIoT (ipari eszközök internetes hálózata) az ipari hatékonyságot a IoT alkalmazásával fokozza a feldolgozóipari iparágban.

![Ipari IOT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

## <a name="improve-industrial-efficiencies"></a>Az ipari hatékonyság javítása
Fejlessze működési termelékenységét és jövedelmezőségét az Azure ipari IoT. Csökkentse az adategységek helyszíni elérésének időigényes folyamatát. A felhőben működő ipari berendezések és eszközök csatlakoztatása és monitorozása – beleértve a gyári szinten már üzemelő gépeket is. Elemezheti a IoT adatait az elemzésekhez, amelyek segítenek a teljes hely teljesítményének növelésében.

## <a name="industrial-iot-components"></a>Ipari IoT-összetevők

**Eszközök IoT Edge** Az IoT Edge-eszközök Edge futtatókörnyezetből és peremhálózati modulokból állnak. 
- Az *Edge-modulok* olyan Docker-tárolók, amelyek a számítások legkisebb egységei, például az OPC-közzétevő és az OPC Twin. 
- Az *Edge-eszköz* az OPC ua-kiszolgáló és a felhőben IoT hub között közvetítőként szolgáló modulok központi telepítésére szolgál. IoT Edgeról további információt [itt](https://azure.microsoft.com/services/iot-edge/)talál.

**IoT hub** A IoT Hub a IoT-alkalmazás és az általa kezelt eszközök közötti kétirányú kommunikációhoz központi üzenetsorként működik. Ez egy nyílt és rugalmas felhőalapú platform, amely támogatja a nyílt forráskódú SDK-kat és a több protokollt támogató szolgáltatásokat. További információ [a IoT hubról](https://azure.microsoft.com/services/iot-hub/).

**Ipari peremhálózati modulok**
- *OPC-közzétevő*: az OPC-közzétevő IoT Edge belül fut. Az OPC UA-kiszolgálókhoz csatlakozik, és az OPC UA "pub/Sub" formátumban közzéteszi a JSON-kódolású telemetria adatait az Azure IoT Hub. Az Azure IoT Hub Client SDK által támogatott összes átviteli protokoll használható, például HTTPS, AMQP és MQTT.
- *OPC Twin*: az OPC Twin olyan szolgáltatásokból áll, amelyek Azure IoT Edge és IoT hub használnak a felhő és a gyári hálózat összekapcsolásához. Az OPC Twin a REST API-kon keresztül biztosítja az ipari eszközök felderítését, regisztrálását és távvezérlését. Az OPC Twin nem igényel OPC Unified Architecture (OPC UA) SDK-t. A programozási nyelv agnosztikus, és a kiszolgáló nélküli munkafolyamatokban is felvehető.
- *Felderítés*: a felderítő modul, amelyet a felfedezője identitása képvisel, felderítési szolgáltatásokat biztosít a peremhálózat szélén, amely tartalmazza az OPC ua-kiszolgáló felderítését. Ha a felderítés konfigurálva van és engedélyezve van, akkor a modul a IoT Edge és IoT Hub telemetria útvonalon küldi el a vizsgálat eredményeit a bevezetési szolgáltatásnak. A szolgáltatás feldolgozza az eredményeket, és frissíti az összes kapcsolódó identitást a beállításjegyzékben.


**Ipari eszközeinek felderítése, regisztrálása és kezelése az Azure-** ban Az Azure Industrial IoT lehetővé teszi, hogy a létesítmények üzemeltetői felfedezzék az OPC UA-kompatibilis kiszolgálókat a gyári hálózaton, és regisztrálják őket az Azure IoT Hubban. Az operatív munkatársak előfizethetnek és reagálnak a gyári szinten lévő eseményekre a világ bármely pontján. A Services REST API-k az OPC UA-szolgáltatások peremhálózati tükrözését tükrözik. Ezek a OAUTH-hitelesítéssel és a Azure Active Directory (HRE) által támogatott engedélyekkel biztonságosak. Ez lehetővé teszi a Felhőbeli alkalmazások számára a kiszolgálók címterület tallózását, illetve az írási/olvasási változók és a metódusok végrehajtását HTTPS-és egyszerű OPC UA-adattartalom használatával.

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte, hogy milyen ipari IoT van, az ipari IoT platformról és az OPC-közzétevőről olvashat:

> [!div class="nextstepaction"]
> [Mi az az ipari IoT platform?](overview-what-is-industrial-iot-platform.md)

> [!div class="nextstepaction"]
> [Mi az OPC-közzétevő?](overview-what-is-opc-publisher.md)