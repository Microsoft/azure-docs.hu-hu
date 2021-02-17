---
title: Metrikák feltöltése a Azure Monitorba
description: Az Azure arc-kompatibilis adatszolgáltatások metrikáinak feltöltése Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: d7c611f1cdb5e3294e38f87c0534003813e50388
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575690"
---
# <a name="upload-metrics-to-azure-monitor"></a>Metrikák feltöltése a Azure Monitorba

Rendszeresen exportálhatja a figyelési metrikákat, majd feltöltheti őket az Azure-ba. Az adatok exportálása és feltöltése szintén létrehozza és frissíti az adatkezelőt, az SQL felügyelt példányát és a PostgreSQL nagy kapacitású Server Group erőforrásait az Azure-ban.

> [!NOTE] 
> Az előzetes verzió ideje alatt az Azure arc-kompatibilis adatszolgáltatások használata díjmentes.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt győződjön meg arról, hogy létrehozta a szükséges szolgáltatásnevet, és hozzárendelte azt egy megfelelő szerepkörhöz. Részletes információ:
* [Egyszerű szolgáltatásnév létrehozása](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Szerepkörök társítása az egyszerű szolgáltatáshoz](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>Metrikák feltöltése

Az Azure arc-adatszolgáltatások használatával feltölthet Azure Monitor mérőszámokat, így összesítheti és elemezheti a metrikákat, riasztásokat küldhet, értesítéseket küldhet, vagy automatizált műveleteket indíthat el. 

Az adatoknak a Azure Monitorba való küldése lehetővé teszi, hogy a metrikák adatait a helyszínen és a nagy léptékben tárolja, lehetővé téve az adatok hosszú távú tárolását a speciális elemzésekhez.

Ha több olyan hellyel rendelkezik, amelyeken Azure arc-adatszolgáltatások vannak, akkor a Azure Monitor központi helyként használhatja a naplók és metrikák összegyűjtéséhez a különböző webhelyeken.

## <a name="set-final-environment-variables-and-confirm"></a>Állítsa be a végső környezeti változókat, és erősítse meg

Az SPN-szolgáltató URL-címének beállítása környezeti változóban:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

Győződjön meg arról, hogy az összes szükséges környezeti változó be van állítva, ha szeretné:


::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

::: zone pivot="client-operating-system-windows-command"

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

## <a name="upload-metrics-to-azure-monitor"></a>Metrikák feltöltése a Azure Monitorba

Az Azure arc használatára képes SQL felügyelt példányok és az Azure arc engedélyezve PostgreSQL nagy kapacitású-kiszolgálócsoportok futtatásához használt metrikák feltöltéséhez a következő CLI-parancsok használhatók:

1. Jelentkezzen be az adatvezérlőbe a használatával `azdata` .
 
1. Az összes metrika exportálása a megadott fájlba:

   ```console
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Metrikák feltöltése az Azure monitorba:

   ```console
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Várjon legalább 30 percet, miután az Azure ív használatára jogosult adatpéldányok létre lettek hozva az első feltöltéshez.
   >
   >Győződjön `upload` meg arról, hogy a metrikák a Azure monitor után azonnal `export` , csak az elmúlt 30 percben fogadják el a metrikákat. [További információ](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting).


Ha az exportálás során "nem sikerült beolvasni a mérőszámokat" hibaüzenet jelenik meg, ellenőrizze, hogy az adatgyűjtés a `true` következő parancs futtatásával van-e beállítva:

```console
azdata arc dc config show
```

Tekintse meg a "biztonsági szakasz" részt

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Ellenőrizze, `allowNodeMetricsCollection` hogy a és a `allowPodMetricsCollection` Tulajdonságok be vannak-e állítva `true` .

## <a name="view-the-metrics-in-the-portal"></a>A metrikák megtekintése a Portalon

A metrikák feltöltése után megtekintheti őket a Azure Portal.
> [!NOTE]
> Vegye figyelembe, hogy a feltöltött adatok feldolgozásához néhány percet is igénybe vehet, mielőtt megtekintheti a metrikákat a portálon.


A metrikák a portálon való megtekintéséhez használja ezt a hivatkozást a portál megnyitásához: ezt <https://portal.azure.com> követően keresse meg az adatbázis-példány nevét a keresősáv használatával:

Megtekintheti a CPU-kihasználtságot az Áttekintés oldalon, vagy ha részletesebb mérőszámokra van szüksége, kattintson a bal oldali navigációs panelen található mérőszámokra.

Válassza az SQL Server elemet metrikus névtérként:

Válassza ki a megjeleníteni kívánt mérőszámot (több lehetőség is kiválasztható):

A gyakoriság módosítása az elmúlt 30 percre:

> [!NOTE]
> Csak az elmúlt 30 percben tölthetők fel mérőszámok. Azure Monitor elutasítja a 30 percnél régebbi metrikákat.

## <a name="automating-uploads-optional"></a>Feltöltések automatizálása (nem kötelező)

Ha a mérőszámokat és a naplókat ütemezett alapon szeretné feltölteni, létrehozhat egy parancsfájlt, és néhány percenként elvégezheti az időzítő futtatását. Az alábbi példa a feltöltések automatizálását mutatja be egy linuxos rendszerhéj-parancsfájl használatával.

A kedvenc szöveg/kód szerkesztőjében adja hozzá a következő parancsfájlt a fájlhoz, és mentse parancsfájlként végrehajtható fájlként, például. sh (Linux/Mac) vagy. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

A parancsfájl végrehajtható fájljának elkészítése

```console
chmod +x myuploadscript.sh
```

Futtassa a szkriptet 20 percenként:

```console
watch -n 1200 ./myuploadscript.sh
```

Olyan Feladatütemezőt is használhat, mint például a cron vagy a Windows Feladatütemező, vagy egy Orchestrator, például a Ansible, a Puppet vagy a Chef.

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Általános útmutató a használat, a metrikák exportálásához és feltöltéséhez

Az Azure arc-kompatibilis adatszolgáltatások létrehozási, olvasási, frissítési és törlési műveleteinek naplózása számlázási és figyelési célból történik. Vannak olyan háttér-szolgáltatások, amelyek figyelik ezeket a szifilisz-műveleteket, és megfelelően kiszámítják a felhasználást. A használat vagy a felhasználás tényleges kiszámítása ütemezett alapon történik, és a háttérben történik. 

Az előzetes verzió ideje alatt ez a folyamat éjjel történik. Az általános útmutató a használat napi egyszeri feltöltése. Ha a használati adatokat több alkalommal exportálják és töltötték fel ugyanazon a 24 órás időszakon belül, akkor csak az erőforrás-leltár frissül Azure Portal de nem az erőforrás-használat.

A metrikák feltöltéséhez az Azure monitor csak az utolsó 30 perces adatokat fogadja el ([További információ](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). A metrikák feltöltésére vonatkozó útmutató a metrikák az exportfájl létrehozása utáni azonnali feltöltésére szolgál, így megtekintheti a teljes adathalmazt Azure Portalban. Ha például a 2:00 ÓRAKOR exportálta a metrikákat, és a feltöltési parancsot 2:50 ÓRAKOR futtatta. Mivel a Azure Monitor csak az elmúlt 30 percben fogadja az adatgyűjtést, előfordulhat, hogy a portálon nem jelennek meg az összes információ. 

## <a name="next-steps"></a>Következő lépések

[Naplók feltöltése a Azure Monitorba](upload-logs.md)

[Használati adatok, metrikák és naplók feltöltése a Azure Monitorba](upload-usage-data.md)

[Számlázási adatok feltöltése az Azure-ba, és megtekintés a Azure Portal](view-billing-data-in-azure.md)

[Az Azure arc adatkezelő erőforrásának megtekintése Azure Portal](view-data-controller-in-azure-portal.md)
