---
title: Fájlok és mappák helyreállítása az Azure virtuális gép biztonsági másolatából
description: Ebből a cikkből megtudhatja, hogyan állíthatja helyre a fájlokat és mappákat egy Azure-beli virtuális gép helyreállítási pontjából.
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: references_regions
ms.openlocfilehash: c5a027773a55347b71c1e6a66d24d7fdb99220d0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725509"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Fájlok helyreállítása az Azure-beli virtuális gép biztonsági mentéséből

Azure Backup lehetővé teszi az Azure-beli [virtuális gépek (VM-EK) és lemezek](./backup-azure-arm-restore-vms.md) visszaállítását az Azure VM biztonsági másolatokból, más néven helyreállítási pontokból. Ez a cikk azt ismerteti, hogyan lehet helyreállítani a fájlokat és mappákat egy Azure-beli virtuális gép biztonsági másolatából. A fájlok és mappák visszaállítása csak a Resource Manager-modell használatával üzembe helyezett Azure-beli virtuális gépekhez és a Recovery Services-tárolóhoz való védelemmel érhető el.


> [!NOTE]
> Ez a funkció a Resource Manager-modell használatával üzembe helyezett Azure-beli virtuális gépek számára érhető el, és Recovery Services-tárolóval védett.
> A titkosított virtuális gépekről történő helyreállítás nem támogatott.
>

![Fájl mappa helyreállítási munkafolyamata](./media/backup-azure-restore-files-from-vm/file-recovery-1.png)

## <a name="step-1-generate-and-download-script-to-browse-and-recover-files"></a>1. lépés: parancsfájl létrehozása és letöltése a fájlok tallózásához és helyreállításához

A fájlok vagy mappák helyreállítási pontról történő visszaállításához nyissa meg a virtuális gépet, és hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.Azure.com) , és a bal oldali ablaktáblán válassza a **virtuális gépek** lehetőséget. A virtuális gépek listájából válassza ki a virtuális gépet a virtuális gép irányítópultjának megnyitásához.

2. A virtuális gép menüjében válassza a **biztonsági** mentés lehetőséget a biztonsági mentés irányítópultjának megnyitásához.

    ![Recovery Services tároló biztonsági másolati elemeinek megnyitása](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. A biztonsági mentés irányítópult menüjében válassza a **fájl-helyreállítás** lehetőséget.

    ![Fájl helyreállításának kiválasztása](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Megnyílik a **Fájl-helyreállítási** menü.

    ![Fájl-helyreállítási menü](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. A **helyreállítási pont kiválasztása** legördülő menüben válassza ki a kívánt fájlokat birtokló helyreállítási pontot. Alapértelmezés szerint a legújabb helyreállítási pont már ki van választva.

5. Válassza a **végrehajtható fájl letöltése** (Windows Azure-beli virtuális gépek esetén) vagy a **parancsfájl letöltése** (Linux Azure-beli virtuális gépek esetén a Python-szkript létrehozása) lehetőséget a fájlok helyreállítási pontról való másolásához használt szoftver letöltéséhez.

    ![Végrehajtható fájl letöltése](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Az Azure letölti a végrehajtható fájlt vagy a parancsfájlt a helyi számítógépre.

    ![üzenet letöltése a végrehajtható fájlhoz vagy parancsfájlhoz](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Ha a végrehajtható fájlt vagy a parancsfájlt rendszergazdaként szeretné futtatni, akkor azt javasoljuk, hogy mentse a letöltött fájlt a számítógépre.

6. A végrehajtható fájl vagy a szkript jelszavas védelemmel van ellátva, és jelszót igényel. A **Fájl-helyreállítási** menüben a Másolás gombra kattintva töltse be a jelszót a memóriába.

    ![Generált jelszó](./media/backup-azure-restore-files-from-vm/generated-pswd.png)


## <a name="step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script"></a>2. lépés: Győződjön meg arról, hogy a gép megfelel a követelményeknek, mielőtt végrehajtja a parancsfájlt.

A parancsfájl sikeres letöltése után ellenőrizze, hogy rendelkezik-e a megfelelő géppel a parancsfájl végrehajtásához. A virtuális gép, amelyen a parancsfájlt szeretné végrehajtani, nem rendelkezhet a következő nem támogatott konfigurációk egyikével sem. Ha igen, válasszon egy másik gépet, lehetőleg ugyanabból a régióból, amely megfelel a követelményeknek.  

### <a name="dynamic-disks"></a>Dinamikus lemezek

A végrehajtható parancsfájl nem futtatható a virtuális gépen a következő jellemzők bármelyikével:

- Több lemezre kiterjedő kötetek (átfedő és csíkozott kötetek).
- Hibatűrő kötetek (tükrözött és RAID-5 kötetek) dinamikus lemezeken.

### <a name="windows-storage-spaces"></a>Windows-tárhelyek

A letöltött végrehajtható fájlt nem futtathatja a Windows-tárolóhelyekhez konfigurált virtuális gépen.

### <a name="virtual-machine-backups-having-large-disks"></a>Nagyméretű lemezekkel rendelkező virtuális gépek biztonsági mentései

Ha a biztonsági másolattal rendelkező gépen nagy számú lemez (>16) vagy nagyméretű lemez található (> 4 TB), nem ajánlott a szkriptet ugyanarra a gépre végrehajtani a visszaállításhoz, mert jelentős hatással lesz a virtuális gépre. Ehelyett ajánlott külön virtuális gépet használni a fájl-helyreállításhoz (Azure VM D2v3 virtuális gépek), majd leállítani, ha nem szükséges. 

Lásd: a fájlok biztonsági mentésének követelményei a nagyméretű lemezzel rendelkező virtuális gépekről:<br>
[Windows operációs rendszer](#for-backed-up-vms-with-large-disks-windows)<br>
[Linux operációs rendszer](#for-backed-up-vms-with-large-disks-linux)


## <a name="step-3-os-requirements-to-successfully-run-the-script"></a>3. lépés: az operációs rendszerre vonatkozó követelmények a parancsfájl sikeres futtatásához

A virtuális gép, amelyen futtatni szeretné a letöltött parancsfájlt, meg kell felelnie az alábbi követelményeknek.

### <a name="for-windows-os"></a>Windows operációs rendszer esetén

A következő táblázat a kiszolgáló és a számítógép operációs rendszerének kompatibilitását mutatja be. A fájlok helyreállításakor nem állíthatja vissza a fájlokat egy korábbi vagy későbbi verziójú operációs rendszerre. Nem lehet például visszaállítani egy fájlt egy Windows Server 2016 rendszerű virtuális gépről a Windows Server 2012-re vagy egy Windows 8 rendszerű számítógépre. A virtuális gépek fájljait visszaállíthatja ugyanarra a kiszolgálói operációs rendszerre vagy a kompatibilis ügyfél operációs rendszerre.

|Kiszolgáló operációs rendszere | Kompatibilis ügyfél operációs rendszer  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Linux operációs rendszer esetén

A Linux rendszerben a fájlok visszaállítására használt számítógép operációs rendszerének támogatnia kell a védett virtuális gép fájlrendszerét. Amikor kijelöl egy számítógépet a parancsfájl futtatásához, győződjön meg arról, hogy a számítógép kompatibilis operációs rendszerrel rendelkezik, és az alábbi táblázatban felsorolt verziók egyikét használja:

|Linux operációs rendszer | Verziók  |
| --------------- | ---- |
| Ubuntu | 12,04 és újabb verziók |
| CentOS | 6,5 és újabb verziók  |
| RHEL | 6,7 és újabb verziók |
| Debian | 7 és újabb verziók |
| Oracle Linux | 6,4 és újabb verziók |
| SLES | 12 és újabb |
| openSUSE | 42,2 és újabb verziók |

> [!NOTE]
> Találtunk néhány problémát a fájl-helyreállítási szkript futtatásához a SLES 12 SP4 operációs rendszert futtató gépeken, és mi vizsgáljuk a SLES csapatot.
> Jelenleg a fájl-helyreállítási parancsfájl futtatása a SLES 12 SP2 és SP3 operációsrendszer-verzióval rendelkező gépeken működik.
>

A parancsfájlnak a Python és a bash összetevők futtatására is szükség van, és biztonságosan csatlakozhat a helyreállítási ponthoz.

|Összetevő | Verzió  |
| --------------- | ---- |
| bash | 4 és újabb verziók |
| python | 2.6.6 és újabb verziók  |
| .NET | 4.6.2 és újabb verziók |
| TLS | a 1,2 támogatottnak kell lennie  |

## <a name="step-4-access-requirements-to-successfully-run-the-script"></a>4. lépés: a parancsfájl sikeres futtatásához szükséges hozzáférési követelmények

Ha korlátozott hozzáféréssel rendelkező számítógépen futtatja a parancsfájlt, ellenőrizze, hogy van-e hozzáférése a következőhöz:

- `download.microsoft.com`
- A helyreállítási szolgáltatás URL-címei (a GEO neve arra a régióra utal, ahol a Recovery Services-tároló található)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.com` (Azure-beli nyilvános régiókban)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.cn` (Az Azure China 21Vianet esetében)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.us` (Azure USA kormánya)
  - `https://pod01-rec2.GEO-NAME.backup.windowsazure.de` (Az Azure Germany esetében)
- Kimenő portok 53 (DNS), 443, 3260

> [!NOTE]
>
> A [fenti](#step-1-generate-and-download-script-to-browse-and-recover-files) 1. lépésben letöltött parancsfájl a fájl nevében fogja tartalmazni a **geo-nevet** . Használja ezt a **földrajzi nevet** az URL-cím kitöltéséhez. A letöltött parancsfájl neve a következőket fogja kezdeni: \' VMname \' \_ \' geoname \' _ \' GUID \' .<br><br>
> Így például, ha a parancsfájl fájlneve *ContosoVM_wcus_12345678*, a **geo-név** *wcus* , és az URL-cím a következő lesz:<br> <https://pod01-rec2.wcus.backup.windowsazure.com>
>

Linux esetén a parancsfájl "Open-iSCSI" és "lshw" összetevőket igényel a helyreállítási ponthoz való kapcsolódáshoz. Ha az összetevők nem léteznek azon a számítógépen, amelyen a parancsfájl fut, a parancsfájl engedélyt kér az összetevők telepítésére. Adja meg a szükséges összetevők telepítésének jóváhagyását.

A hozzáférés `download.microsoft.com` szükséges a biztonságos csatorna létrehozásához használt összetevők letöltéséhez, amely a parancsfájl futtatására szolgáló gép és a helyreállítási pontban lévő információ között található.


## <a name="step-5-running-the-script-and-identifying-volumes"></a>5. lépés: a parancsfájl futtatása és a kötetek azonosítása

### <a name="for-windows"></a>Windows esetén

A 2. lépésben felsorolt követelmények teljesítése után másolja a szkriptet a letöltött helyről (általában a letöltések mappába), kattintson a jobb gombbal a végrehajtható fájlra vagy parancsfájlra, és futtassa a rendszergazdai hitelesítő adatokkal. Ha a rendszer kéri, írja be a jelszót, vagy illessze be a jelszót a memóriából, és nyomja le az ENTER billentyűt. Az érvényes jelszó megadása után a parancsfájl csatlakozik a helyreállítási ponthoz.

  ![Végrehajtható fájl kimenete](./media/backup-azure-restore-files-from-vm/executable-output.png)


A végrehajtható fájl futtatásakor az operációs rendszer csatlakoztatja az új köteteket, és hozzárendeli a meghajtóbetűjeleket. A meghajtók tallózásához használhatja a Windows Intézőt vagy a fájlkezelőt is. Előfordulhat, hogy a kötetekhez rendelt meghajtóbetűjelek nem azonosak az eredeti virtuális géppel. A kötet neve azonban megmarad. Ha például az eredeti virtuális gép kötete "adatlemez (E: `\` )" volt, akkor a kötet a helyi számítógépen "Adatlemezként" ("bármely levél":) csatolható `\` . Tallózással keresse meg a parancsfájl kimenetében említett összes kötetet, amíg meg nem találja a fájlokat vagy a mappát.  

   ![Csatolt helyreállítási kötetek](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-backed-up-vms-with-large-disks-windows"></a>Nagyméretű lemezekkel rendelkező biztonsági másolatok (Windows)

Ha a fájl-helyreállítási folyamat a fájl-visszaállítási parancsfájl futtatása után leáll (például ha a lemezek soha nem vannak csatlakoztatva, vagy csatlakoztatva vannak, de a kötetek nem jelennek meg), hajtsa végre a következő lépéseket:
  
1. Győződjön meg arról, hogy az operációs rendszer WS 2012 vagy újabb.
2. Győződjön meg arról, hogy a beállításjegyzék-kulcsok az alábbi módon vannak beállítva a visszaállítási kiszolgálón, és indítsa újra a kiszolgálót. A GUID melletti szám a 0001-0005-tól terjedhet. A következő példában ez a 0004. A parameters (paraméterek) szakaszig navigáljon a beállításkulcs elérési útjára.

    ![Beállításkulcs módosításai](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

### <a name="for-linux"></a>Linux esetén

Linux rendszerű gépeken Python-szkript jön létre. Töltse le a szkriptet, és másolja a megfelelő/kompatibilis Linux-kiszolgálóra. Előfordulhat, hogy módosítania kell az engedélyeket a futtatásához ```chmod +x <python file name>``` . Ezután futtassa a Python-fájlt a rel ```./<python file name>``` .


A Linux rendszerben a helyreállítási pont kötetei ahhoz a mappához vannak csatlakoztatva, amelyben a parancsfájl fut. Ennek megfelelően megjelennek a csatolt lemezek, kötetek és a hozzájuk tartozó csatlakoztatási útvonalak. Ezek a csatlakoztatási útvonalak a root szintű hozzáféréssel rendelkező felhasználók számára láthatók. Tallózzon a szkript kimenetében említett kötetek között.

  ![Linux-fájl helyreállítási menüje](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)


#### <a name="for-backed-up-vms-with-large-disks-linux"></a>Nagyméretű lemezekkel rendelkező biztonsági másolatok (Linux) * *

Ha a fájl-helyreállítási folyamat a fájl-visszaállítási parancsfájl futtatása után leáll (például ha a lemezek soha nem vannak csatlakoztatva, vagy csatlakoztatva vannak, de a kötetek nem jelennek meg), hajtsa végre a következő lépéseket:

1. A fájl/etc/iSCSI/iscsid.conf módosítsa a beállítást a következőről:
    - `node.conn[0].timeo.noop_out_timeout = 5`  hogy `node.conn[0].timeo.noop_out_timeout = 120`
2. A fenti módosítások végrehajtása után futtassa újra a parancsfájlt. Átmeneti hibák esetén győződjön meg arról, hogy az ismétlések között 20 – 30 percet vesz igénybe, hogy elkerülje a célzott előkészítést érintő kérelmek egymást követő feltöréseit. A ismételt futtatások közötti időköz biztosítja, hogy a cél készen álljon a parancsfájlból való kapcsolódásra.
3. A fájlok helyreállítása után lépjen vissza a portálra, és válassza a **lemezek leválasztása** lehetőséget a helyreállítási pontokhoz, ahol nem tudta csatlakoztatni a köteteket. Ez a lépés lényegében törli a meglévő folyamatokat/munkameneteket, és növeli a helyreállítás esélyét.


#### <a name="lvmraid-arrays-for-linux-vms"></a>LVM/RAID-tömbök (Linux rendszerű virtuális gépekhez)

A Linuxban a logikai kötetek kezelője (LVM) és/vagy a szoftveres RAID tömbök használatával több lemezen is kezelhetők a logikai kötetek. Ha a védett linuxos virtuális gép LVM és/vagy RAID tömböket használ, nem futtathatja ugyanazon a virtuális gépen a parancsfájlt.<br>
Ehelyett futtassa a parancsfájlt bármely más olyan gépen, amely kompatibilis operációs rendszerrel rendelkezik, és amely támogatja a védett virtuális gép fájlrendszerét.<br>
A következő parancsfájl kimenete az LVM és/vagy RAID tömbök lemezeit, valamint a partíció típusát tartalmazó köteteket jeleníti meg.

   ![Linux LVM kimeneti menü](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

A partíciók online állapotba helyezéséhez futtassa a következő szakaszban található parancsokat.

#### <a name="for-lvm-partitions"></a>LVM-partíciók esetén

A szkript futtatása után az LVM-partíciók a szkript kimenetében megadott fizikai kötet (ek)/Disk vannak csatlakoztatva. A folyamat

1. A mennyiségi csoportok neveinek egyedi listájának beolvasása a fizikai kötetekből vagy lemezekről
2. Ezután sorolja fel a kötetek logikai köteteit.
3. Ezután csatlakoztassa a logikai köteteket a kívánt elérési úthoz.

##### <a name="listing-volume-group-names-from-physical-volumes"></a>Mennyiségi csoportok neveinek listázása fizikai kötetekről

A kötetek csoportjai neveinek listázása:

```bash
pvs -o +vguuid
```

Ez a parancs felsorolja az összes fizikai kötetet (beleértve a parancsfájl futtatása előtt is), a hozzájuk tartozó kötetek csoportjának nevét és a kötet csoport egyedi felhasználói azonosítóit (UUID). Alább látható a parancs mintájának kimenete.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdf   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN

  /dev/sdd   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN
```

Az első oszlop (PV) megjeleníti a fizikai kötetet, az azt követő oszlopok a megfelelő kötet csoport nevét, formátumát, attribútumait, méretét, szabad területét és a kötet egyedi AZONOSÍTÓját jelenítik meg. A parancs kimenete az összes fizikai kötetet megjeleníti. Tekintse át a parancsfájl kimenetét, és azonosítsa a biztonsági mentéshez kapcsolódó köteteket. A fenti példában a szkript kimenete/dev/SDF és/dev/SDD. Így a *datavg_db* kötet csoport a parancsfájlhoz tartozik, és a *Appvg_new* kötet csoport a géphez tartozik. Az utolsó ötlet az, hogy egy egyedi kötet csoport nevének egy egyedi AZONOSÍTÓval kell rendelkeznie.

###### <a name="duplicate-volume-groups"></a>Duplikált kötetek csoportjai

Vannak olyan forgatókönyvek, ahol a kötetek nevei a szkript futtatása után 2 UUID-t tartalmazhatnak. Ez azt jelenti, hogy a kötet azon neve, amelyben a parancsfájlt futtatják, és a biztonsági másolatban szereplő virtuális gépen ugyanazok vannak. Ezt követően át kell neveznie a virtuális gépek biztonsági másolatának kötetei csoportot. Tekintse meg az alábbi példát.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdg   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdh   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdm2  rootvg    lvm2 a--  194.57g  127.57g efohjX-KUGB-ETaH-4JKB-MieG-EGOc-XcfLCt
```

A szkript kimenete a/dev/SDG, a/dev/SDH, a/dev/sdm2 és a csatolt módon jelenik meg. Így a megfelelő VG-nevek Appvg_new és rootvg. De ugyanezek a nevek is szerepelnek a gép VG listájában. Ellenőrizheti, hogy egy VG-név két UUID-val rendelkezik-e.

Most át kell neveznie a parancsfájl-alapú kötetek VG-nevét, például:/dev/SDG,/dev/SDH,/dev/sdm2. A kötet csoport átnevezéséhez használja a következő parancsot.

```bash
vgimportclone -n rootvg_new /dev/sdm2
vgimportclone -n APPVg_2 /dev/sdg /dev/sdh
```

Most már minden, egyedi azonosítóval rendelkező VG-név szerepel.

###### <a name="active-volume-groups"></a>Aktív kötetek csoportjai

Győződjön meg arról, hogy a parancsfájl köteteinek megfelelő kötetek aktívak. A következő parancs használatával jelenítheti meg az aktív kötetek csoportjait. Győződjön meg arról, hogy a parancsfájl kapcsolódó kötetei szerepelnek-e a listában.

```bash
vgdisplay -a
```  

Ellenkező esetben aktiválja a kötet csoportot a következő parancs használatával.

```bash
#!/bin/bash
vgchange –a y  <volume-group-name>
```

##### <a name="listing-logical-volumes-within-volume-groups"></a>A mennyiségi csoportokban található logikai kötetek listázása

Miután beolvasta a parancsfájlhoz kapcsolódó VGs egyedi, aktív listáját, az ezekben a kötetekben lévő logikai kötetek a következő paranccsal szerepelhetnek.

```bash
#!/bin/bash
lvdisplay <volume-group-name>
```

Ez a parancs az egyes logikai kötetek elérési útját jeleníti meg "LV Path" néven.

##### <a name="mounting-logical-volumes"></a>Logikai kötetek csatlakoztatása

A logikai kötetek csatlakoztatása a választott útvonalhoz:

```bash
#!/bin/bash
mount <LV path from the lvdisplay cmd results> </mountpath>
```

> [!WARNING]
> Ne használja a "Mount-a" kulcsszót. Ezzel a paranccsal az "/etc/fstab"-ben leírt összes eszköz csatlakoztatható. Ez azt jelentheti, hogy duplikált eszközök csatlakoztatása is lehetséges. Az adatelemek átirányíthatók egy parancsfájl által létrehozott eszközökre, amelyek nem őrzik meg az adatmegőrzést, ezért adatvesztést okozhatnak.

#### <a name="for-raid-arrays"></a>RAID-tömbök esetén

A következő parancs az összes RAID-lemez részleteit jeleníti meg:

```bash
#!/bin/bash
mdadm –detail –scan
```

 A megfelelő RAID-lemez a következőképpen jelenik meg `/dev/mdm/<RAID array name in the protected VM>`

Ha a RAID-lemez fizikai kötetekkel rendelkezik, használja a csatlakoztatási parancsot:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Ha a RAID-lemezen van egy másik LVM konfigurálva, akkor használja a fenti eljárást az LVM-partíciók esetében, de használja a kötet nevét a RAID-lemez nevének helyén.

## <a name="step-6-closing-the-connection"></a>6. lépés: a kapcsolatok bezárása

A fájlok azonosítása és a helyi tárolóhelyre való másolása után távolítsa el (vagy válassza le) a további meghajtókat. A meghajtók leválasztásához a Azure Portal **Fájl-helyreállítási** menüjében válassza a **lemezek** leválasztása lehetőséget.

![Lemezek leválasztása](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

A lemezek leválasztása után üzenet jelenik meg. A kapcsolatok frissítése eltarthat néhány percig, hogy el lehessen távolítani a lemezeket.

A Linux rendszerben a helyreállítási ponttal létesített csatlakozás letelte után az operációs rendszer nem távolítja el automatikusan a megfelelő csatlakoztatási útvonalakat. A csatlakoztatási útvonalak "árva" kötetekként léteznek, és láthatók, de hiba történt a fájlok elérése/írása közben. Ezeket manuálisan is el lehet távolítani. A parancsfájl futtatásakor a rendszer a korábbi helyreállítási pontokból származó összes ilyen kötetet azonosítja, és jóváhagyja őket.

> [!NOTE]
> Győződjön meg arról, hogy a szükséges fájlok visszaállítása után a kapcsolatok be vannak zárva. Ez fontos, különösen abban az esetben, ha a gép, amelyben a parancsfájlt futtatja, a biztonsági mentésre is konfigurálva van. Ha a kapcsolatok még nyitva vannak, a következő biztonsági mentés meghiúsulhat a "UserErrorUnableToOpenMount" hibaüzenettel. Ez azért történik, mert a csatlakoztatott meghajtók/kötetek elérhetőnek kell lenniük, és a hozzáférésük sikertelen lehet, mert az alapul szolgáló tárterület, azaz az iSCSI-célkiszolgáló nem érhető el. A kapcsolatok tisztítása eltávolítja ezeket a meghajtókat/köteteket, így azok nem lesznek elérhetők a biztonsági mentés során.

## <a name="security"></a>Biztonság

Ez a szakasz az Azure-beli virtuális gépek biztonsági másolatainak fájl-helyreállításának megvalósítására tett különböző biztonsági intézkedéseket ismerteti.

### <a name="feature-flow"></a>Szolgáltatás folyamata

Ez a funkció úgy lett felépítve, hogy hozzáférjen a virtuálisgép-információhoz anélkül, hogy vissza kellene állítania a teljes virtuális gépet vagy a virtuálisgép-lemezeket, és a minimális számú lépést. A virtuális gépekhez való hozzáférést egy parancsfájl adja meg (amely az alább látható módon csatlakoztatja a helyreállítási kötetet), és az összes biztonsági implementáció sarokköveként szolgál:

  ![Biztonsági szolgáltatás folyamata](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Biztonsági megvalósítások

#### <a name="select-recovery-point-who-can-generate-script"></a>Válassza ki a helyreállítási pontot (ki tud parancsfájlt előállítani)

A parancsfájl hozzáférést biztosít a virtuális gépekhez, ezért fontos annak szabályozása, hogy ki hozhatja elő az első helyen. Be kell jelentkeznie a Azure Portalba, és a szkript létrehozásához engedélyezni kell az [Azure RBAC](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) .

A fájl-helyreállításhoz a virtuális gépek visszaállításához és a lemezek visszaállításához azonos szintű engedélyezési szint szükséges. Más szóval csak a jogosultsággal rendelkező felhasználók tekinthetik meg a virtuális gépeket, és létrehozhatják a parancsfájlt.

A generált parancsfájl a Azure Backup szolgáltatáshoz tartozó hivatalos Microsoft-tanúsítvánnyal van aláírva. A szkripttel való illetéktelen módosítás azt jelenti, hogy az aláírás megszakadt, és a szkript futtatására tett bármilyen kísérlet az operációs rendszer potenciális kockázataként van kiemelve.

#### <a name="mount-recovery-volume-who-can-run-script"></a>A helyreállítási kötet csatlakoztatása (ki futtathat parancsfájlt)

Csak egy rendszergazda futtathatja a szkriptet, és emelt szintű módban kell futnia. A parancsfájl csak előre létrehozott lépéseket hajt végre, és nem fogadja el a külső forrásból érkező adatokat.

A szkript futtatásához jelszó szükséges ahhoz, hogy a Azure Portal vagy a PowerShell/CLI parancsfájl létrehozásakor csak a jogosultsággal rendelkező felhasználó jelenjen meg. Ezzel biztosíthatja, hogy a parancsfájlt futtató jogosult felhasználó a parancsfájl futtatásához is felelős legyen.

#### <a name="browse-files-and-folders"></a>Fájlok és mappák tallózása

A fájlok és mappák tallózásához a parancsfájl az iSCSI-kezdeményezőt használja a gépen, és csatlakozik az iSCSI-célként konfigurált helyreállítási ponthoz. Itt képzelheti el, hogy az egyik a vagy az összes összetevőt próbálja utánozni/hamisítani.

Kölcsönös CHAP-hitelesítési mechanizmust használunk, hogy minden összetevő hitelesítse a másikat. Ez azt jelenti, hogy a hamis kezdeményezők rendkívül nehéz csatlakozni az iSCSI-tárolóhoz, és ahhoz, hogy hamis célt lehessen csatlakoztatni ahhoz a géphez, amelyen a parancsfájl fut.

A helyreállítási szolgáltatás és a gép közötti adatforgalom védelme a biztonságos TLS-alagút TCP-kapcsolaton keresztüli létrehozásával történik (a TLS 1,2-et a parancsfájl futtatására szolgáló gépen[kell támogatni](#step-3-os-requirements-to-successfully-run-the-script) ).

A szülő/biztonsági mentés alatt lévő virtuális gépen található összes fájl Access Control lista (ACL) a csatlakoztatott fájlrendszerben is megmarad.

A parancsfájl csak olvasási hozzáférést biztosít egy helyreállítási ponthoz, és csak 12 órára érvényes. Ha korábban szeretné eltávolítani a hozzáférést, jelentkezzen be Azure Portal/PowerShell/parancssori felületre, és hajtson végre **leválasztott lemezeket** az adott helyreállítási ponthoz. A parancsfájl azonnal érvénytelenítve lesz.


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [állíthatja vissza a fájlokat a PowerShell](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup) használatával
- Ismerje meg, hogyan [állíthatja vissza a fájlokat az Azure CLI-n keresztül](./tutorial-restore-files.md)
- A virtuális gép visszaállítása után megtudhatja, hogyan [kezelheti a biztonsági mentéseket](./backup-azure-manage-vms.md)
