---
title: Azure VMware Solution – migrálás Azure Data Box
description: A Azure Data Box adatok tömeges áttelepítése Azure VMware Solution.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab772bd9cb415045ef70cb4cf9a518791befb192
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741750"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Adatok áttelepítése Azure VMware Solution a Azure Data Box

A Microsoft Azure Data Box megoldással gyorsan, olcsón és megbízhatóan küldhet több terabájtnyi adatot az Azure-ba. A biztonságos adatátvitelt egy saját fejlesztésű Data Box tárolóeszköz küldésével gyorsítjuk fel. Minden tárolóeszköz maximális használható tárkapacitása 80 TB, és egy regionális szolgáltató szállítja az Adatközpontba. Az eszköz egyenetlen tokokkal rendelkezik az adatok védelme és védelme érdekében az átvitel során.

A virtuális Data Box VMware-adatokat tömegesen is miigálhatja a magánfelhőbe. A helyszíni hálózati VMware vSphere adatokat a Data Box NFS protokollon keresztül másolja a rendszer. Az adatok tömeges migrálásának része a virtuális gépek, konfigurációk és a társított adatok időponthoz tartozó másolatának mentése Data Box manuálisan az Azure-ba való szállítással.

Ebben a cikkben a következővel fog ismerkedni:

* A Data Box.
* Adatok másolása a helyszíni VMware-környezetből a Data Box NFS-n keresztül.
* Felkészülés a Data Box.
* Blobadatok előkészítése a Azure VMware Solution.
* Adatok másolása az Azure-ból a magánfelhőbe.

## <a name="scenarios"></a>Forgatókönyvek

Használja Data Box a következő forgatókönyvekben a tömeges adatáttelepítéshez:

* Nagy mennyiségű adat helyszíni környezetből Azure VMware Solution. Ez a módszer létrehoz egy alapkonfigurációt, és szinkronizálja a hálózaton lévő különbségeket.
* Nagy számú kikapcsolt virtuális gép (hidegen lévő virtuális gépek) áttelepítése.
* Virtuális gépek adatainak áttelepítése fejlesztési és tesztelési környezetek beállítására.
* Nagy számú virtuálisgép-sablon, ISO-fájl és virtuálisgép-lemez áttelepítése.

## <a name="before-you-begin"></a>Előkészületek

* Ellenőrizze az előfeltételeket és [Data Box](../databox/data-box-deploy-ordered.md) a Azure Portal. A rendelési folyamat során ki kell választania egy tárfiókot, amely engedélyezi a Blob Storage-et. Miután megkapja a Data Box eszközt, csatlakoztassa a helyszíni hálózathoz, [](../databox/data-box-deploy-set-up.md) és állítsa be az eszközt a vSphere felügyeleti hálózatról elérhető IP-címmel.

* Hozzon létre egy virtuális hálózatot és egy tárfiókot ugyanabban a régióban, ahol a Azure VMware Solution van kiépítve.

* Hozzon létre [egy Azure-beli](cloudsimple-azure-network-connection.md) virtuális hálózati kapcsolatot a magánfelhő és a tárfiókot létrehozó virtuális hálózat között az Azure-beli virtuális hálózat csatlakoztatása a CloudSimple-hez az [ExpressRoute használatával](virtual-network-connection.md)lépéseit követve.

## <a name="set-up-data-box-for-nfs"></a>NfS-Data Box beállítása

Csatlakozzon Data Box helyi webes felhasználói felülethez az Oktatóanyag: Kábel és csatlakozás az eszközhöz című szakaszának "Csatlakozás az eszközhöz" [Azure Data Box.](../databox/data-box-deploy-set-up.md)  Konfigurálja Data Box nfs-ügyfelek hozzáférésének engedélyezése érdekében:

1. A helyi webes felhasználói felületen lépjen a Csatlakozás **és másolás oldalra.** Az **NFS-beállítások alatt válassza** az **NFS-ügyfél hozzáférése lehetőséget.** 

    ![NFS-ügyfélhozzáférés konfigurálása 1](media/nfs-client-access.png)

2. Adja meg a gazdagépek IP VMware ESXi, majd válassza a **Hozzáadás lehetőséget.** Ezt a lépést megismételve konfigurálhatja a vSphere-fürt összes gazdagépének hozzáférését. Válassza az **OK** lehetőséget.

    ![NFS-ügyfélhozzáférés konfigurálása 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. A blokkblob- és lapblobmegosztások alatt létrehozott mappa azt a tárolót jelöli, amelybe a rendszer feltölti az adatokat blobokként. Nem másolhat fájlokat közvetlenül  a tárfiók gyökérmappába.

A blokkblob- és lapblobmegosztások alatti első szintű entitások tárolók, a második szintű entitások pedig blobok. A megosztások Azure Files az első szintű entitások a megosztások, a második szintű entitások pedig fájlok.

Az alábbi táblázat a Data Boxon található megosztások UNC elérési útját és az adatok feltöltéséhez használt Azure Storage elérési útjának URL-címét mutatja. Az Azure Storage elérési útjának végső URL-címe a megosztás UNC elérési útjából származik.
 
| Blobok és fájlok | Elérési út és URL-cím |
|---------------- | ------------ |
| Azure-blokkblobok | <li>A megosztások UNC elérési útja: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-lapblobok  | <li>A megosztások UNC elérési útja: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>A megosztások UNC elérési útja: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> VMware-adatok másolása Azure-blokkblobokkal.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Csatlakoztassa az NFS-megosztást adattárként a helyszíni vCenter-fürtön, és másolja az adatokat

Ahhoz, hogy az adatokat az NFS-adattárba másolni VMware ESXi helyszíni vCenter-fürtön vagy VMware ESXi-gazdagépen, csatlakoztatni kell az nfs-megosztást a Data Box-kiszolgálóról:

1. Jelentkezzen be a helyszíni vCenter-kiszolgálóra.

2. Kattintson a jobb gombbal az **Adatközpont elemre,** válassza a **Tárolás lehetőséget,** válassza az **Új adattár** lehetőséget, majd válassza a Tovább **lehetőséget.**

   ![Új adattár hozzáadása](media/databox-migration-add-datastore.png)

3. Az Adattár hozzáadása varázsló 1. lépésében válassza az **NFS** lehetőséget a **Típus alatt.**

   ![Új adattár hozzáadása – típus](media/databox-migration-add-datastore-type.png)

4. A varázsló 2. lépésében válassza az **NFS 3** lehetőséget NFS-verzióként, majd válassza a Tovább **lehetőséget.**

   ![Új adattár hozzáadása – NFS-verzió](media/databox-migration-add-datastore-nfs-version.png)

5. A varázsló 3. lépésében adja meg az adattár nevét, az elérési utat és a kiszolgálót. Használhatja a kiszolgálóhoz Data Box IP-címét. A mappa elérési útja a következő formátumban `/<StorageAccountName_BlockBlob>/<ContainerName>/` lesz: .

   ![Új adattár hozzáadása – NFS-konfiguráció](media/databox-migration-add-datastore-nfs-configuration.png)

6. A varázsló 4. lépésében válassza ki azokat az ESXi-gazdagépeket, amelyekhez csatlakoztatni szeretné az adattárat, majd válassza a Tovább **lehetőséget.**  A fürtökben válassza ki az összes gazdagépet a virtuális gépek áttelepítésének biztosításához.

   ![Új adattár hozzáadása – Gazdagépek kiválasztása](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. A varázsló 5. lépésében tekintse át az összefoglalást, és válassza a **Befejezés lehetőséget.**

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Adatok másolása a Data Box NFS-adattárba

A virtuális gépek migrálhatók vagy klónozhatók az új adattárba.  Az áttelepíteni kívánt nem használt virtuális gépek a Storage **vMotion** lehetőség használatával migrálhatók Data Box NFS-adattárba. Az aktív virtuális gépek klónozhatók a Data Box NFS-adattárba.

* Azonosítsa és listába sorolja az áthelyezni lehet virtuális **gépeket.**
* Azonosítsa és listába sorolja a klónozni szükséges **virtuális gépeket.**

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Virtuális gép áthelyezése egy Data Box adattárba

1. Kattintson a jobb gombbal arra a virtuális gépre, amelyről át szeretne lépni a Data Box adattárba, majd válassza az **Áttelepítés lehetőséget.**

    ![Virtuális gép áttelepítése](media/databox-migration-vm-migrate.png)

2. Válassza **a Csak a tárterület** módosítása lehetőséget az áttelepítési típushoz, majd kattintson a Tovább **gombra.**

    ![Virtuális gép áttelepítése – csak tárterület](media/databox-migration-vm-migrate-change-storage.png)

3. Célként válassza a **Databox-Datastore** lehetőséget, majd kattintson a **Tovább gombra.**

    ![Virtuális gép áttelepítése – adattár kiválasztása](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Tekintse át az adatokat, és válassza a **Befejezés lehetőséget.**

5. További virtuális gépek esetén ismételje meg az 1–4. lépést.

> [!TIP]
> Több olyan virtuális gépet is kiválaszthat, amelyek azonos energiaállapotban vannak (be- vagy kikapcsolva), és tömegesen is át lehet őket mirateálni.

A virtuális gép migrálva lesz az NFS-adattárba a Data Box. Az összes virtuális gép áttelepítése után kikapcsolhatja (leállíthatja) az aktív virtuális gépeket az adatok áttelepítésének előkészítéséhez a Azure VMware Solution.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Virtuális gép vagy virtuálisgép-sablon klónozása az Data Box adattárba

1. Kattintson a jobb gombbal a klónozni kívánt virtuális gépre vagy virtuálisgép-sablonra. Válassza **a**  >  **Klónozás virtuális gépre lehetőséget.**

    ![Virtuális gép klónozása](media/databox-migration-vm-clone.png)

2. Válasszon egy nevet a klónozott virtuális gépnek vagy a virtuálisgép-sablonnak.

3. Válassza ki azt a mappát, ahová a klónozott objektumot el szeretné tenni, majd válassza a **Tovább lehetőséget.**

4. Válassza ki azt a fürtöt vagy erőforráskészletet, ahová a klónozott objektumot el szeretné tenni, majd válassza a Tovább **lehetőséget.**

5. Válassza **a Databox-Datastore** lehetőséget tárolási helyként, majd kattintson a **Tovább gombra.**

    ![Virtuális gép klónozása – adattár kiválasztása](media/databox-migration-vm-clone-select-datastore.png)

6. Ha testre szeretné szabni a klónozott objektum beállításait, válassza ki a testreszabási beállításokat, majd válassza a Tovább **lehetőséget.**

7. Tekintse át a konfigurációkat, és válassza a **Befejezés lehetőséget.**

8. Ismételje meg az 1–7. lépést további virtuális gépek vagy virtuálisgép-sablonok esetén.

A virtuális gépek klónozása és tárolása az NFS-adattárban fog Data Box. A virtuális gépek klónozása után mindenképpen állítsa le őket az adatok áttelepítésének előkészítéséhez a Azure VMware Solution.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>ISO-fájlok másolása Data Box adattárba

1. A helyszíni vCenter webes felhasználói felületén válassza a **Storage területet.**  Válassza a **Databox-Datastore,** majd a **Fájlok lehetőséget.** Hozzon létre egy új mappát az ISO-fájlok tárolásához.

    ![ISO másolása – új mappa létrehozása](media/databox-migration-create-folder.png)

2. Adja meg annak a mappának a nevét, ahol az ISO-fájlokat tárolni fogja.

3. Kattintson duplán az újonnan létrehozott mappára annak megnyitásához.

4. Válassza **a Fájlok feltöltése** lehetőséget, majd válassza ki a feltölteni kívánt ISO-fájlokat.
    
    ![ISO másolása – fájlok feltöltése](media/databox-migration-upload-iso.png)

> [!TIP]
> Ha már rendelkezik ISO-fájlokkal a helyszíni adattárban,  kiválaszthatja a fájlokat és a Másolás a következőbe lehetőséget, hogy a fájlokat az NFS Data Box tárba másolja.


## <a name="prepare-data-box-for-return"></a>A Data Box előkészítése a visszatéréshez

Miután az összes virtuálisgép-adatot, virtuálisgép-sablonadatokat és ISO-fájlokat átmásolta az Data Box NFS-adattárba, leválaszthatja az adattárat a vCenterről. Az adattár leválasztása előtt az összes virtuális gépet és virtuálisgép-sablont el kell távolítani a leltárból.

### <a name="remove-objects-from-inventory"></a>Objektumok eltávolítása a leltárból

1. A helyszíni vCenter webes felhasználói felületén válassza a **Storage területet.** Válassza a **Databox-Datastore,** majd a **Virtuális gépek lehetőséget.**

    ![Virtuális gépek eltávolítása a leltárból – kikapcsolva](media/databox-migration-select-databox-vm.png)

2. Győződjön meg arról, hogy az összes virtuális gép le van állítani.

3. Jelölje ki az összes virtuális gépet, kattintson a jobb gombbal, majd válassza az **Eltávolítás a leltárból lehetőséget.**

    ![Virtuális gépek eltávolítása a leltárból](media/databox-migration-remove-vm-from-inventory.png)

4. Válassza **a Virtuálisgép-sablonok lehetőséget** a Mappák területen, majd ismételje meg a 3. lépést.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Az NFS Data Box tároló eltávolítása a vCenterből

A Data Box NFS-adattárat le kell bontani a VMware ESXi gazdagépekkel a visszatérésre való felkészülés előtt.

1. A helyszíni vCenter webes felhasználói felületén válassza a **Storage területet.**

2. Kattintson a jobb gombbal a **Databox-Datastore elemre,** és válassza **az Adattár leválasztása lehetőséget.**

    ![Adattár Data Box leválasztása](media/databox-migration-unmount-datastore.png)

3. Jelölje ki az összes ESXi-gazdagépet, ahol az adattár csatlakoztatva van, majd kattintson az **OK gombra.**

    ![Adattár Data Box leválasztása – gazdagépek kiválasztása](media/databox-migration-unmount-datastore-select-hosts.png)

4. Tekintse át és fogadja el a figyelmeztetéseket, majd kattintson az **OK gombra.**

### <a name="prepare-data-box-for-return-and-then-return-it"></a>A Data Box előkészítése a visszatéréshez, majd a visszaküldéshez

Kövesse a Return [Azure Data Box and verify data upload to Azure](../databox/data-box-deploy-picked-up.md) (Adatfeltöltés az Azure-ba) című cikkben leírt lépéseket a Data Box. Ellenőrizze az Azure Storage-fiókba másolt adatok állapotát. Miután az állapot befejezettként jelenik meg, ellenőrizheti az adatokat az Azure-tárfiókban.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Adatok másolása az Azure Storage-ból Azure VMware Solution

A saját eszközére Data Box adatok azután lesznek elérhetők az Azure Storage-fiókjában, hogy a rendelési Data Box befejezettként megjelenik. Az adatok most már átmásolhatók a Azure VMware Solution. A tárfiókban lévő adatokat a magánfelhő vSAN-adattárába kell másolni az NFS protokoll használatával. 

Először másolja át a Blob Storage-adatokat egy Felügyelt lemezre egy Linux rendszerű virtuális gépen az **Azure-ban az AzCopy használatával.** Tegye elérhetővé a felügyelt lemezt NFS-n keresztül, csatlakoztassa az NFS-megosztást adattárként a magánfelhőben, majd másolja az adatokat. Ez a módszer lehetővé teszi az adatok gyorsabb másolását a magánfelhőbe.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Adatok másolása a magánfelhőbe Linux rendszerű virtuális gép és felügyelt lemezek használatával, majd exportálás NFS-megosztásként

1. Hozzon [létre egy Linux](../virtual-machines/linux/quick-create-portal.md) rendszerű virtuális gépet az Azure-ban ugyanabban a régióban, ahol a tárfiók létre lett hozva, és Azure-beli virtuális hálózati kapcsolattal rendelkezik a magánfelhőhöz.

2. Hozzon létre egy felügyelt lemezt, amelynek tárolási kapacitása meghaladja a blobadatok mennyiségét, és csatolja a Linux rendszerű [virtuális géphez.](../virtual-machines/linux/attach-disk-portal.md)  Ha a blobadatok mennyisége nagyobb, mint a legnagyobb elérhető felügyelt lemez kapacitása, az adatokat több lépésben vagy több felügyelt lemez használatával kell átmásolni.

3. Csatlakozzon a Linux rendszerű virtuális géphez, és csatlakoztassa a felügyelt lemezt.

4. Telepítse [az AzCopyt a Linux rendszerű virtuális gépen.](../storage/common/storage-use-azcopy-v10.md)

5. Töltse le az adatokat az Azure Blob Storage-ból a felügyelt lemezre az AzCopy használatával.  Parancsszintaxis: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` .  Cserélje `<storage-account-name>` le az adatokat az Azure Storage-fiók nevére, a helyére pedig a tárfiókon keresztül `<container-name>` másolt Data Box.

6. Telepítse az NFS-kiszolgálót a Linux rendszerű virtuális gépre:

    - Ubuntu/Debian disztribúción: `sudo apt install nfs-kernel-server` .
    - Enterprise Linux-disztribúción: `sudo yum install nfs-utils` .

7. Módosítsa annak a mappának az engedélyét a felügyelt lemezen, amelybe az Azure Blob Storage-ból másolt adatokat.  Módosítsa az NFS-megosztásként exportálni kívánt mappák engedélyét.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Rendeljen engedélyeket az ügyfél IP-címéhez az NFS-megosztás eléréséhez a fájl `/etc/exports` szerkesztésével.

    ```bash
    sudo vi /etc/exports
    ```
    
    Írja be a következő sorokat a fájlba a magánfelhő összes ESXi-gazdagépének IP-címére.  Ha több mappához hoz létre megosztásokat, adja hozzá az összes mappát.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exportálja az NFS-megosztásokat az `sudo exportfs -a` paranccsal.

10. Indítsa újra az NFS-kernelkiszolgálót az `sudo systemctl restart nfs-kernel-server` paranccsal.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Csatlakoztassa a Linux rendszerű virtuális gép NFS-megosztását adattárként egy magánfelhő vCenter-fürtjéhez, majd másolja az adatokat

A Linux rendszerű virtuális gép NFS-megosztását adattárként kell csatlakoztatni a magánfelhő vCenter-fürtjéhez. A csatlakoztatott adatok átmásolhatók az NFS-adattárból a magánfelhő vSAN-adattárába.

1. Jelentkezzen be a magánfelhő vCenter-kiszolgálóra.

2. Kattintson a jobb gombbal **az Adatközpont elemre,** válassza a **Tárolás lehetőséget,** válassza az **Új adattár** lehetőséget, majd válassza a Tovább **lehetőséget.**

   ![Új adattár hozzáadása](media/databox-migration-add-datastore.png)

3. Az Adattár hozzáadása varázsló 1. lépésében válassza az **NFS típusát.**

   ![Új adattár hozzáadása – típus](media/databox-migration-add-datastore-type.png)

4. A varázsló 2. lépésében **nfs-verzióként** válassza az NFS 3 lehetőséget, majd válassza a Tovább **lehetőséget.**

   ![Új adattár hozzáadása – NFS-verzió](media/databox-migration-add-datastore-nfs-version.png)

5. A varázsló 3. lépésében adja meg az adattár nevét, az elérési utat és a kiszolgálót.  A kiszolgálóhoz használhatja a Linux rendszerű virtuális gép IP-címét.  A mappa elérési útja a következő `/<folder>/<subfolder>/` formátumban lesz: .

   ![Új adattár hozzáadása – NFS-konfiguráció](media/databox-migration-add-datastore-nfs-configuration.png)

6. A varázsló 4. lépésében válassza ki azokat az ESXi-gazdagépeket, amelyekhez csatlakoztatni szeretné az adattárat, majd válassza a Tovább **lehetőséget.**  A fürtökben válassza ki az összes gazdagépet a virtuális gépek áttelepítésének biztosításához.

   ![Új adattár hozzáadása – Gazdagépek kiválasztása](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. A varázsló 5. lépésében tekintse át az összegzést, majd válassza a Befejezés **lehetőséget.**

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Virtuális gépek és virtuálisgép-sablonok hozzáadása NFS-adattárból a leltárhoz

1. A magánfelhő vCenter webes felhasználói felületén válassza a **Storage területet.**  Válasszon ki egy Linux rendszerű virtuális gépet NFS-adattárként, majd válassza a **Fájlok lehetőséget.**

    ![Fájlok kiválasztása NFS-adattárból](media/databox-migration-datastore-select-files.png)

2. Válasszon ki egy mappát, amely egy virtuális gépet vagy egy virtuálisgép-sablont tartalmaz.  A részleteket tartalmazó ablaktáblán válasszon ki egy .vmx fájlt egy virtuális géphez, vagy egy .vmtx fájlt egy virtuálisgép-sablonhoz.

3. A **virtuális gép magánfelhőbeli** vCenterben való regisztráláshoz válassza a Virtuális gép regisztrálása lehetőséget.

    ![Virtuális gép regisztrálása](media/databox-migration-datastore-register-vm.png)

4. Válassza ki azt az adatközpontot, mappát és fürtöt/erőforráskészletet, ahol regisztrálni szeretné a virtuális gépet.

4. Ismételje meg a 3. és a 4. lépést az összes virtuális gép és virtuálisgép-sablon esetén.

5. Ugrás az ISO-fájlokat tartalmazó mappára.  Jelölje ki az ISO-fájlokat, majd válassza a **Másolás** a következőbe lehetőséget a fájloknak a vSAN-adattár egyik mappájába való másoláshoz.

A virtuális gépek és a virtuálisgép-sablonok már elérhetők a magánfelhő vCenterében. Ezeket a virtuális gépeket az NFS-adattárból a vSAN-adattárba kell áthelyezni, mielőtt bekapcsolja őket. Használhatja a **storage vMotion** lehetőséget, és kiválaszthatja a vSAN-adattárat a virtuális gépek célhelyeként.

A virtuálisgép-sablonokat klónozni kell a Linux rendszerű virtuális gép NFS-adattárából a vSAN-adattárba.

### <a name="clean-up-your-linux-virtual-machine"></a>A Linux rendszerű virtuális gép tisztítása

Miután az összes adatot átmásolta a magánfelhőbe, eltávolíthatja az NFS-adattárat a magánfelhőből:

1. Győződjön meg arról, hogy az összes virtuális gép és sablon át van helyezték és klónozták a vSAN-adattárba.

2. Távolítson el a leltárból minden virtuálisgép-sablont az NFS-adattárból.

3. Válassza le a Linux rendszerű virtuális gép adattárát a magánfelhő vCenterről.

4. Törölje a virtuális gépet és a felügyelt lemezt az Azure-ból.

5. Ha nem szeretné a tárfiókban tartani az Data Box által továbbított adatokat, törölje az Azure Storage-fiókot.  
    


## <a name="next-steps"></a>Következő lépések

* További információ a [Data Box.](../databox/data-box-overview.md)
* További információ a számítási feladatok magánfelhőbe való [áttelepítésének különböző lehetőségeiről.](migrate-workloads.md)
