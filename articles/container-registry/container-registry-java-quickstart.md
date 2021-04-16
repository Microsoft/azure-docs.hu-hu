---
title: Rövid útmutató – Java-tárolók rendszerképének összeállítása és leküldése Azure Container Registry Maven és Jib használatával
description: Tárolóba írt Java-alkalmazás összeállítása és leküldése Azure Container Registry Maven Jib beépülő modullal.
author: KarlErickson
ms.author: karler
ms.date: 02/26/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
- mode-api
ms.openlocfilehash: 4d805458d90c73de879a9b87d5b08c98a8f1a250
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537306"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Rövid útmutató: Java-tároló rendszerképének összeállítása és leküldése Azure Container Registry

Ez a rövid útmutató bemutatja, hogyan építhet ki egy tárolóba írt Java-alkalmazást, és hogyan Azure Container Registry a Maven Jib beépülő modullal. A Maven és a Jib használata egy példa arra, hogy fejlesztői eszközök használatával kommunikálunk az Azure Container Registryvel.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial).
* Az [Azure Command-Line Interface (CLI)](/cli/azure/overview).
* Egy támogatott Java fejlesztői készlet (JDK). További információ az Azure-beli fejlesztéshez rendelkezésre álló JDK-król: <https://aka.ms/azure-jdks>.
* Az Apache [Maven buildeszköze](http://maven.apache.org) (3-as vagy újabb verzió).
* Egy [Git](https://git-scm.com)-ügyfél.
* Egy [Docker](https://www.docker.com)-ügyfél.
* Az [ACR Docker hitelesítőadat-segítője.](https://github.com/Azure/acr-docker-credential-helper)

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>A Spring Boot on Docker – Első lépések webalkalmazás létrehozása

A következő lépések végigvezetik egy Spring Boot-webalkalmazás összeállításán és helyszíni tesztelésén.

1. A parancssorból használja a következő parancsot a [Docker Spring Boot mintaprojektben](https://github.com/spring-guides/gs-spring-boot-docker) használt alkalmazás klónozásához.

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

A következő üzenetnek kell jelen lennie: **Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Azure Container Registry létrehozása az Azure CLI-vel

A következő lépésben létre fog hozni egy Azure-erőforráscsoportot és az ACR-t a következő lépésekkel:

1. Jelentkezzen be az Azure-fiókjába a következő paranccsal:

   ```azurecli
   az login
   ```

1. Adja meg a használni kívánt Azure-előfizetést:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Hozzon létre egy erőforráscsoportot az oktatóanyagban használt Azure-erőforrások számára. A következő parancsban cserélje le a helyőrzőket a saját erőforrásnevére és egy olyan helyre, mint a `eastus` .

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Hozzon létre egy privát Azure Container Registryt az erőforráscsoportban az alábbi paranccsal. A helyőrzőket cserélje le tényleges értékekre. Az oktatóanyag a mintaalkalmazást Docker-lemezképként küldi le ennek a regisztrációs adatbázisnak a későbbi lépésekben.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Az alkalmazás leküldése a regisztrációs adatbázisnak a Jibbel

Végül frissíti a projektkonfigurációt, és a parancssor használatával buildalja és üzembe helyezheti a rendszerképet.

> [!NOTE]
> Az újonnan létrehozott Azure Container Registrybe való bejelentkezéshez futnia kell a Docker-démonnak. A Docker gépre való telepítéséhez [itt található a Hivatalos Docker-dokumentáció.](https://docs.docker.com/install/)

1. Jelentkezzen be a Azure Container Registry az Azure CLI-ről a következő paranccsal. A helyőrzőt cserélje le a saját regisztrációs adatbázisának nevére.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   A `az configure` parancs beállítja a beállításjegyzék alapértelmezett nevét a `az acr` parancsokkal való használatra.

1. Lépjen a Spring Boot-alkalmazás befejezett projektkönyvtárába (például „*C:\SpringBoot\gs-spring-boot-docker\complete*” vagy „*/users/robert/SpringBoot/gs-spring-boot-docker/complete*”), és nyissa meg a *pom.xml* fájlt egy szövegszerkesztővel.

1. Frissítse a `<properties>` gyűjteményt a *pom.xml* a következő XML-fájllal. Cserélje le a helyőrzőt a beállításjegyzék nevére, és adjon hozzá egy tulajdonságot a értékkel, vagy `<jib-maven-plugin.version>` `2.2.0` a [jib-maven-plugin újabb verziójával.](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. Frissítse a gyűjteményt apom.xml, hogy a elem tartalmazza a és a `<plugins>`  `<plugin>` `jib-maven-plugin` bejegyzést, ahogy az alábbi példában látható. Vegye figyelembe, hogy a Microsoft Container Registry (MCR) alapként használt rendszerképét használjuk, amely egy hivatalosan támogatott `mcr.microsoft.com/java/jdk:8-zulu-alpine` JDK-t tartalmaz az Azure-hoz. A hivatalosan támogatott JDK-kat használó egyéb MCR-alapként használt rendszerképekért lásd: [Java SE JDK,](https://hub.docker.com/_/microsoft-java-jdk) [Java SE JRE,](https://hub.docker.com/_/microsoft-java-jre) [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)és [Java SE JDK és Maven.](https://hub.docker.com/_/microsoft-java-maven)

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
> Biztonsági okokból a által létrehozott hitelesítő `az acr login` adatok csak 1 óráig érvényesek. Ha a *401 Unauthorized (Jogosulatlan)* hibaüzenetet kapja, újra futtathatja a parancsot az `az acr login -n <your registry name>` újbóli hitelesítéshez.

## <a name="verify-your-container-image"></a>A tároló rendszerképének ellenőrzése

Gratulálunk! Most már leküldte a tárolóba írt Java-alkalmazást az ACR-be az Azure által támogatott JDK-on. Most már tesztelheti a rendszerképet úgy, hogy üzembe Azure App Service, vagy a paranccsal lekért helyi helyre (a helyőrzőt lecserélve):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>Következő lépések

A Hivatalos Microsoft által támogatott Java-alapként használt rendszerképek egyéb verzióiért lásd:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
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
