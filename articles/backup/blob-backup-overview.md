---
title: Az Azure-Blobok működési biztonsági mentésének áttekintése
description: Ismerje meg az Azure-Blobok működés közbeni biztonsági mentését (előzetes verzió).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 1f6bc98b92a2af4b05cae766a2186f2970b7133c
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558754"
---
# <a name="overview-of-operational-backup-for-azure-blobs-in-preview"></a>Azure-Blobok operatív biztonsági mentésének áttekintése (előzetes verzió)

A Blobok működési biztonsági mentése egy felügyelt, helyi adatvédelmi megoldás, amely lehetővé teszi a blokk Blobok védelmét a különböző adatvesztési forgatókönyvek, például a korrupció, a Blobok törlése, valamint a véletlen tárolási fiókok törlésével szemben. Az adattárolást a forrás Storage-fiókban helyileg tárolják, és szükség esetén a kiválasztott időpontra lehet visszaállítani. Így egyszerű, biztonságos és költséghatékony eszközöket biztosít a Blobok védelméhez.

A Blobok működési biztonsági mentése a [Backup Center](backup-center-overview.md)szolgáltatással együtt integrálható a biztonsági mentési felügyeleti funkciók között, így egyetlen üvegtábla biztosítható, amely a biztonsági másolatok szabályozására, figyelésére, üzemeltetésére és elemzésére használható.

## <a name="how-operational-backup-works"></a>Működési biztonsági mentés működése

A Blobok operatív biztonsági mentése egy **helyi biztonsági mentési** megoldás. Így a biztonsági mentési adatforgalom nem a Backup-tárolóba kerül, hanem maga a forrás Storage-fiókban tárolódik. A Backup-tároló azonban továbbra is a biztonsági mentések kezelése egységként szolgál. Emellett ez egy **folyamatos biztonsági mentési** megoldás, ami azt jelenti, hogy nem kell biztonsági mentést ütemeznie, és az összes módosítást a rendszer megőrzi és visszaállítható az állapotból egy kiválasztott időpontban.

Az operatív biztonsági mentés blob platform-képességeket használ az adatai védelme érdekében, és szükség esetén lehetővé teszi a helyreállítást:

- **Időponthoz tartozó visszaállítás**: a [blob-időponthoz tartozó visszaállítás](../storage/blobs/point-in-time-restore-overview.md) lehetővé teszi a blob-Adathelyreállítás korábbi állapotba való visszaállítását. Ez viszont a **Soft delete** használatával **módosítja a hírcsatornák** és a **Blobok verziószámozását** , hogy megőrizze az adatmennyiséget a megadott időtartamra. Az üzemeltetési biztonsági mentés gondoskodik az időponthoz való visszaállítás engedélyezéséről, valamint az alapul szolgáló képességekről, hogy biztosítsa az adatmegőrzést a megadott időtartamra.

- **Zárolás törlése**: a törlési zárolás megakadályozza a Storage-fiók törlését véletlenül vagy jogosulatlan felhasználóktól. A konfigurált működési biztonsági mentés automatikusan törli a törlési zárolást is, hogy csökkentse a Storage-fiókok törlése miatti adatvesztés lehetőségét.

A jelenlegi megoldás korlátainak megismeréséhez tekintse meg a [támogatási mátrixot](blob-backup-support-matrix.md) .

### <a name="protection"></a>Védelem

Az operatív biztonsági mentés a **Storage-fiók** szintjén van konfigurálva és felügyelve, és a Storage-fiókban lévő összes blokk blobra vonatkozik. Az operatív biztonsági mentés egy **biztonsági mentési szabályzattal** kezeli azt az időtartamot, ameddig a biztonsági mentési adatok (beleértve a régebbi verziókat és a törölt blobokat) meg kell őrizni, így azt az időszakot kell meghatározni, amelyből az adatok visszaállíthatók. A biztonsági mentési szabályzat legfeljebb 360 nap, vagy a teljes hét (51) vagy hónap (11) megfelelő számú lehet.

Ha a biztonsági mentést egy Storage-fiókhoz konfigurálja, és olyan biztonsági mentési szabályzatot rendel, amely "n" nap megtartásával rendelkezik, az alapul szolgáló tulajdonságok az alább leírtak szerint vannak beállítva. Ezek a tulajdonságok a Storage-fiók blob szolgáltatásának **Adatvédelem** lapján tekinthetők meg.

- **Időponthoz tartozó visszaállítás**: állítsa n napra a biztonsági mentési Szabályzatban meghatározottak szerint. Ha a Storage-fiókhoz már van engedélyezve a (z) "x" nap, a biztonsági mentés konfigurálása előtt, az időponthoz beállított visszaállítás időtartama a két érték közül a kettőnél nagyobb (n, x) lesz. Ha már engedélyezte az időponthoz való visszaállítást, és a biztonsági mentési szabályzatban megadott megőrzési érték nagyobb, mint az, akkor változatlan marad.

- Helyreállítható **Törlés**: állítsa "n + 5" napra, azaz öt napra a biztonsági mentési szabályzatban megadott időtartamon felül. Ha az Operational Backup szolgáltatáshoz konfigurált Storage-fiók már tartalmazta a (z) "y" nap megtartását, akkor a helyreállítható törlés megőrzése a következő két értékre lesz beállítva: legfeljebb (n + 5, y). Ha már engedélyezte a helyreállított törlést, és a biztonsági mentési szabályzatnak megfelelően meghaladta a megőrzést, akkor változatlan marad.

- **A blobok és a Blobok változási csatornájának verziószámozása**: a verziószámozás és a módosítási csatorna engedélyezve van az operatív biztonsági mentéshez konfigurált Storage-fiókok esetében.

- **Zárolás törlése**: az operatív biztonsági mentés konfigurálása a Storage-fiókban a törlési zárolást is alkalmazza a Storage-fiókon. A biztonsági mentés által alkalmazott törlési zárolás a Storage-fiók **zárolások** lapján tekinthető meg.

Annak engedélyezéséhez, hogy a biztonsági mentés engedélyezze ezeket a tulajdonságokat a Storage-fiókok védelme érdekében, a Backup-tárolónak meg kell adni a **Storage-fiók biztonsági mentési közreműködői** szerepkörét a megfelelő tárolási fiókokban.

>[!NOTE]
>Az operatív biztonsági mentés csak blokk-Blobok esetén támogatja a műveleteket, és a tárolón lévő műveletek nem állíthatók vissza. Ha töröl egy tárolót a Storage-fiókból a **tároló törlése** művelet meghívásával, a tároló nem állítható vissza visszaállítási művelettel. Javasoljuk, hogy engedélyezze az adatvédelmet és a helyreállítást a Soft delete használatával.

### <a name="management"></a>Kezelés

Miután engedélyezte a biztonsági mentést egy Storage-fiókon, a Backup-tárolóban a Storage-fióknak megfelelő biztonsági mentési példány jön létre. Bármilyen biztonsági mentéssel kapcsolatos műveletet elvégezhet egy olyan Storage-fiókhoz, mint például a visszaállítások, a figyelés, a védelem leállítása és így tovább, a hozzá tartozó biztonsági mentési példányon keresztül.

Az operatív biztonsági mentés közvetlenül a Backup Center szolgáltatással is integrálható, így az összes Storage-fiók központilag kezelhető, valamint az összes többi támogatott biztonsági mentési feladattal. A Backup Center az összes biztonsági mentési követelmény, például a feladatok figyelése, a biztonsági mentések és-visszaállítások, a megfelelőség és az irányítás, a biztonsági másolatok használatának elemzése, valamint az adatok biztonsági mentésére és helyreállítására vonatkozó műveletek végrehajtása.

### <a name="restore"></a>Visszaállítás

Az adatok bármikor visszaállíthatók, amíg a helyreállítási pont létezik. A rendszer akkor hozza létre a helyreállítási pontot, ha egy Storage-fiók védett állapotban van, és az adatok visszaállítására szolgál, ha a biztonsági mentési szabályzat által meghatározott megőrzési időtartam (és így a Storage-fiókban lévő blob szolgáltatás időponthoz tartozó visszaállítási funkciója) beleesik. Az operatív biztonsági mentés blob-alapú visszaállítást használ az adatok helyreállítási pontról történő visszaállításához.

Az Operational Backup lehetővé teszi a Storage-fiókban lévő összes blokk blob visszaállítását, a megadott tárolók tallózását és visszaállítását, illetve az előtag-egyezések használatát a Blobok egy részhalmazának visszaállításához. Minden visszaállítás csak a forrásként szolgáló Storage-fiókra hajtható végre.

## <a name="pricing"></a>Díjszabás

A Blobok működés közbeni biztonsági mentésének használatakor nem számítunk fel semmilyen felügyeleti díjat vagy használati díjat. Azonban a következő díjakat kell fizetnie:

- A visszaállítja a blob időponthoz való visszaállítását, és a feldolgozott adatok mennyisége alapján felszámított díjakat. További információ: [az időponthoz tartozó visszaállítás díjszabása](../storage/blobs/point-in-time-restore-overview.md#pricing-and-billing).

- Az adatok megőrzése a [Blobok helyreállított törlésével](../storage/blobs/soft-delete-blob-overview.md), a [hírcsatornák támogatásának módosításával az Azure Blob Storageban](../storage/blobs/storage-blob-change-feed.md)és a [blob verziószámozása](../storage/blobs/versioning-overview.md).

## <a name="next-steps"></a>Következő lépések

- [Azure-Blobok biztonsági mentésének konfigurálása és kezelése](blob-backup-configure-manage.md)