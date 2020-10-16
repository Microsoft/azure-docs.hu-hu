---
title: Véletlen törlés megakadályozása – Azure-fájlmegosztás
description: Ismerje meg az Azure-fájlmegosztás helyreállítható törlését, valamint azt, hogy miként használható az adathelyreállításhoz és a véletlen törlés megakadályozásához.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 50dbca7e32548380c6dbf338260b98ce59cda715
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122415"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Azure-fájlmegosztás véletlen törlésének megakadályozása

Az Azure Storage mostantól helyreállítható törlést biztosít a fájlmegosztás (előzetes verzió) számára. A Soft delete lehetővé teszi a fájlmegosztás helyreállítását, ha azt egy alkalmazás vagy más Storage-fiók felhasználója véletlenül törölte.

## <a name="how-soft-delete-preview-works"></a>A Soft Delete (előzetes verzió) működése

Ha az Azure-fájlmegosztás esetében engedélyezve van az ideiglenes törlés, a rendszer törli a fájlt, és nem véglegesen törlődik. Beállíthatja, hogy a rendszer a véglegesen törölt adatok mennyiségét helyreállítsa, és bármikor törölje a megosztást a megőrzési időszak alatt. A törlést követően a megosztás és az összes tartalom, beleértve a pillanatképeket, vissza lesz állítva a törlés előtti állapotba. A Soft delete csak a fájlmegosztás szintjén működik – a törölt egyedi fájlok továbbra is véglegesen törlődnek.

A helyreállítható törlést engedélyezheti új vagy meglévő fájlmegosztás esetén is. A helyreállítható törlés visszafelé is kompatibilis, így nem kell módosítania az alkalmazásokat, hogy kihasználhassa a Soft delete védelmét. 

Ha véglegesen törölni szeretne egy fájlmegosztást a lejárati idő előtti törlési állapotban, törölje a megosztást, tiltsa le a Soft delete parancsot, majd törölje újra a megosztást. Ezután engedélyezze újra a helyreállítható törlést, mivel az adott Storage-fiókban lévő más fájlmegosztás is ki lesz téve a véletlen törlésnek, miközben a Soft delete ki van kapcsolva.

A prémium szintű törölt fájlmegosztás esetén a fájlmegosztás kvótáját (a fájlmegosztás kiosztott méretét) a rendszer a teljes Storage-fiók kvótájának kiszámításához használja, amíg a rendszer a megosztást teljesen törli.

## <a name="availability"></a>Rendelkezésre állás

Az Azure-fájlmegosztás (előzetes verzió) Soft delete szolgáltatás minden tárolási szinten elérhető, minden tárolási fiók típusa és minden olyan régióban, amely Azure Files elérhető.

## <a name="configuration-settings"></a>Konfigurációs beállítások

### <a name="enabling-or-disabling-soft-delete"></a>A Soft delete engedélyezése vagy letiltása

A fájlmegosztás helyreállítható törlésének engedélyezése a Storage-fiók szintjén történik, ezért a törlési beállítások a Storage-fiókban lévő összes fájlmegosztás esetében érvényesek. Bármikor engedélyezheti vagy letilthatja a Soft deletet. Új Storage-fiók létrehozásakor a rendszer alapértelmezés szerint letiltotta a fájlmegosztás törlését, miközben a funkció nyilvános előzetes verzióban érhető el. Ha a Soft delete általánosan elérhetővé válik, alapértelmezés szerint engedélyezve lesz. A Soft delete alapértelmezés szerint le lesz tiltva a meglévő Storage-fiókok esetében. Ha az Azure-fájlmegosztás [biztonsági mentését](../../backup/azure-file-share-backup-overview.md) konfigurálta egy Azure-fájlmegosztás számára, akkor az Azure-fájlmegosztás helyreállítható törlését a rendszer automatikusan engedélyezi a megosztás Storage-fiókjában.

Ha engedélyezi a fájlmegosztás törlését, töröljön néhány fájlmegosztást, majd tiltsa le a helyreállítható törlést, ha a megosztások ebben az időszakban lettek mentve, akkor továbbra is elérheti és helyreállíthatja a fájlmegosztást. Ha engedélyezi a helyreállítható törlést, a megőrzési időtartamot is konfigurálnia kell.

### <a name="retention-period"></a>Megőrzési időszak

A megőrzési időtartam azt az időtartamot, ameddig a rendszer a helyreállított fájlmegosztást tárolja és elérhetővé válik. A kifejezetten törölt fájlmegosztás esetén a megőrzési időszak órája az adatok törlésekor kezdődik. Jelenleg egy 1 és 365 nap közötti megőrzési időtartamot adhat meg. Bármikor módosíthatja a helyreállítható törlés megőrzési időtartamát. A rendszer csak a megőrzési időszak frissítése után törölt megosztásokra alkalmazza a frissített megőrzési időtartamot. A rendszer a megőrzési idő frissítése előtt törölte a megosztásokat, és az adatok törlésekor beállított megőrzési időtartam alapján lejár.

## <a name="pricing-and-billing"></a>Árak és számlázás

A standard és a prémium szintű fájlmegosztást a rendszer a felhasznált kapacitás esetén is felszámítja, nem pedig kiosztott kapacitással. Emellett a prémium szintű fájlmegosztást a pillanatképek díjszabása alapján számítjuk fel, a Soft delete állapotában. A normál fájlmegosztás számlázása a normál díjszabással történik a helyreállítható törlési állapotban. A beállított megőrzési időtartam után véglegesen törölt adatokért nem számítunk fel díjat.

Az Azure File Storage általános díjszabásával kapcsolatos további információkért tekintse meg az [azure file Storage díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/storage/files/).

Amikor először engedélyezte a Soft delete használatát, javasoljuk, hogy használjon egy kis megőrzési időtartamot, hogy jobban megértse, hogyan befolyásolja a szolgáltatás a számlát.

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan engedélyezheti és használhatja a Soft deletet, folytassa a [Soft delete engedélyezésével](storage-files-enable-soft-delete.md).
