---
title: Az Azure Spring Cloud hibaelhárítási útmutatója | Microsoft Docs
description: Az Azure Spring Cloud hibaelhárítási útmutatója
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7c4dbeae0cfb76063fdca70b3ad1d264b59f9ace
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634243"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Az Azure Spring Cloud-problémák gyakori problémáinak elhárítása

Ez a cikk útmutatást nyújt az Azure Spring Cloud Development-problémák megoldásához. További információ: [Azure Spring Cloud – gyakori kérdések](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Rendelkezésre állással, teljesítménnyel és alkalmazásokkal kapcsolatos problémák

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Az alkalmazás nem indítható el (például a végpont nem csatlakoztatható, vagy egy 502 értéket ad vissza néhány újrapróbálkozás után)

Exportálja a naplókat az Azure Log Analyticsba. A rugós alkalmazások naplóihoz tartozó tábla neve *AppPlatformLogsforSpring*. További információ: [naplók és mérőszámok elemzése diagnosztikai beállításokkal](diagnostic-services.md).

A naplókban a következő hibaüzenet jelenhet meg:

> "org. springframework. Context. ApplicationContextException: nem sikerült elindítani a webkiszolgálót"

Az üzenet a két valószínű probléma egyikét jelzi: 
* Hiányzik az egyik bab vagy annak valamelyik függősége.
* A bean egyik tulajdonsága hiányzik vagy érvénytelen. Ebben az esetben a "Java. lang. IllegalArgumentException" valószínűleg meg fog jelenni.

A szolgáltatási kötések az alkalmazás indítási hibáját is okozhatják. A naplók lekérdezéséhez használja a kötött szolgáltatásokhoz kapcsolódó kulcsszavakat. Tegyük fel például, hogy az alkalmazásnak van egy kötése egy helyi rendszeridőre beállított MySQL-példánnyal. Ha az alkalmazás nem indul el, a következő hibaüzenet jelenhet meg a naplóban:

> "Java. SQL. SQLException: a kiszolgáló időzóna-értéke" egyezményes világidő "nem ismerhető fel, vagy egynél több időzónát jelöl."

A hiba elhárításához lépjen a MySQL- `server parameters` példányhoz, és módosítsa az értéket a `time_zone` *rendszerről* a *+ 0:00* értékre.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Az alkalmazás összeomlik, vagy váratlan hibát ad vissza

Az alkalmazások összeomlásának hibakereséséhez először ellenőrizze az alkalmazás futási állapotát és felderítési állapotát. Ehhez nyissa meg a Azure Portal _alkalmazás-kezelés_ elemét, és győződjön meg arról, hogy az összes alkalmazás állapota _fut_ és _fel van állítva_.

* Ha az állapot _fut_ , de a felderítési állapot nem áll _fenn, lépjen_ a ["saját alkalmazás nem regisztrálható"](#my-application-cant-be-registered) szakaszra.

* Ha a felderítés állapota _fel van állítva_, a metrikák elemre kattintva ellenőrizze az alkalmazás állapotát. Vizsgálja meg a következő metrikákat:


  - `TomcatErrorCount` (_tomcat. Global. Error_): az összes Spring Application-kivételt itt számoljuk el. Ha ez a szám nagy, nyissa meg az Azure Log Analytics az alkalmazás naplófájljainak vizsgálatához.

  - `AppMemoryMax` (_JVM. Memory. max_): az alkalmazás számára rendelkezésre álló memória maximális mennyisége. Lehet, hogy az összeg nem definiált, vagy idővel változhat, ha meg van adva. Ha meg van adva, a felhasznált és az előjegyzett memória mennyisége mindig kisebb vagy egyenlő, mint Max. Előfordulhat azonban, hogy egy memória kiosztása egy `OutOfMemoryError` üzenettel meghiúsul, ha a foglalás a használt memóriát úgy próbálja megjavítani, hogy az *> véglegesítése* során is felhasználja, még akkor is, ha *<= Max* még mindig igaz. Ilyen esetben próbálja meg a maximális halom méretének növelését a `-Xmx` paraméter használatával.

  - `AppMemoryUsed` (_JVM. Memory. használt_): az alkalmazás által jelenleg használt memória mennyisége bájtban kifejezve. A normál betöltésű Java-alkalmazások esetében ez a metrika egy *fűrészfog* mintát alkot, ahol a memóriahasználat folyamatosan nő és csökken, és hirtelen leesik, majd a mintázat ismétlődik. Ez a metrikai sorozat a Java virtuális gépen belüli adatgyűjtési művelet miatt következik be, ahol a gyűjtési műveletek a fűrészfog mintában lévő cseppeket jelölik.
    
    Ez a mérőszám fontos a memóriával kapcsolatos problémák azonosításához, például:
    * A memória alábontása a legelején.
    * A túlterhelési memória kiosztása egy adott logikai útvonalhoz.
    * Fokozatos memóriavesztés.
  További információ: [mérőszámok](spring-cloud-concept-metrics.md).
  
* Ha az alkalmazás nem indul el, ellenőrizze, hogy az alkalmazás rendelkezik-e érvényes JVM-paraméterekkel. Ha a JVM-memória túl magasra van állítva, a következő hibaüzenet jelenhet meg a naplókban:

  >"a szükséges memória-2728741K nagyobb, mint a kiosztáshoz elérhető 2000M."



Ha többet szeretne megtudni az Azure Log Analytics-ról, tekintse meg a [log Analytics beszerzése a Azure monitorban](../azure-monitor/logs/log-analytics-tutorial.md)című témakört.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Az alkalmazás CPU- vagy memóriahasználata magas

Ha az alkalmazás magas CPU-vagy memóriahasználat-használatot tapasztal, az egyik két dolog igaz:
* Az összes alkalmazás-példány magas CPU-vagy memóriahasználat-használattal rendelkezik.
* Néhány alkalmazás-példány magas CPU-vagy memóriahasználat-használatot tapasztal.

A következő esetekben érdemes megállapítani, hogy melyik helyzet érvényes:

1. Lépjen a **metrikák** elemre, majd válassza ki a **szolgáltatás CPU-kihasználtságának százalékos arányát** vagy a **használt szolgáltatási memóriát**.
2. Adjon hozzá egy **app =** filtert a figyelni kívánt alkalmazás megadásához.
3. A metrikák felosztása **példány** szerint.

Ha az *összes példány* magas CPU-vagy memóriahasználat-használatot tapasztal, ki kell bővíteni az alkalmazást, vagy fel kell mérnie a processzor vagy a memória használatát. További információ: [oktatóanyag: alkalmazások méretezése az Azure Spring Cloud-ban](spring-cloud-tutorial-scale-manual.md).

Ha *egyes példányok* magas CPU-vagy memóriahasználat-használatot tapasztalnak, ellenőrizze a példány állapotát és a felderítési állapotát.

További információ: [mérőszámok az Azure Spring Cloud](spring-cloud-concept-metrics.md)-hoz.

Ha minden példány fut, lépjen az Azure Log Analyticsra az alkalmazás naplófájljainak lekérdezéséhez, és tekintse át a kód logikáját. Ez segít megtekinteni, hogy ezek bármelyike érintheti-e a méretezési particionálást. További információ: [naplók és mérőszámok elemzése diagnosztikai beállításokkal](diagnostic-services.md).

Ha többet szeretne megtudni az Azure Log Analytics-ról, tekintse meg a [log Analytics beszerzése a Azure monitorban](../azure-monitor/logs/log-analytics-tutorial.md)című témakört. A naplókat a [Kusto lekérdezési nyelv](/azure/kusto/query/)használatával kérdezheti le.

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Ellenőrzőlista a Spring-alkalmazás üzembe helyezéséhez az Azure Spring Cloud-ban

Az alkalmazás előkészítése előtt győződjön meg arról, hogy az megfelel a következő feltételeknek:

* Az alkalmazás helyileg futtatható a Java futtatókörnyezet megadott verziójával.
* A környezeti konfiguráció (CPU/RAM/példányok) megfelel az alkalmazás szolgáltatója által beállított minimális követelménynek.
* A konfigurációs elemek a várt értékekkel rendelkeznek. További információ: [konfigurációs kiszolgáló](spring-cloud-tutorial-config-server.md).
* A környezeti változókhoz a várt értékek tartoznak.
* A JVM paraméterei a várt értékekkel rendelkeznek.
* Javasoljuk, hogy tiltsa le vagy távolítsa el a beágyazott _konfigurációs kiszolgálót_ és a _Spring Service Registry_ Servicest az alkalmazáscsomag alapján.
* Ha vannak _szolgáltatáskötéssel_ kötni kívánt Azure-erőforrások, ellenőrizze, hogy a célerőforrások működnek-e.

## <a name="configuration-and-management"></a>Konfigurálás és felügyelet

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Probléma merült fel egy Azure Spring Cloud Service-példány létrehozásával

Ha az Azure Spring Cloud Service-példányt a Azure Portal használatával állítja be, az Azure Spring Cloud végrehajtja az érvényesítést.

Ha azonban az Azure [CLI](/cli/azure/get-started-with-azure-cli) vagy a [Azure Resource Manager sablon](../azure-resource-manager/index.yml)használatával próbálja beállítani az Azure Spring Cloud Service-példányt, ellenőrizze a következőket:

* Az előfizetés aktív.
* A helyet az Azure Spring Cloud [támogatja](spring-cloud-faq.md) .
* A példányhoz tartozó erőforráscsoport már létre van hozva.
* Az erőforrás neve megfelel az elnevezési szabálynak. Csak kisbetűket, számokat és kötőjeleket tartalmazhat. Az első karakternek betűnek kell lennie. Az utolsó karakternek betűnek vagy számnak kell lennie. Az értéknek 2 és 32 karakter közöttinek kell lennie.

Ha az Azure Spring Cloud Service-példányt a Resource Manager-sablonnal szeretné beállítani, először olvassa el [a Azure Resource Manager sablonok struktúrájának és szintaxisának megismerését](../azure-resource-manager/templates/template-syntax.md)ismertető témakört.

A rendszer az Azure Spring Cloud Service-példány nevét fogja használni a (z) altartomány nevének megadásához `azureapps.io` , így a telepítés sikertelen lesz, ha a név ütközik egy meglévővel. Előfordulhat, hogy további részleteket talál a tevékenység naplóiban.

### <a name="i-cant-deploy-a-net-core-app"></a>Nem tudok .NET Core-alkalmazást üzembe helyezni

A .NET Core Steeltoe alkalmazáshoz nem tölthet fel *. zip* fájlt a Azure Portal vagy a Resource Manager-sablon használatával.

Az alkalmazáscsomag [Azure CLI](/cli/azure/get-started-with-azure-cli)-vel történő telepítésekor az Azure CLI rendszeresen lekérdezi a telepítési folyamatot, és végül megjeleníti a központi telepítés eredményét.

Győződjön meg arról, hogy az alkalmazás a megfelelő *. zip* -fájlformátumban van csomagolva. Ha nem megfelelően van csomagolva, a folyamat nem válaszol, vagy hibaüzenet jelenik meg.

### <a name="i-cant-deploy-a-jar-package"></a>Nem tudok telepíteni egy JAR-csomagot

A Java Archive file (JAR)/Source csomag nem tölthető fel a Azure Portal vagy a Resource Manager-sablon használatával.

Az alkalmazáscsomag [Azure CLI](/cli/azure/get-started-with-azure-cli)-vel történő telepítésekor az Azure CLI rendszeresen lekérdezi a telepítési folyamatot, és végül megjeleníti a központi telepítés eredményét.

Ha a lekérdezés megszakad, továbbra is használhatja a következő parancsot az üzembehelyezési naplók lekéréséhez:

`az spring-cloud app show-deploy-log -n <app-name>`

Győződjön meg arról, hogy az alkalmazás a megfelelő [végrehajtható jar-formátumban](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)van csomagolva. Ha nem megfelelően van csomagolva, a következőhöz hasonló hibaüzenet jelenik meg:

> "Hiba: érvénytelen vagy sérült jarfile/jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Nem tudom üzembe helyezni a forrásoldali csomagot

A Azure Portal vagy a Resource Manager-sablon használatával nem tölthet fel JAR/forrásoldali csomagot.

Az alkalmazáscsomag [Azure CLI](/cli/azure/get-started-with-azure-cli)-vel történő telepítésekor az Azure CLI rendszeresen lekérdezi a telepítési folyamatot, és végül megjeleníti a központi telepítés eredményét.

Ha a lekérdezés megszakad, továbbra is használhatja a következő parancsot a buildelési és üzembehelyezési naplók lekéréséhez:

`az spring-cloud app show-deploy-log -n <app-name>`

Vegye figyelembe azonban, hogy egy Azure Spring Cloud Service-példány egyszerre csak egy felépítési feladatot tud kiváltani egy adott csomagra. További információ: [alkalmazás üzembe helyezése](spring-cloud-quickstart.md) és [átmeneti környezet beállítása az Azure Spring Cloud-ban](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cant-be-registered"></a>Az alkalmazásom nem regisztrálható

A legtöbb esetben ez a helyzet akkor fordul elő, ha a *szükséges függőségek* és a *szolgáltatások felderítése* nem megfelelően van konfigurálva a Project Object Model (POM) fájlban. A konfigurálását követően a beépített szolgáltatás beállításjegyzék-kiszolgálói végpontja környezeti változóként van befecskendezve az alkalmazással. Az alkalmazások ezután regisztrálják magukat a szolgáltatás beállításjegyzék-kiszolgálójával, és felfedezhetik a többi függő szolgáltatást.

Várjon legalább két percet, mielőtt egy újonnan regisztrált példány megkezdi a forgalom fogadását.

Ha egy meglévő Spring Cloud-alapú megoldást telepít át az Azure-ba, győződjön meg arról, hogy az ad-hoc _szolgáltatás beállításjegyzék_ -és _konfigurációs kiszolgálói_ példányai el lesznek távolítva (vagy le vannak tiltva) az Azure Spring Cloud által biztosított felügyelt példányok ütközésének elkerülése érdekében.

A _szolgáltatás beállításjegyzékbeli_ ügyfél-naplófájljait is megtekintheti az Azure log Analyticsban. További információ: [naplók és mérőszámok elemzése diagnosztikai beállításokkal](diagnostic-services.md)

Ha többet szeretne megtudni az Azure Log Analytics-ról, tekintse meg a [log Analytics beszerzése a Azure monitorban](../azure-monitor/logs/log-analytics-tutorial.md)című témakört. A naplókat a [Kusto lekérdezési nyelv](/azure/kusto/query/)használatával kérdezheti le.

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Meg szeretném vizsgálni az alkalmazás környezeti változóit

A környezeti változók tájékoztatják az Azure Spring Cloud Framework szolgáltatást, hogy az Azure tisztában legyen azzal, hol és hogyan konfigurálhatja az alkalmazást alkotó szolgáltatásokat. A környezeti változók helyességének biztosításához szükséges első lépés a lehetséges problémák elhárítása.  A Spring boot indítószerkezet végpontja segítségével áttekintheti a környezeti változókat.  

> [!WARNING]
> Ez az eljárás a környezeti változókat a tesztelési végpont használatával teszi elérhetővé.  Ne folytassa, ha a tesztelési végpont nyilvánosan elérhető, vagy ha tartománynevet rendelt hozzá az alkalmazáshoz.

1. Nyissa meg a következőt: `https://<your application test endpoint>/actuator/health`.  
    - Egy hasonló válasz, `{"status":"UP"}` amely azt jelzi, hogy a végpont engedélyezve van.
    - Ha a válasz negatív, adja meg a következő függőséget a *POM.xml* fájlban:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Ha engedélyezve van a Spring boot indítószerkezet végpontja, lépjen a Azure Portalra, és keresse meg az alkalmazás konfigurációs lapját.  Adjon hozzá egy környezeti változót a névvel `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` és az értékkel `*` . 

1. Indítsa újra az alkalmazást.

1. Keresse meg `https://<your application test endpoint>/actuator/env` és vizsgálja meg a választ.  Ennek így kell kinéznie:

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

Keresse meg a nevű gyermek csomópontot `systemEnvironment` .  Ez a csomópont tartalmazza az alkalmazás környezeti változóit.

> [!IMPORTANT]
> Ne felejtse el megfordítani a környezeti változók expozícióját, mielőtt az alkalmazása elérhetővé váljon a nyilvánosság számára.  Nyissa meg a Azure Portal, keresse meg az alkalmazás konfigurációs lapját, és törölje a következő környezeti változót:  `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` .

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Nem találom az alkalmazás metrikáit vagy naplóit

Az alkalmazások **felügyeletének** megtartásával _ellenőrizze, hogy_ _fut_ -e az alkalmazás állapota.

Ellenőrizze, hogy az _JMX_ engedélyezve van-e az alkalmazáscsomag. Ez a funkció a Configuration tulajdonsággal engedélyezhető `spring.jmx.enabled=true` .  

Ellenőrizze, hogy a `spring-boot-actuator` függőség engedélyezve van-e az alkalmazáscsomag, és hogy sikeresen elindul-e.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Ha az alkalmazás naplói archiválható egy Storage-fiókba, de az Azure Log Analyticsba nem küldték, ellenőrizze, hogy [helyesen állította-e be a munkaterületet](../azure-monitor/logs/quick-create-workspace.md). Ha az Azure Log Analytics ingyenes szintjét használja, vegye figyelembe, hogy [az ingyenes szint nem biztosít szolgáltatói szerződést (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).

## <a name="next-steps"></a>Következő lépések

* [Az Azure Spring Cloud szolgáltatásban felmerülő problémák önálló diagnosztizálása és megoldása](spring-cloud-howto-self-diagnose-solve.md)
