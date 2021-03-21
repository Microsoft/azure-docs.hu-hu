---
title: Adatforrások összekötése a Logstash-on keresztül az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan továbbíthat naplókat külső adatforrásokból az Azure Sentinel szolgáltatásba a Logstash használatával.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2020
ms.author: yelevin
ms.openlocfilehash: da7d540a4b7982c7f743a7ae968515485b45aa5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035427"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>Adatforrások az Azure Sentinelhez való összekapcsolásához használja a Logstash-t

> [!IMPORTANT]
> A Logstash kimeneti beépülő modullal történő adatfeldolgozás jelenleg nyilvános előzetes verzióban érhető el. Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Sentinel kimeneti beépülő moduljának használatával a **Logstash adatgyűjtő motorhoz** bármilyen típusú naplót küldhet a Logstash-en keresztül közvetlenül az azure Sentinel Log Analytics munkaterületére. A rendszer elküldi a naplókat egy egyéni táblába, amelyet a kimeneti beépülő modullal fog meghatározni.

Ha többet szeretne megtudni a Logstash-adatgyűjtési motor használatáról, tekintse meg a [Logstash használatának első lépéseit](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)ismertető témakört.

## <a name="overview"></a>Áttekintés

### <a name="architecture-and-background"></a>Architektúra és háttér

![A log rejtő architektúra ábrája.](./media/connect-logstash/logstash-architecture.png)

A Logstash motor három összetevőből áll:

- Bemeneti beépülő modulok: a különböző forrásokból származó adatok testreszabott gyűjteménye.
- Beépülő modulok: az adatkezelés és-normalizálás a megadott feltételek szerint.
- Kimeneti beépülő modulok: gyűjtött és feldolgozott adatok testreszabott küldése különböző célhelyekre.

> [!NOTE]
> Az Azure Sentinel csak a saját megadott kimeneti beépülő modulját támogatja. Nem támogatja a külső féltől származó kimeneti bővítményeket az Azure Sentinel számára, vagy bármilyen más Logstash beépülő modult.

A Logstash készült Azure Sentinel kimeneti beépülő modul JSON formátumú adatokat küld a Log Analytics munkaterületre a Log Analytics HTTP-adatgyűjtő REST API használatával. Az adatgyűjtés egyéni naplókba történik.

- További információ a [Log Analytics Rest APIról](/rest/api/loganalytics/create-request).
- További információ az [Egyéni naplókról](../azure-monitor/agents/data-sources-custom-logs.md).

## <a name="deploy-the-azure-sentinel-output-plugin-in-logstash"></a>Az Azure Sentinel kimeneti beépülő modul üzembe helyezése a Logstash-ben

### <a name="step-1-installation"></a>1. lépés: telepítés

Az Azure Sentinel kimeneti beépülő modulja elérhető a Logstash gyűjteményben.

- A ***[Microsoft-Logstash-output-Azure-loganalytics](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics)*** beépülő modul telepítéséhez kövesse a Logstash [használata beépülő](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) modullal című dokumentumot.
   
- Ha a Logstash-rendszer nem rendelkezik internet-hozzáféréssel, kövesse a Logstash [Offline beépülő moduljának felügyeleti](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) dokumentumának utasításait az offline beépülő modul előkészítéséhez és használatához. (Ehhez szüksége lesz egy másik Logstash rendszer létrehozására internet-hozzáféréssel.)

### <a name="step-2-configuration"></a>2. lépés: konfiguráció

Használja a konfigurációs [fájl Logstash struktúrájában](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) található információkat, és adja hozzá az Azure Sentinel kimeneti beépülő modult a konfigurációhoz a következő kulcsokkal és értékekkel. (A megfelelő konfigurációs fájl szintaxisa a tábla után jelenik meg.)

| Mező neve | Adattípus | Description |
|----------------|---------------|-----------------|
| `workspace_id` | sztring | Adja meg a munkaterület AZONOSÍTÓjának GUID azonosítóját. * |
| `workspace_key` | sztring | Adja meg a munkaterület elsődleges kulcsának GUID azonosítóját. * |
| `custom_log_table_name` | sztring | Állítsa be annak a táblának a nevét, amelybe a rendszer betölti a naplókat. Kimeneti beépülő modulok esetében csak egy Táblanév lehet konfigurálni. A naplófájl az Azure Sentinelben a **naplók** területen jelenik meg, az **egyéni naplók** kategória **tábláiban** , `_CL` utótaggal. |
| `endpoint` | sztring | Nem kötelező kitölteni. Alapértelmezés szerint ez a Log Analytics végpont. Ez a mező egy alternatív végpont beállítására használható. |
| `time_generated_field` | sztring | Nem kötelező kitölteni. Ez a tulajdonság felülbírálja Log Analytics alapértelmezett **TimeGenerated** mezőjét. Adja meg az adatforrás timestamp mezőjének nevét. A mezőben szereplő értékeknek meg kell felelniük az ISO 8601 formátumának ( `YYYY-MM-DDThh:mm:ssZ` ) |
| `key_names` | array | Adja meg Log Analytics kimeneti séma mezőinek listáját. Minden listaelemet szimpla idézőjelek között kell megadni, és az elemeket vesszővel kell elválasztani, a teljes listát pedig szögletes zárójelek közé kell foglalni. Lásd az alábbi példát. |
| `plugin_flush_interval` | szám | Nem kötelező kitölteni. A beállítással adhatja meg a maximális időközt (másodpercben) az üzenetek átvitele között Log Analytics. Az alapértelmezett érték az 5. |
    | `amount_resizing` | boolean | Igaz vagy hamis. Engedélyezheti vagy letilthatja az automatikus skálázási mechanizmust, amely a fogadott naplófájlok mennyisége alapján módosítja az üzenet pufferének méretét. |
| `max_items` | szám | Nem kötelező kitölteni. Csak akkor érvényes `amount_resizing` , ha a "false" értékre van állítva. A használatával megadhatja a határértéket az üzenet pufferének méretétől (a rekordokban). Az alapértelmezett érték 2000.  |
| `azure_resource_id` | sztring | Nem kötelező kitölteni. Meghatározza annak az Azure-erőforrásnak az AZONOSÍTÓját, amelyben az adat található. <br>Az erőforrás-azonosító értéke különösen akkor hasznos, ha [erőforrás-környezeti RBAC](resource-context-rbac.md) használ, hogy csak bizonyos adatforrásokhoz férhessenek hozzá. |
| | | |

* A munkaterület-azonosítót és az elsődleges kulcsot a munkaterület-erőforrásban, az **ügynökök kezelése** területen találja.

#### <a name="sample-configurations"></a>Minta konfiguráció

Íme néhány olyan minta-konfiguráció, amely néhány különböző lehetőséget használ.

- Egy filebeat bemeneti csövet használó alapszintű konfiguráció:

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- Egy TCP bemeneti csövet használó alapszintű konfiguráció:

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- Speciális konfiguráció:

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > A belső működésével, konfigurálásával és teljesítményével kapcsolatos további információkért tekintse meg a [GitHub-tárház](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) kimeneti bővítményét.

### <a name="step-3-restart-logstash"></a>3. lépés: a Logstash újraindítása

### <a name="step-4-view-incoming-logs-in-azure-sentinel"></a>4. lépés: a bejövő naplók megtekintése az Azure Sentinelben

1. Ellenőrizze, hogy az üzenetek elküldése a kimeneti beépülő modulba történik-e.

1. Az Azure Sentinel navigációs menüjében kattintson a **naplók** elemre. A **táblák** fejléc alatt bontsa ki az **egyéni naplók** kategóriát. Keresse meg és kattintson a megadott tábla nevére ( `_CL` utótaggal) a konfigurációban.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="Képernyőkép a naplófájlok egyéni naplóiról.":::

1. A tábla rekordjainak megjelenítéséhez a tábla nevét a séma alapján kérdezheti le.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="Képernyőkép: a napló egyéni naplók lekérdezését rejti el.":::

## <a name="monitor-output-plugin-audit-logs"></a>A kimeneti beépülő modul naplófájljainak figyelése

Az Azure Sentinel kimeneti beépülő modul kapcsolatának és tevékenységének figyeléséhez engedélyezze a megfelelő Logstash-naplófájlt. A naplófájl helyének megtekintéséhez tekintse meg a [Logstash könyvtár-elrendezési](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) dokumentumát.

Ha nem lát adatokat ebben a naplófájlban, hozzon elő és küldjön el néhány eseményt helyileg (a bemeneti és a szűrő beépülő modullal), hogy a kimeneti beépülő modul adatokat kapjon. Az Azure Sentinel csak a kimeneti beépülő modullal kapcsolatos problémákat fogja támogatni.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan használhatja a Logstash-t külső adatforrások Azure Sentinelhez való összekapcsolásához. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerkedjen meg a fenyegetések észlelésével az Azure Sentinel használatával, [beépített](tutorial-detect-threats-built-in.md) vagy [Egyéni](tutorial-detect-threats-custom.md) szabályokkal.
