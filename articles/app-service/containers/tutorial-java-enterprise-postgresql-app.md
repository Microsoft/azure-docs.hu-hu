---
title: Nagyvállalati Java-webalkalmazás létrehozása linuxon – az Azure App Service |} A Microsoft Docs
description: Ismerje meg, hogyan üzembe a Linuxon futó Azure App Service Wildfly a Java Enterprise-alkalmazást.
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: 6b9c9500423392ec07482f049697d9b49dc060bf
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603191"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Oktatóanyag: A Java EE-alapú és a Postgres-webalkalmazás létrehozása az Azure-ban

Az oktatóanyag bemutatja, hogyan lehet a Java Enterprise Edition (EE) webalkalmazás létrehozása az Azure App Service-ben, és csatlakoztassa a Postgres-adatbázis. Amikor kész, hogy egy [WildFly](https://www.wildfly.org/about/) adattárolásra alkalmazás [, Azure Database for Postgres](https://azure.microsoft.com/services/postgresql/) Azure-on futó [Linuxon futó App Service](app-service-linux-intro.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Java EE-alapú alkalmazás üzembe helyezése a Maven használatával Azure-bA
> * Postgres-adatbázis létrehozása az Azure-ban
> * A Postgres használandó WildFly kiszolgáló konfigurálása
> * Az alkalmazás frissítése és ismételt üzembe helyezése
> * A WildFly futtatni az egységteszteket

## <a name="prerequisites"></a>Előfeltételek

1. [A git letöltése és telepítése](https://git-scm.com/)
2. [Töltse le és telepítse a Maven 3](https://maven.apache.org/install.html)
3. [Töltse le és telepítse az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Klónozza, és szerkesztheti a mintaalkalmazás

Ebben a lépésben klónozza a mintaalkalmazást, és a Maven Project Hálózatiobjektum-modellt (POM vagy pom.xml) konfigurálása a központi telepítés.

### <a name="clone-the-sample"></a>A minta klónozása

A terminálablakban lépjen egy munkakönyvtárra, és a Klónozás [a minta tárház](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>A Maven POM frissítése

A kívánt nevére és erőforráscsoportjára csoportot az App Service frissítése a Maven Azure beépülő modult. Nem kell előzetesen létre az App Service-csomag vagy a példány. A Maven bővítménnyel hoz létre az erőforráscsoportot és az App Service-ben, ha azt nem létezik. 

Akkor is görgessen le a `<plugins>` szakaszában _pom.xml_, 200, a módosításokat. sor. 

```xml
<!-- Azure App Service Maven plugin for deployment -->
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>${version.maven.azure.plugin}</version>
  <configuration>
    <appName>YOUR_APP_NAME</appName>
    <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup>
    <linuxRuntime>wildfly 14-jre8</linuxRuntime>
  ...
</plugin>  
```
Cserélje le `YOUR_APP_NAME` és `YOUR_RESOURCE_GROUP` az App Service- és erőforrás-csoport nevére.

## <a name="build-and-deploy-the-application"></a>Az alkalmazás létrehozása és üzembe helyezése

Most használjuk Maven az alkalmazás létrehozása és üzembe helyezése az App Service-ben.

### <a name="build-the-war-file"></a>Hozhat létre a .war-fájlt

A ki a projekt POM csomagolhatja be az alkalmazást egy Web Archive-(WAR-) fájlba van konfigurálva. A Maven használatával szeretné alkalmazás létrehozása:

```bash
mvn clean install -DskipTests
```

A vizsgálati eset az alkalmazás futtatható, ha az alkalmazás központi telepítése az alakzatot WildFly lettek kialakítva. Hogy kihagyja a tesztek helyileg hozhat létre, és az alkalmazás App Service-ben való üzembe helyezését követően a tesztek futtatásához.

### <a name="deploy-to-app-service"></a>Üzembe helyezés az App Service-ben

Most, hogy a WAR készen áll, az App Service üzembe helyezése az Azure beépülő modul használatával:

```bash
mvn azure-webapp:deploy
```

Az üzembe helyezést követően folytassa a következő lépéssel.

### <a name="create-a-record"></a>Rekord létrehozása

Nyisson meg egy böngészőt, majd lépjen a következő helyre: `https://<your_app_name>.azurewebsites.net/`. Gratulálunk, központilag telepített a Java EE-alapú alkalmazás az Azure App Service-ben!

Ezen a ponton az alkalmazás egy memórián belüli H2-adatbázist használja. Kattintson a navigációs sávban az "admin", és hozzon létre egy új kategóriát. A rekord a memóriában lévő adatbázishoz a elvesznek, ha újraindítja az App Service-példányhoz. Az alábbi lépéseket fogja úgy, hogy az Azure-ban egy Postgres-adatbázis kiépítése és WildFly a használatára konfigurálja.

![Rendszergazdai gomb helye](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>A Postgres-adatbázis kiépítése

Üzembe helyez egy Postgres-kiszolgálót, nyisson meg egy terminált, és futtassa a következő parancsot a kiszolgáló nevét, felhasználónév, jelszó és helyen a kívánt értékekkel. Használja ugyanazt az erőforráscsoportot, amely az App Service. Jegyezze fel a jelszó megtartása későbbi használatra!

```bash
az postgres server create -n <desired-name> -g <same-resource-group> --sku-name GP_Gen4_2 -u <desired-username> -p <desired-password> -l <location>
```

Keresse meg a portálon, és keressen rá a Postgres-adatbázis. Ha működik a panel, másolja a "Kiszolgálónév" és "Kiszolgálói rendszergazda bejelentkezési neve" értékeket, később szüksége lesz rájuk.

### <a name="allow-access-to-azure-services"></a>Az Azure-szolgáltatásokhoz való hozzáférés engedélyezése

Az a **kapcsolatbiztonság** panel az Azure-adatbázis panel az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" gombra kattintva válthat a **ON** pozíciója.

![Az Azure-szolgáltatásokhoz való hozzáférés engedélyezése](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>A Java-alkalmazás frissítéséhez a Postgres

Most már használunk néhány módosítást, a Java-alkalmazás lehetővé teszi, hogy a Postgres-adatbázis használata.

### <a name="add-postgres-credentials-to-the-pom"></a>A POM Postgres hitelesítő adatok hozzáadása

A _pom.xml_, a nagybetűs helyőrző értékeket cserélje le az Postgres server name, rendszergazdai bejelentkezési nevet és jelszót. Ezek a mezők az Azure-Maven bővítménnyel belül vannak. (Ne felejtse el `YOUR_SERVER_NAME`, `YOUR_PG_USERNAME`, és `YOUR_PG_PASSWORD` a a `<value>` ... címkék nem található a `<name>` címkék!)

```xml
<plugin>
      ...
      <appSettings>
      <property>
        <name>POSTGRES_CONNECTIONURL</name>
        <value>jdbc:postgresql://YOUR_SERVER_NAME:5432/postgres?ssl=true</value>
      </property>
      <property>
        <name>POSTGRES_USERNAME</name>
        <value>YOUR_PG_USERNAME</value>
      </property>
      <property>
        <name>POSTGRES_PASSWORD</name>
        <value>YOUR_PG_PASSWORD</value>
      </property>
    </appSettings>
  </configuration>
</plugin>
```

### <a name="update-the-java-transaction-api"></a>A Java-tranzakció API frissítése

Következő lépésként a Java tranzakció API (JPA) konfiguráció szerkesztése, hogy a Java-alkalmazás is kommunikálni fognak Postgres helyett a memórián belüli H2 adatbázis korábban használja. Nyissa meg a szerkesztő _src/main/resources/META-INF/persistence.xml_. Cserélje le az `<jta-data-source>` értéket a `java:jboss/datasources/postgresDS` értékre. A JTA XML ezzel a beállítással rendelkezik:

```xml
...
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
...
```

## <a name="configure-the-wildfly-application-server"></a>A WildFly-kiszolgáló konfigurálása

Biztosíthatók az újrakonfigurált alkalmazás üzembe helyezése előtt a WildFly alkalmazáskiszolgáló kell frissítjük a Postgres-modult és annak függőségeit. További konfigurációs információkat tekinthet meg [WildFly konfigurálása server](configure-language-java.md#configure-java-ee-wildfly).

Konfigurálja a kiszolgálót, szükségünk lesz a négy fájlt a `wildfly_config/` könyvtár:

- **postgresql-42.2.5.jar**: A JAR-fájlt a Postgres készült JDBC-illesztőprogram. További információkért lásd: a [hivatalos webhely](https://jdbc.postgresql.org/index.html).
- **postgres-module.xml**: Az XML-fájl deklarálja a Postgres-modul (org.postgres) nevét. Az erőforrások és a használt modul szükséges függőségeket is meghatározza.
- **jboss_cli_commands.cl**: Ez a fájl tartalmazza a konfigurációs parancsok, amelyek a JBoss CLI való fogja végrehajtani. A parancsok a Postgres-modul hozzáadása a WildFly application server, adja meg a hitelesítő adatokat, deklarálja JNDI nevét, állítsa be az időkorlát küszöbértéke stb. Ha ismeri a JBoss CLI-vel, tekintse meg a [dokumentációs](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.sh**: Végül Ez a héjparancsfájl fog hajtható végre, amikor elindul az App Service-példányhoz. A parancsfájl csak egy függvényt hajtja végre: szereplő parancsok átirányításával `jboss_cli_commands.cli` JBoss CLI.

Erősen javasoljuk, hogy a fájlok tartalmának olvasása különösen _jboss_cli_commands.cli_.

### <a name="ftp-the-configuration-files"></a>FTP-konfigurációs fájl

FTP-tartalmát kell `wildfly_config/` az App Service-példányhoz. Az FTP-hitelesítő adatok lekéréséhez kattintson a **közzétételi profil lekérése** gomb az App Service panel az Azure Portalon. Az FTP-felhasználónév és jelszó lesz a letöltött XML-dokumentumban. A közzétételi profil további információkért lásd: [ebben a dokumentumban](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

A négy fájlokat átvinni egy FTP-eszközzel a választott `wildfly_config/` való `/home/site/deployments/tools/`. (Vegye figyelembe, hogy nem kell átvinni a címtárban, csak maguknak a fájlokat.)

### <a name="finalize-app-service"></a>App Service-ben véglegesítése

Az App Service-ben panelen keresse meg az "Alkalmazás beállítások" panelen. A "Runtime", "Indítási fájl" mező beállítása `/home/site/deployments/tools/startup_script.sh`. Ez biztosítja, hogy a PowerShell-parancsfájlt az App Service-példány jön létre, de a kiszolgáló újraindítása előtt a WildFly azután fut-e.

Végezetül indítsa újra az App Service. A gomb akkor az "Áttekintés" panelen.

## <a name="redeploy-the-application"></a>Az alkalmazás újbóli üzembe helyezése

Egy terminálablakban építse újra, és újból üzembe helyeznie az alkalmazást.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Gratulálunk! Az alkalmazás már használja egy Postgres-adatbázis, és azokat a rekordokat az alkalmazásban létrehozott lesz tárolva Postgres ahelyett, hogy az előző H3 memóriában lévő adatbázishoz. Ennek ellenőrzéséhez egy rekordot, és indítsa újra az App Service. A rekordok továbbra is megtalálhatók az alkalmazás újraindításakor.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha ezek az erőforrásokra már nincs szüksége más oktatóanyagokhoz (lásd a következő lépésekkel), a következő parancs futtatásával törölheti azokat:

```bash
az group delete --name <your-resource-group>
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Java EE-alapú alkalmazás üzembe helyezése a Maven használatával Azure-bA
> * Postgres-adatbázis létrehozása az Azure-ban
> * A Postgres használandó WildFly kiszolgáló konfigurálása
> * Az alkalmazás frissítése és ismételt üzembe helyezése
> * A WildFly futtatni az egységteszteket

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan képezhet le egyedi DNS-nevet az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni DNS-név leképezése az alkalmazás](../app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg az egyéb erőforrások:

> [!div class="nextstepaction"]
> [Java-alkalmazás konfigurálása](configure-language-java.md)
