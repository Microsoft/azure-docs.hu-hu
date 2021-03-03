---
title: Adatok küldése a Windows Azure Diagnostics bővítményből az Azure-Event Hubs
description: A diagnosztikai bővítmény konfigurálása a Azure Monitorban az Azure Event hub-ba való adatküldéshez, így az Azure-on kívüli helyszínekre is továbbíthatja.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 19c39632a1ed040636372d6bad53bf6859960b94
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732037"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Adatok küldése a Windows Azure Diagnostics bővítményből az Azure-Event Hubs
Az Azure Diagnostics bővítmény olyan Azure Monitor ügynöke, amely a vendég operációs rendszerből és az Azure-beli virtuális gépek és egyéb számítási erőforrások munkaterhelésével kapcsolatos figyelési adatokat gyűjt. Ez a cikk azt ismerteti, hogyan lehet adatok küldését a Windows Azure diagnosztikai bővítménnyel (WAD) az [azure Event Hubsba](https://azure.microsoft.com/services/event-hubs/) , így az Azure-on kívüli helyekre is továbbíthatja őket.

## <a name="supported-data"></a>Támogatott adatértékek

A Event Hubs elküldhető vendég operációs rendszerből gyűjtött adatok az alábbiakat tartalmazzák. A WAD által gyűjtött egyéb adatforrások, például az IIS-naplók és az összeomlási memóriaképek nem küldhetők el Event Hubsba.

* A Windows esemény-nyomkövetés (ETW) eseményei
* Teljesítményszámlálók
* Windows-eseménynaplók, beleértve az alkalmazások naplóit a Windows eseménynaplóban
* Azure Diagnostics-infrastruktúranaplók

## <a name="prerequisites"></a>Előfeltételek

* Windows Diagnostics bővítmény 1,6 vagy újabb. A támogatott erőforrások esetében lásd: [Azure Diagnostics bővítmény konfigurációs sémájának verziói és előzményei](diagnostics-extension-versions.md) a korábbi verziók és a [Azure Diagnostics bővítmények áttekintésében](diagnostics-extension-overview.md) .
* Event Hubs névtérnek mindig kiépítve kell lennie. A részletekért tekintse meg [a Event Hubs első lépéseit](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) ismertető témakört.


## <a name="configuration-schema"></a>Konfigurációs séma
Lásd: a [Windows Azure Diagnostics bővítmény (wad) telepítése és konfigurálása](diagnostics-extension-windows-install.md) különböző beállításokhoz a diagnosztikai bővítmény engedélyezéséhez és konfigurálásához, valamint [Azure Diagnostics konfigurációs séma](diagnostics-extension-schema-windows.md) a konfigurációs séma hivatkozásához. A cikk további része leírja, hogyan használhatja ezt a konfigurációt az adatoknak az Event hub-ba való küldéséhez. 

Azure Diagnostics mindig naplókat és mérőszámokat küld egy Azure Storage-fiókba. Beállíthat egy vagy több olyan *adattárolót* , amely az adatküldést további helyszínekre küldi. Minden fogadó a nyilvános konfiguráció [SinksConfig elemében](diagnostics-extension-schema-windows.md#sinksconfig-element) van definiálva, bizalmas információkkal a privát konfigurációban. Az Event hubok esetében ez a konfiguráció az alábbi táblázatban szereplő értékeket használja.

| Tulajdonság | Leírás |
|:---|:---|
| Név | A fogadó leíró neve. A konfigurációban a fogadóba küldendő adatforrások megadására szolgál. |
| URL-cím  | Az Event hub URL-címe a következő formában: Form \<event-hubs-namespace\> . servicebus.Windows.net/ \<event-hub-name\> .          |
| SharedAccessKeyName | Olyan megosztott hozzáférési szabályzat neve, amely legalább a **küldő** szolgáltatóval rendelkezik. |
| SharedAccessKey     | Az Event hub közös hozzáférési házirendjéből származó elsődleges vagy másodlagos kulcs. |

A következő példa a nyilvános és a privát konfigurációkat mutatja be. Ez egy minimális konfiguráció egyetlen teljesítményszámláló és Eseménynapló használatával, amely bemutatja, hogyan konfigurálhatja és használhatja az Event hub-adatfogadót. Összetettebb példáért tekintse meg [Azure Diagnostics konfigurációs sémát](diagnostics-extension-schema-windows.md) .

### <a name="public-configuration"></a>Nyilvános konfiguráció

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120,
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Privát konfiguráció

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Beállítási lehetőségek
Az adatfogadóba való adatküldéshez meg kell adnia a **mosogatók** attribútumot az adatforrás csomópontjain. A **mosogatók** attribútum helye határozza meg a hozzárendelés hatókörét. A következő példában a **mosogatók** attribútum a **PerformanceCounters** csomóponthoz van definiálva, amely az összes alárendelt teljesítményszámláló továbbítását fogja eredményezni az Event hub számára.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


A következő példában a **mosogatók** attribútumot közvetlenül a három számlálóra alkalmazza a rendszer, ami csak azokat a teljesítményszámlálókat fogja elküldeni, amelyek az Event hub-ba lesznek küldve. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Konfiguráció ellenőrzése
A különböző módszerekkel ellenőrizheti, hogy az adatküldés folyamatban van-e az Event hub számára. a nem egyszerű módszer az Azure-Event Hubs Azure-beli [blob Storage vagy Azure Data Lake Storage-ban](../../event-hubs/event-hubs-capture-overview.md)Event Hubs rögzítésének használata. 


## <a name="troubleshoot-event-hubs-sinks"></a>Event Hubs mosogatók hibáinak megoldása

- Tekintse meg az Azure Storage Table **WADDiagnosticInfrastructureLogsTable** , amely naplókat és hibákat tartalmaz a Azure Diagnostics. Az egyik lehetőség egy olyan eszköz használata, mint például a [Azure Storage Explorer](https://www.storageexplorer.com) az ehhez a Storage-fiókhoz való kapcsolódáshoz, a táblázat megtekintése és az időbélyegzőhöz tartozó lekérdezés hozzáadása az elmúlt 24 órában. Az eszközzel exportálhat egy. csv-fájlt, és megnyithatja azt egy alkalmazásban, például a Microsoft Excelben. Az Excel megkönnyíti a hívó kártyás karakterláncok, például a **EventHubs** keresését a jelentett hibák megtekintéséhez.  

- Győződjön meg arról, hogy az Event hub sikeresen kiépítve. A konfiguráció **PrivateConfig** szakaszában szereplő összes kapcsolatbiztonsági információnak meg kell egyeznie az erőforrásnak a portálon látható értékeivel. Győződjön meg arról, hogy a portálon van definiálva SAS-szabályzat (a példában a *SendRule* ), és hogy a *küldési* engedély meg van adva.  

## <a name="next-steps"></a>Következő lépések

* [Event Hubs áttekintése](../../event-hubs/event-hubs-about.md)
* [Eseményközpont létrehozása](../../event-hubs/event-hubs-create.md)
* [Event Hubs – gyakori kérdések](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



