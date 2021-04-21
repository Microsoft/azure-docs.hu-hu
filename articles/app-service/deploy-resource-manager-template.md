---
title: Alkalmazások üzembe helyezése sablonokkal
description: Útmutató új Azure Resource Manager létrehozásához és üzembe helyezéséhez App Service alkalmazásokhoz.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: ed6edeadfb1c6f73cc10771d4a5328e7bddb3642
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835163"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Útmutató webalkalmazások üzembe helyezéséhez Azure Resource Manager sablonokkal

Ez a cikk javaslatokat tartalmaz a Azure Resource Manager létrehozásához, amelyek segítségével üzembe Azure App Service megoldásokat. Ezek a javaslatok segíthetnek a gyakori problémák elkerülésében.

## <a name="define-dependencies"></a>Függőségek meghatározása

A webalkalmazások függőségeinek meghatározásához meg kell értenie, hogyan kommunikálnak a webalkalmazás erőforrásai. Ha a függőségeket helytelen sorrendben adja meg, üzembe helyezési hibákat okozhat, vagy verseny feltételt hozhat létre, amely miatt az üzembe helyezés elakad.

> [!WARNING]
> Ha az MSDeploy webhelybővítményt is tartalmazza a sablonban, akkor minden konfigurációs erőforrást az MSDeploy erőforrástól függőként kell beállítania. A konfigurációs módosítások hatására a hely aszinkron módon újraindul. Ha a konfigurációs erőforrásokat az MSDeploy szolgáltatástól függővé teszi, győződjön meg arról, hogy az MSDeploy a hely újraindítása előtt befejeződik. E függőségek nélkül a hely újraindulhat az MSDeploy üzembe helyezési folyamata során. Példasablonért lásd: [WordPress-sablon web deploy dependency (Web Deploy függőséggel)](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Az alábbi képen a különböző erőforrások függőségi sorrendje App Service látható:

![Webalkalmazás függőségei](media/web-sites-rm-template-guidance/web-dependencies.png)

Az erőforrásokat a következő sorrendben helyezheti üzembe:

**Első réteg**
* App Service terv.
* Minden más kapcsolódó erőforrás, például adatbázisok vagy tárfiókok.

**Második réteg**
* Webalkalmazás – a csomagtól App Service függ.
* Az Application Insights-példány, amely a kiszolgálófarmot célozza – a tervtől App Service függ.

**3. réteg**
* Forrásvezérlés – a webalkalmazástól függ.
* MSDeploy webhelybővítmény – a webalkalmazástól függ.
* Az Application Insights-példány, amely a webalkalmazást célozza – a webalkalmazástól függ.

**4. réteg**
* App Service tanúsítvány – a forrásvezérléstől vagy az MSDeploytől függ, ha van ilyen. Ellenkező esetben a webalkalmazástól függ.
* Konfigurációs beállítások (kapcsolati sztringek, web.config értékek, alkalmazásbeállítások) – a forrásvezérlőtől vagy az MSDeploytől függ, ha van ilyen. Ellenkező esetben a webalkalmazástól függ.

**5. réteg**
* Gazdagépnév-kötések – ha van ilyen, a tanúsítványtól függ. Ellenkező esetben egy magasabb szintű erőforrástól függ.
* Helybővítmények – a konfigurációs beállításoktól függ, ha vannak. Ellenkező esetben egy magasabb szintű erőforrástól függ.

A megoldás általában csak néhány ilyen erőforrást és szintet tartalmaz. Hiányzó szintek esetében az alacsonyabb erőforrásokat a következő magasabb szintre kell leképezni.

Az alábbi példa egy sablon egy részét mutatja be. A kapcsolati sztring konfigurációjának értéke az MSDeploy bővítménytől függ. Az MSDeploy bővítmény a webalkalmazástól és az adatbázistól függ. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

A fenti kódot használó, futtatásra kész minta: [Template: Build a simple Umbraco Web App (Sablon: Egyszerű Umbraco-webalkalmazás létrehozása).](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple)

## <a name="find-information-about-msdeploy-errors"></a>Információk az MSDeploy-hibákról

Ha a Resource Manager az MSDeployt használja, az üzembe helyezési hibaüzenetek nehezen érthetők. A sikertelen üzembe helyezés utáni további információkért kövesse az alábbi lépéseket:

1. Ugrás a webhely [Kudu-konzoljára.](https://github.com/projectkudu/kudu/wiki/Kudu-console)
2. Keresse meg a mappát a D:\home\LogFiles\SiteExtensions\MSDeploy mappában.
3. Keresse meg a appManagerStatus.xml és appManagerLog.xml fájlokat. Az első fájl az állapotot naplózza. A második fájl a hibával kapcsolatos információkat naplózza. Ha a hiba nem egyértelmű, akkor felveheti, amikor segítséget kér a [fórumon.](/answers/topics/azure-webapps.html)

## <a name="choose-a-unique-web-app-name"></a>Egyedi webalkalmazás-név kiválasztása

A webalkalmazás nevének globálisan egyedinek kell lennie. Használhat olyan elnevezési konvenciót, amely valószínűleg egyedi, vagy használhatja a [uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) függvényt egy egyedi név létrehozásához.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Webalkalmazás-tanúsítvány üzembe helyezése a Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha a sablon tartalmaz [egy Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates) erőforrást a TLS/SSL-kötéshez, és a tanúsítvány tárolása egy Key Vault-ban történik, meg kell győződnie arról, hogy az App Service-identitás hozzáfér a tanúsítványhoz.

A globális Azure App Service szolgáltatásnév azonosítója **abfa0a7c-a6b6-4736-8310-5855508787cd.** Ha hozzáférést ad Key Vault szolgáltatásnév App Service szolgáltatásnévhez, használja a következőt:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

A Azure Government App Service szolgáltatásnév azonosítója **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Használja ezt az azonosítót az előző példában.

A tanúsítvány Key Vault **válassza** a Tanúsítványok, majd a **Tanúsítvány létrehozása/importálás** lehetőséget.

![Tanúsítvány importálása](media/web-sites-rm-template-guidance/import-certificate.png)

A sablonban adja meg a tanúsítványának `keyVaultSecretName` nevét.

Példasablonért [lásd: Deploy a Web App certificate from Key Vault secret (Webalkalmazás-tanúsítvány](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)üzembe helyezése titkos Key Vault használatával SSL-kötés létrehozásához).

## <a name="next-steps"></a>Következő lépések

* A webalkalmazások sablonnal való üzembe helyezését bemutató oktatóanyagért lásd: Mikroszolgáltatások kiszámítható kiépítése és üzembe helyezése az [Azure-ban.](deploy-complex-application-predictably.md)
* A sablonokban az erőforrástípusok JSON-szintaxisával és tulajdonságaival kapcsolatos további információkért tekintse meg [Azure Resource Manager sablonreferenciáját.](/azure/templates/)
