---
title: Java-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhat Java-alkalmazásokat Azure App Serviceon való futtatáshoz. A cikk a leggyakoribb konfigurációs feladatokat ismerteti.
keywords: Azure app Service, webalkalmazás, Windows, OSS, Java, tomcat, JBoss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: cc532c5ac6babb8378860ac5049e931cc7657932
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629257"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Java-alkalmazás konfigurálása Azure App Servicehoz

Azure App Service lehetővé teszi a Java-fejlesztők számára a Java SE-, tomcat-és JBoss EAP-webalkalmazások gyors fejlesztését, üzembe helyezését és méretezését egy teljes körűen felügyelt szolgáltatásban. Alkalmazásokat telepíthet a Maven beépülő modulokkal, a parancssorból vagy a szerkesztőket, például a IntelliJ, az Eclipse vagy a Visual Studio Code-ban.

Ez az útmutató a Java-fejlesztőknek App Service használatával kapcsolatos főbb fogalmakat és útmutatást tartalmazza. Ha még soha nem használta a Azure App Servicet, először olvassa el a [Java](quickstart-java.md) rövid útmutatóját. A Java-fejlesztésre nem jellemző App Service használatának általános kérdései a [app Service gyakori](faq-configuration-and-management.md)kérdések című szakaszban találhatók.

## <a name="deploying-your-app"></a>Az alkalmazás üzembe helyezése

A [mavenhez készült Azure Web App beépülő modullal](https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) üzembe helyezheti a. War vagy a. jar fájlokat. A népszerű ide-val történő üzembe helyezést a [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) vagy [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse)is támogatja.

Ellenkező esetben a telepítési módszer az archiválás típusától függ:

### <a name="java-se"></a>Java SE

Ha a. jar fájlokat a Java SE-re szeretné telepíteni, használja a `/api/zipdeploy/` kudu-hely végpontját. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt](./deploy-zip.md#rest).

### <a name="tomcat"></a>Tomcat

A. War fájlok Tomcatbe való üzembe helyezéséhez használja a `/api/wardeploy/` végpontot az archív fájl közzétételéhez. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt](./deploy-zip.md#deploy-war-file).

::: zone pivot="platform-linux"

### <a name="jboss-eap"></a>JBoss EAP

Ha a. War fájlokat a JBoss-ba szeretné telepíteni, használja a `/api/wardeploy/` végpontot az archív fájl közzétételéhez. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt](./deploy-zip.md#deploy-war-file).

A. EAR fájlok telepítéséhez [használja az FTP](deploy-ftp.md)-t.

::: zone-end

Ne telepítse a. War vagy a. jar fájlt FTP használatával. Az FTP-eszköz indítási parancsfájlok, függőségek vagy más futásidejű fájlok feltöltésére szolgál. A webalkalmazások üzembe helyezése nem optimális megoldás.

## <a name="logging-and-debugging-apps"></a>Alkalmazások naplózása és hibakeresése

A teljesítménnyel kapcsolatos jelentések, a forgalmi vizualizációk és az állapot-kivizsgálások a Azure Portalon keresztül érhetők el az egyes alkalmazásokhoz. További információ: [Azure app Service diagnosztika áttekintése](overview-diagnostics.md).

### <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

További információ: [stream-naplók Cloud Shellban](troubleshoot-diagnostic-logs.md#in-cloud-shell).

::: zone pivot="platform-linux"

### <a name="ssh-console-access"></a>SSH-konzolhoz való hozzáférés

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="troubleshooting-tools"></a>Hibaelhárítási eszközök

A beépített Java-lemezképek az [alpesi Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) operációs rendszeren alapulnak. A `apk` Package Manager használatával telepítse a hibaelhárítási eszközöket és parancsokat.

::: zone-end

### <a name="flight-recorder"></a>Flight Recorder

Az Azul JVMs-t használó App Service Java-futtatókörnyezetek a Zulu Flight Recorder használatával jönnek. Ezt felhasználhatja a JVM, a rendszerek és az alkalmazások eseményeinek rögzítéséhez, valamint a Java-alkalmazásokkal kapcsolatos problémák elhárításához.

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>Időzített rögzítés

Ha időzített rögzítést szeretne készíteni, szüksége lesz a Java-alkalmazás PID-re (Process ID). A PID megkereséséhez nyisson meg egy böngészőt a webalkalmazás SCM-webhelyéhez a https://<saját-hely neve>. scm.azurewebsites.net/ProcessExplorer/. Ezen az oldalon a webalkalmazás futó folyamatai láthatók. Keresse meg a "Java" nevű folyamatot a táblában, és másolja a megfelelő PID-t (Process ID).

Ezután nyissa meg a **hibakeresési konzolt** az SCM-hely felső eszköztárán, és futtassa a következő parancsot. Cserélje le `<pid>` a helyére a korábban átmásolt folyamat azonosítóját. Ez a parancs elindítja a Java-alkalmazás 30 másodperces adatrögzítését, és létrehozza a `timed_recording_example.jfr` címtárban megnevezett fájlt `D:\home` .

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

SSH-t a App Serviceba, és futtassa a `jcmd` parancsot, és tekintse meg az összes futó Java-folyamat listáját. A jcmd mellett egy folyamat-azonosító számmal (PID) rendelkező Java-alkalmazást is látnia kell.

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Hajtsa végre az alábbi parancsot a JVM 30 másodperces rögzítésének elindításához. Ezzel felveszi a JVM, és létrehoz egy *jfr_example. JFR* nevű JFR-fájlt a saját könyvtárában. (A 116 helyére írja be a Java-alkalmazás PID-t.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

A 30 másodperces intervallumban ellenőrizheti, hogy a rögzítés a futtatásával zajlik-e `jcmd 116 JFR.check` . Ez a művelet megjeleníti a megadott Java-folyamat összes felvételét.

#### <a name="continuous-recording"></a>Folyamatos rögzítés

A Zulu Flight Recorder segítségével folyamatosan, a Java-alkalmazásait a futtatási teljesítményre ([forrás](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)) gyakorolt minimális hatású profilt használhatja. Ehhez futtassa az alábbi Azure CLI-parancsot egy JAVA_OPTS nevű alkalmazás-beállítás létrehozásához a szükséges konfigurációval. Az alkalmazás elindításakor a rendszer átadja a parancsnak a JAVA_OPTS Alkalmazásbeállítások tartalmát `java` .

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

A rögzítés megkezdése után bármikor leküldheti az aktuális rögzítési időt a parancs használatával `JFR.dump` .

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

::: zone-end

#### <a name="analyze-jfr-files"></a>`.jfr`Fájlok elemzése

A [FTPS](deploy-ftp.md) használatával töltse le a JFR-fájlt a helyi gépre. A JFR-fájl elemzéséhez töltse le és telepítse a [Zulu Mission Controlt](https://www.azul.com/products/zulu-mission-control/). A Zulu-feladatok ellenőrzésével kapcsolatos utasításokért tekintse meg az [Azul dokumentációját](https://docs.azul.com/zmc/) és a [telepítési utasításokat](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

### <a name="app-logging"></a>Alkalmazás naplózása

::: zone pivot="platform-windows"

Az Azure Portal vagy az [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) -n keresztül történő [alkalmazás-naplózás](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) engedélyezésével beállíthatja, hogy a app Service az alkalmazás szabványos konzoljának kimenetét és standard konzoljának hibáit a helyi fájlrendszerbe vagy az Azure-Blob Storageba írja. A helyi App Service filesystem-példányra való naplózás a konfigurálás után 12 órával le van tiltva. Ha nagyobb adatmegőrzésre van szüksége, konfigurálja úgy az alkalmazást, hogy egy blob Storage-tárolóba írja a kimenetet. A Java-és a Tomcat-alkalmazás naplói a */Home/LogFiles/Application/* könyvtárban találhatók.

::: zone-end
::: zone pivot="platform-linux"

Az Azure Portal vagy az [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) -n keresztül történő [alkalmazás-naplózás](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) engedélyezésével beállíthatja, hogy a app Service az alkalmazás szabványos konzoljának kimenetét és standard konzoljának hibáit a helyi fájlrendszerbe vagy az Azure-Blob Storageba írja. Ha nagyobb adatmegőrzésre van szüksége, konfigurálja úgy az alkalmazást, hogy egy blob Storage-tárolóba írja a kimenetet. A Java-és a Tomcat-alkalmazás naplói a */Home/LogFiles/Application/* könyvtárban találhatók.

Az Azure Blob Storage Linux-alapú App Services naplózása csak [Azure monitor (előzetes verzió)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) használatával konfigurálható 

::: zone-end

Ha az alkalmazás [Logback](https://logback.qos.ch/) -t vagy [Log4j](https://logging.apache.org/log4j) -t használ a nyomkövetéshez, továbbíthatja ezeket a nyomkövetéseket az Azure Application Insightsba való áttelepítéshez a naplózási keretrendszer konfigurációs utasításait követve, a [Java-nyomkövetési naplók megismeréséhez Application Insights](../azure-monitor/app/java-trace-logs.md).

## <a name="customization-and-tuning"></a>Testreszabás és Finomhangolás

A Linux rendszerhez készült Azure App Service a Azure Portal és a CLI használatával támogatja a Box finomhangolását és testreszabását. Tekintse át a következő cikkeket a nem Java-specifikus webalkalmazás-konfigurációhoz:

- [Alkalmazásbeállítások konfigurálása](configure-common.md#configure-app-settings)
- [Egyéni tartomány beállítása](app-service-web-tutorial-custom-domain.md)
- [SSL-kötések konfigurálása](configure-ssl-bindings.md)
- [CDN hozzáadása](../cdn/cdn-add-to-web-app.md)
- [A kudu hely konfigurálása](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


### <a name="set-java-runtime-options"></a>Java-futtatókörnyezet beállításainak megadása

A lefoglalt memória vagy más JVM futásidejű beállításainak megadásához hozzon létre egy nevű [alkalmazást](configure-common.md#configure-app-settings) `JAVA_OPTS` a következő beállításokkal:. App Service átadja ezt a beállítást környezeti változóként a Java futtatókörnyezetnek a indításakor.

A Azure Portal a webalkalmazás **Alkalmazásbeállítások** területén hozzon létre egy nevű új alkalmazás-beállítást `JAVA_OPTS` , amely tartalmazza a további beállításokat, például a következőt: `-Xms512m -Xmx1204m` .

Az Alkalmazásbeállítások a Maven beépülő modulból történő konfigurálásához adja hozzá a beállítás/érték címkéket az Azure beépülő modul szakaszban. A következő példa egy meghatározott minimális és maximális Java-halom méretét állítja be:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

A App Service tervében egyetlen üzembe helyezési ponttal rendelkező fejlesztők a következő lehetőségeket használhatják:

- B1 és S1 példányok: `-Xms1024m -Xmx1024m`
- B2 és S2 példányok: `-Xms3072m -Xmx3072m`
- B3-as és S3-példányok: `-Xms6144m -Xmx6144m`

Az alkalmazás-halom beállításainak hangolásához tekintse át a App Service terv részleteit, és vegye figyelembe, hogy a memória optimális elosztása érdekében több alkalmazásra és üzembe helyezési pontra van szükség.

### <a name="turn-on-web-sockets"></a>Webes szoftvercsatornák bekapcsolása

A WebSockets támogatásának bekapcsolása a Azure Portal az alkalmazás **alkalmazás-beállításaiban** . A beállítás érvénybe léptetéséhez újra kell indítania az alkalmazást.

Kapcsolja be a web socket-támogatást az Azure CLI használatával a következő paranccsal:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Ezután indítsa újra az alkalmazást:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Alapértelmezett karakterkódolás beállítása

A Azure Portal a webalkalmazás **Alkalmazásbeállítások** területén hozzon létre egy új, értékkel ellátott alkalmazás-beállítást `JAVA_OPTS` `-Dfile.encoding=UTF-8` .

Azt is megteheti, hogy az App Service Maven beépülő modullal konfigurálhatja az alkalmazás beállításait. Adja hozzá a beállítás neve és értéke címkéket a beépülő modul konfigurációjában:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>JSP-fájlok előre fordítása

A Tomcat-alkalmazások teljesítményének növelése érdekében lefordíthatja a JSP-fájljait, mielőtt telepítené a App Service. Használhatja az Apache parittya által biztosított [Maven beépülő modult](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) , vagy használja ezt az [Ant-Build fájlt](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Biztonságos alkalmazások

A App Serviceban futó Java-alkalmazásokhoz ugyanaz a [biztonsági eljárások](../security/fundamentals/paas-applications-using-app-services.md) tartoznak, mint más alkalmazások.

### <a name="authenticate-users-easy-auth"></a>Felhasználók hitelesítése (egyszerű hitelesítés)

Az alkalmazás hitelesítésének beállítása a Azure Portal a **hitelesítés és engedélyezés** lehetőséggel. Itt engedélyezheti a hitelesítést Azure Active Directory vagy közösségi bejelentkezéssel, például a Facebook, a Google vagy a GitHub használatával. Azure Portal konfiguráció csak egyetlen hitelesítési szolgáltató konfigurálásakor működik. További információ: [a app Service alkalmazás konfigurálása Azure Active Directory bejelentkezési adatok használatára](configure-authentication-provider-aad.md) és a kapcsolódó cikkek más identitás-szolgáltatóknak való használatára. Ha több bejelentkezési szolgáltatót is engedélyeznie kell, kövesse az [app Service-hitelesítés testreszabása](app-service-authentication-how-to.md) című cikk utasításait.

#### <a name="java-se"></a>Java SE

A Spring boot-fejlesztők a [Azure Active Directory Spring boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory) használatával biztosíthatják az alkalmazásokat az ismerős rugós biztonsági megjegyzésekkel és API-kkal. Ügyeljen arra, hogy növelje a fejléc maximális méretét az *Application. properties* fájlban. Javasoljuk, hogy a értékét `16384` .

#### <a name="tomcat"></a>Tomcat

A Tomcat-alkalmazás közvetlenül a servletből férhet hozzá a felhasználó jogcímeihez, ha a fő objektumot egy Térkép objektummá helyezi. A Térkép objektum az egyes jogcím-típusokat az adott típusú jogcímek gyűjteményéhez rendeli. Az alábbi kódban a `request` egy példánya `HttpServletRequest` .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Most már megvizsgálhatja az `Map` objektumot egy adott jogcím esetében is. A következő kódrészlet például megismétli az összes jogcím-típust, és kinyomtatja az egyes gyűjtemények tartalmát.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

A felhasználók kijelentkezéséhez használja az `/.auth/ext/logout` elérési utat. További műveletek elvégzéséhez tekintse meg [app Service hitelesítési és engedélyezési használati](./app-service-authentication-how-to.md)dokumentációját. A Tomcat [HttpServletRequest felületén](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) és annak módszerein is hivatalos dokumentáció található. A következő servlet metódusok is hidratálva vannak a App Service konfigurációja alapján:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

A funkció letiltásához hozzon létre egy nevű Alkalmazásbeállítás `WEBSITE_AUTH_SKIP_PRINCIPAL` értéket `1` . A App Service által hozzáadott összes servlet-szűrő letiltásához hozzon létre egy nevű beállítást a következő `WEBSITE_SKIP_FILTERS` értékkel: `1` .

### <a name="configure-tlsssl"></a>A TLS/SSL konfigurálása

A meglévő SSL-tanúsítvány feltöltéséhez és az alkalmazás tartománynevéhez való kötéséhez kövesse az [Egyéni DNS-név biztonságossá tétele SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md) található utasításokat. Alapértelmezés szerint az alkalmazás továbbra is engedélyezi a HTTP-kapcsolatokat – az oktatóanyag adott lépéseinek végrehajtásával kényszerítheti az SSL és a TLS használatát.

### <a name="use-keyvault-references"></a>Kulcstartó-hivatkozások használata

Az [Azure](../key-vault/general/overview.md) kulcstartó központosított titkos felügyeletet biztosít a hozzáférési házirendekkel és a naplózási előzményekkel. A kulcsok (például jelszavak vagy kapcsolati karakterláncok) a kulcstartóban tárolhatók, és a titkos kulcsokat az alkalmazásban környezeti változókon keresztül érhetik el.

Először is kövesse az alkalmazáshoz [való hozzáférés megadására](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) vonatkozó utasításokat, és a [titkos kulcshoz tartozó kulcstároló-hivatkozást egy Alkalmazásbeállítások alapján](app-service-key-vault-references.md#reference-syntax)végezze el a Key Vault. Ellenőrizheti, hogy a hivatkozás feloldja-e a titkos kulcsot a környezeti változó kinyomtatásával, miközben távolról fér hozzá a App Service terminálhoz.

Ha ezeket a titkokat be szeretné szúrni a Spring vagy a Tomcat konfigurációs fájljába, használja a környezeti változók befecskendezésének szintaxisát ( `${MY_ENV_VAR}` ). A Spring konfigurációs fájlok esetében tekintse meg ezt a dokumentációt a [külső konfigurációkról](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>A Java Key Store használata

Alapértelmezés szerint a rendszer a [app Service Linuxra feltöltött](configure-ssl-certificate.md) nyilvános vagy privát tanúsítványokat a tároló indításakor betölti a megfelelő Java-kulcs-tárolókban. A tanúsítvány feltöltése után újra kell indítania a App Service, hogy betöltse a Java Key Store-ba. A nyilvános tanúsítványok be vannak töltve a Kulcstárolóba `$JAVA_HOME/jre/lib/security/cacerts` , és a privát tanúsítványok a következő helyen tárolódnak: `$JAVA_HOME/lib/security/client.jks` .

További konfigurálásra lehet szükség a JDBC-kapcsolatok a Java Key Store-ban található tanúsítványokkal való titkosításához. Tekintse meg a kiválasztott JDBC-illesztőprogram dokumentációját.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>A Java-kulcs tárolójának inicializálása

Az objektum inicializálásához `import java.security.KeyStore` töltse be a-tároló fájlját a jelszóval. A Key Stores alapértelmezett jelszava a "changeit".

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>A kulcstároló manuális betöltése

A tanúsítványokat manuálisan is betöltheti a Key Store-ba. Hozzon létre egy alkalmazás-beállítást, `SKIP_JAVA_KEYSTORE_LOAD` amelynek a értéke `1` letiltva app Service a tanúsítványok automatikus betöltése a kulcstárolóba. A App Service a Azure Portal keresztül feltöltött nyilvános tanúsítványokat a rendszer a következő helyen tárolja: `/var/ssl/certs/` . A privát tanúsítványok a alatt találhatók `/var/ssl/private/` .

A Java-kulcs eszköz interaktív vagy hibakereséséhez [Nyisson meg egy SSH-kapcsolatot](configure-linux-open-ssh-session.md) a app Service és futtassa a parancsot `keytool` . A parancsok listáját a [legfontosabb eszköz dokumentációjában](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) találja. A Webáruház API-val kapcsolatos további információkért tekintse meg [a hivatalos dokumentációt](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

::: zone-end

## <a name="configure-apm-platforms"></a>APM-platformok konfigurálása

Ez a szakasz bemutatja, hogyan csatlakoztathatók a Linux rendszeren üzembe Azure App Service helyezett Java-alkalmazások a NewRelic és a AppDynamics Application Performance monitoring (APM) platformokkal.

### <a name="configure-new-relic"></a>Új ereklye konfigurálása

::: zone pivot="platform-windows"

1. NewRelic-fiók létrehozása a [NewRelic.com](https://newrelic.com/signup) -ben
2. Töltse le a Java-ügynököt a NewRelic webhelyről, és a fájl neve hasonló lesz a *newrelic-java-x.x.x.ziphoz*.
3. A licenckulcs másolásához az ügynököt később kell konfigurálnia.
4. [SSH-t a app Service-példányba](configure-linux-open-ssh-session.md) , és hozzon létre egy új címtár- */Home/site/wwwroot/APM*.
5. Töltse fel a kicsomagolt NewRelic Java-ügynök fájljait egy könyvtárba a */Home/site/wwwroot/APM* alatt. Az ügynök fájljainak a */Home/site/wwwroot/APM/newrelic*-ben kell lenniük.
6. Módosítsa a YAML fájlt a */Home/site/wwwroot/APM/newrelic/newrelic.YML* címen, és cserélje le a helyőrző licenc értékét a saját licenckulcs használatára.
7. A Azure Portal tallózással keresse meg az alkalmazást App Service és hozzon létre egy új alkalmazás-beállítást.

    - **Java SE** -alkalmazások esetén hozzon létre egy nevű környezeti változót `JAVA_OPTS` az értékkel `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - A **tomcat** esetében hozzon létre egy nevű környezeti változót `CATALINA_OPTS` az értékkel `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end
::: zone pivot="platform-linux"

1. NewRelic-fiók létrehozása a [NewRelic.com](https://newrelic.com/signup) -ben
2. Töltse le a Java-ügynököt a NewRelic webhelyről, és a fájl neve hasonló lesz a *newrelic-java-x.x.x.ziphoz*.
3. A licenckulcs másolásához az ügynököt később kell konfigurálnia.
4. [SSH-t a app Service-példányba](configure-linux-open-ssh-session.md) , és hozzon létre egy új címtár- */Home/site/wwwroot/APM*.
5. Töltse fel a kicsomagolt NewRelic Java-ügynök fájljait egy könyvtárba a */Home/site/wwwroot/APM* alatt. Az ügynök fájljainak a */Home/site/wwwroot/APM/newrelic*-ben kell lenniük.
6. Módosítsa a YAML fájlt a */Home/site/wwwroot/APM/newrelic/newrelic.YML* címen, és cserélje le a helyőrző licenc értékét a saját licenckulcs használatára.
7. A Azure Portal tallózással keresse meg az alkalmazást App Service és hozzon létre egy új alkalmazás-beállítást.
   
    - **Java SE** -alkalmazások esetén hozzon létre egy nevű környezeti változót `JAVA_OPTS` az értékkel `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - A **tomcat** esetében hozzon létre egy nevű környezeti változót `CATALINA_OPTS` az értékkel `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end

>  Ha már rendelkezik környezeti változóval a vagy a esetében `JAVA_OPTS` `CATALINA_OPTS` , az `-javaagent:/...` aktuális érték végéhez fűzze hozzá a kapcsolót.

### <a name="configure-appdynamics"></a>AppDynamics konfigurálása

::: zone pivot="platform-windows"

1. AppDynamics-fiók létrehozása a [AppDynamics.com](https://www.appdynamics.com/community/register/) -ben
2. Töltse le a Java-ügynököt a AppDynamics webhelyről, a fájl neve hasonló lesz *AppServerAgent-x.x.x.xxxxx.zip*
3. Hozzon létre egy új címtár- */Home/site/wwwroot/APM* a [kudu-konzol](https://github.com/projectkudu/kudu/wiki/Kudu-console) használatával.
4. Töltse fel a Java-ügynök fájljait egy könyvtárba a */Home/site/wwwroot/APM* alatt. Az ügynök fájljainak a */Home/site/wwwroot/APM/appdynamics*-ben kell lenniük.
5. A Azure Portal tallózással keresse meg az alkalmazást App Service és hozzon létre egy új alkalmazás-beállítást.

   - **Java SE** -alkalmazások esetén hozzon létre egy nevű környezeti változót, amelynek `JAVA_OPTS` értéke a `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` app Service neve.
   - A **tomcat** -alkalmazások esetében hozzon létre egy nevű környezeti változót, amelynek `CATALINA_OPTS` értéke a `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` app Service neve.

::: zone-end
::: zone pivot="platform-linux"

1. AppDynamics-fiók létrehozása a [AppDynamics.com](https://www.appdynamics.com/community/register/) -ben
2. Töltse le a Java-ügynököt a AppDynamics webhelyről, a fájl neve hasonló lesz *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH-t a app Service-példányba](configure-linux-open-ssh-session.md) , és hozzon létre egy új címtár- */Home/site/wwwroot/APM*.
4. Töltse fel a Java-ügynök fájljait egy könyvtárba a */Home/site/wwwroot/APM* alatt. Az ügynök fájljainak a */Home/site/wwwroot/APM/appdynamics*-ben kell lenniük.
5. A Azure Portal tallózással keresse meg az alkalmazást App Service és hozzon létre egy új alkalmazás-beállítást.

   - **Java SE** -alkalmazások esetén hozzon létre egy nevű környezeti változót, amelynek `JAVA_OPTS` értéke a `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` app Service neve.
   - A **tomcat** -alkalmazások esetében hozzon létre egy nevű környezeti változót, amelynek `CATALINA_OPTS` értéke a `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` app Service neve.

::: zone-end

> [!NOTE]
>  Ha már rendelkezik környezeti változóval a vagy a esetében `JAVA_OPTS` `CATALINA_OPTS` , az `-javaagent:/...` aktuális érték végéhez fűzze hozzá a kapcsolót.

## <a name="configure-data-sources"></a>Adatforrások konfigurálása

### <a name="java-se"></a>Java SE

Ha a Spring boot-alkalmazásokban lévő adatforrásokhoz szeretne csatlakozni, javasoljuk, hogy hozzon létre kapcsolati karakterláncokat, és szúrja be őket az *Application. properties* fájlba.

1. A App Service lapon a konfiguráció szakaszban adja meg a karakterlánc nevét, illessze be a JDBC-kapcsolódási karakterláncot az érték mezőbe, és állítsa a típust "Custom" értékre. Ezt a kapcsolási karakterláncot tárolóhely-beállításként is megadhatja.

    Ez a kapcsolódási sztring elérhető az alkalmazáshoz egy nevű környezeti változóként `CUSTOMCONNSTR_<your-string-name>` . Például a fentiekben létrehozott kapcsolatok karakterlánc lesz elnevezve `CUSTOMCONNSTR_exampledb` .

2. Az *Application. properties* fájlban adja meg ezt a hivatkozási karakterláncot a környezeti változó nevével. Példánkban a következőt fogjuk használni.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Erről a témakörről a [Spring boot dokumentációjában](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) talál további információt az adathozzáférésről és a [külső konfigurációkról](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) .

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

Ezek az utasítások az összes adatbázis-kapcsolatra érvényesek. A helyőrzőket ki kell töltenie a kiválasztott adatbázis illesztőprogram-osztályának nevével és JAR-fájljával. A megadott tábla az osztályok neveivel és az illesztőprogramok letöltésével közös adatbázisokhoz.

| Adatbázis   | Illesztőprogram-osztály neve                             | JDBC-illesztőprogram                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Letöltés](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Letöltés](https://dev.mysql.com/downloads/connector/j/) (válassza a "platform független" lehetőséget) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Letöltés](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Ha a Tomcat-t a Java Database Connectivity (JDBC) vagy a Java perzisztencia API (közös parlamenti) használatára szeretné konfigurálni, először testre kell szabnia a `CATALINA_OPTS` tomcat által az indításkor beolvasott környezeti változót. Adja meg ezeket az értékeket a [app Service Maven beépülő modul](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)alkalmazás-beállításán keresztül:

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Vagy állítsa be a környezeti változókat a Azure Portal **konfigurációs**  >  **alkalmazás beállításai** lapján.

Ezután állapítsa meg, hogy az adatforrásnak elérhetőnek kell lennie egy alkalmazáshoz vagy a Tomcat servletben futó összes alkalmazáshoz.

#### <a name="application-level-data-sources"></a>Alkalmazás szintű adatforrások

1. Hozzon létre egy *context.xml* fájlt a projekt *META-INF-* fájljában/könyvtárában. Ha nem létezik, hozza létre a *META-INF/* könyvtárat.

2. A *context.xmlban* adjon hozzá egy `Context` elemet, amely összekapcsolja az adatforrást egy JNDI-címnek. Cserélje le a `driverClassName` helyőrzőt az illesztőprogram osztályának nevére a fenti táblázatból.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Frissítse alkalmazása *web.xml* az alkalmazás adatforrásának használatára.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Konfiguráció véglegesítése

Végül elhelyezjük az illesztőprogram-tégelyeket a Tomcat osztályútvonal, és újra kell indítani a App Service. Győződjön meg arról, hogy a */Home/tomcat/lib* könyvtárba HELYEZVE a JDBC-illesztőprogram fájljai elérhetők a Tomcat ClassLoader. (Ha még nem létezik, hozza létre ezt a könyvtárat.) Ha ezeket a fájlokat fel szeretné tölteni a App Service-példányba, hajtsa végre a következő lépéseket:

1. A [Cloud Shell](https://shell.azure.com)telepítse a WebApp bővítményt:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. A következő CLI-parancs futtatásával hozzon létre egy SSH-alagutat a helyi rendszerből a App Serviceához:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Kapcsolódjon a helyi bújtatási porthoz az SFTP-ügyféllel, és töltse fel a fájlokat a */Home/tomcat/lib* mappába.

Azt is megteheti, hogy FTP-ügyfél használatával tölti fel a JDBC-illesztőt. Az [FTP-hitelesítő adatok beszerzéséhez kövesse az alábbi utasításokat](deploy-configure-credentials.md).

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

Ezek az utasítások az összes adatbázis-kapcsolatra érvényesek. A helyőrzőket ki kell töltenie a kiválasztott adatbázis illesztőprogram-osztályának nevével és JAR-fájljával. A megadott tábla az osztályok neveivel és az illesztőprogramok letöltésével közös adatbázisokhoz.

| Adatbázis   | Illesztőprogram-osztály neve                             | JDBC-illesztőprogram                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Letöltés](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Letöltés](https://dev.mysql.com/downloads/connector/j/) (válassza a "platform független" lehetőséget) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Letöltés](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Ha a Tomcat-t a Java Database Connectivity (JDBC) vagy a Java perzisztencia API (közös parlamenti) használatára szeretné konfigurálni, először testre kell szabnia a `CATALINA_OPTS` tomcat által az indításkor beolvasott környezeti változót. Adja meg ezeket az értékeket a [app Service Maven beépülő modul](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)alkalmazás-beállításán keresztül:

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Vagy állítsa be a környezeti változókat a Azure Portal **konfigurációs**  >  **alkalmazás beállításai** lapján.

Ezután állapítsa meg, hogy az adatforrásnak elérhetőnek kell lennie egy alkalmazáshoz vagy a Tomcat servletben futó összes alkalmazáshoz.

#### <a name="application-level-data-sources"></a>Alkalmazás szintű adatforrások

1. Hozzon létre egy *context.xml* fájlt a projekt *META-INF-* fájljában/könyvtárában. Ha nem létezik, hozza létre a *META-INF/* könyvtárat.

2. A *context.xmlban* adjon hozzá egy `Context` elemet, amely összekapcsolja az adatforrást egy JNDI-címnek. Cserélje le a `driverClassName` helyőrzőt az illesztőprogram osztályának nevére a fenti táblázatból.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Frissítse alkalmazása *web.xml* az alkalmazás adatforrásának használatára.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Megosztott kiszolgálói szintű erőforrások

Megosztott, kiszolgálóoldali adatforrások hozzáadásához a Tomcat server.xml szerkesztésére lesz szükség. Először töltse fel az [indítási parancsfájlt](faq-app-service-linux.md#built-in-images) , és állítsa be a parancsfájl elérési útját a **konfigurációs**  >  **indítási parancsban**. Az indítási parancsfájlt az [FTP](deploy-ftp.md)használatával töltheti fel.

Az indítási parancsfájl [XSL-átalakítót](https://www.w3schools.com/xml/xsl_intro.asp) készít a server.xml fájlra, majd az eredményül kapott XML-fájlt kiírja a következőre: `/usr/local/tomcat/conf/server.xml` . Az indítási parancsfájlnak az apk használatával kell telepítenie a libxslt-t. Az XSL-fájl és az indítási parancsfájl FTP-n keresztül tölthető fel. Az alábbi példa egy indítási parancsfájlt mutat be.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Alább található egy példa XSL-fájl. A példában szereplő XSL-fájl új összekötő csomópontot hoz létre a Tomcat server.xmlhoz.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Konfiguráció véglegesítése

Végül helyezze el az illesztőprogram-tégelyeket a Tomcat osztályútvonal, és indítsa újra a App Service.

1. Győződjön meg arról, hogy a */Home/tomcat/lib* könyvtárba HELYEZVE a JDBC-illesztőprogram fájljai elérhetők a Tomcat ClassLoader. (Ha még nem létezik, hozza létre ezt a könyvtárat.) Ha ezeket a fájlokat fel szeretné tölteni a App Service-példányba, hajtsa végre a következő lépéseket:

    1. A [Cloud Shell](https://shell.azure.com)telepítse a WebApp bővítményt:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. A következő CLI-parancs futtatásával hozzon létre egy SSH-alagutat a helyi rendszerből a App Serviceához:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Kapcsolódjon a helyi bújtatási porthoz az SFTP-ügyféllel, és töltse fel a fájlokat a */Home/tomcat/lib* mappába.

    Azt is megteheti, hogy FTP-ügyfél használatával tölti fel a JDBC-illesztőt. Az [FTP-hitelesítő adatok beszerzéséhez kövesse az alábbi utasításokat](deploy-configure-credentials.md).

2. Ha létrehozta a kiszolgálói szintű adatforrást, indítsa újra a App Service Linux alkalmazást. A Tomcat alaphelyzetbe `CATALINA_BASE` áll, `/home/tomcat` és a frissített konfigurációt fogja használni.

### <a name="jboss-eap"></a>JBoss EAP

Az [adatforrások JBoss EAP-vel való regisztrálása](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management)három fő lépésből áll: a JDBC-illesztőprogram feltöltése, a JDBC-illesztőprogram hozzáadása modulként, valamint a modul regisztrálása. App Service egy állapot-nyilvántartó szolgáltatás, ezért az adatforrás modul hozzáadására és regisztrálására szolgáló konfigurációs parancsokat parancsfájlokkal kell elindulni, és a tároló indításakor kell alkalmazni.

1. Szerezze be az adatbázis JDBC-illesztőprogramját. 
2. Hozzon létre egy XML-modul definíciós fájlját a JDBC illesztőprogramhoz. Az alább látható példa a PostgreSQL modul-definíciója.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.postgres">
        <resources>
        <!-- ***** IMPORTANT : REPLACE THIS PLACEHOLDER *******-->
        <resource-root path="/home/site/deployments/tools/postgresql-42.2.12.jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. Helyezze a JBoss CLI-parancsokat egy nevű fájlba `jboss-cli-commands.cli` . A JBoss-parancsoknak hozzá kell adnia a modult, és regisztrálniuk kell adatforrásként. Az alábbi példában látható a PostgreSQL-hez készült JBoss CLI-parancsok.

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. Hozzon létre egy indítási parancsfájlt, `startup_script.sh` amely meghívja a JBoss CLI-parancsokat. Az alábbi példa bemutatja, hogyan hívhatja meg `jboss-cli-commands.cli` . Később a configre App Service fogja futtatni ezt a parancsfájlt a tároló indításakor. 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. Egy tetszőleges FTP-ügyfelet használva feltöltheti a JDBC-illesztőprogramot, a- `jboss-cli-commands.cli` `startup_script.sh` t és a modul definícióját `/site/deployments/tools/` .
2. Konfigurálja úgy a helyet, hogy `startup_script.sh` a tároló indításakor fusson. Az Azure Portalon navigáljon a **konfiguráció**  >  **általános beállítások**  >  **indítási parancshoz**. Az indítási parancs mező értékét állítsa a következőre: `/home/site/deployments/tools/startup_script.sh` . **Mentse** a módosításokat.

Annak ellenőrzéséhez, hogy az adatforrást hozzáadta-e a JBoss-kiszolgálóhoz, SSH-t a webappba, és futtassa `$JBOSS_HOME/bin/jboss-cli.sh --connect` . Miután csatlakozott a JBoss-hoz, futtassa a `/subsystem=datasources:read-resource` parancsot az adatforrások listájának kinyomtatásához.

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Java-futtatókörnyezet verziójának kiválasztása

App Service lehetővé teszi a felhasználók számára a JVM főverziójának (például a Java 8 vagy a Java 11), valamint az alverzió (például 1.8.0 _232 vagy 11.0.5) kiválasztását. Azt is megteheti, hogy az alverzió frissítése automatikusan megtörténik, mivel az új alverziók elérhetővé válnak. A legtöbb esetben az üzemi helyeknek rögzített másodlagos JVM-verziókat kell használniuk. Ez megakadályozza a unnanticipated leállását egy alverzió automatikus frissítése során.

Ha úgy dönt, hogy kijelöli a másodlagos verziót, időnként frissítenie kell a JVM alverzióját a helyen. Annak érdekében, hogy az alkalmazás az újabb alverzión fusson, hozzon létre egy átmeneti tárolóhelyet, és növelje az alverziót az átmeneti helyen. Miután meggyőződött róla, hogy az alkalmazás megfelelően fut az új alverzión, megváltoztathatja az átmeneti és az üzemi tárolóhelyeket.

## <a name="jboss-eap-hardware-options"></a>JBoss EAP-hardver beállításai

A JBoss EAP csak a prémium és az elkülönített hardveres beállításokban érhető el. Azok az ügyfelek, akik egy JBoss EAP-helyet hoztak létre ingyenes, megosztott, alapszintű vagy standard szinten a nyilvános előzetes verzióban, a nem várt viselkedés elkerülése érdekében fel kell skálázást a prémium vagy elkülönített hardveres szintre.

## <a name="java-runtime-statement-of-support"></a>A Java futtatókörnyezet támogatási nyilatkozata

### <a name="jdk-versions-and-maintenance"></a>JDK-verziók és-karbantartás

Az Azure által támogatott Java Development Kit (JDK) a [Azul Systems](https://www.azul.com/)által biztosított [Zulu](https://www.azul.com/downloads/azure-only/zulu/) . A Azul Zulu Enterprise-OpenJDK az Azure-hoz készült OpenJDK, valamint a Microsoft és a Azul rendszerek által támogatott, az Azure-ra vonatkozó, és a Azure Stack. A Java SE-alkalmazások létrehozásához és futtatásához szükséges összes összetevőt tartalmazzák. A JDK-t a [Java jdk-telepítésből](/azure/developer/java/fundamentals/java-jdk-long-term-support)telepítheti.

A főverzió frissítései a Azure App Service új futtatókörnyezeti beállításain keresztül lesznek elérhetők. Az ügyfelek a Java újabb verzióit frissítik a App Service üzembe helyezésének konfigurálásával, és felelősek a fő frissítés megfelelőségének teszteléséhez és biztosításához.

A támogatott JDK minden év januárjában, áprilisban, júliusban és októberben automatikusan megtörténik. Az Azure-beli Java-ról további információt [ebben a támogatási dokumentumban](/azure/developer/java/fundamentals/java-jdk-long-term-support)talál.

### <a name="security-updates"></a>Biztonsági frissítések

A főbb biztonsági rések javításait és javításait a rendszer azonnal felszabadítja, amint azok elérhetők lesznek a Azul Systems-től. A "fő" biztonsági rést az 9,0-es vagy újabb alappontszám határozza meg a [NIST Common sebezhetőségi pontozási rendszer 2. verziójában](https://nvd.nist.gov/vuln-metrics/cvss).

A Tomcat 8,0 [2018. szeptember 30-ig elérte az élettartamot (EOL)](https://tomcat.apache.org/tomcat-80-eol.html). Habár a futtatókörnyezet továbbra is elérhető a Azure App Serviceon, az Azure nem alkalmazza a biztonsági frissítéseket a Tomcat 8,0-es verzióra. Ha lehetséges, telepítse át az alkalmazásokat a Tomcat 8,5 vagy a 9,0-es verzióra. A Tomcat 8,5 és 9,0 egyaránt elérhető Azure App Serviceon. További információért tekintse meg a [hivatalos tomcat-webhelyet](https://tomcat.apache.org/whichversion.html) . 

### <a name="deprecation-and-retirement"></a>Elavulás és nyugdíjazás

Ha egy támogatott Java-futtatókörnyezet megszűnik, az érintett futtatókörnyezetet használó Azure-fejlesztők elavult értesítést kapnak a futtatókörnyezet kivonása előtt legalább hat hónappal.


### <a name="local-development"></a>Helyi fejlesztés

A fejlesztők letölthetik az Azul Zulu Enterprise JDK éles kiadását helyi fejlesztésre az [Azul letöltési webhelyéről](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Fejlesztési támogatás

Az Azure [által támogatott Azul ZULU JDK-](https://www.azul.com/downloads/azure-only/zulu/) vel kapcsolatos terméktámogatás a Microsofton keresztül érhető el az Azure-hoz való fejlesztéshez, illetve a [Azure stack](https://azure.microsoft.com/overview/azure-stack/) egy [minősített Azure-támogatási csomaggal](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Következő lépések

Látogasson el az Azure [for Java Developers](/java/azure/) Center webhelyre, ahol megtalálhatja az Azure rövid útmutatók, oktatóanyagok és a Java-dokumentációt.

A Linux rendszerhez készült App Service használatának általános kérdései a Java [app Service](faq-app-service-linux.md)-fejlesztésre nem vonatkoznak.
