---
title: Tár diagnosztikai beállításainak konfigurálása nagy méretekben
description: Log Analytics diagnosztikai beállítások konfigurálása egy adott hatókörben lévő összes tárolóhoz a Azure Policy használatával
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 55461937381f7551c42714c835d4755ab65f175b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92171533"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Tár diagnosztikai beállításainak konfigurálása nagy méretekben

A Azure Backup által biztosított jelentéskészítési megoldás Log Analytics (LA). Ahhoz, hogy egy adott tároló adatait el lehessen küldeni a LA-nek, az adott tár számára létre kell hozni egy [diagnosztikai beállítást](./backup-azure-diagnostic-events.md) .

Gyakran előfordulhat, hogy a diagnosztikai beállítások manuális hozzáadásával a tárolóban nehézkes feladat lehet. Emellett a létrehozott új tárolónak is engedélyeznie kell a diagnosztikai beállításokat ahhoz, hogy meg tudja jeleníteni a tár jelentéseit.

A diagnosztikai beállítások méretezésének leegyszerűsítése érdekében a Azure Backup beépített [Azure Policy](../governance/policy/index.yml)biztosít. Ez a házirend egy LA Diagnostics-beállítást hoz létre egy adott előfizetés vagy erőforráscsoport összes tárolóján. A következő szakasz útmutatást nyújt ennek a szabályzatnak a használatáról.

## <a name="supported-scenarios"></a>Támogatott helyzetek

* A szabályzat egyszerre alkalmazható egy adott előfizetésben található összes Recovery Services-tárolóra (vagy az előfizetésben található erőforráscsoporthoz). A házirendet hozzárendelő felhasználónak **tulajdonosi** hozzáféréssel kell rendelkeznie ahhoz az előfizetéshez, amelyhez a szabályzat hozzá van rendelve.

* A felhasználó által megadott LA munkaterület (amely a diagnosztikai adatok küldésére szolgál) különböző előfizetésekben lehet a Szabályzathoz hozzárendelt tárolók között. A felhasználónak rendelkeznie kell **olvasóval**, **közreműködővel** vagy **tulajdonosi** hozzáféréssel ahhoz az előfizetéshez, amelyben a megadott La munkaterület létezik.

* A felügyeleti csoport hatóköre jelenleg nem támogatott.

* A beépített szabályzat jelenleg nem érhető el az országos felhőkben.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Beépített házirend társítása hatókörhöz

Az alábbi lépéseket követve rendelheti hozzá a tárolók szabályzatát a szükséges hatókörben:

1. Jelentkezzen be a Azure Portalba, és navigáljon a **szabályzat** irányítópultra.
2. A bal oldali menüben válassza a **definíciók** lehetőséget, hogy lekérje az Azure-erőforrások összes beépített szabályzatának listáját.
3. A **Kategória = Backup** szolgáltatás listájának szűrése Keresse meg az [előzetes verzió] nevű szabályzatot **: Recovery Services-tároló diagnosztikai beállításainak központi telepítése log Analytics munkaterületre erőforrás-specifikus kategóriákhoz**.

    ![Házirend-definíciós ablaktábla](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Válassza ki a szabályzat nevét. A rendszer átirányítja a szabályzat részletes definícióját.

    ![Részletes szabályzat-definíció](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Kattintson a **hozzárendelés** gombra a panel tetején. A rendszer átirányítja a **házirend-hozzárendelési** panelre.

6. Az **alapok** területen válassza a **hatókör** mező melletti három pontot. Ekkor megnyílik a megfelelő környezeti ablaktábla, ahol kiválaszthatja az előfizetést, amelyre alkalmazni kívánja a szabályzatot. Kiválaszthat egy erőforráscsoportot is, hogy a házirend csak egy adott erőforráscsoport tárolói esetében legyen alkalmazva.

    ![Szabályzat-hozzárendelés alapjai](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. A **Paraméterek** területen adja meg a következő adatokat:

    * **Profilnév** – a házirend által létrehozott diagnosztikai beállításokhoz hozzárendelni kívánt név.
    * **Log Analytics munkaterület** – az a log Analytics munkaterület, amelyhez a diagnosztikai beállítást társítani kell. A szabályzat-hozzárendelés hatókörében lévő összes tároló diagnosztikai adatait a rendszer leküldi a megadott LA munkaterületre.

    * **Kizárási címke neve (nem kötelező) és a kizárási címke értéke (nem kötelező)** – kiválaszthatja, hogy ki kell zárnia egy bizonyos címke nevét és értékét tartalmazó tárolókat a szabályzat-hozzárendelésből. Ha például **nem** szeretné, hogy egy diagnosztikai beállítás hozzá legyen adva a "isTest" címkével rendelkező tárolók számára az "igen" értékre, akkor a kizárási címke **értéke** mezőben a "isTest" karakterláncot kell megadnia a **kizárási címke neve** mezőben, az "igen" értéket. Ha a két mező bármelyike (vagy mindkettő) üresen marad, a szabályzat az összes érintett tárolóra érvényes lesz, függetlenül attól, hogy milyen címkéket tartalmaz.

    ![Szabályzat-hozzárendelési paraméterek](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Szervizelési feladat létrehozása** – ha a szabályzatot egy hatókörhöz rendeli hozzá, a hatókörben létrehozott új tárolók automatikusan beolvassák a beállított La diagnosztikai beállításokat (a tár létrehozásának idejétől számított 30 percen belül). A hatókörben lévő meglévő tárolók diagnosztikai beállításainak hozzáadásához a házirend-hozzárendelési időpontban aktiválhat egy szervizelési feladatot. A Szervizelési feladatok elindításához jelölje be a **szervizelési feladat létrehozása** jelölőnégyzetet.

    ![Szabályzat-hozzárendelés szervizelése](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Navigáljon a **felülvizsgálat + létrehozás** lapra, és válassza a **Létrehozás** lehetőséget.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Milyen feltételek esetén érvényes a Szervizelési feladat egy tárolóra?

A Szervizelési feladatot a szabályzat definíciója szerint meg nem felelő tárakra alkalmazza a rendszer. A tár nem megfelelő, ha megfelel a következő feltételek valamelyikének:

* Nem létezik diagnosztikai beállítás a tárolóhoz.
* A rendszer diagnosztikai beállításokat tartalmaz a tárolóhoz, de egyik beállítás sem rendelkezik az **összes** olyan erőforrás-specifikus eseménysel, amely a La as Destination értékkel van engedélyezve, és az **erőforrás-specifikus** elem van kiválasztva a váltásban.

Így még ha a felhasználó rendelkezik egy olyan tárolóval, amelyen engedélyezve van a AzureBackupReport esemény a AzureDiagnostics módban (amelyet a biztonsági mentési jelentések támogatnak), a Szervizelési feladat továbbra is [érvényes lesz erre](./backup-azure-diagnostic-events.md#legacy-event)a tárolóra, mivel az erőforrás-specifikus mód a diagnosztikai beállítások létrehozásának ajánlott módja.

Ha a felhasználó rendelkezik egy olyan tárolóval, amely a hat erőforrás-specifikus eseménynek csak egy részhalmazát engedélyezi, a Szervizelési feladat erre a tárolóra vonatkozik, mivel a biztonsági mentési jelentések csak a várt módon fognak működni, ha az összes hat erőforrás-specifikus esemény engedélyezve van.

> [!NOTE]
>
> Ha egy tároló rendelkezik egy meglévő diagnosztikai beállítással, amelynek az erőforrás-specifikus kategóriák egy **részhalmaza** engedélyezve van, úgy van konfigurálva, hogy adatokat küldjön egy adott La munkaterületre, azaz a "munkaterület x" értéket, a Szervizelési feladat meghiúsul (csak az adott tár esetében), ha a házirend-hozzárendelésben megadott cél La munkaterület **ugyanaz** a "munkaterület x".
>
>Ennek az az oka, hogy ha az ugyanazon az erőforráson két különböző diagnosztikai beállítás által engedélyezett események valamilyen formában **átfedésben** vannak, akkor a beállítások nem rendelkezhetnek a CÉLKÉNT megadott La munkaterülettel. Ennek a hibának a manuális feloldásához navigáljon a megfelelő tárolóhoz, és konfigurálja a diagnosztikai beállításokat egy másik LA-munkaterülettel a célhelyként.
>
> Vegye figyelembe, hogy a Szervizelési feladat **nem** fog sikerülni, ha a meglévő diagnosztikai beállítás csak az X munkaterület AzureBackupReport engedélyezett, mert ebben az esetben a meglévő beállítás által engedélyezett események és a Szervizelési feladat által létrehozott események között nem lesz átfedés.

## <a name="next-steps"></a>Következő lépések

* [Tudnivalók a biztonsági mentési jelentések használatáról](./configure-reports.md)
* [További információ a Azure Policy](../governance/policy/index.yml)
* [A Azure Policy használata az adott hatókörben lévő összes virtuális gép biztonsági mentésének automatikus engedélyezéséhez](./backup-azure-auto-enable-backup.md)
