---
title: Irányítópultok létrehozása és megosztása az Azure Portalon
description: Ez a cikk a Azure Portal irányítópultok létrehozását, testreszabását, közzétételét és megosztását ismerteti.
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 10c322c9d9d18f7b79a4419bd9efa2e366a5b7c3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745825"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Irányítópultok létrehozása és megosztása az Azure Portalon

Az irányítópultok a Azure Portal Felhőbeli erőforrásainak célzott és rendezett nézetét jelentik. Az irányítópultokat munkaterületként használhatja, ahol gyorsan elindíthatja a napi műveletek feladatait, és figyelheti az erőforrásokat. Egyéni irányítópultokat hozhat létre a projektek, feladatok vagy felhasználói szerepkörök alapján, például:.

A Azure Portal kiindulási pontként egy alapértelmezett irányítópultot biztosít. Szerkesztheti az alapértelmezett irányítópultot. Létrehozhat és testreszabhat további irányítópultokat, és közzéteheti és megoszthatja az irányítópultokat, hogy azok elérhetők legyenek más felhasználók számára. Ez a cikk bemutatja, hogyan hozhat létre egy új irányítópultot, hogyan szabhatja testre a felületet, és hogyan tehet közzé és oszthat meg irányítópultokat.

## <a name="create-a-new-dashboard"></a>Új irányítópult létrehozása

Ebben a példában egy új, privát irányítópultot hozunk létre, és hozzárendelünk egy nevet. A kezdéshez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A Azure Portal menüben válassza az **irányítópult** lehetőséget. Lehetséges, hogy az alapértelmezett nézet már be van állítva az irányítópultra.

    ![Az irányítópult megnyitása](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Válassza az **új irányítópult** lehetőséget.

    ![Képernyőfelvétel az új irányítópultról](./media/azure-portal-dashboards/create-new-dashboard.png)

    Ez a művelet megnyitja a **csempe-tárat**, amelyből kiválaszthatja a csempéket, és egy üres rácsot, ahol a csempéket rendezni fogja.

    ![Képernyőfelvétel a csempe és az üres rácsról](./media/azure-portal-dashboards/dashboard-name.png)

1. Jelölje ki a **saját irányítópult** szövegét az irányítópult címkéjén, és adjon meg egy nevet, amely megkönnyíti az egyéni irányítópultok azonosítását.

1. A szerkesztési mód kilépéséhez válassza az oldal fejlécének **kész Testreszabás** elemét.

Az irányítópult nézet most megjeleníti az új irányítópultot. Válassza az irányítópult neve melletti nyilat az elérhető irányítópultok megtekintéséhez. Előfordulhat, hogy a lista más felhasználók által létrehozott és megosztott irányítópultokat tartalmaz.

## <a name="edit-a-dashboard"></a>Irányítópult szerkesztése

Most szerkesszük az irányítópultot az Azure-erőforrásokat jelölő csempék hozzáadásához, átméretezéséhez és rendezéséhez.

### <a name="add-tiles-from-the-dashboard"></a>Csempék hozzáadása az irányítópultról

A csempék irányítópulthoz való hozzáadásához kövesse az alábbi lépéseket:

1. Válassza ![ az ikon ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **szerkesztése** elemet az oldal fejlécében.

    ![Képernyőkép az irányítópult kijelölésének szerkesztéséről](./media/azure-portal-dashboards/dashboard-edit.png)

1. Tallózással keresse meg a **csempét** , vagy a Keresés mező használatával keresse meg a kívánt csempét.

1. Válassza a **Hozzáadás** lehetőséget, hogy hozzáadja a csempét az irányítópulthoz alapértelmezett mérettel és hellyel. Vagy húzza a csempét a rácsra, és helyezze a kívánt helyre.

> [!TIP]
> Ha egynél több szervezettel dolgozik, vegye fel a **szervezeti identitás** csempét az irányítópultra, hogy egyértelműen megmutassa, melyik szervezethez tartoznak az erőforrások.

### <a name="add-tiles-from-a-resource-page"></a>Csempék hozzáadása egy erőforrás-lapról

A csempék az irányítópulton való hozzáadására is lehetőség van. Számos erőforrás-oldal tartalmaz egy gombostű ikont a parancssorban. Ha az ikont választja, a forrás lapot jelképező csempe a jelenleg aktív irányítópultra van rögzítve. 

![Képernyőkép az oldal parancssáv rögzítési ikonnal](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>Csempék átméretezése vagy átrendezése

A csempe méretének módosításához vagy az irányítópult csempéi átrendezéséhez kövesse az alábbi lépéseket:

1. Válassza ![ az ikon ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **szerkesztése** elemet az oldal fejlécében.

1. Válassza a helyi menüt a csempe jobb felső sarkában. Ezután válassza ki a csempe méretét. A méretet támogató csempék a jobb alsó sarokban lévő "Handle" kifejezést is tartalmazzák, amely lehetővé teszi, hogy a csempét a kívánt méretre húzza.

    ![Képernyőkép az irányítópultról a csempe mérete menü megnyitva](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Válasszon ki egy csempét, és húzza a rács egy új helyére az irányítópult elrendezéséhez.

### <a name="additional-tile-configuration"></a>További csempe-konfiguráció

Előfordulhat, hogy egyes csempék több konfigurációt igényelnek a kívánt információk megjelenítéséhez. Például a **metrikák diagram** csempét úgy kell beállítani, hogy a metrika megjelenjen a **Azure monitor**. Az irányítópult alapértelmezett időbeállításainak felülbírálásához emellett testre is szabhatja a csempét.

Minden olyan csempe, amelyet be kell állítani, egy **csempe** -szalagcímet jelenít meg, amíg testre nem szabja a csempét. A csempe testreszabása:

1. A szerkesztési mód kilépéséhez válassza az oldal fejlécének **kész Testreszabás** elemét.

1. Válassza ki a szalagcímet, majd hajtsa végre a szükséges beállításokat.

    ![A konfigurációt igénylő csempe képernyőképe](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> A Markdown csempe lehetővé teszi az egyéni, statikus tartalmak megjelenítését az irányítópulton. Ez lehet az alapszintű utasítások, a képek, a hiperhivatkozások halmaza, vagy akár kapcsolattartási adatok is. A Markdown csempe használatával kapcsolatos további információkért lásd: [Markdown csempe használata az Azure-irányítópultokon az egyéni tartalmak megjelenítéséhez](azure-portal-markdown-tile.md).

### <a name="customize-tile-data"></a>Csempe-adattárolás testreszabása

Az irányítópulton lévő adatműveletek automatikusan megjelenítik az elmúlt 24 órában végzett tevékenységet. Ha egy másik időtartományt szeretne megjeleníteni ehhez a csempéhez, kövesse az alábbi lépéseket:

1. Válassza a csempe **adatainak testreszabása** a helyi menüben vagy a ![ szűrő ikon ](./media/azure-portal-dashboards/dashboard-filter.png) szűrő elemet a csempe bal felső sarkában.

    ![Képernyőfelvétel a csempe helyi menüjéről](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Jelölje be a jelölőnégyzetet az **irányítópult időbeállításainak felülbírálásához a csempe szintjén**.

    ![Képernyőfelvétel a párbeszédpanelről a csempe-idő beállításainak konfigurálásához](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Válassza ki a csempéhez megjelenítendő időtartományt. Az elmúlt 30 percből az elmúlt 30 napra kiválaszthat, vagy egyéni tartományt is meghatározhat.

1. Válassza ki a megjelenítendő idő részletességét. Egy percen belül bárhol megjelenítheti az egy hónapos növekményeket.

1. Kattintson az **Alkalmaz** gombra.

## <a name="delete-a-tile"></a>Csempe törlése

Ha el szeretne távolítani egy csempét az irányítópultról, kövesse az alábbi lépéseket:

* Válassza a helyi menüt a csempe jobb felső sarkában, majd válassza az **Eltávolítás az irányítópultról** lehetőséget. Vagy

* Válassza ki ![ a szerkesztési ikon ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **szerkesztése** lehetőséget a testreszabási mód megadásához. Vigye a kurzort a csempe jobb felső sarkában, majd válassza a törlés ![ ikon ](./media/azure-portal-dashboards/dashboard-delete-icon.png) Törlés ikont a csempe az irányítópultról való eltávolításához.

   ![Képernyőfelvétel: csempe eltávolítása az irányítópultról](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Irányítópult klónozása

Ha egy meglévő irányítópultot sablonként szeretne használni egy új irányítópulthoz, kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy az irányítópult nézet a másolni kívánt irányítópultot jeleníti meg.

1. Az oldal fejlécében válassza a ![ klónozás ikon ](./media/azure-portal-dashboards/dashboard-clone.png) **klónozás** elemet.

1. A rendszer szerkesztési módban nyitja meg az irányítópult nevét, az *irányítópult neve* **klón** néven. A cikk előző lépéseivel átnevezheti és testreszabhatja az irányítópultot.

## <a name="publish-and-share-a-dashboard"></a>Irányítópult közzététele és megosztása

Amikor létrehoz egy irányítópultot, az alapértelmezés szerint magánjellegű, ami azt jelenti, hogy Ön az egyetlen, aki láthatja. Ahhoz, hogy az irányítópultok mások számára is elérhetők legyenek, közzéteheti és megoszthatja azokat. További információ: Azure- [irányítópultok megosztása az Azure szerepköralapú hozzáférés-vezérlés használatával](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Megosztott irányítópult megnyitása

Megosztott irányítópult megkereséséhez és megnyitásához kövesse az alábbi lépéseket:

1. Válassza az irányítópult neve melletti nyilat.

1. Válassza ki az irányítópultok megjelenített listáját. Ha a megnyitni kívánt irányítópult nem szerepel a felsorolásban:

    1. válassza **a Tallózás minden irányítópult** lehetőséget.

        ![Az irányítópult kiválasztási menüjének képernyőképe](./media/azure-portal-dashboards/dashboard-browse.png)

    1. A **típus** mezőben válassza a **megosztott irányítópultok** lehetőséget.

        ![Képernyőkép az összes irányítópult kijelölési menüjéről](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Válasszon ki egy vagy több előfizetést. Szöveget is megadhat az irányítópultok név szerinti szűréséhez.

    1. Válasszon egy irányítópultot a megosztott irányítópultok listájáról.

## <a name="delete-a-dashboard"></a>Irányítópult törlése

Egy privát vagy megosztott irányítópult végleges törléséhez kövesse az alábbi lépéseket:

1. Válassza ki a törölni kívánt irányítópultot az irányítópult neve melletti listából.

1. Válassza ![ a törlés ikon ](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Törlés** lehetőséget az oldal fejlécében.

1. Privát irányítópult esetén a megerősítő párbeszédpanelen kattintson az **OK gombra** az irányítópult eltávolításához. Megosztott irányítópult esetén a megerősítő párbeszédpanelen jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy a közzétett irányítópultot mások nem fogják megtekinteni. Ez után válassza az **OK** gombot.

    ![Képernyőkép a törlés megerősítéséről](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Következő lépések

* [Azure-irányítópultok megosztása az Azure szerepköralapú hozzáférés-vezérlés használatával](azure-portal-dashboard-share-access.md)
* [Azure-irányítópultok létrehozása programozott módon](azure-portal-dashboards-create-programmatically.md)
