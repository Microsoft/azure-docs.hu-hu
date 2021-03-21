---
title: Rövid útmutató szolgáltatás-jelzők hozzáadásához az Azure app Configuration használatával
description: Szolgáltatás-jelzők hozzáadása Spring boot-alkalmazásokhoz és kezelésük az Azure app Configuration használatával
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 08/06/2020
ms.author: alkemper
ms.custom: devx-track-java
ms.openlocfilehash: e931b974fc48d07237b2698e7782c48be20745d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930712"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Gyors útmutató: szolgáltatás-jelzők hozzáadása Spring boot-alkalmazáshoz

Ebben a rövid útmutatóban beépítheti az Azure-alkalmazások konfigurációját egy Spring boot-webalkalmazásba, amely a szolgáltatások felügyeletének teljes körű megvalósítását hozza létre. Az alkalmazás konfigurációs szolgáltatásával központilag tárolhatja az összes funkció jelzőjét, és szabályozhatja az állapotukat.

A tavaszi rendszerindítási szolgáltatás felügyeleti kódtárai kiterjesztik a keretrendszert a szolgáltatás teljes körű támogatásával. Ezek a kódtárak **nem** függnek az Azure-könyvtáraktól. Zökkenőmentesen integrálva vannak az alkalmazások konfigurációjával a Spring boot-konfiguráció szolgáltatóján keresztül.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Támogatott [Java Development Kit SDK](/java/azure/jdk) a 8-as verzióval.
* Az [Apache Maven](https://maven.apache.org/download.cgi) 3,0-es vagy újabb verziója.

## <a name="create-an-app-configuration-instance"></a>Alkalmazás-konfigurációs példány létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Válassza a **funkció-kezelő**  >  **+ Hozzáadás** elemet a szolgáltatáshoz tartozó jelző hozzáadásához `Beta` .

    > [!div class="mx-imgBorder"]
    > ![A szolgáltatás jelölő engedélyezése Beta néven](media/add-beta-feature-flag.png)

    `label`Most hagyja meg a nem definiált lehetőséget.

## <a name="create-a-spring-boot-app"></a>Spring boot-alkalmazás létrehozása

A [Spring inicializáló](https://start.spring.io/) használatával hozzon létre egy új Spring boot-projektet.

1. Nyissa meg a következő címet: <https://start.spring.io/>.

1. Adja meg a következő beállításokat:

   * Hozzon létre egy **Maven** projektet **Javával**.
   * Olyan **Spring boot** -verziót válasszon, amely egyenlő vagy nagyobb, mint 2,0.
   * Adja meg az alkalmazáshoz tartozó **Group** (Csoport) és **Artifact** (Összetevő) neveket.  Ez a cikk a és a használatát ismerteti `com.example` `demo` .
   * Adja hozzá a **rugó webes** függőségét.

1. Az előző beállítások megadása után válassza a **projekt létrehozása** lehetőséget. Ha a rendszer kéri, töltse le a projektet a helyi számítógépre.

## <a name="add-feature-management"></a>Szolgáltatások felügyeletének hozzáadása

1. Miután kicsomagolta a fájlokat a helyi rendszeren, a Spring boot-alkalmazás készen áll a szerkesztésre. Keresse meg  *pom.xml* az alkalmazás gyökérkönyvtárában.

1. Nyissa meg a *pom.xml* fájlt egy szövegszerkesztőben, és adja hozzá a következőt a listájához `<dependencies>` :

    **Spring Cloud 1.1. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
        <version>1.1.5</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.5</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

    **Spring Cloud 1.2. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
        <version>1.2.7</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.2.7</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> A nem webes szolgáltatások felügyeleti könyvtára nem függ a Spring-web-től. A különbségekről a GitHub [dokumentációjában](https://github.com/microsoft/spring-cloud-azure) tájékozódhat.

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Navigáljon az `resources` alkalmazás könyvtárába, és nyissa meg a következőt: `bootstrap.properties` .  Ha a fájl nem létezik, hozza létre. Adja hozzá a következő sort a fájlhoz.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. A konfigurációs tárolóhoz tartozó alkalmazás-konfigurációs portálon válassza `Access keys` a lehetőséget az oldalsávon. Válassza a csak olvasható kulcsok lapot. Másolja ki az elsődleges kapcsolatok karakterláncának értékét.

1. Adja hozzá az elsődleges kapcsolatok karakterláncát környezeti változóként a változó neve alapján `APP_CONFIGURATION_CONNECTION_STRING` .

1. Nyissa meg az alkalmazás fő Java-fájlját, és adja hozzá a `@EnableConfigurationProperties` szolgáltatást a funkció engedélyezéséhez.

    ```java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.boot.context.properties.EnableConfigurationProperties;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Hozzon létre egy új, *MessageProperties. Java* nevű Java-fájlt az alkalmazás csomag könyvtárába.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. Hozzon létre egy új, *HelloController. Java* nevű Java-fájlt az alkalmazás csomag könyvtárába.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;

    import com.microsoft.azure.spring.cloud.feature.manager.FeatureManager;
    import org.springframework.web.bind.annotation.GetMapping;


    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabledAsync("featureManagement.Beta").block());
            return "welcome";
        }
    }
    ```

1. Hozzon létre egy *welcome.html* nevű új HTML-fájlt az alkalmazás sablonok könyvtárában.

    ```html
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title>Feature Management with Spring Cloud Azure</title>

        <link rel="stylesheet" href="/css/main.css">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>

    </head>
    <body>
        <header>
        <!-- Fixed navbar -->
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <a class="navbar-brand" href="#">TestFeatureFlags</a>
            <button class="navbar-toggler" aria-expanded="false" aria-controls="navbarCollapse" aria-label="Toggle navigation" type="button" data-target="#navbarCollapse" data-toggle="collapse">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarCollapse">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item active">
                <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
                </li>
                <li class="nav-item" th:if="${Beta}">
                <a class="nav-link" href="#">Beta</a>
                </li>
                <li class="nav-item">
                <a class="nav-link" href="#">Privacy</a>
                </li>
            </ul>
            </div>
        </nav>
        </header>
        <div class="container body-content">
            <h1 class="mt-5">Welcome</h1>
            <p>Learn more about <a href="https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/appconfiguration/azure-spring-cloud-feature-management/README.md">Feature Management with Spring Cloud Azure</a></p>

        </div>
        <footer class="footer">
            <div class="container">
            <span class="text-muted">&copy; 2019 - Projects</span>
        </div>

        </footer>
    </body>
    </html>

    ```

1. Hozzon létre egy új, CSS nevű mappát `static` a és annak belsejében egy *Main. CSS* nevű CSS-fájl.

    ```css
    html {
     position: relative;
     min-height: 100%;
    }
    body {
     margin-bottom: 60px;
    }
    .footer {
     position: absolute;
     bottom: 0;
     width: 100%;
     height: 60px;
     line-height: 60px;
     background-color: #f5f5f5;
    }

    body > .container {
     padding: 60px 15px 0;
    }

    .footer > .container {
     padding-right: 15px;
     padding-left: 15px;
    }

    code {
     font-size: 80%;
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítsa össze a Spring Boot-alkalmazást a Mavennel, és futtassa.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Nyisson meg egy böngészőablakot, és lépjen a következő URL-címre: `http://localhost:8080/welcome` .

    ![A képernyőképen egy üdvözlő üzenettel rendelkező böngészőablak látható.](./media/quickstarts/spring-boot-feature-flag-local-before.png)

1. Az alkalmazás konfigurációs portálján válassza a **Feature Manager** elemet, és módosítsa a **bétaverzió** **állapotát a következőre:**

    | Kulcs | Állapot |
    |---|---|
    | Beta | Be |

1. A böngésző oldalának frissítésével tekintheti meg az új konfigurációs beállításokat.

    ![Képernyőfelvétel: a böngészőablak egy üdvözlő üzenettel és egy kinevezett bétaverziós hivatkozással jelenik meg.](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, és felhasználta egy Spring boot-webalkalmazás funkcióinak kezeléséhez a [Feature Management librarys](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)használatával.

* További információ a [szolgáltatások kezeléséről](./concept-feature-management.md).
* [Szolgáltatás-jelzők kezelése](./manage-feature-flags.md).
* [A Feature Flags szolgáltatást egy Spring boot Core-alkalmazásban használhatja](./use-feature-flags-spring-boot.md).