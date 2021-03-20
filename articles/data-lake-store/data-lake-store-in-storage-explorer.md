---
title: Data Lake Storage Gen1 erőforrások kezelése – Azure Storage Explorer
description: Ismerje meg, hogyan érheti el és kezelheti Azure Data Lake Storage Gen1 adatait és erőforrásait Azure Storage Explorer
author: jejiang
ms.service: data-lake-store
ms.topic: how-to
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 7f251e6ba2d94c0fcede3387ac12461951de40f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92108745"
---
# <a name="manage-azure-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>Azure Data Lake Storage Gen1 erőforrások kezelése Storage Explorer használatával

[Azure Data Lake Storage Gen1](./data-lake-store-overview.md) a nagy mennyiségű strukturálatlan adat, például szöveg vagy bináris adat tárolására szolgáló szolgáltatás. HTTP-n vagy HTTPS-en keresztül bárhonnan hozzáférhet az adataihoz. A Azure Storage Explorer Data Lake Storage Gen1 lehetővé teszi a Data Lake Storage Gen1-és egyéb Azure-entitások, például a blobok és a várólisták elérését és kezelését. Mostantól ugyanazon eszközzel, egy helyen kezelheti a különböző Azure-entitásokat.

A másik előnye, hogy nem kell előfizetési engedéllyel rendelkeznie Data Lake Storage Gen1-adatkezeléshez. Storage Explorer a **helyi és a csatolt** csomóponthoz is csatlakoztathatja a Data Lake Storage Gen1 elérési utat, ha valaki megadja az engedélyt.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következő előfeltételekre lesz szüksége:

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial).
* Egy Data Lake Storage Gen1-fiók. A létrehozásával kapcsolatos útmutatásért tekintse meg a [Azure Data Lake Storage Gen1 első lépései](./data-lake-store-get-started-portal.md)című témakört.

## <a name="install-storage-explorer"></a>A Storage Explorer telepítése

Az Azure Storage Explorer legújabb összetevői a [termék weblapjáról](https://azure.microsoft.com/features/storage-explorer/) telepíthetők. A Windows-, Linux- és Mac-alapú verzió telepítése is támogatott.

## <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez

1. A Storage Explorerben kattintson a bal oldali beépülő modul ikonra.

   ![Beépülő modul ikonja](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)

1. Válassza az **Azure-fiók hozzáadása** elemet, majd kattintson a **Bejelentkezés** gombra.

   ![„Csatlakozás az Azure Storage-hoz” párbeszédpanel](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

1. A **Bejelentkezés a fiókba** párbeszédpanelen adja meg Azure-beli hitelesítő adatait.

    ![Azure-bejelentkezés párbeszédpanele](./media/data-lake-store-in-storage-explorer/sign-in.png)

1. Válassza ki az előfizetést a listából, majd kattintson az **Alkalmaz** gombra.

    ![Előfizetési információ és „Alkalmaz” gomb](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    Az **EXPLORER** panel frissül, és megjeleníti a kiválasztott előfizetéshez tartozó fiókokat.

    ![Fióklista](./media/data-lake-store-in-storage-explorer/account-list.png)

Sikeresen csatlakoztatta Data Lake Storage Gen1 az Azure-előfizetéséhez.

## <a name="connect-to-data-lake-storage-gen1"></a>Kapcsolódás Data Lake Storage Gen1hoz

Az előfizetésében nem szereplő erőforrásokhoz is hozzáférhet, ha valaki megadja az erőforrások URI-ját. Ezután a bejelentkezés után az URI használatával kapcsolódhat Data Lake Storage Gen1hoz.

1. Nyissa meg a Storage Explorert.
2. A bal oldali panelen bontsa ki a **Helyi és csatolt** elemet.
3. Kattintson a jobb gombbal a **Data Lake Store** elemre, és válassza a **Csatlakozás a Data Lake Store-hoz** lehetőséget.

      ![„Csatlakozás a Data Lake Store-hoz” a helyi menün](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Adja meg az URI-t. Az eszköz a megadott URL-címre lép.

      ![„Csatlakozás a Data Lake Store-hoz” párbeszédpanel, az URI beírására szolgáló szövegmezővel](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Data Lake Storage Gen1hoz való csatlakozás eredménye](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-a-data-lake-storage-gen1-accounts-contents"></a>Data Lake Storage Gen1 fiók tartalmának megtekintése

Egy Data Lake Storage Gen1 fiók erőforrásai tartalmazzák A mappákat és a fájlokat.

A következő lépések bemutatják, hogyan tekintheti meg Data Lake Storage Gen1 fiók tartalmát a Storage Exploreron belül:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki azt az előfizetést, amely a megtekinteni kívánt Data Lake Storage Gen1 fiókot tartalmazza.
3. Bontsa ki a **Data Lake Store** elemet.
4. Kattintson a jobb gombbal a megtekinteni kívánt Data Lake Storage Gen1 fiók csomópontra, majd válassza a **Megnyitás** lehetőséget. A megnyitásához kattintson duplán a Data Lake Storage Gen1 fiókra.

   A fő ablaktábla megjeleníti a Data Lake Storage Gen1 fiók tartalmát.

   ![Fő panel a mappák listájával](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png)

## <a name="manage-resources-in-data-lake-storage-gen1"></a>Erőforrások kezelése a Data Lake Storage Gen1ban

A következő műveletek végrehajtásával kezelheti Data Lake Storage Gen1 erőforrásait:

* Több Data Lake Storage Gen1 fiók között böngészhet Data Lake Storage Gen1 erőforrásokon.  
* Kapcsolati karakterlánc használata a Data Lake Storage Gen1 közvetlen csatlakoztatásához és kezeléséhez.
* A mások által a **helyi és csatolt ACL-** en keresztül megosztott erőforrások megtekintése Data Lake Storage Gen1.
* CRUD-műveletek végrehajtása fájlokon vagy mappákon: rekurzív mappák és több kijelölt fájl támogatása.
* Mappák húzása és hozzáadása a legutóbbi helyek gyors eléréséhez. Ez a művelet ugyanúgy működik, mint az asztali gépek fájlkezelőjében.
* Egy kattintással másolja és nyisson meg egy Data Lake Storage Gen1 hiperhivatkozást Storage Explorer.
* A tevékenységnapló megjelenítése a jobb alsó panelen a tevékenységek állapotának megtekintéséhez.
* Mappastatisztikák és fájltulajdonságok megjelenítése.

## <a name="manage-resources-in-azure-storage-explorer"></a>Erőforrások kezelése az Azure Storage Explorerben

Data Lake Storage Gen1 fiók létrehozása után a következőket teheti:

* Feltölthet mappákat és fájlokat, valamint letölthet és megnyithat erőforrásokat a helyi számítógépen.
* Elemeket rögzíthet a **gyorselérési eszköztáron**, új mappát hozhat létre, kimásolhatja az URL-címet, vagy kijelölhet mindent.
* Másolhat és beilleszthet, átnevezhet, törölhet, mappastatisztikákat tekinthet meg és frissíthet.

A következő elemek bemutatják, hogyan kezelheti az erőforrásokat egy Data Lake Storage Gen1-fiókon belül. Kövesse a végrehajtani kívánt művelet lépéseit.

### <a name="upload-files"></a>Fájlok feltöltése

1. A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Fájlok feltöltése** lehetőséget.

   ![„Fájlok feltöltése” menüelem](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png)

2. A **Feltölteni kívánt fájlok kiválasztása** párbeszédpanelen válassza ki a feltölteni kívánt fájlokat.

   ![Fájlfeltöltés párbeszédpanele](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

3. A feltöltés elindításához válassza a **Megnyitás** elemet.

### <a name="upload-a-folder"></a>Mappa feltöltése

1. A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Mappák feltöltése** lehetőséget.

   ![„Mappa feltöltése” menüelem](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png)

2. A **Feltölteni kívánt mappa kiválasztása** párbeszédpanelen válassza ki a feltölteni kívánt mappát. Kattintson a **Mappa kiválasztása** lehetőségre.

   ![Mappafeltöltés párbeszédpanele](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)

   Megkezdődik a feltöltés.

   ![Feltöltés folyamatban párbeszédpanel](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png)

> [!NOTE]
> A feltöltés elindításához közvetlenül át is húzhatja a mappákat és fájlokat a helyi számítógépen.

### <a name="download-folders-or-files-to-your-local-computer"></a>Mappák vagy fájlok letöltése a helyi számítógépre

1. Válassza ki a letölteni kívánt mappákat vagy fájlokat.
2. A fő ablaktábla eszköztárán válassza a **Letöltés** elemet.
3. A **Letöltött fájlok mentési mappájának kiválasztása** párbeszédpanelen adja meg a mappa helyét és nevét.
4. Kattintson a **Mentés** gombra.

### <a name="open-a-folder-or-file-from-your-local-computer"></a>Mappa vagy fájl megnyitása a helyi számítógépről

1. Válassza ki a megnyitni kívánt mappát vagy fájlt.
2. A fő ablaktábla eszköztárán válassza a **Megnyitás** lehetőséget. Vagy kattintson a jobb gombbal a kijelölt mappára vagy fájlra, és a helyi menüben kattintson a **Megnyitás** elemre.

A rendszer letölti a fájlt, majd megnyitja a fájltípushoz társított alkalmazással. Vagy megnyílik a fájl a fő panelen.

![Megnyitott fájl](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png)

### <a name="copy-folders-or-files-to-the-clipboard"></a>Mappák és fájlok másolása a vágólapra

1. Válassza ki a másolni kívánt mappákat vagy fájlokat.
2. A fő ablaktábla eszköztárán válassza a **Másolás** lehetőséget. Vagy kattintson a jobb gombbal a kijelölt mappákra vagy fájlokra, és a helyi menüben kattintson a **Másolás** elemre.
3. A bal oldali ablaktáblán tallózzon egy másik Data Lake Storage Gen1-fiókra, majd kattintson rá duplán a fő ablaktáblán való megjelenítéshez.
4. A fő panel eszköztárán válassza a **Beillesztés** lehetőséget egy másolat létrehozásához. Vagy kattintson a cél helyi menüjének **Beillesztés** elemére.

![Mappa másolására szolgáló kiválasztások](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

> [!NOTE]
> A tárolási típusok közötti másolási és beillesztési művelet nem támogatott. Másolhatja Data Lake Storage Gen1 mappákat vagy fájlokat, és beillesztheti őket egy másik Data Lake Storage Gen1-fiókba. De *nem* másolhat Data Lake Storage Gen1 mappákat vagy fájlokat, és beillesztheti őket az Azure Blob Storage-ba vagy fordítva.
>
> A másolási és beillesztési művelet során letölti a mappákat vagy fájlokat a helyi számítógépre, majd feltölti őket a célhelyre. Az eszköz *nem* a háttérben hajtja végre a műveletet. A nagyméretű fájlok másolása és beillesztése lassú folyamat. A nagy teljesítményű fájlmásolási és -áthelyezési funkció optimalizálása folyamatban van.

### <a name="delete-folders-or-files"></a>Mappák vagy fájlok törlése

1. Válassza ki a törölni kívánt mappákat vagy fájlokat.
2. A fő ablaktábla eszköztárán válassza a **Törlés** parancsot. Vagy kattintson a jobb gombbal a kijelölt mappákra vagy fájlokra, és a helyi menüben kattintson a **Törlés** elemre.
3. A megerősítési párbeszédpanelen válassza az **Igen** lehetőséget.

![Mappa törlésére szolgáló kiválasztások](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

### <a name="pin-to-quick-access"></a>Rögzítés gyorseléréshez

1. Válassza ki a rögzíteni kívánt mappát.
2. A fő panel eszköztárán válassza a **Rögzítés gyorseléréshez** elemet.

   A bal oldali panelen a kiválasztott mappát a rendszer hozzáadja a **gyorselérési** csomóponthoz.

   ![Mappa gyorseléréshez való rögzítésére szolgáló kiválasztások](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

Miután rögzített egy mappát a **gyorselérési** csomóponton, könnyen hozzáférhet az erőforrásokhoz.

### <a name="use-deep-links"></a>Mélyhivatkozások használata

Az URL-címeket egyszerűen beírhatja a Fájlkezelő vagy a böngésző címsorába. Ezután automatikusan elindul a Storage Explorer.exe, és megjeleníti az URL-cím helyét.

![Mélyhivatkozás a Fájlkezelőben](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)

## <a name="next-steps"></a>Következő lépések

* A [Storage Explorer legújabb kibocsátási megjegyzéseinek és videóinak megtekintése](https://www.storageexplorer.com).
* Megtudhatja, hogyan [kezelheti a Azure Cosmos db a Azure Storage Explorerban](../cosmos-db/storage-explorer.md).
* [A Storage Explorer első lépései](../vs-azure-tools-storage-manage-with-storage-explorer.md).
* [A Azure Data Lake Storage Gen1 első lépései](./data-lake-store-overview.md).
* Tekintse meg ezt a [YouTube-videót az Azure Cosmos DB az Azure Storage Explorerben történő használatáról](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).