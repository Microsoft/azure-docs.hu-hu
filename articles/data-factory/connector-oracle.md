---
title: Adatok másolása az Oracle-ből és-ból az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat át a támogatott forrásokból származó adatok egy Oracle-adatbázisba vagy Oracle-ből a támogatott fogadó áruházakba Data Factory használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: jingwang
ms.openlocfilehash: 9e6be88af13d5dd7ddceba32ec08cab54ca5e3a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587270"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Adatok másolása az Oracle-ből és a rendszerből a Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuális verzió](connector-oracle.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok egy Oracle-adatbázisba való másolásához. A [másolási tevékenység áttekintésére](copy-activity-overview.md)épül.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Oracle-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az Oracle-adatbázisok adatait bármely támogatott fogadó adattárba másolhatja. Az adatok bármely támogatott forrásból származó adattárból is átmásolhatók egy Oracle-adatbázisba. A másolási tevékenység által forrásként vagy nyelőként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Pontosabban, ez az Oracle-összekötő a következőket támogatja:

- Egy Oracle-adatbázis következő verziói:
    - Oracle 19c R1 (19,1) és újabb verziók
    - Oracle 18c R1 (18,1) és újabb verziók
    - Oracle 12c R1 (12,1) és újabb verziók
    - Oracle 11g R1 (11,1) és újabb verziók
    - Oracle 10g R1 (10,1) és újabb
    - Oracle 9i R2 (9,2) és újabb
    - Oracle 8i R3 (8.1.7) és újabb verziók
    - Oracle Database Cloud Exadata szolgáltatás
- Párhuzamos másolás Oracle-forrásból. Részletekért tekintse meg az [Oracle párhuzamos másolási](#parallel-copy-from-oracle) szakaszát.

> [!Note]
> Az Oracle-proxykiszolgáló nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Az Integration Runtime egy beépített Oracle-illesztőprogramot biztosít. Ezért nem kell manuálisan telepítenie az illesztőprogramot, amikor a és az Oracle rendszerbe másol adatokból.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az Oracle-összekötőhöz tartozó Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Oracle társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot az **Oracle** értékre kell beállítani. | Yes |
| connectionString | Megadja az Oracle Database-példányhoz való kapcsolódáshoz szükséges adatokat. <br/>A jelszót Azure Key Vaultba is helyezheti, és lekérheti a `password` konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja Azure Key Vault a hitelesítő adatokat](store-credentials-in-key-vault.md) . <br><br>**Támogatott kapcsolattípus**: az **Oracle SID** vagy az **Oracle szolgáltatás nevét** használhatja az adatbázis azonosításához:<br>– Ha SID-t használ: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Ha a szolgáltatás nevét használja: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>A speciális Oracle natív kapcsolódási lehetőségeihez hozzáadhat egy bejegyzést a TNSNAMES-ben [. ORA](http://www.orafaq.com/wiki/Tnsnames.ora) -fájl az Oracle-kiszolgálón, és az ADF Oracle társított szolgáltatásában válassza az Oracle-szolgáltatásnév kapcsolat típusának használata lehetőséget, és konfigurálja a megfelelő szolgáltatásnevet. | Yes |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime használja. |No |

>[!TIP]
>Ha "ORA-01025: UPI paraméter a tartományon kívül" hibaüzenet jelenik meg, és az Oracle verziója 8i, adja hozzá `WireProtocolMode=1` a kapcsolódási karakterlánchoz. Ezután próbálkozzon újra.

Ha több Oracle-példánnyal rendelkezik a feladatátvételi forgatókönyvhöz, létrehozhat Oracle-alapú társított szolgáltatást, és kitöltheti az elsődleges gazdagépet, a portot, a felhasználónevet, a jelszót stb., és hozzáadhat egy új "**további kapcsolati tulajdonságot**" a tulajdonság nevével `AlternateServers` és az értékkel, és `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)` nem hagyja ki a szögletes zárójeleket, és nem kell figyelnie a kettőspontokra ( `:` ) az A másodlagos kiszolgálók következő értéke például két másodlagos adatbázis-kiszolgálót határoz meg a kapcsolat feladatátvételéhez: `(HostName=AccountingOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)` .

További kapcsolatok tulajdonságai az adott esetben a kapcsolatok karakterláncában állíthatók be:

| Tulajdonság | Leírás | Megengedett értékek |
|:--- |:--- |:--- |
| ArraySize |Az összekötő által egyetlen hálózati menetben lehívható bájtok száma. Például: `ArraySize=‭10485760‬` .<br/><br/>A nagyobb értékek csökkentik az adatátviteli sebességet azáltal, hogy az adatok hálózaton keresztüli lekérésének számával csökken. A kisebb értékek a válaszidő növelését jelentik, mivel kevesebb késéssel jár, amíg a kiszolgáló adatokat továbbít. | 1 és 4294967296 közötti egész szám (4 GB). Az alapértelmezett érték `60000`. Az 1. érték nem határozza meg a bájtok számát, de pontosan egy adatsor számára való lefoglalást jelez. |

Az Oracle-kapcsolatok titkosításának engedélyezéséhez két lehetőség közül választhat:

-   A **Triple-des encryption (3DES) és a Advanced Encryption Standard (AES)** használatához az Oracle Server oldalán nyissa meg az Oracle Advanced Security (OAS) oldalt, és konfigurálja a titkosítási beállításokat. További részletekért tekintse meg ezt az [Oracle-dokumentációt](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Az Oracle Application Development Framework (ADF) összekötő automatikusan egyezteti a titkosítási módszert, hogy a OAS-ben konfigurált konfigurációt használja az Oracle-hez való kapcsolódáskor.

-   A **TLS** használata:

    1.  A TLS/SSL-tanúsítvány adatainak beolvasása. Szerezze be a TLS/SSL-tanúsítvány DER-szabályok (DER) kódolású tanúsítványának adatait, és mentse a kimenetet (-----BEGIN Certificate... Záró tanúsítvány-----) szövegfájlként.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Példa:** Bontsa ki a CERT-adatokat a DERcert. cer fájlból, majd mentse a kimenetet cert.txtba.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Hozza létre a `keystore` vagy a-t `truststore` . A következő parancs `truststore` jelszóval vagy anélkül hozza létre a fájlt PKCS-12 formátumban.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Példa:** Hozzon létre egy `truststore` MyTrustStoreFile nevű PKCS12/pfx-profil-fájlt jelszóval.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Helyezze a `truststore` fájlt a saját üzemeltetésű IR-gépre. Tegyük fel például, hogy a fájl a következő helyen található: C:\MyTrustStoreFile.
    4.  A Azure Data Factoryban konfigurálja az Oracle-kapcsolatok karakterláncát a `EncryptionMethod=1` és a megfelelő `TrustStore` / `TrustStorePassword` értékkel. Például: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Példa**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: a Jelszó tárolása Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Ez a szakasz az Oracle-adatkészlet által támogatott tulajdonságok listáját tartalmazza. Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md). 

Az adatok és az Oracle közötti másoláshoz állítsa az adatkészlet Type (típus) tulajdonságát a következőre: `OracleTable` . A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát be kell állítani `OracleTable` . | Yes |
| schema | A séma neve. |Nem, forrás, igen, fogadó  |
| tábla | A tábla vagy nézet neve. |Nem, forrás, igen, fogadó  |
| tableName | A tábla/nézet neve a sémával. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Az új számítási feladatokhoz használja a és a elemet `schema` `table` . | Nem, forrás, igen, fogadó |

**Példa**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz az Oracle-forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza. A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle forrásként

>[!TIP]
>Az Oracle-adatoknak az adatparticionálással való hatékony betöltéséhez az Oracle-ből származó [párhuzamos másolásról](#parallel-copy-from-oracle)többet is megtudhat.

Az Oracle-adatok másolásához állítsa a forrás típusát a másolási tevékenységbe `OracleSource` . A másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: `OracleSource` . | Yes |
| oracleReaderQuery | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `"SELECT * FROM MyTable"`.<br>Ha engedélyezi a particionált terhelést, össze kell kapcsolnia a lekérdezéshez tartozó beépített partíciós paramétereket. Példaként tekintse meg az [Oracle párhuzamos másolási](#parallel-copy-from-oracle) szakaszát. | No |
| partitionOptions | Az Oracle-adatok betöltéséhez használt adatparticionálási beállításokat adja meg. <br>Az engedélyezett értékek a következők: **none** (alapértelmezett), **PhysicalPartitionsOfTable** és **DynamicRange**.<br>Ha engedélyezve van egy partíciós beállítás (azaz nem `None` ), az Oracle-adatbázisok adatainak párhuzamos betöltésének foka a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállításai vezérlik. | No |
| partitionSettings | Határozza meg az adatparticionálási beállítások csoportját. <br>Akkor alkalmazza, ha a partíció lehetőség nem `None` . | No |
| partitionNames | A másolandó fizikai partíciók listája. <br>Akkor alkalmazza, ha a partíciós beállítás van `PhysicalPartitionsOfTable` . Ha lekérdezést használ a forrásadatok beolvasásához, `?AdfTabularPartitionName` a WHERE záradékban lévő hookot. Példaként tekintse meg az [Oracle párhuzamos másolási](#parallel-copy-from-oracle) szakaszát. | No |
| partitionColumnName | Adja meg a forrás oszlop **olyan egész típusú** nevét, amelyet a párhuzamos másolási tartomány particionálásakor fog használni. Ha nincs megadva, a rendszer automatikusan észleli a tábla elsődleges kulcsát, és a partíció oszlopként használja. <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Ha lekérdezést használ a forrásadatok beolvasásához,  `?AdfRangePartitionColumnName` a WHERE záradékban lévő hookot. Példaként tekintse meg az [Oracle párhuzamos másolási](#parallel-copy-from-oracle) szakaszát. | No |
| partitionUpperBound | Az adatmásolásra szolgáló partíciós oszlop maximális értéke. <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Ha lekérdezést használ a forrásadatok beolvasásához, `?AdfRangePartitionUpbound` a WHERE záradékban lévő hookot. Példaként tekintse meg az [Oracle párhuzamos másolási](#parallel-copy-from-oracle) szakaszát. | No |
| partitionLowerBound | Az adatmásolásra szolgáló partíciós oszlop minimális értéke. <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Ha lekérdezést használ a forrásadatok beolvasásához, `?AdfRangePartitionLowbound` a WHERE záradékban lévő hookot. Példaként tekintse meg az [Oracle párhuzamos másolási](#parallel-copy-from-oracle) szakaszát. | No |

**Példa: adatok másolása egy alapszintű, partíció nélküli lekérdezés használatával**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle as mosogató

Az Oracle-be történő másoláshoz állítsa a fogadó típusát a másolási tevékenységbe `OracleSink` . A másolási tevékenység fogadója szakaszban a következő  tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának Type tulajdonságát be kell állítani `OracleSink` . | Yes |
| writeBatchSize | Beilleszti az adatmennyiséget az SQL-táblába, amikor eléri a puffer méretét `writeBatchSize` .<br/>Az engedélyezett értékek egész szám (sorok száma). |Nem (az alapértelmezett érték 10 000) |
| writeBatchTimeout | Az a várakozási idő, ameddig a Batch INSERT művelet befejeződik, mielőtt időtúllépés történt.<br/>Az engedélyezett értékek a TimeSpan. Például 00:30:00 (30 perc). | No |
| preCopyScript | Adja meg a másolási tevékenység futtatásához szükséges SQL-lekérdezést, mielőtt az összes futtatáskor beírja az Oracle-ba. Ennek a tulajdonságnak a használatával törölheti az előre feltöltött adatkészleteket. | No |
| maxConcurrentConnections |A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No |

**Példa**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Párhuzamos másolás Oracle-ből

A Data Factory Oracle Connector beépített adatparticionálást biztosít az adatok Oracle-ből párhuzamos másolásához. Az adatparticionálási beállítások a másolási tevékenység **forrás** lapján találhatók.

![Képernyőfelvétel a partíciós beállításokról](./media/connector-oracle/connector-oracle-partition-options.png)

A particionált másolás engedélyezésekor a Data Factory párhuzamos lekérdezéseket futtat az Oracle-forráson az adatpartíciók alapján történő betöltéshez. A párhuzamos mértéket a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállításai vezérlik. Ha például négyre van állítva `parallelCopies` , Data Factory egyidejűleg létrehoz és futtat négy lekérdezést a megadott partíciós beállítás és beállítások alapján, és mindegyik lekérdezés az Oracle-adatbázisból származó adatok egy részét kéri le.

Javasoljuk, hogy engedélyezze a párhuzamos másolást az adatok particionálásával, különösen akkor, ha nagy mennyiségű adatmennyiséget tölt be az Oracle-adatbázisból. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációk szerepelnek. Az adatok file-alapú adattárba való másolása során a rendszer úgy helyezi át, hogy több fájlként írjon egy mappába (csak a mappa nevét adja meg), amely esetben a teljesítmény jobb, mint egyetlen fájl írásakor.

| Eset                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés nagyméretű táblából, fizikai partíciókkal.          | **Partíciós beállítás**: a tábla fizikai partíciói. <br><br/>A végrehajtás során a Data Factory automatikusan észleli a fizikai partíciókat, és az Adatmásolást partíciók szerint. |
| Teljes terhelés a nagyméretű táblából fizikai partíciók nélkül, míg az adatparticionálás egész oszlopa. | **Partíciós beállítások**: dinamikus tartományú partíció.<br>**Partíciós oszlop**: az adatparticionáláshoz használt oszlop megadására szolgál. Ha nincs megadva, a rendszer az elsődleges kulcs oszlopot használja. |
| Nagy mennyiségű adattal tölthet be egy egyéni lekérdezést, fizikai partíciókkal. | **Partíciós beállítás**: a tábla fizikai partíciói.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>` .<br>**Partition Name (partíció neve**): adja meg az adatok másolásához szükséges partíciós nevet (ka) t. Ha nincs megadva, a Data Factory automatikusan észleli az Oracle-adatkészletben megadott tábla fizikai partícióit.<br><br>A végrehajtás során Data Factory lecseréli `?AdfTabularPartitionName` a tényleges partíció nevét, és elküldi az Oracle-nek. |
| Nagy mennyiségű adatmennyiséget tölthet be egy egyéni lekérdezéssel, fizikai partíciók nélkül, valamint az adatparticionálás egész oszlopával. | **Partíciós beállítások**: dinamikus tartományú partíció.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Partíciós oszlop**: az adatparticionáláshoz használt oszlop megadására szolgál. Az oszlop egész adattípussal is particionálható.<br>**Partíció felső határa** és a **partíció alsó határa**: adja meg, hogy a partíciós oszlop alapján szeretne-e szűrést végezni, hogy csak az alsó és a felső tartomány között legyen adat.<br><br>A végrehajtás során a Data Factory lecseréli, `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound` és az `?AdfRangePartitionLowbound` egyes partíciók tényleges oszlopának nevét és értékeit, majd elküldi az Oracle-nek. <br>Ha például az "ID" partíciós oszlop értéke az 1 értékre van állítva, a felső határ pedig 80, a párhuzamos másolási beállítás értéke 4, Data Factory 4 partíció alapján kérdezi le az adatmennyiséget. Az azonosítóik [1, 20], [21, 40], [41, 60] és [61, 80] között vannak. |

> [!TIP]
> Az adatok nem particionált táblából való másolása esetén a "dinamikus tartomány" partíciós lehetőséget használhatja egy egész számú oszlopra való particionáláshoz. Ha a forrásadatok nem rendelkeznek ilyen típusú oszloppal, kihasználhatja [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm) függvényt a forrás lekérdezésben egy oszlop létrehozásához és partíciós oszlopként való használatához.

**Példa: lekérdezés fizikai partícióval**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Példa: lekérdezés dinamikus tartományú partícióval**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Az Oracle adattípusának leképezése

Amikor a és az Oracle rendszerbe másol adatokból, a következő leképezések érvényesek. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadónak, tekintse meg a [séma-és adattípus-leképezéseket](copy-activity-schema-and-type-mapping.md).

| Oracle-adattípus | Data Factory időközi adattípus |
|:--- |:--- |
| BFÁJL |Bájt [] |
| BLOB |Bájt []<br/>(csak Oracle 10g és újabb verziók esetén támogatott) |
| CHAR |Sztring |
| CLOB |Sztring |
| DATE |DateTime |
| FLOAT |Decimális, karakterlánc (ha a pontosság > 28) |
| EGÉSZ SZÁM |Decimális, karakterlánc (ha a pontosság > 28) |
| HOSSZÚ |Sztring |
| HOSSZÚ NYERS |Bájt [] |
| NCHAR |Sztring |
| NCLOB |Sztring |
| SZÁM (p, s) |Decimális, sztring (IF p > 28) |
| Pontosság és skála nélküli szám |Dupla |
| NVARCHAR2 |Sztring |
| NYERS |Bájt [] |
| ROWID |Sztring |
| IDŐBÉLYEGZŐ |DateTime |
| IDŐBÉLYEG HELYI IDŐZÓNÁVAL |Sztring |
| IDŐBÉLYEG IDŐZÓNÁVAL |Sztring |
| ELŐJEL NÉLKÜLI EGÉSZ SZÁM |Szám |
| VARCHAR2 |Sztring |
| XML |Sztring |

> [!NOTE]
> Az adattípusok INTERVALLUMa év – hónap, és a nap és a második közötti intervallum nem támogatott.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések
A Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
