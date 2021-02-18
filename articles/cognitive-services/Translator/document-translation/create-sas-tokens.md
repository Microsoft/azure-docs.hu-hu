---
title: Megosztott hozzáférési aláírási (SAS-) token létrehozása tárolók és Blobok számára a Microsoft Storage Explorer
description: Megosztott hozzáférési jogkivonat (SAS) létrehozása tárolók és Blobok számára a Microsoft Storage Explorer és a Azure Portal
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 1425be4fc4e462185f9fee573dc87b04a68bfea4
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650745"
---
# <a name="create-sas-tokens-for-document-translation"></a>SAS-jogkivonatok létrehozása a dokumentumok fordításához

Ebből a cikkből megtudhatja, hogyan hozhat létre közös hozzáférésű aláírási (SAS-) jogkivonatokat a Azure Storage Explorer vagy a Azure Portal használatával. Az SAS-token biztonságos, delegált hozzáférést biztosít az Azure Storage-fiók erőforrásaihoz.

## <a name="create-sas-tokens-with-azure-storage-explorer"></a>SAS-tokenek létrehozása Azure Storage Explorer

### <a name="prerequisites"></a>Előfeltételek

* Szüksége lesz egy [**Azure Storage Explorer**](/azure/vs-azure-tools-storage-manage-with-storage-explorer) alkalmazásra, amelyet Windows, MacOS vagy Linux rendszerű fejlesztői környezetben kell telepíteni. A Azure Storage Explorer egy ingyenes eszköz, amely lehetővé teszi az Azure Cloud Storage-erőforrások egyszerű kezelését.
* A Azure Storage Explorer alkalmazás telepítését követően [kapcsolja össze azt a Storage-fiókot](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows#connect-to-a-storage-account-or-service) , amelyet a dokumentumok fordításához használ.

### <a name="create-your-tokens"></a>Jogkivonatok létrehozása

### <a name="sas-tokens-for-containers"></a>[SAS-jogkivonatok tárolók számára](#tab/Containers)

1. Nyissa meg a Azure Storage Explorer alkalmazást a helyi gépen, és navigáljon a csatlakoztatott **Storage-fiókokhoz**.
1. Bontsa ki a Storage-fiókok csomópontot, és válassza a **blob-tárolók** lehetőséget
1. Bontsa ki a blob-tárolók csomópontot, és kattintson a jobb gombbal a Storage- **tároló** csomópontra, vagy jelenítse meg a Beállítások menüt.
1. Válassza a **közös hozzáférési aláírás beolvasása...** lehetőséget a beállítások menüből.
1. A **megosztott hozzáférés aláírása** ablakban végezze el a következő beállításokat:
    * Válassza ki a **hozzáférési szabályzatot** (az alapértelmezett érték a none).
    * Az aláírt kulcs **kezdő** és **lejárati** dátumának és idejének meghatározása. Egy rövid élettartam ajánlott, mert a létrehozása után az SAS nem vonható vissza.
    * Válassza ki a kezdési és lejárati dátumhoz és időponthoz tartozó **időzónát** (az alapértelmezett érték a helyi).
    * Adja meg a tároló **engedélyeit** a megfelelő jelölőnégyzet bejelölésével és/vagy törlésével.
    * Tekintse át és válassza a **Létrehozás** lehetőséget.

1. Ekkor megjelenik egy új **ablak a tároló nevével** , **URI azonosítóval** és **lekérdezési karakterlánccal** .  
1. **Másolja és illessze be a tároló, az URI és a lekérdezési karakterlánc értékét egy biztonságos helyen. Csak egyszer jelennek meg, és az ablak bezárása után nem kérhetők le.**
1. SAS URL-cím létrehozásához fűzze hozzá az SAS-jogkivonatot (URI) a tárolási szolgáltatás URL-címéhez.

### <a name="sas-tokens-for-blobs"></a>[A Blobok SAS-jogkivonatai](#tab/blobs)

1. Nyissa meg a Azure Storage Explorer alkalmazást a helyi gépen, és navigáljon a csatlakoztatott **Storage-fiókokhoz**.
1. Bontsa ki a tárterület csomópontot, és válassza a **blob-tárolók** lehetőséget.
1. Bontsa ki a blob-tárolók csomópontot, és válassza ki a **tároló** csomópontot a főablak tartalmának megjelenítéséhez.
1. Válassza ki azt a blobot, amelyre az SAS-hozzáférést delegálni kívánja, majd kattintson a jobb gombbal a beállítások menü megjelenítéséhez.
1. Válassza a **közös hozzáférési aláírás beolvasása...** lehetőséget a beállítások menüből.
1. A **megosztott hozzáférés aláírása** ablakban végezze el a következő beállításokat:
    * Válassza ki a **hozzáférési szabályzatot** (az alapértelmezett érték a none).
    * Az aláírt kulcs **kezdő** és **lejárati** dátumának és idejének meghatározása. Egy rövid élettartam ajánlott, mert a létrehozása után az SAS nem vonható vissza.
    * Válassza ki a kezdési és lejárati dátumhoz és időponthoz tartozó **időzónát** (az alapértelmezett érték a helyi).
    * Adja meg a tároló **engedélyeit** a megfelelő jelölőnégyzet bejelölésével és/vagy törlésével.
    * Tekintse át és válassza a **Létrehozás** lehetőséget.
1. Ekkor megjelenik egy új ablak a blob **nevével** , **URI azonosítóval** és **lekérdezési karakterlánccal** .  
1. **Másolja és illessze be a blob, az URI és a lekérdezési karakterlánc értékét egy biztonságos helyen. Ezek csak egyszer lesznek megjelenítve, és az ablak bezárása után nem kérhetők le.**
1. SAS URL-cím létrehozásához fűzze hozzá az SAS-jogkivonatot (URI) a tárolási szolgáltatás URL-címéhez.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>A Blobok SAS-jogkivonatának létrehozása a Azure Portalban

> [!NOTE]
> Az SAS-jogkivonatok közvetlenül a Azure Portalban való létrehozása jelenleg nem támogatott. Létrehozhat azonban egy [**Azure Storage Explorer**](#create-sas-tokens-with-azure-storage-explorer) sas-tokent, vagy [programozott](/azure/storage/blobs/sas-service-create)módon végezheti el a feladatot.

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

* Aktív [**Azure-fiók**](https://azure.microsoft.com/free/cognitive-services/).  Ha még nem rendelkezik ilyennel, [**létrehozhat egy ingyenes fiókot**](https://azure.microsoft.com/free/).
* Egy [**Translator**](https://ms.portal.azure.com/#create/Microsoft) Service-erőforrás (**nem** Cognitive Services több szolgáltatásból álló erőforrás).  *Lásd:* [új Azure-erőforrás létrehozása](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).  
* Egy [**Azure Blob Storage-fiók**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Az Azure Storage-hoz való összes hozzáférés egy Storage-fiókon keresztül történik.

### <a name="create-your-tokens"></a>Jogkivonatok létrehozása

Lépjen a [Azure Portalra](https://ms.portal.azure.com/#home) , és navigáljon a következőképpen:  

 **A Storage** -fiók **→ tárolók →** **saját tároló** → **a blob**

1. Válassza az **sas előállítása** lehetőséget az oldal tetején található menüből.

1. Válassza az **aláírási módszer** → **felhasználói delegálási kulcs** elemet.

1. A megfelelő jelölőnégyzet bejelölésével és/vagy törlésével határozza meg az **engedélyeket** .

1. Az aláírt kulcs **indítási** és **lejárati** idejének meghatározása.

1. Az **engedélyezett IP-címek** mező nem kötelező, és meghatározza azt az IP-címet vagy IP-címtartományt, amelyből a kérelmeket el kell fogadni. Ha a kérés IP-címe nem egyezik meg az SAS-jogkivonatban megadott IP-címmel vagy címtartományból, nem lesz engedélyezve.

1. Az **engedélyezett protokollok** mező nem kötelező, és meghatározza a sas-vel való kérelemre engedélyezett protokollt. Az alapértelmezett érték a HTTPS.

1. Tekintse át **az SAS-jogkivonat és URL-cím előállítása** lehetőséget.

1. A **blob sas-jogkivonat** lekérdezési karakterlánca és a **blob sas URL-címe** az ablak alsó területén jelenik meg.  

1. **Másolja és illessze be a blob SAS-tokent és az URL-értékeket egy biztonságos helyen. Csak egyszer jelennek meg, és az ablak bezárása után nem kérhetők le.**

1. SAS URL-cím létrehozásához fűzze hozzá az SAS-jogkivonatot (URI) a tárolási szolgáltatás URL-címéhez.

## <a name="learn-more"></a>Tudjon meg többet

* [A blobok és a tárolók SAS-jogkivonatának létrehozása programozott módon](/azure/storage/blobs/sas-service-create)
* [Könyvtárra, tárolóra vagy blobra vonatkozó engedélyek](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a dokumentumok fordításával](get-started-with-document-translation.md)
>
>
