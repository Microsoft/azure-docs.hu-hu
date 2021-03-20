---
title: Az Azure Cognitive Services fejlesztői lehetőségei
description: Ismerje meg, hogyan használható az Azure Cognitive Services különböző fejlesztési és üzembe helyezési lehetőségekkel, mint például az ügyféloldali kódtárak, a REST API-k, a Logic Apps, az automatizálás, a Azure Functions, a Azure App Service, a Azure Databricks és sok más.
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 975f7eec31839aefcb1782f573d0210df29a4d00
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98873569"
---
# <a name="cognitive-services-development-options"></a>Cognitive Services – fejlesztési lehetőségek

Ez a dokumentum áttekintést nyújt a fejlesztési és üzembe helyezési lehetőségekről az Azure Cognitive Services használatának megkezdése érdekében.

Az Azure Cognitive Services felhőalapú AI-szolgáltatások, amelyek lehetővé teszik a fejlesztők számára, hogy a gépi tanulás alapos ismerete nélkül építsenek intelligenciát alkalmazásaiba és termékeibe. A Cognitive Services segítségével hozzáférhet a Microsoft által készített, betanított és frissített AI-képességekhez vagy-modellekhez, amelyek az alkalmazásokban való használatra készen állnak. Számos esetben lehetősége van arra is, hogy testreszabja az üzleti igényeinek megfelelő modelleket. 

A Cognitive Services négy kategóriába vannak rendezve: döntés, nyelv, beszéd és jövőkép. Ezeket a szolgáltatásokat általában REST API-k, ügyféloldali kódtárak és egyéni eszközök (például a Microsoft által biztosított parancssori felületek) segítségével érheti el. Azonban ez csak egy elérési út a sikerhez. Az Azure-on keresztül számos fejlesztési lehetőség is elérhető, például:

* Automatizálási és integrációs eszközök, mint például a Logic Apps és a Power automatizálása.
* Az üzembe helyezési lehetőségek, például a Azure Functions és a App Service. 
* Cognitive Services Docker-tárolókat a biztonságos hozzáféréshez.
* Olyan eszközök, mint például a Apache Spark, a Azure Databricks, az Azure szinapszis Analytics és az Azure Kubernetes szolgáltatás Big-adatokhoz. 

Mielőtt beugorjunk, fontos tudni, hogy a Cognitive Services elsődlegesen két különálló feladathoz használják. A végrehajtani kívánt feladat alapján különböző fejlesztési és üzembe helyezési lehetőségek közül választhat. 

* [Fejlesztési lehetőségek az előrejelzéshez és az elemzéshez](#development-options-for-prediction-and-analysis)
* [Modellek testreszabására és konfigurálására szolgáló eszközök](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>Fejlesztési lehetőségek az előrejelzéshez és az elemzéshez 

A modellek testreszabásához és konfigurálásához használt eszközök különböznek a Cognitive Services meghívásához használni kívánt eszközökkel. A legtöbb Cognitive Services lehetővé teszi az adatküldést és az elemzések fogadását testreszabás nélkül. Például: 

* Képeket küldhet a Computer Vision szolgáltatásnak a szavak és kifejezések észlelésére vagy a keretben lévő személyek számának megszámlálására.
* Elküldhet egy hangfájlt a beszédfelismerési szolgáltatásba, és átadhatja a szöveget, és lefordíthatja a beszédet egy időben.
* Küldhet egy PDF-fájlt az űrlap-felismerő szolgáltatásba, és felderítheti a cellákban található táblázatokat, cellákat és szöveget, és a JSON-kimenetet koordinátákkal és részletekkel látja el

Az Azure számos olyan eszközt kínál, amelyek különböző típusú felhasználók számára készültek, amelyek közül sokat használhat a Cognitive Services. A tervező által vezérelt eszközök a legkönnyebben használhatók, és gyorsan beállítható és automatizálható, de előfordulhat, hogy a Testreszabás során korlátozások vannak érvényben. A REST API-k és az ügyféloldali kódtárak nagyobb fokú irányítást és rugalmasságot biztosítanak a felhasználóknak, de több erőfeszítést, időt és szakértelmet igényelnek a megoldások létrehozásához. Ha REST API-kat és ügyféloldali kódtárakat használ, a várt érték a modern programozási nyelvek, például a C#, a Java, a Python, a JavaScript vagy más népszerű programozási nyelv használata. 

Vessünk egy pillantást a Cognitive Services használható különböző módokra.

### <a name="client-libraries-and-rest-apis"></a>Ügyfélkódtárak és REST API-k

Cognitive Services ügyféloldali kódtárak és REST API-k biztosítják a szolgáltatás közvetlen elérését. Ezek az eszközök programozott hozzáférést biztosítanak a Cognitive Serviceshoz, az alapmodellekhez, és számos esetben lehetővé teszik a modellek és megoldások programozott testreszabását. 

* **Célként megadott felhasználó (k)**: fejlesztők és adatszakértők
* **Előnyök**: a lehető legnagyobb rugalmasságot biztosítja a szolgáltatások bármilyen nyelven és környezetben való meghívásához. 
* **Felhasználói felület**: csak N/A-Code
* **Előfizetés (ek)**: Azure-fiók + Cognitive Services erőforrások

Ha többet szeretne megtudni a rendelkezésre álló ügyféloldali kódtárak és a REST API-król, használja a [Cognitive Services áttekintést](index.yml) a kiválasztáshoz és a szolgáltatáshoz, és ismerkedjen meg az első lépések egyikével a látás, a döntés, a nyelv és a beszéd számára.

### <a name="cognitive-services-for-big-data"></a>Cognitive Services big data jellegű adatokhoz

A Big adatok Cognitive Services a folyamatosan fejleszthető, intelligens modellek közvetlenül Apache Spark &trade; és SQL-számításokban is beágyazható. Ezek az eszközök felszabadítják a fejlesztőket az alacsony szintű hálózatkezelési részletektől, hogy az intelligens, elosztott alkalmazások létrehozására összpontosítsanak. A Big reCognitive Services a következő platformokat és összekötőket támogatja: Azure Databricks, Azure szinapszis, Azure Kubernetes Service és adatösszekötők.

* **Cél felhasználó (k)**: adatszakértők és adatmérnökök
* **Előnyök**: a Big User Azure-Cognitive Services lehetővé teszi a felhasználók számára, hogy a Apache Spark használatával Cognitive Services az adatcsatornákat &trade; . Könnyen létrehozhat nagyméretű intelligens alkalmazásokat bármilyen adattárral.
* **Felhasználói felület**: csak N/A-Code
* **Előfizetés (ek)**: Azure-fiók + Cognitive Services erőforrások

Ha többet szeretne megtudni a Cognitive Services Big-adatainak megismeréséről, érdemes megtekinteni az [áttekintést](./big-data/cognitive-services-for-big-data.md). Ha készen áll a létrehozás megkezdésére, próbálja ki a [Python](./big-data/samples-python.md) -és a [Scala](./big-data/samples-scala.md) -mintákat.

### <a name="azure-functions-and-azure-service-web-jobs"></a>Azure Functions-és Azure-szolgáltatások webes feladatai

[Azure functions](../azure-functions/index.yml) és [Azure app Service webes feladatok](../app-service/index.yml) egyaránt biztosítanak a fejlesztők számára tervezett és az [Azure app Services](../app-service/index.yml)-ra épülő, első szintű integrációs szolgáltatásokat. Ezek a termékek kiszolgáló nélküli infrastruktúrát biztosítanak a kód írásához. Ebben a kódban a szolgáltatásainkat az ügyféloldali kódtárak és a REST API-k használatával hívhatja. 

* **Célként megadott felhasználó (k)**: fejlesztők és adatszakértők
* **Előnyök**: kiszolgáló nélküli számítási szolgáltatás, amely lehetővé teszi az esemény által aktivált kód futtatását. 
* **Felhasználói felület**: igen
* **Előfizetés (ek)**: Azure-fiók + Cognitive Services erőforrás + Azure functions előfizetés

### <a name="azure-logic-apps"></a>Azure Logic Apps 

[Azure Logic apps](../logic-apps/index.yml) ugyanazokat a munkafolyamat-tervezőket és összekötőket használja, mint a Power automatizálás, de fejlettebb és felügyelhető, beleértve a Visual Studióval és a DevOps való integrációt. A kibővített automatizálással egyszerűen integrálható a kognitív szolgáltatások erőforrásaiba olyan szolgáltatás-specifikus összekötők használatával, amelyek proxyt vagy burkolót biztosítanak az API-k körül. Ezek ugyanazok az összekötők, mint a Power automatizáló szolgáltatásban elérhetők. 

* **Cél felhasználó (k)**: fejlesztők, integrátorok, informatikai szakemberek, DevOps
* **Előnyök**: tervező-első (deklaratív) fejlesztési modell, amely speciális lehetőségeket és integrációt biztosít egy alacsony kódú megoldásban
* **Felhasználói felület**: igen
* **Előfizetés (ek)**: Azure-fiók + Cognitive Services erőforrás + Logic apps üzembe helyezés

### <a name="power-automate"></a>Power Automate 

A Power [platform](/power-platform/) automatizálási szolgáltatása lehetővé teszi az alkalmazások és szolgáltatások közötti automatizált munkafolyamatok létrehozását kód írása nélkül. Több összekötőt is kínálunk, amelyekkel könnyen dolgozhat a Cognitive Services-erőforrással a Power automatizáló megoldásban. A Power automatizálás Logic Appsra épül. 

* **Célként megadott felhasználó (k)**: üzleti felhasználók (elemzők) és SharePoint-rendszergazdák
* **Előnyök**: automatizálja az ismétlődő manuális feladatokat egyszerűen az egérkattintások rögzítésével, a billentyűleütések és a beillesztési lépések az asztalról való másolásával.
* **Felhasználói felületi eszközök**: igen – felhasználói felület
* **Előfizetés (ek)**: Azure-fiók + Cognitive Services erőforrás + energiagazdálkodási automatizálási előfizetés + Office 365 előfizetés

### <a name="ai-builder"></a>AI Builder 

A [AI Builder](/ai-builder/overview) egy Microsoft Power platform-képesség, amellyel javítható az üzleti teljesítmény a folyamatok automatizálásával és az eredmények előrejelzésével. Az AI-szerkesztő egy pont-és kattintásos élményt nyújt a mesterséges intelligenciának a megoldásaihoz. Számos kognitív szolgáltatás, például az űrlap-felismerő, a Text Analytics és az Computer Vision közvetlenül integráltak itt, és nem kell saját Cognitive Services létrehoznia. 

* **Célként megadott felhasználó (k)**: üzleti felhasználók (elemzők) és SharePoint-rendszergazdák
* **Előnyök**: egy kulcsrakész megoldás, amely egy pont-és kattintásos élményt nyújt a mesterséges intelligenciához. Nincs szükség kódolási vagy adatelemzési szaktudásra.
* **Felhasználói felületi eszközök**: igen – felhasználói felület
* **Előfizetés (ok)**: Ai-szerkesztő

### <a name="continuous-integration-and-deployment"></a>Folyamatos integráció és üzembe helyezés

Az Azure DevOps és a GitHub-műveletek használatával kezelheti a központi telepítéseket. Az alább tárgyalt [szakaszban](#continuous-integration-and-delivery-with-devops-and-github-actions) két példa van a CI/CD-integrációra a beszédfelismeréshez és a Language UNDERSTANDING (Luis) szolgáltatáshoz tartozó egyéni modellek betanításához és üzembe helyezéséhez. 

* **Cél felhasználó (k)**: fejlesztők, adatszakértők és adatmérnökök
* **Előnyök**: lehetővé teszi az alkalmazások és modellek programozott módon történő folyamatos módosítását, frissítését és üzembe helyezését. Jelentős előnyökkel jár, ha rendszeresen használja az adatait, hogy javítsa és frissítse a modelleket a beszéd, a jövőkép, a nyelv és a döntés tekintetében. 
* **Felhasználói felületi eszközök**: csak N/A-Code 
* **Előfizetés (ek)**: Azure-fiók + Cognitive Services erőforrás + GitHub-fiók

## <a name="tools-to-customize-and-configure-models"></a>Modellek testreszabására és konfigurálására szolgáló eszközök

Amikor az Cognitive Services segítségével fejleszti az alkalmazást vagy munkafolyamatot, akkor előfordulhat, hogy testre kell szabnia a modellt a kívánt teljesítmény eléréséhez. Számos szolgáltatásunk lehetővé teszi, hogy az előre elkészített modelleken felül az adott üzleti igényeknek megfelelően építsen. A testre szabható szolgáltatásokhoz a folyamaton áthaladó, valamint a kód vezérelt képzéshez API-kkal is biztosítunk KEZELŐFELÜLETen alapuló élményt. Például:

* Olyan Custom Speech modellt szeretne betanítani, amely helyesen ismeri fel az orvosi kifejezéseket a Word Error Rate (WER) használatával 3% alatt
* Olyan Custom Vision képet szeretne készíteni, amely a tűlevelű és a lombhullató fák közötti különbséget tudja megállapítani
* Egyéni neurális hangvételt szeretne készíteni személyes hangadataival a továbbfejlesztett automatizált felhasználói élmény érdekében

A modellek betanításához és konfigurálásához használt eszközök különböznek a Cognitive Services meghívásához használni kívánt eszközökkel. A testreszabást támogató Cognitive Services sok esetben olyan portálokat és felhasználói felületi eszközöket biztosítanak, amelyek segítik a modellek betanítását, kiértékelését és üzembe helyezését. Vessünk egy pillantást néhány lehetőségre:<br><br>

| Pillér | Szolgáltatás | Testreszabási felhasználói felület | Gyorsútmutató |
|--------|---------|------------------|------------|
| Látás | Custom Vision | https://www.customvision.ai/ | [Gyors útmutató](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| Látás | Form Recognizer | [Minta címkéző eszköz](https://fott-preview.azurewebsites.net/) | [Gyors útmutató](./form-recognizer/quickstarts/label-tool.md?tabs=v2-0) |
| Döntés | Content Moderator | https://contentmoderator.cognitive.microsoft.com/dashboard | [Gyors útmutató](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| Döntés | Metrics Advisor | https://metricsadvisor.azurewebsites.net/  | [Gyors útmutató](./metrics-advisor/quickstarts/web-portal.md) |
| Döntés | Personalizer | A felhasználói felület elérhető a Azure Portal személyre szabott erőforrása alatt. | [Gyors útmutató](./personalizer/quickstart-personalizer-sdk.md) |
| Nyelv | Language Understanding (LUIS) | https://www.luis.ai/ | |
| Nyelv | QnA Maker | https://www.qnamaker.ai/ | [Gyors útmutató](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| Nyelv | Translator/Custom Translator | https://portal.customtranslator.azure.ai/ | [Gyors útmutató](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| Speech | Custom Commands | https://speech.microsoft.com/ | [Gyors útmutató](./speech-service/custom-commands.md) |
| Speech | Custom Speech | https://speech.microsoft.com/ | [Gyors útmutató](./speech-service/custom-speech-overview.md) |
| Speech | Egyéni hang | https://speech.microsoft.com/ | [Gyors útmutató](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>Folyamatos integráció és kézbesítés DevOps-és GitHub-műveletekkel

Language Understanding és a beszédfelismerési szolgáltatás folyamatos integrációs és folyamatos üzembe helyezési megoldásokat kínál, amelyeket az Azure DevOps és a GitHub-műveletek működtetnek. Ezek az eszközök az egyéni modellek automatizált betanítási, tesztelési és kiadási felügyeletére használhatók. 

* [CI/CD a Custom Speechhez](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [CI/CD – LUIS](./luis/luis-concept-devops-automation.md)

## <a name="on-prem-containers"></a>Helyszíni tárolók 

A Cognitive Servicesek többsége tárolókban is üzembe helyezhető helyszíni hozzáférés és használat céljából. Ezen tárolók használatával rugalmasan Cognitive Services a megfelelőségi, biztonsági vagy egyéb működési okokból az adataihoz. Cognitive Services tárolók teljes listájáért tekintse [meg a Cognitive Services helyszíni tárolói](./cognitive-services-container-support.md)című témakört.

## <a name="next-steps"></a>Következő lépések
<!--
* Learn more about low code development options for Cognitive Services -->
* [Cognitive Services-erőforrás létrehozása és a létrehozás megkezdése](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)
