---
title: Azure Data Catalog gyakori kérdések
description: Azure Data Catalogekkel kapcsolatos gyakori kérdések, beleértve az adatforrások felderítésének, megjegyzésének és felügyeletének képességeit.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 98854a4588b59cf0c19877da870e6124fa7c3b9a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674682"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Azure Data Catalog gyakori kérdések

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Ez a cikk a Azure Data Catalog szolgáltatással kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

## <a name="what-is-azure-data-catalog"></a>Mi az az Azure Data Catalog?
A Data Catalog egy teljes körűen felügyelt szolgáltatás, amely a Microsoft Azureban üzemel, és a vállalati adatforrások regisztrálásának és felderítésének rendszerét szolgálja. A Data Catalog, bármely felhasználó, az elemzők által az adatszakértők és a fejlesztők számára az adatforrások regisztrálása, felderítése, megértése és felhasználása is hasznos lehet.

## <a name="what-customer-challenges-does-it-solve"></a>Milyen kihívásokkal jár az ügyfelek megoldása?
Data Catalog az adatforrások felderítése és a "sötét adatok" kihívásaival foglalkozik, így a felhasználók felfedezhetik és megismerhetik a vállalati adatforrásokat.

## <a name="what-are-its-target-audiences"></a>Mik a célközönségek?
A Data Catalog műszaki és nem műszaki felhasználók számára készült, beleértve a következőket:

* Adatfejlesztők és BI-és elemzési szakemberek: azok a személyek, akik az adatokat és az elemzési tartalmakat használják másoknak.
* Adatkezelők: azok, akik ismerik az információkat, mit jelent, és hogyan kívánják használni őket.
* Adatfogyasztók: azok a felhasználók, akik számára szükségük van a feladataik elvégzéséhez szükséges adatok egyszerű felderítésére, megismerésére és a hozzájuk való kapcsolódásra.
* Központi IT: azok a személyek, akiknek több száz adatforrást kell felismerniük az üzleti felhasználók számára, és ki kell tartaniuk az információk felhasználásának és a kinek a felügyeletét.

## <a name="what-is-its-availability-by-region"></a>Mi a rendelkezésre állás régiónként?
Data Catalog szolgáltatások jelenleg a következő adatközpontokban érhetők el:

* USA nyugati régiója
* USA keleti régiója
* Nyugat-Európa
* Észak-Európa
* Kelet-Ausztrália
* Délkelet-Ázsia

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Milyen korlátai vannak az adategységek számának?
A Data Catalog ingyenes kiadása 5 000 regisztrált adategységre korlátozódik.

A Data Catalog standard kiadása legfeljebb 100 000 regisztrált adategységet támogat.

A Data Catalogban regisztrált összes objektum, például a táblák, a nézetek, a fájlok és a jelentések, adategységnek számít.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Mik a támogatott adatforrások és adategységek típusai?
A jelenleg támogatott adatforrások listáját lásd: [Data Catalog DSR](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Hogyan egy másik adatforrás támogatását?
A szolgáltatásra vonatkozó kérések és egyéb visszajelzések elküldéséhez lépjen a [Data Catalog az Azure visszajelzési fórumokon](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="why-do-i-get-an-error-catalog-already-exists-when-i-try-to-create-a-new-catalog"></a>Miért kapok hibaüzenetet *, amikor megpróbálok új katalógust* létrehozni?

Az Office 365 E5 Power BI Pro licenccel való megvásárlásakor a Microsoft automatikusan létrehoz egy alapértelmezett katalógust az előfizetés régiójában. Ez a katalógus az ingyenes SKU-t használja. Az Office 365/Power BI felhasználói licenc kezelése az adminisztráció oldalon történik. 

Ez a típusú adatkatalógus azonban nem rendelkezik **rendszergazdai lehetőséggel** , és nem látható a **Azure Portalban**. Ez a típusú adatkatalógus nem törölhető. Hasonlóképpen nem lehet átnevezni a adatkatalógust, és nem helyezhető át másik régióba. 

Azok a felhasználói fiókok, amelyek Power BI Pro licenccel rendelkeznek, automatikusan hozzáférhetnek az adatkatalógushoz, amikor az Office 365 E5-es Power BI Pro licenccel regisztráltak. Ez a típusú felhasználó rendszergazdai jogosultságok nélkül teljes hozzáféréssel rendelkezik a adatkatalógus eszközeihez. Az ilyen típusú felhasználó *nem* része a **katalógus felhasználói** szerepkörének Azure Data Catalog.


## <a name="how-do-i-get-started-with-data-catalog"></a>Hogyan első lépések a Data Catalog?
Az első lépésekhez a legjobb módszer, ha a [első lépések a Data Catalog](data-catalog-get-started.md). Ez a cikk a szolgáltatás funkcióinak teljes körű áttekintése.

## <a name="how-do-i-register-my-data"></a>Hogyan regisztrálni az adataimat?
Az Data Catalog-ban lévő adatai regisztrálása:
1. A Azure Data Catalog portál **Közzététel** területén indítsa el a Azure Data Catalog regisztrációs eszközt. 
2. A Data Catalog adatforrás-regisztrációs eszközben jelentkezzen be ugyanazzal a hitelesítő adatokkal, amelyeket a Data Catalog portál eléréséhez használ.
3. Válassza ki az adatforrást és a regisztrálni kívánt eszközöket.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Milyen tulajdonságokat kell kinyerni a regisztrált adategységek esetében?
Az adott tulajdonságok eltérnek az adatforrástól az adatforrástól, de általánosságban a Data Catalog közzétételi szolgáltatás a következő információkat bontja ki:

* Eszköz neve
* Objektumtípus
* Eszköz leírása
* Attribútum/oszlopnevek
* Attribútum/oszlop adattípusai
* Attribútum/oszlop leírása

> [!IMPORTANT]
> Az adategységek Data Catalog való regisztrálása nem helyezi át és nem másolja át az adatait a felhőbe. Az adatforrásokból származó eszközök regisztrálása az eszközök metaadatait az Azure-ba másolja, de az adatok a létező adatforrás-helyen maradnak. A szabály alól kivételt képez, ha az eszközök regisztrálásakor az előnézeti rekordok vagy az adatprofilok feltöltését választja. Az előzetes verzió használatakor a rendszer legfeljebb 20 rekordot másol az egyes eszközökről, és pillanatképként tárolja Data Catalog. Adatprofil hozzáadásakor a rendszer az összesített információkat számítja ki és tartalmazza a katalógusban tárolt metaadatokban. Az összesített információk közé tartozhatnak a táblák mérete, a null értékek százalékos aránya oszloponként, vagy az oszlopok minimális, maximális és átlagos értékei. 
>
>

> [!NOTE]
> Az olyan adatforrások esetében, mint az első osztályú **description** tulajdonsággal rendelkező SQL Server Analysis Services, a Data Catalog adatforrás-regisztrációs eszköz kibontja a tulajdonság értékét. A *helyszíni SQL Server olyan, a (* z) Data Catalog adatforrás-regisztrációs eszköz, amely nem rendelkezik első osztályú **leírási** tulajdonsággal, kibontja az értéket az objektumok és oszlopok **ms_description** kiterjesztett tulajdonságában. Ez a tulajdonság SQL Azure esetén nem támogatott. További információ: [kiterjesztett tulajdonságok használata adatbázis-objektumokon](/previous-versions/sql/sql-server-2008-r2/ms190243(v=sql.105)).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Mennyi időt vesz igénybe, hogy az újonnan regisztrált eszközök megjelenjenek a katalógusban?
Miután a Data Cataloghoz regisztrálta az adategységeket, a Data Catalog-portálon megjelenő 5 – 10 másodperces időtartam lehet.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Hogyan a regisztrált adategységek metaadatainak megjegyzéseit és a metaadatok dúsítását?
A regisztrált eszközök metaadatainak biztosításának legegyszerűbb módja az, ha kijelöli az eszközt a Data Catalog-portálon, majd a kijelölt objektum Tulajdonságok ablaktábláján vagy a séma ablaktáblájában adja meg az értékeket.

A regisztrációs folyamat során bizonyos metaadatokat, például szakértőket és címkéket is megadhat. A Data Catalog közzétételi szolgáltatásban megadott értékek az adott időpontban regisztrált összes eszközre érvényesek. Ha a portálon a közelmúltban regisztrált objektumokat szeretné megtekinteni a további megjegyzésekhez, válassza a Data Catalog adatforrás-regisztrációs eszköz utolsó képernyőjén látható **portál megtekintése** gombot.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Hogyan törölni a regisztrált adatobjektumokat?
Az objektum Data Catalogból való törléséhez válassza ki az objektumot a portálon, majd kattintson a **Törlés** gombra. Az objektum eltávolítása eltávolítja a metaadatokat Data Catalog, de nincs hatással az alapul szolgáló adatforrásra.

## <a name="what-is-an-expert"></a>Mi az a szakértő?
A szakértő olyan személy, aki tájékozott perspektívát tartalmaz egy adatobjektummal kapcsolatban. Egy objektum több szakértővel is rendelkezhet. Egy szakértőnek nem kell "tulajdonosnak" lennie egy objektumhoz, de egyszerűen valaki, aki tudja, hogyan kell használni az adatátvitelt.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Ha probléma merül fel, Hogyan megoszthatja az információkat a Data Catalog csapatával?
A problémák jelentéséhez, az információk megosztásához és a kérdések feltételéhez lépjen a [Azure Data Catalog fórumra](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Működik a katalógus egy másik adatforrással, amely érdekli?
Aktívan dolgozunk további adatforrások hozzáadásával Data Catalog. Ha egy adott adatforrást szeretne megtekinteni, javasolja azt (vagy ha már javasolta), az [Azure visszajelzési fórumokon Data Catalog](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Milyen engedélyekre van szükségem az eszközök regisztrálásához a Data Catalog?
A Data Catalog regisztrációs eszköz futtatásához olyan engedélyekre van szüksége az adatforráson, amelyek lehetővé teszik a forrástól származó metaadatok olvasását. Az előzetes verzióhoz is szükség van olyan engedélyekkel, amelyek lehetővé teszik az adatok olvasását a regisztrált objektumokból.

Data Catalog azt is lehetővé teszi, hogy a katalógus-rendszergazdák korlátozzák, hogy mely felhasználók és csoportok adhatnak hozzá metaadatokat a katalógushoz. További információkért lásd: [a adatkatalógus és az adategységek hozzáférésének biztonságossá tétele](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Elérhető lesz-e Data Catalog a helyszíni telepítéshez is?
A Data Catalog egy felhőalapú szolgáltatás, amely a Felhőbeli és a helyszíni adatforrásokkal is képes együttműködni egy hibrid adatforrás-felderítési megoldás nyújtásával. Jelenleg nincs terv a helyszíni rendszeren futó Data Catalog szolgáltatás egyik verziójához sem.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Kinyerhetek több vagy gazdagabb metaadatokat a regisztrálni kívánt adatforrásokból?
Aktívan dolgozunk a Data Catalog képességeinek bővítésén. Ha további metaadatokat szeretne kinyerni az adatforrásból a regisztráció során, javasoljon (vagy szavazzon rá, ha már javasolt) az [Azure-visszajelzési fórumokon Data Catalog](https://feedback.azure.com/forums/906052-data-catalog). 

Ha az oszlop/séma metaadatait, az előzeteseket és az adatprofilokat is bele kívánja foglalni olyan adatforrások esetében, amelyekben ezt a metaadatokat nem az adatforrás-regisztrációs eszköz kinyeri, a metaadatok hozzáadásához használhatja a Data Catalog API-t. További információ: [Azure Data Catalog REST API](/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Hogyan korlátozni a regisztrált adategységek láthatóságát, hogy csak bizonyos személyek tudják felderíteni őket?
Válassza ki az adategységeket a Data Catalogban, majd kattintson a **tulajdonosi részvétel** gombra. Az adategységek tulajdonosai a Data Catalogban megváltoztathatják a láthatósági beállításokat, hogy az összes felhasználó felderítse a tulajdonban lévő eszközöket, vagy korlátozni tudja az adott felhasználók láthatóságát. További információ: [adategységek kezelése Azure Data Catalogban](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Hogyan frissíteni egy adategység regisztrációját, hogy az adatforrás változásai megjelennek a katalógusban?
Ha frissíteni szeretné a katalógusban már regisztrált adategységek metaadatait, egyszerűen regisztrálja újra az eszközöket tartalmazó adatforrást. Az adatforrás bármilyen módosítása, például a táblákból vagy nézetből hozzáadott vagy eltávolított oszlopok frissülnek a katalógusban, a felhasználók által megadott megjegyzések azonban megmaradnak.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>A kérdésem itt nem válaszol. Hol kaphatok választ?
Nyissa meg a [Azure Data Catalog fórumot](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). A feltett kérdésekre itt talál megoldást.