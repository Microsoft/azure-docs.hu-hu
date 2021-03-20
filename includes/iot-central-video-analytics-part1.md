---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2f3e4bf640b8da31a7fa4d818b94b0372d3026b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96763445"
---
A minta alkalmazás két szimulált eszközt és egy IoT Edge átjárót tartalmaz. Az alábbi oktatóanyagok két módszert mutatnak be a kísérlethez és az átjáró képességeinek megismeréséhez:

* Hozza létre az IoT Edge átjárót egy Azure-beli virtuális gépen, és csatlakoztasson egy szimulált kamerát.
* Hozza létre az IoT Edge-átjárót egy valódi eszközön, például egy Intel-NUC, és csatlakoztasson egy valódi kamerát.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Hozzon létre egy kiskereskedelmi áruházbeli alkalmazást az Azure IoT Central video Analytics alkalmazás sablon használatával
> * Az Alkalmazásbeállítások testreszabása
> * IoT Edge Gateway-eszközhöz tartozó eszköz sablonjának létrehozása
> * Átjáró-eszköz hozzáadása a IoT Central alkalmazáshoz

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag-Sorozat elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription).
* Ha valódi kamerát használ, kapcsolatra van szüksége a IoT Edge eszköz és a kamera között, és szüksége van a **valós idejű Streaming Protocol** -csatornára.

## <a name="initial-setup"></a>Kezdeti beállítás

Ezekben az oktatóanyagokban számos konfigurációs fájl frissítésére és használatára van lehetőség. Ezeknek a fájloknak a kezdeti verziói a [LVA-Gateway GitHub-](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) tárházban érhetők el. A tárház tartalmaz egy [firkatömb](https://github.com/Azure/live-video-analytics/blob/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) szövegfájlt, amellyel letöltheti és felhasználhatja a konfigurációs értékek rögzítését a telepített szolgáltatásokból. Ez a fájl segítséget nyújt az oktatóanyagok későbbi lépéseinek végrehajtásához.

Hozzon létre egy *LVA-Configuration* nevű mappát a helyi gépen a fájlok másolatainak mentéséhez. Ezután kattintson a jobb gombbal az alábbi hivatkozásokra, és válassza a **Mentés másként** lehetőséget a fájl mentéséhez a *LVA* mappába:
