---
title: Tartalom üzembe helyezése FTP/S használatával
description: Megtudhatja, hogyan helyezheti üzembe alkalmazását a Azure App Service FTP vagy FTPS használatával. A webhely biztonságának javítása a titkosítatlan FTP letiltásával.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: fd856ee47c1100292f7558bb0fa2a1772951a3cb
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832368"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Alkalmazás üzembe helyezése Azure App Service FTP/S használatával

Ez a cikk bemutatja, hogyan helyezheti üzembe webalkalmazását, mobil-háttéralkalmazását [](./overview.md)vagy API-alkalmazását FTP- vagy FTPS-Azure App Service.

Az alkalmazás FTP/S végpontja már aktív. Az FTP/S üzembe helyezésének engedélyezéséhez nincs szükség konfigurációra.

> [!NOTE]
> A **fejlesztői központ (klasszikus)** Azure Portal, amely a régi üzembe helyezési folyamat, 2021 márciusában elavult lesz. Ez a módosítás nem érinti az alkalmazás meglévő telepítési beállításait, és az alkalmazástelepítést a Központi telepítési központ **lapon folytathatja.**

## <a name="get-deployment-credentials"></a>Üzembe helyezési hitelesítő adatok lekért száma

1. Az alkalmazáshatókörű hitelesítő adatok másolásához vagy a felhasználói hatókör hitelesítő adatainak beállításához kövesse [Azure App Service](deploy-configure-credentials.md) telepítési hitelesítő adatok konfigurálási útmutatóját. Az alkalmazás FTP/S végpontjára mindkét hitelesítő adattal csatlakozhat.

1. Adja meg az FTP-felhasználónevet a következő formátumban a választott hitelesítőadat-hatókörtől függően:

    | Alkalmazáshatókör | Felhasználói hatókör |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    Ebben App Service az FTP/S végpont meg van osztva az alkalmazások között. Mivel a felhasználói hatókör hitelesítő adatai nem kapcsolódnak egy adott erőforráshoz, a felhasználói hatókör felhasználónevét előtagként kell megadnia az alkalmazás nevével, a fent látható módon.

## <a name="get-ftps-endpoint"></a>FTP-/S-végpont lekért száma
    
# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Másolja az FTPS-végpontot az alkalmazás ugyanazon felügyeleti oldalára, ahová az üzembe helyezési hitelesítő adatokat **(Üzembe** helyezési központ  >  **FTP-hitelesítő adatai) máselte.**

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Futtassa [az az webapp deployment list-publishing-profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) parancsot. Az alábbi példa egy [JMES-útvonalat használ](https://jmespath.org/) az FTP/S végpontok kinyerésére a kimenetből.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

Minden alkalmazás két FTP/S végponttal rendelkezik, az egyik írási/olvasási, a másik írási/olvasási (tartalmaz) és adat-helyreállítási `profileName` `ReadOnly` forgatókönyvekhez használható. Fájlok FTP-vel való üzembe helyezéséhez másolja ki az írási/olvasási végpont URL-címét.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Futtassa [a Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) parancsot. Az alábbi példa kinyeri az FTP/S végpontot az XML-kimenetből.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Fájlok üzembe helyezése az Azure-ban

1. Az FTP-ügyfélből [(például Visual Studio,](https://www.visualstudio.com/vs/community/) [Cyberduck](https://cyberduck.io/)vagy [WinSCP)](https://winscp.net/index.php)használja a gyűjtött kapcsolati adatokat az alkalmazáshoz való csatlakozáshoz.
2. Másolja a fájlokat és azok könyvtárstruktúráját az Azure [ **/site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) könyvtárába (vagy a WebJobs **/site/wwwroot/App_Data/Jobs/** könyvtárába).
3. Nyissa meg az alkalmazás URL-címét, és ellenőrizze, hogy az alkalmazás megfelelően fut-e. 

> [!NOTE] 
> A [Git-alapú üzembe helyezésekkel és](deploy-local-git.md) a [zip-alapú](deploy-zip.md)üzembe helyezéssel ellentétben az FTP-telepítés nem támogatja a buildek automatizálását, például: 
>
> - függőség-visszaállítások (például NuGet-, NPM-, PIP- és Composer-automatizálások)
> - .NET bináris fájlok fordítása
> - az web.config generációja ( példa [Node.js )](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps)
> 
> Hozza létre ezeket a szükséges fájlokat manuálisan a helyi gépen, majd telepítse őket az alkalmazással együtt.
>

## <a name="enforce-ftps"></a>FTPS kényszerítés

A fokozott biztonság érdekében csak TLS/SSL protokollon keresztül engedélyezze az FTP-t. Ha nem használ FTP-telepítést, az FTP-t és az FTPS-t is letilthatja.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Az alkalmazás erőforráslapján a bal Azure Portal [válassza](https://portal.azure.com)a **Configuration** General settings (Általános konfiguráció  >   beállításai) lehetőséget.

2. A titkosítatlan FTP letiltásához válassza a Csak **FTP-állapotban** **az FTPS lehetőséget.** Az FTP és az FTPS teljes letiltásához válassza a **Letiltva lehetőséget.** Amikor végzett, kattintson a **Mentés** gombra. Ha csak **FTPS-t használ,** a TLS 1.2-es vagy újabb verzió használatát a webalkalmazás **TLS/SSL-beállítások** paneljéhez navigálva kell végrehajtania. A TLS 1.0 és 1.1 csak a FTPS esetén **támogatott.**

    ![FTP/S letiltása](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Futtassa [az az webapp config set parancsot](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) a `--ftps-state` argumentummal.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

A lehetséges `--ftps-state` értékei: `AllAllowed` (FTP és FTPS engedélyezve), `Disabled` (ftp és FTP letiltva) és `FtpsOnly` (csak FTPS).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Futtassa [a Set-AzWebApp parancsot](/powershell/module/az.websites/set-azwebapp) a `-FtpsState` paraméterrel.

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

A lehetséges `--ftps-state` értékei: `AllAllowed` (FTP és FTPS engedélyezve), `Disabled` (ftp és FTP letiltva) és `FtpsOnly` (csak FTPS).

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>FTP-telepítés hibaelhárítása

- [Hogyan háríthatja el az FTP üzembe helyezésének hibaelhárítását?](#how-can-i-troubleshoot-ftp-deployment)
- [Nem tudom FTP-n keresztül közzétenni a kódot. Hogyan oldható meg a probléma?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hogyan csatlakozhatok az FTP-hez Azure App Service passzív módban?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hogyan háríthatja el az FTP üzembe helyezésének hibaelhárítását?

Az FTP-telepítés hibaelhárításának első lépése egy üzembehelyezéssel kapcsolatos probléma elolása egy futásidejű alkalmazással kapcsolatos problémától.

Az üzembe helyezési probléma általában az alkalmazásba telepített fájlok vagy nem megfelelő fájlokat nem ad vissza. A hibaelhárításhoz vizsgálja meg az FTP-telepítést, vagy válasszon egy alternatív telepítési útvonalat (például a forrásvezérlőt).

A futásidejű alkalmazás problémája általában az alkalmazáshoz üzembe helyezett fájlok megfelelő készletét, de az alkalmazás helytelen viselkedését adja vissza. A hibaelhárításhoz a kód futásidőben való viselkedésére összpontosíthat, és adott hibaútvonalak vizsgálatára összpontosíthat.

Az üzembe helyezési vagy futásidejű probléma meghatározásához lásd: [Üzembe helyezési és futásidejű problémák.](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Nem tudok FTP-hez csatlakozni, és közzétenni a kódot. Hogyan oldható meg a probléma?
Ellenőrizze, hogy a megfelelő [](#get-ftps-endpoint) állomásnevet és hitelesítő [adatokat adta-e meg.](#get-deployment-credentials) Ellenőrizze azt is, hogy a következő FTP-portokat nem blokkolja-e tűzfal:

- FTP-vezérlőkapcsolati port: 21, 990
- FTP-adatkapcsolati port: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hogyan csatlakozhatok FTP-hez Azure App Service passzív módban?
Azure App Service az aktív és passzív módon keresztüli csatlakozást is támogatja. A passzív mód ajánlott, mert az üzembe helyezési gépek általában tűzfal mögött vannak (az operációs rendszerben vagy egy otthoni vagy vállalati hálózat részeként). Tekintsen [meg egy példát a WinSCP dokumentációjában.](https://winscp.net/docs/ui_login_connection) 

## <a name="more-resources"></a>További erőforrások

* [Helyi Git üzembe helyezése Azure App Service](deploy-local-git.md)
* [Azure App Service üzembe helyezés hitelesítő adatai](deploy-configure-credentials.md)
* [Minta: Webalkalmazás létrehozása és fájlok üzembe helyezése FTP használatával (Azure CLI).](./scripts/cli-deploy-ftp.md)
* [Minta: Fájlok feltöltése webalkalmazásba FTP (PowerShell) használatával.](./scripts/powershell-deploy-ftp.md)
