---
title: Adatok megosztása és fogadása az Azure Blob Storage-ból és az Azure Data Lake Storage-ból
description: Megtudhatja, hogyan oszthat meg és fogadhat adatokat a Azure Blob Storage és Azure Data Lake Storage.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 59c1ca67c9e93b62890512cda647ffcdf7712f9a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819267"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Adatok megosztása és fogadása az Azure Blob Storage-ból és az Azure Data Lake Storage-ból

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure Data Share támogatja a tárfiókok pillanatkép-alapú megosztását. Ez a cikk bemutatja, hogyan oszthat meg és fogadhat adatokat Azure Blob Storage, Azure Data Lake Storage Gen1 és Azure Data Lake Storage Gen2.

Azure Data Share azure data lake gen1 és Azure Data Lake Gen2 fájlok, mappák és fájlrendszerek megosztását támogatja. Támogatja a blobok, mappák és tárolók megosztását is a Azure Blob Storage. Megoszthat blokkblobokat, hozzáfűzéseket vagy lapblobokat, és blokkblobként kapják meg őket. Az ezekről a forrásokból megosztott adatokat az Azure Data Lake Gen2 vagy a Azure Blob Storage.

Amikor a fájlrendszerek, tárolók vagy mappák meg vannak osztva pillanatkép-alapú megosztásban, az adat felhasználók dönthetnek úgy, hogy a megosztási adatok teljes másolatát készítik el. Vagy a növekményes pillanatkép funkciót is használhatja, hogy csak az új vagy frissített fájlokat másolja. A növekményes pillanatkép-készítési képesség a fájlok utolsó módosítási ideje alapján történik. 

Az azonos nevű meglévő fájlok felül vannak írva a pillanatképek során. A forrásból törölt fájlok nem törlődnek a célon. A forrás üres almappák nem másolhatók át a célhelyre. 

## <a name="share-data"></a>Adatok megosztása

A következő szakaszokban található információk segítségével adatokat oszthat meg a Azure Data Share. 
### <a name="prerequisites-to-share-data"></a>Az adatok megosztásának előfeltételei

* Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Keresse meg a címzett azure-beli bejelentkezési e-mail-címét. A címzett e-mail-aliasa nem fog működni az Ön céljaira.
* Ha a forrás Azure-adattár egy másik Azure-előfizetésben található, mint amelyben az Data Share-erőforrást létrehozza, regisztrálja a [Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) erőforrás-szolgáltatót abban az előfizetésben, amelyben az Azure-adattár található. 

### <a name="prerequisites-for-the-source-storage-account"></a>A forrás tárfiók előfeltételei

* Egy Azure Storage-fiók. Ha még nem rendelkezik fiókkal, [hozzon létre egyet.](../storage/common/storage-account-create.md)
* Engedély a tárfiókba való íráshoz. Írási engedély: *Microsoft.Storage/storageAccounts/write.* Ez a Közreműködő szerepkör része.
* Engedély szerepkör-hozzárendelés hozzáadására a tárfiókhoz. Ez az engedély a *Microsoft.Authorization/role assignments/write fájlban van meg.* Ez a Tulajdonos szerepkör része. 

### <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Új Data Share létrehozása

Hozzon létre Azure Data Share erőforrást egy Azure-erőforráscsoportban.

1. A portál bal felső sarkában nyissa meg a menüt, majd válassza **az Erőforrás létrehozása** (+) lehetőséget.

1. Keressen rá a *Data Share.*

1. Válassza a **Data Share,** majd **a Létrehozás lehetőséget.**

1. Adja meg a Azure Data Share adatait: 

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Előfizetés | Az Ön előfizetése | Válasszon ki egy Azure-előfizetést az adat megosztási fiókjához.|
    | Erőforráscsoport | *test-resource-group* | Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot. |
    | Hely | *USA 2. keleti régiója* | Válasszon egy régiót az adat megosztási fiókjához.
    | Name | *datashareaccount* | Nevezze el az adat megosztási fiókját. |
    | | |

1. Válassza **az Áttekintés + létrehozás** létrehozás  >  **lehetőséget** az adat share-fiók létrehozásához. Egy új adat share-fiók kiépítése általában körülbelül 2 percet vesz igénybe. 

1. Az üzembe helyezés befejezése után válassza az **Erőforrás megnyitása** elemet.

### <a name="create-a-share"></a>Megosztás létrehozása

1. Ugrás az adat megosztásának **Áttekintés lapjára.**

   :::image type="content" source="./media/share-receive-data.png" alt-text="Képernyőkép az adat megosztásának áttekintésről.":::

1. Válassza **a Start sharing your data (Az adatok megosztásának elkezdődjön) lehetőséget.**

1. Válassza a **Létrehozás** lehetőséget.   

1. Adja meg a megosztás részleteit. Adja meg a nevet, a megosztás típusát, a megosztás tartalmának leírását és a használati feltételeket (nem kötelező). 

    ![Képernyőkép az adat megosztásának részleteiről.](./media/enter-share-details.png "Adja meg az adat megosztásának részleteit.") 

1. Válassza a **Folytatás** lehetőséget.

1. Ha adatkészleteket szeretne hozzáadni a megosztáshoz, válassza az **Adatkészletek hozzáadása lehetőséget.** 

    ![Képernyőkép adatkészletek a megosztáshoz való hozzáadásáról.](./media/datasets.png "Adatkészletek.")

1. Válassza ki a hozzáadni kívánt adatkészlettípust. Az adatkészlettípusok listája attól függ, hogy az előző lépésben pillanatkép-alapú vagy helyi megosztást választott. 

    ![Képernyőkép az adatkészlet típusának kiválasztásáról.](./media/add-datasets.png "Adatkészletek hozzáadása.")    

1. Ugrás a megosztani kívánt objektumra. Ezután válassza **az Adatkészletek hozzáadása lehetőséget.** 

    ![Képernyőkép a megosztani kívánt objektum kiválasztásáról.](./media/select-datasets.png "Válassza az Adatkészletek lehetőséget.")    

1. A **Címzettek lapon** adja hozzá az adat fogyasztója e-mail-címét a **Címzett hozzáadása lehetőség kiválasztásával.** 

    ![A címzett e-mail-címének hozzáadását bemutató képernyőkép.](./media/add-recipient.png "Címzettek hozzáadása.") 

1. Válassza a **Folytatás** lehetőséget.

1. Ha egy pillanatkép-megosztási típust választott, beállíthatja a pillanatkép-ütemezést, hogy frissítse az adatokat az adat fogyasztója számára. 

    ![Képernyőkép a pillanatkép ütemezési beállításairól.](./media/enable-snapshots.png "Pillanatképek engedélyezése.") 

1. Válassza ki a kezdési időt és az ismétlődési időközt. 

1. Válassza a **Folytatás** lehetőséget.

1. Az Áttekintés **+ létrehozás lapon** tekintse át a csomag tartalmát, beállításait, címzettjeit és szinkronizálási beállításait. Ezután kattintson a **Létrehozás** elemre.

Létrehozta az Azure-adat megosztását. Az adat megosztásának címzettje elfogadhatja a meghívást. 

## <a name="receive-data"></a>Adatok fogadása

A következő szakaszok a megosztott adatok fogadását ismertetik.
### <a name="prerequisites-to-receive-data"></a>Az adatok fogadásának előfeltételei
Mielőtt elfogad egy adatmegoszl szóló meghívót, győződjön meg arról, hogy a következő előfeltételeknek kell megfeleltetve: 

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)
* Meghívás az Azure-ból. Az e-mail tárgya legyen *\<yourdataprovider\@domain.com>* "Azure Data Share".
* Regisztrált [Microsoft.DataShare erőforrás-szolgáltató a következő](concepts-roles-permissions.md#resource-provider-registration) szolgáltatásokban:
    * Az Azure-előfizetés, amelyben létre fog hozni egy Data Share erőforrást.
    * Az Azure-előfizetés, amelyben a cél Azure-adattárai találhatók.

### <a name="prerequisites-for-a-target-storage-account"></a>Cél tárfiókra vonatkozó előfeltételek

* Egy Azure Storage-fiók. Ha még nem rendelkezik fiókkal, [hozzon létre egy fiókot.](../storage/common/storage-account-create.md) 
* Engedély a tárfiókba való íráshoz. Ez az engedély a *Microsoft.Storage/storageAccounts/write fájlban van* meg. Ez a Közreműködő szerepkör része. 
* Engedély szerepkör-hozzárendelés hozzáadására a tárfiókhoz. Ez a hozzárendelés a *Microsoft.Authorization/role assignments/write(Microsoft.Authorization/role assignments/write) stb.* Ez a Tulajdonos szerepkör része.  

### <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

### <a name="open-an-invitation"></a>Meghívó megnyitása

Meghívót e-mailben vagy közvetlenül a Azure Portal.

1. Ha e-mailből nyit meg egy meghívót, jelölje be a beérkezett üzenetben az adatszolgáltatótól kapott meghívót. A meghívás Microsoft Azure címe "Azure Data Share meghívás *\<yourdataprovider\@domain.com>* innen: ". Válassza **a Meghívás megtekintése lehetőséget** a meghívás azure-beli megtekintéséhez. 

   Ha a meghívást a Azure Portal megnyitni, keressen rá a *Data Share gombra.* Megjelenik a meghívók Data Share listája.

   ![Képernyőkép a meghívók listájáról a Azure Portal.](./media/invitations.png "Meghívók listája.") 

1. Válassza ki a megtekinteni kívánt megosztást. 

### <a name="accept-an-invitation"></a>Meghívó elfogadása
1. Tekintse át az összes mezőt, beleértve a **Használati feltételek.** Ha elfogadja a feltételeket, jelölje be a jelölőnégyzetet. 

   ![Képernyőkép a Használati feltételek területről.](./media/terms-of-use.png "Használati feltételek.") 

1. A **Cél Data Share fiók alatt** válassza ki azt az előfizetést és erőforráscsoportot, amelyben üzembe Data Share. Ezután töltse ki a következő mezőket:

   * Az Adat **share-fiók mezőben** válassza az **Új** létrehozása lehetőséget, ha még nem Data Share fiókkal. Ellenkező esetben válasszon ki egy meglévő Data Share, amely elfogadja az adat megosztását. 

   * A **Fogadott megosztás neve mezőben** hagyja meg az adatszolgáltató által megadott alapértelmezett nevet, vagy adjon meg egy új nevet a fogadott megosztásnak. 

1. Válassza az **Elfogadás és konfigurálás lehetőséget.** Létrejön egy megosztási előfizetés. 

   ![A konfigurációs beállítások elfogadásának helyét bemutató képernyőkép.](./media/accept-options.png "Beállítások elfogadása") 

    A fogadott megosztás megjelenik a Data Share fiókjában. 

    Ha nem szeretné elfogadni a meghívót, válassza az **Elutasítás lehetőséget.** 

### <a name="configure-a-received-share"></a>Fogadott megosztás konfigurálása
A szakasz lépéseit követve konfigurálhatja a helyet az adatok fogadására.

1. Az **Adatkészletek lapon** jelölje be annak az adatkészletnek a jelölőnégyzetét, ahol hozzá szeretné rendelni a célhelyet. **Céladattár kiválasztásához válassza** a Leképezés célként lehetőséget. 

   ![Képernyőkép a célhoz való leképezésről.](./media/dataset-map-target.png "Leképezés a célhoz.") 

1. Válassza ki az adatok céladattárát. A céladattárban lévő fájlok, amelyek elérési útja és neve megegyezik a fogadott adatok fájljaival, felül lesznek írva. 

   ![A cél tárfiók kiválasztásának helyét bemutató képernyőkép.](./media/map-target.png "Céltároló.") 

1. Pillanatkép-alapú megosztás esetén, ha az adatszolgáltató pillanatkép-ütemezést használ az adatok rendszeres frissítéséhez, az ütemezést a Pillanatkép-ütemezés **lapon engedélyezheti.** Jelölje be a pillanatkép-ütemezés melletti jelölőnégyzetet. Ezután válassza az **Engedélyezés lehetőséget.** Vegye figyelembe, hogy az első ütemezett pillanatkép az ütemezési időponttól számított egy percen belül elindul, a későbbi pillanatképek pedig az ütemezett időponttól számított másodpercen belül indulnak el.

   ![Képernyőkép a pillanatkép-ütemezés engedélyezéséről.](./media/enable-snapshot-schedule.png "Pillanatkép-ütemezés engedélyezése.")

### <a name="trigger-a-snapshot"></a>Pillanatkép aktiválása
Az ebben a szakaszban található lépések csak a pillanatkép-alapú megosztásra vonatkoznak.

1. Pillanatképet a Részletek lapon **aktiválhat.** A lapon válassza az Eseményindító **pillanatképe lehetőséget.** Választhat, hogy teljes pillanatképet vagy növekményes pillanatképet aktivál az adatokról. Ha első alkalommal fogad adatokat az adatszolgáltatótól, válassza a Teljes **másolás lehetőséget.** A pillanatképek végrehajtásakor a későbbi pillanatképek nem indulnak el, amíg az előző be nem fejeződik.

   ![Képernyőkép az eseményindító pillanatképének kiválasztásáról.](./media/trigger-snapshot.png "Eseményindító pillanatképe.") 

1. Ha az utolsó futtatás állapota *sikeres,* a fogadott adatok megtekintéséhez menjen a céladattárba. Válassza **az Adatkészletek** lehetőséget, majd a cél elérési útjának hivatkozását. 

   ![Képernyőkép egy fogyasztói adatkészlet-leképezésről.](./media/consumer-datasets.png "Fogyasztói adatkészlet-leképezés.") 

### <a name="view-history"></a>Előzmények megtekintése
A pillanatképek előzményeit csak pillanatkép-alapú megosztásban tudja megtekinteni. Az előzmények megtekintéséhez nyissa meg az **Előzmények** lapot. Itt láthatja az elmúlt 30 napban létrehozott összes pillanatkép előzményeit. 

## <a name="storage-snapshot-performance"></a>A Storage pillanatkép-teljesítménye
A tároló pillanatképének teljesítményét a fájlok száma és a megosztott adatok mérete mellett számos tényező befolyásolja. Mindig ajánlott saját teljesítményt tesztelni. Az alábbiakban néhány, a teljesítményt befolyásoló tényezőt mutatunk be.

* Egyidejű hozzáférés a forrás- és céladattárakhoz.  
* A forrás- és céladattárak helye. 
* Növekményes pillanatkép esetén a megosztott adatkészletben található fájlok száma befolyásolhatja az utolsó sikeres pillanatkép utáni utolsó módosítás időpontja után módosított fájlok listájának megkereséhez szükséges időt. 


## <a name="next-steps"></a>Következő lépések
Megtanulta, hogyan oszthat meg és fogadhat adatokat egy tárfiókból a Azure Data Share használatával. További információ a más adatforrások megosztásáról: [Támogatott adattárak.](supported-data-stores.md)
