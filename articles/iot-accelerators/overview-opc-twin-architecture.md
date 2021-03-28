---
title: OPC Twin Architecture – Azure | Microsoft Docs
description: Ez a cikk áttekintést nyújt az OPC Twin architektúráról. Ismerteti a kiszolgáló felderítését, aktiválását, böngészését és figyelését.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 602d2f77564709294d28b797ff3f03f12b3b32d8
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646827"
---
# <a name="opc-twin-architecture"></a>OPC Twin architektúra

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

Az alábbi ábrák az OPC Twin architektúrát szemléltetik.

## <a name="discover-and-activate"></a>Felderítés és aktiválás

1. A kezelő lehetővé teszi a hálózati vizsgálat használatát a modulon, vagy egy felderítési URL-cím használatával egyszeri felderítést végez. A felderített végpontokat és az alkalmazással kapcsolatos információkat a rendszer a telemetria-on keresztül küldi el feldolgozásra.  Az OPC UA-eszköz bevezetési ügynöke az OPC UA-kiszolgáló felderítési eseményeit dolgozza fel a felderítési vagy beolvasási módban, ha az OPC Twin IoT Edge modulja. A felderítési események az OPC UA-eszköz beállításjegyzékében az alkalmazások regisztrálását és frissítését eredményezik.

   ![Diagram, amely az OPC Twin IoT Edge modult jeleníti meg a felderítési vagy vizsgálati módban.](media/overview-opc-twin-architecture/opc-twin1.png)

1. Az operátor megvizsgálja a felderített végpont tanúsítványát, és aktiválja a regisztrált végpontot a hozzáféréshez. 

   ![Diagram, amely az OPC Twin architektúrát mutatja a "Twin Identity" IoT Edge.](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Tallózás és figyelés

1. Az aktiválást követően az operátor használhatja a Twin szolgáltatás REST API a kiszolgálói információs modell tallózására és vizsgálatára, az objektum-változók olvasására és írására, valamint a hívási metódusokra.  A felhasználó egy egyszerűsített OPC UA API-t használ, amely teljes mértékben a HTTP és a JSON nyelven van kifejezve.

   ![Diagram, amely a kiszolgálói információs modell tallózására és vizsgálatára szolgáló OPC Twin Architecture-telepítőt mutatja be.](media/overview-opc-twin-architecture/opc-twin3.png)

1. A két szolgáltatás REST-felülete a figyelt elemek és előfizetések létrehozására is használható az OPC-közzétevőben. Az OPC-közzétevő lehetővé teszi, hogy a telemetria az OPC UA-kiszolgáló rendszerekből IoT Hub. További információ az OPC-közzétevőről: [Mi az OPC-közzétevő](overview-opc-publisher.md).

   ![Az OPC Twin működése](media/overview-opc-twin-architecture/opc-twin4.png)
