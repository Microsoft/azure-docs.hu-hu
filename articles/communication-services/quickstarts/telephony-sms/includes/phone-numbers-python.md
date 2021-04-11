---
ms.openlocfilehash: b9ac58a7cf09463020e0bcd424f5fc5974133657
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112999"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 vagy újabb.
- Egy üzembe helyezett kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Nyissa meg a terminál vagy a parancssorablakot, és hozzon létre egy új könyvtárat az alkalmazáshoz, majd navigáljon hozzá.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Egy szövegszerkesztővel hozzon létre egy phone_numbers_sample. file nevű fájlt a projekt gyökérkönyvtárában, és adja hozzá a következő kódot. A hátralévő rövid útmutató kódját a következő részekben fogjuk hozzáadni.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

try:
   print('Azure Communication Services - Phone Numbers Quickstart')
   # Quickstart code goes here
except Exception as ex:
   print('Exception:')
   print(ex)
```

### <a name="install-the-package"></a>A csomag telepítése

Miközben még mindig az alkalmazás könyvtárában van, telepítse a Python-csomaghoz készült Azure Communication Services Felügyeleti ügyféloldali kódtárat a `pip install` paranccsal.

```console
pip install azure-communication-phone-numbers
```

## <a name="authenticate-the-phone-numbers-client"></a>A telefonszám-ügyfél hitelesítése

A `PhoneNumbersClient` Azure Active Directory hitelesítés használatára engedélyezve van. Az `DefaultAzureCredential` objektum használatával megkezdheti a Azure Active Directory használatának első lépéseit, és a paranccsal telepítheti azt `pip install` .

```console
pip install azure-identity
```

Egy objektum létrehozásához az `DefaultAzureCredential` szükséges, hogy a, a `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET` és `AZURE_TENANT_ID` a már beállított környezeti változók legyenek a regisztrált Azure ad-alkalmazás megfelelő értékeivel.

A könyvtár telepítése után `azure-identity` folytathatja az ügyfél hitelesítését.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
except Exception as ex:
    print('Exception:')
    print(ex)
```

Azt is megteheti, hogy a kommunikációs erőforrás végpontját és hozzáférési kulcsát használja a hitelesítéshez.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

# You can find your connection string from your resource in the Azure portal
connection_string = 'https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<YOUR_ACCESS_KEY>'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    phone_numbers_client = PhoneNumbersClient.from_connection_string(connection_string)
except Exception as ex:
    print('Exception:')
    print(ex)
```

## <a name="functions"></a>Functions

A `PhoneNumbersClient` hitelesítése után megkezdheti a munkát az általa elvégezhető különböző funkciókon.

### <a name="search-for-available-phone-numbers"></a>Elérhető telefonszámok keresése

A telefonszámok megvásárlásához először meg kell keresnie az elérhető telefonszámokat. A telefonszámok kereséséhez adja meg a körzetszámot, a hozzárendelés típusát, a [telefonszám-képességeket](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), a [telefonszám típusát](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)és a mennyiséget (az alapértelmezett mennyiség értéke 1). Vegye figyelembe, hogy az ingyenesen hívható telefonszámok esetében a körzetszám megadása nem kötelező.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient, PhoneNumberCapabilityType, PhoneNumberAssignmentType, PhoneNumberType, PhoneNumberCapabilities
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = self.phone_number_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        polling = True
    )
    search_result = search_poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="purchase-phone-numbers"></a>Telefonszámok vásárlása

A telefonszámok keresésének eredménye a következő: `PhoneNumberSearchResult` . Ez tartalmaz egy, a Buy `searchId` Numbers API-nak átadandó számot, amely a keresésben szereplő számok beszerzésére használható. Vegye figyelembe, hogy a Purchase Phone Numbers API meghívásával díjat számítunk fel az Azure-fiókra.

```python
import os
from azure.communication.phonenumbers import (
    PhoneNumbersClient,
    PhoneNumberCapabilityType,
    PhoneNumberAssignmentType,
    PhoneNumberType,
    PhoneNumberCapabilities
)
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        area_code="833",
        polling = True
    )
    search_result = poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

    purchase_poller = phone_numbers_client.begin_purchase_phone_numbers(search_result.search_id, polling = True)
    purchase_poller.result()
    print("The status of the purchase operation was: " + purchase_poller.status())
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="get-purchased-phone-numbers"></a>Vásárolt telefonszám (ok) beolvasása

A vásárlást követően lekérheti azt az ügyfélről.

```python
purchased_phone_number_information = phone_numbers_client.get_purchased_phone_number("+18001234567")
print('Phone number: ' + purchased_phone_number_information.phone_number)
print('Country code: ' + purchased_phone_number_information.country_code)
```

Lekérheti az összes megvásárolt telefonszámot is.

```python
purchased_phone_numbers = phone_numbers_client.list_purchased_phone_numbers()
print('Purchased phone numbers:')
for purchased_phone_number in purchased_phone_numbers:
    print(purchased_phone_number.phone_number)
```

### <a name="update-phone-number-capabilities"></a>Telefonszám-képességek frissítése

Frissíthet egy korábban megvásárolt telefonszám képességeit.
```python
update_poller = phone_numbers_client.begin_update_phone_number_capabilities(
    "+18001234567",
    PhoneNumberCapabilityType.OUTBOUND,
    PhoneNumberCapabilityType.OUTBOUND,
    polling = True
)
update_poller.result()
print('Status of the operation: ' + update_poller.status())
```

### <a name="release-phone-number"></a>Kiadási telefonszám

Megvásárolt telefonszámot is felszabadíthat.

```python
release_poller = phone_numbers_client.begin_release_phone_number("+18001234567")
release_poller.result()
print('Status of the operation: ' + release_poller.status())
```

## <a name="run-the-code"></a>A kód futtatása

A konzol parancssorában navigáljon a phone_numbers_sample. a fájlt tartalmazó könyvtárra, majd hajtsa végre a következő Python-parancsot az alkalmazás futtatásához.

```console
./phone_numbers_sample.py
```
