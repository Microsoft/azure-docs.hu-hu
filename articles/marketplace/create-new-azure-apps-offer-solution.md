---
title: Megoldási sablon konfigurálása
description: Megtudhatja, hogyan konfigurálhat megoldási sablont az Azure-alkalmazás ajánlatához a partner Centerben.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 8469cad02009d054bd8ba97fb4aabfdae84ef842
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96744618"
---
# <a name="configure-a-solution-template-plan"></a>Megoldási sablon konfigurálása

Ez a cikk csak az Azure-alkalmazások ajánlatának megoldási sablonjaira vonatkozik. Ha felügyelt alkalmazási csomagot konfigurál, lépjen a [felügyelt alkalmazáscsomag konfigurálása](create-new-azure-apps-offer-managed.md)című témakörre.

## <a name="choose-who-can-see-your-plan"></a>Válassza ki, hogy ki láthatja a csomagot

Beállíthatja, hogy az egyes tervek mindenki számára láthatók legyenek, vagy csak egy adott közönség számára. Az Azure-előfizetési azonosítók használatával hozzáférést biztosíthat egy privát célközönségnek, amely tartalmazza az összes hozzárendelt előfizetés-azonosító leírását. A használatával legfeljebb 10 előfizetés-azonosítót adhat meg manuálisan vagy akár 10 000 előfizetés-azonosítóhoz. CSV-fájl. Az Azure-előfizetési azonosítók GUID azonosítóként jelennek meg, és a betűknek kisbetűsnek kell lenniük.

> [!NOTE]
> Ha közzétesz egy privát csomagot, később megtekintheti a nyilvános láthatóságot. Ha azonban egy nyilvános csomagot tesz közzé, a saját láthatósága nem módosítható.

A **rendelkezésre állás** lapon, a **terv láthatósága** területen tegye a következők egyikét:

- A csomag nyilvános beállításához válassza a **nyilvános** választógombot (más néven _választógombot_).
- A csomag magánjellegűvé tételéhez válassza a **privát** beállítás gombot, és adja hozzá manuálisan vagy CSV-fájllal az Azure-előfizetési azonosítókat.

    > [!NOTE]
    > A privát vagy a korlátozott célközönség nem azonos az **Előnézet lapon megadott** előnézeti célközönséggel. Az előnézeti közönség hozzáférhet az ajánlathoz a piactéren közzétett élő állapot előtt. Míg a privát célközönség választása csak egy adott csomagra vonatkozik, az előzetes verzió célközönsége az összes csomagot (magán vagy nem) megtekintheti érvényesítési célból.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Azure-előfizetési azonosítók manuális hozzáadása egy privát csomaghoz

1. A **terv láthatósága** területen kattintson a **privát** beállítás gombra.
1. A megjelenő **Azure előfizetés-azonosító** mezőben adja meg annak a célközönségnek az Azure-ELŐfizetési azonosítóját, amelyhez hozzáférést szeretne adni ehhez a privát csomaghoz. Legalább egy előfizetés-azonosítót kötelező megadni.
1. Választható Adja meg a célközönség leírását a **Leírás** mezőben.
1. Egy másik előfizetés-azonosító hozzáadásához válassza az **azonosító hozzáadása (legfeljebb 10)** hivatkozást, és ismételje meg a 2. és a 3. lépést.

## <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Használja a. CSV-fájl egy privát csomaghoz tartozó Azure-előfizetési azonosítók hozzáadásához

1. A **terv láthatósága** területen kattintson a **privát** beállítás gombra.
1. Válassza ki a **célközönség exportálása (CSV)** hivatkozást.
1. Nyissa meg a t. CSV-fájlt, és adja hozzá azokat az Azure-előfizetési azonosítókat, amelyekhez hozzáférést szeretne adni a privát ajánlathoz az **azonosító** oszlophoz.
1. Szükség esetén megadhatja az egyes célközönségek leírását a **Leírás** oszlopban.
1. Adja hozzá a "SubscriptionId" kifejezést a **Type (típus** ) oszlopban az előfizetés-azonosítóval rendelkező sorokhoz.
1. Mentse a t. CSV-fájl.
1. A **rendelkezésre állás** lapon, a **terv láthatósága** területen válassza ki a **célközönség importálása (CSV)** hivatkozást.
1. A megjelenő párbeszédpanelen válassza az **Igen** lehetőséget.
1. Válassza a elemet. CSV-fájl, majd válassza a **Megnyitás** lehetőséget. Megjelenik egy üzenet, amely jelzi, hogy a. A CSV-fájl importálása sikeresen megtörtént.

### <a name="hide-your-plan"></a>A terv elrejtése

Ha a megoldás sablonja kizárólag központilag telepíthető, ha egy másik megoldási sablon vagy egy felügyelt alkalmazás hivatkozik rá, jelölje be a **csomag elrejtése a terv** közzététele a megoldásban című szakaszban jelölőnégyzetet, de rejtse el azokat az ügyfeleket, akik közvetlenül keresik és keresik meg azokat.

A következő szakasz folytatása előtt válassza a **Piszkozat mentése** lehetőséget: a technikai konfiguráció megadása.

## <a name="define-the-technical-configuration"></a>A technikai konfiguráció megadása

A **technikai konfiguráció** lapon fel kell töltenie azt a központi telepítési csomagot, amely lehetővé teszi, hogy az ügyfelek üzembe helyezsék a csomagot, és megadják a csomag verziószámát.

> [!NOTE]
> Ez a lap nem jelenik meg, ha úgy döntött, hogy újrahasználja a csomagokat egy másik csomagból a **terv beállítása** lapon. Ha igen, lépjen a [csomagok megtekintése](#view-your-plans)elemre.

### <a name="assign-a-version-number-for-the-package"></a>A csomag verziószámának kiosztása

A **verzió** mezőben adja meg a technikai konfiguráció aktuális verzióját. Minden alkalommal növelje ezt a verziót, amikor módosításokat tesz közzé ezen az oldalon. A verziószámnak a (z) Integer. egész_szám. Integer formátumban kell lennie. Például: `1.0.2`.

### <a name="upload-a-package-file"></a>Csomagfájl feltöltése

A **csomagfájl (. zip)** területen húzza a csomagfájl a szürke mezőbe, vagy válassza a **fájl (ok) csatolásának tallózását** .

> [!NOTE]
> Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a `https://upload.xboxlive.com` partner központ által használt szolgáltatást.

### <a name="previously-published-packages"></a>Korábban közzétett csomagok

Miután közzétette az ajánlatát, a **korábban közzétett csomagok** almenü megjelenik a **technikai konfiguráció** lapon. Ezen a lapon a technikai konfiguráció összes korábban közzétett verziója szerepel.

## <a name="view-your-plans"></a>A csomagok megtekintése

- Válassza a **Piszkozat mentése** lehetőséget, majd a lap bal felső részén kattintson a **terv áttekintése** lehetőségre a **terv áttekintő** lapjára való visszatéréshez.

Egy vagy több csomag létrehozása után megtekintheti a csomag nevét, a terv AZONOSÍTÓját, a csomag típusát, a rendelkezésre állást (nyilvános vagy magánjellegű), az aktuális közzétételi állapotot, valamint az összes elérhető műveletet a **terv áttekintése** lapon.

A **terv áttekintés** lapjának **művelet** oszlopában elérhető műveletek a csomag állapotától függően változhatnak, és a következőket foglalhatják magukban:

- Ha a terv állapota **Piszkozat**, a **művelet** oszlopban szereplő hivatkozás a **delete piszkozatot** fogja mondani.
- Ha a terv állapota **élő**, a **művelet** oszlopban található hivatkozás nem fogja **megszüntetni a terv értékesítését** vagy a **privát célközönség szinkronizálását**. A **privát célközönség szinkronizálása** hivatkozás csak a privát célközönségek módosításait teszi közzé anélkül, hogy közzé kellene tenni az ajánlatban esetlegesen végrehajtott egyéb frissítéseket.
- Ha egy másik csomagot szeretne létrehozni ehhez az ajánlathoz, a **terv áttekintése** lap tetején válassza az **+ új terv létrehozása** lehetőséget. Ezután ismételje meg az [Azure-alkalmazási ajánlathoz tartozó csomagok létrehozása](create-new-azure-apps-offer-plans.md)című témakör lépéseit. Ellenkező esetben, ha elkészült a csomagok létrehozásával, ugorjon a következő szakaszra: további lépések.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-alkalmazás ajánlatának tesztelése és közzététele](create-new-azure-apps-offer-test-publish.md).
- Ismerje meg [, hogyan értékesítheti Azure-alkalmazásait](create-new-azure-apps-offer-marketing.md) a Microsofttal való közös értékesítéssel és a CSP-programokkal való viszonteladással.
