---
title: 'Gyors útmutató: bejelentkezés felvétele a Microsofttal Java-webalkalmazásba | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan adhat hozzá bejelentkezést a Microsofttal egy Java-webalkalmazáshoz OpenID Connect használatával.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: a7337175241834cef862b4af07c7bcf7c8b845d0
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103770"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Gyors útmutató: bejelentkezés felvétele a Microsofttal egy Java-webalkalmazásba

Ebben a rövid útmutatóban egy olyan kódrészletet tölt le és futtat, amely bemutatja, hogyan jelentkezhet be a Java-webalkalmazás a felhasználókba, és hogyan hívhatja meg a Microsoft Graph API-t. Bármely Azure Active Directory (Azure AD-) szervezet felhasználói bejelentkezhetnek az alkalmazásba.

 Áttekintésért tekintse meg a [minta működésének diagramját](#how-the-sample-works).

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához a következőkre lesz szüksége:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 vagy újabb. 
- [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A gyors üzembe helyezési alkalmazás kétféleképpen indítható el: Express (1. lehetőség) és manuális (2. lehetőség).
>
> ### <a name="option-1-register-and-automatically-configure-your-app-and-then-download-the-code-sample"></a>1. lehetőség: az alkalmazás regisztrálása és automatikus konfigurálása, majd a kód letöltése
>
> 1. Lépjen a <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Alkalmazásregisztrációk</a> gyors üzembe helyezési élményhez.
> 1. Adja meg az alkalmazás nevét, majd válassza a **regisztráció** lehetőséget.
> 1. Az automatikusan konfigurált alkalmazás kódjának letöltéséhez kövesse a portál gyors üzembe helyezési felületének utasításait.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
>
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs adatainak manuális hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kívánja az alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **Kezelés** területen válassza az **Alkalmazásregisztrációk** lehetőséget.
> 1. Válassza az **új regisztráció** lehetőséget.
> 1. Adja meg az alkalmazás **nevét** , például: **Java-WebApp**. Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet. Később is megváltoztathatja.
> 1. Válassza a **Regisztráció** lehetőséget.
> 1. Az **Áttekintés** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítóját** és a **könyvtár (bérlő) azonosítóját**. Ezekre az értékekre később szüksége lesz.
> 1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
> 1. Válassza **a platform**  >  **web** hozzáadása lehetőséget.
> 1. Az **átirányítási URI** -k szakaszban adja meg a értéket `https://localhost:8443/msal4jsample/secure/aad` .
> 1. Válassza a **Konfigurálás** lehetőséget.
> 1. A **web** szakaszban az URI-k **átirányítása** alatt adja meg `https://localhost:8443/msal4jsample/graph/me` második átirányítási URI-ként.
> 1. A **Kezelés** területen válassza a **Tanúsítványok és titkos kódok** lehetőséget. Az **ügyfél titkai** szakaszban válassza az **új ügyfél titka** elemet.
> 1. Adja meg a kulcs leírását (például az *alkalmazás titkát*), hagyja meg az alapértelmezett lejáratot, és válassza a **Hozzáadás** lehetőséget.
> 1. Jegyezze fel az ügyfél titkos kulcsának **értékét** . Erre később még szüksége lesz.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
>
> A kód minta használata ebben a rövid útmutatóban:
>
> 1. Adja hozzá a válasz URL-címeit `https://localhost:8443/msal4jsample/secure/aad` és `https://localhost:8443/msal4jsample/graph/me` .
> 1. Hozzon létre egy ügyfél titkot.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-code-sample"></a>2. lépés: a mintakód letöltése
> [!div renderon="docs"]
> [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Töltse le a projektet, és bontsa ki a. zip fájlt egy olyan mappába, amely a meghajtó gyökeréhez közel található. Például: *C:\Azure-Samples*.
>
> Ha a HTTPS-t a localhost használatával szeretné használni, adja meg a `server.ssl.key` tulajdonságokat. Önaláírt tanúsítvány létrehozásához használja a (JRE részét képező) eszköz segédprogramját.
>
> Bemutatunk egy példát:
>  ```
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Helyezze a generált rendszertároló fájlt a *Resources (erőforrások* ) mappába.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>3. lépés: a mintakód konfigurálása
> 1. Csomagolja ki a zip-fájlt egy helyi mappába.
> 1. *Választható.* Ha integrált fejlesztési környezetet használ, nyissa meg a mintát ebben a környezetben.
> 1. Nyissa meg az *Application. properties* fájlt. A *forrás a src/Main/Resources* /mappában található. Cserélje le a mezőkben szereplő értékeket, `aad.clientId` `aad.authority` és `aad.secretKey` az alkalmazás-azonosító, a bérlő azonosítója és az ügyfél titkos értékei értékeit. A következőképpen kell kinéznie:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
>    Az előző kódban:
>
>    - `Enter_the_Application_Id_here` a regisztrált alkalmazáshoz tartozó alkalmazás azonosítója.
>    - `Enter_the_Client_Secret_Here`a **tanúsítványokban létrehozott &** **titkos kulcs** a regisztrált alkalmazáshoz.
>    - `Enter_the_Tenant_Info_Here` a regisztrált alkalmazás **címtár-(bérlői) azonosítójának** értéke.
> 1. Ha a HTTPS-t a localhost használatával szeretné használni, adja meg a `server.ssl.key` tulajdonságokat. Önaláírt tanúsítvány létrehozásához használja a (JRE részét képező) eszköz segédprogramját.
>
>    Bemutatunk egy példát:
>
>     ```
>      keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>      server.ssl.key-store-type=PKCS12
>      server.ssl.key-store=classpath:keystore.p12
>      server.ssl.key-store-password=password
>      server.ssl.key-alias=testCert
>      ```
>   1. Helyezze a generált rendszertároló fájlt a *Resources (erőforrások* ) mappába.


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>3. lépés: a kód mintájának futtatása
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>4. lépés: a kód mintájának futtatása

A projekt futtatásához hajtsa végre a következő lépések egyikét:

- Futtassa közvetlenül az IDE-ből a beágyazott Spring boot Server használatával. 
- A [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html)használatával csomagolja ki egy háborús fájlba, majd telepítse azt egy J2EE-tároló megoldásba, mint például az [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-the-project-from-an-ide"></a>A projekt futtatása IDE-ből

Ha a webalkalmazást IDE szeretné futtatni, válassza a Futtatás lehetőséget, majd lépjen a projekt kezdőlapjára. Ehhez a mintához a standard Kezdőlap URL-címe: https://localhost:8443 .

1. A kezdőlapon kattintson a login ( **Bejelentkezés** ) gombra a felhasználók átirányításához Azure Active Directory és hitelesítő adatok megadásához.

1. A felhasználók hitelesítése után a rendszer átirányítja a-re `https://localhost:8443/msal4jsample/secure/aad` . Most bejelentkeznek, és az oldalon megjelennek a felhasználói fiókkal kapcsolatos információk. A minta felhasználói felület a következő gombokkal rendelkezik:
    - **Kijelentkezés**: aláírja az aktuális felhasználót az alkalmazásból, és átirányítja a felhasználót a kezdőlapra.
    - **Felhasználói információk megjelenítése**: jogkivonatot kér a Microsoft Graphhoz, és meghívja Microsoft Graph a jogkivonatot tartalmazó kérelemmel, amely a bejelentkezett felhasználó alapszintű információit adja vissza.

##### <a name="running-the-project-from-tomcat"></a>A projekt futtatása a Tomcatből

Ha a webes mintát a Tomcat szolgáltatásban szeretné üzembe helyezni, hajtson végre néhány módosítást a forráskódon.

1. Nyissa meg az *MS-Identity-Java-WebApp/pom.xml*.
    - A területen `<name>msal-web-sample</name>` adja hozzá a t `<packaging>war</packaging>` .

2. Nyissa meg a *MS-Identity-Java-WebApp/src/Main/Java/com. microsoft. Azure. msalwebsample/MsalWebSampleApplication*.

    - Törölje az összes forráskódot, és cserélje le a következő kódra:

      ```Java
       package com.microsoft.azure.msalwebsample;

       import org.springframework.boot.SpringApplication;
       import org.springframework.boot.autoconfigure.SpringBootApplication;
       import org.springframework.boot.builder.SpringApplicationBuilder;
       import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

       @SpringBootApplication
       public class MsalWebSampleApplication extends SpringBootServletInitializer {

        public static void main(String[] args) {
         SpringApplication.run(MsalWebSampleApplication.class, args);
        }

        @Override
        protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
         return builder.sources(MsalWebSampleApplication.class);
        }
       }
      ```

3.   A Tomcat alapértelmezett HTTP-portja a 8080-as, de HTTPS-kapcsolatra van szükség a 8443-es porton keresztül. A beállítás konfigurálása:
        - Nyissa meg a *tomcat/conf/server.xml*.
        - Keresse meg a `<connector>` címkét, és cserélje le a meglévő összekötőt erre az összekötőre:

          ```xml
          <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
          ```

4. Nyisson meg egy parancssori ablakot. Lépjen a minta gyökérkönyvtárára (ahol a pom.xml fájl található), és futtassa a parancsot a `mvn package` projekt felépítéséhez.
    - Ezzel a paranccsal létrejön egy *msal-web-Sample-0.1.0. War* fájl a */Targets* könyvtárban.
    - Nevezze át ezt a fájlt a *msal4jsample. War* névre.
    - A WAR-fájlt a Tomcat vagy bármely más J2EE-tároló megoldás használatával helyezheti üzembe.
        - A msal4jsample. War fájl telepítéséhez másolja a */webapps/* könyvtárba a Tomcat-telepítésben, majd indítsa el a Tomcat-kiszolgálót.

5. A fájl üzembe helyezése után nyissa meg a következőt https://localhost:8443/msal4jsample : böngésző használatával.


> [!IMPORTANT]
> Ez a rövid útmutató alkalmazás egy ügyfél titkos kulcsát használja, amely bizalmas ügyfélként azonosítja magát. Mivel az ügyfél titkos kulcsát egyszerű szövegként adja hozzá a Project-fájlokhoz, biztonsági okokból javasoljuk, hogy az alkalmazás éles környezetben való használata előtt használjon tanúsítványokat az ügyfél titkos kulcsa helyett. A tanúsítványok használatáról a [tanúsítvány hitelesítő adatai az alkalmazás hitelesítéséhez](./active-directory-certificate-credentials.md)című témakörben olvashat bővebben.

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése
![Diagram, amely bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás.](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="get-msal"></a>MSAL beolvasása

A MSAL for Java (MSAL4J) a Microsoft Identity platform által védett API-k eléréséhez használt Java-könyvtár.

Vegyen fel MSAL4J az alkalmazásba a Maven vagy a Gradle használatával a függőségek kezeléséhez azáltal, hogy az alábbi módosításokat hajtja végre az alkalmazás pom.xml (Maven) vagy a Build. Gradle (Gradle) fájlon.

pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

A Build. gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="initialize-msal"></a>MSAL inicializálása

Vegyen fel egy hivatkozást a Java-MSAL a következő kód megadásával a fájl elején, ahol a MSAL4J fogja használni:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

A Microsoft Identity platform felhasználóit bejelentkező webalkalmazások létrehozásával kapcsolatos alaposabb eszmecsere: többrészes forgatókönyvek sorozata:

> [!div class="nextstepaction"]
> [Forgatókönyv: a felhasználók által bejelentkezett webalkalmazás](scenario-web-app-sign-user-overview.md?tabs=java)
