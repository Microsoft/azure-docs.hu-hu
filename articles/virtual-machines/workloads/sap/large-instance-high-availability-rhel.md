---
title: Azure nagyméretű példányok magas rendelkezésre állása SAP on RHEL
description: Megtudhatja, hogyan automatizálhat egy SAP HANA adatbázis-feladatátvételt egy pacemaker-fürt használatával Red Hat Enterprise Linuxban.
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 99e9994d01e4579bf6ef2e369e0fe85c48af52ef
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182434"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>Azure nagyméretű példányok magas rendelkezésre állása SAP on RHEL

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a pacemaker-fürtöt a RHEL 7,6-ben egy SAP HANA adatbázis feladatátvételének automatizálásához. A jelen útmutató lépéseinek elvégzéséhez ismernie kell a Linux, a SAP HANA és a pacemaker megfelelő ismeretét.

A következő táblázat tartalmazza az ebben a cikkben használt állomásnevek nevét. A cikkben szereplő kódrészletek megjelenítik a futtatandó parancsokat, valamint a parancsok kimenetét. Ügyeljen arra, hogy az egyes parancsok melyik csomópontra legyenek hivatkozva.

| Típus | Állomásnév | Csomópont|
|-------|-------------|------|
|Elsődleges gazdagép|`sollabdsm35`|1. csomópont|
|Másodlagos gazdagép|`sollabdsm36`|2. csomópont|

## <a name="configure-your-pacemaker-cluster"></a>A pacemaker-fürt konfigurálása


A fürt konfigurálásának megkezdése előtt állítsa be az SSH-kulcscsere lehetőséget a csomópontok közötti megbízhatósági kapcsolat létrehozásához.

1. A következő parancsokkal azonos módon hozható létre `/etc/hosts` mindkét csomóponton.

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    0.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    0.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    0.20.251.150 sollabdsm36-st

    10.20.251.151 sollabdsm35-st

    

    10.20.252.151 sollabdsm36-back

    10.20.252.150 sollabdsm35-back

    

    10.20.253.151 sollabdsm36-node

    10.20.253.150 sollabdsm35-node

    ```

2.  Hozza létre és cserélje le az SSH-kulcsokat.
    1. Ssh-kulcsok generálása.

       ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
       ```
    2. Másolja a kulcsokat a többi gazdagépre a jelszóval nem rendelkező SSH-hoz.
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  Tiltsa le a SELinux mindkét csomóponton.
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. Indítsa újra a kiszolgálókat, majd az alábbi parancs használatával ellenőrizze a SELinux állapotát.
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. Konfigurálja az NTP (Network Time Protocol) protokollt. A fürtcsomópontok időtartamának és időzónájának egyeznie kell. A `chrony.conf` fájl tartalmának megnyitásához és ellenőrzéséhez használja a következő parancsot.
    1. A konfigurációs fájlba a következő tartalmakat kell hozzáadni. Módosítsa a tényleges értékeket a környezetének megfelelően.
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

6. A System frissítése
    1. Először telepítse a rendszer legújabb frissítéseit a SBD-eszköz telepítése előtt.
    1. Ha nem szeretné, hogy a rendszer teljes frissítése legyen, még akkor is, ha az ajánlott, legalább a következő csomagokat kell frissítenie.
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```
 

7. Telepítse a SAP HANA és a RHEL-HA adattárakat.

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. Telepítse a pacemaker, a SBD, a OpenIPMI, a ipmitools és a fencing_sbd eszközöket az összes csomópontra.

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitools
    ```

  ## <a name="configure-watchdog"></a>A watchdog konfigurálása

Ebből a szakaszból megtudhatja, hogyan konfigurálhatja a watchdogot. Ez a szakasz ugyanazokat a két gazdagépet használja, `sollabdsm35` és a `sollabdsm36` cikk elején hivatkozik rá.

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

2. A telepítés során telepítendő alapértelmezett Linux watchdog a iTCO watchdog, amelyet az FKR és a HPE SDFlex Systems nem támogat. Ezért ezt a watchdogot le kell tiltani.
    1. Helytelen watchdog van telepítve és betöltve a rendszeren:
       ```
   
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. Távolítsa el a helytelen illesztőprogramot a környezetből:
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. Annak biztosítása érdekében, hogy az illesztőprogram ne legyen betöltve a következő rendszerindításkor, blocklisted kell lennie. A iTCO modulok Blocklist adja hozzá a következőt a fájl végéhez `50-blacklist.conf` :
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

    5. Ellenőrizze, hogy elindult-e a IPMI szolgáltatás. Fontos, hogy a IPMI időzítője ne fusson. Az időzítő kezelése a SBD pacemaker szolgáltatásból történik.
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

3. Alapértelmezés szerint a rendszer nem hozza létre a szükséges eszközt a/dev/watchdog.

    ```
    No watchdog device was created

    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. Konfigurálja a IPMI watchdogot.

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
5. Másolja a watchdog konfigurációs fájlját a másodlagosra.
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  Engedélyezze és indítsa el a IPMI szolgáltatást.
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
     Most a IPMI szolgáltatás elindult, és az eszköz/dev/watchdog jön létre – az időzítő azonban továbbra is leáll. Később a SBD fogja kezelni a watchdog alaphelyzetbe állítását, és engedélyezi a IPMI időzítőt.
7.  Győződjön meg arról, hogy a/dev/watchdog létezik, de nincs használatban.
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
Ebből a szakaszból megtudhatja, hogyan konfigurálhatja a SBD. Ez a szakasz ugyanazokat a két gazdagépet használja, `sollabdsm35` és a `sollabdsm36` cikk elején hivatkozik rá.

1.  Győződjön meg arról, hogy az iSCSI-vagy az FC-lemez mindkét csomóponton látható. Ez a példa egy FC-alapú SBD-eszközt használ. A SBD-kerítéssel kapcsolatos további információkért tekintse meg a [dokumentációt](http://www.linux-ha.org/wiki/SBD_Fencing).
2.  A LUN-AZONOSÍTÓnak azonosnak kell lennie az összes csomóponton.
  
3.  A SBD-eszköz többutas állapotának megkeresése.
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

4.  Hozza létre a SBD-lemezeket, és állítsa be a fürt primitív kerítését. Ezt a lépést az első csomóponton kell végrehajtani.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  Másolja a SBD-konfigurációt a Csomópont2-be.
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

6.  Győződjön meg arról, hogy a SBD lemez mindkét csomópontról látható.
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

7.  Adja hozzá a SBD eszközt a SBD konfigurációs fájlhoz.

    ```
    \# SBD_DEVICE specifies the devices to use for exchanging sbd messages

    \# and to monitor. If specifying more than one path, use ";" as

    \# separator.

    \#

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    \## Type: yesno
     Default: yes
     \# Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>Fürt inicializálása
Ebben a szakaszban inicializálja a fürtöt. Ez a szakasz ugyanazokat a két gazdagépet használja, `sollabdsm35` és a `sollabdsm36` cikk elején hivatkozik rá.

1.  A fürt felhasználói jelszavának (minden csomópont) beállítása.
    ```
    passwd hacluster
    ```
2.  SZÁMÍTÓGÉPEK elindítása minden rendszeren.
    ```
    systemctl enable pcsd
    ```
  

3.  Állítsa le a tűzfalat, és tiltsa le (az összes csomóponton).
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  Indítsa el a pcsd szolgáltatást.
    ```
    systemctl start pcsd
    ```
  
  

5.  A fürt hitelesítését csak a csomópont1 futtassa.

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
  

7.  Keresse meg a fürt állapotát.

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

8. Ha az egyik csomópont nem csatlakozik a fürthöz, ellenőrizze, hogy a tűzfal továbbra is fut-e.

  

9. A SBD-eszköz létrehozása és engedélyezése
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  

10. Állítsa le a fürtöt a fürtszolgáltatások újraindításával (az összes csomóponton).

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

12. A Corosync el kell indítania a SBD szolgáltatást.

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. Indítsa újra a fürtöt (ha nem indul el automatikusan a pcsd).

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. Stonith beállításainak engedélyezése.
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. Győződjön meg arról, hogy az új fürt állapota most egy erőforrás.
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
  

16. Most a IPMI időzítőnek futnia kell, és a/dev/watchdog eszközt a SBD-nek kell megnyitnia.

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm351 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. Keresse meg a SBD állapotát.

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. Tesztelje a SBD-kerítést a kernel összeomlásával.

    * A kernel összeomlásának kiváltása.

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * A második teszt futtatása a csomópontok számítógép-parancsok használatával történő kerítése.

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. A SAP HANA-fürtözés további részében a következő beállítással tilthatja le a STONITH:

   * számítógépek tulajdonság beállítása `stonith-enabled=false`
   * Ezt a paramétert igaz értékre kell beállítani a produktív használathoz. Ha a paraméter értéke nem True (igaz), a fürt nem lesz támogatott.
   * számítógépek tulajdonság beállítása `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>HANA-integráció a fürtbe

Ebben a szakaszban a HANA-t integrálja a fürtbe. Ez a szakasz ugyanazokat a két gazdagépet használja, `sollabdsm35` és a `sollabdsm36` cikk elején hivatkozik rá.

A HANA integrálására két lehetőség áll rendelkezésre. Az első lehetőség egy költségtakarékos megoldás, amely a másodlagos rendszer használatával futtathatja a QAS rendszerét. Ezt a metódust nem javasoljuk, mert nem hagy a rendszertől a fürt szoftverén, az operációs rendszeren vagy a HANA-on lévő frissítések tesztelését, és a konfigurációs frissítések a PRD rendszer nem tervezett leállásához vezethetnek. Továbbá, ha a PRD rendszer aktiválása szükséges a másodlagos rendszeren, a QAS le kell állítani a másodlagos csomóponton. A második lehetőség a QAS rendszer telepítése egy fürtön, és egy második fürt használata a PRD. Ez a beállítás lehetővé teszi az összes összetevő tesztelését az éles környezetbe helyezés előtt. Ez a cikk bemutatja, hogyan konfigurálhatja a második lehetőséget.


* Ez a folyamat a RHEL leírásának összeállítása a következő oldalon:

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>A HSR konfigurálásának lépései

1.  Ezek a csomópont1 végrehajtandó műveletek (elsődleges).
    1. Győződjön meg arról, hogy az adatbázis-naplózási mód normál értékre van beállítva.

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p SAPhana10 -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. SAP HANA a rendszer replikálása csak a kezdeti biztonsági mentés végrehajtása után fog működni. A következő parancs létrehoz egy kezdeti biztonsági másolatot a `/tmp/` címtárban. Válasszon ki egy megfelelő biztonsági mentési állományrendszert az adatbázishoz. 
       ```
       * hdbsql -i 00 -u system -p SAPhana10 "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```
    

    3. Az adatbázis összes adatbázis-tárolójának biztonsági mentése.
       ```
   
       * hdbsql -i 00 -u system -p SAPhana10 -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"
   
       
   
       * hdbsql -i 00 -u system -p SAPhana10 -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. Engedélyezze a HSR folyamatot a forrásoldali rendszeren.
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. Az elsődlegesrendszer állapotának megkeresése.
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

 2. Ezek a Csomópont2 végrehajtandó műveletek (másodlagos).
     1. Állítsa le az adatbázist.
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. Csak SAP HANA 2.0 esetében másolja a SAP HANA rendszer `PKI SSFS_HR2.KEY` és a `SSFS_HR2.DAT` fájlok elsődleges csomópontról másodlagos csomópontra.
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. A másodlagos engedélyezése replikációs helyként.
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
    
     5. Keresse meg az adatbázis állapotát.
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

3. További információkat is megtudhat a replikálás állapotáról:
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
  

#### <a name="log-replication-mode-description"></a>Naplózási replikálási mód leírása

A naplózási replikációs móddal kapcsolatos további információkért tekintse meg a [hivatalos SAP dokumentációját](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/c039a1a5b8824ecfa754b55e0caffc01.html).
  

#### <a name="network-setup-for-hana-system-replication"></a>A HANA rendszer replikálásának hálózati beállítása


Annak biztosítása érdekében, hogy a replikálási forgalom a megfelelő VLAN-t használja a replikációhoz, megfelelően kell konfigurálni a-ben `global.ini` . Ha kihagyja ezt a lépést, a HANA a replikáláshoz a hozzáférési VLAN-t fogja használni, ami esetleg nemkívánatos lehet.


Az alábbi példák az állomásnév-feloldási konfigurációt mutatják be a rendszer replikálásához egy másodlagos helyre. Három különböző hálózat azonosítható:

* Nyilvános hálózat 10.0.1-tartományhoz tartozó címmel. *

* Hálózat belső SAP HANA kommunikációhoz a gazdagépek között az egyes helyeken: 192.168.1. *

* Dedikált hálózat rendszer-replikáláshoz: 10.5.1. *

Az első példában a `[system_replication_communication]listeninterface` paraméter be van állítva, és a `.global` rendszer csak a szomszédos replikálási hely gazdagépeit adja meg.

A következő példában a `[system_replication_communication]listeninterface` paraméter be van állítva, és a `.internal` mindkét hely összes gazdagépe meg van adva.

  

### <a name="source-sap-ag-sap-hana-hrs-networking"></a>Forrás SAP AG SAP HANA óra hálózatkezelés

  

A rendszerreplikációhoz nincs szükség a fájl szerkesztésére `/etc/hosts` , a belső ("virtuális") állomásneveket a fájlban lévő IP-címekre kell leképezni, `global.ini` hogy dedikált hálózatot hozzanak létre a rendszer-replikáláshoz. Ennek szintaxisa a következő:

global.ini

[system_replication_hostname_resolution]

<IP-address_site>=<belső gazdagép – name_site>


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>SAP HANA konfigurálása a pacemaker-fürtben
Ebből a szakaszból megtudhatja, hogyan konfigurálhatja a SAP HANAt egy pacemaker-fürtben. Ez a szakasz ugyanazokat a két gazdagépet használja, `sollabdsm35` és a `sollabdsm36` cikk elején hivatkozik rá.

Győződjön meg arról, hogy teljesítette a következő előfeltételeket:  

* A pacemaker-fürt a dokumentációnak megfelelően van konfigurálva, és megfelelő és működő kerítéssel rendelkezik

* SAP HANA indítás a rendszerindításkor beállítás le van tiltva az összes fürtcsomóponton, mivel az Indítás és a leállítás a fürt által lesz kezelve.

* SAP HANA a rendszerreplikáció és az átvétel az SAP-eszközök használatával, megfelelően működik a fürtcsomópontok között

* SAP HANA olyan figyelési fiókot tartalmaz, amelyet a fürt mindkét fürtcsomóponton használhat.

* Mindkét csomópont a "magas rendelkezésre állás" és a "RHEL for SAP HANA" (RHEL 6, RHEL 7) csatornára van előfizetve.

  

* Általánosságban elmondható, hogy csak a csomópontból futtassa az összes számítógép parancsot, mert a CIB automatikusan frissül a számítógépek rendszerhéjból.

* [További információ a kvórum házirendről](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>A konfigurálás lépései 
1. Számítógépek konfigurálása.
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  Konfigurálja a Corosync.
    ```
    https://access.redhat.com/solutions/1293523 --> quorum information RHEL7

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
  

1.  Klónozott SAPHanaTopology-erőforrás létrehozása.
    ```
    pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 InstanceNumber=00 --clone clone-max=2 clone-node-max=1 interleave=true
    SAPHanaTopology resource is gathering status and configuration of SAP
    HANA System Replication on each node. SAPHanaTopology requires
    following attributes to be configured.



        Attribute Name Description

        SID SAP System Identifier (SID) of SAP HANA installation. Must be
    same for all nodes.

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

3.  Elsődleges/másodlagos SAPHana-erőforrás létrehozása.

    ```
    SAPHana resource is responsible for starting, stopping and relocating the SAP HANA database. This resource must be run as a Primary/    Secondary cluster resource. The resource has the following attributes.

    

    Attribute Name Required? Default value Description

    SID Yes None SAP System Identifier (SID) of SAP HANA installation. Must be same for all nodes.

    InstanceNumber Yes none 2-digit SAP Instance identifier.

    PREFER_SITE_TAKEOVER

    no yes Should cluster prefer to switchover to secondary instance instead of restarting primary locally? ("no": Do prefer restart locally;   "yes": Do prefer takeover to remote site)

    AUTOMATED_REGISTER no false Should the former SAP HANA primary be registered as secondary after takeover and DUPLICATE_PRIMARY_TIMEOUT?     ("false": no, manual intervention will be needed; "true": yes, the former primary will be registered by resource agent as secondary)

    DUPLICATE_PRIMARY_TIMEOUT no 7200 Time difference (in seconds) needed between primary time stamps, if a dual-primary situation occurs. If   the time difference is less than the time gap, then the cluster holds one or both instances in a "WAITING" status. This is to give an   admin a chance to react on a failover. A failed former primary will be registered after the time difference is passed. After this   registration to the new primary all data will be overwritten by the system replication.
    ```
  

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

6.  Hozzon létre egy virtuális IP-cím erőforrást.

    ```
    Cluster will contain Virtual IP address in order to reach the Primary instance of SAP HANA. Below is example command to create IPaddr2  resource with IP 10.7.0.84/24

    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  Megkötések létrehozása.

    ```
    For correct operation we need to ensure that SAPHanaTopology resources are started before starting the SAPHana resources and also that  the virtual IP address is present on the node where the Primary resource of SAPHana is running. To achieve this, the following 2    constraints need to be created.

    pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
    pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
    ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>SAPHana-erőforrás manuális mozgatásának tesztelése egy másik csomópontra

#### <a name="sap-hana-takeover-by-cluster"></a>(SAP Hana-átvétel fürt szerint)


Ha ki szeretné próbálni a SAPHana-erőforrás áthelyezését az egyik csomópontról a másikra, használja az alábbi parancsot. Vegye figyelembe, hogy a beállítás `--primary` nem használható a következő parancs futtatásakor, mert a SAPHana-erőforrás belsőleg működik.
```pcs resource move SAPHana_HR2_00-primary```

Az egyes számítógépek erőforrás-áthelyezési parancs meghívása után a fürt létrehozza a hely megkötéseit az erőforrás áthelyezésének eléréséhez. Ezeket a korlátozásokat el kell távolítani, hogy a jövőben engedélyezze az automatikus feladatátvételt.
A következő parancs használatával távolíthatja el őket.
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
  

* Jelentkezzen be a HANA-ba ellenőrzésként.

  * lefokozott gazdagép:

    ```
    hdbsql -i 00 -u system -p SAPhana10 -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * Előléptetett gazdagép:

    ```
    hdbsql -i 00 -u system -p SAPhana10 -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

A kapcsolóval `AUTOMATED_REGISTER=false` nem válthat vissza.

Ha a beállítás értéke false (hamis), akkor újra regisztrálnia kell a csomópontot:

  
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```
  

Most a Csomópont2, amely az elsődleges, a másodlagos gazdagépként működik.

Ha a lefokozott gazdagép regisztrációját automatizálni szeretné, érdemes lehet True értékre beállítani ezt a beállítást.

  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true

pcs cluster node clear node1
```
