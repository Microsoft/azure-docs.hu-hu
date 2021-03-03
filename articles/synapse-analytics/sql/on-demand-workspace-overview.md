---
title: Kiszolgáló nélküli SQL-készlet
description: Ismerje meg a kiszolgáló nélküli SQL-készletet az Azure szinapszis Analytics szolgáltatásban.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: e1a0f4f22411e506cab3b54e955a4cdc16986fe2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667503"
---
# <a name="serverless-sql-pool-in-azure-synapse-analytics"></a>Kiszolgáló nélküli SQL-készlet az Azure szinapszis Analyticsben 

Minden Azure szinapszis Analytics-munkaterület olyan kiszolgáló nélküli SQL Pool-végpontokat tartalmaz, amelyek segítségével lekérdezheti a tóban lévő adatlekérdezéseket.

A kiszolgáló nélküli SQL-készlet egy lekérdezési szolgáltatás a adatközpontban található adaton keresztül. Az alábbi funkciókkal érheti el az adatait:
 
- A jól ismert T-SQL-szintaxist, amellyel az adatlekérdezés nem szükséges, és nem kell egy speciális tárolóba másolni vagy betölteni az adatfájlokat. 
- Integrált kapcsolódás a T-SQL felületen keresztül, amely számos üzleti intelligenciát és alkalmi lekérdezési eszközt kínál, beleértve a legnépszerűbb illesztőprogramokat is. 

A kiszolgáló nélküli SQL-készlet egy elosztott adatfeldolgozó rendszer, amely nagy léptékű adatokra és számítási funkciókra épül. A kiszolgáló nélküli SQL-készlet lehetővé teszi, hogy a munkaterheléstől függően másodpercek alatt elemezze Big adatait. A beépített lekérdezés-végrehajtás hibatűrésének köszönhetően a rendszer magas megbízhatóságot és sikerességi arányt biztosít, még a nagy adatkészleteket is tartalmazó hosszan futó lekérdezések esetében is.

A kiszolgáló nélküli SQL-készlet kiszolgáló nélküli, ezért nincs szükség a telepítéshez vagy a fürtök karbantartásához szükséges infrastruktúra kiépítésére. A szolgáltatás alapértelmezett végpontja minden Azure szinapszis-munkaterületen elérhető, így a munkaterület létrehozása után azonnal elindíthatja az adatlekérdezést. 

A fenntartott erőforrások díjmentesek, csak a futtatott lekérdezések által feldolgozott adatmennyiségért kell fizetnie, ezért ez a modell egy valódi használaton kívüli modell.  

Ha az adatfolyamatban az Azure Szinapszishoz Apache Sparkt használ, az adatok előkészítése, tisztítása vagy dúsítása érdekében a folyamat során létrehozott [külső Spark-táblákat](develop-storage-files-spark-tables.md) közvetlenül a kiszolgáló nélküli SQL-készletből kérdezheti le. A [privát hivatkozás](../security/how-to-connect-to-workspace-with-private-links.md) használatával hozza a kiszolgáló nélküli SQL-készlet végpontját a [felügyelt munkaterület VNet](../security/synapse-workspace-managed-vnet.md).  

## <a name="serverless-sql-pool-benefits"></a>A kiszolgáló nélküli SQL-készlet előnyei

Ha meg kell vizsgálnia az adattárban található adatok elemzését, vagy optimalizálni szeretné a meglévő Adatátalakítási folyamatát, a kiszolgáló nélküli SQL-készlet használata is hasznos lehet. A következő esetekben alkalmas:

- Alapszintű felderítés és feltárás – gyorsan elsajátíthatja az adatok különböző formátumokban (a parketta, a CSV, a JSON) tárolt adatait, így megtervezheti az elemzések kinyerését.
- Logikai adattárház – az adatok áthelyezése és átalakítása nélkül, a nyers vagy a különböző adatokon alapuló összevont absztrakciót biztosít, amely lehetővé teszi az adatok naprakész áttekintését.
- Adatátalakítás – egyszerű, méretezhető és elvégezhető az adatok átalakítása a tóban a T-SQL használatával, így a BI-hoz és más eszközökhöz is betölthetők, vagy egy rokon adattárolóba (szinapszis SQL-adatbázisok, Azure SQL Database stb.).

A kiszolgáló nélküli SQL-készletből a különböző szakmai szerepkörök is részesülhetnek:

- Az adatmérnökök megtekinthetik a tavat, átalakítják és előkészíthetik az adatfeldolgozást, és egyszerűbbé teszik az Adatátalakítási folyamatokat. További információért olvassa el ezt az [oktatóanyagot](tutorial-data-analyst.md).
- Az adatszakértők gyorsan megtudhatják, hogy a tó adatainak tartalma és szerkezete milyen funkciókkal, például a OPENROWSET és az automatikus séma-következtetésekkel kapcsolatos.
- Az adatelemzők megtekinthetik az adatelemzők és az adatmérnökök által létrehozott, ismerős T-SQL nyelvvel vagy azok kedvenc eszközeivel létrehozott, és a kiszolgáló nélküli SQL-készlethez való kapcsolódásra képes [külső táblákat](develop-storage-files-spark-tables.md) .
- A BI-szakemberek gyorsan [hozhatnak létre Power bi jelentéseket a Lake és a](tutorial-connect-power-bi-desktop.md) Spark tábláiban.

## <a name="how-to-start-using-serverless-sql-pool"></a>A kiszolgáló nélküli SQL-készlet használatának megkezdése

A kiszolgáló nélküli SQL Pool végpont minden Azure-beli szinapszis-munkaterületen elérhető. Létrehozhat egy munkaterületet, és azonnal megkezdheti az adatlekérdezést az Ön által jól ismert eszközök használatával.

## <a name="client-tools"></a>Ügyféleszközök

A kiszolgáló nélküli SQL-készlet lehetővé teszi a meglévő SQL ad-hoc lekérdezési és üzleti intelligenciát szolgáló eszközök használatát a Microsoft-adatközpontba való koppintáshoz. Ahogy a jól ismert T-SQL-szintaxist nyújt, a TDS-kapcsolati SQL-ajánlatokat kiépítő bármely eszköz [csatlakozhat és lekérdezheti a SZINAPSZIS SQL igény szerinti elérését](connect-overview.md) . A Azure Data Studiohoz csatlakozhat, és alkalmi lekérdezéseket futtathat, vagy csatlakozhat a Power BIhoz, és percek alatt megszerezheti a betekintést.

## <a name="t-sql-support"></a>T-SQL-támogatás

A kiszolgáló nélküli SQL-készlet olyan T-SQL-lekérdezési felületet kínál, amely kis mértékben bővül/bővíthető bizonyos aspektusokban, hogy a részben strukturált és strukturálatlan adatmennyiségek lekérdezéséhez igazodjon. Emellett a T-SQL nyelv néhány aspektusa nem támogatott a kiszolgáló nélküli SQL-készlet kialakítása miatt, például a DML funkció jelenleg nem támogatott.

- A számítási feladatok jól ismert fogalmakkal rendezhetők:
- Adatbázisok – a kiszolgáló nélküli SQL-készlet végpontja több adatbázissal is rendelkezhet.
- Sémák – egy adatbázison belül egy vagy több, sémák nevű objektum tulajdonosi csoport is lehet.
- Nézetek
- Külső erőforrások – adatforrások, fájlformátumok és táblák

A biztonság a használatával kényszeríthető:

- Bejelentkezések és felhasználók
- A Storage-fiókokhoz való hozzáférés vezérlésére szolgáló hitelesítő adatok
- Engedélyek megadása, megtagadása és visszavonása az egyes objektumok szintjén
- Azure Active Directory-integráció

Támogatott T-SQL:

- A teljes [kijelölési](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) felület támogatott, beleértve az SQL-függvények többségét
- CETAS – KÜLSŐ TÁBLA LÉTREHOZÁSA KIJELÖLÉSKÉNT
- Csak nézetekkel és biztonsággal kapcsolatos DDL-utasítások

A kiszolgáló nélküli SQL-készletnek nincs helyi tárterülete, csak a metaadat-objektumokat tárolja az adatbázisokban. Ezért a következő fogalmakhoz kapcsolódó T-SQL nem támogatott:

- Táblák
- Triggerek
- Tényleges táblán alapuló nézetek
- A nézetekhez és a biztonsághoz kapcsolódó DDL-utasítások
- DML-utasítások

### <a name="extensions"></a>Bővítmények

A következő képességek hozzáadásával lehetővé teszi a zökkenőmentes működést a fájlokban tárolt adatlekérdezések esetében, a kiszolgáló nélküli SQL-készlet pedig kibővíti a meglévő [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) funkciót:

[Több fájl vagy mappa lekérdezése](query-data-storage.md#query-multiple-files-or-folders)

[PARKETTA fájlformátum](query-data-storage.md#query-parquet-files)

[A tagolt szöveg (mező lezáró, sor lezáró, escape-karakter) használatának további lehetőségei](query-data-storage.md#query-csv-files)

[Oszlopok kiválasztott részhalmazának olvasása](query-data-storage.md#read-a-chosen-subset-of-columns)

[Séma-következtetés](query-data-storage.md#schema-inference)

[filename függvény](query-data-storage.md#filename-function)

[filepath függvény](query-data-storage.md#filepath-function)

[Összetett típusok és beágyazott vagy ismétlődő adatstruktúrák használata](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Biztonság

A kiszolgáló nélküli SQL-készlet az adataihoz való hozzáférés biztosítására szolgáló mechanizmusokat kínál.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

A kiszolgáló nélküli SQL-készlet lehetővé teszi az adatbázis-felhasználó és más Microsoft-szolgáltatások identitásának központilag kezelését [Azure Active Directory integrációval](../../azure-sql/database/authentication-aad-configure.md). Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. A Azure Active Directory (Azure AD) támogatja a [többtényezős hitelesítést](../../azure-sql/database/authentication-mfa-ssms-configure.md) (MFA) az adatkezelés és az alkalmazások biztonságának növelésére az egyszeri bejelentkezési folyamat támogatása mellett.

#### <a name="authentication"></a>Hitelesítés

A kiszolgáló nélküli SQL-készlet hitelesítése arra utal, hogy a felhasználók hogyan igazolják identitásukat a végponthoz való csatlakozáskor. Két hitelesítési típus támogatott:

- **SQL-hitelesítés**

  Ez a hitelesítési módszer egy felhasználónevet és egy jelszót használ.

- **Azure Active Directory hitelesítés**:

  Ez a hitelesítési módszer Azure Active Directory által felügyelt identitásokat használ. Az Azure AD-felhasználók esetében a többtényezős hitelesítés is engedélyezhető. [Amikor csak lehet](/sql/relational-databases/security/choose-an-authentication-mode?view=azure-sqldw-latest&preserve-view=true), használja az Active Directory-hitelesítést (beépített biztonság).

#### <a name="authorization"></a>Engedélyezés

Az engedélyezés arra utal, hogy a felhasználók mit tehetnek egy kiszolgáló nélküli SQL Pool-adatbázisban, és a felhasználói fiók adatbázis-szerepkör-tagságai és objektum-szintű engedélyei vezérlik.

SQL-hitelesítés használata esetén az SQL-felhasználó csak a kiszolgáló nélküli SQL-készletben létezik, és az engedélyek hatóköre a kiszolgáló nélküli SQL-készlet objektumaira terjed ki. A biztonságos objektumok elérését más szolgáltatásokban (például az Azure Storage-ban) nem lehet közvetlenül az SQL-felhasználó számára biztosítani, mert csak a kiszolgáló nélküli SQL-készlet hatókörében van. Az SQL-felhasználónak a [támogatott engedélyezési típusok](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) egyikét kell használnia a fájlok eléréséhez.

Ha az Azure AD-hitelesítést használja, a felhasználók bejelentkezhetnek a kiszolgáló nélküli SQL-készletbe és más szolgáltatásokba, például az Azure Storage-ba, és engedélyeket adhatnak az Azure AD-felhasználónak.

### <a name="access-to-storage-accounts"></a>Hozzáférés a Storage-fiókokhoz

A kiszolgáló nélküli SQL Pool szolgáltatásba bejelentkezett felhasználó számára engedélyezni kell a fájlok elérését és lekérdezését az Azure Storage-ban. a kiszolgáló nélküli SQL-készlet a következő engedélyezési típusokat támogatja:

- A **közös hozzáférésű aláírás (SAS)** delegált hozzáférést biztosít a Storage-fiók erőforrásaihoz. A SAS használatával a fiók kulcsainak megosztása nélkül biztosíthat hozzáférést az ügyfeleknek a Storage-fiók erőforrásaihoz. Az SAS részletesen szabályozhatja az olyan ügyfelek számára biztosított hozzáférés típusát, akik SAS: érvényességi időtartam, megadott engedélyek, elfogadható IP-címtartomány, elfogadható protokoll (https/http).

- A **felhasználói identitás** (más néven "átmenő") olyan engedélyezési típus, ahol a kiszolgáló nélküli SQL-készletbe bejelentkezett Azure ad-felhasználó identitása az adatokhoz való hozzáférés engedélyezésére szolgál. Az adatok elérése előtt az Azure Storage rendszergazdájának engedélyeket kell adnia az Azure AD-felhasználónak az adatokhoz való hozzáféréshez. Ez az engedélyezési típus a kiszolgáló nélküli SQL-készletbe bejelentkezett Azure AD-felhasználót használja, ezért az SQL-felhasználói típusok nem támogatottak.

## <a name="next-steps"></a>Következő lépések
A végponti kapcsolatok és a lekérdezési fájlok további információi a következő cikkekben találhatók: 
- [Kapcsolódás a végponthoz](connect-overview.md)
- [Fájlok lekérdezése](develop-storage-files-overview.md)
