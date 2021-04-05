---
title: Az Azure hálózati biztonsági csoport flow naplófájljainak elemzése – Graylog | Microsoft Docs
description: Megtudhatja, hogyan kezelheti és elemezheti a hálózati biztonsági csoportok flow-naplóit az Azure-ban Network Watcher és Graylog használatával.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 8b363d90d05e95b017c3a655b57dbabc3712a155
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965544"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Hálózati biztonsági csoport folyamatábráinak kezelése és elemzése az Azure-ban Network Watcher és Graylog használatával

A [hálózati biztonsági csoport folyamatábrája](network-watcher-nsg-flow-logging-overview.md) olyan információkat biztosít, amelyek segítségével megismerheti az Azure hálózati adapterek bejövő és kimenő IP-forgalmát. A folyamatábrá a kimenő és a bejövő folyamatokat jeleníti meg a hálózati biztonsági csoportokra vonatkozó szabályok alapján, a folyamat hálózati adaptere, 5 rekordos információ (forrás/cél IP-cím, forrás/célport, protokoll), valamint a forgalom engedélyezése vagy megtagadása.

A hálózatban számos hálózati biztonsági csoporttal rendelkezhet, amelyeken engedélyezve van a flow naplózása. Számos olyan hálózati biztonsági csoport, amelyen engedélyezve van a flow-naplózás, megnehezíti a naplók elemzését és betekintését. Ez a cikk megoldást nyújt a hálózati biztonsági csoport folyamatábráinak központi felügyeletére a Graylog, egy nyílt forráskódú napló-felügyeleti és-elemzési eszközzel, valamint a Logstash, amely egy nyílt forráskódú kiszolgálóoldali adatfeldolgozási folyamat.

> [!Warning]
> Az alábbi lépések a flow-naplók 1-es verziójával működnek. Részletekért lásd: a [hálózati biztonsági csoportok flow-naplózásának bemutatása](network-watcher-nsg-flow-logging-overview.md). A következő utasítások nem fognak működni a naplófájlok 2-es verziójával, módosítás nélkül.

## <a name="scenario"></a>Eset

A hálózati biztonsági csoport folyamatábrái engedélyezve vannak a Network Watcher használatával. A flow-naplók beáramlanak az Azure Blob Storage-ba. A Logstash beépülő modul a blob Storage-ból való kapcsolódáshoz és feldolgozáshoz, valamint a Graylog való elküldéséhez használható. A flow-naplók Graylog való tárolása után a rendszer elemezheti és testre szabhatja a testreszabott irányítópultok megjelenítését.

![Graylog munkafolyamat](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Telepítési lépések

### <a name="enable-network-security-group-flow-logging"></a>Hálózati biztonsági csoport adatfolyam-naplózásának engedélyezése

Ebben a forgatókönyvben a hálózati biztonsági csoport flow-naplózási szolgáltatásának engedélyezve kell lennie legalább egy hálózati biztonsági csoportban a fiókjában. A hálózati biztonsági csoport folyamatábráinak engedélyezésével kapcsolatos utasításokért tekintse meg az alábbi cikket a [hálózati biztonsági csoportok folyamatábrájának naplózása](network-watcher-nsg-flow-logging-overview.md)című cikkben.

### <a name="setting-up-graylog"></a>Graylog beállítása

Ebben a példában a Graylog és a Logstash is az Azure-ban üzembe helyezett Ubuntu 14,04-kiszolgálón van konfigurálva.

- Tekintse meg a Graylog [dokumentációját](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) , amely részletes útmutatást nyújt az Ubuntu rendszerre való telepítéshez.
- Győződjön meg arról, hogy a Graylog webes felületet is konfigurálja a [dokumentációt](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif)követve.

Ez a példa a minimális Graylog-telepítőt használja (azaz a Graylog egyetlen példánya), de a Graylog a rendszertől és a termelési igényektől függően méretezhető az erőforrások között. Az architektúrával kapcsolatos megfontolásokról vagy a mélyreható építészeti útmutatóról a Graylog [dokumentációjában](https://docs.graylog.org/en/2.2/pages/architecture.html) és [építészeti útmutatójában](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture)olvashat bővebben.

A Graylog több módon is telepíthető a platformtól és a beállításoktól függően. A lehetséges telepítési módszerek teljes listájáért tekintse meg a Graylog hivatalos [dokumentációját](https://docs.graylog.org/en/2.2/pages/installation.html). A Graylog Server-alkalmazás Linux-disztribúción fut, és a következő előfeltételekkel rendelkezik:

-  Java SE 8 vagy újabb – a [Azul Azure JDK dokumentációja](/azure/developer/java/fundamentals/java-jdk-long-term-support)
-  Rugalmas keresés 2. x (2.1.0 vagy újabb) – a [Elasticsearch telepítési dokumentációja](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2,4 vagy újabb – [MongoDB telepítési dokumentáció](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>A Logstash telepítése

A Logstash a JSON-formázott folyamat naplófájljainak a flow-rekord szintjére való lesimítására szolgál. A flow-naplók összeolvasztásával könnyebben rendszerezheti és keresheti a Graylog.

1. A Logstash telepítéséhez futtassa a következő parancsokat:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Konfigurálja a Logstash a flow-naplók elemzéséhez és a Graylog való elküldéséhez. Hozzon létre egy Logstash. conf fájlt:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Adja hozzá a következő tartalmat a fájlhoz. Módosítsa a Kiemelt értékeket a Storage-fiók részleteinek megfelelően:

   ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
   A megadott Logstash-konfigurációs fájl három részből áll: a bemenetből, a szűrőből és a kimenetből. A beviteli szakasz a Logstash által feldolgozható naplók bemeneti forrását jelöli. ebben az esetben egy Azure-beli blog bemeneti beépülő modulját fogja használni (a következő lépésekben telepítve), amely lehetővé teszi, hogy a blob Storage-ban tárolt hálózati biztonsági csoport folyamatábrá-fájljait elérhetővé tegyük.

A szűrő szakasz ezután összekapcsolja az egyes flow-naplófájlokat, hogy minden egyes flow-rekord és a hozzá tartozó tulajdonságok különálló Logstash-eseményvé válnak.

Végül a kimeneti szakasz továbbítja az egyes Logstash eseményeket a Graylog-kiszolgálónak. Az adott igényeknek megfelelően módosítsa a Logstash konfigurációs fájlját szükség szerint.

   > [!NOTE]
   > Az előző konfigurációs fájl azt feltételezi, hogy a Graylog-kiszolgáló konfigurálva van a helyi gazdagép visszacsatolási IP-címéhez. Ha nem, ügyeljen arra, hogy a kimeneti szakaszban lévő Host paramétert a megfelelő IP-címhez módosítsa.

A Logstash telepítésével kapcsolatos további információkért tekintse meg a Logstash [dokumentációját](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Az Azure Blob Storage Logstash-bemeneti beépülő moduljának telepítése

A Logstash beépülő modul lehetővé teszi, hogy közvetlenül hozzáférhessen a folyamat naplóihoz a kijelölt blob Storage-fiókból. A beépülő modul telepítéséhez az alapértelmezett Logstash telepítési könyvtárból (ebben az esetben a/usr/share/logstash/bin) futtassa a következő parancsot:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

A beépülő modullal kapcsolatos további információkért tekintse meg a [dokumentációt](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Logstash és Graylog közötti kapcsolatok beállítása

Most, hogy létrehozott egy kapcsolódást a folyamat naplóihoz a Logstash használatával, és beállítja a Graylog-kiszolgálót, konfigurálnia kell a Graylog-t a bejövő naplófájlok fogadásához.

1. Navigáljon a Graylog-kiszolgáló webes felületéhez a konfigurált URL-cím használatával. A felületet úgy érheti el, hogy a böngészőt a következőre irányítja: `http://<graylog-server-ip>:9000/`

2. A konfiguráció lapra való navigáláshoz válassza a jobb oldali navigációs sáv **rendszer** legördülő menüjét, majd kattintson a **bemenetek** elemre.
   Másik lehetőségként navigáljon a `http://<graylog-server-ip>:9000/system/inputs`

   ![Első lépések](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Az új bemenet elindításához válassza a *GELF UDP* elemet a **bemenet kiválasztása** legördülő menüben, majd töltse ki az űrlapot. A GELF a Graylog kiterjesztett naplózási formátumát jelenti. A GELF formátumát a Graylog fejlesztette ki. Ha többet szeretne megtudni az előnyeiről, tekintse meg a Graylog [dokumentációját](https://docs.graylog.org/en/2.2/pages/gelf.html).

   Győződjön meg arról, hogy a bemenetet a Graylog-kiszolgálót konfigurált IP-címhez köti. Az IP-címnek egyeznie kell a Logstash konfigurációs fájljának UDP-kimenetének **gazdagép** mezőjével. Az alapértelmezett port a *12201*. Győződjön meg arról, hogy a port megfelel a Logstash konfigurációs fájlban kijelölt UDP-kimenet **port** mezőjének.

   ![Képernyőfelvétel: a Graylog bemenetei, valamint a bemenetek indítására és keresésére szolgáló beállítások.](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Miután elindította a bemenetet, megjelenik a **helyi bemenetek** szakaszban, az alábbi képen látható módon:

   ![Képernyőkép, amely az elindított bemenetet tartalmazó helyi bemenetek szakaszt mutatja.](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Az Graylog kapcsolatos további tudnivalókért tekintse meg a [dokumentációt](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. A konfigurációk megkezdése után elindíthatja a Logstash a flow-naplók olvasásának megkezdéséhez a következő paranccsal: `sudo systemctl start logstash.service` .

### <a name="search-through-graylog-messages"></a>Keresés Graylog-üzenetekben

Miután engedélyezte a Graylog-kiszolgáló számára, hogy üzeneteket gyűjtsön, megkeresheti az üzeneteket. A Graylog-kiszolgálónak küldendő üzenetek megtekintéséhez a **bemenetek** konfigurálása lapon kattintson a létrehozott GELF UDP-bemenet "**fogadott üzenetek megjelenítése**" gombjára. A következő képhez hasonló képernyőre van irányítva: 

![Képernyőfelvétel: a Graylog-kiszolgáló, amelyen a keresési eredmények, a hisztogram és az üzenetek láthatók.](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Ha a kék "% {Message}" hivatkozásra kattint, a rendszer kibontja az egyes üzeneteket, hogy megjelenítse az egyes folyamatábrák paramétereit, ahogy az a következő képen látható:

![A képernyőképen a Graylog-kiszolgálóról származó üzenet részletei láthatók.](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Alapértelmezés szerint az összes üzenet mező szerepel a keresésben, ha nem jelöl ki egy adott üzenetet a kereséshez. Ha konkrét üzenetekre szeretne rákeresni (például – a flow rekordok egy adott forrás IP-címről, a Graylog keresési lekérdezési nyelvét [dokumentálva](https://docs.graylog.org/en/2.2/pages/queries.html) használhatja

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Hálózati biztonsági csoport folyamatábráinak elemzése a Graylog használatával

Most, hogy Graylog-t állított be, használhatja a funkcióit, hogy jobban megértse a folyamat naplójának adatait. Az egyik ilyen módszer az irányítópultok használata az adatnézetek konkrét nézeteinek létrehozásához.

### <a name="create-a-dashboard"></a>Irányítópult létrehozása

1. A felső navigációs sávon válassza az **irányítópultok** lehetőséget, vagy navigáljon a következőre: `http://<graylog-server-ip>:9000/dashboards/`

2. Innen kattintson a zöld **irányítópult létrehozása** gombra, és töltse ki a rövid űrlapot az irányítópult címével és leírásával. Kattintson a **Save (Mentés** ) gombra az új irányítópult létrehozásához. A következő képhez hasonló irányítópult jelenik meg:

    ![Képernyőfelvétel: Graylog-kiszolgáló irányítópultok, az irányítópultok létrehozásához és szerkesztéséhez szükséges beállításokkal.](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Widgetek hozzáadása

Az irányítópult címére kattintva megtekintheti, de most már üres is lehet, mivel nem adott hozzá widgeteket. Az irányítópulthoz hozzáadandó egyszerű és hasznos típusú widgetek **gyors értékek** diagramokat tartalmaznak, amelyek a kiválasztott mező értékeinek listáját és azok eloszlását jelenítik meg.

1. Váltson vissza a flow-naplókat fogadó UDP-bemenet keresési eredményeire úgy, hogy kiválasztja a **Keresés** lehetőséget a felső navigációs sávon.

2. A **keresési eredmények** panelen a képernyő bal oldalán keresse meg a **mezők** lapot, amely felsorolja az egyes bejövő adatfolyam-üzenetek különböző mezőit.

3. Válassza ki a megjeleníteni kívánt paramétereket (ebben a példában az IP-forrás van kiválasztva). A lehetséges widgetek listájának megjelenítéséhez kattintson a mező bal oldalán található kék legördülő nyílra, majd válassza a **gyors értékek** elemet a widget létrehozásához. A következő képhez hasonlóan kell megjelennie:

   ![Forrás IP-címe](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. Innen kiválaszthatja a Hozzáadás az **irányítópulthoz** gombot a widget jobb felső sarkában, és kiválaszthatja a hozzá tartozó irányítópultot.

5. Váltson vissza az irányítópultra, és tekintse meg az imént hozzáadott widgetet.

   Számos más widgetet (például hisztogramokat) adhat hozzá az irányítópulthoz, így nyomon követheti a fontos mérőszámokat, például a következő képen látható minta irányítópultot:

   ![Flowlogs-irányítópult](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Az irányítópultokkal és más típusú widgetekkel kapcsolatos további magyarázatért tekintse meg a Graylog [dokumentációját](https://docs.graylog.org/en/2.2/pages/dashboards.html).

A Network Watcher és a Graylog integrálásával mostantól kényelmes és központosított módon kezelheti és jelenítheti meg a hálózati biztonsági csoport folyamatábráit. A Graylog számos más hatékony funkcióval rendelkezik, mint például a streamek és a riasztások, amelyek a flow-naplók további kezeléséhez és a hálózati forgalom jobb megismeréséhez is használhatók. Most, hogy már beállította a Graylog, és csatlakozott az Azure-hoz, nyugodtan folytassa az általa kínált egyéb funkciók megismerésével.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan jelenítheti meg a hálózati biztonsági csoport folyamatának naplóit Power BI a [hálózati biztonsági csoport adatforgalmának naplófájljainak Power bi](network-watcher-visualize-nsg-flow-logs-power-bi.md)használatával történő megjelenítésével.