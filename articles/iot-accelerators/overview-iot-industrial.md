---
title: Az Azure ipari IoT áttekintése |} A Microsoft Docs
description: Az ipari IoT áttekintése
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: bda40470e3ccf3a5d7b23dca38b21090e864b16a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888487"
---
# <a name="what-is-industrial-iot-iiot"></a>Mi az ipari IoT (IIoT)

IIoT az ipari eszközök internetes hálózata. IIoT fokozza a ipari növelhető a hatékonyság a gyártás IOT az alkalmazáson keresztül. 

## <a name="improve-industrial-efficiencies"></a>Ipari növelhető a hatékonyság növelése

Növelje működtetési hatékonyságát és jövedelmezőségét a csatlakoztatott gyár megoldásgyorsító. Ipari felszereléseit és eszközeit – többek között a már működő gyári gépeket is – a felhőben csatlakoztathatja és figyelheti. IoT-adatainak elemzésével értékes információkhoz juthat, amelyek a teljes gyártás teljesítményét is kedvezően befolyásolhatják.

Csökkentheti a férnek hozzá a gyári emelet gépek OPC Ikereszköz időigényes folyamat, és a hangsúly a idő IIoT megoldások. Egyszerűsíthetők a Tanúsítványkezelő és ipari eszközök integrációja az OPC-tárolóval, és a benne, hogy biztonságos-e az eszközintelligencia-kapcsolat. Ilyen mikroszolgáltatásokból adjon meg egy hasonló REST API-t a [ipari IoT Azure-összetevők](https://github.com/Azure/azure-iiot-opc-ua). A szolgáltatás API-t biztosít edge-modul funkciók irányítását. 

![Ipari IoT – áttekintés](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Ipari IoT Azure-szolgáltatásokkal kapcsolatos további információkért tekintse meg a GitHub [tárház](https://github.com/Azure/azure-iiot-services).
Ha még nem ismeri az Azure IoT Edge-modulok működéséről, kezdődik a következő cikkeket:
- [Tudnivalók az Azure IoT Edge-ről](../iot-edge/about-iot-edge.md)
- [Az Azure IoT Edge-modulok](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Csatlakoztatott gyár

[A csatlakoztatott gyári](../iot-accelerators/iot-accelerators-connected-factory-features.md) implementálhat a Microsoft Azure ipari IoT referenciaarchitektúra testre szabható adott üzleti szükségletek kielégítése céljából. A teljes megoldás kódja nyílt forráskódú és elérhető a csatlakoztatott gyár megoldás gyorsító GitHub-adattárban. Használhatja kereskedelmi termék kiindulásaként is, és percek alatt üzembe helyezhet egy előre elkészített megoldást az Azure-előfizetésében. 

## <a name="factory-floor-connectivity"></a>Gyári emelet kapcsolat

Az OPC-Twin-e egy IIoT összetevő, amely automatizálja az eszköz felderítése és regisztrálása, és lehetővé teszi a távoli ipari eszközök REST API-kon keresztül. OPC Ikereszköz, használja az Azure IoT Edge és IoT Hub a felhőben és a gyári hálózathoz csatlakozni. Az OPC-Twin szolgáltatással IIoT IIoT alkalmazások készítésére is biztonságosan hozzáférhessenek a helyszíni gépek hogyan bajlódnia.

## <a name="security"></a>Biztonság

Az OPC-tároló az OPC UA globális felderítési kiszolgáló (GDS), konfigurálása, regisztrálja, és az OPC UA-kiszolgáló és az ügyfélalkalmazások számára a felhőben a tanúsítvány életciklusának kezelése implementációja. Az OPC-tároló az implementálási vagy a kapcsolat biztonságos objektum az ipari terület karbantartási egyszerűbbé teszi. Tanúsítványkezelés automatizálásával OPC-tároló a kapcsolat és tanúsítványkezelés társított manuális és az összetett folyamat gyári operátorainak szabadít fel.

## <a name="next-steps"></a>További lépések

Most, hogy a bemutató ipari IoT és annak összetevői már rendelkezett, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Mi az OPC-Twin](overview-opc-twin.md)
