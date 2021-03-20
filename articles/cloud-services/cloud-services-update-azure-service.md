---
title: Cloud Service (klasszikus) frissítése | Microsoft Docs
description: Ismerje meg, hogyan frissítheti a Cloud Servicest az Azure-ban. Megtudhatja, hogyan folytathatja a frissítését a felhőalapú szolgáltatásokban a rendelkezésre állás biztosítása érdekében.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 5d85003ca7b4307c308914484502ae03269f66ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741111"
---
# <a name="how-to-update-an-azure-cloud-service-classic"></a>Azure Cloud Service (klasszikus) frissítése

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A felhőalapú szolgáltatás frissítése, beleértve a szerepköröket és a vendég operációs rendszert is, egy három lépésből álló folyamat. Először is fel kell tölteni az új felhőalapú szolgáltatás vagy operációs rendszer verziójának bináris fájljait és konfigurációs fájlját. Ezután az Azure a Cloud Service számítási és hálózati erőforrásait az új Cloud Service-verzió követelményei alapján fenntartja. Végül az Azure működés közbeni frissítésével fokozatosan frissíti a bérlőt az új verzióra vagy a vendég operációs rendszerre, miközben megőrizheti a rendelkezésre állását. Ez a cikk az utolsó lépés részleteit tárgyalja – a működés közbeni frissítést.

## <a name="update-an-azure-service"></a>Azure-szolgáltatás frissítése
Az Azure a szerepkör-példányokat a frissítési tartományok (UD) nevű logikai csoportokba rendezi. A frissítési tartományok (UD) a szerepkör-példányok olyan logikai csoportjai, amelyek csoportként frissülnek.  Az Azure egy adott időpontban frissíti a Cloud Service szolgáltatást, amely lehetővé teszi, hogy a más frissítési lévő példányok továbbra is kiszolgálják a forgalmat.

A frissítési tartományok alapértelmezett száma 5. A szolgáltatás definíciós fájljában (. csdef) a upgradeDomainCount attribútumot is megadhatja a frissítési tartományok különböző számának megadásával. További információ a upgradeDomainCount attribútumról: [Azure Cloud Services Definition Schema (. Csdef fájl)](./schema-csdef-file.md).

Ha helyben frissíti a szolgáltatás egy vagy több szerepkörét, az Azure frissíti a szerepkör-példányok készletét azon frissítési tartomány szerint, amelyhez tartoznak. Az Azure frissíti az adott frissítési tartomány összes példányát – leállítja őket, frissíti őket, és online állapotba helyezi őket –, majd a következő tartományra kerül. Ha csak az aktuális frissítési tartományban futó példányokat szeretné leállítani, az Azure gondoskodik arról, hogy egy frissítés a lehető legkevesebb hatással legyen a futó szolgáltatásra. További információért lásd a cikk későbbi, [a frissítés folytatását](#howanupgradeproceeds) ismertető cikket.

> [!NOTE]
> Habár a **frissítés** és a **frissítés** feltételei némileg eltérő jelentéssel rendelkeznek az Azure kontextusában, a jelen dokumentum funkcióinak folyamataihoz és leírásához egyaránt felhasználhatók.
>
>

A szolgáltatásnak egy szerepkör legalább két példányát kell meghatároznia ahhoz, hogy az adott szerepkört leállás nélkül frissíteni lehessen. Ha a szolgáltatás az egyik szerepkörből csak egy példányból áll, a szolgáltatás addig nem lesz elérhető, amíg a helyben végzett frissítés be nem fejeződik.

Ez a témakör az Azure Updates szolgáltatással kapcsolatos alábbi információkat tartalmazza:

* [A szolgáltatás engedélyezett változásai a frissítés során](#AllowedChanges)
* [A frissítés előrehaladása](#howanupgradeproceeds)
* [Frissítés visszaállítása](#RollbackofanUpdate)
* [Több mutációs művelet kezdeményezése folyamatban lévő központi telepítésre](#multiplemutatingoperations)
* [Szerepkörök eloszlása frissítési tartományok között](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>A szolgáltatás engedélyezett változásai a frissítés során
A következő táblázat a szolgáltatásnak a frissítés során engedélyezett módosításait mutatja be:

| Az üzemeltetés, a szolgáltatások és a szerepkörök számára engedélyezett módosítások | Helyben történő frissítés | Előkészített (VIP swap) | Törlés és újbóli üzembe helyezés |
| --- | --- | --- | --- |
| Operációs rendszer verziója |Igen |Igen |Yes |
| .NET megbízhatósági szint |Igen |Igen |Yes |
| <sup>1</sup> . virtuális gép mérete |Igen<sup>2</sup> |Igen |Yes |
| Helyi tárolási beállítások |Csak<sup>2</sup> |Igen |Yes |
| Szerepkörök hozzáadása vagy eltávolítása egy szolgáltatásban |Igen |Igen |Yes |
| Egy adott szerepkör példányainak száma |Igen |Igen |Yes |
| A szolgáltatáshoz tartozó végpontok száma vagy típusa |Igen<sup>2</sup> |Nem |Igen |
| A konfigurációs beállítások nevei és értékei |Igen |Igen |Yes |
| A konfigurációs beállítások értékei (de nem nevei) |Igen |Igen |Yes |
| Új tanúsítványok hozzáadása |Igen |Igen |Yes |
| Meglévő tanúsítványok módosítása |Igen |Igen |Yes |
| Új kód üzembe helyezése |Igen |Igen |Yes |

<sup>1</sup> a méret változása a felhőalapú szolgáltatás számára elérhető méretek részhalmazára korlátozódik.

<sup>2</sup> az Azure SDK 1,5-as vagy újabb verzióját igényli.

> [!WARNING]
> A virtuális gép méretének módosítása a helyi adatmennyiséget fogja megsemmisíteni.
>
>

A frissítés során a következő elemek nem támogatottak:

* Szerepkör nevének módosítása Távolítsa el, majd adja hozzá a szerepkört az új névvel.
* A frissítési tartományok számának módosítása.
* A helyi erőforrások méretének csökkentése.

Ha más frissítéseket készít a szolgáltatás definíciójában, például a helyi erőforrás méretének csökkentését, akkor helyette VIP-swap-frissítést kell végrehajtania. További információ: az [üzembe helyezés cseréje](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>A frissítés előrehaladása
Eldöntheti, hogy szeretné-e frissíteni a szolgáltatás összes szerepkörét vagy egyetlen szerepkört a szolgáltatásban. Mindkét esetben az összes frissítendő szerepkör és az első frissítési tartományhoz tartozó összes példány leállt, frissül, és online állapotba kerül. Ha ismét online vannak, a második frissítési tartományban található példányok leállnak, frissülnek és online állapotba kerülnek. Egy felhőalapú szolgáltatás legfeljebb egy verziófrissítést tud egyszerre aktívként. A frissítés mindig a Cloud Service legújabb verziójára történik.

A következő ábra azt szemlélteti, hogyan folytatódik a frissítés, ha a szolgáltatásban lévő összes szerepkört frissíti:

![Frissítési szolgáltatás](media/cloud-services-update-azure-service/IC345879.png "Frissítési szolgáltatás")

A következő diagram bemutatja, hogyan folytatódik a frissítés, ha csak egyetlen szerepkört frissít:

![Szerepkör frissítése](media/cloud-services-update-azure-service/IC345880.png "Szerepkör frissítése")  

Az automatikus frissítés során az Azure Fabric Controller rendszeresen kiértékeli a felhőalapú szolgáltatás állapotát, hogy meghatározza, mikor biztonságos a következő UD járni. Ezt az állapot-értékelést szerepköralapú alapon hajtjuk végre, és csak a legújabb verzióban található példányokat (például a már bejárt frissítési származó példányokat) veszi figyelembe. Ellenőrzi, hogy az egyes szerepkörökhöz tartozó szerepkör-példányok minimális száma megfelelő terminál-állapotot ért-e el.

### <a name="role-instance-start-timeout"></a>Szerepkör-példány indítási időtúllépése
A háló vezérlő 30 percet vár az egyes szerepkör-példányok elindítási állapotának elérésére. Ha az időtúllépés időtartama eltelik, a háló vezérlő továbbra is a következő szerepkör-példányra fog járni.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>A Cloud Service-frissítések során felmerülő adatmeghajtók hatása

Ha a szolgáltatást egyetlen példányról több példányra frissíti, akkor a szolgáltatás a frissítés végrehajtása során leáll, miközben az Azure frissíti a szolgáltatásokat. A szolgáltatói szerződés garantálja a szolgáltatás rendelkezésre állását csak az egynél több példánnyal üzembe helyezett szolgáltatásokra vonatkozik. Az alábbi lista azt ismerteti, hogyan érinti az egyes meghajtókon tárolt információkat az egyes Azure-szolgáltatások frissítési forgatókönyvei:

|Eset|C meghajtó|D meghajtó|E meghajtó|
|--------|-------|-------|-------|
|Virtuális gép újraindítása|Megőrzi|Megőrzi|Megőrzi|
|Portál újraindítása|Megőrzi|Megőrzi|Megsemmisül|
|Portál rendszerképének alaphelyzetbe állítása|Megőrzi|Megsemmisül|Megsemmisül|
|In-Place frissítés|Megőrzi|Megőrzi|Megsemmisül|
|Csomópont-áttelepítés|Megsemmisül|Megsemmisül|Megsemmisül|

Vegye figyelembe, hogy a fenti listában az E: meghajtó a szerepkör legfelső szintű meghajtóját jelöli, és nem lehet rögzítetten kódolva. Ehelyett használja a **% RoleRoot%** környezeti változót a meghajtó megjelenítéséhez.

Az Egypéldányos szolgáltatás verziófrissítésének leállásának minimalizálásához helyezzen üzembe egy új, többpéldányos szolgáltatást az átmeneti kiszolgálón, és hajtson végre VIP-swap-cserét.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Frissítés visszaállítása
Az Azure rugalmasságot biztosít a szolgáltatások a frissítések során történő kezelésében azáltal, hogy további műveleteket kezdeményez a szolgáltatáson, miután az Azure Fabric-vezérlő elfogadja a kezdeti frissítési kérést. A visszaállítás csak akkor hajtható végre, ha egy frissítés (konfigurációs változás) vagy a frissítés **folyamatban** állapotban van a központi telepítésben. Egy frissítés vagy frissítés akkor tekinthető folyamatban lévőnek, ha a szolgáltatásnak legalább egy példánya még nem lett frissítve az új verzióra. Annak ellenőrzéséhez, hogy engedélyezett-e a visszaállítás, ellenőrizze a RollbackAllowed jelző értékét, amelyet a [telepítés beolvasása](/previous-versions/azure/reference/ee460804(v=azure.100)) és a [Cloud Service-tulajdonságok beolvasása](/previous-versions/azure/reference/ee460806(v=azure.100)) művelet ad vissza, igaz értékre van állítva.

> [!NOTE]
> A visszaállítást csak helyben végzett frissítéssel vagy frissítéssel kell meghívni, mert a VIP swap **-** frissítések a szolgáltatás egy teljes futó példányának cseréjét igénylik egy másikkal.
>
>

A folyamatban lévő frissítés visszaállítása a következő hatásokkal jár az üzemelő példányra:

* Az új verzióra még nem frissített vagy frissített összes szerepkör-példány frissítése vagy frissítése nem történik meg, mert ezek a példányok már futtatják a szolgáltatás cél verzióját.
* Minden olyan szerepkör-példány, amely már frissítve lett vagy frissítve lett a szervizcsomag ( \* . cspkg) vagy a szolgáltatás konfigurációs ( \* . cscfg) fájljának új verziójára (vagy mindkét fájlra), a rendszer visszaállította a fájlok verziófrissítés előtti verzióját.

Ezt a funkciót a következő funkciók biztosítják:

* A [visszaállítás frissítési vagy frissítési](/previous-versions/azure/reference/hh403977(v=azure.100)) művelete, amely meghívható egy konfigurációs frissítésre (az [üzembe helyezési konfiguráció](/previous-versions/azure/reference/ee460809(v=azure.100))meghívásával) vagy a frissítésre (amelyet a [frissítési telepítés](/previous-versions/azure/reference/ee460793(v=azure.100))meghívásával indít el), feltéve, hogy legalább egy példány van a szolgáltatásban, amely még nem lett frissítve az új verzióra.
* A zárolt elem és a RollbackAllowed elem, amelyet a rendszer az [üzembe helyezés](/previous-versions/azure/reference/ee460804(v=azure.100)) és a [Cloud Service tulajdonságok beolvasása](/previous-versions/azure/reference/ee460806(v=azure.100)) művelet válasz törzsének részeként ad vissza:

  1. A zárolt elem lehetővé teszi annak észlelését, hogy egy adott központi telepítésnél hogyan hívható meg egy mutációs művelet.
  2. A RollbackAllowed elem lehetővé teszi annak észlelését, hogy a rendszer mikor hívja meg a [visszaállítási frissítést vagy a frissítési](/previous-versions/azure/reference/hh403977(v=azure.100)) műveletet egy adott központi telepítésben.

  A visszaállítás végrehajtásához nem kell ellenőriznie a zárolt és a RollbackAllowed elemeket is. Ez elegendő annak megerősítéséhez, hogy a RollbackAllowed értéke TRUE (igaz). Ezeket az elemeket csak akkor adja vissza a rendszer, ha ezeket a metódusokat a kérelem fejlécének "x-MS-Version: 2011-10-01" vagy egy újabb verzióra való meghívásával hívja meg. További információ a verziószámozási fejlécekről: a [Service Management verziószámozása](/previous-versions/azure/gg592580(v=azure.100)).

Vannak olyan helyzetek, amikor egy frissítés vagy frissítés visszaállítása nem támogatott, ezek a következők:

* Helyi erőforrások csökkentése – ha a frissítés növeli a szerepkör helyi erőforrásait, az Azure platform nem teszi lehetővé a visszaállítást.
* Kvóta korlátai – ha a frissítés leskálázási művelet volt, előfordulhat, hogy a visszaállítási művelet elvégzéséhez már nincs elegendő számítási kvóta. Minden Azure-előfizetéshez hozzá van rendelve egy kvóta, amely meghatározza az adott előfizetéshez tartozó összes üzemeltetett szolgáltatás által felhasználható magok maximális számát. Ha egy adott frissítés visszaállítását végrehajtja, akkor az előfizetést kvóta fölé helyezi, és a visszaállítás nem lesz engedélyezve.
* Versenyhelyzet – ha a kezdeti frissítés befejeződött, a visszaállítás nem lehetséges.

Egy példa arra, hogy ha a frissítés visszaállítását manuális módban használja, akkor hasznos lehet, [Ha az Azure](/previous-versions/azure/reference/ee460793(v=azure.100)) által üzemeltetett szolgáltatásra történő helyszíni frissítés sebességét szeretné szabályozni.

A frissítés bevezetése során a [frissítés üzembe helyezését](/previous-versions/azure/reference/ee460793(v=azure.100)) manuális módban hívja meg, és kezdje el a frissítési tartományok elérését. Ha egy bizonyos ponton a frissítés figyelése után megfigyelheti, hogy az első frissítési tartományokban egyes szerepkör-példányok nem válaszolnak, meghívhatja a [visszaállítás frissítését vagy frissítési](/previous-versions/azure/reference/hh403977(v=azure.100)) műveletét a központi telepítésben, ami nem érinti azokat a példányokat, amelyek még nem lettek frissítve, és visszaállítják azokat a példányokat, amelyek az előző szervizcsomagra és konfigurációra frissültek.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Több mutációs művelet kezdeményezése folyamatban lévő központi telepítésre
Bizonyos esetekben előfordulhat, hogy több egyidejű mutációs műveletet szeretne kezdeményezni egy folyamatban lévő telepítésben. Előfordulhat például, hogy egy szolgáltatás frissítését hajtja végre, és a frissítés folyamatban van a szolgáltatásban, ezért módosítani kívánja a frissítést, például a frissítés visszadobásához, egy másik frissítés alkalmazásához, vagy akár az üzemelő példány törléséhez. Ha a szolgáltatás frissítése olyan hibás kódot tartalmaz, amely akkor fordulhat elő, amikor a frissítés egy frissített szerepkör-példányt használ, a rendszer ismételten összeomlást okoz. Ebben az esetben az Azure Fabric-vezérlő nem fog tudni előrehaladást végezni a frissítés alkalmazása során, mert a frissített tartományban a nem megfelelő számú példány kifogástalan állapotú. Ezt az állapotot *beragadt központi telepítésnek* nevezzük. A telepítés visszavonásához visszaállíthatja a frissítést, vagy alkalmazhat egy friss frissítést az egyik sikertelen művelet felett.

Miután az Azure Fabric-vezérlő megkapta a szolgáltatás frissítésére vagy frissítésére vonatkozó kezdeti kérelmet, megkezdheti a további mutációs műveleteket. Ekkor nem kell megvárnia a kezdeti művelet befejeződését, mielőtt elkezdené egy másik mutációs műveletet.

Egy második frissítési művelet kezdeményezése, miközben az első frissítés folyamatban van, hasonló lesz a visszaállítási művelethez. Ha a második frissítés automatikus módban van, az első frissítési tartomány azonnal frissülni fog, valószínűleg a több frissítési tartományból származó példányok is, amelyek offline állapotban vannak az adott időpontban.

A mutációs műveletek a következők: a [központi telepítés konfigurációjának módosítása](/previous-versions/azure/reference/ee460809(v=azure.100)) [, a telepítés frissítése](/previous-versions/azure/reference/ee460793(v=azure.100)), a [központi telepítés állapotának frissítése](/previous-versions/azure/reference/ee460808(v=azure.100)), a [központi telepítés törlése](/previous-versions/azure/reference/ee460815(v=azure.100)), valamint a [frissítés vagy](/previous-versions/azure/reference/hh403977(v=azure.100))a frissítés visszaállítása.

Két művelet, [telepítés beolvasása](/previous-versions/azure/reference/ee460804(v=azure.100)) és [Cloud Service-tulajdonságok beolvasása](/previous-versions/azure/reference/ee460806(v=azure.100)): a zárolt jelzőt adja vissza, amely megvizsgálható annak megállapítására, hogy egy adott központi telepítésnél lehet-e egy mutációs műveletet meghívni.

A zárolt jelzőt visszaadó metódusok verziójának meghívásához a kérelem fejlécét az "x-MS-Version: 2011-10-01" vagy újabb értékre kell beállítani. További információ a verziószámozási fejlécekről: a [Service Management verziószámozása](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Szerepkörök eloszlása frissítési tartományok között
Az Azure egyenletesen osztja el a szerepkörök példányait egy meghatározott számú frissítési tartományon belül, amely a szolgáltatás definíciós (. csdef) fájljának részeként is konfigurálható. A frissítési tartományok maximális száma 20, az alapértelmezett érték pedig 5. A szolgáltatás definíciós fájljának módosításával kapcsolatos további információkért lásd: [Azure Service definition Schema (. Csdef fájl)](cloud-services-model-and-package.md#csdef).

Ha például a szerepkör tíz példányban szerepel, alapértelmezés szerint minden frissítési tartomány két példányt tartalmaz. Ha a szerepkör 14 példánnyal rendelkezik, akkor a frissítési tartományok négy példánya három példányt tartalmaz, az ötödik tartomány pedig kettőt tartalmaz.

A frissítési tartományok nulla alapú indextel vannak azonosítva: az első frissítési tartomány 0 AZONOSÍTÓval rendelkezik, a második frissítési tartomány azonosítója pedig 1, és így tovább.

Az alábbi ábra azt szemlélteti, hogy a két szerepkört tartalmazó szolgáltatások mikor vannak elosztva, ha a szolgáltatás két frissítési tartományt határoz meg. A szolgáltatás a webes szerepkör és a feldolgozói szerepkör kilenc példányának nyolc példányát futtatja.

![Frissítési tartományok eloszlása](media/cloud-services-update-azure-service/IC345533.png "Frissítési tartományok eloszlása")

> [!NOTE]
> Vegye figyelembe, hogy az Azure azt szabályozza, hogyan vannak lefoglalva a példányok a frissítési tartományok között. Nem lehet megadni, hogy mely példányok vannak lefoglalva a tartományhoz.
>
>

## <a name="next-steps"></a>Következő lépések
[A Cloud Services felügyelete](cloud-services-how-to-manage-portal.md)  
[A Cloud Services figyelése](cloud-services-how-to-monitor.md)  
[A Cloud Services Konfigurálása](cloud-services-how-to-configure-portal.md)