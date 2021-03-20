---
title: NSG-flow-naplók megjelenítése – rugalmas verem
titleSuffix: Azure Network Watcher
description: Hálózati biztonsági csoport Folyamatábráinak kezelése és elemzése az Azure-ban Network Watcher és rugalmas verem használatával.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: aca8c75f262e472cbc770c052b86d6e760ee449a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026471"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Azure Network Watcher NSG-forgalomnaplók vizualizációja nyílt forráskódú eszközök használatával

A hálózati biztonsági csoport folyamatábrája olyan információkat biztosít, amelyek segítségével megismerheti a bejövő és a kimenő IP-forgalmat a hálózati biztonsági csoportokon. Ezek a flow-naplók a kimenő és bejövő folyamatokat jelenítik meg egy szabály alapján, a folyamathoz tartozó hálózati adaptert, 5 rekordos információt a folyamatról (forrás/cél IP-cím, forrás/célport, protokoll), és ha a forgalmat engedélyezték vagy megtagadták.

Ezek a flow-naplók nehéz kézzel elemezni és betekintést nyerni. Azonban több nyílt forráskódú eszköz is rendelkezésre áll, amelyek segítenek megjeleníteni ezeket az adatforrásokat. Ez a cikk megoldást nyújt a naplók rugalmas verem használatával történő megjelenítésére, így gyorsan indexelheti és megjelenítheti a folyamat naplóit egy Kibana-irányítópulton.

## <a name="scenario"></a>Eset

Ebben a cikkben egy olyan megoldást hozunk létre, amely lehetővé teszi a hálózati biztonsági csoport folyamatábráinak megjelenítését a rugalmas verem használatával.  A Logstash bemeneti beépülő modulja közvetlenül a flow-naplókat tartalmazó tárolási blobból szerzi be a folyamat naplóit. Ezután a rugalmas verem használatával a rendszer indexeli a folyamat naplóit, és egy Kibana-irányítópult létrehozásához használja az információk megjelenítéséhez.

![A diagram egy olyan forgatókönyvet mutat be, amely lehetővé teszi a hálózati biztonsági csoport folyamatábráinak megjelenítését a rugalmas verem használatával.][scenario]

## <a name="steps"></a>Lépések

### <a name="enable-network-security-group-flow-logging"></a>Hálózati biztonsági csoport adatfolyam-naplózásának engedélyezése
Ebben a forgatókönyvben a hálózati biztonsági csoport flow-naplózási szolgáltatásának engedélyezve kell lennie legalább egy hálózati biztonsági csoportban a fiókjában. A hálózati biztonsági folyamatok naplófájljainak engedélyezésével kapcsolatos utasításokért tekintse meg az alábbi cikket a [hálózati biztonsági csoportok folyamatábrájának naplózása](network-watcher-nsg-flow-logging-overview.md)című cikkben.

### <a name="set-up-the-elastic-stack"></a>A rugalmas verem beállítása
Ha a NSG-flow naplóit a rugalmas Veremtel csatlakoztatja, létrehozhatunk egy Kibana-irányítópultot, amely lehetővé teszi számunkra, hogy megkeressék, megrajzoljuk, elemezzük és származtatjuk a naplókat.

#### <a name="install-elasticsearch"></a>A Elasticsearch telepítése

1. A 5,0-es és újabb verziókhoz tartozó rugalmas verem Java 8-at igényel. Futtassa a parancsot a `java -version` verziójának vizsgálatához. Ha nincs telepítve a Java, tekintse meg az [Azure-suppored JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support)dokumentációját.
2. Töltse le a rendszerének megfelelő bináris csomagot:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   További telepítési módszerek találhatók a Elasticsearch- [telepítésben](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Ellenőrizze, hogy a Elasticsearch fut-e a paranccsal:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Ehhez a következőhöz hasonló válasznak kell megjelennie:

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

A rugalmas keresés telepítésével kapcsolatos további információkért tekintse meg a [telepítési utasításokat](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>A Logstash telepítése

1. A Logstash telepítéséhez futtassa a következő parancsokat:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Ezután konfigurálnia kell a Logstash a folyamat naplófájljainak eléréséhez és elemzéséhez. Hozzon létre egy logstash. conf fájlt a használatával:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adja hozzá a következő tartalmat a fájlhoz:

   ```
   input {
      azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
            container => "insights-logs-networksecuritygroupflowevent"
            codec => "json"
            # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
            # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
            file_head_bytes => 12
            file_tail_bytes => 2
            # Enable / tweak these settings when event is too big for codec to handle.
            # break_json_down_policy => "with_head_tail"
            # break_json_batch_count => 2
        }
      }

      filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"}

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
      add_field => {
                  "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
                  "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
                  "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
                  "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
                  "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
                  "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
                  "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
                  "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
                  "flowstate" => "%{[records][properties][flows][flows][flowTuples][8]}"
                   "packetsSourceToDest" => "%{[records][properties][flows][flows][flowTuples][9]}"
                   "bytesSentSourceToDest" => "%{[records][properties][flows][flows][flowTuples][10]}"
                   "packetsDestToSource" => "%{[records][properties][flows][flows][flowTuples][11]}"
                   "bytesSentDestToSource" => "%{[records][properties][flows][flows][flowTuples][12]}"
                   }
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
       match => ["unixtimestamp" , "UNIX"]
     }
    }
   output {
     stdout { codec => rubydebug }
     elasticsearch {
       hosts => "localhost"
       index => "nsg-flow-logs"
     }
   }  
   ```

A Logstash telepítésével kapcsolatos további információkért tekintse meg a [hivatalos dokumentációt](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>A Logstash bemeneti beépülő moduljának telepítése az Azure Blob Storage-hoz

Ez a Logstash beépülő modul lehetővé teszi, hogy közvetlenül hozzáférhessen a folyamat naplófájljaihoz a kijelölt Storage-fiókból. A beépülő modul telepítéséhez az alapértelmezett Logstash telepítési könyvtárból (ebben az esetben a/usr/share/logstash/bin) futtassa a következő parancsot:

```bash
logstash-plugin install logstash-input-azureblob
```

A Logstash elindításához futtassa a következő parancsot:

```bash
sudo /etc/init.d/logstash start
```

A beépülő modullal kapcsolatos további információkért tekintse meg a [dokumentációt](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>A Kibana telepítése

1. Futtassa a következő parancsokat a Kibana telepítéséhez:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. A Kibana futtatásához használja a következő parancsokat:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. A Kibana webes felületének megtekintéséhez navigáljon a `http://localhost:5601`
4. Ebben az esetben a folyamat naplóihoz használt index minta a "NSG-flow-logs". Az index minta az logstash. conf fájl "output" szakaszában módosítható.
5. Ha távolról szeretné megtekinteni a Kibana-irányítópultot, hozzon létre egy bejövő NSG-szabályt, amely engedélyezi a hozzáférést a 5601-es **porthoz**.

### <a name="create-a-kibana-dashboard"></a>Kibana-irányítópult létrehozása

A következő ábrán látható a trendek és a riasztások részleteinek megtekintésére szolgáló minta irányítópult:

![1. ábra][1]

Töltse le az [irányítópult-fájlt](https://aka.ms/networkwatchernsgflowlogdashboard), a [vizualizációs fájlt](https://aka.ms/networkwatchernsgflowlogvisualizations)és a [mentett keresési fájlt](https://aka.ms/networkwatchernsgflowlogsearch).

A Kibana **felügyelet** lapján navigáljon a **mentett objektumokhoz** , és importálja mindhárom fájlt. Ezután az **irányítópult** lapon megnyithatja és betöltheti a minta irányítópultot.

Saját igényei szerint saját vizualizációkat és irányítópultokat is létrehozhat. További információ a Kibana-vizualizációk létrehozásáról a Kibana [hivatalos dokumentációjában](https://www.tutorialspoint.com/kibana/kibana_create_visualization.htm)olvasható.

### <a name="visualize-nsg-flow-logs"></a>NSG-naplók megjelenítése

A minta irányítópult több vizualizációt biztosít a flow naplóihoz:

1. Az időpontok közötti adatsorozat-grafikonok alapján, az adott időszakon belüli folyamatok számát ábrázoló döntési/irányi folyamatok. Szerkesztheti az időegységet és a két vizualizáció közötti időtartamot is. A folyamatokra vonatkozó döntés az engedélyezési vagy megtagadási döntések arányát jeleníti meg, míg a folyamatok iránya szerint a bejövő és a kimenő forgalom arányát jeleníti meg. Ezekkel a vizualizációkkal az idő múlásával ellenőrizheti a forgalmi trendeket, és megkeresheti az esetleges tüskéket vagy szokatlan mintákat.

   ![A képernyőképen egy minta-irányítópult látható, amelynek folyamata döntés és irány az idő múlásával.][2]

2. Folyamatok a cél/forrás port – kördiagramok – a megfelelő portokra irányuló folyamatok részletezését mutatják. Ebben a nézetben láthatja a leggyakrabban használt portokat. Ha a tortadiagramon belül egy adott portra kattint, az irányítópult többi része az adott porton belüli folyamatokra lesz szűrve.

   ![A képernyőképen egy minta irányítópult látható, amely a cél és a forrásport által forgalmazott folyamatokat jeleníti meg.][3]

3. Folyamatok száma és a legkorábbi naplózási idő – a metrikák a rögzített folyamatok számát és a legkorábbi napló rögzítésének dátumát mutatják.

   ![Képernyőfelvétel: egy minta-irányítópult, amely a folyamatok számát és a legkorábbi naplózási időt jeleníti meg.][4]

4. Folyamatok NSG és Rule szerint – az egyes NSG belüli folyamatok eloszlását, valamint az egyes NSG belüli szabályok eloszlását bemutató oszlopdiagram. Itt láthatja, hogy mely NSG és szabályok generálják a legtöbb forgalmat.

   ![Képernyőfelvétel: az N S G és a szabály által forgalmazott minta-irányítópult.][5]

5. Az első 10 forrás/cél IP-címek – sávdiagramok, amelyek az első 10 forrás-és cél IP-címeket mutatják. Ezeket a diagramokat beállíthatja úgy, hogy több vagy kevesebb felső IP-címet jelenítsen meg. Itt láthatja a leggyakrabban előforduló IP-címeket, valamint az egyes IP-címekre irányuló forgalomra vonatkozó döntést (Engedélyezés vagy megtagadás).

   ![Képernyőfelvétel: az első tíz forrás és a cél I P-címek által forgalmazott minta irányítópult.][6]

6. Flow rekordok – ez a táblázat az egyes folyamatokon belül található információkat, valamint a hozzá tartozó minősítések és szabályt tartalmazza.

   ![Képernyőfelvétel: egy tábla flow-rekordok.][7]

Az irányítópult tetején található lekérdezési sáv használatával a folyamatok bármely paramétere alapján szűrheti az irányítópultot, például az előfizetés AZONOSÍTÓját, az erőforráscsoportot, a szabályt vagy más érdekes változót. A Kibana lekérdezéseit és szűrőit a [hivatalos dokumentációban](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html) találja.

## <a name="conclusion"></a>Összegzés

A hálózati biztonsági csoport folyamatábráinak és a rugalmas verem kombinálásával a hálózati forgalom megjelenítésének hatékony és testreszabható módját is elérjük. Ezek az irányítópultok lehetővé teszik a hálózati forgalom gyors megszerzését és megosztását, valamint az esetleges anomáliák szűrését és kivizsgálását. Az Kibana használatával testreszabhatja ezeket az irányítópultokat, és létrehozhat bizonyos vizualizációkat, hogy azok megfeleljenek a biztonsági, auditálási és megfelelőségi igényeknek.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan jelenítheti meg a NSG-flow-naplókat a Power BI a [NSG flow-naplók megjelenítése a Power bi](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png