---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 3f49977ce5bf32f03dbb301e2d7a37449d7de4cc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630007"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7 vagy 3.6 +.
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../create-communication-resource.md).

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

1. Nyissa meg a terminált vagy a parancssorablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. Szövegszerkesztő használatával hozzon létre egy **Issue-Access-tokens.py** nevű fájlt a projekt gyökérkönyvtárában, és adja hozzá a program struktúráját, beleértve az alapszintű kivételek kezelését. Ehhez a rövid útmutatóhoz tartozó forráskódot a következő részekben adja hozzá ehhez a fájlhoz.

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>A csomag telepítése

Még az alkalmazás könyvtára alatt telepítse a Python-csomaghoz készült Azure kommunikációs szolgáltatások Identity SDK-t a `pip install` paranccsal.

```console
pip install azure-communication-identity
```

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A-példány létrehozása a- `CommunicationIdentityClient` val a kapcsolatok karakterláncával. Az alábbi kód egy nevű környezeti változóból kérdezi le az erőforráshoz tartozó kapcsolatok karakterláncát `COMMUNICATION_SERVICES_CONNECTION_STRING` . Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../create-communication-resource.md#store-your-connection-string).

Adja hozzá ezt a kódot a `try` blokkon belül:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

Ha beállította a felügyelt identitást, lásd: [felügyelt](../managed-identity.md)identitások használata, felügyelt identitással is végezhet hitelesítést.
```python
endpoint = os.environ["COMMUNICATION_SERVICES_ENDPOINT"]
client = CommunicationIdentityClient(endpoint, DefaultAzureCredential())
```

## <a name="create-an-identity"></a>Identitás létrehozása

Az Azure kommunikációs szolgáltatások egy egyszerűsített identitási könyvtárat tartanak fenn. A `create_user` metódus használatával hozzon létre egy új bejegyzést a címtárban egyedi értékkel `Id` . Tárolja a kapott identitást az alkalmazás felhasználóinak való leképezéssel. Például úgy, hogy az alkalmazás-kiszolgáló adatbázisában tárolja őket. Az identitást később kell megadni a hozzáférési tokenek kiküldéséhez.

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.identifier)
```

## <a name="issue-access-tokens"></a>Hozzáférési tokenek kiadása

A `get_token` metódus használatával kiállíthat egy hozzáférési jogkivonatot a már meglévő kommunikációs szolgáltatások identitásához. A paraméter olyan `scopes` primitívek készletét határozza meg, amelyek engedélyezik ezt a hozzáférési jogkivonatot. Tekintse meg a [támogatott műveletek listáját](../../concepts/authentication.md). A paraméter új példánya az `CommunicationUserIdentifier` Azure kommunikációs szolgáltatás identitásának karakterlánc-ábrázolása alapján hozható létre.

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.get_token(identity, ["voip"])
expires_on = token_result.expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

A hozzáférési jogkivonatok olyan rövid élettartamú hitelesítő adatok, amelyeket újra kell adni. Ha ezt nem teszi meg, az alkalmazás felhasználói élményének megszakadását okozhatja. A `expires_on` Response tulajdonság a hozzáférési jogkivonat élettartamát jelzi.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Identitás létrehozása és hozzáférési jogkivonat kiadása ugyanazon a kérésen belül

A `create_user_and_token` metódus használatával hozzon létre egy kommunikációs szolgáltatások identitását, és adja meg a hozzá tartozó hozzáférési jogkivonatot. A paraméter olyan `scopes` primitívek készletét határozza meg, amelyek engedélyezik ezt a hozzáférési jogkivonatot. Tekintse meg a [támogatott műveletek listáját](../../concepts/authentication.md).

```python
# Issue an identity and an access token with the "voip" scope for the new identity
identity_token_result = client.create_user_and_token(["voip"])
identity = identity_token_result[0].identifier
token = identity_token_result[1].token
expires_on = identity_token_result[1].expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nCreated an identity with ID: " + identity)
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token)
```

## <a name="refresh-access-tokens"></a>Hozzáférési jogkivonatok frissítése

Hozzáférési jogkivonat frissítéséhez használja az objektumot az `CommunicationUserIdentifier` újrakibocsátáshoz:

```python
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUserIdentifier(existingIdentity)
token_result = client.get_token( identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>Hozzáférési tokenek visszavonása

Bizonyos esetekben explicit módon visszavonhatja a hozzáférési jogkivonatokat. Például amikor egy alkalmazás felhasználója megváltoztatja a szolgáltatásban való hitelesítéshez használt jelszót. `revoke_tokens`A metódus érvényteleníti az összes aktív hozzáférési jogkivonatot, amelyet az identitáshoz adtak ki.

```python
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.identifier)
```

## <a name="delete-an-identity"></a>Identitás törlése

Az identitás törlése visszavonja az összes aktív hozzáférési jogkivonatot, és megakadályozza, hogy az identitáshoz hozzáférési jogkivonatokat bocsásson ki. Emellett eltávolítja az identitáshoz társított összes megőrzött tartalmat is.

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.identifier)
```

## <a name="run-the-code"></a>A kód futtatása

A konzol parancssorában navigáljon a *Issue-Access-tokens.py* fájlt tartalmazó könyvtárra, majd futtassa az alábbi `python` parancsot az alkalmazás futtatásához.

```console
python ./issue-access-tokens.py
```
