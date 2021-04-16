---
title: SAP BusinessObjects BI-platform üzembe helyezése Az Azure for Linux | Microsoft Docs
description: SAP BusinessObjects BI-platform üzembe helyezése és konfigurálása Linuxhoz az Azure-ban
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: b16a2d9f779232e59eb883f6a254be22990f5c78
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520020"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>SAP BusinessObjects BI platformtelepítési útmutató Linuxhoz az Azure-on

Ez a cikk az SAP BOBI Platform Linuxhoz az Azure-ban való üzembe helyezésének stratégiáját ismerteti. Ebben a példában két virtuális gép van konfigurálva, prémium SSD Managed Disks a telepítési könyvtára. Azure Database for MySQL CMS-adatbázishoz használatos, és Azure NetApp Files fájladattár-kiszolgálóhoz való fájlmegosztás mindkét kiszolgálón meg van osztva. Az alapértelmezett Tomcat Java-webalkalmazás és a BI Platform-alkalmazás együtt van telepítve mindkét virtuális gépen. A felhasználói kérés terheléselosztása érdekében a Application Gateway TLS/SSL kiszervezési képességekkel rendelkezik.

Ez az architektúratípus kis méretű vagy nem éles környezetben is hatékony. Éles vagy nagy léptékű üzembe helyezés esetén külön gazdagépeket is lehet a webalkalmazáshoz, és több BOBI-alkalmazás gazdagépe is lehet, amelyek lehetővé teszik a kiszolgáló számára a további információk feldolgozását.

![SAP BOBI üzemelő példány az Azure-ban Linuxhoz](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

Ebben a példában a termékverziót és a fájlrendszer elrendezését használjuk

- SAP BusinessObjects Platform 4.3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (Verzió: 8.0.15)
- MySQL C API-összekötő – libmysqlclient (verzió: 6.1.11)

| Fájlrendszer        | Leírás                                                                                                               | Méret (GB)             | Tulajdonos  | Group  | Tárolás                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | Az SAP BOBI-példány, az alapértelmezett Tomcat-webalkalmazás és az adatbázis-illesztőprogramok telepítéséhez szükséges fájlrendszer (ha szükséges) | SAP méretezési irányelvek | bl1adm | sapsys | Felügyelt prémium szintű lemez – SSD |
| /usr/sap/frsinput  | A csatlakoztatási könyvtár az összes BOBI-gazdagép megosztott fájljaihoz használható, amelyek bemeneti fájltárkönyvtárként lesznek használva  | Üzleti szükség         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | A csatlakoztatási könyvtár az összes BOBI-gazdagép megosztott fájljaihoz használható, amelyek kimeneti fájltárkönyvtárként lesznek használva | Üzleti szükség         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Linux rendszerű virtuális gép üzembe helyezése Azure Portal

Ebben a szakaszban két virtuális gépet hozunk létre Linux operációs rendszer (OS) rendszerképével az SAP BOBI Platformhoz. A magas szintű lépések a Virtual Machines a következők:

1. Erőforráscsoport [létrehozása](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. Hozzon létre [egy Virtual Network.](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)

   - Ne használjon egyetlen alhálózatot az ÖSSZES Azure-szolgáltatáshoz az SAP BI platform üzembe helyezéséhez. Az SAP BI platform architektúrája alapján több alhálózatot is létre kell hoznia. Ebben az üzembe helyezésben három alhálózatot hozunk létre: az alkalmazás alhálózatát, a fájltár alhálózatát és Application Gateway alhálózatot.
   - Az Azure-ban Application Gateway és Azure NetApp Files mindig külön alhálózaton kell lennie. További [Azure Application Gateway](../../../application-gateway/configuration-overview.md) [és útmutató a Azure NetApp Files tervezésével kapcsolatos cikkben](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) talál.

3. Rendelkezésre állási csoport létrehozása.

   - A többpéldányos üzembe helyezés egyes rétegei redundanciának eléréséhez helyezze az egyes szintek virtuális gépeit egy rendelkezésre állási csoportba. Győződjön meg arról, hogy az architektúra alapján külön-külön választja el az egyes szintek rendelkezésre állási készletét.

4. Hozza létre az 1. virtuális gépet **(azusbosl1).**

   - Használhat egyéni rendszerképet, vagy kiválaszthat egy rendszerképet a Azure Marketplace. Tekintse meg [a Virtuális gép üzembe](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) helyezése a Azure Marketplace az SAP-hez vagy a Virtuális gép üzembe helyezése [az SAP-hez](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) egyéni rendszerképpel az ön szükségének megfelelően.

5. Hozza létre a 2. virtuális gépet **(azusbosl2).**
6. Adjon hozzá egy prémium SSD lemezt. Sap BOBI telepítési könyvtárként lesz használva.

## <a name="provision-azure-netapp-files"></a>Üzembe Azure NetApp Files

Mielőtt folytatja a telepítést a Azure NetApp Files, ismerkedjen meg az Azure [NetApp Files dokumentációjában.](../../../azure-netapp-files/azure-netapp-files-introduction.md)

Azure NetApp Files azure-régióban érhető [el.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Ellenőrizze, hogy a kiválasztott Azure-régió kínál-e Azure NetApp Files.

A [Azure NetApp Files régiónkénti](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) elérhetőség oldalon ellenőrizheti a régiók Azure NetApp Files rendelkezésre állását.

Az Azure NetApp Files regisztrálása előtt Azure NetApp Files regisztráljon a Azure NetApp Files. [](../../../azure-netapp-files/azure-netapp-files-register.md)

### <a name="deploy-azure-netapp-files-resources"></a>Erőforrások Azure NetApp Files üzembe helyezése

Az alábbi utasítások feltételezik, hogy már üzembe helyezett [azure-beli virtuális hálózatot.](../../../virtual-network/virtual-networks-overview.md) A Azure NetApp Files erőforrásokat és virtuális gépeket, amelyekhez a Azure NetApp Files-erőforrások csatlakoztatva lesznek, ugyanazon az Azure-beli virtuális hálózaton vagy társviszonyban álló Azure-beli virtuális hálózatokon kell üzembe helyezni.

1. Ha még nem telepítette az erőforrásokat, kérje az [Azure NetApp Files.](../../../azure-netapp-files/azure-netapp-files-register.md)

2. Hozzon létre egy NetApp-fiókot a kiválasztott Azure-régióban a NetApp-fiók létrehozása [útmutatását követve.](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)

3. Állítson be egy Azure NetApp Files-kapacitáskészletet a Kapacitáskészlet [beállítása Azure NetApp Files útmutatását követve.](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)

   - A jelen cikkben bemutatott SAP BI platformarchitektúra egyetlen Azure NetApp Files  prémium szolgáltatási szinten használ kapacitáskészletet. Az Azure-beli SAP BI-fájltárkiszolgálókhoz prémium vagy *ultra* szintű Azure NetApp Files  [használatát javasoljuk.](../../../azure-netapp-files/azure-netapp-files-service-levels.md)

4. Delegálhat egy alhálózatot a Azure NetApp Files számára [](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)az Alhálózat delegálásának a következőre: Azure NetApp Files.

5. Telepítse Azure NetApp Files köteteket az [NFS-kötet](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)létrehozása a Azure NetApp Files.

   Az ANF-kötet NFSv3 és NFSv4.1 formátumban is üzembe helyezhető, mivel az SAP BOBI Platform mindkét protokollt támogatja. Telepítse a köteteket a megfelelő Azure NetApp Files alhálózaton. Az Azure NetApp-kötetek IP-címei automatikusan ki vannak osztva.

Ne feledje, hogy a Azure NetApp Files erőforrásoknak és az Azure-beli virtuális gépeknek ugyanabban az Azure-beli virtuális hálózatban vagy társviszonyban álló Azure-beli virtuális hálózatokban kell lennie. Például az azusbobi-frsinput, azusbobi-frsoutput a kötetek neve és nfs://10.31.2.4/azusbobi-frsinput, nfs://10.31.2.4/azusbobi-frsoutput a Azure NetApp Files elérési útjai.

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Fontos szempontok

Az SAP BOBI Azure NetApp Files-fájltárkiszolgálóhoz való alkalmazás létrehozása során vegye figyelembe a következő szempontokat:

1. A minimális kapacitáskészlet 4 tebibájt (TiB).
2. A kötet minimális mérete 100 gibibájt (GiB).
3. Azure NetApp Files virtuális gépeknek és az összes olyan virtuális gépnek, Azure NetApp Files köteteket ugyanannak az Azure-beli virtuális hálózatnak vagy társviszonyban lévő virtuális hálózatoknak kell ugyanabban a régióban lennie. [](../../../virtual-network/virtual-network-peering-overview.md) Azure NetApp Files régióban található virtuális hálózatok közötti társviszony-létesítésen keresztüli hozzáférés mostantól támogatott. Az Azure NetApp globális társviszony-létesítésen keresztüli elérése még nem támogatott.
4. A kiválasztott virtuális hálózatnak olyan alhálózattal kell lennie, amely delegálva van a Azure NetApp Files.
5. A Azure NetApp Files [házirendben](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)szabályozhatja az engedélyezett ügyfeleket, a hozzáférés típusát (írási-olvasási, írási és egyéb) stb.
6. Az Azure NetApp Files funkció még nem zónatűnnek számít. A funkció jelenleg nem minden rendelkezésre állási zónában van telepítve egy Azure-régióban. Néhány Azure-régióban vegye figyelembe a lehetséges késési következményeket.
7. Azure NetApp Files kötetek NFSv3- vagy NFSv4.1-kötetként helyezhetők üzembe. Az SAP BI platformalkalmazások mindkét protokollt támogatják.

## <a name="configure-file-systems-on-linux-servers"></a>Fájlrendszerek konfigurálása Linux-kiszolgálókon

A szakasz lépései a következő előtagokat használják:

**[A]**: A lépés az összes gazdagépre vonatkozik

### <a name="format-and-mount-sap-file-system"></a>SAP fájlrendszer formázása és csatlakoztatása

1. **[A]** List all attached disk

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A] Blokkeszköz** formázása /usr/sap számára

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** Csatlakoztatási könyvtár létrehozása

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** Blokkeszköz UUID-nek lekért része

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** A fájlrendszer csatlakoztatási bejegyzésének karbantartása az /etc/fstab fájlban

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A] Fájlrendszer** csatlakoztatása

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>Kötet Azure NetApp Files csatlakoztatása

1. **[A]** Csatlakoztatási könyvtárak létrehozása

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** Az ügyfél operációs rendszerének konfigurálása az NFSv4.1 csatlakoztatásának támogatásához **(csak NFSv4.1 használata esetén alkalmazható)**

   Ha NFSv4.1 protokollal használ Azure NetApp Files-köteteket, hajtsa végre a következő konfigurációt az összes virtuális gépen, ahol csatlakoztatni kell Azure NetApp Files NFSv4.1-köteteket.

   **NFS-tartománybeállítások ellenőrzése**

   Győződjön meg arról, hogy a tartomány alapértelmezett Azure NetApp Files, a  defaultv4iddomain.com és a leképezés senkire van **beállítva.**

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > Győződjön meg arról, hogy az NFS-tartományt az /etc/idmapd.conf fájlban a virtuális gépen úgy állítsa be, hogy megfeleljen az alapértelmezett tartománykonfigurációnak a Azure NetApp Files: **defaultv4iddomain.com.** Ha eltérés van az NFS-ügyfélen (például a virtuális gépen) és az NFS-kiszolgálón (például az Azure NetApp konfigurációjában) lévő tartománykonfiguráció között, akkor a virtuális gépekhez csatlakoztatott Azure NetApp-kötetek fájljaira vonatkozó engedélyek senkiként jelennek meg.

   Ellenőrizze a `nfs4_disable_idmapping` következőt: . A beállításnak **Y-nak kell lennie.** A könyvtárstruktúra létrehozásához, `nfs4_disable_idmapping` ahol a található, hajtsa végre a csatlakoztatási parancsot. A /sys/modules mappában nem hozhatja létre manuálisan a könyvtárat, mert a hozzáférés a kernel/illesztőprogramok számára van fenntartva.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** Csatlakoztatási bejegyzések hozzáadása

   NFSv3 használata esetén

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   NFSv4.1 használata esetén

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** NFS-kötetek csatlakoztatása

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>CMS-adatbázis konfigurálása – Azure Database for MySQL

Ez a szakasz részletesen bemutatja, hogyan lehet Azure Database for MySQL üzembe Azure Portal. Emellett útmutatást nyújt a CMS- és auditadatbázisok létrehozásához az SAP BOBI Platformhoz és egy felhasználói fiókhoz az adatbázis eléréséhez.

Az irányelvek csak akkor alkalmazandók, ha a Azure DB for MySQL. Egyéb adatbázisokhoz az SAP- vagy adatbázis-specifikus dokumentációban talál útmutatást.

### <a name="create-an-azure-database-for-mysql"></a>Azure-adatbázis létrehozása MySQL-hez

Jelentkezzen be a Azure Portal, és kövesse a rövid útmutatóban említett lépéseket a [Azure Database for MySQL.](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md) Néhány fontos megjegyzés a kiépítési Azure Database for MySQL –

1. Válassza ki ugyanazt a régiót Azure Database for MySQL ahol az SAP BI Platform alkalmazáskiszolgálói futnak.

2. Válasszon egy támogatott adatbázisverziót a termék rendelkezésre állási [mátrixa (PAM)](https://support.sap.com/pam) alapján az SAP BI SAP BOBI-verziójához. Kövesse a MySQL AB SAP PAM-ban való kezelésével kapcsolatos kompatibilitási irányelveket

3. A "compute+storage" (számítás+tárolás) mezőben válassza a **Configure server** (Kiszolgáló konfigurálása) lehetőséget, és válassza ki a megfelelő tarifacsomagot a méretezési kimenet alapján.

4. **Az automatikus tárterület-növekedés** alapértelmezés szerint engedélyezve van. Ne feledje, hogy a [Storage](../../../mysql/concepts-pricing-tiers.md#storage) csak skálázható felfelé, lefelé nem.

5. Alapértelmezés szerint a **biztonsági megőrzési** időtartam [](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) hét nap, de igény szerint akár 35 napig is konfigurálható.

6. A Azure Database for MySQL biztonsági másolatai alapértelmezés szerint helyileg redundánsak, ezért ha georedundáns tárolóban szeretné biztonsági másolatokat készíteni a kiszolgálókról, válassza a Georedundáns lehetőséget a Biztonsági mentés redundanciabeállításai **területen.** 

> [!NOTE]
> A biztonsági [mentési redundancia beállításainak](../../../mysql/concepts-backup.md#backup-redundancy-options) módosítása a kiszolgáló létrehozása után nem támogatott.

### <a name="configure-connection-security"></a>A kapcsolatbiztonság konfigurálása

Alapértelmezés szerint a létrehozott kiszolgáló tűzfallal védett, és nem érhető el nyilvánosan. Ahhoz, hogy hozzáférést biztosítson ahhoz a virtuális hálózathoz, ahol az SAP BI Platform alkalmazáskiszolgálói futnak, kövesse az alábbi lépéseket:  

1. A kiszolgálói erőforráscsoportban Azure Portal válassza  a Kapcsolatbiztonság lehetőséget a kiszolgálói erőforrás bal oldali menüjében.
2. Válassza **az Igen lehetőséget** az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése beállításhoz.**
3. A VNET-szabályok alatt válassza **a Meglévő virtuális hálózat hozzáadása lehetőséget.** Válassza ki az SAP BI Platform alkalmazáskiszolgáló virtuális hálózatát és alhálózatát. Emellett hozzáférést kell adnia a Jump boxhoz vagy más kiszolgálókhoz, amelyekről csatlakoztathatja a [MySQL Workbench](../../../mysql/connect-workbench.md) szolgáltatást a Azure Database for MySQL. A Rendszer a MySQL Workbench segítségével fogja létrehozni a CMS- és auditadatbázist
4. A virtuális hálózatok hozzáadása után válassza a **Mentés lehetőséget.**

### <a name="create-cms-and-audit-database"></a>CMS és auditadatbázis létrehozása

1. Töltse le és telepítse a MySQL Workbench alkalmazást a [MySQL webhelyéről.](https://dev.mysql.com/downloads/workbench/) Győződjön meg arról, hogy a MySQL Workbench alkalmazást azon a kiszolgálón telepíti, amely hozzáfér Azure Database for MySQL.

2. Csatlakozzon a kiszolgálóhoz a MySQL Workbench használatával. Kövesse a cikkben említett [utasításokat.](../../../mysql/connect-workbench.md#get-connection-information) Ha a kapcsolat tesztelése sikeres, a következő üzenet jelenik meg:

   ![SQL Workbench-kapcsolat](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. Az SQL-lekérdezés lapon futtassa az alábbi lekérdezést a CMS- és auditadatbázis sémája létrehozásához.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. Felhasználói fiók létrehozása a sémához való csatlakozáshoz

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. A MySQL-felhasználói fiók jogosultságának és szerepkörének ellenőrzése

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>A MySQL C API-összekötő (libmysqlclient) telepítése Linux-kiszolgálón

Ahhoz, hogy az SAP BOBI-alkalmazáskiszolgáló hozzáférjen az adatbázishoz, adatbázis-ügyfélre/-illesztőprogramra van szükség. A Linuxhoz használható MySQL C API-összekötőt a CMS- és auditadatbázisok eléréséhez kell használni. A CMS-adatbázissal létesítendő ODBC-kapcsolat nem támogatott. Ez a szakasz a MySQL C API-összekötő Linux rendszeren való beállításához nyújt útmutatást.

1. Tekintse meg a Azure Database for MySQL kompatibilis [MySQL-illesztőprogramokat](../../../mysql/concepts-compatibility.md) és felügyeleti eszközöket, amely leírja, hogy mely illesztőprogramok kompatibilisek Azure Database for MySQL. Ellenőrizze a **mySQL Connector/C (libmysqlclient)** illesztőprogramot a cikkben.

2. Az illesztőprogramok [letöltéséhez tekintse](https://downloads.mysql.com/archives/c-c/) meg ezt a hivatkozást.

3. Válassza ki az operációs rendszert, és töltse le a MySQL Connector megosztott összetevő rpm csomagját. Ebben a példában a mysql-connector-c-shared-6.1.11-összekötő verzióját használjuk.

4. Telepítse az összekötőt az ÖSSZES SAP BOBI-alkalmazáspéldányban.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Ellenőrizze a libmysqlclient.so

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Állítsa LD_LIBRARY_PATH, hogy a telepítéshez használt felhasználói fiók `/usr/lib64` címtára legyen.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Kiszolgáló előkészítése

A szakasz lépései a következő előtagokat használják:

**[A]**: A lépés az összes gazdagépre vonatkozik.

1. **[A]** A Linux (SLES vagy RHEL) ízének alapján be kell állítania a kernelparamétereket, és telepítenie kell a szükséges kódtárakat. Tekintse meg **az Üzletiintelligencia-platform** [telepítési útmutatója Unix rendszerhez című szakasz Rendszerkövetelmények című szakaszát.](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US)

2. **[A]** Ellenőrizze, hogy a gép időzónája megfelelően van-e beállítva. Tekintse meg a Telepítési útmutató További [Unix-](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) és Linux-követelmények című szakaszát.

3. **[A] Hozzon** létre egy felhasználói fiókot **(bl1** adm) és egy csoportot (sapsys), amelyben a szoftver háttérfolyamatai futnak. Ezzel a fiókkal hajtsa végre a telepítést, és futtassa a szoftvert. A fióknak nincs szüksége gyökér szintű jogosultságra.

4. **[A]** Állítsa be a felhasználói fiók **(bl1** adm) környezetét egy támogatott UTF-8 területi beállítás használatára, és győződjön meg arról, hogy a konzolszoftver támogatja az UTF-8 karakterkészleteket. Annak biztosításához, hogy az operációs rendszer a megfelelő területi beállítását használja, állítsa a LC_ALL és LANG környezeti változókat az Ön által előnyben részesített területi beállításra a **(bl1** adm) felhasználói környezetben.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A] Felhasználói** fiók konfigurálása (**bl1** adm).

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. Töltse le és bontsa ki az SAP BusinessObjects BI Platform médiatartalmait az SAP szolgáltatás piacterén.

## <a name="installation"></a>Telepítés

Ellenőrizze a kiszolgálón a **bl1** adm felhasználói fiók területi nevét

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Navigáljon az SAP BusinessObjects BI Platform adathordozóira, és futtassa az alábbi parancsot a **bl1** adm-felhasználóval –

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Kövesse a [UNIX-hoz](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) való SAP BOBI platform telepítési útmutatóját, amely a saját verziójára vonatkozik. Néhány fontos megjegyzés az SAP BOBI Platform telepítésekor.

- A **Termékregisztráció konfigurálása képernyőn** használhatja az SAP BusinessObjects megoldások ideiglenes licenckulcsát az SAP Note [1288121-es](https://launchpad.support.sap.com/#/notes/1288121) megjegyzésből, vagy létrehozhat licenckulcsot az SAP Service Marketplace-en

- A **Telepítés típusának**  kiválasztása képernyőn válassza a Teljes telepítés az első kiszolgálón (azusbosl1) lehetőséget, más kiszolgálóhoz (azusbosl2) válassza az Egyéni **/ Kibontás** lehetőséget, amely kibővíti a meglévő BOBI-beállítást.

- A **Select Default or Existing Database (Alapértelmezett vagy meglévő adatbázis kiválasztása) képernyőn** válassza a Configure an existing **database**(Meglévő adatbázis konfigurálása) lehetőséget, amely kérni fogja a CMS és a Naplózási adatbázis kiválasztását. A **CMS-adatbázis típusa** és az Audit Database type (Adatbázistípus naplózása) beállításnál válassza a MySQL lehetőséget.

  A Nincs naplózási adatbázis lehetőséget is választhatja, ha a telepítés során nem szeretné konfigurálni a naplózást.

- Válassza ki a megfelelő beállításokat **a Select Java Web Application Server (Java-webalkalmazás-kiszolgáló kiválasztása) képernyőn** az SAP BOBI-architektúra alapján. Ebben a példában az 1. lehetőséget választottuk, amely telepíti a tomcat-kiszolgálót ugyanazon AZ SAP BOBI Platformon.

- Adja meg a CMS-adatbázis adatait **a CONFIGURE CMS Repository Database - MySQL (CMS-adattár adatbázisának konfigurálása – MySQL) oldalon.** Példa bemenet a CMS-adatbázis adataihoz Linux-telepítéshez. Azure Database for MySQL az alapértelmezett 3306-os porton van használva
  
  ![SAP BOBI Üzembe helyezés Linux rendszeren – CMS-adatbázis](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- (Nem kötelező) Adja meg az Audit database information (Adatbázis naplózása) adatokat a **Configure Audit Repository Database - MySQL (Naplóadattár-adatbázis konfigurálása – MySQL) oldalon.** Példabemenet a Linux-telepítés adatbázis-információinak naplózásához.

  ![SAP BOBI Üzembe helyezés Linux rendszeren – Audit Database](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Kövesse az utasításokat, és adja meg a telepítés befejezéséhez szükséges bemeneteket.

Többpéldányos üzembe helyezés esetén futtassa a telepítést a második gazdagépen (azusbosl2). A **Telepítés típusának kiválasztása képernyőn** válassza az Egyéni **/Kibontás** lehetőséget, amely kibontja a meglévő BOBI-beállítást.

Az Azure Database for MySQL ajánlatban egy átjáró irányítja át a kapcsolatokat a kiszolgálópéldányokhoz. A kapcsolatok létrejötte után a MySQL-ügyfél az átjárón beállított MySQL-verziót jeleníti meg, nem a MySQL-kiszolgálópéldányon futó tényleges verziót. A MySQL-kiszolgálópéldány verziójának meghatározásához használja a `SELECT VERSION();` parancsot a MySQL parancssorában. Így a központi felügyeleti konzolon (CMC) különböző adatbázisverziókat talál, amelyek alapvetően az átjárón beállított verziók. További [részletekért tekintse Azure Database for MySQL támogatott kiszolgálóverziókat.](../../../mysql/concepts-supported-versions.md)

![SAP BOBI üzembe helyezése Linux rendszeren – CMC-beállítások](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Telepítés után

### <a name="tomcat-clustering---session-replication"></a>Tomcat-fürtözés – munkamenet-replikáció

A Tomcat két vagy több alkalmazáskiszolgáló fürtözését támogatja a munkamenet-replikációhoz és a feladatátvételhez. Az SAP BOBI platform-munkamenetek szerializálva vannak, egy felhasználói munkamenet zökkenőmentesen átveheti a feladatátvételt a tomcat egy másik példányára, még akkor is, ha egy alkalmazáskiszolgáló meghibásodik.

Ha például egy felhasználó egy olyan webkiszolgálóhoz csatlakozik, amely meghibásodik, miközben a felhasználó egy mappahierarchiában navigál az SAP BI-alkalmazásban. Megfelelően konfigurált fürt esetén a felhasználó továbbra is navigálhat a mappahierarchiában anélkül, hogy a rendszer átirányítja a bejelentkezési oldalra.

Az SAP Note [2808640-ben](https://launchpad.support.sap.com/#/notes/2808640)a Tomcat-fürtszolgáltatás konfigurálásának lépései csoportos küldéssel biztosítanak. Az Azure-ban azonban a csoportos küldés nem támogatott. Ahhoz, hogy a Tomcat-fürt működjön az Azure-ban, a [StaticMembershipInterceptort](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) kell használnia (SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907)). Ellenőrizze a [Tomcat Clustering using Static Membership for SAP BusinessObjects BI Platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) on SAP blog to set up tomcat cluster in Azure (Tomcat-fürtök statikus tagság használata az SAP BusinessObjects BI Platformhoz) blogban a tomcat-fürtök Azure-ban való beállítását.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Az SAP BI platform webes terheléselosztási rétege

Az SAP BOBI többpéldányos üzembe helyezése során a Java-webalkalmazás-kiszolgálók (webes szint) két vagy több gazdagépen futnak. A felhasználói terhelést egyenletesen eloszthatja a webkiszolgálók között, ha terheléselosztást használ a végfelhasználók és a webkiszolgálók között. Az Azure-ban használhatja a Azure Load Balancer vagy Azure Application Gateway webalkalmazás-kiszolgálók forgalmának kezeléséhez. Az egyes ajánlatok részleteit a következő szakaszban ismertetjük.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure Load Balancer (hálózatalapú terheléselosztás)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) egy nagy teljesítményű, alacsony késésű, 4. rétegbeli (TCP, UDP) terheléselosztási rendszer, amely elosztja a forgalmat a kifogástalan Virtual Machines. A terheléselosztási állapotfigyelő mintavétel az egyes virtuális gépek adott portját figyeli, és csak egy működő virtuális gép(ök)nek osztja el a forgalmat. Választhat nyilvános vagy belső terheléselosztást attól függően, hogy az SAP BI platformot elérhetővé szeretné-e tenni az internetről vagy sem. Zónaredundáns, így magas rendelkezésre állást biztosít a Availability Zones.

Tekintse meg az alábbi ábrán Load Balancer Belső port szakaszt, ahol a webalkalmazás-kiszolgáló a 8080-as, alapértelmezett Tomcat HTTP-porton fut, amelyet az állapot-mintavétel fog figyelni. Így a végfelhasználóktól érkező bejövő kérések a háttérkészlet webalkalmazás-kiszolgálóira (azusbosl1 vagy azusbosl2) lesznek átirányítva. A Load Balancer nem támogatja a TLS/SSL-megszakítást (más néven TLS/SSL-kiszervezést). Ha az Azure Load Balancert használja a forgalom webkiszolgálók közötti elosztására, javasoljuk, hogy használja a standard Load Balancer.

> [!NOTE]
> Ha a nyilvános IP-címmel nem rendelkezik virtuális gépek a standard Azure Load Balancer belső (nyilvános IP-cím nélküli) háttérkészletében vannak elhelyezve, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurációt végez a nyilvános végpontok felé irányuló útválasztás érdekében. A kimenő kapcsolatok elérésével kapcsolatos részletekért lásd: Nyilvános végponti kapcsolat Virtual Machines Azure standard Load Balancer sap magas rendelkezésre állású [forgatókönyvekben.](high-availability-guide-standard-load-balancer-outbound-connections.md)

![Azure Load Balancer webkiszolgálók közötti forgalom kiegyensúlyozás érdekében](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure Application Gateway (webalkalmazás terheléselosztása)

[Azure Application Gateway (AGW)](../../../application-gateway/overview.md) szolgáltatásként biztosítja az Application Delivery Controllert (ADC), amellyel az alkalmazások egy vagy több webalkalmazás-kiszolgálóra irányítják a felhasználói forgalmat. Számos 7. rétegi terheléselosztási képességet kínál, például tLS/SSL-kiszervezést, Web Application Firewall (WAF), cookie-alapú munkamenet-affinitást és egyebeket az alkalmazásokhoz.

Az SAP BI Platformon az Application Gateway az alkalmazás webes forgalmát egy háttérkészlet megadott erőforrásaihoz irányítja – azusbosl1 vagy azusbos2. Hozzárendelhet egy figyelőt a porthoz, szabályokat hozhat létre, és erőforrásokat adhat a háttérkészlethez. Az alábbi ábrán az application gateway privát előtere IP-címmel (10.31.3.20) szolgál belépési pontként a felhasználók számára, kezeli a bejövő TLS/SSL-kapcsolatokat (HTTPS - TCP/443), visszafejti a TLS/SSL-t, és a titkosítatlan kérést (HTTP - TCP/8080) továbbküldi a háttérkészlet kiszolgálóira. A beépített TLS/SSL-leépítési funkcióval csak egy TLS/SSL-tanúsítványt kell fenntartanunk az alkalmazásátjárón, ami leegyszerűsíti a műveleteket.

![Application Gateway webkiszolgálók közötti forgalom kiegyensúlyozás érdekében](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

A Application Gateway SAP BOBI-webkiszolgálóhoz való konfigurálásról az [SAP BOBI-webkiszolgálók](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) terheléselosztása Azure Application Gateway SAP blogon talál.

> [!NOTE]
> Javasoljuk, hogy használja az Azure Application Gateway-t a webkiszolgáló forgalmának terheléselosztására, mivel olyan funkciókat biztosít, mint az SSL-kiszervezés, az SSL-kezelés központosítása a kiszolgálón a titkosítási és visszafejtési terhelés csökkentése érdekében, Round-Robin forgalomelosztási algoritmus, Web Application Firewall (WAF) képességek, magas rendelkezésre állás stb.

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>SAP BusinessObjects BI Platform – biztonsági mentés és visszaállítás

A biztonsági mentés és a visszaállítás egy olyan folyamat, amely során rendszeres időközönként másolatokat hozunk létre az adatokról és az alkalmazásokról egy másik helyre. Így visszaállítható vagy visszaállítható az előző állapotba, ha az eredeti adatok vagy alkalmazások elvesznek vagy sérültek. Emellett minden üzleti vészhelyreállítási stratégia nélkülözhetetlen összetevője.

Ha átfogó biztonsági mentési és visszaállítási stratégiát kell kidolgoznia az SAP BOBI Platformhoz, azonosítsa azokat az összetevőket, amelyek a rendszer állásidejéhez vagy az alkalmazás leállásához vezetnek. Az SAP BOBI Platformon a következő összetevők biztonsági mentése létfontosságú az alkalmazás védelméhez.

- SAP BOBI telepítési könyvtár (Managed Premium Disks)
- Fájltárkiszolgáló (Azure NetApp Files Azure Premium Files)
- CMS-adatbázis (Azure Database for MySQL azure-beli virtuális gépen található adatbázis)

A következő szakasz bemutatja, hogyan implementálja a biztonsági mentési és visszaállítási stratégiát az egyes összetevőkre az SAP BOBI Platformon.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Biztonsági mentési & SAP BOBI telepítési könyvtárának visszaállítása

Az Azure-ban az alkalmazáskiszolgálók és az összes csatlakoztatott lemez biztonságimentének legegyszerűbb módja a [Azure Backup](../../../backup/backup-overview.md) használata. Független és elkülönített biztonsági másolatokat biztosít a virtuális gépeken az adatok nem szándékolt megsemmisítése ellen. A biztonsági másolatok egy helyreállítási tárban vannak tárolva, a helyreállítási pontok beépített kezelésével. A konfiguráció és a skálázás egyszerű, a biztonsági másolatok optimalizálva vannak, és szükség esetén egyszerűen visszaállíthatók.

A biztonsági mentési folyamat részeként a rendszer pillanatképet készít, és az adatokat az éles számítási feladatok befolyásolása nélkül továbbítja a helyreállítási tárba. A pillanatkép eltérő konzisztenciaszintet biztosít a Pillanatkép-konzisztencia [cikkben](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) leírtak szerint. Dönthet úgy is, hogy a virtuális gép adatlemezei részkészletének biztonsági mentését szelektív lemezes biztonsági mentési és visszaállítási funkcióval készíti el. További információ: [Azure-beli virtuális](../../../backup/backup-azure-vms-introduction.md) gépek biztonsági mentése dokumentum és gyakori [kérdések – Azure-beli virtuális gépek biztonsági mentése.](../../../backup/backup-azure-vm-backup-faq.yml)

#### <a name="backup--restore-for-file-repository-server"></a>Biztonsági & biztonsági mentése a fájltár-kiszolgálóhoz

A **Azure NetApp Files** létrehozhat igény szerinti pillanatképeket, és ütemezheti az automatikus pillanatképeket pillanatkép-szabályzatok használatával. A pillanatkép-másolatok az ANF-kötet adott időpontban készült másolatát biztosítják. További információ: [Pillanatképek kezelése a Azure NetApp Files.](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)

**Azure Files** biztonsági mentés integrálva van a natív [Azure Backup](../../../backup/backup-overview.md) szolgáltatással, amely központosítja a biztonsági mentési és visszaállítási funkciót, valamint a virtuális gépek biztonsági mentését és leegyszerűsíti a műveleteket. További információ: [Azure-fájlmegosztások](../../../backup/azure-file-share-backup-overview.md) biztonsági mentése és gyakori kérdések – Biztonsági [másolat Azure Files.](../../../backup/backup-azure-files-faq.yml)

#### <a name="backup--restore-for-cms-database"></a>Biztonsági & CMS-adatbázis visszaállítása

Az Azure Database of MySQL az Azure DBaaS ajánlata, amely automatikusan létrehozza a kiszolgálók biztonsági másolatát, és a felhasználó által konfigurált helyileg redundáns vagy georedundáns tárolóban tárolja őket. Az Azure Database of MySQL biztonsági másolatokat készít az adatfájlokról és a tranzakciónaplóról. A támogatott maximális tárterületmérettől függően teljes és különbségi biztonsági mentést (legfeljebb 4 TB tárkiszolgáló) vagy pillanatkép-biztonsági mentést (legfeljebb 16 TB tárkiszolgálót) vesz igénybe. Ezekkel a biztonsági másolatokkal bármikor visszaállíthatja a kiszolgálókat a beállított megőrzési időszakon belül. A biztonsági másolatok alapértelmezett megőrzési ideje hét nap, amelyet akár [három](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) napig is konfigurálhat. Minden biztonsági mentés AES 256 bites titkosítással van titkosítva.

Ezek a biztonsági mentési fájlok nem exportáltak a felhasználók számára, és nem exportálhatók. Ezek a biztonsági másolatok csak visszaállítási műveletekhez használhatók a Azure Database for MySQL. Az adatbázis másolásához használhatja a [mysqldumpot.](../../../mysql/concepts-migrate-dump-restore.md) További információ: Biztonsági [mentés és visszaállítás a Azure Database for MySQL.](../../../mysql/concepts-backup.md)

A Virtual Machines telepített adatbázisokhoz használhatja a szabványos biztonsági mentési eszközöket [vagy](../../../backup/sap-hana-db-about.md) Azure Backup HANA-adatbázishoz. Ha az Azure-szolgáltatások és -eszközök nem felelnie az Ön követelményeinek, más biztonsági mentési eszközöket vagy szkripteket is használhat a lemezek biztonsági másolatának létrehozásához.

## <a name="sap-businessobjects-bi-platform-reliability"></a>SAP BusinessObjects BI-platform megbízhatósága

Az SAP BusinessObjects BI Platform különböző szinteket tartalmaz, amelyek adott feladatokhoz és műveletekhez vannak optimalizálva. Ha egy adott szint valamelyik összetevője elérhetetlenné válik, az SAP BOBI-alkalmazás elérhetetlenné válik, vagy az alkalmazás bizonyos funkciói nem fognak működni. Ezért meg kell győződni arról, hogy minden szint úgy van kialakítva, hogy megbízható legyen, hogy az alkalmazás üzleti fennakadások nélkül is működőképes maradjon.

Ez a szakasz az SAP BOBI Platform következő beállításaival foglalkozik–

- **Magas rendelkezésre állás:** Egy magas rendelkezésre álló platform legalább kettővel rendelkezik az Azure-régión belül ahhoz, hogy az alkalmazás működőképes maradjon, ha az egyik kiszolgáló elérhetetlenné válik.
- **Vészhelyreállítás:** Ez egy olyan folyamat, amely visszaállítja az alkalmazás funkcióit, ha katasztrofális veszteség történik, például ha egy természeti katasztrófa miatt a teljes Azure-régió elérhetetlenné válik.

A megoldás implementációja az Azure-beli rendszerbeállítás jellegétől függően változik. Az ügyfélnek tehát az üzleti igényei alapján kell testre szabni a magas rendelkezésre állási és vészhelyreállítási megoldást.

### <a name="high-availability"></a>Magas rendelkezésre állás

A magas rendelkezésre állás olyan technológiákra utal, amelyek minimálisra csökkenthetik az iteráció megszakadását azáltal, hogy redundáns, hibatűrő vagy feladatátvétel által védett összetevőkkel biztosítják az alkalmazások/szolgáltatások üzletmenet-folytonosságát ugyanazon az adatközponton belül. Esetünkben az adatközpontok egy Azure-régióban találhatók. Az SAP [magas](sap-high-availability-architecture-scenarios.md) rendelkezésre állású architektúrája és forgatókönyvei című cikk kezdeti betekintést nyújt az Azure-ban az SAP-alkalmazásokhoz kínált különböző magas rendelkezésre állási technikákba és javaslatokba, amelyek kiegészítik az ebben a szakaszban található utasításokat.

Az SAP BOBI Platform méretezési eredménye alapján meg kell tervezni a környezetet, és meg kell határozni a BI-összetevők eloszlását az Azure-Virtual Machines alhálózatok között. Az elosztott architektúrában a redundancia szintje az üzlet által megkövetelt helyreállítási időre vonatkozó célkitűzéstől (RTO) és a helyreállítási időkorrektúrától (RPO) függ. Az SAP BOBI Platform minden szinten különböző rétegeket és összetevőket tartalmaz a redundancia eléréséhez. Így ha az egyik összetevő meghibásodik, az SAP BOBI-alkalmazás kismemivel vagy zavartalanul működik. Példa:

- Redundáns alkalmazáskiszolgálók, például BI-alkalmazáskiszolgálók és webkiszolgálók
- Egyedi összetevők, például a CMS-adatbázis, a fájladattár-kiszolgáló és Load Balancer

A következő szakasz azt ismerteti, hogyan lehet magas rendelkezésre állást elérni az SAP BOBI Platform minden összetevőjére.

#### <a name="high-availability-for-application-servers"></a>Magas rendelkezésre állás az alkalmazáskiszolgálók számára

A BI- és webalkalmazás-kiszolgálók esetében , függetlenül attól, hogy külön vagy együtt vannak-e telepítve, nincs szükség egy adott magas rendelkezésre állású megoldásra. Redundancia használatával magas rendelkezésre állást érhet el, azaz több BI- és webkiszolgáló-példányt konfigurálhat a különböző Azure-Virtual Machines.

Egy vagy több esemény miatti állásidő hatásának csökkentése érdekében ajánlott az alábbi magas rendelkezésre állási gyakorlatot követni a több virtuális gépen futó alkalmazáskiszolgálóknál.

- A Availability Zones az adatközpontok meghibásodásának védelmére.
- Konfiguráljon több Virtual Machines rendelkezésre állási készletben a redundancia biztosításához.
- Használja Managed Disks rendelkezésre állási készletben a virtuális gépekhez való Managed Disks.
- Konfigurálja az egyes alkalmazásrétegeket különálló rendelkezésre állási készletekre.

További információ: Linux rendszerű virtuális gépek [rendelkezésre állásának kezelése](../../availability.md)

#### <a name="high-availability-for-cms-database"></a>Magas rendelkezésre állás CMS-adatbázishoz

Ha az Azure Database as a Service (DBaaS) szolgáltatást használja a CMS-adatbázishoz, a magas rendelkezésre állási keretrendszer alapértelmezés szerint elérhető. Csak ki kell választania a régiót és a szolgáltatást, amely eredendően magas rendelkezésre állási, redundancia- és rugalmassági képességeket biztosít anélkül, hogy további összetevőket kellene konfigurálnia. Az Azure-beli támogatott DBaaS-ajánlatok [SLA-jával](../../../mysql/concepts-high-availability.md) kapcsolatos további [](../../../azure-sql/database/high-availability-sla.md) részletekért tekintse meg a magas rendelkezésre állást a Azure Database for MySQL és a magas rendelkezésre állás a Azure SQL Database

A CMS-adatbázis egyéb DBMS üzembe helyezésével kapcsolatban tekintse meg az SAP számítási feladatok [DBMS](dbms_guide_general.md)üzembe helyezési útmutatóit, amelyek a különböző DBMS-környezetekkel és a magas rendelkezésre állás elérésének megközelítésével kapcsolatos információkat tartalmaznak.

#### <a name="high-availability-for-file-repository-server"></a>Magas rendelkezésre állás a fájltárkiszolgálóhoz

A fájltár-kiszolgáló (FRS) azokra a lemez-könyvtárakra vonatkozik, ahol a tartalmak (például a jelentések, az universes és a kapcsolatok) tárolva vannak. Ez a rendszer összes alkalmazáskiszolgálója között meg van osztva. Ezért fontos, hogy magas rendelkezésre áll-e.

Az Azure-ban választhat az [Azure Premium Files](../../../storage/files/storage-files-introduction.md) vagy a [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) a magas rendelkezésre állékony és tartós jellegű fájlmegosztáshoz. További információ: [Redundancy (Redundancia)](../../../storage/files/storage-files-planning.md#redundancy) című Azure Files.

> [!NOTE]
> Az SMB protokoll Azure Files általánosan elérhető, de az NFS protokoll támogatása a Azure Files jelenleg előzetes verzióban érhető el. További információ: [Az NFS 4.1](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/) támogatása a Azure Files előzetes verzióban érhető el.

Mivel ez a fájlmegosztási szolgáltatás nem érhető el [](https://azure.microsoft.com/en-us/global-infrastructure/services/) minden régióban, a naprakész információkért tekintse meg a régiónként elérhető termékeket. Ha a szolgáltatás nem érhető el az Ön régiójában, létrehozhat egy NFS-kiszolgálót, amelyből megoszthatja a fájlrendszert az SAP BOBI-alkalmazással. Ugyanakkor figyelembe kell vennie a magas rendelkezésre állását is.

#### <a name="high-availability-for-load-balancer"></a>Magas rendelkezésre állás a terheléselosztáshoz

A forgalom webkiszolgálók közötti elosztásához használhatja a Azure Load Balancer vagy Azure Application Gateway. A terheléselosztás bármelyikének redundancija az üzembe helyezéshez választott termékváltozat alapján érhető el.

- A Azure Load Balancer a redundancia a zónaredundáns standard Load Balancer konfigurálásával érhető el. További információ: standard Load Balancer [és Availability Zones](../../../load-balancer/load-balancer-standard-availability-zones.md)
- A Application Gateway magas rendelkezésre állás az üzembe helyezés során kiválasztott szint típusától függően érhető el.
  - A v1 termékváltozat támogatja a magas rendelkezésre állású forgatókönyveket, ha kettő vagy több példányt helyezett üzembe. Az Azure elosztja ezeket a példányokat a frissítési és tartalék tartományok között, hogy a példányok ne hibásodnak meg egyszerre. Így ezzel a termékváltozatkal a redundancia a zónán belül érhető el
  - A v2 termékváltozat automatikusan biztosítja, hogy az új példányok elterjednek a tartalék és frissítési tartományok között. Ha a zónaredundaniát választja, a legújabb példányok a rendelkezésre állási zónák között is el vannak ásva, így zóna szintű hibatűrést kínálnak. További részletekért lásd: Automatikus skálázás és [zónaredundáns Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Az SAP BusinessObjects BI-platform magas rendelkezésre állású architektúrája – referencia

Az alábbi referenciaarchitektúra az SAP BOBI Platform rendelkezésre állási készlettel való beállítását ismerteti, amely biztosítja a virtuális gépek redundanciát és rendelkezésre állását a zónán belül. Az architektúra különböző Azure-szolgáltatások, például az Azure Application Gateway, az Azure NetApp Files és az Azure Database for MySQL használatát mutatja be az SAP BOBI Platformhoz, amely beépített redundanciát biztosít, ami csökkenti a különböző magas rendelkezésre állású megoldások kezelésének összetettségét.

Az alábbi ábrán a bejövő forgalom (HTTPS - TCP/443) terheléselosztása az Azure Application Gateway v1 termékváltozat használatával történik, amely két vagy több példányon való üzembe helyezés esetén magas rendelkezésre áll. A redundancia elérése érdekében a webkiszolgáló, a felügyeleti kiszolgálók és a feldolgozókiszolgálók több példánya külön Virtual Machines, és az egyes szintek külön rendelkezésre állási készletekben vannak üzembe stb. Azure NetApp Files beépített redundanciával rendelkezik az adatközponton belül, így a fájladattár-kiszolgáló ANF-kötetei magas rendelkezésre áll majd. A CMS-adatbázis egy Azure Database for MySQL (DBaaS), amely eredendően magas rendelkezésre áll. További információ: Magas rendelkezésre állás a [Azure Database for MySQL](../../../mysql/concepts-high-availability.md) útmutatóban.

![SAP BusinessObjects BI platform-redundancia rendelkezésre állási készletekkel](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

A fenti architektúra betekintést nyújt az SAP BOBI azure-beli üzembe helyezésébe. Ez azonban nem fedi le az AZURE-beli SAP BOBI Platform összes lehetséges konfigurációs beállítását. Az ügyfél az üzleti igényei alapján testre szabhatja az üzembe helyezést, ha különböző termékeket/szolgáltatásokat választ különböző összetevőkhöz, például a Load Balancer, a fájltárkiszolgálóhoz és a DBMS-hez.

Számos Azure-régióban Availability Zones, ami azt jelenti, hogy független áramforrás-, hűtési és hálózati szolgáltatásokkal rendelkezik. Lehetővé teszi az ügyfél számára az alkalmazások két vagy három rendelkezésre állási zónában való üzembe helyezését. Azok az ügyfelek, akik magas rendelkezésre állást szeretne elérni a rendelkezésre állási zónákban, üzembe helyezhetik az SAP BOBI Platformot a rendelkezésre állási zónákban, hogy az alkalmazás minden összetevője zónaredundáns legyen.

### <a name="disaster-recovery"></a>Vészhelyreállítás

Az ebben a szakaszban található utasítás az SAP BOBI Platform vészhelyreállítási védelmének stratégiáját ismerteti. Kiegészíti az [SAP vészhelyreállítási dokumentumát,](../../../site-recovery/site-recovery-sap.md) amely az SAP teljes vészhelyreállítási megközelítésének elsődleges erőforrásait képviseli.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Referencia vészhelyreállítási architektúra az SAP BusinessObjects BI-platformhoz

Ez a referenciaarchitektúra az SAP BOBI Platform többpéldányos üzembe helyezését futtatja redundáns alkalmazáskiszolgálókkal. Vészhelyreállítás esetén az összes szintet egy másodlagos régióba kell átveszni. Minden szint más stratégiát használ a vészhelyreállítás védelmének megvalósításához.

![SAP BusinessObjects BI-platform vészhelyreállítása](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Terheléselosztóval

Load Balancer SAP BOBI Platform webalkalmazás-kiszolgálói közötti forgalom elosztására szolgál. A felhőben való Azure Application Gateway érdekében implementálja az Application Gateway párhuzamos beállítását a másodlagos régióban.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Web- és BI-alkalmazáskiszolgálókat futtató virtuális gépek

Azure Site Recovery szolgáltatás a másodlagos régióban futó Virtual Machines BI-alkalmazáskiszolgálókat futtató alkalmazások replikálása. Replikálja a másodlagos régió kiszolgálóit, így katasztrófa és kimaradás esetén könnyedén átveheti a feladatátvételt a replikált környezetbe, és folytathatja a munkát

#### <a name="file-repository-servers"></a>Fájltár-kiszolgálók

- **Azure NetApp Files** NFS- és SMB-köteteket biztosít, így bármilyen fájlalapú másolási eszköz használható az adatok Azure-régiók közötti replikálása érdekében. Az ANF-kötet másik régióban való másolására vonatkozó további információkért lásd: Gyakori [kérdések a Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  Használhatja a Azure NetApp Files közi replikációt, amely [](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) jelenleg előzetes verzióban érhető el, és a NetApp SnapMirror® technológiát használja. Ezért a rendszer csak a módosított blokkokat küldi át a hálózaton tömörített, hatékony formátumban. Ez a saját fejlesztésű technológia minimálisra csökkenti a régiók közötti replikáláshoz szükséges adatmennyiséget, ami csökkenti az adatátviteli költségeket. Emellett lerövidíti a replikációs időt, így kisebb visszaállításipont-célkitűzést (RPO) érhet el. További [információért tekintse](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) meg a régiók közötti replikáció használatának követelményeit és szempontjait.

- **Az Azure Premium Files** csak a helyileg redundáns (LRS) és zónaredundáns tárolást (ZRS) támogatja. Az Azure Premium Files dr. stratégiája esetében az [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) vagy a [Azure PowerShell](/powershell/module/az.storage/) használatával másolhatja a fájlokat egy másik régióban lévő másik tárfiókba. További információ: [Vészhelyreállítás és tárfiók feladatátvétele](../../../storage/common/storage-disaster-recovery-guidance.md)

#### <a name="cms-database"></a>CMS-adatbázis

Azure Database for MySQL több lehetőséget is kínál az adatbázis helyreállítására katasztrófa esetén. Válassza ki a vállalata számára megfelelő lehetőséget.

- Engedélyezze a régiók közötti olvasási replikákat az üzletmenet folytonosságának és a vészhelyreállítás megtervezésének továbbfejlesztése érdekében. A forráskiszolgálóról legfeljebb öt replikára replikálhat. Az olvasási replikák aszinkron módon frissülnek a MySQL bináris naplóreplikációs technológiájával. A replikák olyan új kiszolgálók, amelyek kezelése hasonló a hagyományos Azure Database for MySQL kiszolgálókhoz. További információ az olvasási replikákról, az elérhető régiókról, a korlátozásokról és a feladatátvételről az olvasási replikákkal kapcsolatos [fogalmakkal kapcsolatos cikkben.](../../../mysql/concepts-read-replicas.md)

- Használja Azure Database for MySQL georedundáns visszaállítási funkcióját, amely georedundáns biztonsági másolatokkal visszaállítja a kiszolgálót. Ezek a biztonsági másolatok akkor is elérhetők, ha a kiszolgálót kiszolgálót kiszolgáló régiója offline állapotban van. A biztonsági másolatokat bármely másik régióba visszaállíthatja, és újra online állapotba hozhatja a kiszolgálót.

  > [!NOTE]
  > Georedundáns visszaállítás csak akkor lehetséges, ha a kiszolgálót georedundáns biztonsági mentési tárhellyel létesíti. A biztonsági **mentési redundancia beállításainak** módosítása a kiszolgáló létrehozása után nem támogatott. További információt a [Backup redundanciával kapcsolatos cikkben](../../../mysql/concepts-backup.md#backup-redundancy-options) talál.

Az alábbiakban az ebben a példában használt egyes szintek vészhelyreállítására vonatkozó javaslatot mutatjuk be.

| SAP BOBI platformszintek   | Ajánlás                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | A másodlagos Application Gateway párhuzamos beállítása                                                |
| Webalkalmazás-kiszolgálók   | Replikálás a Site Recovery                                                                         |
| BI-alkalmazáskiszolgálók    | Replikálás a Site Recovery                                                                         |
| Azure NetApp Files        | Fájlalapú másolási eszköz az adatok másodlagos régióba vagy **ANF** régiók közötti replikációjára (előzetes verzió) |
| Azure Database for MySQL  | Régiók közötti olvasási replikák **vagy** biztonsági másolat visszaállítása georedundáns biztonsági másolatból.                             |

## <a name="next-steps"></a>Következő lépések

- [Többrétegű SAP-alkalmazások üzembe helyezésének vészhelyreállításának beállítása](../../../site-recovery/site-recovery-sap.md)
- [Az Azure Virtual Machines tervezése és implementációja az SAP-hez](planning-guide.md)
- [Azure Virtual Machines üzembe helyezése SAP-hez](deployment-guide.md)
- [Azure Virtual Machines DBMS üzembe helyezése SAP-hez](./dbms_guide_general.md)
