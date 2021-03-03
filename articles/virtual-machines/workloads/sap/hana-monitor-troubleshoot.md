---
title: Az Azure-beli SAP HANA HANA oldalának figyelése és hibaelhárítása (nagyméretű példányok) | Microsoft Docs
description: A HANA oldalának figyelése és hibaelhárítása SAP HANA Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83743a6985bef8ce6c03e01ed8d10aa740852106
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668809"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>HANA-oldali monitorozás és hibaelhárítás

Az Azure-SAP HANAokkal kapcsolatos problémák hatékony elemzése érdekében (nagyméretű példányok) hasznos lehet a probléma kiváltó okának szűkítése. Az SAP nagy mennyiségű dokumentációt tett közzé, hogy segítsen Önnek.

A SAP HANA teljesítményével kapcsolatos vonatkozó gyakori kérdések a következő SAP-megjegyzésekben találhatók:

- [SAP-Megjegyzés #2222200 – gyakori kérdések: SAP HANA Network](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP-Megjegyzés #2100040 – gyakori kérdések: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP-Megjegyzés #199997 – gyakori kérdések: SAP HANA memória](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP-Megjegyzés #200000 – gyakori kérdések: SAP HANA teljesítmény optimalizálása](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP-Megjegyzés #199930 – gyakori kérdések: SAP HANA I/O-elemzés](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP-Megjegyzés #2177064 – gyakori kérdések: SAP HANA szolgáltatás újraindítása és összeomlik](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>Riasztások SAP HANA

Első lépésként tekintse meg az aktuális SAP HANA riasztási naplókat. A SAP HANA Studióban lépjen a **felügyeleti konzol: riasztások: megjelenítés: minden riasztás** elemre. Ezen a lapon jelennek meg a minimális és maximális küszöbértékeken kívül eső adott értékek (szabad fizikai memória, CPU-kihasználtság stb.) összes SAP HANA riasztása. Alapértelmezés szerint a rendszer 15 percenként automatikusan frissíti az ellenőrzéseket.

![A SAP HANA Studióban nyissa meg a felügyeleti konzolt: riasztások: megjelenítés: minden riasztás](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

A nem megfelelő küszöbérték miatt kiváltott riasztás esetén a rendszer visszaállítja a feloldást az alapértelmezett értékre vagy egy ésszerű küszöbértékre.

![Visszaállítás az alapértelmezett értékre vagy egy ésszerű küszöbértékre](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

A következő riasztások a CPU-erőforrásokkal kapcsolatos problémákat jelezhetik:

- Gazdagép CPU-használata (riasztás 5)
- Legutóbbi mentésipont művelet (riasztás 28)
- Mentésipont időtartama (riasztás 54)

A SAP HANA-adatbázis nagy CPU-felhasználását a következők egyikével figyelheti:

- 5. riasztás (gazda CPU-használat) az aktuális vagy korábbi CPU-használat esetén
- Az áttekintő képernyőn megjelenő CPU-használat

![A CPU-használat megjelenítése az áttekintő képernyőn](./media/troubleshooting-monitoring/image3-cpu-usage.png)

A betöltési gráf magas CPU-fogyasztást vagy a múltban magas kihasználtságot mutathat:

![A betöltési gráf magas CPU-fogyasztást vagy a múltban nagy fogyasztást mutathat be](./media/troubleshooting-monitoring/image4-load-graph.png)

A magas CPU-kihasználtság miatt kiváltott riasztás több okból is járhat, többek között a következők miatt: bizonyos tranzakciók végrehajtása, adatok betöltése, nem válaszoló feladatok, hosszú ideig futó SQL-utasítások és hibás lekérdezési teljesítmény (például a BW on HANA-kockák esetében).

A hibaelhárítással kapcsolatos további információkért tekintse meg a [SAP HANA hibaelhárítás: a CPU-hoz kapcsolódó okait és megoldásait](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) ismertető oldalt.

## <a name="operating-system"></a>Operációs rendszer

A Linux SAP HANAének egyik legfontosabb ellenőrzése az, hogy az átlátszó, hatalmas lapok le legyenek tiltva, lásd: [SAP Note #2131662 – transzparens hatalmas lapok (THP) a SAP HANA-kiszolgálókon](https://launchpad.support.sap.com/#/notes/2131662).

- A következő Linux-paranccsal ellenőrizhető, hogy a transzparens hatalmas lapok engedélyezve vannak-e: **Cat/sys/kernel/mm/transparent \_ hugepage/enabled**
- Ha _mindig_ az alábbi zárójelek közé van bejelölve, az azt jelenti, hogy az átlátszó hatalmas lapok engedélyezve vannak: [mindig] madvise soha; Ha _soha nem_ az alábbi zárójelek közé van lefoglalva, az azt jelenti, hogy az átlátszó hatalmas lapok le vannak tiltva: mindig madvise [soha]

A következő Linux-parancsnak semmit nem kell visszaadnia: **rpm-QA | grep ulimit.** Ha úgy tűnik, hogy a _ulimit_ telepítve van, azonnal távolítsa el.

## <a name="memory"></a>Memória

Előfordulhat, hogy a SAP HANA-adatbázis által lefoglalt memória mennyisége nagyobb a vártnál. A következő riasztások nagy memória-használattal kapcsolatos problémákat jeleznek:

- Gazdagép fizikai memóriahasználat (riasztás 1)
- Névkiszolgáló memóriahasználat (12. riasztás)
- Az oszlopok tárolására szolgáló táblák teljes memóriahasználat (riasztás 40)
- Szolgáltatások memóriahasználat (riasztás 43)
- Az oszlopok tárolására szolgáló táblák fő tárterületének memóriahasználat (riasztás 45)
- Futásidejű memóriaképek fájljai (riasztás 46)

A hibaelhárítással kapcsolatos további információkért tekintse meg a [SAP HANA hibaelhárítás: memória problémáit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) ismertető oldalt.

## <a name="network"></a>Network (Hálózat)

Tekintse meg az [SAP note #2081065 – hibaelhárítás SAP HANA hálózat](https://launchpad.support.sap.com/#/notes/2081065) és az SAP-Megjegyzés hálózati hibaelhárítási lépéseinek elvégzése című témakört.

1. A kiszolgáló és az ügyfél közötti oda-és visszautazási idő elemzése.
  A. Futtassa az SQL-parancsfájl [_HANA \_ hálózati \_ ügyfeleit_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. A csomópontok közötti kommunikáció elemzése.
  A. Futtassa az SQL-parancsfájl [_HANA \_ hálózati \_ szolgáltatásait_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Linux-parancs futtatása **ifconfig** (a kimenetben látható, hogy van-e a csomag elvesztése).
4. Futtassa a Linux parancs **tcpdump**.

Emellett a nyílt forráskódú [IPERF](https://iperf.fr/) eszközt (vagy hasonlót) is használhatja a valós alkalmazás hálózati teljesítményének méréséhez.

A hibaelhárítással kapcsolatos részletes utasításokért tekintse meg a [hálózati teljesítménnyel és a kapcsolódási problémákkal kapcsolatos SAP HANA hibaelhárítási](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) webhelyét.

## <a name="storage"></a>Tárolás

A végfelhasználói szempontból az alkalmazás (vagy a rendszer teljes egészében) lassabban fut, nem válaszol, vagy úgy tűnik, hogy nem válaszol, ha probléma van az I/O-teljesítménnyel. SAP HANA Studio **kötetek** lapján megtekintheti a csatlakoztatott köteteket, és az egyes szolgáltatások által használt köteteket.

![SAP HANA Studio kötetek lapján megtekintheti a csatlakoztatott köteteket, és az egyes szolgáltatások milyen köteteket használnak](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

A képernyő alsó részén található csatolt köteteknél láthatja a kötetek részleteit, például a fájlokat és az I/O-statisztikákat.

![A képernyő alsó részén található csatolt köteteknél láthatja a kötetek részleteit, például a fájlokat és az I/O-statisztikát.](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Tekintse át a [SAP HANA hibaelhárítási: I/O-hez kapcsolódó kiváltó okokat és megoldásokat](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) , valamint [SAP HANA hibaelhárítást: lemezzel kapcsolatos alapvető okok és megoldások](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) webhely részletes hibaelhárítási lépéseket.

## <a name="diagnostic-tools"></a>Diagnosztikai eszközök

SAP HANA állapot-ellenőrzés végrehajtása a HANA \_ konfigurációs \_ Minichecks. Ez az eszköz olyan potenciálisan kritikus technikai problémákat ad vissza, amelyeket SAP HANA Studióban már riasztásként kellett volna kiváltani.

Tekintse meg az [SAP note #1969700 – SQL-utasítás gyűjteményét SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) és töltse le a megjegyzéshez csatolt SQL Statements.zip fájlt. Tárolja a. zip fájlt a helyi merevlemezen.

SAP HANA Studióban a **rendszerinformációk** lapon kattintson a jobb gombbal a **név** oszlopra, és válassza az **SQL-utasítások importálása** lehetőséget.

![SAP HANA Studióban a rendszerinformációk lapon kattintson a jobb gombbal a név oszlopra, és válassza az SQL-utasítások importálása elemet.](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Válassza ki a helyileg tárolt SQL Statements.zip fájlt, és a rendszer importálja a megfelelő SQL-utasításokkal rendelkező mappát. Ezen a ponton számos különböző diagnosztikai ellenőrzés futtatható ezekkel az SQL-utasításokkal.

A rendszerreplikálási sávszélességre vonatkozó követelmények SAP HANA teszteléséhez például kattintson a jobb gombbal a **sávszélesség** -utasításra a **replikálás: sávszélesség** területen, és válassza a **Megnyitás** az SQL-konzolban lehetőséget.

Ekkor megnyílik a teljes SQL-utasítás, amely lehetővé teszi a bemeneti paraméterek (módosítási szakasz) módosítását és végrehajtását.

![Megnyílik a teljes SQL-utasítás, amely lehetővé teszi a bemeneti paraméterek (módosítási szakasz) módosítását és végrehajtását.](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Egy másik példa a jobb gombbal a **replikálás: Áttekintés** területen található utasításokra kattint. Válassza a **végrehajtás** elemet a helyi menüből:

![Egy másik példa a jobb gombbal a replikálás: Áttekintés területen található utasításokra kattint. Válassza a végrehajtás lehetőséget a helyi menüben](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Ennek eredményeként a következő információk segítenek a hibaelhárításban:

![Ez olyan információkat eredményez, amelyek segítséget nyújtanak a hibaelhárításhoz](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Végezze el ugyanezt a HANA- \_ konfiguráció \_ Minichecks, és keressen _X_ jeleket a _C_ (kritikus) oszlopban.

Példa kimenetek:

**HANA \_ Konfiguráció \_ MiniChecks \_ Rev 102.01 + 1** az általános SAP HANA ellenőrzésekhez.

![HANA- \_ konfiguráció \_ MiniChecks \_ Rev 102.01 + 1 az általános SAP HANA ellenőrzésekhez](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA \_ A szolgáltatások \_ áttekintése** a jelenleg futó SAP HANA-szolgáltatások áttekintéséhez.

![A HANA \_ Services \_ áttekintése a jelenleg futó SAP HANA-szolgáltatások áttekintéséhez](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA \_ Szolgáltatások \_ statisztikája** SAP HANA szolgáltatással kapcsolatos információk (CPU, memória stb.).

![HANA \_ Services- \_ statisztikák SAP HANA szolgáltatás adataihoz](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA \_ Konfigurációs \_ Áttekintés \_ Rev110 +** az SAP HANA-példányra vonatkozó általános információkhoz.

![HANA- \_ konfiguráció \_ áttekintése \_ Rev110 + az SAP HANA példányra vonatkozó általános információkért](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA \_ Konfigurációs \_ Paraméterek \_ Rev70 +** a SAP HANA paramétereinek vizsgálatához.

![HANA \_ konfigurációs \_ Paraméterek \_ Rev70 + a SAP HANA paramétereinek vizsgálatához](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Következő lépések**

- Tekintse át [a magas rendelkezésre állású készletet a SuSE-ben a STONITH használatával](ha-setup-with-stonith.md).