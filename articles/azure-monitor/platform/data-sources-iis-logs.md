---
title: Az IIS-naplók összegyűjtése Log Analytics ügynökkel Azure Monitor
description: A Internet Information Services (IIS) a Azure Monitor által összegyűjthető naplófájlokban tárolja a felhasználói tevékenységeket.  Ez a cikk bemutatja, hogyan konfigurálhatja az IIS-naplók gyűjteményét és a Azure Monitorban létrehozott rekordok részleteit.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2020
ms.openlocfilehash: a089631ab199b0fe997bba001561c6b027034e2c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993686"
---
# <a name="collect-iis-logs-with-log-analytics-agent-in-azure-monitor"></a>Az IIS-naplók összegyűjtése Log Analytics ügynökkel Azure Monitor
A Internet Information Services (IIS) olyan naplófájlokban tárolja a felhasználói tevékenységeket, amelyeket az Log Analytics ügynök gyűjthet, és [Azure monitor naplókban](data-platform.md)tárol.

> [!IMPORTANT]
> Ez a cikk ismerteti az IIS-naplók összegyűjtését az [log Analytics ügynökkel](log-analytics-agent.md) , amely a Azure monitor által használt ügynökök egyike. Más ügynökök különböző adatokat gyűjtenek, és eltérően vannak konfigurálva. A rendelkezésre álló ügynökök és az összegyűjtött adatok listáját lásd: [Azure monitor ügynökök áttekintése](agents-overview.md) .

![IIS-naplók](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>IIS-naplók konfigurálása
A Azure Monitor az IIS által létrehozott naplófájlokból gyűjt bejegyzéseket, ezért konfigurálnia kell az IIS-t [a naplózáshoz](/previous-versions/orphan-topics/ws.11/hh831775(v=ws.11)).

Azure Monitor csak a W3C formátumban tárolt IIS-naplófájlokat támogatja, és nem támogatja az egyéni mezőket vagy az IIS speciális naplózását. Az NCSA vagy az IIS natív formátumában nem gyűjt naplókat.

Konfigurálja az IIS-naplókat a Azure Monitor a Log Analytics ügynök [Speciális beállítások menüjében](agent-data-sources.md#configuring-data-sources) .  A **W3C-formátumú IIS-naplófájlok gyűjtésének** kiválasztásához nincs szükség konfigurációra.


## <a name="data-collection"></a>Adatgyűjtés
A Azure Monitor az egyes ügynököktől érkező IIS-naplókat gyűjti össze minden alkalommal, amikor megváltozik a napló időbélyege. A napló **5 percenként** olvasható. Ha bármilyen okból kifolyólag az IIS nem frissíti az időbélyeget az új fájl létrehozásakor, a rendszer az új fájl létrehozása után begyűjti a bejegyzéseket. Az új fájl létrehozásának gyakoriságát az IIS-hely **naplófájl-átváltási ütemezési** beállítása szabályozza, amely alapértelmezés szerint naponta egyszer történik. Ha a beállítás **óránként** történik, Azure monitor óránként gyűjti a naplót. Ha a beállítás **naponta** történik, Azure monitor 24 óránként gyűjti a naplót.

> [!IMPORTANT]
> Javasoljuk, hogy **óránként** állítsa be a **naplófájlok ütemezését** . Ha **napi** értékre van állítva, akkor a rendszer az adatokban csak egyszer gyűjthet össze adatokat.

## <a name="iis-log-record-properties"></a>IIS-napló rekordjának tulajdonságai
Az IIS-naplóbejegyzések típusa **W3CIISLog** , és a következő táblázatban található tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Annak a számítógépnek a neve, amelyre az eseményt gyűjtötték. |
| cIP |Az ügyfél IP-címe. |
| csMethod |A kérelem metódusa, például GET vagy POST. |
| csReferer |A hely, amelyet a felhasználó az aktuális helyre mutató hivatkozást követett. |
| csUserAgent |Az ügyfél böngésző-típusa. |
| csUserName |A kiszolgálót elérő hitelesített felhasználó neve. A névtelen felhasználókat kötőjel jelöli. |
| csUriStem |A kérelem célja, például weblap. |
| csUriQuery |Lekérdezés, ha van ilyen, amelyet az ügyfél megpróbált végrehajtani. |
| ManagementGroupName |Operations Manager ügynökök felügyeleti csoportjának neve.  Más ügynökök esetében ez az AOI-\<workspace ID\> |
| RemoteIPCountry |Az ügyfél IP-címének országa/régiója. |
| RemoteIPLatitude |Az ügyfél IP-címének földrajzi szélességét. |
| RemoteIPLongitude |Az ügyfél IP-címének hosszúsága. |
| scStatus |HTTP-állapotkód. |
| scSubStatus |Részállapot-hibakód. |
| scWin32Status |Windows-állapotkód. |
| sIP |A webkiszolgáló IP-címe. |
| SourceSystem |OpsMgr |
| sPort |Annak a kiszolgálónak a portja, amelyhez az ügyfél csatlakozik. |
| sSiteName |Az IIS-hely neve. |
| TimeGenerated |A bejegyzés naplózásának dátuma és időpontja. |
| Eltelt idő |A kérelem feldolgozásának időtartama ezredmásodpercben. |

## <a name="log-queries-with-iis-logs"></a>Lekérdezések naplózása IIS-naplókkal
Az alábbi táblázat különböző példákat tartalmaz az IIS-naplóbejegyzések lekérésére szolgáló naplókra.

| Lekérdezés | Leírás |
|:--- |:--- |
| W3CIISLog |Az összes IIS-naplózási rekord. |
| W3CIISLog &#124;, ahol scStatus = = 500 |Az összes IIS-naplózási rekord 500-as visszatérési állapottal. |
| W3CIISLog &#124; összegzések száma () a cIP szerint |IIS-naplóbejegyzések száma az ügyfél IP-címe alapján. |
| W3CIISLog &#124;, ahol csHost = = "www \. contoso.com" &#124; összesítések száma () a csUriStem szerint |Az IIS-naplóbejegyzések száma a gazdagép www contoso.com URL-címe alapján \. . |
| W3CIISLog &#124; a Sum (csBytes) összegzése a számítógépen &#124; Take 500000 |AZ egyes IIS-számítógépek által fogadott bájtok száma. |

## <a name="next-steps"></a>További lépések
* A Azure Monitor konfigurálásával más [adatforrásokat](agent-data-sources.md) is gyűjthet az elemzéshez.
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../log-query/log-query-overview.md) .
