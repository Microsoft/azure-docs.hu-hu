---
title: Kapcsolódás Azure Media Services V3 API-hoz – Node.js
description: Ez a cikk bemutatja, hogyan csatlakozhat a Media Services V3 API-hoz a Node.js használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 9628e46281e267bd1c1fd8277a3a975bc338c6dc
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096051"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Kapcsolódás Media Services V3 API-hoz – Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk bemutatja, hogyan csatlakozhat a Azure Media Services v3 node.js SDK-hoz az egyszerű szolgáltatás bejelentkezési módszerének használatával.

## <a name="prerequisites"></a>Előfeltételek

- Telepítse a [Node.js](https://nodejs.org/en/download/).
- Telepítse az [írógéppel](https://www.typescriptlang.org/download).
- [Hozzon létre egy Media Services fiókot](./create-account-howto.md). Ügyeljen arra, hogy jegyezze fel az erőforráscsoport nevét és a Media Services fiók nevét.

> [!IMPORTANT]
> Tekintse át a Azure Media Services [elnevezési konvenciókat](media-services-apis-overview.md#naming-conventions) az entitások fontos elnevezési korlátozásainak megismeréséhez. 

## <a name="reference-documentation-for-azurearm-mediaservices"></a>Dokumentáció a következőhöz: @Azure/arm-mediaservices
- [A Node.jsAzure Media Services moduljainak dokumentációja ](https://docs.microsoft.com/javascript/api/overview/azure/media-services?view=azure-node-latest)

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>További fejlesztői dokumentáció az Azure-beli Node.js
- [Azure for JavaScript & Node.js fejlesztők számára](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Media Services forráskód a git hub-tárházban @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Az Azure Package dokumentációja Node.js-fejlesztőknek](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)

## <a name="install-the-packages"></a>A csomagok telepítése

1. Hozzon létre egy package.jsfájlt a kedvenc szerkesztője használatával.
1. Nyissa meg a fájlt, és illessze be a következő kódot:

   Győződjön meg arról, hogy a [javascripthez készült AZUREMEDIASERVICES SDK](https://www.npmjs.com/package/@azure/arm-mediaservices)legújabb verzióját kapja meg.

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

A következő csomagokat kell megadni:

|Csomag|Description|
|---|---|
|`@azure/arm-mediaservices`|Azure Media Services SDK. <br/>Győződjön meg arról, hogy a legújabb Azure Media Services csomagot használja, és ellenőrizze, hogy NPM-e a [telepítés @azure/arm-mediaservices ](https://www.npmjs.com/package/@azure/arm-mediaservices).|
|`@azure/ms-rest-nodeauth` | Az egyszerű szolgáltatásnév vagy a felügyelt identitás használatával történő HRE-hitelesítéshez szükséges|
|`@azure/storage-blob`|Storage SDK. Fájlok eszközökre való feltöltésekor használatos.|
|`@azure/ms-rest-js`| A bejelentkezéshez használatos.|
|`@azure/storage-blob` | Fájlok feltöltésére és letöltésére használható Azure Media Services a kódoláshoz.|
|`@azure/abort-controller`| A Storage-ügyféllel együtt használatos a hosszú ideig futó letöltési műveletek időkorlátja|


A következő parancs futtatásával gondoskodhat arról, hogy a legújabb csomagot használja:

### <a name="install-azurearm-mediaservices"></a>Telepítése @azure/arm-mediaservices
```
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Telepítése @azure/ms-rest-nodeauth

Telepítse a (z) " @azure/ms-rest-nodeauth ": "^ 3.0.0" minimális verzióját.

```
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

## <a name="connect-to-nodejs-client-using-typescript"></a>Kapcsolódás Node.js-ügyfélhez a géppel

1. Hozzon létre egy írógéppel. ts fájlt a kedvenc szerkesztő használatával.
1. Nyissa meg a fájlt, és illessze be a következő kódot.
1. Hozzon létre egy. env fájlt, és töltse ki a részleteket a Azure Portalból. Lásd: [hozzáférés API](./access-api-howto.md)-k.

### <a name="sample-env-file"></a>Minta. env fájl
```
# copy the content of this file to a file named ".env". It should be stored at the root of the repo.
# The values can be obtained from the API Access page for your Media Services account in the portal.
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="typescript---hello-world---list-assets"></a>Írógéppel Hello World – eszközök listázása
Ez a minta bemutatja, hogyan csatlakozhat az Media Services-ügyfélhez egy egyszerű szolgáltatással, és hogyan listázhatja a fiókban lévő eszközöket. Ha új fiókot használ, a lista üresen jelenik meg. A portálon néhány eszköz feltöltésével megjelenítheti az eredményeket.

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="run-the-sample-application-helloworld-listassets"></a>Futtassa a minta alkalmazást HelloWorld-ListAssets

A Node.js-minták tárházának klónozása

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

Könyvtár módosítása a AMSv3Samples mappába
```bash
cd AMSv3Samples
```

Telepítse a packages.jsben használt csomagokat a következőn:
```
npm install 
```

Könyvtár módosítása a HelloWorld-ListAssets mappába
```bash
cd HelloWorld-ListAssets
```

Indítsa el a Visual Studio Code-ot a AMSv3Samples mappából. Erre azért van szükség, hogy az a mappában induljon el, ahol a ". vscode" mappa és a tsconfig.jsfájl található.
```dotnetcli
cd ..
code .
```

Nyissa meg a HelloWorld-ListAssets mappát, és nyissa meg az index. ts fájlt a Visual Studio Code Editorban.
Az index. TS fájlban nyomja le az F5 billentyűt a hibakereső elindításához. Ekkor megjelenik az eszközök listája, amely akkor jelenik meg, ha már rendelkezik a fiókban lévő eszközökkel. Ha a fiók üres, akkor egy üres lista jelenik meg.  Töltsön fel néhány eszközt a portálon az eredmények megtekintéséhez.

## <a name="more-samples"></a>További minták

A [tárházban](https://github.com/Azure-Samples/media-services-v3-node-tutorials) a következő minták érhetők el

|Projektnév|Használati eset|
|---|---|
|Élő/index. TS| Alapszintű élő közvetítés – példa. Ügyeljen **arra, hogy ellenőrizze, hogy** az összes erőforrás törlődik-e, és a továbbiakban nem történik meg a számlázás a portálon az élő használatakor|
|StreamFilesSample/index. TS| Alapszintű példa helyi fájl vagy kódolás forrás URL-címről való feltöltésére. A minta bemutatja, hogyan töltheti le a tartalmat a Storage SDK-val, és hogyan továbbíthatja azt egy lejátszóba |
|StreamFilesWithDRMSample/index. TS| Bemutatja, hogyan kódolhat és továbbíthatja a Widevine és a PlayReady DRM-t |
|VideoIndexerSample/index. TS| Példa a videó-és hangelemző-készletek használatára a videó-vagy hangfájlokból származó metaadatok és elemzések létrehozásához |

## <a name="see-also"></a>Lásd még

- [Media Services fogalmak](concepts-overview.md)
- [NPM telepítése @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure for JavaScript & Node.js fejlesztők számára](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Forráskód Media Services a tárházban @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a Media Services [Node.js ref](/javascript/api/overview/azure/mediaservices/management) dokumentációval, és tekintse meg a Media Services API node.js használatával történő használatát bemutató [mintákat](https://github.com/Azure-Samples/media-services-v3-node-tutorials) .
