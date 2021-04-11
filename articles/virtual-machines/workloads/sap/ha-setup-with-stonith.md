---
title: Magas rendelkezésre állás beállítása az Azure-beli SAP HANA STONITH (nagyméretű példányok) | Microsoft Docs
description: Magas rendelkezésre állás biztosítása SAP HANA számára az Azure-ban (nagyméretű példányok) a SUSE-ben a STONITH használatával
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3dd2a618f22036fd0826a99207d83a3add390c7d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645323"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Magas rendelkezésre állás beállítása SUSE rendszeren a STONITH használatával
Ez a dokumentum részletes útmutatást nyújt a SUSE operációs rendszer magas rendelkezésre állásának beállításához a STONITH eszköz használatával.

**Jogi nyilatkozat:** *Ez az útmutató a Microsoft HANA nagyméretű példányainak környezetében végzett telepítés tesztelésével készült, amely sikeresen működik. Mivel a Microsoft Service Management csapata a HANA nagyméretű példányaihoz nem támogatja az operációs rendszer használatát, előfordulhat, hogy az operációs rendszer rétegének további hibaelhárításához vagy pontosításához kapcsolatba kell lépnie a SUSE-vel. A Microsoft Service Management csapata STONITH-eszközt állít be, és teljes mértékben támogatja a STONITH-eszközök hibáinak elhárítását.*
## <a name="overview"></a>Áttekintés
A magas rendelkezésre állás SUSE fürtszolgáltatással történő beállításához a következő előfeltételeknek kell megfelelnie.
### <a name="pre-requisites"></a>Előfeltételek
- A HANA nagyméretű példányai kiépítve
- Az operációs rendszer regisztrálva van
- A HANA Large instances-kiszolgálók a javítások/csomagok beszerzéséhez csatlakoznak az SMT-kiszolgálóhoz
- Az operációs rendszerhez telepítve vannak a legújabb javítások
- Az NTP (Time Server) beállítása
- Olvassa el és Ismerje meg a SUSE-dokumentáció legújabb verzióját a HA telepítőben

### <a name="setup-details"></a>Telepítés részletei
Ez az útmutató a következő beállításokat használja:
- Operációs rendszer: SLES 12 SP1 SAP-hoz
- HANA nagyméretű példányok: 2xS192 (négy szoftvercsatorna, 2 TB)
- HANA-verzió: HANA 2,0 SP1
- Kiszolgálók nevei: sapprdhdb95 (csomópont1) és sapprdhdb96 (Csomópont2)
- STONITH-eszköz: iSCSI-alapú STONITH-eszköz
- Az NTP beállítása a HANA nagyméretű példányok egyik csomópontján

Ha a HANA Large-példányokat a HSR-mel állítja be, a Microsoft Service Management csapata kérheti a STONITH beállítását. Ha már van egy meglévő, a HANA nagyméretű példányaival rendelkező felhasználó, és STONITH-eszközt kell beállítania a meglévő pengék számára, meg kell adnia a következő információkat a Microsoft Service Management csapatának a szolgáltatási kérelem űrlapján (SRF). A SRF űrlapot a technikai fiókkezelő vagy a Microsoft Contact for HANA nagyméretű példányok bevezetéséhez kérheti le. Az új ügyfelek a kiépítés időpontjában igényelhetnek STONITH eszközt. A bemenetek a kiépítési kérelem űrlapján érhetők el.

- Kiszolgáló neve és kiszolgáló IP-címe (például myhanaserver1, 10.35.0.1)
- Hely (például USA keleti régiója)
- Ügyfél neve (például Microsoft)
- SID-HANA rendszer-azonosító (például H11)

A STONITH-eszköz konfigurálása után a Microsoft Service Management csapata megadja az iSCSI-tároló SBD-eszközének nevét és IP-címét, amelyet a STONITH-telepítő konfigurálására használhat. 

Ha a STONITH használatával szeretné beállítani a végpontot, a következő lépéseket kell követnie:

1.  A SBD-eszköz azonosítása
2.  A SBD-eszköz inicializálása
3.  A fürt konfigurálása
4.  A Softdog watchdog beállítása
5.  A csomópont csatlakoztatása a fürthöz
6.  A fürt ellenőrzése
7.  Erőforrások konfigurálása a fürthöz
8.  A feladatátvételi folyamat tesztelése

## <a name="1---identify-the-sbd-device"></a>1. a SBD-eszköz azonosítása
Ez a szakasz azt ismerteti, hogyan határozható meg a SBD eszköz a telepítőhöz, miután a Microsoft Service Management csapata beállította a STONITH. **Ez a szakasz csak a meglévő ügyfélre vonatkozik**. Ha Ön új ügyfél, a Microsoft Service Management csapata a SBD-eszköz nevét is megadja Önnek, és kihagyhatja ezt a szakaszt.

1,1 */etc/iSCSI/initiatorname.iSCI* módosítása a következőre 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

A Microsoft Service Management ezt a karakterláncot adja meg. Módosítsa a fájlt **mindkét** csomóponton, de a csomópontok száma eltér az egyes csomópontokon.

![Képernyőfelvétel: egy csomópont InitiatorName értékeit tartalmazó initiatorname-fájl.](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1,2 módosítsa a */etc/iSCSI/iscsid.conf*: állítsa be *Node.Session.Timeo.replacement_timeout = 5* és *Node. Startup = automatikus* beállítást. Módosítsa a fájlt **mindkét** csomóponton.

1,3 a felderítési parancs végrehajtása négy munkamenetet mutat be. Futtassa azt mindkét csomóponton.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![A képernyőképen egy konzolablak látható, amely a isciadm-felderítési parancs eredményét tartalmazza.](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1,4 futtassa a parancsot az iSCSI-eszközre való bejelentkezéshez, amely négy munkamenetet mutat be. Futtassa azt **mindkét** csomóponton.

```
iscsiadm -m node -l
```
![A képernyőképen egy konzolablak látható a iscsiadm node parancs eredményeivel.](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1,5 hajtsa végre az újraellenőrzési parancsfájlt: *rescan-SCSI-Bus.sh*.  Ez a szkript megjeleníti az Ön számára létrehozott új lemezeket.  Futtassa azt mindkét csomóponton. A logikai egységnek nullánál nagyobb számnak kell lennie (például: 1, 2 stb.)

```
rescan-scsi-bus.sh
```
![A képernyőképen egy konzolablak látható a szkript eredményeivel.](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1,6 az eszköz nevének lekéréséhez futtassa az *fdisk – l* parancsot. Futtassa azt mindkét csomóponton. Válassza ki az eszközt a **178 MIB** mérettel.

```
  fdisk –l
```

![A képernyőképen a f Disk parancs eredményeit tartalmazó konzolablak látható.](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. a SBD-eszköz inicializálása

2,1 a SBD-eszköz inicializálása **mindkét** csomóponton

```
sbd -d <SBD Device Name> create
```
![A képernyőképen egy olyan konzolablak látható, amely az s b d Create parancs eredményét tartalmazza.](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2,2 megtekintheti, hogy mi lett írva az eszközre. **Mindkettő** a csomópontokon

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. a fürt konfigurálása
Ez a szakasz a SUSE HEKTÁRos fürt beállításának lépéseit ismerteti.
### <a name="31-package-installation"></a>3,1 csomag telepítése
3.1.1 Ellenőrizze, hogy telepítve vannak-e a ha_sles és a SAPHanaSR-doc mintázatok. Ha nincs telepítve, telepítse őket. Telepítse **a** csomópontokra.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![A képernyőképen a minta parancs eredményét bemutató konzolablak látható. ](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
 ![ A képernyőképen a SAPHanaSR-doc parancs eredményét bemutató konzolablak látható.](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3,2 a fürt beállítása
3.2.1 használhatja a *Ha-cluster-init* parancsot is, vagy a YaST2 varázslóval állíthatja be a fürtöt. Ebben az esetben a rendszer a YaST2 varázslót használja. Ezt a lépést **csak az elsődleges csomóponton** hajthatja végre.

Kövesse a YaST2> magas rendelkezésre állás >-fürt ![ képernyőképét, amely a magas rendelkezésre állást és a fürttel rendelkező YaST-vezérlőt mutatja. ](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
 ![ Képernyőfelvétel: a telepítési és a megszakítási beállításokat tartalmazó párbeszédpanel.](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Kattintson a **Mégse** gombra, mert a halk2-csomag már telepítve van.

![A képernyőképen egy üzenet jelenik meg a megszakítási lehetőségről.](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Kattintson a **Folytatás** gombra.

Várt érték = az üzembe helyezett csomópontok száma (ebben az esetben 2) képernyőfelvétel: a ![ fürt biztonsága a biztonsági hitelesítés engedélyezése jelölőnégyzet bejelölésével.](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png)
A **következő** 
 ![ képernyőképen a fürt konfigurálása ablak látható a Szinkronizáló gazdagép és a fájlok szinkronizálása listában.](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
Adja hozzá a csomópontok nevét, majd kattintson a "javasolt fájlok hozzáadása" elemre.

Kattintson a "csync2 bekapcsolása" elemre.

Kattintson az "előmegosztott kulcsok generálása" elemre, amely az alábbi előugró ablakban látható

![A képernyőképen egy üzenet jelenik meg, amely szerint a kulcs létrejött.](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Kattintson **az OK** gombra

A hitelesítés az IP-címek és a Csync2-ben előmegosztott kulcsok használatával történik. A kulcs fájlját a csync2-k/etc/csync2/key_hagroup hozza létre. A key_hagroup fájlt manuálisan kell átmásolni a fürt összes tagjába a létrehozása után. **Győződjön meg arról, hogy a fájlt az 1. csomópontról Csomópont2-re másolja**.

![Képernyőfelvétel: a fürt konfigurálására szolgáló párbeszédpanel, amely a kulcsnak a fürt összes tagjára történő másolásához szükséges beállításokat tartalmazza.](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

A **következő** 
 ![ képernyőképen a fürtszolgáltatás ablak látható.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Az alapértelmezett beállításnál a rendszerindítás kikapcsolva értékre módosult, hogy a pacemaker a rendszerindításkor induljon el. A választást a telepítési követelmények alapján teheti meg.
Kattintson a **tovább** gombra, és a fürt konfigurációja befejeződött.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. a Softdog watchdog beállítása
Ez a szakasz a watchdog (softdog) konfigurációját ismerteti.

4,1 adja hozzá a következő sort  a/etc/init.d/boot.local **mindkét** csomóponton.
```
modprobe softdog
```
![Képernyőfelvétel: a hozzáadott softdog sorral rendelkező rendszerindító fájl.](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4,2 a következő módon frissítse a fájl */etc/sysconfig/SBD* **mindkét** csomóponton:
```
SBD_DEVICE="<SBD Device Name>"
```
![Képernyőfelvétel: az s b d fájl, amely az S B D_DEVICE hozzáadott értéket tartalmazza.](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4,3 a következő parancs futtatásával töltse be a kernel modult **mindkét** csomóponton.
```
modprobe softdog
```
![A képernyőkép a konzol egy részét mutatja be a modprobe softdog paranccsal.](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4,4 Ellenőrizze és győződjön meg arról, hogy a softdog a következőként fut **mindkét** csomóponton:
```
lsmod | grep dog
```
![Képernyőfelvétel: a konzol ablakának egy része, amely az l s mod parancs futtatásának eredményét jeleníti meg.](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4,5 a SBD-eszköz elindítása **mindkét** csomóponton
```
/usr/share/sbd/sbd.sh start
```
![A képernyőképen egy konzolablak egy része látható a start paranccsal.](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4,6 tesztelje a SBD démont **mindkét** csomóponton. Két bejegyzést lát, miután konfigurálta **a** csomópontokon
```
sbd -d <SBD Device Name> list
```
![Képernyőfelvétel: egy konzolablak egy részének megjelenítése, amely két bejegyzést jelenít meg.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,7 Tesztüzenet küldése az **egyik** csomópontnak
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![Képernyőfelvétel: egy konzolablak egy részének megjelenítése, amely két bejegyzést jelenít meg.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,8 a **második** csomóponton (Csomópont2) megtekintheti az üzenet állapotát
```
sbd  -d <SBD Device Name> list
```
![Képernyőfelvétel: egy konzolablak egy részének megjelenítése, amely a másik tag tesztelési értékét jeleníti meg.](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4,9 a SBD-konfiguráció bevezetéséhez frissítse a */etc/sysconfig/SBD* fájlt a következőképpen. A fájl frissítése **mindkét** csomóponton
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4,10 a pacemaker szolgáltatás elindítása az **elsődleges csomóponton** (csomópont1)
```
systemctl start pacemaker
```
![Képernyőfelvétel: egy konzolablak, amely a pacemaker elindítása után megjeleníti az állapotot.](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Ha a pacemaker szolgáltatás *nem sikerül*, tekintse meg az *5. forgatókönyv: a pacemaker szolgáltatás sikertelen*

## <a name="5---joining-the-cluster"></a>5. csatlakozás a fürthöz
Ez a szakasz azt ismerteti, hogyan lehet csatlakoztatni a csomópontot a fürthöz.

### <a name="51-add-the-node"></a>5,1 a csomópont hozzáadása
Futtassa a következő parancsot a **Csomópont2** , hogy a Csomópont2 csatlakozzon a fürthöz.
```
ha-cluster-join
```
Ha a fürthöz való csatlakozás során *hibaüzenet* jelenik meg, tekintse át *a 6. forgatókönyv: 2. csomópont nem tud csatlakozni a fürthöz*.

## <a name="6---validating-the-cluster"></a>6. a fürt ellenőrzése

### <a name="61-start-the-cluster-service"></a>6,1 a fürtszolgáltatás elindítása
Ha a fürtöt első alkalommal szeretné megkeresni és igény szerint elindítani **mindkét** csomóponton.
```
systemctl status pacemaker
systemctl start pacemaker
```
![Képernyőfelvétel: a konzol ablaka, amely a pacemaker állapotát jeleníti meg.](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6,2 az állapot figyelése
A *crm_mon* parancs futtatásával győződjön **meg** arról, hogy a csomópontok online állapotban vannak. A fürt **bármelyik csomópontján** futtathatja.
```
crm_mon
```
![A képernyőfelvételen a c r m_mon eredményeit tartalmazó konzolablak látható.](media/HowToHLI/HASetupWithStonith/crm-mon.png)
A https://való bejelentkezéshez a következőt is bejelentkezhet a Hawk-ba *\<node IP> : 7630*. Az alapértelmezett felhasználó a hacluster, a jelszó pedig Linux. Ha szükséges, módosíthatja a jelszót a *passwd* paranccsal.

## <a name="7-configure-cluster-properties-and-resources"></a>7. a fürt tulajdonságainak és erőforrásainak konfigurálása 
Ez a szakasz a fürt erőforrásainak konfigurálásához szükséges lépéseket ismerteti.
Ebben a példában a következő erőforrást kell beállítania, a REST konfigurálható (szükség esetén) a SUSE-ra vonatkozó útmutatóra hivatkozva. A konfigurációt csak **az egyik csomóponton** hajtsa végre. Az elsődleges csomóponton végezze el a műveleteket.

- Fürt rendszerindítási szolgáltatása
- STONITH-eszköz
- A virtuális IP-cím


### <a name="71-cluster-bootstrap-and-more"></a>7,1 fürt rendszerindításának és további
Fürt betöltésének hozzáadása. Hozza létre a fájlt, és adja hozzá a következő szöveget:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
Adja hozzá a konfigurációt a fürthöz.
```
crm configure load update crm-bs.txt
```
![A képernyőképen egy, a c r m parancsot futtató konzolablak egy részét jeleníti meg.](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7,2 STONITH-eszköz
Erőforrás-STONITH hozzáadása. Hozza létre a fájlt, és adja hozzá a következő szöveget.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Adja hozzá a konfigurációt a fürthöz.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7,3 a virtuális IP-cím
Adja hozzá az erőforrás virtuális IP-címét. Hozza létre a fájlt, és adja hozzá a következő szöveget.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Adja hozzá a konfigurációt a fürthöz.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7,4 az erőforrások ellenőrzése

A Command *crm_mon* futtatásakor Itt láthatja a két erőforrást.
![A képernyőfelvételen két erőforrást tartalmazó konzolablak látható.](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Emellett az állapotot a következő helyen tekintheti meg *\<node IP address> : https://: 7630/CIB/Live/State*

![A képernyőfelvételen a két erőforrás állapota látható.](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. a feladatátvételi folyamat tesztelése
A feladatátvételi folyamat teszteléséhez állítsa le a pacemaker szolgáltatást a csomópont1-on, és a Csomópont2 feladatátvételi erőforrásokat.
```
Service pacemaker stop
```
Most állítsa le a pacemaker szolgáltatást a **Csomópont2** -on, és az erőforrások feladatátvételét a **csomópont1**

**Feladatátvétel előtt**  
![A feladatátvétel előtt a képernyőfelvétel a két erőforrás állapotát jeleníti meg.](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Feladatátvétel után**  
![A feladatátvétel után a képernyőfelvétel a két erőforrás állapotát jeleníti meg.](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![A képernyőfelvételen a konzol ablaka látható az erőforrások állapotával a feladatátvétel után.](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. hibaelhárítás
Ez a szakasz a néhány meghibásodási forgatókönyvet ismerteti, amelyek a telepítés során fordulhatnak elő. Előfordulhat, hogy nem feltétlenül kell szembenézni ezekkel a problémákkal.

### <a name="scenario-1-cluster-node-not-online"></a>1. forgatókönyv: a fürtcsomópont nem online állapotú
Ha a csomópontok bármelyike nem jelenik meg online állapotban a Fürtfelügyelőben, a következőket teheti online állapotba.

Az iSCSI szolgáltatás elindítása
```
service iscsid start
```

És most be kell tudnia jelentkezni az adott iSCSI-csomópontba
```
iscsiadm -m node -l
```
A várt kimenet a következőhöz hasonlóan néz ki:
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>2. forgatókönyv: a YaST2 nem jeleníti meg a grafikus nézetet
A YaST2 grafikus képernyő a magas rendelkezésre állású fürt beállítására szolgál a dokumentumban. Ha a YaST2 nem nyílik meg a grafikus ablakon, ahogy az látható, és nem dob QT-hibát, hajtsa végre a következő lépéseket. Ha megnyílik a grafikus ablak, akkor kihagyhatja a lépéseket.

**Hiba**

![A képernyőképen egy konzolablak egy része jelenik meg, amely egy hibaüzenettel jelenik meg.](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Várt kimenet**

![A képernyőképen a magas rendelkezésre állással és a fürttel jelölt YaST-vezérlési központ látható.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Ha a YaST2 nem a grafikus nézettel van megnyitva, kövesse az alábbi lépéseket.

Telepítse a szükséges csomagokat. A "root" felhasználóként kell bejelentkeznie, és a csomagok letöltéséhez vagy telepítéséhez az SMT-t kell beállítania.

A csomagok telepítéséhez használja a YaST>szoftver>a szoftver-felügyeleti>függőségei> "ajánlott csomagok telepítése..." lehetőséget. A következő képernyőfelvétel a várt képernyőket mutatja be.
>[!NOTE]
>Mindkét csomóponton végre kell hajtania a lépéseket, hogy elérhetők legyenek a YaST2 grafikus nézete mindkét csomópontról.

![A képernyőképen egy konzolablak látható, amely a YaST vezérli központot jeleníti meg.](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

A függőségek alatt válassza a "javasolt csomagok telepítése" ![ képernyőképet, amely a telepítési ajánlott csomagokat tartalmazza.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Tekintse át a módosításokat, és nyomja meg az OK gombot

![YaST](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

A csomag telepítésének folytatása képernyőfelvétel: a ![ telepítés előrehaladását megjelenítő konzolablak.](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Kattintson a Next (Tovább) gombra.

![Képernyőfelvétel: a konzol ablaka a sikert jelző üzenettel jelenik meg.](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Kattintson a Befejezés gombra

Telepítenie kell a libqt4 és a libyui-QT csomagokat is.
```
zypper -n install libqt4
```
![A képernyőképen a libqt4-csomag telepítését bemutató konzolablak látható.](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![A képernyőképen a libyui-QT csomag telepítésére szolgáló konzolablak látható. ](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
 ![ A képernyőképen a libyui-QT csomag telepítésére szolgáló konzolablak látható.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
A Yast2 meg kell tudnia nyitni a grafikus nézetet most az itt látható módon.
![Képernyőfelvétel: a YaST Control Center a kiválasztott szoftverekkel és online frissítéssel jelenik meg.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>3. forgatókönyv: a YaST2 nem magas rendelkezésre állású beállítás
Ahhoz, hogy a magas rendelkezésre állás lehetőség megjelenjen a YaST2-vezérlési központban, telepítenie kell a további csomagokat.

A Yast2>szoftverek>a szoftverek felügyeletének használata>válassza ki a következő mintákat

- SAP HANA Server Base
- C/C++ fordító és eszközök
- Magas rendelkezésre állás
- SAP alkalmazáskiszolgáló alapja

Az alábbi képernyőn a minták telepítésének lépései láthatók.

A YaST2 > szoftverek használata > szoftverfejlesztés

![A képernyőképen a YaST Control Center a telepítés megkezdéséhez kiválasztott szoftverekkel és online frissítéssel jelenik meg.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Mintázatok kiválasztása

![Képernyőfelvétel: az első minta kiválasztása a C/C++ fordító és eszközök menüpontban. ](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
 ![ A képernyőfelvételen a C/C++ fordító és az eszközök elemek második mintája látható.](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Kattintson az **elfogadás** gombra.

![Képernyőfelvétel: a módosított csomagok párbeszédpanel, ahol a csomagok módosultak a függőségek feloldásához.](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Kattintson a **Folytatás** gombra.

![A képernyőképen a telepítési állapot végrehajtása lap látható.](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

A telepítés befejezésekor kattintson a **tovább** gombra.

![A képernyőképen a telepítési jelentés látható.](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>4. forgatókönyv: a HANA telepítése meghiúsul a GCC-szerelvények hibája miatt
A HANA telepítése a következő hibával meghiúsul.

![A képernyőképen egy hibaüzenet jelenik meg, miszerint az operációs rendszer nem áll készen a g c 5 szerelvények elvégzésére.](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

A probléma megoldásához a következő módon telepítenie kell a kódtárakat (libgcc_sl és libstdc + 6).

![A képernyőképen a szükséges kódtárak telepítésére szolgáló konzolablak látható.](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>5. forgatókönyv: a pacemaker szolgáltatás nem sikerül

A következő hiba történt a pacemaker szolgáltatás indításakor.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

A javításhoz törölje a következő sort a fájl */usr/lib/systemd/System/fstrim.Timer*

```
Persistent=true
```

![Képernyőfelvétel: az f s Trim-fájl, amely az állandó = igaz értéket fogja törölni.](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>6. forgatókönyv: a 2. csomópont nem tud csatlakozni a fürthöz

Ha a Csomópont2-t a meglévő fürthöz csatlakoztatja a *Ha-cluster-JOIN* parancs használatával, a következő hiba történt.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![A képernyőképen egy konzolablak jelenik meg, amelyen a hibaüzenetet nem lehet lekérni az I P-címről.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

A javításhoz futtassa a következő parancsot mindkét csomóponton.

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![Képernyőfelvétel: az első csomóponton a parancsot futtató konzolablak egy részét jeleníti meg.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![A képernyőképen egy olyan konzolablak látható, amely a második csomóponton a parancsot futtatja.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Az előző javítás után a Csomópont2 hozzá kell adni a fürthöz

![A képernyőképen egy olyan konzolablak látható, amely sikeres, ha-fürthöz illesztési paranccsal rendelkezik.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. általános dokumentáció
A következő cikkekben talál további információt a SUSE HA setup-ról: 

- [SAP HANA SR teljesítményre optimalizált forgatókönyv](https://www.suse.com/support/kb/doc/?id=000019450 )
- [Storage-alapú kerítés](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog – a SAP HANA-1. részhez tartozó pacemaker-fürt használata](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog – a SAP HANA-2. részhez tartozó pacemaker-fürt használata](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
