---
title: Rövid útmutató – a minta alkalmazás bemutatása – Azure Spring Cloud
description: A jelen útmutatóban használt példa az Azure Spring Cloud üzembe helyezéséhez.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: dd36bb18e84ea299195b77286887a3b279f81469
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879283"
---
# <a name="introduction-to-the-sample-app"></a>A mintaalkalmazás bemutatása

::: zone pivot="programming-language-csharp"
Ez a sorozat a gyors üzembe helyezési útmutató két összetevőből áll, amelyekből megtudhatja, hogyan helyezhet üzembe egy .NET Core Steeltoe-alkalmazást az Azure Spring Cloud Service-ben. Az Azure Spring Cloud képességeit, például a Service Discovery, a konfigurációs kiszolgáló, a naplók, a metrikák és az elosztott nyomkövetés használatát fogja használni.

## <a name="functional-services"></a>Funkcionális szolgáltatások

A minta alkalmazás két szolgáltatásból áll:

* A `planet-weather-provider` szolgáltatás a bolygó nevét MEGADÓ http-kérelemre adott válaszként egy időjárási szöveget ad vissza. Előfordulhat például, hogy a Planet Mercury "nagyon meleg" értéket ad vissza. A konfigurációs kiszolgáló időjárási adatait kéri le. A konfigurációs kiszolgáló beolvassa a git-tárházban található YAML-fájl időjárási adatait, például:

  ```yaml
  MercuryWeather: very warm
  VenusWeather: quite unpleasant
  MarsWeather: very cool
  SaturnWeather: a little bit sandy
  ```

* A `solar-system-weather` szolgáltatás egy http-kérelemre válaszul négy bolygóra vonatkozó adatokkal tér vissza. A szolgáltatás négy HTTP-kérelem beszerzésével kéri le az adatgyűjtést `planet-weather-provider` . Az Eureka Server Discovery szolgáltatást használja a híváshoz `planet-weather-provider` . A JSON-t adja vissza, például:

  ```json
  [{
      "Key": "Mercury",
      "Value": "very warm"
  }, {
      "Key": "Venus",
      "Value": "quite unpleasant"
  }, {
      "Key": "Mars",
      "Value": "very cool"
  }, {
      "Key": "Saturn",
      "Value": "a little bit sandy"
  }]
  ```

Az alábbi ábra a minta alkalmazás architektúráját szemlélteti:

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="Példa az alkalmazás diagramra":::

## <a name="code-repository"></a>Kódtár

A minta alkalmazás az Azure-Samples/Azure-Spring-Cloud-Samples adattár [steeltoe](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) található a githubon.

A következő rövid útmutatók utasításai szükség szerint a forráskódra vonatkoznak.

::: zone-end

::: zone pivot="programming-language-java"
Ebben a rövid útmutatóban egy PiggyMetrics nevű személyes pénzügyi mintát használunk, amely bemutatja, hogyan helyezhet üzembe egy alkalmazást az Azure Spring Cloud Service-ben. A PiggyMetrics bemutatja a Service architektúrájának mintáját, és kiemeli a szolgáltatások részletezését. Megtudhatja, hogyan helyezi üzembe az Azure-ban hatékony Azure Spring Cloud-funkciókkal, beleértve a Service Discovery, a konfigurációs kiszolgáló, a naplók, a metrikák és az elosztott nyomkövetést.

Az Azure Spring Cloud telepítési példáinak követéséhez csak a forráskód helyét kell megadnia, amelyet igény szerint kell biztosítani.

## <a name="functional-services"></a>Funkcionális szolgáltatások

A PiggyMetrics három Core-szolgáltatásból áll. Mindegyik az üzleti tartományok által rendezett, egymástól függetlenül telepíthető alkalmazás.

* **Szolgáltatásfiók (központilag telepítendő)**: általános felhasználói bemeneti logikát és érvényesítést tartalmaz: az adatbevitelek/kiadások elemei, megtakarítások és Fiókbeállítások.
* **Statisztikai szolgáltatás (ebben a rövid útmutatóban nem használható)**: számításokat végez a főbb statisztikai paramétereken, és rögzíti az idősorozatokat az egyes fiókokhoz. A Datapoint az alappénznemre és az időszakra normalizált értékeket tartalmaz. Ezek az adatforgalom a pénzforgalmi dinamika a fiók élettartamában való nyomon követésére szolgálnak.
* **Értesítési szolgáltatás (ebben a rövid útmutatóban nem használható): a** felhasználók kapcsolattartási adatait és értesítési beállításait, például az emlékeztetőket és a biztonsági mentés gyakoriságát tárolja. Az ütemezett feldolgozó más szolgáltatásokból gyűjti a szükséges információkat, és e-mail-üzeneteket küld az előfizetett ügyfeleknek.

## <a name="infrastructure-services"></a>Infrastruktúra-szolgáltatások

Az elosztott rendszerekben számos gyakori minta van, amelyek segítenek az alapvető szolgáltatások működésében. Az Azure Spring Cloud hatékony eszközöket biztosít a Spring boot-alkalmazások működésének növeléséhez a minták megvalósításához: 

* **Konfigurációs szolgáltatás (az Azure Spring Cloud által üzemeltetett)**: az Azure Spring Cloud config egy horizontálisan méretezhető központi konfigurációs szolgáltatás az elosztott rendszerek számára. Egy olyan csatlakoztatható tárházat használ, amely jelenleg támogatja a helyi tárolást, a git-t és az alverziót.
* **Service Discovery (az Azure Spring Cloud által üzemeltetett)**: lehetővé teszi a hálózati telephelyek automatikus észlelését a szolgáltatási példányok számára, ami dinamikusan hozzárendelt címeket tartalmazhat az automatikus skálázás, a hibák és a frissítések miatt.
* **Hitelesítési szolgáltatás (telepítendő)** Az engedélyezési felelősségeket a rendszer teljesen kibontja egy különálló kiszolgálóra, amely OAuth2 jogkivonatokat biztosít a háttérbeli erőforrás-szolgáltatásokhoz. Az Auth-kiszolgáló a felhasználói hitelesítést és a számítógép és a gép közötti kommunikációt is biztosítja a peremhálózaton belül.
* **API Gateway (telepítendő)**: a három alapvető szolgáltatás külső API-t tesz elérhetővé az ügyfél számára. A valós rendszerekben a függvények száma nagyon gyorsan, a rendszer bonyolultságával növelhető. Egy összetett weblap megjelenítéséhez több száz szolgáltatás is kapcsolódhat. Az API-átjáró a rendszer egyetlen belépési pontja, amely a kérelmek kezelésére szolgál, és továbbítja azokat a megfelelő háttér-szolgáltatáshoz, vagy több háttér-szolgáltatást hív meg, így összesítve az eredményeket. 

## <a name="sample-usage-of-piggymetrics"></a>Példa a PiggyMetrics használatára

A teljes körű megvalósítás részleteiért lásd: [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics). A minták szükség szerint hivatkoznak a forráskódra.
::: zone-end

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Spring Cloud-példány kiépítése](spring-cloud-quickstart-provision-service-instance.md)
