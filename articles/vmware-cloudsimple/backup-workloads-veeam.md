---
title: Azure VMware-megoldás CloudSimple – a számítási feladatok virtuális gépei biztonsági mentése a privát felhőben a Veeam használatával
description: A cikk azt ismerteti, hogyan készíthet biztonsági mentést egy Azure-alapú CloudSimple-beli privát felhőben futó virtuális gépekről a Veeam B&R 9,5 használatával
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7be606b3e23a594e67acf3f169d88353403d8577
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899337"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Munkaterhelési virtuális gépek biztonsági mentése a CloudSimple privát felhőben a Veeam B&R használatával

Ez az útmutató leírja, hogyan készíthet biztonsági mentést a virtuális gépekről, amelyek egy Azure-alapú CloudSimple-beli privát felhőben futnak a Veeam B&R 9,5 használatával.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Tudnivalók a Veeam biztonsági mentési és helyreállítási megoldásáról

A Veeam megoldás a következő összetevőket tartalmazza.

**Biztonsági mentési kiszolgáló**

A biztonsági mentési kiszolgáló egy olyan Windows Server-kiszolgáló (VM), amely a Veeam felügyeleti központjaként szolgál, és a következő funkciókat hajtja végre: 

* A biztonsági mentés, a replikálás, a helyreállítási ellenőrzés és a visszaállítási feladatok koordinálása
* A feladatütemezés és az erőforrás-kiosztás szabályozása
* Lehetővé teszi a biztonsági mentési infrastruktúra összetevőinek beállítását és kezelését, valamint a biztonsági mentési infrastruktúra globális beállításainak megadását

**Proxykiszolgálók**

A proxykiszolgáló a biztonsági mentési kiszolgáló és a biztonsági mentési infrastruktúra egyéb összetevői között települ. A következő funkciókat kezelik:

* Virtuálisgép-adatok lekérése az üzemi tárolóból
* Tömörítés
* Deduplikáció
* Titkosítás
* Adatátvitel a biztonsági mentési tárházba

**Biztonsági mentési adattár**

A biztonsági mentési tárház a tárolási hely, ahol a Veeam megőrzi a replikált virtuális gépek biztonsági mentési fájljait, virtuálisgép-másolatait és metaadatait.  A tárház lehet Windows vagy Linux rendszerű kiszolgáló helyi lemezekkel (vagy csatlakoztatott NFS/SMB) vagy egy hardveres tároló deduplikáló berendezéssel.

### <a name="veeam-deployment-scenarios"></a>Veeam üzembe helyezési forgatókönyvek
Az Azure-t kihasználva biztonsági mentési tárházat és tárolási célt biztosíthat a hosszú távú biztonsági mentéshez és archiváláshoz. A privát felhőben lévő virtuális gépek és az Azure biztonsági mentési tárháza közötti összes biztonsági mentési hálózati forgalom nagy sávszélességű, kis késleltetésű kapcsolaton keresztül halad. A régiók közötti replikációs forgalom a belső Azure hátlap-hálózaton halad át, ami csökkenti a felhasználók sávszélességének költségeit.

**Alapszintű üzembe helyezés**

A 30 TB-nál kevesebb környezet esetén a CloudSimple a következő konfigurációt javasolja:

* A Veeam a biztonsági mentési kiszolgáló és a proxykiszolgáló ugyanarra a virtuális gépre van telepítve a privát felhőben.
* Egy Linux-alapú elsődleges biztonsági mentési tárház az Azure-ban, amely a biztonsági mentési feladatok céljaként van konfigurálva.
* `azcopy` az elsődleges biztonsági mentési tárházból egy másik régióba replikált Azure Blob-tárolóba másolt adatok másolására szolgál.

![A Veeam alapszintű központi telepítési forgatókönyveit bemutató diagram.](media/veeam-basicdeployment.png)

**Speciális üzembe helyezés**

A 30 TB-nál több biztonsági mentést végző környezetek esetén a CloudSimple a következő konfigurációt javasolja:

* Egy proxykiszolgáló a vSAN-fürtben, a Veeam által javasolt módon.
* A Windows-alapú elsődleges biztonsági mentési tárház a privát felhőben a gyors visszaállítások öt napjainak gyorsítótárazásához.
* Linux Backup-tárház az Azure-ban, mint a hosszabb időtartamú adatmegőrzési feladatok biztonsági másolatának célhelye. Ezt a tárházat kibővíthető biztonsági mentési tárházként kell konfigurálni.
* `azcopy` az elsődleges biztonsági mentési tárházból egy másik régióba replikált Azure Blob-tárolóba másolt adatok másolására szolgál.

![Alapszintű üzembe helyezési forgatókönyvek](media/veeam-advanceddeployment.png)

Az előző ábrán látható, hogy a biztonsági mentési proxy egy olyan virtuális gép, amely a vSAN-adattárban lévő számítási feladatok virtuálisgép-lemezei számára gyors hozzáférést biztosít. A Veeam a virtuális készülék biztonsági mentési proxyjának átviteli módját használja a vSAN.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>A CloudSimple-alapú Veeam-megoldásra vonatkozó követelmények

A Veeam-megoldáshoz a következőket kell tennie:

* Adja meg saját Veeam-licenceit.
* Veeam üzembe helyezése és kezelése a CloudSimple privát felhőben futó számítási feladatok biztonsági mentéséhez.

Ez a megoldás teljes körű irányítást biztosít a Veeam Backup eszköz felett, és lehetővé teszi a natív Veeam felület vagy a Veeam vCenter beépülő modul használatát a virtuális gépek biztonsági mentési feladatainak kezeléséhez.

Ha Ön egy meglévő Veeam-felhasználó, akkor kihagyhatja a Veeam-megoldás összetevőinek szakaszát, és közvetlenül folytathatja a [Veeam üzembe helyezési forgatókönyveit](#veeam-deployment-scenarios).

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Veeam biztonsági mentések telepítése és konfigurálása a saját CloudSimple-felhőben

Az alábbi szakaszok azt ismertetik, hogyan telepíthet és konfigurálhat egy Veeam biztonsági mentési megoldást a saját CloudSimple-felhőhöz.

Az üzembe helyezési folyamat a következő lépésekből áll:

1. [vCenter felhasználói felület: infrastruktúra-szolgáltatások beállítása saját felhőben](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [CloudSimple-portál: saját felhőalapú hálózatkezelés beállítása a Veeam](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [CloudSimple-portál: jogosultságok kiterjesztésének megvonása](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure Portal: a virtuális hálózat összekötése a saját felhővel](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure Portal: biztonsági mentési adattár létrehozása az Azure-ban](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure Portal: az Azure Blob Storage konfigurálása hosszú távú adatmegőrzéshez](#configure-azure-blob-storage-for-long-term-data-retention)
7. [Privát felhő vCenter felhasználói felülete: install Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam-konzol: Veeam biztonsági mentési & helyreállítási szoftver konfigurálása](#veeam-console-install-veeam-backup-and-recovery-software)
9. [CloudSimple-portál: az Veeam-hozzáférés beállítása és a deeszkalációs jogosultságok](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Előkészületek

A Veeam üzembe helyezésének megkezdése előtt a következők szükségesek:

* Ön által birtokolt Azure-előfizetés
* Egy előre létrehozott Azure-erőforráscsoport
* Azure-beli virtuális hálózat az előfizetésében
* Azure-tárfiók
* A CloudSimple-portál használatával létrehozott [privát felhő](create-private-cloud.md) .  

A megvalósítási fázisban a következő elemek szükségesek:

* VMware-sablonok a Windows rendszerhez a Veeam telepítéséhez (például Windows Server 2012 R2 – 64 bites rendszerkép)
* A biztonsági mentési hálózathoz azonosított egy elérhető VLAN
* A biztonsági mentési hálózathoz hozzárendelni kívánt alhálózat CIDR
* Veeam 9,5 U3-as telepíthető adathordozó (ISO) a privát felhő vSAN-adattárához feltöltve

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter felhasználói felület: infrastruktúra-szolgáltatások beállítása saját felhőben

Konfigurálja az infrastruktúra-szolgáltatásokat a privát felhőben, így könnyebben kezelheti a számítási feladatokat és eszközöket.

* Hozzáadhat egy külső identitás-szolgáltatót a vCenter- [azonosítók beállítása Active Directory használatára](set-vcenter-identity.md) , ha a következők bármelyike érvényes:

  * Azonosítani szeretné a helyszíni Active Directory (AD) felhasználóit a saját felhőben.
  * Egy AD-t szeretne beállítani a saját felhőben az összes felhasználó számára.
  * Az Azure AD-t szeretné használni.
* Ha IP-címeket keres, IP-címek kezelését és névfeloldási szolgáltatásokat szeretne biztosítani a saját számítási feladataihoz a saját felhőben, állítson be egy DHCP-és DNS-kiszolgálót a [DNS-és DHCP-alkalmazások és-munkaterhelések beállítása a CloudSimple privát felhőben](dns-dhcp-setup.md)című részben leírtak szerint.

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>CloudSimple: privát felhőalapú hálózatkezelés beállítása a Veeam

Nyissa meg a CloudSimple-portált, és hozzon létre privát felhőalapú hálózatkezelést a Veeam-megoldáshoz.

Hozzon létre egy VLAN-t a tartalék hálózat számára, és rendeljen hozzá egy alhálózati CIDR. Útmutatásért lásd: [VLAN-ok/alhálózatok létrehozása és kezelése](create-vlan-subnet.md).

Hozzon létre tűzfalszabályok között a felügyeleti alhálózat és a tartalék hálózat között, hogy engedélyezze a Veeam által használt portok hálózati forgalmát. Lásd a Veeam témakörben [használt portok](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)szakaszt. A Tűzfalszabályok létrehozásával kapcsolatos utasításokért tekintse meg a [tűzfalak és szabályok beállítása](firewall.md)című témakört.

A következő táblázat a portok listáját tartalmazza.

| Ikon | Leírás | Ikon | Leírás |
| ------------ | ------------- | ------------ | ------------- |
| Biztonsági mentési kiszolgáló  | vCenter  | HTTPS/TCP  | 443 |
| Biztonsági mentési kiszolgáló <br> *A Veeam biztonsági mentési & replikációs összetevőinek telepítéséhez szükséges* | Biztonsági mentési proxy  | TCP/UDP  | 135, 137 – 139 és 445 |
    | Biztonsági mentési kiszolgáló   | DNS  | UDP  | 53  | 
    | Biztonsági mentési kiszolgáló   | Veeam frissítési értesítési kiszolgáló  | TCP  | 80  | 
    | Biztonsági mentési kiszolgáló   | Veeam-licenc frissítése kiszolgáló  | TCP  | 443  | 
    | Biztonsági mentési proxy   | vCenter |   |   | 
    | Biztonsági mentési proxy  | Linuxos biztonsági mentési tárház   | TCP  | 22  | 
    | Biztonsági mentési proxy  | Windows biztonsági mentési tárház  | TCP  | 49152 – 65535   | 
    | Biztonsági mentési adattár  | Biztonsági mentési proxy  | TCP  | 2500 – 5000  | 
    | Forrás biztonsági mentési adattár<br> *A biztonságimásolat-másolási feladatokhoz használatos*  | Cél biztonsági mentési adattár  | TCP  | 2500 – 5000  | 

Hozzon létre tűzfalszabályokat a munkaterhelési alhálózat és a biztonsági mentési hálózat között a következő témakörben leírtak szerint: [Tűzfalszabályok beállítása és szabályok](firewall.md).  Az alkalmazással kapcsolatos biztonsági mentéshez és visszaállításhoz [további portokat](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) kell megnyitni az adott alkalmazásokat futtató munkaterhelésű virtuális gépeken.

Alapértelmezés szerint a CloudSimple egy 1Gbps ExpressRoute-hivatkozást biztosít. Nagyobb méretű környezeti méretek esetén nagyobb sávszélesség-kapcsolatra lehet szükség. További információért forduljon az Azure támogatási szolgálatához.

A telepítés folytatásához szüksége lesz az engedélyezési kulcsra és a társ áramköri URI-ra, valamint az Azure-előfizetéséhez való hozzáférésre.  Ezek az információk a CloudSimple-portál Virtual Network kapcsolatok lapján érhetők el. Útmutatásért lásd: az [Azure-beli virtuális hálózatra vonatkozó információk beszerzése az CloudSimple való kapcsolódáshoz](virtual-network-connection.md). Ha bármilyen problémája van az információ beszerzésével, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>CloudSimple privát felhő: a cloudowner jogosultságának kiemelése

Az alapértelmezett "cloudowner" felhasználó nem rendelkezik megfelelő jogosultsággal a privát felhő vCenter a VEEAM telepítéséhez, így a felhasználó vCenter jogosultságait ki kell bővíteni. További információ: a [jogosultságok kiterjesztésének](escalate-private-cloud-privileges.md)megemelése.

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Azure Portal: a virtuális hálózat összekötése a saját felhővel

Csatlakoztathatja a virtuális hálózatot a privát felhőhöz az [Azure Virtual Network-kapcsolat](azure-expressroute-connection.md)utasításait követve az ExpressRoute használatával.

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure Portal: biztonsági mentési tárházat tartalmazó virtuális gép létrehozása

1. Hozzon létre egy standard D2 v3 virtuális gépet (2 vCPU és 8 GB memóriával).
2. Válassza ki a CentOS 7,4-alapú rendszerképet.
3. Konfiguráljon egy hálózati biztonsági csoportot (NSG) a virtuális géphez. Győződjön meg arról, hogy a virtuális gép nem rendelkezik nyilvános IP-címmel, és nem érhető el a nyilvános internetről.
4. Hozzon létre egy felhasználónevet és egy jelszó-alapú felhasználói fiókot az új virtuális géphez. Útmutatásért lásd: [Linux rendszerű virtuális gép létrehozása a Azure Portalban](../virtual-machines/linux/quick-create-portal.md).
5. Hozzon létre 1x512 GiB standard HDD-t, és csatolja azt a tárház virtuális géphez.  Útmutatásért lásd: [felügyelt adatlemez csatolása Windows rendszerű virtuális géphez a Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md).
6. [Hozzon létre egy XFS kötetet a felügyelt lemezen](https://www.digitalocean.com/docs/volumes/how-to/). Jelentkezzen be a virtuális gépre a korábban említett hitelesítő adatok használatával. Hajtsa végre a következő parancsfájlt logikai kötet létrehozásához, adja hozzá a lemezt, hozzon létre egy XFS fájlrendszer- [partíciót](https://www.digitalocean.com/docs/volumes/how-to/partition/) , és [csatlakoztassa](https://www.digitalocean.com/docs/volumes/how-to/mount/) a partíciót a/backup1 elérési útja alatt.

    Példa parancsfájl:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. Tegye elérhetővé a/backup1-t NFS-csatlakoztatási pontként a Veeam biztonsági mentési kiszolgálónak, amely a privát felhőben fut. Útmutatásért tekintse meg a Digital Ocean című cikket, [amely bemutatja, hogyan állíthat be egy NFS-csatlakoztatást CentOS 6 rendszeren](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Használja ezt az NFS-megosztási nevet, ha a Veeam biztonsági mentési kiszolgálón konfigurálja a biztonsági mentési tárházat.

8. Konfigurálja a NSG található szűrési szabályokat a biztonsági mentési tárház virtuális gépe számára, hogy explicit módon engedélyezze a virtuális gép összes hálózati forgalmát.

> [!NOTE]
> A Veeam Backup & replikációja az SSH protokoll használatával kommunikál a Linux Backup-Tárházak használatával, és az SCP-segédprogramot igényli a Linux-adattárakban. Ellenőrizze, hogy az SSH démon megfelelően van-e konfigurálva, és hogy az SCP elérhető-e a Linux-gazdagépen.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Az Azure Blob Storage konfigurálása hosszú távú adatmegőrzéshez

1. Hozzon létre egy általános célú Storage-fiókot (GPv2) a standard típusú és egy blob-tárolóhoz a Microsoft video [első lépések Azure Storage](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)-ban leírtak szerint.
2. Hozzon létre egy Azure Storage-tárolót a [tároló létrehozása](/rest/api/storageservices/create-container) hivatkozásban leírtak szerint.
2. Töltse le a `azcopy` Linux parancssori segédprogramját a Microsofttól. A következő parancsokat használhatja a bash-rendszerhéjban a CentOS 7,5-ben.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. A `azcopy` paranccsal másolja a biztonságimásolat-fájlokat a blob-tárolóba.  A részletes parancsokért lásd: [adatok átvitele a AzCopy Linuxon](../storage/common/storage-use-azcopy-v10.md) .

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>vCenter-konzol: install Veeam B&R

Hozzáférés a vCenter a saját felhőből a Veeam-szolgáltatásfiók létrehozásához, a Veeam B&R 9,5-es verziójának telepítéséhez, valamint a Veeam a szolgáltatásfiók használatával történő konfigurálásához.

1. Hozzon létre egy "Veeam biztonsági mentési szerepkör" nevű új szerepkört, és rendelje hozzá a szükséges engedélyeket a Veeam által javasolt módon. További részletekért tekintse meg a Veeam témakör [szükséges engedélyeit](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Hozzon létre egy új "Veeam felhasználói csoport" csoportot a vCenter-ben, és rendelje hozzá a "Veeam Backup" szerepkört.
3. Hozzon létre egy új "Veeam szolgáltatásfiók" felhasználót, és adja hozzá a "Veeam felhasználói csoport" elemhez.

    ![Veeam-szolgáltatásfiók létrehozása](media/veeam-vcenter01.png)

4. Hozzon létre egy elosztott portot a vCenter a biztonsági mentési hálózat VLAN használatával. További részletekért tekintse meg a VMware-videót, [amely az elosztott vSphere webes ügyfélprogramban történő létrehozását](https://www.youtube.com/watch?v=wpCd5ZbPOpA)ismerteti.
5. Hozza létre a virtuális gépeket a vCenter Veeam biztonsági mentési és proxykiszolgáló számára a [Veeam rendszerkövetelményeinek](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)megfelelően. Használhatja a Windows 2012 R2 vagy a Linux rendszert. További információ: [a Linux Backup-Tárházak használatára vonatkozó követelmények](https://www.veeam.com/kb2216).
6. Csatlakoztassa a telepíthető Veeam ISO-t CDROM-eszközként a Veeam Backup Server virtuális gépen.
7. RDP-munkamenet használata a Windows 2012 R2 rendszerű géphez (a Veeam telepítésének céljával), [telepítse a Veeam B&R 9.5 U3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) -ot egy Windows 2012 R2 RENDSZERű virtuális gépen.
8. Keresse meg a Veeam biztonsági mentési kiszolgáló virtuális gépe belső IP-címét, és konfigurálja az IP-címet statikusra a DHCP-kiszolgálón. Az ehhez szükséges pontos lépések a DHCP-kiszolgálótól függenek. Példaként a NETGATE <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">statikus DHCP-hozzárendelések</a> elmagyarázza, hogyan kell konfigurálni a DHCP-kiszolgálót egy pfsense-útválasztó használatával.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veeam-konzol: a Veeam biztonsági mentési és helyreállítási szoftver telepítése

A Veeam-konzol használatával konfigurálja a Veeam biztonsági mentési és helyreállítási szoftvereit. Részletekért lásd: [Veeam Backup & Replication v9 – telepítés és üzembe helyezés](https://www.youtube.com/watch?v=b4BqC_WXARk).

1. VMware vSphere hozzáadása felügyelt kiszolgálói környezetként. Ha a rendszer kéri, adja meg a Veeam-szolgáltatásfiók azon hitelesítő adatait, amelyet a vCenter-konzoljának elején hozott létre [: telepítse a Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)-t.

    * A Load Control és az alapértelmezett speciális beállítások alapértelmezett beállításainak használata.
    * Állítsa be a csatlakoztatási kiszolgáló helyét a biztonsági mentési kiszolgálóként.
    * Módosítsa a Veeam-kiszolgáló konfigurációs biztonsági mentési helyét a távoli tárházra.

2. Adja hozzá a Linux-kiszolgálót az Azure-ban biztonsági mentési tárházként.

    * A Load Control és a speciális beállítások alapértelmezett beállításainak használata. 
    * Állítsa be a csatlakoztatási kiszolgáló helyét a biztonsági mentési kiszolgálóként.
    * Módosítsa a Veeam-kiszolgáló konfigurációs biztonsági mentési helyét a távoli tárházra.

3. Engedélyezze a konfiguráció biztonsági mentésének titkosítását a **Home> konfiguráció biztonsági mentési beállításaival**.

4. Windows Server rendszerű virtuális gép hozzáadása proxykiszolgálóként VMware-környezethez. Egy proxy forgalmi szabályainak használatával titkosíthatja a biztonsági mentési adatokat a hálózaton keresztül.

5. Biztonsági mentési feladatok konfigurálása.
    * A biztonsági mentési feladatok konfigurálásához kövesse a [biztonsági mentési feladat létrehozása](https://www.youtube.com/watch?v=YHxcUFEss4M)című témakör utasításait.
    * Engedélyezze a biztonságimásolat-fájlok titkosítását a **Speciális beállítások > tároló** területen.

6. A biztonsági másolatok másolásával kapcsolatos feladatok konfigurálása.

    * A biztonsági másolatok másolási feladatainak konfigurálásához kövesse a videóban található utasításokat a [biztonsági](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)másolat készítéséhez.
    * Engedélyezze a biztonságimásolat-fájlok titkosítását a **Speciális beállítások > tároló** területen.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>CloudSimple-portál: az Veeam-hozzáférés beállítása és a deeszkalációs jogosultságok
Hozzon létre egy nyilvános IP-címet a Veeam biztonsági mentési és helyreállítási kiszolgálójának. Útmutatásért lásd: [nyilvános IP-címek lefoglalása](public-ips.md).

Hozzon létre egy tűzfalszabály használatával, amely lehetővé teszi, hogy a Veeam Backup kiszolgáló kimenő kapcsolatokat hozzon létre a Veeam webhelye számára a frissítések/javítások letöltéséhez a 80-as TCP-porton. Útmutatásért tekintse [meg a tűzfalak és szabályok beállítása](firewall.md)című témakört.

A jogosultságok megszüntetéséhez tekintse meg a [jogosultságok dekiterjesztését](escalate-private-cloud-privileges.md#de-escalate-privileges)ismertető témakört.

## <a name="references"></a>Hivatkozások

### <a name="cloudsimple-references"></a>CloudSimple-referenciák

* [Privát felhő létrehozása](create-private-cloud.md)
* [VLAN-ok/alhálózatok létrehozása és kezelése](create-vlan-subnet.md)
* [vCenter-identitás forrása](set-vcenter-identity.md)
* [A számítási feladatok DNS-és DHCP-beállítása](dns-dhcp-setup.md)
* [Jogosultságok eszkalációja](escalate-privileges.md)
* [Tűzfaltáblák és -szabályok beállítása](firewall.md)
* [Saját felhő engedélyei](learn-private-cloud-permissions.md)
* [Nyilvános IP-címek lefoglalása](public-ips.md)

### <a name="veeam-references"></a>Veeam-referenciák

* [Használt portok](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Szükséges engedélyek](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Rendszerkövetelmények](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [A Veeam biztonsági mentési & replikációjának telepítése](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Szükséges modulok és engedélyek a Linux RENDSZERhez készült többplatformos FLR és tárház-támogatáshoz](https://www.veeam.com/kb2216)
* [Veeam Backup & replikáció v9 – telepítés és üzembe helyezés – videó](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 biztonsági mentési feladatok létrehozása – videó](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 Backup-másolási feladatok létrehozása – videó](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure-referenciák

* [Virtuális hálózati átjáró konfigurálása a ExpressRoute-hez a Azure Portal használatával](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [VNet összekapcsolása áramkörhöz – eltérő előfizetés](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Linuxos virtuális gép létrehozása a Azure Portalban](../virtual-machines/linux/quick-create-portal.md)
* [Felügyelt adatlemez csatolása Windows rendszerű virtuális géphez a Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Első lépések az Azure Storage-ban – videó](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Tároló létrehozása](/rest/api/storageservices/create-container)
* [Adatok áthelyezése az AzCopyval Linux rendszeren](../storage/common/storage-use-azcopy-v10.md)

### <a name="vmware-references"></a>VMware-referenciák

* [Elosztott porttartomány létrehozása a vSphere webes ügyfélprogramban – videó](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Egyéb referenciák

* [XFS-kötet létrehozása a felügyelt lemezen – RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [NFS-csatlakoztatás beállítása CentOS 7 rendszeren – HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [A DHCP-kiszolgáló konfigurálása – NETGATE](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
