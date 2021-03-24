---
title: Gyors útmutató – Azure Spring Cloud konfigurációs kiszolgáló beállítása
description: Leírja, hogyan állítható be az Azure Spring Cloud config Server az alkalmazás telepítéséhez.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 643d1cd6df3791b2e5ea2118425eecb29dbcdea2
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879282"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Gyors útmutató: az Azure Spring Cloud konfigurációs kiszolgáló beállítása

Az Azure Spring Cloud config Server egy központi konfigurációs szolgáltatás az elosztott rendszerek számára. Egy olyan csatlakoztatható adattárház-réteget használ, amely jelenleg támogatja a helyi tárolást, a git-t és az alverziót. Ebben a rövid útmutatóban úgy állíthatja be a konfigurációs kiszolgálót, hogy a git-tárházból olvassa be az adatgyűjtést.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>Előfeltételek

* Fejezze be az előző rövid útmutatót ebben a sorozatban: [Azure Spring Cloud Service kiépítése](spring-cloud-quickstart-provision-service-instance.md).

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud config Server-eljárások

Állítsa be a konfigurációs kiszolgálóját a projekt git-tárházának helyére a következő parancs futtatásával. Cserélje le a `<service instance name>` nevet a korábban létrehozott szolgáltatás nevére. Az előző rövid útmutatóban beállított szolgáltatási példány nevének alapértelmezett értéke nem működik ezzel a paranccsal.

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

Ez a parancs arra utasítja a konfigurációs kiszolgálót, hogy a [steeltoe-Sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) mappában keresse meg a konfigurációs adattárolót. Mivel annak az alkalmazásnak a neve, amely a konfigurációs adatmennyiséget kapja `planet-weather-provider` , a használni kívánt fájl a [Planet-Weather-Provider. YML](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml)lesz.

::: zone-end

::: zone pivot="programming-language-java"
Az Azure Spring Cloud config Server az elosztott rendszerek központi konfigurációs szolgáltatása. Egy olyan csatlakoztatható adattárház-réteget használ, amely jelenleg támogatja a helyi tárolást, a git-t és az alverziót.  A konfigurációs kiszolgáló beállítása a Service-alkalmazások Azure Spring Cloud-ba való üzembe helyezésére.

## <a name="prerequisites"></a>Előfeltételek

* [A JDK 8 telepítése](/java/azure/jdk/)
* [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)
* Választható [Telepítse az Azure CLI 2.0.67 vagy újabb verzióját](/cli/azure/install-azure-cli) , és telepítse az Azure Spring Cloud bővítményt a paranccsal: `az extension add --name spring-cloud`
* Választható [A Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) és a [Bejelentkezés](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) telepítése

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud config Server-eljárások

#### <a name="portal"></a>[Portál](#tab/Azure-portal)

Az alábbi eljárással állítható be a konfigurációs kiszolgáló a Azure Portal használatával a [Piggymetrics-minta](spring-cloud-quickstart-sample-app-introduction.md)telepítéséhez.

1. Lépjen a szolgáltatás **áttekintése** lapra, és válassza a **konfigurációs kiszolgáló** lehetőséget.

2. Az **alapértelmezett adattár** szakaszban állítsa be az **URI** -t "" értékre https://github.com/Azure-Samples/piggymetrics-config .

3. Kattintson az **Érvényesítés** elemre.

    ![Navigáljon a konfigurációs kiszolgálóhoz](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

4. Ha az ellenőrzés befejeződött, kattintson az **alkalmaz** gombra a módosítások mentéséhez.

    ![A konfigurációs kiszolgáló érvényesítése](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

5. A konfiguráció frissítése néhány percet is igénybe vehet.
 
    ![Konfigurációs kiszolgáló frissítése](media/spring-cloud-quickstart-launch-app-portal/updating-config.png) 

6. A konfigurálás befejeződése után értesítést kell kapnia.

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

Az alábbi eljárás az Azure CLI használatával állítja be a konfigurációs kiszolgálót a [Piggymetrics-minta](spring-cloud-quickstart-sample-app-introduction.md)üzembe helyezéséhez.

Állítsa be a konfigurációs kiszolgálóját a projekt git-tárházának helyére:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```
---
::: zone-end

> [!TIP]
> Ha privát tárházat használ a konfigurációs kiszolgálóhoz, tekintse [meg a hitelesítés beállításával foglalkozó oktatóanyagot](./spring-cloud-howto-config-server.md).

## <a name="troubleshooting-of-azure-spring-cloud-config-server"></a>Az Azure Spring Cloud config Server hibaelhárítása

Az alábbi eljárás a konfigurációs kiszolgáló beállításainak hibakeresését ismerteti.

1. A Azure Portal lépjen a szolgáltatás **áttekintése** lapra, és válassza a **naplók** lehetőséget. 
1. Válassza a **lekérdezések** lehetőséget, és **jelenítse meg a "hiba" vagy "kivétel" kifejezést tartalmazó alkalmazás-naplókat**. 
1. Kattintson a **Futtatás** elemre. 
1. Ha a következő hibaüzenetet találja: **Java. lang. illegalStateException** a naplókban, ez azt jelzi, hogy a Spring Cloud Service nem találja a tulajdonságokat a konfigurációs kiszolgálóról.

    [![Asc-portál – lekérdezés ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png) futtatása](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png)

1. Nyissa meg a szolgáltatás **áttekintése** lapot.
1. Válassza a **Problémák diagnosztizálása és megoldása** lehetőséget. 
1. Válassza a **konfigurációs kiszolgáló** detektor lehetőséget.

    [![Asc-portál – ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png) problémák diagnosztizálása](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png)

3. Kattintson a **konfigurációs kiszolgáló állapotának ellenõrzése** lehetőségre.

    [![Asc-portál Genie ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png)](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png)

4. Kattintson a **konfigurációs kiszolgáló állapota** lehetőségre a detektor további részleteinek megtekintéséhez.

    [![Asc-portál állapotának állapota ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png)](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban olyan Azure-erőforrásokat hozott létre, amelyek továbbra is felhalmozzák a díjakat, ha az előfizetésben maradnak. Ha nem szeretne továbblépni a következő rövid útmutatóra, tekintse meg az [erőforrások tisztítása](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources)című témakört. Ellenkező esetben folytassa a következő rövid útmutatóval:

> [!div class="nextstepaction"]
> [Alkalmazások készítése és üzembe helyezése](spring-cloud-quickstart-deploy-apps.md)