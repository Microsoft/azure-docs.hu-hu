---
title: A mennyiségi merevlemez-kvóták módosítása a Azure NetApp Files szolgáltatáshoz | Microsoft Docs
description: Leírja, hogyan lehet módosítani a mennyiségi merevlemez-kvótát, hogyan tervezze meg a változást, és hogyan felügyelheti és kezelheti a kapacitásokat.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 12807e83f7841bc67999ce385d0cb82bf15f4c71
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175991"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Mi a mennyiségű kemény kvóta módosítása a Azure NetApp Files szolgáltatáshoz

A szolgáltatás kezdetétől Azure NetApp Files kapacitás-készlet létesítési és automatikus növekedési mechanizmust használtak. Azure NetApp Files kötetek kiosztása a kiválasztott réteg és méret alapján, az ügyfél által kiosztott kapacitás készletén történik. A mennyiségi méretek (kvóták) a teljesítmény és a kapacitás biztosítására szolgálnak, és a kvóták bármikor módosíthatók. Ez azt jelenti, hogy a kötet kvótája jelenleg a kötet sávszélességének szabályozására szolgáló teljesítmény. Jelenleg a kapacitás kitöltésekor a kapacitás automatikusan növekszik.   

> [!IMPORTANT] 
> A kötet és a kapacitás-készlet kiépítés Azure NetApp Files viselkedése *manuális* és *ellenőrizhető* mechanizmusra változik. **2021. április 1-től kezdődően a mennyiségi méretek (kvóta) kezeli a sávszélesség-teljesítményt, valamint a kiosztott kapacitást, és a mögöttes kapacitások készlete már nem fog automatikusan növekedni.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>A mennyiségi rögzített kvóta változásának okai

Számos ügyfél három fő kihívást jelzett a *kezdeti* viselkedéssel kapcsolatban:
* A virtuálisgép-ügyfelek a dinamikusan kiosztott (100 TiB) kapacitást fogják látni az operációsrendszer-terület vagy a kapacitás-figyelési eszközök használatakor, ami pontatlan ügyfél-vagy alkalmazás-oldalsó kapacitást biztosít.
* Az alkalmazás tulajdonosai a kapacitás készletének automatikus növekedése miatt nem szabályozzák a kiépített kapacitási készlet területét (és a kapcsolódó költségeket). Ez a helyzet nehézkes olyan környezetekben, ahol a "elindítást végző folyamatok" gyorsan kitölthetik és növelhetik a kiosztott kapacitást és költségeket.
* Az ügyfelek a kötet mérete (kvóta) és a teljesítmény közötti közvetlen korrelációt szeretnék látni és karbantartani. Ha a (implicit) egy kötet (kapacitás-Wise) és a készlet automatikus növekedésének aktuális viselkedését (implicit) túllépi, az ügyfeleknek nincs közvetlen korrelációja, amíg a mennyiségi kvótát aktívan be nem állította vagy nem állították vissza. 

Számos ügyfél közvetlen irányítást kért a kiosztott kapacitás felett. A tároló kapacitásának és kihasználtságának szabályozására és kiegyensúlyozására van szükségük. Azt is szeretnék, hogy a költségeket, valamint az alkalmazási kötetek elérhető, felhasznált és kiosztott kapacitásának és teljesítményének az alkalmazás-és ügyféloldali láthatóságát is szabályozni tudják. 

## <a name="what-is-the-volume-hard-quota-change"></a>Mi a kötethez tartozó rögzített kvóta változása   

A kötet rögzített kvótájának változása esetén Azure NetApp Files kötetek nem lesznek dinamikusan kiépítve a következőnél: (maximum) 100 TiB. A kötetek a tényleges konfigurált méret (kvóta) szerint lesznek kiépítve. Emellett a befektetett kapacitású készletek már nem növekednek automatikusan a teljes kapacitású felhasználás elérése után. Ez a változás tükrözi az Azure Managed Disks szolgáltatásait, amelyek az automatikus kapacitás növekedése nélkül is kiépítve lesznek.

Vegyünk például egy olyan Azure NetApp Files kötetet, amely 1-TIB-méret (kvóta) szerint van konfigurálva egy 4 TiB-os Ultra Service szintű kapacitási készleten. Egy alkalmazás folyamatosan adatírást készít a kötetre.

A *kezdeti* viselkedés:  
* Várt sávszélesség: 128 MiB/s
* Teljes használható (és az ügyfél látható) kapacitása: 100 TiB   
    Ezen a méreten túl nem fog tudni további adatmennyiséget írni a köteten.
* Kapacitási készlet: az automatikusan növekszik 1 TiB-növekményekkel, amikor megtelt.
* Mennyiségi kvóta módosítása: csak a kötet teljesítményének (sávszélességének) változása. Nem változtatja meg az ügyfél látható vagy felhasználható kapacitását.

A *megváltozott* viselkedés:  
* Várt sávszélesség: 128 MiB/s
* Teljes felhasználható (és az ügyfél látható) kapacitása: 1 TiB nem fog tudni többet írni a köteten ezen a méreten túl.
* Kapacitási készlet: marad 4 TiB méretű, és nem növekszik automatikusan. 
* Mennyiségi kvóta módosítása: a kötet (sávszélesség) és az ügyfél látható vagy felhasználható kapacitásának módosítása.

Proaktívan kell figyelnie Azure NetApp Files kötetek és kapacitás-készletek kihasználtságát. Szándékosan kell módosítania a kötet és a készlet kihasználtságát a teljes felhasználás érdekében. A Azure NetApp Files továbbra is engedélyezi az [on-fly kötet és a kapacitás-készlet átméretezési műveleteit](azure-netapp-files-resize-capacity-pools-or-volumes.md).

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>A kötet működővé tenni módosítása

Ez a szakasz útmutatást nyújt ahhoz, hogyan működővé tenni a köteten rögzített kvóták módosítása a zökkenőmentes áttéréshez. Emellett a jelenleg kiosztott kötetek és kapacitás-készletek kezelésére, a folyamatos figyelésre, valamint a riasztásra és a kapacitás-felügyeleti lehetőségekre vonatkozó megállapításokat is biztosít.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>Jelenleg kiépített kötetek és kapacitási készletek

A mennyiségi kvóta változása miatt módosítania kell az operációs modellt. A kiépített kötetek és kapacitás-készletek folyamatos kapacitás-kezelést igényelnek.  Mivel a megváltozott működés azonnal megtörténik, az Azure NetApp Files csapata a meglévő, korábban kiosztott kötetek és kapacitási készletek egy sorozatát javasolja, az ebben a szakaszban leírtak szerint.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Javaslatok az egyszeri javítási vagy megelőző intézkedésekre  

A kötet rögzített kvótájának változása a korábban kiosztott kötetek és készletek kiépített és rendelkezésre álló kapacitásának változását eredményezi. Ennek eredményeképpen előfordulhat, hogy bizonyos kapacitás-elosztási kihívások történnek. A Azure NetApp Files csapat az alábbi, egyszeri javítási/megelőző intézkedéseket ajánlja az ügyfelek számára, hogy elkerülje a rövid távú kihagyható helyzeteket: 

* **Kiépített kötetek mérete**:   
    Méretezze át az összes kiosztott kötetet úgy, hogy a megfelelő puffer legyen a változási arány és a riasztás vagy az átméretezési idő (például 20% a tipikus számítási feladatok alapján) alapján, legfeljebb 100 TiB-vel (amely a [kötet maximális mérete](azure-netapp-files-resource-limits.md#resource-limits)). Az új kötet méretének, beleértve a puffer kapacitását, a következő tényezők alapján kell alapulnia:
    * **Kiépített** kötet kapacitása abban az esetben, ha a felhasznált kapacitás kisebb, mint a kiépített mennyiségi kvóta.
    * A **felhasznált** kötet kapacitása abban az esetben, ha a felhasznált kapacitás meghaladja a kiosztott mennyiségi kvótát.  
    A mennyiségi kapacitás növelésére nem számítunk fel további díjat, ha nem kell megtermelni az adott kapacitású készletet. Ennek a változásnak a hatására előfordulhat, hogy megfigyelheti a kötet sávszélesség *-korlátját* (az [automatikus QoS-kapacitás készletének](azure-netapp-files-understand-storage-hierarchy.md#qos_types) használata esetén).

* **Kiosztott kapacitások készletének méretei**:   
    A kötetek méretének módosítása után, ha a kötetek mérete nagyobb, mint a szolgáltatói kapacitás készlete, a kapacitás-készletet a kötetek összegével megegyező vagy annál nagyobb méretre kell növelni, legfeljebb 500 TiB-val (amely a [Kapacitási készlet maximális mérete](azure-netapp-files-resource-limits.md#resource-limits)). A kapacitások további kapacitása a normál módon ACR-díj lesz.

Ha segítségre van szüksége az alábbi szakaszokban leírtak szerint, a környezet ellenőrzéséhez és a riasztások beállításához segítséget kell nyújtania a Azure NetApp Files szakembereinek.

### <a name="ongoing-capacity-management"></a>Folyamatos kapacitások kezelése  

Az egyszeri javító intézkedések végrehajtása után a kapacitás figyelése és kezelése érdekében a folyamatban lévő folyamatokat össze kell állítani. A következő szakaszokban a kapacitás figyelésével és kezelésével kapcsolatos javaslatokat és alternatívákat talál.

### <a name="monitor-capacity-utilization"></a>A kapacitás kihasználtságának figyelése

A kapacitás kihasználtságát különböző szinteken figyelheti. 

#### <a name="vm-level-monitoring"></a>VIRTUÁLIS gépek szintjének figyelése 

A legmagasabb szintű monitorozás (az alkalmazáshoz legközelebb) az alkalmazás virtuális gépén belülről származik. A kapacitás-jelentéskészítés működésének változását a virtuálisgép-ügyfél operációs rendszerén belül kell megfigyelni.

A következő két forgatókönyvben vegyünk fel egy Azure NetApp Files kötetet, amely 1 – TiB-méret (kvóta) szerint van konfigurálva egy 4 TiB-os, ultra Service-szintű kapacitási készleten. 

##### <a name="windows"></a>Windows

A Windows-ügyfelek a hálózati csatlakoztatott meghajtó tulajdonságaival ellenőrizhetők a kötetek felhasznált és rendelkezésre álló kapacitása. Használhatja az **Explorer**  ->  **meghajtó**  ->  **tulajdonságai** lehetőséget.  

A következő példák a kötet kapacitásainak jelentéskészítését mutatják be a Windowsban a megváltozott viselkedés *előtt* :

![A működés megváltozása előtt a kötetek tárolási kapacitását bemutató képernyőképek.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

A parancsot a parancssorban is használhatja az `dir` alábbi ábrán látható módon:

![Képernyőkép, amely azt mutatja, hogy egy parancs használatával jelenítse meg a kötetek tárolási kapacitását a működés megváltozása előtt.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

A következő példák a kötetek kapacitásának jelentéskészítését mutatják be a Windows rendszerben a megváltozott viselkedés *után* :

![A kötetek tárolási kapacitását bemutató Képernyőképek a működés változása után.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

A következő példában a `dir` parancs kimenete látható:  

![Képernyőkép, amely egy parancs használatával jeleníti meg a kötetek tárolási kapacitását a viselkedés változása után.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

A Linux-ügyfelek a [ `df` parancs](https://linux.die.net/man/1/df)használatával ellenőrizhetők a kötetek felhasznált és rendelkezésre álló kapacitása. A `-h` beállítás megjeleníti a méretet, a felhasznált területet és a rendelkezésre álló területet az emberi használatra alkalmas formátumban, az M, a G és a T egység méretével.

A következő példa a kötetek kapacitásainak jelentéskészítését mutatja be a Linux rendszerben a megváltozott viselkedés *előtt* :  

![Képernyőkép, amely bemutatja, hogy a Linux hogyan jeleníti meg a kötetek tárolási kapacitását a működés megváltozása előtt.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

A következő példa a kötetek kapacitásainak jelentéskészítését mutatja be a Linux rendszerben a megváltozott viselkedést *követően* :  

![Képernyőkép, amely bemutatja, hogy a Linux hogyan jeleníti meg a kötetek tárolási kapacitását a működés megváltozása után.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Riasztások konfigurálása a ANFCapacityManager használatával

A Közösség által támogatott Logic Apps ANFCapacityManager eszköz segítségével figyelheti a Azure NetApp Files kapacitását és személyre szabott riasztásokat fogadhat. A ANFCapacityManager eszköz a [ANFCapacityManager GitHub oldalon](https://github.com/ANFTechTeam/ANFCapacityManager)érhető el.

A ANFCapacityManager egy Azure logikai alkalmazás, amely a kapacitáson alapuló riasztási szabályokat kezeli. Ez automatikusan növeli a kötetek méretét, hogy megakadályozza, hogy a Azure NetApp Files kötetek ne fussanak a szabad területről. Egyszerűen üzembe helyezhető, és a következő Alert Management lehetőségeket biztosítja:

* Azure NetApp Files kapacitási készlet vagy kötet létrehozásakor a ANFCapacityManager metrikai riasztási szabályt hoz létre a megadott százalékos felhasznált küszöbérték alapján.
* Azure NetApp Files kapacitási készlet vagy kötet átméretezése esetén a ANFCapacityManager módosítja a metrika riasztási szabályát a megadott százalékos kapacitás felhasznált küszöbértéke alapján. Ha a riasztási szabály nem létezik, a rendszer létrehozza.
* Azure NetApp Files kapacitású készlet vagy kötet törlésekor a rendszer törli a megfelelő metrika-riasztási szabályt.

A következő kulcsfontosságú riasztási beállításokat állíthatja be:  

* **Kapacitási készlet –% teljes küszöbérték** – ez a beállítás határozza meg a felhasználható küszöbértéket, amely riasztást indít a kapacitási készletekhez. 90 érték esetén a riasztás akkor aktiválódik, ha a kapacitási készlet eléri az 90%-ot.
* **Kötet teljes mérete (%)** – ez a beállítás határozza meg a kötetekre vonatkozó riasztást kiváltó felhasználható küszöbértéket. A 80 érték azt eredményezi, hogy a riasztás akkor aktiválódik, ha a kötet eléri a 80%-ot.
* **Meglévő műveleti csoport a kapacitási értesítések számára** – ez a beállítás az a műveleti csoport, amely a kapacitás alapú riasztások esetén aktiválódik. Ezt a beállítást Önnek előre kell létrehoznia. A műveleti csoport küldhet e-mailt, SMS-t vagy más formátumot.

A következő ábra a riasztás konfigurációját mutatja be:  

![A riasztások konfigurálását a ANFCapacityManager használatával bemutató ábra.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

A ANFCapacityManager telepítése után a következő viselkedés várható: Ha egy Azure NetApp Files kapacitású készletet vagy kötetet hoz létre, módosítanak vagy törölnek, a logikai alkalmazás automatikusan létrehoz, módosít vagy töröl egy kapacitás alapú metrika riasztási szabályt a névvel `ANF_Pool_poolname` vagy `ANF_Volume_poolname_volname` . 

### <a name="manage-capacity"></a>Kapacitás kezelése

A figyelés és a riasztás mellett érdemes beépíteni egy alkalmazás-kapacitás felügyeleti gyakorlatot is a Azure NetApp Files (megnövekedett) kapacitás-felhasználás kezelésére. Ha egy Azure NetApp Files kötet vagy kapacitás-készlet is megtelik, [az alkalmazások megszakítása nélkül további kapacitást is megadhat a menet közben](azure-netapp-files-resize-capacity-pools-or-volumes.md). Ez a szakasz különböző manuális és automatizált módszereket ismertet a kötetek és a kapacitások kiépített területének igény szerinti növelésére.
 
#### <a name="manual"></a>Kézi 

A-portál vagy a parancssori felület használatával manuálisan növelheti a kötetek és a kapacitások készletének méretét. 

##### <a name="portal"></a>Portál 

Szükség szerint [módosíthatja a kötetek méretét](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) . A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába.

1. A NetApp-fiók kezelése panelen kattintson a **kötetek** elemre.  
2. Kattintson a jobb gombbal az átméretezni kívánt kötet nevére, vagy kattintson a `…` kötet sor végén található ikonra a helyi menü megjelenítéséhez. 
3. A kötet átméretezéséhez vagy törléséhez használja a helyi menü beállításait.   

   ![A kötet helyi menüjének beállításait bemutató képernyőkép.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![A mennyiségi kvóta frissítése ablakot megjelenítő képernyőkép.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

Bizonyos esetekben a gazdagép-kapacitási készlet nem rendelkezik elegendő kapacitással a kötetek átméretezéséhez. [A kapacitási készlet mérete](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) azonban 1 – TiB-növekményekben vagy-csökkentésekben is módosítható. A kapacitási készlet mérete nem lehet kisebb, mint 4 TiB. *A kapacitás-készlet átméretezése megváltoztatja a megvásárolt Azure NetApp Files kapacitást.*

1. A NetApp-fiók kezelése panelen kattintson az átméretezni kívánt kapacitási készletre.
2. Kattintson a jobb gombbal a kapacitási készlet nevére, vagy a `…` helyi menü megjelenítéséhez kattintson a kapacitási készlet sora végén található ikonra.
3. A kapacitási készlet átméretezéséhez vagy törléséhez használja a helyi menü beállításait.    

   ![A kapacitási készlet helyi menüjének beállításait bemutató képernyőkép.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![A készlet átméretezése ablakot megjelenítő képernyőkép.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI vagy PowerShell

A kötet vagy a kapacitási készlet méretének manuális módosításához használhatja a [Azure NETAPP Files CLI-eszközöket](azure-netapp-files-sdk-cli.md#cli-tools), beleértve az Azure CLI-t és Azure PowerShellt is.  A következő két parancs használható Azure NetApp Files kötet-és készlet-erőforrások kezeléséhez:  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

Azure NetApp Files erőforrások Azure CLI-vel való kezeléséhez nyissa meg a Azure Portal, és válassza ki az Azure **Cloud Shell** hivatkozást a menüsáv tetején: 

[![A Cloud Shell hivatkozás elérését bemutató képernyőkép. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

A művelettel megnyílik a Azure Cloud Shell:

[![Képernyőkép, amely Cloud Shell ablakot jelenít meg. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

Az alábbi példák a következő parancsokat használják a kötetek méretének [megjelenítéséhez](/cli/azure/netappfiles/volume#az-netappfiles-volume-show) és [frissítéséhez](/cli/azure/netappfiles/volume#az-netappfiles-volume-update) :
 
[![Képernyőkép a kötet méretének megjelenítéséhez a PowerShell használatával. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![Képernyőkép a kötet méretének frissítéséhez a PowerShell használatával. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

Az alábbi példák a következő parancsokat használják a kapacitási készlet méretének [megjelenítéséhez](/cli/azure/netappfiles/pool#az-netappfiles-pool-show) és [frissítéséhez](/cli/azure/netappfiles/pool#az-netappfiles-pool-update) :

[![Képernyőfelvétel: a PowerShell használatával jeleníthető meg a kapacitási készlet mérete. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![Képernyőkép a kapacitási készlet méretének frissítéséhez a PowerShell használatával. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Automatizált  

Létrehozhat egy automatizált folyamatot a megváltozott viselkedés kezeléséhez.

##### <a name="rest-api"></a>REST API   

A Azure NetApp Files szolgáltatás REST API a HTTP-műveleteket definiálja az erőforrásokon, például a NetApp-fiókon, a kapacitás-készleten, a köteteken és a pillanatképeken. A Azure NetApp Files REST API-specifikációja a [Azure NetApp Files Resource Manager GitHub oldalán](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)] jelenik meg. A GitHubon megtalálhatja a REST API-kkal való [használatra szolgáló kódot](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) .

Lásd: [Azure NetApp Files fejlesztése a REST APIsal](azure-netapp-files-develop-with-rest-api.md). 

##### <a name="rest-api-using-powershell"></a>A REST API használata a PowerShell-lel  

A Azure NetApp Files szolgáltatás REST API a HTTP-műveleteket definiálja az erőforrásokon, például a NetApp-fiókon, a kapacitás-készleten, a köteteken és a pillanatképeken. A [Azure NetApp Files REST API-specifikációját](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) a githubon keresztül tesszük közzé.

Lásd: [Azure NetApp Files fejlesztése a REST API PowerShell használatával](develop-rest-api-powershell.md).

##### <a name="capacity-management-using-anfcapacitymanager"></a>Kapacitások kezelése a ANFCapacityManager használatával

A ANFCapacityManager egy Azure logikai alkalmazás, amely a kapacitáson alapuló riasztási szabályokat kezeli. Ez automatikusan növeli a kötetek méretét, hogy megakadályozza, hogy a Azure NetApp Files kötetek ne fussanak a szabad területről. A riasztások küldésén kívül a kötetek és a kapacitások méretének automatikus növelését is lehetővé teszi, hogy a Azure NetApp Files kötetek ne fussanak a szabad területről: 

* Ha egy Azure NetApp Files kötet eléri a megadott százalékos felhasználható küszöbértéket, a rendszer a mennyiségi kvótát (méretet) a 10-100-as számú százalékban megadott százalékkal növeli.  
* Ha a kötet méretének növelése meghaladja a kapacitást tartalmazó készlet kapacitását, akkor a kapacitási készlet mérete is megnő az új kötet méretének megfelelően.

A következő kulcsfontosságú kapacitás-kezelési beállítást állíthatja be:  

* Az automatikus növekedés százalékos arányának **növelése** – a meglévő kötetek méretének százalékos aránya, hogy automatikusan növelje a kötetet, ha eléri a megadott **%-os teljes küszöbértéket**. A 0 (nulla) érték letiltja az automatikus növekedés funkciót. A 10 és 100 közötti érték ajánlott.

    ![Képernyőfelvétel: a kötet automatikus növekedési százalékának beállítása ablak.](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>GYIK 

Ez a szakasz a mennyiségi kvóta változásával kapcsolatos kérdésekre ad választ. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>A pillanatkép-terület a kötetek felhasználható vagy kiosztott kapacitása felé is érvényes?

Igen, a felhasznált Pillanatképek kapacitása a kötet kiépített területe felé is beleszámít. Ha a kötet teljes mértékben fut, vegye figyelembe a két szervizelési beállítást:

* Méretezze át a kötetet a jelen cikkben leírtak szerint.
* Távolítsa el a régebbi pillanatképeket, és szabadítson fel helyet az üzemeltetési köteten.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Ez a módosítás azt jelenti, hogy a kötet automatikus növekedésének viselkedése eltűnik a Azure NetApp Filesből?

Gyakori tévhit, hogy Azure NetApp Files *kötetek* automatikusan növekednek a kitöltéskor. A kötetek kiosztása a tényleges készlettől függetlenül 100 TiB-mérettel történt, míg az üzembe helyezési *kapacitás készlete* automatikusan 1 TIB-os növekedéssel nő. Ez a változás a beállított kvótához tartozó (látható és használható) *kötet* méretével foglalkozik, és a *kapacitás-készletek* többé nem növekednek automatikusan. Ez a változás általában az ügyféloldali terület és a kapacitás jelentésének pontos eredményét eredményezi. Elkerüli a "szökevény" kapacitás felhasználását.

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Befolyásolja ez a változás a régiók közötti replikációval (előzetes verzió) replikált kötetek esetében? 

A rögzített kötetre vonatkozó kvóta nincs kikényszerítve a replikálási célként megadott köteteken.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Befolyásolja ez a változás a Azure Monitor jelenleg elérhető metrikák esetében?

A portál metrikái és Azure Monitor statisztikái pontosan tükrözik az új kiosztási és kihasználtsági modellt.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Befolyásolja ez a változás a Azure NetApp Files erőforrás-korlátozásait?

A jelen cikkben ismertetett kvóták változásain túli Azure NetApp Files erőforrás-korlátai nem változnak.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Van példa ANFCapacityManager munkafolyamat?  

Igen. Tekintse meg a [kötet automatikus növekedési munkafolyamat-példa GitHub-lapját](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md).

### <a name="is-anfcapacitymanager-microsoft-supported"></a>Támogatott-e a Microsoft ANFCapacityManager?  

[A ANFCapacityManager logikai alkalmazás a-ben van megadva, és a NetApp vagy a Microsoft nem támogatja](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer). Javasoljuk, hogy módosítsa az adott környezethez vagy követelményekhez igazodva. Az üzleti szempontból kritikus vagy éles környezetekben való üzembe helyezés előtt tesztelje a funkcionalitást.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Hogyan jelenthetem be a hibát vagy a ANFCapacityManger vonatkozó szolgáltatási kérelmet?
A [ANFCapacityManager GitHub oldalán](https://github.com/ANFTechTeam/ANFCapacityManager/issues)az **új probléma** lehetőségre kattintva elküldheti a hibákat és a szolgáltatásra vonatkozó kéréseket.

## <a name="next-steps"></a>Következő lépések
* [A kapacitáskészlet vagy kötet átméretezése](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Az Azure NetApp Files metrikái](azure-netapp-files-metrics.md)