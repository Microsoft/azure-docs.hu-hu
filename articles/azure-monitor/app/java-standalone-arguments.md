---
title: A JVM ARG-Azure Monitor Application Insights hozzáadása Javához
description: A Java-Azure Monitor Application Insightst engedélyező JVM ARG hozzáadása
ms.topic: conceptual
ms.date: 04/16/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: bd6ed2e5c848b39ad946209d5ea92fcf6ce3b438
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625364"
---
# <a name="adding-the-jvm-arg---azure-monitor-application-insights-for-java"></a>A JVM ARG-Azure Monitor Application Insights hozzáadása Javához



## <a name="azure-environments"></a>Azure-környezetek

[App Services](../../app-service/configure-language-java.md#set-java-runtime-options)konfigurálása.

## <a name="spring-boot"></a>Spring Boot

Adja hozzá a JVM ARG `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` -t valahol `-jar` , például:

```
java -javaagent:path/to/applicationinsights-agent-3.0.2.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Spring boot a Docker belépési pontján keresztül

Ha az *exec* űrlapot használja, adja hozzá a paramétert a paraméterhez a paraméter `"-javaagent:path/to/applicationinsights-agent-3.0.2.jar"` előtt `"-jar"` (például:).

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.2.jar", "-jar", "<myapp.jar>"]
```

Ha a *rendszerhéj* -űrlapot használja, adja hozzá a JVM ARG `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` -t valahol `-jar` , például:

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.2.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat telepítve a `apt-get` vagy a használatával `yum`

Ha a Tomcat-t a vagy a használatával telepítette `apt-get` `yum` , akkor rendelkeznie kell egy fájllal `/etc/tomcat8/tomcat8.conf` .  Adja hozzá ezt a sort a fájl végéhez:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.2.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>A Tomcat a letöltés és a kicsomagolás használatával lett telepítve

Ha a Tomcat-t a letöltésével és kicsomagolásával telepítette [https://tomcat.apache.org](https://tomcat.apache.org) , akkor rendelkeznie kell egy fájllal `<tomcat>/bin/catalina.sh` .  Hozzon létre egy új fájlt a nevű könyvtárban a `<tomcat>/bin/setenv.sh` következő tartalommal:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.2.jar"
```

Ha a fájl `<tomcat>/bin/setenv.sh` már létezik, módosítsa a fájlt, és adja hozzá a következőt: `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` `CATALINA_OPTS` .


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>A Tomcat futtatása a parancssorból

Keresse meg a fájlt `<tomcat>/bin/catalina.bat` .  Hozzon létre egy új fájlt a nevű könyvtárban a `<tomcat>/bin/setenv.bat` következő tartalommal:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.2.jar
```

Az idézőjelek nem szükségesek, de ha be szeretné vonni őket, a megfelelő elhelyezés a következő:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.2.jar"
```

Ha a fájl `<tomcat>/bin/setenv.bat` már létezik, csak módosítsa a fájlt, és adja hozzá a következőt: `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` `CATALINA_OPTS` .

### <a name="running-tomcat-as-a-windows-service"></a>A Tomcat futtatása Windows-szolgáltatásként

Keresse meg a fájlt `<tomcat>/bin/tomcat8w.exe` .  Futtassa ezt a végrehajtható fájlt, és adja hozzá a parancsot a `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` `Java Options` `Java` lapon.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Önálló kiszolgáló

Hozzáadás a `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` fájl meglévő `JAVA_OPTS` környezeti változóhoz `JBOSS_HOME/bin/standalone.conf` (Linux) vagy `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.2.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Tartományi kiszolgáló

Hozzáadás `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` a meglévőhöz a `jvm-options` következőben `JBOSS_HOME/domain/configuration/host.xml` :

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.2.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Ha több felügyelt kiszolgálót futtat egyetlen gazdagépen, akkor mindegyikhez hozzá kell adnia `applicationinsights.agent.id` a `system-properties` következőhöz `server` :

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

A megadott `applicationinsights.agent.id` értéknek egyedinek kell lennie. A rendszer a applicationinsights könyvtárában létrehoz egy alkönyvtárat, mivel minden JVM-folyamathoz saját helyi applicationinsights-konfiguráció és helyi applicationinsights-naplófájl szükséges. Ha a központi gyűjtőnek jelent jelentést, a `applicationinsights.properties` fájlt a több felügyelt kiszolgáló is megosztja, ezért a megadott érték `applicationinsights.agent.id` szükséges a `agent.id` megosztott fájl beállításainak felülbírálásához. `applicationinsights.agent.rollup.id` Hasonlóképpen megadható a kiszolgálón, `system-properties` Ha a `agent.rollup.id` beállítást egy felügyelt kiszolgálón kell felülbírálni.


## <a name="jetty-9"></a>3. Jetty

Sorok hozzáadása a következőhöz `start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.2.jar
```


## <a name="payara-5"></a>5. Payara

Hozzáadás `-javaagent:path/to/applicationinsights-agent-3.0.2.jar` a meglévőhöz a `jvm-options` következőben `glassfish/domains/domain1/config/domain.xml` :

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.2.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

A felügyeleti konzol megnyitásához nyissa meg a **kiszolgálók > WebSphere Application servers > Application Servers** elemet, válassza ki a megfelelő alkalmazásokat, és kattintson a következőre: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
Az "általános JVM argumentumok" elemnél adja hozzá a következőket:
```
-javaagent:path/to/applicationinsights-agent-3.0.2.jar
```
Ezután mentse és indítsa újra az alkalmazást.


## <a name="openliberty-18"></a>OpenLiberty 18

Hozzon létre egy új fájlt `jvm.options` a kiszolgáló könyvtárában (például `<openliberty>/usr/servers/defaultServer` ), és adja hozzá a következő sort:
```
-javaagent:path/to/applicationinsights-agent-3.0.2.jar
```
