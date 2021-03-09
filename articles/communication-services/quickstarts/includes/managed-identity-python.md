---
ms.openlocfilehash: aeeac4ba772899575ab426d76b785a2103a3cdcb
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486604"
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
import azure.communication.identity 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>SMS küldése az Azure felügyelt identitásával

A következő mintakód bemutatja, hogyan hozhat létre egy szolgáltatás-ügyfél objektumot az Azure felügyelt identitásával, majd az ügyfél használatával SMS-üzenetet küldhet:

```python
from azure.communication.sms import (
    PhoneNumberIdentifier,
    SendSmsOptions,
    SmsClient
)

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_phone_number=PhoneNumberIdentitifier(from_phone_number),
          to_phone_numbers=[PhoneNumberIdentifier(to_phone_number)],
          message=message_content,
          send_sms_options=SendSmsOptions(enable_delivery_report=True))  # optional property
     )
```