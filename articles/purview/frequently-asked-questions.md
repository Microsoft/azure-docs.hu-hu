---
title: Gyakori kérdések (GYIK)
description: Ez a cikk az Azure hatáskörébe kapcsolatos gyakori kérdésekre ad választ.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 94b765cbcbdd81505b08052845207ee1d93a28d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667802"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>Gyakran ismételt kérdések (GYIK) az Azure-beli hatáskörébe

## <a name="overview"></a>Áttekintés

Számos szervezet nem rendelkezik átfogó ismeretekkel az adatkezelésről. Meg kell ismernie, hogy milyen adatokat tartalmaz, hol találhatók az adatokat, és hogy miként lehet megkeresni és elérni a kapcsolódó adatokat. Az adatok nem rendelkeznek olyan környezettel, mint a Lineage, a besorolás és az átfogó metaadatok, így az üzleti felhasználók megnehezítik a megfelelő adatok keresését és az adatok megfelelő felhasználását. Ennek eredményeképpen az összegyűjtött adatok csak kis hányadát használják az üzleti döntések meghozatalára. Végül az adatbiztonsággal kapcsolatos problémák azonosítása és a bizalmas adatok védelme inkonzisztens. A szolgáltatásnak folyamatos időt és erőfeszítést, különösen az adatrugalmasságot kell fenntartania.

Az Azure hatáskörébe egy adatirányítási megoldás. Segít az ügyfeleknek az összes adat mélyebb megismerésében, miközben az irányítást a használat felett tartja. Az Azure hatáskörébe tartozik a szervezetek felderítése és a kurátorok beszerzése. Betekintést nyerhetnek az adatközpontba, és központilag szabályozzák az adathozzáférést.

## <a name="purpose-of-this-faq"></a>A GYIK célja

Ez a gyakori kérdések olyan gyakori kérdésekre válaszolnak, amelyeket az ügyfelek és a csoportok gyakran kérnek. Az Azure-beli hatáskörébe és a kapcsolódó megoldásokra, például a Azure Data Catalog (ADC) Gen 2 (elavult) és Azure Information Protectionra vonatkozó kérdések tisztázására szolgál.

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>Milyen típusú források érhetők el a metaadatok vizsgálatához és besorolásához?

|Azure|Nem Azure|
|---------|---------|
|Azure Blob Storage|Power BI|
|Azure Synapse Analytics (SQL DW)|SQL Server |
|Azure Cosmos DB|Teradata (2020-es végével érhető el)|
|Felügyelt Azure SQL-példány|SAP ECC (2020-es végével érhető el)|
|Azure Adatkezelő|SAP S/4 HANA (2020-es végével érhető el)|
|1. generációs Azure Data Lake Storage|Kaptár Metaadattár (2020-es végével érhető el)|
|2. generációs Azure Data Lake Storage|Amazon S3|
|Azure Files|--|
|Azure SQL Database|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>Milyen adatrendszerek/processzorok csatlakozhatnak és lekérhetik a lineageet?

|Adatfeldolgozórendszer/processzor 
|---------
|Azure Data Factory: másolási tevékenység, adatfolyam-tevékenység 
|Egyéni Lineage   
|Azure Data Share   
|Power BI    |
|SQL Server Integration Services  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>Hogyan történik az ADC Gen 2, Azure Information Protection és az Azure hatáskörébe kapcsolódó?

Az Azure-beli hatáskörébe eredetileg az ADC Gen 2 néven indult el, de azóta is kibővült a hatókör. A szolgáltatás most már magában foglalja az ADC Gen 2 speciális katalógusának képességeit, valamint a Azure Information Protection adatbesorolási, címkézési és megfelelőségi szabályzat-kényszerítési képességeit. Napjainkban még jobban igazodik az adatkezelés szélesebb körének meghatározásához.

### <a name="what-happens-to-customers-using-adc-gen-1"></a>Mi történik az ügyfelekkel az 1. generációs ADC használatával?

Az Azure hatáskörébe tartozik a Microsoft katalógus-megoldási területének minden termék-innovációja. Az 1. ADC-Gen továbbra is támogatott lesz.

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>Az ügyfeleknek több Azure-beli hatáskörébe tartozó fiókjuk is van ugyanabban az előfizetésben?

Igen, az előfizetések és a bérlők számos Azure-beli hatáskörébe tartozó fiókot támogatunk.

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>Futtatható-e párhuzamosan az ADC Gen 1 és az Azure hatáskörébe?

Igen. Mindkettő független szolgáltatás.

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>Hogyan áttelepítheti a meglévő ADC Gen 1 adategységeket az Azure hatáskörébe?

Az Azure-beli hatáskörébe API-k használatával kinyerheti az 1. és az Azure-beli rendszerbe való betöltést. A Szószedet esetében a CSV-alapú tömeges eszközöket támogatjuk.

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>Hogyan a bizalmas adatokat az SQL-táblákhoz az Azure hatáskörébe?

Az adattitkosítás az adatforrás szintjén történik. Az Azure hatáskörébe csak a metaadatok vannak tárolva. Nem jeleníti meg az adatmegjelenítést.

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>Az összes, az ADC Gen 2 funkció elérhető az Azure hatáskörébe?

Igen.

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>Mi a különbség a szószedet és a besorolás között?

A Szószedet a elnevezési konvenciót követi, amelyet a nem műszaki/üzleti felhasználók, más néven adatfogyasztók is használnak. Ezek a típusú személyek olyan üzleti elemzők vagy adatszakértők, akik az Azure-t használják bizonyos típusú adatok keresésére az üzleti használat alapján. Előfordulhat például, hogy az ellátási lánc elemzői számára meg kell keresni az *SKU-típusok* és a *küldemények adatait*. Ezek a kifejezések a Szószedet alapján keresik meg a releváns adatokat.
A besorolás egy olyan címke, amely egy olyan adategységre vonatkozik, amely a tábla, oszlop vagy fájl szintjén szerepel, és amely meghatározza, hogy az adott objektum milyen adategységeket tartalmaz. A besorolás a talált adattípustól függően automatikusan vagy manuálisan is alkalmazható. A besorolási címkék használatával általában azonosítható, hogy egy adott eszköz bizalmas adatokat tartalmaz-e, és hogy milyen típusú bizalmas adatokra van szükség.

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>Az Azure hatáskörébe tartozik az e-mailek, PDF-fájlok stb. bevizsgálása és besorolása a saját SharePoint-és OneDrive?

A helyszíni SharePoint-webhelyek és-kódtárak vizsgálatát a Azure Information Protection szkenneren keresztül biztosítjuk. A képolvasó a következő SKU-k használatával érhető el az ügyfél Microsoft 365-előfizetésén keresztül: az M365, az EMR P1, az EMS E3 és az E3. Ha rendelkezik ilyen SKU-kal, a megfelelő jogosultságokkal kell rendelkeznie a Azure Information Protection képolvasó használatának megkezdéséhez.

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>Milyen számítási feladatokra van szükség a vizsgálathoz?
Létezik egy Microsoft által felügyelt keresési infrastruktúra. A támogatott Azure-/AWS-erőforrások esetében nem szükséges beolvasási infrastruktúrát telepítenie.

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>Van lehetőség az Azure-beli környezet kiépítésére Azure Resource Manager (ARM) sablon/CLI/PowerShell használatával?

Igen, az ARM-sablon elérhető

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>Már használom az Atlas szolgáltatást, egyszerűen áthelyezhetem az Azure hatáskörébe?

Az Azure-beli hatáskörébe kompatibilis az Atlas API-val. Ha az Atlasból végzi az áttelepítést, javasoljuk, hogy először az Azure hatáskörébe kell beolvasnia az adatforrásokat. Ha az eszközök elérhetők a fiókjában, a hasonló Atlas API-k használatával integrálhatja például az eszközök frissítését vagy egyéni Lineage hozzáadását. Az Azure-beli hatáskörébe a Azure Search használatára módosítja a Search API-t, így lehetővé kell tenni az előzetes keresés használatát.

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>Hozhatok létre több katalógust a bérlőben?

Igen, előfizetéshez és bérlőhöz több Azure-beli hatáskörébe tartozó fiókot is létrehozhat. Áttekintheti a korlátozások lapot az [Azure hatáskörébe tartozó erőforrások kvótáinak kezeléséhez és növeléséhez](how-to-manage-quotas.md).

Ha további javaslatra van szüksége, vagy ha nem rendelkezik több fiókkal, az [Azure-beli központi telepítéssel kapcsolatos ajánlott eljárások](deployment-best-practices.md)című dokumentumban olvashat.

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>Több bérlőt is regisztrálhatok egyetlen Azure-beli hatáskörébe tartozó fiókban?

Nem, jelenleg egy másik bérlő adatforrásának vizsgálatához létre kell hoznia egy külön Azure-beli hatáskörébe tartozó fiókot a bérlőben.

### <a name="does-azure-purview-support-column-level-lineage"></a>Támogatja az Azure hatáskörébe az oszlopok szintjének szintjét?

Igen, az Azure-beli hatáskörébe az oszlopok szintjének leszármazása is támogatott.