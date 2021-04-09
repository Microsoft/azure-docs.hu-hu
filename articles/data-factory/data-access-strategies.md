---
title: Adathozzáférési stratégiák
description: A Azure Data Factory mostantól támogatja a statikus IP-címtartományok használatát.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: edc773ec2db078b6c50b55c81ad6570758a3f5f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389246"
---
# <a name="data-access-strategies"></a>Adathozzáférési stratégiák

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A szervezet létfontosságú biztonsági célja az adattárak védelme az interneten keresztüli véletlenszerű hozzáféréssel, lehet helyszíni vagy felhőalapú/SaaS-adattár is. 

A Felhőbeli adattár jellemzően az alábbi mechanizmusokkal szabályozza a hozzáférést:
* Privát hivatkozás egy Virtual Networkról privát Endpoint-kompatibilis adatforrásokra
* A kapcsolódást IP-cím alapján korlátozó tűzfalszabályok
* Hitelesítési mechanizmusok, amelyeknek a felhasználóknak igazolniuk kell identitásukat
* Engedélyezési mechanizmusok, amelyek korlátozzák a felhasználókat az adott műveletekre és adatszolgáltatásokra

> [!TIP]
> A [statikus IP-címtartomány bevezetésével](./azure-integration-runtime-ip-addresses.md)mostantól engedélyezheti az adott Azure Integration RUNTIME régió IP-címeinek listázását, így biztosítva, hogy az összes Azure IP-cím ne legyen engedélyezve a Felhőbeli adattárakban. Így korlátozhatja azokat az IP-címeket, amelyek jogosultak az adattárak elérésére.

> [!NOTE] 
> Az IP-címtartományok blokkolva vannak a Azure Integration Runtime számára, és jelenleg csak adatáthelyezésre, folyamatra és külső tevékenységekre használható. A felügyelt Virtual Networkt engedélyező adatfolyamok és Azure Integration Runtime mostantól nem használják ezeket az IP-tartományokat. 

Ennek számos forgatókönyvben kell működnie, és tisztában vagyunk vele, hogy az integrációs modulhoz tartozó egyedi statikus IP-cím kívánatos, de ez nem lehetséges, ha a Azure Integration Runtime jelenleg nem a kiszolgáló nélküli. Ha szükséges, bármikor beállíthatja a saját üzemeltetésű Integration Runtime, és használhatja a statikus IP-címét. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Adathozzáférési stratégiák Azure Data Factory

* **[Privát hivatkozás](../private-link/private-link-overview.md)** – létrehozhat egy Azure Integration Runtimet Azure Data Factory felügyelt Virtual Networkon belül, és a saját végpontokat fogja használni a támogatott adattárakhoz való biztonságos csatlakozáshoz. A felügyelt Virtual Network és az adatforrások közötti forgalom a Microsoft gerinc-hálózatba kerül, és nem a nyilvános hálózatra van kitéve.
* **[Megbízható szolgáltatás](../storage/common/storage-network-security.md#exceptions)** – az Azure Storage (Blob, ADLS Gen2) támogatja a tűzfal-konfigurációt, amely lehetővé teszi a megbízható Azure platform-szolgáltatások kiválasztását a Storage-fiók biztonságos eléréséhez. A megbízható szolgáltatások kikényszerítik a felügyelt identitások hitelesítését, amely biztosítja, hogy más adatok előállítója ne kapcsolódjon ehhez a tárhelyhez, kivéve, ha ezt az engedélyt a felügyelt identitásának használatával engedélyezi További részleteket ebben a **[blogban](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)** talál. Ezért ez rendkívül biztonságos és ajánlott. 
* **Egyedi statikus IP** – létre kell hoznia egy saját üzemeltetésű integrációs modult, amely egy statikus IP-címet kap Data Factory összekötők számára. Ez a mechanizmus biztosítja, hogy letiltsa a hozzáférést az összes többi IP-címről. 
* **[Statikus IP-címtartomány](./azure-integration-runtime-ip-addresses.md)** – a Azure Integration Runtime IP-címeivel engedélyezheti a tárolóban való listázást (például S3, Salesforce stb.). Minden bizonnyal korlátozza azokat az IP-címeket, amelyek csatlakozni tudnak az adattárakhoz, de a hitelesítési/engedélyezési szabályokra is támaszkodnak.
* **[Szolgáltatás címkéje](../virtual-network/service-tags-overview.md)** – a szolgáltatás címkéje az adott Azure-szolgáltatás (például Azure Data Factory) IP-címeinek egy csoportját jelöli. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával, minimalizálva a hálózati biztonsági szabályok gyakori frissítéseinek összetettségét. Ez akkor hasznos, ha a Virtual Network IaaS tárolt adattárakban lévő adathozzáféréseket szűri.
* **Azure-szolgáltatások engedélyezése** – egyes szolgáltatások lehetővé teszik, hogy az összes Azure-szolgáltatás csatlakozni tudjanak hozzá, ha ezt a beállítást választja. 

A Azure Integration Runtime és a saját üzemeltetésű Integration Runtime adattárakban található, támogatott hálózati biztonsági mechanizmusokkal kapcsolatos további információkért lásd: két táblázat.  
* **Azure Integration Runtime**

    | Adattárak                  | Az adattárakban támogatott hálózati biztonsági mechanizmus | Private Link     | Megbízható szolgáltatás     | Statikus IP-címtartomány | Szolgáltatáscímkék | Azure-szolgáltatások engedélyezése |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|-----------------|
    | Azure Pásti-adattárak       | Azure Cosmos DB                                     | Igen              | -                   | Igen             | -            | Yes                  |
    |                              | Azure Adatkezelő                                 | -                | -                   | Igen*            | Igen*         | -                    |
    |                              | Azure Data Lake Gen1                                | -                | -                   | Igen             | -            | Yes                  |
    |                              | Azure Database for MariaDB, MySQL, PostgreSQL       | -                | -                   | Igen             | -            | Yes                  |
    |                              | Azure File Storage                                  | Igen              | -                   | Yes             | -            | .                    |
    |                              | Azure Storage (blob, ADLS Gen2)                     | Yes              | Igen (csak MSI-hitelesítés) | Yes             | -            | .                    |
    |                              | Azure SQL DB, Azure szinapszis Analytics), SQL ml  | Igen (csak az Azure SQL DB/DW)        | -                   | Igen             | -            | Yes                  |
    |                              | Azure Key Vault (a Titkok/a kapcsolatok karakterláncának beolvasásához) | igen      | Igen                 | Yes             | -            | -                    |
    | Egyéb Pásti/SaaS-adattárak | AWS S3, SalesForce, Google Cloud Storage stb.    | -                | -                   | Yes             | -            | -                    |
    | Azure-laaS                   | SQL Server, Oracle stb.                          | -                | -                   | Igen             | Yes          | -                    |
    | Helyszíni laaS              | SQL Server, Oracle stb.                          | -                | -                   | Yes             | -            | -                    |
    
    **Csak akkor alkalmazható, ha az Azure Adatkezelő virtuális hálózati befecskendezéses, és az IP-címtartomány a NSG/Firewall-on is alkalmazható.* 

* **Saját üzemeltetésű Integration Runtime (vnet/helyszíni)**
    
    | Adattárak                  | Az adattárakban támogatott hálózati biztonsági mechanizmus         | Statikus IP-cím | Megbízható szolgáltatások  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Azure Pásti-adattárak       | Azure Cosmos DB                                               | Igen       | -                   |
    |                                | Azure Adatkezelő                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | Yes       | -                   |
    |                                | Azure Database for MariaDB, MySQL, PostgreSQL               | Yes       | -                   |
    |                                | Azure File Storage                                            | Yes       | -                   |
    |                                | Azure Storage (blog, ADLS Gen2)                             | Yes       | Igen (csak MSI-hitelesítés) |
    |                                | Azure SQL DB, Azure szinapszis Analytics), SQL ml          | Yes       | -                   |
    |                                | Azure Key Vault (a Titkok/a kapcsolatok karakterláncának beolvasásához) | Igen       | Yes                 |
    | Egyéb Pásti/SaaS-adattárak | AWS S3, SalesForce, Google Cloud Storage stb.              | Yes       | -                   |
    | Azure-laaS                     | SQL Server, Oracle stb.                                  | Yes       | -                   |
    | Helyszíni laaS              | SQL Server, Oracle stb.                                  | Igen       | -                   |    

## <a name="next-steps"></a>Következő lépések

További információkért tekintse meg a következő kapcsolódó cikkeket:
* [Támogatott adattárak](./copy-activity-overview.md#supported-data-stores-and-formats)
* [Azure Key Vault "megbízható szolgáltatások"](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)
* [Azure Storage – megbízható Microsoft-szolgáltatások](../storage/common/storage-network-security.md#trusted-microsoft-services)
* [Felügyelt identitás Data Factoryhoz](./data-factory-service-identity.md)