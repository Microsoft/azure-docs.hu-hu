---
title: Az Azure Industrial IoT áttekintése | Microsoft Docs
description: Ez a cikk az ipari IoT áttekintését tartalmazza. Ismerteti a csatlakoztatott gyárat, a gyári padlót és a biztonsági összetevőket a IIoT-ben.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3df10c9d7630e9db76994e8e508f30adb986e0d5
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646878"
---
# <a name="what-is-industrial-iot-iiot"></a>Mi az ipari IoT (IIoT)?

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

A IIoT az ipari eszközök internetes hálózata. A IIoT az ipari hatékonyságot a IoT alkalmazásával fokozza a feldolgozóipari iparágban. 

## <a name="improve-industrial-efficiencies"></a>Az ipari hatékonyság javítása

Növelje működési termelékenységét és jövedelmezőségét egy csatlakoztatott gyári megoldás-gyorsító révén. A felhőben működő ipari berendezések és eszközök csatlakoztatása és monitorozása – beleértve a gyári szinten már üzemelő gépeket is. Elemezheti a IoT adatait az elemzésekhez, amelyek segítenek a teljes gyári szint teljesítményének növelésében.

Csökkentse a Factory Floor Machines és az OPC Twin szolgáltatásokhoz való hozzáférés időigényes folyamatát, és a IIoT-megoldások kiépítésének idejére koncentráljon. Egyszerűsítheti a Tanúsítványkezelőt és az iparági eszközök integrációját az OPC-tárolóval, és bízik benne, hogy az adategység-kapcsolat biztonságos. Ezek a szolgáltatások egy REST-szerű API-t biztosítanak az [Azure Industrial IoT összetevőihez](https://github.com/Azure/Industrial-IoT). A Service API lehetővé teszi az Edge-modul funkcióinak vezérlését. 

![Az ipari IoT áttekintése](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Az Azure Industrial IoT szolgáltatásaival kapcsolatos további információkért lásd a GitHub- [tárházat](https://github.com/Azure/Industrial-IoT) és a [dokumentációt](https://azure.github.io/Industrial-IoT/).
Ha nem ismeri a Azure IoT Edge moduljainak működését, kezdje a következő cikkekkel:
- [Tudnivalók az Azure IoT Edge-ről](../iot-edge/about-iot-edge.md)
- [Azure IoT Edge modulok](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Csatlakoztatott gyár

A [csatlakoztatott gyár](../iot-accelerators/iot-accelerators-connected-factory-features.md) a Microsoft Azure Industrial IoT-referenciájának olyan implementációja, amely testreszabható az adott üzleti igények kielégítése érdekében. A teljes megoldás kódja nyílt forráskódú, és elérhető a csatlakoztatott Factory Solution Accelerator GitHub-tárházban. Használhatja kiindulási pontként egy kereskedelmi termék számára, és percek alatt üzembe helyezhet egy előre elkészített megoldást az Azure-előfizetésében. 

## <a name="factory-floor-connectivity"></a>Gyári padló kapcsolat

Az OPC Twin egy IIoT-összetevő, amely automatizálja az eszközök felderítését és regisztrálását, és a REST API-kon keresztül biztosítja az ipari eszközök távvezérlését. Az OPC Twin, Azure IoT Edge és IoT Hub használatával csatlakozik a felhőhöz és a gyári hálózathoz. Az OPC Twin lehetővé teszi a IIoT-fejlesztők számára, hogy a helyszíni gépek biztonságos elérésével kapcsolatos gondok nélkül összpontosítsanak a IIoT-alkalmazások létrehozására.

## <a name="security"></a>Biztonság

Az OPC-tár az OPC ua Global Discovery Server (GDS) implementációja, amely konfigurálhatja, regisztrálhatja és kezelheti a tanúsítványok életciklusát az OPC UA-kiszolgáló és az ügyfélalkalmazások számára a felhőben. Az OPC-tároló leegyszerűsíti a biztonságos eszközök kapcsolatának megvalósítását és karbantartását az ipari térben. A Tanúsítványkezelő automatizálásával az OPC-tároló felszabadítja a gyári operátorokat a kapcsolat-és tanúsítványkezelőhöz kapcsolódó manuális és összetett folyamatokkal.

## <a name="next-steps"></a>Következő lépések

Most, hogy már bevezette az iparági IoT és annak összetevőit, itt látható a következő lépés:

[Mi az az OPC-iker?](overview-opc-twin.md)
