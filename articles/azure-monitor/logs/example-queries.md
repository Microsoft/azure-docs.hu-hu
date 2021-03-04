---
title: A Log Analytics a Azure Monitor a saját lekérdezések kiindulási pontként használható lekérdezési példákat kínál.
description: Az igényeinek megfelelő lekérdezések
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 06/16/2020
ms.openlocfilehash: e4f20032febe1c4afe9cb0964c1b448eaa018103
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030851"
---
# <a name="example-queries-in-azure-monitor-log-analytics"></a>Példák lekérdezésekre Azure Monitor Log Analytics
Log Analytics olyan lekérdezési példákat kínál, amelyek a saját vagy a saját lekérdezések kiindulási pontként használhatók. Ez a cikk példaként szolgáló lekérdezéseket és azok használatát ismerteti.

Ha nem ismeri a Log Analytics vagy a KQL lekérdezési nyelvét, a lekérdezéseket például nagyszerű módon indíthatja el. Azonnali betekintést biztosíthatnak egy erőforrásba, és nagyszerű módszert biztosítanak a KQL tanulásának és használatának megkezdésére, így lerövidítve a Log Analytics használatának megkezdéséhez szükséges időt. Összegyűjtöttünk és gyűjtöttünk több mint 250 példát az azonnali érték biztosítására tervezett lekérdezésekről, valamint arról, hogy a lekérdezések száma folyamatosan növekszik.

## <a name="in-context-queries"></a>Helyi lekérdezések

Az új élmény szűrők és a lekérdezések a kontextusban. Más szóval a rendszer automatikusan csak a kiválasztott hatókörhöz kapcsolódó lekérdezéseket jeleníti meg.

- **Egyetlen erőforrás** esetében – a lekérdezések az erőforrástípus szerint vannak szűrve.
- Egy **erőforráscsoport** esetében – a lekérdezések az adott erőforráscsoport erőforrásai alapján vannak szűrve.
- **Munkaterület** esetén – a lekérdezések a munkaterületen telepített megoldások szerint vannak szűrve.

Ez a viselkedés konzisztens az összes Log Analytics hatókörnél. Ha nem jelenik meg a kívánt erőforrástípus lekérdezése, akkor előfordulhat, hogy a szűrők a környezet kontextusa miatt vannak kialakítva. Egy későbbi szakasz leírja, hogyan távolíthatja el a környezet hatókörét, így megtekintheti az összes lehetséges lekérdezést.

> [!TIP]
> Minél több erőforrás van a hatókörében, annál hosszabb ideig, amíg a portál szűrni és megjeleníteni szeretné a lekérdezési párbeszédpanelt.

## <a name="example-query-user-interface"></a>Példa felhasználói felület lekérdezésére

A lekérdezéseket két különböző helyről is elérheti.

### <a name="example-query-dialog"></a>Példa lekérdezési párbeszédpanelre

Amikor először megadja a Log Analytics élményt, a *lekérdezési példa* automatikusan megjelenik.  A képernyő jobb felső részén található **lekérdezésekre** kattintva is elérhető.

![Oldalsáv – példa lekérdezések](media/example-queries/sidebar-2.png)

Ekkor megjelenik a példa lekérdezési párbeszédpanel, ahogy az alábbi ábrán látható:  

![Példa a lekérdezésekre képernyő](media/example-queries/example-query-start.png)

Az előző képernyőképen megjelenik egy Azure Key Vault példány naplók képernyője. A cikkben korábban említettek szerint a lekérdezések a kontextusban láthatók.  Ennek eredményeképpen a képernyőkép csak Key Vault kapcsolódó példákat jelenít meg. Ha kijelöl egy teljes előfizetést, akkor megjelenik az előfizetésben található összes erőforrástípus lekérdezése.  

Az egyes példák lekérdezéseit egy kártya jelöli. A lekérdezéseket gyorsan átvizsgálva megkeresheti a szükséges igényeket. A lekérdezést közvetlenül a párbeszédablakból is futtathatja, vagy a további finomhangoláshoz és finomhangoláshoz is betöltheti a lekérdezést a lekérdezés-szerkesztőbe.

### <a name="sidebar-query-experience"></a>Oldalsáv-lekérdezési élmény

A párbeszédpanel teljes funkcionalitása a Log Analytics bal oldali oldalsávjának lekérdezések paneljéről érhető el. A lekérdezési Leírás és a további funkciók megjelenítéséhez vigye a kurzort a lekérdezés nevére.

![A lekérdezések ablaktáblát megjelenítő képernyőkép.](media/example-queries/sidebar-3.png)

## <a name="finding-and-filtering-queries"></a>Lekérdezések keresése és szűrése

Az ebben a szakaszban található beállítások a párbeszédablakban és a Sidebar lekérdezési élményében is elérhetők, de egy kicsit eltérő felhasználói felülettel.  

### <a name="use-favorites"></a>Kedvencek használata

A kedvenc gyakran használt lekérdezésekkel gyorsabb hozzáférést biztosíthat.

> [!TIP]
> A lekérdezések összegyűjtése és megtekintése később jó módszer a kezdéshez. Keresse meg a szükséges lekérdezéseket, és kattintson a lekérdezés melletti csillagra a kedvencek hozzáadásához. Ha a későbbiekben úgy találja, hogy a lekérdezés nem hasznos, akkor a Kedvencek közül választhat.  

### <a name="filtering-and-group-by"></a>Szűrés és csoportosítás

Amíg a lekérdezési párbeszédpanel úgy látja a filers-t, hogy csak a megfelelő kontextusú lekérdezéseket jelenítse meg, dönthet úgy, hogy eltávolítja a szűrőt, és megjeleníti az összes lekérdezést.

### <a name="group-by"></a>Csoportosítási szempont

Módosítsa a lekérdezések csoportosítását úgy, hogy rákattint a *Csoportosítás* legördülő listára:

![Példa lekérdezések képernyő groupby](media/example-queries/example-query-groupby.png)

A párbeszédpanel támogatja a csoportosítást:

- **Erőforrástípus** – az Azure-ban definiált erőforrás, például egy virtuális gép. A Azure Monitor naplók/Log Analytics táblák erőforrás-típusra való teljes leképezéséhez tekintse meg a [Azure monitor Table referenciát](/azure/azure-monitor/reference/tables/tables-resourcetype) .  
- **Kategória** – olyan típusú információk, mint például a *Biztonság* vagy a *naplózás*. A kategóriák azonosak a táblák oldalsó ablaktábláján definiált kategóriákkal. A kategóriák teljes listájáért tekintse meg a [Azure monitor Table referenciát](/azure/azure-monitor/reference/tables/tables-category) .  
- **Megoldás** – a lekérdezésekhez társított Azure monitor megoldás
- **Témakör** – a példa lekérdezésének témája, például a *tevékenység naplói* vagy az *alkalmazás naplói*. A témakör tulajdonság egyedi a lekérdezésekhez, és az adott erőforrástípus alapján eltérő lehet.

A csoportosítási értékek szintén aktív tartalomjegyzékként működnek. Ha a képernyő bal oldalán lévő értékekre kattint, a lekérdezések nézet jobbra görgeti a rákattintott elemre.

### <a name="filter"></a>Szűrő

A lekérdezéseket a korábban említett **csoportosítási** értékek alapján is szűrheti. A példában a lekérdezés párbeszédpanelen a szűrők felül találhatók.

![Példa lekérdezési képernyő-szűrőre](media/example-queries/example-query-filter.png)

### <a name="combining-group-by-and-filter"></a>Csoportosítás és szűrés kombinációja

A szűrési és csoportosítási funkciók párhuzamosan működnek. Rugalmasan biztosítják a lekérdezések elrendezését. Ha például több erőforrással rendelkező erőforráscsoportot használ, előfordulhat, hogy egy adott erőforrástípus alapján szeretne szűrni, és az eredményül kapott lekérdezéseket témakör szerint rendezi.

## <a name="sample-query-dialog-appearance-behavior"></a>Példa lekérdezési párbeszédpanel megjelenési viselkedésére

Ha Ön KQL profi, és közvetlenül a lekérdezés-szerkesztőhöz szeretne jutni, a lekérdezési párbeszédpanelt "off" értékre állíthatja. A kikapcsoláskor a példában szereplő lekérdezési párbeszédpanel nem töltődik be, amikor Log Analytics képernyő betöltődik.

![Kikapcsolt példák](media/example-queries/examples-on-off.png)

A minta lekérdezés előugró felületét mindig elérheti a Log Analytics felső sávján található *lekérdezések* gomb használatával.

## <a name="query-explorer"></a>Lekérdezéskezelő

A felhasználó által generált lekérdezések mentésére és megosztására szolgáló Query Explorer felhasználói felülete változatlan marad.

## <a name="next-steps"></a>Következő lépések

[Ismerkedés a KQL-lekérdezésekkel](./get-started-queries.md)