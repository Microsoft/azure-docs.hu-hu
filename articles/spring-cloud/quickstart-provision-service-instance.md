---
title: Gyors útmutató – Azure Spring Cloud-szolgáltatás kiépítése
description: Leírja, hogyan kell létrehozni az Azure Spring Cloud Service-példányt az alkalmazás telepítéséhez.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 6f25c4172b384abd487d2084f31981d16e73ee93
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879286"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Gyors útmutató: Azure Spring Cloud Service kiépítése

::: zone pivot="programming-language-csharp"
Ebben a rövid útmutatóban az Azure CLI használatával kiépítheti az Azure Spring Cloud Service egy példányát.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Az Azure Spring Cloud Service a .NET Core 3,1-as és újabb verzióit támogatja.
* [Az Azure CLI verziója 2.0.67 vagy újabb](/cli/azure/install-azure-cli).
* [Git](https://git-scm.com/).

## <a name="install-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

Győződjön meg arról, hogy az Azure CLI verziója 2.0.67 vagy újabb:

```azurecli
az --version
```

Telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi paranccsal:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

1. Jelentkezzen be az Azure CLI-be.

    ```azurecli
    az login
    ```

1. Ha egynél több előfizetéssel rendelkezik, válassza ki azt, amelyet ehhez a rövid útmutatóhoz szeretne használni.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Azure Spring Cloud-példány kiépítése

1. Hozzon létre egy [erőforráscsoportot](../azure-resource-manager/management/overview.md) , amely tartalmazza az Azure Spring Cloud Service-t. Az erőforráscsoport neve tartalmazhat alfanumerikus karaktereket, aláhúzást, zárójelet, kötőjelet, pontot (kivéve a végét) és Unicode-karaktert.

   ```azurecli
   az group create --location eastus --name <resource group name>
   ```

1. Az Azure Spring Cloud Service egy példányának kiépítése. A szolgáltatási példány nevének egyedinek kell lennie, 4 – 32 karakter hosszúságú, és csak kisbetűket, számokat és kötőjeleket tartalmazhat. A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    A parancs végrehajtása több percet is igénybe vehet.

1. Állítsa be az alapértelmezett erőforráscsoport-nevet és a szolgáltatási példány nevét, hogy ne kelljen ismételten megadnia ezeket az értékeket a következő parancsokban.

   ```azurecli
   az configure --defaults group=<resource group name>
   ```

   ```azurecli
   az configure --defaults spring-cloud=<service instance name>
   ```
::: zone-end

::: zone pivot="programming-language-java"
Az Azure Spring Cloud a Azure Portal vagy az Azure CLI használatával hozható létre.  Mindkét módszert az alábbi eljárásokban ismertetjük.
## <a name="prerequisites"></a>Előfeltételek

* [A JDK 8 telepítése](/java/azure/jdk/)
* [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)
* Választható [Telepítse az Azure CLI 2.0.67 vagy újabb verzióját](/cli/azure/install-azure-cli) , és telepítse az Azure Spring Cloud bővítményt a paranccsal: `az extension add --name spring-cloud`
* Választható [A Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) és a [Bejelentkezés](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) telepítése

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Azure Spring Cloud-példány kiépítése

#### <a name="portal"></a>[Portál](#tab/Azure-portal)

Az alábbi eljárás az Azure Spring Cloud egy példányát hozza létre a Azure Portal használatával.

1. Az új lapon nyissa meg a [Azure Portal](https://ms.portal.azure.com/). 

2. A felső keresőmezőbe keressen az **Azure Spring Cloud** kifejezésre.

3. Válassza az **Azure Spring Cloud** lehetőséget az eredmények közül.

    ![ASC ikon kezdete](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Az Azure Spring Cloud oldalon kattintson a **+ Hozzáadás** gombra.

    ![ASC ikon hozzáadása](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Töltse ki az űrlapot az Azure Spring Cloud **create** oldalon.  Tekintse meg a következő útmutatót:
    - **Előfizetés**: válassza ki azt az előfizetést, amelyet számlázni szeretne az erőforráshoz.
    - **Erőforráscsoport**: új erőforráscsoportok létrehozása új erőforrásokhoz az ajánlott eljárás. Vegye figyelembe, hogy ezt a későbbi lépések során fogjuk használni **\<resource group name\>** .
    - **Szolgáltatás adatai/neve**: adja meg a **\<service instance name\>** .  A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat.  A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.
    - **Hely**: válassza ki a szolgáltatás példányának helyét.

    ![ASC-portál indítása](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Kattintson az **Áttekintés és létrehozás** elemre.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

Az alábbi eljárás az Azure CLI bővítményt használja az Azure Spring Cloud egy példányának kiépítéséhez.

1. Jelentkezzen be az Azure CLI-be, és válassza ki az aktív előfizetését.

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Készítse elő az Azure Spring Cloud-szolgáltatás nevét.  A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat.  A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.

1. Hozzon létre egy erőforráscsoportot, amely tartalmazza az Azure Spring Cloud Service-t.

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

    Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/management/overview.md) kapcsolatban.

1. Nyisson meg egy Azure CLI-ablakot, és futtassa az alábbi parancsokat az Azure Spring Cloud egy példányának kiépítéséhez.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    A szolgáltatás üzembe helyezése körülbelül öt percet vesz igénybe.
---
::: zone-end

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban olyan Azure-erőforrásokat hozott létre, amelyek továbbra is felhalmozzák a díjakat, ha az előfizetésben maradnak. Ha nem szeretne továbblépni a következő rövid útmutatóra, tekintse meg az [erőforrások tisztítása](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources)című témakört. Ellenkező esetben folytassa a következő rövid útmutatóval:

> [!div class="nextstepaction"]
> [A konfigurációs kiszolgáló beállítása](spring-cloud-quickstart-setup-config-server.md)
