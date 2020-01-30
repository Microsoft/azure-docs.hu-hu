---
title: 'Rövid útmutató: Content Moderator a Pythonhoz készült ügyféloldali kódtár'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti el az Azure Cognitive Services Content Moderator a Pythonhoz készült ügyféloldali kódtárat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 31cdc9663283b580acc10e7ac8d5a77f9036a7a8
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772355"
---
# <a name="quickstart-content-moderator-client-library-for-python"></a>Rövid útmutató: Content Moderator a Pythonhoz készült ügyféloldali kódtár

Ismerkedés a Content Moderator a Pythonhoz készült ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Content Moderator egy olyan kognitív szolgáltatás, amely potenciálisan sértő, kockázatos vagy egyéb módon nemkívánatos anyagokra ellenőrzi a szöveget, a képeket és a videó tartalmát. Ha ilyen anyagot talál, a szolgáltatás megfelelő címkéket (jelölőket) alkalmaz a tartalomra. Az alkalmazás ezután kezelheti a megjelölt tartalmakat, így betarthatja az előírásokat, és fenntarthatja a felhasználók számára kívánt környezetet.

A Pythonhoz készült Content Moderator ügyféloldali kódtára a következőre használható:

* [Mérsékelt szöveg](#moderate-text)
* [Egyéni kifejezések listájának használata](#use-a-custom-terms-list)
* [Közepes méretű képek](#moderate-images)
* [Egyéni rendszerkép-lista használata](#use-a-custom-image-list)
* [Felülvizsgálat létrehozása](#create-a-review)

[Dokumentáció](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [csomag (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)

## <a name="setting-up"></a>Beállítás

### <a name="create-a-content-moderator-azure-resource"></a>Content Moderator Azure-erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást Content Moderator a helyi gépen található [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával. További lehetőségek:

* A [próbaverziós kulcs](https://azure.microsoft.com/try/cognitive-services/#decision) ingyenes hét napig érvényes. A regisztráció után elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Az erőforrás megtekintése a [Azure Portal](https://portal.azure.com/)

A próbaverziós előfizetésből vagy erőforrásból származó kulcs lekérése után [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcs és végpont URL-címéhez, `CONTENT_MODERATOR_SUBSCRIPTION_KEY` és `CONTENT_MODERATOR_ENDPOINT`névvel.
 
### <a name="create-a-python-script"></a>Python-szkript létrehozása

Hozzon létre egy új Python-szkriptet, és nyissa meg az előnyben részesített szerkesztőben vagy IDE-ben. Ezután adja hozzá a következő `import` utasításokat a fájl elejéhez.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

Ezután hozzon létre változókat az erőforrás végpontjának helyéhez és a kulcshoz környezeti változókként. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Ha az alkalmazás elindítása után hozta létre a környezeti változókat, akkor a változók eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt.

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Content Moderator ügyféloldali függvénytárát a következő paranccsal telepítheti:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>Objektummodell

A következő osztályok a Content Moderator Python SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Ez az osztály minden Content Moderator funkcióhoz szükséges. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja.|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Ez az osztály lehetővé teszi a képek elemzését a felnőtt tartalmak, a személyes adatok vagy az emberi arcok számára.|
|[TextModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Ez az osztály a nyelv, a káromkodás, a hibák és a személyes adatok szövegének elemzéséhez nyújt funkciókat.|
[ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Ez az osztály a felülvizsgálati API-k funkcionalitását biztosítja, beleértve a feladatok létrehozására, az egyéni munkafolyamatokra és az emberi felülvizsgálatokra vonatkozó módszereket is.|

## <a name="code-examples"></a>Példák a kódokra

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Pythonhoz készült Content Moderator ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Mérsékelt szöveg](#moderate-text)
* [Egyéni kifejezések listájának használata](#use-a-custom-terms-list)
* [Közepes méretű képek](#moderate-images)
* [Egyéni rendszerkép-lista használata](#use-a-custom-image-list)
* [Felülvizsgálat létrehozása](#create-a-review)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató feltételezi, hogy [létrehozott egy környezeti változót](../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a Content moderator kulcshoz és a végponthoz.

Ügyfelet hoz létre a végponttal és a kulccsal. Hozzon létre egy [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) objektumot a kulccsal, és használja a végpontján egy [ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python) objektum létrehozásához.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Mérsékelt szöveg

A következő kód egy Content Moderator ügyfelet használ egy szöveg szövegtörzsének elemzésére és az eredmények a konzolra való kinyomtatására. Először hozzon létre egy **text_files/** mappát a projekt gyökérkönyvtárában, és vegyen fel egy *content_moderator_text_moderation. txt* fájlt. Adja hozzá saját szövegét a fájlhoz, vagy használja a következő mintaszöveg:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Adjon hozzá egy hivatkozást az új mappához.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Ezután adja hozzá a következő kódot a Python-szkripthez.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Egyéni kifejezések listájának használata

A következő kód bemutatja, hogyan kezelheti az egyéni kifejezések listáját a szöveges moderáláshoz. A [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) osztály használatával létrehozhat egy kifejezéseket tartalmazó listát, kezelheti az egyes kifejezéseket, és más szövegeket is megadhat.

### <a name="get-sample-text"></a>Minta szövegének beolvasása

A minta használatához létre kell hoznia egy **text_files/** mappát a projekt gyökérkönyvtárában, és hozzá kell adnia egy *content_moderator_term_list. txt* fájlt. Ennek a fájlnak tartalmaznia kell a feltételek listáján ellenőrizendő szerves szöveget. A következő mintaszöveg használható:

```
This text contains the terms "term1" and "term2".
```

Ha még nem definiált ilyet, adjon hozzá egy hivatkozást a mappához.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Lista létrehozása

Adja hozzá a következő kódot a Python-szkripthez egy egyéni feltételek listájának létrehozásához, és mentse az azonosító értékét.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Lista részleteinek meghatározása

A lista AZONOSÍTÓjának használatával szerkesztheti a nevét és a leírását.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Kifejezés hozzáadása a listához

A következő kód hozzáadja a `"term1"` és `"term2"` a listához.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>A lista összes kifejezésének beolvasása

A lista AZONOSÍTÓját használhatja a lista összes feltételének visszaküldéséhez.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>A lista indexének frissítése

A listában szereplő kifejezések hozzáadásakor vagy eltávolításakor frissítenie kell az indexet, mielőtt használni tudja a frissített listát.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Képernyő szövege a listáról

Az egyéni feltételek lista fő funkciója, hogy összehasonlítsa a szöveg törzsét a listáról, és megkeresi, hogy vannak-e egyező feltételek. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Kifejezés eltávolítása egy listáról

A következő kód eltávolítja a `"term1"` kifejezést a listából.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Összes kifejezés eltávolítása egy listáról

A következő kód használatával törölheti az összes feltétel listáját.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>Lista törlése

Az alábbi kód használatával törölheti az egyéni kifejezések listáját.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Közepes méretű képek

A következő kód egy Content Moderator ügyfelet használ a [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) objektummal együtt, hogy elemezze a képeket a felnőtteknek és a zamatos tartalomnak.

### <a name="get-images"></a>Rendszerképek beolvasása

Adjon meg egy, az elemezni kívánt lemezképre mutató hivatkozást.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Ezután adja hozzá a következő kódot a képeken való iterációhoz. Ebben a szakaszban a kód többi része ebben a hurokban fog megjelenni.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Felnőtt/zamatos tartalom keresése

A következő kód ellenőrzi a képet a felnőtteknek vagy a zamatos tartalomnak az adott URL-címen, és kinyomtatja az eredményeket a-konzolra. A jelen feltételekkel kapcsolatos információkért tekintse meg a [képek moderálási fogalmait](./image-moderation-api.md) ismertető útmutatót.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Látható szöveg keresése

A következő kód ellenőrzi a képet a látható szöveges tartalomhoz, és kinyomtatja az eredményeket a-konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Arcok keresése

Az alábbi kód ellenőrzi az emberi arcok képét, és kinyomtatja az eredményeket a-konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Egyéni rendszerkép-lista használata

A következő kód bemutatja, hogyan kezelheti a lemezképek moderálására szolgáló képek egyéni listáját. Ez a funkció akkor hasznos, ha a platform gyakran ugyanazokat a lemezképeket fogadja, amelyeket ki szeretne próbálni. Az adott lemezképek listájának karbantartásával javíthatja a teljesítményt. A [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) osztály segítségével létrehozhat egy képlistát, kezelheti a listán szereplő egyes lemezképeket, és összehasonlíthatja más képeket.

Hozza létre a következő szöveges változókat az ebben a forgatókönyvben használni kívánt képurl-címek tárolásához.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Ez nem a megfelelő lista, hanem a programkód `add images` szakaszában felvenni kívánt rendszerképek informális listája.


### <a name="create-an-image-list"></a>Rendszerkép-lista létrehozása

Adja hozzá a következő kódot egy rendszerkép-lista létrehozásához, és mentse az AZONOSÍTÓra mutató hivatkozást.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Képek hozzáadása egy listához

A következő kód hozzáadja az összes rendszerképet a listához.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Adja meg a **add_images** Helper függvényt máshol a parancsfájlban.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Képek beolvasása a listában

A következő kód a listában szereplő összes rendszerkép nevét kinyomtatja.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Frissítési lista részletei

A lista AZONOSÍTÓjának használatával frissítheti a lista nevét és leírását.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Lista részleteinek beolvasása

A lista aktuális részleteinek kinyomtatásához használja a következő kódot.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>A lista indexének frissítése

A képek hozzáadása vagy eltávolítása után frissítenie kell a lista indexét, mielőtt más képeket is felhasználhat.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Képek egyeztetése a listával

A Képlista fő funkciója, hogy összehasonlítsa az új képeket, és ellenőrizze, hogy vannak-e egyezések.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Rendszerkép eltávolítása a listáról

A következő kód eltávolít egy elemet a listáról. Ebben az esetben ez egy olyan rendszerkép, amely nem felel meg a lista kategóriájának.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Összes rendszerkép eltávolítása egy listáról

A következő kód használatával törölheti a képek listáját.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>A rendszerkép-lista törlése

A következő kód használatával törölheti az adott rendszerkép listáját.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Felülvizsgálat létrehozása

A Content Moderator Python SDK használatával tartalmat adhat a [felülvizsgálati eszközhöz](https://contentmoderator.cognitive.microsoft.com) , így az emberi moderátorok áttekinthetik azt. További információ a felülvizsgálati eszközről: a [felülvizsgálati eszköz fogalmi útmutatója](./review-tool-user-guide/human-in-the-loop.md).

A következő kód a [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) osztályt használja a felülvizsgálat létrehozásához, az azonosító lekéréséhez, valamint az adatoknak a felülvizsgálati eszköz webportálon való fogadását követően az emberi bevitel megérkezése után.

### <a name="get-review-credentials"></a>Felülvizsgálati hitelesítő adatok beolvasása

Először jelentkezzen be a felülvizsgálati eszközbe, és kérje le a csapat nevét. Ezután rendelje hozzá a kód megfelelő változóhoz. Megadhat egy visszahívási végpontot is, amely a felülvizsgálat tevékenységére vonatkozó frissítéseket fogad.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Rendszerkép-áttekintés létrehozása

Adja hozzá a következő kódot az adott rendszerkép URL-címéhez tartozó felülvizsgálat létrehozásához és közzétételéhez. A kód a felülvizsgálati AZONOSÍTÓra mutató hivatkozást ment. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Áttekintés részleteinek beolvasása

A következő kód használatával ellenőrizheti egy adott áttekintés részleteit. A felülvizsgálat létrehozása után megnyithatja a felülvizsgálati eszközt, és használhatja a tartalmat. Ennek módjáról további információt a következő témakörben talál: [útmutató](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Ha elkészült, újra futtathatja ezt a kódot, és lekéri a felülvizsgálati folyamat eredményét is.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Ha ebben a forgatókönyvben visszahívási végpontot használt, akkor a következő formátumú eseményt kell kapnia:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési fájlban lévő `python` paranccsal.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Content Moderator Python-függvénytárat moderálási feladatokra. Következő lépésként tekintse meg a képek vagy más média moderálását egy elméleti útmutató elolvasásával.

> [!div class="nextstepaction"]
>[Képek moderálási fogalmai](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Mi az Azure Content Moderator?](./overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py)található.