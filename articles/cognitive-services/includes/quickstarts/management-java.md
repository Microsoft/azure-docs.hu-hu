---
title: 'Gyors útmutató: az Azure Management ügyféloldali kódtára a Javához'
description: Ebben a rövid útmutatóban megismerheti a Javához készült Azure Management ügyféloldali kódtárat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 3/22/2021
ms.author: pafarley
ms.openlocfilehash: 4c0d4dd1a834e42a75da5199b7aaed0e123f8e63
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879678"
---
[Dokumentáció](/java/api/com.microsoft.azure.management.cognitiveservices)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/mgmt-v2017_04_18/src/main/java/com/microsoft/azure/management/cognitiveservices/v2017_04_18)  |  [Csomag (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-mgmt-cognitiveservices)

## <a name="java-prerequisites"></a>A Java előfeltételei

* Érvényes Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle Build eszköz](https://gradle.org/install/)vagy egy másik függőségi kezelő.


[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-java-application"></a>Új Java-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `gradle init` parancsot a munkakönyvtárból. Ez a parancs alapvető Build-fájlokat hoz létre a Gradle számára, beleértve a *Build. Gradle. KTS* fájlt, amelyet futásidőben használ az alkalmazás létrehozásához és konfigurálásához.

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL** kiválasztására, válassza a **Kotlin** lehetőséget.

A munkakönyvtárból futtassa a következő parancsot:

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Ez a rövid útmutató a Gradle függőség-kezelőt használja. Megtalálhatja az ügyféloldali függvénytárat és az egyéb függőségi kezelők információit a [Maven központi adattárában](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

A projekt *Build. gradle. KTS* fájljában adja meg az ügyféloldali függvénytárat `implementation` utasításként, valamint a szükséges beépülő modulokat és beállításokat.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.microsoft.azure", name = "azure-mgmt-cognitiveservices", version = "1.10.0-beta")
}
```

### <a name="import-libraries"></a>Kódtárak importálása

Navigáljon az új **src/Main/Java** mappára, és hozzon létre egy *Management. Java* nevű fájlt. Nyissa meg a kívánt szerkesztőben vagy IDE, és adja hozzá a következő `import` utasításokat:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Vegyen fel egy osztályt a *Management. Java* fájlba, majd adja hozzá a következő mezőket és azok értékeit. A létrehozott egyszerű szolgáltatásnév és a többi Azure-fiók adatai alapján feltöltheti értékeit.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_constants)]

Ezután a **Main** metódusban használja ezeket az értékeket egy **CognitiveServicesManager** objektum létrehozásához. Ez az objektum szükséges az összes Azure-felügyeleti művelethez.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_auth)]

## <a name="call-management-methods"></a>Hívás-felügyeleti módszerek

Adja hozzá a következő kódot a **Main** metódushoz a rendelkezésre álló erőforrások listázásához, egy minta erőforrás létrehozásához, a saját tulajdonú erőforrások listázásához, majd a minta erőforrás törléséhez. Ezeket a metódusokat a következő lépésekben fogja meghatározni.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-java"></a>Cognitive Services-erőforrás létrehozása (Java)

Új Cognitive Services erőforrás létrehozásához és előfizetéséhez használja a **create** metódust. Ez a metódus új számlázható erőforrást hoz létre az Ön által beadott erőforráscsoporthoz. Az új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás típusát, valamint az árképzési szintet (vagy SKU-t) és egy Azure-helyet. A következő metódus ezeket argumentumként fogadja el, és létrehoz egy erőforrást.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Válasszon ki egy szolgáltatást és egy díjszabási szintet

Új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás típusát, valamint a kívánt [árképzési szintet](https://azure.microsoft.com/pricing/details/cognitive-services/) (vagy SKU-t). Ezt és egyéb információkat paraméterekként fogja használni az erőforrás létrehozásakor. Az elérhető kognitív szolgáltatások listáját a következő módszer meghívásával érheti el:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Erőforrások megtekintése

Ha az Azure-fiók összes erőforrását (az összes erőforráscsoportot) szeretné megtekinteni, használja a következő metódust:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list)]

## <a name="delete-a-resource"></a>Erőforrás törlése

A következő metódus törli a megadott erőforrást az adott erőforráscsoporthoz.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_delete)]

## <a name="see-also"></a>Lásd még

* Lásd: **[kérelmek hitelesítése az Azure-Cognitive Services](../../authentication.md)** a Cognitive Services biztonságos kezeléséhez.
* Ismerje meg, **[Mi az Azure Cognitive Services?](../../what-are-cognitive-services.md)** a különböző kategóriák listájának lekéréséhez Cognitive Serviceson belül.
* A Cognitive Services által támogatott természetes nyelvek listájának megjelenítéséhez tekintse meg a **[természetes nyelvi támogatás](../../language-support.md)** című témakört.
* Lásd: a **[Cognitive Services használata tárolóként](../../cognitive-services-container-support.md)** a Cognitive Services helyszíni használatának megismeréséhez.
* A Cognitive Services használatának költségeinek **[kiszámításához tekintse meg a Cognitive Services költségeinek tervezése és kezelése](../../plan-manage-costs.md)** című témakört.
* A Management SDK-val kapcsolatos további részletekért tekintse meg az **[Azure Management SDK dokumentációját](/java/api/com.microsoft.azure.management.cognitiveservices)** .
