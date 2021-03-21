---
title: A MARS-ügynök biztonsági másolatainak kezelése és figyelése
description: Megtudhatja, hogyan kezelheti és figyelheti Microsoft Azure Recovery Services (MARS) ügynök biztonsági másolatait a Azure Backup szolgáltatás használatával.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 25f0c41b535f9403d0a7027687cc5261cd437275
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97368596"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Microsoft Azure Recovery Services-(MARS-) ügynök biztonsági másolatainak kezelése a Azure Backup szolgáltatás használatával

Ez a cikk azt ismerteti, hogyan kezelhetők a Microsoft Azure Recovery Services ügynökkel biztonsági mentés alatt álló fájlok és mappák.

## <a name="modify-a-backup-policy"></a>Biztonsági mentési szabályzat módosítása

A biztonsági mentési szabályzat módosításakor hozzáadhat új elemeket, eltávolíthatja a meglévő elemeket a biztonsági másolatból, vagy kizárhatja a fájlok biztonsági mentését a kizárási beállítások használatával.

- **Elemek hozzáadása** ezt a lehetőséget csak új elemek biztonsági mentéshez való hozzáadásához használja. Meglévő elemek eltávolításához használja az **elemek eltávolítása** vagy a **kizárási beállítások** lehetőséget.  
- **Elemek eltávolítása** ezzel a beállítással távolíthatja el az elemeket a biztonsági mentésből.
  - A **kizárási beállítások** használatával **távolítsa el** a köteten belüli összes elemet az elemek eltávolítása helyett.
  - Egy kötet összes kijelölésének törlésével a rendszer a legutóbbi biztonsági mentés időpontjában megőrzi az elemek régi biztonsági másolatait, a módosítás hatóköre nélkül.
  - Ha újra kiválasztja ezeket az elemeket, a rendszer az első teljes biztonsági mentést és az új házirend-módosításokat nem alkalmazza a régi biztonsági másolatokra.
  - A teljes kötet kijelölésének megszüntetése megőrzi a korábbi biztonsági mentést az adatmegőrzési szabályzat módosításának hatóköre nélkül.
- **Kizárási beállítások** ezzel a beállítással kizárhat bizonyos elemeket a biztonsági mentésből.

### <a name="add-new-items-to-existing-policy"></a>Új elemek hozzáadása meglévő szabályzathoz

1. A **műveletek** területen válassza a **biztonsági mentés időzítése** elemet.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-configure-vault/schedule-first-backup.png)

2. A **házirend-elem kiválasztása** lapon válassza **a fájlok és mappák biztonsági mentésének módosítása** lehetőséget, majd kattintson a **tovább** gombra.

    ![Házirend-elemek kiválasztása](./media/backup-azure-manage-mars/select-policy-items.png)

3. A **biztonsági mentés módosítása vagy leállítása** lapon válassza a módosítások elvégzése **a biztonsági másolatok vagy időpontok számára** lehetőséget, és kattintson a **tovább** gombra.

    ![Biztonsági mentés módosítása vagy időzítése](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Az elemek **kiválasztása a biztonsági mentéshez** lapon válassza az **elemek hozzáadása** lehetőséget, hogy hozzáadja azokat az elemeket, amelyekről biztonsági másolatot szeretne készíteni.

    ![Biztonsági másolat hozzáadási elemeinek módosítása vagy ütemezett létrehozása](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Az **elemek kijelölése** ablakban válassza ki azokat a fájlokat vagy mappákat, amelyeket hozzá szeretne adni, majd kattintson **az OK gombra**.

    ![Elemek kijelölése](./media/backup-azure-manage-mars/select-item.png)

6. Végezze el a következő lépéseket, és válassza a **Befejezés** gombot a művelet befejezéséhez.

### <a name="add-exclusion-rules-to-existing-policy"></a>Kizárási szabályok hozzáadása a meglévő szabályzathoz

Kizárási szabályok hozzáadásával kihagyhatja azokat a fájlokat és mappákat, amelyeket nem szeretne biztonsági mentésre készíteni. Ezt az új szabályzat definiálásakor vagy egy meglévő szabályzat módosításakor teheti meg.

1. A Műveletek ablaktáblában válassza a **biztonsági mentés időzítése** elemet. Lépjen a **biztonsági mentés elemre** , és válassza a **kizárási beállítások** lehetőséget.

    ![Kizárási beállítások](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. A **kizárási beállítások** területen válassza a **kizárás hozzáadása** elemet.

    ![Kizárás hozzáadása](./media/backup-azure-manage-mars/add-exclusion.png)

3. A **kizárni kívánt elemek kiválasztásával** tallózzon a fájlokhoz és mappákhoz, és válassza ki azokat az elemeket, amelyeket ki szeretne zárni, majd válassza **az OK** gombot.

    ![Válassza ki a kizárandó elemeket](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Alapértelmezés szerint a kijelölt mappákban lévő összes **almappa** ki van zárva. Ezt megváltoztathatja az **Igen** vagy a **nem** lehetőség kiválasztásával. A kizárni kívánt fájltípusokat a lent látható módon szerkesztheti és adhatja meg:

    ![Almappák típusának kiválasztása](./media/backup-azure-manage-mars/subfolders-type.png)

5. Végezze el a következő lépéseket, és válassza a **Befejezés** gombot a művelet befejezéséhez.

### <a name="remove-items-from-existing-policy"></a>Elemek eltávolítása meglévő házirendből

1. A Műveletek ablaktáblában válassza a **biztonsági mentés időzítése** elemet. Lépjen a **biztonsági mentés elemre**. A listából válassza ki azokat a fájlokat és mappákat, amelyeket el szeretne távolítani a biztonsági mentési ütemtervből, majd válassza az **elemek eltávolítása** lehetőséget.

    ![Válassza ki az eltávolítandó elemeket](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Körültekintően járjon el, ha teljesen eltávolítja a kötetet a szabályzatból.  Ha újra hozzá kell adnia, akkor a rendszer új kötetként kezeli. A következő ütemezett biztonsági mentés a növekményes biztonsági mentés helyett kezdeti biztonsági mentést (teljes biztonsági mentést) hajt végre. Ha a későbbiekben átmenetileg el kell távolítania és hozzá kell adnia az elemeket, akkor az **elemek eltávolítása** helyett a **kizárási beállítások** használata javasolt, hogy a teljes biztonsági mentés helyett a növekményes biztonsági mentést biztosítsa.

2. Végezze el a következő lépéseket, és válassza a **Befejezés** gombot a művelet befejezéséhez.

## <a name="stop-protecting-files-and-folder-backup"></a>Fájlok és mappák biztonsági mentésének leállítása

A fájlok és mappák biztonsági mentése kétféleképpen állítható le:

- **A védelem leállítása és a biztonsági mentési adat megőrzése**.
  - Ezzel a beállítással leállíthatja az összes jövőbeli biztonsági mentési feladatot a védelemből.
  - Azure Backup szolgáltatás továbbra is megőrzi az összes meglévő helyreállítási pontot.  
  - Visszaállíthatja a lejárt helyreállítási pontok biztonsági másolatait.
  - Ha úgy dönt, hogy folytatja a védelmet, használhatja a *biztonsági mentési ütemterv újbóli engedélyezése* lehetőséget. Ezt követően az adatok megmaradnak az új adatmegőrzési szabályzat alapján.
- **Állítsa le a védelmet, és törölje a biztonsági másolati fájlokat**.
  - Ezzel a beállítással leállíthatja az összes jövőbeli biztonsági mentési feladatot az adatok védelme és az összes helyreállítási pont törlése után.
  - A biztonsági mentési adatriasztásról értesítő e-mailt kap, amely üzenettel *törli a biztonsági mentési tételhez tartozó adatait. Ezek az adatok 14 napig átmenetileg elérhetők lesznek, ami után véglegesen törlődik,* és a javasolt művelet *14 napon belül újra védetté teszi a biztonsági mentési tételt az adatok helyreállításához.*
  - A védelem folytatásához a törlési művelettől számított 14 napon belül meg kell oldania a védelmet.

### <a name="stop-protection-and-retain-backup-data"></a>A védelem leállítása és a biztonsági mentési adat megőrzése

1. Nyissa meg a MARS felügyeleti konzolját, lépjen a **műveletek ablaktáblára**, és **válassza a biztonsági mentés időzítése elemet**.

    ![Biztonsági mentés ütemezett kiválasztása](./media/backup-azure-manage-mars/mars-actions.png)
1. A **házirend kiválasztása** lapon válassza a **fájlok és mappák biztonsági mentésének ütemezett módosítása** lehetőséget, majd kattintson a **tovább** gombra.

    ![Házirend elemének kiválasztása](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Az **ütemezett biztonsági mentés módosítása vagy leállítása** lapon válassza a **Leállítás ezzel a biztonsági mentési ütemezéssel lehetőséget, de a tárolt biztonsági mentéseket tartsa meg, amíg újra nem aktiválja az ütemezést**. Ezután válassza a **tovább** lehetőséget.

    ![Állítsa le az ütemezett biztonsági mentést.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Az **ütemezett biztonsági mentés szüneteltetése** lapon tekintse át az információkat, majd kattintson a **Befejezés gombra**.

    ![Ütemezett biztonsági mentés szüneteltetése.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. A **biztonsági mentés** állapotának módosítása lapon győződjön meg arról, hogy az ütemezett biztonsági mentés szüneteltetése sikeres, és a Befejezés gombra kattintva válassza a **Bezárás** lehetőséget.

### <a name="stop-protection-and-delete-backup-data"></a>Védelem leállítása és biztonsági másolatok törlése

1. Nyissa meg a MARS felügyeleti konzolját, lépjen a **műveletek** ablaktáblára, és válassza a **biztonsági mentés időzítése** elemet.
2. Az **ütemezett biztonsági mentés módosítása vagy leállítása** lapon válassza a **Leállítás a biztonsági mentési ütemezés használatával lehetőséget, és törölje az összes tárolt biztonsági** mentést. Ezután válassza a **tovább** lehetőséget.

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Az **ütemezett biztonsági mentés leállítása** lapon válassza a **Befejezés** lehetőséget.

    ![Ütemezett biztonsági mentés leállítása és a Befejezés kiválasztása](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. A rendszer felszólítja, hogy adjon meg egy biztonsági PIN-kódot (személyes azonosító számot), amelyet manuálisan kell előkészítenie. Ehhez először jelentkezzen be a Azure Portalba.
5. Nyissa meg **Recovery Services** tároló  >  **beállításai**  >  **tulajdonságokat**.
6. A **biztonsági PIN-kód** területen válassza a **készítés** elemet. Másolja ezt a PIN-kódot. A PIN-kód csak öt percig érvényes.
7. A felügyeleti konzolon illessze be a PIN-kódot, majd kattintson **az OK gombra**.

    ![Biztonsági PIN-kód létrehozása.](./media/backup-azure-delete-vault/security-pin.png)

8. A **biztonsági mentési folyamat módosítása** lapon a következő üzenet jelenik meg: a *törölt biztonsági mentési adat 14 napig megmarad. Ez idő után véglegesen törlődik a biztonsági mentési adatvesztés.*  

    ![Biztonsági mentési folyamat módosítása](./media/backup-azure-delete-vault/deleted-backup-data.png)

A helyszíni biztonsági mentési elemek törlését követően kövesse a portál következő lépéseit.

## <a name="re-enable-protection"></a>Védelem újbóli engedélyezése

Ha leállította a védelmet, miközben megtartja az adatvédelmet, és úgy döntött, hogy folytatja a védelmet, akkor a biztonsági mentési szabályzat módosításával újból engedélyezheti a biztonsági mentést.

1. A **műveletek** lapon válassza az **ütemezett biztonsági mentés** lehetőséget.
1. Válassza a **biztonsági mentési ütemterv újbóli engedélyezése lehetőséget. A biztonsági másolati elemeket vagy az időpontokat is módosíthatja** , és a **tovább** gombra kattintva.<br>

    ![Biztonsági mentési ütemterv újbóli engedélyezése](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. A **biztonsági mentés elemek kijelölése** területen válassza a **tovább** lehetőséget.

    ![Biztonsági mentésre kijelölt elemek kiválasztása](./media/backup-azure-manage-mars/re-enable-next.png)
1. A **biztonsági mentés** időpontjának megadása lapon adja meg a biztonsági mentés ütemtervét, és kattintson a **Tovább gombra**.
1. A **megőrzési szabály kiválasztása** területen adja meg a megőrzési időtartamot, majd kattintson a **Tovább gombra**.
1. Végül a **megerősítő** képernyőn tekintse át a szabályzat részleteit, és válassza a **Befejezés** lehetőséget.

## <a name="re-generate-passphrase"></a>Jelszó újbóli előállítása

A hitelesítő adatok titkosítására és visszafejtésére szolgálnak a helyszíni vagy helyi gép a MARS-ügynökkel vagy az Azure-ból történő biztonsági mentése vagy visszaállítása során. Ha elvesztette vagy elfelejtette a jelszót, akkor újra létrehozhatja a jelszót (ha a számítógép továbbra is regisztrálva van a Recovery Services-tárolóban, és a biztonsági mentés konfigurálva van), kövesse az alábbi lépéseket:

1. A Mars-ügynök konzolján lépjen a **műveletek ablaktáblára**, és  >  **módosítsa a tulajdonságok** >. Ezután nyissa meg a **titkosítás lapot**.<br>
1. Válassza a **jelszó módosítása** jelölőnégyzetet.<br>
1. Adjon meg egy új jelszót, vagy válassza a **jelszó létrehozása** lehetőséget.
1. Az új jelszó mentéséhez kattintson a **Tallózás** gombra.

    ![Jelszó előállítása.](./media/backup-azure-manage-mars/passphrase.png)

1. A módosítások alkalmazásához kattintson **az OK gombra** .  Ha a [biztonsági funkció](./backup-azure-security-feature.md#enable-security-features) engedélyezve van a Recovery Services-tároló Azure Portalján, a rendszer kérni fogja a biztonsági PIN-kód megadását. A PIN-kód fogadásához kövesse az ebben a [cikkben](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)ismertetett lépéseket.<br>
1. Illessze be a biztonsági PIN-kódot a portálról, majd kattintson az **OK gombra** a módosítások alkalmazásához.<br>

    ![A biztonsági PIN-kód beillesztése](./media/backup-azure-manage-mars/passphrase2.png)
1. Győződjön meg arról, hogy a jelszó biztonságos módon mentve van egy másik helyen (a forrásoldali gépen kívül), lehetőleg a Azure Key Vault. Tartsa nyomon az összes hozzáférési kódot, ha több géppel is rendelkezik a MARS-ügynökökkel való biztonsági mentéssel.

## <a name="managing-backup-data-for-unavailable-machines"></a>Nem elérhető gépek biztonsági mentési információinak kezelése

Ez a szakasz egy olyan forgatókönyvet tárgyal, amelyben a MARStal védett forrásoldali gép már nem érhető el, mert törölték, megsérült, kártevő szoftverrel/ransomware vagy leszerelt.

Ezen gépek esetében a Azure Backup szolgáltatás biztosítja, hogy a legutóbbi helyreállítási pont ne járjon le (azaz ne legyen metszve) a biztonsági mentési szabályzatban megadott megőrzési szabályoknak megfelelően. Ezért biztonságosan visszaállíthatja a gépet.  Vegye figyelembe a következő forgatókönyveket a biztonsági másolatban szereplő adatokon:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>1. forgatókönyv: a forrásszámítógép nem érhető el, és már nem kell megőriznie a biztonsági mentési adatait

- A biztonsági másolatban szereplő adatok törlését a Azure Portal a [cikkben](backup-azure-delete-vault.md#delete-protected-items-on-premises)felsorolt lépések alapján törölheti.

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>2. forgatókönyv: a forrásszámítógép nem érhető el, és meg kell őriznie a biztonsági másolatok adatait

A MARS biztonsági mentési szabályzatának kezelése a MARS-konzolon, és nem a portálon keresztül történik. Ha meg kell hosszabbítani a meglévő helyreállítási pontok megőrzési beállításait a lejárat előtt, akkor vissza kell állítania a gépet, telepítenie kell a MARS-konzolt, és ki kell terjesztenie a szabályzatot.

- A gép visszaállításához hajtsa végre a következő lépéseket:
  1. [A virtuális gép visszaállítása egy másik célszámítógépre](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. Hozza létre újra a célszámítógép nevét ugyanazzal az állomásnévvel, mint a forrásoldali géppel
  1. Telepítse az ügynököt, és regisztrálja újra ugyanazon a tárolón és ugyanazzal a jelszóval
  1. Indítsa el a MARS-ügyfelet, hogy az igényeinek megfelelően kiterjessze a megőrzési időtartamot
- Az újonnan visszaállított, a MARStal védett gép továbbra is készít biztonsági mentést.  

## <a name="configuring-antivirus-for-the-mars-agent"></a>A víruskereső konfigurálása a MARS-ügynökhöz

Javasoljuk, hogy a víruskereső szoftverhez a következő konfigurációt a MARS-ügynök működésével való ütközés elkerülése érdekében.

1. **Elérési út kizárása**: a teljesítmény és a lehetséges ütközések romlásának elkerülése érdekében zárja ki az alábbi elérési utakat a víruskereső szoftver valós idejű figyelésével:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` és almappák
    1. **Üres mappa**: Ha a kaparós mappa nem a szabványos helyen található, adja hozzá a kivételekhez is.  A mappa helyének meghatározásához [szükséges lépéseket itt találja](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible) .
1. **Bináris Kizárások hozzáadása**: Ha el szeretné kerülni a biztonsági mentési és a konzol tevékenységeinek romlását, zárja ki a következő bináris fájlokat a víruskereső szoftver valós idejű figyelésével:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>Az elérési utak kizárása azonban elegendő a legtöbb víruskereső szoftverhez, némelyik továbbra is zavarhatja a MARS-ügynök műveleteit. Ha váratlan hibák merülnek fel, távolítsa el ideiglenesen a víruskereső szoftvert, és figyelje meg, hogy a probléma leáll-e. Ha ez megoldja a problémát, forduljon a víruskereső szoftver gyártójához, és kérjen segítséget a termék megfelelő konfigurációjával.

## <a name="next-steps"></a>Következő lépések

- A támogatott forgatókönyvekkel és korlátozásokkal kapcsolatos információkért tekintse meg a [Mars-ügynök támogatási mátrixát](./backup-support-matrix-mars-agent.md).
- További információ az [igény szerinti biztonsági mentési szabályzat megőrzési viselkedéséről](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
- További gyakori kérdésekért lásd a Mars- [ügynök gyakori](backup-azure-file-folder-backup-faq.md)kérdéseit.
