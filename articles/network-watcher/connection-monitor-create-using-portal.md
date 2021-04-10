---
title: Csatlakozáskezelő létrehozása – Azure Portal
titleSuffix: Azure Network Watcher
description: Ez a cikk azt ismerteti, hogyan lehet figyelőt létrehozni a kapcsolódási figyelőben a Azure Portal használatával.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: edf671c8005fa67f6161f383c503ca278dba3105
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702164"
---
# <a name="create-a-monitor-in-connection-monitor-by-using-the-azure-portal"></a>Figyelő létrehozása a Csatlakozáskezelő Azure Portal használatával

> [!IMPORTANT]
> 2021. július 1-től nem lehet új teszteket felvenni egy meglévő munkaterületre, vagy új munkaterületet engedélyezni Network Performance Monitor. Emellett nem adhat hozzá új kapcsolódási figyelőket a Csatlakozáskezelő (klasszikus) szolgáltatáshoz. Továbbra is használhatja a 2021. július 1. előtt létrehozott teszteket és kapcsolódási figyelőket. A szolgáltatás megszakadásának minimalizálásához a jelenlegi számítási [feladatokhoz telepítse át a teszteket Network Performance monitor ](migrate-to-connection-monitor-from-network-performance-monitor.md) vagy  [migrálja a kapcsolódási figyelőről (klasszikus)](migrate-to-connection-monitor-from-connection-monitor-classic.md) az Azure-Network Watcher új, a 2024. február 29. előtti kapcsolódási figyelője felé.

Ismerje meg, hogyan figyelheti az erőforrások közötti kommunikációt a kapcsolat figyelője használatával. Ez a cikk azt ismerteti, hogyan lehet figyelőt létrehozni a Azure Portal használatával. A kapcsolódási figyelő támogatja a hibrid és az Azure-beli Felhőbeli üzembe helyezést.


## <a name="before-you-begin"></a>Előkészületek 

A Csatlakozáskezelő használatával létrehozott-figyelővel a helyszíni és az Azure-beli virtuális gépeket is felveheti forrásként. Ezek a kapcsolati figyelők a végpontokhoz való kapcsolódás figyelésére is használhatók. A végpontok lehetnek az Azure-ban vagy bármely más URL-címen vagy IP-címen.

Íme néhány definíció a kezdéshez:

* A **Csatlakozáskezelő erőforrása**. Régió-specifikus Azure-erőforrás. A következő entitások mindegyike egy Csatlakozáskezelő erőforrás tulajdonságait képezi.
* **Végpont**. A kapcsolódási ellenőrzésekben részt vevő forrás vagy cél. A végpontok példái a következők:
    * Azure-beli virtuális gépek.
    * Azure-beli virtuális hálózatok.
    * Azure-alhálózatok.
    * Helyszíni ügynökök.
    * Helyszíni alhálózatok.
    * Több alhálózatot tartalmazó helyszíni egyéni hálózatok.
    * URL-címek és IP-címek.
* **Tesztelési konfiguráció**. Egy teszt protokoll-specifikus konfigurációja. A választott protokoll alapján megadhatja a portot, a küszöbértékeket, a tesztelési gyakoriságot és az egyéb paramétereket.
* **Tesztelési csoport**. A forrás-végpontokat, a célként megadott végpontokat és a tesztelési konfigurációkat tartalmazó csoport. Egy Csatlakozáskezelő több tesztelési csoportot is tartalmazhat.
* **Test**. A forrás-végpont, a cél végpont és a tesztelési konfiguráció kombinációja. A teszt a legrészletesebben használt szint, amelyen a figyelési adatokat elérhetővé kell tennie. A figyelési adatmennyiség magában foglalja a sikertelen ellenőrzések százalékos arányát és az oda-és visszaút idejét (RTT).

:::image type="content" source="./media/connection-monitor-2-preview/cm-tg-2.png" alt-text="A kapcsolati figyelőt bemutató diagram, valamint a tesztelési csoportok és tesztek közötti kapcsolat meghatározása.":::


## <a name="create-a-connection-monitor"></a>Kapcsolatfigyelő létrehozása

Figyelő létrehozása a kapcsolódási figyelőben a Azure Portal használatával:

1. A Azure Portal kezdőlapján válassza a **Network Watcher** lehetőséget.
1. A bal oldali ablaktábla **figyelés** területén válassza a **Csatlakozáskezelő** lehetőséget.

   Ekkor megjelenik az összes, a Csatlakozáskezelőben létrehozott figyelő. A klasszikus kapcsolódási figyelőben létrehozott kapcsolódási figyelők megjelenítéséhez nyissa meg a **kapcsolódási figyelő** lapot.

   :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Képernyőkép, amely megjeleníti a Csatlakozáskezelő szolgáltatásban létrehozott figyelőket.":::
   
    
1. A **Csatlakozáskezelő** irányítópultjának bal felső sarkában válassza a **Létrehozás** elemet.

   

1. Az **alapvető beállítások** lapon adja meg a Csatlakozáskezelő adatait: 
   * **Csatlakozáskezelő neve**: adja meg a Csatlakozáskezelő nevét. Használja az Azure-erőforrások szabványos elnevezési szabályait.
   * **Előfizetés**: válasszon egy előfizetést a Csatlakozáskezelő számára.
   * **Régió**: válasszon régiót a kapcsolatok figyelője számára. Csak az ebben a régióban létrehozott forrás virtuális gépeket lehet kiválasztani.
   * **Munkaterület konfigurációja**: válasszon ki egy egyéni munkaterületet vagy az alapértelmezett munkaterületet. A munkaterület tárolja a figyelési adatait.
       * Az alapértelmezett munkaterület használatához jelölje be a jelölőnégyzetet. 
       * Egyéni munkaterület kiválasztásához törölje a jelet a jelölőnégyzetből. Ezután válassza ki az előfizetést és a régiót az egyéni munkaterülethez. 

   :::image type="content" source="./media/connection-monitor-2-preview/create-cm-basics.png" alt-text="Képernyőkép, amely a Csatlakozáskezelő alapjai lapot jeleníti meg.":::
   
1. A lap alján kattintson a Next ( **Tovább) gombra: csoportok tesztelése**.

1. Adja hozzá a tesztelési csoportok forrásait, célhelyeit és tesztelési konfigurációit. A tesztelési csoportok beállításával kapcsolatos további tudnivalókért tekintse meg a [tesztelési csoportok létrehozása a kapcsolódási figyelőben](#create-test-groups-in-a-connection-monitor)című témakört. 

   :::image type="content" source="./media/connection-monitor-2-preview/create-tg.png" alt-text="Képernyőkép, amely a Csatlakozáskezelő tesztelési csoportok lapját jeleníti meg.":::

1. A lap alján kattintson a Next ( **Tovább) gombra: riasztások létrehozása**. A riasztások létrehozásával kapcsolatos további tudnivalókért lásd: [riasztások létrehozása a kapcsolódási figyelőben](#create-alerts-in-connection-monitor).

   :::image type="content" source="./media/connection-monitor-2-preview/create-alert.png" alt-text="A riasztás létrehozása lapot megjelenítő képernyőkép.":::

1. A lap alján válassza a **Tovább: felülvizsgálat + létrehozás** elemet.

1. A **felülvizsgálat + létrehozás** lapon tekintse át az alapszintű információkat és a tesztelési csoportokat a Csatlakozáskezelő létrehozása előtt. Ha szerkesztenie kell a kapcsolódási figyelőt, a megfelelő lapokra visszalépve megteheti. 
   :::image type="content" source="./media/connection-monitor-2-preview/review-create-cm.png" alt-text="A Csatlakozáskezelő áttekintés + létrehozás lapját bemutató képernyőkép.":::
   > [!NOTE] 
   > A **felülvizsgálat + létrehozás** lapon látható a havi díj a csatlakoztatási figyelő szakaszában. Jelenleg a **jelenlegi költség/hónap** oszlop nem számít fel díjat. Ha a kapcsolódási figyelő általánosan elérhetővé válik, ebben az oszlopban a havi díj jelenik meg. 
   > 
   > Még a kapcsolatok figyelési fázisa alatt is Log Analytics betöltési díjat.

1. Amikor készen áll a kapcsolódási figyelő létrehozására, a **felülvizsgálat + létrehozás** lap alján válassza a **Létrehozás** lehetőséget.

A Csatlakozáskezelő a háttérben létrehozza a figyelő erőforrást.

## <a name="create-test-groups-in-a-connection-monitor"></a>Tesztelési csoportok létrehozása egy figyelőben

A-figyelő minden egyes tesztelési csoportja olyan forrásokat és célhelyeket tartalmaz, amelyek a hálózati paramétereken keresztül teszteltek. A rendszer a sikertelen ellenőrzések százalékos arányát és a RTT.

A Azure Portalban a tesztkörnyezet létrehozásához a következő mezők értékeit kell megadnia:

* **Tesztelési csoport letiltása**: Ha bejelöli ezt a jelölőnégyzetet, letilthatja az összes olyan forrás és cél figyelését, amelyet a tesztelési csoport meghatároz. Ez a beállítás alapértelmezés szerint nincs bejelölve.
* **Név**: nevezze el a tesztelési csoportot.
* **Források**: az Azure-beli virtuális gépeket és a helyszíni gépeket forrásként is megadhatja, ha az ügynökök telepítve vannak rajtuk. A forráshoz tartozó ügynök telepítésével kapcsolatos további tudnivalókért lásd: [figyelési ügynökök telepítése](./connection-monitor-overview.md#install-monitoring-agents).
   * Azure-ügynökök kiválasztásához válassza az **Azure-végpontok** fület. Itt csak a kapcsolódási figyelő létrehozásakor megadott régióhoz kötött virtuális gépek jelennek meg. Alapértelmezés szerint a virtuális gépek a hozzájuk tartozó előfizetésbe vannak csoportosítva. Ezek a csoportok össze vannak csukva. 
   
       Az **előfizetések** szintjéről a hierarchiában lévő többi szintre is lebonthatja a részletezést:

      **Előfizetés**  >  **Erőforráscsoport**  >  **VNET**  >  **Alhálózat**  >  **Ügynökökkel rendelkező virtuális gépek**

      A **csoportot** a választóval is megváltoztathatja, hogy bármely más szintről elindítsa a fát. Ha például a virtuális hálózat alapján csoportosítja a csoportot, akkor a hierarchiában található ügynökökkel rendelkező virtuális gépek **VNET**  >  **alhálózati**  >  **virtuális gépekkel** rendelkeznek.

       Ha kiválaszt egy VNET, alhálózatot vagy egyetlen virtuális gépet, a megfelelő erőforrás-azonosító a végpontként van beállítva. Alapértelmezés szerint a kijelölt VNET vagy alhálózatban lévő összes olyan virtuális gép, amely rendelkezik az Azure Network Watcher bővítménnyel, részt vesz a figyelésben. A hatókör csökkentése érdekében válasszon ki egy adott alhálózatot vagy ügynököt, vagy módosítsa a hatókör tulajdonság értékét. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-sources.png" alt-text="Képernyőfelvétel: a források hozzáadása panel és a Csatlakozáskezelő Azure-végpontok lapja.":::

   * A helyszíni ügynökök kiválasztásához válassza a **nem Azure-végpontok** fület. Alapértelmezés szerint az ügynökök régiónként vannak csoportosítva. Ezekhez a munkaterületekhez a Network Performance Monitor konfigurálva van. 
   
       Ha Network Performance Monitort kell hozzáadnia a munkaterülethez, szerezze be azt az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/solarwinds.solarwinds-orion-network-performance-monitor?tab=Overview)-ről. A Network Performance Monitor hozzáadásával kapcsolatos további információkért lásd: [monitorozási megoldások a Azure monitor](../azure-monitor/insights/solutions.md). 
   
       A **Csatlakozáskezelő létrehozása** területen az **alapbeállítások** lapon az alapértelmezett régió van kiválasztva. Ha megváltoztatja a régiót, az ügynököket az új régióban lévő munkaterületekről is kiválaszthatja. Kiválaszthat egy vagy több ügynököt vagy alhálózatot. Az **alhálózat** nézetben kiválaszthatja a figyeléshez megadott IP-címeket. Ha több alhálózatot ad hozzá, a rendszer létrehoz egy **OnPremises_Network_1** nevű egyéni helyszíni hálózatot. A **Group By** választót is módosíthatja ügynökök szerint.

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-sources.png" alt-text="Képernyőfelvétel: a források hozzáadása panel és a nem Azure-végpontok lap a Csatlakozáskezelőben.":::

   * A legutóbb használt végpontok kiválasztásához a **legutóbbi végpont** lapot használhatja 
   
   * Amikor befejezte a források beállítását, kattintson a **kész** gombra a lap alján. Az alapszintű tulajdonságokat, például a végpont nevét továbbra is szerkesztheti, ha kiválasztja a végpontot a **test Group (tesztelési csoport létrehozása** ) nézetben. 

* **Célhelyek**: az Azure-beli virtuális gépekkel, a helyszíni géppel vagy bármely végponttal (egy nyilvános IP-címmel, URL-lel vagy teljes tartománynévvel) való kapcsolat figyeléséhez célként kell megadnia. Egyetlen tesztelési csoportban Azure-beli virtuális gépeket, helyszíni számítógépeket, Office 365 URL-címeket, Dynamics 365 URL-címeket és egyéni végpontokat adhat hozzá.

    * Az Azure-beli virtuális gépek célhelyként való kiválasztásához válassza az **Azure-végpontok** fület. Alapértelmezés szerint az Azure-beli virtuális gépek olyan előfizetési hierarchiába vannak csoportosítva, amely abban a régióban van, amelyet a **Csatlakozáskezelő létrehozása** az **alapok** lapon kiválasztott. Módosíthatja a régiót, és kiválaszthatja az Azure-beli virtuális gépeket az új régióból. Ezután az **előfizetések** szintjéről a hierarchia más szintjeire is kibonthat, ugyanúgy, mint a forrás Azure-végpontok beállításakor.

      Kiválaszthat virtuális hálózatok, alhálózatokat vagy egyetlen virtuális gépet, ahogy a forrásként szolgáló Azure-végpontok beállításakor is. Ha kiválaszt egy VNET, alhálózatot vagy egyetlen virtuális gépet, a megfelelő erőforrás-azonosító a végpontként van beállítva. Alapértelmezés szerint a kijelölt VNET vagy alhálózatban lévő összes olyan virtuális gép, amely rendelkezik Network Watcher bővítménnyel, részt vesz a figyelésben. A hatókör csökkentése érdekében válasszon ki egy adott alhálózatot vagy ügynököt, vagy módosítsa a hatókör tulajdonság értékét. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests1.png" alt-text="<képernyőkép, amely a célhelyek hozzáadása ablaktáblát és az Azure-végpontok lapot mutatja. >":::

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests2.png" alt-text="<képernyőkép, amely megjeleníti a célhelyek hozzáadása ablaktáblát az előfizetési szinten. >":::
       
    
    * A nem Azure-ügynökök célhelyként való kiválasztásához válassza a **nem Azure-beli végpontok** fület. Alapértelmezés szerint az ügynökök régiónként vannak csoportosítva. Ezek a munkaterületek Network Performance Monitor konfigurálva vannak. 
    
      Ha Network Performance Monitort kell hozzáadnia a munkaterülethez, szerezze be azt az Azure Marketplace-ről. A Network Performance Monitor hozzáadásával kapcsolatos további információkért lásd: [monitorozási megoldások a Azure monitor](../azure-monitor/insights/solutions.md). 

      A **Csatlakozáskezelő létrehozása** területen az **alapbeállítások**   lapon az alapértelmezett régió van kiválasztva. Ha megváltoztatja a régiót, az ügynököket az új régióban lévő munkaterületekről is kiválaszthatja. Kiválaszthat egy vagy több ügynököt vagy alhálózatot. Az **alhálózat** nézetben kiválaszthatja a figyeléshez megadott IP-címeket. Ha több alhálózatot ad hozzá, a rendszer létrehoz egy **OnPremises_Network_1** nevű egyéni helyszíni hálózatot.  

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-dest.png" alt-text="Képernyőfelvétel: a célhelyek hozzáadása panel és a nem Azure-beli végpontok lap.":::
    
    * A nyilvános végpontok célhelyként való kiválasztásához válassza a **külső címek** lapot. A végpontok listája az Office 365 test URL-címeit és a Dynamics 365 teszt URL-címeket tartalmazza, név szerint csoportosítva. Más tesztelési csoportokban létrehozott végpontokat is kiválaszthat ugyanabban a Csatlakozáskezelőben. 
    
        Végpont hozzáadásához kattintson a jobb felső sarokban található **végpont hozzáadása** lehetőségre. Ezután adja meg a végpont nevét és URL-címét, az IP-címet vagy a teljes tartománynevet.

       :::image type="content" source="./media/connection-monitor-2-preview/add-endpoints.png" alt-text="Képernyőfelvétel: a nyilvános végpontok célhelyként való hozzáadásának helye a kapcsolódási figyelőben.":::

    * A legutóbb használt végpontok kiválasztásához lépjen a **legutóbbi végpont**   lapra.
    * Amikor befejezte a célhelyek kiválasztását, válassza a **kész** lehetőséget. Az alapszintű tulajdonságokat, például a végpont nevét továbbra is szerkesztheti, ha kiválasztja a végpontot a **test Group (tesztelési csoport létrehozása** ) nézetben. 

* **Tesztelési konfigurációk**: hozzáadhat egy vagy több tesztelési csoportot. Hozzon létre egy új teszt-konfigurációt az **Új konfiguráció** lapon. Vagy adjon meg egy másik tesztelési csoportból származó teszt-konfigurációt a **meglévő kiválasztása** lapról.

    * **Teszt konfigurációjának neve**: nevezze el a teszt konfigurációját.
    * **Protokoll**: válassza a **TCP**, az **ICMP** vagy a **http** lehetőséget. A HTTP HTTPS-re való módosításához válassza a **http** protokollt, majd válassza a **443** portot.
        * **TCP-teszt konfigurációjának létrehozása**: Ez a jelölőnégyzet csak akkor jelenik meg, ha a **protokoll** listában a **http** elemet választja. Jelölje be ezt a jelölőnégyzetet egy olyan tesztelési konfiguráció létrehozásához, amely ugyanazokat a forrásokat és célhelyeket használja, mint amelyeket a konfigurációban máshol adott meg. Az új teszt-konfiguráció neve **\<name of test configuration> _networkTestConfig**.
        * **Traceroute letiltása**: Ez a jelölőnégyzet akkor érvényes, ha a protokoll TCP vagy ICMP. Jelölje be ezt a jelölőnégyzetet, ha le szeretné állítani, hogy a források felfedjék a topológiát és a hop-by-hop RTT.
    * **Célport**: megadhatja a kívánt cél portot.
        * **Figyelés a porton**: Ez a jelölőnégyzet akkor érvényes, ha a protokoll TCP. Jelölje be ezt a jelölőnégyzetet a kiválasztott TCP-port megnyitásához, ha még nincs megnyitva. 
    * **Tesztelési gyakoriság**: ebben a listában megadhatja, hogy a források milyen gyakran fognak pingelni a protokollon és a megadott porton. 30 másodpercet, 1 percet, 5 percet, 15 percet vagy 30 percet is kiválaszthat. Válassza az **Egyéni** lehetőséget, ha egy másik, 30 másodperc és 30 perc közötti gyakoriságot szeretne megadni. A források a választott érték alapján próbálják meg a célokhoz való kapcsolódást. Ha például 30 másodpercet választ, a források 30 másodpercenként legalább egyszer megkeresik a kapcsolatot a célhoz.
    * **Sikerességi küszöb**: a következő hálózati paraméterek küszöbértékeit állíthatja be:
       * Az **ellenőrzések sikertelenek**: állítsa be, hogy az ellenőrzések hány százalékát kell megadnia, ha a források a megadott feltételekkel ellenőrzik a csatlakozást a célhelyekhez. A TCP vagy az ICMP protokoll esetében a sikertelen ellenőrzések százalékaránya a csomagok elvesztésének százalékában adható meg. HTTP protokoll esetén ez az érték a válasz nélküli HTTP-kérelmek százalékos arányát jelöli.
       * **Oda**-és bekapcsolási idő: állítsa be azt az RTT (ezredmásodpercben), hogy mennyi ideig lehet a forrás csatlakozni a célhoz a tesztelési konfiguráción keresztül.
       
   :::image type="content" source="./media/connection-monitor-2-preview/add-test-config.png" alt-text="Képernyőkép, amely bemutatja, hol kell beállítani a tesztelési konfigurációt a kapcsolódási figyelőben.":::
       
## <a name="create-alerts-in-connection-monitor"></a>Riasztások létrehozása a Csatlakozáskezelőben

Riasztásokat állíthat be a tesztelési konfigurációkban beállított küszöbértékek alapján meghiúsuló teszteken.

A Azure Portal a riasztások létrehozásához a kapcsolódási figyelőhöz a következő mezők értékeit kell megadnia: 

- **Riasztás létrehozása**: Ha bejelöli ezt a jelölőnégyzetet, a rendszer metrikai riasztást hoz létre Azure monitorban. Ha bejelöli ezt a jelölőnégyzetet, a többi mező is engedélyezve lesz a szerkesztéshez. A riasztásra vonatkozó további díjak a [riasztások díjszabása](https://azure.microsoft.com/pricing/details/monitor/)alapján lesznek érvényesek. 

- **Hatókör**  >  **Erőforrás**  >  **Hierarchia**: a rendszer automatikusan kitölti ezeket az értékeket az **alapok** lapon megadott értékek alapján.

- **Feltétel neve**: a riasztás a `Test Result(preview)` metrikán jön létre. Ha a figyelő teszt eredménye sikertelen, akkor a riasztási szabály tüzet fog okozni. 

- **Műveleti csoport neve**: közvetlenül is megadhatja az e-mail-címet, vagy létrehozhat riasztásokat a műveleti csoportok használatával. Ha közvetlenül adja meg az e-mail-címét, létrejön egy **NPM E-mail ActionGroup** nevű műveleti csoport. A rendszer hozzáadja az e-mail-azonosítót a műveleti csoporthoz. Ha a műveleti csoportok használata mellett dönt, ki kell választania egy korábban létrehozott műveleti csoportot. A műveleti csoportok létrehozásával kapcsolatos további információkért lásd: [create Action groups in the Azure Portal](../azure-monitor/alerts/action-groups.md). A riasztás létrehozása után [kezelheti a riasztásokat](../azure-monitor/alerts/alerts-metric.md#view-and-manage-with-azure-portal). 

- **Riasztási szabály neve**: a Csatlakozáskezelő neve.

- **Szabály engedélyezése létrehozáskor**: jelölje be ezt a jelölőnégyzetet, ha engedélyezni szeretné a riasztási szabályt a feltétel alapján. Tiltsa le ezt a jelölőnégyzetet, ha engedélyezni szeretné a szabályt anélkül, hogy engedélyezve lenne. 

:::image type="content" source="./media/connection-monitor-2-preview/create-alert-filled.png" alt-text="A figyelő riasztás létrehozása lapját megjelenítő képernyőkép.":::

## <a name="scale-limits"></a>Skálázási korlátok

A hálózati figyelők a következő skálázási korlátokkal rendelkeznek:

* Az előfizetések maximális száma régiónként: 100
* Tesztelési csoportok maximális száma kapcsolatonként: 20
* Maximális források és célhelyek kapcsolatonként: 100
* A tesztelési konfigurációk maximális száma kapcsolatonként: 2 a Azure Portalon keresztül

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [, hogyan elemezheti a figyelési információkat, és hogyan állíthat be riasztásokat](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Ismerje meg [, hogyan diagnosztizálhatja a hálózattal kapcsolatos problémákat](./connection-monitor-overview.md#diagnose-issues-in-your-network).
