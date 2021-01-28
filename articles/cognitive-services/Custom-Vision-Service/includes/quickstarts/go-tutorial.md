---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 49b920ede2b0af306af00875a3368cffd853f89b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947224"
---
Ez az útmutató útmutatást és mintakód segítséget nyújt a Custom Vision ügyféloldali kódtár a Go-hoz való használatának megkezdéséhez a rendszerkép-besorolási modell létrehozásához. Létrehoz egy projektet, címkéket ad hozzá, betanítja a projektet, és a projekt előrejelzési végpontjának URL-címét használja a programozott teszteléshez. Ez a példa sablonként használható a saját rendszerkép-felismerő alkalmazás létrehozásához.

> [!NOTE]
> Ha a besorolási modellt kód írása _nélkül_ szeretné felépíteni és betanítani, tekintse meg a [böngészőalapú útmutatást](../../getting-started-build-a-classifier.md) .

## <a name="prerequisites"></a>Előfeltételek

- [Go 1.8 +](https://golang.org/doc/install)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>Az Custom Vision ügyféloldali kódtár telepítése

Ha Custom Vision for go-val szeretné írni a rendszerkép-elemzési alkalmazást, szüksége lesz a Custom Vision szolgáltatás ügyféloldali könyvtárára. Futtassa az alábbi parancsot a PowerShellben:

```shell
go get -u github.com/Azure/azure-sdk-for-go/...
```

vagy ha `dep` a-t használja, a tárházon belül futtassa a következőket:
```shell
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [python-get-images](../../includes/python-get-images.md)]

## <a name="add-the-code"></a>A kód hozzáadása

Hozzon létre egy *sample. go* nevű új fájlt a kívánt Project-címtárban.

## <a name="create-the-custom-vision-project"></a>A Custom Vision projekt létrehozása

Adja hozzá a következő kódot a szkripthez egy új Custom Vision Service-projekt létrehozásához. Illessze be az előfizetői azonosítókat a megfelelő definíciókba. A végpont URL-címét a Custom Vision webhely beállítások lapján szerezheti be.

Tekintse meg a [CreateProject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) metódust a projekt létrehozásakor a többi beállítás megadásához (az [osztályozó webportál összeállításával](../../getting-started-build-a-classifier.md) foglalkozó útmutatóban).

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    prediction_resource_id = "<your prediction resource id>"
    endpoint string = "<your endpoint URL>"
    project_name string = "Go Sample Project"
    iteration_publish_name = "classifyModel"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    project, err := trainer.CreateProject(ctx, project_name, "sample project", nil, string(training.Multilabel))
    if (err != nil) {
        log.Fatal(err)
    }
```

## <a name="create-tags-in-the-project"></a>Címkék létrehozása a projektben

Ha besorolási címkéket szeretne létrehozni a projekthez, adja hozzá a következő kódot a *minta végéhez. Ugrás*:

```go
// Make two tags in the new project
hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

A minta képek projekthez adásához, helyezze el a következő kódot a címke létrehozása után. Ez a kód a képeket a hozzájuk tartozó címkékkel együtt tölti fel. Egyetlen kötegben akár 64 képet is feltölthet.

> [!NOTE]
> A lemezképek elérési útját módosítania kell, attól függően, hogy a Cognitive Services go SDK Samples projektet korábban letöltötte.

```go
fmt.Println("Adding images...")
japaneseCherryImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Japanese Cherry"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

hemLockImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Hemlock"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

for _, file := range hemLockImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Hemlock", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))

    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ hemlockTag.ID.String() })
}

for _, file := range japaneseCherryImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Japanese Cherry", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))
    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ cherryTag.ID.String() })
}
```

## <a name="train-and-publish-the-project"></a>A projekt betanítása és közzététele

Ez a kód létrehozza az előrejelzési modell első iterációját, majd közzéteszi ezt az iterációt az előrejelzési végponton. A közzétett iterációhoz megadott név felhasználható az előrejelzési kérelmek küldésére. Egy iteráció nem érhető el az előrejelzési végponton, amíg közzé nem teszi.

```go
fmt.Println("Training...")
iteration, _ := trainer.TrainProject(ctx, *project.ID)
for {
    if *iteration.Status != "Training" {
        break
    }
    fmt.Println("Training status: " + *iteration.Status)
    time.Sleep(1 * time.Second)
    iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
}
fmt.Println("Training status: " + *iteration.Status)

trainer.PublishIteration(ctx, *project.ID, *iteration.ID, iteration_publish_name, prediction_resource_id))
```

## <a name="use-the-prediction-endpoint"></a>Az előrejelzési végpont használata

A képek előrejelzési végpontra való küldéséhez és az előrejelzés lekéréséhez adja hozzá a következő kódot a fájl végéhez:

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.ClassifyImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions    {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa a *sample. go* parancsot.

```shell
go run sample.go
```

Az alkalmazás kimenetének az alábbi szöveghez hasonlóan kell kinéznie:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Ezután ellenőrizheti, hogy a tesztkép (az **<base_image_url>/Images/Test/** mappában található) megfelelően lett-e megcímkézve. Vissza is léphet a [Custom Vision webhelyére](https://customvision.ai), és megtekintheti az újonnan létrehozott projekt aktuális állapotát.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan végezhető el az objektum-észlelési folyamat minden lépése a kódban. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran több alkalommal kell betanítania és tesztelni a modellt, hogy pontosabb legyen.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](../../test-your-model.md)

* [Mi a Custom Vision?](../../overview.md)
* [SDK-referenciák dokumentációja (képzés)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/customvision/training)
* [SDK-referenciák dokumentációja (előrejelzés)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.1/customvision/prediction)
