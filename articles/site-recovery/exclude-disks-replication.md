---
title: Lemezek kizárása a replikációból a Azure Site Recovery
description: Lemezek kizárása a replikációból az Azure-ba Azure Site Recovery használatával.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 15989fbfd65f758eb777c5170c217aba8707e0be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008259"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Lemezek kizárása a vész-helyreállításból

Ez a cikk azt ismerteti, hogyan zárhatók ki lemezek a replikáció során a helyszínről az Azure-ba a [Azure site Recovery](site-recovery-overview.md)használatával. Több okból is kizárhat lemezeket a replikációból:

- Annak érdekében, hogy a kizárt lemezen lévő lényegtelen adatforgalom ne legyen replikálva.
- A felhasznált replikációs sávszélesség és a célként megadott erőforrások optimalizálása érdekében.
- A tárolási és hálózati erőforrások mentéséhez a nem szükséges adatreplikálás nélkül.
- Az Azure-beli virtuális gépek elértek Site Recovery replikációs korlátokat.


## <a name="supported-scenarios"></a>Támogatott esetek

A táblázatból összefoglalt lemezek kizárhatók a replikációból.

**Azure – Azure** | **VMware – Azure** | **Hyper-V – Azure** | **Fizikai kiszolgálóról az Azure-ba**
--- | --- | --- | ---
Igen | Igen | Igen | Yes

## <a name="exclude-limitations"></a>Korlátozások kizárása

**Korlátozás** | **Azure-beli virtuális gépek** | **VMware virtuális gépek** | **Hyper-V virtuális gépek**
--- | --- | ---
**Lemez típusa** | A replikációból kizárhatja az alaplemezeket.<br/><br/> Az operációsrendszer-lemezek és a dinamikus lemezek nem zárhatók ki. Az ideiglenes lemezek alapértelmezés szerint ki vannak zárva. | A replikációból kizárhatja az alaplemezeket.<br/><br/> Az operációsrendszer-lemezek és a dinamikus lemezek nem zárhatók ki. | A replikációból kizárhatja az alaplemezeket.<br/><br/> Nem zárhatja ki az operációsrendszer-lemezeket. Azt javasoljuk, hogy ne zárja ki a dinamikus lemezeket. Site Recovery nem tudja azonosítani, hogy melyik VHS alapszintű vagy dinamikus a vendég virtuális gépen. Ha nem zárja ki az összes függő dinamikus kötet lemezét, a védett dinamikus lemez hibás lemezvé válik a feladatátvételi virtuális gépen, és a lemezen lévő adatai nem érhetők el.
**Lemez replikálása** | A replikáló lemezek nem zárhatók ki.<br/><br/> Tiltsa le és engedélyezze újra a virtuális gép replikálását. |  A replikáló lemezek nem zárhatók ki. |  A replikáló lemezek nem zárhatók ki.
**Mobilitási szolgáltatás (VMware)** | Nem releváns | A lemezeket csak olyan virtuális gépeken lehet kizárni, amelyeken telepítve van a mobilitási szolgáltatás.<br/><br/> Ez azt jelenti, hogy manuálisan kell telepítenie a mobilitási szolgáltatást azokon a virtuális gépeken, amelyeken ki szeretné zárni a lemezeket. A leküldéses telepítési mechanizmus nem használható, mert csak a replikáció engedélyezése után telepíti a mobilitási szolgáltatást. | Nem releváns.
**Hozzáadás/Eltávolítás** | A felügyelt lemezekkel rendelkező, replikálást támogató Azure-beli virtuális gépeken is hozzáadhat felügyelt lemezeket. A replikálásra képes Azure-beli virtuális gépeken nem távolíthat el lemezeket. | A replikáció engedélyezése után nem adhat hozzá és nem távolíthat el lemezeket. Lemez hozzáadásához tiltsa le, majd engedélyezze újra a replikációt. | A replikáció engedélyezése után nem adhat hozzá és nem távolíthat el lemezeket. Tiltsa le, majd engedélyezze újra a replikációt.
**Feladatátvétel** | Ha egy alkalmazásnak szüksége van egy kizárt lemezre, a feladatátvételt követően manuálisan kell létrehoznia a lemezt, hogy a replikált alkalmazás futtatható legyen.<br/><br/> Azt is megteheti, hogy a virtuális gép feladatátvétele során létrehozza a lemezt az Azure Automation helyreállítási tervbe való integrálásával. | Ha kizár egy olyan lemezt, amelyet az alkalmazásnak szüksége van, manuálisan kell létrehoznia az Azure-ban a feladatátvétel után. | Ha kizár egy olyan lemezt, amelyet az alkalmazásnak szüksége van, manuálisan kell létrehoznia az Azure-ban a feladatátvétel után.
**Helyszíni feladat-visszavétel – manuálisan létrehozott lemezek** | Nem releváns | **Windows rendszerű virtuális gépek**: az Azure-ban manuálisan létrehozott lemezek nem állnak vissza. Ha például három lemez feladatátvételét hajtja végre, és közvetlenül egy Azure-beli virtuális gépen hoz létre két lemezt, akkor a rendszer csak a feladatátvétel alatt álló három lemezt adja vissza.<br/><br/> **Linuxos virtuális gépek**: az Azure-ban manuálisan létrehozott lemezek visszahívása sikertelen. Ha például egy három lemez feladatátvételét hajtja végre, és két lemezt hoz létre egy Azure-beli virtuális gépen, akkor mind az öt visszakerül a művelet. A manuálisan létrehozott lemezek nem zárhatók ki a feladat-visszavételből. | Az Azure-ban manuálisan létrehozott lemezek nem állnak vissza. Ha például három lemez feladatátvételét hajtja végre, és közvetlenül egy Azure-beli virtuális gépen hoz létre két lemezt, akkor a feladatátvételt csak három lemez hajtja végre.
**Helyszíni feladat-visszavétel – kizárt lemezek** | Nem releváns | Ha az eredeti gépre visszalép, a feladat-visszavételi virtuálisgép-lemez konfigurációja nem tartalmazza a kizárt lemezeket. A VMware-ből az Azure-ba történő replikációból kizárt lemezek nem érhetők el a feladat-visszavételi virtuális gépen. | Ha a feladat-visszavétel az eredeti Hyper-V helyre esik, a feladat-visszavételi virtuálisgép-lemez konfigurációja változatlan marad, mint az eredeti forrású virtuális gép lemeze. A Hyper-V helyről az Azure-ba való kizárni kívánt lemezek elérhetők a feladat-visszavételi virtuális gépen.



## <a name="typical-scenarios"></a>Tipikus forgatókönyvek

Példák a kizárásra alkalmas adatváltozásokra, például a lapozófájlba való írásra (pagefile.sys), és a Microsoft SQL Server tempdb-fájljába való írásra. A munkaterheléstől és a tárolási alrendszertől függően a lapozás és a tempdb-fájlok jelentős mennyiségű adatváltozást regisztrálhatnak. Az ilyen típusú adatmennyiség az Azure-ba való replikálása erőforrás-igényes.

- Az operációs rendszert és a lapozófájlt is tartalmazó virtuális gépek replikálásának optimalizálásához a következőket teheti:
    1. Ossza fel a virtuális lemezt két virtuális lemezre. Az egyik virtuális lemezen az operációs rendszer, a másikon a lapozófájl található.
    2. Zárja ki a lapozófájl lemezét a replikációból.

- A Microsoft SQL Server tempdb-fájlt és a rendszeradatbázis-fájlt is tartalmazó lemezek replikálásának optimalizálásához a következőket teheti:
    1. Tárolja a rendszeradatbázist és a tempdb fájlt két különböző lemezen.
    2. Zárja ki a tempdb lemezét a replikációból.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>1. példa: SQL Server tempdb adatbázist tartalmazó lemezének kizárása

Nézzük meg, hogyan kezelheti a lemezek kizárását, a feladatátvételt és a feladatátvételt egy olyan forráshoz SQL Server Windows rendszerű virtuális gép-* * SalesDB * * *, amelyhez ki szeretnénk zárni a tempdb. 

### <a name="exclude-disks-from-replication"></a>Lemezek kizárása a replikációból

Ezeket a lemezeket a forrás Windows virtuális gép SalesDB.

**Lemez neve** | **Vendég operációsrendszer-lemez** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Operációs rendszer lemeze.
DB-Disk1| Disk1 | D:\ | Az SQL rendszeradatbázis és a felhasználói Adatbázis1.
DB-Disk2 (a lemez ki lett zárva a védelemből) | Disk2 | E:\ | Ideiglenes fájlok.
DB-Disk3 (a lemez ki lett zárva a védelemből) | Disk3 | F:\ | SQL tempdb-adatbázis.<br/><br/> Mappa elérési útja – F:\MSSQL\Data\. Jegyezze fel a mappa elérési útját a feladatátvétel előtt.
DB-Disk4 | Disk4 |G:\ | 2. felhasználói adatbázis

1. Engedélyezjük a SalesDB virtuális gép replikálását.
2. Kizárjuk a Disk2 és a Disk3 a replikációból, mert a lemezek adatváltozása ideiglenes. 


### <a name="handle-disks-during-failover"></a>Lemezek kezelése a feladatátvétel során

Mivel a lemezek nem replikálódnak, az Azure-ba történő feladatátvétel során ezek a lemezek nem találhatók meg a feladatátvételt követően létrehozott Azure virtuális gépen. Az Azure-beli virtuális gép rendelkezik az ebben a táblázatban összefoglalt lemezekkel.

**Vendég operációsrendszer-lemez** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | ---
Disk0 | C:\ | Operációs rendszer lemeze.
Disk1 | E:\ | Ideiglenes tároló<br/><br/>Az Azure hozzáadja ezt a lemezt. Mivel a Disk2 és a Disk3 ki lettek zárva a replikációból, E: az első meghajtóbetűjel az elérhető listából. Az Azure hozzárendeli az E: betűjelet az ideiglenes tárolókötethez. A replikált lemezek további meghajtóbetűjelei változatlanok maradnak.
Disk2 | D:\ | SQL-rendszeradatbázis és 1. felhasználói adatbázis
Disk3 | G:\ | 2. felhasználói adatbázis

A példánkban, mivel a Disk3, az SQL tempdb lemez ki lett zárva a replikációból, és nem érhető el az Azure-beli virtuális gépen, az SQL-szolgáltatás leállított állapotban van, és szüksége van a F:\MSSQL\Data elérési útra. Ezt az elérési utat többféleképpen is létrehozhatja: 

- Vegyen fel egy új lemezt a feladatátvétel után, és rendelje hozzá a tempdb mappa elérési útját.
- Meglévő ideiglenes tárolólemez használatával a tempdb mappa elérési útjaként.

#### <a name="add-a-new-disk-after-failover"></a>Új lemez hozzáadása a feladatátvétel után

1. A feladatátvétel előtt írja le az SQL tempdb.mdf és tempdb.ldf fájljainak elérési útját.
2. A Azure Portal adjon hozzá egy új lemezt a feladatátvételi Azure-beli virtuális géphez. A lemeznek azonos méretűnek (vagy nagyobbnak) kell lennie, mint a forrásként szolgáló SQL tempdb-lemeznek (Disk3).
3. Jelentkezzen be az Azure-beli virtuális gépre.
4. A lemezkezelési (diskmgmt.msc) konzolból inicializálja és formázza az újonnan hozzáadott lemezt.
5. Ugyanazt a meghajtóbetűjelet rendeli hozzá, amelyet az SQL tempdb-lemez (F:) használt.
6. Hozza létre a tempdb mappát az F: köteten (F:\MSSQL\Data).
7. Indítsa el az SQL szolgáltatást a szolgáltatáskonzolról.

#### <a name="use-an-existing-temporary-storage-disk"></a>Meglévő ideiglenes Storage-lemez használata 

1. Nyisson meg egy parancssort.
2. Futtassa az SQL Servert helyreállítási módban a parancssorból.

    ```console
    Net start MSSQLSERVER /f / T3608
    ```

3. Futtassa a következő sqlcmd parancsot a tempdb elérési útjának az új elérési útra történő módosításához.

    ```sql
    sqlcmd -A -S SalesDB        **Use your SQL DBname**
    USE master;     
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
    GO
    ```

4. Állítsa le a Microsoft SQL Server szolgáltatást.

    ```console
    Net stop MSSQLSERVER
    ```

5. Indítsa el a Microsoft SQL Server szolgáltatást.

    ```console
    Net start MSSQLSERVER
    ```

### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware virtuális gépek: lemezek a feladat-visszavétel során az eredeti helyre

Most nézzük meg, hogyan kell kezelni a VMware virtuális gépek lemezeit, ha az eredeti helyszíni helyre kerül vissza.

- **Az Azure-ban létrehozott lemezek**: mivel a példánkban egy Windows rendszerű virtuális gép található, az Azure-ban manuálisan létrehozott lemezek nem replikálódnak vissza a webhelyre, amikor egy virtuális gépet visszaküld vagy újravéd.
- **Ideiglenes tárolóhely az Azure-ban**: az ideiglenes tároló lemez nem replikálódik vissza a helyszíni gazdagépekre.
- **Kizárt lemezek**: a VMware-ből az Azure-ba történő replikációból kizárt lemezek nem érhetők el a helyszíni virtuális gépen a feladat-visszavétel után.

Mielőtt visszaadja a VMware virtuális gépeket az eredeti helyre, az Azure-beli virtuális gép lemezének beállításai a következők.

**Vendég operációsrendszer-lemez** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | ---
Disk0 | C:\ | Operációs rendszer lemeze.
Disk1 | E:\ | Ideiglenes tároló.
Disk2 | D:\ | Az SQL rendszeradatbázis és a felhasználói Adatbázis1.
Disk3 | G:\ | Felhasználói Adatbázis2.

A feladat-visszavétel után az eredeti helyen található VMware virtuális gép a táblázatban összefoglalt lemezekkel rendelkezik.

**Vendég operációsrendszer-lemez** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | ---
Disk0 | C:\ | Operációs rendszer lemeze.
Disk1 | D:\ | Az SQL rendszeradatbázis és a felhasználói Adatbázis1.
Disk2 | G:\ | Felhasználói Adatbázis2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hyper-V virtuális gépek: az eredeti helyre történő feladat-visszavétel során futó lemezek

Most lássuk, hogyan kezelheti a lemezeket a Hyper-V virtuális gépeken, ha az eredeti helyszíni helyre visszalép.

- **Az Azure-ban létrehozott lemezek**: az Azure-ban manuálisan létrehozott lemezek nem replikálódnak vissza a webhelyre, amikor a rendszer visszaadja a virtuális gépet, vagy újból megóvja őket.
- **Ideiglenes tárolóhely az Azure-ban**: az ideiglenes tároló lemez nem replikálódik vissza a helyszíni gazdagépekre.
- **Kizárt lemezek**: a feladat-visszavételt követően a virtuális gép lemezének konfigurációja megegyezik az eredeti virtuálisgép-lemez konfigurációjával. A Hyper-V-ből az Azure-ba történő replikációból kizárt lemezek elérhetők a feladat-visszavételi virtuális gépen.

Mielőtt visszaadja a Hyper-V virtuális gépeket az eredeti helyre, az Azure-beli virtuális gép lemezének beállításai a következők.

**Vendég operációsrendszer-lemez** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | ---
Disk0 | C:\ | Operációs rendszer lemeze.
Disk1 | E:\ | Ideiglenes tároló.
Disk2 | D:\ | Az SQL rendszeradatbázis és a felhasználói Adatbázis1.
Disk3 | G:\ | Felhasználói Adatbázis2.

A tervezett feladatátvétel (feladat-visszavétel) az Azure-ból a helyszíni Hyper-V-be való bekapcsolását követően az eredeti helyen lévő Hyper-V virtuális gép a táblázatban összefoglalt lemezekkel rendelkezik.

**Lemez neve** | **Vendég operációs rendszer lemezének száma** | **Meghajtó betűjele** | **Lemez adattípusa**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | Operációs rendszer lemeze.
DB-Disk1 | Disk1 | D:\ | Az SQL rendszeradatbázis és a felhasználói Adatbázis1.
DB-Disk2 (kizárt lemez) | Disk2 | E:\ | Ideiglenes fájlok.
DB-Disk3 (kizárt lemez) | Disk3 | F:\ | SQL tempdb-adatbázis<br/><br/> Mappa elérési útja (F:\MSSQL\Data \) .
DB-Disk4 | Disk4 | G:\ | 2. felhasználói adatbázis


## <a name="example-2-exclude-the-paging-file-disk"></a>2. példa: a lapozófájl lemezének kizárása

Nézzük meg, hogyan kezelheti a lemezek kizárását, a feladatátvételt és a feladatátvételt a forrásként szolgáló Windowsos virtuális gép esetében, amelyhez ki szeretné zárni a pagefile.sys-lemezt a D meghajtón, valamint egy másik meghajtót is.


### <a name="paging-file-on-the-d-drive"></a>Lapozófájl a D meghajtón

Ezek a lemezek a forrás virtuális gépen vannak.

**Lemez neve** | **Vendég operációsrendszer-lemez** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Operációsrendszer-lemez
DB-Disk1 (kizárás a replikálásból) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

A forrás virtuális gép lapozófájl-beállításai a következők:

![Képernyőfelvétel a virtuális memória párbeszédpanelről a D: meghajtó [lapozófájl mennyisége] sorban, amely a lapozófájl mérete (MB) 3000-7000.](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Engedélyezzük a virtuális gép replikálását.
2. DB-Disk1 kizárása a replikációból.

#### <a name="disks-after-failover"></a>Lemezek feladatátvétel után

A feladatátvételt követően az Azure-beli virtuális gép rendelkezik a táblázatban összegzett lemezekkel.

**Lemez neve** | **Vendég operációsrendszer-lemez száma** | **Meghajtó betűjele** | **A lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Operációsrendszer-lemez
DB-Disk1 | Disk1 | D:\ | Ideiglenes tárolás/pagefile.sys <br/><br/> Mivel DB-Disk1 (D:) ki lett zárva, a D: az első meghajtóbetűjel az elérhető listából.<br/><br/> Az Azure a D: betűjelet rendeli hozzá az ideiglenes tárolókötethez.<br/><br/> Mivel a D: elérhető, a virtuális gép lapozófájljának beállítása változatlan marad.
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

Az Azure-beli virtuális gépen elérhető lapozófájl-beállítások a következők:

![Lapozófájl-beállítások az Azure virtuális gépen](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Lapozófájl egy másik meghajtón (nem D:)

Nézzük meg például, hogy a lapozófájl nem a D meghajtón található.  

Ezek a lemezek a forrás virtuális gépen vannak.

**Lemez neve** | **Vendég operációsrendszer-lemez** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Operációsrendszer-lemez
DB-Disk1 (kizárás a replikálásból) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

A helyszíni virtuális gépen a lapozófájl beállításai a következők:

![Lapozófájl-beállítások a helyszíni virtuális gépen](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Engedélyezzük a virtuális gép replikálását.
2. DB-Disk1 kizárása a replikációból.

#### <a name="disks-after-failover"></a>Lemezek feladatátvétel után

A feladatátvételt követően az Azure-beli virtuális gép rendelkezik a táblázatban összegzett lemezekkel.

**Lemez neve** | **Vendég operációs rendszer lemezének száma** | **Meghajtó betűjele** | **Lemez adattípusa**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | Operációsrendszer-lemez
DB-Disk1 | Disk1 | D:\ | Ideiglenes tároló<br/><br/> Mivel a D: az első elérhető meghajtóbetűjel a listában, az Azure ezt a betűjelet rendeli hozzá az ideiglenes tárolókötethez.<br/><br/> A meghajtó betűjele minden replikált lemez esetében ugyanaz marad.<br/><br/> Mivel a G: lemez nem érhető el, a rendszer a C: meghajtót fogja használni a lapozófájlhoz.
DB-Disk2 | Disk2 | E:\ | Felhasználói adatok, 1
DB-Disk3 | Disk3 | F:\ | Felhasználói adatok, 2

Az Azure-beli virtuális gépen elérhető lapozófájl-beállítások a következők:

![Képernyőfelvétel a virtuális memória párbeszédpanelről a C: meghajtó vonallal, amely a "rendszer által felügyelt" lapozófájl-méret beállítását mutatja.](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Következő lépések

- További információ az ideiglenes Storage-lemezre vonatkozó irányelvekről:
    - [További](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) tudnivalók az SSD-k Azure-beli virtuális gépeken való használatáról SQL Server tempdb és puffer-bővítmények tárolásához
    - [Tekintse át ](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) az Azure-beli virtuális gépek SQL Serverinak teljesítményére vonatkozó ajánlott eljárásokat.
- Ha sikerült beállítania és elindítani az üzemelő példányt, [ismerkedjen meg részletesebben](failover-failback-overview.md) a feladatátvételi különféle típusaival.
