---
title: Azure Application Insights – Függőségek automatikus gyűjteményének | Microsoft Docs
description: Application Insights függőségek automatikus gyűjtése és vizualizációja
ms.topic: reference
ms.custom: devx-track-dotnet
ms.date: 05/06/2020
ms.openlocfilehash: aa4d39ca8964e95ca787d236223e2b475a9597c1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873828"
---
# <a name="dependency-auto-collection"></a>Függőségek automatikus összegyűjtése

Az alábbi lista azokat a függőségi hívásokat tartalmazza, amelyek automatikusan függőségként vannak észlelve anélkül, hogy az alkalmazás kódját bármilyen további módosításra lenne szükség. Ezek a függőségek az Alkalmazástérkép és a [Application Insights-diagnosztikai](./app-map.md) [nézetekben](./transaction-diagnostics.md) vannak ábrázolva. Ha a függősége nem szerepel az alábbi listán, manuálisan is nyomon követheti a függőség nyomon [követése hívással.](./api-custom-events-metrics.md#trackdependency)

## <a name="net"></a>.NET

| Alkalmazás-keretrendszerek| Verziók |
| ------------------------|----------|
| ASP.NET webes formátumok | 4.5+ |
| ASP.NET, MVC | 4+ |
| ASP.NET WebAPI | 4.5+ |
| ASP.NET Core | 1.1+ |
| <b> Kommunikációs kódtárak</b> |
| [HttpClient](https://dotnet.microsoft.com) | 4.5+, .NET Core 1.1+ |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+, NuGet 4.3.0 |
| [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/1.1.2)| 1.1.0 – a legújabb stabil kiadás. (Lásd az alábbi megjegyzést.)
| [EventHubs ügyféloldali SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus ügyféloldali SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Storage-ügyfelek</b>|  |
| ADO.NET | 4.5+ |

> [!NOTE]
> A Microsoft.Data.SqlClient régebbi verzióinak van egy ismert problémája. [](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1347) Javasoljuk, hogy a probléma megoldásához az 1.1.0-s vagy újabb kiadást használja. Entity Framework Core nem feltétlenül rendelkezik a Microsoft.Data.SqlClient legújabb stabil kiadásával, ezért azt javasoljuk, hogy a probléma elkerülése érdekében győződjön meg arról, hogy legalább 1.1.0-s verzióval rendelkezik.   


## <a name="java"></a>Java
| Alkalmazáskiszolgálók | Verziók |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Alkalmazás-keretrendszerek </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>Kommunikációs kódtárak</b> |  |
| [Apache HTTP-ügyfél](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3+<sup>†</sup> |
| <b>Storage-ügyfelek</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [PostgreSQL (bétaverziós támogatás)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [Mysql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>Naplózási kódtárak</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>Metrikakódtárak</b> |  |
| JMX | 1.0+ |

> [!NOTE]
> *A reaktív programtámogatás kivételével.
> <br>†A [JVM-ügynök telepítésének igénylése.](./java-agent.md#install-the-application-insights-agent-for-java)

## <a name="nodejs"></a>Node.js

| Kommunikációs kódtárak | Verziók |
| ------------------------|----------|
| [HTTP,](https://nodejs.org/api/http.html) [HTTPS](https://nodejs.org/api/https.html) | 0.10+ |
| <b>Storage-ügyfelek</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.x – 3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 – 2.16.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x – 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x – 2.x |
| <b>Naplózási kódtárak</b> | |
| [Konzol](https://nodejs.org/api/console.html) | 0.10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x – 3.x |

## <a name="javascript"></a>JavaScript

| Kommunikációs kódtárak | Verziók |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Mind |

## <a name="next-steps"></a>Következő lépések

- Egyéni függőségkövetés beállítása [a .NET-hez.](./asp-net-dependencies.md)
- Egyéni függőségkövetés beállítása [Java-hoz.](./java-agent.md)
- Egyéni függőségkövetés beállítása az [OpenCensus Pythonhoz.](./opencensus-python-dependency.md)
- [Egyéni függőségi telemetria írása](./api-custom-events-metrics.md#trackdependency)
- Az [adattípusokról és](./data-model.md) Application Insights az adatmodellről.
- Tekintse meg [a Application Insights.](./platforms.md)

