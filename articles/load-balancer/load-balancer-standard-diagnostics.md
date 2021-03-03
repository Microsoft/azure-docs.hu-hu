---
title: Diagnosztika mérőszámokkal, riasztásokkal és erőforrás-állapottal – Azure standard Load Balancer
description: Az Azure-standard Load Balancer diagnosztizálásához használja az elérhető metrikákat, riasztásokat és erőforrás-állapotokra vonatkozó információkat.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2021
ms.author: allensu
ms.openlocfilehash: 29584a9453fa052745f417cba0bbe940766c30e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699079"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Metrikák, riasztások és az erőforrások állapotának diagnosztikái a standard Load Balancerben

Az Azure standard Load Balancer a következő diagnosztikai képességeket teszi elérhetővé:

* **Többdimenziós mérőszámok és riasztások**: többdimenziós diagnosztikai képességeket biztosít a standard Load Balancer-konfigurációk [Azure monitoron](../azure-monitor/overview.md) keresztül. A standard Load Balancer-erőforrások figyelésére, kezelésére és hibakeresésére van lehetőség.

* **Erőforrás állapota**: a Load Balancer Resource Health állapota a Figyelés területen található Resource Health oldalon érhető el. Ez az automatikus vizsgálat tájékoztatja a Load Balancer erőforrás aktuális rendelkezésre állásáról.
Ez a cikk gyors áttekintést nyújt ezekről a képességekről, és lehetőséget nyújt a standard Load Balancer használatára. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Többdimenziós metrikák

Azure Load Balancer többdimenziós metrikákat biztosít az Azure-metrikák használatával a Azure Portalban, és segít valós idejű diagnosztikai információk beszerzésében a terheléselosztó erőforrásaiban. 

A különböző standard Load Balancer konfigurációk a következő metrikákat biztosítják:

| Metric | Erőforrás típusa | Leírás | Ajánlott aggregáció |
| --- | --- | --- | --- |
| Adatútvonalak rendelkezésre állása | Nyilvános és belső Load Balancer | A standard szintű Load Balancer folyamatosan kihasználja a régió és a terheléselosztó elülső rétege közötti adatútvonalat egészen a virtuális gépet támogató SDN-veremig. Amíg a kifogástalan állapotú példányok megmaradnak, a mérés ugyanazt az útvonalat követi, mint az alkalmazás elosztott terhelésű forgalma. Az ügyfelek által használt adatútvonal szintén érvényesítve lesz. A mérés nem látható az alkalmazás számára, és nincs hatással más műveletekre.| Átlag |
| Állapotadat-mintavétel állapota | Nyilvános és belső Load Balancer | A standard Load Balancer egy elosztott állapot-ellenőrzési szolgáltatást használ, amely figyeli az alkalmazás-végpont állapotát a konfigurációs beállításoknak megfelelően. Ez a metrika a terheléselosztó készletében lévő példányok összesített vagy végponti szűrt nézetét biztosítja. Láthatja, hogyan tekinti meg a Load Balancer az alkalmazás állapotát az állapotminta konfigurációja alapján. |  Átlag |
| SYN (szinkronizálás) száma | Nyilvános és belső Load Balancer | A standard szintű Load Balancer nem állítja le a Transmission Control Protocol- (TCP-) kapcsolatokat, és nem befolyásolja a TCP- vagy UDP-csomagok átvitelét. A folyamok és a hozzájuk tartozó kézfogások mindig a forrás és a virtuálisgép-példány között történnek. A TCP protokollal kapcsolatos forgatókönyvek hatékonyabb hibaelhárítása érdekében használhatja a SYN csomagszámlálókat, hogy átlássa, hány TCP-csatlakozási kísérlet történt. A metrika a fogadott TCP SYN-csomagok számáról készít jelentést.| Sum |
| SNAT-kapcsolatok száma | Nyilvános Load Balancer |A standard szintű Load Balancer azoknak a kimenő folyamoknak a számáról készít jelentést, amelyek el vannak rejtve a nyilvános IP-cím előtere elől. A forráshálózati címfordítási (SNAT-) portok véges erőforrások. Ez a metrika jelzi, hogy az alkalmazás milyen mértékben támaszkodik az SNAT-ra a kívülről érkező folyamok esetén. A sikeres és meghiúsult kimenő SNAT-folyamok számlálóiról jelentés készül, amely felhasználható a kimenő folyamok hibaelhárításához és az állapotuk áttekintéséhez.| Sum |
| Lefoglalt SNAT-portok | Nyilvános Load Balancer | standard Load Balancer a háttérbeli példányok által lefoglalt SNAT-portok számát jelenti | Átlag. |
| Használt SNAT-portok | Nyilvános Load Balancer | Standard Load Balancer a háttérbeli példányok által használt SNAT-portok számát jelenti. | Átlag | 
| Bájtok száma |  Nyilvános és belső Load Balancer | A standard szintű Load Balancer jelentést készít az előtérrendszerenként feldolgozott adatok mennyiségéről. Látható, hogy a bájtok nincsenek egyenletesen elosztva a háttérpéldányok közt. Ez várhatóan az Azure Load Balancer algoritmusa a folyamatokon alapul. | Sum |
| Csomagok száma |  Nyilvános és belső Load Balancer | A standard szintű Load Balancer jelentést készít az előtérrendszerenként feldolgozott csomagok mennyiségéről.| Sum |

  >[!NOTE]
  >A belső terheléselosztó NVA vagy tűzfal SYN-csomagon keresztüli forgalmának elosztásakor a bájtok száma és a csomagok száma mérőszámok nem érhetők el, és nullaként jelennek meg. 
  
  >[!NOTE]
  >A Max és a min összesítések nem érhetők el a SYN-darabszám, a csomagok száma, a SNAT-kapcsolatok száma és a bájtok száma metrikák esetében. 
  
### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>A terheléselosztó metrikáinak megtekintése a Azure Portalban

A Azure Portal megjeleníti a terheléselosztó mérőszámait a metrikák lapon, amely egy adott erőforráshoz és a Azure Monitor laphoz is elérhető a terheléselosztó erőforrás oldalán. 

A standard Load Balancer erőforrások metrikáinak megtekintése:
1. Nyissa meg a metrikák lapot, és tegye a következők egyikét:
   * A terheléselosztó erőforrása lapon válassza ki a metrika típusát a legördülő listából.
   * A Azure Monitor lapon válassza ki a terheléselosztó erőforrását.
2. Állítsa be a megfelelő metrika-összesítési típust.
3. Szükség esetén a szükséges szűrést és csoportosítást is konfigurálhatja.
4. Igény szerint konfigurálhatja az időtartományt és az összesítést. Az UTC szerint az alapértelmezett idő jelenik meg.

  >[!NOTE] 
  >Az idő összesítése akkor fontos, ha bizonyos mérőszámok értelmezése során percenként egyszer mintát vesznek az adatok. Ha az idő összesítése öt percre van beállítva, és a metrikák összesítési típusának összege a metrikák, például a SNAT kiosztására szolgál, a gráf a lefoglalt SNAT-portok ötszörösét fogja megjeleníteni. 

![standard Load Balancer metrikái](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Ábra: az adatelérési út rendelkezésre állási metrikája standard Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Többdimenziós mérőszámok programozott módon történő beolvasása API-kon keresztül

A többdimenziós metrikák definícióinak és értékeinek beolvasására szolgáló API-útmutatásért lásd: [Azure Monitoring REST API útmutató](../azure-monitor/essentials/rest-api-walkthrough.md#retrieve-metric-definitions-multi-dimensional-api). Ezek a metrikák megírhatók egy Storage-fiókba az "összes metrika" kategóriához tartozó [diagnosztikai beállítás](../azure-monitor/essentials/diagnostic-settings.md) hozzáadásával. 

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Gyakori diagnosztikai forgatókönyvek és javasolt nézetek

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>Az adatelérési út fel van és érhető el az Load Balancer előtéri felületén?
<details><summary>Kibontás</summary>

Az adatelérési út rendelkezésre állási metrikája a régión belüli adatelérési út állapotát mutatja a virtuális gépek helyéül szolgáló számítási gazdagépen. A metrika az Azure-infrastruktúra állapotának áttekintése. A metrika a következőre használható:
- A szolgáltatás külső rendelkezésre állásának monitorozása
- Mélyebbre ássa, hogy az a platform, amelyen a szolgáltatás telepítve van, kifogástalan állapotú-e, illetve hogy a vendég operációs rendszer vagy alkalmazás példánya kifogástalan állapotú-e.
- Elkülöníti, hogy egy esemény kapcsolódik-e a szolgáltatáshoz vagy az alapul szolgáló adatsíkon. Ne tévesszük össze ezt a metrikát az állapot mintavételi állapotával ("háttér-példány rendelkezésre állása").

Az adatelérési út rendelkezésre állásának beolvasása a standard Load Balancer erőforrásaihoz:
1. Győződjön meg arról, hogy a helyes terheléselosztó erőforrás van kiválasztva. 
2. A **metrika** legördülő listában válassza ki az **adatelérési út rendelkezésre állása** elemet. 
3. Az **Összesítés** legördülő listában válassza az **AVG** elemet. 
4. Emellett vegyen fel egy szűrőt a előtérbeli IP-cím vagy a frontend-port számára a szükséges előtér-IP-címmel vagy előtér-porttal rendelkező dimenzióként, majd csoportosítsa őket a kiválasztott dimenzió alapján.

![VIP-szondázás](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Ábra: Load Balancer frontend-szondázás részletei*

A metrikát egy aktív, sávon belüli mérés hozza létre. A régión belüli szondázás-szolgáltatás a méréshez kapcsolódó forgalomból származik. A szolgáltatás akkor aktiválódik, amikor nyilvános előtérrel hoz létre egy központi telepítést, és addig folytatódik, amíg el nem távolítja az előtért. 

A rendszer rendszeres időközönként létrehoz egy csomagot, amely megfelel az üzembe helyezéshez tartozó előtérnek és szabálynak. A rendszer áthelyezi a régiót a forrásból a gazdagépre, ahol a háttérbeli készletben lévő virtuális gép található. A terheléselosztó infrastruktúra ugyanazt a terheléselosztási és fordítási műveletet hajtja végre, mint az összes többi forgalom esetében. Ez a mintavétel sávon kívül esik a terheléselosztási végponton. Miután a mintavétel megérkezik a (z) rendszerre, ahol egy kifogástalan virtuális gép található a háttér-készletben, a számítási gazdagép választ küld a szondázás szolgáltatásnak. A virtuális gép nem látja ezt a forgalmat.

A DataPath elérhetősége a következő okok miatt meghiúsul:
- Az üzemelő példány nem rendelkezik kifogástalan állapotú virtuális gépekkel a háttér-készletben. 
- Infrastruktúra-kimaradás történt.

Diagnosztikai célból használhatja az [adatelérési út rendelkezésre állási metrikáját az állapot mintavételi állapotával együtt](#vipavailabilityandhealthprobes).

A legtöbb forgatókönyv esetében az **átlagot** használja az összesítéshez.
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>A Load Balancer backend-példányai választanak a mintavételre?
<details>
  <summary>Kibontás</summary>
Az állapotadatok állapotának mérőszáma az alkalmazás központi telepítésének állapotát mutatja be a terheléselosztó állapotának beállításakor. A terheléselosztó a Health mintavétel állapota alapján határozza meg, hogy hová szeretné elküldeni az új folyamatokat. Az állapotadatok egy Azure-beli infrastruktúra-címről származnak, és a virtuális gép vendég operációs rendszerében láthatók.

Az standard Load Balancer-erőforrások állapot-mintavételi állapotának lekérése:
1. Válassza ki a **Health mintavételi állapot** mérőszámát **AVG** összesítési típussal. 
2. Alkalmazzon szűrőt a szükséges előtér-IP-címen vagy-porton (vagy mindkettőn).

Az állapot-mintavétel a következő okok miatt meghiúsul:
- Az állapot-mintavétel olyan portra konfigurálható, amely nem figyel vagy nem válaszol, vagy helytelen protokollt használ. Ha a szolgáltatás közvetlen kiszolgáló-visszaküldési (DSR-vagy úszó IP-) szabályokat használ, győződjön meg arról, hogy a szolgáltatás figyeli a hálózati adapter IP-konfigurációjának IP-címét, és nem csak az előtér-IP-címmel konfigurált visszacsatolást.
- A mintavételt nem engedélyezi a hálózati biztonsági csoport, a virtuális gép vendég operációs rendszerének tűzfala vagy az alkalmazás rétegének szűrői.

A legtöbb forgatókönyv esetében az **átlagot** használja az összesítéshez.
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Hogyan a kimenő kapcsolatok statisztikáit? 
<details>
  <summary>Kibontás</summary>
A SNAT-kapcsolatok metrika a [kimenő folyamatok](./load-balancer-outbound-connections.md)sikeres és sikertelen kapcsolatainak mennyiségét írja le.

A sikertelen, nullánál nagyobb kapcsolatok mennyisége azt jelzi, hogy SNAT a portok kimerülése. Meg kell vizsgálnia, hogy mi okozza ezeket a hibákat. A SNAT-port kimerülésének jegyzékfájlja sikertelen a [kimenő folyamat](./load-balancer-outbound-connections.md)létrehozásakor. Tekintse át a kimenő kapcsolatokról szóló cikket, hogy megértsék a munkafolyamatokat és a mechanizmusokat, valamint azt, hogy miként lehet enyhíteni és megtervezni a SNAT-portok kimerülésének elkerülését. 

A SNAT-kapcsolatok statisztikáinak beolvasása:
1. Válassza ki a **SNAT-kapcsolatok** metrika típusát és a **Sum** értéket összesítésként. 
2. A sikeres és sikertelen SNAT-kapcsolatok **állapotának** csoportosítása a különböző sorok szerint. 

![SNAT-kapcsolatok](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Ábra: Load Balancer SNAT kapcsolatok száma*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Hogyan a SNAT-portok használatának és foglalásának ellenőrzése?
<details>
  <summary>Kibontás</summary>
A használt SNAT-portok mérőszáma nyomon követi, hogy hány SNAT-portot használ a rendszer a kimenő folyamatok fenntartásához. Ez azt jelzi, hogy hány egyedi folyamat jön létre egy internetes forrás és egy olyan háttérbeli virtuális gép vagy virtuálisgép-méretezési csoport között, amely egy terheléselosztó mögött található, és nem rendelkezik nyilvános IP-címmel. A lefoglalt SNAT-portok metrikával használt SNAT-portok számának összehasonlításával megállapíthatja, hogy a szolgáltatás tapasztalható-e, vagy a SNAT kimerülése és az eredményül kapott kimenő folyamat meghibásodása esetén is fennáll-e. 

Ha a mérőszámok a [kimenő folyamat](./load-balancer-outbound-connections.md) meghibásodásának kockázatát jelzik, hivatkozzon a cikkre, és végezze el a szükséges lépéseket a szolgáltatás állapotának biztosításához.

SNAT-portok használatának és foglalásának megtekintése:
1. Állítsa be a gráf időösszesítését 1 percre a kívánt adatértékek megjelenítéséhez.
1. Válassza ki a **használt SNAT-portok** és/vagy a **lefoglalt SNAT-portok** mérőszám típusát és **átlagát** az összesítéshez.
    * Alapértelmezés szerint ezek a metrikák az egyes háttérbeli virtuális gépek vagy VMSS által lefoglalt vagy felhasznált SNAT-portok átlagos száma, amelyek az összes, a Load Balancer leképezett, a TCP és az UDP protokollal összevont nyilvános IP-cím számára elérhetők.
    * A terheléselosztó által használt vagy lefoglalt teljes SNAT-portok megjelenítéséhez használja a metrika összesítésének **összegét**
1. Szűrés egy adott **protokolltípus**, a **háttérbeli IP**-címek és/vagy a **felületi IP**-címek készlete alapján.
1. A háttér-vagy frontend-példány állapotának figyeléséhez alkalmazza a felosztást. 
    * Megjegyzés: a felosztás csak egyetlen mérőszámot engedélyez egyszerre. 
1. Ha például egy gépen a TCP-folyamatok SNAT-használatát szeretné figyelni, **átlagosan** összesítve, **háttérbeli IP** -címekkel és a **protokoll típusa** szerinti szűréssel. 

![SNAT kiosztása és használata](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Ábra: átlagos TCP SNAT-portok kiosztása és használata a háttérbeli virtuális gépek készlete számára*

![SNAT-használat a backend-példány szerint](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Ábra: a TCP SNAT port használata backend-példányon*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Hogyan a szolgáltatáshoz tartozó bejövő/kimenő csatlakozási kísérletek ellenőrzését?
<details>
  <summary>Kibontás</summary>
A SYN-csomagok mérőszáma az adott előtérhöz társított TCP SYN-csomagok mennyiségét írja le, amelyek elérkeztek vagy elküldhetők ( [kimenő forgalom](./load-balancer-outbound-connections.md)esetén). Ez a metrika a szolgáltatáshoz való TCP-kapcsolódási kísérletek megismerésére használható.

A legtöbb forgatókönyv esetén használja a **Sum (összeg** ) összesítést.

![SYN-kapcsolatok](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Ábra: Load Balancer SYN-szám*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Hogyan a hálózat sávszélesség-felhasználását? 
<details>
  <summary>Kibontás</summary>
A bájtok és a csomagméret mérőszáma a szolgáltatás által az előtér-alapon küldött vagy fogadott bájtok és csomagok mennyiségét írja le.

A legtöbb forgatókönyv esetén használja a **Sum (összeg** ) összesítést.

A bájt vagy a csomagok számának statisztikáinak beolvasása:
1. Válassza ki a **bájtok számát** és/vagy a **csomagok darabszámának** metrikai típusát **, az összesítéssel együtt.** 
2. A következő lehetőségek közül választhat:
   * Szűrő alkalmazása egy adott előtér-IP-címen, előtér-porton, háttérbeli IP-címen vagy háttér-porton.
   * Teljes statisztikát kaphat a terheléselosztó erőforrásaihoz szűrés nélkül.

![Bájtok száma](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Ábra: Load Balancer bájtok száma*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Hogyan a terheléselosztó üzembe helyezésének diagnosztizálása?
<details>
  <summary>Kibontás</summary>
Az adatelérési út rendelkezésre állásának és az állapot-mintavételi állapot metrikáinak együttes használatával egyetlen diagramon azonosíthatja a problémát, és elháríthatja a problémát. Biztos lehet abban, hogy az Azure megfelelően működik, és ezzel az ismerettel meggyőzően megállapítja, hogy a konfiguráció vagy az alkalmazás a kiváltó ok.

Az állapot mintavételi mérőszámai segítségével megtudhatja, hogyan tekintheti meg az Azure a telepítés állapotát a megadott konfiguráció alapján. Az állapot-ellenőrzéseket mindig remekül megtekintheti a figyeléssel vagy a probléma okának megállapításával.

További lépésként az adatelérési út rendelkezésre állási metrikájának használatával megtudhatja, hogyan tekintheti át az Azure az adott telepítésért felelős mögöttes adatközpont állapotát. Ha mindkét mérőszámot kombinálja, elkülönítheti a hiba helyét, ahogy az a következő példában látható:

![Az adatelérési út rendelkezésre állásának és az állapot-mintavételi állapot metrikáinak egyesítése](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Ábra: az adatelérési út rendelkezésre állásának és az állapot-mintavételi állapot metrikáinak kombinálása*

A diagram a következő információkat jeleníti meg:
- A virtuális gépeket üzemeltető infrastruktúra nem volt elérhető, és a diagram elején 0% volt. Később az infrastruktúra Kifogástalan állapotba került, és a virtuális gépek elérhetők voltak, és több virtuális gépet helyeztek el a háttérbe. Ezt az információt az adatelérési utak rendelkezésre állásának kék nyoma jelzi, amely később 100% volt. 
- A lila nyomkövetés által jelzett állapot-mintavételi állapot a diagram elején 0 százalék. A zöld körben bekarikázott terület kiemeli, hogy az állapotfigyelő állapot állapota Kifogástalan volt, és ekkor az ügyfél üzembe helyezése képes volt új folyamatok fogadására.

A diagram lehetővé teszi, hogy az ügyfelek az üzembe helyezést a saját maguk is tudják elhárítani anélkül, hogy meg kellene állapítaniuk vagy fel kellene kérniük a más problémák előfordulását. A szolgáltatás nem volt elérhető, mert az állapot-mintavétel hibás konfiguráció vagy hibás alkalmazás miatt sikertelen volt.
</details>

## <a name="configure-alerts-for-multi-dimensional-metrics"></a>Riasztások konfigurálása többdimenziós mérőszámokhoz ###

Az Azure standard Load Balancer támogatja a könnyen konfigurálható riasztásokat a többdimenziós metrikák esetében. Egyéni küszöbértékeket állíthat be bizonyos mérőszámokhoz, hogy a riasztásokat a különböző súlyossági szintekkel aktiválja, így biztosítva az erőforrás-figyelési élményt.

A riasztások konfigurálása:
1. Ugrás a terheléselosztó riasztási alpaneljére
1. Új riasztási szabály létrehozása
    1.  Riasztási feltétel konfigurálása
    1.  Választható Műveleti csoport hozzáadása az automatikus javításhoz
    1.  A riasztás súlyosságának, nevének és leírásának megadása, amely lehetővé teszi az intuitív reagálást

### <a name="inbound-availability-alerting"></a>Bejövő rendelkezésre állási riasztások
Ha riasztást szeretne kapni a bejövő rendelkezésre állásról, két külön riasztást hozhat létre az adatelérési út rendelkezésre állása és az állapot-mintavételi állapot metrikái alapján. Az ügyfelek különböző forgatókönyvekkel rendelkezhetnek, amelyek konkrét riasztási logikát igényelnek, de az alábbi példák a legtöbb konfiguráció esetében hasznosak lesznek.

Az adatelérési út rendelkezésre állása esetén riasztást kaphat, ha egy meghatározott terheléselosztási szabály elérhetetlenné válik. Ezt a riasztást úgy is beállíthatja, hogy riasztási feltételt állít be az adatelérési útvonal rendelkezésre állására, és felosztja az összes aktuális és jövőbeli értéket a előtér-porthoz és a előtér-IP-címhez. Ha úgy állítja be a riasztási logikát, hogy a nullánál kisebb vagy egyenlő legyen, akkor a riasztás akkor válik aktívvá, ha a terheléselosztási szabályok nem válaszolnak. Állítsa be az Összesítés részletességét és a kiértékelés gyakoriságát a kívánt kiértékelésnek megfelelően. 

Az állapot mintavételi állapotával riasztást kaphat, ha egy adott háttérbeli példány nem tud jelentős ideig válaszolni az állapot-ellenőrzésre. Állítsa be a riasztási feltételt a Health mintavételi állapot metrikájának használatára, valamint a háttérbeli IP-cím és a háttér-port alapján történő felosztásra. Ez biztosítja, hogy külön riasztást küldjön az egyes háttérrendszer-példányok egy adott port forgalmának kiszolgálására. Használja az **átlagos** összesítési típust, és állítsa be a küszöbértéket annak megfelelően, hogy milyen gyakran történjen a háttérbeli példány vizsgálata, és hogy mit gondol az egészséges küszöbértékre. 

A háttérbeli készlet szintjén is riasztást kaphat, ha nem oszt meg semmilyen dimenzióval, és nem használja az **átlagos** összesítési típust. Ezzel a beállítással riasztási szabályokat állíthat be, például a riasztást, ha a háttérbeli készlet tagjainak 50%-a nem megfelelő állapotú.

### <a name="outbound-availability-alerting"></a>Kimenő rendelkezésre állási riasztások
A kimenő rendelkezésre állás konfigurálásához két külön riasztást konfigurálhat a SNAT-kapcsolatok száma és a SNAT használt portok metrikáinak használatával.

A kimenő kapcsolódási hibák észleléséhez állítson be egy riasztást a SNAT-kapcsolatok száma és a szűrés a kapcsolódási állapot = sikertelen értékkel. Használja a **teljes** összesítést. Ezt a háttérbeli IP-cím beállításával is eloszthatja az összes aktuális és jövőbeli értékre, hogy a rendszer külön riasztást küldjön minden olyan backend-példány esetében, amely sikertelen kapcsolatokat észlelt. Állítsa be a küszöbértéket nullánál nagyobb értékre, vagy nagyobb értéket, ha a kimenő kapcsolódási hibák várhatóan megjelennek.

A használt SNAT-portok segítségével nagyobb kockázatot jelenthet a SNAT és a kimenő kapcsolatok meghibásodása. A riasztás használatakor győződjön meg róla, hogy a háttérbeli IP-cím és protokoll alapján van felosztva, és az **átlagos** összesítést használja. A küszöbértéket úgy állítsa be, hogy az egyes példányokban lefoglalt portok száma nagyobb legyen, mint a nem biztonságos. Előfordulhat például, hogy alacsony súlyosságú riasztást állít be, ha a háttérbeli példány a lefoglalt portok 75%-át és nagy súlyosságot használ, ha a lefoglalt portok 90%-át vagy 100%-át használja.  
## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Erőforrás állapotának állapota

A standard Load Balancer erőforrások állapotának állapota a meglévő **erőforrás** -állapoton keresztül érhető el a **monitor > Service Health**. A rendszer **két percenként** értékeli az adatelérési út rendelkezésre állásának mérésével, amely meghatározza, hogy elérhetők-e a előtér-terheléselosztási végpontok.

| Erőforrás állapotának állapota | Leírás |
| --- | --- |
| Elérhető | A standard Load Balancer erőforrása kifogástalan és elérhető. |
| Csökkentett teljesítményű | A standard Load Balancer platform vagy felhasználó által kezdeményezett események hatással vannak a teljesítményre. Az adatelérési út rendelkezésre állására vonatkozó metrika 90%-osnál rosszabb, de 25%-osnál jobb állapotot jelentett legalább két percig. A teljesítmény mérsékelten befolyásolhatja a teljesítményt. [Kövesse a hibaelhárítási RHC útmutatót](./troubleshoot-rhc.md) , és állapítsa meg, hogy vannak-e olyan felhasználó által kezdeményezett események, amelyek hatással vannak a rendelkezésre állásra.
| Nem érhető el | A standard Load Balancer erőforrás nem kifogástalan állapotú. A DataPath rendelkezésre állási metrikája kevesebb, mint 25%-os állapotot jelentett legalább két percen belül. Jelentős teljesítménybeli hatást vagy a bejövő kapcsolatok elérhetőségének hiányát tapasztalhatja. Előfordulhat, hogy a felhasználó vagy a platform eseményei nem állnak rendelkezésre. [Kövesse a hibaelhárítási RHC útmutatót](./troubleshoot-rhc.md) , és állapítsa meg, hogy vannak-e olyan felhasználó által kezdeményezett események, amelyek befolyásolják a rendelkezésre állást. |
| Ismeretlen | A standard Load Balancer erőforrás erőforrás-állapotának állapota még nem frissült, vagy nem kapott adatelérési utat az elmúlt 10 percben. Ennek az állapotnak átmenetinek kell lennie, és a megfelelő állapot fog megjelenni, amint az adatok beérkeznek. |

A nyilvános standard Load Balancer erőforrások állapotának megtekintése:
1. Válassza a **figyelő**  >  **Service Health** elemet.

   ![Figyelő oldal](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Ábra: a Service Health hivatkozás a Azure Monitor*

2. Válassza a **Resource Health** lehetőséget, majd győződjön meg arról, hogy az **előfizetés azonosítója** és az **erőforrástípus = Load Balancer** van kiválasztva.

   ![Erőforrás állapotának állapota](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Ábra: válasszon erőforrást az állapot nézethez*

3. A listából válassza ki a Load Balancer erőforrást a korábbi állapotának megtekintéséhez.

    ![Load Balancer állapot](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Ábra: Load Balancer erőforrás-állapot nézet*
 
Az általános erőforrás állapotának leírása a [RHC dokumentációjában](../service-health/resource-health-overview.md)érhető el. A Azure Load Balancer meghatározott állapotait az alábbi táblázat tartalmazza: 


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan tekintheti meg a Load Balancerhoz előre konfigurált mérőszámokat [az információk használatával](./load-balancer-insights.md)
- További információ a [standard Load Balancerról](./load-balancer-overview.md).
- További információ a [terheléselosztó kimenő kapcsolatáról](./load-balancer-outbound-connections.md).
- A [Azure monitor](../azure-monitor/overview.md)megismerése.
- Ismerje meg a [Azure Monitor REST API](/rest/api/monitor/) és a [metrikák beolvasását REST API használatával](/rest/api/monitor/metrics/list).