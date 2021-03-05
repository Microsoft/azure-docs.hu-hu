---
title: Face Python ügyféloldali kódtár – rövid útmutató
description: Használja a Face ügyféloldali kódtárat a Pythonhoz az arcok észleléséhez, hasonló kereséséhez (képek kereséséhez) és az arcok azonosításához (arc-felismerési keresés).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/10/2020
ms.author: pafarley
ms.openlocfilehash: 6ae39d0bdce34fbb57e58fb4937b28113ca54fe5
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102193840"
---
Ismerkedés az Arcfelismerés a Pythonhoz készült Face ügyféloldali kódtár használatával. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Face szolgáltatás hozzáférést biztosít a speciális algoritmusokhoz a képeken található emberi arcok észleléséhez és felismeréséhez.

A Pythonhoz készült Face ügyféloldali kódtár a következőre használható:

* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személy csoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)
* [Arcok ellenőrzése](#verify-faces)

[Dokumentáció](/python/api/azure-cognitiveservices-vision-face/)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face)  |  [Csomag (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/)  |  [Példák](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * A Python-telepítésnek tartalmaznia kell a [pip](https://pip.pypa.io/en/stable/)-et. Ha a parancssorban fut, ellenőrizze, hogy a pip telepítve van-e `pip --version` . Szerezze be a pip-et a Python legújabb verziójának telepítésével.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" hozzon létre egy Face-erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Face APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás
 
### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után az ügyféloldali kódtár a következővel telepíthető:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-parancsfájl &mdash; *Quickstart-file.py*, például:. Ezután nyissa meg a kívánt szerkesztőben vagy IDE, és importálja a következő könyvtárakat.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

Ezután hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott Face erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsot és a végpontot az erőforrás- **kezelés** területen, az erőforrás **kulcs és végpont** lapján találja. 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Például az [Azure Key Vault](../../../../key-vault/general/overview.md).

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a Face Python ügyféloldali kódtár főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
|[FaceClient](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient) | Ez az osztály a Face szolgáltatás használatára vonatkozó engedélyt jelöli, és minden arc funkcióhoz szüksége van rá. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja. |
|[FaceOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations)|Ez az osztály az emberi arcokkal elvégezhető alapvető észlelési és felismerési feladatokat kezeli. |
|[DetectedFace](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface)|Ez az osztály a rendszerkép egyetlen arca által észlelt összes adatmennyiséget jelképezi. Ezzel az oldallal kapcsolatos részletes információkat kérhet le.|
|[FaceListOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations)|Ez az osztály kezeli a felhőben tárolt **FaceList** -szerkezeteket, amelyek az arcok válogatott készleteit tárolják. |
|[PersonGroupPersonOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations)| Ez az osztály kezeli a felhőalapú tárolt **személyek** szerkezetét, amelyek egyetlen személyhez tartozó arcok készletét tárolják.|
|[PersonGroupOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations)| Ez az osztály kezeli a felhőben tárolt **PersonGroup** -szerkezeteket, amelyek a különböző **személyre** kiterjedő objektumok készletét tárolják. |
|[ShapshotOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations)|Ez az osztály kezeli a pillanatkép funkciót; a használatával ideiglenesen mentheti az összes felhőalapú Arcfelismerés, és áttelepítheti az adott adatait egy új Azure-előfizetésbe. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Pythonhoz készült Face ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személy csoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)
* [Arcok ellenőrzése](#verify-faces)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Ügyfelet hoz létre a végponttal és a kulccsal. Hozzon létre egy [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) objektumot a kulccsal, és használja a végpontján egy [FaceClient](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient) objektum létrehozásához.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Arcok felismerése a képeken

A következő kód egy távoli rendszerképben észlel egy arcot. Kinyomtatja az észlelt Face AZONOSÍTÓját a konzolon, és a program memóriájában tárolja azt is. Ezt követően észleli a több emberrel rendelkező képekben lévő arcokat, és kiírja az azonosítókat a konzolra. A [detect_with_url](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) metódus paramétereinek módosításával különböző adatokat adhat vissza az egyes [DetectedFace](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface) -objektumokkal.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

> [!TIP]
> A helyi rendszerképben is felderítheti az arcokat. Tekintse meg a [FaceOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations) metódusokat, például a **detect_with_stream**.

### <a name="display-and-frame-faces"></a>Arcok megjelenítése és kerete

A következő kód kiírja a megadott képet a kijelzőre, és téglalapokat rajzol az arcok köré az DetectedFace. faceRectangle tulajdonság használatával.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Egy fiatal nő, amely az arc körül rajzolt piros téglalapot alkot](../../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A következő kód egyetlen észlelt arcot (forrást) használ, és a találatok között megkeresi a többi arcot (TARGET) Ha egyezést talál, kinyomtatja az egyeztetett arc AZONOSÍTÓját a konzolon.

### <a name="find-matches"></a>Egyezések keresése

Először is futtassa a kódot a fenti szakaszban ([egy képen lévő arcok észlelése](#detect-faces-in-an-image)), hogy egyetlen arc hivatkozását mentse. Ezután futtassa a következő kódot, hogy egy csoport rendszerképében több arc hivatkozása legyen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Ezután adja hozzá a következő kódrészletet a csoport első arc példányainak megkereséséhez. A viselkedés módosításához tekintse meg a [find_similar](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) metódust.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Egyezések nyomtatása

A következő kód használatával nyomtassa ki a megfeleltetés részleteit a-konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Személy csoport létrehozása és betanítása

A következő kód egy **PersonGroup** hoz létre három különböző **személy** objektummal. Az egyes **személyeket** például a lemezképek halmazával társítja, majd az összes személyt felismerheti. 

### <a name="create-persongroup"></a>PersonGroup létrehozása

Ennek a forgatókönyvnek a végrehajtásához a következő képeket kell mentenie a projekt gyökérkönyvtárában: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

A képek ezen csoportja három különböző személynek megfelelő arc-rendszerképeket tartalmaz. A kód három **személy** objektumot határoz meg, és társítja azokat a `woman` ,, és rendszerű képfájlokkal `man` `child` .

Miután beállította a lemezképeket, Definiáljon egy címkét a szkript tetején a létrehozandó **PersonGroup** objektumhoz.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Ezután adja hozzá a következő kódot a szkript aljához. Ez a kód egy **PersonGroup** és három **személy** objektumot hoz létre.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Arcok társítása személyekhez

A következő kód a képeket előtagjaként rendezi, észleli az arcokat, és hozzárendeli az arcokat az egyes **személyek** objektumokhoz.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

> [!TIP]
> Az URL-cím által hivatkozott távoli rendszerképekből is létrehozhat **PersonGroup** . Tekintse meg a [PersonGroupPersonOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations) metódusokat, például a **add_face_from_url**.

### <a name="train-persongroup"></a>PersonGroup betanítása

Az arcok hozzárendelése után be kell tanítania a **PersonGroup** , hogy azonosítani tudja az egyes **személy** objektumaihoz kapcsolódó vizuális funkciókat. A következő kód meghívja az aszinkron **betanítási** módszert, és lekérdezi az eredményt, kinyomtatja az állapotot a konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

> [!TIP]
> A Face API a természetben statikus, előre elkészített modelleken fut (a modell teljesítménye nem visszafejlődés meg és nem lesz fejlesztve, mivel a szolgáltatás fut). A modell által előállított eredmények megváltozhatnak, ha a Microsoft frissíti a modell hátterét anélkül, hogy teljesen új modellre kellene migrálni. A modell újabb verziójának kihasználása érdekében áttaníthatja a **PersonGroup**, és megadhatja az újabb modellt ugyanazon beléptetési képpel rendelkező paraméterként.

## <a name="identify-a-face"></a>Arc azonosítása

Az azonosítási művelet egy személy (vagy több személy) képét veszi igénybe, és megkeresi a rendszerképben lévő egyes arcok identitását (Arcfelismerés-keresés). Összehasonlítja az észlelt elemeket egy **PersonGroup**, egy olyan, a különböző **személy** objektumokat tartalmazó adatbázissal, amelyek az arc funkciói ismertek.

> [!IMPORTANT]
> A példa futtatásához először futtatnia kell a kódot [egy személy csoport létrehozása és betanítása csoportban](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Tesztelési rendszerkép beolvasása

A következő kód a projekt gyökerében található a rendszerkép _test-image-person-group.jpg_ és észleli a képen látható arcokat. Ezt a képet a **PersonGroup** -felügyelethez használt rendszerképekkel találja: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Arcok azonosítása

Az **azonosítási** módszer az észlelt arcok tömbjét veszi fel, és összehasonlítja őket egy **PersonGroup**. Ha egy **személynek** észlelt arcot tud megfeleltetni, a rendszer menti az eredményt. Ez a kód részletes találatokat nyomtat a-konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Arcok ellenőrzése

Az ellenőrzési művelet egy arcfelismerés és egy másik Arcfelismerés, vagy egy **személy** objektum, és meghatározza, hogy ugyanahhoz a személyhez tartoznak-e.

A következő kód észleli a két forrás képét, majd ellenőrzi azokat egy, a megcélzott képből észlelt arc alapján.

### <a name="get-test-images"></a>Tesztelési lemezképek beolvasása

A következő kódrészletek deklarálják azokat a változókat, amelyek az ellenőrzési művelet forrás-és célként megadott képeire mutatnak majd.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Arcok észlelése ellenőrzéshez

A következő kód észleli a forrás-és a célként megadott képeket, és azokat változókba menti.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Ellenőrzési eredmények beolvasása

A következő kód összehasonlítja az egyes forrás-lemezképeket a célként megadott képpel, és kinyomtat egy üzenetet, amely jelzi, hogy ugyanahhoz a személyhez tartoznak-e.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az Arcfelismerés alkalmazást az alkalmazás könyvtárából az `python` paranccsal.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Ha ebben a rövid útmutatóban létrehozott egy **PersonGroup** , és törölni szeretné, futtassa a következő kódot a parancsfájlban:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Pythonhoz készült Face ügyféloldali kódtárat az arcfelismerő feladatok elvégzésére. Ezután tekintse át a dokumentációt, és ismerkedjen meg a könyvtárral.

> [!div class="nextstepaction"]
> [Face API-hivatkozás (Python)](/python/api/azure-cognitiveservices-vision-face/)

* [Mi az a Face szolgáltatás?](../../overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py)található.
