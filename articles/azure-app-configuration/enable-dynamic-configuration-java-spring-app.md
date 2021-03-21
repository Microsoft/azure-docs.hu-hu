---
title: Dinamikus konfiguráció használata Spring boot-alkalmazásokban
titleSuffix: Azure App Configuration
description: Ismerje meg, hogyan frissítheti dinamikusan a Spring boot-alkalmazások konfigurációs információit
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 076ab0bb7dbc85a31b626a24d977e6fea558143e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636538"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Oktatóanyag: dinamikus konfiguráció használata Java Spring-alkalmazásokban

Az alkalmazás konfigurációjának két könyvtára van a rugóhoz. `spring-cloud-azure-appconfiguration-config` a Spring boot szükséges, és függőséget igényel `spring-cloud-context` . `spring-cloud-azure-appconfiguration-config-web` a Spring web és a Spring boot használatát igényli. Mindkét függvénytár támogatja a manuális aktiválást a frissített konfigurációs értékek kereséséhez. `spring-cloud-azure-appconfiguration-config-web` Emellett támogatja a konfiguráció frissítésének automatikus ellenőrzését is.

A frissítés lehetővé teszi a konfigurációs értékek frissítését anélkül, hogy újra kellene indítani az alkalmazást, de a rendszer az összes babot újra `@RefreshScope` létrehozza. Az ügyféloldali kódtár gyorsítótárba helyezi az aktuálisan betöltött konfigurációk kivonatoló azonosítóját, hogy ne legyen túl sok hívás a konfigurációs tárolóba. A frissítési művelet nem frissíti az értéket addig, amíg a gyorsítótárazott érték lejár, még akkor is, ha az érték megváltozott a konfigurációs tárolóban. Az egyes kérések alapértelmezett lejárati ideje 30 másodperc. Szükség esetén felül lehet bírálni.

`spring-cloud-azure-appconfiguration-config-web`az automatikus frissítés a tevékenységen alapuló, konkrétan rugós webszolgáltatások esetében aktiválódik `ServletRequestHandledEvent` . Ha a `ServletRequestHandledEvent` nem aktiválódik, az `spring-cloud-azure-appconfiguration-config-web` automatikus frissítés nem indít el frissítést, még akkor sem, ha a gyorsítótár lejárati ideje lejárt.

## <a name="use-manual-refresh"></a>Manuális frissítés használata

Az alkalmazás konfigurációja elérhetővé teszi a `AppConfigurationRefresh` gyorsítótár érvényességének ellenőrzését, és ha lejárt a frissítés, a rendszer lefuttatja a frissítést.

```java
import com.microsoft.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`AppConfigurationRefresh``refreshConfigurations()`a olyan értéket ad vissza `Future` , amely igaz, ha egy frissítés aktiválva lett, és hamis, ha nem. Hamis érték esetén a gyorsítótár lejárati ideje nem járt le, nem történt változás, vagy egy másik szál jelenleg nem ellenőrzi a frissítést.

## <a name="use-automated-refresh"></a>Automatikus frissítés használata

Az automatikus frissítés használatához kezdjen el egy olyan Spring boot-alkalmazással, amely az alkalmazás konfigurációját használja, mint például az alkalmazás [konfigurációját követő Spring boot](quickstart-java-spring-app.md)rövid útmutató alapján létrehozott alkalmazás.

Ezután nyissa meg a *pom.xml* fájlt egy szövegszerkesztőben, és adjon hozzá egy-t `<dependency>` `spring-cloud-azure-appconfiguration-config-web` .

**Spring Cloud 1.1. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Spring Cloud 1.2. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>Az alkalmazás helyi futtatása és tesztelése

1. Állítsa össze a Spring Boot-alkalmazást a Mavennel, és futtassa.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Nyisson meg egy böngészőablakot, és lépjen a következő URL-címre: `http://localhost:8080` .  Ekkor megjelenik a kulcshoz tartozó üzenet.

    Az alkalmazás teszteléséhez a *curl* is használható, például: 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. A dinamikus konfiguráció teszteléséhez nyissa meg az alkalmazáshoz társított Azure app Configuration Portalt. Válassza a **Configuration Explorer** lehetőséget, és frissítse a megjelenített kulcs értékét, például:
    | Kulcs | Érték |
    |---|---|
    | alkalmazás/config. Message | Hello – frissítve |

1. Az új üzenet megjelenítéséhez frissítse a böngésző lapját.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte a Spring boot-alkalmazást, hogy dinamikusan frissítse a konfigurációs beállításokat az alkalmazás konfigurációjában. Ha meg szeretné tudni, hogyan használható az Azure felügyelt identitása az alkalmazás-konfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)
