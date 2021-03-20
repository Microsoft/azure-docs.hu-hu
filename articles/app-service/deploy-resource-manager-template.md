---
title: Alkalmazások telepítése sablonokkal
description: Útmutatást talál Azure Resource Manager sablonok létrehozásához App Service alkalmazások kiépítéséhez és üzembe helyezéséhez.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 1146b5979d81b91c6c6894aa54b2e0ca50c896c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88961618"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Útmutató webalkalmazások üzembe helyezéséhez Azure Resource Manager sablonok használatával

Ez a cikk ajánlásokat tartalmaz Azure Resource Manager sablonok létrehozásához Azure App Service megoldások telepítéséhez. Ezek a javaslatok segíthetnek a gyakori problémák elkerülésében.

## <a name="define-dependencies"></a>Függőségek definiálása

A Web Apps függőségeinek definiálásához meg kell ismernie, hogyan hatnak a webalkalmazások erőforrásai. Ha nem megfelelő sorrendben adta meg a függőségeket, a telepítési hibák merülhetnek fel, vagy létrehozhat egy olyan versenyhelyzet-feltételt, amely megtartja a telepítést.

> [!WARNING]
> Ha a sablonban MSDeploy-bővítményt is tartalmaz, a MSDeploy erőforrástól függőként kell beállítania a konfigurációs erőforrásokat. A konfigurációs módosítások a hely aszinkron újraindítását okozzák. Azáltal, hogy a konfigurációs erőforrások MSDeploy függenek, gondoskodni kell arról, hogy a MSDeploy a hely újraindítása előtt véget ér. Ezen függőségek nélkül a hely a MSDeploy üzembe helyezési folyamata során újraindulhat. Példa sablonra: WordPress- [sablon a web Deploy-függőséggel](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

A következő képen a különböző App Service erőforrások függőségi sorrendje látható:

![Webalkalmazás függőségei](media/web-sites-rm-template-guidance/web-dependencies.png)

Az erőforrásokat a következő sorrendben helyezheti üzembe:

**Első réteg**
* App Service terv.
* Bármilyen más kapcsolódó erőforrás, például adatbázisok vagy Storage-fiókok.

**Második réteg**
* Webalkalmazás – a App Service tervtől függ.
* Az Azure Application Insights-példány, amely a kiszolgálófarmot célozza, a App Service tervtől függ.

**3. szintű**
* Verziókövetés – a webalkalmazástól függ.
* MSDeploy – a webalkalmazástól függ.
* Az Azure Application Insights-példány, amely a webalkalmazást célozza meg – a webalkalmazástól függ.

**4. szintű**
* App Service tanúsítvány – függ a forrás vezérlőelemtől vagy a MSDeploy, ha van ilyen. Ellenkező esetben a webalkalmazástól függ.
* Konfigurációs beállítások (kapcsolatok sztringek, web.config értékek, Alkalmazásbeállítások) – függ a forrás-vagy a MSDeploy, ha van ilyen. Ellenkező esetben a webalkalmazástól függ.

**5. szintű**
* Állomásnév-kötések – a tanúsítványtól függ, ha van. Ellenkező esetben egy magasabb szintű erőforrástól függ.
* Hely bővítményei – a konfigurációs beállításoktól függ, ha vannak ilyenek. Ellenkező esetben egy magasabb szintű erőforrástól függ.

A megoldás általában csak néhány ilyen erőforrást és szintet tartalmaz. A hiányzó rétegek esetében az alacsonyabb erőforrásokat a következő magasabb rétegre kell leképezni.

A következő példa egy sablon részét mutatja be. A kapcsolatok karakterlánc-konfigurációjának értéke a MSDeploy-bővítménytől függ. A MSDeploy bővítmény a webalkalmazástól és az adatbázistól függ. 

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

A fenti kódot használó, használatra kész minta [: sablon: egyszerű árnyék-webalkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple)létrehozása.

## <a name="find-information-about-msdeploy-errors"></a>MSDeploy-hibákkal kapcsolatos információk keresése

Ha a Resource Manager-sablon MSDeploy használ, a telepítési hibaüzenetek nehezen érthetők. Ha további információt szeretne kapni egy sikertelen telepítés után, próbálkozzon a következő lépésekkel:

1. Nyissa meg a hely [kudu-konzolját](https://github.com/projectkudu/kudu/wiki/Kudu-console).
2. Tallózással keresse meg a mappát a következő címen: D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Keresse meg a appManagerStatus.xml és appManagerLog.xml fájlokat. Az első fájl naplózza az állapotot. A második fájl a hibával kapcsolatos információkat naplózza. Ha a hiba nem egyértelmű, akkor azt is megteheti, ha segítséget kér a [fórumon](/answers/topics/azure-webapps.html).

## <a name="choose-a-unique-web-app-name"></a>Egyedi webalkalmazás nevének kiválasztása

A webalkalmazás nevének globálisan egyedinek kell lennie. Használhat olyan elnevezési konvenciót, amely valószínűleg egyedi, vagy használhatja a [uniqueString függvényt](../azure-resource-manager/templates/template-functions-string.md#uniquestring) egyedi név létrehozásához.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Webalkalmazás-tanúsítvány üzembe helyezése Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha a sablon tartalmaz egy [Microsoft. Web/Certificates](/azure/templates/microsoft.web/certificates) erőforrást a TLS/SSL-kötéshez, és a tanúsítványt egy Key Vault tárolja, meg kell győződnie arról, hogy a app Service identitás hozzáfér a tanúsítványhoz.

A globális Azure-ban a App Service egyszerű szolgáltatásnév a **ABFA0A7C-A6B6-4736-8310-5855508787CD** azonosítója. Ahhoz, hogy hozzáférést biztosítson Key Vault számára a App Service egyszerű szolgáltatásnév számára, használja a következőt:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

Azure Government az App Service egyszerű szolgáltatás azonosítója a **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Használja ezt az azonosítót az előző példában.

A tanúsítvány feltöltéséhez a Key Vault válassza a **tanúsítványok** , majd a **Létrehozás/importálás** lehetőséget.

![Tanúsítvány importálása](media/web-sites-rm-template-guidance/import-certificate.png)

A sablonban adja meg a tanúsítvány nevét `keyVaultSecretName` .

Példaként lásd: [webalkalmazás-tanúsítvány üzembe helyezése Key Vault titkos kulcsból, és az SSL-kötés létrehozásához használja](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Következő lépések

* A webalkalmazások sablonnal történő üzembe helyezésével kapcsolatos oktatóanyagért tekintse meg a következő témakört: az [Azure-ban előre jelzett szolgáltatások üzembe helyezése és telepítése](deploy-complex-application-predictably.md).
* A sablonokban található erőforrástípusok JSON-szintaxisával és tulajdonságaival kapcsolatos információkért lásd: [Azure Resource Manager sablon referenciája](/azure/templates/).