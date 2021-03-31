---
title: XML érvényesítése sémákkal
description: Sémák hozzáadása a Azure Logic Appsban lévő XML-dokumentumok érvényesítéséhez Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ade29318fdd1510d03ddd208534258a30b256e98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75979376"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Az XML érvényességének ellenőrzése sémákkal az Azure Logic Appsben az Enterprise Integration Pack segítségével

Annak ellenőrzéséhez, hogy a dokumentumok érvényes XML-t használnak-e, és hogy a várt adatok a Azure Logic Apps vállalati integrációs forgatókönyvek esetében előre meghatározott formátumban vannak-e, a logikai alkalmazás sémákat használhat. A sémák azt is ellenőrizhetik, hogy a Logic apps Exchange a vállalatközi (B2B) helyzetekben van-e.

Az integrációs fiókokhoz és összetevőkhöz (például sémák) kapcsolódó korlátokat a [Azure Logic apps korlátozásai és konfigurációs adatai](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)című részben találja.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , amelyben a sémákat és egyéb összetevőket a vállalati integrációs és a vállalatközi (B2B) megoldások számára tárolja. 

  Ha a séma [mérete 2 MB vagy kisebb](#smaller-schema), közvetlenül a Azure Portal adhatja hozzá a sémát az integrációs fiókhoz. Ha azonban a séma mérete meghaladja a 2 MB-ot, de a [séma méretétől](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)nem nagyobb, akkor feltöltheti a sémát egy Azure Storage-fiókba. 
  Ha hozzá szeretné adni a sémát az integrációs fiókhoz, akkor az integrációs fiókjából is hivatkozhat a Storage-fiókjára. 
  Ehhez a feladathoz az alábbi elemek szükségesek: 

  * [Azure Storage-fiók](../storage/common/storage-account-overview.md) , amelyben létrehoz egy BLOB-tárolót a sémához. Megtudhatja, hogyan [hozhat létre egy Storage-fiókot](../storage/common/storage-account-create.md). 

  * A séma tárolására szolgáló blob-tároló. Megtudhatja, hogyan [hozhat létre BLOB-tárolót](../storage/blobs/storage-quickstart-blobs-portal.md). 
  A tároló tartalmi URI-JÁT később, a séma integrációs fiókjához való hozzáadásakor kell megadnia.

  * [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md), amely a Storage-fiókok és a blob-tárolók kezeléséhez használható. 
  A Storage Explorer használatához válassza az alábbi lehetőségek egyikét:
  
    * A Azure Portal keresse meg és válassza ki a Storage-fiókját. 
    A Storage-fiók menüjében válassza a **Storage Explorer** lehetőséget.

    * Az asztali verziónál [töltse le és telepítse a Azure Storage Explorer](https://www.storageexplorer.com/). 
    Ezután csatlakoztasson Storage Explorer a Storage-fiókjához a [Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)első lépéseinek lépéseit követve. 
    További információ: gyors útmutató [: blob létrehozása az objektum-tárolóban Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)használatával.

Sémák létrehozásakor és hozzáadásakor nincs szükség logikai alkalmazásra. Ha azonban sémát szeretne használni, a logikai alkalmazásnak egy integrációs fiókhoz kell kapcsolódnia, ahol a sémát tárolja. Útmutató [logikai alkalmazások integrációs fiókokhoz való összekapcsolásához](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Ha még nem rendelkezik logikai alkalmazással, Ismerje meg, [hogyan hozhat létre logikai alkalmazásokat](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Sémák felvétele

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a> az Azure-fiókja hitelesítő adataival.

1. Az integrációs fiók megkereséséhez és megnyitásához az Azure főmenüjében válassza a **minden szolgáltatás** lehetőséget. A keresőmezőbe írja be az "integrációs fiók" kifejezést. Válassza az **integrációs fiókok** elemet.

   ![Integrációs fiók keresése](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Válassza ki azt az integrációs fiókot, amelyhez hozzá szeretné adni a sémát, például:

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Az integrációs fiók **áttekintése** oldal **összetevők** területén válassza a **sémák** csempét.

   ![Válassza a "sémák" lehetőséget.](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. A **sémák** lap megnyitása után válassza a **Hozzáadás** lehetőséget.

   ![Válassza a "Hozzáadás" lehetőséget.](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

A séma (. xsd) fájljának mérete alapján végezze el a [2](#smaller-schema) MB-nál több vagy 2 MB-nál nagyobb méretű sémák feltöltésének lépéseit [, akár 8 MB-ot is](#larger-schema)megadhat.

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Sémák hozzáadása 2 MB-ig

1. A **séma hozzáadása** területen adja meg a séma nevét. 
   **Kis fájlok** kiválasztásának megtartása. A **séma** mező mellett válassza a mappa ikont. Keresse meg és válassza ki a feltölteni kívánt sémát, például:

   ![Kisebb séma feltöltése](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Ha elkészült, kattintson **az OK gombra**.

   Miután a séma befejezte a feltöltést, a séma megjelenik a **sémák** listájában.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Sémák hozzáadása 2 MB-nál több

Nagyobb sémák hozzáadásához feltöltheti a sémát egy Azure Blob-tárolóba az Azure Storage-fiókjában. A sémák hozzáadásának lépései eltérőek lehetnek attól függően, hogy a blob-tároló nyilvános olvasási hozzáféréssel rendelkezik-e. Először is győződjön meg arról, hogy a blob-tároló nyilvános olvasási hozzáféréssel rendelkezik-e a következő lépésekkel: [nyilvános hozzáférési szint beállítása blob-tárolóhoz](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Tároló hozzáférési szintjének keresése

1. Nyissa meg Azure Storage Explorer. Az Explorer ablakban bontsa ki az Azure-előfizetését, ha még nincs kibontva.

1. Bontsa ki a **Storage-fiókok** > {*a-Storage-Account*} > **blob-tárolók** elemet. Válassza ki a BLOB-tárolót.

1. A blob-tároló helyi menüjében válassza a **nyilvános hozzáférési szint beállítása** lehetőséget.

   * Ha a blob-tároló legalább nyilvános hozzáféréssel rendelkezik, válassza a **Mégse** lehetőséget, majd kövesse az alábbi lépéseket ezen az oldalon: [feltöltés a nyilvános hozzáféréssel rendelkező tárolók számára](#public-access)

     ![Nyilvános hozzáférés](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Ha a blob-tároló nem rendelkezik nyilvános hozzáféréssel, válassza a **Mégse** lehetőséget, majd kövesse az alábbi lépéseket az oldalon: [feltöltés nyilvános hozzáférés nélküli tárolókban](#public-access)

     ![Nincs nyilvános hozzáférés](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Nyilvános hozzáféréssel rendelkező tárolók feltöltése

1. Töltse fel a sémát a Storage-fiókjába. 
   A jobb oldali ablakban válassza a **feltöltés** lehetőséget.

1. A feltöltés befejezése után válassza ki a feltöltött sémát. Az eszköztáron válassza az **URL másolása** lehetőséget, hogy a séma URL-címét másolja.

1. Térjen vissza a Azure Portal, ahol a **séma hozzáadása** panel meg van nyitva. 
   Adja meg a szerelvény nevét. 
   Válassza a **nagyméretű fájl (2 MB-nál nagyobb)** lehetőséget. 

   A **tartalom URI-ja** mező ekkor megjelenik a **séma** mező helyett.

1. A **tartalom URI-ja** mezőbe illessze be a séma URL-címét. 
   Fejezze be a séma hozzáadását.

Miután a séma befejezte a feltöltést, a séma megjelenik a **sémák** listájában. Az integrációs fiók **áttekintő** lapjának **összetevők** területén a **sémák** csempe most a feltöltött sémák számát jeleníti meg.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Nyilvános hozzáférés nélküli tárolók feltöltése

1. Töltse fel a sémát a Storage-fiókjába. 
   A jobb oldali ablakban válassza a **feltöltés** lehetőséget.

1. A feltöltés befejezése után létrehoz egy közös hozzáférési aláírást (SAS) a sémához. 
   A séma helyi menüjében válassza a **közös hozzáférési aláírás beolvasása** elemet.

1. A **közös hozzáférési aláírás** ablaktáblán válassza a **tároló szintű közös hozzáférésű aláírás-URI**  >  **létrehozása** lehetőséget. 
   A SAS URL-cím generálása után az **URL-cím** mező mellett válassza a **Másolás** lehetőséget.

1. Térjen vissza a Azure Portal, ahol a **séma hozzáadása** panel meg van nyitva. Válassza a **nagyméretű fájl** lehetőséget.

   A **tartalom URI-ja** mező ekkor megjelenik a **séma** mező helyett.

1. A **tartalom URI-ja** mezőben illessze be a korábban létrehozott sas URI-t. Fejezze be a séma hozzáadását.

Miután a séma befejezte a feltöltést, a séma megjelenik a **sémák** listájában. Az integrációs fiók **áttekintő** lapjának **összetevők** területén a **sémák** csempe most a feltöltött sémák számát jeleníti meg.

## <a name="edit-schemas"></a>Sémák szerkesztése

Egy meglévő séma frissítéséhez fel kell töltenie egy új, a kívánt módosításokat tartalmazó sémafájl-fájlt. Először azonban le is töltheti a meglévő sémát szerkesztésre.

1. A <a href="https://portal.azure.com" target="_blank">Azure Portalban</a>keresse meg és nyissa meg az integrációs fiókot, ha még nincs megnyitva.

1. Az Azure fő menüjében válassza a **minden szolgáltatás** lehetőséget. 
   A keresőmezőbe írja be az "integrációs fiók" kifejezést. 
   Válassza az **integrációs fiókok** elemet.

1. Válassza ki azt az integrációs fiókot, ahol frissíteni szeretné a sémát.

1. Az integrációs fiók **áttekintése** oldal **összetevők** területén válassza a **sémák** csempét.

1. A **sémák** lap megnyitása után válassza ki a sémát. 
   Először a séma letöltéséhez és szerkesztéséhez válassza a **Letöltés** lehetőséget, majd mentse a sémát.

1. Amikor készen áll a frissített séma feltöltésére, a **sémák** lapon válassza ki a frissíteni kívánt sémát, és válassza a **frissítés** lehetőséget.

1. Keresse meg és válassza ki a frissített sémát, amelyet fel szeretne tölteni. 
   Miután a sémafájl befejezte a feltöltést, **a sémák listájában** megjelenik a frissített séma.

## <a name="delete-schemas"></a>Sémák törlése

1. A <a href="https://portal.azure.com" target="_blank">Azure Portalban</a>keresse meg és nyissa meg az integrációs fiókot, ha még nincs megnyitva.

1. Az Azure fő menüjében válassza a **minden szolgáltatás** lehetőséget. 
   A keresőmezőbe írja be az "integrációs fiók" kifejezést. 
   Válassza az **integrációs fiókok** elemet.

1. Válassza ki azt az integrációs fiókot, ahol törölni szeretné a sémát.

1. Az integrációs fiók **áttekintése** oldal **összetevők** területén válassza a **sémák** csempét.

1. A **sémák** lap megnyitása után válassza ki a sémát, és válassza a **Törlés** lehetőséget.

1. Annak megerősítéséhez, hogy törölni kívánja a sémát, válassza az **Igen** lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [További információ a Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
* [További információ a mapsről](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [További információ az átalakításokról](../logic-apps/logic-apps-enterprise-integration-transform.md)
