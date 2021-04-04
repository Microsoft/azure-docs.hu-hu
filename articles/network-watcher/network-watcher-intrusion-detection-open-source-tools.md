---
title: Hálózati behatolás-észlelés végrehajtása nyílt forráskódú eszközökkel
titleSuffix: Azure Network Watcher
description: Ez a cikk azt ismerteti, hogyan használható az Azure Network Watcher és a nyílt forráskódú eszközök a hálózati behatolás észleléséhez
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: b4b38112e32a55739ea14d5ff06e327819171557
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98014552"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Hálózati behatolás-észlelés végrehajtása Network Watcher és nyílt forráskódú eszközökkel

A csomagok rögzítése kulcsfontosságú összetevő a hálózati behatolás-észlelési rendszerek (AZONOSÍTÓk) megvalósításához és a hálózati biztonsági monitorozás (NSM) végrehajtásához. Több nyílt forráskódú azonosító eszköz is feldolgozza a csomagok rögzítését, és megkeresi a lehetséges hálózati behatolások és rosszindulatú tevékenységek aláírásait. A Network Watcher által biztosított csomagok rögzítései segítségével elemezheti a hálózatot az ártalmas behatolások vagy sebezhetőségek tekintetében.

Az egyik ilyen nyílt forráskódú eszköz a Suricata, egy szabályrendszerek használó azonosító motor, amely figyeli a hálózati forgalmat, és riasztást küld, amikor gyanús események történnek. A Suricata többszálas motort kínál, ami azt jelenti, hogy a hálózati forgalom elemzése nagyobb sebességgel és hatékonysággal végezhető. A Suricata és képességeivel kapcsolatos további információkért látogasson el a webhelyére a webhelyén https://suricata-ids.org/ .

## <a name="scenario"></a>Eset

Ez a cikk azt ismerteti, hogyan állíthatja be a környezetet hálózati behatolás-észlelés végrehajtásához Network Watcher, Suricata és a rugalmas verem használatával. Network Watcher biztosítja a hálózati behatolás-észlelés végrehajtásához használt csomagok rögzítését. A Suricata feldolgozza a csomagot, és elindítja a riasztásokat az adott fenyegetéseknek megfelelő csomagok alapján. Ezeket a riasztásokat a helyi számítógépen található naplófájlban tárolja a rendszer. A rugalmas verem használatával a Suricata által generált naplók indexelve lehetnek, és felhasználhatók egy Kibana-irányítópult létrehozására, amely lehetővé teszi a naplók vizuális megjelenítését, valamint az esetleges hálózati biztonsági rések gyors elemzését.  

![egyszerű webalkalmazási forgatókönyv][1]

A nyílt forráskódú eszközöket egy Azure-beli virtuális gépen is beállíthatja, így az elemzést saját Azure-beli hálózati környezetében hajthatja végre.

## <a name="steps"></a>Lépések

### <a name="install-suricata"></a>A Suricata telepítése

Az egyéb telepítési módszerekért látogasson el a következő webhelyre: https://suricata.readthedocs.io/en/suricata-5.0.2/quickstart.html#installation

1. A virtuális gép parancssori terminálján futtassa a következő parancsokat:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. A telepítés ellenőrzéséhez futtassa a parancsot a `suricata -h` parancsok teljes listájának megtekintéséhez.

### <a name="download-the-emerging-threats-ruleset"></a>A feltörekvő fenyegetések alapszabályának letöltése

Ebben a szakaszban nem rendelkezünk a Suricata futtatására vonatkozó szabályokkal. Létrehozhatja saját szabályait, ha vannak olyan veszélyforrások a hálózaton, amelyeket észlelni szeretne, vagy több szolgáltatóból is használhat kifejlesztett szabálykészlet, például a felmerülő fenyegetések vagy a VRT szabályok. A szabadon hozzáférhető, újonnan elérhető fenyegetésekkel kapcsolatos szabályokat itt találja:

Töltse le a szabályt, és másolja őket a könyvtárba:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>A Suricata-mel dolgozza fel a csomagok rögzítését

Ha a Suricata használatával szeretné feldolgozni a csomagok rögzítését, futtassa a következő parancsot:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Az eredményül kapott riasztások vizsgálatához olvassa el a Fast. log fájlt:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>A rugalmas verem beállítása

Míg a Suricata által előállított naplók értékes információkat tartalmaznak arról, hogy mi történik a hálózaton, ezek a naplófájlok nem a legkönnyebb olvasni és értelmezni. A Suricata és a rugalmas verem összekapcsolásával létrehozhatunk egy Kibana-irányítópultot, amely lehetővé teszi számunkra, hogy megkeresse, megrajzolja, elemezze és származtatja a naplókat.

#### <a name="install-elasticsearch"></a>A Elasticsearch telepítése

1. A 5,0-es és újabb verziókhoz tartozó rugalmas verem Java 8-at igényel. Futtassa a parancsot a `java -version` verziójának vizsgálatához. Ha nincs telepítve a Java, tekintse meg az [Azure-suppored JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support)dokumentációját.

1. Töltse le a rendszerének megfelelő bináris csomagot:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    További telepítési módszerek találhatók a Elasticsearch- [telepítésben](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Ellenőrizze, hogy a Elasticsearch fut-e a paranccsal:

    ```
    curl http://127.0.0.1:9200
    ```

    Ehhez a következőhöz hasonló válasznak kell megjelennie:

    ```
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

A rugalmas keresés telepítésével kapcsolatos további információkért tekintse meg az oldal [telepítését](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html) ismertető témakört.

### <a name="install-logstash"></a>A Logstash telepítése

1. A Logstash telepítéséhez futtassa a következő parancsokat:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Ezután be kell állítania a Logstash, hogy beolvassa a fájl eve.jskimenetét. Hozzon létre egy logstash. conf fájlt a használatával:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Adja hozzá a következő tartalmat a fájlhoz (Győződjön meg arról, hogy a fájl eve.jselérési útja helyes):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Ügyeljen arra, hogy a fájlhoz megfelelő engedélyeket adjon a eve.jsnak, hogy a Logstash képes legyen a fájl betöltésére.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. A Logstash elindításához futtassa a következő parancsot:

    ```
    sudo /etc/init.d/logstash start
    ```

A Logstash telepítésével kapcsolatos további információkért tekintse meg a [hivatalos dokumentációt](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html) .

### <a name="install-kibana"></a>A Kibana telepítése

1. Futtassa a következő parancsokat a Kibana telepítéséhez:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. A Kibana futtatásához használja a következő parancsokat:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. A Kibana webes felületének megtekintéséhez navigáljon a `http://localhost:5601`
1. Ebben a forgatókönyvben a Suricata naplókhoz használt index minta a következő: "logstash-*"

1. Ha távolról szeretné megtekinteni a Kibana-irányítópultot, hozzon létre egy bejövő NSG-szabályt, amely engedélyezi a hozzáférést a 5601-es **porthoz**.

### <a name="create-a-kibana-dashboard"></a>Kibana-irányítópult létrehozása

Ebben a cikkben egy minta-irányítópultot kaptunk a riasztások trendjeinek és részleteinek megtekintéséhez.

1. Töltse [le itt az irányítópult-fájlt,](https://aka.ms/networkwatchersuricatadashboard)a vizualizációs fájlt [itt, és](https://aka.ms/networkwatchersuricatavisualization)a mentett keresési fájlt [itt](https://aka.ms/networkwatchersuricatasavedsearch).

1. A Kibana **felügyelet** lapján navigáljon a **mentett objektumokhoz** , és importálja mindhárom fájlt. Ezután az **irányítópult** lapon megnyithatja és betöltheti a minta irányítópultot.

Saját igényei szerint saját vizualizációkat és irányítópultokat is létrehozhat. További információ a Kibana-vizualizációk létrehozásáról a Kibana [hivatalos dokumentációjában](https://www.tutorialspoint.com/kibana/kibana_create_visualization.htm)olvasható.

![kibana-irányítópult][2]

### <a name="visualize-ids-alert-logs"></a>AZONOSÍTÓk riasztási naplófájljainak megjelenítése

A minta irányítópult több vizualizációt biztosít a Suricata riasztási naplóihoz:

1. Riasztások GeoIP szerint – Térkép: a riasztások eloszlása az országuk vagy régiójuk szerint, földrajzi hely alapján (IP alapján meghatározva)

    ![földrajzi IP-cím][3]

1. Az első 10 riasztás – a 10 leggyakoribb aktivált riasztás és azok leírásának összefoglalása. Ha az adott riasztásra kattint, az irányítópulton az adott riasztásra vonatkozó információk szerepelnek.

    ![4. rendszerkép][4]

1. Riasztások száma – a szabályrendszert által aktivált riasztások teljes száma

    ![5. rendszerkép][5]

1. Az első 20 forrás/cél IP-címek/portok – a legfontosabb 20 IP-címeket és a riasztásokat kiváltó portokat bemutató kördiagramok. Az egyes IP-címekre/portokra szűkítheti a szűrést, hogy megtudja, hány és milyen típusú riasztást indít el a rendszer.

    ![6. ábra][6]

1. Riasztások összegzése – az egyes riasztások konkrét részleteit Összefoglaló táblázat. A táblázat testreszabható úgy, hogy az egyes riasztásokhoz kapcsolódó egyéb paramétereket is megjelenítse.

    ![7. rendszerkép][7]

Az egyéni vizualizációk és irányítópultok létrehozásával kapcsolatos további információkért tekintse [meg a Kibana hivatalos dokumentációját](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Összegzés

A Network Watcher és a nyílt forráskódú azonosító eszközök, például a Suricata által biztosított csomagok rögzítésének kombinálásával a fenyegetések széles köre miatt hálózati behatolás-észlelést is végezhet. Ezek az irányítópultok lehetővé teszik a hálózaton belüli trendek és anomáliák gyors észlelését, valamint az adatgyűjtést a riasztások, például rosszindulatú felhasználói ügynökök vagy sebezhető portok kiváltó okainak felderítésére. Ezzel a kinyert adatokkal tájékozott döntéseket hozhat arról, hogyan reagálhat a hálózatra, és hogyan védhető meg a kártékony behatolási kísérletek, és hogyan hozhat létre szabályokat a hálózatra irányuló jövőbeli behatolások megelőzése érdekében.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan válthat ki a riasztások alapján a csomagok rögzítése a következővel: [proaktív hálózati figyelés a Azure functions](network-watcher-alert-triggered-packet-capture.md)

Ismerje meg, hogyan jelenítheti meg a NSG-flow-naplókat a Power BI a [NSG flow-naplók megjelenítése a Power bi](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png