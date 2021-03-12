---
title: Beépített mérőszámok elérése – Azure IoT Edge
description: A beépített metrikák távelérése a IoT Edge Runtime-összetevőkből
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 10/05/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1a78db821c0fab01ad5d6752216a8f7682fb2c46
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200496"
---
# <a name="access-built-in-metrics"></a>Beépített mérőszámok elérése

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

A IoT Edge futtatókörnyezet-összetevők, a IoT Edge hub és a IoT Edge-ügynök beépített mérőszámokat hoz létre a [Prometheus kiállítási formátumában](https://prometheus.io/docs/instrumenting/exposition_formats/). A metrikák távolról való elérése egy IoT Edge eszköz állapotának figyelésére és megismerésére.

A kiadási 1.0.10 a metrikák alapértelmezés szerint automatikusan ki vannak téve a **edgeHub** és a **edgeAgent** -modulok (és) 9600-es **portján** `http://edgeHub:9600/metrics` `http://edgeAgent:9600/metrics` . Ezek a portok alapértelmezés szerint nincsenek leképezve a gazdagéphez.

A metrikák elérését a gazdagépről a mérőszámok portjának kiírásával és hozzárendelésével érheti el `createOptions` . Az alábbi példa az alapértelmezett metrikai portot az 9601-as portra képezi le a gazdagépen:

```
{
  "ExposedPorts": {
    "9600/tcp": {}
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

Ha a edgeHub és a edgeAgent metrikáinak végpontját is szeretné leképezni, válassza a különböző és egyedi gazdagépek portszámait.

> [!NOTE]
> A környezeti változó `httpSettings__enabled` nem állítható be úgy, hogy a `false` beépített metrikák elérhetők legyenek a gyűjtemény számára.
>
> A metrikák letiltására használható környezeti változók az [Azure/iotedge adattár doc](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md)-ban találhatók.

## <a name="available-metrics"></a>Rendelkezésre álló metrikák

A metrikák olyan címkéket tartalmaznak, amelyek segítenek azonosítani a begyűjtött metrika természetét. Az összes metrika a következő címkéket tartalmazza:

| Címke | Leírás |
|-|-|
| iothub | Az az eszköz, amelyre az eszközt beszél |
| edge_device | Az aktuális eszköz azonosítója |
| instance_number | Az aktuális futtatókörnyezetet jelképező GUID. Újraindításkor a rendszer az összes metrikát alaphelyzetbe állítja. Ez a GUID megkönnyíti az újraindítások egyeztetését. |

A Prometheus kiállítási formátumában négy fő metrikai típus létezik: számláló, mérőműszer, hisztogram és összefoglalás. A különböző metrikai típusokkal kapcsolatos további információkért lásd a [Prometheus metrikus típusai dokumentációját](https://prometheus.io/docs/concepts/metric_types/).

A beépített hisztogramhoz és az összegző metrikához megadott quantiles 0,1, 0,5, 0,9 és 0,99.

A **edgeHub** modul a következő metrikákat állítja elő:

| Name | Dimenziók | Leírás |
|-|-|-|
| `edgehub_gettwin_total` | `source` (műveleti forrás)<br> `id` (modul azonosítója) | Típus: számláló<br> GetTwin-hívások száma összesen |
| `edgehub_messages_received_total` | `route_output` (az üzenetet küldő kimenet)<br> `id` | Típus: számláló<br> Az ügyfelektől fogadott üzenetek teljes száma |
| `edgehub_messages_sent_total` | `from` (üzenet forrása)<br> `to` (üzenet célhelye)<br>`from_route_output`<br> `to_route_input` (üzenet célhelyének bemenete)<br> `priority` (az üzenet prioritása a célhelynek) | Típus: számláló<br> Az ügyfeleknek vagy felsőbb rétegeknek küldött üzenetek teljes száma<br> `to_route_input` üres, ha `to` $upstream |
| `edgehub_reported_properties_total` | `target`(frissítési cél)<br> `id` | Típus: számláló<br> Jelentett tulajdonságok összes frissítési hívása |
| `edgehub_message_size_bytes` | `id`<br> | Típus: összefoglalás<br> Üzenetek mérete az ügyfelektől<br> Az értékek úgy jelenhetnek `NaN` meg, mintha egy adott időszakra vonatkozóan nem számoltak be új mérések (jelenleg 10 perc) `summary` . a típushoz a megfelelő `_count` és a `_sum` számlálók lesznek kibocsátva. |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | Típus: összefoglalás<br> A kettős műveletekhez szükséges idő |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | Típus: összefoglalás<br> Üzenet küldéséhez szükséges idő |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | Típus: összefoglalás<br> A várólista üzenetének feldolgozásához szükséges idő |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | Típus: összefoglalás<br> A jelentett tulajdonságok frissítéséhez szükséges idő |
| `edgehub_direct_method_duration_seconds` | `from` hívó<br> `to` fogadó | Típus: összefoglalás<br> Közvetlen üzenet feloldásához szükséges idő |
| `edgehub_direct_methods_total` | `from`<br> `to` | Típus: számláló<br> A küldött közvetlen üzenetek teljes száma |
| `edgehub_queue_length` | `endpoint` (üzenet forrása)<br> `priority` (várólista prioritása) | Típus: mérőműszer<br> Egy adott prioritáshoz tartozó edgeHub-várólista jelenlegi hossza |
| `edgehub_messages_dropped_total` | `reason` (no_route, ttl_expiry)<br> `from` <br> `from_route_output` | Típus: számláló<br> Az ok miatt eltávolított üzenetek teljes száma |
| `edgehub_messages_unack_total` | `reason` (storage_failure)<br> `from`<br> `from_route_output` | Típus: számláló<br> A nem nyugtázott üzenetek teljes száma a tárolási hiba miatt |
| `edgehub_offline_count_total` | `id` | Típus: számláló<br> A edgeHub offline állapotba helyezésének teljes száma |
| `edgehub_offline_duration_seconds`| `id` | Típus: összefoglalás<br> A Time Edge hub offline állapotú |
| `edgehub_operation_retry_total` | `id`<br> `operation` (művelet neve) | Típus: számláló<br> A edgeHub-műveletek újrapróbálkozásának teljes száma |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (nincs hitelesítve)<br> | Típus: számláló<br> Azon időpontok száma, amikor az ügyfelek nem tudnak csatlakozni a edgeHub |

A **edgeAgent** modul a következő metrikákat állítja elő:

| Name | Dimenziók | Leírás |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | Típus: mérőműszer<br> Az a mennyiség, ameddig a modul meg lett adva a központi telepítésben, és a futási állapotban volt. |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | Típus: mérőműszer<br> A modul az üzemelő példányban megadott időtartamának mennyisége |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | Típus: számláló<br> A edgeAgent által a modul elindítására kért időpontok száma |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | Típus: számláló<br> Az a szám, ahányszor a edgeAgent a modul leállítására kéri a Docker |
| `edgeAgent_command_latency_seconds` | `command` | Típus: mérőműszer<br> Mennyi ideig tartott a Docker az adott parancs végrehajtásához. Lehetséges parancsok: létrehozás, frissítés, eltávolítás, indítás, Leállítás, újraindítás |
| `edgeAgent_iothub_syncs_total` | | Típus: számláló<br> Az a szám, ahányszor a edgeAgent megpróbálta szinkronizálni a iotHub, sikeres és sikertelen volt. Ez a szám magában foglalja mindkét ügynököt, amely a Twin és a hub értesítését kéri. |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | Típus: számláló<br> A edgeAgent nem tudta szinkronizálni a Twin-et a iotHub. |
| `edgeAgent_deployment_time_seconds` | | Típus: számláló<br> Az új központi telepítés befejezéséhez szükséges idő a változás fogadása után. |
| `edgeagent_direct_method_invocations_count` | `method_name` | Típus: számláló<br> A beépített edgeAgent Direct metódus meghívásának, például pingelésének vagy újraindításának a száma. |
| `edgeAgent_host_uptime_seconds` | | Típus: mérőműszer<br> Mennyi ideig tartott a gazdagép |
| `edgeAgent_iotedged_uptime_seconds` | | Típus: mérőműszer<br> Mennyi ideig futott a iotedged |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | Típus: mérőműszer<br> A lemezen maradó terület mennyisége |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| Típus: mérőműszer<br> A lemez mérete |
| `edgeAgent_used_memory_bytes` | `module_name` | Típus: mérőműszer<br> Az összes folyamat által használt RAM mennyisége |
| `edgeAgent_total_memory_bytes` | `module_name` | Típus: mérőműszer<br> Rendelkezésre álló memória |
| `edgeAgent_used_cpu_percent` | `module_name` | Típus: hisztogram<br> Az összes folyamat által használt CPU százaléka |
| `edgeAgent_created_pids_total` | `module_name` | Típus: mérőműszer<br> A tároló által létrehozott folyamatok vagy szálak száma |
| `edgeAgent_total_network_in_bytes` | `module_name` | Típus: mérőműszer<br> A hálózatból fogadott bájtok száma |
| `edgeAgent_total_network_out_bytes` | `module_name` | Típus: mérőműszer<br> A hálózatba küldendő bájtok száma |
| `edgeAgent_total_disk_read_bytes` | `module_name` | Típus: mérőműszer<br> A lemezről beolvasott bájtok száma |
| `edgeAgent_total_disk_write_bytes` | `module_name` | Típus: mérőműszer<br> A lemezre írt bájtok száma |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | Típus: mérőműszer<br> Az eszköz általános metaadatai. Az érték mindig 0, az adatok kódolása a címkék között történik. Figyelje meg `experimental_features` , hogy `host_information` JSON-objektumok. `host_information` úgy néz ki, mint ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}``` . Megjegyzés: `ServerVersion` a Docker verziója, és `Version` a IoT Edge Security Daemon verziója. |

## <a name="next-steps"></a>Következő lépések

* [A Azure IoT Edge futtatókörnyezet és az architektúrájának ismertetése](iot-edge-runtime.md)
* [A IoT Edge-ügynök és az IoT Edge hub-modulok ikrek tulajdonságai](module-edgeagent-edgehub.md)
