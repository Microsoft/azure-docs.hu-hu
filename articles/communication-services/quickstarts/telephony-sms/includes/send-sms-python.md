---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: 2b96d62fb2be27de03964212557446d2e792beb8
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112852"
---
Ismerkedés az Azure kommunikációs szolgáltatásokkal a kommunikációs szolgáltatások Python SMS SDK használatával SMS-üzenetek küldéséhez.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7 vagy 3.6 +.
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- SMS-kompatibilis telefonszám. [Telefonszám beolvasása](../get-phone-number.md).

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

- A terminál vagy a parancssori ablakban futtassa a `python --version` parancsot a Python telepítésének megadásához.
- A kommunikációs szolgáltatások erőforrásához tartozó telefonszámok megtekintéséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), keresse meg a kommunikációs szolgáltatások erőforrását, és nyissa meg a **telefonszámok** lapot a bal oldali navigációs ablaktáblán.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Nyissa meg a terminál vagy a parancssorablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Szövegszerkesztő használatával hozzon létre egy **Send-SMS.py** nevű fájlt a projekt gyökérkönyvtárában, és adja hozzá a program struktúráját, beleértve az alapszintű kivételek kezelését. Ehhez a rövid útmutatóhoz tartozó forráskódot a következő részekben adja hozzá ehhez a fájlhoz.

```python
import os
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>A csomag telepítése

Még az alkalmazás könyvtára alatt telepítse az Azure kommunikációs szolgáltatások SMS SDK for Python-csomagot a `pip install` paranccsal.

```console
pip install azure-communication-sms
```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek az Azure kommunikációs szolgáltatások SMS SDK for Python számos főbb funkcióját kezelik.

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Ez az osztály minden SMS-funkcióhoz szükséges. Létrehozhatja az előfizetési adataival, és SMS-üzenetek küldéséhez használhatja azt.                                                                                                                 |
| SmsSendResult               | Ez az osztály az SMS szolgáltatás eredményét tartalmazza.                                          |

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

**SmsClient** -példány létrehozása a kapcsolatok karakterláncával. Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../../create-communication-resource.md#store-your-connection-string).

```python
# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(<connection_string>)
```
Az egyszerűség kedvéért ehhez a rövid útmutatóhoz a kapcsolatok karakterláncait használjuk, de éles környezetekben javasoljuk, hogy [felügyelt identitásokat](../../../quickstarts/managed-identity.md) használjanak, mivel azok biztonságosabbak és kezelhetők a nagy léptékben.


## <a name="send-a-11-sms-message"></a>1:1 SMS-üzenet küldése

Ha SMS-üzenetet szeretne küldeni egyetlen címzettnek, hívja meg a ```send``` metódust a **SmsClient** egyetlen címzett telefonszámával. A választható paramétereket is megadhatja annak megadásához, hogy a kézbesítési jelentést engedélyezni kell-e, és egyéni címkéket kell-e beállítani. Adja hozzá ezt a kódot a blokk végéhez a `try` **Send-SMS.py**-ben:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>",
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

`<from-phone-number>`Egy SMS-kompatibilis telefonszámot kell cserélnie a kommunikációs szolgáltatáshoz, valamint `<to-phone-number>` azt a telefonszámot, amelyhez üzenetet kíván küldeni.

> [!WARNING]
> Vegye figyelembe, hogy a telefonszámokat E. 164 nemzetközi szabvány formátumban kell megadni. (például: + 14255550123).

## <a name="send-a-1n-sms-message"></a>1: N SMS-üzenet küldése

Ha SMS-üzenetet szeretne küldeni a címzettek listájára, hívja ```send``` meg a metódust a **SmsClient** a címzett telefonszámait tartalmazó listával. A választható paramétereket is megadhatja annak megadásához, hogy a kézbesítési jelentést engedélyezni kell-e, és egyéni címkéket kell-e beállítani. Adja hozzá ezt a kódot a blokk végéhez a `try` **Send-SMS.py**-ben:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

`<from-phone-number>`Egy SMS-kompatibilis telefonszámot kell cserélnie a kommunikációs szolgáltatáshoz, valamint `<to-phone-number-1>` `<to-phone-number-2>` olyan telefonszám (oka) t, amelyhez üzenetet szeretne küldeni.

> [!WARNING]
> Vegye figyelembe, hogy a telefonszámokat E. 164 nemzetközi szabvány formátumban kell megadni. (például: + 14255550123).

## <a name="optional-parameters"></a>Opcionális paraméterek

A `enable_delivery_report` paraméter egy opcionális paraméter, amely a kézbesítési jelentéskészítés konfigurálására használható. Ez olyan esetekben hasznos, amikor az SMS-üzenetek kézbesítése során eseményeket szeretne kibocsátani. Tekintse meg az [SMS-események kezelése](../handle-sms-events.md) rövid útmutatót az SMS-üzenetek kézbesítési jelentéskészítésének konfigurálásához.

A `tag` paraméter egy opcionális paraméter, amelynek használatával címkét alkalmazhat a kézbesítési jelentésre.

## <a name="run-the-code"></a>A kód futtatása
Futtassa az alkalmazást az alkalmazás könyvtárából a `python` paranccsal.

```console
python send-sms.py
```

A teljes Python-szkriptnek valahogy így kell kinéznie:

```python

import os
from azure.communication.sms import SmsClient

try:
    # Create the SmsClient object which will be used to send SMS messages
    sms_client = SmsClient.from_connection_string("<connection string>")
    # calling send() with sms values
    sms_responses = sms_client.send(
       from_="<from-phone-number>",
       to="<to-phone-number>",
       message="Hello World via SMS",
       enable_delivery_report=True, # optional property
       tag="custom-tag") # optional property

except Exception as ex:
    print('Exception:')
    print(ex)
```
