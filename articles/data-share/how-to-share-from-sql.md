---
title: Adatok megosztása és fogadása az Azure SQL Database-ből és az Azure Synapse Analyticsből
description: Megtudhatja, hogyan oszthat meg és fogadhat adatok a Azure SQL Database és az Azure szinapszis Analytics használatával
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/24/2021
ms.openlocfilehash: ef8c1a50cd3568c6cec9bdb053b02e6e14741eb0
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644670"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Adatok megosztása és fogadása az Azure SQL Database-ből és az Azure Synapse Analyticsből

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Az Azure-beli adatmegosztás támogatja a Snapshot-alapú megosztási Azure SQL Database és az Azure szinapszis Analytics szolgáltatást. Ez a cikk az ezekből a forrásokból származó adatok megosztását és fogadását ismerteti.

Az Azure-beli adatmegosztás támogatja a táblák és nézetek megosztását a Azure SQL Database és az Azure szinapszis Analytics (korábban Azure SQL DW) használatával, valamint a táblák megosztását az Azure szinapszis Analytics (munkaterület) dedikált SQL-készletből. Az Azure szinapszis Analytics (munkaterület) kiszolgáló nélküli SQL-készletének megosztása jelenleg nem támogatott. Az adatfogyasztók dönthetnek úgy, hogy elfogadják az Azure Data Lake Storage Gen2 vagy az Azure Blob Storage CSV-vagy Parque-fájlként, valamint a Azure SQL Database és az Azure szinapszis Analytics táblázatként való elfogadását.

Amikor Azure Data Lake Store Gen2 vagy Azure Blob Storageba fogadja az adatfogadást, a teljes Pillanatképek felülírják a célfájl tartalmát, ha már létezik.
Ha az SQL-táblába beérkeznek az adatforrások, és ha a céltábla még nem létezik, az Azure-beli adatmegosztás létrehozza az SQL-táblázatot a forrás sémával. Ha már létezik ilyen nevű céltábla, a rendszer elveti és felülírja a legújabb teljes pillanatképtel. A növekményes Pillanatképek jelenleg nem támogatottak.

## <a name="share-data"></a>Adatok megosztása

### <a name="prerequisites-to-share-data"></a>Az adatmegosztásra vonatkozó előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* A címzett Azure bejelentkezési e-mail-címe (az e-mail alias használata nem fog működni).
* Ha az Azure-beli adattár egy másik Azure-előfizetésben található, mint amelyet az adatmegosztási erőforrás létrehozásához fog használni, regisztrálja a [Microsoft. DataShare erőforrás-szolgáltatót](concepts-roles-permissions.md#resource-provider-registration) abban az előfizetésben, amelyben az Azure-adattár található. 

### <a name="prerequisites-for-sql-source"></a>Az SQL-forrás előfeltételei
Az alábbi lista tartalmazza az SQL-forrásokból származó adatok megosztásának előfeltételeit. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>A Azure SQL Database vagy az Azure szinapszis Analytics (korábban Azure SQL DW) megosztásának előfeltételei


Az Azure Active Directory hitelesítéssel történő adatmegosztáshoz itt találja az előfeltételek listáját:

* Egy Azure SQL Database vagy Azure szinapszis Analytics (korábban Azure SQL DW) a megosztani kívánt táblázatokkal és nézetekkel.
* A *Microsoft. SQL/Servers/Databases/Write* adatbázisban található SQL Server-adatbázisba való írásra vonatkozó engedély. Ez az engedély a **Közreműködő** szerepkör részét képezi.
* SQL Server **Azure Active Directory-rendszergazda**
* SQL Server tűzfal-hozzáférés. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon az SQL Server elemre. A bal oldali navigációs sávon válassza a *tűzfalak és virtuális hálózatok* lehetőséget.
    1. Az **Igen** gombra kattintva *engedélyezheti, hogy az Azure-szolgáltatások és-erőforrások hozzáférjenek ehhez a kiszolgálóhoz*.
    1. Kattintson az **+ ügyfél IP-** címének hozzáadása elemre. Az ügyfél IP-címének módosítása változhat. Előfordulhat, hogy ezt a folyamatot meg kell ismételni, amikor legközelebb megosztja az SQL-adatok Azure Portalból való megosztását. Hozzáadhat IP-címtartományt is.
    1. Kattintson a **Mentés** gombra. 

Az SQL-hitelesítés használatával az alábbi lista tartalmazza az előfeltételek listáját. Az előfeltételek konfigurálásához kövesse a [lépésenkénti bemutató](https://youtu.be/hIE-TjJD8Dc) lépéseit.

* Egy Azure SQL Database vagy Azure szinapszis Analytics (korábban Azure SQL DW) a megosztani kívánt táblázatokkal és nézetekkel.
* A *Microsoft. SQL/Servers/Databases/Write* adatbázisban található SQL Server-adatbázisba való írásra vonatkozó engedély. Ez az engedély a **Közreműködő** szerepkör részét képezi.
* Engedély az adatmegosztási erőforrás felügyelt identitásához az adatbázis eléréséhez. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon az SQL Serverre, és állítsa be magát a **Azure Active Directory-rendszergazdaként**.
    1. Kapcsolódjon a Azure SQL Database/adattárházhoz a [Lekérdezés-szerkesztő](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) használatával, vagy SQL Server Management Studio Azure Active Directory hitelesítéssel. 
    1. A következő szkript végrehajtásával adja hozzá az adatmegosztási erőforrás felügyelt identitását db_datareaderként. Active Directory használatával kell kapcsolódnia, nem SQL Server a hitelesítéshez. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Vegye figyelembe, hogy a *<share_acc_name>* az adatmegosztási erőforrás neve. Ha még nem hozott létre adatmegosztási erőforrást, később is visszatérhet ehhez az előfeltételhöz.  

* Egy Azure SQL Database **"db_datareader"** hozzáféréssel rendelkező felhasználó navigálhat, és kiválaszthatja a megosztani kívánt táblákat és/vagy nézeteket. 

* SQL Server tűzfal-hozzáférés. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon az SQL Server elemre. A bal oldali navigációs sávon válassza a *tűzfalak és virtuális hálózatok* lehetőséget.
    1. Az **Igen** gombra kattintva *engedélyezheti, hogy az Azure-szolgáltatások és-erőforrások hozzáférjenek ehhez a kiszolgálóhoz*.
    1. Kattintson az **+ ügyfél IP-** címének hozzáadása elemre. Az ügyfél IP-címének módosítása változhat. Előfordulhat, hogy ezt a folyamatot meg kell ismételni, amikor legközelebb megosztja az SQL-adatok Azure Portalból való megosztását. Hozzáadhat IP-címtartományt is.
    1. Kattintson a **Mentés** gombra. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Az Azure szinapszis Analytics (munkaterület) SQL-készlet megosztásának előfeltételei

* Egy Azure szinapszis Analytics (munkaterület) dedikált SQL-készlet a megosztani kívánt táblázatokkal. A nézet megosztása jelenleg nem támogatott. A kiszolgáló nélküli SQL-készletből való megosztás jelenleg nem támogatott.
* Engedély a szinapszis munkaterületen található SQL-készletbe való írásra, amely megtalálható a *Microsoft. szinapszis/munkaterület/sqlPools/Write* fájlokban. Ez az engedély a **Közreműködő** szerepkör részét képezi.
* Engedély az adatmegosztási erőforrás felügyelt identitásához a szinapszis-munkaterület SQL-készletének eléréséhez. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon a szinapszis munkaterületre. Válassza az SQL Active Directory-rendszergazda lehetőséget a bal oldali navigációs sávon, és állítsa be magát a **Azure Active Directory-rendszergazdaként**.
    1. Nyissa meg a szinapszis Studio alkalmazást, és válassza a *kezelés* lehetőséget a bal oldali navigációs sávon. Válassza a *hozzáférés-vezérlés* lehetőséget a biztonság területen. Rendeljen hozzá saját **SQL-rendszergazdai** vagy **munkaterület-rendszergazdai** szerepkört.
    1. A szinapszis Studióban válassza a *fejlesztés* lehetőséget a bal oldali navigációs sávon. Hajtsa végre a következő parancsfájlt az SQL-készletben az adatmegosztási erőforrás felügyelt identitásának db_datareader való hozzáadásához. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Vegye figyelembe, hogy a *<share_acc_name>* az adatmegosztási erőforrás neve. Ha még nem hozott létre adatmegosztási erőforrást, később is visszatérhet ehhez az előfeltételhöz.  

* A szinapszis munkaterület tűzfal-hozzáférése. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon a szinapszis munkaterületre. A bal oldali navigációs sávon válassza a *tűzfalak* lehetőséget.
    1. Ide **kattintva** *engedélyezheti, hogy az Azure-szolgáltatások és-erőforrások hozzáférjenek ehhez a munkaterülethez*.
    1. Kattintson az **+ ügyfél IP-** címének hozzáadása elemre. Az ügyfél IP-címének módosítása változhat. Előfordulhat, hogy ezt a folyamatot meg kell ismételni, amikor legközelebb megosztja az SQL-adatok Azure Portalból való megosztását. Hozzáadhat IP-címtartományt is.
    1. Kattintson a **Mentés** gombra. 

### <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Adatmegosztási fiók létrehozása

Azure-beli adatmegosztási erőforrás létrehozása Azure-erőforráscsoporthoz.

1. Kattintson a portál bal felső sarkában található menü gombra, majd válassza az **erőforrás létrehozása** (+) lehetőséget.

1. Keresse meg az *adatmegosztást*.

1. Válassza az adatmegosztás lehetőséget, majd válassza a **Létrehozás** lehetőséget.

1. Töltse ki az Azure-beli adatmegosztási erőforrás alapvető adatait az alábbi információkkal. 

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Előfizetés | Az Ön előfizetése | Válassza ki az adatmegosztási fiókhoz használni kívánt Azure-előfizetést.|
    | Erőforráscsoport | *teszt – erőforrás-csoport* | Használjon meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. |
    | Hely | *USA 2. keleti régiója* | Válassza ki az adatmegosztási fiókhoz tartozó régiót.
    | Name | *datashareaccount* | Adja meg az adatmegosztási fiók nevét. |
    | | |

1. Válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás** lehetőséget az adatmegosztási fiók kiépítéséhez. Az új adatmegosztási fiók üzembe helyezése általában körülbelül 2 percet vesz igénybe. 

1. Ha a telepítés befejeződött, válassza az **Ugrás erőforráshoz** lehetőséget.

### <a name="create-a-share"></a>Megosztás létrehozása

1. Navigáljon az adatmegosztás áttekintő oldalára.

    ![Az adatok megosztása](./media/share-receive-data.png "Az adatok megosztása") 

1. Válassza **az adatmegosztás megkezdése** lehetőséget.

1. Válassza a **Létrehozás** lehetőséget.   

1. Adja meg a megosztás részleteit. Adja meg a nevet, a megosztás típusát, a megosztási tartalmak leírását és a használati feltételeket (opcionális). 

    ![EnterShareDetails](./media/enter-share-details.png "Adja meg a megosztás részleteit") 

1. Válassza a **Folytatás** lehetőséget.

1. Az adatkészletek megosztáshoz való hozzáadásához válassza az **adatkészletek hozzáadása** elemet. 

    ![Adatkészletek hozzáadása a megosztáshoz](./media/datasets.png "Adathalmazok")

1. Válassza ki a hozzáadni kívánt adatkészlet típusát. Az adathalmazok eltérő listáját fogja látni az előző lépésben kiválasztott megosztási típustól (pillanatkép vagy helyben) függően. 

    ![AddDatasets](./media/add-datasets.png "Adatkészletek hozzáadása")    

1. Válassza ki az SQL Server vagy a szinapszis munkaterületet. Ha HRE hitelesítést használ, és a jelölőnégyzet **lehetővé teszi, hogy az adatmegosztás a fenti "felhasználó létrehozása" SQL-szkriptet futtassa a saját nevében** , jelölje be a jelölőnégyzetet. Ha SQL-hitelesítést használ, adja meg a hitelesítő adatokat, és kövesse a parancsfájl futtatásához szükséges előfeltételek című részt a képernyőn. Ez lehetővé teszi az adatmegosztási erőforrás számára az SQL-ADATBÁZISból való olvasást. 

   A **tovább** gombra kattintva navigáljon a megosztani kívánt objektumhoz, és válassza az "adatkészletek hozzáadása" lehetőséget. Kiválaszthatja a Azure SQL Database és az Azure szinapszis Analytics (korábban Azure SQL DW), illetve az Azure szinapszis Analytics (munkaterület) dedikált SQL-készletből származó táblákat és nézeteket. 

    ![SelectDatasets](./media/select-datasets-sql.png "Adatkészletek kiválasztása")    

1. A címzettek lapon adja meg az adatfogyasztó e-mail-címeit a "+ Címzett hozzáadása" lehetőség kiválasztásával. Az e-mail-címnek a címzett Azure bejelentkezési e-mail-címének kell lennie.

    ![AddRecipients](./media/add-recipient.png "Címzettek hozzáadása") 

1. Válassza a **Folytatás** lehetőséget.

1. Ha kiválasztotta a pillanatkép-megosztás típusát, beállíthatja a pillanatkép-ütemtervet, hogy az adatokra vonatkozó frissítéseket biztosítson az adatfogyasztónak. 

    ![EnableSnapshots](./media/enable-snapshots.png "Pillanatképek engedélyezése") 

1. Válassza ki a kezdési időt és az ismétlődési időközt. 

1. Válassza a **Folytatás** lehetőséget.

1. A felülvizsgálat + létrehozás lapon tekintse át a csomag tartalmát, a beállításokat, a címzetteket és a szinkronizálási beállításokat. Válassza a **Létrehozás** lehetőséget.

Az Azure-beli adatmegosztás már létrejött, és az adatmegosztás címzettje most már készen áll a meghívás elfogadására. 

## <a name="receive-data"></a>Adatfogadás

### <a name="prerequisites-to-receive-data"></a>Az Adatfogadás előfeltételei
Az adatmegosztási Meghívások elfogadása előtt számos Azure-erőforrást kell kiépíteni, amelyek alább láthatók. 

Az adatmegosztási Meghívások elfogadása előtt győződjön meg arról, hogy az összes előfeltétel befejeződik. 

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* Egy adatmegosztás meghívása: Microsoft Azure egy meghívót, melynek tárgya "Azure-adatmegosztási meghívás **<yourdataprovider@domain.com>** ".
* Regisztrálja a [Microsoft. DataShare erőforrás-szolgáltatót](concepts-roles-permissions.md#resource-provider-registration) az Azure-előfizetésben, amelyben létre fog hozni egy adatmegosztási erőforrást és az Azure-előfizetést, ahol a cél Azure-adattárolók találhatók.

### <a name="prerequisites-for-target-storage-account"></a>A célként megadott Storage-fiók előfeltételei
Ha úgy dönt, hogy az Azure Storage-ba fogadja az adatgyűjtést, az alábbi lista tartalmazza az előfeltételek listáját.

* Azure Storage-fiók: Ha még nem rendelkezik ilyennel, létrehozhat egy [Azure Storage-fiókot](../storage/common/storage-account-create.md)is. 
* A Storage-fiókba való írásra vonatkozó engedély, amely megtalálható a *Microsoft. Storage/storageAccounts/Write* szolgáltatásban. Ez az engedély a **Közreműködő** szerepkör részét képezi. 
* Engedély az adatmegosztási erőforrás felügyelt identitásának szerepkör-hozzárendelésének hozzáadásához a Storage-fiókhoz, amely megtalálható a *Microsoft. Authorization/szerepkör-hozzárendelésekben/írásban*. Ez az engedély a **Tulajdonos** szerepkör részét képezi.  

### <a name="prerequisites-for-sql-target"></a>Az SQL-cél előfeltételei
Ha úgy dönt, hogy befogadja az Azure SQL Databaseba az Azure szinapszis Analyticset, az alábbi lista tartalmazza az előfeltételek listáját. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Az Adatfogadás előfeltételei a Azure SQL Database vagy az Azure szinapszis Analytics szolgáltatásba (korábban Azure SQL DW)

Ha olyan SQL Server-kiszolgálóra szeretne adatfogadást kapni, amely az SQL Server **Azure Active Directory rendszergazdája** , itt látható az előfeltételek listája:

* Egy Azure SQL Database vagy Azure szinapszis Analytics (korábban Azure SQL DW).
* A *Microsoft. SQL/Servers/Databases/Write* adatbázisban található SQL Server-adatbázisba való írásra vonatkozó engedély. Ez az engedély a **Közreműködő** szerepkör részét képezi.
* SQL Server tűzfal-hozzáférés. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon az SQL Server elemre. A bal oldali navigációs sávon válassza a *tűzfalak és virtuális hálózatok* lehetőséget.
    1. Az **Igen** gombra kattintva *engedélyezheti, hogy az Azure-szolgáltatások és-erőforrások hozzáférjenek ehhez a kiszolgálóhoz*.
    1. Kattintson az **+ ügyfél IP-** címének hozzáadása elemre. Az ügyfél IP-címének módosítása változhat. Előfordulhat, hogy ezt a folyamatot meg kell ismételni, amikor legközelebb megosztja az SQL-adatok Azure Portalból való megosztását. Hozzáadhat IP-címtartományt is.
    1. Kattintson a **Mentés** gombra. 
    
Ha olyan SQL Server-kiszolgálóra szeretne adatfogadást kapni, amely nem a **Azure Active Directory-rendszergazda**, az alábbi lista tartalmazza az előfeltételeket. Az előfeltételek konfigurálásához kövesse a [lépésenkénti bemutató](https://youtu.be/aeGISgK1xro) lépéseit.

* Egy Azure SQL Database vagy Azure szinapszis Analytics (korábban Azure SQL DW).
* A *Microsoft. SQL/Servers/Databases/Write* adatbázisban található SQL Server-adatbázisba való írásra vonatkozó engedély. Ez az engedély a **Közreműködő** szerepkör részét képezi. 
* Az adatmegosztási erőforrás felügyelt identitására vonatkozó engedély a Azure SQL Database vagy az Azure szinapszis Analytics eléréséhez. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon az SQL Serverre, és állítsa be magát a **Azure Active Directory-rendszergazdaként**.
    1. Kapcsolódjon a Azure SQL Database/adattárházhoz a [Lekérdezés-szerkesztő](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) használatával, vagy SQL Server Management Studio Azure Active Directory hitelesítéssel. 
    1. A következő szkript végrehajtásával adja hozzá az adatmegosztás felügyelt identitását "db_datareader, db_datawriter, db_ddladmin" értékre. Active Directory használatával kell kapcsolódnia, nem SQL Server a hitelesítéshez. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Vegye figyelembe, hogy a *<share_acc_name>* az adatmegosztási erőforrás neve. Ha még nem hozott létre adatmegosztási erőforrást, később is visszatérhet ehhez az előfeltételhöz.         

* SQL Server tűzfal-hozzáférés. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portal található SQL Serverben navigáljon a *tűzfalak és a virtuális hálózatok* területére.
    1. Az **Igen** gombra kattintva *engedélyezheti, hogy az Azure-szolgáltatások és-erőforrások hozzáférjenek ehhez a kiszolgálóhoz*.
    1. Kattintson az **+ ügyfél IP-** címének hozzáadása elemre. Az ügyfél IP-címének módosítása változhat. Előfordulhat, hogy ezt a folyamatot meg kell ismételni, amikor legközelebb megosztja az SQL-adatok Azure Portalból való megosztását. Hozzáadhat IP-címtartományt is.
    1. Kattintson a **Mentés** gombra. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Az Azure szinapszis Analytics (munkaterület) SQL-készletbe való adatfogadás előfeltételei

* Egy Azure szinapszis Analytics (munkaterület) dedikált SQL-készlet. Az Adatfogadás kiszolgáló nélküli SQL-készletbe jelenleg nem támogatott.
* Engedély a szinapszis munkaterületen található SQL-készletbe való írásra, amely megtalálható a *Microsoft. szinapszis/munkaterület/sqlPools/Write* fájlokban. Ez az engedély a **Közreműködő** szerepkör részét képezi.
* Engedély az adatmegosztási erőforrás felügyelt identitásához a szinapszis-munkaterület SQL-készletének eléréséhez. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon a szinapszis munkaterületre. Válassza az SQL Active Directory-rendszergazda lehetőséget a bal oldali navigációs sávon, és állítsa be magát a **Azure Active Directory-rendszergazdaként**.
    1. Nyissa meg a szinapszis Studio alkalmazást, és válassza a *kezelés* lehetőséget a bal oldali navigációs sávon. Válassza a *hozzáférés-vezérlés* lehetőséget a biztonság területen. Rendeljen hozzá saját **SQL-rendszergazdai** vagy **munkaterület-rendszergazdai** szerepkört.
    1. A szinapszis Studióban válassza a *fejlesztés* lehetőséget a bal oldali navigációs sávon. Hajtsa végre a következő parancsfájlt az SQL-készletben az adatmegosztási erőforrás felügyelt identitásának "db_datareader, db_datawriter, db_ddladmin" értékkel való hozzáadásához. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       Vegye figyelembe, hogy a *<share_acc_name>* az adatmegosztási erőforrás neve. Ha még nem hozott létre adatmegosztási erőforrást, később is visszatérhet ehhez az előfeltételhöz.  

* A szinapszis munkaterület tűzfal-hozzáférése. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portalban navigáljon a szinapszis munkaterületre. A bal oldali navigációs sávon válassza a *tűzfalak* lehetőséget.
    1. Ide **kattintva** *engedélyezheti, hogy az Azure-szolgáltatások és-erőforrások hozzáférjenek ehhez a munkaterülethez*.
    1. Kattintson az **+ ügyfél IP-** címének hozzáadása elemre. Az ügyfél IP-címének módosítása változhat. Előfordulhat, hogy ezt a folyamatot meg kell ismételni, amikor legközelebb megosztja az SQL-adatok Azure Portalból való megosztását. Hozzáadhat IP-címtartományt is.
    1. Kattintson a **Mentés** gombra. 

### <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

### <a name="open-invitation"></a>Meghívás megnyitása

1. A meghívót megnyithatja e-mailben, vagy közvetlenül a Azure Portalból. 

   Ha meg szeretné nyitni a meghívót az e-mailből, akkor az adatszolgáltatótól érkező meghívót a Beérkezett üzenetek közül A meghívó Microsoft Azure származik, és az **Azure-beli adatmegosztási meghívást kapott <yourdataprovider@domain.com>**. Kattintson a **meghívás megtekintése** elemre, hogy megtekintse az Azure-beli meghívót. 

   Ha közvetlenül Azure Portal szeretné megnyitni a meghívót, keresse meg az **adatmegosztási meghívásokat** a Azure Portalban. Ekkor megjelenik az adatmegosztási meghívások listája.

   ![Meghívások listája](./media/invitations.png "Meghívások listája") 

1. Válassza ki a megtekinteni kívánt megosztást. 

### <a name="accept-invitation"></a>Meghívás elfogadása
1. Győződjön meg arról, hogy az összes mezőt felülvizsgálják, beleértve a használati **feltételeket** is. Ha elfogadja a használati feltételeket, a jelölőnégyzet bejelölésével jelezheti, hogy elfogadja. 

   ![Használati feltételek](./media/terms-of-use.png "Használati feltételek") 

1. A *cél adatmegosztási fiók* területen válassza ki azt az előfizetést és erőforráscsoportot, amelybe az adatmegosztást telepíteni fogja. 

   Az **adatmegosztási fiók** mezőben válassza az **új létrehozása** lehetőséget, ha nem rendelkezik meglévő adatmegosztási fiókkal. Ellenkező esetben válasszon ki egy meglévő adatmegosztási fiókot, amelyet el szeretne fogadni az adatmegosztásban. 

   A **Beérkezett megosztás neve** mezőben hagyhatja az alapértelmezett által megadott értéket, vagy megadhat egy új nevet a fogadott megosztáshoz. 

   Miután jóváhagyta a használati feltételeket, és megadott egy adatmegosztási fiókot a kapott megosztás kezeléséhez, válassza az **elfogadás és konfigurálás** lehetőséget. A rendszer létrehoz egy megosztási előfizetést. 

   ![Elfogadás beállításai](./media/accept-options.png "Elfogadás beállításai") 

   Ekkor megjelenik a kapott megosztás az adatmegosztási fiókban. 

   Ha nem szeretné elfogadni a meghívót, válassza az *elutasítás* lehetőséget. 

### <a name="configure-received-share"></a>Fogadott megosztás konfigurálása
Az alábbi lépésekkel konfigurálhatja, hogy hová kívánja fogadni az adatgyűjtést.

1. Válassza az **adatkészletek** lapot. Jelölje be azon adatkészlet melletti jelölőnégyzetet, amelyhez hozzá szeretné rendelni a célhelyet. A cél adattár kiválasztásához válassza a **+ Térkép** lehetőséget. 

   ![Leképezés célhelyre](./media/dataset-map-target.png "Leképezés célhelyre") 

1. Válassza ki azt a cél adattárat, amelybe az adatterületet szeretné kijelölni. A célként megadott adattárban lévő adatfájlokat vagy táblákat ugyanazzal az elérési úttal és névvel írja felül a rendszer. Ha az SQL-célhelyre küldi az információt, és az **adatmegosztás engedélyezése a fenti "felhasználó létrehozása" SQL-parancsfájl futtatása a saját nevében** jelölőnégyzet jelenik meg, jelölje be a jelölőnégyzetet. Ellenkező esetben kövesse a parancsfájl futtatásához szükséges előfeltételek című témakör utasításait a képernyőn. Ez megadja az adatmegosztási erőforrás írási engedélyét a cél SQL-ADATBÁZIShoz.

   ![Cél Storage-fiók](./media/dataset-map-target-sql.png "Célként megadott adattár") 

1. A pillanatkép-alapú megosztáshoz, ha az adatszolgáltató pillanatkép-ütemtervet hozott létre az adatok rendszeres frissítéséhez, **akkor a pillanatkép-ütemterv** kiválasztásával is engedélyezheti a pillanatkép-ütemtervet. Jelölje be a pillanatkép-ütemterv melletti jelölőnégyzetet, majd válassza a **+ Engedélyezés** lehetőséget. Vegye figyelembe, hogy az első ütemezett pillanatkép az ütemezési idő egy percén belül indul el, és az azt követő Pillanatképek az ütemezett időponttól számított másodperceken belül kezdődnek.

   ![Pillanatkép-ütemterv engedélyezése](./media/enable-snapshot-schedule.png "Pillanatkép-ütemterv engedélyezése")

### <a name="trigger-a-snapshot"></a>Pillanatkép indítása
Ezek a lépések csak a pillanatkép-alapú megosztásra vonatkoznak.

1. A pillanatképek elindításához válassza a **részletek** lapot, majd az **trigger pillanatképét**. Itt aktiválhatja az adatok teljes vagy növekményes pillanatképét. Ha az első alkalommal fogadja az adatait az adatszolgáltatótól, válassza a teljes másolás lehetőséget. Az SQL-források esetében csak a teljes pillanatkép támogatott. A pillanatképek végrehajtásakor a további Pillanatképek nem indulnak el, amíg az előző végére nem kerül sor.

   ![Pillanatkép indítása](./media/trigger-snapshot.png "Pillanatkép indítása") 

1. Ha a legutóbbi futtatási állapot *sikeres*, nyissa meg a cél adattárt a kapott adatértékek megtekintéséhez. Válassza az **adatkészletek** lehetőséget, majd kattintson a hivatkozásra a cél elérési úton. 

   ![Fogyasztói adatkészletek](./media/consumer-datasets.png "Fogyasztói adatkészlet megfeleltetése") 

### <a name="view-history"></a>Előzmények megtekintése
Ez a lépés csak a pillanatkép-alapú megosztásra vonatkozik. A pillanatképek előzményeinek megtekintéséhez válassza az **Előzmények** fület. Itt megtalálhatja az elmúlt 30 napban létrehozott összes pillanatkép előzményeit. 

## <a name="supported-data-types"></a>Támogatott adattípusok
Az SQL-forrásokból származó adatok megosztásakor a rendszer a következő leképezést használja SQL Server adattípusokból az Azure-beli adatmegosztások köztes adattípusokra a pillanatkép-készítési folyamat során. 

| SQL Server adattípus | Az Azure-adatmegosztás közbenső adattípusa |
|:--- |:--- |
| bigint |Int64 |
| binary |Bájt [] |
| bit |Logikai |
| char |Karakterlánc, char [] |
| dátum |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Tizedesjegy |Tizedesjegy |
| FILESTREAM attribútum (varbinary (max)) |Bájt [] |
| Float |Dupla |
| image |Bájt [] |
| int |Int32 |
| pénzt |Tizedesjegy |
| NCHAR |Karakterlánc, char [] |
| ntext |Karakterlánc, char [] |
| numerikus |Tizedesjegy |
| nvarchar |Karakterlánc, char [] |
| valós szám |Egyirányú |
| ROWVERSION |Bájt [] |
| idő adattípusúra |DateTime |
| smallint |Int16 |
| túlcsordulási |Tizedesjegy |
| sql_variant |Objektum |
| szöveg |Karakterlánc, char [] |
| time |időtartam |
| időbélyeg |Bájt [] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Bájt [] |
| varchar |Karakterlánc, char [] |
| xml |Sztring |

>[!NOTE]
> 1. A decimális ideiglenes típusra leképezett adattípusok esetén a pillanatkép jelenleg legfeljebb 28 pontosságot támogat. Ha a 28-nál nagyobb pontosságú adatmennyiségre van szüksége, érdemes lehet karakterlánccá konvertálni. 
> 1.  Ha az Azure SQL Database-ből származó adatok megosztása az Azure szinapszis Analytics szolgáltatásba történik, nem minden adattípus támogatott. A részletekért tekintse meg a következő témakört [: table adattípusok a DEDIKÁLT SQL-készletben](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md) . 

## <a name="sql-always-encrypted-or-dynamic-data-masking"></a>SQL Always Encrypted vagy dinamikus adatmaszkolás
Az Azure-beli adatmegosztás jelenleg nem támogatja az Azure SQL-adatbázisokat, Always Encrypted konfigurálva. 

A dinamikus adatmaszkolást tartalmazó forrás SQL-táblák esetében az adat a címzett oldalon maszkoltan fog megjelenni.

## <a name="sql-snapshot-performance"></a>SQL-pillanatkép teljesítménye
Az SQL-Pillanatképek teljesítményét számos tényező befolyásolja. A saját teljesítményű tesztelést mindig ajánlott elvégezni. Az alábbiakban néhány példát befolyásoló tényezők befolyásolják a teljesítményt.

* A forrás és a cél SQL-adattár hardveres konfigurációja (például virtuális mag, memória, DWU). 
* Párhuzamos hozzáférés a forrás-és a célként megadott adattárakhoz. Ha ugyanazt az SQL-adattárból több táblát és nézetet oszt meg, vagy több táblát és nézetet is fogad ugyanabba az SQL-adattárba, a rendszer hatással lesz a teljesítményre.   
* A forrás és a cél adattárolók helye. 

## <a name="troubleshoot-sql-snapshot-failure"></a>SQL-pillanatkép hibáinak hibaelhárítása
A pillanatképek meghibásodásának leggyakoribb oka az, hogy az adatmegosztás nem rendelkezik engedéllyel a forrás-vagy a célként megadott adattárhoz. Ahhoz, hogy adatmegosztási engedélyt adjon a forrás vagy a cél Azure SQL Database vagy az Azure szinapszis Analytics (korábban Azure SQL DW) számára, futtatnia kell a megadott SQL-parancsfájlt az SQL-adatbázishoz való csatlakozáskor Azure Active Directory hitelesítés használatával. További SQL-Pillanatképek hibáinak hibaelhárításához tekintse meg a [Pillanatkép-hiba hibaelhárítása](data-share-troubleshoot.md#snapshots)című témakört.

## <a name="next-steps"></a>Következő lépések
Megtanulta, hogyan oszthat meg és fogadhat adatait SQL-forrásokból az Azure adatmegosztási szolgáltatás használatával. Ha többet szeretne megtudni a más adatforrásokból történő megosztásról, folytassa a [támogatott adattárakkal](supported-data-stores.md).