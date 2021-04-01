---
title: A Storage Explorer használata az Azure File Storage szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan használhatja a fájlmegosztásokat és a fájlokat a Storage Explorer szolgáltatásban.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/09/2017
ms.author: cawa
ms.openlocfilehash: 84f6473c25a5be11eeda7cd2b311d93a7226a78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96488391"
---
# <a name="using-storage-explorer-with-azure-file-storage"></a>A Storage Explorer használata az Azure File Storage szolgáltatással

Az Azure File storage egy felhőalapú fájlmegosztást kínáló, SMB protokollt használó szolgáltatás. Az SMB 2.1 és az SMB 3.0 protokollt is támogatja. Az Azure File Storage szolgáltatással költséges újraírások nélkül, gyorsan megoldható a fájlmegosztásra támaszkodó, régi típusú alkalmazások áttelepítése az Azure-ra. A Blob Storage segítségével bárki számára nyilvánosan elérhetővé tehet adatokat, vagy privát módon tárolhat alkalmazásadatokat. Ez a cikk ismerteti, hogyan használhatja a fájlmegosztásokat és a fájlokat a Storage Explorer szolgáltatásban.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

- [A Storage Explorer letöltése és telepítése](https://www.storageexplorer.com/)

- [Kapcsolódás Azure Storage-fiókhoz vagy-szolgáltatáshoz](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

Minden fájlnak fájlmegosztásban kell lennie, amely egyszerűen egy logikai fájlcsoportot jelent. Egy fiók korlátlan számú fájlmegosztást tartalmazhat, egy adott megosztás pedig korlátlan számú fájl tárolására használható.

A következő lépések bemutatják, hogyan hozhat létre fájlmegosztást a Storage Explorerben.

1. Nyissa meg a Storage Explorert.

1. A bal oldali ablaktáblán bontsa ki a tárfiókot, ahol létre kívánja hozni a fájlmegosztást

1. Kattintson a jobb gombbal a **Fájlmegosztások** elemre, majd a helyi menüben válassza a **Fájlmegosztás létrehozása** lehetőséget.

    ![Fájlmegosztás létrehozása](media/vs-azure-tools-storage-explorer-files/image1.png)

1. A **Fájlmegosztások** mappa alatt megjelenik egy szövegbeviteli mező. Adja meg a fájlmegosztás nevét. A fájlmegosztások elnevezésére vonatkozó szabályokat és korlátozásokat a [Megosztáselnevezési szabályok](./storage/blobs/storage-quickstart-blobs-dotnet.md) című szakaszban olvashatja el.

    ![A megosztás elnevezése](media/vs-azure-tools-storage-explorer-files/image2.png)

1. A név megadása után nyomja le az **Enter** billentyűt az új fájlmegosztás létrehozásához, vagy az **Esc** billentyűt a művelet megszakításához. A sikeresen létrehozott fájlmegosztás megjelenik a kiválasztott tárfiókhoz tartozó **Fájlmegosztások** mappában.

    ![Az új megosztás](media/vs-azure-tools-storage-explorer-files/image3.png)

## <a name="view-a-file-shares-contents"></a>Fájlmegosztás tartalmának megtekintése

A fájlmegosztások fájlokat és mappákat tartalmaznak (a mappákban pedig további fájlok lehetnek).

A következő lépések bemutatják, hogyan tekintheti meg egy fájlmegosztás tartalmát a Storage Explorerben:+

1. Nyissa meg a Storage Explorert.

1. A bal oldali ablaktáblán bontsa ki a megtekinteni kívánt fájlmegosztást tartalmazó tárfiókot.

1. Bontsa ki a tárfiók **Fájlmegosztásait**.

1. Kattintson a jobb gombbal a megtekinteni kívánt fájlmegosztásra, és a helyi menüben válassza a **Megnyitás** lehetőséget. Vagy kattintson duplán a megtekinteni kívánt fájlmegosztásra.

    ![Megosztás megnyitása](media/vs-azure-tools-storage-explorer-files/image4.png)

1. A fájlmegosztás tartalma megjelenik a fő ablaktáblán.
    
    ![Képernyőfelvétel a megosztás fő paneljéről Storage Explorer a megosztás tartalmának megjelenítéséhez.](media/vs-azure-tools-storage-explorer-files/image5.png)

## <a name="delete-a-file-share"></a>Fájlmegosztás törlése

A fájlmegosztások szükség szerint egyszerűen létrehozhatók és törölhetők. (Az egyes fájlok törlésének módját lásd a [Fájlmegosztásban lévő fájlok kezelése](./vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) című szakaszban.)

A következő lépések bemutatják, hogyan törölhet fájlmegosztást a Storage Explorerben.

1. Nyissa meg a Storage Explorert.

1. A bal oldali ablaktáblán bontsa ki a megtekinteni kívánt fájlmegosztást tartalmazó tárfiókot.

1. Bontsa ki a tárfiók **Fájlmegosztásait**.

1. Kattintson a jobb gombbal a törölni kívánt fájlmegosztásra, és a helyi menüben válassza a **Törlés** lehetőséget. Az aktuálisan kijelölt fájlmegosztás a **Delete** billentyű lenyomásával is törölhető.

    ![Törlés](media/vs-azure-tools-storage-explorer-files/image6.png)

1. Válassza az **Igen** lehetőséget a megerősítési párbeszédpanelen.
    
    ![Megerősítési párbeszédpanel](media/vs-azure-tools-storage-explorer-files/image7.png)

## <a name="copy-a-file-share"></a>Fájlmegosztás másolása

A Storage Explorer lehetővé teszi egy fájlmegosztás vágólapra másolását, majd egy másik tárfiókba történő beillesztését. (Az egyes fájlok másolásának módját lásd a [Fájlmegosztásban lévő fájlok kezelése](./vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) című szakaszban.)

A következő lépések bemutatják, hogyan másolhat át fájlmegosztást egyik tárfiókból a másikba.

1. Nyissa meg a Storage Explorert.

1. A bal oldali ablaktáblán bontsa ki a másolni kívánt fájlmegosztást tartalmazó tárfiókot.

1. Bontsa ki a tárfiók **Fájlmegosztásait**.

1. Kattintson a jobb gombbal a másolni kívánt fájlmegosztásra, és a helyi menüben válassza a **Fájlmegosztás másolása** lehetőséget.

    ![Fájlmegosztás másolása](media/vs-azure-tools-storage-explorer-files/image8.png)

1. Kattintson a jobb gombbal a kívánt „cél” tárfiókra, amelybe a fájlmegosztást be kívánja illeszteni, majd válassza a helyi menüből a **Fájlmegosztás beillesztése** lehetőséget.

    ![Fájlmegosztás beillesztése](media/vs-azure-tools-storage-explorer-files/image9.png)

## <a name="get-the-sas-for-a-file-share"></a>SAS lekérése fájlmegosztáshoz

A [közös hozzáférésű jogosultságkód (SAS)](./storage/common/storage-sas-overview.md) delegált hozzáférést biztosít a tárfiókon lévő erőforrásokhoz. Ez azt jelenti, hogy egy adott időszakra megadhatja az ügyfeleknek a tárfiókban lévő objektumokra vonatkozó engedélyek bizonyos készletét a tár hozzáférési kulcsainak megosztása nélkül.

A következő lépések bemutatják, hogyan hozhat létre SAS-t egy fájlmegosztáshoz:+

1. Nyissa meg a Storage Explorert.

1. A bal oldali ablaktáblán bontsa ki azt a fájlmegosztást tartalmazó tárfiókot, amelyhez SAS-t kíván beszerezni.

1. Bontsa ki a tárfiók **Fájlmegosztásait**.

1. Kattintson a jobb gombbal a kívánt fájlmegosztásra, és válassza a helyi menüből a **Közös hozzáférésű jogosultságkód igénylése** lehetőséget.

    ![Közös hozzáférésű jogosultságkód igénylése](media/vs-azure-tools-storage-explorer-files/image10.png)

1. A **Közös hozzáférésű jogosultságkód** párbeszédpanelen adja meg a szabályzatot, a kezdési és a lejárati dátumokat, az időzónát és az erőforrás kívánt hozzáférési szintjeit.

    ![SAS párbeszédpanel](media/vs-azure-tools-storage-explorer-files/image11.png)

1. Az SAS-beállítások megadása után válassza a **Létrehozás** lehetőséget.

1. Ezután egy második **Közös hozzáférésű jogosultságkód** párbeszédpanel jelenik meg, amelyen fel vannak sorolva a fájlmegosztások, valamint a tárolási erőforrások eléréséhez használható URL-cím és a lekérdezési karakterlánc. Válassza a **Másolás** parancsot a vágólapra másolni kívánt URL-cím mellett.
    
    ![Második SAS párbeszédpanel](media/vs-azure-tools-storage-explorer-files/image12.png)

1. Ha elkészült, válassza a **Bezárás** lehetőséget.

## <a name="manage-access-policies-for-a-file-share"></a>Fájlmegosztás hozzáférési szabályzatainak kezelése

A következő lépések bemutatják, hogyan történik a fájlmegosztáshoz tartozó hozzáférési szabályzatok kezelése (hozzáadása és eltávolítása):+ . A hozzáférési szabályzatokkal olyan SAS URL-címek hozhatók létre, amelyeken keresztül a Storage-fájl erőforrás adott időtartamig hozzáférhető.

1. Nyissa meg a Storage Explorert.

1. A bal oldali ablaktáblán bontsa ki azt a fájlmegosztást tartalmazó tárfiókot, amelynek a hozzáférési szabályzatait kezelni kívánja.

1. Bontsa ki a tárfiók **Fájlmegosztásait**.

1. Jelölje ki a kívánt fájlmegosztást, és válassza a helyi menüből a **Hozzáférési szabályzatok kezelése** lehetőséget.

    ![Hozzáférési szabályzatok kezelése helyi menü](media/vs-azure-tools-storage-explorer-files/image13.png)

1. A **Hozzáférési szabályzatok** párbeszédpanelen fel lesznek sorolva a kiválasztott fájlmegosztáshoz már létrehozott hozzáférési szabályzatok.
    
    ![Hozzáférési szabályzatok](media/vs-azure-tools-storage-explorer-files/image14.png)

1. A hozzáférésiszabályzat-kezelési feladattól függően kövesse az alábbi lépéseket:
    
    - **Új hozzáférési szabályzat hozzáadása** – Válassza a **Hozzáadás** lehetőséget. A **Hozzáférési szabályzatok** megjeleníti az újonnan létrehozott és hozzáadott hozzáférési szabályzatot (az alapértelmezett beállításokkal).

    - **Hozzáférési szabályzat szerkesztése** – Hajtsa végre a kívánt módosításokat, majd válassza a **Mentés** lehetőséget.

    - **Hozzáférési szabályzat eltávolítása** – Válassza az **Eltávolítás** parancsot az eltávolítani kívánt hozzáférési szabályzat mellett.

1. Hozzon létre egy új SAS URL-címet a korábban létrehozott hozzáférési szabályzat használatával:
    
    ![SAS beszerzése](media/vs-azure-tools-storage-explorer-files/image15.png)
    
    ![Az SAS neve és tulajdonságai](media/vs-azure-tools-storage-explorer-files/image16.png)

## <a name="managing-files-in-a-file-share"></a>Fájlmegosztásban lévő fájlok kezelése

A fájlmegosztás létrehozása után többek között fájlokat tölthet fel a fájlmegosztásra, fájlokat tölthet le a helyi számítógépre, illetve fájlokat nyithat meg a helyi számítógépen.

A következő lépések bemutatják, hogyan kezelhetők a fájlok (és mappák) egy fájlmegosztásban.

1.  Nyissa meg a Storage Explorert.

1.  A bal oldali ablaktáblán bontsa ki a kezelni kívánt fájlmegosztást tartalmazó tárfiókot.

1.  Bontsa ki a tárfiók **Fájlmegosztásait**.

1.  Kattintson duplán a megtekinteni kívánt fájlmegosztásra.

1.  A fájlmegosztás tartalma megjelenik a fő ablaktáblán.

    ![Képernyőkép a Storage Explorer fájlmegosztás myazurefileshare fő paneljéről, amely a megosztás tartalmát mutatja az első kiválasztott mappával.](media/vs-azure-tools-storage-explorer-files/image17.png)

1.  A fájlmegosztás tartalma megjelenik a fő ablaktáblán.

1.  Kövesse az alábbi lépéseket a végrehajtani kívánt feladattól függően:

    - **Fájlok feltöltése egy fájlmegosztásba**

        a.  A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Fájlok feltöltése** lehetőséget.

        ![Fájlok feltöltése](media/vs-azure-tools-storage-explorer-files/image18.png)
        
        b. A **Fájlok feltöltése** párbeszédpanelen válassza a **Fájlok** szövegbeviteli mező jobb oldalán lévő, három pontot (**…**) ábrázoló gombot a feltölteni kívánt fájl(ok) kiválasztásához.

        ![Fájlok hozzáadása](media/vs-azure-tools-storage-explorer-files/image19.png)

        c. Válassza a **Feltöltés** lehetőséget.

    - **Mappa feltöltése egy fájlmegosztásba**
        
        a. A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Mappa feltöltése** lehetőséget.

        ![Mappa feltöltése menü](media/vs-azure-tools-storage-explorer-files/image20.png)

        b. A **Mappa feltöltése** párbeszédpanelen a **Mappa** szövegbeviteli mező jobb oldalán lévő, három pontot (**…**) ábrázoló gombbal válassza ki a mappát, amelynek a tartalmát fel kívánja tölteni.

        c. Igény szerint megadhat egy célmappát, amelybe a kiválasztott mappa tartalma fel lesz töltve. Ha a célmappa nem létezik, a rendszer létrehozza.

        d. Válassza a **Feltöltés** lehetőséget.

    - **Fájl letöltése a helyi számítógépre**
        
        a. Jelölje ki a letölteni kívánt fájlt.
        
        b. A fő ablaktábla eszköztárán válassza a **Letöltés** elemet.
        
        c. **A letöltött fájl helyének megadása** párbeszédpanelen adja meg, hogy hová kívánja letölteni a fájlt, és adja meg a fájl kívánt nevét.

        d. Kattintson a **Mentés** gombra.

    - **Fájl megnyitása a helyi számítógépen**
        
        a.  Jelölje ki a megnyitni kívánt fájlt.
        
        b.  A fő ablaktábla eszköztárán válassza a **Megnyitás** lehetőséget.
        
        c.  A rendszer letölti a fájlt, majd megnyitja a fájltípussal társított alkalmazással.

    - **Fájl másolása a vágólapra**

        a. Jelölje ki a másolni kívánt fájlt.

        b. A fő ablaktábla eszköztárán válassza a **Másolás** lehetőséget.

        c. A bal oldali ablaktáblán lépjen egy másik fájlmegosztásra, és kattintson rá duplán a fő ablaktáblán való megtekintéshez.

        d. A fő ablaktábla eszköztárán válassza a **Beillesztés** lehetőséget a fájl másolatának létrehozásához.

    - **Fájl törlése**

        a. Jelölje ki a törölni kívánt fájlt.

        b. A fő ablaktábla eszköztárán válassza a **Törlés** parancsot.

        c. Válassza az **Igen** lehetőséget a megerősítési párbeszédpanelen.

## <a name="next-steps"></a>Következő lépések

- A [Storage Explorer legújabb kibocsátási megjegyzéseinek és videóinak megtekintése](https://www.storageexplorer.com/).

- Annak megismerése, hogyan [hozhat létre alkalmazásokat Azure-blobok, -táblák, -üzenetsorok és -fájlok használatával](https://azure.microsoft.com/documentation/services/storage/).