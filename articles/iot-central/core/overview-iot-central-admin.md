---
title: Azure IoT Central útmutató
description: Az Azure IoT Central egy IoT-alkalmazásplatform, amely megkönnyíti IoT-megoldások létrehozását. Ez a cikk áttekintést nyújt a rendszergazdai szerepkörről a IoT Central.
author: philmea
ms.author: philmea
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 658ebe027565a3acaf427a7b3dbf3963701069d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868644"
---
# <a name="iot-central-administrator-guide"></a>IoT Central útmutató

*Ez a cikk a rendszergazdákra vonatkozik.*

Egy IoT Central alkalmazással eszközök millióit figyelheti és kezelheti azok életciklusa során. Ez az útmutató olyan rendszergazdáknak készült, akik IoT Central alkalmazásokat.

A IoT Central rendszergazda:

- Az alkalmazás felhasználóit és szerepköreit kezeli.
- Kezeli a biztonságot, például az eszközhitelesítést.
- Konfigurálja az alkalmazásbeállításokat.
- Frissíti az alkalmazásokat.
- Alkalmazásokat exportál és megoszt.
- Az alkalmazás állapotát figyeli.

## <a name="users-and-roles"></a>Felhasználók és szerepkörök

IoT Central szerepköralapú hozzáférés-vezérlési rendszert használ a felhasználói engedélyek alkalmazáson belüli kezeléséhez. IoT Central három beépített szerepköre van a rendszergazdák, a megoldáskészítők és az operátorok számára. A rendszergazdák adott engedélykészletekkel rendelkező egyéni szerepköröket hozhatnak létre. A rendszergazdák felelősek a felhasználók alkalmazáshoz való hozzáadásáért és szerepkörökhöz való hozzárendeléséért.

További tudnivalókért lásd: Felhasználók és szerepkörök kezelése a IoT Central [alkalmazásban.](howto-manage-users-roles.md)

## <a name="application-security"></a>Alkalmazások biztonsága

Az alkalmazáshoz IoT Central eszközök általában X.509-tanúsítványokat vagy közös hozzáférésű jogosultságokat (SAS) használnak hitelesítő adatként. A rendszergazda kezeli a csoporttanúsítványokat vagy kulcsokat, amelyekből az eszköz hitelesítő adatai származnak.

További tudnivalókért lásd: X.509-csoportregisztrálás, SAS-csoportregisztrálás és [X.509-eszköztanúsítványok igénylése.](how-to-roll-x509-certificates.md) [](concepts-get-connected.md#x509-group-enrollment) [](concepts-get-connected.md#sas-group-enrollment)

A rendszergazda emellett létrehozhatja és kezelheti az API-jogkivonatokat, amelyek használatával az ügyfélalkalmazás hitelesíti magát a IoT Central alkalmazással. Az ügyfélalkalmazások a REST API használják a IoT Central.

## <a name="configure-an-application"></a>Alkalmazás konfigurálása

A rendszergazda konfigurálhatja egy alkalmazás IoT Central megjelenését. További információ:

- [Alkalmazás nevének és URL-címének módosítása](howto-administer.md#change-application-name-and-url)
- [A felhasználói felület testreszabása](howto-customize-ui.md)
- [Alkalmazás áthelyezése másik díjszabási csomagba](howto-view-bill.md)
- [Fájlfeltöltések konfigurálása](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Alkalmazás exportálása

A rendszergazdák a következő jogosultságokat kaphatják meg:

- Ha csak az alkalmazás másolatára van szüksége, hozzon létre egy másolatot az alkalmazásról. A teszteléshez például szükség lehet egy duplikált másolatra.
- Hozzon létre egy alkalmazássablont egy meglévő alkalmazásból, ha több példányt tervez létrehozni.

További információ: [Az Azure IoT-alkalmazás exportálása.](howto-use-app-templates.md)

## <a name="migrate-to-a-new-version"></a>Áttelepítés új verzióra

A rendszergazdák újabb verzióra is átemelheti az alkalmazásokat. Jelenleg az újonnan létrehozott alkalmazás egy V3-as alkalmazás. Előfordulhat, hogy egy rendszergazdának V2-alkalmazást kell átemelni egy V3-alkalmazásba.

További információ: V2-es virtuális gép IoT Central [V3-ra.](howto-migrate.md)

## <a name="monitor-application-health"></a>Alkalmazásállapot monitorozása

A rendszergazdák a IoT Central metrikák használatával felmérheti a csatlakoztatott eszközök és a futó adatexportok állapotát.

A metrikák megtekintéséhez a rendszergazdák diagramokat használhatnak a Azure Portal, REST API PowerShell- vagy Azure CLI-lekérdezésekben.

További információ: Monitor the overall health of an IoT Central application (Egy alkalmazás [általános állapotának IoT Central figyelése).](howto-monitor-application-health.md)

## <a name="tools"></a>Eszközök

A rendszergazdaként használt eszközök közül számos  elérhető az egyes alkalmazások Adminisztráció IoT Central szakaszában. Néhány felügyeleti feladat elvégzéséhez a következő eszközöket is használhatja:

- [Azure CLI](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Azure Portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan felügyelhet egy Azure IoT Central-alkalmazást, a javasolt [](howto-manage-users-roles.md) következő lépés a felhasználók és szerepkörök kezelése a Azure IoT Central.
