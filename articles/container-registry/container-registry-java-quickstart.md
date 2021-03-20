---
title: Gyors útmutató – Java-tárolók lemezképének létrehozása és leküldése Azure Container Registry a Maven és a gém használatával
description: Hozzon létre egy tárolóban lévő Java-alkalmazást, és küldje el Azure Container Registry a Maven gém beépülő modullal.
author: KarlErickson
ms.custom: devx-track-java, devx-track-azurecli
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: 9e400ee0bae2690a84f9cfd3f6a76359e08eabc2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92018338"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Gyors útmutató: Java-tárolók rendszerképének létrehozása és leküldése Azure Container Registry

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Java-alkalmazást, és hogyan küldheti el Azure Container Registry a Maven gém beépülő modullal. A Maven és a gém használatának egyik példája a fejlesztői eszközök használata az Azure Container registrytel való interakcióhoz.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial).
* Az [Azure Command-Line felülete (CLI)](/cli/azure/overview).
* Egy támogatott Java fejlesztői készlet (JDK). További információ az Azure-beli fejlesztéshez rendelkezésre álló JDK-król: <https://aka.ms/azure-jdks>.
* Apache [Maven](http://maven.apache.org) Build eszköz (3. vagy újabb verzió).
* Egy [Git](https://git-scm.com)-ügyfél.
* Egy [Docker](https://www.docker.com)-ügyfél.
* Az [ACR Docker hitelesítő adatainak segítője](https://github.com/Azure/acr-docker-credential-helper).

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>A Spring Boot on Docker – Első lépések webalkalmazás létrehozása

A következő lépések végigvezetik egy Spring Boot-webalkalmazás összeállításán és helyszíni tesztelésén.

1. A parancssorban a következő parancs használatával klónozott a [Spring boot a docker első lépések](https://github.com/spring-guides/gs-spring-boot-docker) Sample projektben.

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Váltsa a könyvtárat a befejezett projektre.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Használja a Mavent a mintaalkalmazás összeállításához és futtatásához.

   ```bash
   mvn package spring-boot:run
   ```

1. Tesztelje a webalkalmazást a `http://localhost:8080` cím megnyitásával vagy az alábbi `curl` paranccsal:

   ```bash
   curl http://localhost:8080
   ```

A következő üzenetnek kell megjelennie: **Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Azure Container Registry létrehozása az Azure CLI-vel

Ezután létre kell hoznia egy Azure-erőforráscsoportot és az ACR-t az alábbi lépések végrehajtásával:

1. Jelentkezzen be az Azure-fiókjába a következő parancs használatával:

   ```azurecli
   az login
   ```

1. Válassza ki a használni kívánt Azure-előfizetést:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Hozzon létre egy erőforráscsoportot az oktatóanyagban használt Azure-erőforrások számára. A következő parancsban mindenképp cserélje le a helyőrzőket a saját erőforrás nevére és egy olyan helyre, mint például a (z `eastus` ).

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Hozzon létre egy privát Azure Container registryt az erőforráscsoporthoz az alábbi parancs használatával. Ügyeljen arra, hogy a helyőrzőket a tényleges értékekkel cserélje le. Az oktatóanyag a mintaalkalmazást Docker-lemezképként küldi le ennek a regisztrációs adatbázisnak a későbbi lépésekben.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Az alkalmazás leküldése a regisztrációs adatbázisnak a Jibbel

Végezetül frissítse a projekt konfigurációját, és a parancssor használatával hozza létre és telepítse a lemezképet.

> [!NOTE]
> Az imént létrehozott Azure Container registrybe való bejelentkezéshez szüksége lesz a Docker-démon futtatására. A Docker a gépen való telepítéséhez [itt látható a hivatalos Docker-dokumentáció](https://docs.docker.com/install/).

1. Jelentkezzen be a Azure Container Registryba az Azure CLI-vel az alábbi parancs használatával. A helyőrzőt cserélje le a saját beállításjegyzék-nevére.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   A `az configure` parancs az alapértelmezett beállításjegyzék-nevet állítja be a `az acr` parancsokkal való használatra.

1. Lépjen a Spring Boot-alkalmazás befejezett projektkönyvtárába (például „*C:\SpringBoot\gs-spring-boot-docker\complete*” vagy „*/users/robert/SpringBoot/gs-spring-boot-docker/complete*”), és nyissa meg a *pom.xml* fájlt egy szövegszerkesztővel.

1. Frissítse a `<properties>` gyűjteményt a *pom.xml* fájlban a következő XML-fájllal. Cserélje le a helyőrzőt a beállításjegyzék nevére, és adjon hozzá egy `<jib-maven-plugin.version>` tulajdonságot értékkel `2.2.0` , vagy a [gém-Maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)újabb verzióját.

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. Frissítse a `<plugins>` gyűjteményt a *pom.xml* fájlban, hogy a `<plugin>` elem tartalmazza és a bejegyzést a `jib-maven-plugin` következő példában látható módon. Vegye figyelembe, hogy a Microsoft Container Registryból (MCR) származó alaprendszerképet használunk: `mcr.microsoft.com/java/jdk:8-zulu-alpine` , amely az Azure-hoz hivatalosan támogatott JDK-t tartalmaz. A hivatalosan támogatott JDK rendelkező más MCR-alaplemezképek esetében lásd: [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE fej nélküli JRE](https://hub.docker.com/_/microsoft-java-jre-headless)és [Java SE JDK és Maven](https://hub.docker.com/_/microsoft-java-maven).

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Lépjen a Spring Boot-alkalmazás befejezett projektkönyvtárába, és futtassa a következő parancsot a lemezkép elkészítéséhez, majd a könyvtárba való leküldéséhez:

   ```bash
   az acr login && mvn compile jib:build
   ```

> [!NOTE]
>
> Biztonsági okokból a által létrehozott hitelesítő adatok `az acr login` csak 1 órára érvényesek. Ha *401 jogosulatlan* hibaüzenetet kap, akkor `az acr login -n <your registry name>` újbóli hitelesítéshez futtathatja a parancsot.

## <a name="verify-your-container-image"></a>A tároló rendszerképének ellenőrzése

Gratulálunk! Most már telepítette az ACR-be az Azure által támogatott JDK-t. Most tesztelheti a rendszerképet úgy, hogy üzembe helyezi a Azure App Service, vagy meghúzza a parancsot a helyi parancsra (a helyőrző helyére):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>Következő lépések

A Microsoft által támogatott Java alaplemezképek egyéb verzióihoz lásd:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE fej nélküli JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK és Maven](https://hub.docker.com/_/microsoft-java-maven)

Ha szeretne többet megtudni a Spring és az Azure szolgáltatásról, lépjen tovább a Spring on Azure dokumentációs központra.

> [!div class="nextstepaction"]
> [A Spring használata az Azure-ban](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>További források

További információkat találhat az alábbi forrásokban:

* [Azure Java-fejlesztőknek](/azure/java)
* [Az Azure DevOps és a Java használata](/azure/devops/java)
* [Spring Boot on Docker – Első lépések](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Spring Boot-alkalmazás üzembe helyezése az Azure App Service-ben](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-on-linux#configure-maven-to-build-image-to-your-azure-container-registry)
* [Egyéni Docker-rendszerkép használata az Azure Web App on Linuxban](../app-service/tutorial-custom-container.md)
