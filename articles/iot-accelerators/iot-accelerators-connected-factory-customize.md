---
title: A csatlakoztatott gyári megoldás testreszabása – Azure | Microsoft Docs
description: Annak leírása, hogyan szabhatja testre a csatlakoztatott gyári megoldás-gyorsító viselkedését.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: a092859517ee6ac41e62e55fe95f72b8bfdf87d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646097"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Annak testreszabása, hogy a csatlakoztatott gyári megoldás hogyan jelenítse meg az OPC UA-kiszolgálók adatait

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

A csatlakoztatott gyári megoldás összesíti és megjeleníti a megoldáshoz kapcsolódó OPC UA-kiszolgálók adatait. A megoldásban megkeresheti és elküldheti a parancsokat az OPC UA-kiszolgálókra. Az OPC UA architektúráról a [Csatlakoztatott gyár – GYIK](iot-accelerators-faq-cf.md) fejezetben talál további információt.

A megoldás összesített adatainak példái közé tartoznak az általános berendezések hatékonysága (OEE) és a fő teljesítménymutatók (KPI-k), amelyeket az irányítópulton a gyári, a vonal-és az állomás szintjén lehet megtekinteni. A következő képernyőfelvételen a **szerelvény** állomásának OEE-és KPI-értékei láthatók az **1. termelési sorban**, a **müncheni** gyárban:

![Példa OEE-és KPI-értékekre a megoldásban][img-oee-kpi]

A megoldás lehetővé teszi, hogy az OPC UA-kiszolgálókról, *állomásoktól* származó részletes adatokat tekintse meg a megadott adatelemekről. Az alábbi képernyőfelvételen egy adott állomásról származó feldolgozott elemek számának ábrázolása látható:

![A gyártott elemek számának ábrázolása][img-manufactured-items]

Ha az egyik gráfra kattint, az Time Series Insights (ÁME) használatával tovább is vizsgálhatja az adatelemzést:

![Az adatfelfedezés Time Series Insights használatával][img-tsi]

Ez a cikk a következőket ismerteti:

- Az adatgyűjtés módja a megoldás különböző nézetei számára.
- Hogyan szabhatja testre a megoldás által megjelenített adatmegjelenítés módját.

## <a name="data-sources"></a>Adatforrások

A csatlakoztatott gyár megoldás a megoldáshoz kapcsolódó OPC UA-kiszolgálók adatait jeleníti meg. Az alapértelmezett telepítés több, gyári szimulációt futtató OPC UA-kiszolgálót tartalmaz. Saját OPC UA-kiszolgálókat adhat hozzá, amelyek [egy átjárón keresztül csatlakoznak] [lnk-csatlakozni-CF] a megoldáshoz.

A csatlakoztatott OPC UA-kiszolgáló által a megoldáshoz az irányítópulton küldött adatelemek között böngészhet:

1. Válassza a **böngésző** lehetőséget az **OPC ua-kiszolgáló kiválasztása nézet megnyitásához** :

    ![Navigáljon az OPC UA-kiszolgáló kiválasztása nézethez][img-select-server]

1. Válasszon ki egy kiszolgálót, és kattintson a **kapcsolat** elemre. A biztonsági figyelmeztetés megjelenésekor kattintson a **Folytatás** gombra.

    > [!NOTE]
    > Ez a figyelmeztetés csak egyszer jelenik meg az egyes kiszolgálókon, és megbízhatósági kapcsolatot létesít a megoldás irányítópultja és a kiszolgáló között.

1. Most megtekintheti azokat az adatelemeket, amelyeket a kiszolgáló el tud küldeni a megoldásnak. A megoldásnak küldendő elemek Pipa jel:

    ![Közzétett elemek][img-published]

1. Ha Ön *rendszergazda* a megoldásban, kiválaszthatja, hogy közzétesz egy adatelemet, hogy elérhető legyen a csatlakoztatott gyári megoldásban. Rendszergazdaként az OPC UA-kiszolgálón módosíthatja az adatelemek és a hívási metódusok értékét is.

## <a name="map-the-data"></a>Az adatleképezés

A csatlakoztatott gyár megoldás leképezi és összesíti a közzétett adatelemeket az OPC UA-kiszolgálóról a megoldás különböző nézeteire. A csatlakoztatott gyár megoldás üzembe helyezése az Azure-fiókban a megoldás kiépítésekor. A Visual Studio csatlakoztatott Factory megoldásban található JSON-fájl tárolja ezt a leképezési információt. A JSON konfigurációs fájl megtekinthető és módosítható a csatlakoztatott Factory Visual Studio-megoldásban. A módosítás után újból üzembe helyezheti a megoldást.

A konfigurációs fájlt a következőre használhatja:

- Szerkessze a meglévő szimulált gyárakat, az üzemi vonalakat és az állomásokat.
- A megoldáshoz kapcsolódó valós OPC UA-kiszolgálók adatainak leképezése.

További információ az adatok leképezéséről és összesítéséről a konkrét követelmények teljesítése érdekében: [a csatlakoztatott gyári megoldás-gyorsító konfigurálása ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>A módosítások központi telepítése

Ha végzett a **ContosoTopologyDescription.js** fájlon végzett módosításával, újra kell telepítenie a csatlakoztatott gyári megoldást az Azure-fiókjába.

Az **Azure-IOT-Connected-Factory** adattár tartalmaz egy **build.ps1** PowerShell-szkriptet, amely segítségével újraépítheti és telepítheti a megoldást.

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekben további információt talál a csatlakoztatott gyári megoldási gyorssegédről:

* [Engedélyek a azureiotsolutions.com webhelyen][lnk-permissions]
* [Csatlakoztatott gyár – GYIK](iot-accelerators-faq-cf.md)
* [Gyakori kérdések][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md