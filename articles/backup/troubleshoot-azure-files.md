---
title: Az Azure-fájlmegosztások biztonsági mentésének hibaelhárítása
description: A cikk olyan hibákkal kapcsolatos hibaelhárítási információkat tartalmaz, amelyek az Azure fájlmegosztások védelmekor következnek be.
ms.date: 08/20/2019
ms.topic: troubleshooting
ms.openlocfilehash: 050df5b96c265e468346535ff011e1baf7d86ad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252388"
---
# <a name="troubleshoot-problems-backing-up-azure-file-shares"></a>Az Azure-fájlmegosztások biztonsági mentésével kapcsolatos problémák elhárítása

Az alábbi táblázatokban szereplő információk segítségével elháríthatja az Azure-fájlmegosztások biztonsági mentése közben fellépő problémákat és hibákat.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Az Azure-fájlmegosztás biztonsági mentésének korlátozásai az előzetes verzióban

Az Azure-fájlmegosztások biztonsági mentése jelenleg előzetes verzióban érhető el. Az Azure fájlmegosztások általános célú v1 és általános célú v2 tárfiókok támogatottak. Az Azure-fájlmegosztások nem támogatják az alábbi biztonsági mentési forgatókönyveket:

- Az Azure-fájlok Azure Backup használatával történő védelméhez nem áll rendelkezésre CLI.
- Az ütemezett biztonsági mentések maximális száma naponta egy.
- Az igény szerinti biztonsági mentések maximális száma naponta négy.
- Használja [az erőforrás-zárolások](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) a tárfiókban a biztonsági mentések véletlen törlésének megakadályozása a Recovery Services-tárolóban.
- Ne törölje az Azure Backuppal létrehozott pillanatképeket. A pillanatképek törlése helyreállítási pontok elvesztését és/vagy visszaállítási hibákat eredményezhet.
- Ne törölje az Azure Backup által védett fájlmegosztásokat. A jelenlegi megoldás törli az Azure Backup által készített összes pillanatképet, miután a fájlmegosztást törölték, és így elveszíti az összes visszaállítási pontot

Biztonsági mentés a [zónaredundáns tárolással](../storage/common/storage-redundancy-zrs.md) (ZRS) replikációval rendelkező tárfiókok Azure-fájlmegosztásainak biztonsági mentése jelenleg csak az USA középső részén (CUS), az USA keleti régiójában (EUS), az USA keleti régiójában (EUS2), Észak-Európában (NE), Délkelet-Ázsiában (SEA), Nyugat-Európában (WE) és az USA nyugati részén 2 (WUS2) érhető el.

## <a name="configuring-backup"></a>Biztonsági másolat konfigurálása

Az alábbi táblázat a biztonsági mentés minél pontosabb konfigurálásához használható:

| Hibaüzenetek | Megkerülő vagy megoldási tippek |
| ------------------ | ----------------------------- |
| Nem találom a tárfiókom az Azure-fájlmegosztás biztonsági mentésének konfigurálásához | <ul><li>Várjon, amíg a felderítés befejeződik. <li>Ellenőrizze, hogy egy másik helyreállítási tár védi-e már bármelyik fájlmegosztást a tárfiókból. **Megjegyzés**: A tárfiókban lévő összes fájlmegosztás védelme biztosítható egyetlen helyreállítási tárral. <li>Győződjön meg arról, hogy a fájlmegosztás nincs jelen egyik nem támogatott tárfiókban sem.<li> Győződjön meg arról, hogy a **megbízható Microsoft-szolgáltatások hozzáférhetnek ehhez a tárfiókhoz** jelölőnégyzet be van jelölve a tárfiókban. [További információ.](../storage/common/storage-network-security.md)|
| A portál hibája azt jelenti, hogy a tárfiókok felderítése sikertelen volt. | Ha az előfizetése partneri (CSP-engedélyezett), hagyja figyelmen kívül a hibát. Ha az előfizetés nem engedélyezi a CSP-t, a tárfiókok pedig nem deríthetők fel, forduljon az ügyfélszolgálathoz.|
| A kiválasztott tárfiók érvényesítése vagy regisztrációja sikertelen.| Próbálkozzon újra a művelettel. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz.|
| A kiválasztott tárfiókban nem listázható vagy nem található fájlmegosztás. | <ul><li> Győződjön meg arról, hogy a tárfiók létezik az erőforráscsoportban (és nem törölték vagy helyezték át a tárolóban az utolsó ellenőrzés/regisztráció után).<li>Ellenőrizze, hogy a védeni kívánt fájlmegosztás nem lett-e törölve. <li>Ellenőrizze, hogy a tárfiók támogatott-e fájlmegosztásokról való biztonsági másolat készítéséhez.<li>Ellenőrizze, hogy a fájlmegosztás már védve van-e ugyanabban a helyreállítási tárban.|
| A fájlmegosztás biztonsági mentésének konfigurációja (vagy a védelmi szabályzat konfigurálása) sikertelen. | <ul><li>Próbálja megismételni a műveletet annak ellenőrzéséhez, hogy a probléma továbbra is fennáll-e. <li> Győződjön meg arról, hogy a védeni kívánt fájlmegosztás nem lett törölve. <li> Ha több fájlmegosztást kíván egyszerre védeni, és a fájlmegosztások némelyike nem működik, ismételje meg a sikertelen fájlmegosztások biztonsági mentésének konfigurálását. |
| Nem sikerült törölni a helyreállítási tárat a fájlmegosztás védelmének feloldása után. | Az Azure Portalon nyissa meg a Vault **Unregister** > backup **infrastruktúra-tároló** > **fiókok** és kattintson a regisztráció megszüntetése a storage-fiók eltávolításához a Recovery Services-tárolóból.|

## <a name="error-messages-for-backup-or-restore-job-failures"></a>Hibaüzenetek a biztonsági mentési vagy visszaállítási hibákhoz

| Hibaüzenetek | Megkerülő vagy megoldási tippek |
| -------------- | ----------------------------- |
| A művelet nem sikerült, mert a fájlmegosztás nem található. | Ellenőrizze, hogy a védeni kívánt fájlmegosztás nem lett-e törölve.|
| A tárfiók nem található vagy nem támogatott. | <ul><li>Győződjön meg arról, hogy az erőforráscsoport tartalmazza a tárfiókot, és nem törölték vagy távolították el az erőforráscsoportból az utolsó ellenőrzés alkalmával. <li> Ellenőrizze, hogy a tárfiók támogatott-e fájlmegosztásokról való biztonsági másolat készítéséhez.|
| Elérte a fájlmegosztás pillanatképekre vonatkozó maximális korlátját. Akkor fog tudni továbbiakat készíteni, ha a régebbiek érvényessége lejár. | <ul><li> Ez a hiba akkor fordulhat elő, ha több igény szerinti biztonsági mentést hoz létre egy fájlhoz. <li> Egy fájlmegosztásról legfeljebb 200 pillanatkép készíthető, beleértve az Azure Backup által készítetteket is. A régebbi ütemezett biztonsági mentések (vagy pillanatképek) automatikusan törlődnek. Törölni kell az igény szerinti biztonsági mentést (vagy pillanatképeket), ha elérik a maximális korlátot.<li> Törölje az igény szerinti biztonsági mentéseket (az Azure fájlmegosztási pillanatképeket) az Azure Files portálról. **Megjegyzés:**: A helyreállítási pontok elvesznek, ha törli az Azure Backuppal létrehozott pillanatképeket. |
| A fájlmegosztás biztonsági mentése vagy visszaállítása a társzolgáltatás szabályozása miatt meghiúsult. Ez azért történhet, mert a társzolgáltatás a megadott tárfiók más kérelmeinek feldolgozásával van elfoglalva.| Próbálja meg újra a műveletet később. |
| A visszaállítás sikertelen, mert a célként megadott fájlmegosztás nem található. | <ul><li>Győződjön meg arról, hogy a kiválasztott tárfiók létezik, a célfájlmegosztás pedig nincs törölve. <li> Ellenőrizze, hogy a tárfiók támogatott-e fájlmegosztásokról való biztonsági másolat készítéséhez. |
| A biztonsági mentési vagy visszaállítási feladat sikertelen volt a tárfiók zárolt állapota miatt. | Távolítsa el a zárolást a tárfiókról, vagy használjon törlési zárolást az olvasási zárolás helyett, majd ismételje meg a műveletet. |
| A helyreállítás sikertelen volt, mert a hibás fájlok száma meghaladja a küszöbértéket. | <ul><li> A helyreállítási hibák okainak listája egy fájlban található (a feladat részletei tartalmazzák az elérési utat). Kérjük, oldja meg a hibákat, és ismételje meg visszaállítási műveletet csak a hibás fájlokra vonatkozóan. <li> A fájlvisszaállítási hibák gyakori okai: <br/> – győződjön meg arról, hogy a hibás fájlok jelenleg nincsenek használatban, <br/> – a hibás fájllal megegyező nevű könyvtár található a szülőkönyvtárban. |
| A helyreállítás sikertelen volt, mert egyetlen fájl sem állítható helyre. | <ul><li> A helyreállítási hibák okainak listája egy fájlban található (a feladat részletei tartalmazzák az elérési utat). Oldja meg a hibákat, majd ismételje meg a visszaállítási műveleteket csak a hibás fájlokra vonatkozóan. <li> A sikertelen fájlvisszaállítás gyakori okai: <br/> – Győződjön meg arról, hogy a hibás fájlok jelenleg nincsenek használatban. <br/> – A hibás fájllal megegyező nevű könyvtár található a szülőkönyvtárban. |
| A visszaállítás sikertelen, mert a forrás egyik fájlja nem létezik. | <ul><li> A kijelölt elemek nem szerepelnek a helyreállítási pont adataiban. A fájlok helyreállításához adja meg a helyes fájllistát. <li> A helyreállítási ponthoz tartozó megosztás fájlpillanatképet manuálisan kell törölni. Válasszon egy másik helyreállítási pontot, és ismételje meg a visszaállítási műveletet. |
| Már folyamatban van egy ugyanerre a célhelyre irányuló helyreállítási feladat. | <ul><li>A fájlmegosztás biztonsági mentése nem támogatja az ugyanazon célfájlmegosztásra végzett párhuzamos helyreállítást. <li>Várja meg a meglévő helyreállítási feladat befejeződését, majd próbálja meg újból. Ha nem talál helyreállítási feladatot a helyreállítási tárban, ellenőrizze az adott előfizetéshez tartozó többi helyreállítási tárat. |
| A visszaállítási művelet sikertelen volt, mert a célfájlmegosztás megtelt. | Növelje meg a célfájlmegosztás méretkvótáját, hogy az képes legyen fogadni a visszaállítási adatokat, majd ismételje meg a műveletet. |
| A visszaállítási művelet sikertelen volt, mivel hiba történt a File Sync szolgáltatás a célfájlmegosztással társított erőforrásain végrehajtott visszaállítás előtti műveletek során. | Később próbálkozzon újra, és ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. |
| Egy vagy több fájlt nem sikerült helyreállítani. További információkért ellenőrizze a hibás fájlok listáját a fent megadott elérési úton. | <ul> <li> A helyreállítási hibák okai megtalálhatók a fájlban (az elérési utat a feladat részletei tartalmazzák). Vizsgálja ki az okokat, majd ismételje meg a visszaállítási műveletet csak a hibás fájlokra vonatkozóan. <li> A fájlvisszaállítási hibák leggyakoribb okai a következők: <br/> – Győződjön meg arról, hogy a hibás fájlok jelenleg nincsenek használatban. <br/> – Egy, a hibás fájlokkal megegyező nevű könyvtár található a szülőkönyvtárban. |

## <a name="modify-policy"></a>Házirend módosítása

| Hibaüzenetek | Megkerülő vagy megoldási tippek |
| ------------------ | ----------------------------- |
| Egy másik konfigurálási védelmi művelet van folyamatban ehhez az elemhez. | Várjon, amíg az előző módosítási házirend-művelet befejeződik, majd egy idő után újra próbálkozik.|
| Egy másik művelet van folyamatban a kijelölt elemen. | Várjon, amíg a másik folyamatban lévő művelet befejeződik, majd valamikor újra próbálkozik |

## <a name="next-steps"></a>További lépések

Az Azure-fájlmegosztások biztonsági mentéséről a következő témakörben talál további információt:

- [Az Azure-fájlmegosztások biztonsági és biztonsági dokumentációjának biztonsági és biztonsági dokumentációj](backup-afs.md)
- [Gyakori kérdések az Azure-fájlmegosztásról](backup-azure-files-faq.md)
