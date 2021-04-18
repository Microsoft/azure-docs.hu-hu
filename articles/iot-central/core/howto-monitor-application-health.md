---
title: Egy alkalmazásalkalmazás Azure IoT Central állapotának | Microsoft Docs
description: Operátorként vagy rendszergazdaként figyelje az alkalmazáshoz csatlakoztatott eszközök általános IoT Central állapotát.
author: dominicbetts
ms.author: dobett
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: df89d53e6b5043c1ef3caa1c92f2abaae542d6ec
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599009"
---
# <a name="monitor-the-overall-health-of-an-iot-central-application"></a>Egy alkalmazás általános állapotának IoT Central figyelése

> [!NOTE]
> A metrikák csak a 3-as verziójú alkalmazásokhoz IoT Central érhetők el. Az alkalmazás verziójának ellenőrzését lásd: [Tudnivalók az alkalmazásról.](./howto-get-app-info.md)

*Ez a cikk a kezelőkre és a rendszergazdákra vonatkozik.*

Ebből a cikkből megtudhatja, hogyan használhatja a IoT Central által biztosított metrikákat az IoT Central-alkalmazáshoz csatlakoztatott eszközök és a futó adatexportok állapotának értékeléséhez.

A metrikák alapértelmezés szerint engedélyezve vannak a IoT Central alkalmazáshoz, és ön a következő [elérésű](https://portal.azure.com/)Azure Portal. A [Azure Monitor adatplatform elérhetővé teszi](../../azure-monitor/essentials/data-platform-metrics.md) ezeket a metrikákat, és számos módszert kínál a velük való interakcióra. Használhat például diagramokat a powershellben Azure Portal, REST API lekérdezésekben a PowerShellben vagy az Azure CLI-ban.

### <a name="trial-applications"></a>Próbaverziós alkalmazások

Az ingyenes próbatervet használó alkalmazások nem társított Azure-előfizetéssel, ezért nem támogatják a Azure Monitor metrikákat. Az [alkalmazásokat standard tarifacsomagra konvertálhatja,](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) és hozzáférhet ezekhez a metrikákhoz.

## <a name="view-metrics-in-the-azure-portal"></a>Metrikák megtekintése a Azure Portal

A következő lépések azt feltételezik, hogy [](./tutorial-connect-device.md) IoT Central [néhány](./quick-deploy-iot-central.md) csatlakoztatott eszközzel vagy egy futó adatexportál-alkalmazással. [](howto-export-data.md)

A IoT Central megtekintése a portálon:

1. Lépjen a IoT Central-alkalmazás erőforrásához a portálon. Alapértelmezés szerint a IoT Central erőforrások egy **IOTC nevű erőforráscsoportban találhatók.**
1. Diagram létrehozásához az alkalmazás metrikákból válassza a **Metrikák** lehetőséget a **Monitorozás szakaszban.**

![Azure-metrikák](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Azure Portal engedélyek

A metrikaelérést Azure Portal Azure-beli szerepköralapú [hozzáférés-vezérlés kezeli.](../../role-based-access-control/overview.md) A Azure Portal adhat hozzá felhasználókat a IoT Central-alkalmazáshoz/erőforráscsoporthoz/előfizetéshez, hogy hozzáférést biztosít nekik. Akkor is hozzá kell adni egy felhasználót a portálon, ha már hozzá vannak adva IoT Central alkalmazáshoz. Az [Azure beépített szerepkörei segítségével](../../role-based-access-control/built-in-roles.md) finomabb hozzáférés-vezérlést használhat.

## <a name="iot-central-metrics"></a>IoT Central metrikák

A szolgáltatáshoz jelenleg elérhető metrikák listájáért lásd a IoT Central támogatott metrikákat a [Azure Monitor.](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps)

### <a name="metrics-and-invoices"></a>Metrikák és számlák

A metrikák eltérhetnek a számlán szereplő Azure IoT Central számtól. Ez a helyzet több okból is fennáll, például:

- IoT Central [tarifacsomagok két](https://azure.microsoft.com/pricing/details/iot-central/) eszközt tartalmaznak, és ingyenesen különböző üzenetkvótákat tartalmaznak. Bár az ingyenes elemek ki vannak zárva a számlázásból, továbbra is beleszámulnak a metrikákba.

- IoT Central egy teszteszköz-azonosítót automatikusan generál az alkalmazás minden eszközsablonja számára. Ez az eszközazonosító az eszközsablon **Teszteszköz** kezelése lapján látható. A megoldáskészítők dönthetnek úgy, hogy ellenőrzik az eszközsablonokat, mielőtt közzétenik azokat, ha olyan kódot hoznak létre, amely ezeket a teszteszköz-kódokat használja. Bár ezek az eszközök ki vannak zárva a számlázásból, továbbra is beleszámulnak a metrikákba.

- Bár a metrikák az eszközről a felhőbe való kommunikáció egy részkészletét mutatják, az eszköz és a felhő közötti összes kommunikáció a [számlázás üzenetének számít.](https://azure.microsoft.com/pricing/details/iot-central/)

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan használhatók az alkalmazássablonok, a javasolt következő lépés az alkalmazássablonok IoT Central a [Azure Portal.](howto-manage-iot-central-from-portal.md)