---
title: Face go ügyféloldali kódtár – rövid útmutató
description: Használja a Face ügyféloldali függvénytárát a Go-hoz az arcok észleléséhez, hasonló kereséshez (képkeresés a képhez), az arcok azonosításához (arc-felismerési keresés) és az arc-adatok átállításához.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 2c693c9e972cdfc66cce0d52323c113b6006bd97
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102444796"
---
Ismerkedjen meg az arc-felismeréssel a Face ügyféloldali kódtár for go használatával. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Face szolgáltatás hozzáférést biztosít a speciális algoritmusokhoz a képeken található emberi arcok észleléséhez és felismeréséhez.

A következőhöz való ugráshoz használja a Face Service ügyféloldali függvénytárát:

* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személy csoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)

[Dokumentáció](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face)  |  [SDK letöltése](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Előfeltételek

* A [Go](https://golang.org/dl/) legújabb verziója
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" hozzon létre egy Face-erőforrást "  target="_blank"> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Face APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* A kulcs és a végpont beszerzése után [hozzon létre környezeti változókat](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a kulcshoz és a végponthoz, a nevet és a-t `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT` .

## <a name="setting-up"></a>Beállítás

### <a name="create-a-go-project-directory"></a>Go-projekt könyvtárának létrehozása

A konzol ablakban (cmd, PowerShell, Terminal, bash) hozzon létre egy új munkaterületet a go-projekt számára, `my-app` és keresse meg a nevet.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

A munkaterület három mappát fog tartalmazni:

* **src** – ez a könyvtár a forráskódot és a csomagokat fogja tartalmazni. A paranccsal telepített csomagok ebben `go get` a mappában lesznek.
* **pkg** – ez a könyvtár tartalmazni fogja a lefordított go Package objektumokat. Ezek a fájlok mindegyike rendelkezik `.a` bővítménnyel.
* **bin** – ez a könyvtár fogja tartalmazni a futtatásakor létrehozott bináris végrehajtható fájlokat `go install` .

> [!TIP]
> Ha többet szeretne megtudni a go-munkaterület struktúrájáról, tekintse meg a [Go Language dokumentációját](https://golang.org/doc/code.html#Workspaces). Ez az útmutató a és a beállításával kapcsolatos információkat tartalmaz `$GOPATH` `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Az ügyféloldali kódtár telepítése a Go-hoz

Ezután telepítse az ügyféloldali kódtárat a Go-hoz:

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

vagy ha a DEP-t használja a tárházon belül, futtassa a következőket:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Go-alkalmazás létrehozása

Következő lépésként hozzon létre egy fájlt a (z) nevű **src** könyvtárban `sample-app.go` :

```bash
cd src
touch sample-app.go
```

Nyissa meg az `sample-app.go` előnyben részesített ide-vagy szövegszerkesztőben. Ezután adja hozzá a csomag nevét, és importálja a következő könyvtárakat:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Ezt követően megkezdheti a kód hozzáadását a különböző Face Service-műveletek végrehajtásához.

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik a Face Service go ügyféloldali függvénytárának főbb funkcióit.

|Név|Leírás|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Ez az osztály a Face szolgáltatás használatára vonatkozó engedélyt jelöli, és minden arc funkcióhoz szüksége van rá. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja. |
|[Ügyfél](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Ez az osztály az emberi arcokkal elvégezhető alapvető észlelési és felismerési feladatokat kezeli. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Ez az osztály a rendszerkép egyetlen arca által észlelt összes adatmennyiséget jelképezi. Ezzel az oldallal kapcsolatos részletes információkat kérhet le.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Ez az osztály kezeli a felhőben tárolt **FaceList** -szerkezeteket, amelyek az arcok válogatott készleteit tárolják. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Ez az osztály kezeli a felhőalapú tárolt **személyek** szerkezetét, amelyek egyetlen személyhez tartozó arcok készletét tárolják.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Ez az osztály kezeli a felhőben tárolt **PersonGroup** -szerkezeteket, amelyek a különböző **személyre** kiterjedő objektumok készletét tárolják. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Ez az osztály kezeli a pillanatkép funkcióit. A használatával ideiglenesen mentheti az összes felhőalapú Arcfelismerés, és áttelepítheti az adott adatait egy új Azure-előfizetésbe. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan végezheti el az alapszintű feladatokat a go szolgáltatás ügyféloldali kódtára használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személy csoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE] 
> Ez a rövid útmutató azt feltételezi, hogy [létrehozott egy környezeti változót](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) az arc kulcsához és a végponthoz, illetve a nevet `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT` .

Hozzon létre egy **fő** függvényt, és adja hozzá a következő kódot egy ügyfél létrehozásához a végponttal és a kulccsal. Hozzon létre egy **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** objektumot a kulccsal, és használja azt a végponttal egy **[ügyfél](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** -objektum létrehozásához. Ez a kód egy környezeti objektumot is létrehoz, amely az ügyfélalkalmazások létrehozásához szükséges. Emellett olyan távoli helyet is meghatároz, ahol a rövid útmutatóban szereplő néhány minta lemezkép található.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Arcok felismerése a képeken

Adja hozzá a következő kódot a **Main** metódushoz. Ez a kód egy távoli mintaképet határoz meg, és meghatározza, hogy mely arc-funkciókat kell kinyerni a rendszerképből. Azt is meghatározza, hogy melyik AI-modellt kell használnia az észlelt arc (ok) ből származó adatok kinyeréséhez. A beállításokkal kapcsolatos információkért tekintse [meg a felismerési modell megadása](../../Face-API-How-to-Topics/specify-recognition-model.md) című témakört. Végül a **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** metódus az Arcfelismerés műveletet hajtja végre a képen, és menti az eredményeket a program memóriájában.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

> [!TIP]
> A helyi rendszerképben is felderítheti az arcokat. Tekintse meg az [ügyfél](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client) módszereit, például a **DetectWithStream**.

### <a name="display-detected-face-data"></a>Észlelt Arcfelismerés

A kód következő blokkja a **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** objektumok tömb első elemét veszi fel, és kiírja az attribútumait a konzolra. Ha több képpel rendelkező rendszerképet használt, akkor inkább a tömbön keresztül kell megismételni a tömböt.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A következő kód egyetlen észlelt arcot (forrást) használ, és a találatok között megkeresi a többi arcot (TARGET) Ha egyezést talál, kinyomtatja az egyeztetett arc AZONOSÍTÓját a konzolon.

### <a name="detect-faces-for-comparison"></a>Arcok észlelése összehasonlításhoz

Először mentse a képek szakaszban az [arcok észlelése](#detect-faces-in-an-image) során észlelt arcot. Ez az arc lesz a forrás.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Ezután adja meg a következő kódot az arcok egy másik rendszerképben való észleléséhez. Ezek az arcok lesznek a célpontok.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Egyezések keresése

A következő kód a **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** metódus használatával keresi meg az összes cél arcot, amely megfelel a forrás arcanek.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Egyezések nyomtatása

A következő kód a megfeleltetés részleteit jeleníti meg a konzolon.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Személy csoport létrehozása és betanítása

Ennek a forgatókönyvnek a végrehajtásához a következő képeket kell mentenie a projekt gyökérkönyvtárában: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

A rendszerkép ezen csoportja három különböző személynek megfelelő egyoldalas rendszerképeket tartalmaz. A kód három PersonGroup- **személyt** határoz meg, és társítja azokat a (, `woman` `man` , és) kezdetű képfájlokhoz `child` .

### <a name="create-persongroup"></a>PersonGroup létrehozása

A lemezképek letöltése után adja hozzá a következő kódot a **Main** metódus aljához. Ez a kód hitelesít egy **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** objektumot, majd egy új **PersonGroup** definiálásához használja.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>PersonGroup-személyek létrehozása

A kód következő blokkja hitelesít egy **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** , és a használatával három új PersonGroup- **személyt** határoz meg. Ezek az objektumok a képek készletében egyetlen személyt képviselnek.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Arcok társítása személyekhez

A következő kód a lemezképeket az előtagjaik alapján rendezi, észleli az arcokat, és hozzárendeli az arcokat a megfelelő **PersonGroup-személyekhez** a képfájl neve alapján.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

> [!TIP]
> Az URL-cím által hivatkozott távoli rendszerképekből is létrehozhat **PersonGroup** . Tekintse meg a [PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient) metódusokat, például a **AddFaceFromURL**.

### <a name="train-persongroup"></a>PersonGroup betanítása

Az arcok hozzárendelése után betanítja a **PersonGroup** , hogy képes legyen azonosítani az egyes **személy** objektumaihoz kapcsolódó vizuális funkciókat. A következő kód meghívja az aszinkron **betanítási** módszert, és lekérdezi az eredményt, kinyomtatja az állapotot a konzolra.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

> [!TIP]
> A Face API a természetben statikus, előre elkészített modelleken fut (a modell teljesítménye nem visszafejlődés meg és nem lesz fejlesztve, mivel a szolgáltatás fut). A modell által előállított eredmények megváltozhatnak, ha a Microsoft frissíti a modell hátterét anélkül, hogy teljesen új modellre kellene migrálni. A modell újabb verziójának kihasználása érdekében áttaníthatja a **PersonGroup**, és megadhatja az újabb modellt ugyanazon beléptetési képpel rendelkező paraméterként.

## <a name="identify-a-face"></a>Arc azonosítása

Az azonosítási művelet egy személy (vagy több személy) képét veszi igénybe, és megkeresi a rendszerképben lévő egyes arcok identitását (Arcfelismerés-keresés). Összehasonlítja az észlelt elemeket egy **PersonGroup**, egy olyan, a különböző **személy** objektumokat tartalmazó adatbázissal, amelyek az arc funkciói ismertek.

> [!IMPORTANT]
> A példa futtatásához először futtatnia kell a kódot [egy személy csoport létrehozása és betanítása csoportban](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Tesztelési rendszerkép beolvasása

A következő kód a projekt gyökerében jelenik meg egy rendszerképet _test-image-person-group.jpg_ és betölti a program memóriába. Ezt a lemezképet ugyanabban a tárházban találja, mint a [személyek létrehozása és betanítása csoportban](#create-and-train-a-person-group)használt rendszerképek: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Forrásként szolgáló arcok észlelése a tesztelési képen

A következő kódrészlet az összes arc lekérésére és a tömbbe mentésére szolgáló, egyszerű Arcfelismerés.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Arcok azonosítása

Az **[azonosított](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** metódus az észlelt arcok tömbjét veszi fel, és összehasonlítja őket az adott **PersonGroup** (a korábbi szakaszban meghatározott és betanított). Ha a csoport egyik tagjának is képesnek kell lennie az észlelt **személyre** , a rendszer menti az eredményt.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

A kód ezután a részletes egyezési eredményeket jeleníti meg a konzolon.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Arcok ellenőrzése

Az ellenőrzési művelet egy arcfelismerés és egy másik Arcfelismerés, vagy egy **személy** objektum, és meghatározza, hogy ugyanahhoz a személyhez tartoznak-e.

A következő kód észleli az arcok két forrás képét, majd ellenőrzi, hogy mindegyiket egy célként megadott képpel szemben észleltek-e.

### <a name="get-test-images"></a>Tesztelési lemezképek beolvasása

A következő kódrészletek deklarálják azokat a változókat, amelyek az ellenőrzési művelet céljának és forrásának rendszerképeire mutatnak.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Arcok észlelése ellenőrzéshez

A következő kód észleli a forrás-és a célként megadott képeket, és azokat változókba menti.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Ellenőrzési eredmények beolvasása

A következő kód összehasonlítja az egyes forrás-lemezképeket a célként megadott képpel, és kinyomtat egy üzenetet, amely jelzi, hogy ugyanahhoz a személyhez tartoznak-e.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az Arcfelismerés alkalmazást az alkalmazás könyvtárából az `go run <app-name>` paranccsal.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Ha ebben a rövid útmutatóban létrehozott egy **PersonGroup** , és törölni szeretné, hívja meg a **[delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** metódust.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja az arc ügyféloldali függvénytárat a go to do-alapú Arcfelismerés feladatok elvégzéséhez. Ezután tekintse át a dokumentációt, és ismerkedjen meg a könyvtárral.

> [!div class="nextstepaction"]
> [Face API referenciája (go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Mi az a Face szolgáltatás?](../../overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go)található.