---
title: Az ElasticSearch telepítése egy fejlesztési virtuális gépre Azure-ban
description: Oktatóanyag – Az Elastic Stack telepítése egy fejlesztési célú linuxos virtuális gépre az Azure-ban
services: virtual-machines
documentationcenter: virtual-machines
author: rloutlaw
manager: justhe
tags: azure-resource-manager
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: b3213d7de9aee88486fa1db1388c51948fbde430
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557811"
---
# <a name="install-the-elastic-stack-on-an-azure-vm"></a>Az Elastic Stack telepítése egy Azure-beli virtuális gépen

Ez a cikk ismerteti az [Elasticsearch](https://www.elastic.co/products/elasticsearch), a [Logstash](https://www.elastic.co/products/logstash) és a [Kibana](https://www.elastic.co/products/kibana) egy Ubuntu rendszerű virtuális gépre történő telepítését az Azure-ban. Ha szeretné működés közben megtekinteni az Elastic Stacket, lehetősége van csatlakozni a Kibanához, és használhatja a mintául szolgáló naplózási adatokat. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Ubuntus virtuális gép létrehozása egy Azure-erőforráscsoportban
> * Az Elasticsearch, a Logstash és a Kibana telepítése a virtuális gépre
> * Mintaadatok elküldése az Elasticsearch számára a Logstash használatával 
> * Portok megnyitása és adatok használata a Kibana-konzolon


 Az üzemelő példány alkalmas az Elastic Stackkel végzett alapszintű fejlesztésre. További információt az Elastic Stackről – beleértve az éles környezetre vonatkozó javaslatokat – az [Elastic dokumentációjában](https://www.elastic.co/guide/index.html) és az [Azure Architecture Centerben](/azure/architecture/elasticsearch/) talál.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.4 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. 

Az alábbi példa egy *myVM* nevű virtuális gépet és SSH-kulcsokat hoz létre, ha azok még nem léteznek a kulcsok alapméretezett helyén. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. Jegyezze fel a `publicIpAddress` értékét. Ez a cím használható a virtuális gép eléréséhez.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="ssh-into-your-vm"></a>Bejelentkezés a virtuális gépre SSH-val

Ha még nem ismeri a virtuális gép nyilvános IP-címét, futtassa az az [Network Public-IP List](/cli/azure/network/public-ip) parancsot:

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Helyettesítse be a virtuális gépe tényleges nyilvános IP-címét. Ebben a példában az IP-cím a következő: *40.68.254.142*.

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>Az Elastic Stack telepítése

Importálja az Elasticsearch-aláírókulcsot, és frissítse az APT-források listáját, hogy szerepeljen benne az Elastic csomagtára:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

Telepítse a Java Virtualt a virtuális gépen, és konfigurálja a JAVA_HOME változót – ez az Elastic Stack-összetevők futtatásához szükséges.

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

Futtassa az alábbi parancsokat az ubuntus csomagforrások frissítéséhez, és az Elasticsearch, a Kibana és a Logstash telepítéséhez.

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> A részletes telepítési utasításokat, beleértve a mappastruktúrát és a kezdeti konfigurációt az [Elastic dokumentációjában](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html) találja.

## <a name="start-elasticsearch"></a>Az Elasticsearch indítása 

Az Elasticsearch a virtuális gépen való indításához használja az alábbi parancsot:

```bash
sudo systemctl start elasticsearch.service
```

Ez a parancs nem hoz létre kimenetet, ezért ellenőrizze ezzel a `curl`-paranccsal, hogy az Elasticsearch fut-e a virtuális gépen:

```bash
sudo curl -XGET 'localhost:9200/'
```

Ha az Elasticsearch fut, az alábbihoz hasonló kimenet jelenik meg:

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>A Logstash indítása és adatok hozzáadása az Elasticsearchhöz

Indítsa el a Logstasht a következő paranccsal:

```bash 
sudo systemctl start logstash.service
```

Tesztelje a Logstasht interaktív módban, hogy meggyőződhessen a helyes működéséről:

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

Ez egy alapszintű logstash-[folyamat](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html), amely a standard bemenetet egy standard kimenetbe adja vissza. 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

Állítsa be a Logstasht úgy, hogy továbbítsa a kernelüzeneteket erről a virtuális gépről az Elasticsearchre. Hozzon létre egy új fájlt egy üres, `vm-syslog-logstash.conf` nevű könyvtárban, és illessze be az alábbi Logstash-konfigurációt:

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

Tesztelje ezt a konfigurációt, és küldje a rendszernaplóadatokat az Elasticsearchbe:

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

A rendszernapló-bejegyzések a terminálon úgy jelennek meg, ahogy a rendszer elküldte őket az Elasticsearchbe. Az adatok elküldése után lépjen ki a Logstashből a `CTRL+C` használatával.

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>A Kibana indítása és az adatok megjelenítése az Elasticsearchben

Szerkessze az `/etc/kibana/kibana.yml` fájlt, és módosítsa a Kibana által figyelt IP-címet, hogy hozzá tudjon férni a böngészőből.

```bash
server.host: "0.0.0.0"
```

Indítsa el a Kibanát a következő paranccsal:

```bash
sudo systemctl start kibana.service
```

Nyissa meg az 5601-es portot az Azure CLI-ről, hogy távolról is hozzá lehessen férni a Kibana-konzolhoz:

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

Nyissa meg a Kibana-konzolt, és a **Create** (Létrehozás) elemet választva hozzon létre egy alapértelmezett indexet az Elasticsearchbe korábban elküldött rendszernaplóadatok alapján. 

![Képernyőkép, amely a Kibana-konzolt mutatja, és kiemeli a létrehozás gombot.](media/elasticsearch-install/kibana-index.png)

A Kibana-konzolon a **Discover** (Felderítés) elemet választva kereshet és tallózhat a rendszernapló-események között, és szűrheti is őket.

![Rendszernapló-események tallózása a Kibanában](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban telepítette az Elastic Stacket egy fejlesztési célú virtuális gépre az Azure-ban. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Ubuntus virtuális gép létrehozása egy Azure-erőforráscsoportban
> * Az Elasticsearch, a Logstash és a Kibana telepítése a virtuális gépre
> * Mintaadatok elküldése az Elasticsearch számára a Logstashből 
> * Portok megnyitása és adatok használata a Kibana-konzolon
