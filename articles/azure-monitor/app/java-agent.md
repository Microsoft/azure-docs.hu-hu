---
title: Java-webalkalmazások teljesítményének monitorozása – Azure Application Insights
description: A Java-webhely kiterjesztett teljesítmény-és használati figyelése Application Insights.
ms.topic: conceptual
ms.date: 01/10/2019
ms.custom: devx-track-java
ms.openlocfilehash: 9cce87fa742df2e623217b5b0f72ad19814fd3b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87371362"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Függőségek, kifogott kivételek és metódus-végrehajtási idők figyelése Java-webalkalmazásokban


Ha a [Java-webalkalmazást Application Insights][java]használatával állította be, a Java-ügynökkel mélyebb elemzéseket kaphat, kód módosítása nélkül:

* **Függőségek:** Az alkalmazás által más összetevőkre irányuló hívásokkal kapcsolatos információk, beleértve a következőket:
  * Az Apache HttpClient, a OkHttp és a rögzített **http-hívások** rögzítése megtörtént `java.net.HttpURLConnection` .
  * A Jedis ügyféllel készített **Redis-hívások** rögzítése történik.
  * **JDBC-lekérdezések** – a MySQL és a PostgreSQL esetében, ha a hívás 10 másodpercnél hosszabb időt vesz igénybe, az ügynök jelentést készít a lekérdezési tervről.

* **Alkalmazás naplózása:** Az alkalmazás naplófájljainak rögzítése és korrelálása HTTP-kérelmekkel és egyéb telemetria
  * **Log4j 1,2**
  * **Log4j2**
  * **Logback**

* **Jobb működés elnevezése:** (a portálon található kérelmek összesítéséhez használják)
  * **Rugó** – alapján `@RequestMapping` .
  * **Jax-RS** -alapján `@Path` . 

A Java-ügynök használatához telepítenie kell a-kiszolgálóra. A webalkalmazásokat a [Application Insights Java SDK][java]-val kell kiépíteni. 

## <a name="install-the-application-insights-agent-for-java"></a>A Javához készült Application Insights-ügynök telepítése
1. [Töltse le az ügynököt](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) a Java-kiszolgálót futtató számítógépre. Ügyeljen rá, hogy a Java ügynök verziója és az Application Insights Java SDK core és web csomagok verziója azonos legyen.
2. Szerkessze az alkalmazáskiszolgáló indítási parancsfájlját, és adja hozzá a következő JVM argumentumot:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Például a Tomcat egy Linux rendszerű gépen:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Indítsa újra az alkalmazást.

## <a name="configure-the-agent"></a>Az ügynök konfigurálása
Hozzon létre egy nevű fájlt, `AI-Agent.xml` és helyezze ugyanabba a mappába, amelyben az ügynök jar-fájlja található.

Adja meg az XML-fájl tartalmát. Szerkessze az alábbi példát a kívánt funkciók belefoglalásához vagy kihagyásához.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>További konfiguráció (Spring boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Az Azure App Services esetében tegye a következőket:

* Válassza a Beállítások > Alkalmazásbeállítások lehetőséget.
* Az alkalmazásbeállításoknál adjon meg egy új kulcs-érték párt:

Kulcs: `JAVA_OPTS` érték: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

A Java-ügynök legújabb verziójáért tekintse meg a kiadásokat [itt](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

Az ügynököt erőforrásként kell csomagolni a projektben úgy, hogy az a D:/Home/site/wwwroot/könyvtárban végződik. A **fejlesztői eszközök**speciális eszközök hibakeresési konzolján ellenőrizheti, hogy az ügynök a megfelelő app Service könyvtárban található-e,  >  **Advanced Tools**  >  **Debug Console** és megvizsgálja a hely könyvtárának tartalmát.    

* Mentse a beállításokat, és indítsa újra az alkalmazást. (Ezek a lépések csak Windows rendszeren futó App Services vonatkoznak.)

> [!NOTE]
> AI-Agent.xml és az ügynök jar-fájljának ugyanabban a mappában kell lennie. Ezeket gyakran együtt helyezik el a `/resources` projekt mappájába.  

#### <a name="enable-w3c-distributed-tracing"></a>W3C elosztott nyomkövetés engedélyezése

Adja hozzá a következőt a AI-Agent.xmlhoz:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> A visszafelé kompatibilitási mód alapértelmezés szerint engedélyezve van, és a enableW3CBackCompat paraméter nem kötelező, és csak akkor használható, ha ki szeretné kapcsolni. 

Ideális esetben ez az eset, amikor az összes szolgáltatás frissítve lett a W3C protokollt támogató SDK-k újabb verziójára. Javasoljuk, hogy a lehető leghamarabb váltson a W3C-támogatással rendelkező SDK-k újabb verziójára.

Győződjön meg arról, hogy a ** [bejövő](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) és a kimenő (ügynök) konfigurációk** is pontosan azonosak.

## <a name="view-the-data"></a>Az adatok megtekintése
A Application Insights erőforrásban [a teljesítmény csempén][metrics]a távoli függőségi és metódus-végrehajtási idők szerepelnek.

A függőség, a kivétel és a metódus-jelentések egyes példányainak kereséséhez nyissa meg a [keresést][diagnostic].

[Függőségi problémák diagnosztizálása – további információ](./asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Kérdése van? Problémákat tapasztal?
* Nincs adat? [Tűzfal-kivételek beállítása](./ip-addresses.md)
* [A Java hibaelhárítása](java-troubleshoot.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../platform/metrics-charts.md

