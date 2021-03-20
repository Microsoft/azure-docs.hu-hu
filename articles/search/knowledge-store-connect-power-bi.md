---
title: Kapcsolódás a Power BIsal rendelkező Knowledge Store-hoz
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search Knowledge Store összekapcsolásával elemzést és feltárást Power BI.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 91e75b60f5324288c9f1adac59e31b9c1a1b0e9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89289171"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Tudástár összekötése Power BI

Ebből a cikkből megtudhatja, hogyan csatlakozhat a Knowledge Store-hoz, és hogyan deríthető fel a Power Query használatával a Power BI Desktop alkalmazásban. A sablonokkal gyorsabban elsajátíthatja az első lépéseket, vagy létrehozhat egy teljesen új irányítópultot. Ebből a rövid videóból megtudhatja, hogyan bővítheti az adatait az Azure Cognitive Search és az Power BI együttes használatával.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=593&end=663]



+ Kövesse a [Tudásbázis létrehozása a Azure Portalban](knowledge-store-create-portal.md) című témakör lépéseit, vagy [hozzon létre egy Azure Cognitive Search Knowledge Store](knowledge-store-create-rest.md) -t a REST használatával, és hozza létre az ebben az útmutatóban használt, a minta tudásbázist. Szüksége lesz a Knowledge Store létrehozásához használt Azure Storage-fiók nevére, valamint a Azure Portal elérési kulcsára.

+ [A Power BI Desktop telepítése](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Példa Power BI sablonra – csak Azure Portal

Ha [a Azure Portal használatával](knowledge-store-create-portal.md)hoz létre egy tudásbázist, lehetősége van egy [Power bi sablon](https://github.com/Azure-Samples/cognitive-search-templates) letöltésére az **adat importálása** varázsló második oldalán. Ez a sablon több vizualizációt, például WordCloud és hálózati Navigátort biztosít a szöveges tartalomhoz. 

Kattintson a **Beolvasás Power bi sablon** lehetőségre a **kognitív képességek hozzáadása** oldalon a sablon nyilvános GitHub-helyről való lekéréséhez és letöltéséhez. A varázsló módosítja a sablont úgy, hogy az megfeleljen az adatmennyiségnek, ahogy azt a varázslóban megadott Tudásbázis-prognózis rögzíti. Emiatt a letöltött sablon a varázsló minden egyes futtatásakor eltérő lesz, feltételezve, hogy különböző adatbevitelek és szaktudás-kiválasztás történik.

![Példa Azure Cognitive Search Power BI-sablonra](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Minta Power BI sablon")

> [!NOTE]
> Bár a sablon le van töltve, miközben a varázsló a közepén repül, meg kell várnia, amíg az áruház ténylegesen létre nem jön az Azure Table Storage-ban, mielőtt használni lehetne.

## <a name="connect-with-power-bi"></a>Kapcsolódás Power BI-jal

1. Indítsa el Power BI Desktop és kattintson **az adatlekérdezés** gombra.

1. Az **adatlekérdezés** ablakban válassza az **Azure** lehetőséget, majd válassza az **Azure Table Storage** elemet.

1. Kattintson a **Csatlakozás** gombra.

1. A **fiók neve vagy URL-címe** mezőben adja meg az Azure Storage-fiók nevét (a teljes URL-cím lesz létrehozva).

1. Ha a rendszer kéri, adja meg a Storage-fiók kulcsát.

1. Válassza ki azokat a táblákat, amelyek tartalmazzák az előző forgatókönyvek által létrehozott, a Hotel által áttekintett adatelemzési 

   + A portál útmutatójában a *hotelReviewsSsDocument*, a *hotelReviewsSsEntities*, a *hotelReviewsSsKeyPhrases* és a *hotelReviewsSsPages* tábla neve szerepel. 
   
   + A REST-útmutatóban a *hotelReviewsDocument*, a *hotelReviewsPages*, a *hotelReviewsKeyPhrases* és a *hotelReviewsSentiment* tábla neve szerepel.

1. Kattintson a **Betöltés** elemre.

1. A felső menüszalagon kattintson a **lekérdezések szerkesztése** elemre a **Power Query-szerkesztő** megnyitásához.

   ![Power Query megnyitása](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Power Query megnyitása")

1. Válassza a *hotelReviewsSsDocument* lehetőséget, majd távolítsa el a *PartitionKey*, a *RowKey* és az *időbélyegző* oszlopokat. 
   ![Táblák szerkesztése](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Táblák szerkesztése")

1. A *tartalom* kibontásához kattintson a táblázat jobb felső sarkában található, ellentétes nyíllal ellátott ikonra. Amikor megjelenik az oszlopok listája, válassza a minden oszlop lehetőséget, majd törölje a metaadatokkal kezdődő oszlopokat. A kijelölt oszlopok megjelenítéséhez kattintson **az OK** gombra.

   ![Tartalom kibontása](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Tartalom kibontása")

1. Módosítsa a következő oszlopok adattípusát az oszlop bal felső részén található ABC-123 ikonra kattintva.

   + A *Content. Latitude* és a *Content. hosszúság* esetében válassza a **decimális szám** lehetőséget.
   + *Content.reviews_date* és *Content.reviews_dateAdded* válassza a **dátum/idő** lehetőséget.

   ![Adattípusok módosítása](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Adattípusok módosítása")

1. Válassza a *hotelReviewsSsPages* lehetőséget, majd ismételje meg a 9. és a 10. lépést az oszlopok törléséhez és a *tartalom* kibontásához.
1. Módosítsa a *Content. SentimentScore* adattípus adattípusát **decimális számra**.
1. Válassza ki a *hotelReviewsSsKeyPhrases* , és ismételje meg a 9. és a 10. lépést az oszlopok törléséhez és a *tartalom* kibontásához. Ehhez a táblához nincs Adattípus-módosítás.

1. A parancssorban kattintson a **Bezárás és alkalmaz** gombra.

1. A bal oldali navigációs ablaktáblán kattintson a modell csempére, és ellenőrizze, hogy Power BI a három tábla közötti kapcsolatokat jeleníti-e meg.

   ![Kapcsolatok ellenőrzése](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Kapcsolatok ellenőrzése")

1. Kattintson duplán az egyes kapcsolatokra, és győződjön meg arról, hogy a **kereszt-szűrés iránya** **mindkettőre** van beállítva.  Ez lehetővé teszi, hogy a vizualizációk egy szűrő alkalmazása esetén frissüljenek.

1. A bal oldali navigációs panelen kattintson a jelentés csempére az adatvizualizációk használatával való ismerkedéshez. A szöveges mezőkhöz a táblázatok és a kártyák hasznos vizualizációk. A táblázat vagy kártya kitöltéséhez a három tábla mezői közül választhat. 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon.

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkből megtudhatja, hogyan derítheti fel ezt a Knowledge áruházat a Storage Explorer használatával.

> [!div class="nextstepaction"]
> [Megtekintés a Storage Explorerrel](knowledge-store-view-storage-explorer.md)