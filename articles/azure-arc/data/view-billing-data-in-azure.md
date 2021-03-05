---
title: Számlázási adatok feltöltése az Azure-ba, és megtekintés a Azure Portal
description: Számlázási adatok feltöltése az Azure-ba, és megtekintés a Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 7ef1cd43d2efbc5ab92cc2b4cba4d237805d8921
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202654"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>Számlázási adatok feltöltése az Azure-ba, és megtekintés a Azure Portal

> [!IMPORTANT] 
>  Az előzetes verzió ideje alatt az Azure arc-kompatibilis adatszolgáltatások használata díjmentes. Bár a számlázási rendszer befejezi a teljes számlázási mérőszámot, a $0 értékre van állítva.  Ha ezt a forgatókönyvet követi, a számlázási adatokat a jelenleg **hibrid adatszolgáltatások** nevű szolgáltatáshoz, illetve a **Microsoft. AzureArcData/ `<resource type>`**. nevű típusú erőforrásokhoz tartozó adatokat fogja látni. Ekkor megtekintheti az egyes adatszolgáltatások rekordjait – az Azure-ívet, amelyet Ön hoz létre, de minden egyes rekordot $0-ra számlázunk.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes---implications-for-billing-data"></a>Csatlakozási módok – a számlázási információk következményei

A jövőben két mód lesz az Azure arc-kompatibilis adatszolgáltatások futtatására:

- **Közvetett** csatlakozás – nincs közvetlen kapcsolat az Azure-hoz. Az adatok csak az exportálási/feltöltési folyamaton keresztül érkeznek az Azure-ba. Az Azure arc-adatszolgáltatások összes központi telepítése ebben a módban jelenleg előzetes verzióban működik.
- **Közvetlen csatlakozás** – ebben a módban az Azure arc-kompatibilis Kubernetes szolgáltatástól függ, hogy közvetlen kapcsolatot biztosít az Azure és a Kubernetes-fürt között, amelyen az Azure arc-kompatibilis adatszolgáltatások futnak. Ez lehetővé teszi a további képességeket, és lehetővé teszi, hogy a Azure Portal és az Azure CLI-vel kezelje az Azure arc-kompatibilis adatszolgáltatásait ugyanúgy, mint az Azure-beli adatszolgáltatások kezelését.  Ez a csatlakozási mód még nem érhető el előzetes verzióban, de hamarosan elérhető lesz.

További információt a [kapcsolati módok](./connectivity.md)közötti különbségről itt olvashat.

A közvetve csatlakoztatott módban a számlázási adatmennyiséget az Azure arc-adatvezérlőből egy biztonságos fájlba exportálja a rendszer, majd feltölti az Azure-ba, majd feldolgozza azokat.  A közelgő közvetlen csatlakozású módban a számlázási adatait körülbelül 1/óra múlva automatikusan elküldi az Azure-ba, hogy közel valós időben megtekintse a szolgáltatások költségeit. A közvetetten csatlakoztatott módban tárolt adatexportálás és-feltöltés folyamata parancsfájlok használatával is automatizálható, vagy létrehozhatunk egy olyan szolgáltatást, amely elvégzi Önt.

## <a name="upload-billing-data-to-azure"></a>Számlázási adatok feltöltése az Azure-ba

A számlázási adatok Azure-ba való feltöltéséhez először a következők történnek:

1. Hozzon létre egy Azure arc-kompatibilis adatszolgáltatást, ha még nem rendelkezik ilyennel. Például hozzon létre egyet a következők közül:
   - [Azure SQL felügyelt példány létrehozása az Azure arc-ban](create-sql-managed-instance.md)
   - [Azure Arc-kompatibilis, rugalmas skálázású PostgreSQL-kiszolgálócsoport létrehozása](create-postgresql-hyperscale-server-group.md)
1. [Az erőforrás-leltár, a használati adatok, a metrikák és a naplók feltöltésével Azure monitor](upload-metrics-and-logs-to-azure-monitor.md) , ha még nem tette meg.
1. Várjon legalább 2 órát az adatszolgáltatás létrehozása óta, hogy a számlázási telemetria gyűjtési folyamata egyes számlázási adatokat is gyűjtsön.

Futtassa a következő parancsot a számlázási információk exportálásához:

```console
azdata arc dc export -t usage -p usage.json
```

Egyelőre a fájl nincs titkosítva, hogy láthassa a tartalmat. Nyugodtan megnyithat egy szövegszerkesztőben, és megnézheti a tartalom megjelenését.

Megfigyelheti, hogy két adathalmaz létezik: `resources` és `data` . Az `resources` adatkezelő, a PostgreSQL nagy kapacitású-kiszolgálócsoportok és az SQL felügyelt példányai. Az `resources` adatokban szereplő rekordok rögzítik az erőforrások előzményeiben lévő, a létrehozáskor, a frissítéskor és a törléskor érintett eseményeket. A `data` rekordok rögzítik, hogy egy adott példány hány magot tudott használni minden órában.

Egy bejegyzés példája `resource` :

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

Egy bejegyzés példája `data` :

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

Futtassa a következő parancsot a usage.jsfájlba való feltöltéséhez az Azure-ba:

```console
azdata arc dc upload -p usage.json
```

## <a name="view-billing-data-in-azure-portal"></a>Számlázási információk megtekintése Azure Portal

A következő lépésekkel tekintheti meg a számlázási adatAzure Portalt:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. A képernyő felső részén található keresőmezőbe **Cost Management** majd kattintson a Cost Management szolgáltatásra.
1. A **Cost Management áttekintés** alatt kattintson a **Cost Management** fülre.
1. Kattintson a bal oldali **Cost Analysis** lapra.
1. A nézet tetején kattintson a **Cost by Resource (erőforrás-ellenőrzés** ) gombra.
1. Győződjön meg arról, hogy a hatóköre arra az előfizetésre van beállítva, amelyben az adatszolgáltatási erőforrásokat létrehozták.
1. A nézet felső részén a hatókör-választó melletti legördülő listában válassza a **Cost by Resource (erőforrás-erőforrások** ) lehetőséget.
1. Győződjön meg arról, hogy a Dátum szűrő az adott hónapra van beállítva, vagy egy másik időtartomány, amely az adatszolgáltatási erőforrások létrehozásakor **megjelenő** időzítést biztosítja.
1. Kattintson a **szűrő hozzáadása** lehetőségre, ha **erőforrás-típussal** szeretne szűrőt felvenni,  =  `Microsoft.AzureArcData/<data service type>` Ha csak egy Azure arc-kompatibilis adatszolgáltatásra kíván szűrni.
1. Ekkor megjelenik a létrehozott és az Azure-ba feltöltött összes erőforrás listája. Mivel a számlázási mérőszám $0, látni fogja, hogy a Cost mindig $0.

## <a name="download-billing-data"></a>Számlázási információk letöltése

A számlázási összesítő adatok közvetlenül a Azure Portal tölthetők le.

1. Ugyanebben a **Cost Analysis-> az erőforrástípus nézet szerint** , amelyet a fenti utasítások követésével elért, kattintson a felül található Letöltés gombra.
1. Válassza ki a letöltött fájl típusát – Excel vagy CSV –, és kattintson az **adatletöltés** gombra.
1. Nyissa meg a fájlt egy megfelelő szerkesztőben a kiválasztott fájltípus miatt.

## <a name="export-billing-data"></a>Számlázási információk exportálása

A számlázási exportálási feladatok létrehozásával rendszeres időközönként automatikusan exportálhatja a **részletes** használati és számlázási adatokat egy Azure Storage-tárolóba. Ez akkor hasznos, ha meg szeretné tekinteni a számlázás részleteit, például azt, hogy egy adott példány hány órán belül lett számlázva a számlázási időszakban.

Az alábbi lépéseket követve állíthatja be a számlázási exportálási feladatokat:

1. Kattintson a bal oldali **Exportálás** elemre.
1. Kattintson a **Hozzáadás** parancsra.
1. Adja meg a név és az Exportálás gyakoriságát, majd kattintson a Tovább gombra.
1. Hozzon létre egy új Storage-fiókot, vagy használjon egy meglévőt, és töltse ki az űrlapot a számlázási adatfájlok exportálásához a Storage-fiók, a tároló és a könyvtár elérési útjának megadásához, és kattintson a Tovább gombra.
1. Kattintson a **Létrehozás** lehetőségre.

A számlázási adatok exportálási fájljai körülbelül 4 órán belül elérhetők lesznek, és a számlázási exportálási feladatok létrehozásakor megadott ütemterv szerint lesznek exportálva.

Az exportált számlázási adatfájlok megtekintéséhez kövesse az alábbi lépéseket:

A Azure Portalban ellenőrizheti a számlázási adatfájlokat. 

> [!IMPORTANT]
> Miután létrehozta a számlázási exportálási feladatot, várjon 4 órát, mielőtt folytatná a következő lépéseket.

1. A portál felső részén található keresőmezőbe írja be a **Storage-fiókok** kifejezést, majd kattintson a **Storage-fiókok** elemre.
3. Kattintson arra a Storage-fiókra, amelyet a fenti számlázási exportálási feladatok létrehozásakor adott meg.
4. Kattintson a bal oldalon található tárolók elemre.
5. Kattintson arra a tárolóra, amelyet a fenti számlázási exportálási feladatok létrehozásakor adott meg.
6. Kattintson arra a mappára, amelyet a fenti számlázási exportálási feladatok létrehozásakor adott meg.
7. Bontsa ki a létrehozott mappákat és fájlokat, majd kattintson az egyik létrehozott. csv fájlra.
8. Kattintson a **Letöltés** gombra, amely menti a fájlt a helyi letöltések mappájába.
9. Nyissa meg a fájlt egy. csv-fájl megjelenítővel, például az Excel használatával.
10. Szűrje az eredményeket úgy, hogy csak az **Erőforrás típusú** sorok jelenjenek meg  =  `Microsoft.AzureArcData/<data service resource type` .
11. Látni fogja, hogy hány óra elteltével használták a rendszer a példányt a UsageQuantity oszlop aktuális 24 órában.
