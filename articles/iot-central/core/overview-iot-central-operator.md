---
title: Azure IoT Central-kezelői útmutató
description: Az Azure IoT Central egy IoT-alkalmazásplatform, amely megkönnyíti IoT-megoldások létrehozását. Ez a cikk áttekintést nyújt a IoT Central operátori szerepköréről.
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: b8692973f187743e282de6f8e54a55363cc3105b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669940"
---
# <a name="iot-central-operator-guide"></a>IoT Central operátori útmutató

*Ez a cikk az operátorokra vonatkozik.*

Egy IoT Central alkalmazás lehetővé teszi több millió eszköz figyelését és kezelését életciklusuk során. Ez az útmutató azoknak a kezelőknek szól, akik IoT Central alkalmazást használnak a IoT-eszközök felügyeletére.

Operátor:

- Figyeli és kezeli az alkalmazáshoz csatlakoztatott eszközöket.
- Az eszközökkel kapcsolatos problémák elhárítása és szervizelése.
- Új eszközök kiosztása.

## <a name="monitor-and-manage-devices"></a>Eszközök figyelése és kezelése

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="Az eszköz nézetét megjelenítő képernyőkép":::

Az eszközök figyeléséhez az operátor az eszköz sablonjának részeként használhatja a megoldás-szerkesztő által meghatározott nézeteket. Ezek a nézetek az eszköz telemetria és a tulajdonságértékek megjelenítésére használhatók. Ilyen például az előző képernyőképen látható **áttekintő** nézet.

Részletesebb tájékoztatásért az operátor használhatja az eszközöket és a beépített elemzési funkciókat. További információért lásd: [az eszközök](howto-create-analytics.md)elemzésének használata

Az egyes eszközök kezeléséhez az operátor az eszközök és a felhő tulajdonságainak megadására, valamint az eszköz parancsainak meghívására használhatja az eszközök nézeteit. Ilyenek például az **eszköz** és a **parancsok** kezelése az előző képernyőképen.

Az eszközök tömeges felügyeletéhez az operátor létrehozhat és ütemezhet feladatokat. A feladatok frissíthetik a tulajdonságokat, és futtathatnak parancsokat több eszközön. További információ: [feladatok létrehozása és futtatása az Azure IoT Central alkalmazásban](howto-run-a-job.md).

## <a name="troubleshoot-and-remediate-issues"></a>Problémák elhárítása és szervizelése

Az operátor felelős az alkalmazás és az eszköz állapotáról. A [hibaelhárítási útmutató](troubleshoot-connection.md) segítséget nyújt a kezelők számára a gyakori problémák diagnosztizálásában és szervizelésében. Az operátorok az **eszközök** lapon letilthatják azokat az eszközöket, amelyek úgy tűnik, hogy a probléma megoldódik.

## <a name="add-and-remove-devices"></a>Eszközök hozzáadása és eltávolítása

Az operátor akár egyénileg, akár tömegesen is hozzáadhat és eltávolíthat eszközöket a IoT Central alkalmazáshoz. További információ: [eszközök kezelése az Azure IoT Central alkalmazásban](howto-manage-devices.md).

## <a name="personalize"></a>Személyre szabás

A kezelők személyre szabott irányítópultokat hozhatnak létre egy IoT Central alkalmazásban, amely a leggyakrabban használt erőforrásokra mutató hivatkozásokat tartalmaz. További információt az [irányítópultok kezelése](howto-create-personal-dashboards.md#manage-dashboards)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a IoT Central használatáról, a javasolt következő lépések az [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md)című rövid útmutatók kipróbálása.
