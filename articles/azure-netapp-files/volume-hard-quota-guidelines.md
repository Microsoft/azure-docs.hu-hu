---
title: Mit jelent a mennyiségi kvóta módosítása a Azure NetApp Files szolgáltatási | Microsoft Docs
description: Ismerteti a mennyiségi kvóta használatának változását, a változás tervezésének, valamint a kapacitások figyelése és kezelése mikéntját.
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
ms.date: 03/29/2021
ms.author: b-juche
ms.openlocfilehash: 5e7f71f91e5778b4f096bb760bfe5a0a89b5cbcb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764278"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Mit jelent a kvótakvóta mennyiségi módosítása a Azure NetApp Files számára

A szolgáltatás elejétől kezdve a Azure NetApp Files egy kapacitáskészlet-kiépítési és automatikus növekedési mechanizmust használt. Azure NetApp Files kötetek egy kiválasztott réteg és méret aláfedő, ügyfél által kiépített kapacitáskészleten vannak jelölve. A kötetméretek (kvóták) a teljesítményt és a kapacitást biztosítják, és a kvóták bármikor módosíthatók működés közben. Ez a viselkedés azt jelenti, hogy jelenleg a kötetkvóta a kötet sávszélességének szabályozására használt teljesítménykvóta. Az aláfedő kapacitáskészletek jelenleg automatikusan növekednek, amikor a kapacitás megtelik.   

> [!IMPORTANT] 
> A Azure NetApp Files és a kapacitáskészlet-kiépítés viselkedése  manuális és vezérelhető *mechanizmusra változik.* **2021. április 30-tól (frissítve) a kötetméretek (kvóta) kezelik a sávszélesség teljesítményét, valamint a kiépített kapacitást, és a mögöttes kapacitáskészletek nem növekednek automatikusan.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>A mennyiségi kvóta változásának okai

Számos ügyfél a kezdeti viselkedés három fő *kihívását jelezte* ki:
* A virtuálisgép-ügyfelek az operációs rendszer tárhelyének vagy kapacitásfigyelő eszközeinek használata esetén bármely kötet vékonyan kiépített (100 TiB) kapacitását látják, ami pontatlan ügyfél- vagy alkalmazásoldali kapacitás-láthatóságot biztosít.
* A kapacitáskészlet automatikus növekedésének viselkedése miatt az alkalmazástulajdonosok nem szabályoznék a kiépített kapacitáskészlet-területet (és a kapcsolódó költségeket). Ez a helyzet nehézkes olyan környezetekben, ahol a "kifutott folyamatok" gyorsan megtelhetnek, és növekedhetnek a kiépített kapacitás és költségek.
* Az ügyfelek közvetlen korrelációt szeretnék látni és fenntartani a kötetméret (kvóta) és a teljesítmény között. A kötetek (kapacitás alapján történő) előfizetése és a készlet automatikus növelése aktuális (implicit) viselkedése miatt az ügyfelek nem rendelkeznek közvetlen korrelációval, amíg a kötetkvóta aktív beállítása vagy alaphelyzetbe nem állítása meg nem történt. 

Számos ügyfél kérte a kiépített kapacitás közvetlen vezérlését. Szabályozni és egyensúlyba szeretnék tartani a tárkapacitást és a kihasználtságot. Emellett szeretnék szabályozni a költségeket, valamint az alkalmazásoldali és ügyféloldali láthatóságot az alkalmazásköteteik elérhető, felhasznált és kiépített kapacitásával és teljesítményével. 

## <a name="what-is-the-volume-hard-quota-change"></a>Mi a mennyiségi kvótakvóta módosítása?   

A kötetkvóta-módosítással a Azure NetApp Files kötetek 100 TiB-os értéken már nem lesznek vékonyan kiépítve. A kötetek a tényleges konfigurált méretben (kvótában) lesznek kiépítve. Emellett az aláfedő kapacitáskészletek nem növekednek automatikusan a teljes kapacitásfelhasználás elérésekor. Ez a változás az Azure-beli felügyelt lemezekhez hasonló viselkedést tükrözi, amelyek szintén a jelen esetben vannak kiépítve, automatikus kapacitásnövekedés nélkül.

Tegyük fel például, hogy Azure NetApp Files 1-TiB mérettel (kvótával) konfigurált kötet egy 4 TiB Ultra szintű kapacitáskészleten. Az alkalmazások folyamatosan írnak adatokat a kötetre.

A *kezdeti viselkedés:*  
* Várt sávszélesség: 128 MiB/s
* Teljes használható (és látható ügyfél) kapacitás: 100 TiB   
    Ennél a méretnél több adatot nem tud majd írni a köteten.
* Kapacitáskészlet: Ha megtelt, 1 TiB-os növekményekkel automatikusan növekszik.
* Kötetkvóta módosítása: Csak a kötet teljesítményét (sávszélességét) módosítja. Nem módosítja az ügyfél látható vagy használható kapacitását.

A *módosított* viselkedés:  
* Várt sávszélesség: 128 MiB/s
* Teljes használható (és az ügyfél számára látható) kapacitás: 1 TiB Nem fog tudni ennél nagyobb méretű adatot írni a kötetre.
* Kapacitáskészlet: 4 TiB méretű marad, és nem nő automatikusan. 
* Kötetkvóta módosítása: Módosítja a kötet teljesítményét (sávszélességét) és az ügyfél látható vagy használható kapacitását.

A kötetek és kapacitáskészletek kihasználtságának proaktív Azure NetApp Files monitorokat kell követnie. A teljes használathoz szándékosan módosítania kell a kötet és a készlet kihasználtságát. Azure NetApp Files továbbra is engedélyezi a működés közbeni kötet- és [kapacitáskészlet-átméretezési műveleteket.](azure-netapp-files-resize-capacity-pools-or-volumes.md)

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>A kötetkvóta-módosítás működőképesizálása

Ez a szakasz útmutatást nyújt a kötetkvóta változásának a zökkenőmentes átállás érdekében való működéshez. Emellett betekintést nyújt a jelenleg kiépített kötetek és kapacitáskészletek kezeléséhez, a folyamatos monitorozáshoz, valamint a riasztási és kapacitáskezelési lehetőségekhez.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>Jelenleg kiépített kötetek és kapacitáskészletek

A kötetkvóta-kvóta módosítása miatt módosítania kell az üzemeltetési modellt. A kiépített kötetek és kapacitáskészletek folyamatos kapacitáskezelést igényelnek.  Mivel a módosított viselkedés azonnal bekövetkezik, a Azure NetApp Files csapata egy sor, a meglévő, korábban kiépített kötetek és kapacitáskészletek számára javasolt, az ebben a szakaszban leírtak szerint.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Egyszeres korrekciós vagy megelőző intézkedésekre vonatkozó javaslatok  

A kötetkvóta-módosítás a korábban kiépített kötetek és készletek kiépített és rendelkezésre álló kapacitásának változásait eredményezi. Ennek eredményeképpen bizonyos kapacitáskiosztási kihívások fordulhatnak elő. Annak érdekében, hogy az ügyfelek ne kerüljék el a rövid távon eltűrő helyzeteket, az Azure NetApp Files csapata a következő, egyszeres korrekciós/megelőző intézkedéseket javasolja: 

* **Kiépített kötetméretek:**   
    Méretezze át az összes kiépített kötetet a megfelelő pufferre a változási sebesség és a riasztások vagy a rendelkezésre állási idő (például 20% a jellemző számítási feladatok alapján) alapján, legfeljebb 100 TiB (a kötet méretkorlátja) [alapján.](azure-netapp-files-resource-limits.md#resource-limits) Az új kötetméretnek, beleértve a pufferkapacitást is, a következő tényezők alapján kell figyelembe vennie:
    * **Kiépített** kötetkapacitás, ha a felhasznált kapacitás kisebb, mint a kiépített kötetkvóta.
    * **Felhasznált** kötetkapacitás, ha a felhasznált kapacitás nagyobb, mint a kiépített kötetkvóta.  
    A kötetszintű kapacitás növelése nem számít fel további díjat, ha az aláfedő kapacitáskészletet nem kell növelni. Ennek a változásnak a hatállyal  a kötet sávszélességkorlát-növekedését figyelheti meg (ha az automatikus QoS-kapacitáskészlet-típust használja). [](azure-netapp-files-understand-storage-hierarchy.md#qos_types)

* **Kiépített kapacitáskészletek méretei:**   
    A kötetméretek módosítása után, ha a kötetméretek összege nagyobb, mint az üzemeltetési kapacitáskészlet mérete, a kapacitáskészletet a kötetek összegével egyenlő vagy annál nagyobb méretre [](azure-netapp-files-resource-limits.md#resource-limits)kell növelni, amely legfeljebb 500 TiB (ez a kapacitáskészlet méretkorlátja). A további kapacitáskészlet-kapacitásra a szokásos módon az ACR-díj vonatkozik.

Ha segítségre van szüksége a monitorozás és a riasztások beállításához, Azure NetApp Files a környezet ellenőrzéséhez az alábbi szakaszokban leírtak szerint működjön együtt a biztonsági szakemberekkel.

### <a name="ongoing-capacity-management"></a>Folyamatos kapacitáskezelés  

Miután végrehajtotta az egyszer szükséges korrekciós intézkedéseket, össze kell hoznia a kapacitás figyelése és kezelése érdekében folyamatban lévő folyamatokat. A következő szakaszok a kapacitás monitorozására és kezelésére vonatkozó javaslatokat és alternatív megoldásokat tartalmaznak.

### <a name="monitor-capacity-utilization"></a>Kapacitás kihasználtságának figyelése

A kapacitáskihasználtságot különböző szinteken figyelheti. 

#### <a name="vm-level-monitoring"></a>Virtuálisgép-szintű monitorozás 

A legmagasabb szintű figyelés (az alkalmazáshoz legközelebb) az alkalmazás virtuális gépről való. Megfigyelheti a virtuálisgép-ügyfél operációs rendszeréről származó kapacitásjelentések viselkedésének változását.

A következő két esetben érdemes egy 1–TiB méretű (kvótával) konfigurált Azure NetApp Files-kötetet egy 4 TiB méretű, ultra szintű kapacitáskészleten. 

##### <a name="windows"></a>Windows

A Windows-ügyfelek a csatlakoztatott hálózati meghajtó tulajdonságaival ellenőrizhetik a kötet felhasznált és elérhető kapacitását. Használhatja az **Explorer**  ->  **meghajtótulajdonságokat.**  ->    

Az alábbi példák a windowsos kötetkapacitás *jelentéskészítését mutatják be a* módosított viselkedés előtt:

![Képernyőképek a kötet tárolási kapacitásának példáiról a viselkedés módosítása előtt.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

Az parancsot a `dir` parancssorban is használhatja az alább látható módon:

![Képernyőkép egy kötet tárolási kapacitásának a viselkedés módosítása előtti megjelenítéséhez használt paranccsal.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

Az alábbi példák a windowsos kötetkapacitás *jelentéskészítését mutatják be a* módosított viselkedés után:

![Képernyőképek a kötet tárolási kapacitásának példáiról a viselkedés módosítása után.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

Az alábbi példa a parancs `dir` kimenetét mutatja be:  

![Képernyőkép egy kötet tárolási kapacitásának a viselkedés módosítása utáni megjelenítésére parancs használatával.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

A Linux-ügyfelek a paranccsal ellenőrizhetik egy kötet felhasznált és elérhető [ `df` kapacitását.](https://linux.die.net/man/1/df) A beállítás a méretet, a felhasznált területet és a rendelkezésre álló területet mutatja ember által olvasható formátumban, M, G és `-h` T egységméretek használatával.

Az alábbi példa a linuxos kötetkapacitás jelentéskészítését *mutatja be a* módosított viselkedés előtt:  

![Képernyőkép egy kötet tárolási kapacitásának Linuxszal való megjelenítéséről a viselkedés módosítása előtt.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

Az alábbi példa a linuxos kötetkapacitás jelentéskészítését *mutatja be a* módosított viselkedés után:  

![Képernyőkép egy kötet tárolási kapacitásának Linuxszal való megjelenítéséről a viselkedés módosítása után.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Riasztások konfigurálása az ANFCapacityManager használatával

Az ANFCapacityManager eszköz közösségi támogatású Logic Apps segítségével figyelheti a kapacitást Azure NetApp Files és testre szabott riasztásokat kaphat. Az ANFCapacityManager eszköz az [ANFCapacityManager GitHub-oldalon érhető el.](https://github.com/ANFTechTeam/ANFCapacityManager)

Az ANFCapacityManager egy kapacitásalapú riasztási szabályokat kezelő Azure Logic App. Automatikusan növeli a kötetméretet, hogy Azure NetApp Files kötetek ne fogynak el a rendelkezésre álló területből. Könnyen üzembe helyezhető, és a következő funkciókat Alert Management biztosítja:

* A kapacitáskészlet Azure NetApp Files kötet létrehozásakor az ANFCapacityManager létrehoz egy metrikriasztás-szabályt a megadott százalékos felhasznált mennyiség küszöbértéke alapján.
* Egy Azure NetApp Files kapacitáskészlet vagy kötet átméretezhetők, az ANFCapacityManager módosítja a metrikaalapú riasztási szabályt a kapacitás által felhasznált kapacitás megadott százalékának küszöbértéke alapján. Ha a riasztási szabály nem létezik, létrejön.
* Ha egy Azure NetApp Files vagy kötetet törölnek, a vonatkozó metrikriasztás-szabály törlődik.

A következő fő riasztási beállításokat konfigurálhatja:  

* **Kapacitáskészlet %-os teljes küszöbértéke** – Ez a beállítás határozza meg a felhasznált küszöbértéket, amely riasztást vált ki a kapacitáskészletek számára. A 90 érték riasztást vált ki, ha a kapacitáskészlet eléri a 90%-ot.
* **Kötet %-os teljes küszöbértéke** – Ez a beállítás határozza meg a felhasznált küszöbértéket, amely riasztást vált ki a kötetek számára. A 80 érték riasztást vált ki, ha a kötet eléri a 80%-ot.
* **Kapacitásértesítések** meglévő műveletcsoportja – Ez a beállítás a kapacitásalapú riasztáshoz aktivált műveletcsoport. Ezt a beállítást Önnek kell előre létrehoznia. A műveletcsoport küldhet e-mailt, SMS-t vagy más formátumot.

Az alábbi ábra a riasztás konfigurációját mutatja be:  

![Az ANFCapacityManager használatával riasztáskonfigurációt bemutató ábra.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

Az ANFCapacityManager telepítése után a következő viselkedés várható Azure NetApp Files: Kapacitáskészlet vagy kötet létrehozásakor, módosításakor vagy törlésekor a logikai alkalmazás automatikusan létrehoz, módosít vagy töröl egy kapacitásalapú metrikaalapú riasztási szabályt vagy `ANF_Pool_poolname` `ANF_Volume_poolname_volname` néven. 

### <a name="manage-capacity"></a>Kapacitás kezelése

A monitorozás és a riasztások mellett egy alkalmazáskapacitás-kezelési gyakorlatot is érdemes beépítenie a Azure NetApp Files (megnövekedett) kapacitásfelhasználás kezeléséhez. Ha egy Azure NetApp Files vagy kapacitáskészlet megtelik, az alkalmazás megszakítása nélkül lehet további kapacitást biztosítani a [gépről.](azure-netapp-files-resize-capacity-pools-or-volumes.md) Ez a szakasz különböző manuális és automatizált módszereket ismertet a kötet- és kapacitáskészlet által kiépített terület szükség szerinti növeléséhez.
 
#### <a name="manual"></a>Kézi 

A kötet- vagy kapacitáskészletek méretének manuális növeléséhez használhatja a portált vagy a cli-t. 

##### <a name="portal"></a>Portál 

A kötet [méretét szükség szerint](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) módosíthatja. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába.

1. A Manage NetApp Account (NetApp-fiók kezelése) panelen kattintson a **Volumes (Kötetek) elemre.**  
2. Kattintson a jobb gombbal az átméretezni kívánt kötet nevére, vagy kattintson a kötet sorának végén található ikonra a helyi `…` menü megjelenítéséhez. 
3. A helyi menü beállításaival átméretezheti vagy törölheti a kötetet.   

   ![A kötet helyi menüjének beállításait bemutató képernyőkép.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![A Kötetkvóta frissítése ablakot bemutató képernyőkép.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

Bizonyos esetekben az üzemeltetési kapacitáskészlet nem rendelkezik elegendő kapacitással a kötetek átméretezéshez. A kapacitáskészlet [méretét](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) azonban módosíthatja 1 TiB-os növekményekben vagy növelésekben. A kapacitáskészlet mérete nem lehet kisebb 4 TiB-osnál. *A kapacitáskészlet átméretezése módosítja a megvásárolt Azure NetApp Files kapacitást.*

1. A Manage NetApp Account (NetApp-fiók kezelése) panelen kattintson az átméretezni kívánt kapacitáskészletre.
2. Kattintson a jobb gombbal a kapacitáskészlet nevére, vagy kattintson a kapacitáskészlet sorának végén található ikonra a `…` helyi menü megjelenítéséhez.
3. A helyi menü beállításaival átméretezheti vagy törölheti a kapacitáskészletet.    

   ![A kapacitáskészlet helyi menüjének beállításait bemutató képernyőkép.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![A Készlet átméretezése ablakot bemutató képernyőkép.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI vagy PowerShell

A kötet vagy [a Azure NetApp Files](azure-netapp-files-sdk-cli.md#cli-tools)méretének manuális módosíthatósága érdekében használhatja a Azure NetApp Files CLI-eszközöket, beleértve az Azure CLI-t és a Azure PowerShell is.  Az alábbi két paranccsal felügyelheti a Azure NetApp Files- és készleterőforrásokat:  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

A Azure NetApp Files Azure CLI használatával való kezeléséhez nyissa meg a Azure Portal, és válassza az Azure **Cloud Shell** hivatkozást a menüsáv tetején: 

[![A hivatkozás elérésének Cloud Shell képernyőképe. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Ez a művelet megnyitja a Azure Cloud Shell:

[![Képernyőkép az Cloud Shell ablakról. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

Az alábbi példák a [](/cli/azure/netappfiles/volume#az_netappfiles_volume_show) parancsokkal [](/cli/azure/netappfiles/volume#az_netappfiles_volume_update) mutatják be és frissítik egy kötet méretét:
 
[![Képernyőkép a kötetméret PowerShell-rel való megjelenítésekor. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![Képernyőkép a kötetméret PowerShell használatával való frissítésével. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

Az alábbi példák a [](/cli/azure/netappfiles/pool#az_netappfiles_pool_show) parancsokkal [](/cli/azure/netappfiles/pool#az_netappfiles_pool_update) mutatják be és frissítik a kapacitáskészletek méretét:

[![Képernyőkép a kapacitáskészlet méretének PowerShell-rel való megjelenítésekor. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![Képernyőkép a kapacitáskészlet méretének PowerShell használatával való frissítésével. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Automatizált  

A módosított viselkedés kezelésére automatizált folyamatot is felépíthet.

##### <a name="rest-api"></a>REST API   

A REST API szolgáltatás Azure NetApp Files http-műveleteket határoz meg az olyan erőforrásokon, mint a NetApp-fiók, a kapacitáskészlet, a kötetek és a pillanatképek. A REST API specifikációja Azure NetApp Files a következő [GitHub Azure NetApp Files Resource Manager oldalon](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)érhető el: ]. A REST [API-khoz használható](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) példakódot a GitHubon találhatja meg.

Lásd: [Fejlesztés Azure NetApp Files a REST API.](azure-netapp-files-develop-with-rest-api.md) 

##### <a name="rest-api-using-powershell"></a>A REST API használata a PowerShell-lel  

A REST API szolgáltatás Azure NetApp Files http-műveleteket határoz meg az olyan erőforrásokon, mint a NetApp-fiók, a kapacitáskészlet, a kötetek és a pillanatképek. A [REST API specifikációja Azure NetApp Files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) a GitHubon keresztül érhető el.

Lásd: [Fejlesztés Azure NetApp Files és REST API PowerShell használatával.](develop-rest-api-powershell.md)

##### <a name="capacity-management-using-anfcapacitymanager"></a>Kapacitáskezelés az ANFCapacityManager használatával

Az ANFCapacityManager egy kapacitásalapú riasztási szabályokat kezelő Azure Logic App. Automatikusan növeli a kötetméretet, hogy Azure NetApp Files kötetek ne fogynak el a rendelkezésre álló területből. A riasztások küldése mellett lehetővé teszi a kötet- és kapacitáskészletek méretének automatikus növekedését is, hogy a Azure NetApp Files kötetek ne fogynak el a rendelkezésre álló területből: 

* Ha egy Azure NetApp Files eléri a megadott százalékos felhasznált mennyiséget, a kötetkvóta (méret) a 10 és 100 között megadott százalékkal nő.  
* Ha a kötet méretének növelése meghaladja a kapacitáskészlet kapacitását, a kapacitáskészlet mérete is nő az új kötetméretnek való igazodás érdekében.

A következő kulcskapacitás-kezelési beállítást konfigurálhatja:  

* **Automatikus növekedés százalékának növelése** – A kötet méretének százalékos aránya a kötet automatikus növeléséhez, ha eléri a megadott **%-os teljes küszöbértéket.** A 0 (nulla) érték letiltja az automatikus növekedés funkciót. A javasolt érték 10 és 100 között van.

    ![Képernyőkép a Kötet automatikus növekedési arányának beállítása ablakról.](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>GYIK 

Ez a szakasz a mennyiségi kvótakvóta-módosítással kapcsolatos néhány kérdésre ad választ. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>Számít a pillanatkép-terület egy kötet használható vagy kiépíthető kapacitásában?

Igen, a felhasznált pillanatkép-kapacitás beleszámol a köteten kihelyelt területbe. Ha a kötet megtelt, fontolja meg a következő két szervizelési lehetőséget:

* Méretezze át a kötetet a cikkben leírtak szerint.
* Távolítsa el a régebbi pillanatképeket, hogy helyet szabadítson fel az üzemeltetési köteten.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Ez a változás azt jelenti, hogy a kötet automatikus növekedésének viselkedése eltűnik a Azure NetApp Files?

Gyakori félreértés, hogy a Azure NetApp Files *mennyiségei* automatikusan növekednek a feltöltéskor. A kötetek 100 TiB mérettel, a tényleges beállított kvótától függetlenül vékonyan voltak kiépítve, míg az átfedésben található kapacitáskészlet automatikusan növekedett 1 TiB-os növekményekkel.  Ez a módosítás a beállított kvótára fogja módosítani a  (látható és *használható)* kötetméretet, és a kapacitáskészletek nem növekednek automatikusan. Ez a változás gyakran kívánt pontos ügyféloldali területet és kapacitásjelentést ad. Elkerüli az "elfogyott" kapacitásfelhasználást.

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Ez a módosítás hatással van a régiók közötti replikációval (előzetes verzió) replikált kötetre? 

A rendszer nem kényszeríti a kötetkvóta kényszerítése a replikáció célkötetei esetében.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Van ez a módosítás hatással a jelenleg a Azure Monitor?

A portál mérőszámai és Azure Monitor statisztikák pontosan tükrözik az új foglalási és kihasználtsági modellt.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Ez a módosítás hatással van az erőforráskorlátra a Azure NetApp Files?

Az erőforráskorlátok nem módosulnak a Azure NetApp Files a cikkben ismertetett kvótaváltozások után.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Van példa ANFCapacityManager munkafolyamatra?  

Igen. Lásd a [Kötet automatikus növekedés munkafolyamata – példa GitHub-oldalt.](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md)

### <a name="is-anfcapacitymanager-microsoft-supported"></a>Az ANFCapacityManager Microsoft támogatott?  

[Az ANFCapacityManager](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer)logikai alkalmazást a rendszer adott ként biztosítja, és a NetApp vagy a Microsoft nem támogatja. Javasolt a saját környezetéhez vagy követelményeihez illeszkedő módosítás. Tesztelje a funkciót, mielőtt üzleti kritikus vagy éles környezetben üzembe helyezi.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Hogyan küldhetek hibajelentést vagy funkciókérést az ANFCapacityMangerhez?
A hibákat és a funkciókéréseket az [ANFCapacityManager GitHub-oldalon](https://github.com/ANFTechTeam/ANFCapacityManager/issues)található Új probléma elemre kattintva küldheti el. 

## <a name="next-steps"></a>Következő lépések
* [A kapacitáskészlet vagy kötet átméretezése](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Az Azure NetApp Files metrikái](azure-netapp-files-metrics.md)
