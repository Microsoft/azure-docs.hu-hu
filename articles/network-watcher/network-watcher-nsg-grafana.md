---
title: NSG-folyamatok naplófájljainak kezelése a Grafana használatával
titleSuffix: Azure Network Watcher
description: Hálózati biztonsági csoport Folyamatábráinak kezelése és elemzése az Azure-ban Network Watcher és Grafana használatával.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: d522d305c70214009b8aa2886d07d2d5403dd2b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656308"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Hálózati biztonsági csoport folyamatábráinak kezelése és elemzése Network Watcher és Grafana használatával

A [hálózati biztonsági csoport (NSG) folyamatábrája](network-watcher-nsg-flow-logging-overview.md) olyan információkat biztosít, amelyek segítségével megismerheti a bejövő és kimenő IP-forgalmat a hálózati adaptereken. Ezek a flow-naplók a kimenő és bejövő folyamatokat jelenítik meg NSG, a hálózati adapter a folyamatra vonatkozik, 5 rekordos információ a folyamatról (forrás/cél IP-cím, forrás/célport, protokoll), és ha a forgalom engedélyezett vagy tiltott.

A hálózatban számos NSG engedélyezheti a flow naplózását. A naplózási adatok mennyisége nehézkesen elemezheti és betekintést nyerhet a naplókba. Ez a cikk megoldást nyújt ezen NSG-naplók központi felügyeletére a Grafana, egy nyílt forráskódú gráf-kezelő eszköz, egy ElasticSearch, egy elosztott keresési és elemzési motor, valamint a Logstash, amely egy nyílt forráskódú kiszolgálóoldali adatfeldolgozási folyamat.  

## <a name="scenario"></a>Eset

A NSG-flow naplói engedélyezve vannak a Network Watcher használatával, és az Azure Blob Storage-ban tárolódnak. A Logstash beépülő modul a blob Storage-ból való kapcsolódáshoz és feldolgozáshoz, valamint a ElasticSearch való elküldéséhez használható.  A flow-naplók ElasticSearch való tárolása után a rendszer elemezheti és testre szabhatja a Grafana testreszabott irányítópultján.

![NSG Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>A telepítés lépései

### <a name="enable-network-security-group-flow-logging"></a>Hálózati biztonsági csoport adatfolyam-naplózásának engedélyezése

Ebben a forgatókönyvben a hálózati biztonsági csoport flow-naplózási szolgáltatásának engedélyezve kell lennie legalább egy hálózati biztonsági csoportban a fiókjában. A hálózati biztonsági folyamatok naplófájljainak engedélyezésével kapcsolatos utasításokért tekintse meg az alábbi cikket a [hálózati biztonsági csoportok folyamatábrájának naplózása](network-watcher-nsg-flow-logging-overview.md)című cikkben.

### <a name="setup-considerations"></a>Beállítási szempontok

Ebben a példában a Grafana, a ElasticSearch és a Logstash az Azure-ban üzembe helyezett Ubuntu 16,04 LTS-kiszolgálón vannak konfigurálva. Ez a minimális beállítás mind a három összetevő futtatására használatos – ezek mind ugyanazon a virtuális gépen futnak. Ez a beállítás csak tesztelési és nem kritikus fontosságú számítási feladatokhoz használható. A Logstash, a Elasticsearch és a Grafana mind a különböző példányok egymástól független skálázására használható. További információkért tekintse meg az egyes összetevők dokumentációját.

### <a name="install-logstash"></a>A Logstash telepítése

A Logstash használatával lelapulhatja a JSON formátumú adatfolyam-naplókat a flow-rekord szintjére.

1. A Logstash telepítéséhez futtassa a következő parancsokat:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Konfigurálja a Logstash a flow-naplók elemzéséhez és a ElasticSearch való elküldéséhez. Hozzon létre egy Logstash. conf fájlt a használatával:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adja hozzá a következő tartalmat a fájlhoz. Módosítsa a Storage-fiók nevét és a hozzáférési kulcsot, hogy az tükrözze a Storage-fiók adatait:

   ```bash
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

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
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
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
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

A megadott Logstash-konfigurációs fájl három részből áll: a bemenetből, a szűrőből és a kimenetből.
A beviteli szakasz a Logstash által feldolgozható naplók bemeneti forrását jelöli. ebben az esetben a "azureblob" beviteli beépülő modult fogjuk használni (a következő lépésekben telepítve), amely lehetővé teszi, hogy hozzáférhessen a blob Storage-ban tárolt NSG log JSON-fájlokhoz. 

A szűrő szakasz ezután összekapcsolja az egyes flow-naplófájlokat, hogy minden egyes flow-rekord és a hozzá tartozó tulajdonságok különálló Logstash-eseményvé válnak.

Végül a kimeneti szakasz továbbítja az egyes Logstash eseményeket a ElasticSearch-kiszolgálónak. Nyugodtan módosíthatja a Logstash konfigurációs fájlját, hogy az megfeleljen az adott igényeknek.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>A Logstash bemeneti beépülő moduljának telepítése az Azure Blob Storage-hoz

Ez a Logstash beépülő modul lehetővé teszi, hogy közvetlenül hozzáférhessen a folyamat naplófájljaihoz a kijelölt blob Storage-fiókból. Ha ezt a beépülő modult szeretné telepíteni, az alapértelmezett Logstash telepítési könyvtárból (ebben az esetben a/usr/share/logstash/bin) futtassa a következő parancsot:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

További információ erről a beépülő modulról: [Logstash bemeneti beépülő modul az Azure Storage-blobokhoz](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>A ElasticSearch telepítése

A ElasticSearch telepítéséhez a következő parancsfájlt használhatja. További információ a ElasticSearch telepítéséről: [rugalmas verem](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>A Grafana telepítése

A Grafana telepítéséhez és futtatásához futtassa a következő parancsokat:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

További telepítési információk: [telepítés Debian/Ubuntu rendszeren](https://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>A ElasticSearch-kiszolgáló hozzáadása adatforrásként

Ezután hozzá kell adnia a flow-naplókat tartalmazó ElasticSearch-indexet adatforrásként. Az adatforrások hozzáadásához válassza az **adatforrás hozzáadása** lehetőséget, és töltse ki az űrlapot a megfelelő információkkal. Ennek a konfigurációnak a mintája a következő képernyőképen található:

![Adatforrás hozzáadása](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Irányítópult létrehozása

Most, hogy sikeresen konfigurálta a Grafana, hogy beolvassa a NSG flow-naplókat tartalmazó ElasticSearch-indexet, létrehozhat és személyre szabhatja az irányítópultokat. Új irányítópult létrehozásához válassza **az első irányítópult létrehozása** lehetőséget. A következő gráf-konfiguráció a NSG-szabály szerint szegmentált folyamatokat jeleníti meg:

![Irányítópult-gráf](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Az alábbi képernyőképen egy gráf és egy diagram látható, amely a felső folyamatokat és azok gyakoriságát mutatja. A folyamatokat a NSG-szabályok és a folyamatok döntés szerint is mutatják. A Grafana kifejezetten testreszabható, ezért javasoljuk, hogy irányítópultokat hozzon létre az adott figyelési igényeknek megfelelően. Az alábbi példa egy tipikus irányítópultot mutat be:

![Képernyőkép, amely a NSG-szabály által szegmentált folyamatokat ábrázoló minta gráf-konfigurációt jeleníti meg.](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Összegzés

A Network Watcher ElasticSearch és Grafana való integrálásával mostantól kényelmes és központosított módon kezelheti és jelenítheti meg a NSG, valamint más-más adatmennyiségeket is. A Grafana számos más nagy teljesítményű gráf-funkcióval rendelkezik, amelyekkel a folyamatok további kezeléséhez és a hálózati forgalom jobb megismeréséhez is használható. Most, hogy már beállította és csatlakoztatta az Azure-hoz egy Grafana-példányt, nyugodtan folytathatja az általa kínált egyéb funkciók megismerését.

## <a name="next-steps"></a>Következő lépések

- További információ a [Network Watcher](network-watcher-monitoring-overview.md)használatáról.

