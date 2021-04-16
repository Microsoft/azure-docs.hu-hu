---
title: Az NSG-k folyamatnaplózásának bemutatása
titleSuffix: Azure Network Watcher
description: Ez a cikk az Azure-szolgáltatások NSG-forgalomnaplók funkcióját Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: f737be68a28f95ab5402ba5ea08e85fcf1b04d37
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565899"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Bevezetés a hálózati biztonsági csoportok folyamatnaplózásába

## <a name="introduction"></a>Bevezetés

[A hálózati biztonsági](../virtual-network/network-security-groups-overview.md#security-rules) csoport (NSG) forgalomnaplói az Azure Network Watcher szolgáltatása, amely lehetővé teszi az NSG-n áthaladó IP-forgalomra vonatkozó információk naplózását. A rendszer elküldi a folyamatadatokat az Azure Storage-fiókoknak, ahonnan elérheti őket, valamint exportálhatja őket bármely vizualizációs eszközbe, SIEM-be vagy azonosítóba.

![folyamatnaplók áttekintése](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Miért érdemes a Flow-naplókat használni?

Elengedhetetlen, hogy saját hálózatát figyelje, kezelje és ismerje meg a nem egyértelmű biztonság, megfelelőség és teljesítmény érdekében. A saját környezet ismerete rendkívül fontos a védelemhez és az optimalizáláshoz. Gyakran kell tudni a hálózat aktuális állapotát, azt, hogy ki csatlakozik, honnan csatlakoznak, milyen portok vannak nyitva az internet számára, milyen hálózati viselkedés várható, szabálytalan hálózati viselkedés, valamint a forgalom hirtelen növekedése.

A folyamatnaplók a felhőalapú környezetben végzett összes hálózati tevékenység igazságforrását biztosítják. Akár egy hamarosan induló startup, akár erőforrásokat próbál optimalizálni, akár nagyvállalatot próbál észlelni a behatolások után, a Flow-naplók a legjobb megoldás. A segítségével optimalizálhatja a hálózati folyamatokat, monitorozhatja az átviteli sebességet, ellenőrizheti a megfelelőséget, észlelheti a behatolásokat és egyéb stb.

## <a name="common-use-cases"></a>Gyakori használati helyzetek

**Hálózatfigyelés:** Azonosítsa az ismeretlen vagy nem szükséges forgalmat. A forgalomszintek és a sávszélesség-használat figyelése. Az alkalmazás viselkedésének megtekintéséhez szűrje a forgalomnaplókat IP-cím és port alapján. Folyamatnaplók exportálása a monitorozási irányítópultok beállításához választott elemzési és vizualizációs eszközökbe.

**Használat monitorozása és optimalizálása:** Azonosítsa a hálózat legfontosabb beszélgetőit. Kombinálhatja a GeoIP-adatokat a régiók közötti forgalom azonosításához. A forgalomnövekedést a kapacitás-előrejelzéshez. Használja az adatokat a túlságosan korlátozó forgalmi szabályok eltávolításához.

**Megfelelőség:** Folyamatadatok használata a hálózat elkülönítésének és a vállalati hozzáférési szabályoknak való megfelelés ellenőrzéséhez

**Hálózati kriminalisztika & biztonságelemzés:** Sérült IP-ről és hálózati adapterről származó hálózati folyamatok elemzése. Exportálja a folyamatnaplókat bármely SIEM- vagy IDS-eszközbe.

## <a name="how-logging-works"></a>A naplózás működése

**Kulcstulajdonságok**

- A folyamatnaplók [a 4. rétegben](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) működnek, és az NSG-k be- és kifelé áramló összes IP-folyamatát rögzítik
- A naplókat a rendszer 1 perces időközönként gyűjti az Azure **platformon** keresztül, és semmilyen módon nem befolyásolja az ügyfelek erőforrásait vagy hálózati teljesítményét.
- A naplók JSON formátumban vannak megírva, és NSG-szabályonként mutatják a kimenő és bejövő folyamatokat.
- Minden naplórekord tartalmazza azt a hálózati adaptert (NIC), amelyre a folyamat vonatkozik, ötrekordos információk, a forgalom döntési & (csak 2.verzió) átviteli sebességére vonatkozó információk. További _részleteket az alábbi Naplóformátumban_ talál.
- A folyamatnaplók olyan megőrzési funkcióval is rendelkezik, amely lehetővé teszi a naplók automatikus törlését a létrehozásuk után egy évig. 

> [!NOTE]
> A megőrzés csak akkor érhető el, ha általános célú [v2 tárfiókokat (GPv2) használ.](../storage/common/storage-account-overview.md#types-of-storage-accounts) 

**Alapfogalmak**

- A szoftveres hálózatok a virtuális hálózatok (VNET-ek) és az alhálózatok köré vannak szervezve. Ezeknek a virtuális hálózatoknak és alhálózatoknak a biztonsága egy NSG-vel kezelhető.
- A hálózati biztonsági csoport (NSG)  olyan biztonsági szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják a hálózati forgalmat az olyan erőforrásokban, amelyekhez csatlakozik. Az NSG-k társíthatóak alhálózatokkal, különálló virtuális gépekkel vagy a virtuális gépekhez csatlakoztatott különálló hálózati adapterekkel (NIC- Resource Manager). További információ: [Hálózati biztonsági csoportok áttekintése.](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)
- A hálózatban az összes forgalom kiértékelése a megfelelő NSG szabályai alapján történik.
- Az értékelés eredménye az NSG-forgalom naplói. A folyamatnaplókat a rendszer az Azure platformon gyűjti össze, és nem igényli az ügyfélerőforrások változását.
- Megjegyzés: A szabályok két típusból állnak – a megszakítást & megszakítást nem, és mindegyik különböző naplózási viselkedést ad vissza.
- - Az NSG megtagadási szabályai lezárnak. A forgalmat megtagadó NSG naplózza azt a Forgalmi naplókban, a feldolgozás pedig akkor áll le, ha valamelyik NSG megtagadja a forgalmat. 
- - Az NSG-megengedő szabályok nem végződnek, ami azt jelenti, hogy még ha az egyik NSG is engedélyezi, a feldolgozás a következő NSG-n folytatódik. A forgalmat engedélyező utolsó NSG naplózza a forgalmat a Flow-naplókba.
- Az NSG-forgalom naplói a tárfiókba lesznek írva, ahonnan elérhetők.
- A folyamatnaplókat exportálhatja, feldolgozhatja, elemezheti és vizualizálhatja olyan eszközökkel, mint a TA, a Splunk, a Grafana, a Stealthwatch stb.

## <a name="log-format"></a>Naplóformátum

A folyamatnaplók a következő tulajdonságokat tartalmazzák:

* **time** – Az esemény naplózásának ideje
* **systemId** – Hálózati biztonsági csoport rendszerazonosítója.
* **category** – Az esemény kategóriája. A kategória mindig **NetworkSecurityGroupFlowEvent**
* **resourceid** – Az NSG erőforrás-azonosítója
* **operationName** – Always NetworkSecurityGroupFlowEvents
* **properties** – A folyamat tulajdonságainak gyűjteménye
    * **Verzió** – A folyamatnapló eseménysémának verziószáma
    * **flows** – Folyamatok gyűjteménye. Ez a tulajdonság több bejegyzést tartalmaz a különböző szabályokhoz
        * **szabály** – Az a szabály, amelynek a folyamatait listázta
            * **flows** – folyamatok gyűjteménye
                * **mac** – A folyamatot gyűjtő virtuális gép hálózati adapterének MAC-címe
                * **flowTuples** – Egy sztring, amely a folyamat rekordja több tulajdonságát tartalmazza vesszővel elválasztott formátumban
                    * **Időbélyeg** – Ez az érték a folyamat UNIX-alapidőformátumban történt időbélyege
                    * **Forrás IP-címe** – A forrás IP-címe
                    * **Cél IP-címe** – A cél IP-címe
                    * **Forrásport** – A forrásport
                    * **Célport** – Célport
                    * **Protokoll** – A folyamat protokollja. UDP esetén a TCP és **az U** érvényes értéke **T**
                    * **Forgalom áramlása** – A forgalom iránya. Az érvényes értékek az **I** a bejövő és **az O a** kimenő forgalom esetén.
                    * **Forgalmi döntés** – Azt határozza meg, hogy a forgalom engedélyezve vagy tiltva lett-e. Az érvényes értékek **az A** az engedélyezettek, **a D** pedig a megtagadva.
                    * Folyamat állapota – Csak **2-es verzió** – Rögzíti a folyamat állapotát. Lehetséges államok: **B**: Kezdete folyamat létrehozásakor. Statisztikák nem biztosítanak. **C:** Folyamatban lévő folyamat folytatása. A statisztikák 5 perces időközönként biztosítanak. **E:** A folyamat befejeződik. A statisztikákat a rendszer biztosítja.
                    * **Csomagok – Forrás és cél – Csak 2-es verzió** A forrásból a célhelyre az utolsó frissítés óta küldött TCP-csomagok teljes száma.
                    * **Küldött bájtok – Forrástól a célig – Csak 2-es verzió** A forrásból a célhelyre küldött TCP-csomagok bájtszáma az utolsó frissítés óta. A csomag bájtja tartalmazza a csomagfejlécet és a hasznos adatokat.
                    * **Csomagok – Célhely – Csak 2-es verzió** A célhelyről a forráshoz az utolsó frissítés óta küldött TCP-csomagok teljes száma.
                    * **Küldött bájtok – Cél és forrás – Csak 2-es verzió** A célhelyről a forráshoz az utolsó frissítés óta küldött TCP-csomagok bájtszáma. A csomag bájtja tartalmazza a csomagfejlécet és a hasznos adatokat.


**NSG-forgalomnaplók 2. verziója (és 1. verzió)** 

A naplók 2. verziója bevezeti a folyamatállapot fogalmát. Beállíthatja, hogy a folyamatnaplók melyik verzióját kapja meg.

A _folyamat elindításakor_ a rendszer a B folyamatállapotot rögzíti. A _C folyamatállapot_ és az _E_ folyamatállapot egy folyamat- és folyamat-megszakítás folytatását jelöli. A _C és_ az E _állam_ is tartalmazza a forgalom sávszélességével kapcsolatos információkat.

### <a name="sample-log-records"></a>Naplórekordok mintája

A következő szöveg egy példa a folyamatnaplóra. Mint látható, több rekord is követi az előző szakaszban leírt tulajdonságlistát.

> [!NOTE]
> A *flowTuples tulajdonság* értékei vesszővel vannak elválasztva.
 
**1. verziójú NSG-forgalom naplóformátumának mintája**
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        
        
```
**2. verziójú NSG-forgalom naplóformátumának mintája**
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        }
        
```
**Naplóbejegyzés magyarázata**

![folyamatnaplók rekordja](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Példa a sávszélesség kiszámítására**

A forgalom a 185.170.185.105:35370 és a 10.2.0.4:23 közötti TCP-beszélgetésből ad vissza kapcsolatot:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B ,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

A _C_ és az _E_ folyamat végének folytatásakor a bájtok és a csomagok száma az előző folyamatrekordrekordból származó összesített darabszám. Az előző beszélgetésre hivatkozva az átvitt csomagok teljes száma 1021+52+8005+47 = 9125. Az átvitt bájtok teljes száma 588096+29952+4610880+27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>NSG-folyamat-naplók engedélyezése

A folyamatnaplók engedélyezésével kapcsolatos útmutatókhoz használja az alábbi hivatkozásokat.

- [Azure Portal](./network-watcher-nsg-flow-logging-portal.md)
- [PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [Parancssori felület](./network-watcher-nsg-flow-logging-cli.md)
- [REST](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>Paraméterek frissítése

**Azure Portal**

A Azure Portal nyissa meg az NSG-forgalom naplói szakaszt a Network Watcher. Ezután kattintson az NSG nevére. Ez a Folyamatnapló beállítások panelét fogja elhozni. Módosítsa a kívánt paramétereket, majd nyomja le a **Mentés gombra** a módosítások üzembe helyezéséhez.

**PS/CLI/REST/ARM**

A paraméterek parancssori eszközökkel való frissítéséhez használja ugyanazt a parancsot, mint a folyamatnaplók engedélyezéséhez (a fentiekből), de a módosítani kívánt frissített paraméterekkel.

## <a name="working-with-flow-logs"></a>Flow-naplók megtekintése

*Folyamatnaplók olvasása és exportálása*

- [&amp;Folyamatnaplók megtekintése letöltése a portálról](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [Flow-naplók olvasása PowerShell-függvényekkel](./network-watcher-read-nsg-flow-logs.md)
- [NSG-forgalom naplóinak exportálása a Splunkba](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Bár a folyamatnaplók cél NSG-k, nem ugyanúgy jelennek meg, mint a többi napló. A folyamatnaplók csak egy tárfiókban vannak tárolva, és az alábbi példában látható naplózási útvonalat követik:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Folyamatnaplók vizualizációja*

- [Az Azure Traffic Analytics](./traffic-analytics.md) egy natív Azure-szolgáltatás a folyamatnaplók feldolgozásához, elemzések kinyeréséhez és a forgalmi naplók megjelenítéséhez. 
- [[Oktatóanyag] NSG-forgalom naplóinak vizualizációja Power BI](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [[Oktatóanyag] NSG-forgalom naplóinak vizualizációja az Elastic Stack segítségével](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [[Oktatóanyag] NSG-forgalom naplóinak kezelése és elemzése a Grafana használatával](./network-watcher-nsg-grafana.md)
- [[Oktatóanyag] NSG-forgalom naplóinak kezelése és elemzése a Graylog használatával](./network-watcher-analyze-nsg-flow-logs-graylog.md)

*Folyamatnaplók letiltása*

Ha a folyamatnapló le van tiltva, a társított NSG-hez tartozó folyamatnaplózás leáll. A folyamat erőforrásként való naplója azonban továbbra is létezik az összes beállításával és társításával együtt. Bármikor engedélyezhető, ha hozzákezd a konfigurált NSG-n a forgalomnaplózáshoz. A folyamatnaplók letiltásához/engedélyezéséhez szükséges lépéseket ebben a [útmutatóban talál.](./network-watcher-nsg-flow-logging-powershell.md)  

*Folyamatnaplók törlése*

A folyamatnapló törlésekor nem csak a társított NSG folyamatnaplózása lesz leállítva, hanem a folyamatnapló-erőforrás is törlődik annak beállításaival és társításaival együtt. A folyamatnaplózás újra megkezdéséhez új folyamatnapló-erőforrást kell létrehozni az adott NSG-hez. A folyamatnaplók a [PowerShell,](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkwatcherflowlog)a [parancssori](https://docs.microsoft.com/cli/azure/network/watcher/flow-log#az_network_watcher_flow_log_delete) felület vagy a [REST API.](https://docs.microsoft.com/rest/api/network-watcher/flowlogs/delete) Folyamatban van a folyamatnaplók Azure Portal törlése a folyamatból.    

Emellett egy NSG törlésekor alapértelmezés szerint a társított folyamatnapló-erőforrás is törlődik.

> [!NOTE]
> Egy NSG másik erőforráscsoportba vagy előfizetésbe való áthelyezéséhez törölni kell a társított folyamatnaplókat, csak a folyamatnaplók letiltása nem fog működni. Az NSG áttelepítése után újra létre kell hozni a folyamatnaplókat, hogy engedélyezve legyen rajta a folyamatnaplózás.  

## <a name="nsg-flow-logging-considerations"></a>NSG-forgalom naplózási szempontjai

**Tárfiókkal kapcsolatos szempontok:** 

- Hely: A használt tárfióknak és az NSG-nek ugyanabban a régióban kell lennie.
- Teljesítményszint: Jelenleg csak a standard szintű tárfiókok támogatottak.
- Önkiszolgáló kulcsrotáció: Ha módosítja/rotitálja a tárfiók hozzáférési kulcsait, az NSG-forgalom naplói leállnak. A probléma megoldásához le kell tiltania, majd újra engedélyeznie kell az NSG-forgalom naplóit.

**Folyamatnaplózási költségek:** Az NSG-forgalom naplózása a előállított naplók mennyiségének alapján van kiszámlázva. A nagy forgalom nagy forgalommal jár, ami nagy forgalomnapló-mennyiséghez és a kapcsolódó költségekhez vezethet. Az NSG-forgalom naplói díjszabása nem tartalmazza a tárolási mögöttes költségeket. A megőrzési szabályzat funkció NSG-forgalomnaplózással való használata azt jelenti, hogy hosszabb ideig külön tárolási költségekkel kell jár. Ha örökre meg szeretné őrizni az adatokat, és nem szeretne megőrzési szabályzatot alkalmazni, állítsa a megőrzést (nap) 0-ra. További részletekért tekintse meg [Network Watcher díjszabását](https://azure.microsoft.com/pricing/details/network-watcher/) és [az Azure Storage](https://azure.microsoft.com/pricing/details/storage/) díjszabását.

**Felhasználó által definiált bejövő TCP-szabályokkal** kapcsolatos problémák: A hálózati biztonsági csoportok [(NSG-k)](../virtual-network/network-security-groups-overview.md) állapot-alapú tűzfalként [vannak megvalósítva.](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true) A jelenlegi platformkorlátozások miatt azonban a bejövő TCP-folyamatokat befolyásoló, felhasználó által definiált szabályok állapot nélküli módon vannak megvalósítva. Emiatt a felhasználó által megadott bejövő szabályok által érintett folyamatok nem lesznek lezárva. Ezeken a folyamatokon emellett a bájtok és a csomagok száma sem lesz rögzítve. Ennek következtében az NSG-forgalom naplóiban (és Traffic Analytics) jelentett bájtok és csomagok száma különbözhet a tényleges számoktól. A problémákat kijavítást jelző 2021. márciusra van ütemezve. E körülmények között az ilyen viselkedés miatt súlyos problémákkal szembesülő ügyfelek kérhetik a támogatáson keresztüli feliratkozás kérését, ezért kérjük, hogy az NSG-forgalom naplói Network Watcher > támogatási kérést.  

Az internetes IP-címekről a nyilvános IP-cím nélküli virtuális gépekre naplózott bejövő **folyamatok:** Azok a virtuális gépek, amelyek nyilvános IP-címe nincs hozzárendelve a hálózati adapterhez példányszintű nyilvános IP-címként társított nyilvános IP-címmel, vagy amelyek egy alapszintű terheléselosztási háttérkészlet részei, használja az alapértelmezett [SNAT-t,](../load-balancer/load-balancer-outbound-connections.md) és az Azure által hozzárendelt IP-címmel rendelkezik a kimenő kapcsolat elősegítéséhez. Ennek eredményeképpen folyamatnapló-bejegyzéseket láthat az internetes IP-címekről származó folyamatokhoz, ha a folyamat egy, az SNAT-hez hozzárendelt porttartományban található porthoz van rendelve. Bár az Azure nem engedélyezi ezeket a folyamatokat a virtuális gép felé, a rendszer naplózza a kísérletet, és Network Watcher NSG-folyamatnaplójában. Javasoljuk, hogy a nem kívánt bejövő internetes forgalmat kifejezetten blokkolja az NSG.

**V2 Application Gateway NSG-vel** kapcsolatos probléma: Az Application Gateway V2 alhálózati NSG-n a folyamatnaplózás [jelenleg nem](../application-gateway/application-gateway-faq.yml#are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet) támogatott. Ez a probléma nincs hatással Application Gateway V1-re.

**Incompatible Services**(Inkompatibilis szolgáltatások): A jelenlegi platformkorlátozások miatt az NSG-forgalom naplói nem támogatják az Azure-szolgáltatások egy kis készletét. Az inkompatibilis szolgáltatások aktuális listája:
- [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>Ajánlott eljárások

**Engedélyezés kritikus virtuális hálózatokon/alhálózaton:** A folyamatnaplókat naplózási és biztonsági ajánlott eljárásként engedélyezni kell az előfizetés összes kritikus VNET-én/alhálózatán. 

**NSG-forgalomnaplózás** engedélyezése az erőforráshoz csatolt összes NSG-n: Az Azure folyamatnaplózása az NSG-erőforráson van konfigurálva. Egy folyamat csak egy NSG-szabályhoz lesz társítva. A több NSG-t használó forgatókönyvek esetében javasoljuk az NSG-forgalomnaplók engedélyezését az erőforrás alhálózatában vagy hálózati adapterén alkalmazott összes NSG esetében, hogy a rendszer biztosan rögzítse a teljes forgalmat. További információért tekintse meg a forgalom [kiértékelésének a hálózati](../virtual-network/network-security-group-how-it-works.md) biztonsági csoportokban való kiértékelésének mikéntját. 

Néhány gyakori forgatókönyv:
1. **Több hálózati számítógép egy** virtuális gépen: Ha egy virtuális géphez több hálózati csatolva van, mindegyiken engedélyezni kell a folyamatnaplózást
1. **NSG hálózati** adapter- és alhálózati szinten is: Ha az NSG-t a hálózati adapter és az alhálózat szintjén konfigurálták, akkor mindkét NSG-n engedélyezni kell a folyamatnaplózást. 

**Tároló kiépítése:** A tárterületet a folyamatnapló várható kötetének megfelelően kell kiépítenünk.

**Elnevezés:** Az NSG neve legfeljebb 80 karakter lehet, az NSG-szabály nevei pedig legfeljebb 65 karakterből. Ha a nevek túllépik a karakterkorlátjukat, csonkolhatják a naplózás közben.

## <a name="troubleshooting-common-issues"></a>Gyakori problémák megoldása

**Nem tudtam engedélyezni az NSG-forgalom naplóit**

- **A Microsoft.Insights erőforrás-szolgáltató** nincs regisztrálva

Ha _AuthorizationFailed_ vagy _GatewayAuthenticationFailed_ hibát kapott, lehet, hogy nem engedélyezte a Microsoft Insights erőforrás-szolgáltatóját az előfizetésében. [Kövesse az utasításokat a](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider) Microsoft Insights-szolgáltató engedélyezéséhez.

**Engedélyeztem az NSG-forgalom naplóit, de nem látok adatokat a tárfiókomban**

- **A telepítés időtartama**

Akár 5 percet is igénybe vehet, hogy az NSG-forgalom naplói megjelenjenek a tárfiókjában (megfelelő konfiguráció esetén). Egy PTH1.json fájl fog megjelenni, amely [az itt leírtak szerint](./network-watcher-nsg-flow-logging-portal.md#download-flow-log) érhető el.

- **Nincs forgalom az NSG-kben**

Néha nem fog naplókat látni, mert a virtuális gépek nem aktívak, vagy egy alkalmazásátjárón vagy más eszközön olyan felsőbb rétegbeli szűrők találhatók, amelyek blokkolják a forgalmat az NSG-k felé.

**Automatizálni szeretném az NSG-forgalom naplóit**

Az ARM-sablonokkal történő automatizálás jelenleg nem érhető el az NSG-forgalom naplói esetében. További információért olvassa el a [funkcióbejelentést](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/).

## <a name="faq"></a>GYIK

**Mire valók az NSG-forgalom naplói?**

Az Azure-beli hálózati erőforrások a hálózati biztonsági csoportokkal [(NSG-k)](../virtual-network/network-security-groups-overview.md)kombinálhatók és kezelhetők. Az NSG-forgalom naplói lehetővé teszik az NSG-ken áthaladó összes forgalom öt rekordos folyamatinformációinak naplózását. A nyers folyamatnaplók egy Azure Storage-fiókba lesznek írva, ahonnan szükség szerint tovább feldolgozhatók, elemezhetők, lekérdezhetők vagy exportálhatók.

**Befolyásolja a forgalomnaplók használata a hálózati késést vagy a teljesítményt?**

A forgalmi naplók adatait a rendszer a hálózati forgalom útvonalán kívül gyűjti, ezért nincs hatással a hálózati teljesítményre vagy a késésre. Folyamatnaplókat a hálózati teljesítmény befolyásolása nélkül hozhat létre vagy törölhet.

**Hogyan NSG-forgalom naplóit tűzfal mögötti tárfiókkal?**

Ha tűzfal mögötti Storage-fiókot használ, kivételt kell adnia a megbízható Microsoft-szolgáltatások számára a tárfiók eléréséhez:

- Navigáljon a tárfiókhoz úgy, hogy beírja a tárfiók nevét a globális keresés portálon vagy a [Tárfiókok oldalon](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
- A  **BEÁLLÍTÁSOK szakaszban**  válassza a  **Tűzfalak és virtuális hálózatok lehetőséget.**
- A **Hozzáférés engedélyezése innen: beállításnál** válassza a **Kijelölt hálózatok lehetőséget.** Ezután a  **Kivételek alatt** jelölje be a *Megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a tárfiókhoz" jelölőnégyzetet.
- Ha ez a beállítás már ki lett választva, nincs szükség módosításra.
- Keresse meg a cél NSG-t az [NSG-forgalom](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) naplói áttekintő oldalon, és engedélyezze az NSG-forgalom naplóit a fenti tárfiók kiválasztása után.

Pár perc elteltével ellenőrizheti a tárnaplókat – egy frissített időbélyeget vagy egy újonnan létrehozott JSON-fájlt kell látnia.

**Hogyan NSG-forgalom naplóit egy szolgáltatásvégpont mögötti tárfiókkal használja?**

Az NSG-forgalom naplói további konfiguráció nélkül kompatibilisek a szolgáltatásvégpontokkal. Tekintse meg [a szolgáltatásvégpont virtuális](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) hálózaton való engedélyezését ismertető oktatóanyagot.

**Mi a különbség a folyamatnaplók 1. és 2. & verziója között?**

A Flow-naplók 2.  verziója bevezeti a folyamatállapot fogalmát& a továbbított bájtokkal és csomagokkal kapcsolatos információkat tárolja. [További információ](#log-format)

## <a name="pricing"></a>Díjszabás

Az NSG-forgalom naplói az összegyűjtött naplók GB-onként vannak felszámtöltve, és előfizetésenként 5 GB/hó ingyenes csomaggal jönek el. A régió aktuális díjszabását a [díjszabást Network Watcher oldalon láthatja.](https://azure.microsoft.com/pricing/details/network-watcher/)

A naplók tárolásáért külön kell fizetni, a vonatkozó árakért lásd az [Azure Storage-blokkblobok](https://azure.microsoft.com/pricing/details/storage/blobs/) díjszabását.