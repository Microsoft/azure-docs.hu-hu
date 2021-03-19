---
title: A Azure Disk Encryption használatával titkosított lemezek átméretezése
description: Ez a cikk útmutatást nyújt az ADE titkosított lemezek logikai kötetek kezelése használatával történő átméretezéséhez.
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: 92e3be9111abe5712549ba85ef4550a9ac1473f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596807"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>A Azure Disk Encryptiont használó logikai kötet-felügyeleti eszközök átméretezése

Ebből a cikkből megtudhatja, hogyan méretezheti át Azure Disk Encryptiont használó adatlemezeket. A lemezek átméretezéséhez használja a logikai kötetek felügyeletét (LVM) Linux rendszeren. A lépések több forgatókönyvre is érvényesek.

Ez az átméretezési folyamat a következő környezetekben használható:

- Linux-disztribúciók:
    - Red Hat Enterprise Linux (RHEL) 7 vagy újabb
    - Ubuntu 16 vagy újabb
    - SUSE 12 vagy újabb
- Azure Disk Encryption verziók: 
    - Single-pass kiterjesztés
    - Kettős pass kiterjesztés

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik a következővel:

- Egy meglévő LVM-konfiguráció. További információ: az [LVM konfigurálása Linux rendszerű virtuális gépen](/previous-versions/azure/virtual-machines/linux/configure-lvm).

- A Azure Disk Encryption által már titkosított lemezek. További információ: az [LVM és a RAID konfigurálása titkosított eszközökön](how-to-configure-lvm-raid-on-crypt.md).

- A Linux és az LVM használatának élménye.

- Az Azure-beli adatlemezek */dev/disk/scsi1/* elérési útjainak használata. További információkért lásd: Linux rendszerű [virtuális gépek eszközével kapcsolatos problémák elhárítása](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems). 

## <a name="scenarios"></a>Forgatókönyvek

A jelen cikkben ismertetett eljárások a következő helyzetekben érvényesek:

- Hagyományos LVM és LVM-on-Crypt konfigurációk
- Hagyományos LVM titkosítás 
- LVM-on-Crypt 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>Hagyományos LVM és LVM-on-Crypt konfigurációk

A hagyományos LVM és LVM-on-Crypt konfigurációk kibővítik a logikai kötetet (LV), ha a kötet (VG) elérhető területtel rendelkezik.

### <a name="traditional-lvm-encryption"></a>Hagyományos LVM titkosítás 

A hagyományos LVM titkosításban a LVs titkosítva vannak. A teljes lemez titkosítása nem történik meg.

A hagyományos LVM titkosítás használatával a következőket teheti:

- Kiterjesztheti az LV-t új fizikai kötet (PV) hozzáadásakor.
- Kiterjesztheti az LV-t egy meglévő PV átméretezése közben.

### <a name="lvm-on-crypt"></a>LVM-on-Crypt 

A lemezes titkosítás ajánlott módszere az LVM-titkosítás. Ez a módszer titkosítja a teljes lemezt, nem csak az LV-t.

Az LVM-on-Crypt használatával a következőket teheti: 

- Terjessze ki az LV-t új PV hozzáadásakor.
- Kiterjesztheti az LV-t egy meglévő PV átméretezése közben.

> [!NOTE]
> Nem javasoljuk, hogy ugyanazon a virtuális gépen a hagyományos LVM titkosítást és az LVM-on-Crypt-t keverje.

A következő szakaszokban példákat talál az LVM és az LVM – a crypt használatával történő használatáról. A példák a lemezek, a PVs, a VGs, a LVs, a fájlrendszerek, az univerzálisan egyedi azonosítók (UUID-EK) és a csatlakoztatási pontok már meglévő értékeit használják. Cserélje le ezeket az értékeket a saját értékeire, hogy illeszkedjen a környezetéhez.

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>Az LV kibővítése, ha a VG rendelkezésre áll terület

A LVs hagyományos módszere az, hogy kiterjessze az LV-t, ha a VG terület elérhető. Ezt a módszert használhatja a nem titkosított lemezekhez, a hagyományos LVM titkosítású kötetekhez és az LVM-on-Crypt-konfigurációkhoz.

1. Ellenőrizze a bővíteni kívánt fájlrendszer aktuális méretét:

    ``` bash
    df -h /mountpoint
    ```

    ![A fájlrendszer méretét ellenőrző kódot megjelenítő képernyőkép. A parancs és az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Ellenőrizze, hogy a VG rendelkezik-e elegendő hellyel az LV növeléséhez:

    ``` bash
    vgs
    ```

    ![Képernyőkép: a VG területét ellenőrző kód. A parancs és az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    A következőket is használhatja `vgdisplay` :

    ``` bash
    vgdisplay vgname
    ```

    ![Képernyőfelvétel: a következő, a VG-on helyet kereső V G megjelenítési kód. A parancs és az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Határozza meg, hogy melyik LV-t kell átméretezni:

    ``` bash
    lsblk
    ```

    ![Képernyőfelvétel: az l s s l k parancs eredményének megjelenítése. A parancs és az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    Az LVM-on-Crypt esetében a különbség az, hogy ez a kimenet azt mutatja, hogy a titkosított réteg a lemez szintjén van.

    ![Képernyőfelvétel: az l s s l k parancs eredményének megjelenítése. A kimenet ki van emelve. A titkosított réteget mutatja.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Az LV méretének ellenõrzése:

    ``` bash
    lvdisplay lvname
    ```

    ![Képernyőkép a logikai kötet méretét ellenőrző kód megjelenítéséről. A parancs és az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Növelje az LV-méretet a használatával a `-r` fájlrendszer online átméretezéséhez:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Képernyőfelvétel: a logikai kötet méretét megnövelő kód. A parancs és az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Ellenőrizze az LV és a fájlrendszer új méreteit:

    ``` bash
    df -h /mountpoint
    ```

    ![Képernyőfelvétel: a kód, amely ellenőrzi az LV és a fájlrendszer méretét. A parancs és az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    A méret kimenete azt jelzi, hogy az LV és a fájlrendszer átméretezése sikeresen megtörtént.

A lv-adatok újbóli ellenőrzésével ellenőrizheti az LV szintjén történt módosításokat:

``` bash
lvdisplay lvname
```

![Képernyőfelvétel: az új méreteket igazoló kód. A méretek ki vannak emelve.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>Hagyományos LVM-kötet kiterjesztése új PV hozzáadásával

Ha új lemezt kell hozzáadnia a VG méretének növeléséhez, a hagyományos LVM-kötetet új PV hozzáadásával bővítheti.

1. Ellenőrizze a bővíteni kívánt fájlrendszer aktuális méretét:

    ``` bash
    df -h /mountpoint
    ```

    ![Képernyőfelvétel a fájlrendszer aktuális méretét ellenőrző kód megjelenítéséről. A parancs és az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Az aktuális PV-konfiguráció ellenőrzése:

    ``` bash
    pvs
    ```

    ![Képernyőfelvétel: az aktuális PV-konfigurációt ellenőrző kód. A parancs és az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. A jelenlegi VG-információ keresése:

    ``` bash
    vgs
    ```

    ![Képernyőfelvétel: az aktuális mennyiségi csoport adatait ellenőrző kód. A parancs és az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Az aktuális lemez listájának megkeresése. Az adatlemezek azonosításához ellenőrizze az eszközöket a */dev/disk/Azure/scsi1/*-ben.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Képernyőfelvétel: az aktuális lemezek listáját ellenőrző kód. A parancs és az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. A kimenetének megkeresése `lsblk` : 

    ``` bash
    lsbk
    ```

    ![Képernyőfelvétel: az l s b l k kimenetét ellenőrző kód. A parancs és az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Csatlakoztassa az új lemezt a virtuális géphez az [adatlemez csatlakoztatása egy Linux rendszerű virtuális géphez](attach-disk-portal.md)című részben leírtak szerint.

7. Tekintse át a lemezek listáját, és figyelje meg az új lemezt.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Képernyőkép a lemezes listát ellenőrző kód megjelenítéséről. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![Képernyőfelvétel: a lemezes listát ellenőrző kód, l s b l A parancs és az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Hozzon létre egy új PV-t az új adatlemez tetejére:

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![Képernyőfelvétel: az új PV-t létrehozó kód. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Ez a metódus a teljes lemezt egy partíció nélküli PV-ként használja. Azt is megteheti, `fdisk` hogy partíciót hoz létre, majd ezt a partíciót használja a alkalmazáshoz `pvcreate` .

9. Ellenőrizze, hogy a PV hozzá lett-e adva a PV-listához:

    ``` bash
    pvs
    ```

    ![A fizikai kötetek listáját megjelenítő kódot bemutató képernyőkép. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Terjessze ki a VG-t az új PV hozzáadásával:

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![Képernyőfelvétel: a kötetet kibővítő kód. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Győződjön meg arról, hogy az új VG-méret:

    ``` bash
    vgs
    ```

    ![Képernyőfelvétel a kötet csoport méretét ellenőrző kód megjelenítéséről. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. `lsblk`A használatával azonosíthatja az átméretezni kívánt lv-t:

    ``` bash
    lsblk
    ```

    ![Képernyőfelvétel: az átméretezni kívánt helyi kötetet azonosító kódot megjelenítő kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Az LV méretének kiterjesztése a használatával a `-r` fájlrendszer online állapotának növeléséhez:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Képernyőfelvétel: a fájlrendszer online méretét megnövelő kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Ellenőrizze az LV és a fájlrendszer új méretét:

    ``` bash
    df -h /mountpoint
    ```

    ![Képernyőfelvétel: a helyi kötet és a fájlrendszer méretét ellenőrző kód. A parancs és az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >Ha az Azure-Adattitkosítás hagyományos LVM-konfigurációkon van használatban, a titkosított réteg az LV szintjén jön létre, nem pedig a lemez szintjén.
    >
    >Ezen a ponton a titkosított réteg ki van bontva az új lemezre. A tényleges adatlemez nem rendelkezik titkosítási beállításokkal a platform szintjén, így a titkosítási állapota nem frissül.
    >
    >Íme néhány ok, amiért az LVM-on-Crypt az ajánlott módszer. 

15. Keresse meg a titkosítási adatokat a portálon:

    ![A portálon található titkosítási adatokat bemutató képernyőkép. A lemez neve és a titkosítás ki van emelve.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    A lemezen lévő titkosítási beállítások frissítéséhez adjon hozzá egy új LV-t, és engedélyezze a bővítményt a virtuális gépen.
    
16. Vegyen fel egy új LV-et, hozzon létre egy fájlrendszert, és adja hozzá a következőhöz: `/etc/fstab` .

17. Állítsa be újra a titkosítási bővítményt. Ekkor az új adatlemez titkosítási beállításait a platform szintjén kell lepecsételni. Íme egy példa a CLI-re:

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Keresse meg a titkosítási adatokat a portálon:

    ![A portálon található titkosítási adatokat bemutató képernyőkép. A lemez neve és a titkosítási adatok ki vannak emelve.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

A titkosítási beállítások frissítése után törölheti az új LV-t. Törölje a bejegyzést a és a létrehozott elemből is `/etc/fstab` `/etc/crypttab` .

![Képernyőfelvétel: az új logikai kötetet törlő kód. A törölt F S és a crypt lap ki van emelve.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

A tisztítás befejezéséhez kövesse az alábbi lépéseket:

1. Az LV leválasztása:

    ``` bash
    umount /mountpoint
    ```

1. A kötet titkosított rétegének lezárása:

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. Az LV törlése:

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>Hagyományos LVM-kötet kiterjesztése egy meglévő PV átméretezésével

Bizonyos forgatókönyvek esetében előfordulhat, hogy a korlátozásokhoz egy meglévő lemez átméretezése szükséges. Ezt a következőképpen teheti meg:

1. Azonosítsa a titkosított lemezeket:

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![A titkosított lemezeket azonosító kódot bemutató képernyőkép. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![Képernyőfelvétel: a titkosított lemezeket azonosító alternatív kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Keresse meg a PV-információkat:

    ``` bash
    pvs
    ```

    ![Képernyőfelvétel: a fizikai kötet adatait ellenőrző kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    A képen látható eredmények azt mutatják, hogy az összes PVs lévő terület jelenleg használatban van.

3. Keresse meg a VG adatait:

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![Képernyőfelvétel: a kötetre vonatkozó információkat ellenőrző kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Vizsgálja meg a lemez méretét. Használhatja `fdisk` `lsblk` a vagy a lehetőséget a meghajtók méretének listázásához.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Képernyőkép a lemez méretét ellenőrző kód megjelenítéséről. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Itt azonosította azokat a PVs, amelyekhez LVs van társítva `lsblk -fs` . A társítások a futtatásával azonosíthatók `lvdisplay` .

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Képernyőkép a helyi kötetekkel rendelkező fizikai kötetek hozzárendeléseinek azonosítására szolgáló alternatív módszerről. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    Ebben az esetben mind a négy adatmeghajtó ugyanannak a VG-nek és egyetlen LV-nek a részét képezi. A konfiguráció eltérő lehet.

5. A fájlrendszer aktuális kihasználtságának keresése:

    ``` bash
    df -h /datalvm*
    ```

    ![A fájlrendszer kihasználtságát ellenőrző kódot bemutató képernyőkép. A parancs és az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Méretezze át az adatlemezeket az [Azure felügyelt lemez kibontása](expand-disks.md#expand-an-azure-managed-disk)című részben leírtak szerint. Használhatja a portált, a CLI-t vagy a PowerShellt.

    >[!IMPORTANT]
    >A virtuális lemezek nem méretezhetők át, amíg a virtuális gép fut. A virtuális gép felszabadítása ehhez a lépéshez.

7. Indítsa el a virtuális gépet, és a használatával vizsgálja meg az új méreteket `fdisk` .

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Képernyőfelvétel a lemez méretét ellenőrző kód megjelenítéséről. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    Ebben az esetben `/dev/sdd` 5 g és 20 g közötti méretre módosult.

8. Az aktuális PV-méret ellenõrzése:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Képernyőfelvétel: a P V méretét ellenőrző kód. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Bár a lemez átméretezése megtörtént, a PV továbbra is az előző mérettel rendelkezik.

9. Méretezze át a PV-t:

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![Képernyőfelvétel: a fizikai kötet átméretezésére szolgáló kód. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. A PV méretének ellenõrzése:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Képernyőfelvétel: a fizikai kötet méretét ellenőrző kód. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Alkalmazza ugyanazt az eljárást az összes átméretezni kívánt lemez esetében.

11. Keresse meg a VG-információkat.

    ``` bash
    vgdisplay vgname
    ```

    ![Képernyőfelvétel a kötetre vonatkozó információkat ellenőrző kód megjelenítéséről. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    A VG-nek most már elegendő lemezterületet kell kiosztania a LVs.

12. Az LV átméretezése:

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![Képernyőfelvétel: az L V átméretezni kívánt kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. A fájlrendszer méretének ellenõrzése:

    ``` bash
    df -h /datalvm2
    ```

    ![A fájlrendszer méretét ellenőrző kódot megjelenítő képernyőkép. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>Egy új PV hozzáadásával kiterjesztheti az LVM-on-Crypt kötetet

Egy új PV hozzáadásával is kiterjesztheti az LVM-on-Crypt kötetet. Ez a módszer szorosan követi az [LVM és a RAID konfigurálása titkosított eszközökön](how-to-configure-lvm-raid-on-crypt.md#general-steps)című témakör lépéseit. Tekintse meg az új lemezek hozzáadását és az LVM-on-Crypt konfigurációban való beállítását ismertető szakaszt.

Ezzel a módszerrel hozzáadhat helyet egy meglévő LV-hez. Vagy létrehozhat új VGs vagy LVs is.

1. A VG jelenlegi méretének ellenőrzése:

    ``` bash
    vgdisplay vgname
    ```

    ![Képernyőfelvétel a kötet csoport méretét ellenőrző kód megjelenítéséről. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Ellenőrizze a kibontani kívánt fájlrendszer és LV méretét:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Képernyőfelvétel: a helyi kötet méretét ellenőrző kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![Képernyőfelvétel: a fájlrendszer méretét ellenőrző kód. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Adjon hozzá egy új adatlemezt a virtuális géphez, és azonosítsa azt.

    Az új lemez hozzáadása előtt tekintse meg a lemezeket:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![A lemezek méretét ellenőrző kódot megjelenítő képernyőkép. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Az új lemez hozzáadása előtt a következő egy másik módja a lemezek ellenőrzésének:

    ``` bash
    lsblk
    ```

    ![Képernyőfelvétel: a lemezek méretét ellenőrző alternatív kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Az új lemez hozzáadásához használhatja a PowerShellt, az Azure CLI-t vagy a Azure Portal. További információ: [adatlemez csatlakoztatása Linux rendszerű virtuális géphez](attach-disk-portal.md).

    A kernel-elnevezési séma az újonnan hozzáadott eszközre vonatkozik. Az új meghajtó általában a következő elérhető levélhez van rendelve. Ebben az esetben a hozzáadott lemez a következő: `sdd` .

4. Ellenőrizze a lemezeket, és győződjön meg arról, hogy az új lemez hozzá lett adva:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![A lemezeket felsoroló kódot megjelenítő képernyőkép. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![Képernyőfelvétel: az újonnan hozzáadott lemez a kimenetben.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Hozzon létre egy fájlrendszert a legutóbb hozzáadott lemez tetején. Társítsa a lemezt a társított eszközökhöz `/dev/disk/azure/scsi1/` .

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![Képernyőfelvétel a fájlrendszert létrehozó kód megjelenítéséről. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![Képernyőfelvétel: a fájlrendszert létrehozó további kód, amely megfelel a társított eszközök lemezének. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Hozzon létre egy ideiglenes csatlakozási pontot az új hozzáadott lemezhez:

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Adja hozzá a legutóbb létrehozott fájlrendszert a következőhöz: `/etc/fstab` .

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Csatlakoztassa az újonnan létrehozott fájlrendszert:

    ``` bash
    mount -a
    ```

9. Ellenőrizze, hogy az új fájlrendszer csatlakoztatva van-e:

    ``` bash
    df -h
    ```

    ![Képernyőfelvétel: az a kód, amely ellenőrzi, hogy a fájlrendszer csatlakoztatva van-e. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Képernyőfelvétel: további kód, amely ellenőrzi, hogy a fájlrendszer csatlakoztatva van-e. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Indítsa újra az adatmeghajtók korábban elindított titkosítását.

    >[!TIP]
    >Az LVM-Crypt esetében ajánlott a használata `EncryptFormatAll` . Ellenkező esetben előfordulhat, hogy a további lemezek beállításakor a rendszer dupla titkosítást lát.
    >
    >További információ: az [LVM és a RAID konfigurálása titkosított eszközökön](how-to-configure-lvm-raid-on-crypt.md).

    Bemutatunk egy példát:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    Ha a titkosítás befejeződik, a rendszer egy Crypt réteget lát az újonnan hozzáadott lemezen:

    ``` bash
    lsblk
    ```

    ![Képernyőkép a crypt réteget ellenőrző kód megjelenítéséről. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Válassza le az új lemez titkosított rétegét:

    ``` bash
    umount ${newmount}
    ```

12. Az aktuális PV-információ keresése:

    ``` bash
    pvs
    ```

    ![Képernyőfelvétel: a fizikai kötet adatait ellenőrző kód. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Hozzon létre egy PV-t a lemez titkosított rétegének tetején. Használja az eszköz nevét az előző `lsblk` parancsból. `/dev/`A PV létrehozásához vegyen fel egy Mapper-t az eszköz neve elé:

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![Képernyőfelvétel: a titkosított réteg fizikai kötetét létrehozó kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Az aktuális aláírás törlésével kapcsolatos figyelmeztetés jelenik meg `ext4 fs` . Ez a figyelmeztetés várható. Válaszolja meg ezt a kérdést a következővel: `y` .

14. Győződjön meg arról, hogy az új PV hozzá lett adva az LVM-konfigurációhoz:

    ``` bash
    pvs
    ```

    ![Képernyőfelvétel: az a kód, amely ellenőrzi, hogy a fizikai kötet hozzá lett-e adva az LVM-konfigurációhoz. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Adja hozzá az új PV-t a növeléshez szükséges VG-hez.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![Képernyőfelvétel: a fizikai kötetet egy kötet csoportba feltüntető kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Ellenőrizze a VG új méretét és szabad területét:

    ``` bash
    vgdisplay vgname
    ```

    ![A kötet méretét és szabad területét ellenőrző kódot bemutató képernyőkép. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Figyelje meg a `Total PE` darabszám és a mennyiség növekedését `Free PE / Size` .

17. Növelje a LV és a fájlrendszer méretét. Használja a `-r` kapcsolót `lvextend` . Ebben a példában a teljes rendelkezésre álló tárterületet adjuk hozzá a VG-hoz az adott LV-hez.

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![Képernyőfelvétel: a helyi kötet és a fájlrendszer méretét megnövelő kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

A módosítások ellenőrzéséhez kövesse a következő lépéseket.

1. A LV méretének ellenőrzése:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Képernyőfelvétel: a helyi kötet új méretét ellenőrző kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. Ellenőrizze a fájlrendszer új méretét:

    ``` bash
    df -h mountpoint
    ```

    ![A fájlrendszer új méretét ellenőrző kódot megjelenítő képernyőkép. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. Győződjön meg arról, hogy az LVM réteg a titkosított rétegen felül van:

    ``` bash
    lsblk
    ```

    ![Képernyőfelvétel: a kód, amely azt ellenőrzi, hogy az LVM réteg a titkosított rétegen felül van-e. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Ha `lsblk` beállítások nélkül használja, akkor a csatlakoztatási pontok többször is megjelennek. A parancs eszköz-és LVs szerint rendezi. 

    Érdemes lehet használni `lsblk -fs` . Ebben a parancsban `-fs` megfordítja a rendezési sorrendet, hogy a csatlakoztatási pontok egyszer megjelenjenek. A lemezek többször is megjelennek.

    ``` bash
    lsblk -fs
    ```

    ![Képernyőfelvétel: alternatív kód, amely azt ellenőrzi, hogy az LVM réteg a titkosított rétegen felül van-e. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>Az LVM kibővítése egy Crypt-köteten egy meglévő PV átméretezésével

1. Azonosítsa a titkosított lemezeket:

    ``` bash
    lsblk
    ```

    ![A titkosított lemezeket azonosító kódot megjelenítő képernyőkép. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![A titkosított lemezeket azonosító alternatív kódot bemutató képernyőkép. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. A PV-információk megkeresése:

    ``` bash
    pvs
    ```

    ![A fizikai kötetek adatait ellenőrző kódot ábrázoló képernyőkép. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Keresse meg a VG-adatokat:

    ``` bash
    vgs
    ```

    ![Képernyőfelvétel: a mennyiségi csoportok adatait ellenőrző kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Az LV-információk megtekintése:

    ``` bash
    lvs
    ```

    ![Képernyőkép a helyi kötet információit ellenőrző kód megjelenítéséről. Az eredmény ki van emelve.](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Keresse meg a fájlrendszer kihasználtságát:

    ``` bash
    df -h /mountpoint(s)
    ```

    ![Képernyőfelvétel: a fájlrendszer mennyiségét ellenőrző kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. A lemezek méretének ellenőrzéséhez:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![A lemezek méretét ellenőrző kódot megjelenítő képernyőkép. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Méretezze át az adatlemezt. Használhatja a portált, a CLI-t vagy a PowerShellt. További információ: a [virtuális merevlemezek kibontása Linux](expand-disks.md#expand-an-azure-managed-disk)rendszerű virtuális gépen a lemez átméretezése szakasz. 

    >[!IMPORTANT]
    >A virtuális lemezek nem méretezhetők át, amíg a virtuális gép fut. A virtuális gép felszabadítása ehhez a lépéshez.

8. A lemezek méretének ellenőrzéséhez:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![A lemez méretét ellenőrző kódot bemutató képernyőkép. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    Ebben az esetben mindkét lemez mérete 2 GB és 4 GB között volt. A fájlrendszer, az LV és a PV mérete azonban változatlan marad.

9. Az aktuális PV-méret ellenõrzése. Ne feledje, hogy az LVM-on-Crypt-on a PV az `/dev/mapper/` eszköz, nem az `/dev/sd*` eszköz.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Képernyőfelvétel: az aktuális fizikai kötet méretét ellenőrző kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Méretezze át a PV-t:

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![Képernyőfelvétel: a fizikai kötet átméretezésére szolgáló kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Győződjön meg arról, hogy az új PV-méret:

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Képernyőfelvétel: a fizikai kötet méretét ellenőrző kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Méretezze át a titkosított réteget a PV-ben:

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Alkalmazza ugyanazt az eljárást az összes átméretezni kívánt lemez esetében.

13. Keresse meg a VG-adatokat:

    ``` bash
    vgdisplay vgname
    ```

    ![Képernyőfelvétel a kötetre vonatkozó információkat ellenőrző kód megjelenítéséről. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    A VG-nek most már elegendő lemezterületet kell kiosztania a LVs.

14. Keresse meg az LV-adatokat:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Képernyőkép a helyi kötet információit ellenőrző kód megjelenítéséről. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Keresse meg a fájlrendszer kihasználtságát:

    ``` bash
    df -h /mountpoint
    ```

    ![A fájlrendszer kihasználtságát ellenőrző kódot bemutató képernyőkép. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Az LV átméretezése:

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![Képernyőfelvétel: a helyi kötet átméretezésére szolgáló kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Itt azt a `-r` lehetőséget használjuk, hogy a fájlrendszert is átméretezheti.

17. Keresse meg az LV-adatokat:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Képernyőfelvétel: a helyi kötetre vonatkozó adatokat lekérdező kód. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Keresse meg a fájlrendszer kihasználtságát:

    ``` bash
    df -h /mountpoint
    ```

    ![A fájlrendszer kihasználtságát ellenőrző kódot megjelenítő képernyőkép. Az eredmények ki vannak emelve.](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

Alkalmazza ugyanazt az átméretezési eljárást minden olyan LV-re, amelyhez szükség van.

## <a name="next-steps"></a>Következő lépések

[Hibakeresés Azure Disk Encryption](disk-encryption-troubleshooting.md)