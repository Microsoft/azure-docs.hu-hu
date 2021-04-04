---
title: Virtuális gép bejövő és kimenő forgalmának naplózása – oktatóanyag – Azure Portal | Microsoft Docs
description: Megismerheti, hogyan naplózhatja egy virtuális gép bejövő és kimenő hálózati forgalmát a Network Watcher NSG-folyamatnaplózási funkciójának használatával.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
Customer intent: I need to log the network traffic to and from a VM so I can analyze it for anomalies.
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 385d43e46cd3f9465c0fbf9a02eeae356f48fac4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966530"
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>Oktatóanyag: Virtuális gép bejövő és kimenő hálózati forgalmának naplózása az Azure Portal használatával

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)

A hálózati biztonsági csoportok (NSG-k) lehetővé teszik a virtuális gépek bejövő és kimenő forgalmának szűrését. A Network Watcher NSG-folyamatnaplózási funkciójával naplózhatja az egyes hálózati biztonsági csoportokon áthaladó hálózati forgalmat. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hálózati biztonsági csoporttal rendelkező virtuális gép létrehozása
> * A Network Watcher engedélyezése és a Microsoft.Insights szolgáltató regisztrálása
> * Forgalom naplózásának engedélyezése egy hálózati biztonsági csoport esetében a Network Watcher NSG-folyamatnaplózási funkciójának használatával
> * Naplózott adatok letöltése
> * Naplózott adatok megtekintése

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Az Azure Portal bal felső sarkában kattintson az **+ Erőforrás létrehozása** gombra.
2. Válassza a **számítás** lehetőséget, majd válassza a **Windows Server 2016 Datacenter** vagy az **Ubuntu Server** verzióját.
3. Adja meg vagy válassza ki a következő adatokat, fogadja el a többi beállítás alapértelmezett értékeit, majd kattintson az **OK gombra**:

    |Beállítás|Érték|
    |---|---|
    |Név|myVM|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza az **Új létrehozása** elemet, és adja meg a **myResourceGroup** nevet.|
    |Hely| Válassza ki az **USA keleti** régióját|

4. Válassza ki a virtuális gép méretét, majd kattintson a **Kiválasztás** gombra.
5. A **Beállítások** területen fogadja el az összes alapértelmezett beállítást, majd kattintson az **OK** gombra.
6. Az **Összegzés** lap **Létrehozás** területén kattintson a **Létrehozás** elemre a virtuális gép üzembe helyezésének megkezdéséhez. A virtuális gép üzembe helyezése néhány percet vesz igénybe. Várjon, amíg a virtuális gép üzembe helyezése befejeződik, mielőtt folytatná a további lépésekkel.

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne ugorjon a további lépésekre, amíg a virtuális gép létrehozása be nem fejeződött. Miközben a portál létrehozza a virtuális gépet, egy hálózati biztonsági csoportot is létrehoz **myVm-nsg** néven, és társítja azt a virtuális gép hálózati adapteréhez.

## <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Ha már engedélyezve van a Network Watcher az USA keleti régiójában, folytassa az [Insights-szolgáltató regisztrálása](#register-insights-provider) szakasszal.

1. A portálon válassza a **Minden szolgáltatás** lehetőséget. A **Szűrő** mezőbe írja be a *Network Watcher* kifejezést. Ha **Network Watcher** jelenik meg az eredmények között, válassza ki.
2. Válassza a **Régiók** lehetőséget a kibontáshoz, majd válassza a **...** jelet az **USA keleti régiója** melletti jobb oldalon, az alábbi ábrán látható módon:

    ![A Network Watcher engedélyezése](./media/network-watcher-nsg-flow-logging-portal/enable-network-watcher.png)

3. Válassza a **Network Watcher engedélyezése** lehetőséget.

## <a name="register-insights-provider"></a>Insights-szolgáltató regisztrálása

Az NSG-folyamatnaplózáshoz a **Microsoft.insights** szolgáltató szükséges. A szolgáltató regisztrálásához hajtsa végre a következő lépéseket:

1. Válassza a portál bal felső sarkában található **Minden szolgáltatás** lehetőséget. A Szűrő mezőbe írja be az *Előfizetések* kifejezést. Amikor az **Előfizetések** elem megjelenik a keresési eredmények között, válassza ki.
2. Az előfizetések listájából válassza ki azt, amelyik esetében engedélyezni szeretné a szolgáltatót.
3. A **BEÁLLÍTÁSOK** területen válassza az **Erőforrás-szolgáltatók** lehetőséget.
4. Ellenőrizze, hogy a **microsoft.insighs** szolgáltató **ÁLLAPOTA****Regisztrálva**, ahogyan az az alábbi képen is látható. Ha az állapot **Nem regisztrált**, válassza a szolgáltatótól jobbra található **Regisztrálás** lehetőséget.

    ![Szolgáltató regisztrálása](./media/network-watcher-nsg-flow-logging-portal/register-provider.png)

## <a name="enable-nsg-flow-log"></a>NSG-folyamatnapló engedélyezése

1. A rendszer az NSG-folyamatnapló adatait egy Azure Storage-fiókba fogja írni. Azure Storage-fiók létrehozásához válassza a portál bal felső sarkában található **+ Erőforrás létrehozása** elemet.
2. Válassza a **Storage** elemet, majd a **Tárfiók – blob, fájl, tábla, üzenetsor** lehetőséget.
3. Adja meg vagy válassza ki a következő adatokat, fogadja el a többi alapértelmezett értéket, majd válassza a **Létrehozás** lehetőséget.

    | Beállítás        | Érték                                                        |
    | ---            | ---   |
    | Név           | A hossza 3–24 karakter lehet, kizárólag kisbetűket és számokat tartalmazhat, és egyedinek kell lennie az összes Azure Storage-fiókban.                                                               |
    | Hely       | Válassza ki az **USA keleti** régióját                                           |
    | Erőforráscsoport | Válassza a **meglévő használata** lehetőséget, majd a **myResourceGroup** lehetőséget. |

    A Storage-fiók létrehozása nagyjából egy percet vesz igénybe. Ne folytassa a további lépésekkel, amíg a tárfiók létrehozása be nem fejeződött. A Storage-fióknak minden esetben ugyanabban a régióban kell lennie, mint a NSG.
4. Válassza a portál bal felső sarkában található **Minden szolgáltatás** lehetőséget. A **Szűrő** mezőbe írja be a *Network Watcher* kifejezést. Amikor a **Network Watcher** elem megjelenik a keresési eredmények között, válassza ki.
5. A **NAPLÓK** területen válassza az **NSG-folyamatnaplók** lehetőséget, ahogyan az a következő képen látható:

    ![A képernyőképen látható a Network Watcher NSG.](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. A hálózati biztonsági csoportok közül válassza a **myVm-nsg** nevű csoportot.
7. A **Folyamatnaplók beállításai** területen válassza a **Be** lehetőséget.
8. Válassza ki a folyamat naplózásának verzióját. A 2. verzió folyamat-munkameneti statisztikát tartalmaz (bájtok és csomagok)

   ![Flow-naplók verziójának kiválasztása](./media/network-watcher-nsg-flow-logging-portal/select-flow-log-version.png)

9. Válassza ki a 3. lépésben létrehozott Storage-fiókot.
   > [!NOTE]
   > A NSG-folyamatok naplói nem működnek olyan Storage-fiókokkal, amelyeken engedélyezve van a [hierarchikus névtér](../storage/blobs/data-lake-storage-namespace.md) .
1. Válassza a portál bal felső sarkában található **Minden szolgáltatás** lehetőséget. A **Szűrő** mezőbe írja be a *Network Watcher* kifejezést. Amikor a **Network Watcher** elem megjelenik a keresési eredmények között, válassza ki.
10. A **Megőrzés (nap)** beállítást állítsa 5 értékre, majd válassza a **Mentés** lehetőséget.

## <a name="download-flow-log"></a>Folyamatnapló letöltése

1. A portálon, a Network Watcherben válassza az **NSG-folyamatnaplók** lehetőséget a **NAPLÓK** területen.
2. Válassza **A folyamatnaplókat a konfigurált tárfiókokból töltheti le** lehetőséget, ahogyan az az alábbi képen is látható:

   ![Folyamatnaplók letöltése](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. Válassza ki az [NSG-folyamatnapló engedélyezése](#enable-nsg-flow-log) szakasz 2. lépésében konfigurált Storage-fiókot.
4. A **blob Service** területen válassza a **tárolók** lehetőséget, majd válassza ki az elemzések **– naplók-networksecuritygroupflowevent** tárolót.
5. A tárolóban navigáljon a mappa-hierarchiába, amíg nem kap egy PT1H.jsa fájlban, ahogy az a következő képen látható. A naplófájlokat a rendszer a következő elnevezési konvenciót követő mappa-hierarchiába írja: https://{storageAccountName}. blob. Core. Windows. net/elemzések-naplók-networksecuritygroupflowevent/resourceId =/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y = {Year}/m = {hónap}/d = {Day}/h = {Hour}/m = 00/macAddress = {macAddress}/PT1H.js

   ![A folyamat naplója](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

6. Válassza a PT1H.json fájl jobb oldalán található **...** elemet, majd a **Letöltés** lehetőséget.

## <a name="view-flow-log"></a>Folyamatnapló megtekintése

Az alábbi json-fájl egy példa arra, mi látható majd a PT1H.json fájlban az egyes folyamatok esetén, amelyekhez adatokat naplóz a rendszer:

### <a name="version-1-flow-log-event"></a>1-es verziójú flow-napló esemény
```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [
            {
                "rule": "UserRule_default-allow-rdp",
                "flows": [
                    {
                        "mac": "000D3A170C69",
                        "flowTuples": [
                            "1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"
                        ]
                    }
                ]
            }
        ]
    }
}
```
### <a name="version-2-flow-log-event"></a>2. verziójú flow napló esemény
```json
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
}
```

A **mac** érték az előző kimenetben azon hálózati adapter MAC-címét jelöli, amely a virtuális gép létrehozásakor lett létrehozva. A **flowTuples** vesszővel tagolt adatai a következők:

| Példaadatok | Az adatok jelentése   | Magyarázat                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1542110377   | Időbélyeg             | Az az időpont, amikor a forgalom jelentkezett, UNIX EPOCH formátumban. Az előzőben példában látható dátum átalakítva: 2018. május 1. 14:59:05 GMT.                                                                                    |
| 10.0.0.4  | Forrás IP-címe      | A forrás IP-cím, ahonnan a forgalom érkezett. A 10.0.0.4 a [Virtuális gép létrehozása](#create-a-vm) szakaszban létrehozott virtuális gép magánhálózati IP-címe.
| 13.67.143.118     | Cél IP-cím | A cél IP-cím, ahová a forgalom tartott.                                                                                  |
| 44931        | Forrásport            | A forrásport, ahonnan a forgalom érkezett.                                           |
| 443         | Célport       | A célport, ahová a forgalom tartott. Mivel a forgalom a 443-as portra lett szánva, az **UserRule_default-Allow-RDP** nevű szabály a naplófájlban feldolgozta a folyamatot.                                                |
| T            | Protokoll               | Azt jelöli, hogy a forgalom protokollja TCP (T) vagy UDP (U) volt-e.                                  |
| O            | Irány              | Azt jelöli, hogy a forgalom bejövő (I) vagy kimenő (O) volt-e.                                     |
| A            | Művelet                 | Azt jelöli, hogy a forgalom engedélyezve (A) vagy elutasítva (D) lett-e.  
| C            | Csak a flow állapota **2. verzió** | Rögzíti a folyamat állapotát. A lehetséges állapotok a **B**: Begin, amikor létrejön egy folyamat. Nincs megadva statisztika. **C**: folyamatos folyamat folytatása. A statisztikák 5 perces időközönként vannak megadva. **E**: Befejezés, ha egy folyamat leáll. Statisztikákat biztosítunk. |
| 30 | Küldött csomagok – a forrás célhelye **csak a 2. verzióra** | A forrásról a célhelyre küldött TCP-vagy UDP-csomagok teljes száma az utolsó frissítés óta. |
| 16978 | Küldési sebesség (bájt) – a forrás és a cél **2-es verziója csak** | A forrás és a célhely között a legutóbbi frissítés óta küldött TCP-vagy UDP-csomagok teljes száma. A csomagok bájtjai a csomagok fejlécét és a hasznos adatokat tartalmazzák. |
| 24 | Küldött csomagok – célhely a forrás **2-es verzióra** | Az utolsó frissítés óta a célhelyről a forrásba küldött TCP-vagy UDP-csomagok teljes száma. |
| 14008| Eljuttatott bájtok – célhely a forrás **2-es verzióra** | A célhelyről a forrásig a legutóbbi frissítés óta küldött TCP-és UDP-csomagok teljes száma. A csomagok bájtjai tartalmazzák a csomagok fejlécét és a hasznos adatokat.|

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan engedélyezhető az NSG-folyamatnaplózás egy hálózati biztonsági csoport esetében. Azt is megismerhette, hogyan töltheti le és tekintheti meg a fájlokban naplózott adatokat. A json-fájlban található nyers adatok értelmezése kihívást jelenthet. A flow-naplók megjelenítéséhez használhatja az [Azure Traffic Analytics](traffic-analytics.md), a [Microsoft Power bi](network-watcher-visualize-nsg-flow-logs-power-bi.md)és más eszközöket. Alternatív módszereket is kipróbálhat a NSG flow-naplók, például a [PowerShell](network-watcher-nsg-flow-logging-powershell.md), az [Azure CLI](network-watcher-nsg-flow-logging-cli.md), a [REST API](network-watcher-nsg-flow-logging-rest.md) és az [ARM-sablonok](network-watcher-nsg-flow-logging-azure-resource-manager.md)engedélyezéséhez.