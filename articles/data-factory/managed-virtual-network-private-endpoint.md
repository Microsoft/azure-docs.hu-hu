---
title: Felügyelt virtuális hálózat & felügyelt magánhálózati végpontok
description: Ismerkedjen meg a felügyelt virtuális hálózattal és a felügyelt privát végpontokkal Azure Data Factoryban.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: 81d82bccd6b6bd97b84df5269dd59ffac4903370
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980361"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory felügyelt Virtual Network (előzetes verzió)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk ismerteti a felügyelt Virtual Network és a felügyelt privát végpontokat a Azure Data Factoryban.


## <a name="managed-virtual-network"></a>Felügyelt virtuális hálózat

Ha Azure Data Factory felügyelt Virtual Networkon (VNET) belül hoz létre Azure Integration Runtime (IR), az integrációs modult a felügyelt Virtual Network fogja kiépíteni, és a magánhálózati végpontokat a támogatott adattárakhoz való biztonságos csatlakozásra fogja használni. 

A felügyelt Virtual Networkon belüli Azure IR létrehozása biztosítja az adatintegrációs folyamat elkülönítését és védelmét. 

A felügyelt Virtual Network használatának előnyei:

- Felügyelt Virtual Network esetén kiszervezheti a Virtual Network Azure Data Factoryre való felügyeletének terhét. Nem kell létrehoznia olyan alhálózatot Azure Integration Runtimehoz, amely végül sok magánhálózati IP-címet használhat a Virtual Network, és a hálózati infrastruktúra korábbi megtervezését igényli. 
- Az adatintegrációk biztonságos elvégzéséhez nincs szükség az Azure hálózatkezelési szolgáltatásának alapos ismeretére. Ehelyett a biztonságos ETL-vel való Ismerkedés sokkal egyszerűbbé teszi az adatmérnökök számára. 
- A felügyelt Virtual Network és a felügyelt privát végpontok is megvédik az kiszűrése. 

> [!IMPORTANT]
>A felügyelt VNet jelenleg csak a Azure Data Factory régióval azonos régióban támogatott.
 

![Az ADF felügyelt Virtual Network architektúrája](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Felügyelt privát végpontok

A felügyelt magánhálózati végpontok a Azure Data Factory felügyelt Virtual Network létrehozott privát végpontok, amelyek az Azure-erőforrásokra mutató privát hivatkozást hoznak létre. Azure Data Factory kezeli ezeket a privát végpontokat az Ön nevében. 

![Új felügyelt magánhálózati végpont](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory támogatja a privát hivatkozásokat. A privát hivatkozás lehetővé teszi az Azure (Pásti) szolgáltatások elérését (például Azure Storage, Azure Cosmos DB, Azure szinapszis Analytics (korábban SQL Data Warehouse)).

Privát kapcsolat használatakor az adattárak és a felügyelt Virtual Network közötti adatforgalom teljes egészében a Microsoft gerinc hálózatán halad át. A privát hivatkozás védi az adatkiszűrése kockázatait. Privát végpont létrehozásával létrehozhat egy erőforrásra mutató privát hivatkozást.

A magánhálózati végpont egy magánhálózati IP-címet használ a felügyelt Virtual Networkban, hogy hatékonyan hozza a szolgáltatást. A magánhálózati végpontok egy adott erőforrásra vannak leképezve az Azure-ban, és nem a teljes szolgáltatás. Az ügyfelek korlátozhatják a szervezete által jóváhagyott adott erőforráshoz való kapcsolódást. További információ a [privát kapcsolatokról és a privát végpontokról](../private-link/index.yml).

> [!NOTE]
> Javasoljuk, hogy felügyelt privát végpontokat hozzon létre az összes Azure-adatforráshoz való kapcsolódáshoz. 
 
> [!WARNING]
> Ha egy Pásti-adattár (blob, ADLS Gen2, Azure szinapszis Analytics) rendelkezik egy már létrehozott privát végponttal, és akkor is, ha az összes hálózatról engedélyezi a hozzáférést, az ADF csak felügyelt privát végponton keresztül férhet hozzá. Győződjön meg róla, hogy ilyen helyzetekben Hozzon létre egy privát végpontot. 

A privát végponti kapcsolatok "függő" állapotban jönnek létre, amikor felügyelt privát végpontot hoz létre Azure Data Factoryban. A rendszer elindít egy jóváhagyási munkafolyamatot. A magánhálózati kapcsolat erőforrásának tulajdonosa a kapcsolat jóváhagyása vagy elutasítása.

![Privát végpont kezelése](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Ha a tulajdonos jóváhagyja a kapcsolatot, a magánhálózati kapcsolat létrejött. Ellenkező esetben a magánhálózati kapcsolat nem lesz létrehozva. Mindkét esetben a felügyelt magánhálózati végpont a kapcsolatok állapotával lesz frissítve.

![Felügyelt privát végpont jóváhagyása](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Csak egy felügyelt magánhálózati végpont engedélyezett állapotban küldhet forgalmat egy adott privát kapcsolati erőforrásnak.

## <a name="interactive-authoring"></a>Interaktív szerzői műveletek
Az interaktív szerzői képességek olyan funkciókhoz használatosak, mint a kapcsolat tesztelése, a mappák listájának és a táblázatok listájának lekérése, a séma beolvasása és az előnézet. Az ADF által felügyelt virtuális hálózatban lévő Azure Integration Runtime létrehozásakor vagy szerkesztésekor engedélyezheti az interaktív szerzői műveleteket. A háttérrendszer előre lefoglalja a számítási funkciókat az interaktív szerzői funkciókhoz. Ellenkező esetben a számítás minden olyan interaktív művelet elvégzése után le lesz foglalva, amely több időt vesz igénybe. Az interaktív létrehozás élettartama (TTL) 60 perc, ami azt jelenti, hogy az utolsó interaktív szerzői művelethez tartozó 60 perc elteltével automatikusan le lesz tiltva.

![Interaktív szerzői műveletek](./media/managed-vnet/interactive-authoring.png)

## <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák
### <a name="supported-data-sources"></a>Támogatott adatforrások
Az alábbi adatforrások az ADF által felügyelt Virtual Networkon keresztüli privát kapcsolaton keresztül csatlakozhatnak.
- Azure Blob-tároló
- Azure Table Storage
- Azure Files
- Azure Data Lake Gen2
- Azure SQL Database (nem tartalmazza az Azure SQL felügyelt példányát)
- Azure Synapse Analytics (korábban SQL Data Warehouse)
- Azure CosmosDB SQL
- Azure Key Vault
- Azure Private link Service
- Azure Search
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Database for MariaDB

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Azure Data Factory felügyelt Virtual Network a következő Azure-régiókban érhető el:
- USA keleti régiója
- USA 2. keleti régiója
- USA nyugati középső régiója
- USA nyugati régiója
- USA 2. nyugati régiója
- USA déli középső régiója
- USA középső régiója
- Észak-Európa
- Nyugat-Európa
- Az Egyesült Királyság déli régiója
- Délkelet-Ázsia
- Kelet-Ausztrália
- Délkelet-Ausztrália

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Kimenő kommunikáció nyilvános végponton keresztül, felügyelt Virtual Network
- A kimenő kommunikációhoz csak az 443-es port van megnyitva.
- Az Azure Storage és a Azure Data Lake Gen2 nem támogatottak nyilvános végponton keresztüli csatlakoztatáshoz az ADF által felügyelt Virtual Network.

### <a name="linked-service-creation-of-azure-key-vault"></a>Azure Key Vault társított szolgáltatásának létrehozása 
- Ha Azure Key Vaulthoz társított szolgáltatást hoz létre, nincs Azure Integration Runtime-hivatkozás. Így nem hozhat létre privát végpontot a Azure Key Vault társított szolgáltatásának létrehozásakor. Ha azonban olyan adattárakhoz hoz létre társított szolgáltatást, amelyek Azure Key Vault társított szolgáltatásra hivatkoznak, és ez a társított szolgáltatás olyan Azure Integration Runtime, amelyeken engedélyezve van a felügyelt Virtual Network, akkor a létrehozás során létre tud hozni egy privát végpontot a Azure Key Vault társított szolgáltatáshoz. 
- A Azure Key Vault társított szolgáltatásának **tesztelési** művelete csak az URL-formátumot ellenőrzi, de nem végez hálózati műveletet.

## <a name="next-steps"></a>Következő lépések

- Oktatóanyag: [másolási folyamat létrehozása felügyelt Virtual Network és privát végpontok használatával](tutorial-copy-data-portal-private.md) 
- Oktatóanyag: [leképezési adatfolyam folyamat létrehozása felügyelt Virtual Network és privát végpontok használatával](tutorial-data-flow-private.md)