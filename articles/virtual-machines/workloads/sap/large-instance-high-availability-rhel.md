---
title: Az Azure Large Instances magas rendelkezésre állása az SAP on RHEL-hez
description: Megtudhatja, hogyan automatizálhat egy SAP HANA-feladatátvételt egy Pacemaker-fürt használatával a Red Hat Enterprise Linux.
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-sap
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: f7b6e6efbbd17655b4f68d79ac26ee34ae754a3b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728445"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>Az Azure Large Instances magas rendelkezésre állása az SAP on RHEL-hez

> [!NOTE]
> Ez a cikk a feketelista kifejezésre mutató hivatkozásokat *tartalmaz,* a Microsoft által már nem használt kifejezést. Ha ezt a kifejezést eltávolítjuk a szoftverből, eltávolítjuk a cikkből.

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Pacemaker-fürtöt az RHEL 7.6-ban egy adatbázis SAP HANA automatizálásához. Az útmutató lépéseit a Linux, a SAP HANA és a Pacemaker ismerete szükséges.

Az alábbi táblázat a cikkben használt állomásneveket tartalmazza. A cikkben található kódblokkok a futtatandó parancsokat, valamint a parancsok kimenetét mutatják. Fordítson különös figyelmet arra, hogy melyik csomópontra hivatkoznak az egyes parancsok.

| Típus | Állomásnév | Csomópont|
|-------|-------------|------|
|Elsődleges gazdagép|`sollabdsm35`|1. csomópont|
|Másodlagos gazdagép|`sollabdsm36`|2. csomópont|

## <a name="configure-your-pacemaker-cluster"></a>A Pacemaker-fürt konfigurálása


A fürt konfigurálását megelőzően állítsa be az SSH-kulcscserét a csomópontok közötti megbízhatósági kapcsolat létesítéhez.

1. Az alábbi parancsokkal hozzon létre egyforma adatokat `/etc/hosts` mindkét csomóponton.

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    10.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    10.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    10.20.251.150 sollabdsm36-st
    10.20.251.151 sollabdsm35-st
    10.20.252.151 sollabdsm36-back
    10.20.252.150 sollabdsm35-back
    10.20.253.151 sollabdsm36-node
    10.20.253.150 sollabdsm35-node
    ```

2.  Hozza létre és cserélje le az SSH-kulcsokat.
    1. SSH-kulcsok létrehozása.

    ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
    ```
    2. Másolja át a kulcsokat a többi gazdagépre a jelszó nélküli SSH-hoz.
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  Tiltsa le a selinuxot mindkét csomóponton.
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. Indítsa újra a kiszolgálókat, majd a következő paranccsal ellenőrizze a selinux állapotát.
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. Konfigurálja az NTP-t (Network Time Protocol). Mindkét fürtcsomópont időzónának egyeznie kell. A fájl tartalmának megnyitásához és ellenőrzéséhez `chrony.conf` használja a következő parancsot.
    1. Az alábbi tartalmakat kell hozzáadni a konfigurációs fájlhoz. Módosítsa a tényleges értékeket a környezetnek megfelelő értékekkel.
        ```
        vi /etc/chrony.conf
    
         Use public servers from the pool.ntp.org project.
    
         Please consider joining the pool (http://www.pool.ntp.org/join.html).
    
        server 0.rhel.pool.ntp.org iburst
       ```
    
    2. Engedélyezze a chrony szolgáltatást. 
      
        ```
        systemctl enable chronyd
    
        systemctl start chronyd
    
        
    
        chronyc tracking
    
        Reference ID : CC0BC90A (voipmonitor.wci.com)
    
        Stratum : 3
    
        Ref time (UTC) : Thu Jan 28 18:46:10 2021
    
        chronyc sources
    
        210 Number of sources = 8
    
        MS Name/IP address Stratum Poll Reach LastRx Last sample
    
        ===============================================================================
    
        ^+ time.nullroutenetworks.c> 2 10 377 1007 -2241us[-2238us] +/- 33ms
    
        ^* voipmonitor.wci.com 2 10 377 47 +956us[ +958us] +/- 15ms
    
        ^- tick.srs1.ntfo.org 3 10 177 801 -3429us[-3427us] +/- 100ms
        ```

6. A rendszer frissítése
    1. Először telepítse a legújabb frissítéseket a rendszerre, mielőtt elkezdené az SBD-eszköz telepítését.
    1. Ha nem szeretné a rendszer teljes frissítését, még ha ajánlott is, legalább az alábbi csomagokat frissítse.
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```

7. Telepítse a SAP HANA RHEL-HA adattárakat.

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. Telepítse a Pacemaker, SBD, OpenIPMI, ipmitool és fencing_sbd összes csomópontra.

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitool
    ```

  ## <a name="configure-watchdog"></a>A Watchdog konfigurálása

Ebből a szakaszból megtudhatja, hogyan konfigurálhatja a Watchdogot. Ebben a szakaszban ugyanazt a két gazdagépet (és) használjuk, amelyekre a cikk elején `sollabdsm35` `sollabdsm36` hivatkozunk.

1. Győződjön meg arról, hogy a watchdog démon nem fut semmilyen rendszeren.
    ```
    [root@sollabdsm35 ~]# systemctl disable watchdog
    [root@sollabdsm36 ~]# systemctl disable watchdog
    [root@sollabdsm35 ~]# systemctl stop watchdog
    [root@sollabdsm36 ~]# systemctl stop watchdog
    [root@sollabdsm35 ~]# systemctl status watchdog

    ● watchdog.service - watchdog daemon

    Loaded: loaded (/usr/lib/systemd/system/watchdog.service; disabled;
    vendor preset: disabled)

    Active: inactive (dead)

    Nov 28 23:02:40 sollabdsm35 systemd[1]: Collecting watchdog.service

    ```

2. Az alapértelmezett Linux Watchdog, amely a telepítés során lesz telepítve, az iTCO Watchdog, amelyet az UCS és a HPE SDFlex rendszerek nem támogatnak. Ezért ezt a figyelőőrt le kell tiltani.
    1. Nem a megfelelő watchdog van telepítve és betöltve a rendszeren:
       ```
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. A nem megfelelő illesztőprogram eltávolítása a környezetből:
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. Annak érdekében, hogy az illesztőprogram ne legyen betöltve a következő rendszerindítás során, az illesztőprogramnak blokkolva kell lennie. Az iTCO-modulok blokkolásához adja hozzá a következőket a fájl `50-blacklist.conf` végéhez:
       ```
       sollabdsm35:~ # vi /etc/modprobe.d/50-blacklist.conf
   
        unload the iTCO watchdog modules
   
       blacklist iTCO_wdt
   
       blacklist iTCO_vendor_support
       ```
    4. Másolja a fájlt a másodlagos gazdagépre.
       ```
       sollabdsm35:~ # scp /etc/modprobe.d/50-blacklist.conf sollabdsm35:
       /etc/modprobe.d/50-blacklist.conf
       ```  

    5. Ellenőrizze, hogy az ipmi szolgáltatás elindult-e. Fontos, hogy az IPMI-időzítő ne fut. Az időzítő kezelése az SBD Pacemaker szolgáltatásból történik.
       ```
       sollabdsm35:~ # ipmitool mc watchdog get
   
       Watchdog Timer Use: BIOS FRB2 (0x01)
   
       Watchdog Timer Is: Stopped
   
       Watchdog Timer Actions: No action (0x00)
   
       Pre-timeout interval: 0 seconds
   
       Timer Expiration Flags: 0x00
   
       Initial Countdown: 0 sec
   
       Present Countdown: 0 sec
   
       ``` 

3. Alapértelmezés szerint a szükséges eszköz a /dev/watchdog nem jön létre.

    ```
    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. Konfigurálja az IPMI Watchdogot.

    ``` 
    sollabdsm35:~ # mv /etc/sysconfig/ipmi /etc/sysconfig/ipmi.org

    sollabdsm35:~ # vi /etc/sysconfig/ipmi

    IPMI_SI=yes
    DEV_IPMI=yes
    IPMI_WATCHDOG=yes
    IPMI_WATCHDOG_OPTIONS="timeout=20 action=reset nowayout=0
    panic_wdt_timeout=15"
    IPMI_POWEROFF=no
    IPMI_POWERCYCLE=no
    IPMI_IMB=no
    ```
5. Másolja a watchdog konfigurációs fájlt a másodlagosba.
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  Engedélyezze és indítsa el az ipmi szolgáltatást.
    ```
    [root@sollabdsm35 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm35 ~]# systemctl start ipmi

    [root@sollabdsm36 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm36 ~]# systemctl start ipmi
    ```
     Most az IPMI szolgáltatás elindult, és létrejött a /dev/watchdog eszköz – de az időzítő továbbra is leáll. Később az SBD kezeli a watchdog alaphelyzetbe állítását, és engedélyezi az IPMI-időzítőt.
7.  Ellenőrizze, hogy a /dev/watchdog létezik-e, de nincs-e használatban.
    ```
    [root@sollabdsm35 ~]# ipmitool mc watchdog get
    Watchdog Timer Use: SMS/OS (0x04)
    Watchdog Timer Is: Stopped
    Watchdog Timer Actions: No action (0x00)
    Pre-timeout interval: 0 seconds
    Timer Expiration Flags: 0x10
    Initial Countdown: 20 sec
    Present Countdown: 20 sec

    [root@sollabdsm35 ~]# ls -l /dev/watchdog
    crw------- 1 root root 10, 130 Nov 28 23:12 /dev/watchdog
    [root@sollabdsm35 ~]# lsof /dev/watchdog
    ```

## <a name="sbd-configuration"></a>SBD-konfiguráció
Ebben a szakaszban az SBD konfigurálásán ismerkedik meg. Ebben a szakaszban ugyanazt a két gazdagépet (és) használjuk, amelyekre a cikk `sollabdsm35` `sollabdsm36` elején hivatkozunk.

1.  Győződjön meg arról, hogy az iSCSI- vagy FC-lemez mindkét csomóponton látható. Ez a példa FC-alapú SBD-eszközt használ. Az SBD-elkerítéssel kapcsolatos további információkért lásd: Tervezési útmutató az RHEL magas rendelkezésre állású fürtökhöz [– SBD-szempontok.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Faccess.redhat.com%2Farticles%2F2941601&data=04%7C01%7Cralf.klahr%40microsoft.com%7Cd49d7a3e3871449cdecc08d8c77341f1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637478645171139432%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=c%2BUAC5gmgpFNWZCQFfiqcik8CH%2BmhH2ly5DsOV1%2FE5M%3D&reserved=0)
2.  A LUN-ID azonosítónak minden csomóponton azonosnak kell lennie.
  
3.  Ellenőrizze az SBD-eszköz többcsatornás állapotát.
    ```
    multipath -ll
    3600a098038304179392b4d6c6e2f4b62 dm-5 NETAPP ,LUN C-Mode
    size=1.0G features='4 queue_if_no_path pg_init_retries 50
    retain_attached_hw_handle' hwhandler='1 alua' wp=rw
    |-+- policy='service-time 0' prio=50 status=active
    | |- 8:0:1:2 sdi 8:128 active ready running
    | `- 10:0:1:2 sdk 8:160 active ready running
    `-+- policy='service-time 0' prio=10 status=enabled
    |- 8:0:3:2 sdj 8:144 active ready running
    `- 10:0:3:2 sdl 8:176 active ready running
    ```

4.  Az SBD-lemezek létrehozása és a fürt primitív elkerítésének beállítása. Ezt a lépést az első csomóponton kell végrehajtani.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  Másolja az SBD-konfigurációt a node2 csomópontra.
    ```
    vi /etc/sysconfig/sbd

    SBD_DEVICE="/dev/mapper/3600a09803830417934d6c6e2f4b62" 
    SBD_PACEMAKER=yes
    SBD_STARTMODE=always
    SBD_DELAY_START=no
    SBD_WATCHDOG_DEV=/dev/watchdog
    SBD_WATCHDOG_TIMEOUT=15
    SBD_TIMEOUT_ACTION=flush,reboot
    SBD_MOVE_TO_ROOT_CGROUP=auto
    SBD_OPTS=

    scp /etc/sysconfig/sbd node2:/etc/sysconfig/sbd
    ```

6.  Ellenőrizze, hogy az SBD-lemez látható-e mindkét csomóponton.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 dump

    ==Dumping header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62

    Header version : 2.1

    UUID : ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce

    Number of slots : 255
    Sector size : 512
    Timeout (watchdog) : 5
    Timeout (allocate) : 2
    Timeout (loop) : 1
    Timeout (msgwait) : 10

    ==Header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is dumped
    ```

7.  Adja hozzá az SBD-eszközt az SBD konfigurációs fájlhoz.

    ```
    # SBD_DEVICE specifies the devices to use for exchanging sbd messages
    # and to monitor. If specifying more than one path, use ";" as
    # separator.
    #

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    ## Type: yesno
     Default: yes
     # Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>Fürt inicializálása
Ebben a szakaszban inicializálja a fürtöt. Ebben a szakaszban ugyanazt a két gazdagépet (és) használjuk, amelyekre a cikk elején `sollabdsm35` `sollabdsm36` hivatkozunk.

1.  Állítsa be a fürt felhasználói jelszavát (az összes csomópontot).
    ```
    passwd hacluster
    ```
2.  Indítsa el a PCS-t minden rendszeren.
    ```
    systemctl enable pcsd
    ```
  

3.  Állítsa le a tűzfalat, és tiltsa le (minden csomóponton).
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  Indítsa el a pcsd szolgáltatást.
    ```
    systemctl start pcsd
    ```

5.  A fürthitelesítést csak a node1 csomópontról futtassa.

    ```
    pcs cluster auth sollabdsm35 sollabdsm36

        Username: hacluster

            Password:
            sollabdsm35.localdomain: Authorized
            sollabdsm36.localdomain: Authorized

     ``` 

6.  A fürt létrehozása.
    ```
    pcs cluster setup --start --name hana sollabdsm35 sollabdsm36
    ```
  

7.  Ellenőrizze a fürt állapotát.

    ```
    pcs cluster status

    Cluster name: hana

    WARNINGS:

    No stonith devices and stonith-enabled is not false

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.20-5.el7_7.2-3c4c782f70) -
    partition with quorum

    Last updated: Sat Nov 28 20:56:57 2020

    Last change: Sat Nov 28 20:54:58 2020 by hacluster via crmd on
    sollabdsm35

    2 nodes configured

    0 resources configured

    Online: [ sollabdsm35 sollabdsm36 ]

    No resources

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/disabled
    ```

8. Ha egy csomópont nem csatlakozik a fürthöz, ellenőrizze, hogy fut-e még a tűzfal.

9. Az SBD-eszköz létrehozása és engedélyezése
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  
10. Állítsa le a fürtöt, és indítsa újra a fürtszolgáltatást (minden csomóponton).

    ```
    pcs cluster stop --all
    ```

11. Indítsa újra a fürtszolgáltatásokat (az összes csomóponton).

    ```
    systemctl stop pcsd
    systemctl stop pacemaker
    systemctl stop corocync
    systemctl enable sbd
    systemctl start corosync
    systemctl start pacemaker
    systemctl start pcsd
    ```

12. A Corosync-nek el kell kezdenie az SBD-szolgáltatást.

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. Indítsa újra a fürtöt (ha nem indul el automatikusan a pcsd-ről).

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. Engedélyezze a Stonith-beállításokat.
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. Ellenőrizze az új fürt állapotát egy erőforrással.
    ```
    pcs status

    Cluster name: hana

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.16-12.el7-94ff4df) - partition
    with quorum

    Last updated: Tue Oct 16 01:50:45 2018

    Last change: Tue Oct 16 01:48:19 2018 by root via cibadmin on
    sollabdsm35

    2 nodes configured

    1 resource configured

    Online: [ sollabdsm35 sollabdsm36 ]

    Full list of resources:

    SBD (stonith:fence_sbd): Started sollabdsm35

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/enabled

    sbd: active/enabled

    [root@node1 ~]#
    ```
  

16. Most le kell futnia az IPMI-időzítőnek, és az sbd-nek meg kell nyitnia a /dev/watchdog eszközt.

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm35 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. Ellenőrizze az SBD állapotát.

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. Tesztelje az SBD-elkerítést a kernel összeomlása által.

    * Aktiválja a kernel összeomlását.

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * A futtatás második tesztje egy csomópont körülkerítése PCS-parancsokkal.

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. A fürtözés további SAP HANA letilthatja a STONITH-t a következő beállítással:

   * pcs tulajdonságkészlet `stonith-enabled=false`
   * Néha könnyebb inaktiválni a STONITH-t a fürt telepítése során, mert elkerülheti a rendszer váratlan újraindítását.
   * A produktív használathoz ezt a paramétert true (igaz) értékre kell állítani. Ha ez a paraméter nem true (igaz) értékre van állítva, a fürt nem támogatott.
   * pcs tulajdonságkészlet `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>HANA-integráció a fürtbe

Ebben a szakaszban a HANA-t integrálja a fürtbe. Ebben a szakaszban ugyanazt a két gazdagépet (és) használjuk, amelyekre a cikk `sollabdsm35` `sollabdsm36` elején hivatkozunk.

Két lehetőség van a HANA integrálására. Az első lehetőség egy költségoptimalált megoldás, amelyben a másodlagos rendszerrel futtathatja a QAS-rendszert. Ez a módszer nem ajánlott, mivel nem hagy rendszert a fürtszoftver, az operációs rendszer vagy a HANA frissítésének tesztelésére, és a konfigurációs frissítések a PRD-rendszer nem tervezett leállását is vezethetik. Emellett ha a PRD-rendszert aktiválni kell a másodlagos rendszeren, a QAS-t le kell kapcsolni a másodlagos csomóponton. A második lehetőség a QAS rendszer telepítése az egyik fürtre, és egy második fürt használata a prD-hez. Ez a beállítás azt is lehetővé teszi, hogy az összes összetevőt tesztelje, mielőtt azok éles környezetbe kerülnek. Ez a cikk bemutatja, hogyan konfigurálhatja a második lehetőséget.


* Ez a folyamat az RHEL leírásának buildszáma az oldalon:

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>A HSR konfigurálásának lépései

1.  Ezeket a műveleteket kell végrehajtani a node1 (elsődleges) csomóponton.
    1. Győződjön meg arról, hogy az adatbázis napló üzemmódja normálra van állítva.

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p $YourPass -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. SAP HANA rendszerreplikáció csak a kezdeti biztonsági mentés után fog működni. A következő parancs létrehoz egy kezdeti biztonsági mentést a `/tmp/` könyvtárban. Válassza ki az adatbázishoz megfelelő biztonsági mentési fájlrendszert. 
       ```
       * hdbsql -i 00 -u system -p $YourPass "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```  

    3. Az adatbázis összes adatbázistárolója biztonsági mentése.
       ``` 
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"     
   
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. Engedélyezze a HSR-folyamatot a forrásrendszeren.
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. Ellenőrizze az elsődleges rendszer állapotát.
       ```
       hdbnsutil -sr_state
    
       System Replication State
   
   
       online: true
   
       mode: primary
   
       operation mode: primary
   
       site id: 1
   
       site name: DC1
   
       
   
       is source system: true
   
       is secondary/consumer system: false
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       
   
       Host Mappings:
   
       ~~~~~~~~~~~~~~
   
       Site Mappings:
   
       ~~~~~~~~~~~~~~
   
       DC1 (primary/)
   
       Tier of DC1: 1
   
       Replication mode of DC1: primary
   
       Operation mode of DC1:
   
       done.
       ```

 2. Ezeket a műveleteket kell végrehajtani a node2 (másodlagos) csomóponton.
     1. Állítsa le az adatbázist.
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. Csak az SAP HANA2.0 esetén másolja a SAP HANA rendszerből és fájlokból az elsődleges csomópontról `PKI SSFS_HR2.KEY` `SSFS_HR2.DAT` a másodlagos csomópontra.
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. Engedélyezze a másodlagos replikációs helyet replikációs helyként.
       ``` 
       su - hr2adm
   
       hdbnsutil -sr_register --remoteHost=node1 --remoteInstance=00
       --replicationMode=syncmem --name=DC2
   
       
   
       adding site ...
   
       --operationMode not set; using default from global.ini/[system_replication]/operation_mode: logreplay
   
       nameserver node2:30001 not responding.
   
       collecting information ...
   
       updating local ini files ...
   
       done.
   
       ```

     4. Indítsa el az adatbázist.
       ```
       sapcontrol -nr 00 -function StartSystem 
       ```
    
     5. Ellenőrizze az adatbázis állapotát.
       ```
       hdbnsutil -sr_state
   
       ~~~~~~~~~
       System Replication State
   
       online: true
   
       mode: syncmem
   
       operation mode: logreplay
   
       site id: 2
   
       site name: DC2   
   
       is source system: false
   
       is secondary/consumer system: true
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       active primary site: 1
   
       
   
       primary primarys: node1
   
       Host Mappings:
   
   
   
       node2 -> [DC2] node2
   
       node2 -> [DC1] node1
   
       
   
       Site Mappings:
   
   
   
       DC1 (primary/primary)
   
       |---DC2 (syncmem/logreplay)
   
       
   
       Tier of DC1: 1
   
       Tier of DC2: 2
   
       
   
       Replication mode of DC1: primary
   
       Replication mode of DC2: syncmem
   
       Operation mode of DC1: primary
   
       Operation mode of DC2: logreplay
   
       
   
       Mapping: DC1 -> DC2
   
       done.
       ~~~~~~~~~~~~~~
       ```

3. A replikáció állapotával kapcsolatos további információk is lekért:
    ```
    ~~~~~
    hr2adm@node1:/usr/sap/HR2/HDB00> python
    /usr/sap/HR2/HDB00/exe/python_support/systemReplicationStatus.py

    | Database | Host | Port | Service Name | Volume ID | Site ID | Site
    Name | Secondary | Secondary | Secondary | Secondary | Secondary |
    Replication | Replication | Replication |

    | | | | | | | | Host | Port | Site ID | Site Name | Active Status |
    Mode | Status | Status Details |

    | SYSTEMDB | node1 | 30001 | nameserver | 1 | 1 | DC1 | node2 | 30001
    | 2 | DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30007 | xsengine | 2 | 1 | DC1 | node2 | 30007 | 2 |
    DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30003 | indexserver | 3 | 1 | DC1 | node2 | 30003 | 2
    | DC2 | YES | SYNCMEM | ACTIVE | |


    status system replication site "2": ACTIVE

    overall system replication status: ACTIVE

    Local System Replication State
    

    mode: PRIMARY

    site id: 1

    site name: DC1
    ```
  

#### <a name="log-replication-mode-description"></a>Naplóreplikációs mód leírása

A naplóreplikációs móddal kapcsolatos további információkért tekintse meg a hivatalos [SAP-dokumentációt.](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/627bd11e86c84ec2b9fcdf585d24011c.html)
  

#### <a name="network-setup-for-hana-system-replication"></a>Hálózat beállítása a HANA rendszerreplikációhoz


Annak biztosításához, hogy a replikációs forgalom a megfelelő VLAN-t használja a replikációhoz, azt megfelelően kell konfigurálni a `global.ini` fájlban. Ha kihagyja ezt a lépést, a HANA a VLAN hozzáférési VLAN-t fogja használni a replikációhoz, ami lehet, hogy nem szükséges.


Az alábbi példák a másodlagos helybe történő rendszerreplikáció gazdagépnév-feloldási konfigurációját mutatják be. Három különböző hálózat azonosítható:

* Nyilvános hálózat 10.0.1-es tartományú címekkel.*

* Belső hálózati SAP HANA gazdagépek közötti kommunikációhoz az egyes telephelyek között: 192.168.1.*

* Dedikált hálózat a rendszerreplikációhoz: 10.5.1.*

Az első példában a paraméter értéke , és csak a szomszédos replikálási hely gazdagépe `[system_replication_communication]listeninterface` `.global` van megadva.

A következő példában a paraméter értéke , és mindkét hely `[system_replication_communication]listeninterface` `.internal` összes gazdagépe meg van adva.

  

További információ: Hálózati konfiguráció SAP HANA [rendszerreplikációhoz.](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)

  

Rendszerreplikáció esetén nem szükséges szerkeszteni a fájlt, a belső (virtuális) gazdagépneveket a fájlban lévő IP-címekre kell leképezni egy dedikált hálózat létrehozásához `/etc/hosts` `global.ini` a rendszerreplikációhoz. Ennek szintaxisa a következő:

global.ini

[system_replication_hostname_resolution]

<ip-address_site>=<belső gazdagép-name_site>


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>A SAP HANA pacemaker-fürtben való konfigurálás
Ebben a szakaszban megtudhatja, hogyan konfigurálhatja a SAP HANA egy Pacemaker-fürtben. Ebben a szakaszban ugyanazt a két gazdagépet (és) használjuk, amelyekre a cikk elején `sollabdsm35` `sollabdsm36` hivatkozunk.

Győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:  

* A Pacemaker-fürt a dokumentációnak megfelelően van konfigurálva, és megfelelő és működő elkerítési eljárásokkal rendelkezik

* SAP HANA rendszerindításkor le van tiltva az összes fürtcsomóponton, mivel az indítást és a leállítást a fürt kezeli

* SAP HANA SAP-eszközök használatával történő rendszerreplikáció és -feladatátvétel megfelelően működik a fürtcsomópontok között

* SAP HANA a fürt által mindkét fürtcsomópontról használható figyelési fiókot tartalmaz

* Mindkét csomópont elő van fizetve a "Magas rendelkezésre állás" és a "RHEL for SAP HANA" (RHEL 6,RHEL 7) csatornákra

  

* Általánosságban elmondható, hogy az összes pcs-parancsot csak a csomóponton hajtsa végre, mert a CIB automatikusan frissül a pcs-rendszerhéjból.

* [További információ a kvórum szabályzatról](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>A konfigurálás lépései 
1. Számítógépek konfigurálása.
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  Konfigurálja a corosyncet.
    További információ: [How can I configure my RHEL 7 High Availability Cluster with pacemaker and corosync (Az RHEL 7 magas](https://access.redhat.com/solutions/1293523)rendelkezésre állású fürt konfigurálása pacemakerrel és corosync használatával).
    ```
    cat /etc/corosync/corosync.conf

    totem {

    version: 2

    secauth: off

    cluster_name: hana

    transport: udpu

    }

    

    nodelist {

    node {

    ring0_addr: node1.localdomain

    nodeid: 1

    }

    

    node {

    ring0_addr: node2.localdomain

    nodeid: 2

    }

    }

    

    quorum {

    provider: corosync_votequorum

    two_node: 1

    }

    

    logging {

    to_logfile: yes

    logfile: /var/log/cluster/corosync.log

    to_syslog: yes

    }

    ```
  

3.  Klónozott SAPHanaTopology erőforrás létrehozása.
    Az SAPHanaTopology erőforrás az egyes csomópontok SAP HANA rendszerreplikáció állapotát és konfigurációját gyűjti. Az SAPHanaTopology használatához a következő attribútumokat kell konfigurálni.
       ```
       pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 InstanceNumber=00 --clone clone-max=2 clone-node-max=1    interleave=true
       ```

    | Attribútum neve | Description  |
    |---|---|
    | SID | A telepített SAP HANA SAP rendszerazonosítója (SID). Minden csomóponton azonosnak kell lennie. |
    | InstanceNumber (Példányszám) | Kétjegyű SAP-példányazonosító.|

    * Erőforrás állapota
       ```
       pcs resource show SAPHanaTopology_HR2_00
   
       InstanceNumber 2-digit SAP Instance identifier.
       pcs resource show SAPHanaTopology_HR2_00-clone
   
       Clone: SAPHanaTopology_HR2_00-clone
   
        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true
   
        Resource: SAPHanaTopology_HR2_00 (class=ocf provider=heartbeat
       type=SAPHanaTopology)
   
        Attributes: InstanceNumber=00 SID=HR2
   
        Operations: monitor interval=60 timeout=60
       (SAPHanaTopology_HR2_00-monitor-interval-60)
   
        start interval=0s timeout=180
       (SAPHanaTopology_HR2_00-start-interval-0s)
   
        stop interval=0s timeout=60 (SAPHanaTopology_HR2_00-stop-interval-0s)
   
       ```

4.  Elsődleges/másodlagos SAPHana-erőforrás létrehozása.
    * Az SAPHana-erőforrás feladata az adatbázis indítása, leállítása és SAP HANA áthelyezése. Ezt az erőforrást elsődleges/másodlagos fürterőforrásként kell futtatni. Az erőforrás a következő attribútumokkal rendelkezik.

| Attribútum neve            | Kötelező? | Alapértelmezett érték | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|---------------------------|-----------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SID                       | Yes       | Nincsenek          | A telepítés SAP-rendszerazonosítója (SID SAP HANA. Minden csomópontnak azonosnak kell lennie.                                                                                                                                                                                                                                                                                                                                                                                       |
| InstanceNumber (Példányszám)            | Yes       | Nincs          | Kétjegyű SAP-példányazonosító.                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| PREFER_SITE_TAKEOVER      | nem        | igen           | Érdemes-e a fürtnek az elsődleges helyi újraindítás helyett a másodlagos példányra váltania? ("nem": Helyi újraindítást részesít előnyben; "yes": Do preferover to remote site)                                                                                                                                                                                                                                                                                            |
|                           |           |               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| AUTOMATED_REGISTER        | nem        | HAMIS         | A korábbi elsődleges SAP HANA kell másodlagosként regisztrálni az átvételt és a DUPLICATE_PRIMARY_TIMEOUT? ("false": nem, manuális beavatkozásra lesz szükség; "true": igen, a korábbi elsődlegest az erőforrás-ügynök másodlagosként regisztrálja)                                                                                                                                                                                                                        |
| DUPLICATE_PRIMARY_TIMEOUT | nem        | 7200          | Kettős elsődleges helyzet esetén az elsődleges időbélyegek között szükséges időel különbség (másodpercben). Ha az időelomlódás kisebb, mint az időelvárás, akkor a fürt egy vagy mindkét példányt "WAITING" (VÁRAKOZÓ) állapotúként tartja. Ezzel lehetőséget ad a rendszergazdának, hogy reagáljon a feladatátvételre. Az idő különbségének eltelte után a rendszer regisztrál egy meghibásodott, korábbi elsődleges elsődleges et. Az új elsődleges kiszolgálóra való regisztrációt követően a rendszer minden adatot felülír a rendszerreplikációval. |

5.  Hozza létre a HANA-erőforrást.
    ```
    pcs resource create SAPHana_HR2_00 SAPHana SID=HR2 InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200   AUTOMATED_REGISTER=true primary notify=true clone-max=2 clone-node-max=1 interleave=true

    pcs resource show SAPHana_HR2_00-primary


    Primary: SAPHana_HR2_00-primary

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true notify=true

        Resource: SAPHana_HR2_00 (class=ocf provider=heartbeat type=SAPHana)

        Attributes: AUTOMATED_REGISTER=false DUPLICATE_PRIMARY_TIMEOUT=7200
    InstanceNumber=00 PREFER_SITE_TAKEOVER=true SID=HR2

        Operations: demote interval=0s timeout=320
    (SAPHana_HR2_00-demote-interval-0s)

        monitor interval=120 timeout=60 (SAPHana_HR2_00-monitor-interval-120)

        monitor interval=121 role=Secondary timeout=60
    (SAPHana_HR2_00-monitor-

        interval-121)

        monitor interval=119 role=Primary timeout=60 (SAPHana_HR2_00-monitor-

        interval-119)

        promote interval=0s timeout=320 (SAPHana_HR2_00-promote-interval-0s)

        start interval=0s timeout=180 (SAPHana_HR2_00-start-interval-0s)

        stop interval=0s timeout=240 (SAPHana_HR2_00-stop-interval-0s)

    
    
    crm_mon -A1

    ....

    2 nodes configured

    5 resources configured

    Online: [ node1.localdomain node2.localdomain ]

    Active resources:

    .....

    Node Attributes:

    * Node node1.localdomain:

    + hana_hr2_clone_state : PROMOTED

    + hana_hr2_remoteHost : node2

    + hana_hr2_roles : 4:P:primary1:primary:worker:primary

    + hana_hr2_site : DC1

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : PRIM

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node1

    + lpa_hr2_lpt : 1540866498

    + primary-SAPHana_HR2_00 : 150

    * Node node2.localdomain:

    + hana_hr2_clone_state : DEMOTED

    + hana_hr2_op_mode : logreplay

    + hana_hr2_remoteHost : node1

    + hana_hr2_roles : 4:S:primary1:primary:worker:primary

    + hana_hr2_site : DC2

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : SOK

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node2

    + lpa_hr2_lpt : 30

    + primary-SAPHana_HR2_00 : 100
    ```

6.  Virtuális IP-cím erőforrás létrehozása.
    A fürt virtuális IP-címet fog tartalmazni, hogy elérje a virtuális gép elsődleges SAP HANA. Az alábbi példaparancs IPaddr2-erőforrást hoz létre a 10.7.0.84/24 IP-címmel.
    ```
    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  Korlátozások létrehozása.
    * A megfelelő működés érdekében az SAPHanaTopology erőforrásokat az SAPHana-erőforrások indítása előtt el kell indítottuk, valamint meg kell győződnünk arról, hogy a virtuális IP-cím jelen van a csomóponton, ahol az SAPHana elsődleges erőforrása fut. Ehhez az alábbi két korlátozást kell létrehozni.
       ```
       pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
       pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
       ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>Az SAPHana-erőforrás másik csomópontra való manuális áthelyezésének tesztelése

#### <a name="sap-hana-takeover-by-cluster"></a>(SAP Hana-feladatátvétel fürtönként)


Az SAPHana erőforrás egyik csomópontról egy másikra való áthelyezésének tesztelésére használja az alábbi parancsot. Vegye figyelembe, hogy az SAPHana erőforrás belső működése miatt a következő parancs futtatásakor ne használja `--primary` a kapcsolót.
```pcs resource move SAPHana_HR2_00-primary```

Az egyes pcs erőforrás-áthelyezési parancsok meghívása után a fürt helykorlátozásokat hoz létre az erőforrás áthelyezésének megvalósításához. Ezeket a korlátozásokat el kell távolítani, hogy a jövőben lehetővé tegye az automatikus feladatátvételt.
Az eltávolításukhoz használhatja a következő parancsot.
```
pcs resource clear SAPHana_HR2_00-primary
crm_mon -A1
Node Attributes:
    * Node node1.localdomain:
    + hana_hr2_clone_state : DEMOTED
    + hana_hr2_remoteHost : node2
    + hana_hr2_roles : 2:P:primary1::worker:
    + hana_hr2_site : DC1
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : PRIM
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node1
    + lpa_hr2_lpt : 1540867236
    + primary-SAPHana_HR2_00 : 150
    * Node node2.localdomain:
    + hana_hr2_clone_state : PROMOTED
    + hana_hr2_op_mode : logreplay
    + hana_hr2_remoteHost : node1
    + hana_hr2_roles : 4:S:primary1:primary:worker:primary
    + hana_hr2_site : DC2
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : SOK
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node2
    + lpa_hr2_lpt : 1540867311
    + primary-SAPHana_HR2_00 : 100
```
  

* Jelentkezzen be a HANA-hoz ellenőrzésként.

  * lefokozásos gazdagép:

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * Előléptetett gazdagép:

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

A kapcsolóval `AUTOMATED_REGISTER=false` nem válthat oda-vissza.

Ha ez a beállítás false (hamis) értéket ad meg, akkor újra regisztrálnia kell a csomópontot:
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```

Most a node2, amely az elsődleges volt, másodlagos gazdagépként működik.

Érdemes lehet true (igaz) beállítással automatizálni a lefokozásos gazdagép regisztrációját.
  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true
pcs cluster node clear node1
```

Az, hogy az automatikus regisztrációt részesíti-e előnyben, az ügyfél forgatókönyvétől függ. Az átvételt követően a csomópont automatikus regisztrációja egyszerűbb lesz az üzemeltetési csapat számára. Előfordulhat azonban, hogy manuálisan szeretné regisztrálni a csomópontot, hogy először további teszteket futtatjon, hogy biztosan minden a vártnak megfelelő legyen.
