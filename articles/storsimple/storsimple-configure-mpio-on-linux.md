---
title: Az MPIO konfigurálása a StorSimple Linux-gazdagépen
description: Megtudhatja, hogyan konfigurálhatja a többutas i/o (MPIO) szolgáltatást a StorSimple Linux (CentOS 6,6) rendszerű gazdagépen.
author: alkohli
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.topic: how-to
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 2b7ddf6423db4c471ee2065635f4e3e89f7eb7b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745733"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Az MPIO konfigurálása a CentOS-t futtató StorSimple-gazdagépen
Ez a cikk ismerteti a többutas i/o (MPIO) szolgáltatásnak a CentOS 6,6-es gazdagép-kiszolgálón való konfigurálásához szükséges lépéseket. Az iSCSI-kezdeményezők használatával a gazdagép-kiszolgáló a magas rendelkezésre állás érdekében csatlakozik a Microsoft Azure StorSimple eszközhöz. Részletesen ismerteti a Többutas eszközök automatikus észlelését és a StorSimple-kötetek adott beállítását.

Ez az eljárás az StorSimple 8000 Series-eszközök összes modelljére érvényes.

> [!NOTE]
> Ez az eljárás nem használható StorSimple Cloud Appliancehoz. További információ: a gazdagép-kiszolgálók konfigurálása a felhőalapú berendezéshez.

## <a name="about-multipathing"></a>Tudnivalók a többutas használatról
A többutas szolgáltatás lehetővé teszi több I/O-útvonal konfigurálását egy gazdagép és egy tárolóeszköz között. Ezek az I/O-útvonalak olyan fizikai SAN-kapcsolatok, amelyek különálló kábeleket, kapcsolókat, hálózati adaptereket és vezérlőket tartalmazhatnak. A többútvonalas összesítések az I/O-útvonalakat összesítik az összes összesített útvonalhoz társított új eszköz konfigurálásához.

A többútvonalos elérés célja kettős:

* **Magas rendelkezésre állás**: egy másik elérési utat biztosít, ha az I/O-útvonal bármely eleme (például a kábel, a kapcsoló, a hálózati adapter vagy a vezérlő) nem sikerül.
* **Terheléselosztás**: a tárolóeszköz konfigurációjától függően javíthatja a teljesítményt az I/O-útvonalak terhelésének észlelésével és a terhelések dinamikus kiegyensúlyozásával.

### <a name="about-multipathing-components"></a>Tudnivalók a többutas összetevőkről
A többszörös elérésű Linux a következő táblázatokból álló kernel-összetevőket és felhasználói felületi összetevőket tartalmaz.

* **Kernel**: a fő összetevő az az *eszköz-Mapper* , amely átirányítja az I/O-t, és támogatja a feladatátvételt az elérési utak és az elérésiút-csoportok számára.

* **Felhasználói terület**: ezek a *többutas* eszközök, amelyek a Többutas eszközök felügyeletére szolgálnak az eszköz-Mapper többutas modul működésének elutasításával. Az eszközök a következőkből állnak:
   
   * **Többutas**: a többútvonalas eszközök listája és konfigurálása.
   * **Többútvonalas**: démon, amely végrehajtja a többutas és figyeli az elérési utakat.
   * **Devmap-Name**: a devmaps udev megfelelő eszköz-nevet biztosít.
   * **Kpartx**: leképezi a lineáris devmaps az eszközök partíciói számára, hogy a többutas térképek particionálható legyen.
   * **Többutas. conf**: a többutas démon konfigurációs fájlja, amely a beépített konfigurációs tábla felülírására szolgál.

### <a name="about-the-multipathconf-configuration-file"></a>Tudnivalók a többutas. conf konfigurációs fájlról
A konfigurációs fájl `/etc/multipath.conf` számos, felhasználó által konfigurálható többutas funkciót tesz elérhetővé. A `multipath` parancs és a kernel démon a `multipathd` fájlban található információkat használja. A fájlt csak a Többutas eszközök konfigurálása során kell megkeresni. A parancs futtatása előtt győződjön meg arról, hogy minden módosítást végzett `multipath` . Ha ezt követően módosítja a fájlt, a módosítások érvénybe léptetéséhez le kell állítania és újra kell indítania a többutas elérési utat.

A többutas. conf fájl öt szakaszt tartalmaz:

- **Rendszerszintű alapértékek** *(alapértékek)*: felülbírálhatja a rendszerszintű alapértékeket.
- **Feketelistán lévő eszközök** *(feketelista)*: megadhatja azoknak az eszközöknek a listáját, amelyeket az eszköz-Mapper nem szabályozhat.
- **Kivételek feketelistája** *(blacklist_exceptions)*: azonosíthatja a többutas eszközként kezelendő egyes eszközöket, még akkor is, ha azok szerepelnek a Blocklist.
- **Tároló-vezérlőre vonatkozó beállítások** *(eszközök)*: megadhatja azokat a konfigurációs beállításokat, amelyeket a rendszer a szállítóval és a termékkel kapcsolatos adatokkal rendelkező eszközökre alkalmaz.
- **Eszközre vonatkozó beállítások** *(több útvonal)*: ebben a szakaszban az egyes logikai egységek konfigurációs beállításainak finomhangolásához használható.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>A Linux-gazdagéphez csatlakoztatott StorSimple többutas elérésének konfigurálása
A Linux-gazdagéphez csatlakoztatott StorSimple-eszközök magas rendelkezésre állást és terheléselosztást is konfigurálhat. Ha például a Linux-gazdagép két, a SAN-hoz csatlakoztatott csatolóval rendelkezik, és az eszközhöz két interfész csatlakozik a SAN hálózathoz, például hogy ezek a felületek ugyanazon az alhálózaton vannak, akkor elérhető 4 útvonal lesz. Ha azonban az eszköz és a gazdagép felületének minden adatfelülete eltérő IP-alhálózaton található (és nem irányítható), akkor csak 2 útvonal lesz elérhető. Az összes elérhető útvonal automatikus észleléséhez konfigurálhatja a többutas elérési utat, kiválaszthatja az elérési utakhoz tartozó terheléselosztási algoritmust, alkalmazhatja az adott konfigurációs beállításokat a StorSimple kötetekhez, majd engedélyezheti és ellenőrizheti a többutas elérést.

Az alábbi eljárás azt ismerteti, hogyan konfigurálhatja a többutas módszert, ha két hálózati adapterrel rendelkező StorSimple-eszköz két hálózati adapterrel rendelkező gazdagéphez csatlakozik.

## <a name="prerequisites"></a>Előfeltételek
Ez a szakasz a CentOS-kiszolgáló és a StorSimple-eszköz konfigurációs előfeltételeit részletezi.

### <a name="on-centos-host"></a>CentOS-gazdagépen
1. Győződjön meg arról, hogy a CentOS-gazdagépen 2 hálózati adapter van engedélyezve. Típus:
   
    `ifconfig`
   
    A következő példa a kimenetet mutatja be, ha két hálózati adapter ( `eth0` és `eth1` ) van jelen a gazdagépen.
   
    ```output
    [root@centosSS ~]# ifconfig
    eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
        inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
        inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
        inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
        UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
        RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
        TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)

    eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
        inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
        inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
        inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
        UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
        RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
        TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)

    loLink encap:Local Loopback  
        inet addr:127.0.0.1  Mask:255.0.0.0
        inet6 addr: ::1/128 Scope:Host
        UP LOOPBACK RUNNING  MTU:65536  Metric:1
        RX packets:12 errors:0 dropped:0 overruns:0 frame:0
        TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:0
        RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
    ```
1. Telepítse az *iSCSI-kezdeményező-utils* szolgáltatást a CentOS-kiszolgálóra. Az *iSCSI-kezdeményező-utils* telepítéséhez hajtsa végre az alábbi lépéseket.
   
   1. Jelentkezzen be a `root` CentOS-gazdagépre.
   1. Telepítse az *iSCSI-kezdeményező-utils-* t. Típus:
      
       `yum install iscsi-initiator-utils`
   1. Az *iSCSI-kezdeményező-utils* sikeres telepítése után indítsa el az iSCSI szolgáltatást. Típus:
      
       `service iscsid start`
      
       Előfordulhat, hogy a rendszer `iscsid` nem indul el ténylegesen, és szükség lehet a `--force` lehetőségre.
   1. Annak ellenőrzéséhez, hogy az iSCSI-kezdeményező engedélyezve van-e a rendszerindítás során, a `chkconfig` parancs használatával engedélyezze a szolgáltatást.
      
       `chkconfig iscsi on`
   1. A megfelelő beállítás ellenőrzéséhez futtassa a következő parancsot:
      
       `chkconfig --list | grep iscsi`
      
       Az alábbiakban egy példa látható a kimenetre.
      
        ```output
        iscsi   0:off   1:off   2:on3:on4:on5:on6:off
        iscsid  0:off   1:off   2:on3:on4:on5:on6:off
        ```
      
       A fenti példát követve láthatja, hogy az iSCSI-környezet a 2., 3., 4. és 5. futtatási szinten fut a rendszerindítás során.
1. Telepítse az *eszközt – Mapper-többutas*. Típus:
   
    `yum install device-mapper-multipath`
   
    Ekkor elindul a telepítés. Ha a rendszer megerősítést kér, a folytatáshoz írja be az **Y karaktert** .

### <a name="on-storsimple-device"></a>StorSimple-eszközön
A StorSimple-eszköznek a következőket kell tartalmaznia:

* Legalább két interfész van engedélyezve az iSCSI-hez. Annak ellenőrzéséhez, hogy a StorSimple-eszköz két csatolója iSCSI-engedélyezve van-e, hajtsa végre a következő lépéseket a StorSimple-eszköz klasszikus Azure-portálján:
  
  1. Jelentkezzen be a StorSimple-eszköz klasszikus portálján.
  1. Válassza ki a StorSimple Manager szolgáltatást, kattintson az **eszközök** elemre, és válassza ki az adott StorSimple eszközt. Kattintson a **Konfigurálás** elemre, és ellenőrizze a hálózati adapter beállításait. Alább látható egy két iSCSI-kompatibilis hálózati adapterrel rendelkező képernyőkép. Itt az adat 2 és az adat 3, mind a 10 GbE interfész engedélyezve van az iSCSI-hez.
     
      ![MPIO StorsSimple-adatfájlok – 2. konfiguráció](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple-adatfájlok – 3. konfiguráció](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      A **configure (Konfigurálás** ) lapon
     
     1. Győződjön meg arról, hogy mindkét hálózati adapter iSCSI-kompatibilis. Az **iSCSI-kompatibilis** mezőt **Igen** értékre kell állítani.
     1. Győződjön meg arról, hogy a hálózati adapterek azonos sebességgel rendelkeznek, mindkettőnek 1 GbE vagy 10 GbE kell lennie.
     1. Jegyezze fel az iSCSI-kompatibilis felületek IPv4-címeit, és mentse a szolgáltatást későbbi használatra a gazdagépen.
* A StorSimple eszközön lévő iSCSI-felületeknek elérhetőnek kell lenniük a CentOS-kiszolgálóról.
      Ennek ellenőrzéséhez meg kell adnia a StorSimple iSCSI-kompatibilis hálózati adapterek IP-címeit a gazdagép kiszolgálóján. A használt parancsok és a DATA2 (10.126.162.25) és DATA3 (10.126.162.26) megfelelő kimenete alább látható:
  
    ```console
    [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    ```

### <a name="hardware-configuration"></a>Hardverkonfiguráció
Javasoljuk, hogy a két iSCSI hálózati adaptert külön elérési utakon kapcsolja össze a redundancia érdekében. Az alábbi ábrán a CentOS-kiszolgáló és a StorSimple-eszköz esetében a magas rendelkezésre állás és a terheléselosztás többútvonalos beállítása ajánlott.

![MPIO hardver-konfiguráció a Linux-gazdagép StorSimple](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Az előző ábrán látható módon:

* A StorSimple-eszköz aktív-passzív konfigurációban két vezérlővel rendelkezik.
* Két SAN-kapcsoló csatlakozik az eszköz-vezérlőkhöz.
* A StorSimple-eszközön két iSCSI-kezdeményező van engedélyezve.
* A CentOS-gazdagépen két hálózati adapter van engedélyezve.

A fenti konfiguráció 4 külön útvonalat fog eredményezni az eszköz és a gazdagép között, ha a gazdagép és az adatillesztők irányíthatók.

> [!IMPORTANT]
> * Javasoljuk, hogy ne keverjünk össze 1 GbE és 10 GbE hálózati adaptert a többutas eléréshez. Két hálózati adapter használata esetén mindkét csatolónak azonos típusúnak kell lennie.
> * A StorSimple-eszközön a DATA0, DATA1, DATA4 és DATA5 1 GbE felületek, míg a DATA2 és a DATA3 10 GbE hálózati adapter. |
> 
> 

## <a name="configuration-steps"></a>Konfigurációs lépések
A többutas létrehozás konfigurációs lépései magában foglalják az automatikus felderítéshez elérhető elérési utakat, a használandó terheléselosztási algoritmust, valamint a többutas és végül a konfiguráció ellenőrzését. A következő szakaszokban részletesen ismertetjük ezeket a lépéseket.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>1. lépés: többutas automatikus észlelés konfigurálása
A többutas által támogatott eszközök automatikusan felderíthető és konfigurálhatók.

1. Fájl inicializálása `/etc/multipath.conf` . Típus:
   
     `mpathconf --enable`
   
    A fenti parancs létrehoz egy `sample/etc/multipath.conf` fájlt.
1. A többutas szolgáltatás elindítása. Típus:
   
    `service multipathd start`
   
    A következő kimenet jelenik meg:
   
    `Starting multipathd daemon:`
1. Engedélyezze a több elérési út automatikus észlelését. Típus:
   
    `mpathconf --find_multipaths y`
   
    Ez a beállítás a lent látható alapértelmezett értékeket fogja módosítani `multipath.conf` :
   
    ```config
    defaults {
    find_multipaths yes
    user_friendly_names yes
    path_grouping_policy multibus
    }
    ```

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>2. lépés: a StorSimple-kötetek többutas elérésének konfigurálása
Alapértelmezés szerint minden eszköz a többutas. conf fájlban blocklisted, és a rendszer figyelmen kívül hagyja. Blocklist-kivételeket kell létrehoznia a StorSimple-eszközökön lévő kötetek többutas elérésének engedélyezéséhez.

1. Szerkessze a `/etc/mulitpath.conf` fájlt. Típus:
   
    `vi /etc/multipath.conf`
1. Keresse meg a blacklist_exceptions szakaszt a többutas. conf fájlban. Ebben a szakaszban a StorSimple-eszközt Blocklist-kivételként kell megjelenni. A fájl megfelelő sorait a lent látható módon törölheti (csak a használt eszköz adott modelljét használja):
   
    ```config
    blacklist_exceptions {
        device {
                    vendor  "MSFT"
                    product "STORSIMPLE 8100*"
        }
        device {
                    vendor  "MSFT"
                    product "STORSIMPLE 8600*"
        }
    }
    ```

### <a name="step-3-configure-round-robin-multipathing"></a>3. lépés: a ciklikus multiplexelés többszörös elérésének konfigurálása
Ez a terheléselosztási algoritmus a rendelkezésre álló többszörös elérési utakat az aktív vezérlőhöz a kiegyensúlyozott, ciklikus időszeleteléses módon használja.

1. Szerkessze a `/etc/multipath.conf` fájlt. Típus:
   
    `vi /etc/multipath.conf`
1. A `defaults` szakasz alatt állítsa be a következőt: `path_grouping_policy` `multibus` . A `path_grouping_policy` meghatározza az alapértelmezett elérési út csoportosítására vonatkozó házirendet, amely a meghatározatlan többutas elemekre vonatkozik. Az Alapértelmezések szakasz az alább látható módon jelenik meg.
   
    ```config
    defaults {
            user_friendly_names yes
            path_grouping_policy multibus
    }
    ```

> [!NOTE]
> A következők leggyakoribb értékei `path_grouping_policy` :
> 
> * feladatátvétel = 1 elérési út/prioritási csoport
> * Multibus = az összes érvényes elérési út 1 prioritású csoportban
> 
> 

### <a name="step-4-enable-multipathing"></a>4. lépés: a többutas működés engedélyezése
1. Indítsa újra a `multipathd` démont. Típus:
   
    `service multipathd restart`
1. A kimenet az alábbi módon jelenik meg:
   
    ```output
    [root@centosSS ~]# service multipathd start
    Starting multipathd daemon:  [OK]
    ```

### <a name="step-5-verify-multipathing"></a>5. lépés: a többutas elérés ellenőrzése
1. Először győződjön meg arról, hogy az iSCSI-kapcsolatok létrejöttek a StorSimple eszközzel a következőképpen:
   
   a. A StorSimple-eszköz felderítése. Típus:
      
    `iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>`
    
    Ha a DATA0 IP-címe 10.126.162.25, és az 3260-as port a StorSimple-eszközön van megnyitva a kimenő iSCSI-forgalomhoz, az alábbi ábrán látható:
    
    ```output
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Másolja a StorSimple-eszköz IQN az `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` előző kimenetből.

   b. Kapcsolódjon az eszközhöz a TARGET IQN használatával. Az StorSimple-eszköz itt az iSCSI-cél. Típus:

      `iscsiadm -m node --login -T <IQN of iSCSI target>`

    A következő példa a kimenetet mutatja a IQN `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` . A kimenet azt jelzi, hogy sikeresen csatlakozott az eszköz két iSCSI-kompatibilis hálózati adapteréhez.

    ```output
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    Ha csak egy gazdagép-felületet és két elérési utat lát, akkor az iSCSI-gazdagéphez is engedélyeznie kell a csatolókat. A [Linux dokumentációjában találhat részletes útmutatást](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

1. A rendszer a CentOS-kiszolgáló számára teszi elérhetővé a kötetet a StorSimple-eszközről. További információ [: 6. lépés: kötet létrehozása](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) a StorSimple-eszközön lévő Azure Portal használatával.

1. Ellenőrizze az elérhető elérési utakat. Típus:

    `multipath -l`

      A következő példa egy StorSimple-eszköz két hálózati adapterének kimenetét mutatja két elérhető elérési úttal rendelkező egyetlen gazdagép hálózati adapterhez csatlakoztatva.

    ```output
    mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
    size=100G features='0' hwhandler='0' wp=rw
    `-+- policy='round-robin 0' prio=0 status=active
    |- 7:0:0:1 sdc 8:32 active undef running
    `- 6:0:0:1 sdd 8:48 active undef running
    ```

    A következő példa egy StorSimple-eszköz két hálózati adapterének kimenetét mutatja be, amely két, négy elérhető elérési úttal rendelkező gazdagép-hálózati adapterhez csatlakozik.

    ```output
    mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
    size=100G features='0' hwhandler='0' wp=rw
    `-+- policy='round-robin 0' prio=0 status=active
    |- 17:0:0:0 sdb 8:16 active undef running
    |- 15:0:0:0 sdd 8:48 active undef running
    |- 14:0:0:0 sdc 8:32 active undef running
    `- 16:0:0:0 sde 8:64 active undef running
    ```

    Az elérési utak konfigurálása után a kötet csatlakoztatásához és formázásához tekintse meg a gazdagép operációs rendszerén (CentOS 6,6) megadott utasításokat.

## <a name="troubleshoot-multipathing"></a>Többútvonalas hibák megoldása
Ez a szakasz néhány hasznos tippet tartalmaz, ha a többutas konfiguráció során problémákba ütközik.

K. Nem látom, hogy a fájl módosításai `multipath.conf` érvénybe lépnek.

A. Ha bármilyen módosítást végzett a `multipath.conf` fájlon, újra kell indítania a többutas szolgáltatást. Írja be a következő parancsot:

`service multipathd restart`

K. Engedélyeztem két hálózati adaptert a StorSimple-eszközön, és két hálózati adaptert a gazdagépen. Ha kilistázza az elérhető elérési utakat, csak két elérési utat látok. Azt vártam, hogy négy elérhető útvonal látható.

A. Győződjön meg arról, hogy a két útvonal ugyanazon az alhálózaton van és irányítható. Ha a hálózati adapterek eltérő VLAN-ok és nem irányíthatók, csak két elérési utat fog látni. Ennek egyik módja annak ellenőrzése, hogy a StorSimple-eszköz hálózati adapteréről is elérheti-e a gazdagép-adaptereket. [Kapcsolatba kell lépnie Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md) , mert ez az ellenőrzés csak támogatási munkameneten keresztül végezhető el.

K. Ha elérhető útvonalakat listázok, nem látok kimenetet.

A. Általában nem látható, hogy a többutas útvonalak egyike a többutas démon hibáját sugallja, és ez valószínűleg a fájl egyik problémája `multipath.conf` .

Érdemes ellenőrizni azt is, hogy a célhelyhez való csatlakozás után valóban látni lehet-e lemezeket, mivel a többutas listák egyik válasza sem jelent lemezeket.

* Az SCSI-busz újraellenőrzéséhez használja a következő parancsot:
  
    `$ rescan-scsi-bus.sh` (sg3_utils csomag része)
* Írja be a következő parancsokat:
  
    `$ dmesg | grep sd*`
     
     Vagy
  
    `$ fdisk -l`
  
    Ezek a legutóbb hozzáadott lemezek részleteit adják vissza.
* Annak megállapításához, hogy az StorSimple-e, használja a következő parancsokat:
  
    `cat /sys/block/<DISK>/device/model`
  
    Ez egy karakterláncot ad vissza, amely meghatározza, hogy StorSimple-e a lemez.

A kevésbé valószínű, de lehetséges ok is elavult iSCSI-PID. Használja az alábbi parancsot az iSCSI-munkamenetből való kijelentkezéshez:

`iscsiadm -m node --logout -p <Target_IP>`

Ismételje meg ezt a parancsot az iSCSI-tároló összes csatlakoztatott hálózati adapterén, amely a StorSimple-eszköz. Miután kijelentkezett az összes iSCSI-munkamenetből, az iSCSI-tároló IQN használatával hozza létre újra az iSCSI-munkamenetet. Írja be a következő parancsot:

`iscsiadm -m node --login -T <TARGET_IQN>`


K. Nem tudom, hogy az eszközem engedélyezve van-e.

A. Annak ellenőrzéséhez, hogy az eszköz engedélyezve van-e, használja a következő hibaelhárítási interaktív parancsot:

```console
multipathd -k
multipathd> show devices
available block devices:
ram0 devnode blacklisted, unmonitored
ram1 devnode blacklisted, unmonitored
ram2 devnode blacklisted, unmonitored
ram3 devnode blacklisted, unmonitored
ram4 devnode blacklisted, unmonitored
ram5 devnode blacklisted, unmonitored
ram6 devnode blacklisted, unmonitored
ram7 devnode blacklisted, unmonitored
ram8 devnode blacklisted, unmonitored
ram9 devnode blacklisted, unmonitored
ram10 devnode blacklisted, unmonitored
ram11 devnode blacklisted, unmonitored
ram12 devnode blacklisted, unmonitored
ram13 devnode blacklisted, unmonitored
ram14 devnode blacklisted, unmonitored
ram15 devnode blacklisted, unmonitored
loop0 devnode blacklisted, unmonitored
loop1 devnode blacklisted, unmonitored
loop2 devnode blacklisted, unmonitored
loop3 devnode blacklisted, unmonitored
loop4 devnode blacklisted, unmonitored
loop5 devnode blacklisted, unmonitored
loop6 devnode blacklisted, unmonitored
loop7 devnode blacklisted, unmonitored
sr0 devnode blacklisted, unmonitored
sda devnode whitelisted, monitored
dm-0 devnode blacklisted, unmonitored
dm-1 devnode blacklisted, unmonitored
dm-2 devnode blacklisted, unmonitored
sdb devnode whitelisted, monitored
sdc devnode whitelisted, monitored
dm-3 devnode blacklisted, unmonitored
```


További információ: [Hibaelhárítás a többutas eléréshez](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot).

## <a name="list-of-useful-commands"></a>Hasznos parancsok listája
| Típus | Parancs | Leírás |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |ISCSI szolgáltatás indítása |
| &nbsp; |`service iscsid stop` |Az iSCSI szolgáltatás leállítása |
| &nbsp; |`service iscsid restart` |ISCSI szolgáltatás újraindítása |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Elérhető célok felderítése a megadott címen |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Bejelentkezés az iSCSI-tárolóba |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Kijelentkezés az iSCSI-tárolóból |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |ISCSI-kezdeményező nevének nyomtatása |
| &nbsp; |`iscsiadm -m session -s <sessionid> -P 3` |A gazdagépen felderített iSCSI-munkamenet és kötet állapotának keresése |
| &nbsp; |`iscsi -m session` |Megjeleníti a gazdagép és a StorSimple-eszköz között létrejött összes iSCSI-munkamenetet. |
|  | | |
| **Többutas** |`service multipathd start` |Többutas démon indítása |
| &nbsp; |`service multipathd stop` |Többutas démon leállítása |
| &nbsp; |`service multipathd restart` |Többutas démon újraindítása |
| &nbsp; |`chkconfig multipathd on` </br> OR </br> `mpathconf -with_chkconfig y` |A többutas démon indításának engedélyezése rendszerindítás közben |
| &nbsp; |`multipathd -k` |Az interaktív konzol elindítása hibaelhárításhoz |
| &nbsp; |`multipath -l` |Többutas kapcsolatok és eszközök listázása |
| &nbsp; |`mpathconf --enable` |Hozzon létre egy minta mulitpath. conf fájlt a `/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Következő lépések
Az MPIO Linux-gazdagépen való konfigurálásakor előfordulhat, hogy a következő CentoS 6,6-dokumentumokra is szüksége lesz:

* [Az MPIO beállítása a CentOS-ben](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Linux-betanítási útmutató](http://linux-training.be/linuxsys.pdf)
