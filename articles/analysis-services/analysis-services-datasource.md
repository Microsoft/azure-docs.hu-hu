---
title: A Azure Analysis Services által támogatott adatforrások | Microsoft Docs
description: A táblázatos 1200-es és újabb adatmodellek által támogatott adatforrásokat és összekötőket ismerteti Azure Analysis Servicesban.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6e558962ad8a84b5f44abe21bc7c0ab67a4861ba
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493820"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Támogatott adatforrások az Azure Analysis Servicesben

Az adatforrások és az összekötők a Visual Studióban Analysis Services-projektekkel együtt jelennek meg a beolvasás vagy a tábla importálása varázslóban Azure Analysis Services és SQL Server Analysis Services esetében is. A Azure Analysis Servicesban azonban nem minden megjelenített adatforrás és összekötő támogatott. A csatlakozáshoz használható adatforrások típusai számos tényezőtől függenek, például a modell kompatibilitási szintjétől, a rendelkezésre álló adatösszekötők, a hitelesítési típus és a helyszíni adatátjárók támogatása. Az alábbi táblázatok a Azure Analysis Services támogatott adatforrásait ismertetik.

## <a name="azure-data-sources"></a>Azure-beli adatforrások

|Adatforrás  |Memóriabeli  |DirectQuery  |Megjegyzések |
|---------|---------|---------|---------|
|Azure SQL Database      |   Igen      |    Igen      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL DW)      |   Igen      |   Igen       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   Igen       |    Nem      | <sup>[1](#tab1400a)</sup> |
|Azure Table Storage     |   Igen       |    Nem      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Igen        |  Nem        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Storage Gen1      |   Igen       |    Nem      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Storage Gen2       |   Igen       |    Nem      |<sup>[1](#tab1400a)</sup>, <sup> [5](#gen2)</sup>|
|Azure HDInsight-HDFS    |     Igen     |   Nem       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Igen       |   Nem       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**Megjegyzések**:

<a name="tab1400a">1</a> – táblázatos 1400 és újabb modellek.  
<a name="azprovider">2</a> – ha *szolgáltatói* adatforrásként van megadva a táblázatos 1200-es és a magasabb modellekben, a memóriában és a DirectQuery-modellekben a Microsoft OLE DB illesztőprogram szükséges a SQL Server MSOLEDBSQL (ajánlott), a SQL Server Native Client 11,0 vagy a .NET-keretrendszer adatszolgáltatójának SQL Server.  
<a name="azsqlmanaged">3</a> – az Azure SQL felügyelt példánya támogatott. Mivel az SQL felügyelt példánya egy magánhálózati IP-címmel rendelkező Azure VNet fut, a példányon engedélyezni kell a nyilvános végpontot. Ha nincs engedélyezve, helyszíni [adatátjáró](analysis-services-gateway.md) szükséges.  
<a name="databricks">4</a> – Azure Databricks a Spark-összekötő használata jelenleg nem támogatott.  
<a name="gen2">5</a> – ADLS Gen2 összekötő jelenleg nem támogatott, de az Azure Blob Storage Connector ADLS Gen2 adatforrással is használható.

## <a name="other-data-sources"></a>Más adatforrások

|Adatforrás | Memóriabeli | DirectQuery |Jegyzetek   |
|  --- | --- | --- | --- |
|Access-adatbázis     |  Igen | Nem |  |
|Active Directory     |  Igen | Nem | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  Igen | Nem |  |
|Elemzési platform rendszer     |  Igen | Nem |  |
|CSV-fájl  |Igen | Nem |  |
|Dynamics 365     |  Igen | Nem | <sup>[6](#tab1400b)</sup> |
|Excel-munkafüzet     |  Igen | Nem |  |
|Exchange      |  Igen | Nem | <sup>[6](#tab1400b)</sup> |
|Mappa      |Igen | Nem | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Igen | Nem |  |
|JSON-dokumentum      |  Igen | Nem | <sup>[6](#tab1400b)</sup> |
|Bináris sorok      | Igen | Nem | <sup>[6](#tab1400b)</sup> |
|MySQL-adatbázis     | Igen | Nem |  |
|OData-adatcsatorna      |  Igen | Nem | <sup>[6](#tab1400b)</sup> |
|ODBC-lekérdezés     | Igen | Nem |  |
|OLE DB     |   Igen | Nem |  |
|Oracle  | Igen  |Igen  | <sup>[9](#oracle)</sup> |
|PostgreSQL-adatbázis   | Igen | Nem | <sup>[6](#tab1400b)</sup> |
|Salesforce-objektumok|  Igen | Nem | <sup>[6](#tab1400b)</sup> |
|Salesforce-jelentések |Igen | Nem | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  Igen | Nem |  |
|SAP Business Warehouse    |  Igen | Nem | <sup>[6](#tab1400b)</sup> |
|SharePoint-lista      |   Igen | Nem | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |Igen   | Igen  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Adattárház SQL Server |Igen   | Igen  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Sybase-adatbázis     |  Igen | Nem |  |
|Teradata | Igen  | Igen  | <sup>[10](#teradata)</sup> |
|TXT-fájl  |Igen | Nem |  |
|XML-táblázat    |  Igen | Nem | <sup>[6](#tab1400b)</sup> |
| | | |

**Megjegyzések**:  
<a name="tab1400b">6</a> – csak táblázatos 1400 és újabb modellek.  
<a name="sqlim">7</a> – ha *szolgáltatói* adatforrásként van megadva a táblázatos 1200 és a magasabb modellekben, adja meg a Microsoft OLE DB illesztőprogramot SQL Server MSOLEDBSQL (ajánlott), a SQL Server Native Client 11,0 vagy a .NET-keretrendszer adatszolgáltatóját SQL Serverhoz.  
<a name="instgw">8</a> – ha a MSOLEDBSQL adatszolgáltatóként van megadva, előfordulhat, hogy a helyszíni adatátjáróval megegyező számítógépen le kell töltenie és telepítenie kell a [Microsoft OLE DB illesztőprogramot a SQL Serverhoz](/sql/connect/oledb/oledb-driver-for-sql-server) .  
<a name="oracle">9</a> – táblázatos 1200-modellekhez vagy *szolgáltatói* adatforráshoz táblázatos 1400 + modellekben, a .net-hez készült Oracle-adatszolgáltató megadása. Strukturált adatforrásként való megadása esetén ügyeljen arra, hogy az [Oracle által felügyelt szolgáltatót engedélyezze](#enable-oracle-managed-provider).   
<a name="teradata">10</a> – táblázatos 1200-modellekhez vagy *szolgáltatói* adatforrásként táblázatos 1400 + modellekben a Teradata-adatszolgáltató megadása a .net-hez.  
<a name="filesSP">11</a> – a helyszíni SharePointban található fájlok nem támogatottak.

Egy Azure Analysis Services kiszolgáló helyszíni adatforrásaihoz való csatlakozáshoz helyszíni [átjáró](analysis-services-gateway.md)szükséges. Átjáró használata esetén 64 bites szolgáltatók szükségesek.

## <a name="understanding-providers"></a>Szolgáltatók ismertetése

Táblázatos 1400 és a Visual Studióban a magasabb modellbeli projektek létrehozásakor alapértelmezés szerint nem adhat meg adatszolgáltatót adatforráshoz való csatlakozáskor az **adatlekérdezés** használatával. A táblázatos 1400-es és újabb modellek [Power Query](/power-query/power-query-what-is-power-query) összekötőket használnak a kapcsolatok, az adatlekérdezések és az adatforrások és a Analysis Services közötti adategyesítések kezelésére. Ezeket más néven *strukturált* adatforrás-kapcsolatoknak nevezzük, amelyekben a kapcsolati tulajdonságok beállításai vannak beállítva. A Visual Studióban azonban engedélyezheti az örökölt adatforrásokat a modell-projektekhez. Ha engedélyezve van, a **Táblázat importálása varázslóval** kapcsolódhat a táblázatos 1200-ben hagyományosan támogatott adatforrásokhoz, valamint az *örökölt* vagy *szolgáltatói* adatforrásként használt alacsonyabb modellekhez. Ha szolgáltatói adatforrásként van megadva, megadhat egy adott adatszolgáltatót és egyéb speciális kapcsolódási tulajdonságokat. Csatlakozhat például egy SQL Server adattárház-példányhoz, vagy akár egy Azure SQL Database örökölt adatforrásként is. Ezután kiválaszthatja a OLE DB illesztőprogramot SQL Server MSOLEDBSQL adatszolgáltatóhoz. Ebben az esetben egy OLE DB adatszolgáltató kiválasztása nagyobb teljesítményt biztosíthat az Power Query-összekötőn keresztül. 

Ha a Visual Studióban a tábla importálása varázslót használja, az adatforrásokhoz való kapcsolódáshoz adatszolgáltató szükséges. Az alapértelmezett adatszolgáltató van kiválasztva. Szükség esetén módosíthatja az adatszolgáltatót. A választott szolgáltató típusa a teljesítménytől függ, függetlenül attól, hogy a modell memóriában tárolt tárolót vagy DirectQuery használ-e, és Analysis Services platformot, amelybe üzembe helyezi a modellt.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Szolgáltatói adatforrások meghatározása táblázatos 1400 és magasabb modell-projektekben

A szolgáltatói adatforrások engedélyezéséhez a Visual Studióban kattintson az **eszközök**  >  **Beállítások**  >  **Analysis Services táblázatos**  >  **adatimportálás** elemre, majd válassza az **örökölt adatforrások engedélyezése** lehetőséget.

![Örökölt adatforrások engedélyezése](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Ha az örökölt adatforrások engedélyezve vannak, a **táblázatos modell Explorerben** kattintson a jobb gombbal az **adatforrások** importálás adatforrásból  >  **(örökölt)** elemre.

![Örökölt adatforrások a táblázatos modell Explorerben](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Mint a táblázatos 1200 modell projektjeihez hasonlóan, a **tábla importálása varázslóval** csatlakozhat egy adatforráshoz. A kapcsolat lapon kattintson a **speciális** elemre. Adja meg az adatszolgáltatót és az egyéb kapcsolatbeállításokat a **Speciális tulajdonságok beállítása** lapon.

![Örökölt adatforrások speciális tulajdonságai](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>Megszemélyesítési
Bizonyos esetekben szükség lehet egy másik megszemélyesítési fiók megadására. A megszemélyesítési fiók megadható a Visual Studióban vagy SQL Server Management Studioban (SSMS).

Helyszíni adatforrások esetén:

* SQL-hitelesítés használata esetén a Megszemélyesítésnek szolgáltatásfiók-nek kell lennie.
* Windows-hitelesítés használata esetén állítsa be a Windows-felhasználó/jelszó beállítását. SQL Server esetében az adott megszemélyesítési fiókkal rendelkező Windows-hitelesítés csak memóriában tárolt adatmodellek esetén támogatott.

Felhőbeli adatforrások esetén:

* SQL-hitelesítés használata esetén a Megszemélyesítésnek szolgáltatásfiók-nek kell lennie.

## <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Ha *szolgáltatói* adatforrásként van megadva, Azure Analysis Services támogatja a [MSOLEDBSQL](/sql/connect/oledb/release-notes-for-oledb-driver-for-sql-server) Azure Active Directory egyszerű hitelesítését Azure SQL Database és az Azure szinapszis adatforrásaihoz.

`
Provider=MSOLEDBSQL;Data Source=[server];Initial Catalog=[database];Authentication=ActiveDirectoryServicePrincipal;User ID=[Application (client) ID];Password=[Application (client) secret];Use Encryption for Data=true
`

## <a name="oauth-credentials"></a>OAuth hitelesítő adatai

A 1400-es és újabb kompatibilitási szinten található táblázatos modellek *memórián belüli* módban, Azure SQL Database, az Azure szinapszis, a Dynamics 365 és a SharePoint-lista segítségével támogatják a OAuth hitelesítő adatait. Érvényes tokenek létrehozásához Power Query használatával állítsa be a hitelesítő adatokat. Azure Analysis Services kezeli a OAuth-adatforrások jogkivonat-frissítését, hogy elkerülje a hosszan futó frissítési műveletek időtúllépését. 
> [!NOTE]
> A felügyelt jogkivonat frissítése nem támogatott az átjárón keresztül elért adatforrások esetén. Például egy vagy több adatforráshoz egy vagy több adatforrás van hozzáférése egy átjárón keresztül, és/vagy a [ASPaaS\AlwaysUseGateway](analysis-services-vnet-gateway.md) tulajdonság értéke **true (igaz**). 

A OAuth hitelesítő adatai nem támogatják a közvetlen lekérdezési módot.

## <a name="enable-oracle-managed-provider"></a>Oracle által felügyelt szolgáltató engedélyezése

Bizonyos esetekben előfordulhat, hogy egy Oracle-adatforráshoz tartozó DAX-lekérdezések váratlan eredményeket adnak vissza. Ennek oka lehet az adatforrás-kapcsolódáshoz használt szolgáltató.

A [szolgáltatók ismertetése](#understanding-providers) című szakaszban leírtak szerint a táblázatos modellek *strukturált* adatforrásként vagy *szolgáltatói* adatforrásként csatlakoznak az adatforrásokhoz. A szolgáltatói adatforrásként megadott Oracle-adatforrással rendelkező modellek esetén győződjön meg arról, hogy a megadott szolgáltató a .NET-hez készült Oracle-adatszolgáltató (Oracle. az adatelérési. Client). 

Ha az Oracle-adatforrás strukturált adatforrásként van megadva, engedélyezze a **MDataEngine\UseManagedOracleProvider** -kiszolgáló tulajdonságot. Ha ezt a tulajdonságot állítja be, a modell az Oracle-adatforráshoz csatlakozik az ajánlott Oracle-adatszolgáltató használatával a .NET által felügyelt szolgáltatóhoz.
 
Az Oracle által felügyelt szolgáltató engedélyezése:

1. A SQL Server Management Studioban kapcsolódjon a kiszolgálóhoz.
2. Hozzon létre egy XMLA-lekérdezést a következő parancsfájllal. Cserélje le a **servername** nevet a teljes kiszolgálónévre, majd hajtsa végre a lekérdezést.

    ```xml
    <Alter AllowCreate="true" ObjectExpansion="ObjectProperties" xmlns="http://schemas.microsoft.com/analysisservices/2003/engine">
        <Object />
        <ObjectDefinition>
            <Server xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ddl2="http://schemas.microsoft.com/analysisservices/2003/engine/2" xmlns:ddl2_2="http://schemas.microsoft.com/analysisservices/2003/engine/2/2" 
    xmlns:ddl100_100="http://schemas.microsoft.com/analysisservices/2008/engine/100/100" xmlns:ddl200="http://schemas.microsoft.com/analysisservices/2010/engine/200" xmlns:ddl200_200="http://schemas.microsoft.com/analysisservices/2010/engine/200/200" 
    xmlns:ddl300="http://schemas.microsoft.com/analysisservices/2011/engine/300" xmlns:ddl300_300="http://schemas.microsoft.com/analysisservices/2011/engine/300/300" xmlns:ddl400="http://schemas.microsoft.com/analysisservices/2012/engine/400" 
    xmlns:ddl400_400="http://schemas.microsoft.com/analysisservices/2012/engine/400/400" xmlns:ddl500="http://schemas.microsoft.com/analysisservices/2013/engine/500" xmlns:ddl500_500="http://schemas.microsoft.com/analysisservices/2013/engine/500/500">
                <ID>ServerName</ID>
                <Name>ServerName</Name>
                <ServerProperties>
                    <ServerProperty>
                        <Name>MDataEngine\UseManagedOracleProvider</Name>
                        <Value>1</Value>
                    </ServerProperty>
                </ServerProperties>
            </Server>
        </ObjectDefinition>
    </Alter>
    ```

3. Indítsa újra a kiszolgálót.


## <a name="next-steps"></a>Következő lépések

* [Helyszíni átjáró](analysis-services-gateway.md)
* [A kiszolgáló kezelése](analysis-services-manage.md)