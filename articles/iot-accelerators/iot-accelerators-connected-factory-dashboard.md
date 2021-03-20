---
title: A csatlakoztatott gyári irányítópult használata – Azure | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a csatlakoztatott gyári irányítópult szolgáltatásai az ipari IoT-eszközök figyelésére és felügyeletére.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 797bd97940aa49db87b4ca4dd96f140208cd69b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011217"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>A csatlakoztatott Factory megoldás-gyorsító irányítópultján található szolgáltatások használata

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

A [felhőalapú megoldás üzembe helyezése az ipari IoT-eszközökön](quickstart-connected-factory-deploy.md) a gyors útmutató bemutatja, hogyan navigálhat az irányítópulton, és hogyan reagálhat a riasztásokra. Ez a útmutató útmutatást nyújt a további irányítópult-funkciókhoz, amelyek segítségével figyelheti és kezelheti az ipari IoT-eszközöket.

## <a name="apply-filters"></a>Szűrők alkalmazása

Az irányítópulton megjelenített információk a **gyári helyszínek** panelen vagy a **riasztások** panelen is szűrhetők:

1. Kattintson a **tölcsérre** a gyári telephelyek vagy a riasztások panelén elérhető szűrők listájának megjelenítéséhez.

1. Megjelenik a szűrők panel:

    [![A csatlakoztatottgyár-alapú megoldásgyorsító szűrői](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Válassza ki a szükséges szűrőt, és kattintson az **alkalmaz** gombra. Az is lehetséges, hogy szabad szöveget is beírhat a szűrő mezőibe.

1. Ekkor a rendszer alkalmazza a szűrőt. Az extra tölcsér ikon azt jelzi, hogy a rendszer a szűrőt alkalmazza:

    [![Csatlakoztatott gyári megoldás-gyorsító szűrő alkalmazva](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Az aktív szűrő nem befolyásolja a megjelenített OEE és KPI értékeket, csak a lista tartalmát szűri.

1. Egy szűrő törléséhez kattintson a tölcsérre, majd a szűrő panelen kattintson a **Törlés** elemre.

## <a name="browse-an-opc-ua-server"></a>OPC UA-kiszolgáló tallózása

A megoldás-gyorsító telepítésekor automatikusan kiépítheti a szimulált OPC UA-kiszolgálók készletét, amelyeket böngészhet az irányítópulton. A szimulált kiszolgálók megkönnyítik a megoldás-gyorsító kísérletét anélkül, hogy valódi kiszolgálókat kellene üzembe helyezni.

1. Kattintson a **böngésző ikonjára** az irányítópult navigációs sávján:

    [![A csatlakoztatottgyár-alapú megoldásgyorsító kiszolgálótallózója](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Válassza ki az egyik kiszolgálót a listából, amely a megoldás-gyorsító által telepített kiszolgálókat jeleníti meg:

    [![Csatlakoztatott Factory megoldás-gyorsító kiszolgálók listája](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenik egy biztonsági párbeszédablak. A szimulációhoz nyugodtan kattintson a **Folytatás** gombra.

1. Kattintson bármely csomópontra a kiszolgálófán a kibontásához. A telemetria közzétevő csomópontok mellett Pipa látható:

    [![A csatlakoztatottgyár-alapú megoldásgyorsító kiszolgálófája](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Egy csomópont olvasásához, írásához, közzétételéhez vagy meghívásához kattintson jobb gombbal az adott elemre. Az elérhető műveletek a jogosultságaitól és a csomópont attribútumaitól függnek. Az olvasási lehetőség egy helyi panelt jelenít meg, amelyen az adott csomópont értéke látható. Az írási lehetőség egy olyan helyi panelt jelenít meg, amelyen új értéket adhat meg. A hívási lehetőség egy csomópontot jelenít meg, amelyen megadhatja a hívás paramétereit.

## <a name="publish-a-node"></a>Csomópont közzététele

Ha egy *szimulált OPC UA-kiszolgálóhoz* tallóz, új csomópontokat is közzétehet, ha szeretne. Ezeknek a csomópontoknak a telemetriáját is elemezheti a megoldásban. Ezek a *szimulált OPC ua-kiszolgálók* megkönnyítik a megoldás-gyorsító kísérletét valódi eszközök üzembe helyezése nélkül:

1. Lépjen egy közzétenni kívánt csomópontra az OPC UA-kiszolgáló tallózási fáján.

1. Kattintson a jobb gombbal a csomópontra. Kattintson a **Közzététel** gombra:

    [![Csatlakoztatott Factory-megoldás gyorssegéd-közzétételi csomópontja](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. Megjelenik egy helyi panel, amely tájékoztatja, hogy a közzététel sikeres volt. A csomópont az állomás szintű nézetben jelenik meg a mellette lévő pipa:

    [![A csatlakoztatott Factory megoldás-gyorssegéd sikeres közzététele](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Parancs és vezérlés

A csatlakoztatott gyár segítségével közvetlenül a felhőből irányíthatja és felügyelheti ipari eszközeit. A szolgáltatással az eszközök által létrehozott riasztásokra is reagálhat, Például küldhet egy parancsot az eszközre, és megnyithatja a nyomást kioldó szelepet. A rendelkezésre álló parancsokat az OPC UA-kiszolgáló tallózási fájának **StationCommands** csomópontján találja. Ebben a forgatókönyvben megnyit egy nyomáskiegyenlítő szelepet egy müncheni gyártósor összeszerelő állomásán. A parancs-és vezérlési funkció használatához a megoldás-gyorsító üzembe helyezéséhez **rendszergazdai** szerepkörrel kell rendelkeznie:

1. Tallózással keresse meg a **StationCommands** csomópontot az OPC ua-kiszolgáló böngésző fájában a müncheni, gépsor: 0, Assembly Station.

1. Válassza ki a használni kívánt parancsot. Kattintson a jobb gombbal a **OpenPressureReleaseValve** csomópontra. Kattintson a **hívás** gombra:

    [![A csatlakoztatottgyár-alapú megoldásgyorsító hívás parancsa](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Megjelenik egy helyi panel, amely tájékoztatja arról, hogy melyik metódust kell hívnia, és a paraméterek részleteit. Kattintson a **hívás** gombra:

    [![Csatlakoztatott gyári megoldások gyorsító hívási paraméterei](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. A helyi panel frissül, és tájékoztatja róla, ha a metódus meghívása sikeres volt. A hívás sikerességét ellenőrizheti a hívás eredményeképpen frissített nyomásmérő csomópont értékének leolvasásával.

    [![A csatlakoztatottgyár-alapú megoldásgyorsító sikeres hívása](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>A színfalak mögött

Megoldásgyorsító üzembe helyezésekor az üzembehelyezési folyamat több erőforrást hoz létre a kiválasztott Azure-előfizetésben. Ezeket az erőforrásokat az Azure [Portalon](https://portal.azure.com) tekintheti meg. Az üzembehelyezési folyamat létrehoz egy **erőforráscsoportot** a megoldásgyorsítóhoz kiválasztott néven alapuló névvel:

[![Csatlakoztatott Factory megoldás-gyorsító erőforráscsoport](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Az egyes erőforrások beállításainak megtekintéséhez válassza ki az erőforrást az erőforráscsoport erőforráslistájában.

Az [Azure-IOT-Connected-Factory](https://github.com/Azure/azure-iot-connected-factory) GitHub-adattárban megtekintheti a megoldás-gyorsító forráskódját is.

Ha elkészült, törölheti a megoldás-gyorssegédet az Azure-előfizetésből a [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) webhelyen. Így könnyedén törölheti a megoldásgyorsító létrehozásakor megkapott összes erőforrást.

> [!NOTE]
> A megoldás-gyorssegédtel kapcsolatos összes adat törléséhez törölje azt a [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) webhelyen. Ne törölje az erőforráscsoportot a portálon.

## <a name="next-steps"></a>Következő lépések

Most, hogy üzembe helyezett egy működő megoldásgyorsítót, a következő cikkek elolvasásával folytathatja az ismerkedést az IoT-megoldásgyorsítók használatával:

* [A csatlakoztatott gyári megoldás-gyorsító konfigurálása](iot-accelerators-connected-factory-configure.md)
* [Engedélyek a azureiotsolutions.com webhelyen](iot-accelerators-permissions.md)
