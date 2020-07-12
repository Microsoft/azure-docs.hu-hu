---
title: Az Azure-arc hibaelhárítása a kiszolgálók ügynökével kapcsolatban – problémák
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani az Azure arc for Servers szolgáltatással (előzetes verzió) felmerülő, a csatlakoztatott számítógép ügynökével kapcsolatos problémákat, amikor a szolgáltatáshoz próbál csatlakozni.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/10/2020
ms.topic: conceptual
ms.openlocfilehash: 37f99ade366a73cb96caf55a562a92476223eb6b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86262023"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>A csatlakoztatott gépi ügynök kapcsolódási problémáinak elhárítása

Ez a cikk az Azure arc for Servers (előzetes verzió) Windows vagy Linux rendszerhez csatlakoztatott számítógép-ügynökkel való konfigurálására tett kísérlet során felmerülő problémák elhárításával és megoldásával kapcsolatos információkat tartalmaz. A szolgáltatáshoz való kapcsolódás konfigurálásakor az interaktív és a méretezhető telepítési módszerek is szerepelnek. Általános információk: [arc for Servers – áttekintés](./overview.md).

## <a name="agent-verbose-log"></a>Ügynök részletes naplója

A jelen cikk későbbi részében ismertetett hibaelhárítási lépések követése előtt a részletes napló a szükséges minimális információ. A **azcmagent** eszköz parancsainak kimenetét tartalmazza, amikor a részletes (-v) argumentumot használják. A naplófájlok a Windows és a Linux rendszerre íródnak `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

A következő példa arra a parancsra mutat példát, amely lehetővé teszi a részletes naplózást a Windows csatlakoztatott számítógép-ügynökével, ha interaktív telepítést végez.

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

A következő példa arra a parancsra mutat példát, amely lehetővé teszi a részletes naplózást a Windows rendszerhez csatlakoztatott számítógép-ügynökkel, ha egy egyszerű szolgáltatást végez egy egyszerű szolgáltatásnév használatával.

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

A következő példa arra a parancsra mutat példát, amely lehetővé teszi a részletes naplózást a Linux rendszerhez csatlakoztatott gépi ügynökkel, ha interaktív telepítést végez.

```
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

A következő példa arra a parancsra mutat példát, amely lehetővé teszi a részletes naplózást a Linux rendszerhez készült csatlakoztatott gépi ügynökkel, amikor egy egyszerű szolgáltatásnév használatával végez méretezéses telepítést.

```
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Ügynök kapcsolódási problémái a szolgáltatáshoz

A következő táblázat a hibák elhárításával és megoldásával kapcsolatos ismert hibákat és javaslatokat sorolja fel.

|Üzenet |Hiba |Lehetséges ok |Megoldás |
|--------|------|---------------|---------|
|Nem sikerült beszerezni az engedélyezési jogkivonat eszközének folyamatát |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |A végpont nem érhető el `login.windows.net` | Ellenőrizze, hogy van-e kapcsolat a végponttal. |
|Nem sikerült beszerezni az engedélyezési jogkivonat eszközének folyamatát |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |A proxy vagy a tűzfal blokkolja a `login.windows.net` végponthoz való hozzáférést. | Ellenőrizze, hogy csatlakozik-e a végponthoz, és hogy nem blokkolja-e tűzfal vagy proxykiszolgáló. |
|Nem sikerült beszerezni az engedélyezési jogkivonatot az SPN-ből |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |A proxy vagy a tűzfal blokkolja a `login.windows.net` végponthoz való hozzáférést. |Ellenőrizze, hogy csatlakozik-e a végponthoz, és hogy nem blokkolja-e tűzfal vagy proxykiszolgáló. |
|Nem sikerült beszerezni az engedélyezési jogkivonatot az SPN-ből |`Invalid client secret is provided` |Helytelen vagy érvénytelen a szolgáltatás egyszerű titka. |Ellenőrizze az egyszerű szolgáltatás titkos kulcsát. |
| Nem sikerült beszerezni az engedélyezési jogkivonatot az SPN-ből |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Az egyszerű szolgáltatásnév és/vagy a bérlő azonosítója helytelen. |Ellenőrizze a szolgáltatásnevet és/vagy a bérlő AZONOSÍTÓját.|
|ARM-erőforrás válaszának beolvasása |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Helytelen hitelesítő adatok és/vagy engedélyek |Ellenőrizze, hogy az egyszerű szolgáltatásnév tagja-e az **Azure Connected Machine** bevezetési szerepkörnek. |
|Nem sikerült AzcmagentConnect a ARM-erőforrást |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Az Azure-erőforrás-szolgáltatók nincsenek regisztrálva. |Regisztrálja az [erőforrás-szolgáltatókat](./agent-overview.md#register-azure-resource-providers). |
|Nem sikerült AzcmagentConnect a ARM-erőforrást |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |A proxykiszolgáló vagy a tűzfal blokkolja a hozzáférést a `management.azure.com` végponthoz. |Ellenőrizze, hogy csatlakozik-e a végponthoz, és hogy nem blokkolja-e tűzfal vagy proxykiszolgáló. |

## <a name="next-steps"></a>Következő lépések

Ha itt nem találja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure szakértőitől a [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-arc.html)-n keresztül.

* A szolgáltatással való együttműködéshez [@AzureSupport](https://twitter.com/azuresupport) a hivatalos Microsoft Azure fiók a felhasználói élmény javítása érdekében. Az Azure-támogatás a válaszokat, támogatást és szakértőket az Azure-Közösséggel köti össze.

* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése**lehetőséget.