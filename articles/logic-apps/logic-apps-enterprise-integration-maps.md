---
title: XML átalakítása XSLT-leképezésekkel
description: XSLT-leképezések hozzáadása az XML átalakításához Azure Logic Appsban Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: 62c3d4533dd04dbb5a2ce0c73afa52b81d433913
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91570780"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-átalakítás térképekkel az Azure Logic Appsben az Enterprise Integration Pack segítségével

Az Azure Logic Apps vállalati integrációs forgatókönyveiben a logikai alkalmazás leképezéseket, pontosabban Extensible Stylesheet Language Transformations- (XSLT-) leképezéseket használhat az XML-adatok formátumok közötti átviteléhez. A leképezés egy XML-dokumentum, amely leírja, hogyan lehet más formátumba konvertálni az XML-dokumentumok adatait. 

Tegyük fel például, hogy rendszeresen kap B2B-rendeléseket vagy-számlákat a YYYMMDD-formátumot használó ügyféltől. A szervezet azonban a MMDDYYY formátumot használja. Megadhatja és használhatja azt a térképet, amely átalakítja a YYYMMDD a MMDDYYY formátumba, mielőtt a rendelés vagy a számla részleteit a felhasználói tevékenység adatbázisában tárolja.

Az integrációs fiókokkal és összetevőkkel, például a térképekkel kapcsolatos korlátokat lásd: [Azure Logic apps korlátozásai és konfigurációs adatai](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , amelyben a Maps és egyéb összetevők a vállalati integrációs és a vállalatközi (B2B) megoldások számára is tárolhatók.

* Ha a Térkép egy külső szerelvényre hivatkozik, akkor *a szerelvényt és a térképet* is fel kell töltenie az integrációs fiókjába. [*Először töltse fel a szerelvényt*](#add-assembly), majd töltse fel a szerelvényre hivatkozó térképet.

  Ha a szerelvény mérete 2 MB vagy kisebb, *közvetlenül* a Azure Portal adhatja hozzá a szerelvényt az integrációs fiókhoz. Ha azonban a szerelvény vagy a Térkép 2 MB-nál nagyobb, de nem nagyobb, mint a [szerelvények vagy a térképek mérete](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), a következő lehetőségek közül választhat:

  * Szerelvények esetén szüksége van egy Azure Blob-tárolóra, ahol feltöltheti a szerelvényt és a tároló helyét. Így később is megadhatja ezt a helyet, amikor hozzáadja a szerelvényt az integrációs fiókhoz. 
  Ehhez a feladathoz a következő elemek szükségesek:

    | Elem | Leírás |
    |------|-------------|
    | [Azure Storage-fiók](../storage/common/storage-account-overview.md) | Ebben a fiókban hozzon létre egy Azure BLOB-tárolót a szerelvényhez. Megtudhatja [, hogyan hozhat létre egy Storage-fiókot](../storage/common/storage-account-create.md). |
    | Blobtároló | Ebben a tárolóban feltöltheti a szerelvényt. Erre a tárolóra is szüksége lesz, amikor hozzáadja a szerelvényt az integrációs fiókhoz. Megtudhatja, hogyan [hozhat létre BLOB-tárolót](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Ezzel az eszközzel könnyebben kezelhetők a Storage-fiókok és a blob-tárolók. Storage Explorer használatához [töltse le és telepítse a Azure Storage Explorert](https://www.storageexplorer.com/). Ezután csatlakoztasson Storage Explorer a Storage-fiókjához a [Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)első lépéseinek lépéseit követve. További információ: gyors útmutató [: blob létrehozása az objektum-tárolóban Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)használatával. <p>Vagy a Azure Portalban keresse meg és válassza ki a Storage-fiókját. A Storage-fiók menüjében válassza a **Storage Explorer** lehetőséget. |
    |||

  * A Maps esetében jelenleg nagyobb leképezéseket adhat hozzá a [Azure Logic Apps REST API-Maps](/rest/api/logic/maps/createorupdate)használatával.

Térképek létrehozásakor és hozzáadásakor nincs szükség logikai alkalmazásra. A Térkép használatához azonban a logikai alkalmazásnak olyan integrációs fiókhoz kell kapcsolódnia, ahol a térképet tárolja. Útmutató [logikai alkalmazások integrációs fiókokhoz való összekapcsolásához](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Ha még nem rendelkezik logikai alkalmazással, Ismerje meg, [hogyan hozhat létre logikai alkalmazásokat](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Hivatkozott szerelvények hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. Az integrációs fiók megkereséséhez és megnyitásához az Azure főmenüjében válassza a **minden szolgáltatás** lehetőséget. 
   A keresőmezőbe írja be az "integrációs fiók" kifejezést. 
   Válassza az **integrációs fiókok** elemet.

   ![Integrációs fiók keresése](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Válassza ki azt az integrációs fiókot, amelyhez hozzá szeretné adni a szerelvényt, például:

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Az integrációs fiók **áttekintése** oldal **összetevők** területén válassza a **szerelvények** csempét.

   !["Szerelvények" kiválasztása](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. A **szerelvények** lap megnyitása után válassza a **Hozzáadás** lehetőséget.

   ![Képernyőfelvétel: a szerelvények lapon található Hozzáadás gomb kiemelése.](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

A szerelvény fájljának mérete alapján hajtsa végre az olyan szerelvények feltöltésének lépéseit, amelyek akár [2](#smaller-assembly) MB vagy [több mint 2 MB méretűek, de legfeljebb 8 MB](#larger-assembly)-ra.
A szerelvények mennyiségi korlátozásai az integrációs fiókokban: [Azure Logic apps korlátai és konfigurálása](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Ha módosítja a szerelvényt, akkor a térképet is frissítenie kell, hogy a Térkép módosult-e.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Szerelvények hozzáadása 2 MB-ig

1. A **szerelvény hozzáadása** területen adja meg a szerelvény nevét. **Kis fájlok** kiválasztásának megtartása. A **szerelvény** mező mellett válassza a mappa ikont. Keresse meg és válassza ki a feltölteni kívánt szerelvényt, például:

   ![Kisebb szerelvény feltöltése](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   A szerelvény **neve** tulajdonságban a szerelvény fájljának neve automatikusan megjelenik a szerelvény kiválasztása után.

1. Ha elkészült, kattintson **az OK gombra**.

   Miután a szerelvény-fájl feltöltése befejeződött, a szerelvény megjelenik a **szerelvények** listájában.

   ![Feltöltött szerelvények listája](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Az integrációs fiók **áttekintő** lapjának **összetevők** területén a **szerelvények** csempe most a feltöltött szerelvények számát jeleníti meg, például:

   ![Feltöltött szerelvények](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>2 MB-nál nagyobb szerelvények hozzáadása

Nagyobb szerelvények hozzáadásához feltöltheti a szerelvényt egy Azure Blob-tárolóba az Azure Storage-fiókban. A szerelvények hozzáadásának lépései eltérnek attól függően, hogy a blob-tároló nyilvános olvasási hozzáféréssel rendelkezik-e. Először is győződjön meg arról, hogy a blob-tároló nyilvános olvasási hozzáféréssel rendelkezik-e a következő lépésekkel: [nyilvános hozzáférési szint beállítása blob-tárolóhoz](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Tároló hozzáférési szintjének keresése

1. Nyissa meg Azure Storage Explorer. Az Explorer ablakban bontsa ki az Azure-előfizetését, ha még nincs kibontva.

1. Bontsa ki a **Storage-fiókok** > {*a-Storage-Account*} > **blob-tárolók** elemet. Válassza ki a BLOB-tárolót.

1. A blob-tároló helyi menüjében válassza a **nyilvános hozzáférési szint beállítása** lehetőséget.

   * Ha a blob-tároló legalább nyilvános hozzáféréssel rendelkezik, válassza a **Mégse** lehetőséget, majd kövesse az alábbi lépéseket ezen az oldalon: [feltöltés a nyilvános hozzáféréssel rendelkező tárolók számára](#public-access-assemblies)

     ![Nyilvános hozzáférés](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Ha a blob-tároló nem rendelkezik nyilvános hozzáféréssel, válassza a **Mégse** lehetőséget, majd kövesse az alábbi lépéseket az oldalon: [feltöltés nyilvános hozzáférés nélküli tárolókban](#no-public-access-assemblies)

     ![Nincs nyilvános hozzáférés](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Nyilvános hozzáféréssel rendelkező tárolók feltöltése

1. Töltse fel a szerelvényt a Storage-fiókjába. 
   A jobb oldali ablakban válassza a **feltöltés** lehetőséget.

1. A feltöltés befejezése után válassza ki a feltöltött szerelvényt. Az eszköztáron válassza az **URL másolása** lehetőséget, hogy a szerelvény URL-címét másolja.

1. Térjen vissza a Azure Portal, ahol a **szerelvény hozzáadása** panel meg van nyitva. 
   Adja meg a szerelvény nevét. 
   Válassza a **nagyméretű fájl (2 MB-nál nagyobb)** lehetőséget.

   A **tartalom URI-ja** mező most megjelenik, nem pedig a **szerelvény** mező.

1. A **tartalom URI-ja** mezőbe illessze be a szerelvény URL-címét. 
   Fejezze be a szerelvény hozzáadását.

Miután a szerelvény befejezte a feltöltést, a séma megjelenik a **szerelvények** listájában.
Az integrációs fiók **áttekintő** lapjának **összetevők** területén a **szerelvények** csempe most a feltöltött szerelvények számát jeleníti meg.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Nyilvános hozzáférés nélküli tárolók feltöltése

1. Töltse fel a szerelvényt a Storage-fiókjába. 
   A jobb oldali ablakban válassza a **feltöltés** lehetőséget.

1. A feltöltés befejezése után létrehoz egy közös hozzáférési aláírást (SAS) a szerelvényhez. 
   A szerelvény helyi menüjében válassza a **közös hozzáférési aláírás beolvasása** elemet.

1. A **közös hozzáférési aláírás** ablaktáblán válassza a **tároló szintű közös hozzáférésű aláírás-URI**  >  **létrehozása** lehetőséget. 
   A SAS URL-cím generálása után az **URL-cím** mező mellett válassza a **Másolás** lehetőséget.

1. Térjen vissza a Azure Portal, ahol a **szerelvény hozzáadása** panel meg van nyitva. 
   Adja meg a szerelvény nevét. 
   Válassza a **nagyméretű fájl (2 MB-nál nagyobb)** lehetőséget.

   A **tartalom URI-ja** mező most megjelenik, nem pedig a **szerelvény** mező.

1. A **tartalom URI-ja** mezőben illessze be a korábban létrehozott sas URI-t. Fejezze be a szerelvény hozzáadását.

A szerelvény feltöltésének befejezése után a szerelvény megjelenik a **sémák** listájában. Az integrációs fiók **áttekintő** lapjának **összetevők** területén a **szerelvények** csempe most a feltöltött szerelvények számát jeleníti meg.

## <a name="create-maps"></a>Térképek létrehozása

Térképként használható XSLT-dokumentum létrehozásához használhatja a Visual Studio 2015-et a BizTalk integrációs projekt létrehozásához a [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)használatával. Ebben a projektben létrehozhat egy integrációs leképezési fájlt, amely lehetővé teszi, hogy az elemeket vizuálisan képezze két XML-sémafájl között. A projekt létrehozása után egy XSLT-dokumentumot kap.
Az integrációs fiókokban lévő leképezési mennyiségek korlátozásai: [Azure Logic apps korlátozásai és konfigurálása](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Térképek hozzáadása

A térképre hivatkozó szerelvények feltöltése után már feltöltheti a térképet.

1. Ha még nem jelentkezett be, jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure-fiókja hitelesítő adataival. 

1. Ha az integrációs fiók még nincs megnyitva, az Azure fő menüjében válassza a **minden szolgáltatás** lehetőséget. 
   A keresőmezőbe írja be az "integrációs fiók" kifejezést. 
   Válassza az **integrációs fiókok** elemet.

   ![Integrációs fiók keresése](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Válassza ki azt az integrációs fiókot, amelyhez hozzá szeretné adni a térképet, például:

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Az integrációs fiók **áttekintése** oldal **összetevők** területén válassza a **Maps (térképek** ) csempét.

   ![Válassza a "Maps" lehetőséget.](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. A **térképek** lap megnyitása után válassza a **Hozzáadás** lehetőséget.

   ![Válassza a "Hozzáadás" lehetőséget.](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Térképek hozzáadása legfeljebb 2 MB

1. A **Térkép hozzáadása** területen adja meg a Térkép nevét. 

1. A **Térkép típusa** területen válassza ki a típust, például: **Liquid**, **XSLT**, **XSLT 2,0** vagy **XSLT 3,0**.

1. **Kis fájlok** kiválasztásának megtartása. A **Térkép** mező mellett válassza a mappa ikont. Keresse meg és válassza ki a feltölteni kívánt térképet, például:

   ![Térkép feltöltése](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Ha üresen hagyta a **Name (név** ) tulajdonságot, a leképezés fájljának neve automatikusan megjelenik a tulajdonságban, miután kiválasztja a térkép fájlt. 
   Azonban bármilyen egyedi nevet használhat.

1. Ha elkészült, kattintson **az OK gombra**. 
   Miután a leképezési fájl befejezte a feltöltést, a Térkép megjelenik a **Maps** listán.

   ![Feltöltött térképek listája](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Az integrációs fiók **áttekintő** lapjának **összetevők** területén a **Maps (térképek** ) csempén a feltöltött térképek száma látható, például:

   ![Feltöltött térképek](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Térképek hozzáadása 2 MB-nál több

Jelenleg nagyobb térképek hozzáadásához használja a [Azure Logic Apps REST API-Maps](/rest/api/logic/maps/createorupdate)szolgáltatást.

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Térképek szerkesztése

Egy meglévő Térkép frissítéséhez fel kell töltenie egy új leképezési fájlt, amely a kívánt módosításokkal rendelkezik. Először azonban letöltheti a meglévő térképet szerkesztésre.

1. A [Azure Portalban](https://portal.azure.com)keresse meg és nyissa meg az integrációs fiókot, ha még nincs megnyitva.

1. Az Azure fő menüjében válassza a **minden szolgáltatás** lehetőséget. A keresőmezőbe írja be az "integrációs fiók" kifejezést. Válassza az **integrációs fiókok** elemet.

1. Válassza ki azt az integrációs fiókot, ahol frissíteni szeretné a térképet.

1. Az integrációs fiók **áttekintése** oldal **összetevők** területén válassza a **Maps (térképek** ) csempét.

1. A **térképek** lap megnyitása után válassza ki a térképet. 
   Ha először szeretné letölteni és szerkeszteni a térképet, válassza a **Letöltés** lehetőséget, és mentse a térképet.

1. Amikor készen áll a frissített Térkép feltöltésére, a **Maps** lapon válassza ki a frissíteni kívánt térképet, és válassza a **frissítés** lehetőséget.

1. Keresse meg és válassza ki a frissített térképet, amelyet fel szeretne tölteni. 
   A leképezési fájl feltöltésének befejeződése után a frissített Térkép megjelenik a **Maps** listában.

## <a name="delete-maps"></a>Térképek törlése

1. A [Azure Portalban](https://portal.azure.com)keresse meg és nyissa meg az integrációs fiókot, ha még nincs megnyitva.

1. Az Azure fő menüjében válassza a **minden szolgáltatás** lehetőséget. 
   A keresőmezőbe írja be az "integrációs fiók" kifejezést. 
   Válassza az **integrációs fiókok** elemet.

1. Válassza ki azt az integrációs fiókot, ahová a térképet törölni szeretné.

1. Az integrációs fiók **áttekintése** oldal **összetevők** területén válassza a **Maps (térképek** ) csempét.

1. A **térképek** lap megnyitása után válassza ki a térképet, és válassza a **Törlés** lehetőséget.

1. Annak megerősítéséhez, hogy törölni kívánja a térképet, válassza az **Igen** lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [További információ a Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [További információ a sémákkal kapcsolatban](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [További információ az átalakításokról](../logic-apps/logic-apps-enterprise-integration-transform.md)
