---
title: Az Azure Data Share szerepkörei és követelményei
description: Ismerje meg az Azure-adatmegosztás használatával történő adatmegosztáshoz és az adatfogadáshoz szükséges engedélyeket.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: f5c5d6da239d302b57bdb37e9d49116a29c1ccb4
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558132"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Az Azure Data Share szerepkörei és követelményei 

Ez a cikk azokat a szerepköröket és engedélyeket ismerteti, amelyek szükségesek az Azure adatmegosztási szolgáltatással történő adatmegosztáshoz és fogadáshoz. 

## <a name="roles-and-requirements"></a>Szerepkörök és követelmények

Az Azure adatmegosztási szolgáltatással az adatszolgáltató és a fogyasztó közötti hitelesítő adatok cseréje nélkül is megoszthatja az adatokat. Az Azure adatmegosztási szolgáltatás felügyelt identitásokat (korábbi nevén rendszercsomagok) használ az Azure-adattárba való hitelesítéshez. 

Az Azure-beli adatmegosztási erőforrás felügyelt identitásának hozzáférést kell biztosítani az Azure-adattárhoz. Az Azure adatmegosztási szolgáltatás ezután ezt a felügyelt identitást használja az adatok olvasására és írására a pillanatkép-alapú megosztáshoz, valamint a szimbolikus hivatkozás létrehozásához a helyi megosztáshoz. 

Az Azure-adattárból származó adatok megosztásához vagy fogadásához a felhasználónak legalább a következő engedélyekkel kell rendelkeznie. Az SQL-alapú megosztáshoz további engedélyek szükségesek.

* Engedély az Azure-adattárba való íráshoz. Ez az engedély általában a **közreműködő** szerepkörben található.
* Engedély a szerepkör-hozzárendelés létrehozásához az Azure-adattárban. A szerepkör-hozzárendelések létrehozásához általában a **tulajdonosi** szerepkör, a felhasználói hozzáférés rendszergazdai szerepköre, vagy a Microsoft. Authorization/szerepkör-hozzárendelés/írási engedély hozzárendelésére vonatkozó engedély van rendelve. Ez az engedély nem szükséges, ha az adatmegosztási erőforrás felügyelt identitása már hozzáférést kap az Azure-adattárhoz. Az alábbi táblázatban találja a szükséges szerepkört.

Az alábbiakban az adatmegosztási erőforrás felügyelt identitásához rendelt szerepkörök összegzése látható:

|**Adattár típusa**|**Adatszolgáltató forrásának adattára**|**Adatfogyasztói célként szolgáló adattár**|
|---|---|---|
|Azure Blob Storage| Storage-blobadatok olvasója | Storage-blobadatok közreműködője
|Azure Data Lake Gen1 | Tulajdonos | Nem támogatott
|Azure Data Lake Gen2 | Storage-blobadatok olvasója | Storage-blobadatok közreműködője
|Azure Data Explorer-fürt | Közreműködő | Közreműködő
|

SQL-alapú megosztás esetén az SQL-felhasználót egy olyan külső szolgáltatóból kell létrehozni, amely az Azure-beli adatmegosztási erőforrással megegyező nevű Azure SQL Databaseban van. A felhasználó létrehozásához Azure Active Directory rendszergazdai engedély szükséges. Az alábbiakban az SQL-felhasználó által megkövetelt engedélyek összegzése látható.

|**SQL Database típusa**|**Adatszolgáltató SQL-felhasználói engedélye**|**Adatfogyasztói SQL-felhasználói engedély**|
|---|---|---|
|Azure SQL Database | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics | db_datareader | db_datareader, db_datawriter, db_ddladmin
|

### <a name="data-provider"></a>Adatszolgáltató

Az adatkészletek Azure-adatmegosztásban való hozzáadásához a szolgáltatói adatmegosztási erőforrás felügyelt identitásának hozzáférést kell biztosítania a forrás Azure-adattárhoz. A Storage-fiók esetében például az adatmegosztási erőforrás felügyelt identitása megkapja a Storage blob adatolvasói szerepkört. 

Ezt az Azure-adatmegosztási szolgáltatás automatikusan elvégzi, ha a felhasználó Azure Portalon keresztül adja hozzá az adatkészletet, és a felhasználó rendelkezik a megfelelő engedélyekkel. Például a felhasználó az Azure-beli adattár tulajdonosa, vagy egy olyan egyéni szerepkör tagja, amely rendelkezik a Microsoft. Authorization/szerepkör-hozzárendelésekkel/írási engedéllyel. 

Azt is megteheti, hogy a felhasználó az Azure adattár tulajdonosaként manuálisan hozzáadja az adatmegosztási erőforrás felügyelt identitását az Azure-adattárban. Ezt a műveletet csak egyszer kell végrehajtani az adatmegosztási erőforráson.

Ha manuálisan szeretné létrehozni az adatmegosztási erőforrás felügyelt identitásához tartozó szerepkör-hozzárendelést, kövesse az alábbi lépéseket.  

1. Navigáljon az Azure-adattárhoz.
1. Válassza a **Access Control (iam)** lehetőséget.
1. Válassza **a szerepkör-hozzárendelés hozzáadása** lehetőséget.
1. A *szerepkör* területen válassza ki a szerepkört a fenti szerepkör-hozzárendelési táblában (például Storage-fiók esetén válassza a *Storage blob-Adatolvasó* lehetőséget).
1. A *kiválasztás* mezőben adja meg az Azure-beli adatmegosztási erőforrás nevét.
1. Kattintson a *Mentés* gombra.

A szerepkör-hozzárendeléssel kapcsolatos további tudnivalókért tekintse meg [Az Azure-szerepkörök hozzárendelése a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)című témakört. Ha REST API-k használatával oszt meg adatmegosztást, a szerepkör-hozzárendelést az API használatával hozhatja létre [Az Azure-szerepkörök hozzárendelésével a REST API használatával](../role-based-access-control/role-assignments-rest.md). 

SQL-alapú források esetén létre kell hozni egy SQL-felhasználót egy külső szolgáltatótól SQL Database ugyanazzal a névvel, mint az Azure-beli adatmegosztási erőforrással az SQL Database-hez való kapcsolódáskor Azure Active Directory hitelesítés használatával. Ezt a felhasználót *db_datareader* jogosultsággal kell megadnia. Az SQL-alapú megosztás egyéb előfeltételeivel együtt a [megosztás a Azure SQL Database vagy az Azure szinapszis Analytics](how-to-share-from-sql.md) oktatóanyagban található. 

### <a name="data-consumer"></a>Adatfogyasztó
Az adatok fogadásához a fogyasztói adatmegosztási erőforrás felügyelt identitásának hozzáférést kell biztosítania a cél Azure-adattárhoz. A Storage-fiók esetében például az adatmegosztási erőforrás felügyelt identitása megkapja a Storage blob adatközreműködői szerepkört. 

Ezt az Azure-adatmegosztási szolgáltatás automatikusan végzi el, ha a felhasználó a Azure Portalon keresztül határozza meg a cél adattárat, és a felhasználó rendelkezik megfelelő engedéllyel. A felhasználó például az Azure adattár tulajdonosa, vagy egy olyan egyéni szerepkör tagja, amelynek van hozzárendelve a Microsoft. Authorization/szerepkör-hozzárendelés/írás engedélye. 

Azt is megteheti, hogy a felhasználó az Azure adattár tulajdonosaként manuálisan hozzáadja az adatmegosztási erőforrás felügyelt identitását az Azure-adattárban. Ezt a műveletet csak egyszer kell végrehajtani az adatmegosztási erőforráson.

Ha manuálisan szeretné létrehozni az adatmegosztási erőforrás felügyelt identitásához tartozó szerepkör-hozzárendelést, kövesse az alábbi lépéseket. 

1. Navigáljon az Azure-adattárhoz.
1. Válassza a **Access Control (iam)** lehetőséget.
1. Válassza **a szerepkör-hozzárendelés hozzáadása** lehetőséget.
1. A *szerepkör* területen válassza ki a szerepkört a fenti szerepkör-hozzárendelési táblában (például Storage-fiók esetén válassza a *Storage blob-Adatolvasó* lehetőséget).
1. A *kiválasztás* mezőben adja meg az Azure-beli adatmegosztási erőforrás nevét.
1. Kattintson a *Mentés* gombra.

A szerepkör-hozzárendeléssel kapcsolatos további tudnivalókért tekintse meg [Az Azure-szerepkörök hozzárendelése a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)című témakört. Ha REST API-k használatával fogadja az adatgyűjtést, létrehozhat szerepkör-hozzárendelést az API-val [Az Azure-szerepkörök hozzárendelésével a REST API használatával](../role-based-access-control/role-assignments-rest.md). 

SQL-alapú cél esetén az SQL-felhasználót egy külső szolgáltatóból kell létrehozni SQL Database az Azure-beli adatmegosztási erőforrással megegyező névvel az SQL Database-hez való kapcsolódáskor Azure Active Directory hitelesítés használatával. Ezt a felhasználót *db_datareader, db_datawriter, db_ddladmin* engedéllyel kell megadnia. Az SQL-alapú megosztás egyéb előfeltételeivel együtt a [megosztás a Azure SQL Database vagy az Azure szinapszis Analytics](how-to-share-from-sql.md) oktatóanyagban található. 

## <a name="resource-provider-registration"></a>Erőforrás-szolgáltató regisztrálása 

Előfordulhat, hogy manuálisan kell regisztrálnia a Microsoft. DataShare erőforrás-szolgáltatót az Azure-előfizetésében a következő esetekben: 

* Azure-beli adatmegosztási meghívások megtekintése az Azure-bérlőn első alkalommal
* Adatok megosztása egy Azure-adattárból egy másik Azure-előfizetésben az Azure-beli adatmegosztási erőforrásból
* Adatok fogadása egy Azure-adattárba egy másik Azure-előfizetésben az Azure-beli adatmegosztási erőforrásból

Az alábbi lépéseket követve regisztrálja a Microsoft. DataShare erőforrás-szolgáltatót az Azure-előfizetésében. Az erőforrás-szolgáltató regisztrálásához *közreműködői* hozzáférésre van szüksége az Azure-előfizetéshez.

1. A Azure Portal navigáljon az **előfizetések** elemre.
1. Válassza ki az Azure-adatmegosztáshoz használt előfizetést.
1. Kattintson az **erőforrás-szolgáltatók** elemre.
1. Keressen rá a Microsoft. DataShare kifejezésre.
1. Kattintson a **Regisztrálás** parancsra.
 
Az erőforrás-szolgáltatóval kapcsolatos további tudnivalókért tekintse meg az [Azure-erőforrás-szolgáltatókat és-típusokat](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Következő lépések

- További információ az Azure-beli szerepkörökről – az [Azure szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md)