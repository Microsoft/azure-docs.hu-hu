---
title: Kiszolgáló nélküli SQL-készlet
description: Ismerje meg a kiszolgáló nélküli SQL-készletet a Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f0abef835e406b442239cecd81fed5751f1c9a92
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378159"
---
# <a name="serverless-sql-pool-in-azure-synapse-analytics"></a>Kiszolgáló nélküli SQL-készlet a Azure Synapse Analytics 

Minden Azure Synapse Analytics munkaterület kiszolgáló nélküli SQL-készletvégpontokat tartalmaz, amelyek használatával adatokat lehet lekérdezni a lake-ben.

A kiszolgáló nélküli SQL-készlet egy lekérdezési szolgáltatás a Data Lake-ben található adatokon. A következő funkciókkal teszi lehetővé az adatokhoz való hozzáférést:
 
- Egy jól ismert T-SQL-szintaxis az adatok helyi lekérdezéséhez anélkül, hogy adatokat kellene másolnia vagy betöltenie egy speciális tárolóba. 
- Integrált kapcsolat a T-SQL-felülettel, amely üzletiintelligencia- és alkalmi lekérdezési eszközök széles választékát kínálja, beleértve a legnépszerűbb illesztőprogramokat is. 

A kiszolgáló nélküli SQL-készlet egy elosztott adatfeldolgozási rendszer, amely nagy méretű adatokhoz és számítási funkciókhoz készült. A kiszolgáló nélküli SQL-készlet lehetővé teszi a Big Data másodpercek vagy percek alatt való elemzését a számítási feladattól függően. A beépített lekérdezés-végrehajtás hibatűrésének köszönhetően a rendszer nagy megbízhatóságot és sikeresség arányt biztosít még a nagy adatkészleteket is érintő hosszú ideig futó lekérdezések esetén is.

A kiszolgáló nélküli SQL-készlet kiszolgáló nélküli, ezért nem kell infrastruktúrát beállítania vagy fürtöt karbantartani. A szolgáltatás alapértelmezett végpontja minden munkaterületen Azure Synapse, így a munkaterület létrehozása után a lehető leghamarabb elkezdheti lekérdezni az adatokat. 

Fenntartott erőforrásokért nem kell fizetnie, csak a futtatott lekérdezések által feldolgozott adatokért kell fizetnie, ezért ez a modell egy valódi használat alapján fizetendő modell.  

Ha a Apache Spark az Azure Synapse-hoz, az adatok előkészítéséhez, tisztításához vagy gazdagításához közvetlenül a kiszolgáló nélküli SQL-készletből is lekérdezheti a folyamat során létrehozott külső Spark-táblákat. [](develop-storage-files-spark-tables.md) A [Private Link](../security/how-to-connect-to-workspace-with-private-links.md) a kiszolgáló nélküli SQL-készlet végpontját a felügyelt munkaterület virtuális [hálózatába.](../security/synapse-workspace-managed-vnet.md)  

## <a name="serverless-sql-pool-benefits"></a>Kiszolgáló nélküli SQL-készlet előnyei

Ha a data lake-ben található adatokat kell feltárni, elemzéseket kell kihozni onnan, vagy optimalizálni kell a meglévő adatátalakítási folyamatot, kihasználhatja a kiszolgáló nélküli SQL-készletet. A következő forgatókönyvekhez alkalmas:

- Alapszintű felderítés és feltárás – Gyorsan elemezheti az adatokat a data lake-ben található különböző formátumokban (Parquet, CSV, JSON), így megtervezheti, hogyan nyerhet ki elemzéseket a data lake-ben.
- Logikai adattárház – Relációs absztrakciót biztosít nyers vagy különböző adatokon az adatok áthelyezése és átalakítása nélkül, így mindig naprakész nézetben megtekintheti az adatokat.
- Adatátalakítás – Egyszerű, skálázható és hatékony módszer a tóban található adatok T-SQL használatával történő átalakítására, hogy bi-ba és más eszközökbe legyen betöltve, vagy betölthető legyen egy relációs adattárba (Synapse SQL adatbázisokba, Azure SQL Database stb.).

A kiszolgáló nélküli SQL-készlet a különböző professzionális szerepkörök számára is előnyös lehet:

- Az adatmérnökök a szolgáltatással felfedezhetik a tavat, átalakíthetik és előkészíthetik az adatokat, és leegyszerűsíthetik az adatátalakítási folyamatukat. További információért tekintse meg ezt az [oktatóanyagot.](tutorial-data-analyst.md)
- Az adattudósok az OPENROWSET és az automatikus séma-dedukencia funkciónak köszönhetően gyorsan meg tudnak bizonyosodni a tóban található adatok tartalmáról és szerkezetével kapcsolatban.
- Az adatelemzők az ismerős T-SQL nyelv vagy kedvenc eszközeik használatával tárhatnak fel adatszakértők vagy adatszakértők által létrehozott külső Spark-táblákat, amelyek csatlakozhatnak a kiszolgáló nélküli SQL-készlethez. [](develop-storage-files-spark-tables.md)
- A BI-szakemberek gyorsan Power BI [jelentéseket](tutorial-connect-power-bi-desktop.md) a lake- és Spark-táblákban lévő adatok alapján.

## <a name="how-to-start-using-serverless-sql-pool"></a>A kiszolgáló nélküli SQL-készlet használatának első kezdése

A kiszolgáló nélküli SQL-készlet végpontja minden munkaterületen Azure Synapse meg. Létrehozhat egy munkaterületet, és azonnal elkezdheti az adatok lekérdezését az Ön által ismert eszközökkel.

## <a name="client-tools"></a>Ügyféleszközök

A kiszolgáló nélküli SQL-készlet lehetővé teszi, hogy a meglévő alkalmi SQL-lekérdezések és üzletiintelligencia-eszközök a Data Lake-be is bekoppintanak. Mivel ismerős T-SQL-szintaxist biztosít, minden olyan eszköz, amely képes TDS-kapcsolatot létesíteni az SQL-ajánlatokkal, csatlakozhat a-hoz, és lekérdezheti [Synapse SQL.](connect-overview.md) Csatlakozhat a Azure Data Studio és alkalmi lekérdezéseket futtathat, vagy akár Power BI is csatlakozhat, hogy néhány perc alatt elemzéseket nyerjen ki.

## <a name="t-sql-support"></a>T-SQL-támogatás

A kiszolgáló nélküli SQL-készlet T-SQL lekérdezési felületet kínál, amely bizonyos szempontból némileg továbbfejlesztett/kiterjesztett felülettel rendelkezik, hogy igazodjon a részben strukturált és strukturálatlan adatok lekérdezésével kapcsolatos tapasztalatokhoz. Emellett a kiszolgáló nélküli SQL-készlet kialakítása miatt a T-SQL nyelv néhány aspektusa nem támogatott, például a DML-funkciók jelenleg nem támogatottak.

- A számítási feladatok a már ismert fogalmak alapján rendszerezhatóak:
- Adatbázisok – A kiszolgáló nélküli SQL-készlet végpontja több adatbázissal is rendelkezik.
- Sémák – Egy adatbázisban egy vagy több objektum tulajdonjogi csoport is lehet, az úgynevezett sémák.
- Megjelenítések
- Külső erőforrások – adatforrások, fájlformátumok és táblák

A biztonság a következővel kényszeríthető ki:

- Bejelentkezések és felhasználók
- Hitelesítő adatok a tárfiókok hozzáférésének szabályozása érdekében
- Engedélyek megadása, megtagadása és visszavonása objektumszintenként
- Azure Active Directory-integráció

Támogatott T-SQL:

- A [teljes SELECT](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) felület támogatott, beleértve az SQL-függvények többségét
- CETAS – KÜLSŐ TÁBLA LÉTREHOZÁSA KIJELÖLÉSKÉNT
- Csak nézetekhez és biztonsághoz kapcsolódó DDL-utasítások

A kiszolgáló nélküli SQL-készlet nem rendelkezik helyi tárolóval, csak a metaadat-objektumok vannak adatbázisokban tárolva. Ezért a következő fogalmakhoz kapcsolódó T-SQL nem támogatott:

- Táblázatok
- Triggerek
- Tényleges táblán alapuló nézetek
- Nézetekhez és biztonsághoz kapcsolódó DDL-utasítások
- DML-utasítások

### <a name="extensions"></a>Bővítmények

Annak érdekében, hogy zökkenőmentes legyen a data lake-ben található fájlokban található adatok lekérdezése, a kiszolgáló nélküli SQL-készlet a következő képességek hozzáadásával kibővíti a meglévő [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) függvényt:

[Több fájl vagy mappa lekérdezése](query-data-storage.md#query-multiple-files-or-folders)

[PARQUET-fájlformátum](query-data-storage.md#query-parquet-files)

[A tagolt szöveg további beállításai (mezőválasztó, sorválasztó, escape-karakter)](query-data-storage.md#query-csv-files)

[Oszlopok kiválasztott részkészletének olvasása](query-data-storage.md#read-a-chosen-subset-of-columns)

[Séma-következtetés](query-data-storage.md#schema-inference)

[filename függvény](query-data-storage.md#filename-function)

[filepath függvény](query-data-storage.md#filepath-function)

[Összetett típusok és beágyazott vagy ismétlődő adatstruktúrák](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Biztonság

A kiszolgáló nélküli SQL-készlet olyan mechanizmusokat kínál, amelyek biztosítják az adatokhoz való hozzáférést.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

A kiszolgáló nélküli SQL-készlet lehetővé teszi, hogy központilag kezelje az adatbázis-felhasználó és más Microsoft-szolgáltatások identitását [Azure Active Directory integrációval.](../../azure-sql/database/authentication-aad-configure.md) Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. Azure Active Directory (Azure AD) támogatja a [többtényezős](../../azure-sql/database/authentication-mfa-ssms-configure.md) hitelesítést (MFA) az adatok és az alkalmazások biztonságának növelése érdekében, miközben támogatja az egyszeri bejelentkezést.

#### <a name="authentication"></a>Hitelesítés

A kiszolgáló nélküli SQL-készlet hitelesítése azt jelenti, hogy a felhasználók hogyan igazolják identitásukat a végponthoz való csatlakozáskor. Kétféle hitelesítés támogatott:

- **SQL-hitelesítés**

  Ez a hitelesítési módszer felhasználónevet és jelszót használ.

- **Azure Active Directory hitelesítés:**

  Ez a hitelesítési módszer a által felügyelt identitásokat Azure Active Directory. Az Azure AD-felhasználók számára engedélyezhető a többtényezős hitelesítés. [Amikor csak lehet](/sql/relational-databases/security/choose-an-authentication-mode?view=azure-sqldw-latest&preserve-view=true), használja az Active Directory-hitelesítést (beépített biztonság).

#### <a name="authorization"></a>Engedélyezés

Az engedélyezés azt jelenti, hogy a felhasználó mit tud tenni egy kiszolgáló nélküli SQL-készlet adatbázisában, és a felhasználói fiók adatbázis-szerepkörtagságai és objektumszintű engedélyei vezérlik.

SQL-hitelesítés használata esetén az SQL-felhasználó csak kiszolgáló nélküli SQL-készletben létezik, és az engedélyek hatóköre a kiszolgáló nélküli SQL-készletben lévő objektumokra terjed ki. A más szolgáltatásokban (például az Azure Storage-ban) lévő biztonságos objektumokhoz való hozzáférés nem adható közvetlenül az SQL-felhasználónak, mivel csak a kiszolgáló nélküli SQL-készlet hatókörében létezik. Az SQL-felhasználónak a támogatott hitelesítési típusok [valamelyikét](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) kell használnia a fájlok eléréséhez.

Azure AD-hitelesítés használata esetén a felhasználó bejelentkezhet a kiszolgáló nélküli SQL-készletbe és más szolgáltatásokba, például az Azure Storage-ba, és engedélyeket adhat az Azure AD-felhasználónak.

### <a name="access-to-storage-accounts"></a>Hozzáférés a tárfiókhoz

A kiszolgáló nélküli SQL-készlet szolgáltatásba bejelentkezett felhasználónak jogosultnak kell lennie az Azure Storage-ban lévő fájlok elérésére és lekérdezésére. kiszolgáló nélküli SQL-készlet a következő engedélyezési típusokat támogatja:

- **A közös hozzáférésű jogosultság jogosultsága (SAS)** delegált hozzáférést biztosít a tárfiók erőforrásaihoz. Sas használatával fiókkulcsok megosztása nélkül adhat hozzáférést az ügyfeleknek a tárfiókban található erőforrásokhoz. Az SAS részletesen szabályozhatja, hogy milyen típusú hozzáférést biztosít az SAS: érvényességi időtartam, megadott engedélyek, elfogadható IP-címtartomány, elfogadható protokoll (https/http) ügyfelek számára.

- **A felhasználói** identitás (más néven "átmenő") egy olyan engedélyezési típus, amelyben a kiszolgáló nélküli SQL-készletbe bejelentkezett Azure AD-felhasználó identitásával engedélyezi az adatokhoz való hozzáférést. Az adatok elérése előtt az Azure Storage-rendszergazdának engedélyeket kell adnunk az Azure AD-felhasználónak az adatok eléréséhez. Ez az engedélyezési típus azt az Azure AD-felhasználót használja, aki bejelentkezett a kiszolgáló nélküli SQL-készletbe, ezért az SQL-felhasználótípusok esetében nem támogatott.

## <a name="next-steps"></a>Következő lépések
A végpontok kapcsolatának és a fájlok lekérdezésének további információit a következő cikkekben talál: 
- [Csatlakozás a végponthoz](connect-overview.md)
- [Fájlok lekérdezése](develop-storage-files-overview.md)
