---
title: 'ML Studio (klasszikus): a klasszikus webszolgáltatás újratanítása – Azure'
description: Megtudhatja, hogyan lehet áttanítani a modelleket, és hogyan frissíthet egy klasszikus webszolgáltatást, hogy az újonnan betanított modellt használja Azure Machine Learning Studio (klasszikus).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: 90c968ee953e80238775639964cb09a25741b33d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517569"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Klasszikus Studio-webszolgáltatás újratanítása és üzembe helyezése

**a következőkre vonatkozik:** ![ Zöld pipa. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasszikus) ![ X, amely nem.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


A gépi tanulási modellek átképzésének egyik módja annak biztosítása, hogy pontosak maradjanak, és a rendelkezésre álló legfontosabb információk alapján. Ez a cikk bemutatja, hogyan végezheti el a klasszikus Studio (klasszikus) webszolgáltatás újratanítását. Az új Studio (klasszikus) webszolgáltatás újratanításával kapcsolatos útmutatóért [tekintse meg ezt a cikket.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már rendelkezik egy átképzési kísérlettel és egy prediktív kísérlettel is. Ezeket a lépéseket a [Machine learning-modellek újratanítása és üzembe helyezése](./retrain-machine-learning-model.md) című témakör ismerteti. A gépi tanulási modell új webszolgáltatásként való üzembe helyezése helyett azonban klasszikus webszolgáltatásként kell üzembe helyezni a prediktív kísérletet.
     
## <a name="add-a-new-endpoint"></a>Új végpont hozzáadása

A központilag telepített prediktív webszolgáltatás tartalmaz egy alapértelmezett pontozási végpontot, amely szinkronban van az eredeti betanítási és pontozási kísérletek betanított modelljével. Ha a webszolgáltatást új betanított modellel szeretné frissíteni, létre kell hoznia egy új pontozási végpontot.

Két módon adhat hozzá új végpontot egy webszolgáltatáshoz:

* Programozott módon
* Az Azure Web Services portál használata

> [!NOTE]
> Győződjön meg arról, hogy hozzáadja a végpontot a prediktív webszolgáltatáshoz, nem pedig a betanítási webszolgáltatás számára. Ha megfelelően telepített egy képzést és egy prediktív webszolgáltatást, akkor két különálló webszolgáltatásnak kell szerepelnie. A prediktív webszolgáltatás végén szerepelnie kell a következőnek: "[prediktív exp.]".
>

### <a name="programmatically-add-an-endpoint"></a>Végpont programozott hozzáadása

Pontozási végpontokat adhat hozzá a [GitHub-tárházban](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint)megadott mintakód használatával.

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Végpont hozzáadása az Azure Web Services portál használatával

1. Machine Learning Studio (klasszikus), a bal oldali navigációs oszlopban kattintson a webszolgáltatások elemre.
1. A webszolgáltatás irányítópultjának alján kattintson a **végpontok kezelése előnézet** elemre.
1. Kattintson a **Hozzáadás** parancsra.
1. Adja meg az új végpont nevét és leírását. Válassza ki a naplózási szintet, valamint azt, hogy a mintaadatok engedélyezve vannak-e. További információ a naplózásról: [Machine learning webszolgáltatások naplózásának engedélyezése](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>A hozzáadott végpont betanított modellének frissítése

### <a name="retrieve-patch-url"></a>JAVÍTÁS URL-címének beolvasása

Kövesse az alábbi lépéseket a javítás URL-címének beszerzéséhez a webes portál használatával:

1. Jelentkezzen be a [Azure Machine learning Web Services](https://services.azureml.net/) portálra.
1. Kattintson felül a **webszolgáltatások** vagy a **klasszikus webszolgáltatások** lehetőségre.
1. Kattintson arra a pontozási webszolgáltatásra, amelyen dolgozik (ha nem módosította a webszolgáltatás alapértelmezett nevét, akkor a "[pontozási exp.]" kifejezés jelenik meg).
1. Kattintson az **+ új** elemre.
1. A végpont hozzáadása után kattintson a végpont nevére.
1. A **patch** URL-cím alatt kattintson az **API Súgó** elemre a javítási Súgó lap megnyitásához.

> [!NOTE]
> Ha a prediktív webszolgáltatás helyett hozzáadta a végpontot a betanítási webszolgáltatáshoz, a következő hibaüzenet jelenik meg, amikor rákattint az **erőforrás frissítése** hivatkozásra: "Sajnáljuk, de ez a funkció nem támogatott, vagy nem érhető el ebben a környezetben. A webszolgáltatás nem rendelkezik frissíthető erőforrásokkal. Elnézést kérünk a kellemetlenségért, és dolgozunk a munkafolyamat javításán. "
>

A javítás súgója lapon a használni kívánt PATCH URL-cím szerepel, és a meghívásához használható mintakód is rendelkezésre áll.

![Javítási URL-cím.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>A végpont frissítése

Most már a betanított modellt is használhatja a korábban létrehozott pontozási végpont frissítéséhez.

Az alábbi mintakód bemutatja, hogyan használhatja a *BaseLocation*, a *RelativeLocation*, a *SasBlobToken* és a patch URL-címet a végpont frissítéséhez.

```csharp
private async Task OverwriteModel()
{
    var resourceLocations = new
    {
        Resources = new[]
        {
            new
            {
                Name = "Census Model [trained model]",
                Location = new AzureBlobDataReference()
                {
                    BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                    RelativeLocation = "your endpoint relative location", //from the output, for example: "experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner"
                    SasBlobToken = "your endpoint SAS blob token" //from the output, for example: "?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl"
                }
            }
        }
    };

    using (var client = new HttpClient())
    {
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

        using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
        {
            request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
            HttpResponseMessage response = await client.SendAsync(request);

            if (!response.IsSuccessStatusCode)
            {
                await WriteFailedResponse(response);
            }

            // Do what you want with a successful response here.
        }
    }
}
```

A hívás *apiKey* és *endpointUrl* a végpont-irányítópultból szerezhető be.

Az *erőforrásokban* lévő *Name* paraméter értékének meg kell egyeznie a prediktív kísérletben a mentett betanított modell erőforrásának nevével. Az erőforrás nevének lekérése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben kattintson a **Machine learning** elemre.
1. A név területen kattintson a munkaterületre, majd a **webszolgáltatások** elemre.
1. A név területen kattintson a **népszámlálási modell [prediktív exp.]** elemre.
1. Kattintson a hozzáadott új végpontra.
1. A végpont Irányítópultján kattintson az **erőforrás frissítése** elemre.
1. A webszolgáltatáshoz tartozó Resource API-dokumentáció frissítése oldalon megtekintheti az **erőforrás nevét** a **frissíthető erőforrások** területen.

Ha az SAS-jogkivonat lejár a végpont frissítése előtt, akkor a lekérést a feladatnak kell megadnia a friss jogkivonat beszerzéséhez.

A kód sikeres futtatása után az új végpontnak körülbelül 30 másodpercen belül el kell indulnia az átdolgozott modell használatával.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a webszolgáltatások kezeléséről vagy több kísérlet futtatásának nyomon követéséről, tekintse meg a következő cikkeket:

* [Ismerkedés a webszolgáltatások portálján](manage-new-webservice.md)
* [Kísérletismétlések kezelése](manage-experiment-iterations.md)