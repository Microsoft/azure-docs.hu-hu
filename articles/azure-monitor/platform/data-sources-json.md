---
title: Egyéni JSON-adatforrások gyűjtése a Linux rendszerhez készült Log Analytics-ügynökkel Azure Monitor
description: Az egyéni JSON-adatforrások begyűjthetők Azure Monitorba a Linux-alapú Log Analytics-ügynök használatával.  Ezek az egyéni adatforrások olyan egyszerű parancsfájlok lehetnek, mint például a curl, vagy az egyik a több mint 300 + beépülő modul. Ez a cikk az adatgyűjtés szükséges konfigurációját ismerteti.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa6931fc91838173a856ef500145fa49f2606271
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655203"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Egyéni JSON-adatforrások gyűjtése a Linux rendszerhez készült Log Analytics-ügynökkel Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Az egyéni JSON-adatforrások begyűjthetők [Azure monitorba](data-platform.md) a Linux-alapú log Analytics-ügynök használatával.  Ezek az egyéni adatforrások olyan egyszerű parancsfájlok lehetnek, mint például a [curl](https://curl.haxx.se/) , vagy az egyik a több mint [300 + beépülő](https://www.fluentd.org/plugins/all)modul. Ez a cikk az adatgyűjtés szükséges konfigurációját ismerteti.


> [!NOTE]
> A Linux v 1.1.0-217 + Log Analytics ügynöke az egyéni JSON-adatvédelemhez szükséges

## <a name="configuration"></a>Konfiguráció

### <a name="configure-input-plugin"></a>Bemeneti beépülő modul konfigurálása

A JSON-adatok Azure Monitorba való gyűjtéséhez vegyen fel egy, a `oms.api.` bemeneti beépülő modulban található, egy Fluent címkével rendelkező címke elejére.

Például a következő egy különálló konfigurációs fájl `exec-json.conf` a ben `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` .  Ez a folyékonyan futó beépülő modul használatával `exec` 30 másodpercenként futtat egy cURL-parancsot.  A parancs kimenetét a JSON kimeneti beépülő modulja gyűjti.

```xml
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

Az itt hozzáadott konfigurációs fájlhoz a `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` következő paranccsal kell megváltoztatnia a tulajdonosát:.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Kimeneti beépülő modul konfigurálása 
Adja hozzá a következő kimeneti beépülő modul konfigurációját a fő konfigurációhoz a `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` vagy egy külön konfigurációs fájlban `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```xml
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>A Linux-ügynök újraindítása Log Analytics
Indítsa újra a Linux rendszerhez készült Log Analytics Agent ügynököt a következő paranccsal.

```console
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="output"></a>Kimenet
Az adatokat a rendszer a Azure Monitor rögzíti `<FLUENTD_TAG>_CL` .

A Azure Monitor egyéni címkéje például a következő `tag oms.api.tomcat` : `tomcat_CL` .  Az ilyen típusú rekordokat a következő napló lekérdezéssel kérheti le.

```console
Type=tomcat_CL
```

A beágyazott JSON-adatforrások támogatottak, de a szülő mező alapján indexelve vannak. Például a következő JSON-adatok szerepelnek a napló lekérdezésében `tag_s : "[{ "a":"1", "b":"2" }]` .

```json
{
    "tag": [{
      "a":"1",
      "b":"2"
    }]
}
```


## <a name="next-steps"></a>Következő lépések
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../log-query/log-query-overview.md) . 
