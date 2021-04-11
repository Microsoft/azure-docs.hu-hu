---
title: Azure ipari IoT platform
description: Ez a cikk áttekintést nyújt az ipari IoT platformról és annak összetevőiről.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 24932b17c630190cb3121d5310a865758fa6a920
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801553"
---
# <a name="what-is-the-industrial-iot-iiot-platform"></a>Mi az ipari IoT (IIoT) platform?

Az Azure Industrial IoT platform az Azure-ban üzembe helyezett modulok és szolgáltatások Microsoft-csomagja. Ezek a modulok és szolgáltatások teljes mértékben megnyitottak. Az Azure felügyelt platformként nyújtott szolgáltatásként (AMQP, MQTT, HTTP) és a felületeken (nyílt API) és a felhőben nyitott ipari adatmodelleket (OPC ua-t, az OPC ua-t, a HTTP-t) és a felületek (Open API) és az Open Industrial adatmodelleket (OPC UA) pedig a felhőben kell alkalmazni.

## <a name="enabling-shopfloor-connectivity"></a>Shopfloor-kapcsolat engedélyezése 

Az Azure ipari IoT platform a shopfloor-eszközök (beleértve az OPC UA-kompatibilis eszközök felderítését) ipari kapcsolatait is magában foglalja, az adatokat az OPC ua formátumba, és az eszköz telemetria adatokat az Azure-ba az OPC UA közzététel formátumban továbbítja. Ott tárolja a telemetria-adatkészletet egy felhőalapú adatbázisban. Emellett a platform lehetővé teszi a shopfloor-eszközök biztonságos elérését az OPC UA-n keresztül a felhőből. Az eszközkezelés funkciói (beleértve a biztonsági konfigurációt is) beépítettek. Az OPC UA-funkciók a Docker-tároló technológiával lettek felépítve az egyszerű üzembe helyezéshez és felügyelethez. A nem OPC UA-kompatibilis eszközök esetében a vezető ipari kapcsolati szolgáltatókkal együttműködve biztosítjuk, hogy az OPC UA-adapter szoftverét Azure IoT Edge. Ezek az adapterek az Azure Marketplace-en érhetők el.

## <a name="industrial-iot-components-iot-edge-modules-and-cloud-microservices"></a>Ipari IoT-összetevők: IoT Edge modulok és Cloud Service-szolgáltatások

A peremhálózati szolgáltatások Azure IoT Edge modulként vannak megvalósítva, és a helyszínen futnak. A Cloud Service-szolgáltatások olyan ASP.NET-szolgáltatásként valósulnak meg, amely REST-felülettel rendelkezik, és felügyelt Azure Kubernetes-szolgáltatásokon fut, vagy önálló Azure App Service. Az Edge és a Cloud Services esetében a Microsoft Container Registry (MCR) szolgáltatásban előre elkészített Docker-tárolókat is biztosítottunk, így az ügyfél nem távolítja el ezt a lépést. Az Edge és a Cloud Services kihasználja egymást, és együtt kell használni őket. Olyan könnyen használható telepítési szkripteket is biztosítunk, amelyek lehetővé teszik, hogy az egyik a teljes platformot egyetlen paranccsal telepítse.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az Azure Industrial IoT platformot, megismerheti az OPC-közzétevőt:

> [!div class="nextstepaction"]
> [Mi az OPC-közzétevő?](overview-what-is-opc-publisher.md)