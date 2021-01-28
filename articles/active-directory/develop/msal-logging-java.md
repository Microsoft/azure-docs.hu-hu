---
title: Hibák és kivételek naplózása a MSAL for Java esetében
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan naplózhat hibákat és kivételeket a MSAL for Java szolgáltatásban
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d3fa13a6751b2d8acf1fc99aecbf174f1823fb0b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954918"
---
# <a name="logging-in-msal-for-java"></a>Naplózás a Javához készült MSAL-ben

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-java-logging"></a>MSAL a Java-naplózáshoz

A MSAL for Java lehetővé teszi, hogy az alkalmazással már használt naplózási könyvtárat használja, amennyiben az kompatibilis a SLF4J-mel. A MSAL for Java a Java (SLF4J) egyszerű [naplózási](http://www.slf4j.org/) homlokzatát használja egyszerű homlokzatként vagy absztraktként a különböző naplózási keretrendszerek, például a [Java. util. Logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), a [Logback](http://logback.qos.ch/) és a [Log4j](https://logging.apache.org/log4j/2.x/). A SLF4J lehetővé teszi a felhasználó számára, hogy a központi telepítési időszakban csatlakoztassa a kívánt naplózási keretrendszert.

Ha például a Logback-t az alkalmazás naplózási keretrendszereként szeretné használni, adja hozzá a Logback függőséget az alkalmazás Maven Pom-fájljához:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Ezután adja hozzá a Logback konfigurációs fájlt:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

A SLF4J a telepítéskor automatikusan kötést hoz létre a Logback. A rendszer a MSAL-naplókat a konzolra írja.

A más naplózási keretrendszerek kötésével kapcsolatos utasításokért tekintse meg a [SLF4J kézikönyvét](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Személyes és szervezeti adatok

Alapértelmezés szerint a MSAL naplózása nem rögzíti vagy nem naplózza személyes vagy szervezeti adataikat. A következő példában a személyes vagy szervezeti adatai naplózása alapértelmezés szerint ki van kapcsolva:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

A személyes és szervezeti adatnaplózás bekapcsolásához állítsa be `logPii()` az ügyfélalkalmazás-szerkesztőt. Ha bekapcsolja a személyes vagy szervezeti adatnaplózást, az alkalmazásnak felelősséget kell vállalnia a fokozottan bizalmas adatok biztonságos kezeléséhez és a szabályozási követelmények teljesítéséhez.

A következő példában a személyes vagy szervezeti adatai naplózása engedélyezve van:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="next-steps"></a>Következő lépések

További példákért tekintse meg a [Microsoft Identity platform Code Samples](sample-v2-code.md)című témakört.