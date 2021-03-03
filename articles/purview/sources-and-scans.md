---
title: Támogatott adatforrások és fájltípusok
description: Ez a cikk a hatáskörébe tartozó támogatott adatforrások és fájltípusok fogalmi részleteit ismerteti.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 3b19fab33d0c8f53025605fd14fe65f08e660392
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677923"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Támogatott adatforrások és fájltípusok az Azure hatáskörébe

Ez a cikk a hatáskörébe tartozó támogatott adatforrásokat, fájltípusokat és vizsgálati fogalmakat ismerteti.

## <a name="supported-data-sources"></a>Támogatott adatforrások

Az Azure-beli hatáskörébe a következő források támogatottak:

| Áruház típusa | Támogatott hitelesítési típus | Vizsgálatok beállítása UX/PowerShell használatával |
| ---------- | ------------------- | ------------------------------ |
| Helyszíni SQL Server                   | SQL-hitelesítés                        | UX                                |
| Azure Synapse Analytics (korábban SQL DW)            | SQL-hitelesítés, egyszerű szolgáltatásnév, MSI               | UX                             |
| Azure SQL Database (DB)                  | SQL-hitelesítés, egyszerű szolgáltatásnév, MSI               | UX |
| Felügyelt Azure SQL Database-példány      | SQL-hitelesítés, egyszerű szolgáltatásnév, MSI               | UX    |
| Azure Blob Storage                       | Fiók kulcsa, szolgáltatásnév, MSI | UX            |
| Azure Data Explorer                      | Szolgáltatásnév                              | UX            |
| Azure Data Lake Storage Gen1 (ADLS Gen1) | Szolgáltatásnév, MSI                              | UX            |
| Azure Data Lake Storage Gen2 (ADLS Gen2) | Fiók kulcsa, szolgáltatásnév, MSI            | UX            |
| Azure Cosmos DB                          | Fiók kulcsa                                    | UX            |


> [!Note]
> Az Azure Data Lake Storage Gen2 mostantól általánosan elérhető. Javasoljuk, hogy már ma kezdje el használni. További információkért tekintse meg a [termék oldalát](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-supported-for-scanning"></a>A vizsgálathoz támogatott fájltípusok

A következő fájltípusok támogatottak a vizsgálathoz, a sémák kinyeréséhez és besorolásához, ahol lehetséges:

- A bővítmény által támogatott strukturált fájlformátumok: AVRO, ork, PARQUEt, CSV, JSON, PSV, SSV, TSV, TXT, XML, GZIP
- A kiterjesztés által támogatott dokumentum-fájlformátumok: DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX, XLT
- A hatáskörébe az egyéni fájlkiterjesztések és az egyéni elemzők is támogatottak.
 
> [!Note]
> Minden gzip-fájlt egyetlen CSV-fájlhoz kell hozzárendelni a alkalmazáson belül. A gzip-fájlok a rendszer és az egyéni besorolási szabályok hatálya alá esnek. Jelenleg nem támogatjuk a több fájlra leképezett gzip-fájlok, illetve a CSV-fájltól eltérő fájltípusok vizsgálatát. 

## <a name="sampling-within-a-file"></a>Mintavételezés fájlon belül

A hatáskörébe tartozó terminológiában
- L1 vizsgálat: az alapszintű információk és a metaadatok (például fájlnév, méret és teljes név) kibontása
- L2-vizsgálat: a strukturált fájltípusok és adatbázistáblák sémájának kibontása
- L3-vizsgálat: a séma kibontása, ahol alkalmazható, és a mintául szolgáló fájl a rendszerre és az egyéni besorolási szabályokra vonatkozik

Az összes strukturált fájlformátum esetében a hatáskörébe képolvasó a következő módon keresi a fájlokat:

- A strukturált fájltípusok esetében az egyes oszlopokban 128 sort, vagy 1 MB-ot, attól függően, hogy melyik a kisebb.
- A dokumentum-fájlformátumok esetében az egyes fájlokhoz 20 MB-ot vesz fel.
    - Ha a dokumentum-fájl 20 MB-nál nagyobb, akkor nem vonatkozik rá a mélyreható vizsgálat (a besorolás alá esik). Ebben az esetben a hatáskörébe csak az alapszintű metaadatokat rögzíti, például a fájlnevet és a teljesen minősített nevet.

## <a name="resource-set-file-sampling"></a>Erőforrás-készlet fájljának mintavételezése

A rendszer egy mappát vagy partíció-csoportot észlel a hatáskörébe tartozó *erőforrásként* , ha az megfelel a rendszererőforrás-készlet házirendjének vagy a felhasználó által meghatározott erőforrás-készlet házirendjének. Ha a rendszer egy erőforrás-készletet észlel, akkor a hatáskörébe kerül a benne található összes mappa. További információ az erőforrás- [készletekről](concept-resource-sets.md).

A fájlok mintavételezése fájltípusok szerinti erőforrás-készletek esetén:

- **Tagolt fájlok (CSV, PSV, SSV, TSV)** – az 100-es fájlokban az 1. rétegbeli fájlok (L3-vizsgálat) egy olyan mappában vagy partíciós csoporton belül találhatók, amelyek "erőforrás-készletnek" minősülnek.
- **Data Lake fájltípusok (parketta, Avro, ork)** – 1 a 18446744073709551615 (Long max) fájlokban (L3-vizsgálat) egy olyan mappában vagy partíciós csoporton belül, amely erőforrás- *készletnek* minősül.
- **Egyéb strukturált fájltípusok (JSON, XML, txt)** – az 100-es fájlokban a rendszer mintát vesz (L3-vizsgálat) egy olyan mappában vagy partíciós csoporton belül, amely az erőforrás-készletnek minősül.
- **SQL-objektumok és CosmosDB-entitások** – minden fájl L3-ra van beolvasva.
- **Dokumentumok** fájltípusai – az egyes fájlok L3-ban vannak beolvasva. Az erőforrás-készlet mintái nem vonatkoznak ezekre a fájltípusokra.

## <a name="scan-regions"></a>Vizsgálati régiók
Az alábbi lista felsorolja az összes olyan Azure-adatforrást (adatközpont-) régiót, ahol a hatáskörébe tartozó képolvasó fut. Ha az Azure-adatforrás ezen a listán kívüli régióban található, a képolvasó a hatáskörébe tartozó példány régiójában fog futni.
 
### <a name="purview-scanner-regions"></a>A hatáskörébe tartozó képolvasó régiói

- EastUs
- EastUs2 
- SouthCentralUS
- WestUs
- WestUs2
- SoutheastAsia
- WestEurope
- NorthEurope
- UkSouth
- AustraliaEast
- CanadaCentral
- BrazilSouth
- CentralIndia
- JapanEast
- SouthAfricaNorth
- FranceCentral

## <a name="classification"></a>Besorolás

Az összes 105 rendszerbesorolási szabály a strukturált fájlformátumokra vonatkozik. Csak az MCE besorolási szabályok érvényesek a dokumentumok fájltípusára (nem az adatvizsgálati natív regex-mintákra, a Bloom Filter-alapú észlelésre). A támogatott besorolásokkal kapcsolatos további információkért lásd: [támogatott besorolások az Azure hatáskörébe](supported-classifications.md).

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: a Starter Kit futtatása és az adatvizsgálat](tutorial-scan-data.md)
- [Adatforrások kezelése az Azure hatáskörébe (előzetes verzió)](manage-data-sources.md)