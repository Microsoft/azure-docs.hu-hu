---
title: V2 Azure IoT Central-alkalmazás migrálása v3-re | Microsoft Docs
description: Rendszergazdaként megtudhatja, hogyan telepítheti át a v2 Azure IoT Central alkalmazást a v3-ra
author: troyhopwood
ms.author: troyhop
ms.date: 01/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 735ad7ad9ded6baded59ab3f08e239d1c8376b74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702725"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>Telepítse át a v2 IoT Central alkalmazást a v3-ra

*Ez a cikk a rendszergazdákra vonatkozik.*

Jelenleg új IoT Central-alkalmazás létrehozásakor ez egy v3-alkalmazás. Ha korábban létrehozott egy alkalmazást, attól függően, hogy mikor hozta létre, lehet, hogy v2. Ez a cikk azt ismerteti, hogyan telepítheti át a v2-t egy v3-alkalmazásba, hogy biztosan a legújabb IoT Central funkciókat használja.

Az IoT Central-alkalmazások verziójának azonosításához tekintse meg az [alkalmazásról szóló](howto-get-app-info.md)témakört.

Az alkalmazások v2-ről v3-re való átlépésének lépései a következők:

1. Hozzon létre egy új v3 alkalmazást a v2 alkalmazásból.
1. Konfigurálja a v3 alkalmazást.
1. Törlés a v2 alkalmazásba.

## <a name="create-a-new-v3-application"></a>Új v3-alkalmazás létrehozása

Az áttelepítési varázsló segítségével hozzon létre egy új v3-alkalmazást.

IoT Central nem támogatja az áttelepítést egy meglévő v3 alkalmazásba. A meglévő eszközök automatikus áthelyezéséhez használja az áttelepítési varázslót a v3-alkalmazás létrehozásához.

Az áttelepítési varázsló:

- Létrehoz egy új v3 alkalmazást.
- Ellenőrzi az eszköz sablonjait a v3-vel való kompatibilitás érdekében.
- Az összes eszköz sablonjának másolása az új v3 alkalmazásba.
- Az összes felhasználót és szerepkör-hozzárendelést az új v3 alkalmazásba másolja.

> [!NOTE]
> Annak érdekében, hogy az eszközök a v3-kompatibilisek legyenek, az eszköz sablonjának egyszerű típusai lesznek az objektumok tulajdonságai. Nem kell módosítania az eszköz kódját.

Egy alkalmazásnak a v3-be való migrálása rendszergazdának kell lennie.

1. Az **Adminisztráció** menüben válassza az **áttelepítés v3-alkalmazásba** lehetőséget:

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="Az alkalmazás áttelepítési varázslóját bemutató képernyőkép":::

1. Adja meg az új **alkalmazás nevét** , és szükség esetén módosítsa az automatikusan létrehozott  **URL-címet**. Az URL-cím nem egyezhet meg az aktuális v2-alkalmazás URL-címével. A v2-alkalmazás törlése után azonban később is megváltoztathatja az URL-címet.

1. Válassza **az új v3-alkalmazás létrehozása** lehetőséget.

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="Az alkalmazás áttelepítési varázslójának beállításait bemutató képernyőkép":::

    Az eszköz sablonjainak számától és összetettségtől függően ez a folyamat több percet is igénybe vehet.

    > [!Warning]
    > A v3-alkalmazás létrehozása sikertelen lesz, ha bármelyik sablon olyan mezőket tartalmaz, amelyek számmal kezdődnek, vagy amelyek a következő karakterek bármelyikét tartalmazzák: (,,,,,,,,,,, `+` `*` `?` `^` `$` `(` `)` `[` `]` `{` `}` `|` , `\` ). A v3-alkalmazások által használt DTDL-sablon sémája nem engedélyezi ezeket a karaktereket. Frissítse az eszköz sablonját a probléma megoldásához a v3 verzióra való Migrálás előtt.

1. Ha az új v3-alkalmazás elkészült, válassza **az új alkalmazás megnyitása** lehetőséget a megnyitásához.

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="Az alkalmazás migrálása után az alkalmazás áttelepítési varázslóját bemutató képernyőkép":::

## <a name="configure-the-v3-application"></a>A v3 alkalmazás konfigurálása

Miután létrehozta az új v3 alkalmazást, végezze el a konfigurációs módosításokat, mielőtt az eszközöket a v2-alkalmazásból a v3-alkalmazásba helyezi át.

> [!TIP]
> Szánjon egy kis időt arra, hogy [Ismerkedjen](overview-iot-central-tour.md#navigate-your-application) meg a v3-vel, mivel az a korábbi verziótól eltérő eltéréseket tartalmaz.

Az alábbiakban néhány ajánlott konfigurációs lépést érdemes figyelembe venni:

- [Irányítópultok konfigurálása](howto-add-tiles-to-your-dashboard.md)
- [Az adatexportálás konfigurálása](howto-export-data.md)
- [Szabályok és műveletek konfigurálása](quick-configure-rules.md)
- [Az alkalmazás felhasználói felületének testreszabása](howto-customize-ui.md)

Ha a v3-alkalmazás úgy van konfigurálva, hogy megfeleljen az igényeinek, készen áll arra, hogy az eszközöket a v2-alkalmazásból a v3-alkalmazásba helyezze át.

## <a name="move-your-devices-to-the-v3-application"></a>Helyezze át az eszközöket a v3-alkalmazásba

Ha ez a lépés befejeződik, az összes eszköz csak az új v3-alkalmazással kommunikál.

> [!IMPORTANT]
> Mielőtt áthelyezi az eszközöket a v3-alkalmazásba, törölje a v3-alkalmazásban létrehozott eszközöket.

Ez a lépés az összes meglévő eszközt áthelyezi az új v3-alkalmazásba. Az eszköz adatait nem másolja a rendszer.

Válassza a **minden eszköz áthelyezése** lehetőséget az eszközök áthelyezésének megkezdéséhez. Ennek a lépésnek a végrehajtása több percet is igénybe vehet.

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="Az alkalmazások áttelepítése varázslót megjelenítő képernyőkép az eszközök áthelyezése lehetőséggel":::

Az áthelyezés befejezése után indítsa újra az összes eszközt, és győződjön meg arról, hogy csatlakoznak az új v3 alkalmazáshoz.

> [!WARNING]
> Ne törölje a v3 alkalmazást, mert a v2-alkalmazás már nem működőképes.

## <a name="delete-your-old-v2-application"></a>A régi v2-alkalmazás törlése

Miután ellenőrizte, hogy minden a vártnak megfelelően működik-e az új v3-alkalmazásban, törölje a régi v2 alkalmazást. Ez a lépés biztosítja, hogy a már nem használt alkalmazásokért ne legyen számlázás.

> [!Note]
> Egy alkalmazás törléséhez engedélyekkel kell rendelkeznie az alkalmazás létrehozásakor kiválasztott Azure-előfizetésben lévő erőforrások törléséhez. További információ: [szerepköralapú hozzáférés-vezérlés használata az Azure-előfizetések erőforrásaihoz való hozzáférés kezeléséhez](../../role-based-access-control/role-assignments-portal.md).

1. A v2-alkalmazásban válassza a menü **Adminisztráció** lapját.
2. Válassza a **Törlés** lehetőséget a IoT Central alkalmazás végleges törléséhez. Ezzel a beállítással véglegesen törölheti az adott alkalmazáshoz társított összes adatmennyiséget.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az alkalmazás áttelepítését, a javasolt következő lépés az [Azure IoT Central felhasználói felületének](overview-iot-central-tour.md) áttekintése, amelyből megtudhatja, hogy mi változott a v3-as verzióban.