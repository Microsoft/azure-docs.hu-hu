---
title: MARS-ügynök biztonsági mentésének kezelése és figyelése
description: Megtudhatja, hogyan kezelheti és figyelheti Microsoft Azure Recovery Services- (MARS-) ügynök biztonsági másolatokat az Azure Backup használatával.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 4306f01d608542f7453b32b32a1a6894c2379159
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515022"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>A Microsoft Azure (MARS) ügynök biztonsági mentésének kezelése az Azure Backup szolgáltatással

Ez a cikk bemutatja, hogyan kezelheti a Microsoft Azure Recovery Services-ügynökkel biztonsági mentése során biztonsági Microsoft Azure mappákat.

## <a name="modify-a-backup-policy"></a>Biztonsági mentési szabályzat módosítása

A biztonsági mentési házirend módosításakor új elemeket adhat hozzá, eltávolíthatja a meglévő elemeket a biztonsági mentésből, vagy kizárhat fájlok biztonsági mentését a Kizárási beállítások használatával.

- **Az Elemek hozzáadása** lehetőség csak új elemek hozzáadására használható a biztonságimentni kívánt elemekhez. Meglévő elemek eltávolításához használja az **Elemek eltávolítása vagy** a **Kizárási beállítások lehetőséget.**  
- **Az Elemek eltávolítása** lehetőséggel eltávolíthatja az elemek biztonságimentett elemeit.
  - Az **Elemek eltávolítása helyett** használja a Kizárási beállításokat a köteten belüli összes elem **eltávolítására.**
  - Ha törli egy köteten az összes kijelölést, akkor az elemek régi biztonsági másolatai az utolsó biztonsági mentéskor a megőrzési beállításoknak megfelelően lesznek megőrizve, módosítási hatókör nélkül.
  - Az elemek újra kijelölését, az első teljes biztonsági mentéshez vezet, és a szabályzat új módosításai nem vonatkoznak a régi biztonsági mentésekre.
  - A teljes kötet kijelölésének mellőzését a rendszer a megőrzési szabályzat módosítása nélkül őrzi meg a korábbi biztonsági másolatokat.
- **A kizárási** beállítások ezzel a beállítással zárnak ki bizonyos elemeket a biztonságimentésből.

### <a name="add-new-items-to-existing-policy"></a>Új elemek hozzáadása meglévő szabályzathoz

1. Az **Actions (Műveletek)** mezőben válassza **a Schedule Backup (Biztonsági mentés ütemezése) lehetőséget.**

    ![Windows Server biztonsági mentés ütemezése](./media/backup-configure-vault/schedule-first-backup.png)

2. A **Select policy item (Szabályzatelem kiválasztása)** lapon válassza a Modify backup schedule for your files and folders (Biztonsági mentési ütemezés módosítása **a** fájlokhoz és mappákhoz) lehetőséget, majd válassza a Next (Tovább) **lehetőséget.**

    ![Szabályzatelemek kiválasztása](./media/backup-azure-manage-mars/select-policy-items.png)

3. A **Biztonsági mentés ütemezésének módosítása vagy leállítása lapon** válassza a Módosítások a biztonsági mentési **elemeken** vagy időpontokban lehetőséget, majd válassza a **Tovább lehetőséget.**

    ![Biztonsági mentés módosítása vagy ütemezése](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Az **Elemek kijelölése a biztonsági mentéshez lapon** válassza az **Elemek** hozzáadása lehetőséget a biztonsági mentésre kívánt elemek hozzáadásához.

    ![Biztonsági mentési elemek módosítása vagy ütemezése](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Az **Elemek kiválasztása ablakban** válassza ki a hozzáadni kívánt fájlokat vagy mappákat, majd kattintson az OK **gombra.**

    ![Az elemek kiválasztása](./media/backup-azure-manage-mars/select-item.png)

6. A művelet befejezéséhez kövesse a következő lépéseket, **és** válassza a Befejezés lehetőséget.

### <a name="add-exclusion-rules-to-existing-policy"></a>Kizárási szabályok hozzáadása meglévő szabályzathoz

Kizárási szabályokat adhat hozzá, hogy kihagyja azokat a fájlokat és mappákat, amelyekről nem szeretne biztonsági mentéset. Ezt új szabályzat definiálása vagy egy meglévő szabályzat módosítása során is meg lehet tenni.

1. A Műveletek panelen válassza a Biztonsági **mentés ütemezése lehetőséget.** Válassza az **Elemek kiválasztása a biztonsági mentéshez lehetőséget,** majd válassza a **Kizárási beállítások lehetőséget.**

    ![Kizárási beállítások](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. A **Kizárási beállítások lapon** válassza a Kizárás hozzáadása **lehetőséget.**

    ![Kizárás hozzáadása](./media/backup-azure-manage-mars/add-exclusion.png)

3. A Select Items to Exclude (Elemek **kijelölése a kizárni)** menüben tallózhatja a fájlokat és mappákat, és kiválaszthatja a kizárni kívánt elemeket, majd kattintson az **OK gombra.**

    ![A kizárni kívánt elemek kiválasztása](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Alapértelmezés szerint a kijelölt **mappákban** lévő almappák ki vannak zárva. Ezt az Igen vagy a Nem **lehetőség választásával** **módosíthatja.** Az alábbiak szerint szerkesztheti és megadhatja a kizárni kívánt fájltípusokat:

    ![Almappák típusainak kiválasztása](./media/backup-azure-manage-mars/subfolders-type.png)

5. A művelet befejezéséhez kövesse a következő lépéseket, **és válassza** a Befejezés lehetőséget.

### <a name="remove-items-from-existing-policy"></a>Elemek eltávolítása meglévő szabályzatból

1. A Műveletek panelen válassza a Biztonsági **mentés ütemezése lehetőséget.** Válassza a Select items to Backup (Elemek **kiválasztása a biztonsági mentéshez) lehetőséget.** A listából válassza ki a biztonsági mentési ütemezésből eltávolítani kívánt fájlokat és mappákat, majd válassza az **Elemek eltávolítása lehetőséget.**

    ![Az eltávolítani kívánt elemek kiválasztása](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Körültekintően járjon el, ha teljesen eltávolít egy kötetet a házirendből.  Ha újra hozzá kell adni, a rendszer új kötetként fogja kezelni. A következő ütemezett biztonsági mentés kezdeti biztonsági mentést (teljes biztonsági mentést) hajt végre a növekményes biztonsági mentés helyett. Ha később ideiglenesen el kell távolítania és hozzá kell  adni elemeket,  ajánlott a Kizárási beállításokat használni az Elemek eltávolítása helyett, hogy a teljes biztonsági mentés helyett növekményes biztonsági mentést használjon.

2. A művelet befejezéséhez kövesse a következő lépéseket, **majd válassza** a Befejezés lehetőséget.

## <a name="stop-protecting-files-and-folder-backup"></a>A fájlok és mappák biztonsági mentésének védelemének leállítása

A fájlok és mappák biztonsági mentésének védelmét kétféleképpen állíthatja le:

- **Állítsa le a védelmet, és őrizze meg a biztonsági mentési adatokat.**
  - Ez a beállítás leállítja az összes jövőbeli biztonsági mentési feladat védelmét.
  - Azure Backup szolgáltatás továbbra is megőrzi az összes meglévő helyreállítási pontot.  
  - A nem lekért helyreállítási pontok biztonsági másolati adatait vissza tudja állítani.
  - Ha úgy dönt, hogy folytatja a védelmet, használhatja a *Biztonsági mentés ütemezésének újra engedélyezése beállítást.* Ezt követően az adatok az új adatmegőrzési szabályzat alapján lesznek megőrizve.
- **Állítsa le a védelmet, és törölje a biztonsági mentési adatokat.**
  - Ez a beállítás leállítja, hogy a jövőbeli biztonsági mentési feladatok megvédjék az adatokat, és törölje az összes helyreállítási pontot.
  - A Biztonsági másolat adatairól értesítő e-mailt fog kapni, amely a következő üzenetet tartalmazza: A biztonsági mentési elem adatai *törölve vannak. Ezek az adatok 14* napig ideiglenesen elérhetők lesznek, ezt követően véglegesen törölve lesznek, és javasolt művelet A Biztonsági másolat elem ismételt védelme 14 napon belül az *adatok helyreállításához.*
  - A védelem folytatásához a törlési művelettől számított 14 napon belül állítsa újra a védelmet.

### <a name="stop-protection-and-retain-backup-data"></a>Védelem leállítása és a biztonsági mentési adatok megőrzése

1. Nyissa meg a MARS felügyeleti konzolt, nyissa meg a **Műveletek panelt,** és válassza **a Biztonsági mentés ütemezése lehetőséget.**

    ![Biztonsági mentés ütemezésének kiválasztása](./media/backup-azure-manage-mars/mars-actions.png)
1. A **Házirendelem kiválasztása lapon** válassza a Fájlok és mappák biztonsági mentési **ütemezésének módosítása,** majd a Tovább **lehetőséget.**

    ![Szabályzatelem kiválasztása](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Az Ütemezett **biztonsági mentés módosítása** vagy leállítása lapon válassza a Stop using this backup schedule (Ütemezett biztonsági mentés használatának leállítása ezzel a biztonsági mentési ütemezéssel) lehetőséget, de tartsa meg a tárolt biztonsági másolatokat, amíg újra nem aktivál egy **ütemezést.** Ezután válassza a **Tovább lehetőséget.**

    ![Állítson le egy ütemezett biztonsági mentést.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Az **Ütemezett biztonsági mentés szüneteltetése beállításban** tekintse át az adatokat, és válassza a Befejezés **lehetőséget.**

    ![Ütemezett biztonsági mentés szüneteltetése.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. A **Biztonsági mentés állapotának** módosítása beállításban ellenőrizze, hogy az ütemezett biztonsági mentés szüneteltetése sikeres állapotú-e, és válassza a **Bezárás** lehetőséget a befejezéshez.

### <a name="stop-protection-and-delete-backup-data"></a>Védelem leállítása és biztonsági mentési adatok törlése

1. Nyissa meg a MARS felügyeleti konzolt, nyissa meg a **Műveletek panelt,** és válassza a **Biztonsági mentés ütemezése lehetőséget.**
2. Az Ütemezett **biztonsági mentés módosítása** vagy leállítása lapon válassza a Stop using this backup schedule (Leállítás a biztonsági mentés ütemezésének használatával) lehetőséget, és törölje az összes tárolt biztonsági **mentést.** Ezután válassza a **Tovább lehetőséget.**

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Az Ütemezett **biztonsági mentés befejezése lapon válassza a** Befejezés **lehetőséget.**

    ![Ütemezett biztonsági mentés befejezése és a befejezés kiválasztása](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. A rendszer kéri, hogy adja meg a biztonsági PIN-kódot (személyes azonosító számot), amelyet manuálisan kell létrehoznia. Ehhez először jelentkezzen be a Azure Portal.
5. Ugrás a **Recovery Services-tároló**  >  **beállításainak**  >  **tulajdonságaira.**
6. A **Biztonsági PIN-kód alatt** válassza a Generate **(Generálás) lehetőséget.** Másolja ki ezt a PIN-kódot. A PIN-kód csak öt percig érvényes.
7. A felügyeleti konzolon illessze be a PIN-kódot, majd kattintson az **OK gombra.**

    ![Biztonsági PIN-kód létrehozása.](./media/backup-azure-delete-vault/security-pin.png)

8. A Biztonsági **mentés folyamatának** módosítása lapon a következő üzenet jelenik meg: A törölt biztonsági mentési adatok *14 napig lesznek megőrizve. Ezt követően a biztonsági mentési adatok véglegesen törlődnek.*  

    ![Biztonsági mentés folyamatának módosítása](./media/backup-azure-delete-vault/deleted-backup-data.png)

A helyszíni biztonsági mentési elemek törlését követően kövesse a következő lépéseket a portálon.

## <a name="re-enable-protection"></a>Védelem újra engedélyezése

Ha leállította a védelmet az adatok megőrzése közben, és úgy döntött, hogy folytatja a védelmet, akkor újra engedélyezheti a biztonsági mentési ütemezést a biztonsági mentési házirend módosításával.

1. A **Műveletek oldalon válassza** a Biztonsági mentés **ütemezése lehetőséget.**
1. Válassza **a Biztonsági mentés ütemezésének újra engedélyezése lehetőséget. Módosíthatja a biztonsági mentési elemeket vagy időpontokat is,** és kiválaszthatja a **Tovább lehetőséget.**<br>

    ![Biztonsági mentés ütemezésének újra engedélyezése](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. A **Select Items to Backup (Biztonsági mentésre kívánt elemek kiválasztása) menüben** válassza a Next **(Tovább) lehetőséget.**

    ![Biztonsági mentésre kívánt elemek kiválasztása](./media/backup-azure-manage-mars/re-enable-next.png)
1. A **Biztonsági mentés ütemezésének megadása beállításban** adja meg a biztonsági mentés ütemezését, majd válassza a **Tovább lehetőséget.**
1. Az **Adatmegőrzési szabályzat kiválasztása beállításban** adja meg a megőrzés időtartamát, majd válassza a **Tovább lehetőséget.**
1. Végül a Megerősítés **képernyőn tekintse** át a szabályzat részleteit, és válassza a **Befejezés lehetőséget.**

## <a name="re-generate-passphrase"></a>Jelszó újra létrehozása

A jelszó az adatok titkosítására és visszafejtéséhez használható a helyszíni vagy helyi gép Mars-ügynökkel vagy az Azure-ból való biztonsági mentésével vagy visszaállításával. Ha elveszett vagy elfelejtette a jelszót, az alábbi lépésekkel újragenerálhatja a jelszót (feltéve, hogy a gép még regisztrálva van a Recovery Services-tárolóban, és a biztonsági mentés konfigurálva van):

1. A MARS-ügynökkonzolon válassza a **Műveletek panel** Tulajdonságok  >  **módosítása >.** Ezután válassza a **Titkosítás lapot.**<br>
1. Jelölje **be a Jelszó módosítása jelölőnégyzetet.**<br>
1. Adjon meg egy új jelszót, vagy válassza a **Jelszó létrehozása lehetőséget.**
1. Az **új jelszó** mentéshez válassza a Tallózás lehetőséget.

    ![Jelszó létrehozása.](./media/backup-azure-manage-mars/passphrase.png)

1. A **módosítások alkalmazáshoz** kattintson az OK gombra.  Ha [a Biztonsági funkció engedélyezve](./backup-azure-security-feature.md#enable-security-features) van a Azure Portal a Recovery Services-tárolóhoz, a rendszer kérni fogja a biztonsági PIN-kód beíratására. A PIN-kód fogadáshoz kövesse az ebben a cikkben felsorolt [lépéseket.](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)<br>
1. Illessze be a biztonsági PIN-kódot a portálról, és kattintson **az OK gombra** a módosítások alkalmazáshoz.<br>

    ![A biztonsági PIN-kód beillesztése](./media/backup-azure-manage-mars/passphrase2.png)
1. Győződjön meg arról, hogy a jelszó biztonságosan mentve van egy másik helyen (a forrásgépen kívül), lehetőleg a Azure Key Vault. Kövesse nyomon az összes jelszót, ha több gépről is biztonságimentés történik a MARS-ügynökökkel.

## <a name="managing-backup-data-for-unavailable-machines"></a>Nem elérhető gépek biztonsági mentési adatainak kezelése

Ez a szakasz egy olyan forgatókönyvet tárgyal, amelyben a MARS által védett forrásgép már nem érhető el, mert törölték, sérültek, kártevők/zsarolóprogramok fertőzöttek, vagy leszerelték.

Ezeknél a gépeknél a Azure Backup szolgáltatás biztosítja, hogy a legutóbbi helyreállítási pont ne járjon le (azaz ne törlődik) a biztonsági mentési szabályzatban megadott megőrzési szabályoknak megfelelően. Így biztonságosan visszaállíthatja a gépet.  Vegye figyelembe a következő forgatókönyveket, amelyek a biztonságimentett adatokon hajtatnak végre:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>1. forgatókönyv: A forrásgép nem érhető el, és már nem kell megőriznie a biztonsági mentési adatokat

- A biztonságimentett adatokat a(z) Azure Portal a cikkben felsorolt [lépésekkel törölheti.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>2. forgatókönyv: A forrásgép nem érhető el, és meg kell őriznie a biztonsági mentési adatokat

A MARS biztonsági mentési szabályzatának kezelése a MARS-konzolon keresztül történik, nem a portálon. Ha ki kell terjesztenie a meglévő helyreállítási pontok megőrzési beállításait a lejáratuk előtt, akkor vissza kell állítania a gépet, telepítenie kell a MARS-konzolt, és ki kell terjesztenie a szabályzatot.

- A gép visszaállításához hajtsa végre a következő lépéseket:
  1. [A virtuális gép visszaállítása egy másik célgépre](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. Hozza létre újra a célgépet a forrásgéppel azonos állomásnévvel
  1. Telepítse az ügynököt, és regisztráljon újra ugyanerre a tárolóra ugyanazokkal a jelszóval
  1. Indítsa el a MARS-ügyfelet, hogy az igényeinek megfelelően meghosszabbítsa a megőrzési időtartamot
- Az újonnan visszaállított, MARS által védett gép továbbra is biztonsági másolatokat készít.  

## <a name="configuring-antivirus-for-the-mars-agent"></a>Víruskereső konfigurálása a MARS-ügynökhöz

A MARS-ügynök működésével való ütközések elkerülése érdekében javasoljuk a következő konfigurációt a víruskereső szoftverhez.

1. **Elérésiút-kizárások hozzáadása:** A teljesítménycsökkenés és a lehetséges ütközések elkerülése érdekében zárja ki a következő elérési utakat a víruskereső szoftver valós idejű figyelésből:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` és almappák
    1. Scratch folder (Scratch **mappa):** Ha az scratch mappa nem a szokásos helyen található, azt is vegye fel a kivételekbe.  [Az új mappa helyének](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-) meghatározásához szükséges lépéseket itt láthatja.
1. **Bináris kizárások hozzáadása:** A biztonsági mentési és konzoltevékenységek teljesítménycsökkenésének elkerülése érdekében zárja ki a következő bináris fájlok folyamatait a víruskereső szoftver által végzett valós idejű figyelésből:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>Bár az elérési utak kizárása elegendő a legtöbb víruskereső szoftverhez, előfordulhat, hogy némelyik továbbra is zavarja a MARS-ügynök működését. Ha váratlan hibákat lát, távolítsa el ideiglenesen a víruskereső szoftvert, és figyelje meg, hogy a probléma megoldatlan-e. Ha ez megoldja a problémát, forduljon a víruskereső szoftver gyártójához a termék megfelelő konfigurációjával kapcsolatos segítségért.

## <a name="next-steps"></a>Következő lépések

- A támogatott forgatókönyvekkel és korlátozásokkal kapcsolatos információkért tekintse meg a MARS-ügynök támogatási [mátrixát.](./backup-support-matrix-mars-agent.md)
- További információ az igény szerinti biztonsági [mentési szabályzatok megőrzési viselkedéséről.](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)
- További gyakori kérdésekért tekintse meg a [MARS-ügynökkel kapcsolatos gyakori kérdéseket.](backup-azure-file-folder-backup-faq.yml)
