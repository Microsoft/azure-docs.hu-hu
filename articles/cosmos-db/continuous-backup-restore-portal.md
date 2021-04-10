---
title: A folyamatos biztonsági mentés és az időponthoz tartozó visszaállítás konfigurálása a Azure Cosmos DB Azure Portal használatával.
description: Megtudhatja, hogyan azonosíthatja a visszaállítási pontot, és hogyan konfigurálhatja a folyamatos biztonsági mentést Azure Portal használatával. Bemutatja, hogyan lehet visszaállítani egy élő és törölt fiókot.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ee6eedbc078e1b9c07ed00922ce1c37b38410128
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381868"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-portal"></a>A folyamatos biztonsági mentés és az időponthoz tartozó visszaállítás (előzetes verzió) konfigurálása és kezelése – Azure Portal
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> A Azure Cosmos DB időponthoz tartozó visszaállítási funkciója (folyamatos biztonsági mentési mód) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB az időponthoz tartozó visszaállítási funkció (előzetes verzió) segítségével helyreállíthatja a tárolók véletlen változását, a törölt fiókok, adatbázisok vagy tárolók visszaállítását, illetve bármely régióba való visszaállítást (ahol a biztonsági másolatok léteztek). A folyamatos biztonsági mentési mód lehetővé teszi, hogy az elmúlt 30 napban bármikor visszaállítsa az adott időpontra.

Ez a cikk a visszaállítási pont azonosítását és a folyamatos biztonsági mentés konfigurálását ismerteti Azure Portal használatával.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Folyamatos biztonsági mentéssel rendelkező fiók kiépítése

Új Azure Cosmos DB-fiók létrehozásakor a **biztonsági mentési házirend** beállításnál válassza a **folyamatos** mód lehetőséget az új fiók időpontra vonatkozó visszaállítási funkciójának engedélyezéséhez. Miután a funkció engedélyezve lett a fiókhoz, az összes adatbázis és tároló elérhető a folyamatos biztonsági mentéshez. Az időponthoz tartozó visszaállítással a rendszer mindig új fiókba állítja vissza az adatforrásokat, jelenleg nem állítható vissza meglévő fiókra.

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="Folyamatos biztonsági mentési konfigurációval rendelkező Azure Cosmos DB fiók kiépítése." border="true":::

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>Élő fiók visszaállítása véletlen módosításból

A Azure Portal használatával visszaállíthat egy élő fiókot vagy a hozzá tartozó kiválasztott adatbázisokat és tárolókat. Az alábbi lépéseket követve állíthatja vissza az adatait:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/)
1. Navigáljon a Azure Cosmos DB-fiókjához, és nyissa meg az **időpontot az idő visszaállítása** panelen.

   > [!NOTE]
   > A Azure Portal visszaállítási panelje csak akkor van kitöltve, ha rendelkezik `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` engedéllyel. Az engedély beállításával kapcsolatos további tudnivalókért tekintse meg a [biztonsági mentési és visszaállítási engedélyek](continuous-backup-restore-permissions.md) című cikket.

1. A visszaállításhoz adja meg a következő adatokat:

   * **Visszaállítási pont (UTC)** – az elmúlt 30 napban megadott időbélyeg. A fióknak léteznie kell az időbélyegen. Megadhatja a visszaállítási pontot UTC szerint. Ha vissza kívánja állítani, a másodikhoz is megközelítheti a szolgáltatást. A **kattintson ide hivatkozásra kattintva** kérjen segítséget a [visszaállítási pont azonosításához](#event-feed).

   * **Location (hely** ) – a célhely, ahol a fiók vissza van állítva. A fióknak ebben a régióban kell lennie az adott időbélyegzőn (például USA nyugati régiója vagy az USA keleti régiója). Egy fiók csak azokra a régiókba állítható vissza, amelyekben a forrásoldali fiók létezett.

   * **Erőforrás-visszaállítás** – kiválaszthatja a **teljes fiókot** vagy egy **kijelölt adatbázist/tárolót** a visszaállításhoz. Az adatbázisoknak és a tárolóknak léteznie kell a megadott időbélyegzőn. A visszaállítási pont és a hely kiválasztása alapján a visszaállítási erőforrások fel vannak töltve, ami lehetővé teszi, hogy a felhasználó kiválassza a visszaállítani kívánt adatbázisokat vagy tárolókat.

   * **Erőforráscsoport** – az az erőforráscsoport, amelyben a rendszer létrehozza és visszaállítja a célként megadott fiókot. Az erőforráscsoport már léteznie kell.

   * **Cél fiók visszaállítása** – a cél fiók neve. A célként megadott fióknév ugyanazokat az irányelveket kell követnie, mint amikor új fiókot hoz létre. Ezt a fiókot a visszaállítási folyamat fogja létrehozni abban a régióban, ahol a forrásoldali fiók létezik.
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="Élő fiók visszaállítása véletlen módosításból Azure Portal." border="true":::

1. Miután kiválasztotta a fenti paramétereket, kattintson a **Submit (Küldés** ) gombra a visszaállítás elindításához. A visszaállítási költség egy egyszeri díj, amely az adott régióban tárolt adatok mennyiségétől és díjaitól függ. További információt a [díjszabás](continuous-backup-restore-introduction.md#continuous-backup-pricing) című szakaszban talál.

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>Az Event feed használata a visszaállítási idő azonosításához

Ha a Azure Portal a visszaállítási pont időpontjának kitöltésekor segítségre van szüksége a visszaállítási pont azonosításához, válassza a **kattintson ide** hivatkozásra, és az esemény-hírcsatorna panelre lép. Az esemény-hírcsatorna teljes körű megbízhatósági listát biztosít a fiók adatbázisaiban található adatbázisok és tárolók létrehozásához, cseréjéhez és törléséhez. 

Ha például egy bizonyos tároló törlése vagy frissítése előtt szeretné visszaállítani a pontot, ellenőrizze ezt az eseményt. Az események kronológiailag csökkenő sorrendben jelennek meg a bekövetkezett időszakokban, a legutóbbi eseményekkel együtt. Böngészhet az eredmények között, és kiválaszthatja az esemény előtt vagy után megjelenő időt az idő további szűkítéséhez.

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="Az esemény-hírcsatorna használatával azonosíthatja a visszaállítási pont idejét." border="true":::

> [!NOTE]
> Az esemény-hírcsatorna nem jeleníti meg az elemek erőforrásainak módosításait. Az elmúlt 30 napban manuálisan is megadhatja az időbélyeget (ha a fiók létezik ebben az időben) a visszaállításhoz.

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>Törölt fiók visszaállítása

A törölt fiókokat a törléstől számított 30 napon belül teljesen vissza lehet állítani a Azure Portal használatával. A törölt fiókok visszaállításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/)
1. Keressen *Azure Cosmos db* erőforrásokat a globális keresési sávon. Felsorolja az összes meglévő fiókját.
1. Ezután kattintson a **visszaállítás** gombra. A visszaállítás ablaktábla megjeleníti a törölt fiókok listáját, amelyek visszaállíthatók a megőrzési időtartamon belül, amely a törlési idő 30 napja.
1. Válassza ki a visszaállítani kívánt fiókot.

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="Törölt fiók visszaállítása Azure Portalból." border="true":::

   > [!NOTE]
   > Megjegyzés: a Azure Portal visszaállítás ablaktáblája csak akkor van kitöltve, ha rendelkezik `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` engedéllyel. Az engedély beállításával kapcsolatos további tudnivalókért tekintse meg a [biztonsági mentési és visszaállítási engedélyek](continuous-backup-restore-permissions.md) című cikket.

1. Válassza ki a visszaállítani kívánt fiókot, és adja meg a következő adatokat a Törölt fiók visszaállításához:

   * **Visszaállítási pont (UTC)** – az elmúlt 30 napban megadott időbélyeg. A fióknak léteznie kell az időbélyegen. Adja meg a visszaállítási pontot UTC szerint. Ha vissza kívánja állítani, a másodikhoz is megközelítheti a szolgáltatást.

   * **Location (hely** ) – a cél régió, ahová a fiókot vissza kell állítani. A forrásoldali fióknak ebben a régióban kell lennie a megadott időbélyegen belül. Például az USA nyugati régiója vagy az USA keleti régiója.  

   * **Erőforráscsoport** – az az erőforráscsoport, amelyben a rendszer létrehozza és visszaállítja a célként megadott fiókot. Az erőforráscsoport már léteznie kell.

   * **Cél fiók visszaállítása** – a cél fiók nevének követnie kell ugyanazokat az irányelveket, mint amikor új fiókot hoz létre. Ezt a fiókot a visszaállítási folyamat fogja létrehozni abban a régióban, ahol a forrásoldali fiók létezik.

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>A visszaállítási művelet állapotának nyomon követése

A visszaállítási művelet megkezdése után válassza az **értesítési** harang ikont a portál jobb felső sarkában. Ez a hivatkozás a visszaállított fiók állapotát jeleníti meg. Ha a visszaállítás folyamatban van, a rendszer *létrehozza* a fiók állapotát, miután a visszaállítási művelet befejeződik, majd a fiók állapota *online* értékre változik.

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="A visszaállított fiók állapota a létrehozásról a online állapotra változik, ha a művelet befejeződött." border="true":::

## <a name="next-steps"></a>Következő lépések

* A folyamatos biztonsági mentés konfigurálása és kezelése az [Azure CLI](continuous-backup-restore-command-line.md), a [PowerShell](continuous-backup-restore-powershell.md)vagy a [Azure Resource Manager](continuous-backup-restore-template.md)használatával.
* [Folyamatos biztonsági mentési mód erőforrás-modellje](continuous-backup-restore-resource-model.md)
* [Kezelheti](continuous-backup-restore-permissions.md) az adathelyreállításhoz szükséges engedélyeket a folyamatos biztonsági mentési módban.
