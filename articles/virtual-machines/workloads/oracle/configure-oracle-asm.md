---
title: Az Oracle ASM beállítása Azure Linux rendszerű virtuális gépeken | Microsoft Docs
description: Az Oracle ASM gyors üzembe helyezése az Azure-környezetben.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: b180a0f148db5222a288958724ad248acd18b565
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673852"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Az Oracle ASM beállítása Azure Linux rendszerű virtuális gépen  

Az Azure-beli virtuális gépek egy teljes mértékben konfigurálható és rugalmas számítási környezetet nyújtanak. Ez az oktatóanyag az Azure-beli virtuális gépek alapszintű üzembe helyezését ismerteti az Oracle automatizált Storage Management (ASM) telepítésével és konfigurálásával együtt.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Oracle Database virtuális gép létrehozása és kapcsolódás
> * Az Oracle automatizált tárolók felügyeletének telepítése és konfigurálása
> * Az Oracle Grid-infrastruktúra telepítése és konfigurálása
> * Oracle ASM-telepítés inicializálása
> * Az ASM által felügyelt Oracle DB létrehozása


Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>A környezet előkészítése

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Erőforráscsoport létrehozásához használja az az [Group Create](/cli/azure/group) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelyben az Azure-erőforrások üzembe helyezése és kezelése történik. Ebben a példában egy *myResourceGroup* nevű erőforráscsoportot a *eastus* régióban.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Virtuális gép létrehozása

Ha a Oracle Database rendszerkép alapján szeretne virtuális gépet létrehozni, és az Oracle ASM használatára szeretné konfigurálni, használja az az [VM Create](/cli/azure/vm) parancsot. 

Az alábbi példa egy myVM nevű virtuális gépet hoz létre, amely egy Standard_DS2_v2-méret, amely négy, 50 GB-nyi adatlemezzel rendelkezik. Ha még nem léteznek az alapértelmezett kulcs helyén, az SSH-kulcsokat is létrehoz.  Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. Jegyezze fel a értékét `publicIpAddress` . Ennek a címnek a használatával férhet hozzá a virtuális géphez.

   ```output
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Ha SSH-munkamenetet szeretne létrehozni a virtuális géppel, és további beállításokat konfigurál, használja a következő parancsot. Cserélje le az IP-címet a `publicIpAddress` virtuális gép értékére.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Az Oracle ASM telepítése

Az Oracle ASM telepítéséhez hajtsa végre az alábbi lépéseket. 

Az Oracle ASM telepítésével kapcsolatos további információkért lásd: [Oracle ASMLib-letöltések Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Az ASM telepítésének folytatásához root-ként kell bejelentkeznie:

   ```bash
   sudo su -
   ```
   
2. Futtassa ezeket a további parancsokat az Oracle ASM-összetevők telepítéséhez:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Ellenőrizze, hogy telepítve van-e az Oracle ASM:

   ```bash
   rpm -qa |grep oracleasm
   ```

    A parancs kimenetének a következő összetevőket kell kilistáznia:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. Az ASM-nek konkrét felhasználóknak és szerepköröknek kell lennie ahhoz, hogy megfelelően működjön. Az alábbi parancsok az előfeltételként szükséges felhasználói fiókokat és csoportokat hozza létre: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Ellenőrizze, hogy a felhasználók és csoportok helyesen lettek-e létrehozva:

   ```bash
   id grid
   ```

    A parancs kimenetének a következő felhasználókat és csoportokat kell listáznia:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Hozzon létre egy mappát a felhasználói *Rácshoz* , és módosítsa a tulajdonost:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Oracle ASM beállítása

Ebben az oktatóanyagban az alapértelmezett felhasználó a *Grid* , és az alapértelmezett csoport a *asmadmin*. Győződjön meg arról, hogy az *Oracle* -felhasználó tagja a asmadmin csoportnak. Az Oracle ASM telepítésének beállításához hajtsa végre a következő lépéseket:

1. Az Oracle ASM Library-illesztőprogram beállítása magában foglalja az alapértelmezett felhasználó (rács) és az alapértelmezett csoport (asmadmin) definiálását, valamint a meghajtó konfigurálását a rendszerindítási indításhoz (az y választása) és a lemezek a rendszerindításkor való vizsgálatához A következő parancs utasításait kell megválaszolnia:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   A parancs kimenetének az alábbihoz hasonlóan kell kinéznie, és a rendszer kéri, hogy válaszoljon.

    ```output
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. A lemez konfigurációjának megtekintése:

   ```bash
   cat /proc/partitions
   ```

   A parancs kimenetének az elérhető lemezek alábbi listájához hasonlóan kell kinéznie

   ```output
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Formázza a lemez */dev/SDC* az alábbi parancs futtatásával, és az utasításokat a következő paranccsal válaszolva:
   - *n* új partícióhoz
   - *p* az elsődleges partícióhoz
   - *1* az első partíció kiválasztásához
   - `enter`az alapértelmezett első henger megnyomása
   - `enter`az alapértelmezett utolsó henger megnyomása
   - nyomja meg a *w* billentyűt a partíciós tábla módosításainak írásához  

   ```bash
   fdisk /dev/sdc
   ```
   
   A fent megadott válaszok használatával a parancs kimenetének a `fdisk` következőhöz hasonlóan kell kinéznie:

   ```output
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Ismételje meg az előző `fdisk` parancsot `/dev/sdd` , `/dev/sde` és `/dev/sdf` .

5. A lemez konfigurációjának ellenõrzése:

   ```bash
   cat /proc/partitions
   ```

   A parancs kimenetének a következőhöz hasonlóan kell kinéznie:

   ```output
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Keresse meg az Oracle ASM szolgáltatás állapotát, és indítsa el az Oracle ASM szolgáltatást:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   A parancs kimenetének a következőhöz hasonlóan kell kinéznie:

   ```output
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Oracle ASM-lemezek létrehozása:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```

   A parancs kimenetének a következőhöz hasonlóan kell kinéznie:

   ```output
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Oracle ASM-lemezek listázása:

   ```bash
   service oracleasm listdisks
   ```

   A parancs kimenetének ki kell listáznia a következő Oracle ASM-lemezeket:

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. A gyökér, az Oracle és a Grid felhasználók jelszavának módosítása. **Jegyezze fel ezeket az új jelszavakat** , amikor később a telepítés során használja őket.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. A mappa engedélyeinek módosítása:

    ```bash
    chmod -R 775 /opt 
    chown grid:oinstall /opt 
    chown oracle:oinstall /dev/sdc1 
    chown oracle:oinstall /dev/sdd1 
    chown oracle:oinstall /dev/sde1 
    chown oracle:oinstall /dev/sdf1 
    chmod 600 /dev/sdc1 
    chmod 600 /dev/sdd1 
    chmod 600 /dev/sde1 
    chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Oracle Grid-infrastruktúra letöltése és előkészítése

Az Oracle Grid Infrastructure szoftver letöltéséhez és előkészítéséhez végezze el a következő lépéseket:

1. Töltse le az Oracle Grid-infrastruktúrát az [Oracle ASM letöltési oldaláról](https://www.oracle.com/database/technologies/oracle19c-linux-downloads.html). 

   Töltse le a két. zip fájlt a következő címen **: Oracle Database 12C Release 1 Grid Infrastructure (12.1.0.2.0) a Linux x86-64-hez**.

2. Miután letöltötte a. zip-fájlokat az ügyfélszámítógépre, a biztonságos másolási protokoll (SCP) használatával másolja a fájlokat a virtuális gépre:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. Az SSH-t az Azure-ban visszahelyezheti Oracle-beli virtuális gépén, hogy a. zip-fájlokat a/opt mappába helyezze. Ezután módosítsa a fájlok tulajdonosát:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Bontsa ki a fájlokat. (Telepítse a Linux unzip eszközt, ha még nincs telepítve.)

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Módosítási engedély:

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. A beállított swap-terület frissítése. Az Oracle Grid-összetevőkön legalább 6,8 GB-nyi lapozófájl szükséges a rács telepítéséhez. Az Azure-ban Oracle Linux rendszerképeknél az alapértelmezett lapozófájl-méret csak 2048 MB. A `ResourceDisk.SwapSizeMB` `/etc/waagent.conf` frissített beállítások érvénybe léptetéséhez növelje a fájlt, majd indítsa újra a WALinuxAgent szolgáltatást. Mivel ez egy írásvédett fájl, meg kell változtatnia a fájl engedélyeit az írási hozzáférés engedélyezéséhez.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   Keresse meg `ResourceDisk.SwapSizeMB` és módosítsa az értéket a **8192** értékre. A `insert` beszúrási mód megadásához meg kell adnia a értéket, írja be a következőt: **8192** , majd nyomja meg a gombot a `esc` parancs módba való visszatéréshez. A módosítások írásához és a fájl bezárásához írja be a következőt: `:wq` és nyomja meg a gombot `enter` .
   
   > [!NOTE]
   > Azt javasoljuk, hogy mindig használja a `WALinuxAgent` swap-terület konfigurálását úgy, hogy az mindig a helyi ideiglenes lemezen (ideiglenes lemez) legyen létrehozva a legjobb teljesítmény érdekében. További információ: [swap-fájl hozzáadása a Linux Azure Virtual Machines](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines)szolgáltatásban.

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Készítse elő a helyi ügyfelet és a virtuális gépet az X11 futtatásához
Az Oracle ASM konfigurálásához grafikus felület szükséges a telepítés és a konfiguráció végrehajtásához. Az X11 protokollt használjuk a telepítés megkönnyítésére. Ha olyan (Mac vagy Linux rendszerű) ügyfélszoftvert használ, amelyen már engedélyezve van és konfigurálva van az X11 funkció, akkor kihagyhatja ezt a konfigurációt és a telepítőt kizárólag a Windows rendszerű gépekre. 

1. [Töltse le a PuTTY](https://www.putty.org/) -t, és [töltse le a Xming](https://xming.en.softonic.com/) a Windows rendszerű számítógépére. A folytatás előtt el kell végeznie mindkét alkalmazás telepítésének befejezését az alapértelmezett értékekkel.

2. A PuTTY telepítése után nyisson meg egy parancssort, váltson a PuTTY mappára (például C:\Program Files\PuTTY), és futtassa a parancsot a `puttygen.exe` kulcs létrehozásához.

3. A PuTTY Key Generatorban:
   
   1. Egy kulcs létrehozásához kattintson a `Generate` gombra.
   2. Másolja a kulcs tartalmát (CTRL + C).
   3. Kattintson a(z) `Save private key` gombra.
   4. Hagyja figyelmen kívül a kulcs jelszavas védelmével kapcsolatos figyelmeztetést, majd válassza a lehetőséget `OK` .

   ![A PuTTY Key Generator képernyőképe](./media/oracle-asm/puttykeygen.png)

4. A virtuális gépen futtassa a következő parancsokat:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Hozzon létre egy `authorized_keys` nevű fájlt. Illessze be a kulcs tartalmát a fájlban, majd mentse a fájlt.

   > [!NOTE]
   > A kulcsnak tartalmaznia kell a karakterláncot `ssh-rsa` . Emellett a kulcs tartalmának egysoros szövegnek kell lennie.
   >  

6. Az ügyfél rendszeren indítsa el a PuTTY-t. A **Kategória** ablaktáblán válassza a **Kapcsolódás**  >  **SSH**-  >  **hitelesítés** lehetőséget. A **hitelesítő fájl titkos kulcsa** mezőben keresse meg a korábban létrehozott kulcsot.

   ![Az SSH-hitelesítési lehetőségek képernyőképe](./media/oracle-asm/setprivatekey.png)

7. A **Kategória** ablaktáblán lépjen a **Kapcsolódás**  >  **SSH**  >  **X11-hez**. Jelölje be az **X11 továbbításának engedélyezése** jelölőnégyzetet.

   ![Képernyőkép az SSH X11 továbbítási lehetőségeiről](./media/oracle-asm/enablex11.png)

8. A **Kategória** ablaktáblán lépjen a **munkamenet** elemre. Adja meg az Oracle ASM virtuális gép `<publicIPaddress>` nevét az állomásnév párbeszédpanelen, majd adja meg az új `Saved Session` nevet, majd kattintson a be gombra `Save` .  A mentés után kattintson a be gombra az `open` Oracle ASM-beli virtuális géphez való kapcsolódáshoz.  Amikor először kapcsolódik, a rendszer figyelmezteti, hogy a távoli rendszer nem gyorsítótárazza a beállításjegyzékben. A `yes` hozzáadásához és a folytatáshoz kattintson a Tovább gombra.

   ![Képernyőfelvétel a PuTTY munkamenet lehetőségeiről](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Az Oracle Grid-infrastruktúra telepítése

Az Oracle Grid-infrastruktúra telepítéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be **rácsként**. (Be kell jelentkeznie anélkül, hogy jelszót kellene megadnia.) 

   > [!NOTE]
   > Ha Windows rendszert futtat, a telepítés megkezdése előtt győződjön meg arról, hogy elindította a Xming.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Megnyílik az Oracle Grid Infrastructure 12c Release 1 telepítője. (Eltarthat néhány percig, amíg a telepítő elindul.)

2. A **telepítési lehetőség kiválasztása** lapon válassza az **Oracle Grid-infrastruktúra telepítése és konfigurálása különálló kiszolgálóhoz** lehetőséget.

   ![A telepítő telepítési lehetőségének kiválasztása lap képernyőképe](./media/oracle-asm/install01.png)

3. A **termék nyelvének kiválasztása** lapon győződjön meg arról, hogy az **angol** vagy a kívánt nyelv van kiválasztva.  Kattintson a `next` gombra.

4. Az **ASM lemez csoport létrehozása** lapon:
   - Adja meg a lemez csoport nevét.
   - A **redundancia** alatt válassza a **külső** lehetőséget.
   - A **foglalási egység mérete** területen válassza a **4** lehetőséget.
   - A **lemezek hozzáadása** területen válassza a **ORCLASMSP** lehetőséget.
   - Kattintson a `next` gombra.

5. Az **ASM-jelszó megadása** lapon jelölje be az **azonos jelszavak használata ezekhez a fiókokhoz** lehetőséget, és adjon meg egy jelszót.

   ![Képernyőfelvétel a telepítő ASM-jelszavának megadása oldaláról](./media/oracle-asm/install04.png)

6. A **felügyeleti beállítások megadása** lapon lehetősége van az em Cloud Control konfigurálására. Kihagyjuk ezt a lehetőséget – a `next` folytatáshoz kattintson ide. 

7. A **Rendszerjogosultságú operációs rendszerek csoportjai** lapon használja az alapértelmezett beállításokat. Kattintson `next` a folytatáshoz.

8. A **telepítési hely megadása** lapon használja az alapértelmezett beállításokat. Kattintson `next` a folytatáshoz.

9. A **Leltár létrehozása** lapon módosítsa a leltári könyvtárat a következőre: `/u01/app/grid/oraInventory` . Kattintson `next` a folytatáshoz.

   ![Képernyőkép a telepítő leltár létrehozása oldaláról](./media/oracle-asm/install08.png)

10. A **gyökérszintű parancsfájl-végrehajtási konfiguráció** lapon jelölje be a **konfigurációs parancsfájlok automatikus futtatása** jelölőnégyzetet. Ezután válassza a **"root" felhasználói hitelesítő adatok használata** lehetőséget, és adja meg a gyökér felhasználói jelszavát.

    ![Képernyőfelvétel a telepítő gyökérszintű parancsfájl-végrehajtási konfigurációjának oldaláról](./media/oracle-asm/install09.png)

11. Az **Előfeltételek ellenőrzésének végrehajtása** lapon a jelenlegi telepítés hibákkal meghiúsul. Ez egy várt viselkedés. Válassza a(z) `Fix & Check Again` lehetőséget.

12. A **parancsfájl kijavítása** párbeszédpanelen kattintson a elemre `OK` .

13. Az **Összefoglalás** lapon tekintse át a kiválasztott beállításokat, majd kattintson a elemre `Install` .

    ![A telepítő összegző oldalának képernyőképe](./media/oracle-asm/install12.png)

14. Megjelenik egy figyelmeztető párbeszédpanel, amely tájékoztatja, hogy a konfigurációs parancsfájlokat Kiemelt felhasználóként kell futtatni. Kattintson `Yes` a folytatáshoz.

15. A **Befejezés** lapon kattintson a gombra `Close` a telepítés befejezéséhez.

## <a name="set-up-your-oracle-asm-installation"></a>Az Oracle ASM telepítésének beállítása

Az Oracle ASM telepítésének beállításához hajtsa végre a következő lépéseket:

1. Győződjön meg arról, hogy továbbra is be van jelentkezve **rácsként** az X11-munkamenetből. Előfordulhat, hogy el kell érnie a terminál újrakezdését `enter` . Ezután indítsa el az Oracle automatizált tároló-felügyeleti konfigurációs segédjét:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Megnyílik az Oracle ASM konfigurációs segéd.

2. Az **ASM: lemez csoportok konfigurálása** párbeszédpanelen kattintson a `Create` gombra, majd kattintson a elemre `Show Advanced Options` .

3. A **lemezcsoport létrehozása** párbeszédpanelen:

   - Adja meg a lemez csoportjának **nevét.**
   - A **tag lemezek kiválasztása** területen válassza a **ORCL_DATA** és **ORCL_DATA1** lehetőséget.
   - A **foglalási egység mérete** területen válassza a **4** lehetőséget.
   - Kattintson ide `ok` a lemez csoport létrehozásához.
   - Kattintson ide `ok` a megerősítő ablak bezárásához.

   ![A lemezcsoport létrehozása párbeszédpanel képernyőképe](./media/oracle-asm/asm02.png)

4. Az **ASM: lemez csoportok konfigurálása** párbeszédpanelen kattintson a `Create` gombra, majd kattintson a elemre `Show Advanced Options` .

5. A **lemezcsoport létrehozása** párbeszédpanelen:

   - Adja meg a lemezcsoport neve **FRA** értéket.
   - A **redundancia** területen válassza a **külső (nincs)** lehetőséget.
   - A **tag lemezek kiválasztása** területen válassza a **ORCL_FRA** lehetőséget.
   - A **foglalási egység mérete** területen válassza a **4** lehetőséget.
   - Kattintson ide `ok` a lemez csoport létrehozásához.
   - Kattintson ide `ok` a megerősítő ablak bezárásához.

   ![Képernyőkép a lemez létrehozása párbeszédpanelről, és kiemeli a külső (nincs) beállítást.](./media/oracle-asm/asm04.png)

6. A **Kilépés** elemre kattintva zárja be az ASM konfigurációs Segédet.

   ![Képernyőkép az ASM beállítása: lemez csoportok párbeszédpanel kilépési gombbal](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Az adatbázis létrehozása

Az Oracle Database szoftver már telepítve van az Azure Marketplace-rendszerképben. Adatbázis létrehozásához hajtsa végre a következő lépéseket:

1. Váltson felhasználókat az Oracle-rendszergazdákra, majd inicializálja a figyelőt a naplózáshoz:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   Megnyílik az adatbázis-konfigurációs segéd.

2. Az **adatbázis-művelet** lapon kattintson a elemre `Create Database` .

3. A **létrehozási mód** lapon:

   - Adja meg az adatbázis nevét.
   - A **tárolás típusa** beállításnál győződjön meg arról, hogy az **automatikus tárolási felügyelet (ASM)** van kiválasztva.
   - Az **adatbázisfájlok helyéhez** használja az alapértelmezett ASM javasolt helyet.
   - A **gyors helyreállítási területen** használja az alapértelmezett ASM javasolt helyet.
   - írja be a **rendszergazdai jelszót** , és **erősítse meg a jelszót**.
   - Győződjön meg róla `create as container database` , hogy be van jelölve.
   - írjon be egy `pluggable database name` értéket.

4. Az **Összefoglalás** lapon tekintse át a kiválasztott beállításokat, majd kattintson az `Finish` adatbázis létrehozásához.

   ![Az Összefoglalás lap képernyőképe](./media/oracle-asm/createdb03.png)

5. Az adatbázis létrejött. A **Befejezés** lapon lehetősége van arra, hogy feloldja a további fiókokat az adatbázis használatához és a jelszavak megváltoztatásához. Ha ezt szeretné tenni, válassza a **jelszó-kezelés** lehetőséget – máskülönben kattintson a gombra `close` .

## <a name="delete-the-vm"></a>A virtuális gép törlése

Sikeresen konfigurálta az Oracle automatizált tárolók kezelését a Oracle DB rendszerképre az Azure piactéren.  Ha már nincs szüksége erre a virtuális gépre, a következő paranccsal távolíthatja el az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: Oracle-DataGuard konfigurálása konfigurálása](configure-oracle-dataguard.md)

[Oktatóanyag: Oracle-GoldenGate konfigurálása](Configure-oracle-golden-gate.md)

[Oracle db építész](oracle-design.md) áttekintése
