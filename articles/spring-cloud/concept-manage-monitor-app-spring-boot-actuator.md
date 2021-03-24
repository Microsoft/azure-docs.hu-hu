---
title: Alkalmazás kezelése és monitorozása az Azure Spring Boot Actuatorrel
description: Ismerje meg, hogyan kezelheti és figyelheti az alkalmazást a Spring boot indítószerkezettel.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 93fd286aa76a0409a515abbf8c9dabd88a9a65c4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878016"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>Alkalmazás kezelése és monitorozása az Azure Spring Boot Actuatorrel

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

Miután telepítette az új bináris alkalmazást az alkalmazásba, érdemes megtekinteni a funkciót, és megtekintheti a futó alkalmazással kapcsolatos információkat. Ez a cikk azt ismerteti, hogyan érheti el az API-t egy Azure Spring Cloud által biztosított tesztelési végpontból, és elérhetővé teheti az alkalmazás éles használatra kész funkcióit.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy van egy Spring boot 2. x alkalmazás, amely sikeresen üzembe helyezhető és indítható az Azure Spring Cloud Service-ben.  Lásd [: gyors útmutató: meglévő Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával](spring-cloud-quickstart.md)

## <a name="verify-app-through-test-endpoint"></a>Alkalmazás ellenőrzése tesztelési végponton keresztül
1. Lépjen az **alkalmazás irányítópultra** , és kattintson az alkalmazásra az alkalmazás – áttekintés oldal megadásához.

1. Az **Áttekintés** ablaktáblán látnia kell a **teszt végpontot**.  Ezt a végpontot a parancssorból vagy a böngészőből érheti el, és megfigyelheti az API-választ.

1. Figyelje meg az elkövetkező szakaszban használt **teszt végpont** URI azonosítóját.

>[!TIP]
> * Ha az alkalmazás egy előtér-oldalt ad vissza, és a relatív elérési úton más fájlokra hivatkozik, ellenőrizze, hogy a teszt végpontja perjeltel (/) végződik-e. Ez biztosítja a CSS-fájl megfelelő betöltését.
> * Ha az API-t egy böngészőből tekinti meg, és a böngészőjében meg kell adnia bejelentkezési hitelesítő adatait az oldal megtekintéséhez, használja az [URL-címet](https://www.urldecoder.org/) a tesztelési végpont dekódolásához. Az URL-cím dekódolása egy "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/ \<app-name> / \<deployment-name> " formátumú URL-címet ad vissza.  Ezt az űrlapot használhatja a végpont eléréséhez.

## <a name="add-actuator-dependency"></a>Indítószerkezet függőségének hozzáadása

Az indítószerkezet egy Maven-alapú projekthez való hozzáadásához adja hozzá az "indító" függőséget:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

Fordítsa le az új bináris fájlt, és telepítse azt az alkalmazásba.

## <a name="enable-production-ready-features"></a>Éles használatra kész funkciók engedélyezése
Az indítószerkezetek végpontjai lehetővé teszik az alkalmazás figyelését és kezelését. Alapértelmezés szerint a Spring boot Application kiteszi `health` és `info` végpontokat jelenít meg tetszőleges alkalmazás-információ és állapotadatok megjelenítéséhez.

A konfiguráció és a konfigurálható környezet megfigyeléséhez engedélyeznie kell a `env` és a `configgrops` végpontokat is.

1. Az alkalmazás **áttekintése** ablaktáblán kattintson a beállítás menü **konfiguráció** elemére, lépjen a **környezeti változók** konfigurálása lapra.
1. Adja hozzá a következő tulajdonságokat a "kulcs: érték" űrlaphoz. Ez a környezet megnyitja az "env", "Health", "info" nevű rugós indítószerkezet végpontot.

   ```
   management.endpoints.web.exposure.include: env,health,info
   ```
1. Kattintson a **Save (Mentés** ) gombra, az alkalmazás automatikusan újraindul, és betölti az új környezeti változókat.

Most visszatérhet az alkalmazás áttekintés paneljére, és megvárhatja, amíg a kiépítési állapot "sikeres" állapotúra nem változik.  Több futó példány is lesz.

> [!Note] 
> Miután közzétette az alkalmazást a nyilvánosság számára, ezek az indítószerkezet-végpontok nyilvánosan is elérhetővé válnak. Az összes végpontot elrejtheti a környezeti változók törlésével `management.endpoints.web.exposure.include` , és beállíthatja a `management.endpoints.web.exposure.exclude=*`

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>Az alkalmazás adatainak megtekintéséhez az indítószerkezet végpontjának megtekintése
1. Most már elérheti az URL-címet `"<test-endpoint>/actuator/"` , és megtekintheti a Spring boot-működtető által elérhető összes végpontot.
1. Hozzáférési URL-cím `"<test-endpoint>/actuator/env"` : megtekintheti az alkalmazás által használt aktív profilokat, valamint az összes betöltött környezeti változót.
1. Ha egy adott környezetben szeretne keresni, elérheti az URL-címet,  `"<test-endpoint>/actuator/env/{toMatch}"` és megtekintheti azt.

A beépített összes végpont megtekintéséhez lásd: [végpontok](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints) kimutatása

## <a name="next-steps"></a>Következő lépések

* [Az Azure Spring Cloud metrikáinak ismertetése](spring-cloud-concept-metrics.md)
* [Az Azure Spring Cloud alkalmazásállapotainak ismertetése](spring-cloud-concept-app-status.md)

