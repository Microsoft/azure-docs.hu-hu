---
title: Használati adatok feltöltése a Azure Monitorba
description: Azure arc-kompatibilis adatszolgáltatási adatok feltöltése a Azure Monitorba
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 0c72eda59f375c70274b17796ca53614ef95505b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669508"
---
# <a name="upload-usage-data-to-azure-monitor"></a>Használati adatok feltöltése a Azure Monitorba

Rendszeres időközönként exportálhatja a használati adatokat. Az adatok exportálása és feltöltése létrehozza és frissíti az adatkezelőt, az SQL felügyelt példányát és a PostgreSQL nagy kapacitású Server Group erőforrásait az Azure-ban.

> [!NOTE] 
> Az előzetes verzió ideje alatt az Azure arc-kompatibilis adatszolgáltatások használata díjmentes.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> Várjon legalább 24 órát az Azure arc-adatkezelő létrehozása után a használati adatok feltöltése előtt.

## <a name="create-service-principal-and-assign-roles"></a>Egyszerű szolgáltatásnév létrehozása és szerepkörök társítása

A folytatás előtt győződjön meg arról, hogy létrehozta a szükséges szolgáltatásnevet, és hozzárendelte azt egy megfelelő szerepkörhöz. Részletes információ:
* [Egyszerű szolgáltatásnév létrehozása](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Szerepkörök társítása az egyszerű szolgáltatáshoz](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>Használati adatok feltöltése

A következő kétlépéses módon töltheti fel az Azure-ba a használati adatokat, például a leltárt és az erőforrás-használatot:

1. Jelentkezzen be az adatvezérlőbe. Adja meg az értékeket a parancssorban. 

   ```console
   azdata login
   ```

1. Exportálja a használati adatokat a `azdata arc dc export` paranccsal a következő módon:

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   Ez a parancs létrehoz egy `usage.json` fájlt az összes Azure arc-kompatibilis adaterőforrással, például az SQL által felügyelt példányokkal és a PostgreSQL nagy kapacitású-példányokkal, amelyek az adatvezérlőn jönnek létre.

2. A használati adatok feltöltése a ```azdata upload``` parancs használatával

   ```console
   azdata arc dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>Feltöltések automatizálása (nem kötelező)

Ha a mérőszámokat és a naplókat ütemezett alapon szeretné feltölteni, létrehozhat egy parancsfájlt, és néhány percenként elvégezheti az időzítő futtatását. Az alábbi példa a feltöltések automatizálását mutatja be egy linuxos rendszerhéj-parancsfájl használatával.

A kedvenc szöveg/kód szerkesztőjében adja hozzá a következő parancsfájlt a fájlhoz, és mentse parancsfájlként végrehajtható fájlként, például `.sh` (Linux/Mac) vagy, `.cmd` `.bat` vagy `.ps1` .

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

## <a name="next-steps"></a>Következő lépések

[Metrikák és naplók feltöltése a Azure Monitorba](upload-metrics.md)

[Naplók feltöltése a Azure Monitorba](upload-logs.md)

[Számlázási adatok feltöltése az Azure-ba, és megtekintés a Azure Portal](view-billing-data-in-azure.md)

[Az Azure arc adatkezelő erőforrásának megtekintése Azure Portal](view-data-controller-in-azure-portal.md)
