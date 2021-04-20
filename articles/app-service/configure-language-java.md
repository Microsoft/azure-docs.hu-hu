---
title: Java-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhat Java-alkalmazásokat a Azure App Service. A cikk a leggyakoribb konfigurációs feladatokat ismerteti.
keywords: azure app service, webalkalmazás, windows, oss, java, tomcat, jboss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: 1caa8b680b6bc3df20ad31e581d336195308a2dc
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726321"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Java-alkalmazás konfigurálása Azure App Service

Azure App Service lehetővé teszi a Java-fejlesztők számára, hogy java SE-, Tomcat- és JBoss EAP-webalkalmazásokat építsenek ki, helyeznek üzembe és skáláznak egy teljes mértékben felügyelt szolgáltatáson. Alkalmazások üzembe helyezése Maven beépülő modulokkal, a parancssorból, vagy olyan szerkesztőkben, mint az IntelliJ, az Eclipse vagy a Visual Studio Code.

Ez az útmutató alapvető fogalmakat és utasításokat tartalmaz a java-fejlesztők számára a App Service. Ha még soha nem használta Azure App Service, először olvassa át a Java rövid [útmutatóját.](quickstart-java.md) A Java-App Service nem specifikus alkalmazások használatával kapcsolatos általános kérdésekre a gyakori [kérdések App Service választ.](faq-configuration-and-management.md)

## <a name="deploying-your-app"></a>Az alkalmazás üzembe helyezése

Az [Azure Web App Plugin for Maven használatával](https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) üzembe helyezheti a .war- vagy .jar-fájlokat. A népszerű I/S-környezetekkel való üzembe helyezést az Azure Toolkit for IntelliJ vagy [a](/azure/developer/java/toolkit-for-intellij/) Azure Toolkit for Eclipse is [támogatja.](/azure/developer/java/toolkit-for-eclipse)

Ellenkező esetben az üzembe helyezési módszer az archívum típusától függ:

### <a name="java-se"></a>Java SE

.jar-fájlok Java SE környezetben való üzembe helyezéséhez használja `/api/zipdeploy/` a Kudu-webhely végpontját. Az API-val kapcsolatos további információkért tekintse meg ezt [a dokumentációt.](./deploy-zip.md#rest) 

> [!NOTE]
>  Az alkalmazás azonosításához és futtatásához a .jar-App Service nevet kell `app.jar` adnunk. A fent említett Maven beépülő modul automatikusan átnevezi az alkalmazást az üzembe helyezés során. Ha nem szeretné átnevezni a JAR-t *app.jar* névre, feltölthet egy héjparancsprogramot az paranccsal a .jar-alkalmazás futtatásához. Illessze be a szkript [](faq-app-service-linux.md#built-in-images) abszolút elérési útját a Portál Konfiguráció szakaszában található Indítási fájl szövegmezőbe. Az indítási szkript nem abban a könyvtárban fut, amelyben el van helyezve. Ezért mindig abszolút elérési utakat használjon az indítási szkriptben található fájlokra való hivatkozáskor (például: `java -jar /home/myapp/myapp.jar`).

### <a name="tomcat"></a>Tomcat

A .war-fájlok Tomcatben való üzembe helyezéséhez használja a végpontot az `/api/wardeploy/` archív fájl postálásához. Az API-val kapcsolatos további információkért tekintse meg ezt [a dokumentációt.](./deploy-zip.md#deploy-war-file)

::: zone pivot="platform-linux"

### <a name="jboss-eap"></a>JBoss EAP

A .war-fájlok JBossban való üzembe helyezéséhez használja a végpontot az `/api/wardeploy/` archív fájl postálásához. Az API-val kapcsolatos további információkért tekintse meg ezt [a dokumentációt.](./deploy-zip.md#deploy-war-file)

.fülfájlok üzembe helyezéséhez használja [az FTP-t.](deploy-ftp.md)

::: zone-end

Ne telepítse a .war vagy .jar fájlokat FTP használatával. Az FTP-eszköz indítási szkriptek, függőségek vagy más futásidejű fájlok feltöltésére lett kialakítva. Nem ez az optimális választás webalkalmazások üzembe helyezéséhez.

## <a name="logging-and-debugging-apps"></a>Alkalmazások naplózása és hibakeresése

A teljesítményjelentések, a forgalmi vizualizációk és az állapotellenőrzések minden alkalmazáshoz elérhetők a Azure Portal. További információkért lásd a [Azure App Service áttekintését.](overview-diagnostics.md)

### <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

További információ: [Stream-naplók a Cloud Shell.](troubleshoot-diagnostic-logs.md#in-cloud-shell)

::: zone pivot="platform-linux"

### <a name="ssh-console-access"></a>SSH-konzol elérése

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="troubleshooting-tools"></a>Hibaelhárítási eszközök

A beépített Java-lemezképek az [Alpine Linux operációs](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) rendszeren alapulnak. A `apk` csomagkezelővel telepítheti a hibaelhárítási eszközöket és parancsokat.

::: zone-end

### <a name="flight-recorder"></a>Flight Recorder

A Zulu Flight Recorder App Service minden Java-App Service az Azul JVM-ekkel. Ezzel JVM-, rendszer- és alkalmazáseseményeket rögzíthet, és elháríthatja a Java-alkalmazások hibáit.

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>Timed Recording

Az időről időre rögzített felvételhez szüksége lesz a Java-alkalmazás PID azonosítójára (Folyamatazonosító). A PIN-fájl megkereséhez nyisson meg egy böngészőt a webalkalmazás SCM-webhelyén a https://<-site-name>.scm.azurewebsites.net/ProcessExplorer/. Ezen az oldalon a webalkalmazásban futó folyamatok megjelenik. Keresse meg a táblában a "java" nevű folyamatot, és másolja ki a megfelelő PID-t (folyamatazonosítót).

Ezután nyissa meg a **Hibakeresési konzolt** az SCM-webhely felső eszköztárán, és futtassa a következő parancsot. Cserélje `<pid>` le a helyére a korábban kimáselt folyamatazonosítót. Ez a parancs elindítja a Java-alkalmazás 30 másodperces Profiler-felvételét, és létrehoz egy nevű fájlt a `timed_recording_example.jfr` `D:\home` könyvtárban.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

SSH-App Service és futtassa a parancsot az összes `jcmd` futó Java-folyamat listájának megjelenik. A jcmd mellett a Java-alkalmazásnak is futnia kell egy folyamatazonosító számmal (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Az alábbi parancs végrehajtásával indítsa el a JVM 30 másodperces felvételét. Ez profilt hoz létre a JVM-ről, és létrehoz egy *jfr_example.jfr* nevű JFR-fájlt a kezdőkönyvtárban. (Cserélje le a 116-ot a Java-alkalmazás kódjára.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

A 30 másodperces időtartam alatt a futtatásával ellenőrizheti, hogy a felvétel `jcmd 116 JFR.check` zajlik-e. Ez az adott Java-folyamat összes felvételét megmutatja.

#### <a name="continuous-recording"></a>Folyamatos rögzítés

A Zulu Flight Recorder használatával folyamatosan profilt futtathat a Java-alkalmazásról úgy, hogy az minimális hatással legyen a futásidő[teljesítményére (forrás).](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf) A következő Azure CLI-parancs futtatásával hozzon létre egy JAVA_OPTS nevű alkalmazásbeállítást a szükséges konfigurációval. Az alkalmazás JAVA_OPTS alkalmazásbeállítás tartalmát a rendszer az `java` parancsnak továbbadja.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

A felvétel elindulása után az paranccsal bármikor kiírást készíthet az aktuális `JFR.dump` rögzítési adatokról.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

::: zone-end

#### <a name="analyze-jfr-files"></a>Fájlok `.jfr` elemzése

Az [FTPS használatával](deploy-ftp.md) töltse le a JFR-fájlt a helyi gépre. A JFR-fájl elemzéséhez töltse le és telepítse a [Zulu Mission Controlt.](https://www.azul.com/products/zulu-mission-control/) A Zulu Mission Controlre vonatkozó utasításokért tekintse meg az [Azul dokumentációját](https://docs.azul.com/zmc/) és [a telepítési utasításokat.](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

### <a name="app-logging"></a>Alkalmazásnaplózás

::: zone pivot="platform-windows"

Engedélyezze [az alkalmazásnaplózást](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) a Azure Portal vagy az Azure [CLI](/cli/azure/webapp/log#az-webapp-log-config) használatával, hogy a App Service-t úgy konfigurálja, hogy az alkalmazás szabványos konzolkimenetét és a szokásos konzolhiba-streameket a helyi fájlrendszerbe vagy virtuális Azure Blob Storage. A helyi fájlrendszer App Service példányra való naplózás a konfigurálás után 12 órával le van tiltva. Ha hosszabb megőrzésre van szüksége, konfigurálja az alkalmazást úgy, hogy kimenetet írjon egy Blob Storage-tárolóba. A Java- és Tomcat-alkalmazásnaplók a */home/LogFiles/Application/ könyvtárban találhatók.*

::: zone-end
::: zone pivot="platform-linux"

Engedélyezze [az alkalmazásnaplózást](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) a Azure Portal vagy az Azure [CLI](/cli/azure/webapp/log#az-webapp-log-config) használatával, hogy konfigurálja a App Service-t, hogy az alkalmazás szabványos konzolkimenetét és a szokásos konzolhiba-streameket írja a helyi fájlrendszerbe vagy Azure Blob Storage. Ha hosszabb megőrzésre van szüksége, konfigurálja az alkalmazást úgy, hogy kimenetet írjon egy Blob Storage-tárolóba. A Java- és Tomcat-alkalmazásnaplók a */home/LogFiles/Application/ könyvtárban találhatók.*

Azure Blob Storage linuxos virtuális gépek naplózása App Services konfigurálható a Azure Monitor [(előzetes verzió) használatával](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) 

::: zone-end

Ha az alkalmazás [Logbacket](https://logback.qos.ch/) vagy [Log4j-et](https://logging.apache.org/log4j) használ a nyomkövetéshez, ezeket a nyomkövetéseket továbbíthatja felülvizsgálatra az Azure Application Insights-ba a naplózási keretrendszer konfigurációs utasításait követve, amely a [Java](../azure-monitor/app/java-trace-logs.md)nyomkövetési naplók Application Insights.

## <a name="customization-and-tuning"></a>Testreszabás és finomhangolás

Azure App Service Linuxhoz való alkalmazás támogatja a gyári finomhangolást és testreszabást a Azure Portal cli segítségével. Tekintse át a következő cikkeket a nem Java-specifikus webalkalmazás-konfigurációkról:

- [Alkalmazásbeállítások konfigurálása](configure-common.md#configure-app-settings)
- [Egyéni tartomány beállítása](app-service-web-tutorial-custom-domain.md)
- [SSL-kötések konfigurálása](configure-ssl-bindings.md)
- [CDN hozzáadása](../cdn/cdn-add-to-web-app.md)
- [A Kudu-hely konfigurálása](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


### <a name="set-java-runtime-options"></a>Java-futásidejű beállítások megadása

A lefoglalt memória vagy más JVM-futásidejű beállítások beállításának beállításhoz hozzon létre egy nevű [](configure-common.md#configure-app-settings) `JAVA_OPTS` alkalmazásbeállítást a beállításokkal. App Service ezt a beállítást környezeti változóként továbbítja a Java-futásidőnek az indításakor.

A Azure Portal alatt a  webalkalmazás Alkalmazásbeállítások részen hozzon létre egy új alkalmazásbeállítást néven, amely tartalmazza a további beállításokat, `JAVA_OPTS` `-Xms512m -Xmx1204m` például: .

Ha az alkalmazásbeállítást a Maven beépülő modulból konfigurálja, adjon hozzá beállítás-érték címkéket az Azure beépülő modul szakaszban. Az alábbi példa egy adott minimális és maximális Java-halommemóta-méretet állít be:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Azok a fejlesztők, akik egyetlen alkalmazást futtatnak, és a App Service üzembe helyezési tárolóhelyet tartalmaznak, a következő lehetőségeket használhatja:

- B1 és S1 példányok: `-Xms1024m -Xmx1024m`
- B2 és S2 példányok: `-Xms3072m -Xmx3072m`
- B3- és S3-példányok: `-Xms6144m -Xmx6144m`

Az alkalmazás halommemória-beállításainak finomhangolása során tekintse át a App Service terv részleteit, és vegye figyelembe, hogy több alkalmazásnak és üzembehelyezési tárolóhelynek kell megtalálnia a memória optimális lefoglalását.

### <a name="turn-on-web-sockets"></a>Webes szoftvercsatornák bekapcsolás

Kapcsolja be a webes szoftvercsatornák támogatását a Azure Portal az alkalmazás **alkalmazásbeállításai** között. Ahhoz, hogy a beállítás érvénybe lép, újra kell indítania az alkalmazást.

Kapcsolja be a webes szoftvercsatornák támogatását az Azure CLI használatával a következő paranccsal:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Ezután indítsa újra az alkalmazást:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Alapértelmezett karakterkódolás beállítása

A Azure Portal alatt, a  webalkalmazás alkalmazásbeállítása alatt hozzon létre egy új, nevű `JAVA_OPTS` alkalmazásbeállítást `-Dfile.encoding=UTF-8` értékkel.

Másik lehetőségként konfigurálhatja az alkalmazásbeállítást az App Service Maven beépülő modullal. Adja hozzá a beállítás nevének és értékének címkéit a beépülő modul konfigurációjában:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>JSP-fájlok előzetes fordítása

A Tomcat-alkalmazások teljesítményének javítása érdekében lefordíthatja a JSP-fájlokat, mielőtt üzembe helyezné App Service. Használhatja az Apache Sling által biztosított [Maven beépülő](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) modult, vagy használhatja ezt az [Ant buildfájlt.](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)

## <a name="secure-applications"></a>Alkalmazások biztonságossá tere

A környezetben App Service Java-alkalmazások a [](../security/fundamentals/paas-applications-using-app-services.md) többi alkalmazáshoz hasonló ajánlott biztonsági eljárásokkal.

### <a name="authenticate-users-easy-auth"></a>Felhasználók hitelesítése (Easy Auth)

Állítsa be az alkalmazáshitelesítést a Azure Portal a Hitelesítés **és engedélyezés lehetőséggel.** Innen engedélyezheti a hitelesítést olyan közösségi Azure Active Directory, mint a Facebook, a Google vagy a GitHub. Azure Portal konfiguráció csak egyetlen hitelesítésszolgáltató konfigurálásakor működik. További információ: [Configure your App Service app to use Azure Active Directory login](configure-authentication-provider-aad.md) and the related articles for other identity providers. Ha több bejelentkezési szolgáltatót is engedélyeznie kell, kövesse a hitelesítés testreszabása [App Service cikk utasításait.](app-service-authentication-how-to.md)

#### <a name="java-se"></a>Java SE

Spring Boot a [Azure Active Directory Spring Boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory) használatával biztonságossá teheti az alkalmazásokat az ismerős Spring Security-jegyzetek és API-k használatával. Mindenképpen növelje a fejléc maximális méretét az *application.properties fájlban.* A értéket `16384` javasoljuk.

#### <a name="tomcat"></a>Tomcat

A Tomcat-alkalmazás közvetlenül a servletből férhet hozzá a felhasználó jogcímeit úgy, hogy a Principal objektumot egy Map objektumra önti. A Leképezés objektum leképezi az egyes jogcímtípusokat az adott típusú jogcímek gyűjteményére. Az alábbi kódban `request` a egy `HttpServletRequest` példánya.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Most már megvizsgálhatja az `Map` objektumot, hogy van-e konkrét jogcím. Az alábbi kódrészlet például végigveszi az összes jogcímtípust, és kinyomtatja az egyes gyűjtemények tartalmát.

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

A felhasználók kijelentkeztethez használja az `/.auth/ext/logout` elérési utat. Egyéb műveletek végrehajtásához tekintse meg a hitelesítés és engedélyezés használatának App Service [dokumentációját.](./app-service-authentication-how-to.md) A Tomcat [HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) felületének és metódusának hivatalos dokumentációja is elérhető. A következő servlet metódusok is a konfigurációtól függően App Service vannak:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

A szolgáltatás letiltásához hozzon létre egy nevű alkalmazásbeállítást `WEBSITE_AUTH_SKIP_PRINCIPAL` `1` értékkel. Ha le szeretné tiltani a App Service által hozzáadott összes servletszűrőt, hozzon létre egy nevű beállítást `WEBSITE_SKIP_FILTERS` `1` értékkel.

### <a name="configure-tlsssl"></a>TLS/SSL konfigurálása

Egy meglévő SSL-tanúsítvány feltöltéséhez és az alkalmazás tartománynevéhez kötéséhez kövesse az Egyéni DNS-név biztonságossáése [SSL-kötéssel](configure-ssl-bindings.md) az Azure App Service-ben című dokumentum utasításait. Alapértelmezés szerint az alkalmazás továbbra is engedélyezi a HTTP-kapcsolatokat– kövesse az oktatóanyag lépéseit az SSL és a TLS kényszerítése érdekében.

### <a name="use-keyvault-references"></a>KeyVault-hivatkozások használata

[Az Azure KeyVault](../key-vault/general/overview.md) központi titkos kulcsok kezelését biztosítja hozzáférési szabályzatokkal és naplózási előzmények használatával. A titkos kulcsokat (például jelszavakat vagy kapcsolati sztringeket) a KeyVaultban tárolhatja, és környezeti változók használatával elérheti ezeket a titkos kulcsokat az alkalmazásban.

Először kövesse az alkalmazás hozzáférésének megadására vonatkozó [utasításokat Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) és egy KeyVault-hivatkozást a titkos kulcsra egy [alkalmazásbeállításban.](app-service-key-vault-references.md#reference-syntax) Ellenőrizheti, hogy a hivatkozás feloldódik-e a titkos kódra, ha kinyomtatja a környezeti változót, miközben távolról hozzáfér a App Service terminálhoz.

Ezeknek a titkos kulcsoknak a Spring- vagy Tomcat-konfigurációs fájlba való be injektálásához használja a környezeti változó injektálás szintaxisát ( `${MY_ENV_VAR}` ). Spring-konfigurációs fájlok esetén tekintse meg ezt a dokumentációt a [külső konfigurációkról.](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>A Java Key Store használata

Alapértelmezés szerint a [linuxos](configure-ssl-certificate.md) virtuális gépekre App Service nyilvános vagy magántanúsítványok a tároló indításakor betöltődnek a megfelelő Java-kulcstárolókba. A tanúsítvány feltöltése után újra kell indítania a App Service, hogy betölthető legyen a Java Key Store-ba. A nyilvános tanúsítványokat a rendszer a kulcstárolóba a helyen, a privát tanúsítványokat pedig a helyen `$JAVA_HOME/jre/lib/security/cacerts` `$JAVA_HOME/lib/security/client.jks` tárolja.

A JDBC-kapcsolat a Java Key Store-ban található tanúsítványokkal való titkosításához további konfigurációra lehet szükség. Tekintse meg a kiválasztott JDBC-illesztő dokumentációját.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>A Java Key Store inicializálása

Az objektum `import java.security.KeyStore` inicializálásához töltse be a kulcstárfájlt a jelszóval. Mindkét kulcstartó alapértelmezett jelszava a "changeit".

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

A tanúsítványokat manuálisan is betöltheti a kulcstárolóba. Hozzon létre egy alkalmazásbeállítást () a értékkel, hogy letiltsa App Service a tanúsítványok automatikus betöltését a `SKIP_JAVA_KEYSTORE_LOAD` `1` kulcstárolóba. A rendszer a App Service feltöltött összes nyilvános Azure Portal a következő helyen tárolja: `/var/ssl/certs/` . A magántanúsítványok a alatt vannak `/var/ssl/private/` tárolva.

A Java Key Tool használatával kommunikálhat, illetve hibakeresést is futtathat, ha megnyit egy [SSH-kapcsolatot](configure-linux-open-ssh-session.md) a App Service és futtatja a `keytool` parancsot. A parancsok [listáját a Kulcseszköz](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) dokumentációjában találhatja meg. A KeyStore API-val kapcsolatos további információkért tekintse meg a [hivatalos dokumentációt.](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)

::: zone-end

## <a name="configure-apm-platforms"></a>APM-platformok konfigurálása

Ez a szakasz bemutatja, hogyan csatlakoztathatja a Azure App Service on Linux telepített Java-alkalmazásokat a NewRelic és az AppDynamics alkalmazásteljesítmény-figyelési (APM) platformhoz.

### <a name="configure-new-relic"></a>A New Relic

::: zone pivot="platform-windows"

1. NewRelic-fiók létrehozása a [NewRelic.com](https://newrelic.com/signup)
2. Töltse le a Java-ügynököt a NewRelicről. A fájl neve a következőhöz *newrelic-java-x.x.x.zip.*
3. Másolja ki a licenckulcsot, mert később szüksége lesz rá az ügynök konfiguráláshoz.
4. [SSH-t a App Service,](configure-linux-open-ssh-session.md) és hozzon létre egy új *könyvtárat a /home/site/wwwroot/apm könyvtárban.*
5. Töltse fel a kicsomagolt NewRelic Java-ügynökfájlokat a */home/site/wwwroot/apm könyvtárba.* Az ügynök fájljainak a */home/site/wwwroot/apm/newrelic helyen kell lennie.*
6. Módosítsa a YAML-fájlt a */home/site/wwwroot/apm/newrelic/newrelic.yml* helyen, és cserélje le a helyőrző licenc értékét a saját licenckulcsára.
7. A Azure Portal nyissa meg az alkalmazást a App Service, és hozzon létre egy új alkalmazásbeállítást.

    - **Java SE-alkalmazások** esetén hozzon létre egy nevű környezeti változót `JAVA_OPTS` a `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` értékkel.
    - A **Tomcat esetében** hozzon létre egy nevű környezeti változót `CATALINA_OPTS` a `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` értékkel.

::: zone-end
::: zone pivot="platform-linux"

1. NewRelic-fiók létrehozása a [NewRelic.com](https://newrelic.com/signup)
2. Töltse le a Java-ügynököt a NewRelicről. A fájl neve a következőhöz *newrelic-java-x.x.x.zip.*
3. Másolja ki a licenckulcsot, mert később szüksége lesz rá az ügynök konfiguráláshoz.
4. [SSH-App Service a saját példányába,](configure-linux-open-ssh-session.md) és hozzon létre egy *új könyvtárat a /home/site/wwwroot/apm könyvtárban.*
5. Töltse fel a kicsomagolt NewRelic Java-ügynökfájlokat a */home/site/wwwroot/apm könyvtárba.* Az ügynök fájljainak a */home/site/wwwroot/apm/newrelic helyen kell lennie.*
6. Módosítsa a YAML-fájlt a */home/site/wwwroot/apm/newrelic/newrelic.yml* helyen, és cserélje le a helyőrző licenc értékét a saját licenckulcsára.
7. A Azure Portal nyissa meg az alkalmazást a App Service, és hozzon létre egy új alkalmazásbeállítást.
   
    - **Java SE-alkalmazások** esetén hozzon létre egy nevű környezeti változót `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` értékkel.
    - A **Tomcat esetében** hozzon létre egy nevű környezeti változót `CATALINA_OPTS` a `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` értékkel.

::: zone-end

>  Ha már rendelkezik környezeti változóval a vagy a változóhoz, fűzheti hozzá a beállítást az `JAVA_OPTS` `CATALINA_OPTS` aktuális érték `-javaagent:/...` végéhez.

### <a name="configure-appdynamics"></a>Az AppDynamics konfigurálása

::: zone pivot="platform-windows"

1. AppDynamics-fiók létrehozása a [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Töltse le a Java-ügynököt az AppDynamics webhelyéről. A fájlnév azAppServerAgent-x.x.x.xxxxx.zip
3. A [Kudu-konzollal hozzon](https://github.com/projectkudu/kudu/wiki/Kudu-console) létre egy új *könyvtárat a /home/site/wwwroot/apm könyvtárban.*
4. Töltse fel a Java-ügynök fájljait a */home/site/wwwroot/apm könyvtárba.* Az ügynök fájljainak a */home/site/wwwroot/apm/appdynamics helyen kell lennie.*
5. A Azure Portal nyissa meg az alkalmazást a App Service, és hozzon létre egy új alkalmazásbeállítást.

   - **Java SE-alkalmazások** esetén hozzon létre egy nevű környezeti változót azzal az értékkel, ahol a App Service `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` neve.
   - **Tomcat-alkalmazások** esetén hozzon létre egy nevű környezeti változót azzal az értékkel, ahol a App Service `CATALINA_OPTS` `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` neve.

::: zone-end
::: zone pivot="platform-linux"

1. AppDynamics-fiók létrehozása a [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Töltse le a Java-ügynököt az AppDynamics webhelyéről, és a fájlnév az *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH-t a App Service,](configure-linux-open-ssh-session.md) és hozzon létre egy új *könyvtárat a /home/site/wwwroot/apm könyvtárban.*
4. Töltse fel a Java-ügynök fájljait a */home/site/wwwroot/apm könyvtárba.* Az ügynök fájljainak a */home/site/wwwroot/apm/appdynamics helyen kell lennie.*
5. A Azure Portal nyissa meg az alkalmazást a App Service, és hozzon létre egy új alkalmazásbeállítást.

   - **Java SE-alkalmazások** esetén hozzon létre egy nevű környezeti változót azzal az értékkel, ahol a App Service `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` neve.
   - **Tomcat-alkalmazások** esetén hozzon létre egy nevű környezeti változót azzal az értékkel, ahol a App Service `CATALINA_OPTS` `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` neve.

::: zone-end

> [!NOTE]
>  Ha már rendelkezik környezeti változóval a vagy a változóhoz, fűzheti hozzá a kapcsolót az `JAVA_OPTS` `CATALINA_OPTS` aktuális érték `-javaagent:/...` végéhez.

## <a name="configure-data-sources"></a>Adatforrások konfigurálása

### <a name="java-se"></a>Java SE

A különböző alkalmazásokban található adatforrások Spring Boot javasoljuk, hogy hozzon létre kapcsolati sztringeket, és injektálja őket az *application.properties fájlba.*

1. A App Service oldal "Konfiguráció" szakaszában adja meg a sztring nevét, illessze be a JDBC kapcsolati sztringet az érték mezőbe, és állítsa a típust "Custom" (Egyéni) értékre. Ezt a kapcsolati sztringet igény szerint slot beállításként is beállíthatja.

    Ez a kapcsolati sztring egy nevű környezeti változóként érhető el az alkalmazás `CUSTOMCONNSTR_<your-string-name>` számára. A fent létrehozott kapcsolati sztring neve például `CUSTOMCONNSTR_exampledb` lesz.

2. Az *application.properties fájlban* hivatkozhat erre a kapcsolati sztringre a környezeti változó nevével. A példánkban a következőt használjuk.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Erről a [témakörről Spring Boot az](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) adateléréssel és a külső konfigurációval kapcsolatos részletes dokumentációt. [](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

Ezek az utasítások minden adatbázis-kapcsolatra érvényesek. Helyőrzőket kell kitöltenie a választott adatbázis illesztőprogram-osztályának nevével és JAR-fájllal. A megadott táblázat tartalmazza az általános adatbázisok osztálynevét és illesztőprogram-letöltését.

| Adatbázis   | Illesztőprogram-osztály neve                             | JDBC-illesztő                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Letöltés](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Letöltés](https://dev.mysql.com/downloads/connector/j/) (Válassza a "Platformfüggetlen" lehetőséget) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Letöltés](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Ha a Tomcatet a Java Database Connectivity (JDBC) vagy a Java Persistence API (JPA) használatára konfigurálja, először szabja testre a Tomcat által indításkor beolvasott környezeti `CATALINA_OPTS` változót. Ezeket az értékeket a [Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)beépülő modul alkalmazásbeállításával App Service meg:

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Vagy állítsa be a környezeti változókat a konfigurációs alkalmazás beállításai lapon a  >   Azure Portal.

Ezután állapítsa meg, hogy az adatforrás elérhető legyen-e egy alkalmazás vagy a Tomcat-servleten futó összes alkalmazás számára.

#### <a name="application-level-data-sources"></a>Alkalmazásszintű adatforrások

1. Hozzon *context.xml* fájlt a *projekt META-INF/* könyvtárában. Ha még nem létezik, hozza létre a *META-INF/* könyvtárat.

2. A *context.xml* adjon hozzá egy `Context` elemet, amely egy JNDI-címhez csatolja az adatforrást. Cserélje le a helyőrzőt az illesztő `driverClassName` osztálynevére a fenti táblázatból.

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

3. Frissítse az alkalmazás *web.xml,* hogy az adatforrást használja az alkalmazásban.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Konfiguráció véglegesít

Végül a Tomcat osztályban helyezzük el az illesztőprogram JAR-jét, és újraindítjuk App Service. A */home/tomcat/lib* könyvtárban elhelyezve győződjön meg arról, hogy a JDBC-illesztőprogramfájlok elérhetők a Tomcat osztálybetöltő számára. (Ha még nem létezik, hozza létre ezt a könyvtárat.) Ezeknek a fájloknak a saját App Service való feltöltéséhez hajtsa végre a következő lépéseket:

1. A [Cloud Shell](https://shell.azure.com)telepítse a webalkalmazás-bővítményt:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Futtassa a következő CLI-parancsot egy SSH-alagút létrehozásához a helyi rendszerből a App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Csatlakozzon a helyi alagútporthoz az SFTP-ügyféllel, és töltse fel a fájlokat a */home/tomcat/lib mappába.*

Másik lehetőségként egy FTP-ügyféllel is feltöltheti a JDBC-illesztőt. Kövesse ezeket [az utasításokat az FTP-hitelesítő adatok lekért megadásához.](deploy-configure-credentials.md)

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

Ezek az utasítások minden adatbázis-kapcsolatra érvényesek. Helyőrzőket kell kitöltenie a kiválasztott adatbázis illesztőprogram-osztályának nevével és JAR-fájllal. A megadott táblázat tartalmazza az általános adatbázisok osztálynevét és illesztőprogram-letöltését.

| Adatbázis   | Illesztőprogram-osztály neve                             | JDBC-illesztő                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Letöltés](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Letöltés](https://dev.mysql.com/downloads/connector/j/) (Válassza a "Platformfüggetlen" lehetőséget) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Letöltés](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Ha a Tomcatet a Java Database Connectivity (JDBC) vagy a Java Persistence API (JPA) használatára konfigurálja, először szabja testre a Környezeti változót, amelyet a Tomcat az indításkor `CATALINA_OPTS` beolvas. Ezeket az értékeket a [Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)beépülő modul alkalmazásbeállításával App Service meg:

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Vagy állítsa be a környezeti változókat a Konfigurációs alkalmazás beállításai lapon a  >   Azure Portal.

Ezután állapítsa meg, hogy az adatforrás elérhető legyen-e egy alkalmazás vagy a Tomcat-servleten futó összes alkalmazás számára.

#### <a name="application-level-data-sources"></a>Alkalmazásszintű adatforrások

1. Hozzon *context.xml* fájlt a *projekt META-INF/* könyvtárában. Ha még nem létezik, hozza létre a *META-INF/* könyvtárat.

2. A *context.xml* adjon hozzá egy `Context` elemet, amely egy JNDI-címhez csatolja az adatforrást. Cserélje le a helyőrzőt az illesztő `driverClassName` osztálynevére a fenti táblázatból.

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

3. Frissítse az alkalmazás *web.xml,* hogy az adatforrást használja az alkalmazásban.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Megosztott kiszolgálószintű erőforrások

Megosztott, kiszolgálószintű adatforrás hozzáadásához szerkesztenie kell a Tomcat server.xml. Először töltsön fel egy [indítási szkriptet,](faq-app-service-linux.md#built-in-images) és állítsa be a szkript elérési útját a **Konfigurációs**  >  **indítási parancsban.** Az indítási szkriptet az FTP használatával [töltheti fel.](deploy-ftp.md)

Az indítási szkript egy [xsl-átalakítást](https://www.w3schools.com/xml/xsl_intro.asp) hoz létre a server.xml fájlba, és az eredményül kapott XML-fájlt a következőbe eredményezi: `/usr/local/tomcat/conf/server.xml` . Az indítási szkriptnek az apk használatával kell telepítenie a libxslt et. Az xsl-fájl és az indítási szkript FTP-n keresztül feltölthető. Az alábbi példa egy indítási szkriptet mutat be.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Alább látható egy xsl-példafájl. A példa xsl-fájl egy új összekötőcsomópontot ad hozzá a Tomcat server.xml.

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

#### <a name="finalize-configuration"></a>A konfiguráció véglegesít

Végül helyezze el az illesztőprogram JAR-eket a Tomcat osztályban, és indítsa újra a App Service.

1. A */home/tomcat/lib* könyvtárban helyezze el a JDBC-illesztőprogramfájlokat a Tomcat osztálybetöltő számára. (Hozza létre ezt a könyvtárat, ha még nem létezik.) Ha fel kell töltenie ezeket a fájlokat a App Service példányra, hajtsa végre a következő lépéseket:

    1. A [Cloud Shell](https://shell.azure.com)telepítse a webalkalmazás-bővítményt:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Futtassa a következő CLI-parancsot egy SSH-alagút létrehozásához a helyi rendszerből a App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Csatlakozzon a helyi alagútporthoz az SFTP-ügyféllel, és töltse fel a fájlokat a */home/tomcat/lib mappába.*

    Másik lehetőségként egy FTP-ügyféllel is feltöltheti a JDBC-illesztőt. Kövesse ezeket [az utasításokat az FTP-hitelesítő adatok lekért megadásához.](deploy-configure-credentials.md)

2. Ha kiszolgálószintű adatforrást hozott létre, indítsa újra az App Service Linux-alkalmazást. A Tomcat alaphelyzetbe `CATALINA_BASE` áll, `/home/tomcat` és a frissített konfigurációt használja.

### <a name="jboss-eap"></a>JBoss EAP

Az [adatforrások JBoss EAP-val](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management)való regisztrálásának három alapvető lépése van: a JDBC-illesztő feltöltése, a JDBC-illesztő modulként való hozzáadása és a modul regisztrálása. App Service egy állapot nélküli üzemeltetési szolgáltatás, ezért az adatforrásmodul hozzáadására és regisztrálására szolgáló konfigurációs parancsokat szkriptekkel kell szkriptekkel alkalmazni a tároló indításakor.

1. Szerezze be az adatbázis JDBC-illesztőjét. 
2. Hozzon létre egy XML-moduldefiníciós fájlt a JDBC-illesztő számára. Az alábbi példa a PostgreSQL moduldefiníciója.

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

1. Helyezze a JBoss CLI-parancsokat egy nevű `jboss-cli-commands.cli` fájlba. A JBoss-parancsoknak hozzá kell adniuk a modult, és regisztrálniuk kell adatforrásként. Az alábbi példa a PostgreSQL-hez való JBoss CLI-parancsokat mutatja be.

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. Hozzon létre egy indítási `startup_script.sh` szkriptet, amely a JBoss PARANCSSORi felület parancsait hívja meg. Az alábbi példa bemutatja, hogyan hívhatja meg a következőt: `jboss-cli-commands.cli` . Később úgy fogja App Service, hogy a tároló indításakor futtassa ezt a szkriptet. 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. Egy ön által választott FTP-ügyféllel töltse fel a JDBC-illesztőt `jboss-cli-commands.cli` (, `startup_script.sh` , és a modul definícióját) a következőbe: `/site/deployments/tools/` .
2. Konfigurálja úgy a webhelyet, hogy a `startup_script.sh` tároló indításakor fusson. Az Azure Portalon lépjen a **Konfiguráció általános**  >  **beállítások**  >  **indítási parancsa elemre.** Állítsa az indítási parancs mezőjét a `/home/site/deployments/tools/startup_script.sh` következőre: . **Mentse a** módosításokat.

Annak megerősítéséhez, hogy az adatforrás hozzá lett adva a JBoss-kiszolgálóhoz, SSH-n keresztül a webalkalmazásba, és futtassa a következőt: `$JBOSS_HOME/bin/jboss-cli.sh --connect` . Miután csatlakozott a JBosshoz, futtassa az parancsát az `/subsystem=datasources:read-resource` adatforrások listájának kinyomtatása érdekében.

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Java-alapú futásidejű verzió kiválasztása

App Service lehetővé teszi a felhasználók számára, hogy a JVM főverzióját (például Java 8 vagy Java 11) és az alverziót (például 1.8.0_232 vagy 11.0.5) választják. Dönthet úgy is, hogy automatikusan frissíti az alverziót, amint az új alverziók elérhetővé válnak. A legtöbb esetben az éles helyeknek rögzített JVM-verziókat kell használniuk. Ez megakadályozza a váratlan kimaradásokat egy alverzió automatikus frissítése során.

Ha úgy dönt, hogy kitűzi az alverziót, rendszeresen frissítenie kell a JVM alverzióját a webhelyen. Annak biztosításához, hogy az alkalmazás az újabb alverzión fut, hozzon létre egy előkészítési helyet, és növelje az alverziót az előkészítési helyen. Miután megerősítette, hogy az alkalmazás megfelelően fut az új alverzión, felcserélheti az előkészítési és az éles tárolóhelyeket.

## <a name="jboss-eap-hardware-options"></a>JBoss EAP-hardverbeállítások

A JBoss EAP csak a prémium és izolált hardverek esetén érhető el. A nyilvános előzetes verzióban az Ingyenes, Közös, Alapszintű vagy Standard szinten JBoss EAP-webhelyet létrehozó ügyfeleknek a váratlan viselkedés elkerülése érdekében fel kell skálázva a Prémium vagy az Izolált hardverszintre.

## <a name="java-runtime-statement-of-support"></a>Java-alapú futásidejű támogatási nyilatkozat

### <a name="jdk-versions-and-maintenance"></a>JDK-verziók és karbantartás

Az Azure által támogatott Java fejlesztői készlet (JDK) a [Zulu,](https://www.azul.com/downloads/azure-only/zulu/) amelyet az [Azul Systems biztosít.](https://www.azul.com/) Az OpenJDK Azul Zulu Enterprise-buildek az Azure-hoz készült OpenJDK ingyenes, többplatformos, éles használatra kész disztribúciói, amelyek Azure Stack Microsoft és az Azul Systems által is szolgálnak. A Java SE-alkalmazások létrehozásához és futtatásához szükséges összes összetevőt tartalmazzák. A JDK-t a [Java JDK-telepítésből telepítheti.](/azure/developer/java/fundamentals/java-jdk-long-term-support)

A főverzió-frissítéseket az új futásidejű beállítások biztosítják a Azure App Service. Az ügyfelek a Java ezen újabb verzióira frissítve konfigurálják a App Service üzemelő példányukat, és ők felelnek a tesztelésért és annak biztosításáért, hogy a fő frissítés megfeleljen az igényeiknek.

A támogatott JDK-k minden évben negyedévente automatikusan frissülnek januárban, áprilisban, júliusban és októberben. Az Azure-beli Javával kapcsolatos további információkért tekintse meg ezt [a támogatási dokumentumot.](/azure/developer/java/fundamentals/java-jdk-long-term-support)

### <a name="security-updates"></a>Biztonsági frissítések

A nagyobb biztonsági rések javításokat és javításokat amint elérhetővé válnak az Azul Systemsből. A "nagyobb" biztonsági rést egy 9.0-s vagy újabb alappontszám határozza meg az NIST gyakori sebezhetőségi pontozási rendszer [2-es verziójában.](https://nvd.nist.gov/vuln-metrics/cvss)

A Tomcat 8.0 [2018. szeptember 30-án](https://tomcat.apache.org/tomcat-80-eol.html)elérte az életciklus végét (EOL). Bár a futtatás továbbra is elérhető a Azure App Service, az Azure nem alkalmazza a biztonsági frissítéseket a Tomcat 8.0-ra. Ha lehetséges, az alkalmazásokat a Tomcat 8.5-ös vagy 9.0-s vagy a 9.0-s vagy a 8.0-s vagy a 8.0-s vagy a 8-as vagy a 9.0-s vagy a tomcat- A Tomcat 8.5 és 9.0 egyaránt elérhető a Azure App Service. További [információért tekintse meg](https://tomcat.apache.org/whichversion.html) a hivatalos Tomcat-webhelyet. 

### <a name="deprecation-and-retirement"></a>Elalasztás és kiesés

Ha egy támogatott Java-futásidőt kivezetnek, az érintett futásidőt használó Azure-fejlesztőknek legalább hat hónappal a futásidejű idő elalasztása előtt értesítést kapnak.


### <a name="local-development"></a>Helyi fejlesztés

A fejlesztők letölthetik az Azul Zulu Enterprise JDK Production Edition kiadását helyi fejlesztéshez az [Azul letöltési webhelyén.](https://www.azul.com/downloads/azure-only/zulu/)

### <a name="development-support"></a>Fejlesztési támogatás

Az Azure által támogatott [Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) terméktámogatása a [](https://azure.microsoft.com/overview/azure-stack/) Microsofton keresztül érhető el, ha az Azure-hoz fejleszt, vagy Azure Stack egy minősített Azure-támogatás [csomaggal.](https://azure.microsoft.com/support/plans/)

## <a name="next-steps"></a>Következő lépések

Keresse fel [az Azure for Java fejlesztői központot](/java/azure/) az Azure gyorsútmutatók, oktatóanyagok és a Java-referenciadokumentációk keresésével.

A linuxos App Service kapcsolatos általános kérdésekre, amelyek nem a Java-fejlesztésre vonatkoznak, a [Linuxra vonatkozó gyakori App Service tartalmaznak.](faq-app-service-linux.md)
