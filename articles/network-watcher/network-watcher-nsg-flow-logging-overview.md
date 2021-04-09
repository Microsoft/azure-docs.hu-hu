---
title: A flow naplózásának bemutatása a NSG
titleSuffix: Azure Network Watcher
description: Ez a cikk azt ismerteti, hogyan használható az Azure Network Watcher NSG flow-naplói szolgáltatás.
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
ms.openlocfilehash: bc085163b4f738d022ab9771794ec85293de5ed8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100521679"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Bevezetés a hálózati biztonsági csoportok folyamatnaplózásába

## <a name="introduction"></a>Bevezetés

A [hálózati biztonsági csoport](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) folyamatábrája az Azure Network Watcher egyik funkciója, amely lehetővé teszi, hogy naplózza a NSG keresztül ÁRAMLÓ IP-forgalomra vonatkozó információkat. A flow-adatok az Azure Storage-fiókokba kerülnek, ahonnan elérheti, és exportálhatja bármely vizualizációs eszközre, SIEM-re vagy tetszőleges AZONOSÍTÓra.

![a flow naplói – áttekintés](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Miért érdemes a flow-naplókat használni?

A biztonság, a megfelelőség és a teljesítmény érdekében elengedhetetlen a saját hálózat figyelése, kezelése és megismerése. A saját környezetének ismerete rendkívül fontos a védelemhez és az optimalizáláshoz. Gyakran ismernie kell a hálózat aktuális állapotát, amely összeköti a csatlakozást, és hogy mely portok állnak nyitva az interneten, a várt hálózati működés, a szabálytalan hálózati viselkedés és a forgalom hirtelen emelkedése.

A flow-naplók a Felhőbeli környezet összes hálózati tevékenységének az igazság forrása. Akár egy közelgő indítást próbál meg optimalizálni az erőforrások vagy a nagyvállalatok számára a behatolás észlelésére tett kísérlet során, a flow-naplók a legjobb megoldás. Használhatja a hálózati folyamatok optimalizálására, a teljesítmény figyelésére, a megfelelőség ellenőrzésére, a behatolások észlelésére és egyebekre.

## <a name="common-use-cases"></a>Gyakori használati helyzetek

**Hálózati figyelés**: ismeretlen vagy nemkívánatos forgalom azonosítása. A forgalmi szintek és a sávszélesség-felhasználás figyelése. Az alkalmazás működésének megismeréséhez IP és port szerint szűrje a folyamat naplóit. Exportálja a folyamat naplóit az elemzési és vizualizációs eszközökre, amelyek segítségével beállíthatja a figyelési irányítópultokat.

**Használat figyelése és optimalizálása:** Azonosítsa a legjobb beszélőket a hálózaton. A GeoIP-adatokkal kombinálva azonosíthatja a régiók közötti forgalmat. A kapacitás-előrejelzési forgalom növekedésének megismerése. Adatok használata a nyíltan korlátozó forgalmi szabályok eltávolításához.

**Megfelelőség**: a flow használatával ellenőrizheti a hálózat elkülönítését és a vállalati hozzáférési szabályoknak való megfelelést

**Hálózati kriminalisztika & biztonsági elemzés**: a hálózati folyamatok elemzése a feltört IP-címekről és hálózati adapterekről. Exportálja a flow-naplókat az Ön által választott SIEM-vagy IDS-eszközre.

## <a name="how-logging-works"></a>A naplózás működése

**Kulcs tulajdonságai**

- A flow-naplók a [4. rétegben](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) működnek, és rögzítik az összes olyan IP-folyamatot, amely egy NSG
- A naplókat az Azure platformon **1 – min időközönként** gyűjti a rendszer, és semmilyen módon nem befolyásolja az ügyfelek erőforrásait vagy a hálózati teljesítményt.
- A naplók JSON formátumban vannak megírva, és a kimenő és a bejövő folyamatok megjelenítése NSG-szabály alapján történik.
- Minden naplóbejegyzés tartalmazza a hálózati adaptert (NIC), amely 5 rekordos információra vonatkozik, a forgalmi döntés & (csak 2. verzió) átviteli sebességre vonatkozó információk. A részletekért tekintse meg az alábbi _naplózási formátumot_ .
- A flow-naplók egy megőrzési funkcióval rendelkeznek, amely lehetővé teszi a naplók automatikus törlését a létrehozásuk után egy évig. 

> [!NOTE]
> Az adatmegőrzés csak az [általános célú v2 Storage-fiókok (GPv2-EK)](../storage/common/storage-account-overview.md#types-of-storage-accounts)használata esetén érhető el. 

**Alapfogalmak**

- A szoftver által definiált hálózatok a virtuális hálózatok (virtuális hálózatok) és az alhálózatok köré szerveződnek. Ezeknek a virtuális hálózatok és alhálózatoknak a biztonsága felügyelhető NSG használatával.
- A hálózati biztonsági csoport (NSG) olyan _biztonsági szabályok_ listáját tartalmazza, amelyek engedélyezik vagy megtagadják a hálózati forgalmat a kapcsolódó erőforrásokban. A NSG a virtuális gépekhez (Resource Manager) csatolt alhálózatokhoz, egyéni virtuális gépekhez vagy egyedi hálózati adapterekhez (NIC) is társítható. További információ: [hálózati biztonsági csoport áttekintése](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- A hálózatban lévő összes adatforgalom kiértékelése a vonatkozó NSG található szabályok alapján történik.
- Ezen értékelések eredménye a NSG. A flow-naplókat az Azure platformon gyűjtjük össze, és nem szükséges módosítani az ügyfél erőforrásait.
- Megjegyzés: a szabályok két típusból állnak – az & megszakítása leáll, amelyek mindegyike különböző naplózási viselkedéssel rendelkezik.
- - A NSG megtagadási szabályai leállnak. A forgalmat megtagadó NSG naplózza a folyamat naplófájljaiban, és a feldolgozás ebben az esetben leáll, miután bármely NSG megtagadja a forgalmat. 
- - A NSG engedélyezési szabályai nem állnak le, ami azt jelenti, hogy még akkor is, ha egy NSG engedélyezi, a feldolgozás a következő NSG fog folytatódni. A forgalmat engedélyező utolsó NSG naplózza a forgalmat a flow-naplókba.
- A NSG a Storage-fiókokba vannak írva, ahonnan elérhetők.
- A flow-naplókat exportálhatja, feldolgozhatja, elemezheti és megjelenítheti olyan eszközökkel, mint például a TA, a splunk, a Grafana, a Stealthwatch stb.

## <a name="log-format"></a>Napló formátuma

A flow-naplók a következő tulajdonságokat tartalmazzák:

* az esemény naplózásának **időpontja**
* **systemId** – hálózati biztonsági csoport rendszerazonosítója.
* **Kategória** – az esemény kategóriája. A kategória mindig **NetworkSecurityGroupFlowEvent**
* **ResourceId** – a NSG erőforrás-azonosítója
* **operationName** – mindig NetworkSecurityGroupFlowEvents
* **Tulajdonságok** – a folyamat tulajdonságainak gyűjteménye
    * A flow log esemény sémájának **verziószáma**
    * **flow** -folyamatok gyűjteménye. Ez a tulajdonság több bejegyzést tartalmaz a különböző szabályokhoz
        * **szabály** – a folyamatok listáját tartalmazó szabály
            * **folyamatok** – folyamatok gyűjteménye
                * **Mac** – a virtuális gép hálózati adapterének MAC-címe, ahová a folyamat begyűjtése történik
                * **flowTuples** – a flow-rekord több tulajdonságát tartalmazó sztring vesszővel tagolt formátumban
                    * **Időbélyegző – ez** az érték a folyamat UNIX EPOCH formátumban való előfordulásának időbélyegzője.
                    * **Forrás IP** -címe – a forrás IP-címe
                    * **Cél IP** -címe – a cél IP-címe
                    * **Forrásoldali port** – a forrásport
                    * **Célport** – a célport
                    * **Protokoll** – a folyamat protokollja. Az UDP esetében érvényes értékek: **T** , TCP és **U**
                    * **Forgalmi folyamat** – a forgalom iránya. Az érvényes értékek **a bejövő** és kimenő **kimeneti** értékekhez tartoznak.
                    * **Forgalmi döntés** – azt, hogy a forgalom engedélyezett vagy tiltott. **Az érvényes** értékek a megengedettek, a **D** megtagadva.
                    * **Flow State – csak 2-es verzió** – rögzíti a folyamat állapotát. A lehetséges állapotok a **B**: Begin, amikor létrejön egy folyamat. Nincs megadva statisztika. **C**: folyamatos folyamat folytatása. A statisztikák 5 perces időközönként vannak megadva. **E**: Befejezés, ha egy folyamat leáll. Statisztikákat biztosítunk.
                    * **Csomagok – forrás – cél – csak 2-es verzió** A forrásról a célhelyre küldött TCP-vagy UDP-csomagok teljes száma az utolsó frissítés óta.
                    * **Eljuttatott bájtok – forrás – cél – csak 2-es verzió** A forrás és a célhely között a legutóbbi frissítés óta küldött TCP-vagy UDP-csomagok teljes száma. A csomagok bájtjai a csomagok fejlécét és a hasznos adatokat tartalmazzák.
                    * **Csomagok – cél a forrás – csak 2-es verzió** Az utolsó frissítés óta a célhelyről a forrásba küldött TCP-vagy UDP-csomagok teljes száma.
                    * **Eljuttatott bájtok – cél – forrás – csak 2-es verzió** A célhelyről a forrásig a legutóbbi frissítés óta küldött TCP-és UDP-csomagok teljes száma. A csomagok bájtjai tartalmazzák a csomagok fejlécét és a hasznos adatokat.


**NSG flow-naplók 2. verzió (vs 1. verzió)** 

A naplók 2. verziója a folyamat állapotának fogalmát mutatja be. Beállíthatja, hogy a rendszer milyen verziójú flow-naplókat kapjon.

A " _B_ " folyamat a folyamat indításakor kerül rögzítésre. A _C_ és a _flow állapot olyan_ állapotú, amely a folyamat folytatását és a folyamat befejezését jelöli. A _C_ és az _E_ -Államok forgalmi sávszélességgel kapcsolatos információkat tartalmaznak.

### <a name="sample-log-records"></a>Minta napló rekordjai

Az alábbi szöveg egy folyamat naplóját szemlélteti. Ahogy láthatja, több rekord is van, amelyek követik az előző szakaszban ismertetett tulajdonságokat.

> [!NOTE]
> A *flowTuples* tulajdonság értékei vesszővel tagolt lista.
 
**1. verzió NSG folyamat naplójának formátuma minta**
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
**2. verzió NSG-folyamatábra-formátum minta**
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
**Naplózott rekord magyarázata**

![flow-naplók rekordja](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Minta sávszélességének kiszámítása**

A flow rekordok egy TCP-beszélgetésből 185.170.185.105:35370 és 10.2.0.4:23:

"1493763938, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, B,,,," "1493695838, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, C, 1021, 588096, 8005, 4610880" "1493696138, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, E, 52, 29952, 47, 27072"

A " _C_ " és a "End _E_ flow" állapot esetén a bájt és a csomagok száma az előző folyamat rekordjának időpontjától számított összesített szám. Az előző példában szereplő beszélgetésre hivatkozva az átvitt csomagok teljes száma 1021 + 52 + 8005 + 47 = 9125. Az átvitt bájtok teljes száma: 588096 + 29952 + 4610880 + 27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>NSG-folyamat-naplók engedélyezése

Használja az alábbi hivatkozásokat a flow-naplók engedélyezésére vonatkozó útmutatókhoz.

- [Azure Portal](./network-watcher-nsg-flow-logging-portal.md)
- [PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [Parancssori felület](./network-watcher-nsg-flow-logging-cli.md)
- [REST](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>Paraméterek frissítése

**Azure Portal**

A Azure Portal navigáljon a Network Watcher NSG flow-naplók szakaszához. Ezután kattintson a NSG nevére. Ekkor megjelenik a folyamat naplójának beállítások panelje. Módosítsa a kívánt paramétereket, és nyomja meg a **Mentés gombot** a módosítások telepítéséhez.

**PS/CLI/REST/ARM**

Ha parancssori eszközökkel szeretné frissíteni a paramétereket, használja ugyanazt a parancsot, amely a flow-naplók engedélyezésére szolgál (a fentiekben), de a frissített paraméterekkel, amelyeket módosítani szeretne.

## <a name="working-with-flow-logs"></a>A flow-naplók használata

*Adatforgalmi naplók olvasása és exportálása*

- [Letöltési &amp; folyamat naplóinak letöltése a portálról](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [Flow-naplók beolvasása a PowerShell-függvények használatával](./network-watcher-read-nsg-flow-logs.md)
- [NSG adatfolyam-naplók exportálása a splunk-be](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

A flow naplózza a célként megadott NSG, de nem ugyanazokat a naplókat jeleníti meg. A flow-naplókat a rendszer csak egy Storage-fiókon belül tárolja, és az alábbi példában látható naplózási útvonalat követi:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Folyamatok naplófájljainak megjelenítése*

- Az [Azure Traffic Analytics](./traffic-analytics.md) egy natív Azure-szolgáltatás, amely a flow-naplók feldolgozására, az elemzések kinyerésére és a flow-naplók megjelenítésére szolgál. 
- [Oktatóanyag NSG-naplók megjelenítése Power BI](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Oktatóanyag NSG-naplók megjelenítése rugalmas veremmel](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Oktatóanyag NSG-folyamatok naplófájljainak kezelése és elemzése a Grafana használatával](./network-watcher-nsg-grafana.md)
- [Oktatóanyag NSG-folyamatok naplófájljainak kezelése és elemzése a Graylog használatával](./network-watcher-analyze-nsg-flow-logs-graylog.md)


## <a name="nsg-flow-logging-considerations"></a>NSG-folyamatok naplózási szempontjai

A **Storage-fiókkal kapcsolatos megfontolások**: 

- Hely: a használt Storage-fióknak ugyanabban a régióban kell lennie, mint a NSG.
- Teljesítményi szint: jelenleg csak a standard szintű Storage-fiókok támogatottak.
- A kulcs önálló kezelése: Ha módosítja vagy elforgatja a hozzáférési kulcsokat a Storage-fiókhoz, a NSG-naplók nem fognak működni. A probléma megoldásához le kell tiltania, majd újra engedélyeznie kell a NSG folyamat naplóit.

A **flow naplózási költségei**: a NSG folyamatának naplózása a létrehozott naplók mennyiségétől függ. A nagy forgalmú kötetek nagy flow-naplózási kötetet és a hozzájuk kapcsolódó költségeket okozhatják. A NSG-forgalmi napló díjszabása nem tartalmazza a tárterület alapjául szolgáló költségeket. Az adatmegőrzési házirend szolgáltatás NSG flow-naplózással való használata esetén a tárolási költségek hosszabb ideig tartanak. Ha örökre meg szeretné őrizni az adatmegőrzési szabályzatot, és nem szeretné alkalmazni az adatmegőrzési szabályt, állítsa a megőrzés (nap) értéket 0-ra. További információkért tekintse meg a [Network Watcher díjszabását](https://azure.microsoft.com/pricing/details/network-watcher/) és az [Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/) ismertető témakört.

**A felhasználó által megadott bejövő TCP-szabályokkal kapcsolatos problémák**: a [hálózati biztonsági csoportok (NSG-EK) állapot-](../virtual-network/network-security-groups-overview.md) [nyilvántartó tűzfalként](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true)vannak implementálva. Azonban a platform jelenlegi korlátai miatt a bejövő TCP-forgalmat befolyásoló felhasználó által definiált szabályok állapot nélküli módon lesznek implementálva. Ennek következtében a felhasználó által definiált Bejövő szabályok által érintett folyamatok nem állnak le. Ezen folyamatok esetében a bájtok és a csomagok száma nem kerül rögzítésre. Következésképpen a NSG-naplók (és Traffic Analytics) által jelentett bájtok és csomagok száma nem lehet azonos a tényleges számokkal. Az ezeket a problémákat kijavító opt-in jelzőt a rendszer a legutóbbi március 2021-as verzióra ütemezi. Ebben az esetben az ilyen viselkedés miatt jelentős problémákkal szembesülő ügyfelek támogatást kérhetnek a támogatási szolgálattól, ha Network Watcher > NSG-adatforgalmi naplóban szeretne támogatási kérelmet készíteni.  

Az internetes IP-címekről a nyilvános IP-címek **nélküli virtuális gépekre naplózott bejövő folyamatok**: olyan virtuális gépek, amelyek nem rendelkeznek nyilvános IP-címmel a hálózati adapterhez társított nyilvános IP-címen keresztül, vagy amelyek egy alapszintű terheléselosztó-készlet részét képezik, az [alapértelmezett SNAT](../load-balancer/load-balancer-outbound-connections.md) használják, és az Azure által hozzárendelt IP-címmel rendelkeznek a kimenő kapcsolatok megkönnyítéséhez. Ennek eredményeképpen előfordulhat, hogy az internetes IP-címekről érkező adatfolyamok esetében a flow-naplóbejegyzések megjelennek, ha a folyamat a SNAT hozzárendelt portok tartományában lévő portra van szánva. Amíg az Azure nem engedélyezi ezeket a folyamatokat a virtuális gép számára, a rendszer naplózza a kísérletet, és a Network Watcher NSG flow-naplójában jelenik meg. Javasoljuk, hogy a nem kívánt bejövő internetes forgalmat explicit módon tiltsa le a NSG.

**Probléma a Application Gateway v2 alhálózati NSG**: az Application Gateway v2 alhálózat NSG jelenleg [nem támogatott](../application-gateway/application-gateway-faq.yml#are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet) a flow naplózása. Ez a probléma nem érinti a Application Gateway v1-es verzióját.

Nem **kompatibilis szolgáltatások**: az aktuális NSG miatt az Azure-szolgáltatások egy kis készlete nem támogatott a folyamat naplófájljaiban. A nem kompatibilis szolgáltatások aktuális listája
- [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>Ajánlott eljárások

**Engedélyezés a kritikus virtuális hálózatok/alhálózatokon**: a flow-naplókat az előfizetés minden kritikus virtuális hálózatok/alhálózatán engedélyezni kell, mint a naplózási és biztonsági ajánlott eljárás. 

**NSG-naplózás engedélyezése az erőforráshoz csatolt összes NSG**: az Azure-ban a flow naplózása a NSG-erőforráson van konfigurálva. Egy folyamat csak egyetlen NSG-szabályhoz lesz társítva. Az olyan forgatókönyvekben, ahol több NSG van használatban, javasoljuk, hogy engedélyezze a NSG flow-naplókat az erőforrás alhálózatán vagy hálózati adapterén alkalmazott összes NSG az összes forgalom rögzítésének biztosítása érdekében. További információ: a hálózati biztonsági csoportokban lévő [forgalom kiértékelésének módja](../virtual-network/network-security-group-how-it-works.md) . 

Néhány gyakori forgatókönyv:
1. **Több hálózati adapter egy** virtuális gépen: abban az esetben, ha több hálózati adapter van csatlakoztatva egy virtuális géphez, a folyamat naplózását engedélyezni kell mindegyiken
1. A **hálózati adapteren és az alhálózaton is NSG**: abban az esetben, ha a NSG a hálózati adapteren, valamint az alhálózati szinten van konfigurálva, akkor a folyamat naplózását mindkét NSG engedélyezni kell. 

**Tárolási kiépítés**: a tárterületet a várt flow-naplózási kötetnek megfelelően kell kiépíteni.

**Elnevezés**: a NSG nevének upto 80 karakterből kell állnia, és a NSG-szabályok neve upto 65 karakter. Ha a nevek meghaladják a karakteres korlátot, akkor a naplózás során lerövidítheti azt.

## <a name="troubleshooting-common-issues"></a>Gyakori problémák megoldása

**Nem tudtam engedélyezni az NSG-forgalom naplóit**

- A **Microsoft. ininsights** erőforrás-szolgáltató nincs regisztrálva

Ha _AuthorizationFailed_ vagy _GatewayAuthenticationFailed_ hibát kapott, lehet, hogy nem engedélyezte a Microsoft Insights erőforrás-szolgáltatóját az előfizetésében. Az [utasításokat követve](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider) engedélyezze a Microsoft bepillantást nyújtó szolgáltatót.

**Engedélyeztem az NSG-forgalom naplóit, de nem látok adatokat a tárfiókomban**

- **A telepítés időtartama**

Akár 5 percet is igénybe vehet, hogy az NSG-forgalom naplói megjelenjenek a tárfiókjában (megfelelő konfiguráció esetén). Egy PTH1.json fájl fog megjelenni, amely [az itt leírtak szerint](./network-watcher-nsg-flow-logging-portal.md#download-flow-log) érhető el.

- **Nincs forgalom az NSG-kben**

Néha nem fog naplókat látni, mert a virtuális gépek nem aktívak, vagy egy alkalmazásátjárón vagy más eszközön olyan felsőbb rétegbeli szűrők találhatók, amelyek blokkolják a forgalmat az NSG-k felé.

**Automatizálni szeretném az NSG-forgalom naplóit**

Az ARM-sablonokkal történő automatizálás jelenleg nem érhető el az NSG-forgalom naplói esetében. További információért olvassa el a [funkció bejelentését](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/) .

## <a name="faq"></a>GYIK

**Mit jelentenek a NSG flow-naplók?**

Az Azure hálózati erőforrásait [hálózati biztonsági csoportok (NSG-EK)](../virtual-network/network-security-groups-overview.md)segítségével lehet egyesíteni és felügyelni. A NSG flow-naplók lehetővé teszik az 5 rekordos adatfolyamok naplózását a NSG keresztüli összes forgalomról. A nyers flow-naplók egy Azure Storage-fiókba íródnak, ahonnan szükség szerint további feldolgozásra, elemzésre, lekérdezésre vagy exportálásra kerülhet sor.

**A flow-naplók használata hatással van a hálózati késésre vagy a teljesítményre?**

A rendszer a hálózati forgalom elérési útján kívül gyűjti az adatokat, így nem befolyásolja a hálózat átviteli sebességét vagy késését. A folyamat naplóit a hálózati teljesítményre gyakorolt hatás kockázata nélkül hozhatja létre vagy törölheti.

**Hogyan használja a NSG a tűzfal mögötti Storage-fiókkal?**

Ha tűzfal mögötti Storage-fiókot szeretne használni, meg kell adnia egy kivételt a megbízható Microsoft-szolgáltatások számára a Storage-fiókhoz való hozzáféréshez:

- Navigáljon a Storage-fiókhoz úgy, hogy beírja a Storage-fiók nevét a portálon vagy a [Storage-fiókok lapon](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) található globális Keresés mezőbe.
- A **Beállítások** szakaszban válassza a **tűzfalak és virtuális hálózatok** lehetőséget.
- A **hozzáférés engedélyezése lehetőségnél** válassza a  **kiválasztott hálózatok** elemet. Ezután a  **kivételek** területen jelölje be a * * * * melletti jelölőnégyzetet, hogy a megbízható Microsoft-szolgáltatások hozzáférjenek ehhez a Storage-fiókhoz * * * *
- Ha ez a beállítás már ki lett választva, nincs szükség módosításra.
- Keresse meg a cél NSG a [NSG flow naplói – áttekintés oldalon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) , és engedélyezze a NSG-folyamat naplóit a fenti Storage-fiók kiválasztásával.

Pár perc elteltével ellenőrizheti a tárnaplókat – egy frissített időbélyeget vagy egy újonnan létrehozott JSON-fájlt kell látnia.

**Hogyan használja a NSG a szolgáltatási végpont mögötti Storage-fiókkal?**

A NSG-folyamatok további konfigurációk nélkül kompatibilisek a szolgáltatási végpontokkal. Tekintse meg a szolgáltatás-végpontok virtuális hálózatban való [engedélyezésével foglalkozó oktatóanyagot](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) .

**Mi a különbség a flow-naplók között 1 & 2 verzió között?**

A flow-naplók 2. verziója bevezeti a _folyamat állapotának_ fogalmát & tárolja a bájtok és a továbbított csomagok adatait. [További információ](#log-format)

## <a name="pricing"></a>Díjszabás

A NSG flow-naplók díja GB-onként történik, és egy előfizetés után 5 GB/hó ingyenes szintet kell fizetni. A régió aktuális díjszabását a [Network Watcher díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/network-watcher/)tekintheti meg.

A naplók tárolása külön díjat számítunk fel, lásd az [Azure Storage blokk blob díjszabási lapját](https://azure.microsoft.com/pricing/details/storage/blobs/) a megfelelő árakért.