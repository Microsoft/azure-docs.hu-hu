---
ms.openlocfilehash: c642b0e5f459b2412bca6588c8ae625142f0f59f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450466"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Felügyelt identitás hozzáadása a kommunikációs szolgáltatások megoldásához

### <a name="install-the-sdk-packages"></a>Az SDK-csomagok telepítése

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-sdk-packages"></a>Az SDK-csomagok használata

Adja hozzá a következőt a `import` kódjához az Azure-identitás használatához.

```python
from azure.identity import DefaultAzureCredential
```

Az alábbi példák a [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential)használják. Ez a hitelesítő adat megfelelő az éles és a fejlesztési környezetekhez.

A felügyelt identitások hitelesítésének használatának egyszerű módja: [hozzáférés engedélyezése felügyelt identitással](../managed-identity-from-cli.md)

Részletesebben tájékozódhat arról, hogyan működik a DefaultAzureCredential objektum, és hogyan használhatja azt olyan módon, amely nem szerepel ebben a rövid útmutatóban: az [Azure Identity ügyféloldali kódtára a Pythonhoz](https://docs.microsoft.com/python/api/overview/azure/identity-readme)

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

### <a name="list-all-your-purchased-phone-numbers"></a>Az összes megvásárolt telefonszám listázása

A következő mintakód bemutatja, hogyan hozhat létre egy szolgáltatás-ügyfél objektumot az Azure felügyelt identitásával, majd az ügyfél használatával megtekintheti az erőforrás által megvásárolt összes telefonszámot:

```python
from azure.communication.phonenumbers import PhoneNumbersClient

def list_purchased_phone_numbers(resource_endpoint):
     credential = DefaultAzureCredential()
     phone_numbers_client = PhoneNumbersClient(resource_endpoint, credential)

     return phone_numbers_client.list_purchased_phone_numbers()
```