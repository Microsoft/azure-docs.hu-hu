---
ms.openlocfilehash: 3fcfb364ebffdd3643e803922cbe4f3dd0d87935
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512307"
---
## <a name="setting-up"></a>Beállítása

## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Nyissa meg a terminált vagy a parancsablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg.

```console
mkdir managed-identity-quickstart && cd managed-identity-quickstart
```

### <a name="install-the-sdk-packages"></a>Az SDK-csomagok telepítése

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="create-a-new-file"></a>Új fájl létrehozása
Nyisson meg és mentsen egy nevű új fájlt a létrehozott mappában. Ebben a fájlban fogjuk elhelyezni a `managed-identity.py` kódot.

### <a name="use-the-sdk-packages"></a>Az SDK-csomagok használata

Adja hozzá a következő utasításokat a fájl tetejéhez a telepített `import` SDK-k használatára.

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient
```

### <a name="create-a-defaultazurecredential"></a>DefaultAzureCredential létrehozása

A [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential)értéket fogjuk használni. Ez a hitelesítő adat éles és fejlesztési környezetekhez megfelelő. Mivel ebben a rövid útmutatóban ezt fogjuk használni, a fájl tetején fogjuk létrehozni.

```python
     credential = DefaultAzureCredential()
```

## <a name="create-an-identity-and-issue-a-token-with-managed-identities"></a>Identitás létrehozása és jogkivonat kiállítása felügyelt identitásokkal

Most hozzáadunk egy kódot, amely a létrehozott hitelesítő adatokat használja a VoIP hozzáférési jogkivonat kiállításához. Ezt a kódot később fogjuk hívni:

```python
def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-managed-identities"></a>SMS küldése felügyelt identitásokkal
A felügyelt identitások használatának egy másik példájaként hozzáadjuk ezt a kódot, amely ugyanazt a hitelesítő adatokat használja AZ SMS-ek elküldését:

```python
def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

## <a name="write-our-main-code"></a>A fő kód megírása

A létrehozott függvények után megírhatja a fő kódot, amely az előzőben megírt függvényeket fogja hívni.

```python
# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Managed Identities");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Managed Identities");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Managed Identities");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```

A végső `managed-identity.py` fájlnak a következő képhez hasonlónak kell lennie:

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient

credential = DefaultAzureCredential()

def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     response = sms_client.send(
          from_=from_phone_number,
          to=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
     return response

# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Managed Identities");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Managed Identities");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Managed Identities");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```
## <a name="run-the-program"></a>A program futtatása

Ha minden elkészült, futtathatja a fájlt a projekt könyvtárában a `python managed-identity.py` beírásával. Ha minden jól ment, az alábbihoz hasonlót kell látnia.

```Bash
    $ python managed-identity.py
    Retrieving new Access Token, using Managed Identities
    Retrieved Access Token: ey...Q
    Sending SMS using Managed Identities
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
