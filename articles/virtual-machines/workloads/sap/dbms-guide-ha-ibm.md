---
title: Az IBM DB2-HADR beállítása Azure-beli virtuális gépeken (VM-EK) | Microsoft Docs
description: Magas rendelkezésre állású IBM DB2-LUW létrehozása Azure-beli virtuális gépeken (VM).
author: msjuergent
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 10/16/2020
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: faafce32c3452a5c4ff08783ec2edd28f7f961e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671889"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Az IBM DB2-LUW magas rendelkezésre állása Azure-beli virtuális gépeken SUSE Linux Enterprise Server a pacemakerrel

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
| [1984787] | SUSE LINUX Enterprise Server 12: telepítési megjegyzések |
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
| [SUSE Linux Enterprise Server for SAP Applications 12 SP4 – ajánlott eljárások útmutatók][sles-for-sap-bp] |
| [SUSE Linux Enterprise magas rendelkezésre állású bővítmény 12 SP4][sles-ha-guide] |
| [IBM Db2 Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz][dbms-db2] |
| [IBM DB2 HADR 11,1][db2-hadr-11.1] |
| [IBM DB2 HADR R 10,5][db2-hadr-10.5] |

## <a name="overview"></a>Áttekintés
A magas rendelkezésre állás elérése érdekében az IBM DB2 LUW és a HADR legalább két Azure-beli virtuális gépre van telepítve, amelyek egy Azure-beli [rendelkezésre állási csoportba](../../windows/tutorial-availability-sets.md) vagy [Azure Availability Zonesba](./sap-ha-availability-zones.md)vannak telepítve. 

Az alábbi ábrán két adatbázis-kiszolgáló Azure-beli virtuális gép telepítése látható. Az adatbázis-kiszolgáló mindkét Azure-beli virtuális gépe saját tárolóval rendelkezik, és működik. A HADR-ben az egyik Azure-beli virtuális gép egyik adatbázis-példánya rendelkezik az elsődleges példány szerepkörével. Minden ügyfél csatlakozik ehhez az elsődleges példányhoz. Az adatbázis-tranzakciókban történt összes változás helyileg, a DB2 tranzakciónaplóban marad. Mivel a tranzakciónapló-rekordok helyileg vannak tárolva, a rekordok továbbítása a TCP/IP protokollon keresztül történik a második adatbázis-kiszolgálón, a készenléti kiszolgálón vagy a készenléti példányon. A készenléti példány frissíti a helyi adatbázist az átvitt tranzakciónapló-rekordok továbbításával. Így a készenléti kiszolgáló szinkronban marad az elsődleges kiszolgálóval.

A HADR csak replikációs funkció. Nem észleli a hibák észlelését, és nincs automatikus átvételi vagy feladatátvételi lehetőség. Az adatbázis-rendszergazdának manuálisan kell megindítania a bevezetést vagy a készenléti kiszolgálóra való átvételt. Az automatikus átvétel és a hibák észlelése érdekében használhatja a Linux pacemaker fürtszolgáltatást. A pacemaker figyeli a két adatbázis-kiszolgáló példányát. Ha az elsődleges adatbázis-kiszolgáló példánya összeomlik, a pacemaker *automatikus* HADR kezdeményez a készenléti kiszolgálótól. A pacemaker azt is biztosítja, hogy a virtuális IP-cím hozzá legyen rendelve az új elsődleges kiszolgálóhoz.

![Az IBM DB2 magas rendelkezésre állásának áttekintése](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Ahhoz, hogy az SAP-alkalmazáskiszolgáló csatlakozni tudjanak az elsődleges adatbázishoz, szüksége lesz egy virtuális gazdagép nevére és egy virtuális IP-címére. Feladatátvétel esetén az SAP-alkalmazás-kiszolgálók új elsődleges adatbázis-példányhoz fognak csatlakozni. Az Azure-környezetekben az [Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) szükséges ahhoz, hogy virtuális IP-címet lehessen használni az IBM DB2 HADR szükséges módon. 

Annak érdekében, hogy teljes mértékben tisztában legyen azzal, hogyan illeszkedik az IBM DB2-LUW a HADR és a pacemaker szolgáltatáshoz, a következő ábra áttekintést nyújt az SAP-rendszerek egy, az IBM DB2-adatbázison alapuló, magasan elérhető beállításáról. Ez a cikk csak az IBM DB2-t ismerteti, de az SAP-rendszer egyéb összetevőinek beállításával kapcsolatos további cikkekre mutató hivatkozásokat tartalmaz.

![IBM DB2 – magas rendelkezésre állású teljes környezet – áttekintés](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>A szükséges lépések magas szintű áttekintése
Az IBM DB2-konfiguráció üzembe helyezéséhez kövesse az alábbi lépéseket:

  + Tervezze meg a környezetét.
  + Telepítse a virtuális gépeket.
  + A SUSE Linux frissítése és a fájlrendszerek konfigurálása.
  + A pacemaker telepítése és konfigurálása.
  + Telepítse a [kiválóan elérhető NFS][nfs-ha]-t.
  + Telepítse a [ASCS/ERS-t egy különálló fürtre][ascs-ha].
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
| Azure-kerítés | Azure-kerítés vagy SBD-kerítés (kifejezetten ajánlott). Módszer a felosztott agyi helyzetek elkerülésére. |
| SBD VM | SBD virtuális gép mérete, tárterülete, hálózata. |
| Azure Load Balancer | Alapszintű vagy standard (ajánlott), mintavételi port a DB2-adatbázishoz (javaslat 62500) mintavételi **port**. |
| Névfeloldás| A névfeloldás működése a környezetben. A DNS-szolgáltatás használata kifejezetten ajánlott. A helyi gazdagépek fájlja használható. |
    
További információ az Azure-beli Linux Pacemakerről: [a pacemaker beállítása SUSE Linux Enterprise Server az Azure-ban](./high-availability-guide-suse-pacemaker.md).

## <a name="deployment-on-suse-linux"></a>Üzembe helyezés SUSE Linux rendszeren

Az IBM DB2 LUW erőforrás-ügynöke az SAP-alkalmazások SUSE Linux Enterprise Server részét képezi. A jelen dokumentumban ismertetett beállításhoz SUSE Linux Servert kell használnia az SAP-alkalmazásokhoz. Az Azure piactéren megtalálható a SUSE Enterprise Server for SAP Applications 12 rendszer lemezképe, amely az új Azure-beli virtuális gépek üzembe helyezésére használható. Vegye figyelembe a SUSE által az Azure Marketplace-en keresztül kínált különböző támogatási vagy szolgáltatási modelleket, ha a virtuálisgép-rendszerképeket az Azure virtuálisgép-piactéren választja ki. 

### <a name="hosts-dns-updates"></a>Gazdagépek: DNS-frissítések
Készítsen egy listát az összes állomásnévről, beleértve a virtuális gazdagépek nevét, és frissítse a DNS-kiszolgálókat, hogy a megfelelő IP-címet engedélyezze az állomásnév feloldásához. Ha a DNS-kiszolgáló nem létezik, vagy nem frissíthető és nem hozható létre DNS-bejegyzés, akkor a forgatókönyvben részt vevő egyes virtuális gépek helyi gazdagépeit kell használnia. Ha Host Files-bejegyzéseket használ, győződjön meg arról, hogy a bejegyzéseket az SAP rendszerkörnyezetében lévő összes virtuális gépre alkalmazza a rendszer. Javasoljuk azonban, hogy használja a DNS-t, amely ideális esetben az Azure-ra is kiterjed


### <a name="manual-deployment"></a>Manuális üzembe helyezés

Győződjön meg arról, hogy a kiválasztott operációs rendszer támogatja az IBM/SAP az IBM DB2 LUW. Az Azure-beli virtuális gépek és a DB2-kiadások támogatott operációsrendszer-verzióinak listája a [1928533]-es SAP-megjegyzésben érhető el. Az egyes DB2-kiadásokból származó operációsrendszer-kiadások listája az SAP-termékek rendelkezésre állási mátrixában érhető el. Ezen vagy későbbi SUSE Linux-verziókban az Azure-hoz kapcsolódó teljesítménybeli változások miatt legalább 12 SLES ajánlott.

1. Hozzon létre vagy válasszon ki egy erőforráscsoportot.
1. Hozzon létre vagy válasszon ki egy virtuális hálózatot és alhálózatot.
1. Azure-beli rendelkezésre állási csoport létrehozása vagy rendelkezésre állási zóna üzembe helyezése.
    + A rendelkezésre állási csoport esetében állítsa a maximális frissítési tartományt 2 értékre.
1. Hozzon létre egy 1. virtuális gépet.
    + Használja az SLES for SAP-rendszerképet az Azure Marketplace-en.
    + Válassza ki a 3. lépésben létrehozott Azure rendelkezésre állási készletet, vagy válassza a rendelkezésre állási zóna lehetőséget.
1.  A 2. virtuális gép létrehozása
    + Használja az SLES for SAP-rendszerképet az Azure Marketplace-en.
    + Válassza ki a 3. lépésben létrehozott Azure rendelkezésre állási készletet, vagy válassza ki a rendelkezésre állási zónát (nem a 3. lépésben megadott zónát).
1. Adja hozzá az adatlemezeket a virtuális gépekhez, majd az [IBM DB2 Azure Virtual Machines adatbázis-kezelő üzembe helyezése az SAP][dbms-db2]számítási feladatához című cikkben talál egy fájlrendszer-telepítő ajánlását.

## <a name="create-the-pacemaker-cluster"></a>A pacemaker-fürt létrehozása
    
Ha alapszintű pacemaker-fürtöt szeretne létrehozni ehhez az IBM DB2-kiszolgálóhoz, tekintse [meg a pacemaker beállítása SUSE Linux Enterprise Server az Azure-ban][sles-pacemaker]című témakört. 

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

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Telepítési útmutatók az IBM DB2-LUW beállításához a HADR-ben

Az elsődleges IBM DB2 LUW adatbázis-példány beállítása:

- A magas rendelkezésre állás vagy az elosztott lehetőség használata.
- Telepítse az SAP ASCS/ERS és az adatbázis-példányt.
- Készítsen biztonsági másolatot az újonnan telepített adatbázisról.


> [!IMPORTANT] 
> Jegyezze fel a telepítés során beállított "adatbázis-kommunikációs port" kifejezést. Az adatbázis-példányok azonos portszámának kell lennie

Ha a készenléti adatbázis-kiszolgálót az SAP homogén rendszermásolási eljárással szeretné beállítani, hajtsa végre a következő lépéseket:

1. Válassza a **rendszermásolási** lehetőség > a **célként kijelölt rendszerek**  >  **elosztott**  >  **adatbázis-példányát**.
1. Másolási módszerként válassza a **homogén rendszer** lehetőséget, hogy a biztonsági mentés használatával visszaállítsa a biztonsági mentést a készenléti kiszolgáló példányán.
1. Amikor eléri a kilépési lépést az adatbázis homogén rendszermásolásra való visszaállításához, lépjen ki a telepítőből. Állítsa vissza az adatbázist az elsődleges gazdagép biztonsági másolatából. Az összes további telepítési fázis már végre lett hajtva az elsődleges adatbázis-kiszolgálón.
1. HADR beállítása az IBM DB2-hez.

   > [!NOTE]
   > Az Azure-hoz és a Pacemakerhez kapcsolódó telepítéshez és konfiguráláshoz: az SAP Software kiépítési kezelőjén keresztüli telepítési eljárás során egyértelmű kérdés van az IBM DB2 LUW magas rendelkezésre állásáról:
   >+ Ne válassza az **IBM DB2-pureScale** lehetőséget.
   >+ Ne válassza **a többplatformos IBM Tivoli System Automation telepítése** lehetőséget.
   >+ Ne válassza a **fürt konfigurációs fájljainak előállítása** lehetőséget.

   Ha a Linux Pacemakerhez SBD eszközt használ, állítsa be a következő DB2 HADR paramétereket:
   + HADR-ablak időtartama (másodperc) (HADR_PEER_WINDOW) = 300  
   + HADR időtúllépési értéke (HADR_TIMEOUT) = 60

   Ha Azure pacemaker kerítési ügynököt használ, állítsa be a következő paramétereket:
   + HADR-ablak időtartama (másodperc) (HADR_PEER_WINDOW) = 900  
   + HADR időtúllépési értéke (HADR_TIMEOUT) = 60

Az előző paramétereket a kezdeti feladatátvételi/átvételi tesztelés alapján javasoljuk. Kötelező a feladatátvétel és az átvétel megfelelő működésének tesztelése a paraméterek beállításaival. Mivel az egyes konfigurációk eltérőek lehetnek, a paraméterek módosítást igényelhetnek. 

> [!IMPORTANT]
> Az IBM DB2-re jellemző, HADR-konfigurációval, normál indítással: a másodlagos vagy készenléti adatbázis példányának az elsődleges adatbázis-példány elindítása előtt kell futnia.

A bemutató és a jelen cikkben ismertetett eljárások esetében az adatbázis SID-je **PTR**.

#### <a name="ibm-db2-hadr-check"></a>IBM DB2 HADR-vizsgálat
Miután konfigurálta a HADR, és az állapota EGYENRANGÚ, és csatlakoztatva van az elsődleges és a készenléti csomóponton, hajtsa végre a következő ellenőrzéseket:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>DB2-pacemaker konfigurálása

Ha a csomópont meghibásodása esetén a pacemakert használja az automatikus feladatátvételhez, akkor ennek megfelelően konfigurálnia kell a DB2-példányokat és a pacemakert. Ez a szakasz az ilyen típusú konfigurációkat ismerteti.

A következő elemek előtaggal vannak ellátva:

- **[A]**: az összes csomópontra érvényes
- **[1]**: csak az 1. csomópontra érvényes 
- **[2]**: csak a 2. csomópontra vonatkozik

**[A]** a pacemaker konfigurálásának előfeltételei:
1. Állítsa le mindkét adatbázis-kiszolgálót a db2stop-mel rendelkező User DB2- \<sid> vel.
1. Változtassa meg a/bin/ksh a rendszerhéj-környezetet a DB2 \<sid> -felhasználó számára.  Javasoljuk, hogy használja a YaST eszközt. 


### <a name="pacemaker-configuration"></a>Pacemaker-konfiguráció

> [!IMPORTANT]
> A közelmúltbeli tesztelés feltárta a helyzeteket, ahol a netcat nem válaszol a várakozó kérelmekre, és csak egyetlen kapcsolat kezelésére vonatkozó korlátozásokat okoz. A netcat erőforrás nem figyeli az Azure Load Balancer kéréseit, és a lebegőpontos IP-cím elérhetetlenné válik.  
> A meglévő pacemaker-fürtök esetében a netcat és a socat helyett a múltban javasolt. Jelenleg a csomag erőforrás-ügynökök részét képező Azure-LB erőforrás-ügynök használatát javasoljuk a következő csomag-verzióra vonatkozó követelményekkel:
> - A SLES 12 SP4/SP5 esetében a verziónak legalább Resource-Agent-4.3.018. a7fb5035-3.30.1 kell lennie.  
> - A SLES 15/15 SP1 esetében a verziónak legalább Resource-Agent-4.3.0184.6 ee15eb2-4.13.1 kell lennie.  
>
> Vegye figyelembe, hogy a módosítás rövid állásidőt igényel.  
> Meglévő pacemaker-fürtök esetén, ha a konfigurációt már úgy módosították, hogy a socat használja az [azure Load-Balancer észlelésének megerősítése](https://www.suse.com/support/kb/doc/?id=7024128)című cikkben leírtak szerint, nem kell azonnal váltania az Azure-LB erőforrás-ügynökre.

**[1]** IBM DB2 HADR-specifikus pacemaker-konfiguráció:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** IBM DB2-erőforrások létrehozása:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** az IBM DB2-erőforrások elindítása:
* Állítsa be a pacemakert a karbantartási módból.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** ellenőrizze, hogy a fürt állapota rendben van-e, és hogy az összes erőforrás el van-e indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 konfigurált csomópont
# <a name="5-resources-configured"></a>5 konfigurált erőforrás

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Erőforrások teljes listája:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-SBD (stonith: External/SBD): elindítva azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Erőforráscsoport: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (OCF:: szívverés: IPaddr2): elindítva azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (OCF:: szívverés: Azure-LB): elindítva a azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Master/slave set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Főkiszolgálók: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Rabszolgák: [azibmdb01]
</pre>

> [!IMPORTANT]
> A pacemaker fürtözött DB2-példányt a pacemaker Tools használatával kell felügyelni. Ha DB2-parancsokat (például db2stop) használ, a pacemaker észleli a műveletet az erőforrás meghibásodása esetén. Ha karbantartást végez, a csomópontokat vagy erőforrásokat a karbantartási módba helyezheti. A pacemaker felfüggeszti a figyelési erőforrásokat, és normál DB2 felügyeleti parancsokat is használhat.


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
 
1. Jelentkezzen be a J2EE-példány elsődleges alkalmazáskiszolgáló kiszolgálójára, és hajtsa végre a következőt:   `sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. A bal oldali keretben válassza a **biztonsági tároló** elemet.
1. A jobb oldali keretben válassza a JDBC/Pool//URL. elemet. \<SAPSID>
1. Módosítsa az állomásnév nevét a JDBC URL-címében a virtuális állomásnévre.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Válassza a **Hozzáadás** lehetőséget.
1. A módosítások mentéséhez válassza a bal felső sarokban található lemez ikont.
1. A konfigurációs eszköz bezárásához.
1. Indítsa újra a Java-példányt.

## <a name="configure-log-archiving-for-hadr-setup"></a>A HADR-telepítő naplózásának konfigurálása
Azt javasoljuk, hogy a HADR beállításához a DB2-naplók archiválását konfigurálja úgy, hogy az elsődleges és a készenléti adatbázist is konfigurálja úgy, hogy az automatikus napló-lekérési funkció az összes log Archive-helyről beolvasható legyen. Mind az elsődleges, mind a készenléti adatbázisnak képesnek kell lennie a naplófájlok archiválására az összes olyan napló archiválási helyről, amelybe az egyik adatbázis-példány archiválhatja a naplófájlokat. 

A naplózási archiválást csak az elsődleges adatbázis hajtja végre. Ha megváltoztatja az adatbázis-kiszolgálók HADR-szerepköreit, vagy ha hiba történik, az új elsődleges adatbázis feladata a naplózás archiválása. Ha több log Archive-helyet állított be, előfordulhat, hogy a naplók kétszer is archiválva lesznek. Helyi vagy távoli felzárkózás esetén előfordulhat, hogy manuálisan át kell másolnia az archivált naplókat a régi elsődleges kiszolgálóról az új elsődleges kiszolgáló aktív naplójának helyére.

Javasoljuk, hogy olyan közös NFS-megosztást állítson be, amelyben a naplók mindkét csomópontról íródnak. Az NFS-megosztásnak nagyon elérhetőnek kell lennie. 

Használhatja a meglévő, magasan elérhető NFS-megosztásokat a szállításokhoz vagy a profilok címtárához. További információkért lásd:

- [Magas rendelkezésre állás az NFS-en SUSE Linux Enterprise Server Azure-beli virtuális gépeken][nfs-ha] 
- [Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver-hez SUSE Linux Enterprise Serveron Azure NetApp Files SAP-alkalmazásokhoz](./high-availability-guide-suse-netapp-files.md)
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (NFS-megosztások létrehozásához)


## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

Ez a szakasz azt ismerteti, hogyan tesztelheti a DB2 HADR-telepítőjét. *Minden teszt azt feltételezi, hogy felhasználói gyökérként van bejelentkezve* , és az IBM DB2 Primary a *azibmdb01* virtuális gépen fut.

Az összes tesztelési eset kezdeti állapotát itt találja: (crm_mon-r vagy CRM-állapot)

- a **CRM-állapot** a pacemaker állapotának pillanatképe a végrehajtási időpontban 
- **crm_mon – r** a szívritmus-állapot folyamatos kimenete

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Az SAP-rendszer eredeti állapota dokumentálva van a Transaction DBACOCKPIT > Configuration > áttekintésében, ahogy az alábbi képen látható:

![DBACockpit – előzetes áttelepítés](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Az IBM DB2 átvételének tesztelése


> [!IMPORTANT] 
> A teszt elkezdése előtt győződjön meg a következőket:
> * A pacemaker nem rendelkezik sikertelen műveletekkel (CRM-állapot).
> * Nincsenek megkötések (az áttelepítési teszt maradékai)
> * Az IBM DB2 HADR szinkronizálása működik. Keresés DB2-vel\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Telepítse át az elsődleges DB2-adatbázist futtató csomópontot a következő parancs végrehajtásával:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Az áttelepítés befejezése után a CRM-állapot kimenete a következőképpen néz ki:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Az SAP-rendszer eredeti állapota dokumentálva van a Transaction DBACOCKPIT > Configuration > áttekintésében, ahogy az alábbi képen látható:

![DBACockpit – áttelepítés utáni](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

A "CRM-erőforrás áttelepítése" nevű erőforrás-áttelepítés helybeli korlátozásokat hoz létre. A hely megkötéseit törölni kell. Ha a hely megkötéseit nem törli, az erőforrás nem tud visszavenni a feladatokat, vagy nem kívánt megvásárlást tapasztalhat. 

Telepítse újra az erőforrást a *azibmdb01* , és törölje a hely megkötéseit
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **CRM-erőforrás migrálása \<res_name> \<host> :** a hely megkötéseit hozza létre, és problémákat okozhat az átvétel során
- **CRM-erőforrás \<res_name> törlése**: törli a hely megkötéseit
- **CRM-erőforrás \<res_name> karbantartása**: törli az erőforrás összes hibáját

### <a name="test-the-fencing-agent"></a>A kerítés ügynök tesztelése

Ebben az esetben teszteljük a SBD kerítést, amelyet a SUSE Linux használata esetén ajánlott elvégezni.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

A fürtcsomópont *azibmdb01* újra kell indítani. A rendszer áthelyezi az IBM DB2 elsődleges HADR szerepkört a *azibmdb02*-be. Ha a *azibmdb01* újra online állapotba kerül, a DB2-példány egy másodlagos adatbázis-példány szerepkörében lesz áthelyezve. 

Ha a pacemaker szolgáltatás nem indul el automatikusan az újraindított korábbi elsődlegesen, akkor manuálisan indítsa el a következővel:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Manuális átvétel tesztelése

A *azibmdb01* csomóponton a pacemaker szolgáltatás leállításával manuális átvételt is kipróbálhat:
<pre><code>service pacemaker stop</code></pre>

állapot a *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

A feladatátvételt követően újra elindíthatja a szolgáltatást a *azibmdb01*-on.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>A HADR elsődleges adatbázisát futtató csomóponton található DB2-folyamat leölése

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

A DB2-példány sikertelen lesz, és a pacemaker a következő állapotot fogja jelenteni:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

A pacemaker újrakezdi a DB2 elsődleges adatbázis példányát ugyanazon a csomóponton, vagy feladatátvételt hajt végre a másodlagos adatbázis-példányt futtató csomóponton, és hibát jelez.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>A másodlagos adatbázis-példányt futtató csomóponton található DB2-folyamat leölése

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

A csomópont meghiúsult és jelentett hibát jelzett.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

A DB2-példány újra lesz indítva a korábban hozzárendelt másodlagos szerepkörben.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Az adatbázis leállítása az elsődleges HADR futtató csomóponton a db2stop kényszerített használatával

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Felhasználó DB2- \<sid> db2stop kényszerítési parancsának futtatása:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Hiba észlelhető
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

A DB2 HADR másodlagos adatbázis-példánya előléptetve lett az elsődleges szerepkörbe
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Összeomlást okozó virtuális gép újraindítással a HADR elsődleges adatbázis-példányát futtató csomóponton

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

A pacemaker a másodlagos példányt az elsődleges példány szerepkörre fogja előléptetni. A régi elsődleges példány a virtuális gép és az összes szolgáltatás teljes visszaállítását követően a másodlagos szerepkörbe lép a virtuális gép újraindítása után:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Összeomlik a virtuális gép, amely a HADR elsődleges adatbázis-példányát futtatja a "állj" értékkel

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Ebben az esetben a pacemaker azt fogja érzékelni, hogy az elsődleges adatbázis-példányt futtató csomópont nem válaszol.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

A következő lépés a *felosztott agyi* helyzetek keresése. Ha a túlélő csomópont azt állapította meg, hogy az elsődleges adatbázis-példányt legutóbb futtató csomópont nem fut, a rendszer végrehajtja az erőforrások feladatátvételét.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


A csomópont "leállítása" esetén a sikertelen csomópontot újra kell indítani az Azure felügyeleti eszközein keresztül (a Azure Portal, a PowerShellben vagy az Azure CLI-ben). Miután a meghibásodott csomópont újra online állapotba került, elindítja a DB2-példányt a másodlagos szerepkörbe.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Következő lépések
- [Magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver-hoz](./sap-high-availability-architecture-scenarios.md)
- [A pacemaker beállítása SUSE Linux Enterprise Server az Azure-ban](./high-availability-guide-suse-pacemaker.md)

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
