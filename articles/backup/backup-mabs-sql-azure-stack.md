---
title: SQL Server munkaterhelések biztonsági mentése Azure Stack
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja Microsoft Azure Backup Servert (MABS) SQL Server-adatbázisok védelmére Azure Stackon.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 80de7913b010fca69c3703e423109f2ede653590
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91332814"
---
# <a name="back-up-sql-server-on-azure-stack"></a>SQL Server biztonsági mentése Azure Stack

Ebből a cikkből megtudhatja, Microsoft Azure Backup-kiszolgáló (MABS) SQL Server-adatbázisok védelméhez Azure Stack.

Az Azure-ba való SQL Server adatbázis biztonsági mentésének és az Azure-ba történő helyreállításnak a kezelése három lépést tesz szükségessé:

1. Biztonsági mentési szabályzat létrehozása SQL Server adatbázisok védelméhez
2. Igény szerinti biztonsági másolatok létrehozása
3. Az adatbázis helyreállítása lemezekről és az Azure-ból

## <a name="prerequisites-and-limitations"></a>Előfeltételek és korlátozások

* Ha olyan adatbázist használ, amelynek fájljai távoli fájlmegosztásban találhatók, a védelem sikertelen lesz, és a 104-es azonosítójú hibaüzenet jelenik meg. A MABS nem támogatja a távoli fájlmegosztás SQL Server-adatvédelmének védelmét.
* A MABS nem tudja megóvni a távoli SMB-megosztásokon tárolt adatbázisokat.
* Győződjön meg arról, hogy a [rendelkezésre állási csoport replikái csak olvashatóként vannak konfigurálva](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server).
* Explicit módon hozzá kell adnia a rendszerfiók **NTAuthority\System** a sysadmin (rendszergazda) csoporthoz SQL Serveron.
* Ha egy másik helyre történő helyreállítást hajt végre egy részlegesen tárolt adatbázis esetében, győződjön meg arról, hogy a célként megadott SQL-példányon engedélyezve van a [tárolt adatbázisok](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable) funkció.
* Ha egy másik helyre történő helyreállítást hajt végre egy file stream-adatbázis esetében, akkor győződjön meg arról, hogy a cél SQL-példányon engedélyezve van a [file stream Database](/sql/relational-databases/blob/enable-and-configure-filestream) szolgáltatás.
* Az SQL Server AlwaysOn rendelkezésre állási csoportok védelme:
  * A MABS észleli a rendelkezésre állási csoportokat, amikor lekérdezést futtat a védelmi csoport létrehozásakor.
  * A MABS észleli a feladatátvételt, és folytatja az adatbázis védelmét.
  * A MABS támogatja a többhelyes fürtök konfigurációját SQL Server egy példányához.
* A AlwaysOn szolgáltatást használó adatbázisok védelméhez a MABS a következő korlátozásokkal rendelkezik:
  * A MABS tiszteletben tartja a biztonsági mentési szabályzatot a SQL Serverban beállított rendelkezésre állási csoportok számára, a következő módon:
    * Másodlagos előnyben részesítése – A biztonsági mentéseket egy másodlagos replikán kell végrehajtani, kivéve ha az elsődleges replika az egyetlen online replika. Ha több másodlagos replika érhető el, akkor a legmagasabb biztonsági mentési prioritású csomópont lesz kiválasztva a biztonsági mentéshez. Ha csak az elsődleges replika érhető el, akkor a biztonsági mentést az elsődleges replikán kell megtörténnie.
    * Csak másodlagos – Az elsődleges replikán nem hajtható végre biztonsági mentés. Ha az elsődleges replika az egyetlen online replika, a biztonsági mentés nem lesz végrehajtva.
    * Elsődleges – A biztonsági mentéseket mindig az elsődleges replikán kell végrehajtani.
    * Bármely replika – A biztonsági mentések bármelyik rendelkezésre állási replikán végrehajthatók a rendelkezésre állási csoportban. A biztonsági mentéshez használt csomópont kiválasztása a csomópontok biztonsági mentési prioritása alapján történik.
  * Vegye figyelembe a következőket:
    * A biztonsági mentések bármilyen olvasható replikáról megtörténhetnek – ez az elsődleges, a szinkron másodlagos, az aszinkron másodlagos.
    * Ha bármely replika ki van zárva a biztonsági mentésből, például a **kizárási replika** engedélyezve van, vagy nem olvashatóként van megjelölve, akkor a replika nem lesz kiválasztva a biztonsági mentéshez az egyik lehetőség közül.
    * Ha több replika érhető el és olvasható, akkor a legmagasabb biztonsági mentési prioritású csomópont lesz kiválasztva a biztonsági mentéshez.
    * Ha a biztonsági mentés sikertelen a kiválasztott csomóponton, a biztonsági mentési művelet meghiúsul.
    * Az eredeti helyre történő helyreállítás nem támogatott.
* SQL Server 2014 vagy újabb biztonsági mentési problémák:
  * Az SQL Server 2014 új funkciót adott hozzá, amely létrehoz egy adatbázist a helyszíni SQL Server számára a [Windows Azure Blob Storage-ban](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure). A MABS nem használható ennek a konfigurációnak a megvédésére.
  * Létezik néhány ismert probléma, amely a "másodlagos előnyben részesített" biztonsági mentést részesíti előnyben az SQL-AlwaysOn beállításnál. A MABS mindig a másodlagos biztonsági mentést végzi. Ha nem található másodlagos, akkor a biztonsági mentés sikertelen lesz.

## <a name="before-you-start"></a>Előkészületek

[Azure Backup Server telepítése és előkészítése](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Biztonsági mentési szabályzat létrehozása SQL Server adatbázisok védelméhez az Azure-ban

1. A Azure Backup Server felhasználói felületen válassza a **védelem** munkaterületet.

2. Az eszközsávon kattintson az **új** elemre egy új védelmi csoport létrehozásához.

    ![Védelmi csoport létrehozása](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup Server elindítja a védelmi csoport varázslót, amely végigvezeti a **védelmi csoport** létrehozásának lépésein. Kattintson a **Tovább** gombra.

3. A **védelmi csoport típusának kiválasztása** képernyőn válassza a **kiszolgálók** elemet.

    ![Védelmi csoport típusának kiválasztása – "kiszolgálók"](./media/backup-azure-backup-sql/pg-servers.png)

4. A csoporttagok **kiválasztása** képernyőn az elérhető tagok lista a különböző adatforrásokat jeleníti meg. Válassza ki a **+** mappa kibontásához és az almappák megjelenítéséhez. Jelölje be a jelölőnégyzetet egy elem kiválasztásához.

    ![SQL-adatbázis kiválasztása](./media/backup-azure-backup-sql/pg-databases.png)

    Az összes kijelölt elem megjelenik a kijelölt tagok listájában. A védelemmel ellátni kívánt kiszolgálók vagy adatbázisok kiválasztása után válassza a **tovább** lehetőséget.

5. Az **adatvédelmi módszer kiválasztása** képernyőn adja meg a védelmi csoport nevét, és jelölje be a szeretnék **online védelem** jelölőnégyzetet.

    ![Adatvédelmi módszer – rövid távú lemez & online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. A **Short-Term célok megadása** képernyőn adja meg a szükséges bemeneteket a lemezre történő biztonsági mentési pontok létrehozásához, majd kattintson a **tovább** gombra.

    A példában a **megőrzési** időtartam **5 nap**, a **szinkronizálás gyakorisága** **15 percenként** történik, ami a biztonsági mentés gyakorisága. Az **expressz teljes biztonsági mentés** **8:00 P. M** értékre van állítva.

    ![Rövid távú célok](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > A következő példában a rendszer minden nap 8:00 ÓRAKOR létrehoz egy biztonsági mentési pontot a módosított adatoknak az előző nap 8:00 PM biztonsági mentési pontjából való átadásával. Ezt a folyamatot **expressz teljes biztonsági mentésnek** nevezzük. A tranzakciós naplók 15 percenként szinkronizálhatók. Ha az adatbázist 9:00 ÓRAKOR kell helyreállítani, a pont az utolsó expressz teljes biztonsági mentési pont naplóiból jön létre (ebben az esetben 08:00).
   >
   >

7. A **lemez kiosztásának áttekintése** képernyőn ellenőrizze a rendelkezésre álló teljes tárolóhelyet, valamint a lehetséges lemezterületet. Kattintson a **Tovább** gombra.

8. A **replika-létrehozási módszer választása** területen válassza ki, hogyan hozza létre az első helyreállítási pontot. A kezdeti biztonsági mentést manuálisan (hálózaton kívül) is átviheti a sávszélesség-torlódás vagy a hálózat felett. Ha úgy dönt, hogy megvárja az első biztonsági mentést, megadhatja a kezdeti átvitel időpontját. Kattintson a **Tovább** gombra.

    ![Kezdeti replikációs módszer](./media/backup-azure-backup-sql/pg-manual.png)

    A kezdeti biztonsági másolathoz a teljes adatforrást (SQL Server adatbázist) át kell vinni az üzemi kiszolgálóról (SQL Server számítógépről) a Azure Backup Serverre. Ezek az adatmennyiségek nagy méretűek lehetnek, és a hálózaton keresztüli adatátvitel meghaladhatja a sávszélességet. Ebből kifolyólag dönthet úgy, hogy áthelyezi a kezdeti biztonsági másolatot: **manuálisan** (cserélhető adathordozó használatával) a sávszélesség-torlódás elkerüléséhez, vagy **automatikusan a hálózaton keresztül** (adott időpontban).

    A kezdeti biztonsági mentés befejezését követően a biztonsági mentések a kezdeti biztonsági másolaton lévő növekményes biztonsági másolatok. A növekményes biztonsági mentések általában kicsik, és könnyen átvihetők a hálózaton keresztül.

9. Válassza ki, hogy mikor szeretné futtatni a konzisztencia-ellenőrzést, és válassza a **tovább** lehetőséget.

    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sql/pg-consistent.png)

    A Azure Backup Server konzisztencia-ellenőrzést végez a biztonsági mentési pont integritásán. Azure Backup Server kiszámítja a biztonságimásolat-fájl ellenőrzőösszegét az üzemi kiszolgálón (SQL Server számítógép ebben a forgatókönyvben), valamint az adott fájl biztonsági másolatait. Ütközés esetén feltételezhető, hogy a biztonsági másolatban szereplő fájl Azure Backup Server sérült. Azure Backup Server helyesbíti a biztonsági másolatban szereplő adatokat úgy, hogy elküldi az ellenőrzőösszeg-eltérésnek megfelelő blokkokat. Mivel a konzisztencia-ellenőrzések teljesítmény-igényesek, a konzisztencia-ellenőrzést ütemezhet vagy automatikusan futtathatja.

10. Az adatforrások online védelmének megadásához válassza ki az Azure-ba védeni kívánt adatbázisokat, és kattintson a **tovább** gombra.

    ![Adatforrások kiválasztása](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Válassza ki a szervezeti szabályzatoknak megfelelő biztonsági mentési ütemterveket és adatmegőrzési házirendeket.

    ![Ütemterv és megőrzés](./media/backup-azure-backup-sql/pg-schedule.png)

    Ebben a példában a biztonsági mentések naponta egyszer, 12:00 ÓRAKOR és 20:00 órakor (a képernyő alsó részén) jelennek meg.

    > [!NOTE]
    > Jó megoldás, ha a gyors helyreállítás érdekében néhány rövid távú helyreállítási pontot kell használnia a lemezen. Ezek a helyreállítási pontok az operatív helyreállításhoz használatosak. Az Azure jó helyen található, magasabb SLA-kat és garantált rendelkezésre állást kínál.
    >
    >

    **Ajánlott eljárás**: Ha az Azure-ba történő biztonsági mentést úgy ütemezi, hogy a helyi lemezes biztonsági mentések befejezését követően induljon el, a rendszer mindig az Azure-ba másolja a legújabb lemez

12. Válassza ki az adatmegőrzési szabály ütemtervét. Az adatmegőrzési szabályzat működésének részletes leírását a [használati Azure Backup a szalagos infrastruktúra lecserélése című cikkben](backup-azure-backup-cloud-as-tape.md)találja.

    ![Adatmegőrzési házirend](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Ebben a példában:

    * A biztonsági mentések naponta egyszer, 12:00 és 8 ÓRAKOR (a képernyő alsó részén) jelennek meg, és 180 napig őrződnek meg.
    * A biztonsági mentés szombaton, 12:00 órakor 104 hétig megőrzött
    * A biztonsági mentés az elmúlt szombaton 12:00 órakor 60 hónapig megőrzött
    * A biztonsági mentés március 12:00 órakor, az utolsó szombaton 10 évig őrzi meg
13. Válassza a **tovább** lehetőséget, és válassza ki a megfelelő lehetőséget a kezdeti biztonsági másolat Azure-ba történő átviteléhez. Automatikusan kiválaszthatja **a hálózaton keresztül**

14. Ha áttekinti a szabályzat részleteit az **Összefoglalás** képernyőn, válassza a **csoport létrehozása** lehetőséget a munkafolyamat befejezéséhez. Válassza a **Bezárás** lehetőséget, és figyelje a feladatok előrehaladását a figyelés munkaterületen.

    ![Védelmi csoport létrehozása In-Progress](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>SQL Server-adatbázis igény szerinti biztonsági mentése

Míg az előző lépések létrehozott egy biztonsági mentési szabályzatot, a rendszer csak az első biztonsági mentés alkalmával hozza létre a helyreállítási pontot. Ahelyett, hogy az ütemező beindítására vár, az alábbi lépések a helyreállítási pontok manuális létrehozását indítja el.

1. Várjon, amíg a védelmi csoport állapota a helyreállítási pont létrehozása előtt a-adatbázishoz tartozó **OK** értékre mutat.

    ![Védelmi csoport tagjai](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Kattintson a jobb gombbal az adatbázisra, és válassza a **helyreállítási pont létrehozása** lehetőséget.

    ![Online helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. A legördülő menüben válassza az **online védelem** lehetőséget, majd kattintson **az OK gombra** egy helyreállítási pont Azure-beli létrehozásának megkezdéséhez.

    ![Helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Tekintse meg a feladatok előrehaladását a **figyelés** munkaterületen.

    ![Figyelési konzol](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>SQL Server-adatbázis helyreállítása az Azure-ból

A következő lépések szükségesek egy védett entitás (SQL Server adatbázis) Azure-ból történő helyreállításához.

1. Nyissa meg a Azure Backup Server felügyeleti konzolt. Navigáljon a **helyreállítási** munkaterületre, ahol megtekintheti a védett kiszolgálókat. Tallózással keresse meg a szükséges adatbázist (ebben az esetben a reportserver $ MSDPM2012). Válasszon ki egy **online** pontként megadott **helyreállítási** időpontot.

    ![Helyreállítási pont kiválasztása](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Kattintson a jobb gombbal az adatbázis nevére, és válassza a **helyreállítás** lehetőséget.

    ![Helyreállítás az Azure-ból](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. A MABS a helyreállítási pont részleteit jeleníti meg. Kattintson a **Tovább** gombra. Az adatbázis felülírásához válassza a helyreállítás típust a **SQL Server eredeti példányára**. Kattintson a **Tovább** gombra.

    ![Visszaállítás az eredeti helyre](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Ebben a példában a MABS egy másik SQL Server példányra vagy egy különálló hálózati mappába helyreállítja az adatbázist.

4. A **helyreállítási beállítások megadása** képernyőn kiválaszthatja a helyreállítási beállításokat, például a hálózati sávszélesség használatának szabályozását a helyreállítás által használt sávszélesség szabályozásához. Kattintson a **Tovább** gombra.

5. Az **Összefoglalás** képernyőn az eddig megadott összes helyreállítási konfiguráció látható. Válassza a **helyreállítás** lehetőséget.

    A helyreállítás állapota a helyreállított adatbázist mutatja. A **Bezárás** gombra kattintva zárhatja be a varázslót, és megtekintheti a folyamat állapotát a **figyelés** munkaterületen.

    ![Helyreállítási folyamat elindítása](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    A helyreállítás befejeződése után a visszaállított adatbázis az alkalmazás konzisztens.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [biztonsági másolat fájljait és az alkalmazásról](backup-mabs-files-applications-azure-stack.md) szóló cikket.
Tekintse [meg a biztonsági mentés SharePoint Azure stack](backup-mabs-sharepoint-azure-stack.md) cikkben.
