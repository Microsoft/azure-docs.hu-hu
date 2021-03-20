---
title: Nyílt forráskódú technológiákkal kapcsolatos gyakori kérdések
description: Választ kaphat a Azure App Service nyílt forráskódú technológiákkal kapcsolatos gyakori kérdésekre.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 36dfbf0fda060a8f273fee64098d6234b575088c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831839"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Nyílt forráskódú technológiákkal kapcsolatos gyakori kérdések az Azure-beli Web Apps

Ez a cikk a [Azure App Service Web Apps funkciójának](https://azure.microsoft.com/services/app-service/web/)nyílt forráskódú technológiákkal kapcsolatos problémákkal kapcsolatos gyakori kérdéseire (GYIK) ad válaszokat.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Hogyan bekapcsolja a PHP-naplózást a PHP-problémák megoldásához?

A PHP-naplózás bekapcsolása:

1. Jelentkezzen be a **kudu webhelyére** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. A felső menüben válassza a **Debug konzol**  >  **cmd** elemet.
3. Válassza ki a **hely** mappáját.
4. Válassza ki a **wwwroot** mappát.
5. Válassza ki az **+** ikont, majd válassza az **új fájl** elemet.
6. Adja meg a fájl nevét **.user.ini**.
7. Válassza a **.user.ini** melletti ceruza ikont.
8. A fájlban adja hozzá a következő kódot: `log_errors=on`
9. Kattintson a **Mentés** gombra.
10. Válassza a **wp-config. php** melletti ceruza ikont.
11. Módosítsa a szöveget a következő kódra:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. A Azure Portal webalkalmazás menüjében indítsa újra a webalkalmazást.

További információ: a [WordPress-hibák naplózásának engedélyezése](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Hogyan a Python-alkalmazások hibáit a App Serviceban üzemeltetett alkalmazásokban?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Hogyan módosítja a App Service-ben üzemeltetett Node.js alkalmazás verzióját?

A Node.js alkalmazás verziójának módosításához a következő lehetőségek egyikét használhatja:

* Az Azure Portalban használja az **Alkalmazásbeállítások alkalmazást**.
  1. A Azure Portal nyissa meg a webalkalmazást.
  2. A **Beállítások** panelen válassza az **Alkalmazásbeállítások** lehetőséget.
  3. Az **Alkalmazásbeállítások** között megadhatja a kulcsként WEBSITE_NODE_DEFAULT_VERSION, valamint az értékként használni kívánt Node.js verzióját.
  4. Nyissa meg a **kudu-konzolt** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
  5. A Node.js verziójának vizsgálatához írja be a következő parancsot:  
     ```
     node -v
     ```
* Módosítsa a iisnode. YML fájlt. A iisnode. YML fájl Node.js verziójának módosítása csak azt a futásidejű környezetet állítja be, amelyet a iisnode használ. A kudu cmd és mások továbbra is a Azure Portal **alkalmazás beállításaiban** beállított Node.js verziót használják.

  A iisnode. YML manuális beállításához hozzon létre egy iisnode. YML fájlt az alkalmazás gyökérkönyvtárában. A fájlban adja meg a következő sort:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Állítsa be a iisnode. YML fájlt a verziókövetés üzembe helyezése során package.jshasználatával.
  Az Azure-verziókövetés telepítési folyamata a következő lépésekkel jár:
  1. Tartalmat helyez át az Azure-webalkalmazásba.
  2. Létrehoz egy alapértelmezett telepítési parancsfájlt, ha nincs egyetlen (Deploy. cmd,. Deployment Files) a webalkalmazás gyökérkönyvtárában.
  3. Futtat egy üzembe helyezési parancsfájlt, amelyben létrehoz egy iisnode. YML fájlt, ha a package.js> Engine-ben megemlíti a Node.js verzióját. `"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. A iisnode. YML fájl a következő kódrészlettel rendelkezik:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>"Hiba történt az adatbázis-kapcsolatok létrehozásakor" üzenet jelenik meg a saját WordPress-alkalmazásban, amely a App Serviceban található. Hogyan a hibával kapcsolatban?

Ha ezt a hibaüzenetet látja az Azure WordPress-alkalmazásban, a php_errors. log és a Debug. log naplófájl engedélyezéséhez végezze el a [WordPress-hibák naplózásának engedélyezése](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2)című témakör lépéseit.

Ha a naplók engedélyezve vannak, reprodukálja a hibát, majd ellenőrizze a naplókat, hogy kifogyott-e a kapcsolatok:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Ha ezt a hibát a Debug. log vagy a php_errors. log fájlban látja, az alkalmazás túllépi a kapcsolatok számát. Ha a ClearDB-on üzemelteti, ellenőrizze a [szolgáltatási](https://www.cleardb.com/pricing.view)csomagban elérhető kapcsolatok számát.

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Hogyan a App Serviceban üzemeltetett Node.js-alkalmazás hibakeresését?

1.  Nyissa meg a **kudu-konzolt** ( `https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole` ).
2.  Nyissa meg az Application logs mappát (D:\home\LogFiles\Application).
3.  A logging_errors.txt fájlban keresse meg a tartalmat.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Hogyan natív Python-modulokat telepíthet egy App Service Web App vagy API-alkalmazásban?

Előfordulhat, hogy egyes csomagok nem települnek a pip használatával az Azure-ban. Előfordulhat, hogy a csomag nem érhető el a Python-csomag indexén, vagy szükség van egy fordítóra (a fordító nem érhető el a webalkalmazást App Service) futtató számítógépen. A natív modulok App Service webalkalmazásokban és API-alkalmazásokban való telepítésével kapcsolatos információkért lásd: [Python-modulok telepítése app Service-ben](/archive/blogs/azureossds/install-native-python-modules-on-azure-web-apps-api-apps).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Hogyan üzembe helyezhet egy Django-alkalmazást a git és a Python új verziójának használatával App Servicehoz?

További információ a Django telepítéséről: [Django-alkalmazás telepítése app Service](/archive/blogs/azureossds/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python).

## <a name="where-are-the-tomcat-log-files-located"></a>Hol találhatók a Tomcat-naplófájlok?

Az Azure Marketplace és az egyéni telepítések esetén:

* Mappa helye: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Fontos fájlok:
    * Catalina. *éééé-hh-nn*. log
    * gazdagép-kezelő. *éééé-hh-nn*. log
    * localhost. *éééé-hh-nn*. log
    * Manager. *éééé-hh-nn*. log
    * site_access_log. *éééé-hh-nn*. log


Portál **alkalmazás beállításai** :

* Mappa helye: D:\home\LogFiles
* Fontos fájlok:
    * Catalina. *éééé-hh-nn*. log
    * gazdagép-kezelő. *éééé-hh-nn*. log
    * localhost. *éééé-hh-nn*. log
    * Manager. *éééé-hh-nn*. log
    * site_access_log. *éééé-hh-nn*. log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Hogyan a JDBC illesztőprogram-kapcsolatok hibáinak elhárítása?

A Tomcat-naplókban a következő üzenet jelenhet meg:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

A hiba elhárítása:

1. Távolítsa el az sqljdbc*. jar fájlt az alkalmazás/lib mappából.
2. Ha az egyéni tomcat vagy az Azure Marketplace tomcat webkiszolgálót használja, másolja ezt a. jar-fájlt a Tomcat lib mappájába.
3. Ha engedélyezi a javát a Azure Portalból (válassza a **Java 1,8**  >  **tomcat-kiszolgálót**), másolja a sqljdbc. * jar fájlt az alkalmazásával párhuzamos mappába. Ezután adja hozzá a következő osztályútvonal-beállítást a web.config fájlhoz:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Miért látok hibaüzeneteket az élő naplófájlok másolásának megkísérlése során?

Ha a Java-alkalmazások (például a Tomcat) élő naplófájljainak másolását kísérli meg, akkor a következő FTP-hiba jelenhet meg:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Előfordulhat, hogy a hibaüzenet az FTP-ügyféltől függően változhat.

Minden Java-alkalmazás rendelkezik ezzel a zárolási hibával. Csak a kudu támogatja a fájl letöltését az alkalmazás futása közben.

Az alkalmazás leállítása lehetővé teszi az FTP-hozzáférést ezekhez a fájlokhoz.

Egy másik megkerülő megoldás egy olyan Webjobs írása, amely egy ütemezett időpontban fut, és átmásolja ezeket a fájlokat egy másik könyvtárba. Egy minta projekt esetében tekintse meg a [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) projektet.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Hol találhatók a Jetty naplófájljai?

A piactér és az egyéni telepítések esetében a naplófájl a D:\home\site\wwwroot\bin\jetty-Distribution-9.1.2.v20140210\logs mappában található. Vegye figyelembe, hogy a mappa helye a használt Jetty verziójától függ. Például az itt megadott elérési út a Jetty 9.1.2. Keresse meg a jetty_ *YYYY_MM_DD*. stderrout. log naplófájlt.

A portál alkalmazás telepítése esetén a naplófájl a következő helyen található: D:\home\LogFiles. Keresse meg a jetty_ *YYYY_MM_DD*. stderrout. log naplófájlt

## <a name="can-i-send-email-from-my-azure-web-app"></a>Küldhetek e-mailt az Azure-webalkalmazásból?

App Service nem rendelkezik beépített e-mail-funkcióval. Az alkalmazásból érkező e-mailek küldésének jó alternatíváiért tekintse meg ezt a [stack overflow vitát](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Miért történik a WordPress-oldalam átirányítása egy másik URL-címre?

Ha nemrég áttelepítette az Azure-ba, a WordPress átirányíthatja a régi tartomány URL-címét. Ezt a MySQL-adatbázis egyik beállítása okozza.

A WordPress Buddy + egy Azure-beli hely bővítmény, amellyel az átirányítási URL-cím közvetlenül az adatbázisban frissíthető. A WordPress Buddy + használatával kapcsolatos további információkért lásd: [a WordPress-eszközök és a MySQL-áttelepítés a WordPress Buddy +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows)segítségével.

Ha az átirányítási URL-címet az SQL-lekérdezések vagy a PHPMyAdmin használatával szeretné manuálisan frissíteni, tekintse meg a [WordPress: átirányítást a helytelen URL-címre](/archive/blogs/azureossds/wordpress-redirecting-to-wrong-url).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Hogyan módosítja a WordPress bejelentkezési jelszavát?

Ha elfelejtette a WordPress bejelentkezési jelszavát, akkor a WordPress Buddy + használatával frissítheti azt. A jelszó alaphelyzetbe állításához telepítse a WordPress Buddy + Azure site bővítményt, majd hajtsa végre a [WordPress-eszközök és a MySQL-áttelepítés a WordPress Buddy +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows)használatával című témakörben ismertetett lépéseket.

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Nem tudok bejelentkezni a WordPress webalkalmazásba. Hogyan oldhatom meg ezt?

Ha úgy találja, hogy kizárta a WordPresst, miután nemrég telepített egy beépülő modult, lehet, hogy hibás a beépülő modul. A WordPress Buddy + egy Azure-beli bővítmény, amely segítséget nyújt a beépülő modulok letiltásához a WordPress webhelyén. További információ: [WordPress-eszközök és MySQL-áttelepítés a WordPress Buddy +-vel](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Hogyan áttelepíteni a WordPress-adatbázist?

A WordPress-webhelyhez csatlakoztatott MySQL-adatbázis áttelepítésének több lehetősége van:

* Fejlesztők: a [parancssor vagy a phpMyAdmin](/archive/blogs/azureossds/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service) használata
* Nem fejlesztőknek: a [WordPress Buddy +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows) használata

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Hogyan segít a WordPress biztonságosabbá tételében?

A WordPress biztonsági eljárásaival kapcsolatos további információkért lásd: [ajánlott eljárások a WordPress biztonságához az Azure-ban](/archive/blogs/azureossds/best-practices-for-wordpress-security-on-azure).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>A PHPMyAdmin használatával próbálkozom, és a "hozzáférés megtagadva" üzenet jelenik meg. Hogyan oldhatom meg ezt?

Ez a probléma akkor fordulhat elő, ha a beépített mySQL funkció még nem fut ebben a App Service-példányban. A probléma megoldásához próbálja meg elérni a webhelyét. Ez elindítja a szükséges folyamatokat, beleértve a beépített mySQL folyamatot is. Annak ellenőrzéséhez, hogy a beépített mySQL fut-e, a Process Explorerben ellenőrizze, hogy a mysqld.exe szerepel-e a folyamatokban.

Miután meggyőződni arról, hogy a beépített mySQL fut, próbálkozzon a PHPMyAdmin használatával.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>HTTP 403 hibaüzenetet kapok, amikor a PHPMyadmin használatával megpróbálom importálni vagy exportálni az beépített mySQL-adatbázist. Hogyan oldhatom meg ezt?

Ha a Chrome egy régebbi verzióját használja, lehet, hogy ismert hibát észlelt. A probléma megoldásához frissítsen a Chrome újabb verziójára. Próbáljon másik böngészőt használni, például az Internet Explorert vagy a Microsoft Edge-t, ahol a probléma nem fordul elő.