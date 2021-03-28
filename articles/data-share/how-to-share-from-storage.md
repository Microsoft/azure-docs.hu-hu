---
title: Adatok megosztása és fogadása az Azure Blob Storage-ból és az Azure Data Lake Storage-ból
description: Ismerje meg, hogyan oszthat meg és fogadhat adatait az Azure Blob Storage és Azure Data Lake Storage.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/23/2021
ms.openlocfilehash: c94d6f7d4d06d7dbaa6a2ad54f94575a6cc85d33
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644662"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Adatok megosztása és fogadása az Azure Blob Storage-ból és az Azure Data Lake Storage-ból

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Az Azure-beli adatmegosztás támogatja a pillanatkép-alapú megosztást egy Storage-fiókból. Ez a cikk az Azure Blob Storage, Azure Data Lake Storage Gen1 és Azure Data Lake Storage Gen2 adatainak megosztását és fogadását ismerteti.

Az Azure-beli adatmegosztás támogatja a fájlok, mappák és fájlrendszerek megosztását Azure Data Lake Gen1 és Azure Data Lake Gen2. Emellett támogatja a Blobok, mappák és tárolók megosztását az Azure Blob Storageból. Jelenleg csak a blokk Blobok támogatottak. Az ezekből a forrásokból megosztott adatok Azure Data Lake Gen2 vagy az Azure Blob Storagetól is fogadhatók.

Ha a fájlrendszer, a tárolók vagy a mappák a pillanatkép-alapú megosztásban vannak megosztva, az adatok felhasználói dönthetnek úgy, hogy teljes másolatot készítenek a megosztási adatokról. Vagy használhatják a növekményes pillanatképet is, amely csak az új vagy frissített fájlokat másolja. A növekményes pillanatkép-képesség a fájlok utolsó módosításának időpontján alapul. 

Az azonos nevű meglévő fájlokat a rendszer felülírja egy pillanatkép során. A forrásból törölt fájl nem törlődik a célhelyen. Az üres almappákat a forrás nem másolja át a célhelyre. 

## <a name="share-data"></a>Adatok megosztása

Az alábbi részekben található információk használatával oszthat meg adatokat az Azure-adatmegosztás használatával. 
### <a name="prerequisites-to-share-data"></a>Az adatmegosztásra vonatkozó előfeltételek

* Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* A címzett Azure-beli bejelentkezési e-mail-címének megkeresése. A címzett e-mail-aliasa nem fog működni a célra.
* Ha az Azure-beli adattár egy másik Azure-előfizetésben található, mint amelyikben az adatmegosztási erőforrást hozza létre, regisztrálja a [Microsoft. DataShare erőforrás-szolgáltatót](concepts-roles-permissions.md#resource-provider-registration) az előfizetésben, ahol az Azure-adattár található. 

### <a name="prerequisites-for-the-source-storage-account"></a>A forrásként szolgáló Storage-fiók előfeltételei

* Egy Azure Storage-fiók. Ha még nem rendelkezik fiókkal, [hozzon létre egyet](../storage/common/storage-account-create.md).
* Engedély a Storage-fiókba való íráshoz. Az írási engedély a *Microsoft. Storage/storageAccounts/Write*. Ez a közreműködő szerepkör részét képezi.
* A szerepkör-hozzárendelésnek a Storage-fiókhoz való hozzáadására vonatkozó engedély. Ez az engedély a *Microsoft. Authorization/szerepkör-hozzárendelésekben/írásban* érhető el. Ez a tulajdonosi szerepkör részét képezi. 

### <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Adatmegosztási fiók létrehozása

Azure-beli adatmegosztási erőforrás létrehozása Azure-erőforráscsoporthoz.

1. A portál bal felső sarkában nyissa meg a menüt, majd válassza az **erőforrás létrehozása** (+) lehetőséget.

1. Keresse meg az *adatmegosztást*.

1. Válassza **az adatmegosztás** és a **Létrehozás** lehetőséget.

1. Adja meg az Azure-beli adatmegosztási erőforrás alapszintű adatait: 

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Előfizetés | Az Ön előfizetése | Válasszon ki egy Azure-előfizetést az adatmegosztási fiókjához.|
    | Erőforráscsoport | *teszt – erőforrás-csoport* | Használjon meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot. |
    | Hely | *USA 2. keleti régiója* | Válassza ki az adatmegosztási fiókhoz tartozó régiót.
    | Name | *datashareaccount* | Nevezze el az adatmegosztási fiókot. |
    | | |

1. Az adatmegosztási fiók kiépítéséhez válassza a **felülvizsgálat +**  >  **Létrehozás** létrehozása lehetőséget. Az új adatmegosztási fiók üzembe helyezése általában körülbelül 2 percet vesz igénybe. 

1. Az üzembe helyezés befejezése után válassza az **Erőforrás megnyitása** elemet.

### <a name="create-a-share"></a>Megosztás létrehozása

1. Nyissa meg az adatmegosztás **– Áttekintés** lapot.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Az adatmegosztás áttekintését bemutató képernyőkép.":::

1. Válassza **az adatmegosztás megkezdése** lehetőséget.

1. Válassza a **Létrehozás** lehetőséget.   

1. Adja meg a megosztás részleteit. Adja meg a nevet, a megosztás típusát, a megosztási tartalmak leírását és a használati feltételeket (opcionális). 

    ![Az adatmegosztás részleteit ábrázoló képernyőfelvétel.](./media/enter-share-details.png "Adja meg az adatmegosztási adatokat.") 

1. Válassza a **Folytatás** lehetőséget.

1. Az adatkészletek megosztáshoz való hozzáadásához válassza az **adatkészletek hozzáadása** elemet. 

    ![Képernyőfelvétel: adatkészletek hozzáadása a megosztáshoz.](./media/datasets.png "Adatkészletek.")

1. Válassza ki a hozzáadni kívánt adatkészlet-típust. Az adatkészlet-típusok listája attól függ, hogy az előző lépésben a pillanatkép-alapú megosztást vagy a helyben történő megosztást választotta-e. 

    ![Az adatkészlet típusának kiválasztását bemutató képernyőkép.](./media/add-datasets.png "Adatkészletek hozzáadása.")    

1. Nyissa meg a megosztani kívánt objektumot. Ezután válassza az **adatkészletek hozzáadása** elemet. 

    ![A megosztani kívánt objektumok kiválasztását bemutató képernyőkép.](./media/select-datasets.png "Válassza az adatkészletek lehetőséget.")    

1. A **címzettek** lapon adja hozzá az adatfogyasztó e-mail-címét a **Címzett hozzáadása** elem kiválasztásával. 

    ![A címzett e-mail-címeinek hozzáadását bemutató képernyőkép.](./media/add-recipient.png "Címzettek hozzáadása.") 

1. Válassza a **Folytatás** lehetőséget.

1. Ha kiválasztott egy pillanatkép-megosztási típust, beállíthatja, hogy a pillanatkép-készítési terv frissítse az adatfogyasztó adatait. 

    ![A pillanatkép-ütemterv beállításait bemutató képernyőkép.](./media/enable-snapshots.png "Pillanatképek engedélyezése.") 

1. Válassza ki a kezdési időt és az ismétlődési időközt. 

1. Válassza a **Folytatás** lehetőséget.

1. A **felülvizsgálat + létrehozás** lapon tekintse át a csomag tartalmát, a beállításokat, a címzetteket és a szinkronizálási beállításokat. Ezután kattintson a **Létrehozás** elemre.

Ezzel létrehozta az Azure-beli adatmegosztást. Az adatmegosztás címzettje elfogadhatja a meghívót. 

## <a name="receive-data"></a>Adatfogadás

A következő szakaszok ismertetik, hogyan fogadhat megosztott adattípusokat.
### <a name="prerequisites-to-receive-data"></a>Az Adatfogadás előfeltételei
Mielőtt elfogad egy adatmegosztási meghívót, ellenőrizze, hogy rendelkezik-e az alábbi előfeltételekkel: 

* Azure-előfizetés. Ha nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Egy meghívás az Azure-ból. Az e-mail-tárgynak "Azure-beli adatmegosztási meghívás" típusúnak kell lennie *\<yourdataprovider\@domain.com>* .
* Regisztrált [Microsoft. DataShare erőforrás-szolgáltató](concepts-roles-permissions.md#resource-provider-registration) a-ben:
    * Az Azure-előfizetés, amelyben létre fog hozni egy adatmegosztási erőforrást.
    * Az Azure-előfizetés, amelyben a cél Azure-adattárolók találhatók.

### <a name="prerequisites-for-a-target-storage-account"></a>A célként megadott Storage-fiók előfeltételei

* Egy Azure Storage-fiók. Ha még nem rendelkezik ilyennel, [hozzon létre egy fiókot](../storage/common/storage-account-create.md). 
* Engedély a Storage-fiókba való íráshoz. Ez az engedély *Microsoft. Storage/storageAccounts/Write* nyelven érhető el. Ez a közreműködő szerepkör részét képezi. 
* A szerepkör-hozzárendelésnek a Storage-fiókhoz való hozzáadására vonatkozó engedély. Ez a hozzárendelés a *Microsoft. Authorization/szerepkör-hozzárendelésekben/írásban* érhető el. Ez a tulajdonosi szerepkör részét képezi.  

### <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

### <a name="open-an-invitation"></a>Meghívás megnyitása

Megnyithat egy meghívót e-mailben vagy közvetlenül a Azure Portal.

1. Ha e-mailben szeretne meghívni egy meghívót, keresse meg az adatszolgáltatótól érkező meghívót. A Microsoft Azure meghívása "Azure-adatmegosztási meghívás" címmel van elküldve *\<yourdataprovider\@domain.com>* . Válassza a **meghívás megtekintése** lehetőséget az Azure-beli meghívás megtekintéséhez. 

   Ha meg szeretne nyitni egy meghívót a Azure Portal, keressen rá az *adatmegosztási meghívásokra*. Ekkor megjelenik az adatmegosztási meghívások listája.

   ![A Azure Portal meghívóinak listáját bemutató képernyőkép.](./media/invitations.png "Meghívások listája.") 

1. Válassza ki a megtekinteni kívánt megosztást. 

### <a name="accept-an-invitation"></a>Meghívás elfogadása
1. Tekintse át az összes mezőt, beleértve a **használati feltételek** is. Ha elfogadja a feltételeket, jelölje be a jelölőnégyzetet. 

   ![Képernyőkép a Használati feltételeki körzetről.](./media/terms-of-use.png "Használati feltételek.") 

1. A **cél adatmegosztási fiók** területen válassza ki azt az előfizetést és erőforráscsoportot, amelyben az adatmegosztást telepíteni fogja. Ezután adja meg a következő mezőket:

   * Az **adatmegosztási fiók** mezőben válassza az **új létrehozása** lehetőséget, ha nem rendelkezik adatmegosztási fiókkal. Ellenkező esetben válasszon ki egy meglévő adatmegosztási fiókot, amely elfogadja az adatmegosztást. 

   * A **fogadott megosztás neve** mezőben hagyja meg az alapértelmezett értéket, amelyet az adatszolgáltató megadott, vagy adjon meg egy új nevet a fogadott megosztáshoz. 

1. Válassza **az elfogadás és konfigurálás** lehetőséget. A rendszer létrehoz egy megosztási előfizetést. 

   ![A konfigurációs beállítások elfogadásának helyét bemutató képernyőkép.](./media/accept-options.png "Elfogadás beállításai") 

    A fogadott megosztás megjelenik az adatmegosztási fiókban. 

    Ha nem szeretné elfogadni a meghívót, válassza az **elutasítás** lehetőséget. 

### <a name="configure-a-received-share"></a>Fogadott megosztás konfigurálása
Az ebben a szakaszban ismertetett lépéseket követve konfigurálhatja az Adatfogadás helyét.

1. Az **adatkészletek** lapon jelölje be azon adatkészlet melletti jelölőnégyzetet, amelyhez célhelyet szeretne rendelni. Válassza a **leképezés célhelyre** lehetőséget a cél adattár kiválasztásához. 

   ![A célhelyre való leképezést bemutató képernyőkép.](./media/dataset-map-target.png "Leképezés a célhelyre.") 

1. Válassza ki az adattároló adattárat. A célként megadott adattárban található, azonos elérési úttal és névvel rendelkező fájlok felül lesznek írva. 

   ![A cél Storage-fiók kiválasztásának helyét bemutató képernyőkép.](./media/map-target.png "Cél tárterület.") 

1. Pillanatkép-alapú megosztás esetén, ha az adatszolgáltató egy pillanatkép-ütemtervet használ az adatok rendszeres frissítéséhez, akkor a **Pillanatkép-ütemterv** lapon engedélyezheti az ütemtervet. Jelölje be a pillanatkép-ütemterv melletti jelölőnégyzetet. Ezután válassza az **Engedélyezés** lehetőséget. Vegye figyelembe, hogy az első ütemezett pillanatkép az ütemezési idő egy percén belül indul el, és az azt követő Pillanatképek az ütemezett időponttól számított másodperceken belül kezdődnek.

   ![A pillanatkép-ütemterv engedélyezését bemutató képernyőkép.](./media/enable-snapshot-schedule.png "Pillanatkép-ütemterv engedélyezése.")

### <a name="trigger-a-snapshot"></a>Pillanatkép indítása
Az ebben a szakaszban szereplő lépések csak a pillanatkép-alapú megosztásra vonatkoznak.

1. A **részletek** lapról aktiválhat egy pillanatképet. A lapon válassza a **Pillanatkép indítása** lehetőséget. Dönthet úgy, hogy az adatok teljes pillanatképét vagy növekményes pillanatképét indítja el. Ha első alkalommal fogadja az adatszolgáltató adatait, válassza a **teljes másolás** lehetőséget. A pillanatképek végrehajtásakor a további Pillanatképek nem indulnak el, amíg az előző végére nem kerül sor.

   ![Képernyőkép a trigger-pillanatkép kiválasztásáról.](./media/trigger-snapshot.png "Pillanatfelvétel elindítása.") 

1. Ha a legutóbbi futtatási állapot *sikeres*, nyissa meg a cél adattárat a kapott adattároló megtekintéséhez. Válassza az **adatkészletek** lehetőséget, majd válassza ki a cél elérési utat mutató hivatkozást. 

   ![A fogyasztói adatkészlet megfeleltetését bemutató képernyőkép.](./media/consumer-datasets.png "Fogyasztói adatkészlet leképezése.") 

### <a name="view-history"></a>Előzmények megtekintése
A pillanatképek előzményeit csak pillanatkép-alapú megosztásban tekintheti meg. Az előzmények megjelenítéséhez nyissa meg az **Előzmények** lapot. Itt láthatja az elmúlt 30 napban generált összes pillanatkép előzményeit. 

## <a name="storage-snapshot-performance"></a>Tárolási Pillanatképek teljesítménye
A tárolási Pillanatképek teljesítményét számos tényező befolyásolja, a fájlok száma és a megosztott adatok mérete mellett. A saját teljesítményű tesztelést mindig ajánlott elvégezni. Az alábbiakban néhány példát befolyásoló tényezők befolyásolják a teljesítményt.

* Párhuzamos hozzáférés a forrás-és a célként megadott adattárakhoz.  
* A forrás és a cél adattárolók helye. 
* Növekményes pillanatkép esetén a megosztott adatkészletben található fájlok száma hatással lehet az utolsó sikeres pillanatkép utáni utolsó módosítási idővel rendelkező fájlok listájának megkeresésére. 


## <a name="next-steps"></a>Következő lépések
Megtanulta, hogyan oszthat meg és fogadhat adatait egy Storage-fiókból az Azure adatmegosztási szolgáltatás használatával. További információ a más adatforrásokból történő megosztásról: [támogatott adattárak](supported-data-stores.md).