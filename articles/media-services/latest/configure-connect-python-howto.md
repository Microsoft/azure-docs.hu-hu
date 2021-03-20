---
title: Kapcsolódás Azure Media Services V3 API-hoz – Python
description: Ez a cikk bemutatja, hogyan csatlakozhat a Media Services V3 API-hoz a Python használatával.
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
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-python
ms.openlocfilehash: 76df8baaf170b05762b93478a496eb1e9ed802d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94916746"
---
# <a name="connect-to-media-services-v3-api---python"></a>Kapcsolódás Media Services V3 API-hoz – Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk bemutatja, hogyan csatlakozhat a Azure Media Services v3 Python SDK-hoz az egyszerű szolgáltatás bejelentkezési metódusának használatával.

## <a name="prerequisites"></a>Előfeltételek

- A Python letöltése a [Python.org](https://www.python.org/downloads/) -ből
- Ügyeljen arra, hogy a `PATH` környezeti változót állítsa be
- [Hozzon létre egy Media Services fiókot](./create-account-howto.md). Ügyeljen arra, hogy jegyezze fel az erőforráscsoport nevét és a Media Services fiók nevét.
- Kövesse az API-k [elérését](./access-api-howto.md) ismertető témakör lépéseit. Jegyezze fel az előfizetés-azonosítót, az alkalmazás AZONOSÍTÓját (ügyfél-AZONOSÍTÓját), a hitelesítő kulcsot (Secret) és a bérlő AZONOSÍTÓját, amelyet a későbbi lépésben kell megadnia.

> [!IMPORTANT]
> Tekintse át az [elnevezési konvenciókat](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>A modulok telepítése

A Azure Media Services Python használatával történő használatához telepítenie kell ezeket a modulokat.

* A `azure-mgmt-resource` modul, amely Active Directory Azure-modulokat tartalmaz.
* A `azure-mgmt-media` modul, amely tartalmazza a Media Services entitásokat.

    Győződjön meg arról, hogy a [Pythonhoz készült Media Services SDK legújabb verzióját](https://pypi.org/project/azure-mgmt-media/)kapja meg.

Nyisson meg egy parancssori eszközt, és a következő parancsokkal telepítse a modulokat.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==3.0.0
```

## <a name="connect-to-the-python-client"></a>Kapcsolódás a Python-ügyfélhez

1. `.py`Kiterjesztésű fájl létrehozása
1. Nyissa meg a fájlt a kedvenc szerkesztőjében
1. Adja hozzá a következő kódot a fájlhoz. A kód importálja a szükséges modulokat, és létrehozza a Media Serviceshoz való kapcsolódáshoz szükséges Active Directory hitelesítő adatokat tartalmazó objektumot.

      A változók értékeinek beállítása a [hozzáférési API](./access-api-howto.md) -kkal kapott értékekre

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. A fájl futtatása

## <a name="next-steps"></a>Következő lépések

- A [PYTHON SDK](https://aka.ms/ams-v3-python-sdk)használata.
- Tekintse át a Media Services [Python ref](/python/api/overview/azure/mediaservices/management) dokumentációját.
