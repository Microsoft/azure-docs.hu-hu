---
title: SAP BusinessObjects BI platform üzembe helyezése az Azure-ban Linux rendszeren | Microsoft Docs
description: Az SAP BusinessObjects BI platform üzembe helyezése és konfigurálása az Azure-ban Linux rendszeren
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
ms.openlocfilehash: 7a40acf7dea11781a0381e93ea180c5185b7818b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671919"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>SAP BusinessObjects BI platformtelepítési útmutató Linuxhoz az Azure-on

Ez a cikk az SAP BOBI platform Linux rendszeren való üzembe helyezésének stratégiáját ismerteti. Ebben a példában két, prémium SSD Managed Disks rendelkező virtuális gép van konfigurálva a telepítési könyvtáraként. A Azure Database for MySQL a CMS-adatbázishoz használatos, és a file repository-kiszolgáló Azure NetApp Files a két kiszolgáló között van megosztva. Az alapértelmezett tomcat Java-webalkalmazás és BI platform alkalmazás mindkét virtuális gépen együtt települ. A felhasználói kérések terheléselosztásához a rendszer olyan Application Gateway használ, amely natív TLS/SSL-kiszervezési képességekkel rendelkezik.

Az ilyen típusú architektúra a kis-és a nem éles környezetekben is érvényes. Éles vagy nagyméretű üzembe helyezés esetén külön gazdagépek is rendelkezhetnek a webalkalmazásokhoz, és a több BOBI-alkalmazással is rendelkezhetnek, amelyekkel a kiszolgáló további információkat dolgozhat fel.

![SAP BOBI üzembe helyezése az Azure-ban Linuxra](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

Ebben a példában a termék verziója és a fájlrendszer elrendezése van használatban

- SAP BusinessObjects platform 4,3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (verzió: 8.0.15)
- MySQL C API-összekötő – libmysqlclient (verzió: 6.1.11)

| Fájlrendszer        | Leírás                                                                                                               | Méret (GB)             | Tulajdonos  | Group  | Tárolás                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | Az SAP BOBI-példány, az alapértelmezett tomcat-webalkalmazás és az adatbázis-illesztőprogramok telepítéséhez használt fájlrendszer (ha szükséges) | SAP-Méretezési irányelvek | bl1adm | sapsys | Felügyelt prémium lemez – SSD |
| /usr/sap/frsinput  | A csatlakoztatási könyvtár a megosztott fájlok között minden olyan BOBI-gazdagépen megtalálható, amelyet bemeneti adattárként fog használni.  | Üzleti igények         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | A csatlakoztatási könyvtár a megosztott fájlok között minden olyan BOBI-gazdagépen megtalálható, amelyet kimeneti fájl adattárként fog használni. | Üzleti igények         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Linux rendszerű virtuális gép üzembe helyezése Azure Portal használatával

Ebben a szakaszban két virtuális gépet (VM) hozunk létre az SAP BOBI platformhoz készült Linux operációs rendszer (OS) rendszerképpel. A Virtual Machines létrehozásának magas szintű lépései a következők:

1. [Erőforráscsoport](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) létrehozása

2. Hozzon létre egy [Virtual Network](../../../virtual-network/quick-create-portal.md#create-a-virtual-network).

   - Ne használjon egyetlen alhálózatot az összes Azure-szolgáltatáshoz az SAP BI platform üzembe helyezése során. Az SAP BI platform architektúrája alapján több alhálózatot kell létrehoznia. Ebben az üzembe helyezésben három alhálózatot hozunk létre – alkalmazás-alhálózatot, adattár-tároló alhálózatot és Application Gateway alhálózatot.
   - Az Azure-ban Application Gateway és Azure NetApp Files mindig külön alhálózaton kell lennie. További részletekért olvassa el az [Azure Application Gateway](../../../application-gateway/configuration-overview.md) és [a Azure NetApp Files Network Planning című cikk útmutatását](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) .

3. Hozzon létre egy rendelkezésre állási készletet.

   - A többpéldányos telepítés egyes szintjeinél a redundancia érdekében helyezze a virtuális gépeket egy rendelkezésre állási csoport minden rétegéhez. Győződjön meg róla, hogy az architektúra alapján elkülöníti az egyes rétegek rendelkezésre állási csoportjait.

4. Hozzon létre egy 1. virtuális gépet **(azusbosl1).**

   - Használhat egyéni rendszerképet, vagy kiválaszthat egy rendszerképet az Azure Marketplace-ről. Tekintse át a [virtuális gép üzembe helyezését az Azure Marketplace-](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) en az SAP-hez, vagy [helyezzen üzembe egy virtuális gépet egyéni rendszerképekkel](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) az Ön igényeinek megfelelően.

5. A 2. virtuális gép **(azusbosl2) létrehozása.**
6. Adjon hozzá egy prémium SSD lemezt. Az SAP BOBI telepítési könyvtáraként lesz használva.

## <a name="provision-azure-netapp-files"></a>Kiépítés Azure NetApp Files

Mielőtt folytatná a Azure NetApp Files telepítését, ismerkedjen meg az Azure [NetApp Files dokumentációval](../../../azure-netapp-files/azure-netapp-files-introduction.md).

Azure NetApp Files több [Azure-régióban](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)is elérhető. Ellenőrizze, hogy a kiválasztott Azure-régió kínál-e Azure NetApp Files.

Az [Azure-régió Azure NetApp Files elérhetősége](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) lapon tekintheti meg a Azure NetApp Files régiók szerinti rendelkezésre állását.

A Azure NetApp Files üzembe helyezése előtt a Azure NetApp Files bevezetéséhez regisztrálni kell a [Azure NetApp Files utasításokat](../../../azure-netapp-files/azure-netapp-files-register.md) .

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files erőforrások üzembe helyezése

Az alábbi utasítások azt feltételezik, hogy már üzembe helyezte az Azure-beli [virtuális hálózatot](../../../virtual-network/virtual-networks-overview.md). A Azure NetApp Files erőforrásokat és virtuális gépeket, amelyeken a Azure NetApp Files erőforrásokat csatlakoztatni kell, ugyanabban az Azure-beli virtuális hálózatban vagy az Azure-beli virtuális hálózatokban kell telepíteni.

1. Ha még nem telepítette az erőforrásokat, kérje a [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md)bevezetését.

2. Hozzon létre egy NetApp-fiókot a kiválasztott Azure-régióban a [NetApp-fiók létrehozása](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)című részben található utasításokat követve.

3. Hozzon létre egy Azure NetApp Files kapacitási készletet a [Azure NetApp Files kapacitásának beállítása](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)című részben leírtak szerint.

   - A jelen cikkben bemutatott SAP BI platform architektúrája egyetlen Azure NetApp Files kapacitási készletet használ a *prémium* szintű szolgáltatási szinten. Az Azure-beli SAP BI file adattár-kiszolgáló esetén ajánlott Azure NetApp Files *prémium* vagy *Ultra* [szolgáltatási szintet](../../../azure-netapp-files/azure-netapp-files-service-levels.md)használni.

4. Alhálózat delegálása Azure NetApp Filesre, az [alhálózat delegálása Azure NetApp Filesra](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)című részben leírtak szerint.

5. Azure NetApp Files kötetek üzembe helyezéséhez kövesse az [NFS-kötet létrehozása a Azure NetApp Files számára](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)című témakör utasításait.

   A ANF-kötet NFSv3 és NFSv 4.1-ként is telepíthető, mivel az SAP BOBI platform esetében mindkét protokoll támogatott. Telepítse a köteteket a megfelelő Azure NetApp Files alhálózatban. Az Azure NetApp-kötetek IP-címeit a rendszer automatikusan hozzárendeli.

Ne feledje, hogy a Azure NetApp Files erőforrásoknak és az Azure-beli virtuális gépeknek ugyanabban az Azure-beli virtuális hálózaton vagy az Azure-beli virtuális hálózatokban kell lenniük. Például: azusbobi-frsinput, azusbobi-frsoutput a kötetek nevei és nfs://10.31.2.4/azusbobi-frsinput, a nfs://10.31.2.4/azusbobi-frsoutput a fájlok elérési útja a Azure NetApp Files kötetek számára.

- Mennyiségi azusbobi – frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Mennyiségi azusbobi – frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Fontos szempontok

Az SAP BOBI platform file adattár-kiszolgáló Azure NetApp Files létrehozásakor vegye figyelembe az alábbi szempontokat:

1. A minimális kapacitási készlet 4 tebibájt (TiB).
2. A minimális kötet mérete 100 gibibájtban értendők (GiB).
3. Azure NetApp Files és az összes virtuális gépet, amelybe a Azure NetApp Files köteteket csatlakoztatni kell, ugyanabban az Azure-beli virtuális hálózatban vagy azonos régióban lévő, egymással azonos [virtuális hálózatokban](../../../virtual-network/virtual-network-peering-overview.md) kell lennie. A VNET-társítások ugyanazon régióban való elérésének Azure NetApp Files jelenleg támogatott. Az Azure NetApp a globális társon keresztül való elérése még nem támogatott.
4. A kiválasztott virtuális hálózatnak rendelkeznie kell egy Azure NetApp Files delegált alhálózattal.
5. Az Azure NetApp Files [exportálási házirend](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)segítségével szabályozhatja az engedélyezett ügyfeleket, a hozzáférési típust (írható és olvasható, csak olvasható stb.).
6. A Azure NetApp Files funkció még nem tud zónával foglalkozni. Jelenleg a funkció nincs telepítve az Azure-régió összes rendelkezésre állási zónájában. Vegye figyelembe, hogy egyes Azure-régiókban lehetséges a késés következményei.
7. Azure NetApp Files kötetek NFSv3 vagy NFSv 4.1 kötetként telepíthetők. Mindkét protokoll támogatott az SAP BI platform alkalmazásaiban.

## <a name="configure-file-systems-on-linux-servers"></a>Fájlrendszerek konfigurálása Linux-kiszolgálókon

Az ebben a szakaszban szereplő lépések az alábbi előtagokat használják:

**[A]**: a lépés az összes gazdagépre vonatkozik.

### <a name="format-and-mount-sap-file-system"></a>SAP fájlrendszer formázása és csatlakoztatása

1. **[A]** az összes csatlakoztatott lemez listázása

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

2. **[A]** Format blokk-eszköz a/usr/SAP

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** csatlakozási könyvtár létrehozása

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** az eszköz blokkjának lekérése

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** a fájlrendszer csatlakoztatási bejegyzésének fenntartása az/etc/fstab fájlban

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** csatlakoztatási fájlrendszer

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

### <a name="mount-azure-netapp-files-volume"></a>Azure NetApp Files kötet csatlakoztatása

1. **[A]** csatlakoztatási könyvtárak létrehozása

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** az ügyfél operációs rendszerének konfigurálása a Nfsv 4.1 csatlakoztatásának támogatásához **(csak a nfsv 4.1 használata esetén alkalmazható)**

   Ha Azure NetApp Files köteteket használ a NFSv 4.1 protokollal, hajtsa végre a következő konfigurációt az összes virtuális gépen, ahol Azure NetApp Files NFSv 4.1 köteteket csatlakoztatni kell.

   **NFS-tartomány beállításainak ellenőrzése**

   Győződjön meg arról, hogy a tartomány alapértelmezett Azure NetApp Files tartományként van konfigurálva, azaz a **defaultv4iddomain.com** , és a leképezés nem **értékre van állítva.**

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
   > Ügyeljen arra, hogy az NFS-tartományt a/etc/idmapd.conf-ben állítsa be a virtuális gépen, hogy az megfeleljen az alapértelmezett tartományi konfigurációnak Azure NetApp Files: **defaultv4iddomain.com**. Ha az NFS-ügyfél (azaz a virtuális gép) és az NFS-kiszolgáló (például az Azure NetApp-konfiguráció) közötti eltérés nem egyezik, akkor a virtuális gépekre csatlakoztatott Azure NetApp-köteteken található fájlok engedélyei nem lesznek láthatók.

   Ellenőrizze `nfs4_disable_idmapping` . Értékeként az **Y** értéknek kell lennie. A-t tartalmazó könyvtár-struktúra létrehozásához `nfs4_disable_idmapping` hajtsa végre a csatlakoztatási parancsot. Nem lehet manuálisan létrehozni a könyvtárat a/sys/modules alatt, mivel a hozzáférés a kernel/illesztőprogramok számára van fenntartva.

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

3. **[A]** csatlakoztatási bejegyzések hozzáadása

   NFSv3 használata esetén

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   NFSv 4.1 használata esetén

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

## <a name="configure-cms-database---azure-database-for-mysql"></a>A CMS-adatbázis konfigurálása – Azure Database for MySQL

Ez a szakasz részletesen ismerteti, hogyan kell kiépíteni Azure Database for MySQL a Azure Portal használatával. Emellett útmutatást nyújt arról, hogyan hozhat létre CMS-és naplózási adatbázisokat az SAP BOBI platformhoz, valamint egy felhasználói fiókot az adatbázis eléréséhez.

Az irányelvek csak akkor alkalmazhatók, ha a MySQL-hez készült Azure DB-t használja. Más adatbázis (ok) esetén tekintse meg az SAP vagy az adatbázisra vonatkozó dokumentációt az utasításokért.

### <a name="create-an-azure-database-for-mysql"></a>Azure-adatbázis létrehozása a MySQL-hez

Jelentkezzen be Azure Portal, és kövesse a [Azure Database for MySQL rövid útmutatójában](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)említett lépéseket. Néhány szempont a Azure Database for MySQL kiépítés közben:

1. Válassza ki ugyanazt a régiót, Azure Database for MySQL ahol az SAP BI platformon futó alkalmazás-kiszolgálók futnak.

2. Válasszon egy támogatott adatbázis-verziót a [termék rendelkezésre állási mátrixa (PAM)](https://support.sap.com/pam) alapján, amely az SAP Bobi-verzióra jellemző SAP BI-ra vonatkozik. Az SAP PAM-ban a MySQL AB-hez tartozó kompatibilitási irányelvek követése

3. A "számítás + tárolás" területen válassza a **kiszolgáló konfigurálása** lehetőséget, és válassza ki a megfelelő díjszabási szintet a méretezési kimenet alapján.

4. Alapértelmezés szerint engedélyezve van a **tárterület autogrowth** . Ne feledje, hogy a [tárterület](../../../mysql/concepts-pricing-tiers.md#storage) csak akkor méretezhető, ha nem.

5. Alapértelmezés szerint a **megőrzési időszak biztonsági mentése** hét nap, de opcionálisan akár 35 napig is [beállítható](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) .

6. A Azure Database for MySQL biztonsági mentései alapértelmezés szerint helyileg redundánsak, így ha a kiszolgáló biztonsági mentését a Geo-redundáns tárolóban szeretné használni, válassza a **földrajzilag redundáns** **lehetőséget a biztonsági mentési redundancia lehetőségei** közül.

> [!NOTE]
> A [biztonsági mentési redundancia beállításainak](../../../mysql/concepts-backup.md#backup-redundancy-options) módosítása a kiszolgáló létrehozása után nem támogatott.

### <a name="configure-connection-security"></a>A kapcsolatbiztonság konfigurálása

Alapértelmezés szerint a létrehozott kiszolgáló tűzfallal védett, és nem érhető el nyilvánosan. Az alábbi lépések végrehajtásával biztosíthatja a hozzáférést ahhoz a virtuális hálózathoz, ahol az SAP BI platform Application Servers fut.  

1. Lépjen a Azure Portal kiszolgáló erőforrásai elemre, és válassza ki a kiszolgáló erőforrásának bal oldali menüjének **kapcsolatbiztonsági** elemét.
2. Válassza az **Igen** lehetőséget az **Azure-szolgáltatásokhoz való hozzáférés engedélyezéséhez**.
3. A VNET-szabályok területen válassza a **meglévő virtuális hálózat hozzáadása** elemet. Válassza ki a virtuális hálózatot és az SAP BI platform Application Server alhálózatát. Emellett hozzáférést kell biztosítania a Jump Box-hoz vagy más kiszolgálókhoz, ahonnan a [MySQL Workbench](../../../mysql/connect-workbench.md) -t a Azure Database for MySQLhoz lehet kötni. A MySQL Workbench a CMS és a naplózási adatbázis létrehozásához használatos
4. A virtuális hálózatok hozzáadása után válassza a **Mentés** lehetőséget.

### <a name="create-cms-and-audit-database"></a>CMS-és naplózási adatbázis létrehozása

1. Töltse le és telepítse a MySQL Workbench alkalmazást a [MySQL-webhelyről](https://dev.mysql.com/downloads/workbench/). Győződjön meg arról, hogy telepíti a MySQL Workbench-t azon a kiszolgálón, amely hozzáférhet a Azure Database for MySQLhoz.

2. Kapcsolódás a kiszolgálóhoz a MySQL Workbench használatával. Kövesse az ebben a [cikkben](../../../mysql/connect-workbench.md#get-connection-information)említett útmutatást. Ha a kapcsolatok tesztelése sikeres, a következő üzenet jelenik meg:

   ![SQL Workbench-kapcsolatok](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. Az SQL-lekérdezés lapon az alábbi lekérdezés futtatásával hozzon létre sémát a CMS és a naplózási adatbázis számára.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. Felhasználói fiók létrehozása a sémához való kapcsolódáshoz

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

5. A MySQL felhasználói fiók engedélyeinek és szerepköreinek ellenõrzése

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

Ahhoz, hogy az SAP BOBI Application Server hozzáférhessen az adatbázishoz, adatbázis-ügyfélre/illesztőprogramokra van szükség. A Linux rendszerhez készült MySQL C API-összekötőt a CMS és a naplózási adatbázisok elérésére kell használni. A CMS-adatbázishoz való ODBC-kapcsolatok nem támogatottak. Ez a szakasz útmutatást nyújt a MySQL C API-összekötő Linux rendszeren történő beállításához.

1. Tekintse meg a [MySQL-illesztőprogramokat és a Azure Database for MySQL cikkel kompatibilis felügyeleti eszközöket](../../../mysql/concepts-compatibility.md) , amelyek a Azure Database for MySQL kompatibilis illesztőprogramokat ismertetik. Keresse meg a **MySQL Connector/C (libmysqlclient)** illesztőprogramot a cikkben.

2. Az illesztőprogramok letöltéséhez tekintse meg ezt a [hivatkozást](https://downloads.mysql.com/archives/c-c/) .

3. Válassza ki az operációs rendszert, és töltse le a MySQL-összekötő megosztott összetevő RPM-csomagját. Ebben a példában a MySQL-Connector-c-Shared-6.1.11 Connector verzióját használja a rendszer.

4. Telepítse az összekötőt az összes SAP BOBI alkalmazás-példányban.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. A libmysqlclient.so elérési útjának keresése

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Állítsa be LD_LIBRARY_PATH, hogy a `/usr/lib64` könyvtárra mutasson a felhasználói fiókhoz, amelyet a rendszer a telepítéshez fog használni.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Kiszolgáló előkészítése

Az ebben a szakaszban szereplő lépések az alábbi előtagokat használják:

**[A]**: a lépés az összes gazdagépre vonatkozik.

1. **[A]** a Linux-íz (SLES vagy RHEL) alapján be kell állítania a rendszermag paramétereit, és telepítenie kell a szükséges kódtárakat. Tekintse meg a UNIX rendszerhez készült [Business Intelligence platform telepítési útmutatójának](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US) **rendszerkövetelmények** című szakaszát.

2. **[A]** ellenőrizze, hogy a számítógép időzónája helyesen van-e beállítva. A telepítési útmutató [további UNIX-és Linux-követelmények című szakasza tartalmaz további](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) információt.

3. **[A]** hozzon létre egy felhasználói fiókot (**BL1** adm) és csoportot (sapsys), amely alatt a szoftver háttérben futó folyamatai futnak. Ezzel a fiókkal hajthatja végre a telepítést, és futtathatja a szoftvert. A fiók nem követeli meg a gyökérszintű jogosultságokat.

4. **[A]** állítsa be a felhasználói fiók (**BL1** adm) környezetét egy támogatott UTF-8 területi beállítás használatára, és győződjön meg arról, hogy a konzol szoftvere támogatja az UTF-8 karakterkészleteket. Annak biztosítása érdekében, hogy az operációs rendszer a megfelelő területi beállítást használja, állítsa a LC_ALL és a LANG környezeti változót a kívánt területi beállításra (**BL1** adm) felhasználói környezetében.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** felhasználói fiók konfigurálása (**BL1** adm).

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

6. Töltse le és csomagolja ki az SAP BusinessObjects BI platformhoz készült adathordozót az SAP Service Piactérről.

## <a name="installation"></a>Telepítés

A felhasználói fiók **BL1** tartozó területi beállításának keresése a kiszolgálón

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Navigáljon az SAP BusinessObjects BI platform adathordozóra, és futtassa az alábbi parancsot az **BL1** adm-felhasználóval

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Kövesse az [SAP Bobi platform](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) telepítési útmutatóját UNIX rendszerhez, amely a saját verziójára vonatkozik. Néhány szempontot figyelembe kell venni az SAP BOBI platform telepítésekor.

- A **termék regisztrációjának konfigurálása** képernyőn használhat ideiglenes licenckulcs SAP BusinessObjects-megoldásokhoz az sap Note [1288121](https://launchpad.support.sap.com/#/notes/1288121) -es verziójában, vagy létrehozhat licenckulcs az SAP Service piactéren

- A **telepítés típusának kiválasztása** képernyőn válassza a **teljes** telepítés az első kiszolgálón (azusbosl1) lehetőséget a többi kiszolgáló (azusbosl2) beállításnál válassza az **Egyéni/Kibontás** lehetőséget, amely kibővíti a meglévő Bobi-telepítést.

- Az **alapértelmezett vagy a meglévő adatbázis kiválasztása** képernyőn válassza a **meglévő adatbázis konfigurálása** lehetőséget, amely felszólítja a CMS és a naplózási adatbázis kiválasztására. Válassza a **MySQL** lehetőséget a CMS-adatbázis típusa és a naplózási adatbázis típusa beállításnál.

  Ha a telepítés során nem kívánja konfigurálni a naplózást, válassza a nincs naplózási adatbázis lehetőséget is.

- Válassza ki a megfelelő beállításokat a **Java webalkalmazás-kiszolgáló kiválasztása képernyőn** az SAP Bobi architektúrája alapján. Ebben a példában az 1. lehetőséget választottuk, amely a Tomcat-kiszolgálót ugyanarra az SAP BOBI platformra telepíti.

- Adja meg a CMS-adatbázis információit a **CMS adattár-adatbázis konfigurálása – MySQL**. Példa a CMS-adatbázis Linux-telepítésre vonatkozó információinak bevitelére. A Azure Database for MySQL az alapértelmezett 3306-es porton van használatban.
  
  ![SAP BOBI üzembe helyezése Linux-CMS-adatbázison](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- Választható Adja meg a naplózási adatbázis adatait a **naplózási adattár konfigurálása adatbázis – MySQL** című témakörben. Példa bemenet a Linux-telepítés naplózási adatbázisával kapcsolatos információkhoz.

  ![SAP BOBI üzembe helyezés Linux rendszeren – naplózási adatbázis](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Kövesse az utasításokat, és adja meg a szükséges bemeneteket a telepítés befejezéséhez.

A többpéldányos telepítéshez futtassa a telepítőt a második gazdagépen (azusbosl2). A **telepítés típusának kiválasztása** képernyőn válassza az **Egyéni/Kibontás** lehetőséget, amely kibővíti a meglévő Bobi-telepítést.

A MySQL-hez készült Azure Database-ben egy átjárót használ a kapcsolatok átirányításához a kiszolgálói példányokkal. A kapcsolatok létrejötte után a MySQL-ügyfél az átjárón beállított MySQL-verziót jeleníti meg, nem a MySQL-kiszolgálópéldányon futó tényleges verziót. A MySQL-kiszolgálópéldány verziójának meghatározásához használja a `SELECT VERSION();` parancsot a MySQL parancssorában. Tehát a központi felügyeleti konzolon (CMC) eltérő adatbázis-verziót talál, amely alapvetően az átjárón beállított verzió. További részletekért keresse fel a [támogatott Azure Database for MySQL Server-verziókat](../../../mysql/concepts-supported-versions.md) .

![SAP BOBI üzembe helyezés Linux rendszeren – CMC-beállítások](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Telepítés utáni

### <a name="tomcat-clustering---session-replication"></a>Tomcat-fürtszolgáltatás – munkamenet-replikáció

A Tomcat két vagy több alkalmazáskiszolgáló fürtözését támogatja a munkamenet-replikáláshoz és a feladatátvételhez. Az SAP BOBI platform-munkamenetek szerializálva vannak, a felhasználói munkamenetek zökkenőmentesen, a Tomcat egy másik példányán is átadhatják a feladatátvételt, még akkor is, ha az alkalmazáskiszolgáló meghibásodik.

Ha például egy felhasználó olyan webkiszolgálóhoz csatlakozik, amely nem sikerül, miközben a felhasználó egy, az SAP BI-alkalmazásban található mappa-hierarchiában navigál. Megfelelően konfigurált fürt esetén a felhasználó továbbra is folytathatja a mappa-hierarchia navigálását anélkül, hogy átirányítja a bejelentkezés lapra.

A [2808640](https://launchpad.support.sap.com/#/notes/2808640)-es SAP-megjegyzésben a Tomcat-fürtszolgáltatás konfigurálásának lépései csoportos küldéssel vannak megadva. Az Azure-ban azonban a csoportos küldés nem támogatott. Tehát ahhoz, hogy a Tomcat-fürt működjön az Azure-ban, a [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) -t kell használnia (SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907)). A Tomcat-fürtözést az SAP blogon az SAP [BUSINESSOBJECTS bi platformon statikus tagsággal is megkeresheti](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) az Azure-ban.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>SAP BI platform terheléselosztási webes rétege

Az SAP BOBI többpéldányos telepítése esetén a Java webalkalmazás-kiszolgálók (webes réteg) két vagy több gazdagépen futnak. Ha a felhasználói terhelést egyenletesen szeretné terjeszteni a webkiszolgálókon, a végfelhasználók és a webkiszolgálók között terheléselosztó is használható. Az Azure-ban Azure Load Balancer vagy Azure Application Gateway használatával kezelheti a webalkalmazás-kiszolgálók forgalmát. Az egyes ajánlatokkal kapcsolatos részleteket a következő szakasz ismerteti.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure Load Balancer (hálózati alapú Load Balancer)

A [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) egy nagy teljesítményű, kis késleltetésű, 4 (TCP, UDP) terheléselosztó, amely az egészséges Virtual Machines között osztja el a forgalmat. A terheléselosztó állapotának mintavétele figyeli az adott portot az egyes virtuális gépeken, és csak egy működő virtuális gép (ek) ra osztja el a forgalmat. A nyilvános terheléselosztó vagy a belső terheléselosztó attól függően választhatja ki, hogy az SAP BI platform elérhető-e az internetről vagy sem. A zóna feleslegessé vált, és magas rendelkezésre állást biztosít a Availability Zones között.

Tekintse meg az alábbi ábra belső Load Balancer szakaszát, ahol a webalkalmazás-kiszolgáló a 8080-as porton fut, az alapértelmezett tomcat HTTP-portot, amelyet az állapot-ellenőrzés fog figyelni. Így a végfelhasználótól érkező összes kérelem átirányítva lesz a webalkalmazás-kiszolgálókra (azusbosl1 vagy azusbosl2) a háttér-készletben. A Load Balancer nem támogatja a TLS/SSL-lezárást (más néven TLS/SSL-kiszervezést). Ha az Azure Load Balancer használatával osztja el a forgalmat a webkiszolgálók között, javasoljuk, hogy használjon standard Load Balancer.

> [!NOTE]
> Ha a nyilvános IP-címek nélküli virtuális gépek a belső (nincs nyilvános IP-cím) standard Azure Load Balancer háttér-készletbe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurálást végeznek a nyilvános végpontok útválasztásának engedélyezéséhez. A kimenő kapcsolatok elérésével kapcsolatos részletekért lásd: [nyilvános végpontú kapcsolat Virtual Machines az Azure standard Load Balancer használata az SAP magas rendelkezésre állási helyzetekben](high-availability-guide-standard-load-balancer-outbound-connections.md).

![Azure Load Balancer a forgalom elosztásához a webkiszolgálók között](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure Application Gateway (webalkalmazás-terheléselosztó)

Az [Azure Application Gateway (AGW)](../../../application-gateway/overview.md) biztosítja az Application Delivery Controller (ADC) szolgáltatást, amely segítségével az alkalmazások egy vagy több webalkalmazás-kiszolgálóra irányítják a felhasználói forgalmat. Különböző 7. rétegbeli terheléselosztási funkciókat kínál, például a TLS/SSL-kiszervezést, a webalkalmazási tűzfalat (WAF), a cookie-alapú munkamenet-affinitást és másokat az alkalmazásaihoz.

Az SAP BI platformon az Application Gateway az alkalmazás webes forgalmát a megadott erőforrásokra irányítja a háttér-készletben – azusbosl1 vagy azusbos2. Hozzá kell rendelnie egy figyelőt a porthoz, szabályokat kell létrehoznia, és erőforrásokat kell hozzáadnia egy háttér-készlethez. Az alábbi ábrán az Application Gateway with Private frontend IP-címe (10.31.3.20) a felhasználók számára belépési pontként működik, kezeli a bejövő TLS/SSL (HTTPS-TCP/443) kapcsolatokat, visszafejti a TLS/SSL-t, és továbbítja a titkosítatlan kérést (HTTP-TCP/8080) a háttér-készletben található kiszolgálókra. A beépített TLS/SSL-megszakítási funkcióval csak egy TLS/SSL-tanúsítványt kell fenntartani az Application Gateway-ben, ami leegyszerűsíti a műveleteket.

![Application Gateway a forgalom elosztásához a webkiszolgálók között](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Az SAP BOBI webkiszolgáló Application Gateway konfigurálásához tekintse át az SAP [Bobi webkiszolgálók terheléselosztása az Azure Application Gateway használatával](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) az SAP blogon című témakört.

> [!NOTE]
> Azt javasoljuk, hogy az Azure Application Gateway használatával terheléselosztást nyújtson a webkiszolgáló felé irányuló forgalom számára, mivel a szolgáltatás biztosítja az SSL-alapú kiszervezést, központosítja az SSL-kezelést, hogy csökkentse a titkosítást és a visszafejtést a kiszolgálón, Round-Robin algoritmust a forgalom terjesztésére, a webalkalmazási tűzfal (WAF) képességeire, a magas rendelkezésre állásra

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>SAP BusinessObjects BI platform – biztonsági mentés és visszaállítás

A biztonsági mentés és a visszaállítás egy folyamat, amellyel az adatmennyiség és az alkalmazások rendszeres másolata hozható létre a különböző helyekre. Így visszaállíthatja vagy helyreállíthatja az előző állapotot, ha az eredeti vagy sérült alkalmazások elvesznek vagy sérültek. Ez az üzleti katasztrófák helyreállítási stratégiájának alapvető eleme is.

Az SAP BOBI platform átfogó biztonsági mentési és visszaállítási stratégiájának fejlesztéséhez azonosítsa azokat az összetevőket, amelyek az alkalmazásban rendszerleállást vagy megszakadást eredményeznek. Az SAP BOBI platformon a következő összetevők biztonsági mentése létfontosságú az alkalmazás biztonsága érdekében.

- SAP BOBI telepítési könyvtár (felügyelt prémium szintű lemezek)
- File adattár-kiszolgáló (Azure NetApp Files vagy prémium szintű Azure-fájlok)
- CMS-adatbázis (Azure Database for MySQL vagy adatbázis az Azure-beli virtuális gépen)

A következő szakasz azt ismerteti, hogyan valósítható meg a biztonsági mentési és visszaállítási stratégia a SAP BOBI platformon futó egyes összetevőkhöz.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Backup & Restore for SAP BOBI telepítési könyvtár

Az Azure-ban az alkalmazás-kiszolgálók és az összes csatlakoztatott lemez biztonsági mentésének legegyszerűbb módja [Azure Backup](../../../backup/backup-overview.md) szolgáltatás használata. Független és elkülönített biztonsági mentéseket biztosít a virtuális gépeken tárolt adatmennyiségek nem szándékos megsemmisítéséhez. A biztonsági másolatok egy helyreállítási tárban vannak tárolva, a helyreállítási pontok beépített kezelésével. A konfiguráció és a skálázás egyszerű, a biztonsági mentések optimalizáltak, és szükség esetén könnyen visszaállíthatók.

A biztonsági mentési folyamat részeként a rendszer pillanatképet készít, és az adatok átkerülnek a helyreállítási tárolóba, és nincs hatással az éles munkaterhelésekre. A pillanatkép különböző szintű konzisztenciát biztosít a [Pillanatkép-konzisztencia](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) című cikkben leírtak szerint. Dönthet úgy is, hogy a virtuális gépen lévő adatlemezek részhalmazát is biztonsági mentést készít a szelektív lemezek biztonsági mentési és visszaállítási funkcióinak használatával. További információ: Azure-beli [virtuális gép biztonsági mentési](../../../backup/backup-azure-vms-introduction.md) dokumentum és [Gyakori kérdések – Azure virtuális gépek biztonsági mentése](../../../backup/backup-azure-vm-backup-faq.yml).

#### <a name="backup--restore-for-file-repository-server"></a>Backup & Restore for File adattár-kiszolgáló

**Azure NetApp Files** létrehozhat egy igény szerinti pillanatképeket, és automatikus pillanatképet ütemezhet a pillanatkép-házirendek használatával. A pillanatkép-másolatok a ANF-kötet időponthoz tartozó másolatát adják meg. További információ: a [Pillanatképek kezelése Azure NetApp Files használatával](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).

**Azure Files** Backup a natív [Azure Backup](../../../backup/backup-overview.md) szolgáltatással van integrálva, amely központosítja a biztonsági mentési és visszaállítási funkciót a virtuális gépek biztonsági mentésével és egyszerűbbé teszi a művelet működését. További információ: Azure- [fájlmegosztás biztonsági mentése](../../../backup/azure-file-share-backup-overview.md) és [Gyakori kérdések – biztonsági mentés Azure Files](../../../backup/backup-azure-files-faq.md).

#### <a name="backup--restore-for-cms-database"></a>A CMS-adatbázis biztonsági mentésének & visszaállítása

A MySQL-hez készült Azure Database egy DBaaS ajánlat az Azure-ban, amely automatikusan létrehozza a kiszolgáló biztonsági másolatait, és a helyileg redundáns vagy földrajzilag redundáns tárolóban tárolja őket. A MySQL-hez készült Azure Database biztonsági másolatokat készít az adatfájlokról és a tranzakciónaplóról. A maximálisan támogatott tárterülettől függően teljes és különbözeti biztonsági mentéseket (4 TB-os maximális tárolási kiszolgálókat) vagy pillanatképes biztonsági mentést (akár 16 TB-os maximális tárolási kiszolgáló) is igénybe vesz. Ezek a biztonsági másolatok lehetővé teszik a kiszolgálók bármikor visszaállítását a beállított biztonsági mentési megőrzési időszakon belül. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap, amelyet igény szerint három napig is [beállíthat](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) . Minden biztonsági mentés AES 256 bites titkosítással van titkosítva.

Ezek a biztonságimásolat-fájlok nem felhasználók számára lettek kitéve, és nem exportálhatók. Ezek a biztonsági másolatok csak Azure Database for MySQL-beli visszaállítási műveletekhez használhatók. A [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) használatával másolhat egy adatbázist. További információ: [biztonsági mentés és visszaállítás a Azure Database for MySQLban](../../../mysql/concepts-backup.md).

A Virtual Machinesre telepített adatbázisok esetében szabványos biztonsági mentési eszközöket vagy [Azure Backup](../../../backup/sap-hana-db-about.md) HASZNÁLHAT a HANA-adatbázishoz. Ha az Azure-szolgáltatások és-eszközök nem felelnek meg a követelménynek, más biztonságimásolat-eszközöket vagy parancsfájlokat is használhat a lemezek biztonsági mentésének létrehozásához.

## <a name="sap-businessobjects-bi-platform-reliability"></a>SAP BusinessObjects BI platform megbízhatósága

Az SAP BusinessObjects BI platform különböző szinteket tartalmaz, amelyek konkrét feladatokra és műveletekre vannak optimalizálva. Ha bármelyik réteg egyik összetevője elérhetetlenné válik, az SAP BOBI alkalmazás elérhetetlenné válik, vagy az alkalmazás bizonyos funkciói nem fognak működni. Ezért meg kell győződnie arról, hogy minden egyes rétegnek megbízhatónak kell lennie ahhoz, hogy az alkalmazások működőképesek legyenek üzleti fennakadás nélkül.

Ez a szakasz az SAP BOBI platform következő lehetőségeire koncentrál:

- **Magas rendelkezésre állás:** Ha az egyik kiszolgáló elérhetetlenné válik, a magas rendelkezésre állású platformon az Azure-régión belül legalább két mindent megtarthat, hogy az alkalmazás működőképes maradjon.
- Vész- **helyreállítás:** Ez az alkalmazás funkcióinak visszaállítási folyamata, ha az egész Azure-régióhoz hasonló katasztrofális veszteségek merülnek fel, mert valamilyen természeti katasztrófa miatt elérhetetlenné válik.

Ennek a megoldásnak a megvalósítása az Azure rendszerbeállításainak természetétől függően változhat. Az ügyfélnek az üzleti igényeknek megfelelően testre kell szabnia a magas rendelkezésre állást és a vész-helyreállítási megoldást.

### <a name="high-availability"></a>Magas rendelkezésre állás

A magas rendelkezésre állás olyan technológiákra utal, amelyek csökkenthetik az IT-zavarokat azáltal, hogy redundáns, hibatűrő vagy feladatátvételsel védett összetevőkön keresztül biztosítják az alkalmazások/szolgáltatások üzleti folytonosságát ugyanazon az adatközponton belül. Ebben az esetben az adatközpontok egy Azure-régión belül találhatók. A [magas rendelkezésre állást biztosító architektúra és az SAP-forgatókönyvek](sap-high-availability-architecture-scenarios.md) az Azure-beli SAP-alkalmazásokhoz kínált különböző magas rendelkezésre állású technikák és javaslatok kezdeti betekintést nyújtanak, amely az ebben a szakaszban ismertetett utasításokat fogja tartalmazni.

Az SAP BOBI platform méretezési eredménye alapján meg kell terveznie a tájat, és meg kell határoznia a BI-összetevők elosztását az Azure-Virtual Machines és az alhálózatokon. Az elosztott architektúrában a redundancia szintje a szükséges üzleti helyreállítási idő célkitűzéstől (RTO) és a helyreállítási időcélkitűzéstől (RPO) függ. Az SAP BOBI platform különböző szinteket és összetevőket tartalmaz az egyes csomagokhoz, így redundancia valósítható meg. Így ha az egyik összetevő meghibásodik, az SAP BOBI-alkalmazásának nem kell megszakadnia. Példa:

- Redundáns alkalmazás-kiszolgálók, például a BI-alkalmazások és a webkiszolgálók
- Egyedi összetevők, mint például a CMS-adatbázis, a file repository-kiszolgáló, Load Balancer

A következő szakasz azt ismerteti, hogyan lehet magas rendelkezésre állást elérni az SAP BOBI platform egyes összetevőin.

#### <a name="high-availability-for-application-servers"></a>Alkalmazás-kiszolgálók magas rendelkezésre állása

A BI-és webalkalmazás-kiszolgálók esetében, függetlenül attól, hogy azok külön vagy együtt vannak telepítve, nincs szükség speciális, magas rendelkezésre állású megoldásra. A redundancia révén magas rendelkezésre állást érhet el, amely a BI-és webkiszolgálók több példányának konfigurálását teszi lehetővé különböző Azure-Virtual Machinesekben.

Egy vagy több esemény miatti leállások hatásának csökkentése érdekében tanácsos a több virtuális gépen futó alkalmazás-kiszolgálók esetében az alábbi magas rendelkezésre állási gyakorlat követése.

- Az adatközpont-meghibásodások elleni védelem Availability Zones használata.
- Több Virtual Machines konfigurálható egy rendelkezésre állási csoportba a redundancia érdekében.
- Használjon Managed Disks virtuális gépekhez egy rendelkezésre állási csoporton belül.
- Konfigurálja az egyes alkalmazási szinteket különálló rendelkezésre állási csoportokra.

További információkért tekintse meg [a Linux rendszerű virtuális gépek rendelkezésre állásának felügyeletét](../../manage-availability.md) ismertető témakört.

#### <a name="high-availability-for-cms-database"></a>Magas rendelkezésre állás a CMS-adatbázishoz

Ha Azure Database as Service (DBaaS) szolgáltatást használ a CMS-adatbázishoz, alapértelmezés szerint a magas rendelkezésre állási keretrendszer van megadva. Csak ki kell választania a régiót és a szolgáltatást, amely magas rendelkezésre állást, redundanciát és rugalmasságot biztosít anélkül, hogy további összetevőket kellene konfigurálnia. További információ az Azure-beli támogatott DBaaS-ajánlat SLA-ról: [magas rendelkezésre állás Azure Database for MySQL](../../../mysql/concepts-high-availability.md) és [magas rendelkezésre állás Azure SQL Database](../../../azure-sql/database/high-availability-sla.md)

A CMS-adatbázisok más adatbázis-KEZELŐi telepítéséhez tekintse meg a következő témakört: az adatbázis- [kezelő rendszer központi telepítési útmutatója az SAP-Munkaterheléshez](dbms_guide_general.md), amely betekintést nyújt a különböző adatbázis-kezelő

#### <a name="high-availability-for-file-repository-server"></a>Magas rendelkezésre állás a file adattár-kiszolgáló számára

A file adattár-kiszolgáló (FRS) arra a lemez-címtárakra utal, ahol a tartalmak, például a jelentések, a világegyetemek és a kapcsolatok tárolódnak. Ezt a rendszer az összes alkalmazás-kiszolgálója között megosztja. Ezért győződjön meg arról, hogy a szolgáltatás nagyon elérhető.

Az Azure-ban kiválaszthatja az [Azure Premium-fájlokat](../../../storage/files/storage-files-introduction.md) vagy a [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) olyan fájlmegosztás esetén, amelynek a használata kiválóan elérhető és nagyon tartós jellegű. További információ: Azure Files [redundancia](../../../storage/files/storage-files-planning.md#redundancy) című szakasza.

> [!NOTE]
> Az SMB protokoll Azure Files általánosan elérhető, de a Azure Files NFS protokoll támogatása jelenleg előzetes verzióban érhető el. További információ: az [NFS 4,1-támogatás a Azure Files számára már előzetes](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/) verzióban érhető el

Mivel ez a fájlmegosztási szolgáltatás nem érhető el az összes régióban, ügyeljen arra, hogy a [régióbeli hely által elérhető termékekkel](https://azure.microsoft.com/en-us/global-infrastructure/services/) kapcsolatban tájékozódjon a naprakész információkról. Ha a szolgáltatás nem érhető el a régióban, létrehozhat NFS-kiszolgálót, amelyről megoszthatja a fájlrendszert az SAP BOBI alkalmazással. Emellett a magas rendelkezésre állást is figyelembe kell vennie.

#### <a name="high-availability-for-load-balancer"></a>Magas rendelkezésre állás a Load Balancerhez

A forgalom webkiszolgálón keresztüli terjesztéséhez használhatja Azure Load Balancer vagy az Azure Application Gatewayt is. A terheléselosztó bármelyikének redundancia az üzembe helyezéshez választott SKU alapján érhető el.

- A redundancia Azure Load Balancer érdekében a standard Load Balancer előtér-zóna redundánsként való konfigurálásával érhető el. További információ: [standard Load Balancer és Availability Zones](../../../load-balancer/load-balancer-standard-availability-zones.md)
- Application Gateway esetében a magas rendelkezésre állás az üzembe helyezés során kiválasztott rétegek típusától függően érhető el.
  - a v1 SKU támogatja a magas rendelkezésre állási helyzeteket, ha két vagy több példányt telepített. Az Azure ezeket a példányokat a frissítési és a tartalék tartományok között osztja el, így biztosítva, hogy a példányok ne legyenek egyszerre a hibák. Így ebben az SKU-ban a redundancia a zónán belül is elérhető
  - a v2 SKU automatikusan biztosítja, hogy az új példányok a tartalék tartományok és a frissítési tartományok között legyenek elosztva. Ha a zóna-redundancia lehetőséget választja, a legújabb példányok a rendelkezésre állási zónák között is elterjednek, hogy a zónákhoz való sikertelen rugalmasságot nyújtsanak. További részletekért tekintse meg az automatikus [skálázás és a zóna – redundáns Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Az SAP BusinessObjects BI platform magas rendelkezésre állású architektúrájának referenciája

Az alábbi hivatkozási architektúra leírja az SAP BOBI platform telepítését a rendelkezésre állási csoport használatával, amely virtuális gépek redundanciát és rendelkezésre állást biztosít a zónán belül. Az architektúra különböző Azure-szolgáltatások, például az Azure Application Gateway, a Azure NetApp Files és a Azure Database for MySQL használatát mutatja be a SAP BOBI platformon, amely beépített redundanciát kínál, ami csökkenti a különböző magas rendelkezésre állású megoldások kezelésének összetettségét.

Az alábbi ábrán a bejövő forgalom (HTTPS-TCP/443) terheléselosztása az Azure Application Gateway v1 SKU használatával történik, amely két vagy több példányra való üzembe helyezés esetén nagyon elérhető. A webkiszolgáló, a felügyeleti kiszolgálók és a feldolgozó kiszolgálók több példánya külön Virtual Machines van telepítve a redundancia érdekében, és az egyes szintek külön rendelkezésre állási csoportokban vannak üzembe helyezve. Azure NetApp Files beépített redundancia van az adatközponton belül, így a ANF-kötetek a Fájlszolgáltatások kiszolgálója számára is elérhetők lesznek. A CMS-adatbázis Azure Database for MySQL (DBaaS) van kiépítve, amely magas rendelkezésre állással rendelkezik. További információ: [magas rendelkezésre állás Azure Database for MySQL](../../../mysql/concepts-high-availability.md) útmutatóban.

![SAP BusinessObjects BI platform redundancia a rendelkezésre állási csoportokkal](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

A fenti architektúra betekintést nyújt az Azure-beli SAP BOBI üzembe helyezésének módjára. Ez azonban nem vonatkozik az Azure-beli SAP BOBI platform összes lehetséges konfigurációs beállítására. Az ügyfél az üzleti igényeknek megfelelően testre szabhatja az üzembe helyezést különböző termékek/szolgáltatások kiválasztásával különböző összetevőkhöz, például a Load Balancer, a file adattár-kiszolgálóhoz és az adatbázis-kezelő rendszerekhez.

Számos Azure-régióban Availability Zones kínálunk, ami azt jelenti, hogy független energiaellátási forrással, hűtéssel és hálózattal rendelkezik. Lehetővé teszi, hogy az ügyfél két vagy három rendelkezésre állási zónán keresztül telepítse az alkalmazást. Az olyan ügyfelek számára, akik magas rendelkezésre állást szeretnének elérni a AZs-ben, az SAP BOBI platformot a rendelkezésre állási zónákon keresztül is üzembe helyezhetik, így biztosítva, hogy az alkalmazás egyes összetevői

### <a name="disaster-recovery"></a>Vészhelyreállítás

Az ebben a szakaszban található utasítás ismerteti az SAP BOBI platform vész-helyreállítási védelmét biztosító stratégiát. Kiegészíti az SAP-dokumentum vész- [helyreállítását](../../../site-recovery/site-recovery-sap.md) , amely a teljes SAP vész-helyreállítási megközelítés elsődleges erőforrásait képviseli.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Az SAP BusinessObjects BI platformra vonatkozó vészhelyzeti helyreállítási architektúra

Ez a hivatkozási architektúra az SAP BOBI platform többpéldányos telepítését redundáns alkalmazás-kiszolgálókkal futtatja. Vész-helyreállítás esetén az összes szintet átadja egy másodlagos régiónak. Az egyes szintek egy másik stratégiát használnak a vész-helyreállítási védelem biztosításához.

![SAP BusinessObjects BI platform vész-helyreállítás](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Terheléselosztóval

A Load Balancer az SAP BOBI platform webalkalmazás-kiszolgálói közötti forgalmának elosztására szolgál. A DR Azure Application Gateway eléréséhez az Application Gateway párhuzamos telepítését kell megvalósítani a másodlagos régióban.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Web-és BI-kiszolgálói alkalmazásokat futtató virtuális gépek

A Azure Site Recovery szolgáltatás használatával Virtual Machines replikálhatja a webes és BI-alkalmazás-kiszolgálókat a másodlagos régióban. A rendszer replikálja a kiszolgálókat a másodlagos régióban, hogy a katasztrófák és kimaradások bekövetkezésekor a feladatátvételt egyszerűen átadja a replikált környezetnek, és folytassa a munkát

#### <a name="file-repository-servers"></a>File adattár-kiszolgálók

- A **Azure NETAPP Files** NFS-és SMB-köteteket biztosít, ezért az Azure-régiók közötti adatreplikáláshoz bármilyen fájl alapú másolási eszközt használhat. A ANF-kötetek egy másik régióban való másolásával kapcsolatos további információkért tekintse meg a [gyakori kérdéseket Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  Használhatja Azure NetApp Files régiók közötti replikálást, amely jelenleg [előzetes](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) verzióban érhető el, amely a NetApp snapmirror® technológiát használja. Így a rendszer csak a megváltozott blokkokat tömörített, hatékony formátumban továbbítja a hálózaton. Ez a szabadalmaztatott technológia minimálisra csökkenti a régiók közötti replikáláshoz szükséges adatmennyiséget, így az adatátviteli költségeket is megtakaríthatja. Emellett lerövidíti a replikálási időt, így elérheti a kisebb visszaállítási pontok célját (RPO). További információkért tekintse meg a [régiók közötti replikáció használatára vonatkozó követelményeket és szempontokat](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) .

- Az **Azure Premium-fájlok** csak a helyileg REDUNDÁNS (LRS) és a Zone redundáns tárolás (ZRS) használatát támogatják. Az Azure Premium Files DR stratégia esetében a [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) vagy a [Azure PowerShell](/powershell/module/az.storage/) használatával másolhatja át a fájlokat egy másik, eltérő régióban lévő Storage-fiókba. További információ: vész [-helyreállítási és Storage-fiók feladatátvétele](../../../storage/common/storage-disaster-recovery-guidance.md)

#### <a name="cms-database"></a>CMS-adatbázis

Azure Database for MySQL több lehetőséget kínál az adatbázis helyreállítására, ha vannak ilyen katasztrófák. Válassza a megfelelő lehetőséget, amely a vállalat számára működik.

- Az üzletmenet-folytonosság és a vész-helyreállítási tervezés fokozása érdekében engedélyezze a régiók közötti olvasási replikákat. A forrás-kiszolgálóról legfeljebb öt replikára lehet replikálni. Az olvasási replikák aszinkron módon frissülnek a MySQL bináris naplójának replikációs technológiájának használatával. A replikák olyan új kiszolgálók, amelyeket a rendszeres Azure Database for MySQL-kiszolgálókhoz hasonló módon kezel. További információ az olvasási replikák, az elérhető régiók, a korlátozások és a feladatátvétel az [olvasási replika fogalmakkal című cikkben](../../../mysql/concepts-read-replicas.md)olvashatók.

- Használja Azure Database for MySQL geo-visszaállítási funkcióját, amely visszaállítja a kiszolgálót a Geo-redundáns biztonsági másolatok használatával. Ezek a biztonsági másolatok akkor is elérhetők, ha a kiszolgáló által üzemeltetett régió offline állapotban van. Ezeket a biztonsági másolatokból bármely más régióba visszaállíthatja, és a kiszolgáló ismét online állapotba helyezhető.

  > [!NOTE]
  > A Geo-visszaállítás csak akkor lehetséges, ha a kiszolgálót geo-redundáns biztonsági mentési tárolóval kiépített. A **biztonsági mentési redundancia beállításainak** módosítása a kiszolgáló létrehozása után nem támogatott. További információ: [Backup redundancia](../../../mysql/concepts-backup.md#backup-redundancy-options) című cikk.

Az alábbiakban az ebben a példában használt egyes szintek vész-helyreállítási javaslata látható.

| SAP BOBI platform szintjei   | Ajánlás                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | Application Gateway párhuzamos beállítása a másodlagos régióban                                                |
| Webalkalmazás-kiszolgálók   | Replikálás Site Recovery használatával                                                                         |
| BI-alkalmazások kiszolgálói    | Replikálás Site Recovery használatával                                                                         |
| Azure NetApp Files        | Fájl alapú másolási eszköz az adatreplikáláshoz a másodlagos régióba **vagy** a ANF régiók közötti replikálásra (előzetes verzió) |
| Azure Database for MySQL  | A tartományok közötti olvasási replikák **vagy** a biztonsági mentés visszaállítása a Geo-redundáns biztonsági mentésből.                             |

## <a name="next-steps"></a>Következő lépések

- [Vész-helyreállítás beállítása többrétegű SAP-alkalmazások üzembe helyezéséhez](../../../site-recovery/site-recovery-sap.md)
- [Azure Virtual Machines az SAP tervezéséhez és megvalósításához](planning-guide.md)
- [Azure Virtual Machines üzembe helyezés az SAP-ban](deployment-guide.md)
- [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz](./dbms_guide_general.md)
