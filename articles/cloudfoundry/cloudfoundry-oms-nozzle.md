---
title: Azure Log Analytics szívófej üzembe helyezése Cloud Foundry monitorozáshoz
description: Részletes útmutató az Azure Log Analytics Cloud Foundry loggregator-fúvókának üzembe helyezéséhez. A fúvóka segítségével figyelje a Cloud Foundry rendszerállapot-és teljesítmény-mérőszámait.
services: virtual-machines
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 54001c47d03b686a8e7c1f59f1e53d405e3bc506
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557386"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Azure Log Analytics-szívófej üzembe helyezése Cloud Foundry rendszer-figyeléshez

[Azure monitor](https://azure.microsoft.com/services/log-analytics/) egy Azure-beli szolgáltatás. Segít összegyűjteni és elemezni a felhőből és a helyszíni környezetből létrehozott adatokat.

A Log Analytics fúvóka (a fúvóka) egy Cloud Foundry (CF) összetevő, amely a [Cloud Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose származó mérőszámokat Azure monitor naplókba továbbítja. A fúvókával összegyűjtheti, megtekintheti és elemezheti a CF rendszerállapot-és teljesítmény-mérőszámait több üzemelő példány között.

Ebből a dokumentumból megtudhatja, hogyan helyezheti üzembe a fúvókát a CF-környezetében, majd Hogyan férhet hozzá az adatokhoz a Azure Monitor logs konzolon.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

A következő lépések a fúvóka üzembe helyezésének előfeltételei.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. CF vagy Pivotal Cloud Foundry környezet üzembe helyezése az Azure-ban

Használhatja a fúvókát egy nyílt forráskódú CF üzembe helyezéssel vagy egy Pivotal Cloud Foundry (PCF) üzemelő példány használatával.

* [A Cloud Foundry on Azure üzembe helyezése](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Pivotal Cloud Foundry üzembe helyezése az Azure-ban](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Telepítse a CF parancssori eszközöket a szívófej üzembe helyezéséhez

A fúvóka alkalmazásként fut a CF-környezetben. Az alkalmazás telepítéséhez a CF CLI szükséges.

A szívófejnek hozzáférési engedéllyel kell rendelkeznie a loggregator firehose és a felhőalapú vezérlőhöz is. A felhasználó létrehozásához és konfigurálásához szüksége lesz a felhasználói fiók és a hitelesítés (MHT) szolgáltatásra.

* [Cloud Foundry parancssori felület telepítése](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Cloud Foundry UAA parancssori ügyfél telepítése](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Az UAA parancssori ügyfél beállítása előtt győződjön meg arról, hogy a RubyGems telepítve van.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Log Analytics munkaterület létrehozása az Azure-ban

A Log Analytics munkaterületet manuálisan vagy sablon használatával is létrehozhatja. A sablon üzembe helyezi az előre konfigurált KPI-nézetek és riasztások telepítését a Azure Monitor naplók konzolon. 

#### <a name="to-create-the-workspace-manually"></a>A munkaterület manuális létrehozása:

1. A Azure Portal keresse meg a szolgáltatások listáját az Azure piactéren, majd válassza a Log Analytics munkaterületek lehetőséget.
2. Válassza a **Létrehozás** lehetőséget, majd válassza ki a kívánt beállításokat a következő elemekhez:

   * **Log Analytics munkaterület**: írja be a munkaterület nevét.
   * **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki azt, amelyik megegyezik a CF üzembe helyezésével.
   * **Erőforráscsoport**: létrehozhat egy új erőforráscsoportot, vagy használhatja ugyanezt a CF-telepítéssel.
   * **Hely**: adja meg a helyet.
   * **Árképzési szintek**: válassza **az OK gombot** a befejezéshez.

További információ: Ismerkedés [a Azure monitor-naplókkal](../azure-monitor/overview.md).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Az Log Analytics munkaterület létrehozása a figyelési sablonnal az Azure Market helyről:

1. Nyissa meg az Azure Portalt.
1. Kattintson a "+" jelre, vagy az "erőforrás létrehozása" elemre a bal felső sarokban.
1. A keresés ablakban írja be a "Cloud Foundry" kifejezést, majd válassza a "Cloud Foundry figyelési megoldás" lehetőséget.
1. A Cloud Foundry figyelési megoldás sablonjának elülső lapja betöltődik, majd a Létrehozás gombra kattintva elindíthatja a sablon panelt.
1. Adja meg a szükséges paramétereket:
    * **Előfizetés**: válassza ki a log Analytics munkaterülethez tartozó Azure-előfizetést, amely általában megegyezik a Cloud Foundry üzemelő példányával.
    * **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat a log Analytics munkaterülethez.
    * **Erőforráscsoport helye**: válassza ki az erőforráscsoport helyét.
    * **OMS_Workspace_Name**: adjon meg egy munkaterület-nevet, ha a munkaterület nem létezik, akkor a sablon egy újat hoz létre.
    * **OMS_Workspace_Region**: válassza ki a munkaterület helyét.
    * **OMS_Workspace_Pricing_Tier**: válassza ki a log Analytics munkaterület SKU-t. Tekintse meg az [árképzési útmutatót](https://azure.microsoft.com/pricing/details/log-analytics/) a hivatkozáshoz.
    * **Jogi feltételek**: kattintson a jogi feltételek elemre, majd a "létrehozás" gombra a jogi kifejezés elfogadásához.
1. Az összes paraméter megadása után a sablon üzembe helyezéséhez kattintson a "létrehozás" gombra. Ha a telepítés befejeződött, az állapot megjelenik az értesítés lapon.


## <a name="deploy-the-nozzle"></a>A szívófej üzembe helyezése

A szívófej több módon is üzembe helyezhető: PCF csempeként vagy CF-alkalmazásként.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>A fúvóka üzembe helyezése PCF Ops Manager-csempével

Kövesse az [Azure log Analytics fúvóka PCF telepítéséhez és konfigurálásához](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html)szükséges lépéseket. Ez az egyszerűsített megközelítés, a PCF Ops Manager csempe automatikusan konfigurálja és leküldi a fúvókát. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>A szívófej manuális üzembe helyezése CF-alkalmazásként

Ha nem használja a PCF Ops-kezelőt, telepítse a fúvókát alkalmazásként. A következő szakaszok ismertetik ezt a folyamatot.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Bejelentkezés a CF üzembe helyezéséhez rendszergazdaként a CF CLI-n keresztül

Futtassa az alábbi parancsot:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

A "SYSTEM_DOMAIN" a CF-tartomány neve. A beolvasáshoz keresse meg a "SYSTEM_DOMAIN" kifejezést a CF üzembe helyezési jegyzékfájljában. 

A "CF_User" a CF rendszergazdai neve. A név és a jelszó lekéréséhez keresse meg a "scim" szakaszt, és a "cf_admin_password" kifejezést keresi a CF telepítési jegyzékfájljában.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>CF-felhasználó létrehozása és a szükséges jogosultságok megadása

Futtassa az alábbi parancsot:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

A "SYSTEM_DOMAIN" a CF-tartomány neve. A beolvasáshoz keresse meg a "SYSTEM_DOMAIN" kifejezést a CF üzembe helyezési jegyzékfájljában.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>A legújabb Log Analytics fúvóka kiadásának letöltése

Futtassa az alábbi parancsot:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Környezeti változók beállítása

Most már beállíthatja a környezeti változókat az aktuális könyvtár manifest. YML fájljában. Az alábbi ábrán a fúvóka alkalmazásának jegyzékfájlja látható. Cserélje le az értékeket az adott Log Analytics munkaterület adataira.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Az alkalmazás leküldése a fejlesztői számítógépről

Győződjön meg arról, hogy a OMS-log-Analytics-firehose-fúvóka mappában van. Futtassa az alábbi parancsot:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>A fúvóka telepítésének ellenőrzése

### <a name="from-apps-manager-for-pcf"></a>Az App Managerből (PCF)

1. Jelentkezzen be az Ops-kezelőbe, és ellenőrizze, hogy a csempe megjelenik-e a telepítési irányítópulton.
2. Jelentkezzen be az alkalmazás-kezelőbe, győződjön meg arról, hogy a szívófejhez létrehozott terület szerepel a használati jelentésben, és ellenőrizze, hogy az állapot normális-e.

### <a name="from-your-development-computer"></a>A fejlesztői számítógépről

A CF CLI-ablakban írja be a következőt:
```
cf apps
```
Ellenőrizze, hogy fut-e a OMS-fúvóka alkalmazás.

## <a name="view-the-data-in-the-azure-portal"></a>A Azure Portalban lévő adatmegjelenítés

Ha a figyelési megoldást a Piactéri sablonon keresztül telepítette, lépjen a Azure Portalra, és keresse meg a megoldást. A megoldás a sablonban megadott erőforráscsoporthoz is megkereshető. Kattintson a megoldásra, keresse meg a "log Analytics-konzolt", az előre konfigurált nézeteket, amelyek a fő Cloud Foundry rendszerkpi-k, az alkalmazásadatok, a riasztások és a VM-állapot metrikái. 

Ha manuálisan hozta létre a Log Analytics munkaterületet, kövesse az alábbi lépéseket a nézetek és a riasztások létrehozásához:

### <a name="1-import-the-oms-view"></a>1. importálja a OMS nézetet

A OMS-portálon keresse meg a **Designer**-  >  **Importálás**  >  **tallózását**, és válassza ki az egyik omsview-fájlt. Válassza például a *Cloud Foundry. omsview* elemet, és mentse a nézetet. Most egy csempe jelenik meg az **Áttekintés** oldalon. Jelölje ki a vizualizációs mérőszámok megjelenítéséhez.

Ezeket a nézeteket testreszabhatja, vagy létrehozhat új nézeteket a **Tervező nézet** használatával.

A *"Cloud Foundry. omsview"* a Cloud Foundry OMS View sablon előzetes verziója. Ez egy teljesen konfigurált, alapértelmezett sablon. Ha javaslata vagy visszajelzése van a sablonról, küldje el őket a [probléma szakaszba](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. riasztási szabályok létrehozása

[Létrehozhatók a riasztások](../azure-monitor/alerts/alerts-overview.md), és igény szerint testre szabhatók a lekérdezések és a küszöbértékek. A következő ajánlott riasztások:

| Keresési lekérdezés                                                                  | Riasztás előállítása a következő alapján | Leírás                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type = CF_ValueMetric_CL Origin_s = BBS Name_s = "tartomány. CF-apps"                   | Találatok száma < 1   | **BBS. Domain.cf – az alkalmazások** azt jelzik, hogy a CF-apps tartomány naprakész-e. Ez azt jelenti, hogy a Cloud Controller által benyújtott CF-alkalmazások szinkronizálása a BBS-be történik. LRPsDesired (Diego – kívánt AIs) a végrehajtáshoz. Nem érkezett adat: a CF-apps tartomány nem naprakész a megadott időablakban. |
| Type = CF_ValueMetric_CL Origin_s = rep Name_s = UnhealthyCell Value_d>1            | Találatok száma > 0   | Diego-sejtek esetén a 0 a kifogástalan állapotot jelenti, az 1 pedig sérült. Állítsa be a riasztást, ha a megadott időtartományban több nem kifogástalan állapotú Diego-cella észlelhető. |
| Type = CF_ValueMetric_CL Origin_s = "Bosh-HM-továbbító" Name_s = "System. Healthy" Value_d = 0 | Találatok száma > 0 | 1: a rendszer kifogástalan állapotú, és 0 azt jelenti, hogy a rendszer nem kifogástalan állapotú. |
| Type = CF_ValueMetric_CL Origin_s = route_emitter Name_s = ConsulDownMode Value_d>0 | Találatok száma > 0   | A konzul időről időre kibocsátja az állapotot. 0 azt jelenti, hogy a rendszer kifogástalan, és 1 azt jelenti, hogy az útvonal-kibocsátó észleli, hogy a konzul nem működik. |
| Type = CF_CounterEvent_CL Origin_s = DopplerServer (Name_s = "TruncatingBuffer. DroppedMessages" vagy Name_s = "Doppler. shedEnvelopes") Delta_d>0 | Találatok száma > 0 | A Doppler által a háttérbeli nyomás miatt szándékosan eldobott üzenetek különbözeti száma. |
| Type = CF_LogMessage_CL SourceType_s = LGR MessageType_s = ERR                      | Találatok száma > 0   | A Loggregator kibocsátja a **LGR** , hogy jelezze a naplózási folyamattal kapcsolatos problémákat. Ilyen probléma például akkor, ha a napló üzeneteinek kimenete túl magas. |
| Type = CF_ValueMetric_CL Name_s = slowConsumerAlert                               | Találatok száma > 0   | Ha a fúvóka lassú fogyasztói riasztást kap a loggregator, elküldi a **slowConsumerAlert** -ValueMetric Azure monitor naplókba. |
| Type = CF_CounterEvent_CL Job_s = fúvóka Name_s = eventsLost Delta_d>0              | Találatok száma > 0   | Ha az elveszett események különbözeti száma eléri a küszöbértéket, az azt jelenti, hogy a fúvóka probléma fut. |

## <a name="scale"></a>Méretezés

A fúvókát és a loggregator is méretezheti.

### <a name="scale-the-nozzle"></a>A szívófej méretezése

A fúvóka legalább két példányával kell kezdődnie. A firehose elosztja a számítási feladatot a fúvóka összes példánya között.
Annak ellenőrzéséhez, hogy a fúvóka képes-e a firehose lévő adatforgalom fenntartására, állítsa be a **slowConsumerAlert** riasztást (az előző szakaszban "riasztási szabályok létrehozása"). Miután elvégezte a riasztást, kövesse a [lassú fúvókákkal kapcsolatos útmutatót](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) , és állapítsa meg, hogy szükséges-e a skálázás.
A fúvóka vertikális felskálázásához használja az alkalmazások kezelőjét vagy a CF CLI-t a példányszám vagy a fúvóka memória-vagy lemez-erőforrásainak növeléséhez.

### <a name="scale-the-loggregator"></a>A loggregator skálázása

A Loggregator **LGR** küld a naplózási folyamattal kapcsolatos problémák jelzésére. Figyelheti a riasztást annak megállapítására, hogy a loggregator bővíteni kell-e.
A loggregator vertikális felskálázásához növelje a Doppler-puffer méretét, vagy vegyen fel további Doppler-kiszolgálói példányokat a CF-jegyzékfájlba. További információ: a [loggregator skálázásának útmutatója](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Frissítés

Ha újabb verzióval szeretné frissíteni a fúvókát, töltse le az új fúvóka-kiadást, kövesse az előző "a fúvóka üzembe helyezése" című szakaszt, és küldje el újra az alkalmazást.

### <a name="remove-the-nozzle-from-ops-manager"></a>A fúvóka eltávolítása az Ops-kezelőből

1. Jelentkezzen be az Ops-kezelőbe.
2. Keresse meg a **PCF csempe Microsoft Azure log Analytics fúvókát** .
3. Válassza ki a szemetet ikont, és erősítse meg a törlést.

### <a name="remove-the-nozzle-from-your-development-computer"></a>A fúvóka eltávolítása a fejlesztői számítógépről

A CF CLI-ablakba írja be a következőt:
```
cf delete <App Name> -r
```

Ha eltávolítja a fúvókát, a rendszer nem távolítja el automatikusan a OMS-portálon lévő adatfájlokat. Ez a Azure Monitor naplók adatmegőrzési beállításának függvényében lejár.

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Az Azure Log Analytics fúvóka nyílt forráskódú. Küldje el kérdéseit és visszajelzéseit a [GitHub szakaszra](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Azure-támogatási kérelem megnyitásához válassza a "Cloud Foundry-t futtató virtuális gép" lehetőséget a szolgáltatás kategóriája. 

## <a name="next-step"></a>Következő lépés

A PCF 2.0-s verziójában a virtuális gépek teljesítmény-mérőszámai az Azure Log Analytics-fúvókára kerülnek át a rendszermetrikák továbbítója által, és integrálva vannak a Log Analytics munkaterületbe. Már nincs szüksége a virtuális gép teljesítmény-metrikáinak Log Analytics ügynökére. Azonban továbbra is használhatja a Log Analytics ügynököt a syslog-adatok gyűjtésére. A Log Analytics ügynök a CF virtuális gépekhez tartozó Bosh-bővítményként van telepítve. 

Részletekért lásd: [log Analytics ügynök központi telepítése a Cloud Foundry üzemelő példányra](https://github.com/Azure/oms-agent-for-linux-boshrelease).