---
ms.openlocfilehash: 50707b46445803ee27118ee72b90a237a3e76200
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103020843"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Felügyelt identitás hozzáadása a kommunikációs szolgáltatások megoldásához

### <a name="install-the-client-library-packages"></a>Az ügyféloldali kódtár csomagjainak telepítése

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-client-library-packages"></a>Az ügyféloldali kódtár csomagjainak használata

Adja hozzá a következőt a `import` kódjához az Azure-identitás használatához.

```python
from azure.identity import DefaultAzureCredential
```

Az alábbi példák a [DefaultAzureCredential](/python/api/azure.identity.defaultazurecredential)használják. Ez a hitelesítő adat megfelelő az éles és a fejlesztési környezetekhez.

Az alkalmazások fejlesztési környezetben való regisztrálásához és környezeti változók beállításához lásd: [hozzáférés engedélyezése felügyelt identitással](../managed-identity-from-cli.md)

### <a name="create-an-identity-and-issue-a-token"></a>Identitás létrehozása és jogkivonat kiadása

A következő mintakód bemutatja, hogyan hozhat létre egy felügyelt identitással rendelkező szolgáltatás-ügyfél objektumot, majd az ügyfél használatával kiállítson egy új felhasználót a jogkivonat számára:

```python
from azure.communication.identity import CommunicationIdentityClient 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>SMS küldése az Azure felügyelt identitásával

A következő mintakód bemutatja, hogyan hozhat létre egy szolgáltatás-ügyfél objektumot az Azure felügyelt identitásával, majd az ügyfél használatával SMS-üzenetet küldhet:

```python
from azure.communication.sms import SmsClient

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```
