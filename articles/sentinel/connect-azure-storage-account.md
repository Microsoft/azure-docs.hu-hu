---
title: Az Azure Storage-fiók diagnosztikai naplóinak csatlakoztatása a Azure Sentinel
description: Megtudhatja, hogyan csatlakoztathatja Azure Policy Azure Storage-fiók diagnosztikai naplóit a Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: b4260d31b587f2a20d7bc9d4c4e3e6a0d225a416
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879129"
---
# <a name="connect-azure-storage-account-diagnostics-logs"></a>Az Azure Storage-fiók diagnosztikai naplóinak csatlakoztatása

Az Azure Storage-fiók egy felhőalapú megoldás a modern adattárolási forgatókönyvekhez. Ez tartalmazza az összes adatobjektumot: blobokat, fájlokat, üzenetsorokat, táblákat és lemezeket.

Ez az összekötő lehetővé teszi az Azure Storage-fiókok diagnosztikai naplóinak streamelését a Azure Sentinel-ba, így folyamatosan monitorozhatja a tevékenységeket, és észlelheti a biztonsági fenyegetéseket a szervezet összes Azure Storage-erőforrásában.

További információ az [Azure Storage-fiók figyelésről.](../storage/common/storage-analytics-logging.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure Storage-fiók diagnosztikai naplóinak a következő Azure Sentinel:

- Olvasási és írási engedélyekkel kell rendelkeznie a Azure Sentinel munkaterületen.

- Ha naplóstreamelési szabályzatot Azure Policy Azure Storage-erőforrásokra, akkor a szabályzat-hozzárendelés hatókörében tulajdonosi szerepkörrel kell lennie.

## <a name="connect-to-azure-storage-account"></a>Csatlakozás Azure Storage-fiókhoz

Ez az összekötő Azure Policy egyetlen naplóstreamelési konfigurációt alkalmaz a hatókörként meghatározott Azure Storage-fiókerőforrások gyűjteményére. Az elérhető Azure Storage-naplók és metrikatípusok az összekötő oldal bal oldalán, az **Adattípusok alatt érhetők el.**

1. A navigációs Azure Sentinel válassza az **Adat-összekötők lehetőséget.**

1. Válassza **ki az Azure Storage-fiókot** az adat-összekötők katalógusából, majd az előnézeti panelen válassza az Összekötő **oldal** megnyitása lehetőséget.

1. Az összekötő **lap Konfiguráció** szakaszában bontsa ki a Stream diagnosztikai naplóit az **Azure Storage-fiókból nagy méretekben.**

1. Válassza a **Hozzárendelés Azure Policy indítása gombot.**

    Megnyílik a szabályzat-hozzárendelési varázsló, amely készen áll egy új, Configure diagnostic settings for storage accounts to Log Analytics workspace (Diagnosztikai beállítások konfigurálása a tárfiókok számára a **Log Analytics-munkaterületen) nevű új szabályzat létrehozására.**

    1. Az Alapvető **beállítások lapon** kattintson a Hatókör melletti  három ponttal a gombra az előfizetés (és opcionálisan egy erőforráscsoport) kiválasztásához. Leírást is hozzáadhat.

    1. A Paraméterek **lapon:**
        - Válassza ki Azure Sentinel munkaterületét a **Log Analytics-munkaterület** legördülő listából.
        - Válassza ki a **Tárolási** szolgáltatások üzembe helyezni kívánt legördülő listából azokat a tárolási erőforrástípusokat (fájl, tábla, üzenetsor stb.), amelyekre telepíteni szeretné a diagnosztikai beállításokat.
        - Hagyja meg **a Beállítás neve** és az **Hatás** mezőket.
        - A többi legördülő mező az elérhető diagnosztikai naplót és metrikatípusokat képviseli. Hagyja a "True" (Igaz) jelölést az összes beni kívánt típusnál.

    1. A fenti lépések az összes jövőbeli tárolási erőforrásra alkalmazni fogják a szabályzatot. A szabályzat meglévő erőforrásokra való  alkalmazáshoz válassza a Szervizelés lapot, és jelölje be **a Szervizelési** feladat létrehozása jelölőnégyzetet.

    1. Az Áttekintés **+ létrehozás lapon** kattintson a Létrehozás **elemre.** A szabályzat most már hozzá van rendelve a kiválasztott hatókörhöz.

> [!NOTE]
>
> Ezzel az adatösszekötővel a kapcsolat állapotjelzői (az adatösszekötők katalógusának színsávja és az adattípusok neve melletti kapcsolati ikonok) csak akkor jelennek meg *csatlakoztatottként* (zöld), ha az adatokat az elmúlt 14 napban már betöltötték. Ha 14 nap telt el adatbevallás nélkül, az összekötő leválasztottként fog mutatni. Amint több adat érkezik, *a csatlakoztatott* állapot vissza fog térni.

## <a name="next-steps"></a>Következő lépések

Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja az Azure Storage-fiókot Azure Sentinel. A további Azure Sentinel a következő cikkekben talál:

- Ismerje meg, [hogyan lehet betekintést szerezni az adatokba és a lehetséges fenyegetésekbe.](quickstart-get-visibility.md)
- A fenyegetések [észlelésének első Azure Sentinel.](tutorial-detect-threats-built-in.md)
