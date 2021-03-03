---
title: Az IBM DB2-HADR beállítása az Azure Virtual Machines szolgáltatásban a RHEL-on | Microsoft Docs
description: Magas rendelkezésre állású IBM DB2-LUW létrehozása az Azure Virtual Machines (VM) RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: juergent
ms.openlocfilehash: 979de230655b4804fa65a4bddb678e21b59c4ea2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674451"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Az IBM Db2 LUW magas rendelkezésre állása Azure-beli virtuális gépeken Red Hat Enterprise Linux Serveren

Az IBM DB2 for Linux, UNIX és Windows (LUW) a [magas rendelkezésre állású és vész-helyreállítási (HADR) konfigurációban](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) egy olyan csomópontból áll, amely egy elsődleges adatbázis-példányt és legalább egy olyan csomópontot futtat, amely egy másodlagos adatbázis-példányt futtat. Az elsődleges adatbázis példányának módosításait a rendszer a konfigurációtól függően szinkron vagy aszinkron módon replikálja egy másodlagos adatbázis-példányba. 

> [!NOTE]
> Ez a cikk a " *Master* " és a " *Slave*" kifejezésekre mutató hivatkozásokat tartalmaz, amelyeket a Microsoft már nem használ. Ha eltávolítja ezeket a feltételeket a szoftverből, a rendszer eltávolítja őket ebből a cikkből.

Ez a cikk az Azure Virtual Machines (VM) üzembe helyezését és konfigurálását, a fürt keretrendszerének telepítését, valamint az IBM DB2-LUW HADR-konfigurációval való telepítését ismerteti. 

A cikk nem foglalkozik az IBM DB2-LUW telepítésével és konfigurálásával a HADR vagy az SAP szoftver telepítésével. Ezeknek a feladatoknak a megvalósításához az SAP és az IBM telepítési kézikönyvek hivatkozásaira mutató hivatkozásokat biztosítunk. Ez a cikk az Azure-környezetre jellemző részekre összpontosít. 

A támogatott IBM DB2-verziók 10,5-es és újabb verziójúak, az SAP Note [1928533]-ben dokumentálva.

A telepítés megkezdése előtt tekintse meg a következő SAP-megjegyzéseket és dokumentációt:

| SAP-Megjegyzés | Leírás |
| --- | --- |
| [1928533] | SAP-alkalmazások az Azure-ban: támogatott termékek és Azure-beli virtuális gépek típusai |
| [2015553] | SAP az Azure-on: támogatási előfeltételek |
| [2178632] | Az Azure-beli SAP legfontosabb monitorozási mérőszámai |
| [2191498] | SAP Linuxon az Azure-ban: továbbfejlesztett monitorozás |
| [2243692] | Linux on Azure (IaaS) VM: SAP-licenctel kapcsolatos problémák |
| [2002167] | Red Hat Enterprise Linux 7. x: telepítés és frissítés |
| [2694118] | Red Hat Enterprise Linux HA Add-On az Azure-ban |
| [1999351] | Az SAP-hez készült bővített Azure monitoring hibaelhárítása |
| [2233094] | DB6: SAP-alkalmazások az Azure-ban, amelyek az IBM DB2-t használják Linuxra, UNIX-ra és Windowsra – további információk |
| [1612105] | DB6: a HADR-mel kapcsolatos gyakori kérdések |


| Dokumentáció | 
| --- |
| [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): az összes szükséges SAP-Megjegyzés a Linux rendszerhez |
| [Az Azure Virtual Machines tervezése és megvalósítása az SAP-hez Linux rendszeren –][planning-guide] útmutató |
| [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren][deployment-guide] (ez a cikk) |
| [Azure Virtual Machines adatbázis-kezelő rendszer (adatbázisok) üzembe helyezése az SAP-hez Linux][dbms-guide] rendszeren – útmutató |
| [SAP számítási feladatok az Azure tervezési és üzembe helyezési ellenőrzőlistáján][azr-sap-plancheck] |
| [A Red Hat Enterprise Linux 7 magas rendelkezésre állású Add-On áttekintése][rhel-ha-addon] |
| [Magas rendelkezésre állású Add-On felügyelet][rhel-ha-admin] |
| [Magas rendelkezésre állású Add-On referenciája][rhel-ha-ref] |
| [A RHEL magas rendelkezésre állású fürtökre vonatkozó támogatási szabályzatok – Microsoft Azure Virtual Machines a fürt tagjai][rhel-azr-supp]
| [Red Hat Enterprise Linux 7,4 (és újabb) High-Availability fürt telepítése és konfigurálása Microsoft Azure][rhel-azr-inst]
| [IBM Db2 Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz][dbms-db2] |
| [IBM DB2 HADR 11,1][db2-hadr-11.1] |
| [IBM DB2 HADR 10,5][db2-hadr-10.5] |
| [Támogatási szabályzat a RHEL magas rendelkezésre állású fürtökhöz – az IBM DB2 for Linux, UNIX és Windows rendszerű fürtben való kezelése][rhel-db2-supp]



## <a name="overview"></a>Áttekintés
A magas rendelkezésre állás elérése érdekében az IBM DB2 LUW és a HADR legalább két Azure-beli virtuális gépre van telepítve, amelyek egy Azure-beli [rendelkezésre állási csoportba](../../windows/tutorial-availability-sets.md) vagy [Azure Availability Zonesba](./sap-ha-availability-zones.md)vannak telepítve. 

Az alábbi ábrán két adatbázis-kiszolgáló Azure-beli virtuális gép telepítése látható. Az adatbázis-kiszolgáló mindkét Azure-beli virtuális gépe saját tárolóval rendelkezik, és működik. A HADR-ben az egyik Azure-beli virtuális gép egyik adatbázis-példánya rendelkezik az elsődleges példány szerepkörével. Minden ügyfél az elsődleges példányhoz csatlakozik. Az adatbázis-tranzakciókban történt összes változás helyileg, a DB2 tranzakciónaplóban marad. Mivel a tranzakciónapló-rekordok helyileg vannak tárolva, a rekordok továbbítása a TCP/IP protokollon keresztül történik a második adatbázis-kiszolgálón, a készenléti kiszolgálón vagy a készenléti példányon. A készenléti példány frissíti a helyi adatbázist az átvitt tranzakciónapló-rekordok továbbításával. Így a készenléti kiszolgáló szinkronban marad az elsődleges kiszolgálóval.

A HADR csak replikációs funkció. Nem észleli a hibák észlelését, és nincs automatikus átvételi vagy feladatátvételi lehetőség. Az adatbázis-rendszergazdának manuálisan kell megindítania a bevezetést vagy a készenléti kiszolgálóra való átvételt. Az automatikus átvétel és a hibák észlelése érdekében használhatja a Linux pacemaker fürtszolgáltatást. A pacemaker figyeli a két adatbázis-kiszolgáló példányát. Ha az elsődleges adatbázis-kiszolgáló példánya összeomlik, a pacemaker *automatikus* HADR kezdeményez a készenléti kiszolgálótól. A pacemaker azt is biztosítja, hogy a virtuális IP-cím hozzá legyen rendelve az új elsődleges kiszolgálóhoz.

![Az IBM DB2 magas rendelkezésre állásának áttekintése](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Ahhoz, hogy az SAP-alkalmazáskiszolgáló csatlakozni tudjanak az elsődleges adatbázishoz, szüksége lesz egy virtuális gazdagép nevére és egy virtuális IP-címére. Feladatátvétel esetén az SAP-alkalmazás-kiszolgálók új elsődleges adatbázis-példányhoz fognak csatlakozni. Az Azure-környezetekben az [Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) szükséges ahhoz, hogy virtuális IP-címet lehessen használni az IBM DB2 HADR szükséges módon. 

Annak érdekében, hogy teljes mértékben tisztában legyen azzal, hogyan illeszkedik az IBM DB2-LUW a HADR és a pacemaker szolgáltatáshoz, a következő ábra áttekintést nyújt az SAP-rendszerek egy, az IBM DB2-adatbázison alapuló, magasan elérhető beállításáról. Ez a cikk csak az IBM DB2-t ismerteti, de az SAP-rendszer egyéb összetevőinek beállításával kapcsolatos további cikkekre mutató hivatkozásokat tartalmaz.

![IBM DB2 – magas rendelkezésre állású teljes környezet – áttekintés](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>A szükséges lépések magas szintű áttekintése
Az IBM DB2-konfiguráció üzembe helyezéséhez kövesse az alábbi lépéseket:

  + Tervezze meg a környezetét.
  + Telepítse a virtuális gépeket.
  + Frissítse a RHEL Linux rendszerre, és konfigurálja a fájlrendszereket.
  + A pacemaker telepítése és konfigurálása.
  + [Glusterfs-fürt][glusterfs] vagy- [Azure NetApp Files][anf-rhel] beállítása
  + Telepítse a [ASCS/ERS-t egy különálló fürtre][ascs-ha-rhel].
  + Telepítse az IBM DB2-adatbázist elosztott/magas rendelkezésre állási lehetőséggel (SWPM).
  + Telepítsen és hozzon létre egy másodlagos adatbázis-csomópontot és-példányt, és konfigurálja a HADR.
  + Ellenőrizze, hogy a HADR működik-e.
  + Alkalmazza a pacemaker konfigurációját az IBM DB2 vezérlésére.
  + Azure Load Balancer konfigurálása.
  + Telepítse az elsődleges és a párbeszédpanel-alkalmazás kiszolgálóit.
  + Az SAP-alkalmazáskiszolgáló konfigurációjának megkeresése és átalakítása.
  + Feladatátvételi és átvételi tesztek végrehajtása.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Azure-infrastruktúra megtervezése az IBM DB2-LUW az HADR-vel való üzemeltetéséhez

A telepítés végrehajtása előtt fejezze be a tervezési folyamatot. A tervezés létrehozza a DB2 konfigurációjának üzembe helyezését az Azure HADR-ben. Az alábbi táblázatban láthatók azok a kulcsfontosságú elemek, amelyeknek meg kell IMB a DB2-LUW (az SAP-környezet adatbázisának részét képező) tervezéséhez.

| Témakör | Rövid leírás |
| --- | --- |
| Azure-erőforráscsoportok definiálása | Erőforráscsoportok, amelyeken üzembe helyezheti a virtuális gépet, a VNet, a Azure Load Balancert és más erőforrásokat. Lehet meglévő vagy új. |
| Virtuális hálózat/alhálózat definíciója | Ahol az IBM DB2 és Azure Load Balancer rendszerű virtuális gépek üzembe helyezése folyamatban van. Lehet létező vagy újonnan létrehozott. |
| IBM DB2-LUW üzemeltető virtuális gépek | VM-méret, tárterület, hálózatkezelés, IP-cím. |
| Virtuális gazdagép neve és virtuális IP-címe az IBM DB2-adatbázishoz| Az SAP-alkalmazáskiszolgáló csatlakoztatásához használt virtuális IP-cím vagy állomásnév. **db-virt-hostname**, **db-virt-IP**. |
| Azure-kerítés | Megakadályozható a felosztott agyi helyzetek elkerülésének módszere. |
| Azure Load Balancer | Alapszintű vagy standard (ajánlott), mintavételi port a DB2-adatbázishoz (javaslat 62500) mintavételi **port**. |
| Névfeloldás| A névfeloldás működése a környezetben. A DNS-szolgáltatás használata kifejezetten ajánlott. A helyi gazdagépek fájlja használható. |
    
További információ az Azure-beli Linux Pacemakerről: [a pacemaker beállítása Red Hat Enterprise Linux az Azure-ban][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Üzembe helyezés Red Hat Enterprise Linux

Az IBM DB2 LUW erőforrás-ügynöke a Red Hat Enterprise Linux Server HA addon része. A jelen dokumentumban ismertetett beállításhoz a Red Hat Enterprise Linux for SAP használatát kell használnia. Az Azure Marketplace-en Red Hat Enterprise Linux 7,4 for SAP vagy újabb lemezkép található, amely új Azure-beli virtuális gépek üzembe helyezésére használható. Vegye figyelembe a Red Hat által az Azure Marketplace-en keresztül kínált különböző támogatási és szolgáltatási modelleket, ha a virtuálisgép-rendszerképeket az Azure virtuálisgép-piactéren választja ki.

### <a name="hosts-dns-updates"></a>Gazdagépek: DNS-frissítések
Készítsen egy listát az összes állomásnévről, beleértve a virtuális gazdagépek nevét, és frissítse a DNS-kiszolgálókat, hogy a megfelelő IP-címet engedélyezze az állomásnév feloldásához. Ha a DNS-kiszolgáló nem létezik, vagy nem frissíthető és nem hozható létre DNS-bejegyzés, akkor a forgatókönyvben részt vevő egyes virtuális gépek helyi gazdagépeit kell használnia. Ha Host Files-bejegyzéseket használ, győződjön meg arról, hogy a bejegyzéseket az SAP rendszerkörnyezetében lévő összes virtuális gépre alkalmazza a rendszer. Javasoljuk azonban, hogy használja a DNS-t, amely ideális esetben az Azure-ra is kiterjed


### <a name="manual-deployment"></a>Manuális üzembe helyezés

Győződjön meg arról, hogy a kiválasztott operációs rendszer támogatja az IBM/SAP az IBM DB2 LUW. Az Azure-beli virtuális gépek és a DB2-kiadások támogatott operációsrendszer-verzióinak listája a [1928533]-es SAP-megjegyzésben érhető el. Az egyes DB2-kiadásokból származó operációsrendszer-kiadások listája az SAP-termékek rendelkezésre állási mátrixában érhető el. Javasoljuk, hogy az Azure-hoz kapcsolódó teljesítmény-tökéletesítések, illetve a későbbi Red Hat Enterprise Linux-verziók esetében legalább Red Hat Enterprise Linux 7,4 az SAP-hez.

1. Hozzon létre vagy válasszon ki egy erőforráscsoportot.
1. Hozzon létre vagy válasszon ki egy virtuális hálózatot és alhálózatot.
1. Azure-beli rendelkezésre állási csoport létrehozása vagy rendelkezésre állási zóna üzembe helyezése.
    + A rendelkezésre állási csoport esetében állítsa a maximális frissítési tartományt 2 értékre.
1. Hozzon létre egy 1. virtuális gépet.
    + Az Azure Marketplace-en Red Hat Enterprise Linux for SAP-rendszerképet használhat.
    + Válassza ki a 3. lépésben létrehozott Azure rendelkezésre állási készletet, vagy válassza a rendelkezésre állási zóna lehetőséget.
1.  A 2. virtuális gép létrehozása
    + Az Azure Marketplace-en Red Hat Enterprise Linux for SAP-rendszerképet használhat.
    + Válassza ki a 3. lépésben létrehozott Azure rendelkezésre állási készletet, vagy válassza ki a rendelkezésre állási zónát (nem a 3. lépésben megadott zónát).
1. Adja hozzá az adatlemezeket a virtuális gépekhez, majd az [IBM DB2 Azure Virtual Machines adatbázis-kezelő üzembe helyezése az SAP][dbms-db2]számítási feladatához című cikkben talál egy fájlrendszer-telepítő ajánlását.

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Az IBM DB2-LUW és az SAP-környezet telepítése

Az IBM DB2 LUW alapuló SAP-környezet telepítésének megkezdése előtt tekintse át a következő dokumentációt:

+ Azure-dokumentáció
+ SAP-dokumentáció
+ IBM-dokumentáció

A jelen dokumentációra mutató hivatkozásokat a jelen cikk bevezető szakasza tartalmazza.

A NetWeaver-alapú alkalmazások IBM DB2-LUW való telepítéséről a SAP telepítési útmutatójában tájékozódhat.
Az útmutatókat az SAP-Súgó portálon találja az [SAP telepítési útmutatójának Finder][sap-instfind]használatával.

Csökkentheti a portálon megjelenített útmutatók számát a következő szűrők beállításával:
- Szeretnék: "új rendszer telepítése"
- Saját adatbázis: "IBM DB2 for Linux, UNIX és Windows"
- További szűrők az SAP NetWeaver-verziókhoz, a stack-konfigurációhoz vagy az operációs rendszerhez

#### <a name="red-hat-firewall-rules"></a>Red Hat tűzfalszabályok
A Red Hat Enterprise Linux alapértelmezés szerint engedélyezve van a tűzfal. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Telepítési útmutatók az IBM DB2-LUW beállításához a HADR-ben

Az elsődleges IBM DB2 LUW adatbázis-példány beállítása:

- A magas rendelkezésre állás vagy az elosztott lehetőség használata.
- Telepítse az SAP ASCS/ERS és az adatbázis-példányt.
- Készítsen biztonsági másolatot az újonnan telepített adatbázisról.

> [!IMPORTANT] 
> Jegyezze fel a telepítés során beállított "adatbázis-kommunikációs port" kifejezést. Az adatbázis-példányok esetében ugyanazt a portszámot kell megadni.
>![SAP SWPM-port definíciója](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>IBM DB2 HADR-beállítások az Azure-hoz

   Ha Azure pacemaker kerítési ügynököt használ, állítsa be a következő paramétereket:

   - HADR-ablak időtartama (másodperc) (HADR_PEER_WINDOW) = 240  
   - HADR időtúllépési értéke (HADR_TIMEOUT) = 45

Az előző paramétereket a kezdeti feladatátvételi/átvételi tesztelés alapján javasoljuk. Kötelező a feladatátvétel és az átvétel megfelelő működésének tesztelése a paraméterek beállításaival. Mivel az egyes konfigurációk eltérőek lehetnek, a paraméterek módosítást igényelhetnek. 

> [!NOTE]
> Az IBM DB2-re jellemző, HADR-konfigurációval, normál indítással: a másodlagos vagy készenléti adatbázis példányának az elsődleges adatbázis-példány elindítása előtt kell futnia.

   
> [!NOTE]
> Az Azure-hoz és a Pacemakerhez kapcsolódó telepítéshez és konfiguráláshoz: az SAP Software kiépítési kezelőjén keresztüli telepítési eljárás során egyértelmű kérdés van az IBM DB2 LUW magas rendelkezésre állásáról:
>+ Ne válassza az **IBM DB2-pureScale** lehetőséget.
>+ Ne válassza **a többplatformos IBM Tivoli System Automation telepítése** lehetőséget.
>+ Ne válassza a **fürt konfigurációs fájljainak előállítása** lehetőséget.
>![SAP-SWPM – DB2 HA-beállítások](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Ha a készenléti adatbázis-kiszolgálót az SAP homogén rendszermásolási eljárással szeretné beállítani, hajtsa végre a következő lépéseket:

1. Válassza a **rendszermásolási** lehetőség > a **célként kijelölt rendszerek**  >  **elosztott**  >  **adatbázis-példányát**.
1. Másolási módszerként válassza a **homogén rendszer** lehetőséget, hogy a biztonsági mentés használatával visszaállítsa a biztonsági mentést a készenléti kiszolgáló példányán.
1. Amikor eléri a kilépési lépést az adatbázis homogén rendszermásolásra való visszaállításához, lépjen ki a telepítőből. Állítsa vissza az adatbázist az elsődleges gazdagép biztonsági másolatából. Az összes további telepítési fázis már végre lett hajtva az elsődleges adatbázis-kiszolgálón.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Red Hat tűzfalszabályok DB2-HADR
Tűzfalszabályok hozzáadásával engedélyezheti a (z) és a HADR közötti adatforgalmat a DB2 és az DB2 között:
+ Adatbázis-kommunikációs port. Ha partíciót használ, adja hozzá ezeket a portokat is.
+ HADR port (DB2-paraméter értéke HADR_LOCAL_SVC)
+ Azure mintavételi port
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM DB2 HADR-vizsgálat
A bemutató és a jelen cikkben ismertetett eljárások esetében az adatbázis SID **ID2**.

Miután konfigurálta a HADR, és az állapota EGYENRANGÚ, és csatlakoztatva van az elsődleges és a készenléti csomóponton, hajtsa végre a következő ellenőrzéseket:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N


#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N
</code></pre>

### <a name="configure-azure-load-balancer"></a>Az Azure Load Balancer konfigurálása

Azure Load Balancer konfigurálásához javasoljuk, hogy az [Azure standard Load BALANCER SKU](../../../load-balancer/load-balancer-overview.md) -t használja, majd tegye a következőket:

> [!NOTE]
> A standard Load Balancer SKU korlátozza a nyilvános IP-címek elérését a Load Balancer alatti csomópontok között. Az [Azure standard Load Balancer az SAP magas rendelkezésre állási forgatókönyvekben való használatával történő Virtual Machines nyilvános végponti kapcsolata](./high-availability-guide-standard-load-balancer-outbound-connections.md) című cikk leírja, hogyan engedélyezheti a csomópontok számára a nyilvános IP-címek elérését.

> [!IMPORTANT]
> A lebegő IP-címek nem támogatottak a terheléselosztási helyzetekben a hálózati adapter másodlagos IP-konfigurációjában. További részletek: az [Azure Load Balancer korlátozásai](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Ha a virtuális gép további IP-címére van szüksége, helyezzen üzembe egy második hálózati adaptert.  

1. Előtér-IP-címkészlet létrehozása:

   a. A Azure Portal nyissa meg a Azure Load Balancer, válassza a előtéri **IP-készlet** lehetőséget, majd kattintson a **Hozzáadás** gombra.

   b. Adja meg az új előtér-IP-készlet nevét (például **DB2-kapcsolatok**).

   c. Állítsa a **hozzárendelést** **statikus** értékre, és adja meg az elején megadott IP **-cím virtuális IP-** címét.

   d. Válassza az **OK** lehetőséget.

   e. Az új előtér-IP-készlet létrehozása után jegyezze fel a készlet IP-címét.

1. Háttérbeli készlet létrehozása:

   a. A Azure Portal nyissa meg a Azure Load Balancer, válassza ki a **háttér-készletek** elemet, majd kattintson a **Hozzáadás** gombra.

   b. Adja meg az új háttér-készlet nevét (például **DB2-backend**).

   c. Válassza **a virtuális gép hozzáadása** lehetőséget.

   d. Válassza ki a rendelkezésre állási készletet, vagy az előző lépésben létrehozott IBM DB2-adatbázist futtató virtuális gépeket.

   e. Válassza ki az IBM DB2-fürthöz tartozó virtuális gépeket.

   f. Válassza az **OK** lehetőséget.

1. Állapot mintavételének létrehozása:

   a. A Azure Portal nyissa meg a Azure Load Balancer, válassza az **állapot**-tesztek elemet, majd kattintson a **Hozzáadás** gombra.

   b. Adja meg az új állapot-mintavétel nevét (például **DB2-HP**).

   c. Válassza a **TCP** lehetőséget a protokoll és a **62500**-es port közül. Tartsa meg az **intervallum** értékét **5** értékre, és tartsa meg a nem kifogástalan **állapot küszöbértékét** **2** értékre.

   d. Válassza az **OK** lehetőséget.

1. Hozza létre a terheléselosztási szabályokat:

   a. A Azure Portal nyissa meg a Azure Load Balancer, válassza a terheléselosztási **szabályok** lehetőséget, majd kattintson a **Hozzáadás** gombra.

   b. Adja meg az új Load Balancer szabály nevét (például **DB2-SID**).

   c. Válassza ki az előtér-IP-címet, a háttér-készletet és a korábban létrehozott állapot-mintavételt (például **DB2-frontend**).

   d. Tartsa a **protokollt** **TCP**-re, és írja be a port *adatbázis kommunikációs portját*.

   e. Növelje az **üresjárati időkorlátot** 30 percre.

   f. Ügyeljen arra, hogy a **lebegő IP-címet engedélyezze**.

   : Válassza az **OK** lehetőséget.

**[A]** tűzfalszabály hozzáadása a mintavételi porthoz:

<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

## <a name="create-the-pacemaker-cluster"></a>A pacemaker-fürt létrehozása
    
Ha alapszintű pacemaker-fürtöt szeretne létrehozni ehhez az IBM DB2-kiszolgálóhoz, tekintse [meg a pacemaker beállítása Red Hat Enterprise Linux az Azure-ban][rhel-pcs-azr]című témakört. 

## <a name="db2-pacemaker-configuration"></a>DB2-pacemaker konfigurálása

Ha a csomópont meghibásodása esetén a pacemakert használja az automatikus feladatátvételhez, akkor ennek megfelelően konfigurálnia kell a DB2-példányokat és a pacemakert. Ez a szakasz az ilyen típusú konfigurációkat ismerteti.

A következő elemek előtaggal vannak ellátva:

- **[A]**: az összes csomópontra érvényes
- **[1]**: csak az 1. csomópontra érvényes 
- **[2]**: csak a 2. csomópontra vonatkozik

**[A]** a pacemaker konfigurálásának előfeltételei:
1. Állítsa le mindkét adatbázis-kiszolgálót a db2stop-mel rendelkező User DB2- \<sid> vel.
1. A/bin/ksh-re vonatkozó rendszerhéj \<sid> -környezet módosítása:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>  

### <a name="pacemaker-configuration"></a>Pacemaker-konfiguráció

**[1]** IBM DB2 HADR-specifikus pacemaker-konfiguráció:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** IBM DB2-erőforrások létrehozása:

Ha a **RHEL 7. x verzióban** hoz létre fürtöt, használja a következő parancsokat:

<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

Ha a **RHEL 8. x verzióban** hoz létre fürtöt, használja a következő parancsokat:

<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' promotable meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-clone meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-clone

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-clone then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** az IBM DB2-erőforrások elindítása:
* Állítsa be a pacemakert a karbantartási módból.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** ellenőrizze, hogy a fürt állapota rendben van-e, és hogy az összes erőforrás el van-e indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.
<pre><code>sudo pcs status
2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

> [!IMPORTANT]
> A pacemaker fürtözött DB2-példányt a pacemaker Tools használatával kell felügyelni. Ha DB2-parancsokat (például db2stop) használ, a pacemaker észleli a műveletet az erőforrás meghibásodása esetén. Ha karbantartást végez, a csomópontokat vagy erőforrásokat a karbantartási módba helyezheti. A pacemaker felfüggeszti a figyelési erőforrásokat, és normál DB2 felügyeleti parancsokat is használhat.

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Az SAP-profilok módosítása virtuális IP-cím használatára a kapcsolódáshoz
A HADR-konfiguráció elsődleges példányához való kapcsolódáshoz az SAP-alkalmazás rétegének a Azure Load Balancerhoz megadott és konfigurált virtuális IP-címet kell használnia. A következő módosítások szükségesek:

/sapmnt/ \<SID> /Profile/default. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/ \<SID> /global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>

## <a name="install-primary-and-dialog-application-servers"></a>Az elsődleges és a párbeszédpanel-alkalmazás-kiszolgálók telepítése

Ha az elsődleges és a párbeszédpaneles alkalmazás-kiszolgálókat egy DB2 HADR-konfigurációval telepíti, használja a konfigurációhoz kiválasztott virtuális gazdagép nevét.

Ha a telepítést a DB2 HADR-konfiguráció létrehozása előtt végezte el, végezze el a módosításokat az előző szakaszban leírtak szerint, és az SAP Java-stackek esetében az alábbiak szerint.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java vagy Java stack Systems JDBC URL-cím-ellenőrzési

A J2EE konfigurációs eszköz használatával megvizsgálhatja vagy frissítheti a JDBC URL-címet. Mivel a J2EE konfigurációs eszköz egy grafikus eszköz, telepítenie kell az X Servert:
 
1. Jelentkezzen be a J2EE-példány elsődleges alkalmazáskiszolgáló kiszolgálójára, és hajtsa végre a következőt:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. A bal oldali keretben válassza a **biztonsági tároló** elemet.
1. A jobb oldali keretben válassza ki a kulcsot `jdbc/pool/\<SAPSID>/url` .
1. Módosítsa az állomásnév nevét a JDBC URL-címében a virtuális állomásnévre.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. Válassza a **Hozzáadás** lehetőséget.
1. A módosítások mentéséhez válassza a bal felső sarokban található lemez ikont.
1. A konfigurációs eszköz bezárásához.
1. Indítsa újra a Java-példányt.

## <a name="configure-log-archiving-for-hadr-setup"></a>A HADR-telepítő naplózásának konfigurálása

Azt javasoljuk, hogy a HADR beállításához a DB2-naplók archiválását konfigurálja úgy, hogy az elsődleges és a készenléti adatbázist is konfigurálja úgy, hogy az automatikus napló-lekérési funkció az összes log Archive-helyről beolvasható legyen. Mind az elsődleges, mind a készenléti adatbázisnak képesnek kell lennie a naplófájlok archiválására az összes olyan napló archiválási helyről, amelybe az egyik adatbázis-példány archiválhatja a naplófájlokat. 

A naplózási archiválást csak az elsődleges adatbázis hajtja végre. Ha megváltoztatja az adatbázis-kiszolgálók HADR-szerepköreit, vagy ha hiba történik, az új elsődleges adatbázis feladata a naplózás archiválása. Ha több log Archive-helyet állított be, előfordulhat, hogy a naplók kétszer is archiválva lesznek. Helyi vagy távoli felzárkózás esetén előfordulhat, hogy manuálisan át kell másolnia az archivált naplókat a régi elsődleges kiszolgálóról az új elsődleges kiszolgáló aktív naplójának helyére.

Javasoljuk, hogy olyan közös NFS-megosztást vagy GlusterFS állítson be, amelyben mindkét csomópontból a naplók íródnak. Az NFS-megosztásnak vagy-GlusterFS nagyon elérhetőnek kell lennie. 

Használhatja a meglévő, magasan elérhető NFS-megosztásokat vagy GlusterFS a szállításokhoz vagy a profilok címtárához. További információkért lásd:

- [Red Hat Enterprise Linuxon futó Azure-beli virtuális gépeken üzemelő GlusterFS SAP NetWeaverhez][glusterfs] 
- [Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver-hez Red Hat Enterprise Linuxon Azure NetApp Files SAP-alkalmazásokhoz][anf-rhel]
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (NFS-megosztások létrehozásához)

## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

Ez a szakasz azt ismerteti, hogyan tesztelheti a DB2 HADR-telepítőjét. Minden teszt feltételezi, hogy az IBM DB2 elsődleges az az *-idb01* virtuális gépen fut. A sudo jogosultságokkal vagy gyökérrel (nem ajánlott) rendelkező felhasználót kell használni.

Az összes tesztelési eset kezdeti állapotát itt találja: (crm_mon-r vagy PC-k állapota)

- a **számítógépek állapota** a pacemaker állapotának pillanatképe a végrehajtási időpontban 
- **crm_mon – r** a szívritmus-állapot folyamatos kimenete

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

Az SAP-rendszer eredeti állapota dokumentálva van a Transaction DBACOCKPIT > Configuration > áttekintésében, ahogy az alábbi képen látható:

![DBACockpit – előzetes áttelepítés](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)

### <a name="test-takeover-of-ibm-db2"></a>Az IBM DB2 átvételének tesztelése


> [!IMPORTANT] 
> A teszt elkezdése előtt győződjön meg a következőket:
> * A pacemaker nem rendelkezik sikertelen műveletekkel (számítógépek állapota).
> * Nincsenek megkötések (az áttelepítési teszt maradékai)
> * Az IBM DB2 HADR szinkronizálása működik. Keresés DB2-vel\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>

Telepítse át az elsődleges DB2-adatbázist futtató csomópontot a következő parancs végrehajtásával:
<pre><code># On RHEL 7.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-clone --master
</code></pre>

Az áttelepítés befejezése után a CRM-állapot kimenete a következőképpen néz ki:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

Az SAP-rendszer eredeti állapota dokumentálva van a Transaction DBACOCKPIT > Configuration > áttekintésében, ahogy az alábbi képen látható:

![DBACockpit – áttelepítés utáni](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Az erőforrás-Migrálás a "számítógépek erőforrás-áthelyezéssel" a hely megkötéseit hozza létre. Ebben az esetben a hely megkötései akadályozzák az IBM DB2-példány futtatását az az-idb01-on. Ha a hely megkötéseit nem törli, az erőforrás nem tud visszavenni a feladatokat.

Távolítsa el a hely korlátozását, és a készenléti csomópontot az az-idb01-on fogja elindítani.
<pre><code># On RHEL 7.x
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource clear Db2_HADR_<b>ID2</b>-clone</code></pre>

És a fürt állapota a következőre változik:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit – eltávolított hely korlátozása](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)

Telepítse át az erőforrást az *az-idb01-* be, és törölje a hely megkötéseit
<pre><code># On RHEL 7.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-clone --master
sudo pcs resource clear Db2_HADR_<b>ID2</b>-clone</code></pre>

- **RHEL 7. x-PC-k erőforrás áthelyezése \<res_name> <host> :** hely megkötésének létrehozása, és problémák merülhetnek fel az átvétel során
- **RHEL 8. x-PC-k erőforrás áthelyezése \<res_name> – főkiszolgáló:** megkötések létrehozása, és problémák merülhetnek fel az átvétel során
- **számítógépek erőforrásának \<res_name> törlése**: törli a hely megkötéseit
- **számítógépek erőforrás- \<res_name> karbantartása**: törli az erőforrás összes hibáját

### <a name="test-a-manual-takeover"></a>Manuális átvétel tesztelése

Az az *-idb01* csomóponton található pacemaker szolgáltatás leállításával manuális átvételt is kipróbálhat:
<pre><code>systemctl stop pacemaker</code></pre>

állapot az *az-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

A feladatátvételt követően újra elindíthatja a szolgáltatást az *az-idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>A HADR elsődleges adatbázisát futtató csomóponton található DB2-folyamat leölése

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

A DB2-példány sikertelen lesz, és a pacemaker áthelyezi a fő csomópontot, és a következő állapotot fogja jelenteni:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

A pacemaker újrakezdi a DB2 elsődleges adatbázis példányát ugyanazon a csomóponton, vagy feladatátvételt hajt végre a másodlagos adatbázis-példányt futtató csomóponton, és hibát jelez.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>A másodlagos adatbázis-példányt futtató csomóponton található DB2-folyamat leölése

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

A csomópont meghiúsult és jelentett hibát jelzett.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

A DB2-példány újra lesz indítva a korábban hozzárendelt másodlagos szerepkörben.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Az adatbázis leállítása az elsődleges HADR futtató csomóponton a db2stop kényszerített használatával

Felhasználó DB2- \<sid> db2stop kényszerítési parancsának futtatása:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Hiba észlelhető:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

A DB2 HADR másodlagos adatbázis-példánya előléptetve lett az elsődleges szerepkörbe.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Összeomlik a virtuális gép, amely a HADR elsődleges adatbázis-példányát futtatja a "állj" értékkel

<pre><code>#Linux kernel panic.
sudo echo b > /proc/sysrq-trigger</code></pre>

Ebben az esetben a pacemaker azt fogja érzékelni, hogy az elsődleges adatbázis-példányt futtató csomópont nem válaszol.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

A következő lépés a *felosztott agyi* helyzetek keresése. Ha a túlélő csomópont azt állapította meg, hogy az elsődleges adatbázis-példányt legutóbb futtató csomópont nem fut, a rendszer végrehajtja az erőforrások feladatátvételét.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


Kernel pánik esetén a rendszer a meghibásodott csomópontot a kerítés ügynöke fogja megbámulni. Miután a meghibásodott csomópont újra online állapotba került, el kell indítania a pacemaker-fürtöt
<pre><code>sudo pcs cluster start</code></pre> elindítja a DB2-példányt a másodlagos szerepkörbe.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>Következő lépések
- [Magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver-hoz](./sap-high-availability-architecture-scenarios.md)
- [A pacemaker beállítása Red Hat Enterprise Linux az Azure-ban][rhel-pcs-azr]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
