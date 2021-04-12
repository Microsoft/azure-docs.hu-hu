---
title: A metrikák és naplók összegyűjtése és megjelenítése az Azure Log Analytics használatával (előzetes verzió)
description: Megtudhatja, hogyan engedélyezheti a szinapszis beépített Azure Log Analytics-összekötőjét az Apache Spark alkalmazás-metrikák és naplók Azure Log Analytics-munkaterületre való összegyűjtéséhez és elküldéséhez.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 03/25/2021
ms.custom: references_regions
ms.openlocfilehash: 243618192593d93bba9d5229e7becfb2af62ce32
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108550"
---
# <a name="tutorial-use-azure-log-analytics-to-collect-and-visualize-metrics-and-logs-preview"></a>Oktatóanyag: mérőszámok és naplók összegyűjtésére és megjelenítésére az Azure Log Analytics használatával (előzetes verzió)

Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti a szinapszis beépített Azure Log Analytics-összekötőjét az Apache Spark alkalmazás-metrikák és naplók [azure log Analytics-munkaterületre](/azure/azure-monitor/logs/quick-create-workspace)való összegyűjtéséhez és elküldéséhez. Ezután kihasználhatja az Azure monitor-munkafüzetet a metrikák és naplók megjelenítéséhez.

## <a name="configure-azure-log-analytics-workspace-information-in-synapse-studio"></a>Az Azure Log Analytics-munkaterület adatainak konfigurálása a szinapszis Studióban

### <a name="step-1-create-an-azure-log-analytics-workspace"></a>1. lépés: Azure Log Analytics munkaterület létrehozása

Log Analytics munkaterület létrehozásához kövesse az alábbi dokumentumokat:
- [Log Analytics-munkaterület létrehozása az Azure Portalon](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace)
- [Log Analytics munkaterület létrehozása az Azure CLI-vel](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace-cli)
- [Log Analytics munkaterület létrehozása és konfigurálása Azure Monitorban a PowerShell használatával](https://docs.microsoft.com/azure/azure-monitor/logs/powershell-workspace-configuration)

### <a name="step-2-prepare-a-spark-configuration-file"></a>2. lépés: Spark konfigurációs fájl előkészítése

#### <a name="option-1-configure-with-azure-log-analytics-workspace-id-and-key"></a>1. lehetőség: Konfigurálás az Azure Log Analytics munkaterület-azonosító és-kulcs segítségével 

Másolja a következő Spark-konfigurációt, mentse **"spark_loganalytics_conf.txt"** értékre, és töltse ki a paramétereket:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Azure Log Analytics-munkaterület azonosítója.
   - `<LOG_ANALYTICS_WORKSPACE_KEY>`: Azure Log Analytics kulcs: **Azure Portal > Azure log Analytics munkaterület > ügynökök kezelése > elsődleges kulcs**

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.secret <LOG_ANALYTICS_WORKSPACE_KEY>
```

#### <a name="option-2-configure-with-an-azure-key-vault"></a>2. lehetőség: Konfigurálás Azure Key Vault

> [!NOTE]
>
> Olvasási titkos engedélyt kell adnia azoknak a felhasználóknak, akik beküldik a Spark-alkalmazásokat. Tekintse [meg az Azure szerepköralapú hozzáférés-vezérléssel rendelkező kulcsok, tanúsítványok és titkok Key Vault való hozzáférésének megadását](https://docs.microsoft.com/azure/key-vault/general/rbac-guide) ismertető témakört.

Ha egy Azure Key Vault szeretne konfigurálni a munkaterület kulcsának tárolásához, kövesse az alábbi lépéseket:

1. Hozzon létre és navigáljon a Key vaulthoz a Azure Portal
2. A Key Vault beállítások oldalain válassza a **titkok** elemet.
3. Kattintson a **Létrehozás/Importálás** gombra.
4. A **Titkos kód létrehozása** képernyőn válassza az alábbi értékeket:
   - **Név**: írja be a titok nevét, írja be az `"SparkLogAnalyticsSecret"` alapértelmezett nevet.
   - **Érték**: írja be a titok **<LOG_ANALYTICS_WORKSPACE_KEY>** .
   - A többi értéket hagyja az alapértelmezett értéken. Kattintson a **Létrehozás** lehetőségre.
5. Másolja a következő Spark-konfigurációt, mentse **"spark_loganalytics_conf.txt"** értékre, és töltse ki a paramétereket:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Azure Log Analytics-munkaterület azonosítója.
   - `<AZURE_KEY_VAULT_NAME>`: A beállított Azure Key Vault neve.
   - `<AZURE_KEY_VAULT_SECRET_KEY_NAME>` (Nem kötelező): a titok neve a munkaterület kulcsának Azure Key Vaultjában, alapértelmezett: "SparkLogAnalyticsSecret".

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
```

> [!NOTE]
>
> Az Log Analytics munkaterület-azonosítót az Azure Key vaultban is tárolhatja. Tekintse át a fenti lépéseket, és tárolja a munkaterület azonosítóját a titkos névvel `"SparkLogAnalyticsWorkspaceId"` . Vagy a konfiguráció használatával `spark.synapse.logAnalytics.keyVault.key.workspaceId` adja meg a munkaterület-azonosító titkos nevét az Azure Key vaultban.

#### <a name="option-3-configure-with-an-azure-key-vault-linked-service"></a>3. lehetőség: Konfigurálás Azure Key Vault társított szolgáltatással

> [!NOTE]
>
> Olvasási titkos engedélyt kell adnia a szinapszis munkaterülethez. Tekintse [meg az Azure szerepköralapú hozzáférés-vezérléssel rendelkező kulcsok, tanúsítványok és titkok Key Vault való hozzáférésének megadását](https://docs.microsoft.com/azure/key-vault/general/rbac-guide) ismertető témakört.

Ha Azure Key Vault társított szolgáltatást szeretne konfigurálni a szinapszis Studióban a munkaterület kulcsának tárolásához, kövesse az alábbi lépéseket:

1. Kövesse a szakasz lépéseit `Option 2. Configure with an Azure Key Vault` .
2. Azure Key Vault-beli társított szolgáltatás létrehozása a szinapszis Studióban:

    a. Navigáljon a **szinapszis Studio > a > társított szolgáltatások kezelése** elemre, majd kattintson az **új** gombra.

    b. Keressen **Azure Key Vault** a keresőmezőbe.

    c. Adja meg a társított szolgáltatás nevét.

    d. Válassza ki az Azure Key vaultot. Kattintson a **Létrehozás** lehetőségre.

3. Elem hozzáadása `spark.synapse.logAnalytics.keyVault.linkedServiceName` a Spark-konfigurációhoz.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
spark.synapse.logAnalytics.keyVault.linkedServiceName <LINKED_SERVICE_NAME>
```

#### <a name="available-spark-configuration"></a>Elérhető Spark-konfiguráció

| Konfiguráció neve                                  | Alapértelmezett érték                | Description                                                                                                                                                                                                |
| --------------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Spark. szinapszis. logAnalytics. enabled                  | hamis                        | Az Azure Log Analytics-fogadó a Spark-alkalmazásokhoz való engedélyezése igaz. Ellenkező esetben hamis.                                                                                                                  |
| Spark. szinapszis. logAnalytics. munkaterület azonosítója              | -                            | A cél Azure Log Analytics munkaterület azonosítója                                                                                                                                                          |
| Spark. szinapszis. logAnalytics. Secret                   | -                            | A cél Azure Log Analytics munkaterület titka.                                                                                                                                                      |
| Spark. szinapszis. logAnalytics. kulcstartó. linkedServiceName   | -                            | Az Azure Key Vault társított szolgáltatásának neve az Azure Log Analytics munkaterület-AZONOSÍTÓhoz és kulcshoz                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.name            | -                            | Az Azure Key Vault neve az Azure Log Analytics-AZONOSÍTÓhoz és-kulcshoz                                                                                                                                                |
| Spark. szinapszis. logAnalytics. kulcstartó. key. munkaterület azonosítója | SparkLogAnalyticsWorkspaceId | Azure Key Vault-titok neve az Azure Log Analytics munkaterület-AZONOSÍTÓhoz                                                                                                                                       |
| Spark. szinapszis. logAnalytics. kulcstartó. key. Secret      | SparkLogAnalyticsSecret      | Azure Key Vault-titok neve az Azure Log Analytics-munkaterület kulcsa                                                                                                                                      |
| Spark. szinapszis. logAnalytics. kulcstartó. uriSuffix       | ods.opinsights.azure.com     | A cél Azure Log Analytics munkaterület [URI-utótagja][uri_suffix]. Ha az Azure Log Analytics munkaterület nem az Azure Global-ban van, akkor a megfelelő felhőnek megfelelően frissítenie kell az URI-utótagot. |

> [!NOTE]  
> - Az Azure China felhők esetében a "Spark. szinapszis. logAnalytics. kulcstartó. uriSuffix" paraméternek "ods.opinsights.azure.cn" értéknek kell lennie. 
> - Az Azure gov-felhők esetében a "Spark. szinapszis. logAnalytics. kulcstartó. uriSuffix" paraméternek "ods.opinsights.azure.us" értéknek kell lennie. 

[uri_suffix]: https://docs.microsoft.com/azure/azure-monitor/logs/data-collector-api#request-uri


### <a name="step-3-upload-your-spark-configuration-to-a-spark-pool"></a>3. lépés: a Spark-konfiguráció feltöltése Spark-készletbe
A konfigurációs fájlt fel lehet tölteni a szinapszis Spark-készletbe a szinapszis Studióban.

   1. Navigáljon a Apache Spark-készlethez az Azure szinapszis Studióban (Manage-> Apache Spark-készletek)
   2. Kattintson a Apache Spark készlet jobb oldalán található **"..."** gombra.
   3. Apache Spark konfiguráció kiválasztása 
   4. Kattintson a **feltöltés** gombra, és válassza a létrehozott **"spark_loganalytics_conf.txt"** lehetőséget.
   5. Kattintson a **feltöltés** és **alkalmaz** gombra.

      > [!div class="mx-imgBorder"]
      > ![Spark-készlet konfigurálása](./media/apache-spark-azure-log-analytics/spark-pool-configuration.png)

> [!NOTE] 
>
> A fenti Spark-készletbe küldött összes Spark-alkalmazás a konfigurációs beállítás használatával küldi el a Spark-alkalmazás metrikáit és naplóit a megadott Azure Log Analytics-munkaterületre.

## <a name="submit-a-spark-application-and-view-the-logs-and-metrics-in-azure-log-analytics"></a>Beküld egy Spark-alkalmazást, és megtekintheti a naplókat és mérőszámokat az Azure Log Analytics

 1. Az alábbi módszerek egyikével elküldheti a Spark-alkalmazást az előző lépésben konfigurált Spark-készletbe:
    - A szinapszis Studio notebookjának futtatása. 
    - Egy szinapszis Apache Spark batch-feladatot küldhet a Spark-feladatdefiníció használatával.
    - Egy Spark-tevékenységet tartalmazó folyamat futtatása.

 2. Nyissa meg a megadott Azure Log Analytics munkaterületet, majd tekintse meg az alkalmazás metrikáit és naplóit, amikor a Spark-alkalmazás elindul.

## <a name="use-the-sample-azure-log-analytics-workbook-to-visualize-the-metrics-and-logs"></a>A mérőszámok és naplók megjelenítéséhez használja a minta Azure Log Analytics munkafüzetét

1. [Töltse le a munkafüzetet](https://aka.ms/SynapseSparkLogAnalyticsWorkbook) itt.
2. Nyissa meg és **másolja** a munkafüzet fájljának tartalmát.
3. Navigáljon az Azure Log Analytics munkafüzethez ([Azure Portal](https://portal.azure.com/) > Log Analytics munkaterület > munkafüzetek)
4. Nyissa meg az **"üres"** Azure log Analytics munkafüzetet a **"speciális szerkesztő"** módban (nyomja meg a </> ikont).
5. **Illessze be** a létező JSON-t.
6. Ezután nyomja meg az **alkalmaz** , majd a **Szerkesztés** gombot.

    > [!div class="mx-imgBorder"]
    > ![Új munkafüzet](./media/apache-spark-azure-log-analytics/new-workbook.png)

    > [!div class="mx-imgBorder"]
    > ![munkafüzet importálása](./media/apache-spark-azure-log-analytics/import-workbook.png)

Ezután küldje be Apache Spark alkalmazást a beállított Spark-készletbe. Miután az alkalmazás futása megszakadt, válassza ki a futó alkalmazást a munkafüzet legördülő listában.

> [!div class="mx-imgBorder"]
> ![munkafüzet inange](./media/apache-spark-azure-log-analytics/workbook.png)

A munkafüzetet a Kusto lekérdezéssel és a riasztások konfigurálásával is testreszabhatja.

> [!div class="mx-imgBorder"]
> ![kusto-lekérdezés és-riasztások](./media/apache-spark-azure-log-analytics/kusto-query-and-alerts.png)

## <a name="sample-kusto-queries"></a>Példa Kusto-lekérdezésekre

1. Példa a Spark-események lekérdezésére.

   ```kusto
   SparkListenerEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100 
   ```

2. Példa a Spark-alkalmazás illesztőprogramjának és a végrehajtói naplóinak lekérdezésére.

   ```kusto
   SparkLoggingEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100
   ```

3. Példa a Spark-metrikák lekérdezésére.

   ```kusto
   SparkMetrics_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | where name_s endswith "jvm.total.used"
   | summarize max(value_d) by bin(TimeGenerated, 30s), executorId_s
   | order by TimeGenerated asc
   ```

## <a name="create-and-manage-alerts-using-azure-log-analytics"></a>Riasztások létrehozása és kezelése az Azure Log Analytics

Azure Monitor riasztások lehetővé teszik a felhasználók számára, hogy egy Log Analytics-lekérdezést használjanak a metrikák kiértékeléséhez és az összes beállított gyakoriság naplózásához, valamint egy riasztást az eredmények alapján.

További információ: a [naplók létrehozása, megtekintése és kezelése Azure monitor használatával](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log).

## <a name="limitation"></a>Korlátozás

 - A [felügyelt virtuális hálózattal](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-vnet) rendelkező Azure szinapszis Analytics-munkaterület használata nem támogatott.
 - A következő régiók jelenleg nem támogatottak:
   - USA 2. keleti régiója
   - Kelet-Norvégia
   - Észak-Egyesült Arab

## <a name="next-steps"></a>Következő lépések

 - Ismerje meg, hogyan [használható a kiszolgáló nélküli Apache Spark készlet a szinapszis Studióban](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio).
 - Megtudhatja, hogyan [futtathat Spark-alkalmazást a notebookon](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-development-using-notebooks).
 - Megtudhatja, hogyan [hozhat létre Apache Spark feladatdefiníció a szinapszis Studióban](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-job-definitions).