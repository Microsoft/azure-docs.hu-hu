---
title: Hozzon létre egy Knowledge Store-t a Azure Portal
titleSuffix: Azure Cognitive Search
description: Az adatimportálás varázsló használatával hozzon létre egy, a dúsított tartalom megőrzésére szolgáló tudásbázist. Kapcsolódjon a Knowledge Store-hoz más alkalmazásokból származó elemzéshez, vagy a dúsított tartalmat küldje el az alsóbb rétegbeli folyamatoknak.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/17/2020
ms.openlocfilehash: 3225013f09abd326c619b67caf77918889a64859
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94741807"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Gyors útmutató: Azure Cognitive Search Knowledge Store létrehozása a Azure Portal

A Knowledge Store az Azure Cognitive Search szolgáltatása, amely egy tartalom-feldolgozási folyamat kimenetét őrzi meg a későbbi elemzések vagy az alsóbb rétegbeli feldolgozás céljából. 

Egy folyamat nem strukturált szöveg-és képtartalomot fogad el, alkalmazza a mesterséges intelligenciát Cognitive Services (például OCR és természetes nyelvi feldolgozás), és az új struktúrákat és információkat ad vissza, amelyek korábban nem léteztek. A folyamat által létrehozott fizikai összetevők egyike egy [Tudásbázis](knowledge-store-concept-intro.md), amelyet az eszközökön keresztül érhet el, és elemezheti a tartalmakat.

Ebben a rövid útmutatóban a szolgáltatásait és adatait az Azure-felhőben egyesítve hozhat létre egy tudásbázist. Ha minden megtörtént, a portálon futtathatja az **adatimportálás** varázslót, hogy az összeset együtt kell lekérnie. A végeredmény az eredeti szöveges tartalom, valamint a mesterséges intelligenciával létrehozott tartalom, amelyet megtekintheti a portálon ([Storage Explorer](knowledge-store-view-storage-explorer.md)).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).

+ Egy Azure Cognitive Search szolgáltatás. [Hozzon létre egy szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat. 

+ Egy Azure Storage-fiók [blob Storage](../storage/blobs/index.yml)-val.

> [!NOTE]
> Ez a rövid útmutató az AI-hoz készült [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) is használja. Mivel a számítási feladatok olyan kicsik, Cognitive Services a jelenetek mögött, akár 20 tranzakcióra is felhasználható. Ez azt jelenti, hogy ezt a gyakorlatot anélkül végezheti el, hogy további Cognitive Services erőforrást kellene létrehoznia.

## <a name="set-up-your-data"></a>Az adatai beállítása

A következő lépésekben hozzon létre egy BLOB-tárolót az Azure Storage-ban a heterogén tartalmú fájlok tárolásához.

1. [HotelReviews_Free.csvletöltése ](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Ezek az adatok egy CSV-fájlban (Kaggle.com-ből származó) tárolt adatok, amelyek egy adott szállodával kapcsolatban 19 darab ügyfél-visszajelzést tartalmaznak. 

1. [Hozzon létre egy Azure Storage-fiókot](../storage/common/storage-account-create.md?tabs=azure-portal) , vagy [keressen egy meglévő fiókot](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) a jelenlegi előfizetése alatt. Az Azure Storage-t fogja használni mind az importálandó nyers tartalomhoz, mind a Tudásbázis végeredményéhez.

   + Válassza ki a **StorageV2 (általános célú v2)** fiók típusát.

1. Nyissa meg a blob Services lapjait, és hozzon létre egy " *Hotel-Reviews*" nevű tárolót.

1. Kattintson a **Feltöltés** gombra.

    ![Adatok feltöltése](media/knowledge-store-create-portal/upload-command-bar.png "A szállodai értékelések feltöltése")

1. Válassza ki az első lépésben letöltött **HotelReviews-Free.csv** fájlt.

    ![Az Azure Blob-tároló létrehozása](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Az Azure Blob-tároló létrehozása")

1. A blob Storage-lapok bezárása előtt a bal oldali navigációs ablaktáblán található hivatkozást használva nyissa meg a **hozzáférési kulcsok** lapot. A blob Storage-ból származó adatok lekérésére szolgáló kapcsolódási karakterlánc beolvasása. A kapcsolódási karakterlánc az alábbi példához hasonlóan néz ki: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Most már készen áll az **adatimportálás** varázsló áthelyezésére.

## <a name="run-the-import-data-wizard"></a>Az adatimportálás varázsló futtatása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.

1. [Keresse meg a keresési szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) , és az Áttekintés lapon kattintson az **adat importálása** parancsra a parancssorban, és hozzon létre egy tudásbázist négy lépésben.

   ![Adatok importálása parancs](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása

1. A **Kapcsolódás az adataihoz** lapon válassza az **Azure Blob Storage** lehetőséget, majd válassza ki a létrehozott fiókot és tárolót. 

1. A **név** mezőbe írja be a következőt: `hotel-reviews-ds` .

1. **Elemzési mód** esetén válassza a **tagolt szöveg** lehetőséget, majd jelölje be az **első sor fejléce** jelölőnégyzetet. Győződjön meg arról, hogy az elválasztó **karakter** egy vessző (,).

1. A **kapcsolati karakterláncban** illessze be az Azure Storage **hozzáférési kulcsok** lapjáról másolt kapcsolati karakterláncot.

1. A **tárolók** mezőben adja meg az adattárolási blob-tároló nevét.

    Az oldalnak az alábbi képernyőképhez hasonlóan kell kinéznie.

    ![Adatforrás-objektum létrehozása](media/knowledge-store-create-portal/hotel-reviews-ds.png "Adatforrás-objektum létrehozása")

1. Folytassa a következő oldallal.

### <a name="step-2-add-cognitive-skills"></a>2. lépés: Kognitív képességek hozzáadása

A varázsló lépésében egy készségkészlet hoz létre a kognitív képességek gazdagítása révén. A forrásadatok több nyelven is különböző felhasználói felülvizsgálatokból állnak. Az ehhez az adatkészlethez kapcsolódó készségek közé tartozik a fő kifejezés kinyerése, a hangulat észlelése és a szöveg fordítása. Egy későbbi lépésben ezeket a bővítéseket az Azure Tables (Tudásbázisban) a Knowledge Store-ba fogjuk felépíteni.

1. Bontsa ki a **csatolás Cognitive Services** elemet. Alapértelmezés szerint az **ingyenes (korlátozott alkoholtartalom-növelés)** beállítás van kiválasztva. Ezt az erőforrást akkor használhatja, ha a HotelReviews-Free.csvban lévő rekordok száma 19, és ez az ingyenes erőforrás naponta legfeljebb 20 tranzakciót tesz lehetővé.

1. Bontsa ki a bővítések **hozzáadása** elemet.

1. A **készségkészlet neve** mezőbe írja be a következőt: `hotel-reviews-ss` .

1. A **forrásadatok mezőben** válassza a **reviews_text** lehetőséget.

1. A **dúsítás részletességi szintjének** kiválasztásához válassza a **lapok (5000 karakteres tömbök) elemet.**

1. Válassza ki ezeket a kognitív képességeket:
    + **A legfontosabb kifejezések kinyerése**
    + **Szöveg lefordítása**
    + **Érzelmek észlelése**

      ![Képességcsoport létrehozása](media/knowledge-store-create-portal/hotel-reviews-ss.png "Képességcsoport létrehozása")

1. Bontsa ki a bővítések **mentése a Knowledge Store-** ba lehetőséget.

1. Válassza ki ezeket az **Azure Table-kivetítéseket**:
    + **Dokumentumok**
    + **Oldalak**
    + **Legfontosabb kifejezések**

1. Adja meg az előző lépésben mentett **Storage-fiókhoz tartozó kapcsolatok sztringjét** .

    ![A Knowledge Store konfigurálása](media/knowledge-store-create-portal/hotel-reviews-ks.png "A Knowledge Store konfigurálása")

1. Szükség esetén letöltheti Power BI sablont. Amikor a varázslóból fér hozzá a sablonhoz, a helyi. pbit fájl alkalmazkodik az adatok alakjának megjelenítéséhez.

1. Folytassa a következő oldallal.

### <a name="step-3-configure-the-index"></a>3. lépés: Az index konfigurálása

Ebben a varázslóban a választható teljes szöveges keresési lekérdezések indexét kell konfigurálnia. A varázsló felveszi az adatforrást a mezők és adattípusok lekötésére. Csak ki kell választania a kívánt viselkedés attribútumait. A lekérdezhető **attribútum például** lehetővé teszi, hogy a Search szolgáltatás a mező értékét visszaállítsa, miközben a **kereshetőség** lehetővé teszi a teljes szöveges keresést a mezőben.

1. Az **index neve** mezőbe írja be a következőt: `hotel-reviews-idx` .

1. Attribútumok esetében fogadja el az alapértelmezett beállításokat  : lekérdezhető és **kereshető** a folyamat által létrehozott új mezőkhöz.

    Az indexnek az alábbi képhez hasonlóan kell kinéznie. Mivel a lista hosszú, nem minden mező látható a képen.

    ![Index konfigurálása](media/knowledge-store-create-portal/hotel-reviews-idx.png "Index konfigurálása")

1. Folytassa a következő oldallal.

### <a name="step-4-configure-the-indexer"></a>4. lépés: Az indexelő konfigurálása

A varázsló lépésében olyan indexelő fog konfigurálni, amely összefogja az adatforrást, a készségkészlet és a varázsló előző lépéseiben megadott indexet.

1. A **név** mezőbe írja be a következőt: `hotel-reviews-idxr` .

1. Az **ütemterv** **beállításnál** tartsa meg az alapértelmezett értéket.

1. Az indexelő futtatásához kattintson a **Submit (elküldés** ) gombra. Az adatgyűjtés, az indexelés, a kognitív képességek alkalmazása mindez ebben a lépésben történik.

## <a name="monitor-status"></a>Figyelő állapota

A kognitív képességek indexelése hosszabb időt vesz igénybe, mint a szokásos szöveges indexelés. A varázslónak meg kell nyitnia az indexelő listát az Áttekintés oldalon, hogy nyomon követhesse a folyamat előrehaladását. Az önnavigációhoz lépjen az Áttekintés lapra, és kattintson az **Indexelő** elemre.

A Azure Portal az értesítési tevékenység naplóját is figyelheti egy kattintható **Azure Cognitive Search értesítési** állapot hivatkozására. A végrehajtás több percet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések

Most, hogy bővítette az adatait a Cognitive Services segítségével, és kivetítette az eredményeket egy tudásbázisba, Storage Explorer vagy Power BI használatával tárja fel a dúsított adatkészletet.

A tartalmat megtekintheti Storage Explorerban, vagy a Power BI további lépéseit követve betekintést nyerhet a vizualizációk segítségével.

> [!div class="nextstepaction"]
> [Megtekintés Storage Explorer](knowledge-store-view-storage-explorer.md) 
>  [Kapcsolat Power bi](knowledge-store-connect-power-bi.md)

> [!Tip]
> Ha szeretné megismételni ezt a gyakorlatot, vagy próbáljon meg egy másik AI-bővítési bemutatót használni, törölje a *Hotel-Reviews-idxr* indexelő. Az indexelő törlése visszaállítja az ingyenes napi tranzakció számlálóját a Cognitive Services feldolgozáshoz.
