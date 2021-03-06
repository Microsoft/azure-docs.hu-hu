---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 7c1ea3fbf8e5cef4b1e8f8a2b0963fdd66584f5a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075578"
---
Ebben a szakaszban a Azure File Sync-ügynököt telepíti a Windows Server-példányra.

Az [üzembe helyezési útmutató](../articles/storage/files/storage-sync-files-deployment-guide.md) azt szemlélteti, hogy ki kell kapcsolnia az **Internet Explorer fokozott biztonsági beállításait**. Ez a biztonsági mérték nem alkalmazható a Azure File Sync. A kikapcsolás lehetővé teszi, hogy problémák nélkül hitelesítse magát az Azure-ban.

Nyissa meg a PowerShellt, és telepítse a szükséges PowerShell-modulokat az alábbi parancsokkal. Ha a rendszer kéri, telepítse a teljes modult és a NuGet-szolgáltatót.

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Ha problémák merülnek fel az internetről a kiszolgálóról, most már az idő a megoldására. Azure File Sync bármely elérhető hálózati kapcsolatot használ az internethez. A proxykiszolgáló az Internet elérésének megkövetelése is támogatott. Beállíthat egy gépi szintű proxyt, vagy megadhat egy proxyt, amely csak Azure File Sync fogja használni az ügynök telepítése során.

Ha a proxy konfigurálása azt jelenti, hogy meg kell nyitnia a tűzfalat ehhez a kiszolgálóhoz, a megközelítés elfogadható lehet. A kiszolgáló telepítésének befejezését követően a hálózati kapcsolat jelentés megjeleníti az Azure-beli pontos végponti URL-címeket, amelyeknek Azure File Sync kell kommunikálnia a kiválasztott régióval. A jelentés arról is tájékoztat, hogy miért van szükség a kommunikációra. A jelentés használatával zárolhatja a kiszolgálón lévő tűzfalakat meghatározott URL-címekre.

Követheti az olyan konzervatív megközelítést is, amelyben nem nyitja meg a tűzfalak széles körét, hanem korlátozza a kiszolgálót a magasabb szintű DNS-névterekkel való kommunikációra. További információ: [Azure file Sync proxy és tűzfal beállításai](../articles/storage/files/storage-sync-files-firewall-and-proxy.md). Kövesse a saját hálózatkezeléssel kapcsolatos ajánlott eljárásait.

A kiszolgáló telepítése varázsló végén megnyílik a kiszolgáló regisztrálása varázsló. Regisztrálja a kiszolgálót a Storage Sync szolgáltatás Azure-erőforrásához korábban.

Ezeket a lépéseket részletesebben ismertetjük a telepítési útmutatóban, amely tartalmazza azokat a PowerShell-modulokat, amelyeket először telepítenie kell: [Azure file Sync ügynök telepítése](../articles/storage/files/storage-sync-files-deployment-guide.md).

Használja a legújabb ügynököt. A következőt letöltheti a Microsoft letöltőközpontból: [Azure file Sync Agent](https://aka.ms/AFS/agent "Azure File Sync ügynök letöltése").

A sikeres telepítés és a kiszolgáló regisztrálása után megtekintheti, hogy sikeresen végrehajtotta-e ezt a lépést. Nyissa meg a Storage Sync Service-erőforrást a Azure Portal. A bal oldali menüben válassza a **regisztrált kiszolgálók** lehetőséget. Itt láthatja a kiszolgálót.
