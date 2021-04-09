---
title: Az alapértelmezett ideiglenes mappa mérete túl kicsi a szerepkörhöz | Microsoft Docs
description: A felhőalapú szolgáltatások szerepköre korlátozott mennyiségű hellyel rendelkezik a TEMP mappához. Ez a cikk néhány javaslatot tartalmaz arra vonatkozóan, hogyan kerülheti el a lemezterületet.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 1b7bfb47168c31f9e2e1b7e40764439118c00805
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743202"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-classic-webworker-role"></a>Az alapértelmezett ideiglenes mappa mérete túl kicsi a Cloud Service (klasszikus) webes és feldolgozói szerepkörben

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A Cloud Service Worker vagy webes szerepkör alapértelmezett ideiglenes könyvtára legfeljebb 100 MB méretű lehet, ami egy bizonyos ponton megtelt. Ez a cikk azt ismerteti, hogyan kerülhető el a szabad hely az ideiglenes címtárban.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Miért fogy el a szabad terület?
A szabványos Windows környezeti változók TEMP és TMP az alkalmazásban futó kód számára elérhetők. Mind a TEMP, mind a TMP egy olyan könyvtárba mutat, amelynek maximális mérete 100 MB. Az ebben a könyvtárban tárolt összes adatmennyiséget a Cloud Service életciklusa során nem őrzi meg a rendszer. Ha a felhőalapú szolgáltatásban lévő szerepkör-példányok újra lesznek hasznosítva, a rendszer megtisztítja a könyvtárat.

## <a name="suggestion-to-fix-the-problem"></a>A probléma megoldására vonatkozó javaslat
A következő alternatívák egyikének implementálása:

* Konfiguráljon egy helyi tárolási erőforrást, és közvetlenül a TEMP vagy a TMP használata helyett. Ha egy helyi tárolási erőforrást szeretne elérni az alkalmazáson belül futó kódból, hívja meg a [RoleEnvironment. GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) metódust.
* Konfiguráljon egy helyi tárolási erőforrást, és mutasson a TEMP és TMP könyvtárakra, hogy a helyi tárolási erőforrás elérési útjára mutasson. Ezt a módosítást a [RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) metóduson belül kell végrehajtani.

A következő mintakód bemutatja, hogyan módosítható a TEMP és a TMP cél könyvtára a OnStart metóduson belül:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések
Olvassa el az [Azure web Role ASP.net ideiglenes mappa méretének növelését](/archive/blogs/kwill/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder)ismertető blogot.

További [hibaelhárítási cikkek](/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) a Cloud Services szolgáltatáshoz.

Ha meg szeretné tudni, hogyan lehet elhárítani a Cloud Service szerepkörrel kapcsolatos problémákat az Azure Pásti számítógép-diagnosztikai adataival, tekintse [meg a Kevin Williamson blog-sorozatát](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).