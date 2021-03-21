---
title: Az adatkezelési átjáró kibocsátási megjegyzései
description: adatkezelés Gateway Tory kibocsátási megjegyzései
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: b5bf0c6d6996a67e8b0fec3ec51e31fef8eedf26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363916"
---
# <a name="release-notes-for-data-management-gateway"></a>Az adatkezelési átjáró kibocsátási megjegyzései
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a következőt: saját üzemeltetésű [integrációs modul a v2-ben](../create-self-hosted-integration-runtime.md).

A modern Adatintegráció egyik kihívása a helyszíni és a felhőbe irányuló adatok áthelyezése. A Data Factory a adatkezelés átjáróval való integrációt is lehetővé teszi, amely egy olyan ügynök, amely a helyszínen telepíthető a hibrid adatáthelyezés lehetővé tételéhez.

A adatkezelés-átjáróval és annak használatával kapcsolatos részletes információkért tekintse meg a következő cikkeket:

*  [Adatkezelési átjáró](data-factory-data-management-gateway.md)
*  [Az adatáthelyezés a helyszíni és a felhő között Azure Data Factory használatával](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>AKTUÁLIS VERZIÓ 
A kibocsátási megjegyzések itt nem állnak fenn. A legújabb [kibocsátási megjegyzések letöltése](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Korábbi verziók
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Fejlesztések
- Hozzáadhat DNS-bejegyzéseket a Service Bus engedélyezéséhez ahelyett, hogy az összes Azure-beli IP-címet engedélyezi a tűzfalról (ha szükséges). Megtalálhatja a megfelelő DNS-bejegyzést Azure Portal (Data Factory-> "author and Deploy"-> "átjárók"-> "serviceUrls" (JSON-ban)
- A HDFS Connector mostantól támogatja az önaláírt nyilvános tanúsítványokat a TLS-érvényesítés kihagyása mellett.
- Kijavítva: hiba történt az átjáróval a frissítés során (az óra torzítása miatt)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Fejlesztések
-    Hozzáadhat olyan DNS-bejegyzéseket, amelyek lehetővé teszik Service Bus használatát, és nem engedélyezi az összes Azure-beli IP-címet a tűzfalról (ha szükséges). További részletek.
-    Mostantól akár 4,75 TB-ig is másolhatja az adatait egy blokkos blobba, amely a blokkolt Blobok maximálisan támogatott mérete. (korábbi korlát: 195 GB).
-    Kijavítva: kevés a memória-probléma, miközben a másolási tevékenység során több kis fájlt is kicsomagolta.
-    Kijavítva: az index tartományon kívüli probléma a Document DB-ről SQL Server a idempotencia szolgáltatással való másoláskor.
-    Kijavítva: az SQL-karbantartási parancsfájl nem működik SQL Server a másolás varázslóval.
-    Javítva: az oszlop neve, amelynek a végén található a helye, nem működik a másolási tevékenységben.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Fejlesztések
- Kijavítva: hiányzó hitelesítő adatokkal rendelkező probléma az átjáró gép újraindításakor.
- Kijavítva: a regisztráció során a rendszer egy biztonságimásolat-fájl használatával adja meg az átjáró visszaállítását.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Fejlesztések
- Kijavítva: az Oracle-ből származó decimális Null érték helytelen olvasása forrásként.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Újdonságok
- Az ügyfelek visszajelzéseket adhatnak az átjáró regisztrálásához.
- Új tömörítési formátum támogatása: ZIP (deflate)

### <a name="enhancements-"></a>Fejlesztések
- Teljesítmény-fejlesztés az Oracle-fogadó, HDFS-forrás esetében.
- Hibajavítás az átjárók automatikus frissítéséhez, az átjáró párhuzamos feldolgozási kapacitásának javítása.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Fejlesztések
- Továbbfejlesztett és megbízhatóbb átjáró-regisztrációs élmény – mostantól nyomon követheti a folyamat állapotát az átjáró regisztrálási folyamata során, így a regisztrációs élmény még rugalmasabb lehet.
- Az átjárók visszaállítási folyamatának továbbfejlesztése – akkor is helyreállíthatja az átjárót, ha nem rendelkezik ezzel a frissítéssel az átjáró biztonságimásolat-fájljával. Ehhez vissza kell állítania a társított szolgáltatás hitelesítő adatait a portálon.
- Hibajavítás.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Újdonságok

- Mostantól helyileg tárolhatja az adatforrás hitelesítő adatait. A hitelesítő adatok titkosítva vannak. Az adatforráshoz tartozó hitelesítő adatok helyreállíthatók és visszaállíthatók a meglévő átjáróról, az összes helyszínen exportálható biztonságimásolat-fájllal.

### <a name="enhancements-"></a>Fejlesztések

- Továbbfejlesztett és robusztusabb átjáró-regisztrációs élmény.
- Támogatja a QuoteChar-konfiguráció automatikus észlelését a másolási varázslóban a szöveges formátumhoz, és javíthatja az általános formázási pontosságot.

## <a name="2361002"></a>2.3.6100.2

- A firstRowAsHeader és a SkipLineCount automatikus észlelésének támogatása a helyi fájlrendszerben és a HDFS lévő szövegfájlok másolási varázslójával.
- Az átjáró és a Service Bus közötti hálózati kapcsolat stabilitásának növelése
- Néhány hibajavítás


## <a name="2260721"></a>2.2.6072.1

*  Támogatja az átjáróhoz tartozó HTTP-proxy beállítását az átjáró Configuration Manager használatával. Ha be van állítva, az Azure Blob, az Azure Table, a Azure Data Lake és a Document DB a HTTP-proxyn keresztül érhető el.
*  A támogatja az Szövegformátum fejléc-kezelését az Azure Blob, Azure Data Lake Store, helyszíni fájlrendszer és helyszíni HDFS adatainak másolása során.
*  Támogatja az adatok másolását a blob és az oldal Blobból a már támogatott blokk Blobtal együtt.
*  Bevezet egy új átjáró-állapotot **online (korlátozott)**, amely azt jelzi, hogy az átjáró fő funkciója működik, kivéve a másolás varázsló interaktív művelet általi támogatását.
*  Növeli az átjárók regisztrációjának megbízhatóságát a regisztrációs kulccsal.

## <a name="216040"></a>2.1.6040.

*  A DB2-illesztőprogramot most már az átjáró telepítési csomagja tartalmazza. így nem kell külön telepítenie.
*  A DB2-illesztőprogram mostantól támogatja a z/OS és a DB2-t (AS/400) a már támogatott platformokkal (Linux, UNIX és Windows) együtt.
*  Támogatja Azure Cosmos DB használatát a helyszíni adattárakhoz forrásként vagy célhelyként
*  A támogatja az adatok másolását a/a hideg/gyors blob Storage-ba és a már támogatott általános célú Storage-fiókkal együtt.
*  Lehetővé teszi az átjárón keresztüli SQL Server kapcsolódását távoli bejelentkezési jogosultságokkal.  

## <a name="2060131"></a>2.0.6013.1

*  Kiválaszthatja az átjáró által a manuális telepítés során használandó nyelvet/kultúrát.

*  Ha az átjáró nem a várt módon működik, dönthet úgy, hogy az elmúlt hét nap átjáró-naplóit küldi el a Microsoftnak a probléma elhárításának elősegítése érdekében. Ha az átjáró nincs csatlakoztatva a Cloud Service-hez, dönthet úgy, hogy menti és archiválja az átjáró naplóit.  

*  Az átjáró Configuration Manager felhasználói felületének fejlesztése:

    *  Az átjáró állapotának láthatóvá tétele a kezdőlapon.

    *  Átszervezve és egyszerűsített vezérlők.

    *  Az adatok a [kód – ingyenes másolási eszközzel](data-factory-copy-data-wizard-tutorial.md)másolhatók a tárolóból. A szolgáltatással kapcsolatos további részletekért tekintse meg a [szakaszos másolást](data-factory-copy-activity-performance.md#staged-copy) .
*  A adatkezelés Gateway segítségével közvetlenül egy SQL Server-adatbázisból is beáramlhat az adatokba Azure Machine Learning.

*  Teljesítménnyel kapcsolatos fejlesztések

    * Javítsa a teljesítményt a séma/előnézet megtekintésére SQL Server a kód nélküli másolás eszközben.

## <a name="11259531"></a>1.12.5953.1

*  Hibajavítások

## <a name="11159181"></a>1.11.5918.1

*  Az átjáró eseménynaplójának maximális mérete 1 MB-ról 40 MB-ra nőtt.

*  Egy figyelmeztető párbeszédablak jelenik meg, ha újraindításra van szükség az átjáró automatikus frissítése során. Dönthet úgy, hogy azonnal újraindul, vagy később.

*  Ha az automatikus frissítés meghiúsul, az átjáró telepítője a maximálisan háromszor újrapróbálkozik az automatikus frissítéssel.

*  Teljesítménnyel kapcsolatos fejlesztések

    * Javítsa a nagy táblák betöltését a helyszíni kiszolgálóról a kód nélküli másolási forgatókönyvben.

*  Hibajavítások

## <a name="11058921"></a>1.10.5892.1

*  Teljesítménnyel kapcsolatos fejlesztések

*  Hibajavítások

## <a name="1958652"></a>1.9.5865.2

*  Nulla érintéses automatikus frissítési képesség
*  Új tálca ikon átjáró állapotának indikátorokkal
*  A "frissítés most" lehetőség az ügyfélről
*  A frissítési ütemterv időpontjának beállítása
*  PowerShell-parancsfájl az automatikus frissítés be-és kikapcsolásakor
*  JSON-formátum támogatása  
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

## <a name="1858221"></a>1.8.5822.1

*  A hibaelhárítási élmény javítása
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1757951"></a>1.7.5795.1

*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1757641"></a>1.7.5764.1

*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1657351"></a>1.6.5735.1

*  Helyszíni HDFS forrás/fogadó támogatása
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1656961"></a>1.6.5696.1

*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1656761"></a>1.6.5676.1

*  A Configuration Manager diagnosztikai eszközeinek támogatása
*  Az Azure Data Factory táblázatos adatforrásaihoz tartozó táblázat oszlopainak támogatása
*  Az Azure szinapszis Analytics támogatása Azure Data Factory
*  Támogatás kivonulása a BlobSource-ben és a FileSource-ben Azure Data Factory
*  CopyBehavior támogatása – MergeFiles, PreserveHierarchy és FlattenHierarchy a BlobSink és a FileSink bináris példánnyal Azure Data Factory
*  A másolási tevékenység jelentéskészítési folyamatának támogatása Azure Data Factory
*  Adatforrás-kapcsolat ellenőrzésének támogatása Azure Data Factory
*  Hibajavítások

### <a name="1656721"></a>1.6.5672.1

*  A Azure Data Factory ODBC-adatforrásának támogatási táblázatának neve
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1656581"></a>1.6.5658.1

*  A Azure Data Factory fájl fogadójának támogatása
*  A-hierarchia megőrzésének támogatása a bináris másolásban Azure Data Factory
*  A másolási tevékenység idempotencia támogatása Azure Data Factory
*  Hibajavítások

### <a name="1656401"></a>1.6.5640.1

*  További 3 adatforrások támogatása Azure Data Factoryhoz (ODBC, OData, HDFS)
*  Az idézőjel karakterének támogatása a CSV-elemzőben Azure Data Factory
*  Tömörítési támogatás (BZip2)
*  Hibajavítások

### <a name="1556121"></a>1.5.5612.1

*  Öt, a Azure Data Factoryhoz (MySQL, PostgreSQL, DB2, Teradata és Sybase) kapcsolódó adatbázisok támogatása
*  Tömörítési támogatás (gzip és deflate)
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1455491"></a>1.4.5549.1

*  Oracle-adatforrás támogatásának hozzáadása Azure Data Factory
*  Teljesítménnyel kapcsolatos fejlesztések
*  Hibajavítások

### <a name="1454921"></a>1.4.5492.1

*  Egységesített bináris fájl, amely a Microsoft Azure Data Factory és az Office 365 Power BI szolgáltatásokat egyaránt támogatja
*  A konfigurációs felhasználói felület és a regisztrációs folyamat pontosítása
*  Azure Data Factory – az Azure bejövő és kimenő támogatása SQL Server adatforráshoz

### <a name="1253031"></a>1.2.5303.1

*  Javítsa ki az időtúllépési hibát az időigényes adatforrás-kapcsolatok támogatásához.

### <a name="1155268"></a>1.1.5526.8

*  A telepítéséhez a .NET-keretrendszer 4.5.1-es verziójának előfeltétele.

### <a name="1051442"></a>1.0.5144.2

*  Nincs olyan változás, amely hatással van Azure Data Factory forgatókönyvekre.
