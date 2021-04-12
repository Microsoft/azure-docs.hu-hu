---
title: Oktatóanyag – az áramkör-megszakító irányítópultjának használata az Azure Spring Cloud használatával
description: Ismerje meg, hogyan használhatja az áramkör-megszakító irányítópultját az Azure Spring Cloud használatával.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 09acdc0c4ea2dbe90c0b7b037b6aec501cc1dd55
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223030"
---
# <a name="tutorial-use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>Oktatóanyag: az áramkör-megszakító irányítópultjának használata az Azure Spring Cloud használatával

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

A Spring [Cloud Netflix turbina](https://github.com/Netflix/Turbine) széles körben használatos több [Hystrix](https://github.com/Netflix/Hystrix) mérőszámok összesítésére, így a streamek egyetlen nézetben figyelhetők meg a Hystrix-irányítópult használatával. Ez az oktatóanyag bemutatja, hogyan használhatja őket az Azure Spring Cloud-on.
> [!NOTE]
> A Netflix Hystrix széles körben használatos számos meglévő Spring Cloud-alkalmazásban, de már nem aktív fejlesztés alatt áll. Ha új projektet fejleszt, használja inkább a Spring Cloud Circuit megszakító implementációit, például a [resilience4j](https://github.com/resilience4j/resilience4j)-t. Az oktatóanyagban bemutatott új Spring Cloud Circuit Breaker-keretrendszer a metrikák adatfolyamatának minden implementációját a mikrométerbe egyesíti, amelyet az Azure Spring Cloud is támogat. [További információk](https://docs.microsoft.com/azure/spring-cloud/how-to-circuit-breaker-metrics).

## <a name="prepare-your-sample-applications"></a>A mintául szolgáló alkalmazások előkészítése
A minta a [tárházból](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine)származik.

A minta tárház klónozása a fejlesztői környezetbe:
```
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

Hozza létre az oktatóanyagban használt 3 alkalmazást:
* felhasználói szolgáltatás: egy egyszerű REST-szolgáltatás, amely egyetlen/Personalized/{ID}-végponttal rendelkezik
* javaslat – szolgáltatás: egy egyszerű REST-szolgáltatás, amely egyetlen/Recommendations-végponttal rendelkezik, amelyet a felhasználói szolgáltatás fog meghívni.
* Hystrix-Turbine: egy Hystrix irányítópult-szolgáltatás, amely Hystrix-adatfolyamokat és egy turbina-szolgáltatást jelenít meg, amely más szolgáltatásokból származó Hystrix metrikákat továbbít.
```
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```
## <a name="provision-your-azure-spring-cloud-instance"></a>Azure Spring Cloud-példány kiépítése
Kövesse az eljárást, és hozzon létre [egy Service-példányt az Azure CLI](./spring-cloud-quickstart.md#provision-an-instance-of-azure-spring-cloud)-ben.

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>Alkalmazások üzembe helyezése az Azure Spring Cloud-on
Ezek az alkalmazások nem használják a **konfigurációs kiszolgálót**, így nincs szükség az Azure Spring Cloud-hoz készült **konfigurációs kiszolgáló** beállítására.  Hozza létre és telepítse a következőt:
```azurecli
az spring-cloud app create -n user-service --assign-endpoint
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --assign-endpoint

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```
## <a name="verify-your-apps"></a>Alkalmazások ellenőrzése
Miután az összes alkalmazás fut és felderíthető, elérheti `user-service` a böngészőből a https://-User-Service.azuremicroservices.IO/Personalized/1 elérési utat <username> . Ha a felhasználó hozzáférhet a szolgáltatáshoz `recommendation-service` , a következő kimenetnek kell megjelennie. Frissítse a weblapot néhányszor, ha nem működik.
```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```
## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>Hozzáférés a Hystrix-irányítópulthoz és a metrikák streamhez
Ellenőrizze a nyilvános végpontok vagy a privát tesztelési végpontok használatát.

### <a name="using-public-endpoints"></a>Nyilvános végpontok használata
Hozzáférés a Hystrix-turbinához a böngésző elérési útjával `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/hystrix` .  Az alábbi ábra az alkalmazásban futó Hystrix-irányítópultot mutatja be.

![Hystrix-irányítópult](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

Másolja a turbina stream URL-címét `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default` a szövegmezőbe, majd kattintson az **adatfolyam figyelése** lehetőségre.  Ekkor megjelenik az irányítópult. Ha semmi sem jelenik meg a megjelenítőben, nyomja `user-service` meg a végpontokat a streamek létrehozásához.

![A Hystrix stream ](media/spring-cloud-circuit-breaker/hystrix-stream.png) mostantól az áramkör-megszakító irányítópultján is kísérletezhet.
> [!NOTE] 
> Éles környezetben a Hystrix-irányítópult és a metrikák adatfolyama nem lehet elérhető az interneten.

### <a name="using-private-test-endpoints"></a>Privát tesztelési végpontok használata
A Hystrix metrikái streamek is elérhetők innen: `test-endpoint` . Háttérbeli szolgáltatásként nem rendelek hozzá nyilvános végpontot a ( `recommendation-service` z) rendszerhez, de a tesztelési végponttal megjelenítheti a metrikákat `https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream`

![Hystrix test – Endpoint Stream](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

Webalkalmazásként a Hystrix irányítópultjának kell működnie `test-endpoint` . Ha nem működik megfelelően, két ok lehet: először a (z) vagy a `test-endpoint` (z) alapurl-címének módosítása a `/ to /<APP-NAME>/<DEPLOYMENT-NAME>` (z) vagy a másodperc alapján, a webalkalmazás a statikus erőforrás abszolút elérési útját használja. A működésének megkezdéséhez `test-endpoint` Előfordulhat, hogy manuálisan kell szerkesztenie az <base> előtér-fájlokat.

## <a name="next-steps"></a>Következő lépések
* [Szolgáltatási példány kiépítése az Azure CLI-ben](spring-cloud-quickstart.md#provision-an-instance-of-azure-spring-cloud)
* [Java Spring-alkalmazás előkészítése az Azure Spring Cloud üzembe helyezéséhez](how-to-prepare-app-deployment.md)

