---
title: 'GYIK: Azure Files biztonsági mentése'
description: Ebből a cikkből megismerheti az Azure-fájlmegosztás Azure Backup szolgáltatással való védelemmel kapcsolatos gyakori kérdésekre adott válaszokat.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: e2b6afb25e189ee2848f25c0ba59d843baf37090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91940835"
---
# <a name="questions-about-backing-up-azure-files"></a>Kérdések az Azure Files biztonsági mentéséről

Ez a cikk az Azure Files biztonsági mentésével kapcsolatos általános kérdéseket válaszol meg. Egyes válaszokban részletes információkat tartalmazó cikkekre mutató hivatkozások találhatók. A Microsoft Q&a Azure Backup szolgáltatással kapcsolatos kérdéseket is felteheti a [kérdéses oldalon](/answers/topics/azure-backup.html).

A cikk szakaszainak gyors áttekintéséhez használja **A cikk tartalma** terület hivatkozásait a jobb oldalon.

## <a name="configuring-the-backup-job-for-azure-files"></a>Biztonsági mentési feladat konfigurálása Azure Fileshoz

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Miért nem látok a védelemmel ellátni kívánt tárterület-fiókokat, amelyek érvényes Azure-fájlmegosztást tartalmaznak?

Tekintse át az [Azure-fájlmegosztás támogatási mátrixát](azure-file-share-support-matrix.md) , és győződjön meg arról, hogy a Storage-fiók a támogatott Storage-fiókok egyikéhez tartozik. Az is előfordulhat, hogy a keresett Storage-fiók már védett, vagy egy másik tárolóban van regisztrálva. [Szüntesse meg a Storage-fiók regisztrációját](manage-afs-backup.md#unregister-a-storage-account) a tárolóból, hogy felderítse a Storage-fiókot a többi tárolóban a védelem érdekében.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Miért nem látom néhány Azure-fájlmegosztásomat a tárfiókban, miközben a biztonsági mentést próbálom konfigurálni?

Ellenőrizze, hogy az Azure-fájlmegosztás már védve van-e ugyanabban a helyreállítási tárban, vagy nemrégiben lett-e törölve.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Meg tudom védeni az Azure Files Sync szinkronizált csoportjához csatlakoztatott fájlmegosztásaimat?

Igen. A szinkronizálási csoportokhoz csatlakozó Azure-fájlmegosztás védelme engedélyezve van.

### <a name="when-trying-to-back-up-file-shares-i-selected-a-storage-account-to-discover-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>A fájlmegosztás biztonsági mentésére tett kísérlet során kiválasztott egy Storage-fiókot a fájlmegosztás felderítéséhez. Azonban nem védik őket. Hogyan a fájlmegosztást bármilyen más tárolóval?

Amikor megkísérli a biztonsági mentést, válassza ki azt a Storage-fiókot, amellyel a fájlmegosztást fel szeretné deríteni, regisztrálja a Storage-fiókot azzal a tárolóval, amelyről ez történik. Ha úgy dönt, hogy egy másik tárolóval gondoskodik a fájlmegosztás megosztásáról, akkor szüntesse meg a kiválasztott Storage-fiók [regisztrációját](manage-afs-backup.md#unregister-a-storage-account) ebből a tárból.

### <a name="why-cant-i-change-the-vault-to-configure-backup-for-the-file-share"></a>Miért nem változtathatom meg a tárolót a fájlmegosztás biztonsági mentésének konfigurálásához?

Ha a Storage-fiók már regisztrálva van egy tárolóban, vagy a Storage-fiókban lévő más fájlmegosztás is védelemmel van ellátva, akkor nincs lehetőség a módosítására. A Storage-fiókokban lévő összes fájlmegosztást csak ugyanahhoz a tárolóhoz lehet védeni. Ha módosítani szeretné a [tárolót, le kell állítania a Storage-fiókban lévő összes fájlmegosztás védelmét](manage-afs-backup.md#stop-protection-on-a-file-share) a csatlakoztatott tárból, [törölni](manage-afs-backup.md#unregister-a-storage-account) kell a Storage-fiókot, majd egy másik tárolót kell választania a védelemhez.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Módosíthatom azt a tárolót, amelyre biztonsági másolatot készítek a fájlmegosztást?

Igen. Azonban [le kell állítania a fájlmegosztás védelmét](manage-afs-backup.md#stop-protection-on-a-file-share) a csatlakoztatott tárolóból, törölni kell a Storage-fiók [regisztrációját](manage-afs-backup.md#unregister-a-storage-account) , majd egy másik tárból kell védelemmel ellátnia.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Biztosíthatok védelmet két különböző fájlmegosztás számára ugyanabból a Storage-fiókból különböző tárolókba?

Nem. A Storage-fiókban lévő összes fájlmegosztás számára csak ugyanazzal a tárolóval biztosítható védelem.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>Mi a teendő, ha a biztonsági mentések sikertelenek, mert a maximális korlát elérte a hibát?

Egy tetszőleges időpontban legfeljebb 200 pillanatkép készíthető fájlmegosztásonként. A korlátba a szabályzatban meghatározottak szerint beletartoznak az Azure Backup által készített pillanatképek is. Ha a biztonsági mentések a korlát elérése után sikertelenek lesznek, törölje az igény szerinti pillanatképeket a sikeres jövőbeli biztonsági mentésekhez.

## <a name="restore"></a>Visszaállítás

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Tudok helyreállítani törölt Azure-fájlmegosztásból?

Ha a fájlmegosztás lágyan törölt állapotban van, először törölnie kell a fájlmegosztást a visszaállítási művelet elvégzéséhez. A törlési művelettel a fájlmegosztás aktív állapotba kerül, ahol bármikor visszaállítható. A fájlmegosztás törlésének visszavonásához látogasson el [erre a hivatkozásra](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) , vagy tekintse meg a [fájlmegosztási parancsfájl törlésének](./scripts/backup-powershell-script-undelete-file-share.md)visszavonása című témakört. Ha a fájlmegosztás véglegesen törölve lett, nem fogja tudni visszaállítani a tartalmakat és a pillanatképeket.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Vissza tudok állítani biztonsági mentésekből, ha leállítottam az Azure-fájlmegosztás védelmét?

Igen. Ha a védelem leállításakor a **Biztonsági másolatok adatainak megőrzése** lehetőséget választotta, akkor minden meglévő visszaállítási pontból vissza tud állítani.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Mi történik, ha megszakítok egy folyamatban lévő visszaállítási feladatot?

Ha egy folyamatban lévő visszaállítási feladat meg lett szakítva, a visszaállítási folyamat leáll, és a megszakítás előtt visszaállított összes fájl megmarad a konfigurált célhelyen (eredeti vagy másodlagos helyen) a visszaállítások nélkül.

## <a name="manage-backup"></a>Biztonsági mentés kezelése

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Használhatom a PowerShellt az Azure-fájlmegosztás biztonsági másolatainak konfigurálására/kezelésére/visszaállítására?

Igen. A részletes dokumentációt [itt](backup-azure-afs-automation.md)találja.

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Hozzáférhetek az Azure Backups által készített pillanatképekhez, és csatlakoztatják őket?

A Azure Backup által készített Pillanatképek a portálon, a PowerShellben vagy a CLI-ben is elérhetők. Ha többet szeretne megtudni a Azure Files-megosztási pillanatképekről, tekintse meg a [Azure Files megosztási Pillanatképek áttekintése](../storage/files/storage-snapshots-files.md)című témakört.

### <a name="what-happens-after-i-move-a-backed-up-file-share-to-a-different-subscription"></a>Mi történik, ha egy biztonsági másolatba mentett fájlmegosztást másik előfizetésre helyezek át?

A fájlmegosztás egy másik előfizetésbe való áthelyezését követően a rendszer a Azure Backup új fájlmegosztást tekinti. Ezek az ajánlott lépések:
 
Forgatókönyv: tegyük fel, hogy van egy fájlmegosztási *FS1* a (z) *S1* előfizetésben, és a *v1* -tárolón keresztül védett. Most szeretné áthelyezni a fájlmegosztást az előfizetés *S2*-re.
 
1.  Helyezze át a kívánt Storage-fiókot és-fájlmegosztást (FS1) a másik előfizetésbe (S2).
2.  A v1-tárolóban aktiválja a FS1 adattörlési művelettel való védelem leállítása műveletet.
3.  Szüntesse meg a FS1 üzemeltető Storage-fiók regisztrációját a v1-tárból.
4.  Konfigurálja újra a biztonsági mentést a FS1, most pedig S2-re költözött, az S2-előfizetésben található tárolóval (v2). 
 
Vegye figyelembe, hogy a biztonsági mentés v2-vel való újrakonfigurálását követően a v1-mel készített pillanatképeket a továbbiakban nem fogja felügyelni Azure Backup. Ezért ezeket a pillanatképeket manuálisan kell törölnie az igényeinek megfelelően.

### <a name="can-i-move-my-backed-up-file-share-to-a-different-resource-group"></a>Áthelyezhetem a mentett fájlmegosztást egy másik erőforráscsoporthoz?
 
Igen, áthelyezheti a mentett fájlmegosztást egy másik erőforráscsoporthoz. A fájlmegosztás biztonsági mentését azonban újra kell konfigurálnia, mivel az Azure Backup alapján új erőforrásként lesz kezelve. Emellett az Azure Backup nem fogja felügyelni az erőforráscsoportok áthelyezése előtt létrehozott pillanatképeket. Ezért ezeket a pillanatképeket manuálisan kell törölnie az igényeinek megfelelően.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Mi a biztonsági mentésekhez konfigurálható maximális adatmegőrzés?

A maximális megőrzéssel kapcsolatos részletekért tekintse meg a [támogatási mátrixot](azure-file-share-support-matrix.md) . Azure Backup valós idejű számítást végez a pillanatképek számának megadásával, amikor a biztonsági mentési szabályzatot konfigurálja a megőrzési értékekhez. Amint a megadott adatmegőrzési értékeknek megfelelő Pillanatképek száma meghaladja az 200-as értéket, a portál egy figyelmeztetést jelenít meg, amely a megőrzési értékek módosítására kéri. Így nem lépi túl a Azure Files által támogatott Pillanatképek maximális számát az adott időpontban.

### <a name="what-is-the-impact-on-existing-recovery-points-and-snapshots-when-i-modify-the-backup-policy-for-an-azure-file-share-to-switch-from-daily-policy-to-gfs-policy"></a>Milyen hatással van a meglévő helyreállítási pontokra és pillanatképekre, amikor módosítom egy Azure-fájlmegosztás biztonsági mentési szabályzatát, hogy a "napi szabályzat" értékről "GFS Policy"-re váltson?

Ha módosítja a napi biztonsági mentési szabályzatot a GFS házirendre (hetente/havonta/évenkénti megőrzéssel), a viselkedés a következő:

- **Megőrzés**: Ha a szabályzat módosításának részeként hetente/havonta/évenkénti megőrzést vesz fel, az ütemezett biztonsági mentés részeként létrehozott jövőbeli helyreállítási pontok az új szabályzatnak megfelelően lesznek címkézve. A meglévő helyreállítási pontok továbbra is napi helyreállítási pontokként jelennek meg, így nem lesznek hetente/havonta/évente megjelölve.

- **Pillanatképek és helyreállítási pontok karbantartása**:

  - Ha a napi megőrzés kibővül, a meglévő helyreállítási pontok lejárati dátuma az új szabályzatban konfigurált napi megőrzési értéknek megfelelően frissül.
  - Ha csökkenti a napi adatmegőrzést, a meglévő helyreállítási pontok és Pillanatképek törlésre vannak megjelölve a következő karbantartási futtatási feladatokban az új szabályzatban konfigurált napi megőrzési értéknek megfelelően, majd törölve.

Íme egy példa arra, hogyan működik ez:

#### <a name="existing-policy-p1"></a>Meglévő házirend [P1]

|Adatmegőrzés típusa |Ütemezés |Megőrzés  |
|---------|---------|---------|
|Napi    |    Minden nap 20:00-kor    |  100 nap       |

#### <a name="new-policy-modified-p1"></a>Új szabályzat [módosított P1]

| Adatmegőrzés típusa | Ütemezés                       | Megőrzés |
| -------------- | ------------------------------ | --------- |
| Napi          | Minden nap 21:00 órakor              | 50 nap   |
| Heti         | Vasárnap, 21:00 órakor              | 3 hét   |
| Havonta        | Az utolsó hétfőn, 9 ÓRAKOR         | 1 hónap   |
| Éves         | Januártól harmadik vasárnap, 21:00-kor | 4 év   |

#### <a name="impact"></a>Hatás

1. A meglévő helyreállítási pontok lejárati dátuma az új szabályzat napi adatmegőrzési értékének megfelelően lesz módosítva: ez 50 nap. Így a 50 napnál régebbi helyreállítási pontok törlésre lesznek megjelölve.

2. A meglévő helyreállítási pontok nem lesznek hetente/havonta/évente megjelölve az új szabályzat alapján.

3. Az új ütemezések szerint az összes jövőbeli biztonsági mentés a következő ütemezés szerint fog megjelenni: 9 ÓRAKOR.

4. A jövőbeli helyreállítási pontok lejárati dátuma az új házirenddel lesz igazítva.

>[!NOTE]
>A házirend módosításai csak az ütemezett biztonsági mentési feladatok futtatásának részeként létrehozott helyreállítási pontokat érintik. Igény szerinti biztonsági mentés esetén a megőrzési időt a biztonsági mentés időpontjában megadott **megőrzési** érték határozza meg.

### <a name="what-is-the-impact-on-existing-recovery-points-when-i-modify-an-existing-gfs-policy"></a>Milyen hatással van a meglévő helyreállítási pontokra a meglévő GFS-szabályzatok módosításakor?

Amikor új házirendet alkalmaznak a fájlmegosztás esetében, az összes jövőbeli biztonsági mentés a módosított szabályzatban konfigurált ütemezésnek megfelelően lesz végrehajtva.  Az összes meglévő helyreállítási pont megőrzése a beállított új megőrzési értékek szerint igazodik. Így ha a megőrzést kiterjesztik, a meglévő helyreállítási pontok az új szabályzatnak megfelelően megmaradnak. Ha csökkenti a megőrzési időt, a rendszer a következő karbantartási feladatokban törli a tisztítást, majd törölve lesz.

Íme egy példa arra, hogyan működik ez:

#### <a name="existing-policy-p2"></a>Meglévő házirend [P2]

| Adatmegőrzés típusa | Ütemezés           | Megőrzés |
| -------------- | ------------------ | --------- |
| Napi          | Minden nap 20:00-kor | 50 nap   |
| Heti         | Hétfő: 20:00  | 3 hét   |

#### <a name="new-policy-modified-p2"></a>Új szabályzat [módosított P2]

| Adatmegőrzés típusa | Ütemezés               | Megőrzés |
| -------------- | ---------------------- | --------- |
| Napi          | Minden nap 21:00 órakor     | 10 nap   |
| Heti         | Hétfőn, 21:00 órakor      | 2 hét   |
| Havonta        | Az utolsó hétfőn, 9 ÓRAKOR | 2 hónap  |

#### <a name="impact-of-change"></a>A változás hatása

1. A meglévő napi helyreállítási pontok lejárati dátuma az új napi adatmegőrzési érték szerint lesz igazítva, amely 10 nap. Így minden 10 napnál régebbi helyreállítási pont törölve lesz.

2. A meglévő heti helyreállítási pontok lejárati dátuma az új heti adatmegőrzési érték szerint lesz igazítva, amely két hét. Így a két hétnél régebbi heti helyreállítási pontok törölve lesznek.

3. A havi helyreállítási pontok csak a jövőbeli biztonsági mentések részeként jönnek létre az új házirend-konfiguráció alapján.

4. A jövőbeli helyreállítási pontok lejárati dátuma az új házirenddel lesz igazítva.

>[!NOTE]
>A házirend módosítása csak az ütemezett biztonsági mentés részeként létrehozott helyreállítási pontokra lesz hatással. Igény szerinti biztonsági mentések esetén a megőrzési időtartamot a biztonsági mentés során megadott **megőrzési** érték határozza meg.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-fájlmegosztás biztonsági mentése során felmerülő problémák elhárítása](troubleshoot-azure-files.md)
