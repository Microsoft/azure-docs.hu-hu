---
title: Tartalom üzembe helyezése FTP/S használatával
description: Megtudhatja, hogyan helyezheti üzembe az alkalmazást Azure App Service FTP-vagy FTPS használatával. A titkosítatlan FTP letiltásával javíthatja a webhelyek biztonságát.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c7427a1f8f528fdf405b22c4e91941ea7a915ffa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045802"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Az alkalmazás üzembe helyezése az Azure App Service FTP/S használatával

Ez a cikk bemutatja, hogyan használható az FTP vagy a FTPS a webalkalmazások, a Mobile apps-háttérrendszer vagy az API-alkalmazások [Azure app Service](./overview.md)való üzembe helyezéséhez.

Az alkalmazáshoz tartozó FTP/S végpont már aktív. Az FTP/S központi telepítésének engedélyezéséhez nincs szükség konfigurációra.

> [!NOTE]
> A Azure Portal, amely a régi telepítési élmény, a **fejlesztői központ (klasszikus)** lapja 2021 márciusában elavulttá válik. Ez a módosítás nem befolyásolja az alkalmazásban meglévő központi telepítési beállításokat, és az alkalmazás központi telepítését továbbra is kezelheti a **központi telepítési központ** lapon.

## <a name="get-deployment-credentials"></a>Központi telepítési hitelesítő adatok beolvasása

1. Az alkalmazás-hatókörbeli hitelesítő adatok másolásához, illetve a felhasználói hatókörű hitelesítő adatok beállításához kövesse az [Azure app Service telepítési hitelesítő adatainak konfigurálása](deploy-configure-credentials.md) című témakör útmutatását. Az alkalmazás FTP/S-végpontját bármelyik hitelesítő adat használatával kapcsolódhat.

1. Az FTP-felhasználónevet a következő formátumban kell megadnia, a hitelesítő adatokra vonatkozó hatókörtől függően:

    | Alkalmazás – hatókör | Felhasználó – hatókör |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    App Service az FTP/S végpontot az alkalmazások között osztják meg. Mivel a felhasználói hatókörbeli hitelesítő adatok nem egy adott erőforráshoz vannak csatolva, a felhasználói hatókörhöz tartozó felhasználónevet a fent látható módon kell megadnia az alkalmazás nevével.

## <a name="get-ftps-endpoint"></a>FTP/S végpont beolvasása
    
# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Az alkalmazás ugyanazon felügyeleti lapján, amelyen az üzembe helyezési hitelesítő adatokat (a **központi telepítési központ**  >  **FTP-hitelesítő adatait**) másolta, másolja az **FTPS-végpontot**.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Futtassa az az [WebApp Deployment List-Publishing-Profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) parancsot. Az alábbi példa egy [JMES útvonalat](https://jmespath.org/) használ az FTP/S végpontok kimenetből való kinyeréséhez.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

Minden alkalmazás két FTP/S végponttal rendelkezik, az egyik írható-olvasható, míg a másik írásvédett ( `profileName` tartalmaz `ReadOnly` ), és az adatok helyreállítási forgatókönyvek esetében. A fájlok FTP-vel történő üzembe helyezéséhez másolja az írható-olvasható végpont URL-címét.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Futtassa a [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) parancsot. A következő példa kibontja az FTP/S végpontot az XML-kimenetből.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Fájlok üzembe helyezése az Azure-ban

1. Az FTP-ügyfélről (például [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/)vagy [megnyerő](https://winscp.net/index.php)) használja az összegyűjtött kapcsolati adatokat az alkalmazáshoz való kapcsolódáshoz.
2. Másolja a fájlokat és a hozzá tartozó címtár-struktúrát a [ **/site/wwwroot** könyvtárba](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) az Azure-ban (vagy a **/site/wwwroot/App_Data/Jobs/** könyvtárat a webjobs szolgáltatáshoz).
3. Keresse meg az alkalmazás URL-címét, és ellenőrizze, hogy az alkalmazás megfelelően fut-e. 

> [!NOTE] 
> A [git-alapú telepítések](deploy-local-git.md) és a [zip-telepítéstől](deploy-zip.md)eltérően az FTP-telepítés nem támogatja a Build automationt, például: 
>
> - függőségek visszaállítása (például NuGet, NPM, PIP és zeneszerzői automatizálások)
> - a .NET bináris fájljainak összeállítása
> - web.config generációja (itt egy [Node.js példa](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Létrehozza ezeket a szükséges fájlokat manuálisan a helyi gépen, majd telepítse azokat az alkalmazással együtt.
>

## <a name="enforce-ftps"></a>FTPS betartatása

A fokozott biztonság érdekében csak a TLS/SSL protokollt használó FTP-t engedélyezze. Ha nem használja az FTP-telepítést, le is tilthatja az FTP-t és a FTPS.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Az alkalmazás erőforrás-lapja [Azure Portalban](https://portal.azure.com)válassza a **konfiguráció**  >  **általános beállítások** lehetőséget a bal oldali navigációs sávon.

2. A titkosítatlan FTP letiltásához válassza a **FTPS csak** **FTP-állapotban** lehetőséget. Az FTP és a FTPS teljes letiltásához válassza a **Letiltva** lehetőséget. Amikor végzett, kattintson a **Mentés** gombra. Ha **csak a FTPS**-t használja, a webalkalmazás **TLS/SSL-beállítások** paneljére kell kikényszeríteni a TLS 1,2-es vagy újabb verzióját. A TLS 1,0 és 1,1 **csak a FTPS** esetében támogatott.

    ![FTP/S letiltása](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Futtassa az az [WebApp config set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) parancsot az `--ftps-state` argumentummal.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

A lehetséges értékei `--ftps-state` : `AllAllowed` (FTP és FTPS engedélyezve), `Disabled` (FTP és FTPS letiltva) és `FtpsOnly` (csak FTPS).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Futtassa a [set-AzWebApp](/powershell/module/az.websites/set-azwebapp) parancsot a `-FtpsState` paraméterrel.

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

A lehetséges értékei `--ftps-state` : `AllAllowed` (FTP és FTPS engedélyezve), `Disabled` (FTP és FTPS letiltva) és `FtpsOnly` (csak FTPS).

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>FTP-telepítés hibáinak megoldása

- [Hogyan lehet elhárítani az FTP-telepítést?](#how-can-i-troubleshoot-ftp-deployment)
- [Nem tudok FTP-t és közzétenni a kódot. Hogyan oldható fel a probléma?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hogyan csatlakozhatok az FTP-hez Azure App Service passzív módban?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hogyan lehet elhárítani az FTP-telepítést?

Az FTP-telepítés hibaelhárításának első lépéseként elszigeteli az üzembe helyezési problémát egy futásidejű alkalmazás hibája alapján.

A központi telepítési probléma általában nem tartalmaz az alkalmazásba telepített fájlokat vagy helytelen fájlokat. Az FTP központi telepítésének kivizsgálásával vagy egy másik telepítési útvonal (például a verziókövetés) kiválasztásával elháríthatja a hibakeresést.

A futásidejű alkalmazások problémái általában az alkalmazáshoz központilag telepített fájlok megfelelő készletét eredményezik, de helytelen az alkalmazás viselkedése. A hibakereséshez összpontosíthat a futásidejű kódok viselkedésére, és kivizsgálhatja a hibák adott elérési útját.

Az üzembe helyezési vagy futásidejű probléma okának meghatározásához lásd: [üzembe helyezési](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)és futásidejű problémák.

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Nem tudok FTP-hez csatlakozni, és közzétenni a kódot. Hogyan oldható fel a probléma?
Győződjön meg arról, hogy a helyes [állomásnevet](#get-ftps-endpoint) és [hitelesítő adatokat](#get-deployment-credentials)adta meg. Győződjön meg arról is, hogy a számítógépen a következő FTP-portok nincsenek letiltva a tűzfalon:

- FTP-vezérlő kapcsolati portja: 21, 990
- FTP-adatkapcsolati port: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hogyan csatlakozhatok az FTP-hez Azure App Service passzív módban?
Azure App Service támogatja az aktív és passzív módban történő csatlakozást. A passzív üzemmód használata ajánlott, mert az üzembe helyezési gépek általában tűzfal mögött vannak (az operációs rendszeren vagy otthoni vagy üzleti hálózat részeként). Tekintse meg [a megnyerő dokumentációban található példát](https://winscp.net/docs/ui_login_connection). 

## <a name="more-resources"></a>További erőforrások

* [Helyi git üzembe helyezése Azure App Service](deploy-local-git.md)
* [Központi telepítési hitelesítő adatok Azure App Service](deploy-configure-credentials.md)
* [Példa: Webalkalmazás létrehozása és fájlok üzembe helyezése FTP-vel (Azure CLI)](./scripts/cli-deploy-ftp.md).
* [Példa: fájlok feltöltése egy webalkalmazásba FTP használatával (PowerShell)](./scripts/powershell-deploy-ftp.md).
