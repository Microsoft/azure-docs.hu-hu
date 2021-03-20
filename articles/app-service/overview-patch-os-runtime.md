---
title: Operációs rendszer és futtatókörnyezet javításának ritmusa
description: Megtudhatja, hogyan frissíti Azure App Service az operációs rendszer és a futtatókörnyezet frissítéseit, milyen futtatókörnyezeteket és javítási szinteket használ, és hogyan kérheti le a frissítési hirdetményeket.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 8b52223aea0f0bdfecf58906ac192e893da3b47d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96558487"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Operációs rendszer és futtatókörnyezet javítása Azure App Service

Ebből a cikkből megtudhatja, hogyan szerezheti be a [app Service](overview.md)operációs rendszerével vagy szoftverével kapcsolatos egyes verziókat. 

App Service egy szolgáltatásként szolgáló platform, ami azt jelenti, hogy az operációs rendszer és az alkalmazás veremét az Azure felügyeli. csak az alkalmazás és az adatai kezelhetők. Az operációs rendszer és az alkalmazás verem további felügyelete az [Azure Virtual Machines](../virtual-machines/index.yml)érhető el. Ennek ellenére hasznos, ha App Service-felhasználó további információkat szeretne megtudni, például:

-   Hogyan és mikor alkalmazza az operációs rendszer frissítéseit?
-   Hogyan App Service javítani a jelentős biztonsági rések (például a nulla nap) ellen?
-   Mely operációsrendszer-és futtatókörnyezet-verziók futnak az alkalmazásaiban?

Biztonsági okokból a biztonsági információk bizonyos sajátosságai nincsenek közzétéve. A cikk azonban a folyamat átláthatóságának maximalizálása és a biztonsággal kapcsolatos bejelentések és futtatókörnyezet frissítéseinek naprakészen tartása érdekében igyekszik enyhíteni a problémákat.

## <a name="how-and-when-are-os-updates-applied"></a>Hogyan és mikor alkalmazza az operációs rendszer frissítéseit?

Az Azure két szinten kezeli az operációs rendszer javításait, a fizikai kiszolgálókat és a App Service erőforrásokat futtató vendég virtuális gépeket (VM). Mindkettő havonta frissül, amely igazodik a havi [patch keddi](/security-updates/) időponthoz. A rendszer automatikusan alkalmazza ezeket a frissítéseket, így garantálja az Azure-szolgáltatások magas rendelkezésre állású SLA-t. 

A frissítések alkalmazásával kapcsolatos részletes információkért lásd: [a Demystifying app Service operációs rendszer frissítései mögött](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Hogyan foglalkozik az Azure a jelentős sebezhetőségekkel?

Ha a súlyos biztonsági rések azonnali javítást igényelnek, például a [zéró napi biztonsági réseket](https://wikipedia.org/wiki/Zero-day_(computing)), a magas prioritású frissítéseket a rendszer eseti alapon kezeli.

Az Azure [Security blogon](https://azure.microsoft.com/blog/topics/security/)meglátogatva naprakészen tarthatja a kritikus biztonsági közleményeket az Azure-ban. 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Mikor frissülnek a támogatott nyelvi futtatókörnyezetek, hogyan lettek hozzáadva vagy elavultak?

A támogatott nyelvi futtatókörnyezetek (Major, Minor vagy patch) új stabil verzióit a rendszer rendszeresen hozzáadja App Service példányokhoz. Egyes frissítések felülírják a meglévő telepítést, míg másokat a meglévő verziókkal párhuzamosan telepítenek. A felülírásos telepítés azt jelenti, hogy az alkalmazás automatikusan a frissített futtatókörnyezeten fut. A párhuzamos telepítés azt jelenti, hogy az új futtatókörnyezet kihasználása érdekében manuálisan kell áttelepítenie az alkalmazást. További információ: az alszakaszok egyike.

A futtatókörnyezet frissítései és az elavulás itt jelent meg:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Az itt található információk a App Service alkalmazásba beépített nyelvi futtatókörnyezetekre vonatkoznak. A App Servicera feltöltött egyéni futtatókörnyezet például változatlan marad, hacsak nem frissíti manuálisan.
>
>

### <a name="new-patch-updates"></a>Új javítási frissítések

A .NET, a PHP, a Java SDK vagy a Tomcat verzió frissítései automatikusan érvénybe lépnek, ha felülírja a meglévő telepítést a legújabb verzióval. Node.js a javítások frissítései a meglévő verziókkal együtt települnek (a következő szakaszban a fő-és alverzióhoz hasonlóan). Az új Python-javítási verziók manuálisan is telepíthetők a [site Extensions](https://azure.microsoft.com/blog/azure-web-sites-extensions/)használatával, a beépített Python-telepítésekkel párhuzamosan.

### <a name="new-major-and-minor-versions"></a>Új fő-és alverziók

Új fő vagy másodlagos verzió hozzáadásakor a rendszer a meglévő verziókkal együtt telepíti. Az alkalmazást manuálisan is frissítheti az új verzióra. Ha egy konfigurációs fájlban (például és) konfigurálta a futásidejű verziót `web.config` `package.json` , ugyanezt a metódust kell frissítenie. Ha App Service beállítást használt a futtatókörnyezet verziójának konfigurálásához, akkor azt a [Azure Portalban](https://portal.azure.com) vagy egy [Azure CLI](/cli/azure/get-started-with-azure-cli) -parancs futtatásával módosíthatja a [Cloud Shell](../cloud-shell/overview.md), ahogy az alábbi példákban is látható:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Elavult verziók  

Ha egy régebbi verzió elavult, a rendszer az Eltávolítás dátumát is bejelenti, hogy a futtatókörnyezet verziófrissítésének megtervezése megfelelő legyen. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Hogyan lehet lekérdezni az operációs rendszer és a futtatókörnyezet frissítési állapotát a példányokon?  

Habár a kritikus operációsrendszer-információk le vannak tiltva a hozzáféréstől (lásd: [operációs rendszer funkciójának Azure app Service](operating-system-functionality.md)), a [kudu-konzol](https://github.com/projectkudu/kudu/wiki/Kudu-console) lehetővé teszi, hogy lekérdezze a app Service példányt az operációsrendszer-verzióra és a futásidejű verziókra vonatkozóan. 

Az alábbi táblázat bemutatja, hogyan használhatók a Windows és az alkalmazásokat futtató nyelvi futtatókörnyezetek verziói:

| Tájékoztatás | Hol található | 
|-|-|
| Windows-verzió | Lásd: `https://<appname>.scm.azurewebsites.net/Env.cshtml` (a Rendszerinformáció területen) |
| .NET-verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole` alkalmazásban futtassa a következő parancsot a parancssorban: <br>`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full"` |
| .NET Core-verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole` alkalmazásban futtassa a következő parancsot a parancssorban: <br> `dotnet --version` |
| PHP-verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole` alkalmazásban futtassa a következő parancsot a parancssorban: <br> `php --version` |
| Alapértelmezett Node.js verziója | A [Cloud Shell](../cloud-shell/overview.md)futtassa a következő parancsot: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python-verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole` alkalmazásban futtassa a következő parancsot a parancssorban: <br> `python --version` |  
| Java-verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole` alkalmazásban futtassa a következő parancsot a parancssorban: <br> `java -version` |  

> [!NOTE]  
> Hozzáférés a beállításjegyzék helyéhez `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages` , ahol a ["kb"-os javításokat](/security-updates/SecurityBulletins/securitybulletins) tartalmazó információk tárolódnak, zárolva vannak.
>
>

## <a name="more-resources"></a>További erőforrások

[Adatvédelmi központ: biztonság](https://www.microsoft.com/en-us/trustcenter/security)  
[64 bites ASP.NET Core Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
