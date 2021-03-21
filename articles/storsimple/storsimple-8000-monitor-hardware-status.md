---
title: StorSimple 8000 sorozatú hardver összetevői és állapota | Microsoft Docs
description: Megtudhatja, hogyan figyelheti a StorSimple-eszköz hardveres összetevőit a StorSimple Eszközkezelő szolgáltatásán keresztül.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: 6b750659bf22e856ee8ad7368e3fea64dd7245df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960359"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>A StorSimple Eszközkezelő szolgáltatás használata a hardver összetevőinek és állapotának figyeléséhez

## <a name="overview"></a>Áttekintés
Ez a cikk a helyszíni StorSimple 8000 Series-eszköz különböző fizikai és logikai összetevőit ismerteti. Azt is ismerteti, hogyan lehet figyelni az eszköz összetevő állapotát a StorSimple Eszközkezelő szolgáltatás **állapot és hardver** állapota paneljének használatával.

Az **állapot és a hardver** állapota panelen látható az összes StorSimple-eszköz összetevőinek hardveres állapota.

Az 8100-es összetevők listájában három szakasz található:

* **Megosztott összetevők** – ezek nem részei a vezérlőknek, például a lemezmeghajtók, a bekerítések, a Power és a hűtési modul (PCM) összetevői, a PCM hőmérséklete, a vonal feszültsége és az aktuális érzékelők.
* **0. vezérlő összetevők** – a 0. vezérlőn található összetevők, például a vezérlő, az SAS Expander és az összekötő, a vezérlő hőmérséklet-érzékelők és a különböző hálózati adapterek.
* **1. vezérlő összetevők** – az 1. vezérlőt alkotó összetevők, hasonlóan a 0. vezérlőhöz.

Az 8600-es eszközökhöz további összetevők tartoznak, amelyek megfelelnek a kiterjesztett EBOD-bekerítésnek. Az összetevők listájában öt rész található. Ezek közül három szakasz tartalmazza az elsődleges ház összetevőit, és megegyeznek az 8100-es verzióban ismertetett összetevőkkel. A EBOD-ház két további szakasza van, amelyek a következőket írják le:

* **EBOD Controller 0 Components** – a EBOD-ház 0 részén található összetevők, például a EBOD vezérlő, SAS Expander és Connector, valamint vezérlő hőmérséklet-érzékelők.
* **EBOD Controller 1 Components** – az 1. EBOD-ház részét képező összetevők, hasonlóan a EBOD-ház 0.
* **Megosztott összetevők EBOD** – a EBOD-bekerítésben és a PCM-ben lévő összetevők, amelyek nem részei a EBOD vezérlőnek.

> [!NOTE]
> **A hardver állapota StorSimple Cloud Appliance (8010/8020) nem érhető el.**


## <a name="monitor-the-hardware-status"></a>A hardver állapotának figyelése
A következő lépések végrehajtásával tekintheti meg egy eszköz összetevőjének hardveres állapotát:

1. Navigáljon az **eszközök** pontra, és válasszon ki egy adott StorSimple-eszközt. Válassza a **figyelés > hardver állapota** lehetőséget.

    ![A Eszközkezelő szolgáltatás beállítások paneljét ábrázoló képernyőfelvétel. A Figyelés területen válassza a hardver állapota lehetőséget.](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Keresse meg a **hardver-összetevők** szakaszt, és válasszon a rendelkezésre álló összetevők közül. Egyszerűen kattintson az összetevő címkéjére a lista kibontásához és a különböző eszköz-összetevők állapotának megtekintéséhez. Tekintse meg az [elsődleges ház részletes összetevőinek listáját](#component-list-for-primary-enclosure-of-storsimple-device) , valamint a [EBOD-ház részletes összetevő-listáját](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![A hardver-összetevők szakaszban kiválasztott egyik összetevőt ábrázoló képernyőfelvétel. A szomszédos panel az adott összetevő részeinek nevét és állapotát jeleníti meg.](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Az összetevő állapotának értelmezéséhez használja a következő színkódolási sémát:
   
   * **Zöld pipa** – az **OK** állapotú, kifogástalan állapotú összetevőket jelöli.
   * **Sárga** – **Figyelmeztetési** állapotú, csökkentett teljesítményű összetevőt jelöl.
   * **Vörös felkiáltójel** – egy sikertelen, **meghibásodási** állapotú összetevőt jelöl.
   * **Fehér fekete szöveggel** – olyan összetevőt jelöl, amely nincs jelen.
   
   Az alábbi képernyőfelvételen egy olyan eszköz látható, amely **az OK**, a **Figyelmeztetés** és a **hiba** állapotú összetevőket tartalmazza.
       
   ![A Hardware Components szakaszban három összetevőt ábrázoló képernyőfelvétel: egy rendben állapotú, egy hibás állapotú, és egy figyelmeztetési állapotban.](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   A **megosztott összetevők listájának** kibontásakor láthatjuk, hogy az NVRAM és a fürt is csökken.

   ![A megosztott összetevők kiválasztott elemét bemutató képernyőkép. A szomszédos panelen az N V R A, a fürt pedig figyelmeztetési állapotban van.](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Az **1. vezérlő összetevői** listájának kibontásakor láthatjuk, hogy a fürt csomópontja meghiúsult.  

   ![Képernyőfelvétel: a vezérlő 1 összetevő elem van kiválasztva. A szomszédos panelen a fürtcsomópont meghibásodott állapotban van.](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Ha olyan összetevővel találkozik, amely nem **kifogástalan** állapotban van, forduljon a Microsoft ügyfélszolgálatahoz. Ha a riasztások engedélyezve vannak az eszközön, e-mailben értesítést fog kapni. Ha egy sikertelen hardveres összetevőt kell lecserélnie, tekintse meg a [StorSimple hardveres összetevők pótlása](./storsimple-8000-hardware-component-replacement.md)című témakört.

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>StorSimple-eszköz elsődleges bekerítéséhez tartozó összetevők listája
Az alábbi táblázat az elsődleges házban található fizikai és logikai összetevőket ismerteti (a helyszíni StorSimple-eszköz 8100-es és 8600-es verziójában egyaránt).

| Összetevő | Modul | Típus | Hely | Mezőre cserélhető egység (cserélhető)? | Description |
| --- | --- | --- | --- | --- | --- |
| Meghajtó a tárolóhelyen [0-11] |Lemezmeghajtók |Fizikai |Megosztott |Yes |Az egyes SSD-k vagy az elsődleges házban található HDD meghajtók esetében egy sor jelenik meg. |
| Környezeti hőmérséklet érzékelője |Ház |Fizikai |Megosztott |No |Az alvázon belüli hőmérsékletet méri. |
| Középsõ hőmérséklet-érzékelő |Ház |Fizikai |Megosztott |No |A középső sík hőmérsékletét méri. |
| Hallható riasztás |Ház |Fizikai |Megosztott |No |Azt jelzi, hogy az alvázon belül hallható riasztási alrendszer működik-e. |
| Ház |Ház |Fizikai |Megosztott |Yes |Egy váz jelenlétét jelzi. |
| Bekerítés beállításai |Ház |Fizikai |Megosztott |No |Az alváz elülső paneljére hivatkozik. |
| Vonali feszültség érzékelők |PCM |Fizikai |Megosztott |No |Számos vonalbeli feszültség érzékelők állapota megjelenik, ami azt jelzi, hogy a mért feszültség a tűréshatáron belül van-e. |
| Aktuális érzékelők |PCM |Fizikai |Megosztott |No |Számos aktuális érzékelők állapota látható, ami azt jelzi, hogy a mért áramerősség a tűréshatáron belül van-e. |
| Hőmérséklet-érzékelők PCM-ben |PCM |Fizikai |Megosztott |No |Számos hőmérséklet-érzékelő, például a bemeneti és a hozzáférési pont érzékelők állapota megjelenik, ami azt jelzi, hogy a mért hőmérséklet a tűréshatáron belül van-e. |
| Tápegység [0-1] |PCM |Fizikai |Megosztott |Yes |Az eszköz hátoldalán található két PCMs egy sor jelenik meg az egyes energiaellátási kellékeknél. |
| Hűtés [0-1] |PCM |Fizikai |Megosztott |Yes |A két PCMs található négy hűtési ventilátorhoz egy sor jelenik meg. |
| Akkumulátor [0-1] |PCM |Fizikai |Megosztott |Yes |Egy sor jelenik meg a PCM-ben ülő biztonsági mentési akkumulátor-modulok mindegyikéhez. |
| METiS |N/A |Logikai |Megosztott |N/A |Az akkumulátorok állapotát jeleníti meg: szükség van-e a töltésre, és az élettartama közeledik. |
| Fürt |N/A |Logikai |Megosztott |N/A |Megjeleníti a két integrált vezérlő modul között létrehozott fürt állapotát. |
| Fürtcsomópont |N/A |Logikai |Megosztott |N/A |A vezérlő állapotát jelzi a fürt részeként. |
| Fürt kvóruma |N/A |Logikai | |N/A |Azt jelzi, hogy a lemezes tagság a HDD-tárolóban van-e. |
| HDD-adatterület |N/A |Logikai |Megosztott |N/A |A merevlemez-meghajtón (HDD) tárolt adattárolóban használt tárterület. |
| HDD-felügyeleti terület |N/A |Logikai |Megosztott |N/A |A HDD-tárolóban a felügyeleti feladatokhoz lefoglalt terület. |
| HDD kvórum területe |N/A |Logikai |Megosztott |N/A |A fürt Kvórumának a HDD-tárolóban lefoglalt területe. |
| HDD-helyettesítési terület |N/A |Logikai |Megosztott |N/A |A HDD-tárolóban a vezérlő cseréjéhez lefoglalt terület. |
| SSD-adatterület |N/A |Logikai |Megosztott |N/A |A SSD-tárolóban tárolt adattárolási terület. |
| SSD NVRAM-terület |N/A |Logikai |Megosztott |N/A |Az SSD-tárolóban az NVRAM-logika számára dedikált tárolóhely. |
| HDD-tároló készlete |N/A |Logikai |Megosztott |N/A |Megjeleníti a logikai tároló azon állapotát, amely az eszköz HDD-ről lett létrehozva. |
| SSD Storage-készlet |N/A |Logikai |Megosztott |N/A |Megjeleníti az SSD-eszközökről létrehozott logikai tárolási készlet állapotát. |
| Vezérlő [0-1] [State] |I/O |Fizikai |Vezérlő |Yes |Megjeleníti a vezérlő állapotát, valamint azt, hogy aktív vagy készenléti üzemmódban van-e az alvázon belül. |
| Hőmérséklet-érzékelők a vezérlőben |I/O |Fizikai |Vezérlő |No |Számos hőmérséklet-érzékelő, például az I/O modul, a CPU hőmérséklete, a DIMM és a PCIe érzékelők állapota megjelenik, ami azt jelzi, hogy a hőmérséklet észlelve van-e a tűréshatáron belül. |
| SAS-Expander |I/O |Fizikai |Vezérlő |No |A soros csatlakozású SCSI (SAS) Expander állapotát jelzi, amely az integrált tárolónak a vezérlőhöz való összekapcsolására szolgál. |
| SAS-összekötő [0-1] |I/O |Fizikai |Vezérlő |No |Az egyes SAS-összekötők állapotát jelzi, amely az integrált tárolónak az SAS expanderhez való összekapcsolására szolgál. |
| SBB Közép-sík összekötő |I/O |Fizikai |Vezérlő |No |Az egyes vezérlőknek a középső síkon való összekapcsolására szolgáló Közép-sík összekötő állapotát jelzi. |
| Processzor magja |I/O |Fizikai |Vezérlő |No |Meghatározza a processzor magok állapotát az egyes vezérlőkön belül. |
| Az elektronikai energia bekerítése |I/O |Fizikai |Vezérlő |No |A ház által használt energiaellátási rendszer állapotát jelzi. |
| Az elektronikai diagnosztika bekerítése |I/O |Fizikai |Vezérlő |No |A vezérlő által biztosított diagnosztikai alrendszerek állapotát jelzi. |
| Alaplapi felügyeleti vezérlő (BMC) |I/O |Fizikai |Vezérlő |No |A alaplapi felügyeleti vezérlő (BMC) állapotát jelzi, amely egy speciális szolgáltatási processzor, amely érzékelőkön keresztül figyeli a hardvereszközöket, és független kapcsolatban kommunikál a rendszergazdával. |
| Ethernet |I/O |Fizikai |Vezérlő |No |Az egyes hálózati adapterek, azaz a vezérlőn megadott felügyeleti és adatportok állapotát jelzi. |
| NVRAM |I/O |Fizikai |Vezérlő |No |Az NVRAM állapotot jelzi, amely egy nem felejtő, véletlenszerű hozzáférésű memória, amely az alkalmazás szempontjából kritikus fontosságú információk megtartására szolgál az áramszünet esetén. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>A StorSimple-eszköz EBOD bekerítéséhez tartozó összetevők listája
Az alábbi táblázat az EBOD-házban található fizikai és logikai összetevőket ismerteti (csak a 8600 modellben jelennek meg) a helyszíni StorSimple-eszközön.

| Összetevő | Modul | Típus | Hely | FRU? | Description |
| --- | --- | --- | --- | --- | --- |
| Meghajtó a tárolóhelyen [0-11] |Lemezmeghajtók |Fizikai |Megosztott |Yes |Az EBOD-ház elején található HDD-meghajtókon egy sor jelenik meg. |
| Környezeti hőmérséklet érzékelője |Ház |Fizikai |Megosztott |No |Az alvázon belüli hőmérsékletet méri. |
| Középsõ hőmérséklet-érzékelő |Ház |Fizikai |Megosztott |No |A középső sík hőmérsékletét méri. |
| Hallható riasztás |Ház |Fizikai |Megosztott |No |Azt jelzi, hogy az alvázon belül hallható riasztási alrendszer működik-e. |
| Ház |Ház |Fizikai |Megosztott |Yes |Egy váz jelenlétét jelzi. |
| Bekerítés beállításai |Ház |Fizikai |Megosztott |No |A váz OPS-vagy előlapját jelöli. |
| Vonali feszültség érzékelők |PCM |Fizikai |Megosztott |No |Számos vonalbeli feszültség érzékelők állapota megjelenik, ami azt jelzi, hogy a mért feszültség a tűréshatáron belül van-e. |
| Aktuális érzékelők |PCM |Fizikai |Megosztott |No |Számos aktuális érzékelők állapota látható, ami azt jelzi, hogy a mért áramerősség a tűréshatáron belül van-e. |
| Hőmérséklet-érzékelők PCM-ben |PCM |Fizikai |Megosztott |No |Számos hőmérséklet-érzékelő, például a bemeneti és a hozzáférési pont érzékelők állapota megjelenik, ami azt jelzi, hogy a mért hőmérséklet a tűréshatáron belül van-e. |
| Tápegység [0-1] |PCM |Fizikai |Megosztott |Yes |Az eszköz hátoldalán található két PCMs egy sor jelenik meg az egyes energiaellátási kellékeknél. |
| Hűtés [0-1] |PCM |Fizikai |Megosztott |Yes |A két PCMs található négy hűtési ventilátorhoz egy sor jelenik meg. |
| Helyi tárterület [HDD] |N/A |Logikai |Megosztott |N/A |Megjeleníti a logikai tároló azon állapotát, amely az eszköz HDD-ről lett létrehozva. |
| Vezérlő [0-1] [State] |I/O |Fizikai |Vezérlő |Yes |Megjeleníti a vezérlők állapotát a EBOD modulban. |
| Hőmérséklet-érzékelők a EBOD-ben |I/O |Fizikai |Vezérlő |No |Az egyes vezérlők számos hőmérséklet-érzékelője állapota megjelenik, ami azt jelzi, hogy a hőmérséklet észlelve van-e a tűréshatáron belül. |
| SAS-Expander |I/O |Fizikai |Vezérlő |No |Megadja az SAS Expander állapotát, amely az integrált tároló vezérlőhöz való összekapcsolására szolgál. |
| SAS-összekötő [0-2] |I/O |Fizikai |Vezérlő |No |Az egyes SAS-összekötők állapotát jelzi, amely az integrált tárolónak az SAS expanderhez való összekapcsolására szolgál. |
| SBB Közép-sík összekötő |I/O |Fizikai |Vezérlő |No |Az egyes vezérlőknek a középső síkon való összekapcsolására szolgáló Közép-sík összekötő állapotát jelzi. |
| Az elektronikai energia bekerítése |I/O |Fizikai |Vezérlő |No |A ház által használt energiaellátási rendszer állapotát jelzi. |
| Az elektronikai diagnosztika bekerítése |I/O |Fizikai |Vezérlő |No |A vezérlő által biztosított diagnosztikai alrendszerek állapotát jelzi. |
| Kapcsolódás az eszköz-vezérlőhöz |I/O |Fizikai |Vezérlő |No |Az EBOD I/O-modul és az eszköz vezérlője közötti kapcsolat állapotát jelzi. |

## <a name="next-steps"></a>Következő lépések
* Ha a StorSimple Eszközkezelő szolgáltatást szeretné használni az eszköz felügyeletéhez, folytassa [a StorSimple-eszköz felügyeletéhez használja a StorSimple Eszközkezelő szolgáltatását](storsimple-8000-manager-service-administration.md).
* Ha egy csökkentett teljesítményű vagy sikertelen állapotú eszköz-összetevőt kell elhárítani, tekintse meg a [StorSimple figyelési mutatóit](storsimple-monitoring-indicators.md).
* A meghibásodott hardver-összetevők lecserélését lásd: [StorSimple hardveres összetevők cseréje](./storsimple-8000-hardware-component-replacement.md).
* Ha továbbra is problémákat tapasztal, [forduljon a Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md).