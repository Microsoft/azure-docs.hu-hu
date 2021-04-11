---
title: Azure IoT Central rendszergazdai útmutató
description: Az Azure IoT Central egy IoT-alkalmazásplatform, amely megkönnyíti IoT-megoldások létrehozását. Ez a cikk áttekintést nyújt a IoT Central rendszergazdai szerepköréről.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 16a8aecae70d73399acb3878d7088e5086c053a1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110491"
---
# <a name="iot-central-administrator-guide"></a>IoT Central rendszergazdai útmutató

*Ez a cikk a rendszergazdákra vonatkozik.*

Egy IoT Central alkalmazás lehetővé teszi több millió eszköz figyelését és kezelését életciklusuk során. Ez az útmutató azoknak a rendszergazdáknak szól, akik IoT Central alkalmazást felügyelik.

IoT Central a rendszergazda:

- Kezeli a felhasználókat és a szerepköröket az alkalmazásban.
- Kezeli a biztonságot, például az eszközök hitelesítését.
- Az Alkalmazásbeállítások konfigurálása.
- Alkalmazások frissítése.
- Alkalmazások exportálása és megosztása.
- Az alkalmazás állapotának figyelése.

## <a name="users-and-roles"></a>Felhasználók és szerepkörök

A IoT Central szerepköralapú hozzáférés-vezérlési rendszer használatával kezeli a felhasználói engedélyeket egy alkalmazáson belül. A IoT Central három beépített szerepkörrel rendelkezik a rendszergazdák, a megoldás-építők és a kezelők számára. A rendszergazdák adott engedélyekkel hozhatnak létre egyéni szerepköröket. A rendszergazdák feladata, hogy felhasználókat adjanak hozzá egy alkalmazáshoz, és hozzá lehessen rendelni őket a szerepkörökhöz.

További információt a [felhasználók és szerepkörök kezelése a IoT Central alkalmazásban](howto-manage-users-roles.md)című témakörben talál.

## <a name="application-security"></a>Alkalmazások biztonsága

A IoT Central alkalmazáshoz csatlakozó eszközök általában X. 509 tanúsítványokat vagy közös hozzáférési aláírásokat (SAS) használnak hitelesítő adatként. A rendszergazda felügyeli azokat a tanúsítványokat vagy kulcsokat, amelyeket az eszköz hitelesítő adatai származtatnak.

További információ: [x. 509 csoportos regisztráció](concepts-get-connected.md#x509-group-enrollment), SAS- [csoport beléptetése](concepts-get-connected.md#sas-group-enrollment)és [az x. 509 eszköz tanúsítványainak](how-to-roll-x509-certificates.md)beolvasása.

A rendszergazda olyan API-jogkivonatokat is létrehozhat és kezelhet, amelyeket az ügyfélalkalmazás használ a IoT Central alkalmazással való hitelesítéshez. Az ügyfélalkalmazások a REST API használják a IoT Central való interakcióhoz.

## <a name="configure-an-application"></a>Alkalmazás konfigurálása

A rendszergazda konfigurálhatja egy IoT Central alkalmazás viselkedését és megjelenését. További információ:

- [Alkalmazás nevének és URL-címének módosítása](howto-administer.md#change-application-name-and-url)
- [A felhasználói felület testreszabása](howto-customize-ui.md)
- [Alkalmazás áthelyezése másik díjszabási csomagba](howto-view-bill.md)
- [Fájlfeltöltés konfigurálása](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Alkalmazás exportálása

A rendszergazda a következőket teheti:

- Hozzon létre egy másolatot az alkalmazásról, ha csak egy másolatot szeretne készíteni az alkalmazásról. Előfordulhat például, hogy a teszteléshez duplikált másolatot kell készítenie.
- Hozzon létre egy alkalmazás-sablont egy meglévő alkalmazásból, ha több példányt szeretne létrehozni.

További információ: [Az Azure IoT-alkalmazás exportálása](howto-use-app-templates.md).

## <a name="migrate-to-a-new-version"></a>Migrálás új verzióra

A rendszergazdák egy újabb verzióra telepíthetik az alkalmazásokat. Jelenleg egy újonnan létrehozott alkalmazás egy v3-alkalmazás. Előfordulhat, hogy a rendszergazdának v2 alkalmazást kell áttelepítenie egy v3 alkalmazásba.

További információ: [a V2 IoT Central alkalmazás áttelepítésének v3 verzióra](howto-migrate.md).

## <a name="monitor-application-health"></a>Alkalmazásállapot monitorozása

A rendszergazdák IoT Central metrikákat használhatnak a csatlakoztatott eszközök állapotának és az adatexportálások állapotának felméréséhez.

A metrikák megtekintéséhez a rendszergazdák használhatnak diagramokat a Azure Portal, a REST API, a PowerShell vagy az Azure CLI lekérdezésekben.

További információ: [IoT Central alkalmazások általános állapotának figyelése](howto-monitor-application-health.md).

## <a name="tools"></a>Eszközök

A rendszergazdaként használt számos eszköz az egyes IoT Central alkalmazások **Adminisztráció** szakaszában érhető el. A következő eszközöket is használhatja a felügyeleti feladatok elvégzéséhez:

- [Azure CLI](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Azure Portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az Azure IoT Central-alkalmazás felügyeletének módját, a javasolt következő lépés a [felhasználók és szerepkörök kezelése](howto-manage-users-roles.md) az Azure IoT Central-ban című témakörben.
