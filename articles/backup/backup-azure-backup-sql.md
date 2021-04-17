---
title: Biztonsági SQL Server Azure-ba DPM számítási feladatként
description: Bevezetés a biztonsági SQL Server biztonsági mentése a Azure Backup használatával
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 03763c3bee5ee4ca5239c49c99fdbeedc242b24d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515175"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Biztonsági SQL Server Azure-ba DPM számítási feladatként

Ez a cikk végigvezeti a konfigurációs lépéseken, amelyek segítségével biztonsági SQL Server adatbázisokról a Azure Backup.

A biztonsági SQL Server Azure-ba való biztonsági mentése egy Azure-fiókra van szükség. Ha még nincs fiókja, néhány perc alatt létrehozhat egy ingyenes fiókot. További információ: [Ingyenes Azure-fiók létrehozása.](https://azure.microsoft.com/pricing/free-trial/)

Biztonsági mentése egy SQL Server Az Azure-ba és annak helyreállítása az Azure-ból:

1. Biztonsági mentési szabályzat létrehozása a SQL Server védelméhez az Azure-ban.
1. Igény szerinti biztonsági másolatok létrehozása az Azure-ban.
1. Az adatbázis helyreállítása az Azure-ból.

>[!NOTE]
>A DPM 2019 UR2 SQL Server fürt megosztott köteteket (CSV) használó feladatátvevőfürt-példányokat (FCI) támogat.<br><br>
>Ez a SQL Server támogatja a feladatátvevőfürt Közvetlen tárolóhelyek példányok [Azure-beli](../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md) SQL Server és azure-beli megosztott lemezekkel való védelmét. [](../azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md) A DPM-kiszolgálót az Azure-beli virtuális gépeken üzembe helyezett SQL FCI-példány védelme érdekében üzembe kell helyezni az Azure-beli virtuális gépen. 

## <a name="prerequisites-and-limitations"></a>Előfeltételek és korlátozások

* Ha olyan adatbázist használ, amelynek fájljai távoli fájlmegosztásban találhatók, a védelem sikertelen lesz, és a 104-es azonosítójú hibaüzenet jelenik meg. A DPM nem támogatja a SQL Server fájlmegosztáson található adatok védelmét.
* A DPM nem tudja védeni a távoli SMB-megosztáson tárolt adatbázisokat.
* Győződjön meg arról, hogy a rendelkezésre állási csoport replikái csak [olvashatóként vannak konfigurálva.](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server)
* Explicit módon hozzá kell adni az **NTAuthority\System** rendszerfiókot a sysadmin csoporthoz a SQL Server.
* Amikor egy másodlagos helyre végez helyreállítást egy részlegesen tartalmazott adatbázison, győződjön meg arról, hogy a cél sql-példányon engedélyezve van a Tartalmazott [adatbázisok](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable) szolgáltatás.
* Ha egy másik helyre végez helyreállítást egy fájlstream-adatbázison, győződjön meg arról, hogy a célKÉNT célKÉNT megcélzott SQL-példányon engedélyezve van a [fájlstream-adatbázis](/sql/relational-databases/blob/enable-and-configure-filestream) szolgáltatás.
* Az SQL Server AlwaysOn rendelkezésre állási csoportok védelme:
  * A DPM felismeri a rendelkezésre állási csoportokat, amikor lekérdezést futtat a védelmi csoport létrehozásakor.
  * A DPM felismeri a feladatátvételeket, és folytatja az adatbázis védelmét.
  * A DPM támogatja a többhelyes fürtkonfigurációkat az SQL Server-példányok esetében.
* Amikor olyan adatbázist lát el védelemmel, amely használja az AlwaysOn funkciót, a DPM-re a következő korlátozások vonatkoznak:
  * A DPM az alábbi biztonsági mentési beállítások alapján SQL Server be a rendelkezésre állási csoportokra vonatkozó biztonsági mentési házirendet:
    * Másodlagos előnyben részesítése – A biztonsági mentéseket egy másodlagos replikán kell végrehajtani, kivéve ha az elsődleges replika az egyetlen online replika. Ha több másodlagos replika is elérhető, akkor a legmagasabb biztonsági mentési prioritású csomópont lesz kiválasztva a biztonsági mentéshez. Ha csak az elsődleges replika érhető el, akkor a biztonsági mentésnek az elsődleges replikán kell lennie.
    * Csak másodlagos – Az elsődleges replikán nem hajtható végre biztonsági mentés. Ha az elsődleges replika az egyetlen online replika, a biztonsági mentés nem lesz végrehajtva.
    * Elsődleges – A biztonsági mentéseket mindig az elsődleges replikán kell végrehajtani.
    * Bármely replika – A biztonsági mentések bármelyik rendelkezésre állási replikán végrehajthatók a rendelkezésre állási csoportban. A biztonsági mentéshez használt csomópont kiválasztása a csomópontok biztonsági mentési prioritása alapján történik.
  * Vegye figyelembe a következőket:
    * A biztonsági mentések bármilyen olvasható replikáról történhetnek , azaz elsődlegesről, szinkron másodlagos replikáról, aszinkron másodlagos replikáról.
    * Ha egy replika ki  van zárva a biztonsági mentésből, például a Replika kizárása lehetőség engedélyezve van vagy nem olvashatóként van megjelölve, akkor a replika nem lesz kiválasztva a biztonsági mentéshez egyik lehetőség alatt sem.
    * Ha több replika is elérhető és olvasható, akkor a legmagasabb biztonsági mentési prioritású csomópont lesz kiválasztva a biztonsági mentéshez.
    * Ha a biztonsági mentés meghiúsul a kiválasztott csomóponton, akkor a biztonsági mentési művelet sikertelen lesz.
    * Az eredeti helyre való helyreállítás nem támogatott.
* SQL Server 2014-es vagy magasabb biztonsági mentési problémák:
  * Az SQL Server 2014 új szolgáltatással létrehozott egy adatbázist a helyszíni SQL Server [a Windows Azure Blob Storage-ban.](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) A DPM nem használható a konfiguráció védelmére.
  * Az SQL AlwaysOn beállítás "Másodlagos előnyben részesít" biztonsági mentési beállításával kapcsolatban vannak ismert problémák. A DPM mindig a másodlagosról készít biztonsági másolatot. Ha nem található másodlagos adatbázis, a biztonsági mentés sikertelen lesz.

## <a name="before-you-start"></a>Előkészületek

Mielőtt hozzákezd, győződjön meg [](backup-azure-dpm-introduction.md#prerequisites-and-limitations) arról, hogy megfelel a számítási feladatok Azure Backup használatára vonatkozó előfeltételeknek. Néhány előfeltételként szükséges feladat:

* Backup-tároló létrehozása.
* Tároló hitelesítő adatainak letöltése.
* Telepítse a Azure Backup ügynököt.
* Regisztrálja a kiszolgálót a tárolóban.

## <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

Az Azure-SQL Server védelméhez először hozzon létre egy biztonsági mentési szabályzatot:

1. A Data Protection Manager (DPM) kiszolgálón válassza a **Védelem munkaterületet.**
1. Védelmi **csoport létrehozásához** válassza az Új lehetőséget.

    ![Védelmi csoport létrehozása](./media/backup-azure-backup-sql/protection-group.png)
1. A kezdőlapon tekintse át a védelmi csoport létrehozásával kapcsolatos útmutatót. Ezután kattintson a **Tovább** gombra.
1. Válassza a **Kiszolgálók lehetőséget.**

    ![Válassza ki a Kiszolgálók védelmi csoport típusát](./media/backup-azure-backup-sql/pg-servers.png)
1. Bontsa SQL Server virtuális gépet, ahol azok az adatbázisok találhatók, amelyekről biztonsági adatbázisokat szeretne biztonsági mentése. Láthatja az adatforrásokat, amelyekről biztonságimenthet a kiszolgálóról. **Bontsa ki a Minden SQL-megosztás** bontsa ki, majd válassza ki azokat az adatbázisokat, amelyekről biztonsági mentéset szeretne. Ebben a példában a ReportServer$MSDPM2012 és a ReportServer$MSDPM2012TempDB lehetőséget választjuk. Ezután kattintson a **Tovább** gombra.

    ![Válasszon ki egy SQL Server-adatbázist](./media/backup-azure-backup-sql/pg-databases.png)
1. Nevezze el a védelmi csoportot, majd válassza az **Online védelmet szeretnék lehetőséget.**

    ![Adatvédelmi módszer kiválasztása – rövid távú lemezvédelem vagy online Azure-védelem](./media/backup-azure-backup-sql/pg-name.png)
1. A **Cél Short-Term lapon** adja meg a szükséges bemeneteket a lemezre való biztonsági mentési pontok létrehozásához.

    Ebben a példában **a Megőrzési időtartam** *5 napra van állítva.* A biztonsági mentés **szinkronizálásának** gyakorisága *15 percenként.* **Az Expressz teljes biztonsági** mentés *20:00-kor van beállítva.*

    ![A biztonsági mentések védelmének rövid távú céljainak beállítása](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Ebben a példában minden nap 20:00-kor létrejön egy biztonsági mentési pont. A rendszer az előző nap 20:00 óta módosított adatokat továbbítja. Ennek a folyamatnak **Express Full Backup (Expressz teljes biztonsági mentés) a neve.** Bár a tranzakciónaplók szinkronizálása 15 percenként történik, ha az adatbázist 9:00-kor kell helyreállítanunk, akkor a pont az utolsó expressz teljes biztonsági mentési pont naplóinak visszajátszásával jön létre, amely ebben a példában 18:00-kor kezdődik.
   >
   >

1. Kattintson a **Tovább** gombra. A DPM megjeleníti a rendelkezésre álló teljes tárterületet. Emellett a lehetséges lemezterület-kihasználtságot is megjeleníti.

    ![Lemezfoglalás beállítása](./media/backup-azure-backup-sql/pg-storage.png)

    Alapértelmezés szerint a DPM adatforrásonként egy kötetet hoz létre (SQL Server adatbázisonként). A rendszer a kötetet használja a kezdeti biztonsági másolat készítéséhez. Ebben a konfigurációban a Logikai lemezkezelő (LDM) 300 adatforrásra korlátozza a DPM-védelmet (SQL Server adatbázisokra). Ennek a korlátozásnak a megoldáshoz válassza az Adatok közös keresése a **DPM-tárolókészletben lehetőséget.** Ha ezt a beállítást használja, a DPM egyetlen kötetet használ több adatforráshoz. Ez a beállítás lehetővé teszi, hogy a DPM akár 2000 adatbázist SQL Server számára.

    Ha a **Kötetek automatikus növelése** lehetőséget választja, a DPM képes figyelembe venni a megnövekedett biztonsági mentési kötetet az éles adatok növekedésével. Ha nem választja ki a **Kötetek** automatikus növelése lehetőséget, akkor a DPM a biztonsági mentési tárterületet a védelmi csoport adatforrásaira korlátozza.

1. Ha Ön rendszergazda, átadhatja ezt a kezdeti  biztonsági mentést automatikusan a hálózaton keresztül, és kiválaszthatja az átvitel idejét. Vagy manuálisan is **átadhatja** a biztonsági mentést. Ezután kattintson a **Tovább** gombra.

    ![Replika-létrehozási módszer kiválasztása](./media/backup-azure-backup-sql/pg-manual.png)

    A biztonsági másolat kezdeti másolásához a teljes adatforrást át kell SQL Server adatbázisba. A biztonsági mentési adatok az éles kiszolgálóról (SQL Server) a DPM-kiszolgálóra kerülnek. Ha ez a biztonsági másolat nagy, akkor az adatok hálózaton keresztüli átvitele sávszélesség-torlódást okozhat. Emiatt a rendszergazdák cserélhető adathordozót használhatnak a kezdeti biztonsági mentés manuális **átviteléhez.** Vagy át tudják adni az adatokat **automatikusan a hálózaton keresztül** egy adott időpontban.

    A kezdeti biztonsági mentés befejezése után a biztonsági mentések növekményesen folytatódnak a kezdeti biztonsági mentés másolatán. A növekményes biztonsági mentések általában kicsik, és könnyen átvihetők a hálózaton.

1. Válassza ki, hogy mikor kell konzisztencia-ellenőrzést futtatni. Ezután kattintson a **Tovább** gombra.

    ![Annak kiválasztása, hogy mikor kell konzisztencia-ellenőrzést futtatni](./media/backup-azure-backup-sql/pg-consistent.png)

    A DPM konzisztencia-ellenőrzést tud futtatni a biztonsági mentési pont integritásán. Kiszámítja az éles kiszolgálón (ebben a példában a SQL Server számítógépen) található biztonsági mentési fájl ellenőrzőumát, valamint a fájl biztonsági másolatának adatait a DPM-be. Ha az ellenőrzés ütközést talál, a rendszer feltételezi, hogy a DPM biztonsági mentése sérült. A DPM úgy javítja ki a biztonságimentett adatokat, hogy elküldi az ellenőrző összeg eltérésének megfelelő blokkokat. Mivel a konzisztencia-ellenőrzés teljesítményigényes művelet, a rendszergazdák dönthetnek úgy, hogy ütemezik vagy automatikusan futtatják a konzisztencia-ellenőrzést.

1. Válassza ki az Azure-ban védeni kívánt adatforrásokat. Ezután kattintson a **Tovább** gombra.

    ![Az Azure-ban védeni kívánt adatforrások kiválasztása](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Ha Ön rendszergazda, kiválaszthatja a szervezet szabályzatának megfelelő biztonsági mentési ütemezéseket és adatmegőrzési szabályzatokat.

    ![Ütemezések és adatmegőrzési szabályzatok kiválasztása](./media/backup-azure-backup-sql/pg-schedule.png)

    Ebben a példában a biztonsági mentés naponta 12:00-kor és 21:00-kor történik.

    > [!TIP]
    > A gyors helyreállítás érdekében tartson néhány rövid távú helyreállítási pontot a lemezen. Ezek a helyreállítási pontok a működés helyreállítására használatosak. Az Azure jó helyszíni hely, amely magasabb szintű rendelkezésre állást és garantált rendelkezésre állást biztosít.
    >
    > A DPM használatával ütemezze az Azure Backupot a helyi lemez biztonsági mentésének befejezése után. Ha követi ezt a gyakorlatot, a rendszer átmásolja a lemez legújabb biztonsági másolatát az Azure-ba.
    >

1. Válassza ki a megőrzési szabályzat ütemezését. További információ a megőrzési szabályzatról: Use Azure Backup to replace your tape infrastructure (A szalagos infrastruktúra cseréje a [szalagos infrastruktúra helyett).](backup-azure-backup-cloud-as-tape.md)

    ![Adatmegőrzési szabályzat kiválasztása](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Ebben a példában:

    * A biztonsági mentés naponta 12:00-kor és 21:00-kor történik. 180 napig vannak megőrizve.
    * A biztonsági másolat szombat 12:00-kor 104 hétig lesz megőrizve.
    * A hónap utolsó szombatján 12:00-kor a biztonsági másolat 60 hónapig lesz megőrizve.
    * A március utolsó szombatján 12:00-kor a biztonsági másolat 10 évig lesz megőrizve.

    A megőrzési szabályzat kiválasztása után válassza a Tovább **lehetőséget.**

1. Válassza ki, hogyan továbbítja a kezdeti biztonsági másolati másolatot az Azure-ba.

    * Az **Automatikusan a hálózaton keresztül** beállítás a biztonsági mentési ütemezés szerint továbbítja az adatokat az Azure-ba.
    * További információ az offline biztonsági **mentésről:** [Az offline biztonsági mentés áttekintése.](offline-backup-overview.md)

    Az átviteli mechanizmus kiválasztása után válassza a **Tovább lehetőséget.**

1. Az Összefoglalás **lapon** tekintse át a szabályzat részleteit. Ezután válassza **a Csoport létrehozása lehetőséget.** A Bezárás lehetőséget **választva** figyelheti a feladat előrehaladását a **Figyelés munkaterületen.**

    ![A védelmi csoport létrehozásának előrehaladása](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Igény szerinti biztonsági másolatok létrehozása egy SQL Server adatbázisból

Az első biztonsági mentéskor létrejön egy helyreállítási pont. Ahelyett, hogy megvárja az ütemezés futtatását, manuálisan aktiválhatja a helyreállítási pont létrehozását:

1. A védelmi csoportban győződjön meg arról, hogy az adatbázis állapota **OK.**

    ![Egy védelmi csoport, amely az adatbázis állapotát mutatja](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Kattintson a jobb gombbal az adatbázisra, majd válassza a **Helyreállítási pont létrehozása lehetőséget.**

    ![Online helyreállítási pont létrehozása](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. A legördülő menüben válassza az **Online védelem lehetőséget.** Ezután kattintson **az OK** gombra egy helyreállítási pont létrehozásának az Azure-ban való létrehozásához.

    ![Helyreállítási pont létrehozásának elkezdése az Azure-ban](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. A feladat előrehaladását a Figyelés **munkaterületen tudja** megtekinteni.

    ![A feladat előrehaladásának megtekintése a Figyelési konzolon](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Adatbázis SQL Server azure-ból

Védett entitás, például egy SQL Server helyreállítása az Azure-ból:

1. Nyissa meg a DPM kiszolgálókezelési konzolját. A DPM által **biztonsági** mentésekor a Helyreállítás munkaterületen tekintse meg a kiszolgálókat. Válassza ki az adatbázist (ebben a példában ReportServer$MSDPM2012). Válasszon ki **egy Online végződés** utáni helyreállítási **időt.**

    ![Helyreállítási pont kiválasztása](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Kattintson a jobb gombbal az adatbázis nevére, és válassza a **Helyreállítás lehetőséget.**

    ![Adatbázis helyreállítása az Azure-ból](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. A DPM megjeleníti a helyreállítási pont részleteit. Kattintson a **Tovább** gombra. Az adatbázis felülírása érdekében válassza a Helyreállítás az adatbázis eredeti példányára **SQL Server.** Ezután kattintson a **Tovább** gombra.

    ![Adatbázis helyreállítása az eredeti helyükre](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Ebben a példában a DPM lehetővé teszi az adatbázis helyreállítását egy másik SQL Server egy önálló hálózati mappába.
1. A Helyreállítási **beállítások megadása lapon** kiválaszthatja a helyreállítási beállításokat. Kiválaszthatja például a  Hálózati sávszélesség használatának szabályozása lehetőséget a helyreállítás által használt sávszélesség szabályozásához. Ezután kattintson a **Tovább** gombra.
1. Az Összefoglalás **lapon** láthatja az aktuális helyreállítási konfigurációt. Válassza a **Helyreállítás lehetőséget.**

    A helyreállítás állapota a helyreállított adatbázist mutatja. A **Bezárás** gombra választva bezárhatja a varázslót, és megtekintheti a folyamat előrehaladását a **Figyelés munkaterületen.**

    ![A helyreállítási folyamat elkezdése](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    A helyreállítás befejezésekor a visszaállított adatbázis konzisztens lesz az alkalmazással.

## <a name="next-steps"></a>Következő lépések

További információ: gyakori [Azure Backup.](backup-azure-backup-faq.yml)