---
title: 'Oktatóanyag: adategységek regisztrálása a Azure Data Catalogban'
description: Ez az oktatóanyag leírja, hogyan regisztrálhat adategységeket a Azure Data Catalogban.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: de85cad8232d76706358c36806ce19a7c1df893f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675005"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Oktatóanyag: adategységek regisztrálása a Azure Data Catalogban

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Ebben az oktatóanyagban a regisztrációs eszközzel regisztrálja az adategységeket az adatbázis mintából a katalógus használatával. A regisztráció az a folyamat, amelynek során az adatforrásból és a benne található adategységekből kinyert fő szerkezeti metaadatok (például nevek, típusok és helyek) hozzáadódnak a katalógushoz. Az adatforrás és az adategységek az eredeti helyükön maradnak, de a metaadatok használatával a katalógus könnyebben feltárhatóvá és értelmezhetővé teszi őket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Adategységek regisztrálása 
> * Adategységek keresése
> * Adategységek ellátása dekorációkkal
> * Csatlakozás adategységekhez
> * Adategységek felügyelete
> * Adategységek törlése

## <a name="prerequisites"></a>Előfeltételek

Első lépésként el kell végeznie a [gyors](register-data-assets-tutorial.md)üzembe helyezést.

* Egy [Microsoft Azure](https://azure.microsoft.com/)-előfizetéssel.
* Saját [Azure Active Directory Bérlővel](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)kell rendelkeznie.

Data Catalog beállításához az Azure-előfizetés tulajdonosának vagy tulajdonosának kell lennie.

## <a name="register-data-assets"></a>Adategységek regisztrálása

### <a name="register-a-data-source"></a>Adatforrás regisztrálása

Az adategységeket (táblákat) a Azure SQL Database [adatbázis-mintából](../azure-sql/database/single-database-create-quickstart.md) regisztrálja, de bármilyen támogatott adatforrást használhat, ha a szerepköre ismerős és releváns adatokkal szeretne dolgozni. A támogatott adatforrások listájáért lásd: [Supported data sources](data-catalog-dsr.md) (Támogatott adatforrások).

Az oktatóanyagban használt *RLSTest*.

Mostantól a Azure Data Catalog használatával regisztrálhat adategységeket az adatbázis mintából.

1. Lépjen a [Azure Data Catalog kezdőlapjára](http://azuredatacatalog.com) , majd válassza az **adatközzététel** lehetőséget.

   ![Azure Data Catalog – Adatok közzététele gomb](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. Válassza az **alkalmazás elindítása** lehetőséget a regisztrációs eszköz letöltéséhez, telepítéséhez és futtatásához a számítógépen.

   ![Azure Data Catalog – Indítás gomb](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Az **Üdvözöljük** lapon válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait.

    ![Azure Data Catalog – Kezdőlap](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. A **Microsoft Azure Data Catalog** lapon válassza a **SQL Server** és a **tovább** lehetőséget.

    ![Azure Data Catalog – adatforrások](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Adja meg az adatbázis-minta SQL Server kapcsolati tulajdonságait Azure SQL Database és válassza a **kapcsolat** lehetőséget.

   ![Azure Data Catalog – SQL Server-kapcsolati tulajdonságok](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Regisztrálja az adategység metaadatait. Ebben a példában a **termék** objektumait regisztrálja a minta névterből:

   1. A **kiszolgálói hierarchia** fáján bontsa ki az adatbázis mintát, és válassza a **SalesLT** lehetőséget.

   2. Válassza a **termék**, a **ProductCategory**, a **ProductDescription** és a **ProductModel** lehetőséget a CTRL + Select billentyűkombinációval.

   3. Válassza a **mozgatás kijelölt nyilat** ( **>** ). Ez a művelet az összes kiválasztott objektumot áthelyezi az **Objects to be registered** (Regisztrálandó objektumok) listára.

      ![Azure Data Catalog oktatóanyag – objektumok megkeresése és kiválasztása](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

   4. Ha szeretne pillanatképes előnézetet is hozzáadni az adatokról, jelölje be az **Előnézet belefoglalása** jelölőnégyzetet. A pillanatkép legfeljebb 20 rekordot tartalmaz az egyes táblákból, és a katalógusba másolja.

   5. Ha szeretné, hogy az adatprofil objektumstatisztikáit (például: minimális, maximális és átlagos értékek az oszlopban, sorok száma) bemutató pillanatképet is tartalmazza a katalógus, válassza az **Adatprofil belefoglalása** lehetőséget.

   6. A **Címkék hozzáadása** mezőben adja meg a **Sales, a Product, az Azure SQL** értéket. Ez a művelet hozzáadja az adategységekhez a keresési címkéket. A címkék nagy segítséget nyújtanak abban, hogy a felhasználók megtaláljanak egy bizonyos regisztrált adatforrást.

   7. Adja meg az adatokhoz kapcsolódó **szakértő** nevét (kihagyható).

      ![Azure Data Catalog oktatóanyag – regisztrálandó objektumok](media/register-data-assets-tutorial/data-catalog-objects-register.png)

   8. Válassza a **regisztráció** lehetőséget. Az Azure Data Catalog regisztrálja a kiválasztott objektumokat. Ebben a gyakorlatban az adatbázis mintából kiválasztott objektumok regisztrálva vannak. A regisztrációs eszköz kinyeri a metaadatokat az adategységből, majd az Azure Data Catalogba másolja az adatokat. Az adatmennyiség marad, ahol jelenleg is marad. Az adat a forrásrendszer rendszergazdáinak és házirendjeinek felügyelete alatt marad.

      ![Azure Data Catalog – regisztrált objektumok](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. A regisztrált adatforrás-objektumok megtekintéséhez válassza a **portál megtekintése** lehetőséget. A Azure Data Catalog-portálon győződjön meg arról, hogy mind a négy tábla, mind az adatbázis megjelenik a rács nézetben (ellenőrizze, hogy a keresősáv üres-e).

       ![Objektumok az Azure Data Catalog-portálon](media/register-data-assets-tutorial/data-catalog-view-portal.png)

Ebben a gyakorlatban a Azure SQL Database adatbázis-mintájában regisztrált objektumokat, hogy azok könnyen felismerhetők legyenek a felhasználók számára a szervezeten belül.

A következő gyakorlatból elsajátíthatja, hogyan találhatja meg a regisztrált adategységeket.

## <a name="discover-data-assets"></a>Adategységek felderítése

Az Azure Data Catalog felderítési funkciója elsődlegesen két mechanizmust használ: keresés és szűrés.

A keresés nem csupán magától értetődő, de rendkívül hatékony is. Alapértelmezés szerint a keresőkifejezéseket a rendszer összeveti a katalógusban szereplő összes tulajdonsággal, még a felhasználók által beírt dekorációkkal is.

A szűrés a keresést hivatott kiegészíteni. Különböző jellemzőket (például szakértők, adatforrástípusok, objektumtípusok és címkék) adhat meg, és megtekintheti az azokkal egyező adategységeket, illetve az egyezést mutató adategységekre szűkítheti a találatokat.

A keresés és szűrés együttes használatával gyorsan navigálhatja a Azure Data Catalogban regisztrált adatforrásokat.

Ebben a gyakorlatban az Azure Data Catalog-portál segítségével fogja felderíteni az előző gyakorlat során regisztrált adategységeket. A keresési szintaxissal kapcsolatban lásd: [Data Catalog Search syntax reference](/rest/api/datacatalog/#search-syntax-reference) (A Data Catalog keresési szintaxisának leírása).

Az alábbiakban különböző példákat hozunk a katalógusban szereplő adategységek felderítésére.  

### <a name="discover-data-assets-with-basic-search"></a>Adategységek felderítése az alapszintű kereséssel

Az alapszintű kereséssel egy vagy több keresőkifejezést megadva végezhet keresést a katalógusban. Megjelenik az összes olyan adategység, amelynek tulajdonságai egyeznek egy vagy több megadott kifejezéssel.

1. Válassza a **Kezdőlap** lehetőséget a Azure Data Catalog portálon. Ha bezárta a böngészőt, lépjen a [Azure Data Catalog kezdőlapjára](https://www.azuredatacatalog.com).

2. A keresőmezőbe írja be a `product` kifejezést, majd nyomja le az **ENTER** billentyűt.

   ![Azure Data Catalog – alapszintű szöveges keresés](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Győződjön meg arról, hogy az eredmények között mind a négy tábla, mind az adatbázis megjelenik. A **rács nézet** és a **listanézet** közötti váltáshoz válassza az eszköztár gombjait, ahogy az alábbi képen is látható. Figyelje meg, hogy a kereséshez használt kulcsszót a rendszer kijelöli a találatok között. Ez azért van, mert a **Kiemelés** funkció **BE** értékre van állítva. Megadhatja az **eredmények száma oldalanként** beállítás értékét is.

   ![Azure Data Catalog – alapszintű szöveges keresés, találatok](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

   A **Keresések** panel bal oldalon, a **Tulajdonságok** panel jobb oldalon található. A **Keresések** panelen módosíthatja a keresési feltételeket, valamint szűrheti a találatokat. A **Tulajdonságok** panelen láthatja a rácsban vagy a listában kiválasztott objektum tulajdonságait.

4. A keresési eredmények között válassza a **Product (termék** ) lehetőséget. Válassza ki az **előnézet**, az **oszlopok**, **az adatprofil** és a **dokumentáció** lapokat, vagy a nyílra kattintva bontsa ki az alsó ablaktáblát.  

   ![Azure Data Catalog – alsó panel](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

   A **Preview** (Előnézet) lapon a **Product** táblában szereplő adatok előnézete látható.

5. Válassza az **oszlopok** fület az adategységben található oszlopok (például a **név** és **az adattípus**) részleteinek megtekintéséhez.

6. Válassza az **adatprofil** fület, és tekintse meg az adategységben található adatprofilkészítést (például: sorok száma, az adatmennyiség vagy az oszlop minimális értéke).

### <a name="discover-data-assets-with-property-scoping"></a>Adategységek felderítése tulajdonságértékekben való kereséssel

A tulajdonságértékekben való keresés segítségével megkeresheti azokat az adategységeket, amelyeknél a keresőkifejezés és a megadott tulajdonság között egyezés található.

1. A **Szűrők** menü **Objektumtípus** szűrőjének **Tábla** beállításánál törölje az oda korábban esetleg beírt értéket.  

2. A keresőmezőbe írja be a `tags:product` kifejezést, majd nyomja le az **ENTER** billentyűt. Az adatkatalógusban való kereséshez használható összes tulajdonság megtekintéséhez lásd: [Data Catalog Search syntax reference](/rest/api/datacatalog/#search-syntax-reference) (A Data Catalog keresési szintaxisának leírása).

3. Győződjön meg róla, hogy a táblák és az adatbázis megjelenik az eredmények között.  

   ![Data Catalog – tulajdonságértékekben való keresés, találatok](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>A keresés mentése

1. **A keresések ablaktáblán** az **aktuális keresés** szakaszban adja meg a keresés nevét, majd válassza a **Mentés** lehetőséget.

   ![Azure Data Catalog – keresés mentése](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. Ellenőrizze, hogy a mentett keresés megjelent-e a **Mentett keresések** menüben.

3. Válasszon a mentett kereséseken elvégezhető műveletek közül (**Átnevezés**, **Törlés**, **Mentés alapértelmezettként**).

### <a name="grouping-with-parentheses"></a>Csoportosítás zárójelekkel

Zárójelek segítségével csoportosítva különböző logikai egységekre bonthatja a lekérdezés részeit, ami különösen a logikai operátorokkal együtt alkalmazva lehet hasznos.

1. A keresőmezőbe írja be a `name:product AND (tags:product AND objectType:table)` kifejezést, majd nyomja le az **ENTER** billentyűt.

2. Ellenőrizze, hogy a találatok között csak a **Product** tábla jelent-e meg.

   ![Azure Data Catalog – keresések csoportosítása](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>Összehasonlító operátorok

Az összehasonlító operátorok segítségével a szám és adat adattípusú tulajdonságok esetében a nem egyenlő összehasonlítások is használhatók.

1. A keresőmezőbe írja be a `lastRegisteredTime:>"06/09/2016"` kifejezést.

2. Az **Objektumtípus** szűrő **Tábla** beállításánál törölje az oda korábban esetleg beírt értéket.

3. Nyomja le az **ENTER** billentyűt.

4. Ellenőrizze, hogy megjelenik-e a **termék**, a **ProductCategory** és a **ProductDescription** tábla, valamint a keresési eredmények között regisztrált SQL-adatbázis.

   ![Azure Data Catalog – összehasonlító találatok](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Az adategységek felderítésével kapcsolatos részletes információkért tekintse [meg az adategységek észlelését](data-catalog-how-to-discover.md) ismertető témakört. A keresési szintaxissal kapcsolatos további információkért lásd: [Data Catalog keresési szintaxis referenciája](/rest/api/datacatalog/#search-syntax-reference).

## <a name="annotate-data-assets"></a>Adategységek ellátása dekorációkkal

Ebben a gyakorlatban a Azure Data Catalog portál használatával jegyzeteket adhat hozzá (például leírásokat, címkéket vagy szakértőket) a katalógusban lévő adategységekhez. A jegyzetek kiegészítik az adatforrásból a regisztrálás során kinyert szerkezeti metaadatokat. A jegyzet az adategységeket sokkal könnyebben deríti fel és értelmezni.

Ebben a gyakorlatban egyetlen adategységet (ProductPhoto) fogunk dekorációkkal ellátni. Hozzáadunk egy rövid nevet és egy leírást a ProductPhoto adategységhez.  

1. Nyissa meg a [Azure Data Catalog kezdőlapját](https://www.azuredatacatalog.com) , és keresse meg a t a `tags:product` regisztrált adategységek kereséséhez.

2. A keresési eredmények között válassza a **ProductModel** lehetőséget.  

3. A **Rövid név** mezőbe írja be: **Product images**, a **Leírás** mezőbe pedig a következőt: **Product photos for marketing materials**.

    ![Azure Data Catalog – ProductPhoto leírása](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    A **Description** (Leírás) mező segít másoknak megismerni és értelmezni, hogy miért és hogyan használják a kiválasztott adategységet. Lehetősége van további címkék hozzáadására és oszlopok megtekintésére is. Az adatforrásokat a katalógushoz hozzáadott leíró metaadatok használatával keresheti meg és szűrheti.

Ezen az oldalon a következő lépéseket is végrehajthatja:

* Szakértőket adhat az adategységekhez. a **szakértők** területen válassza a **Hozzáadás** lehetőséget.

* Címkéket adhat hozzá az adatkészlet szintjén. a **címkék** területen válassza a **Hozzáadás** lehetőséget. A címke lehet felhasználói vagy szószedetcímke. A Data Catalog Standard kiadása tartalmaz egy üzleti szószedetet is, amelynek segítségével a katalógus-rendszergazdák megalkothatják a központi üzleti elnevezési rendszert. A katalógus felhasználói ezután a szószedet kifejezéseivel jelölhetik meg az adategységeket. További információ: [az üzleti Szószedet beállítása az irányított címkézéshez](data-catalog-how-to-business-glossary.md)

* Címkéket adhat hozzá az oszlopok szintjén. Válassza a **Hozzáadás** lehetőséget a **címkék** területen a jegyzetként használni kívánt oszlophoz.

* Leírást adhat hozzá az oszlopok szintjén. Töltse ki az oszlop **Leírás** mezőjét. Megtekintheti az adatforrásból kinyert leírási metaadatokat.

* Adjon hozzá **Hozzáférés kérése** típusú információkat, amelyekből a felhasználók megtudhatják, hogyan kérhetnek hozzáférést az adategységhez.
  
* Válassza a **Dokumentáció** lapfület, és adja meg az adategységhez tartozó dokumentációt. Az Azure Data Catalog dokumentációs funkciójával az adatkatalógus egyben tartalomtárként is használható, amely bemutatja az adategységek közötti összefüggéseket is.
  
Ugyanazt a dekorációt egyszerre több adategységhez is hozzáadhatja. Kiválaszthatja például a korábban regisztrált összes adategységet, és megadhat hozzájuk egy szakértőt.

![Azure Data Catalog – dekoráció készítése több adategységhez](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Az Azure Data Catalog közösségi kiszervezést alkalmazó megközelítést használ a dekorációknál. Bármely Data Catalog felhasználó hozzáadhat címkéket (felhasználót vagy szószedetet), leírásokat és egyéb metaadatokat is. Így a felhasználók perspektívát adhatnak egy adategységhez és annak használatához, és megoszthatják ezt a perspektívát más felhasználókkal.

Az adategységek dekorációkkal való ellátásával kapcsolatban [az adategységek dekorálását bemutató cikkben](data-catalog-how-to-annotate.md) olvashat részletesen.

## <a name="connect-to-data-assets"></a>Csatlakozás adategységekhez

Ebben a gyakorlatban a kapcsolatadatok segítségével az adategységeket egy integrált ügyféleszközben (Excel) és egy nem integrált eszközben (SQL Server Management Studio) is meg fogja nyitni.

> [!NOTE]
> Nem szabad elfelejteni, hogy az Azure Data Catalog nem ad hozzáférést magához az adatforráshoz, csupán megkönnyíti a felderítését és a megértését. Amikor Ön csatlakozik egy adatforráshoz, a választott ügyfélalkalmazás a windowsos hitelesítési adatokat fogja használni, illetve ha szükséges, felszólítja Önt a hitelesítési adatok megadására. Ha korábban még nem kapott hozzáférést az adatforráshoz, akkor a csatlakozáshoz hozzáférést kell kapnia.

### <a name="connect-to-a-data-asset-from-excel"></a>Csatlakozás adategységhez az Excelből

1. A találatok közül válassza ki a **Product** elemet. Válassza az eszköztár **Megnyitás elemét** , majd válassza az **Excel** lehetőséget.

   ![Azure Data Catalog – csatlakozás adategységhez](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. Válassza a **Megnyitás** lehetőséget a letöltési előugró ablakban. Ez böngészőnként eltérően jelenhet meg.

3. A **Microsoft Excel biztonsági figyelmeztetési** ablakban válassza az **Engedélyezés** lehetőséget.

   ![Azure Data Catalog – Excel biztonsági előugró ablak](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. Tartsa meg az alapértelmezett értékeket az **adatimportálás** párbeszédpanelen, majd kattintson **az OK gombra**.

   ![Azure Data Catalog – Excel, adatok beolvasása](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Tekintse meg az adatforrást az Excelben.

   ![Azure Data Catalog – terméktábla az Excelben](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Ebben a gyakorlatban az Azure Data Catalog segítségével felderített adategységekhez fog csatlakozni. Az Azure Data Catalog-portál segítségével közvetlenül, a **Open in** (Megnyitás a következőben) menüben elérhető integrált ügyfélalkalmazásokkal végezheti el a csatlakozást. Ezenfelül tetszőleges alkalmazáshoz csatlakozhat az adategység metaadataiban szereplő csatlakozási helyadatok segítségével. Használhatja például a SQL Server Management Studiot a Azure SQL Databasehoz való kapcsolódáshoz az ebben az oktatóanyagban regisztrált adategységekben lévő adatok eléréséhez.

1. Nyissa meg **SQL Server Management Studio**.

2. A **Kapcsolódás kiszolgálóhoz** párbeszédpanelen írja be a kiszolgáló nevét az Azure Data Catalog portál **Tulajdonságok** paneljéről.

3. Adja meg az adategység eléréséhez szükséges megfelelő hitelesítési és bejelentkezési adatokat. Ha nincs hozzáférése, használja a **Hozzáférés kérése** mezőben szereplő adatokat, és kérjen hozzáférést.

   ![Azure Data Catalog – hozzáférés kérése](media/register-data-assets-tutorial/data-catalog-request-access.png)

A **kapcsolódási karakterláncok megtekintése** lehetőség kiválasztásával megtekintheti és átmásolhatja a vágólapra az alkalmazásban használandó ADO.net, ODBC és OLEDB kapcsolódási karakterláncokat.

## <a name="manage-data-assets"></a>Adategységek felügyelete

Ebben a lépésben bemutatjuk, hogyan állítható be az adategységek biztonsága. Data Catalog nem biztosít hozzáférést a felhasználóknak az adatszolgáltatáshoz. Az adatok elérését az adatforrás tulajdonosa szabályozza.

A Data Catalog szolgáltatás arra szolgál, hogy adatforrásokat derítsen fel, és megtekintse a katalógusban regisztrált forrásokhoz tartozó metaadatokat. Előfordulhat azonban, hogy az adatforrásokat csak bizonyos felhasználók, illetve adott csoportok tagjai számára szeretné láthatóvá tenni. Ezekben az esetekben a Data Catalog használatával veheti át a regisztrált adategységek tulajdonjogát, és szabályozhatja a tulajdonában lévő eszközök láthatóságát.

> [!NOTE]
> Az ebben a gyakorlatban leírt felügyeleti funkciók kizárólag az Azure Data Catalog Standard kiadásában érhetők el, az ingyenes kiadásban nem.
> Az Azure Data Catalog lehetőséget nyújt az adategységek saját tulajdonba vételére, társtulajdonosok hozzáadására, valamint az adategységek láthatóságának beállítására.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Adategységek birtokbavétele és láthatóságának korlátozása

1. Nyissa meg az [Azure Data Catalog honlapját](https://www.azuredatacatalog.com). A keresés szövegmezőbe írja be a **kifejezést** , `tags:cycles` majd nyomja le az **ENTER** billentyűt.

2. Válasszon ki egy elemet az eredmények listájában, és válassza az eszköztáron a saját **tulajdonba vétel** lehetőséget.

3. A **Tulajdonságok** panel **felügyelet** szakaszában válassza a saját **tulajdonba vétel** lehetőséget.

    ![Azure Data Catalog – saját tulajdonba vétel](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. A láthatóság korlátozásához válassza a **tulajdonosok & ezeket a felhasználókat** a **láthatóság** szakaszban, és válassza a **Hozzáadás** lehetőséget. A szövegmezőbe írja be a kívánt e-mail-címet, majd nyomja le az **ENTER** billentyűt.

    ![Azure Data Catalog – hozzáférés korlátozása](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Adategységek eltávolítása

Ebben a gyakorlatban az Azure Data Catalog-portál használatával el fogja távolítani az előnézeti adatokat a regisztrált adategységekből, valamint adategységeket fog törölni a katalógusból.

Az Azure Data Catalogban az adategységek egyesével és csoportosan is törölhetők.

1. Nyissa meg az [Azure Data Catalog honlapját](https://www.azuredatacatalog.com).

2. A keresés szövegmezőbe írja be a **kifejezést** , `tags:cycles` majd kattintson az ENTER ( **bevitel**) gombra.

3. Válasszon ki egy elemet az eredmények listájában, és válassza a **Törlés** lehetőséget az eszköztáron az alábbi ábrán látható módon:

   ![Azure Data Catalog – rácselem törlése](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

   Ha a listanézet használatát használja, a jelölőnégyzet az elem bal oldalán látható, az alábbi képen látható módon:

   ![Azure Data Catalog – listaelem törlése](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

   Ahogy az az alábbi képen is látható, egyszerre több adategységet is kiválaszthat:

   ![Azure Data Catalog – több adategység törlése](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> A katalógus alapértelmezett viselkedése szerint bármely felhasználó regisztrálhat bármilyen adatforrást, és bármely felhasználó törölhet bármilyen regisztrált adatforrást. Az Azure Data Catalog Standard kiadásának felügyeleti lehetőségei további lehetőségeket nyújtanak, amelyekkel az adategységek saját tulajdonba vehetők, és korlátozható az adategységeket felderíteni és törölni jogosult felhasználók köre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Kövesse az [adategységek eltávolítása](#remove-data-assets) lépéseit az oktatóanyag követése során esetlegesen használt eszközök törléséhez.

## <a name="summary"></a>Összefoglalás

Ebben az oktatóanyagban bemutattuk az Azure Data Catalog alapvető funkcióit, például a regisztrálást, a dekorálást, a felderítést és a vállalati adategységek felügyeletét. Most, hogy teljesítette az oktatóprogramot, ideje megkezdeni a használatot. Regisztrálja az adatforrásokat, amelyeket a csapatával használ, és hívja fel a munkatársai figyelmét is a katalógus használatára.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Támogatott adatforrások](data-catalog-dsr.md)
