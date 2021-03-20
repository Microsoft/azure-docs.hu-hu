---
title: Első lépések a Azure Data Lake Storage Gen1-Portalon
description: A Azure Portal használatával hozzon létre egy Data Lake Storage Gen1-fiókot, és hajtson végre alapszintű műveleteket a fiókban.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 428ed96c3223e644b0c78712723231a5fabbdc77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91578580"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>A Azure Data Lake Storage Gen1 használatának első lépései a Azure Portal

> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Megtudhatja, hogyan hozhat létre Data Lake Storage Gen1-fiókot a Azure Portal használatával, és hogyan végezhet el olyan alapvető műveleteket, mint például a mappák létrehozása, az adatfájlok feltöltése és letöltése, a fiók törlése stb. További információ: [Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1-fiók létrehozása

1. Jelentkezzen be az új [Azure Portalra](https://portal.azure.com).
2. Kattintson **az erőforrás létrehozása > Storage > Data Lake Storage Gen1** elemre.
3. Az **új Data Lake Storage Gen1** panelen adja meg az értékeket az alábbi képernyőfelvételen látható módon:

    ![Új Data Lake Storage Gen1 fiók létrehozása](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Új Data Lake Storage Gen1 fiók létrehozása")

   * **Név**. Adjon egyedi nevet a Data Lake Storage Gen1 fióknak.
   * **Előfizetés**. Válassza ki azt az előfizetést, amelybe új Data Lake Storage Gen1 fiókot szeretne létrehozni.
   * **Erőforráscsoport**. Válasszon ki egy meglévő erőforráscsoportot, vagy válassza a **Create new** (Új létrehozása) elemre, ha szeretne létrehozni egyet. Az erőforráscsoport egy tároló, amely alkalmazásokhoz kapcsolódó erőforrásokat tárol. További információk: [Erőforráscsoportok az Azure-ban](../azure-resource-manager/management/overview.md#resource-groups).
   * **Hely**: válassza ki azt a helyet, ahol létre szeretné hozni a Data Lake Storage Gen1 fiókot.
   * **Titkosítási beállítások**. Három beállítás érhető el:

     * **A titkosítás letiltása**.
     * **Data Lake Storage Gen1 által felügyelt kulcsok használata**, ha azt szeretné, hogy a Data Lake Storage Gen1 kezelje a titkosítási kulcsokat.
     * **Saját tulajdonú Key Vault-kulcsok használata**. Kiválaszthat egy meglévő Azure Key Vault-tárolót, vagy létrehozhat egy újat. Ha egy Key Vault kulcsait szeretné használni, akkor a Azure Key Vault eléréséhez engedélyeket kell rendelnie a Data Lake Storage Gen1 fiókhoz. Az utasításokért lásd [az engedélyek az Azure Key Vaulthoz történő hozzárendelését](#assign-permissions-to-azure-key-vault) ismertető cikket.

        ![Képernyőkép az új Data Lake Storage Gen 1 panelről és a titkosítási beállítások panelről.](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Titkosítás Data Lake Storage Gen1")

        Kattintson az **OK** gombra a **Titkosítási beállítások** panelen.

        További információ: az [adatok titkosítása Azure Data Lake Storage Gen1ban](./data-lake-store-encryption.md).

4. Kattintson a **Létrehozás** lehetőségre. Ha úgy döntött, hogy rögzíti a fiókot az irányítópulton, visszakerül az irányítópultra, és megtekintheti a Data Lake Storage Gen1 fiók üzembe helyezésének folyamatát. A Data Lake Storage Gen1 fiók üzembe helyezése után megjelenik a fiók panel.

## <a name="assign-permissions-to-azure-key-vault"></a><a name="assign-permissions-to-azure-key-vault"></a>Engedélyek hozzárendelése az Azure Key Vaulthoz

Ha egy Azure Key Vault kulcsait használta a titkosítás konfigurálásához a Data Lake Storage Gen1-fiókban, konfigurálnia kell a Data Lake Storage Gen1 fiók és a Azure Key Vault fiók közötti hozzáférést. Ehhez kövesse az alábbi lépéseket.

1. Ha a Azure Key Vault kulcsait használta, a Data Lake Storage Gen1 fiók paneljén egy figyelmeztetés jelenik meg a tetején. Kattintson a figyelmeztetésre a **Titkosítás** panel megnyitásához.

    ![Képernyőkép a Data Lake Storage Gen1 fiók panelről, amely a következő figyelmeztetést jeleníti meg: "Key Vault engedély konfigurálása szükséges. Kattintson ide a beállításhoz.](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Titkosítás Data Lake Storage Gen1")
2. A panelen két lehetőség szerepel a hozzáférés konfigurálásához.

    ![Képernyőkép a titkosítás panelről.](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Titkosítás Data Lake Storage Gen1")

   * Az első lehetőségnél kattintson az **Engedélyek megadása** gombra a hozzáférés konfigurálásához. Az első beállítás csak akkor engedélyezett, ha a Data Lake Storage Gen1 fiókot létrehozó felhasználó a Azure Key Vault is rendszergazdai jogosultságokkal rendelkezik.
   * A másik lehetőség a panelen megjelenő PowerShell parancsmag futtatása. Az Azure Key Vault tulajdonosának kell lennie, vagy képesnek kell lennie engedélyeket hozzárendelni az Azure Key Vaulton. A parancsmag futtatása után térjen vissza a panelre, és kattintson az **Engedélyezés** gombra a hozzáférés konfigurálásához.

> [!NOTE]
> Data Lake Storage Gen1 fiókot Azure Resource Manager sablonok használatával is létrehozhat. Ezek a sablonok az [Azure gyors üzembehelyezési sablonokból](https://azure.microsoft.com/resources/templates/?term=data+lake+store) érhetők el:
> * Adattitkosítás nélkül: [telepítse az adattitkosítás nélküli Azure Data Lake Storage Gen1 fiókot](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> * Data Lake Storage Gen1 használatával történő adattitkosítással: [Data Lake Storage Gen1 fiók telepítése titkosítással (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> * Azure Key Vault használatával történő adattitkosítással: [Data Lake Storage Gen1 fiók telepítése titkosítással (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
>
>

## <a name="create-folders"></a><a name="createfolder"></a>Mappák létrehozása

Az adatkezeléshez és az adattároláshoz a Data Lake Storage Gen1 fiók alatt hozhat létre mappákat.

1. Nyissa meg a létrehozott Data Lake Storage Gen1 fiókot. A bal oldali panelen kattintson a **minden erőforrás** lehetőségre, majd a **minden erőforrás** panelen kattintson arra a fiók nevére, amelyben mappákat kíván létrehozni. Ha rögzítette a fiókot a kezdőpulton, kattintson a fiók csempéjére.
2. A Data Lake Storage Gen1-fiók paneljén kattintson az **adatkezelő** elemre.

    ![Képernyőkép a Data Lake Storage Gen 1 fiók panelről, az adatkezelő lehetőségével.](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Mappák létrehozása Data Lake Storage Gen1 fiókban")
3. A Data Explorer (Adatkezelő) panelen kattintson a **New Folder** (Új mappa) elemre, adjon nevet az új mappának, majd kattintson az **OK** gombra.

    ![Képernyőkép a Adatkezelő panelről az új mappa lehetőséggel és az új mappa létrehozása szövegmezőből.](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Mappák létrehozása Data Lake Storage Gen1 fiókban")

    Az újonnan létrehozott mappa megjelenik az **Adatkezelő** panelen lévő listában. Beágyazott mappákat bármilyen szinten létrehozhat.

    ![Mappák létrehozása Data Lake fiókban](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Mappák létrehozása Data Lake fiókban")

## <a name="upload-data"></a><a name="uploaddata"></a>Adatok feltöltése

Az adatait közvetlenül a gyökér szintjén vagy a fiókon belül létrehozott mappába töltheti fel Data Lake Storage Gen1-fiókba.

1. A **Data Explorer** (Adatkezelő) panelen kattintson az **Upload** (Feltöltés) elemre.
2. Az **Upload files** (Fájlok feltöltése) panelen keresse meg a feltölteni kívánt fájlokat, majd kattintson az **Add selected files** (Kiválasztott fájlok hozzáadása) gombra. Egyszerre több fájlt is kijelölhet a feltöltéshez.

    ![Adatok feltöltése](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Adatok feltöltése")

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát.

## <a name="actions-available-on-the-stored-data"></a><a name="properties"></a>A tárolt adatokhoz elérhető műveletek

Kattintson a fájl melletti, három pontot ábrázoló ikonra, majd az előugró menüben kattintson arra a műveletre, amelyet el kíván végezni az adatokon.

![Az adatok tulajdonságai](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Az adatok tulajdonságai")

## <a name="secure-your-data"></a>Az adatok védelme

Azure Active Directory és hozzáférés-vezérlés (ACL-ek) használatával biztonságossá teheti a Data Lake Storage Gen1-fiókban tárolt adatait. Ennek módjával kapcsolatos útmutatásért lásd: az [adatvédelme Azure Data Lake Storage Gen1ban](data-lake-store-secure-data.md).

## <a name="delete-your-account"></a>Fiók törlése

Data Lake Storage Gen1 fiók törléséhez a Data Lake Storage Gen1 paneljén kattintson a **Törlés** elemre. A művelet megerősítéséhez a rendszer a törölni kívánt fiók nevének megadását kéri. Adja meg a fiók nevét, majd kattintson a **Delete** (Törlés) gombra.

![Data Lake Storage Gen1 fiók törlése](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Data Lake-fiók törlése")

## <a name="next-steps"></a>Következő lépések

* [Azure Data Lake Storage Gen1 használata big data követelményekhez](data-lake-store-data-scenarios.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
