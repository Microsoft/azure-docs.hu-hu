---
title: SDK-k Azure Event Grid
description: A Azure Event Grid SDK-kat ismerteti. Ezek az SDK-k felügyeletet, közzétételt és felhasználást biztosítanak.
ms.topic: reference
ms.date: 07/07/2019
ms.openlocfilehash: c5b79f7cb7ae0112e2dacd077289786fc7b5c956
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516755"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>SDK-k Event Grid a felügyelethez és a közzétételhez

A Event Grid olyan SDK-kat biztosít, amelyek lehetővé teszik az erőforrások programozott felügyeletét és az események közzétételét.

## <a name="management-sdks"></a>Felügyeleti SDK-k

A felügyeleti SDK-k lehetővé teszik az Event Grid-témakörök és-előfizetések létrehozását, frissítését és törlését. Jelenleg a következő SDK-k érhetők el:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Ugrás](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Csomópont](https://www.npmjs.com/package/@azure/arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Adatsík SDK-k

Az adatközponti SDK-k lehetővé teszik események küldését a témakörökbe a hitelesítés, az esemény kialakítása és a megadott végpontra történő aszinkron közzététel megtételével. Azt is lehetővé teszik, hogy az első féltől származó eseményeket használja. Jelenleg a következő SDK-k érhetők el:

| Programozási nyelv | SDK | 
| -------------------- | ---------- | ---------- | 
| .NET | Stabil SDK: [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)<p>Előzetes verzió SDK: [Azure. Messaging. EventGrid](https://www.nuget.org/packages/Azure.Messaging.EventGrid/) |
| Java | Stabil SDK: [Azure-eventgrid](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)<p>Előzetes verzió SDK: [Azure-Messaging-eventgrid](https://search.maven.org/artifact/com.azure/azure-messaging-eventgrid/)</p> |  
| Python | [Azure-eventgrid](https://pypi.org/project/azure-eventgrid/#history) (lásd a legújabb stabil és előzetes verziókat a **kiadási előzmények** oldalon) |
| JavaScript | [@azure/eventgrid](https://www.npmjs.com/package/@azure/eventgrid/) (váltson a **verziók** lapra a legújabb stabil és bétaverziós csomagok megtekintéséhez). | 
| Go | [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) | | 
| Ruby | [azure_event_grid](https://rubygems.org/gems/azure_event_grid) | | 


## <a name="next-steps"></a>Következő lépések

* Példa az alkalmazásokra: [Event Grid a kód mintáit](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* A Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Az Azure CLI Event Grid parancsaival kapcsolatban lásd az [Azure CLI](/cli/azure/eventgrid)-t.
* Event Grid parancsok a PowerShellben: [PowerShell](/powershell/module/az.eventgrid).
