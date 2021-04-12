---
title: Az Azure Blob Storage-erőforrások kezelése Storage Explorerokkal | Microsoft Docs
description: Az Azure Blob Storage-erőforrások kezelése Storage Explorerokkal. BLOB-tárolót hozhat létre, megtekintheti a blob-tároló tartalmát, törölheti vagy másolhatja a BLOB-tárolót, és így tovább.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 48f00d964a87790b8d8c9b1d8eceaed26d15199e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95531967"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Azure Blob Storage-erőforrások kezelése Storage Explorer

## <a name="overview"></a>Áttekintés

Az [Azure Blob Storage](./storage/blobs/storage-quickstart-blobs-dotnet.md) egy olyan szolgáltatás, amely nagy mennyiségű strukturálatlan adat, például szöveges vagy bináris adatok tárolására szolgál, amelyek http-vagy https-kapcsolaton keresztül bárhonnan elérhetők a világ bármely pontján.
A Blob Storage segítségével bárki számára nyilvánosan elérhetővé tehet adatokat, vagy privát módon tárolhat alkalmazásadatokat. Ebből a cikkből megtudhatja, hogyan használható a Storage Explorer a blob-tárolók és a Blobok használatához.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* [A Storage Explorer letöltése és telepítése](https://www.storageexplorer.com)
* [Kapcsolódás Azure Storage-fiókhoz vagy-szolgáltatáshoz](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Blobtároló létrehozása

Minden blobnak egy blob-tárolóban kell lennie, amely egyszerűen a Blobok logikai csoportosítása. Egy fiók korlátlan számú tárolót tartalmazhat, és mindegyik tároló korlátlan számú blobot tud tárolni.

A következő lépések bemutatják, hogyan hozhat létre BLOB-tárolót Storage Exploreron belül.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki azt a Storage-fiókot, amelyen létre kívánja hozni a BLOB-tárolót.
3. Kattintson a jobb gombbal a **blob-tárolók** elemre, és a helyi menüből válassza a **blob-tároló létrehozása** lehetőséget.

   ![BLOB-tárolók létrehozása helyi menü][0]
4. A **blob-tárolók** mappa alatt egy szövegmező jelenik meg. Adja meg a blobtároló nevét. A blob-tárolók elnevezésével kapcsolatos szabályokról és korlátozásokról további információt a [tároló létrehozása](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) című témakörben talál.

   ![BLOB-tárolók létrehozása szövegmező][1]
5. Nyomja le az **ENTER** billentyűt, amikor elkészült a blob-tároló létrehozásához, vagy az **ESC billentyűt** a megszakításhoz. A blob-tároló sikeres létrehozása után a rendszer a kiválasztott Storage-fiók **blob containers** mappájában jelenik meg.

   ![BLOB-tároló létrehozva][2]

## <a name="view-a-blob-containers-contents"></a>BLOB-tároló tartalmának megtekintése

A blob-tárolók blobokat és mappákat tartalmaznak (amelyek tartalmazhatnak blobokat is).

A következő lépések bemutatják, hogyan tekintheti meg a blob-tárolók tartalmát Storage Explorer belül:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a megtekinteni kívánt BLOB-tárolót tartalmazó Storage-fiókot.
3. Bontsa ki a Storage-fiók **blob-tárolóit**.
4. Kattintson a jobb gombbal a megtekinteni kívánt blob-tárolóra, majd a helyi menüből válassza a **blob Container Editor megnyitása** lehetőséget.
   Azt is megteheti, hogy duplán kattint a megtekinteni kívánt blob-tárolóra.

   ![A blob Container Editor helyi menüjének megnyitása][19]
5. A fő panelen megjelenik a blob tároló tartalma.

   ![BLOB Container Editor][3]

## <a name="delete-a-blob-container"></a>BLOB-tároló törlése

A blob-tárolók szükség szerint egyszerűen létrehozhatók és törölhetők. (Az egyes Blobok törlésével kapcsolatban lásd a [blob-tárolóban található](#managing-blobs-in-a-blob-container)Blobok kezelése szakaszt.)

A következő lépések bemutatják, hogyan törölhet BLOB-tárolót Storage Exploreron belül:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a megtekinteni kívánt BLOB-tárolót tartalmazó Storage-fiókot.
3. Bontsa ki a Storage-fiók **blob-tárolóit**.
4. Kattintson a jobb gombbal a törölni kívánt blob-tárolóra, és a helyi menüből válassza a **Törlés** lehetőséget.
   A jelenleg kijelölt blob-tároló törléséhez a **delete** billentyűt is használhatja.

   ![BLOB-tároló helyi menüjének törlése][4]
5. Válassza az **Igen** lehetőséget a megerősítési párbeszédpanelen.

   ![BLOB-tároló törlésének jóváhagyása][5]

## <a name="copy-a-blob-container"></a>BLOB-tároló másolása

Storage Explorer lehetővé teszi a blob-tárolók vágólapra másolását, majd a blob-tároló egy másik Storage-fiókba való beillesztését. (Az egyes Blobok másolásával kapcsolatban tekintse meg a [blob-tárolóban található](#managing-blobs-in-a-blob-container)Blobok kezelése című szakaszt.)

A következő lépések bemutatják, hogyan másolhat BLOB-tárolót az egyik Storage-fiókból a másikba.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a másolni kívánt BLOB-tárolót tartalmazó Storage-fiókot.
3. Bontsa ki a Storage-fiók **blob-tárolóit**.
4. Kattintson a jobb gombbal a másolni kívánt blob-tárolóra, majd a helyi menüből válassza a **blob-tároló másolása** lehetőséget.

   ![BLOB-tároló helyi menüjének másolása][6]
5. Kattintson a jobb gombbal a kívánt "cél" Storage-fiókra, amelybe be kívánja illeszteni a BLOB-tárolót, majd a helyi menüből válassza a **blob-tároló beillesztése** lehetőséget.

   ![BLOB-tároló helyi menüjének beillesztése][7]

## <a name="get-the-sas-for-a-blob-container"></a>SAS lekérése blob tárolóhoz

A [közös hozzáférésű jogosultságkód (SAS)](./storage/common/storage-sas-overview.md) delegált hozzáférést biztosít a tárfiókon lévő erőforrásokhoz.
Ez azt jelenti, hogy egy adott időszakra megadhatja az ügyfeleknek a tárfiókban lévő objektumokra vonatkozó engedélyek bizonyos készletét a tár hozzáférési kulcsainak megosztása nélkül.

A következő lépések bemutatják, hogyan hozhatók létre SAS a blob-tárolóhoz:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki azt a Storage-fiókot, amely tartalmazza azt a BLOB-tárolót, amelyhez SAS-t szeretne kapni.
3. Bontsa ki a Storage-fiók **blob-tárolóit**.
4. Kattintson a jobb gombbal a kívánt blob-tárolóra, és a helyi menüből válassza a **megosztott hozzáférés aláírásának beolvasása** elemet.

   ![SAS helyi menü beolvasása][8]
5. A **Közös hozzáférésű jogosultságkód** párbeszédpanelen adja meg a szabályzatot, a kezdési és a lejárati dátumokat, az időzónát és az erőforrás kívánt hozzáférési szintjeit.

   ![SAS-beállítások beolvasása][9]
6. Az SAS-beállítások megadása után válassza a **Létrehozás** lehetőséget.
7. Ekkor megjelenik egy második **közös hozzáférési aláírás** párbeszédpanel, amely felsorolja a BLOB-tárolót, valamint a tárolási erőforrás eléréséhez használható URL-címet és QueryStrings.
   Válassza a **Másolás** parancsot a vágólapra másolni kívánt URL-cím mellett.

   ![SAS URL-címek másolása][10]
8. Ha elkészült, válassza a **Bezárás** lehetőséget.

## <a name="manage-access-policies-for-a-blob-container"></a>BLOB-tároló hozzáférési házirendjeinek kezelése

A következő lépések bemutatják, hogyan kezelhetők a blob-tárolóhoz tartozó hozzáférési szabályzatok (Hozzáadás és eltávolítás):

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki azt a Storage-fiókot, amely a kezelni kívánt hozzáférési házirendeket tartalmazó BLOB-tárolót tartalmazza.
3. Bontsa ki a Storage-fiók **blob-tárolóit**.
4. Válassza ki a kívánt BLOB-tárolót, és a helyi menüből válassza a **hozzáférési házirendek kezelése** lehetőséget.

   ![Hozzáférési szabályzatok kezelése helyi menü][11]
5. A **hozzáférési házirendek** párbeszédpanel felsorolja a kiválasztott blob-tárolóhoz már létrehozott hozzáférési házirendeket.

   ![Hozzáférési szabályzat beállításai][12]
6. A hozzáférésiszabályzat-kezelési feladattól függően kövesse az alábbi lépéseket:

   * **Új hozzáférési szabályzat hozzáadása** – Válassza a **Hozzáadás** lehetőséget. A **Hozzáférési szabályzatok** megjeleníti az újonnan létrehozott és hozzáadott hozzáférési szabályzatot (az alapértelmezett beállításokkal).
   * **Hozzáférési szabályzat szerkesztése** – végezze el a kívánt módosításokat, majd kattintson a **Mentés** gombra.
   * **Hozzáférési szabályzat eltávolítása** – Válassza az **Eltávolítás** parancsot az eltávolítani kívánt hozzáférési szabályzat mellett.

## <a name="set-the-public-access-level-for-a-blob-container"></a>BLOB-tároló nyilvános hozzáférési szintjének beállítása

Alapértelmezés szerint minden blob-tároló "nincs nyilvános hozzáférés" értékre van állítva.

A következő lépések bemutatják, hogyan lehet nyilvános hozzáférési szintet megadni egy blob-tárolóhoz.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki azt a Storage-fiókot, amely a kezelni kívánt hozzáférési házirendeket tartalmazó BLOB-tárolót tartalmazza.
3. Bontsa ki a Storage-fiók **blob-tárolóit**.
4. Válassza ki a kívánt BLOB-tárolót, és a helyi menüből válassza a **nyilvános hozzáférési szint beállítása** lehetőséget.

   ![Nyilvános hozzáférési szint helyi menüjének beállítása][13]
5. A **tároló nyilvános hozzáférési szintjének beállítása** párbeszédpanelen adja meg a kívánt hozzáférési szintet.

   ![Nyilvános hozzáférési szint beállításainak megadása][14]
6. Kattintson az **Alkalmaz** gombra.

## <a name="managing-blobs-in-a-blob-container"></a>Blobok kezelése blob-tárolóban

Miután létrehozott egy BLOB-tárolót, feltöltheti a blobot a blob-tárolóba, letöltheti a blobot a helyi számítógépre, megnyithatja a blobot a helyi számítógépen, és még sok más.

A következő lépések bemutatják, hogyan kezelhetők a Blobok (és mappák) a blob-tárolón belül.

1. Nyissa meg a Storage Explorert.
2. A bal oldali panelen bontsa ki a kezelni kívánt blobtárolót tartalmazó tárfiókot.
3. Bontsa ki a Storage-fiók **blob-tárolóit**.
4. Kattintson duplán a megtekinteni kívánt blob-tárolóra.
5. A fő panelen megjelenik a blob tároló tartalma.

   ![BLOB-tároló megtekintése][3]
6. A fő panelen megjelenik a blob tároló tartalma.
7. Kövesse az alábbi lépéseket a végrehajtani kívánt feladattól függően:

   * **Fájlok feltöltése blob-tárolóba**

     1. A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Fájlok feltöltése** lehetőséget.

        ![Fájlok feltöltése menü][15]
     2. A **Fájlok feltöltése** párbeszédpanelen válassza a **Fájlok** szövegbeviteli mező jobb oldalán lévő, három pontot (**…**) ábrázoló gombot a feltölteni kívánt fájl(ok) kiválasztásához.

        ![Fájlok feltöltése lehetőség][16]
     3. Adja meg a **blob típusát**. További információt [a tároló létrehozása](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) című témakörben talál.
     4. Szükség esetén megadhat egy célmappát, amelybe a kiválasztott fájl (ok) fel lesz töltve. Ha a célmappa nem létezik, a rendszer létrehozza.
     5. Válassza a **Feltöltés** lehetőséget.
   * **Mappa feltöltése blob-tárolóba**

     1. A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Mappa feltöltése** lehetőséget.

        ![Mappa feltöltése menü][17]
     2. A **Mappa feltöltése** párbeszédpanelen a **Mappa** szövegbeviteli mező jobb oldalán lévő, három pontot (**…**) ábrázoló gombbal válassza ki a mappát, amelynek a tartalmát fel kívánja tölteni.

        ![Mappa beállításainak feltöltése][18]
     3. Adja meg a **blob típusát**. További információt [a tároló létrehozása](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) című témakörben talál.
     4. Igény szerint megadhat egy célmappát, amelybe a kiválasztott mappa tartalma fel lesz töltve. Ha a célmappa nem létezik, a rendszer létrehozza.
     5. Válassza a **Feltöltés** lehetőséget.
   * **BLOB letöltése a helyi számítógépre**

     1. Válassza ki a letölteni kívánt blobot.
     2. A fő ablaktábla eszköztárán válassza a **Letöltés** elemet.
     3. A **letöltött blob mentési** helyének megadása párbeszédpanelen adja meg azt a helyet, ahol a blobot le szeretné tölteni, majd adja meg a kívánt nevet.  
     4. Kattintson a **Mentés** gombra.
   * **BLOB megnyitása a helyi számítógépen**

     1. Válassza ki a megnyitni kívánt blobot.
     2. A fő ablaktábla eszköztárán válassza a **Megnyitás** lehetőséget.
     3. A blobot a rendszer a blob alapjául szolgáló fájltípushoz társított alkalmazás használatával tölti le és nyitja meg.
   * **BLOB másolása a vágólapra**

     1. Válassza ki a másolni kívánt blobot.
     2. A fő ablaktábla eszköztárán válassza a **Másolás** lehetőséget.
     3. A bal oldali ablaktáblán navigáljon egy másik blob-tárolóhoz, majd kattintson rá duplán a fő ablaktáblán való megjelenítéshez.
     4. A fő ablaktábla eszköztárán válassza a **Beillesztés** lehetőséget a blob másolatának létrehozásához.
   * **Blob törlése**

     1. Válassza ki a törölni kívánt blobot.
     2. A fő ablaktábla eszköztárán válassza a **Törlés** parancsot.
     3. Válassza az **Igen** lehetőséget a megerősítési párbeszédpanelen.

## <a name="next-steps"></a>Következő lépések

* A [Storage Explorer legújabb kibocsátási megjegyzéseinek és videóinak megtekintése](https://www.storageexplorer.com).
* Annak megismerése, hogyan [hozhat létre alkalmazásokat Azure-blobok, -táblák, -üzenetsorok és -fájlok használatával](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png