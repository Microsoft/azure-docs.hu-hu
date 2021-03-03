---
ms.openlocfilehash: 9da0cbc3777359994fac3778dcc126c844754861
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657638"
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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A hitelesítés ismertetése](../../concepts/authentication.md)

A következőket is érdemes elvégezheti:

- [További információ az Azure szerepköralapú hozzáférés-vezérléséről](../../../../articles/role-based-access-control/index.yml)
- [További információ a Pythonhoz készült Azure Identity Library-ről](/net/api/overview/azure/identity-readme)
- [Felhasználói hozzáférési tokenek létrehozása](../../quickstarts/access-tokens.md)
- [SMS küldése](../../quickstarts/telephony-sms/send.md)
- [További információ az SMS-ről](../../concepts/telephony-sms/concepts.md)