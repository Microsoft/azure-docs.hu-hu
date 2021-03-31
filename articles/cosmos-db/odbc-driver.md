---
title: Kapcsolódás Azure Cosmos DB BI Analytics-eszközök használatával
description: Ebből a témakörből megtudhatja, hogyan hozhat létre táblázatokat és nézeteket a Azure Cosmos DB ODBC-illesztővel, hogy a normalizált információkat BI-és adatelemzési szoftverekben lehessen megtekinteni.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: e7d6a67f5322c5bb640430f66ccb0917f6faada1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003497"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Kapcsolódás Azure Cosmos DB BI Analytics-eszközökkel az ODBC-illesztővel
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Az Azure Cosmos DB ODBC-illesztő lehetővé teszi, hogy olyan BI Analytics-eszközökkel kapcsolódjon Azure Cosmos DBhoz, mint például a SQL Server Integration Services, a Power BI Desktop és a tabló, így az ezekben a megoldásokban lévő Azure Cosmos DB-adataikat is elemezheti és létrehozhatja.

Az Azure Cosmos DB ODBC-illesztő kompatibilis az ODBC 3.8-as verzióval, és támogatja az ANSI SQL-92 szintaxist. Az illesztő rengeteg funkciót tartalmaz, amelyek segítségével újranormalizálhatja az Azure Cosmos DB adatait. Az illesztő használatával az Azure Cosmos DB-ben található adatokat táblaként és nézetként jelenítheti meg. Az illesztő lehetővé teszi az SQL-műveletek elvégzését a táblákon és nézeteken, például a lekérdezések szerinti csoportosítást, beszúrást, frissítést és törlést.

> [!NOTE]
> Az ODBC-illesztővel való Azure Cosmos DBhoz való csatlakozás jelenleg csak Azure Cosmos DB SQL API-fiókok esetén támogatott.

## <a name="why-do-i-need-to-normalize-my-data"></a>Miért kell normalizálni az adataimat?
Azure Cosmos DB egy séma nélküli adatbázis, amely lehetővé teszi az alkalmazások gyors fejlesztését és az adatmodellek megismétlését anélkül, hogy szigorú sémára kellene korlátozódnia. Egyetlen Azure Cosmos-adatbázis tartalmazhat különböző szerkezetű JSON-dokumentumokat. Ez nagyszerűen használható az alkalmazások gyors fejlesztéséhez, de ha az adatelemzés és a BI-eszközök használatával szeretné elemezni és létrehozni az adatait, az adatmennyiséget gyakran össze kell egyengetni, és be kell tartani egy adott sémát.

Itt jön az ODBC-illesztő. Az ODBC-illesztővel mostantól az adatelemzési és jelentéskészítési igényeknek megfelelő táblákba és nézetekben is újranormalizálhatja Azure Cosmos DB adatait. A normalizált sémák nem befolyásolják a mögöttes adatokat, és nem szűkítik a fejlesztőket a számukra való betartásra. Ehelyett lehetővé teszik az ODBC-kompatibilis eszközök kihasználása az adateléréshez. Így most az Azure Cosmos-adatbázis nem csak a kedvenc fejlesztői csapatának, hanem az adatelemzők is szeretik.

Ismerkedjen meg az ODBC-illesztővel.

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>1. lépés: az Azure Cosmos DB ODBC-illesztő telepítése

1. Töltse le a környezetéhez tartozó illesztőprogramokat:

    | Telepítő | Támogatott operációs rendszerek| 
    |---|---| 
    |[Microsoft Azure Cosmos db ODBC-64-bit.msi](https://aka.ms/cosmos-odbc-64x64) a 64 bites Windowshoz| a Windows 8,1 vagy újabb, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 és Windows Server 2008 R2 64 bites verziói.| 
    |[Microsoft Azure Cosmos db ODBC-32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64) a 32 bites rendszerhez 64 bites Windows rendszeren| 64 – Windows 8,1 vagy újabb verziók, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 és Windows Server 2003.| 
    |[Microsoft Azure Cosmos db ODBC-32-bit.msi](https://aka.ms/cosmos-odbc-32x32) a 32 bites Windowshoz|a Windows 8,1 vagy újabb, Windows 8, Windows 7, Windows XP és Windows Vista 32 bites verziói.|

    Futtassa helyileg az msi-fájlt, amely elindítja az **Microsoft Azure Cosmos db ODBC illesztőprogram-telepítő varázslót**. 

1. Az ODBC-illesztő telepítéséhez az alapértelmezett bemenettel végezze el a telepítési varázslót.

1. Nyissa meg az **ODBC adatforrás-felügyeleti** alkalmazást a számítógépen. Ezt úgy teheti meg, hogy beírja az **ODBC-adatforrásokat** a Windows keresési mezőjébe. 
    Az illesztőprogram telepítésének megerősítéséhez kattintson az **illesztőprogramok** lapra, és győződjön meg arról, **Microsoft Azure Cosmos db ODBC-illesztő** szerepel a felsorolásban.

    :::image type="content" source="./media/odbc-driver/odbc-driver.png" alt-text="Azure Cosmos DB ODBC-adatforrás rendszergazdája":::

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>2. lépés: Kapcsolódás az Azure Cosmos-adatbázishoz

1. [Az Azure Cosmos db ODBC-illesztő telepítése](#install)után az **ODBC-adatforrás rendszergazdája** ablakban kattintson a **Hozzáadás** gombra. Létrehozhat egy felhasználói vagy rendszer-DSN-t. Ebben a példában felhasználói ADATFORRÁSNEVET hoz létre.

1. Az **új adatforrás létrehozása** ablakban válassza ki **Microsoft Azure Cosmos db ODBC-illesztő** elemet, majd kattintson a **Befejezés** gombra.

1. Az **Azure Cosmos db ODBC-ILLESZTŐ Sdn beállítása** ablakban adja meg a következő információkat: 

    :::image type="content" source="./media/odbc-driver/odbc-driver-dsn-setup.png" alt-text="Azure Cosmos DB ODBC-illesztő DSN beállítása ablak":::
    - Adatforrás **neve**: az ODBC-DSN saját felhasználóbarát neve. Ez a név egyedi a Azure Cosmos DB-fiókban, ezért a megfelelő módon nevezze el, ha több fiókja van.
    - **Description (Leírás**): az adatforrás rövid leírása.
    - **Gazdagép**: a Azure Cosmos db-fiókhoz tartozó URI. Ezt a Azure Portal Azure Cosmos DB kulcsok lapjáról kérheti le, ahogy az alábbi képernyőképen is látható. 
    - **Hozzáférési kulcs**: a Azure Portal Azure Cosmos db kulcsok oldalának elsődleges vagy másodlagos, írható-olvasható vagy írásvédett kulcsa, ahogy az alábbi képernyőfelvételen is látható. Javasoljuk, hogy a csak olvasási jogosultsággal rendelkező kulcsot használja, ha a DSN csak olvasható adatfeldolgozásra és jelentéskészítésre szolgál.
    :::image type="content" source="./media/odbc-driver/odbc-cosmos-account-keys.png" alt-text="Azure Cosmos DB kulcsok lap":::
    - **Hozzáférési kulcs titkosítása**: válassza ki a legjobb választást a számítógép felhasználói alapján. 
    
1. Kattintson a **teszt** gombra, és ellenőrizze, hogy tud-e csatlakozni a Azure Cosmos db-fiókjához. 

1.  Kattintson a **Speciális beállítások** elemre, és állítsa be a következő értékeket:
    *  **REST API verziója**: válassza ki a [REST API verzióját](/rest/api/cosmos-db/) a műveletekhez. Az alapértelmezett 2015-12-16. Ha [nagyméretű partíciós kulcsokkal](large-partition-keys.md) rendelkező tárolókkal rendelkezik, és a REST API 2018-12-31-es verziójának megkövetelése:
        - Írja be a **2018-12-31** -es verzióját REST API verzióra
        - A **Start** menüben írja be a "regedit" parancsot a rendszerleíróadatbázis- **szerkesztő** alkalmazás megkereséséhez és megnyitásához.
        - A Rendszerleíróadatbázis-szerkesztőben navigáljon a következő elérési úthoz: **Computer\HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC.INI**
        - Hozzon létre egy új alkulcsot ugyanazzal a névvel, mint a DSN, például "contoso-fiók ODBC DSN".
        - Navigáljon a "contoso-fiók ODBC DSN" alkulcshoz.
        - Kattintson a jobb gombbal egy új **karakterláncérték** hozzáadásához:
            - Érték neve: **IgnoreSessionToken**
            - Érték: **1** 
             :::image type="content" source="./media/odbc-driver/cosmos-odbc-edit-registry.png" alt-text="Rendszerleíróadatbázis-szerkesztő beállításai":::
    - **Lekérdezés konzisztenciája**: válassza ki a műveletek [konzisztenciáji szintjét](consistency-levels.md) . Az alapértelmezett érték a munkamenet.
    - **Újrapróbálkozások száma**: Itt adhatja meg, hogy a rendszer hányszor próbálkozzon újra egy művelettel, ha a kezdeti kérelem nem fejeződött be a szolgáltatási díjak korlátozása miatt.
    - **Sémafájl**: itt számos lehetőség közül választhat.
        - Alapértelmezés szerint, ha a bejegyzést (üres) hagyja, az illesztőprogram megvizsgálja az összes tárolóhoz tartozó adat első oldalát az egyes tárolók sémájának meghatározásához. Ezt nevezzük tároló-hozzárendelésnek. A séma definiálása nélkül az illesztőprogramnak el kell végeznie az egyes illesztőprogram-munkamenetek vizsgálatát, és a DSN használatával magasabb indítási időt eredményezhet az alkalmazás számára. Azt javasoljuk, hogy mindig rendeljen hozzá egy sémafájl-fájlt egy DSN-hez.
        - Ha már rendelkezik egy sémafájl (valószínűleg a séma-szerkesztő használatával létrehozott), kattintson a **Tallózás** gombra, keresse meg a fájlt, és kattintson a **Mentés** elemre, majd **az OK** gombra.
        - Ha új sémát szeretne létrehozni, kattintson az **OK** gombra, majd kattintson a főablakban található **sémakezelő szerkesztő** elemre. Ezután folytassa a séma-szerkesztő adataival. Az új sémafájl létrehozása után ne felejtse el visszatérni a **Speciális beállítások** ablakra az újonnan létrehozott sémafájl belefoglalásához.

1. Miután elvégezte és bezárta az **Azure Cosmos db ODBC-ILLESZTŐ DSN-telepítőjének** ablakát, a rendszer hozzáadja az új felhasználói DSN-t a felhasználói DSN lapra.

    :::image type="content" source="./media/odbc-driver/odbc-driver-user-dsn.png" alt-text="Új Azure Cosmos DB ODBC DSN a felhasználói DSN lapon":::

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>3. lépés: séma-definíció létrehozása a tároló-hozzárendelési módszer használatával

Kétféle mintavételi módszer használható: **tároló-hozzárendelés** vagy **tábla-határolójelek**. A mintavételi munkamenetek mindkét mintavételi módszert használhatják, de mindegyik tároló csak egy adott mintavételi módszert használhat. Az alábbi lépések egy sémát hoznak létre egy vagy több tárolóban lévő adataihoz a tároló-hozzárendelési módszer használatával. Ez a mintavételi módszer a tárolók oldalán lévő összes adat lekérésével határozza meg az adat szerkezetét. Egy tárolót helyez át az ODBC-oldalon található táblába. Ez a mintavételi módszer hatékony és gyors, ha a tárolóban lévő adathalmaz homogén. Ha egy tároló heterogén típusú adattípust tartalmaz, javasoljuk, hogy használja a [Table-határolójelek leképezési módszerét](#table-mapping) , mivel ez robusztusabb mintavételi módszert biztosít a tárolóban lévő adatstruktúrák meghatározásához. 

1. Miután befejezte a 1-4-es lépést az [Azure Cosmos-adatbázishoz való kapcsolódás](#connect)során, kattintson a **Azure Cosmos db ODBC-illesztő DSN-telepítő** ablakában található **Schema Editor** elemre.

    :::image type="content" source="./media/odbc-driver/odbc-driver-schema-editor.png" alt-text="Schema Editor gomb a Azure Cosmos DB ODBC-illesztő DSN beállítása ablakban":::
1. A **séma-szerkesztő** ablakban kattintson az **új létrehozása** elemre.
    A **séma létrehozása** ablak megjeleníti a Azure Cosmos db fiókban található összes tárolót. 

1. Válasszon ki egy vagy több mintát a mintavételezéshez, majd kattintson a **minta** elemre. 

1. A **tervezési nézet** lapon az adatbázis, a séma és a tábla képviselteti magát. A táblázat nézetben a vizsgálat az oszlopnevek (az SQL-név, a forrás neve stb.) tulajdonságaihoz tartozó tulajdonságokat jeleníti meg.
    Minden oszlop esetében módosíthatja az SQL-név, az SQL-típus, az SQL-hossz (ha van), a skálázás (ha alkalmazható), a pontosság (ha van) és a null értékű.
    - Beállíthatja az **oszlop elrejtését** **true** értékre, ha ki szeretné zárni az oszlopot a lekérdezési eredményekből. A kiválasztáshoz és a kivetítéshez nem ad vissza oszlopokat a Hide oszlop = true értékkel, de még mindig a séma részét képezik. Elrejtheti például az összes Azure Cosmos DB szükséges tulajdonságot a "_" értékkel kezdődően.
    - Az **ID** oszlop az egyetlen olyan mező, amely nem rejthető el, mert a normalizált sémában elsődleges kulcsként van használatban. 

1. Miután befejezte a séma definiálását, kattintson a **fájl**  |  **Mentés** lehetőségre, keresse meg a könyvtárat a séma mentéséhez, majd kattintson a **Mentés** gombra.

1. Ha a sémát DSN-sel szeretné használni, nyissa meg az **Azure Cosmos db ODBC-ILLESZTŐ DSN-telepítő ablakát** (az ODBC adatforrás-rendszergazdája használatával), kattintson a **Speciális beállítások** elemre, majd a **séma fájlja** mezőben navigáljon a mentett sémához. A sémafájl meglévő DSN-re való mentése módosítja a séma által definiált adathoz és struktúrához tartozó DSN-kapcsolatokat.

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>4. lépés: séma-definíció létrehozása a tábla-határolójelek leképezési módszerével

Kétféle mintavételi módszer használható: **tároló-hozzárendelés** vagy **tábla-határolójelek**. A mintavételi munkamenetek mindkét mintavételi módszert használhatják, de mindegyik tároló csak egy adott mintavételi módszert használhat. 

A következő lépések egy sémát hoznak létre egy vagy több tárolóban lévő adatkészletek számára a **Table-határolójelek** leképezési módszerének használatával. Javasoljuk, hogy ezt a mintavételi módszert használja, ha a tárolók heterogén adattípust tartalmaznak. Ezzel a módszerrel a mintavételezést attribútumok és a hozzájuk tartozó értékek halmazára is felhasználhatja. Ha például egy dokumentum "type" (típus) tulajdonságot tartalmaz, akkor a mintavételezést a tulajdonság értékeire szűkítheti. A mintavétel végeredménye a megadott típusú értékekhez tartozó táblák halmaza lesz. Írja be például, hogy = Car egy táblát hoz létre, miközben a Type = Plane (sík) táblát fogja létrehozni.

1. Miután befejezte a 1-4-es lépést az [Azure Cosmos-adatbázishoz való kapcsolódás](#connect)során, kattintson a Azure Cosmos db ODBC-illesztő DSN-telepítő ablakában található **Schema Editor** elemre.

1. A **séma-szerkesztő** ablakban kattintson az **új létrehozása** elemre.
    A **séma létrehozása** ablak megjeleníti a Azure Cosmos db fiókban található összes tárolót. 

1. Válasszon egy tárolót a **Minta nézet** lapon, a tároló **hozzárendelés definíciója** oszlopban kattintson a **Szerkesztés** elemre. Ezután a **leképezés definíciója** ablakban válassza a **tábla** határolójelek metódus elemet. Ezután tegye a következőket:

    a. Az **attribútumok** mezőbe írja be a határolójelek tulajdonság nevét. Ez a dokumentum azon tulajdonsága, amelynek a mintavételezési hatókörét meg szeretné jeleníteni, például a városra, és nyomja le az ENTER billentyűt. 

    b. Ha a fent megadott attribútumhoz csak bizonyos értékekre szeretné szűkíteni a mintavételezést, jelölje ki az attribútumot a kiválasztási mezőben, adjon meg egy értéket az **érték** mezőben (például Seattle), majd nyomja le az ENTER billentyűt. Továbbra is hozzáadhat több értéket az attribútumokhoz. Győződjön meg arról, hogy a megfelelő attribútum van kiválasztva az értékek megadásakor.

    Ha például a város **attribútum** értékét tartalmazza, és korlátozni szeretné a táblázatot úgy, hogy csak a New York-i és a Dubai városi értékkel rendelkező sorokat foglalja bele, akkor a City értéket kell megadnia az attribútumok mezőben, valamint a New York-i, majd a Dubai **értéket az értékek** mezőben.

1. Kattintson az **OK** gombra. 

1. A mintaként használni kívánt tárolók leképezési definícióinak befejezése után a **sémakezelő** ablakban kattintson a **minta** elemre.
     Minden oszlop esetében módosíthatja az SQL-név, az SQL-típus, az SQL-hossz (ha van), a skálázás (ha alkalmazható), a pontosság (ha van) és a null értékű.
    - Beállíthatja az **oszlop elrejtését** **true** értékre, ha ki szeretné zárni az oszlopot a lekérdezési eredményekből. A kiválasztáshoz és a kivetítéshez nem ad vissza oszlopokat a Hide oszlop = true értékkel, de még mindig a séma részét képezik. A-től kezdődően például elrejtheti a Azure Cosmos DB rendszer szükséges tulajdonságait `_` .
    - Az **ID** oszlop az egyetlen olyan mező, amely nem rejthető el, mert a normalizált sémában elsődleges kulcsként van használatban. 

1. Miután befejezte a séma definiálását, kattintson a **fájl**  |  **Mentés** lehetőségre, keresse meg a könyvtárat a séma mentéséhez, majd kattintson a **Mentés** gombra.

1. A **Azure Cosmos db ODBC-ILLESZTŐ DSN-telepítő** ablakában kattintson a **Speciális beállítások** elemre. Ezután a **sémafájl** mezőben navigáljon a mentett sémafájl elemhez, és kattintson az **OK** gombra. Kattintson ismét az **OK** gombra a DSN mentéséhez. Ezzel elmenti a létrehozott sémát a DSN-ben. 

## <a name="optional-set-up-linked-server-connection"></a>Választható Csatolt kiszolgáló kapcsolatának beállítása

A SQL Server Management Studio (SSMS) Azure Cosmos DB lekérdezhető egy csatolt kiszolgáló kapcsolatának beállításával.

1. Hozzon létre egy rendszeradatforrást a [2. lépés](#connect), például a neve című témakörben leírtak szerint `SDS Name` .

1. [Telepítse a SQL Server Management Studiot](/sql/ssms/download-sql-server-management-studio-ssms) , és kapcsolódjon a kiszolgálóhoz. 

1. A SSMS lekérdezési szerkesztőjében hozzon létre egy csatolt kiszolgáló objektumot az `DEMOCOSMOS` adatforráshoz az alábbi parancsokkal. Cserélje le a `DEMOCOSMOS` nevet a csatolt kiszolgáló nevére, a helyére pedig a `SDS Name` rendszeradatforrás nevét.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Az új csatolt kiszolgáló nevének megtekintéséhez frissítse a társított kiszolgálók listáját.

:::image type="content" source="./media/odbc-driver/odbc-driver-linked-server-ssms.png" alt-text="Csatolt kiszolgáló a SSMS-ben":::

### <a name="query-linked-database"></a>Csatolt adatbázis lekérdezése

A csatolt adatbázis lekérdezéséhez adjon meg egy SSMS-lekérdezést. Ebben a példában a lekérdezés a (z) nevű tárolóban lévő táblából van kiválasztva `customers` :

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Hajtsa végre a lekérdezést. Ennek az eredménynek a következőhöz hasonlónak kell lennie:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> A csatolt Cosmos DB kiszolgáló nem támogatja a négy részből álló elnevezést. A rendszer a következő üzenethez hasonló hibaüzenetet ad vissza:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>Választható Nézetek létrehozása
A mintavételi folyamat részeként megadhatja és létrehozhatja a nézeteket. Ezek a nézetek az SQL-nézetekkel egyenértékűek. Ezek csak olvashatók, és hatókörük a Azure Cosmos DB SQL-lekérdezés kiválasztásait és kivetítéseit határozza meg. 

Az adatnézet létrehozásához a **séma szerkesztő** ablakában, a **definíciók megtekintése** oszlopban kattintson a **Hozzáadás** elemre a tárolóhoz a minta sorban. 

:::image type="content" source="./media/odbc-driver/odbc-driver-create-view.png" alt-text="Adatnézet létrehozása":::


Ezután a **definíciók megtekintése** ablakban tegye a következőket:

1. Kattintson az **új** elemre, adja meg a nézet nevét, például EmployeesfromSeattleView, majd kattintson **az OK** gombra.

1. A **nézet szerkesztése** ablakban adjon meg egy Azure Cosmos db lekérdezést. Ennek egy [Azure Cosmos db SQL-lekérdezésnek](./sql-query-getting-started.md)kell lennie, például `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"` , majd kattintson **az OK** gombra.

    :::image type="content" source="./media/odbc-driver/odbc-driver-create-view-2.png" alt-text="Lekérdezés hozzáadása nézet létrehozásakor":::


Több nézetet is létrehozhat, ahogy szeretné. Ha elkészült a nézetek definiálásával, megtekintheti azokat. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>5. lépés: az adatai megtekintése BI-eszközökön, például Power BI Desktop

Az új DSN használatával bármely ODBC-kompatibilis eszközzel csatlakozhat Azure Cosmos DBhoz – ez a lépés egyszerűen azt mutatja be, hogyan csatlakozhat a Power BI Desktophoz, és hogyan hozhat létre Power BI vizualizációt.

1. Nyissa meg a Power BI Desktopot.

1. Kattintson **az adatlekérdezés** elemre.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data.png" alt-text="Az Power BI Desktopban lévő adatlekérdezés":::

1. Az **adatlekérdezés** ablakban kattintson a **más**  |  **ODBC**-  |  **kapcsolat** lehetőségre.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-2.png" alt-text="Válassza ki az ODBC-adatforrást az Power BI lekérése elemnél":::

1. A **from ODBC** ablakban válassza ki a létrehozott adatforrás nevét, majd kattintson az **OK** gombra. A **Speciális beállítások** bejegyzéseket üresen hagyhatja.

   :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-3.png" alt-text="Adatforrás nevének (DSN) kiválasztása Power BI":::

1. Az **adatforráshoz való hozzáférés ODBC-illesztő használatával** ablakban válassza az **alapértelmezett vagy az egyéni** lehetőséget, majd kattintson a **Kapcsolódás** elemre. Nem kell megadnia a **hitelesítő adatok kapcsolódási karakterláncának tulajdonságait**.

1. A **navigátor** ablak bal oldali ablaktábláján bontsa ki az adatbázist, a sémát, majd válassza ki a táblázatot. Az eredmények ablaktábla a létrehozott sémával együtt tartalmazza az adatmennyiséget.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-4.png" alt-text="Válassza ki a táblát Power BI az adatlekérdezés":::

1. Az Power BI Desktopban található adatmegjelenítéshez jelölje be a tábla neve előtt található jelölőnégyzetet, majd kattintson a **Betöltés** elemre.

1. Power BI Desktop a bal szélen kattintson az adatlapra az :::image type="icon" source="./media/odbc-driver/odbc-driver-data-tab.png"::: adatimportálás ellenőrzéséhez. 

1. Mostantól létrehozhat vizualizációkat Power BI használatával, ha a jelentés lapra kattint :::image type="icon" source="./media/odbc-driver/odbc-driver-report-tab.png"::: , majd az **új vizualizáció** lehetőségre kattint, majd testreszabja a csempét. További információ a vizualizációk létrehozásáról a Power BI Desktopban: [Power bi vizualizációs típusai](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/). 

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a következő hibaüzenetet kapja, győződjön meg arról, hogy a [2. lépésben](#connect) a Azure Portal átmásolt **gazdagép** -és **hozzáférési kulcs** értéke helyes, majd próbálkozzon újra. Használja a **gazdagép** és a kulcs értékeinek jobb oldalán lévő másolási gombokat a Azure Portal az értékek szabad **eléréséhez** .

```output
[HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}
```


## <a name="next-steps"></a>Következő lépések

További információ az Azure Cosmos DB-ről: [Üdvözli az Azure Cosmos DB](introduction.md).