---
title: Azure Data Catalog fejlesztői fogalmak
description: Bemutatjuk a Azure Data Catalog fogalmi modellben található főbb fogalmakat a katalógus REST API használatával.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: ddeab4838feb07d1101993cab4ebc86581b4d8b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674699"
---
# <a name="azure-data-catalog-developer-concepts"></a>Azure Data Catalog fejlesztői fogalmak

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

A Microsoft **Azure Data Catalog** egy teljes körűen felügyelt felhőalapú szolgáltatás, amely lehetővé teszi az adatforrások felderítését és a közösségi-adatforrások metaadatait. A fejlesztők a REST API-kon keresztül használhatják a szolgáltatást. A szolgáltatásban megvalósított fogalmak megismerése fontos a fejlesztők számára a **Azure Data Catalog** való sikeres integráláshoz.

## <a name="key-concepts"></a>Fő fogalmak 
A **Azure Data Catalog** fogalmi modell a következő négy alapfogalmakon alapul: a **katalógus**, a **felhasználók**, az **eszközök** és a **jegyzetek**.

![Azure Data Catalog fogalmi modell ábrája](./media/data-catalog-developer-concepts/concept2.png)

### <a name="catalog"></a>Katalógus
A **katalógus** a szervezet által tárolt metaadatok legfelső szintű tárolója. Azure-fiókban egy **katalógus** engedélyezett. A katalógusok egy Azure-előfizetéshez vannak kötve, de egy adott Azure-fiókhoz csak egy **katalógus** hozható létre, annak ellenére, hogy egy fiók több előfizetéssel is rendelkezhet.

A katalógus A **felhasználókat** és az **eszközöket** tartalmazza.

### <a name="users"></a>Felhasználók
A felhasználók olyan rendszerbiztonsági tag, amelyek jogosultak műveletek végrehajtására (keresés a katalógusban, elemek hozzáadása, szerkesztése vagy eltávolítása stb.) a katalógusban.

A felhasználók több különböző szerepkörrel rendelkezhetnek. A szerepkörökkel kapcsolatos további információkért tekintse meg a szerepkörök és engedélyezés című szakaszt.

Egyéni felhasználók és biztonsági csoportok is hozzáadhatók.

A Azure Data Catalog az identitás-és hozzáférés-kezeléshez Azure Active Directory használ. Minden katalógus-felhasználónak a fiókhoz tartozó Active Directory tagjának kell lennie.

### <a name="assets"></a>Objektumok
A **katalógus** adategységeket tartalmaz. Az **eszközök** a katalógus által kezelt részletességi egység.

Az eszközök részletessége az adatforrástól függ. SQL Server vagy Oracle Database esetén az eszköz lehet tábla vagy nézet. SQL Server Analysis Services esetén az eszköz lehet mérték, dimenzió vagy fő teljesítménymutató (KPI). SQL Server Reporting Services esetében az eszköz egy jelentés.

Az **eszköz** a katalógusból hozzáadott vagy eltávolított dolog. Ez az az eredmény, amelyet a **keresésből** kap vissza.

A **rendszer a** nevét, helyét és típusát, valamint a hozzájuk tartozó jegyzeteket tartalmazza.

### <a name="annotations"></a>Széljegyzetek
A jegyzetek olyan elemek, amelyek az eszközök metaadatait jelölik.

A jegyzetek példái a következők: Leírás, címkék, séma, dokumentáció stb. Az eszközök típusai és a jegyzetek típusai teljes listáját az [objektum modellje szakaszban](#asset-object-model) találja.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Közösségi jegyzetek és felhasználói perspektíva (a vélemények sokfélesége)
A Azure Data Catalog fő aspektusa, hogy miként támogatja a rendszer közösségi. A wiki megközelítésével szemben – ahol csak egyetlen vélemény és az utolsó író nyer – a Azure Data Catalog modell lehetővé teszi, hogy több vélemény is legyen élő egymás mellett a rendszeren.

Ez a megközelítés tükrözi a nagyvállalati adat valós világát, ahol a különböző felhasználók különböző perspektívákkal rendelkezhetnek egy adott eszközön:

* Az adatbázis-rendszergazdák információkat biztosíthatnak a szolgáltatói szerződésekről, illetve a tömeges ETL-műveletek rendelkezésre álló feldolgozási ablakáról
* Az adatkezelők információkat biztosíthatnak azokról az üzleti folyamatokról, amelyekre az eszköz vonatkozik, illetve a vállalat által alkalmazott besorolásokra
* A pénzügyi elemző információkat biztosíthat arról, hogyan használják az adatokat az időszak végi jelentési feladatok során

Ennek a példának a támogatásához minden felhasználó – a DBA, az adatközpont és az elemző – egy leírást adhat hozzá egy, a katalógusban regisztrált egyetlen táblához. Az összes Leírás a rendszeren marad, és a Azure Data Catalog portálon minden leírás megjelenik.

Ez a minta az objektummodell elemeinek többségére vonatkozik, így a JSON-adattartalomban szereplő objektumtípusok gyakran tömbök a tulajdonságok esetében, amelyeknek egy egypéldányos kivárnia lehet.

Az eszköz gyökerében például a Leírás objektumok tömbje látható. A Array tulajdonság neve "Leírás". A Leírás objektumnak van egy tulajdonsága – leírás. A minta az, hogy a leírásban szereplő minden felhasználó egy, a felhasználó által megadott értékhez létrehozott Leírás objektumot kap.

Az UX megadhatja a kombináció megjelenítésének módját. Három különböző mintázat látható.

* A legegyszerűbb minta az "összes megjelenítése". Ebben a mintában az összes objektum megjelenik a lista nézetben. A Azure Data Catalog portál UX ezt a mintát használja a leíráshoz.
* Egy másik minta: "Merge". Ebben a mintában a különböző felhasználók összes értékének egyesítése együtt történik, és a rendszer duplikálja az elemeket. Példa erre a mintára a Azure Data Catalog portál UX a címkék és a szakértők tulajdonságai.
* A harmadik minta az "utolsó író WINS". Ebben a mintában csak a legújabb beírt érték jelenik meg. a friendlyName példa erre a mintára.

## <a name="asset-object-model"></a>Tárgyieszköz-objektum modellje
A főbb fogalmak szakaszban bemutatott módon a **Azure Data Catalog** objektummodell olyan elemeket tartalmaz, amelyek lehetnek eszközök vagy jegyzetek. Az elemekhez tulajdonságok tartoznak, amelyek opcionálisak vagy kötelezőek lehetnek. Egyes tulajdonságok minden elemre érvényesek. Egyes tulajdonságok minden eszközre érvényesek. Bizonyos tulajdonságok csak bizonyos típusú eszközökre érvényesek.

### <a name="system-properties"></a>Rendszertulajdonságok
<table><tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr><tr><td>időbélyeg</td><td>DateTime</td><td>Az objektum utolsó módosításának időpontja. Ezt a mezőt a kiszolgáló hozza létre, amikor egy tétel be van beszúrva, és minden alkalommal, amikor egy adott tétel frissül. A tulajdonság értékét a közzétételi műveletek bemenete figyelmen kívül hagyja.</td></tr><tr><td>ID (Azonosító)</td><td>URI</td><td>Az elemek abszolút URL-címe (csak olvasható). Ez az objektum egyedi címezhető URI-ja.  A tulajdonság értékét a közzétételi műveletek bemenete figyelmen kívül hagyja.</td></tr><tr><td>típus</td><td>Sztring</td><td>Az eszköz típusa (csak olvasható).</td></tr><tr><td>ETAG</td><td>Sztring</td><td>Az elem azon verziójához tartozó karakterlánc, amely az optimista Egyidejűség-vezérléshez használható a katalógusban lévő elemeket frissítő műveletek végrehajtásakor. a "*" bármely érték egyeztetésére használható.</td></tr></table>

### <a name="common-properties"></a>Közös tulajdonságok
Ezek a tulajdonságok az összes gyökérszintű eszköz típusára és az összes Megjegyzés típusára vonatkoznak.

<table>
<tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>
<tr><td>fromSourceSystem</td><td>Logikai</td><td>Azt jelzi, hogy az elem adatainak forrása rendszerből származik-e (például SQL Server adatbázis, Oracle Database), vagy egy felhasználó szerzője.</td></tr>
</table>

### <a name="common-root-properties"></a>Általános gyökérszintű tulajdonságok
<p>
Ezek a tulajdonságok az összes gyökérszintű eszköz típusára vonatkoznak.

<table><tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr><tr><td>name</td><td>Sztring</td><td>Az adatforrás helyéről származó adatokból származtatott név</td></tr><tr><td>DSL</td><td>DataSourceLocation</td><td>Egyedi módon ismerteti az adatforrást, és az eszköz egyik azonosítóját mutatja. (Lásd a kettős identitás szakaszt).  A DSL szerkezete a protokoll és a forrás típustól függ.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>További részletek az eszköz típusáról.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Azt a felhasználót ismerteti, aki legutóbb regisztrálta ezt az eszközt.  A felhasználó egyedi AZONOSÍTÓját (UPN) és a megjelenítendő nevet (lastName és firstName) is tartalmazza.</td></tr><tr><td>containerID</td><td>Sztring</td><td>Az adatforrás tároló-eszközének azonosítója. A tároló típusa nem támogatja ezt a tulajdonságot.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Közös, nem egyedi megjegyzések tulajdonságai
Ezek a tulajdonságok az összes nem egypéldányos jegyzet típusra vonatkoznak (a jegyzeteket, amelyek az egyes eszközökön többek között megengedettek).

<table>
<tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>
<tr><td>kulcs</td><td>Sztring</td><td>A felhasználó által megadott kulcs, amely egyedileg azonosítja a jegyzetet a jelenlegi gyűjteményben. A kulcs hossza nem lehet hosszabb 256 karakternél.</td></tr>
</table>

### <a name="root-asset-types"></a>Legfelső szintű eszközök típusai
A legfelső szintű eszközök típusai azok a típusok, amelyek a katalógusban regisztrálható adategységek különböző típusait jelölik. Minden gyökérszintű típushoz van egy nézet, amely a nézetben szereplő eszközöket és megjegyzéseket ismerteti. A nézet nevét a megfelelő {view_name} URL-szegmensben kell használni, amikor REST API használatával tesz közzé egy eszközt.

<table><tr><td><b>Eszköz típusa (nézet neve)</b></td><td><b>További tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Engedélyezett jegyzetek</b></td><td><b>Megjegyzések</b></td></tr><tr><td>Tábla ("táblák")</td><td></td><td></td><td>Leírás<p>FriendlyName<p>Címke<p>Séma<p>ColumnDescription<p>ColumnTag<p> Szakértő<p>Előnézet<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Dokumentáció<p></td><td>A táblázat a táblázatos adatokat ábrázolja.  Például: SQL-tábla, SQL-nézet, Analysis Services táblázatos táblázat, Analysis Services többdimenziós dimenzió, Oracle Table stb.   </td></tr><tr><td>Mérték ("mértékek")</td><td></td><td></td><td>Leírás<p>FriendlyName<p>Címke<p>Szakértő<p>AccessInstruction<p>Dokumentáció<p></td><td>Ez a típus egy Analysis Services mértéket jelöl.</td></tr><tr><td></td><td>intézkedés</td><td>Oszlop</td><td></td><td>A mértéket leíró metaadatok</td></tr><tr><td></td><td>isCalculated </td><td>Logikai</td><td></td><td>Meghatározza, hogy a mérték kiszámítva vagy sem.</td></tr><tr><td></td><td>measureGroup</td><td>Sztring</td><td></td><td>A mérték fizikai tárolója</td></tr><td>KPI (KPI-k)</td><td></td><td></td><td>Leírás<p>FriendlyName<p>Címke<p>Szakértő<p>AccessInstruction<p>Dokumentáció</td><td></td></tr><tr><td></td><td>measureGroup</td><td>Sztring</td><td></td><td>A mérték fizikai tárolója</td></tr><tr><td></td><td>goalExpression</td><td>Sztring</td><td></td><td>MDX numerikus kifejezés vagy számítás, amely visszaadja a KPI céljának értékét.</td></tr><tr><td></td><td>valueExpression</td><td>Sztring</td><td></td><td>MDX numerikus kifejezés, amely a KPI tényleges értékét adja vissza.</td></tr><tr><td></td><td>statusExpression</td><td>Sztring</td><td></td><td>Egy MDX-kifejezés, amely a KPI állapotát jelöli egy adott időpontban.</td></tr><tr><td></td><td>trendExpression</td><td>Sztring</td><td></td><td>MDX-kifejezés, amely kiértékeli a KPI értékét az idő múlásával. A trend bármilyen időalapú feltétel lehet, amely egy adott üzleti környezetben hasznos.</td>
<tr><td>Jelentés ("jelentések")</td><td></td><td></td><td>Leírás<p>FriendlyName<p>Címke<p>Szakértő<p>AccessInstruction<p>Dokumentáció<p></td><td>Ez a típus SQL Server Reporting Services jelentést jelöl </td></tr><tr><td></td><td>assetCreatedDate</td><td>Sztring</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Sztring</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Sztring</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Sztring</td><td></td><td></td></tr><tr><td>Tároló ("tárolók")</td><td></td><td></td><td>Leírás<p>FriendlyName<p>Címke<p>Szakértő<p>AccessInstruction<p>Dokumentáció<p></td><td>Ez a típus más eszközök tárolóját jelöli, például egy SQL-adatbázist, egy Azure BLOB-tárolót vagy egy Analysis Services modellt.</td></tr></table>

### <a name="annotation-types"></a>Jegyzetek típusai
A jegyzetek típusai a metaadatok olyan típusait jelölik, amelyek a katalógusban más típusokhoz rendelhetők.

<table>
<tr><td><b>Jegyzet típusa (beágyazott nézet neve)</b></td><td><b>További tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>

<tr><td>Leírás ("leírások")</td><td></td><td></td><td>Ez a tulajdonság egy eszköz leírását tartalmazza. A rendszer minden felhasználója saját leírást adhat hozzá.  Csak az adott felhasználó szerkesztheti a Leírás objektumot.  (A rendszergazdák és az eszközök tulajdonosai törölhetik a Leírás objektumot, de nem szerkeszthetik azt). A rendszeren külön kell karbantartani a felhasználók leírását.  Így a leírások tömbje minden eszközön megtalálható (egyet minden olyan felhasználó számára, aki az adatforrással kapcsolatos ismeretekkel járult hozzá, és az adatforrásból származtatott adatokat is tartalmazza).</td></tr>
<tr><td></td><td>leírás</td><td>sztring</td><td>Az eszköz rövid leírása (2-3 sor)</td></tr>

<tr><td>Címke ("címkék")</td><td></td><td></td><td>Ez a tulajdonság határozza meg az eszköz címkéjét. A rendszer minden felhasználója több címkét is hozzáadhat egy eszközhöz.  Csak a címkézett objektumokat létrehozó felhasználó szerkesztheti őket.  (A rendszergazdák és az eszközök tulajdonosai törölhetik a címke objektumot, de nem szerkeszthetik azt). A rendszeren a felhasználók címkéi külön vannak tárolva.  Így az egyes adategységeken található címkézett objektumok tömbje.</td></tr>
<tr><td></td><td>címke</td><td>sztring</td><td>Az eszközt leíró címke.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Ez a tulajdonság egy eszköz rövid nevét tartalmazza. A FriendlyName egy egyedi jegyzet – csak egy FriendlyName adható hozzá egy objektumhoz.  Csak a FriendlyName objektumot létrehozó felhasználó szerkesztheti. (A rendszergazdák és az eszközök tulajdonosai törölhetik a FriendlyName objektumot, de nem szerkeszthetik azt). A rendszeren a felhasználók felhasználóbarát nevei külön vannak tárolva.</td></tr>
<tr><td></td><td>friendlyName</td><td>sztring</td><td>Az eszköz rövid neve.</td></tr>

<tr><td>Séma ("séma")</td><td></td><td></td><td>A séma leírja az adat szerkezetét.  Felsorolja az attribútumot (oszlop, attribútum, mező stb.), a típusokat és a metaadatokat is.  Ezek az adatok az adatforrásból származnak.  A séma egy egyedi jegyzet – csak egy sémát lehet hozzáadni egy adott eszközhöz.</td></tr>
<tr><td></td><td>oszlopok</td><td>Oszlop []</td><td>Oszlopos objektumok tömbje. Leírják az oszlopot az adatforrásból származtatott adatokkal.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Ez a tulajdonság egy oszlop leírását tartalmazza.  A rendszer minden felhasználója saját leírásokat adhat hozzá több oszlophoz (legfeljebb egy oszlopra vetítve). Csak a ColumnDescription objektumokat létrehozó felhasználó szerkesztheti őket.  (A rendszergazdák és az eszközök tulajdonosai törölhetik a ColumnDescription objektumot, de nem szerkeszthetik azt). A rendszer külön kezeli ezeket a felhasználó oszlopának leírásait.  Így a ColumnDescription objektumok tömbje minden eszközön (egy oszlopban minden olyan felhasználó számára, aki az adatforrásból származtatott adatokat tartalmaz, valamint az oszlopra vonatkozó ismereteiket is tartalmazza).  A ColumnDescription lazán kötődik a sémához, így elkerülhet a szinkronizálás. Előfordulhat, hogy a ColumnDescription olyan oszlopot ír le, amely már nem létezik a sémában.  A leírás és a séma szinkronizálásban való megtartásához az író gondoskodik.  Az adatforrás tartalmazhat oszlopokra vonatkozó leírást is, amelyek további ColumnDescription objektumokat hoznak létre az eszköz futtatásakor.</td></tr>
<tr><td></td><td>columnName</td><td>Sztring</td><td>Annak az oszlopnak a neve, amelyre a Leírás hivatkozik.</td></tr>
<tr><td></td><td>leírás</td><td>Sztring</td><td>az oszlop rövid leírása (2-3 sor).</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Ez a tulajdonság egy oszlop címkéjét tartalmazza. A rendszer minden felhasználója több címkét is hozzáadhat egy adott oszlophoz, és több oszlophoz is hozzáadhat címkéket. Csak a ColumnTag objektumokat létrehozó felhasználó szerkesztheti őket. (A rendszergazdák és az eszközök tulajdonosai törölhetik a ColumnTag objektumot, de nem szerkeszthetik azt). A System ezeket a felhasználói oszlopokat külön-külön kezeli.  Így a ColumnTag objektumok tömbje minden eszközön megtalálható.  A ColumnTag lazán kötődik a sémához, így elkerülhet a szinkronizálás. Előfordulhat, hogy a ColumnTag olyan oszlopot ír le, amely már nem létezik a sémában.  Az oszlop címkéje és a sémája szinkronban tartása az író.</td></tr>
<tr><td></td><td>columnName</td><td>Sztring</td><td>Annak az oszlopnak a neve, amelyre a címke hivatkozik.</td></tr>
<tr><td></td><td>címke</td><td>Sztring</td><td>Az oszlopot leíró címke.</td></tr>

<tr><td>Expert ("szakértők")</td><td></td><td></td><td>Ez a tulajdonság olyan felhasználót tartalmaz, aki az adatkészletben szakértőnek számít. A szakértői vélemények (leírások) buborék az UX tetejére a leírások listázásakor. Minden felhasználó megadhatja saját szakértőit. Csak az adott felhasználó szerkesztheti a szakértői objektumot. (A rendszergazdák és az eszközök tulajdonosai törölhetik a szakértői objektumokat, de nem szerkeszthetik azt).</td></tr>
<tr><td></td><td>szakértői</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Előnézet ("előzetes verziók")</td><td></td><td></td><td>Az előnézet tartalmaz egy pillanatképet az eszköz legfontosabb 20 soráról. Az előzetes verzió csak bizonyos típusú eszközök esetében hasznos (ez a mérték értelme a táblázatnak, de nem használható).</td></tr>
<tr><td></td><td>preview</td><td>objektum []</td><td>Egy oszlopot jelölő objektumok tömbje.  Minden objektumhoz tartozik egy tulajdonság hozzárendelése egy olyan oszlophoz, amelynek értéke az adott oszlophoz tartozik.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>sztring</td><td>A tartalom MIME-típusa.</td></tr>
<tr><td></td><td>tartalom</td><td>sztring</td><td>Útmutató az adategységhez való hozzáféréshez. A tartalom lehet egy URL-cím, egy e-mail-cím vagy egy utasításkészlet.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Az adatkészletben lévő sorok száma</td></tr>
<tr><td></td><td>size</td><td>hosszú</td><td>Az adathalmaz mérete bájtban megadva.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>sztring</td><td>A séma legutóbbi módosításának időpontja</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>sztring</td><td>Az adatkészlet legutóbbi módosításának utolsó időpontja (az adathalmazok hozzáadása, módosítása vagy törlése)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>oszlopok</td></td><td>ColumnDataProfile[]</td><td>Oszlop típusú adatprofilok tömbje.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>Sztring</td><td>Annak az oszlopnak a neve, amelyre a besorolás vonatkozik.</td></tr>
<tr><td></td><td>besorolás</td><td>Sztring</td><td>Az ebben az oszlopban lévő adatbesorolás.</td></tr>

<tr><td>Dokumentáció ("dokumentáció")</td><td></td><td></td><td>Egy adott eszközhöz csak egy dokumentáció rendelhető hozzá.</td></tr>
<tr><td></td><td>mimeType</td><td>sztring</td><td>A tartalom MIME-típusa.</td></tr>
<tr><td></td><td>tartalom</td><td>sztring</td><td>A dokumentáció tartalma.</td></tr>

</table>

### <a name="common-types"></a>Gyakori típusok
A gyakori típusok a tulajdonságok típusaként használhatók, de nem elemek.

<table>
<tr><td><b>Gyakori típus</b></td><td><b>Tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Forrás típusa</td><td>sztring</td><td>Az adatforrás típusának leírása.  Például: SQL Server, Oracle Database stb.  </td></tr>
<tr><td></td><td>Objektumtípus</td><td>sztring</td><td>Az adatforrás objektumának típusát írja le. Például: tábla, SQL Server megtekintése.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protokoll</td><td>sztring</td><td>Kötelező. Az adatforrással való kommunikációhoz használt protokollt ismerteti. Például: `tds` SQL Server, `oracle` Oracle, stb. Tekintse meg az [adatforrás-hivatkozás specifikációja – DSL-struktúra](data-catalog-dsr.md) a jelenleg támogatott protokollok listájához című témakört.</td></tr>
<tr><td></td><td>address</td><td>Szótár &lt; karakterlánca, objektum&gt;</td><td>Kötelező. A címek a hivatkozott adatforrás azonosítására szolgáló protokollra jellemző adathalmazok. Egy adott protokoll hatóköre, ami azt jelenti, hogy a protokoll ismerete nélkül van.</td></tr>
<tr><td></td><td>hitelesítés</td><td>sztring</td><td>Választható. Az adatforrással való kommunikációhoz használt hitelesítési séma. Például: Windows, OAuth stb.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Szótár &lt; karakterlánca, objektum&gt;</td><td>Választható. További információ az adatforrásokhoz való kapcsolódásról.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>A háttérrendszer nem hajtja végre a megadott tulajdonságok érvényesítését Azure Active Directory a közzététel során.</td></tr>
<tr><td></td><td>UPN</td><td>sztring</td><td>A felhasználó egyedi e-mail-címe. Meg kell adni, ha a objectId nincs megadva vagy a "lastRegisteredBy" tulajdonság kontextusában, máskülönben nem kötelező.</td></tr>
<tr><td></td><td>objectId</td><td>Guid</td><td>Felhasználói vagy biztonsági csoport Azure Active Directory identitása. Választható. Meg kell adni, ha az UPN nincs megadva, ellenkező esetben nem kötelező.</td></tr>
<tr><td></td><td>firstName</td><td>sztring</td><td>A felhasználó vezetékneve (megjelenítési célra). Választható. Csak a "lastRegisteredBy" tulajdonság kontextusában érvényes. Nem adható meg a rendszerbiztonsági tag "szerepkörök", "engedélyek" és "szakértők" számára való megadásakor.</td></tr>
<tr><td></td><td>lastName</td><td>sztring</td><td>A felhasználó vezetékneve (megjelenítés céljából). Választható. Csak a "lastRegisteredBy" tulajdonság kontextusában érvényes. Nem adható meg a rendszerbiztonsági tag "szerepkörök", "engedélyek" és "szakértők" számára való megadásakor.</td></tr>

<tr><td>Oszlop</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>sztring</td><td>Az oszlop vagy attribútum neve.</td></tr>
<tr><td></td><td>típus</td><td>sztring</td><td>az oszlop vagy attribútum adattípusa. A megengedett típusok az eszköz forrás típusa függenek.  Csak a típusok egy részhalmaza támogatott.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>Az oszlop vagy attribútum számára engedélyezett maximális hossz. Az adatforrásból származtatva. Csak bizonyos típusú forrásokra érvényes.</td></tr>
<tr><td></td><td>pontosság</td><td>byte</td><td>Az oszlop vagy attribútum pontossága. Az adatforrásból származtatva. Csak bizonyos típusú forrásokra érvényes.</td></tr>
<tr><td></td><td>isNullable</td><td>Logikai</td><td>Azt határozza meg, hogy az oszlop tartalmazhat-e null értéket. Az adatforrásból származtatva. Csak bizonyos típusú forrásokra érvényes.</td></tr>
<tr><td></td><td>kifejezés</td><td>sztring</td><td>Ha az érték egy számított oszlop, akkor ez a mező tartalmazza az értéket kifejező kifejezést. Az adatforrásból származtatva. Csak bizonyos típusú forrásokra érvényes.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>sztring</td><td>Az oszlop neve</td></tr>
<tr><td></td><td>típus </td><td>sztring</td><td>Az oszlop típusa</td></tr>
<tr><td></td><td>p </td><td>sztring</td><td>Az adatkészletben szereplő minimális érték</td></tr>
<tr><td></td><td>max. </td><td>sztring</td><td>Az adatkészletben található maximális érték</td></tr>
<tr><td></td><td>AVG </td><td>double</td><td>Az adatkészletben lévő átlagos érték</td></tr>
<tr><td></td><td>szórás </td><td>double</td><td>Az adathalmaz szórása</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Az adatkészletben lévő null értékek száma</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Az adatkészletben lévő különböző értékek száma</td></tr>
</table>

## <a name="asset-identity"></a>Eszköz identitása
Azure Data Catalog a "protokoll" és a "DataSourceLocation" tulajdonságot használja a "DSL" tulajdonság "címe" tulajdonságának a "DSL" tulajdonságának az eszköz identitásának létrehozásához, amely a katalóguson belüli eszköz kezelésére szolgál.
A táblázatos adatfolyam (TDS) protokoll például a "kiszolgáló", az "adatbázis", a "Schema" és az "Object" azonosító tulajdonságokkal rendelkezik. A protokoll és az Identity tulajdonság kombinációi a SQL Server Table objektum identitásának előállítására szolgálnak.
A Azure Data Catalog számos beépített adatforrás-protokollt biztosít, amelyek az [adatforrás-hivatkozás specifikációja – DSL-struktúra](data-catalog-dsr.md)című részben találhatók.
A támogatott protokollok készletét programozott módon lehet kiterjeszteni (lásd: Data Catalog REST API-hivatkozás). A katalógus rendszergazdái regisztrálhatják az egyéni adatforrás-protokollokat. Az alábbi táblázat az egyéni protokoll regisztrálásához szükséges tulajdonságokat ismerteti.

### <a name="custom-data-source-protocol-specification"></a>Egyéni adatforrás protokolljának specifikációja
<table>
<tr><td><b>Típus</b></td><td><b>Tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>névtér</td><td>sztring</td><td>A protokoll névtere. A névtérnek 1 – 255 karakter hosszúnak kell lennie, és tartalmaznia kell egy vagy több, ponttal (.) elválasztott, nem üres részt. Minden résznek 1 – 255 karakter hosszúnak kell lennie, betűvel kell kezdődnie, és csak betűket és számokat tartalmazhat.</td></tr>
<tr><td></td><td>name</td><td>sztring</td><td>A protokoll neve. A névnek 1 – 255 karakter hosszúnak kell lennie, betűvel kell kezdődnie, és csak betűket, számokat és kötőjel (-) karaktert tartalmazhat.</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>Az azonosító tulajdonságok listájának legalább egy, de legfeljebb 20 tulajdonságot kell tartalmaznia. Például: a "kiszolgáló", az "adatbázis", a "Schema", az "Object" a "TDS" protokoll identitási tulajdonságai.</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>Az azonosítók listája. Meghatározza az azonosító tulajdonságok készletét, amely az érvényes eszköz identitását jelöli. Legalább egy, de legfeljebb 20 készletet kell tartalmaznia. A (z) {"Server", az "Database", a "Schema" és az "Object"} például a TDS protokoll identitási készlete, amely az SQL Server Table objektum identitását határozza meg.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>sztring</td><td>A tulajdonság neve. A névnek 1 – 100 karakter hosszúnak kell lennie, betűvel kell kezdődnie, és csak betűket és számokat tartalmazhat.</td></tr>
<tr><td></td><td>típus</td><td>sztring</td><td>A tulajdonság típusa Támogatott értékek: "bool", boolean "," byte "," GUID "," int "," Integer "," Long "," string "," URL "</td></tr>
<tr><td></td><td>ignoreCase</td><td>logikai</td><td>Azt jelzi, hogy a rendszer figyelmen kívül hagyja-e az esetet a tulajdonság értékének használatakor. Csak "string" típusú tulajdonságokhoz adható meg. Az alapértelmezett érték false (hamis).</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool []</td><td>Azt jelzi, hogy az esetet figyelmen kívül kell-e hagyni az URL elérési útjának minden egyes szegmense esetében. Csak "URL" típusú tulajdonságokhoz adható meg. Az alapértelmezett érték a [FALSE].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>sztring</td><td>Az identitás neve.</td></tr>
<tr><td></td><td>properties</td><td>karakterlánc []</td><td>Az identitási készletbe belefoglalt identitási tulajdonságok listája. Nem tartalmazhat duplikált elemeket. Az Identity set által hivatkozott minden tulajdonságot meg kell adni a protokoll "identityProperties" listájában.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Szerepkörök és engedélyezés
A Microsoft Azure Data Catalog engedélyezési képességeket biztosít a SZIFILISZi műveletekhez az eszközökön és a jegyzeteken.

A Azure Data Catalog két engedélyezési mechanizmust használ:

* Szerepköralapú hitelesítés
* Engedély alapú engedélyezés

### <a name="roles"></a>Szerepkörök
Három szerepkör létezik: **rendszergazda**, **tulajdonos** és **közreműködő**.  Az egyes szerepkörök hatókörét és jogosultságait az alábbi táblázat foglalja össze.

<table><tr><td><b>Szerepkör</b></td><td><b>Hatókör</b></td><td><b>Jogok</b></td></tr><tr><td>Rendszergazda</td><td>Catalog (az összes eszköz/jegyzet a katalógusban)</td><td>Olvasási ViewRoles törlése

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Tulajdonos</td><td>Minden eszköz (gyökérelem)</td><td>Olvasási ViewRoles törlése

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Közreműködő</td><td>Minden egyes eszköz és jegyzet</td><td>Olvasási ViewRoles frissítése Megjegyzés: a rendszer minden jogot visszavon, ha az elemre való olvasási jogosultságot visszavonják a Közreműködőtől</td></tr></table>

> [!NOTE]
> Az **olvasási**, **frissítési**, **törlési** és **ViewRoles** jogok bármely elemre (eszközre vagy jegyzetre) érvényesek, míg a **TakeOwnerShip**, a **ChangeOwnership**, a **ChangeVisibility**, a **ViewPermissions** csak a Főeszközre érvényes.
> 
> A **törlési** jogosultság egy elemre, illetve az alatta lévő alelemekre vagy egyetlen elemre vonatkozik. Egy eszköz törlése például törli az adott objektumhoz tartozó jegyzeteket is.
> 

### <a name="permissions"></a>Engedélyek
Az engedély a hozzáférés-vezérlési bejegyzések listája. Minden hozzáférés-vezérlési bejegyzés jogosultságokat rendel egy rendszerbiztonsági tag számára. Engedélyek csak egy eszközön (azaz gyökérelem) adhatók meg, és alkalmazhatók az eszközre és az alelemekre.

A **Azure Data Catalog** előzetes verziójában csak **olvasási** jogosultság támogatott az engedélyek listájában, hogy a forgatókönyvek lehetővé tegyék egy adott eszköz láthatóságának korlátozását.

Alapértelmezés szerint bármely hitelesített felhasználó **olvasási** joggal rendelkezik a katalógus bármely eleméhez, kivéve, ha a láthatóság az engedélyekben lévő rendszerbiztonsági tagok készletére korlátozódik.

## <a name="rest-api"></a>REST API
Szerepkörök és engedélyek vezérlésére szolgáló **put** és **post** nézetbeli elemek kérése: az elem hasznos adatai mellett két rendszertulajdonság is megadható **szerepkörök** és **engedélyek**.

> [!NOTE]
> az **engedélyek** csak a legfelső szintű elemek esetében érvényesek.
> 
> A **tulajdonosi** szerepkör csak a legfelső szintű elemek esetében alkalmazható.
> 
> Alapértelmezés szerint amikor létrejön egy elemet a katalógusban, annak **közreműködői** beállítása a jelenleg hitelesített felhasználó. Ha az elemnek mindenki számára elérhetőnek kell lennie, a **közreműködőt** &lt; &gt; a **szerepkörök** tulajdonságban található mindenki speciális rendszerbiztonsági tag értékre kell állítani az elem közzétételekor. A **közreműködő** nem módosítható, és az elem élettartama alatt változatlan marad (még akkor is, ha a **rendszergazda** vagy a **tulajdonos** nem jogosult a **közreműködő** módosítására). Az egyetlen támogatott érték a **közreműködői** explicit beállítása esetén &lt; : a &gt; **közreműködő** csak olyan felhasználó lehet, aki létrehozta az elemeket vagy &lt; mindenkit &gt; .
> 

### <a name="examples"></a>Példák
**&lt;Az elemek közzétételekor adja meg mindenki számára a közreműködőt &gt; .**
A speciális rendszerbiztonsági tag &lt; mindenkinek &gt; van "00000000-0000-0000-0000-000000000201" objectId.
  Https- **üzenet** : \/ /API.azuredatacatalog.com/Catalogs/default/views/Tables/?API-Version=2016-03-30

> [!NOTE]
> Néhány HTTP-ügyfél implementációja automatikusan kiadhatja a kérelmeket egy 302-re adott válaszként a kiszolgálóról, de általában a kérelemben szereplő engedélyezési fejléceket is. Mivel az engedélyezési fejléc szükséges ahhoz, hogy Azure Data Catalog kérelmeket, meg kell győződnie arról, hogy az engedélyezési fejléc továbbra is meg van adva, amikor a Azure Data Catalog által megadott átirányítási helyre küldi a kérelmet. A következő mintakód a .NET HttpWebRequest objektum használatával mutatja be.
> 

**Törzs**
```json
    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }
```

  **Tulajdonosok kiosztása és a láthatóság korlátozása meglévő gyökérszintű elemek esetében**: **put** https: \/ /API.azuredatacatalog.com/Catalogs/default/views/Tables/042297b0...1be45ecd462a?API-Version=2016-03-30

```json
    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }
```

> [!NOTE]
> A PUT-ben nem szükséges egy elemek adattartalomának megadása a törzsben: PUT – csak szerepkörök és/vagy engedélyek frissítésére használható.
> 

## <a name="next-steps"></a>Következő lépések
[Azure Data Catalog REST API-hivatkozás](/rest/api/datacatalog/)
