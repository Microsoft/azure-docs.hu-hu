---
title: JMX-metrikák konfigurálása – Azure Monitor Application bepillantást a javára
description: További JMX-metrikai gyűjtemény konfigurálása Azure Monitor Application ininsight Java-ügynökhöz
ms.topic: conceptual
ms.date: 03/16/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 020278bf6e5a823f6b3caa22d03f4b5dd003c0d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609253"
---
# <a name="configuring-jmx-metrics"></a>JMX-metrikák konfigurálása

Az Application Insight Java 3,0-ügynöke alapértelmezés szerint gyűjt néhány JMX metrikát, de sok esetben ez nem elég. Ez a dokumentum a részletek JMX konfigurációs beállítását ismerteti.

## <a name="how-do-i-collect-additional-jmx-metrics"></a>Hogyan további JMX-metrikákat gyűjthet?

A JMX-metrikák gyűjteménye beállítható úgy ```"jmxMetrics"``` , hogy egy szakaszt ad hozzá a applicationinsights.jsfájlhoz. Megadhatja a metrika nevét, ahogy szeretné, hogy megjelenjen a Azure Portal az Application ininsights erőforrásban. Meg kell határoznia az objektum nevét és attribútumát minden összegyűjteni kívánt metrika esetében.

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>Hogyan tudja, milyen mérőszámok érhetők el a konfiguráláshoz?

Azt is megteheti, hogy ismernie kell az objektumok nevét és az attribútumokat, ezek a tulajdonságok eltérőek a különböző könyvtárak, keretrendszerek és alkalmazáskiszolgáló esetében, és gyakran nem dokumentált. Az objektumok nevének és attribútumainak lekéréséhez meg kell tekintenie a MBean fát. Az MBean egy felügyelt Java-objektum, amely egy eszközt, egy alkalmazást vagy egy erőforrást jelképez, és attribútumokkal rendelkezik. 

Az elérhető metrikák megtekintéséhez és a rendelkezésre álló metrikák böngészéséhez javasoljuk, hogy a [Java-feladatok felügyeletét](https://www.oracle.com/java/technologies/jdk-mission-control.html)használja.

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>Hogyan lehet navigálni a Java Mission Control szolgáltatásban a megfelelő metrikák eléréséhez?

A Java Mission Control eszköz futtatásakor a bal oldalon elérhető JVMs közül választhat, kattintson a megfelelő folyamatra a "JVM Browser" (böngésző megnyitása) lapon. Várjon, amíg a közös Irányítóbizottság betölti az irányítópultot a folyamathoz, válassza az alján található "MBean Browser" fület (lásd alább). A közös irányítóbizottságnak ugyanabban a mappában kell lennie, mint a JVM, és a folyamatnak/alkalmazásnak futnia kell.

![Képernyőfelvétel a MBean-böngészőről](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>Hogyan érheti el a kívánt mérőszámokat és a szükséges attribútumokat?

A MBean böngésző megnyitja a MBean fát a kibontható kategóriák listájával. A bal oldali kategória kiválasztásakor megnyílik az attribútumok listája a jobb oldalon. Az alábbi példa egy mérőszámot, az objektum nevét és az attribútumokat mutatja be. Előfordulhat, hogy az attribútumok beágyazva vannak, az alábbi példában látható módon.

![Képernyőfelvétel a közös Irányítóbizottság MBean fájáról](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>Konfiguráció – példa

A fenti képen látható módon konfigurálhat néhány mérőszámot. Az első példa egy beágyazott metrika – `LastGcInfo` amely több tulajdonságot tartalmaz, és a következőt szeretnénk rögzíteni: `GcThreadCount` .

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>A begyűjtött metrikák típusai és a rendelkezésre álló konfigurációs lehetőségek?

A numerikus és a logikai JMX mérőszámok is támogatottak, míg más típusok nem támogatottak, és a rendszer figyelmen kívül hagyja. 

Jelenleg a helyettesítő karakterek és az összesített attribútumok nem támogatottak, ezért a "object name"/"Attribute" párokat külön kell konfigurálni. 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>Hol találom a JMX metrikáit az Application ininsights szolgáltatásban?

Ha az alkalmazás fut, és a JMX metrikáit gyűjti be, megtekintheti őket a Azure Portal, és navigáljon az Application ininsight-erőforráshoz. A metrikák lapon válassza ki az alább látható legördülő listát a metrikák megtekintéséhez.

![A mérőszámok képernyőképe a portálon](media/java-ipa/jmx/jmx-portal.png)
