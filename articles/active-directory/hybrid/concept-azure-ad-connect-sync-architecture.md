---
title: 'Azure AD Connect Sync: az architektúra megismerése – Azure'
description: Ez a témakör a Azure AD Connect Sync architektúráját ismerteti, és ismerteti a használt feltételeket.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27055ce84bbb073045b69b942fd13f4fde4e3b3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90563862"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure AD Connect Sync: az architektúra megismerése
Ez a témakör a Azure AD Connect Sync alapszintű architektúráját ismerteti. Számos szempontból hasonló az előzőekhez MIIS 2003, ILM 2007 és FIM 2010. Azure AD Connect szinkronizálás a technológiák fejlődése. Ha ismeri a korábbi technológiák bármelyikét, a jelen témakör tartalma is ismerős lesz. Ha még nem ismeri a szinkronizálást, akkor ez a témakör Önnek szól. Azonban nem kell tudnia, hogy a témakör részletei sikeresek legyenek ahhoz, hogy a testreszabások Azure AD Connect szinkronizálást (ez a témakör szinkronizálási motornak hívják).

## <a name="architecture"></a>Architektúra
A Szinkronizáló motor a több csatlakoztatott adatforrásban tárolt objektumok integrált nézetét hozza létre, és az adatforrásokban kezeli az identitási adatokat. Ezt az integrált nézetet a csatlakoztatott adatforrásokból beolvasott azonosító adatok és az adatok feldolgozását meghatározó szabályok határozzák meg.

### <a name="connected-data-sources-and-connectors"></a>Csatlakoztatott adatforrások és összekötők
A Szinkronizáló motor különböző adattárakból dolgozza fel az identitási adatokat, például Active Directory vagy egy SQL Server adatbázisból. Minden adattárház, amely adatbázis-szerű formátumban rendezi az adataikat, és szabványos adatelérési módszereket biztosít, a szinkronizálási motor számára lehetséges adatforrásra jelölt. A szinkronizálási motor által szinkronizált adattárházak neve **csatlakoztatott adatforrások** vagy **csatlakoztatott könyvtárak** (CD).

A szinkronizálási motor egy **összekötő** nevű modulon belül beágyazza az interakciót egy csatlakoztatott adatforrással. A csatlakoztatott adatforrások mindegyik típusa egy adott összekötővel rendelkezik. Az összekötő lefordít egy szükséges műveletet a csatlakoztatott adatforrás által értelmezhető formátumba.

Az összekötők olyan API-hívásokat tesznek elérhetővé, amelyek egy csatlakoztatott adatforrással (olvasási és írási) identitási adatokat cserélnek. Az Extensible connectivity Framework használatával egyéni összekötőt is hozzáadhat. Az alábbi ábra azt szemlélteti, hogy az összekötő hogyan kapcsol össze egy csatlakoztatott adatforrást a Szinkronizáló motorral.

![Az ábrán egy összekötő nevű vonal van társítva egy csatlakoztatott adatforrást és egy szinkronizáló motort.](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Az adatforgalom mindkét irányban elvégezhető, de egyszerre nem lehet mindkét irányban felfolyni. Más szóval egy összekötő konfigurálható úgy, hogy lehetővé tegye az adatok átvitelét a csatlakoztatott adatforrásból a motor vagy a szinkronizálási motorról a csatlakoztatott adatforrásba, de csak az egyik objektum és attribútum esetében lehet egyszerre egy adott művelet. Az irány különböző objektumok és különböző attribútumok esetében eltérő lehet.

Összekötő konfigurálásához meg kell adnia a szinkronizálni kívánt objektumokat. Az Objektumtípusok megadása meghatározza a szinkronizálási folyamat részét képező objektumok hatókörét. A következő lépés a szinkronizálandó attribútumok kiválasztása, amelyet az attribútum-befoglalási listának nevezünk. Ezek a beállítások bármikor módosíthatók az üzleti szabályok változásaira adott válaszként. A Azure AD Connect telepítővarázsló használatakor ezek a beállítások konfigurálhatók.

Ha objektumokat szeretne exportálni egy csatlakoztatott adatforrásba, a befoglalási listának szerepelnie kell legalább egy adott objektumtípus egy csatlakoztatott adatforrásban való létrehozásához szükséges minimális attribútumoknak. Például a **sAMAccountName** attribútumnak szerepelnie kell az attribútum-befoglalási listán, hogy egy felhasználói objektumot exportáljon Active Directory, mert a Active Directory összes felhasználói objektumához meg kell adni egy **sAMAccountName** attribútumot. A telepítővarázsló ezt a konfigurációt is elvégzi Önnek.

Ha a csatlakoztatott adatforrás szerkezeti összetevőket, például partíciókat vagy tárolókat használ az objektumok rendszerezésére, korlátozhatja az adott megoldáshoz használt csatlakoztatott adatforrás területeit.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>A szinkronizálási motor névterének belső szerkezete
A teljes szinkronizálási motor névtere két névteret tartalmaz, amelyek az azonosító adatokat tárolják. A két névtér a következők:

* Az összekötő területe (CS)
* A metaverse (MV)

Az **összekötő** terület olyan átmeneti terület, amely a kijelölt objektumoknak a csatlakoztatott adatforrásból és az attribútumok belefoglalása listában megadott attribútumokból való ábrázolását tartalmazza. A szinkronizálási motor az összekötő területével határozza meg, hogy mi változott a csatlakoztatott adatforrásban, és hogyan lehet a bejövő módosításokat megállapítani. A Szinkronizáló motor az összekötő területét is használja az exportáláshoz a csatlakoztatott adatforrásba való exportáláshoz. A Szinkronizáló motor külön összekötő területet tart fenn az egyes összekötők átmeneti területe számára.

Egy átmeneti terület használata esetén a Szinkronizáló motor a csatlakoztatott adatforrástól függetlenül marad, és a rendelkezésre állásuk és a hozzáférhetőségük nem érinti. Ennek eredményeképpen bármikor feldolgozhatja az identitási adatokat az átmeneti terület adatainak használatával. A szinkronizálási motor csak a csatlakoztatott adatforráson belül végrehajtott módosításokat kérheti le az utolsó kommunikációs munkamenet leállása óta, vagy csak a csatlakoztatott adatforrás által még nem fogadott azonosítók változásait küldi el, ami csökkenti a szinkronizálási motor és a csatlakoztatott adatforrás közötti hálózati forgalmat.

A szinkronizálási motor továbbá az összes olyan objektumra vonatkozó állapotinformációkat tárolja, amely az összekötő területének szakaszában van. Amikor új adatokat fogad, a Szinkronizáló motor mindig kiértékeli, hogy az adatokat már szinkronizálták-e.

A **metaverse** egy tárolóhely, amely a több csatlakoztatott adatforrásból származó összesített azonosító adatokat tartalmazza, és egyetlen globális, integrált nézetet biztosít az összes egyesített objektumról. A metaverse-objektumok a csatlakoztatott adatforrásokból beolvasott személyazonossági adatok, valamint olyan szabályok alapján jönnek létre, amelyek lehetővé teszik a szinkronizálási folyamat testreszabását.

A következő ábra az összekötő területének névterét és a metaverse-névteret mutatja a Szinkronizáló motoron belül.

![A diagramon egy összekötőhöz csatlakozó, összekötőként szolgáló adatforrást és egy szinkronizáló motort láthat, amely a Connector és a metaverse névterekhez van elkülönítve.](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Szinkronizáló motor Identity Objects
A Szinkronizáló motorban lévő objektumok a csatlakoztatott adatforrásban lévő objektumok vagy a motort szinkronizáló integrált nézet szerint jelennek meg. Minden szinkronizáló motor objektumnak globálisan egyedi azonosítóval (GUID) kell rendelkeznie. A GUID azonosítók adatintegritást és expressz kapcsolatokat biztosítanak az objektumok között.

### <a name="connector-space-objects"></a>Összekötő terület objektumai
Ha a szinkronizálási motor egy csatlakoztatott adatforrással kommunikál, beolvassa a csatlakoztatott adatforrásban lévő azonosító adatokat, és ezeket az információkat használja az Identity objektumnak az összekötő területére való létrehozásához. Ezeket az objektumokat nem lehet egyenként létrehozni vagy törölni. Azonban manuálisan is törölheti az összes objektumot egy összekötő területéről.

Az összekötő terület összes objektumának két attribútuma van:

* Globálisan egyedi azonosító (GUID)
* Megkülönböztető név (más néven DN)

Ha a csatlakoztatott adatforrás egy egyedi attribútumot rendel hozzá az objektumhoz, akkor az összekötő terület objektumai is rendelkezhetnek Anchor attribútummal. A Anchor attribútum egyedi módon azonosít egy objektumot a csatlakoztatott adatforrásban. A Szinkronizáló motor a horgony használatával megkeresi az objektum megfelelő megjelenítését a csatlakoztatott adatforrásban. A Szinkronizáló motor feltételezi, hogy egy objektum horgonya soha nem változik az objektum élettartama során.

Az összekötők közül sok egy ismert egyedi azonosítóval rendelkezik, amely automatikusan létrehoz egy horgonyt minden objektumhoz az importáláskor. Az Active Directory-összekötő például a **ObjectGUID** attribútumot használja egy horgonyhoz. Olyan csatlakoztatott adatforrások esetén, amelyek nem rendelkeznek egyértelműen meghatározott egyedi azonosítóval, az összekötő konfigurációjának részeként megadhatja a rögzítési generációt.

Ebben az esetben a horgony egy vagy több objektumtípus egyedi attribútumaiból épül fel, amelyek egyike sem változik, és amely egyedileg azonosítja az objektumot az összekötő területén (például egy alkalmazotti szám vagy egy felhasználói azonosító).

Az összekötő terület objektum a következők egyike lehet:

* Egy előkészítési objektum
* Egy helyőrző

### <a name="staging-objects"></a>Előkészítési objektumok
Az előkészítési objektum a kijelölt objektumtípusok egy példányát jelöli a csatlakoztatott adatforrásból. A GUID és a megkülönböztető név mellett az előkészítési objektumnak mindig van egy értéke, amely jelzi az objektum típusát.

Az importált előkészítési objektumok mindig rendelkeznek a Anchor attribútum értékével. A szinkronizálási motor által újonnan kiépített átmeneti objektumok, amelyek a csatlakoztatott adatforrásban való létrehozás folyamatban vannak, nem rendelkeznek értékkel a Anchor attribútumhoz.

Az előkészítési objektumok az üzleti attribútumok aktuális értékeit is végrehajtják, és a szinkronizálási folyamat végrehajtásához szükséges üzemeltetési adatokat is elvégezhetik. Az operatív információ olyan jelzőket is tartalmaz, amelyek az átmeneti objektumon előkészített frissítések típusát jelölik. Ha egy átmeneti objektum olyan új identitási adatokat kapott a csatlakoztatott adatforrásból, amelyek még nem lettek feldolgozva, az objektum az **Importálás függőben állapotában** van megjelölve. Ha egy előkészítési objektum olyan új identitási információkkal rendelkezik, amelyek még nem lettek exportálva a csatlakoztatott adatforrásba, a rendszer az **Exportálás függőben állapotba** kerül.

Az átmeneti objektumok lehetnek importálási objektumok vagy exportálási objektumok. A szinkronizálási motor létrehoz egy importálási objektumot a csatlakoztatott adatforrásból kapott objektum-adatok használatával. Ha a szinkronizálási motor információt kap egy olyan új objektum létezéséről, amely megfelel az összekötőben kiválasztott objektumtípusok egyikének, a rendszer létrehoz egy importálási objektumot az összekötő területen az objektumnak a csatlakoztatott adatforrásban való ábrázolásával.

A következő ábra egy olyan importálási objektumot mutat be, amely a csatlakoztatott adatforrásban lévő objektumot jelöli.

![Ábrán egy olyan importálási objektum látható, amely a csatlakoztatott adatforrásból a Szinkronizáló motorban található összekötő-terület névterére mutat.](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

A Szinkronizáló motor a metaverse-ban objektum-információ használatával hoz létre exportálási objektumot. Az exportálási objektumokat a rendszer a következő kommunikációs munkamenet során exportálja a csatlakoztatott adatforrásba. A szinkronizálási motor szempontjából az exportálási objektumok még nem léteznek a csatlakoztatott adatforrásban. Ezért az exportálási objektumhoz tartozó Anchor attribútum nem érhető el. Miután az objektumot a Szinkronizáló motortól kapta, a csatlakoztatott adatforrás egyedi értéket hoz létre az objektum Anchor attribútumához.

Az alábbi ábra azt szemlélteti, hogyan hozható létre exportálási objektum a metaverse-beli Identity Information használatával.

![A diagramon egy, a metaverse-ból az összekötő területére, majd a csatlakoztatott adatforrásra indított exportálási objektum látható.](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

A Szinkronizáló motor megerősíti az objektum exportálását az objektumnak a csatlakoztatott adatforrásból való újraimportálásával. Az objektumok exportálása akkor válik elérhetővé, ha a szinkronizálási motor a következő importálás során fogadja őket a csatlakoztatott adatforrásból.

### <a name="placeholders"></a>Helyőrzők
A szinkronizálási motor egy egyszerű névteret használ az objektumok tárolásához. Egyes csatlakoztatott adatforrások, például a Active Directory hierarchikus névteret használnak. Egy hierarchikus névtérből származó információk egy egyszerű névtérbe való átalakításához a szinkronizálási motor helyőrzőket használ a hierarchia megőrzése érdekében.

Az egyes helyőrzők egy olyan összetevőt (például szervezeti egységet) jelölnek, amely nem lett importálva a Szinkronizáló motorba, de szükséges a hierarchikus név létrehozásához. A csatlakoztatott adatforrásban lévő hivatkozások által létrehozott hézagokat kitöltik olyan objektumokra, amelyek nem készítenek objektumokat az összekötő területén.

A Szinkronizáló motor helyőrzőket is használ a még nem importált, hivatkozott objektumok tárolására. Ha például a szinkronizálás úgy van konfigurálva, hogy tartalmazza a következőt: *a Sperry-objektum Manager* -attribútuma, és a kapott érték olyan objektum, amely még nem lett importálva, például: *CN = Lee, CN = Users, DC = Fabrikam, DC = com*, a kezelő adatai helyőrzőként tárolódnak az összekötő területén. Ha a kezelő objektumot később importálja, a rendszer felülírja a helyőrző objektumot a felettest jelképező átmeneti objektummal.

### <a name="metaverse-objects"></a>Metaverse-objektumok
Egy metaverse-objektum tartalmazza azt az összesített nézetet, amellyel a rendszer szinkronizálja a motort az összekötő területének átmeneti objektumaival. A szinkronizálási motor metaverse-objektumokat hoz létre az importálási objektumok információi alapján. Az összekötők több objektuma egyetlen metaverse-objektumhoz csatolható, de egy összekötő terület objektum nem kapcsolható több metaverse-objektumhoz.

A metaverse objektumokat nem lehet manuálisan létrehozni vagy törölni. A Szinkronizáló motor automatikusan törli azokat a metaverse-objektumokat, amelyek nem rendelkeznek hivatkozással az összekötő területének bármely összekötő terület objektumához.

Ha egy csatlakoztatott adatforrásban lévő objektumokat a metaverse-ben lévő megfelelő objektumtípust kívánja leképezni, a Szinkronizáló motor egy bővíthető sémát biztosít az Objektumtípusok és a társított attribútumok előre meghatározott készletével. A metaverse-objektumokhoz új objektumtípusok és attribútumok is létrehozhatók. Az attribútumok lehetnek egyértékű vagy többértékű értékek, és az attribútum típusa lehet karakterlánc, hivatkozás, szám és logikai érték.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Az átmeneti objektumok és a metaverse-objektumok közötti kapcsolatok
A szinkronizálási motor névterén belül az adatfolyamatot az átmeneti objektumok és a metaverse-objektumok közötti kapcsolati kapcsolat engedélyezi. A metaverse-objektumhoz csatolt előkészítési objektumot egy **csatlakoztatott objektumnak** (vagy **összekötő objektumnak**) nevezzük. Egy metaverse-objektumhoz nem csatolt előkészítési objektumot egy leválasztott **objektumnak** (vagy a **leválasztó objektumnak**) nevezzük. A csatlakoztatott és a megszakított kifejezések használata javasolt, hogy ne tévesszük össze azokat az összekötőket, amelyek a csatlakoztatott címtárból származó adatok importálásához és exportálásához felelősek.

A helyőrzők soha nem kapcsolódnak metaverse-objektumhoz

Egy csatlakoztatott objektum egy előkészítési objektumból és a hozzá tartozó, egyetlen metaverse-objektumhoz csatolt kapcsolatból áll. Az összevont objektumok segítségével szinkronizálhatók az attribútumok egy összekötő terület objektum és egy metaverse objektum között.

Ha egy előkészítési objektum a szinkronizálás során egy csatlakoztatott objektum lesz, az attribútumok az előkészítési objektum és a metaverse objektum között válthatnak. Az attribútum folyamata kétirányú, és az attribútum-szabályok importálása és az attribútumok exportálása szabályok használatával van konfigurálva.

Egyetlen összekötő terület objektum csak egy metaverse-objektumhoz kapcsolható. Az egyes metaverse-objektumok azonban az alábbi ábrán látható módon több összekötő területhez is összekapcsolhatók ugyanahhoz a különböző összekötő-terekhez.

![A diagramon két csatlakoztatott adatobjektum van társítva egy olyan szinkronizáló motorhoz, amely csatlakoztatott objektumokkal és egy megszüntetett objektummal rendelkezik.](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

Az előkészítési objektum és a metaverse objektum közötti csatolt kapcsolat állandó, és csak a megadott szabályok alapján távolítható el.

A kihagyott objektum olyan átmeneti objektum, amely nincs egy metaverse-objektumhoz csatolva. A kihagyott objektumok attribútum-értékei a metaverse-n belül még nem lesznek feldolgozva. A csatlakoztatott adatforrás megfelelő objektumának attribútum-értékeit a rendszer nem frissíti a Szinkronizáló motorral.

A leválasztott objektumok használatával a Szinkronizáló motorban tárolhatók az azonosító adatok, és később feldolgozhatók. Ha egy előkészítési objektumot egy leválasztott objektumként tart az összekötő-térben, számos előnnyel jár. Mivel a rendszer már előkészítette az objektumra vonatkozó szükséges adatokat, nem szükséges újból létrehozni az objektumot a csatlakoztatott adatforrás következő importálásakor. Így a Szinkronizáló motornak mindig teljes pillanatképe van a csatlakoztatott adatforrásról, még akkor is, ha nincs aktuális kapcsolat a csatlakoztatott adatforrással. A leválasztott objektumok átállíthatók az összevont objektumokra, és fordítva, a megadott szabályoktól függően.

Egy importálási objektum nem összeillesztett objektumként jön létre. Az exportálási objektumnak csatlakoztatott objektumnak kell lennie. A rendszerlogika kikényszeríti ezt a szabályt, és törli az összes olyan exportálási objektumot, amely nem csatlakoztatott objektum.

## <a name="sync-engine-identity-management-process"></a>A szinkronizálási motor identitás-kezelési folyamata
Az Identitáskezelés folyamata azt szabályozza, hogyan frissülnek az identitási adatok a különböző csatlakoztatott adatforrások között. Az Identitáskezelés három folyamaton történik:

* Importálás
* Szinkronizálás
* Exportálás

Az importálási folyamat során a szinkronizálási motor kiértékeli a bejövő azonosító adatokat egy csatlakoztatott adatforrásból. A módosítások észlelésekor új átmeneti objektumokat hoz létre, vagy frissíti a meglévő átmeneti objektumokat az összekötő területéről a szinkronizáláshoz.

A szinkronizálási folyamat során a szinkronizálási motor frissíti a metaverse-t, hogy tükrözze az összekötő területének változásait, és frissíti az összekötő területét, hogy tükrözze a metaverse-ban történt módosításokat.

Az exportálási folyamat során a szinkronizálási motor leküldi az átmeneti objektumokra előkészített és a függőben lévő exportálásra megjelölt módosításokat.

Az alábbi ábrán látható, hogy az egyes folyamatok hogyan alakulnak át az egyik csatlakoztatott adatforrásból egy másikba.

![Az ábrán látható, hogy a csatlakoztatott adatokból származó azonosító információk a kapcsolódó adatokra (Importálás) a metaverse-ről összekötő területre (synchonization) és a csatlakoztatott adatokra (exportálásra) mutatnak.](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Importálási folyamat
Az importálási folyamat során a szinkronizálási motor kiértékeli az azonosítási adatok frissítéseit. A Szinkronizáló motor összehasonlítja a csatlakoztatott adatforrástól kapott azonosító adatokat egy átmeneti objektum azonosító adataival, és meghatározza, hogy az előkészítési objektum frissítést igényel-e. Ha szükség van az előkészítési objektum új adattal való frissítésére, az előkészítési objektum függőben lévő importálásként van megjelölve.

Ha a szinkronizálás előtt az összekötő térben lévő objektumokat átmeneti állapotba helyezi, a szinkronizálási motor csak a módosított azonosító adatokat tudja feldolgozni. Ez a folyamat a következő előnyöket biztosítja:

* **Hatékony szinkronizálás**. A szinkronizálás során feldolgozott adatmennyiség minimálisra csökken.
* **Hatékony Újraszinkronizálás**. Megváltoztathatja, hogy a szinkronizálási motor hogyan dolgozza fel az identitási adatokat anélkül, hogy újra csatlakoztatná a Szinkronizáló motort az adatforráshoz.
* **Lehetőség a szinkronizálás előnézetére**. Megtekintheti a szinkronizálást, és ellenőrizheti, hogy helyesek-e az identitás-felügyeleti folyamatra vonatkozó feltételezések.

Az összekötőben megadott minden egyes objektum esetében a Szinkronizáló motor először az összekötő területének az objektumnak a helyét keresi. A Szinkronizáló motor megvizsgálja az összekötő területének összes előkészítési objektumát, és megpróbál egy megfelelő, megfelelő szerkesztőpont-attribútummal rendelkező előkészítési objektumot keresni. Ha egy meglévő előkészítési objektumnak nincs megfelelő Anchor attribútuma, a szinkronizálási motor megpróbálja megkeresni egy megfelelő átmeneti objektumot ugyanazzal a megkülönböztető névvel.

Ha a Szinkronizáló motor olyan átmeneti objektumot talál, amely a megkülönböztető név alapján, de nem a horgony alapján egyezik, a következő különleges viselkedés fordul elő:

* Ha az összekötő területén található objektumnak nincs horgonya, akkor a szinkronizálási motor eltávolítja ezt az objektumot az összekötő területéről, és megjelöli az újrapróbálkozási kiépítést a **következő szinkronizálási futtatáskor**. Ezután létrehozza az új importálási objektumot.
* Ha az összekötő területén található objektum horgonyt tartalmaz, akkor a Szinkronizáló motor azt feltételezi, hogy az objektum átnevezve lett vagy törölve lett a csatlakoztatott címtárban. Egy ideiglenes, új megkülönböztető nevet rendel hozzá az összekötő terület objektumhoz, hogy az a bejövő objektumot megtudja. A régi objektum **átmeneti** állapotba kerül, és arra vár, hogy az összekötő importálja az átnevezést vagy törlést a helyzet feloldásához.

Ha a Szinkronizáló motor olyan átmeneti objektumot keres, amely az összekötőben megadott objektumnak felel meg, meghatározza, hogy milyen módosításokat kell alkalmazni. A szinkronizálási motor például átnevezheti vagy törölheti az objektumot a csatlakoztatott adatforrásban, vagy csak az objektum attribútum-értékeit lehet frissíteni.

A frissített adattal rendelkező előkészítési objektumok az importálás függőben vannak megjelölve. A függőben lévő importálások különböző típusai érhetők el. Az importálási folyamat eredményétől függően az összekötő területének egy előkészítési objektuma a következő függőben lévő importálási típusok egyikét eredményezi:

* **Nincs**. Nem érhető el az előkészítési objektum egyik attribútumának módosítása. A szinkronizálási motor nem jelöli meg a típust függőben lévő importálásként.
* **Hozzáadás**. Az előkészítési objektum az összekötő terület új importálási objektuma. A szinkronizálási motor a metaverse-ben a további feldolgozáshoz függőben lévő importálási jelzőket adja meg.
* **Frissítés**. A Szinkronizáló motor megkeresi az összekötő területének megfelelő előkészítési objektumot, és a típust függőben lévő importálásként adja meg, hogy az attribútumok frissítései a metaverse-ben is feldolgozhatók legyenek. A frissítések közé tartozik az objektum átnevezése.
* **Delete**. A Szinkronizáló motor talál egy megfelelő előkészítési objektumot az összekötő területén, és a típust függőben lévő importálásként adja meg, hogy az összekapcsolt objektum törölhető legyen.
* **Törlés/Hozzáadás**. A Szinkronizáló motor megkeresi az összekötő területének megfelelő előkészítési objektumot, de az Objektumtípusok nem egyeznek. Ebben az esetben a törlés hozzáadására szolgáló módosítás előkészítése folyamatban van. A DELETE-Add módosítás azt jelzi, hogy a szinkronizálási motornak az objektum teljes újraszinkronizálására van szükség, mert az objektum típusának megváltozásakor a szabályok különböző készletei érvényesek erre az objektumra.

Egy átmeneti objektum függőben lévő importálási állapotának beállításával jelentősen csökkentheti a szinkronizálás során feldolgozott adatmennyiséget, mert így a rendszer csak azokat az objektumokat dolgozza fel, amelyek frissítettek.

### <a name="synchronization-process"></a>Szinkronizálási folyamat
A szinkronizálás két kapcsolódó folyamatból áll:

* Bejövő szinkronizálás, ha a metaverse tartalmának frissítése az összekötő területének adataival történik.
* Kimenő szinkronizálás, ha az összekötő területének tartalma a metaverse-ban található adatokat használva frissül.

Az összekötő térben található információk használatával a bejövő szinkronizálási folyamat a metaverse-ben hozza létre a csatlakoztatott adatforrásokban tárolt adatok integrált nézetét. Vagy az összes átmeneti objektum, vagy csak a függőben lévő importálási adatokkal rendelkezők összesítve lesznek, attól függően, hogy a szabályok hogyan vannak konfigurálva.

A kimenő szinkronizációs folyamat frissíti az objektumok exportálását, amikor a metaverse-objektumok megváltoznak.

A bejövő szinkronizálás a csatlakoztatott adatforrásokból kapott személyazonossági adatok metaverse-beli integrált nézetét hozza létre. A Szinkronizáló motor bármikor feldolgozhatja az identitási adatokat a csatlakoztatott adatforrásból származó legfrissebb azonosítási adatok használatával.

**Bejövő szinkronizálás**

A bejövő szinkronizálás a következő folyamatokat tartalmazza:

* **Kiépítés** (más néven **kivetítés** , ha fontos megkülönböztetni ezt a folyamatot a kimenő szinkronizációs kiépítés során). A Szinkronizáló motor létrehoz egy új metaverse-objektumot egy átmeneti objektum alapján, és csatolja őket. A kiépítés egy objektumorientált művelet.
* **Csatlakozás**. A Szinkronizáló motor egy átmeneti objektumhoz csatol egy meglévő metaverse-objektumot. A JOIN objektum szintű művelet.
* **Attribútum-folyamat importálása**. A Szinkronizáló motor frissíti a metaverse nevű objektum attribútum-értékeit. Az attribútumok importálása folyamat olyan attribútum-szintű művelet, amely egy átmeneti objektum és egy metaverse objektum közötti kapcsolatot igényel.

A létesítés az egyetlen olyan folyamat, amely objektumokat hoz létre a metaverse-ben. A kiépítés csak a megszüntetett objektumok importálására vonatkozik. A kiépítés során a szinkronizálási motor létrehoz egy metaverse-objektumot, amely megfelel az importálási objektum objektumtípusének, és kapcsolatot létesít mindkét objektum között, így egy csatlakoztatott objektumot hoz létre.

Az illesztési folyamat az importálási objektumok és a metaverse objektum közötti kapcsolatot is létrehozza. A csatlakozás és a kiépítés közötti különbség az, hogy az illesztési folyamat megköveteli, hogy az importálási objektum egy meglévő metaverse-objektumhoz legyen társítva, ahol a létesítési folyamat létrehoz egy új metaverse-objektumot.

A szinkronizálási motor megpróbál csatlakozni egy importálási objektumhoz egy metaverse-objektumhoz a szinkronizálási szabály konfigurációjában megadott feltételek használatával.

A kiépítés és a csatlakozás folyamata során a szinkronizálási motor összekapcsolja egy megszüntetett objektumot egy metaverse-objektummal, így azok összekapcsolhatók. Az objektumorientált műveletek befejezését követően a Szinkronizáló motor frissíteni tudja a társított metaverse objektum attribútum-értékeit. Ezt a folyamatot nevezzük importálási attribútum folyamatának.

Az attribútum importálása folyamat minden olyan importálási objektumon történik, amely új adatmennyiséget végez, és egy metaverse-objektumhoz van csatolva.

**Kimenő szinkronizálás**

A kimenő szinkronizálási frissítések exportálják az objektumokat, amikor egy metaverse-objektum megváltozik, de nem törlődik. A kimenő szinkronizálás célja annak kiértékelése, hogy a metaverse-objektumokra vonatkozó módosítások szükségesek-e az átmeneti objektumokhoz az összekötő szóközökben. Bizonyos esetekben előfordulhat, hogy a változtatások megkövetelik, hogy a rendszer az összes összekötő tárolóhelyén lévő átmeneti objektumokat frissítse. A módosított előkészítési objektumok az Exportálás függőben vannak megjelölve, így objektumokat exportálnak. Ezeket az exportálási objektumokat a rendszer később az exportálási folyamat során leküldi a csatlakoztatott adatforrásnak.

A kimenő szinkronizálás három folyamattal rendelkezik:

* **Telepítés**
* **Megszüntetés**
* **Attribútum-folyamat exportálása**

A kiépítés és a megszüntetés egyaránt objektumorientált művelet. A megszüntetés a kiépítés függ, mivel csak a kiépítés kezdeményezhető. A megszüntetés akkor aktiválódik, amikor a kiépítés eltávolítja a metaverse-objektum és az exportálási objektum közötti kapcsolatot.

A kiépítés mindig aktiválódik, ha a rendszer módosításokat alkalmaz a metaverse objektumokra. A metaverse-objektumok módosításakor a Szinkronizáló motor a következő feladatokat hajthatja végre a létesítési folyamat részeként:

* Egyesített objektumok létrehozása, ahol egy metaverse-objektum egy újonnan létrehozott exportálási objektumhoz van társítva.
* Egy csatlakoztatott objektum átnevezése.
* Kapcsolat megválasztása egy metaverse-objektum és az átmeneti objektumok között, egy leválasztott objektum létrehozásával.

Ha a kiépítés megköveteli, hogy a szinkronizálási motor hozzon létre egy új összekötő objektumot, akkor az az átmeneti objektum, amelyhez a metaverse objektum csatolva van, mindig egy exportálási objektum, mert az objektum még nem létezik a csatlakoztatott adatforrásban.

Ha a kiépítés megköveteli, hogy a szinkronizálási motor megvegyen egy csatlakoztatott objektumot, a rendszer kiépített objektumot hozzon létre, és kivonja a megszüntetést. A megszüntetési folyamat törli az objektumot.

A kiépítés során az exportálási objektum törlése nem törli fizikailag az objektumot. Az objektum **törölve** lett megjelölve, ami azt jelenti, hogy a törlési művelet az objektumon van elrendezve.

Az attribútum exportálása a kimenő szinkronizálási folyamat során is megtörténik, hasonlóan ahhoz, ahogyan az az attribútum importálása a bejövő szinkronizálás során történik. Az attribútumok exportálása csak a metaverse és a csatlakoztatott objektumok között történik.

### <a name="export-process"></a>Exportálási folyamat
Az exportálási folyamat során a szinkronizálási motor megvizsgálja az összes olyan exportálási objektumot, amely a függőben lévő exportálásként van megjelölve az összekötő területén, majd frissítéseket küld a csatlakoztatott adatforrásnak.

A Szinkronizáló motor képes meghatározni az Exportálás sikerességét, de nem tudja eléggé meghatározni, hogy az Identitáskezelés folyamata befejeződött. A csatlakoztatott adatforrásban lévő objektumokat a többi folyamat mindig módosíthatja. Mivel a Szinkronizáló motor nem rendelkezik állandó kapcsolattal a csatlakoztatott adatforrással, nem elegendő, ha a csatlakoztatott adatforrásban lévő objektum tulajdonságaira vonatkozó feltételezéseket csak egy sikeres exportálási értesítés alapján kíván létrehozni.

A csatlakoztatott adatforrás egyik folyamata például megváltoztathatja az objektum attribútumait az eredeti értékekre (azaz a csatlakoztatott adatforrás felülírhatja az értékeket, miután a szinkronizálási motor leküldte az adatait, és sikeresen alkalmazta őket a csatlakoztatott adatforrásban).

A szinkronizálási motor tárolja az egyes átmeneti objektumok exportálási és importálási állapotára vonatkozó információkat. Ha a legutóbbi exportálás óta megváltoztak az attribútum-befoglalási listán megadott attribútumok értékei, az importálási és exportálási állapot tárolása lehetővé teszi a szinkronizálási motor megfelelő reagálását. A szinkronizálási motor az importálási folyamat segítségével megerősíti a csatlakoztatott adatforrásba exportált attribútum-értékeket. Az importált és az exportált információk összehasonlítása az alábbi ábrán látható módon lehetővé teszi a szinkronizálási motor számára annak meghatározását, hogy az Exportálás sikeres volt-e, vagy hogy meg kell-e ismételni.

![Az ábrán egy objektum szinkronizálása látható az összekötő és a csatlakoztatott adat között az összekötőn keresztül.](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Ha például a szinkronizálási motor exportálja a C attribútumot, amelynek értéke 5, egy csatlakoztatott adatforráshoz, a a C = 5 értéket tárolja az exportálási állapot memóriájában. Az objektum minden további exportálásakor a rendszer megkísérli a C = 5 exportálását a csatlakoztatott adatforrásba, mivel a Szinkronizáló motor azt feltételezi, hogy ez az érték nem lett tartósan alkalmazva az objektumra (azaz ha a csatlakoztatott adatforrásból nem történt meg a másik érték importálása). Az exportálási memória törlődik, ha a C = 5 fogadása az objektumon végzett importálási művelet során történik.

## <a name="next-steps"></a>Következő lépések
További információ a [Azure ad Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációról.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

