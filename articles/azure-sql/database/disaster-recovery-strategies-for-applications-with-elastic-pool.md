---
title: Tervezési vész-helyreállítási megoldások
description: Megtudhatja, hogyan tervezheti meg a felhőalapú megoldást a vész-helyreállításhoz a megfelelő feladatátvételi minta kiválasztásával.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb-1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 0463d11466859c0f30901a0afd960bdc7b2599a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91357784"
---
# <a name="disaster-recovery-strategies-for-applications-using-azure-sql-database-elastic-pools"></a>Vészhelyzeti helyreállítási stratégiák Azure SQL Database rugalmas készleteket használó alkalmazásokhoz
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Azure SQL Database számos képességet biztosít az alkalmazás üzleti folytonosságának biztosításához, amikor katasztrofális incidensek történnek. A [rugalmas készletek](elastic-pool-overview.md) és az önálló adatbázisok ugyanolyan típusú vész-helyreállítási (Dr) képességeket támogatnak. Ez a cikk több, a rugalmas készletekre vonatkozó DR-stratégiát ismertet, amelyek kihasználják ezeket a Azure SQL Database üzletmenet-folytonossági funkciókat.

Ez a cikk a következő Canonical SaaS ISV-alkalmazási mintát használja:

A modern felhőalapú webalkalmazások egy adatbázist biztosítanak az egyes végfelhasználók számára. Az ISV számos ügyfelet tartalmaz, ezért számos adatbázist használ, más néven bérlői adatbázisokat. Mivel a bérlői adatbázisok jellemzően előre nem látható tevékenységi mintákkal rendelkeznek, az ISV rugalmas készletet használ, hogy az adatbázis a hosszabb időtartamon keresztül kiszámítható legyen. A rugalmas készlet leegyszerűsíti a teljesítmény-kezelést, amikor a felhasználói tevékenység tüskéket mutat. A bérlői adatbázisokon kívül az alkalmazás több adatbázist is használ a felhasználói profilok, a biztonság, a használati minták összegyűjtése stb. alapján. Az egyes bérlők rendelkezésre állása nem befolyásolja az alkalmazás teljes rendelkezésre állását. Azonban a felügyeleti adatbázisok rendelkezésre állása és teljesítménye kritikus fontosságú az alkalmazás függvényében, és ha a felügyeleti adatbázisok offline állapotban vannak, a teljes alkalmazás offline állapotban van.

Ez a cikk ismerteti a DR-stratégiákat, amelyek számos különböző forgatókönyvet érintenek a költséges és a szigorú rendelkezésre állást igénylő indítási alkalmazások esetében.

> [!NOTE]
> Ha prémium szintű vagy üzletileg kritikus-adatbázisokat és rugalmas készleteket használ, rugalmasan végezheti el a regionális kimaradásokat a zónák redundáns üzembe helyezési konfigurációjához való átalakításával. Lásd: [zóna – redundáns adatbázisok](high-availability-sla.md).

## <a name="scenario-1-cost-sensitive-startup"></a>1. forgatókönyv Cost szenzitív indítás

Induló vállalkozás vagyok, és rendkívül költséges vagyok.  Szeretném leegyszerűsíteni az alkalmazás üzembe helyezését és felügyeletét, és korlátozott SLA-t biztosíthatok az egyes ügyfelek számára. De szeretném biztosítani, hogy az alkalmazás egésze soha ne legyen offline állapotban.

Az egyszerűség követelmény kielégítése érdekében telepítse az összes bérlői adatbázist egy rugalmas készletbe az Ön által választott Azure-régióban, és telepítse a felügyeleti adatbázisokat földrajzilag replikált önálló adatbázisokként. A bérlők vész-helyreállításához használja a Geo-visszaállítást, amely további díjak nélkül elérhető. A felügyeleti adatbázisok rendelkezésre állásának biztosítása érdekében geo-replikálja őket egy másik régióba egy automatikus feladatátvételi csoport használatával (1. lépés). Ebben a forgatókönyvben a vész-helyreállítási konfiguráció folyamatos költsége megegyezik a másodlagos adatbázisok teljes összegével. Ez a konfiguráció a következő diagramon látható.

![1. ábra](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Ha áramkimaradás fordul elő az elsődleges régióban, akkor az alkalmazás online állapotba állításához szükséges helyreállítási lépéseket a következő ábra szemlélteti.

* A feladatátvételi csoport kezdeményezi a felügyeleti adatbázis automatikus feladatátvételét a DR régióba. Az alkalmazás automatikusan újracsatlakozik az új elsődleges szolgáltatáshoz, és az összes új fiókhoz és bérlői adatbázishoz létre lett hozva a DR régióban. A meglévő ügyfelek átmenetileg nem érhetők el.
* Hozza létre a rugalmas készletet ugyanazzal a konfigurációval, mint az eredeti készlet (2).
* A bérlői adatbázisok másolatainak létrehozásához használja a Geo-visszaállítást (3). Megtekintheti az egyes visszaállításokat a végfelhasználói kapcsolatok, vagy más, alkalmazásspecifikus prioritási sémákat is használhat.

Ezen a ponton az alkalmazás ismét online állapotba kerül a DR régióban, de egyes ügyfelek az adatokhoz való hozzáférés során késésben vannak.

![2. ábra](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Ha a leállás ideiglenes volt, lehetséges, hogy az elsődleges régiót az Azure állítja helyre, mielőtt az összes adatbázis-visszaállítás befejeződik a DR régióban. Ebben az esetben az alkalmazást az elsődleges régióba helyezi át. A folyamat a következő ábrán látható lépéseket mutatja be.

* Az összes függőben lévő geo-visszaállítási kérelem megszakítása.
* Hajtsa végre a felügyeleti adatbázisok feladatátvételét az elsődleges régióba (5). A régió helyreállítása után a régi primerek automatikusan formátumú másodlagos zónák válnak. Most újra átváltják a szerepköröket.
* Módosítsa az alkalmazás kapcsolódási karakterláncát úgy, hogy az az elsődleges régióra mutasson vissza. Most minden új fiók és bérlői adatbázis létrejön az elsődleges régióban. Néhány meglévő ügyfél átmenetileg nem érhető el.
* A DR-készletben lévő összes adatbázis írásvédett értékre állításával biztosíthatja, hogy nem módosíthatók a DR régióban (6).
* A DR készlet minden olyan adatbázisa esetében, amely a helyreállítás óta megváltozott, nevezze át vagy törölje a megfelelő adatbázisokat az elsődleges készletben (7).
* Másolja a frissített adatbázisokat a DR-készletből az elsődleges készletbe (8).
* A DR-készlet törlése (9)

Ezen a ponton az alkalmazás online állapotban van az elsődleges régióban az elsődleges készletben elérhető összes bérlői adatbázissal.

![3. ábra](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

### <a name="benefit"></a>Előny

Ennek a stratégiának a legfőbb előnye, hogy az adatcsomag-redundancia terén alacsony a folyamatos díj. A Azure SQL Database automatikusan biztonsági másolatot készít az alkalmazások újraírása nélküli adatbázisokról, felár nélkül. A költségek csak a rugalmas adatbázisok visszaállításakor merülnek fel. 

### <a name="trade-off"></a>Kompromisszum

A kompromisszum az, hogy az összes bérlői adatbázis teljes helyreállítása jelentős időt vesz igénybe. Az idő hossza a DR régióban kezdeményezett visszaállítások számától és a bérlői adatbázisok teljes méretétől függ. A meglévő ügyfelek adatbázisaira gyakorolt általános hatás csökkentése érdekében még akkor is, ha a bérlők számára rangsorolja a többi bérlőt, Ön pedig az összes többi olyan visszaállítással verseng, amelyek ugyanabban a régióban lettek elindítva, mint a szolgáltatási egyeztetések és a szabályozások. Emellett a bérlői adatbázisok helyreállítása nem indítható el, amíg a DR régió új rugalmas készlete nem jön létre.

## <a name="scenario-2-mature-application-with-tiered-service"></a>2. forgatókönyv Érett alkalmazás többplatformos szolgáltatással

Egy kiforrott SaaS-alkalmazást használok, amely többplatformos szolgáltatást kínál, valamint különböző SLA-kat a próbaverziós ügyfelekhez és az ügyfelek fizetéséhez. A próbaverziós ügyfelek esetében a lehető legnagyobb mértékben csökkenteni kell a költségeket. A próbaverziós ügyfelek leállást vehetnek igénybe, de szeretnék csökkenteni a valószínűségét. A fizető ügyfelek esetében minden állásidő egy repülési kockázat. Ezért szeretném meggyőződni arról, hogy az ügyfelek mindig hozzáférhetnek az adatszolgáltatáshoz.

A forgatókönyv támogatásához válassza szét a próbaverziós bérlőket a fizetős bérlők számára, ha különálló rugalmas készletbe helyezi őket. A próbaverziós ügyfeleknél alacsonyabb eDTU vagy virtuális mag, illetve alacsonyabb SLA-ra van szükség. A fizetős ügyfelek egy-egy magasabb eDTU vagy virtuális mag rendelkező készletben vannak, és magasabb SLA-t biztosítanak. A legalacsonyabb helyreállítási idő biztosításához a fizetős ügyfelek bérlői adatbázisai földrajzilag replikálódnak. Ez a konfiguráció a következő diagramon látható.

![A diagram egy elsődleges régiót és egy D R-régiót mutat be, amely geo-replikációt alkalmaz a felügyeleti adatbázis és a fizetős ügyfelek elsődleges készlete és a másodlagos készlet között, és nem replikálja a próbaverziós ügyfelek készletét.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Ahogy az első forgatókönyvben is, a felügyeleti adatbázisok meglehetősen aktívak, így egyetlen földrajzilag replikált adatbázist használ hozzá (1). Ez biztosítja az új ügyfél-előfizetések, a profilok frissítései és egyéb felügyeleti műveletek kiszámítható teljesítményét. Az a régió, amelyben a felügyeleti adatbázisok elsődlegesek, a DR régió, ahol a felügyeleti adatbázisok formátumú másodlagos zónák található, az elsődleges régió és a régió.

A fizetős ügyfelek bérlői adatbázisai aktív adatbázisokkal rendelkeznek az elsődleges régióban kiépített "fizetős" készletben. Hozzon létre egy másodlagos készletet ugyanazzal a névvel a DR régióban. Minden bérlő földrajzilag replikálódik a másodlagos készletre (2). Ez lehetővé teszi az összes bérlői adatbázis gyors helyreállítását a feladatátvételsel.

Ha az elsődleges régióban áramkimaradás történik, az alkalmazás online állapotba helyezésének helyreállítási lépései a következő ábrán láthatók:

![A diagram az elsődleges régió leállását jeleníti meg, a felügyeleti adatbázis feladatátvételével, a fizetős ügyfél másodlagos készletével, valamint a próbaverziós ügyfelek létrehozásával és visszaállításával.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* A felügyeleti adatbázisok azonnali feladatátvétele a DR régióba (3).
* Módosítsa az alkalmazás kapcsolódási karakterláncát úgy, hogy az a DR régióra mutasson. Most minden új fiók és bérlői adatbázis létre lett hozva a DR régióban. A meglévő próbaverziós ügyfelek átmenetileg nem érhetők el.
* A fizetős bérlő adatbázisainak feladatátvétele a DR régióban lévő készletbe, hogy azonnal visszaállítsa a rendelkezésre állást (4). Mivel a feladatátvétel a metaadatok gyors megváltozása, érdemes lehet olyan optimalizációt is figyelembe venni, amelyben a végfelhasználói kapcsolatok igény szerint aktiválják az egyes feladatátvételeket.
* Ha a másodlagos készlet eDTU mérete vagy virtuális mag értéke alacsonyabb volt az elsődlegesnél, mert a másodlagos adatbázisok csak azt a kapacitást igénylik, hogy a változási naplók feldolgozzák a formátumú másodlagos zónák, azonnal növelje a készlet kapacitását, hogy az összes bérlő teljes munkaterhelését (5) befogadja.
* Hozza létre az új rugalmas készletet ugyanazzal a névvel és ugyanazzal a konfigurációval a DR régióban a próbaverziós ügyfelek adatbázisaiban (6).
* A próbaverziós ügyfelek készletének létrehozása után a Geo-visszaállítás használatával állítsa vissza az egyes próbaverziós bérlői adatbázisokat az új készletbe (7). Gondolja át, hogy a végfelhasználói kapcsolatok elindítják-e az egyes visszaállításokat, vagy más alkalmazás-specifikus prioritási sémát használnak.

Ezen a ponton az alkalmazás ismét online állapotba kerül a DR régióban. Az összes fizető ügyfél hozzáférhet az adatokhoz, miközben a próbaverziós ügyfelek az adatokhoz való hozzáféréskor késleltetik a késést.

Ha az Azure-t a DR régióban visszaállította az alkalmazás visszaállítása *után* , akkor folytathatja az alkalmazás futtatását az adott régióban, vagy dönthet úgy is, hogy visszaadja az elsődleges régiónak. Ha az elsődleges régiót a feladatátvételi folyamat befejezése *előtt* állítja helyre, érdemes lehet azonnal visszaadnia a hibát. A feladat-visszavétel a következő ábrán látható lépéseket mutatja be:

![A diagram az elsődleges régió visszaállítása utáni megvalósítási feladat-visszavétel lépéseit mutatja be.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Az összes függőben lévő geo-visszaállítási kérelem megszakítása.
* A felügyeleti adatbázisok (8) feladatátvétele. A régió helyreállítása után a régi elsődleges automatikusan a másodlagos lesz. Most már az elsődleges lesz.  
* A fizetős bérlői adatbázisok (9) feladatátvétele. Hasonlóképpen, a régió helyreállítása után a régi elsõdleges automatikusan a formátumú másodlagos zónák válik. Most már az elsõdlegesek lesznek.
* Állítsa be a DR régióban megváltoztatott visszaállított próbaverziós adatbázisokat írásvédett értékre (10).
* A próbaverziós ügyfelek DR készletének minden olyan adatbázisa esetében, amely a helyreállítás óta megváltozott, nevezze át vagy törölje a megfelelő adatbázist a próbaverziós ügyfelek elsődleges készletében (11).
* Másolja a frissített adatbázisokat a DR-készletből az elsődleges készletbe (12).
* Törölje a DR-készletet (13).

> [!NOTE]
> A feladatátvételi művelet aszinkron módon működik. A helyreállítási idő minimálisra csökkentése érdekében fontos, hogy a bérlői adatbázisok feladatátvételi parancsát legalább 20 adatbázist tartalmazó kötegekben hajtsa végre.

### <a name="benefit"></a>Előny

Ennek a stratégiának a legfőbb előnye, hogy a fizetős ügyfelek számára a legmagasabb SLA-t biztosítja. Emellett azt is garantálja, hogy az új próbaverziók feloldása azonnal megtörténjen, amint létrejön a próbaverzió DR-készlete. 

### <a name="trade-off"></a>Kompromisszum

A kikapcsolás az, hogy ez a beállítás növeli a bérlői adatbázisok teljes díját a másodlagos DR-készletnek a fizetős ügyfelek esetében felszámolt költsége alapján. Emellett, ha a másodlagos készlet mérete eltérő, a fizető ügyfelek a feladatátvételt követően alacsonyabb teljesítményt tapasztalhatnak, amíg a DR régióban a készlet frissítése be nem fejeződik.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>3. forgatókönyv Földrajzilag elosztott alkalmazás többplatformos szolgáltatással

Van egy kiforrott SaaS-alkalmazásom, amely többplatformos szolgáltatást kínál. Nagyon agresszív SLA-t szeretnék nyújtani fizetős ügyfeleinknek, és csökkenthetik a kiesések kockázatát, mivel a leállások miatt még a rövid megszakítás is okozhat vásárlói elégedettséget. Fontos, hogy a fizető ügyfelek mindig hozzáférhessenek az adatkezeléshez. A próbaverziók ingyenesek, és a próbaidőszak alatt nem biztosítunk SLA-t.

A forgatókönyv támogatásához használjon három különálló rugalmas készletet. Hozzon létre két egyenlő méretű készletet a magas Edtu vagy a virtuális mag adatbázissal két különböző régióban, hogy tartalmazza a fizetős ügyfelek bérlői adatbázisait. A próbaverziós bérlőket tartalmazó harmadik készlet adatbázisa alacsonyabb Edtu vagy virtuális mag, és a két régió egyikében kiépíthető.

A leállások legalacsonyabb helyreállítási idejének garantálása érdekében a fizetős ügyfelek bérlői adatbázisai a két régió elsődleges adatbázisainak 50%-ában vannak replikálva. Hasonlóképpen, minden régió a másodlagos adatbázisok 50%-a. Így ha egy régió offline állapotban van, a fizetős ügyfelek adatbázisainak csak 50%-a érintett, és a feladatátvételre van hatással. A többi adatbázis érintetlen marad. Ezt a konfigurációt az alábbi ábra szemlélteti:

![A diagram az A és a B régió nevű elsődleges régiót jeleníti meg, amely geo-replikációt alkalmaz a felügyeleti adatbázis és a fizetős ügyfelek elsődleges készlete és másodlagos készlete között, és nem replikálja a próbaverziós ügyfelek készletét.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Ahogy az előző forgatókönyvek esetében is, a felügyeleti adatbázisok meglehetősen aktívak, ezért konfigurálja őket egyetlen földrajzilag replikált adatbázisként (1). Ez biztosítja az új ügyfél-előfizetések, a profilok frissítései és az egyéb felügyeleti műveletek kiszámítható teljesítményét. Az A régió a felügyeleti adatbázisok elsődleges régiója, a B régió pedig a felügyeleti adatbázisok helyreállításához használatos.

A fizetős ügyfelek bérlői adatbázisai is földrajzilag replikálódnak, de elsődleges és formátumú másodlagos zónák az A és B régió (2) régiója között oszlik meg. Ily módon a leállás által érintett elsődleges bérlői adatbázisok feladatátvételt végezhetnek a másik régióba, és elérhetővé válnak. A bérlői adatbázisok másik felét egyáltalán nem érinti.

A következő diagram azt mutatja be, hogy milyen helyreállítási lépéseket kell végrehajtania, ha az A régióban áramkimaradás történik.

![A diagram az elsődleges régió meghibásodását mutatja, a felügyeleti adatbázis feladatátvételével, a fizetős ügyfél másodlagos készletével, valamint a próbaverziós ügyfelek számára a B régióba történő létrehozásával és visszaállításával.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* A felügyeleti adatbázisok azonnali feladatátvétele a B régióra (3).
* Módosítsa az alkalmazás kapcsolódási karakterláncát úgy, hogy az a B régió felügyeleti adatbázisaira mutasson. módosítsa a felügyeleti adatbázisokat, hogy az új fiókok és bérlői adatbázisok a B régióban jöjjenek létre, és a meglévő bérlői adatbázisok is megtalálhatók. A meglévő próbaverziós ügyfelek átmenetileg nem érhetők el.
* A fizetős bérlő adatbázisainak átadása a B régióban lévő 2. készletre, hogy azonnal visszaállítsa a rendelkezésre állást (4). Mivel a feladatátvétel a metaadatok gyors megváltozása, érdemes lehet olyan optimalizálást figyelembe venni, amelyben a végfelhasználói kapcsolatok igény szerint aktiválják az egyes feladatátvételeket.
* Mivel a jelenleg a 2. készlet csak elsődleges adatbázisokat tartalmaz, a készlet teljes terhelése növekszik, és azonnal növelheti a eDTU méretét (5) vagy a virtuális mag számát.
* Hozza létre az új rugalmas készletet ugyanazzal a névvel és ugyanazzal a konfigurációval a B régióban a próbaverziós ügyfelek adatbázisaihoz (6).
* A készlet létrehozása után a Geo-visszaállítás használatával állítsa vissza az egyes próbaverziós bérlői adatbázist a készletbe (7). Megtekintheti az egyes visszaállításokat a végfelhasználói kapcsolatok, vagy más, alkalmazásspecifikus prioritási sémákat is használhat.

> [!NOTE]
> A feladatátvételi művelet aszinkron módon működik. A helyreállítási idő minimálisra csökkentése érdekében fontos, hogy a bérlői adatbázisok feladatátvételi parancsát legalább 20 adatbázist tartalmazó kötegekben hajtsa végre.

Ezen a ponton az alkalmazás ismét online állapotba kerül a B régióban. Az összes fizető ügyfél hozzáférhet az adatokhoz, miközben a próbaverziós ügyfelek az adatokhoz való hozzáféréskor késleltetik a késést.

Az A régió helyreállítása esetén el kell döntenie, hogy a B régiót szeretné-e használni a próbaverziós ügyfelek vagy a feladat-visszavétel számára az A régióban lévő próbaverziós ügyfelek készletének használatával. Az egyik feltétel lehet a helyreállítás óta módosított próbaverziós bérlői adatbázisok%-a. A döntéstől függetlenül át kell osztania a fizetős bérlőket két készlet között. a következő diagram azt mutatja be, hogy a próbaverziós bérlő adatbázisai hogyan működnek az A régióba.  

![A diagram az A régió visszaállítása utáni megvalósítási feladat-visszavételi lépéseket mutatja be.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Törölje az összes kihelyezett geo-visszaállítási kérést a DR-készlet próbaverziója számára.
* A felügyeleti adatbázis feladatátvétele (8). A régió helyreállítása után a régi elsődleges automatikusan a másodlagos lett. Most már az elsődleges lesz.  
* Válassza ki, hogy mely fizetős bérlői adatbázisok teljesítik az 1. készletet, és kezdeményezzen feladatátvételt a formátumú másodlagos zónák (9). A régió helyreállítása után az 1. készletben lévő összes adatbázis automatikusan formátumú másodlagos zónák vált. Mostantól 50%-kal újra elérhetővé válik.
* Csökkentse a 2. készlet méretét az eredeti eDTU (10) vagy a virtuális mag számával.
* Állítsa be az összes visszaállított próbaverziós adatbázist a B régióban a csak olvasható (11) értékre.
* A próbaverziós DR készlet minden olyan adatbázisa esetében, amely megváltozott a helyreállítás óta, nevezze át vagy törölje a megfelelő adatbázist a próbaverzió elsődleges készletében (12).
* Másolja a frissített adatbázisokat a DR-készletből az elsődleges készletbe (13).
* Törölje a DR-készletet (14).

### <a name="benefit"></a>Előny

A stratégia főbb előnyei a következők:

* Támogatja a fizetős ügyfelek számára legagresszívebb SLA-t, mivel biztosítja, hogy a leállás a bérlői adatbázisok több mint 50%-át nem befolyásolhatja.
* Garantálja, hogy az új próbaverziók feloldják a zárolást, amint a rendszer a nyomvonal DR-készletet a helyreállítás során hozza létre.
* Lehetővé teszi, hogy a készlet kapacitása hatékonyabban használhassa a másodlagos adatbázisok 50%-át az 1. készletben, a 2. készlet pedig garantáltan kevésbé legyen aktív, mint az elsődleges adatbázisok.

### <a name="trade-offs"></a>Kompromisszumok

A fő kompromisszumok a következők:

* A kezelési adatbázisokkal szembeni szifilisz-műveletek alacsonyabb késéssel rendelkeznek az A régióhoz csatlakozó végfelhasználók számára, mint a B régióhoz csatlakoztatott végfelhasználók számára, mivel azok a felügyeleti adatbázisok elsődleges részén futnak.
* A felügyeleti adatbázis összetettebb kialakítását igényli. Az egyes bérlői rekordok például egy olyan hely címkével rendelkeznek, amelyet a feladatátvétel és a feladat-visszavétel során módosítani kell.  
* A fizető ügyfelek a szokásosnál alacsonyabb teljesítményt tapasztalhatnak, amíg a B régióban a készlet frissítése be nem fejeződik.

## <a name="summary"></a>Összefoglalás

Ez a cikk a SaaS ISV több-bérlős alkalmazás által használt adatbázis-rétegre vonatkozó vész-helyreállítási stratégiákra összpontosít. Az Ön által választott stratégia az alkalmazás igényeinek, például az üzleti modellnek, az ügyfeleknek felkínált SLA-nak, a költségvetési korlátozásnak stb. Mindegyik ismertetett stratégia ismerteti az előnyöket és a kompromisszumot, így tájékozott döntést hozhat. Emellett az adott alkalmazás valószínűleg más Azure-összetevőket is tartalmaz. Ezért tekintse át az üzletmenet-folytonossági útmutatót, és hangolja össze az adatbázis-rétegek helyreállítását. Az adatbázis-alkalmazások Azure-ban történő helyreállításával kapcsolatos további tudnivalókért tekintse meg a [felhőalapú megoldások tervezése a vész-helyreállításhoz](designing-cloud-solutions-for-disaster-recovery.md)című témakört.  

## <a name="next-steps"></a>Következő lépések

* Az automatikus biztonsági mentések Azure SQL Databaseáról a [Azure SQL Database automatizált biztonsági mentések](automated-backups-overview.md)című témakörben olvashat bővebben.
* Az üzletmenet folytonosságának áttekintése és forgatókönyvei: az [üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md).
* Ha többet szeretne megtudni a helyreállítás automatizált biztonsági mentéséről, olvassa el [az adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági másolatokból](recovery-using-backups.md)című témakört.
* A gyorsabb helyreállítási lehetőségek megismeréséhez lásd: [aktív földrajzi replikálás](active-geo-replication-overview.md) és [automatikus feladatátvételi csoportok](auto-failover-group-overview.md).
* Az automatikus biztonsági mentések archiválásra történő használatáról az [adatbázis másolása](database-copy.md)című témakörben olvashat bővebben.
