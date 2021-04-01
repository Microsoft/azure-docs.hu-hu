---
title: Oktatóanyag a Feature Flags használatához egy Spring boot-alkalmazásban – Azure app Configuration | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan valósíthatók meg a szolgáltatás-jelzők a Spring boot-alkalmazásokban.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 0dcf85add4e1c9f1d701c4ce4a122c8d6b31382a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92077830"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Oktatóanyag: funkció-jelzők használata Spring boot-alkalmazásokban

A tavaszi rendszerindítási alapszolgáltatások kezelési kódtárai támogatják a szolgáltatás-jelzők megvalósítását egy Spring boot-alkalmazásban. Ezek a kódtárak lehetővé teszik a funkció-jelzők deklaratív hozzáadását a kódhoz.

A szolgáltatás-felügyeleti kódtárak a funkciók jelző életciklusait is kezelik a színfalak mögött. Például a tárak frissítési és gyorsítótár-jelző állapota, vagy ha egy kérelem hívásakor a jelző állapot nem változtatható meg. Emellett a Spring boot Library integrációkat is kínál, beleértve az MVC vezérlő műveleteit, az útvonalakat és a köztes adatforrásokat.

A [Spring boot-alkalmazás funkcióinak hozzáadása](./quickstart-feature-flag-spring-boot.md) a Spring boot-alkalmazásban számos módszert mutat be a funkció-jelzők hozzáadására. Ez az oktatóanyag részletesen ismerteti ezeket a módszereket.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * A szolgáltatások rendelkezésre állásának szabályozásához vegyen fel szolgáltatás-jelzőket az alkalmazás legfontosabb részeibe.
> * Integrálhatja az alkalmazás konfigurációját, ha a funkció-jelzők kezelésére használja.

## <a name="set-up-feature-management"></a>A szolgáltatások kezelésének beállítása

A Spring boot Feature Manager a `FeatureManager` keretrendszer natív konfigurációs rendszerének a funkcióinak jelzőit kapja. Ennek eredményeképpen meghatározhatja az alkalmazás funkciójának jelzőit bármely olyan konfigurációs forrás használatával, amelyet a Spring boot támogat, beleértve a helyi *bootstrap. YML* fájl-vagy környezeti változókat is. `FeatureManager` függőségi injektálásra támaszkodik. A szolgáltatás-felügyeleti szolgáltatásokat szabványos konvenciók használatával regisztrálhatja:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Javasoljuk, hogy az alkalmazáson kívül tartsa meg a szolgáltatás jelölőit, és ezeket külön kell kezelnie. Ezzel a beállítással bármikor módosíthatja a jelző állapotokat, és ezek a módosítások azonnal érvénybe lépnek az alkalmazásban. Az alkalmazás konfigurációja központosított helyet biztosít az összes funkció jelzőjének egy dedikált portál felhasználói felületen való rendszerezéséhez és szabályozásához. Az alkalmazás konfigurációja emellett közvetlenül a Spring boot-ügyfél könyvtárain keresztül továbbítja az alkalmazáshoz tartozó jelzőket.

A Spring boot-alkalmazásnak az alkalmazás-konfigurációhoz való összekapcsolásának legegyszerűbb módja a konfigurációs szolgáltatón keresztül:

### <a name="spring-cloud-11x"></a>Spring Cloud 1.1. x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Spring Cloud 1.2. x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Funkció jelző deklarációja

Minden egyes szolgáltatás jelölője két részből áll: egy vagy több szűrőből áll, amelyek segítségével kiértékelheti, hogy a szolgáltatás állapota be van-e *kapcsolva* (azaz ha értéke `True` ). A szűrők a használati esetet határozzák meg, ha egy szolgáltatás bekapcsolására van lehetőség.

Ha egy szolgáltatás jelölője több szűrővel rendelkezik, a rendszer átadja a szűrőlisták sorrendjét, amíg az egyik szűrő nem határozza meg, hogy a szolgáltatást engedélyezni kell. Ekkor a funkció jelzője *be van kapcsolva*, és a rendszer kihagyja a többi szűrő eredményét. Ha nincs szűrő azt jelzi, hogy a funkciót engedélyezni kell, a szolgáltatás jelzője *ki van kapcsolva*.

A Feature Manager támogatja az *Application. YML* konfigurációs forrásként való használatát a szolgáltatások jelzői számára. Az alábbi példa bemutatja, hogyan állíthatja be a szolgáltatás jelzőit egy YAML-fájlban:

```yml
feature-management:
  feature-set:
    feature-a: true
    feature-b: false
    feature-c:
      enabled-for:
        -
          name: Percentage
          parameters:
            value: 50
```

Az egyezmény szerint a `feature-management` YML-dokumentum szakasza a szolgáltatások jelző beállításaihoz használatos. Az előző példa három funkció jelzőjét mutatja a tulajdonságban definiált szűrőkkel `EnabledFor` :

* `feature-a`*be van kapcsolva*.
* `feature-b`*ki van kapcsolva*.
* `feature-c` egy tulajdonsággal megnevezett szűrőt ad meg `Percentage` `parameters` . `Percentage` konfigurálható szűrő. Ebben a példában a `Percentage` jelző 50 százalékos valószínűségét adja `feature-c` *meg*.

## <a name="feature-flag-checks"></a>Szolgáltatás-jelző ellenőrzése

A szolgáltatások felügyeletének alapszintű mintája először ellenőrizze, hogy be van *-e állítva* a szolgáltatás jelölője. Ebben az esetben a Feature Manager ezután futtatja a funkció által tartalmazott műveleteket. Például:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>Függőséginjektálás

A Spring boot-ben a szolgáltatás-kezelő `FeatureManager` függőségi befecskendezéssel érhető el:

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Vezérlő műveletei

Az MVC-vezérlőkben az `@FeatureGate` attribútum segítségével szabályozhatja, hogy egy adott művelet engedélyezve van-e. A következő `Index` művelet végrehajtása szükséges a `feature-a` futtatásához: 

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

Ha egy MVC vezérlő vagy művelet le van tiltva, mert a vezérlő funkció jelzője *ki van kapcsolva*, a rendszer egy regisztrált `IDisabledFeaturesHandler` felületet hív meg. Az alapértelmezett `IDisabledFeaturesHandler` illesztőfelület 404 állapotkódot ad vissza az ügyfélnek a válasz törzse nélkül.

## <a name="mvc-filters"></a>MVC-szűrők

Az MVC-szűrőket beállíthatja úgy, hogy azok a szolgáltatás jelzőjének állapota alapján legyenek aktiválva. A következő kód egy nevű MVC szűrőt hoz létre `FeatureFlagFilter` . Ez a szűrő csak akkor aktiválódik az MVC-folyamaton belül `feature-a` , ha engedélyezve van.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabledAsync("feature-a").block()) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Útvonalak

Az útvonalak átirányításához használhatja a szolgáltatás jelzőit. A következő kód lehetővé teszi a felhasználó átirányítását `feature-a` :

```java
@GetMapping("/redirect")
@FeatureGate(feature = "feature-a", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan implementálhatja a szolgáltatás-jelzőket a Spring boot-alkalmazásban a `spring-cloud-azure-feature-management-web` kódtárak használatával. A Spring boot és az alkalmazások konfigurációjának szolgáltatás-kezelési támogatásával kapcsolatos további információkért tekintse meg a következő forrásokat:

* [Tavaszi rendszerindítási funkció jelölője – mintakód](./quickstart-feature-flag-spring-boot.md)
* [Funkciójelölők kezelése](./manage-feature-flags.md)