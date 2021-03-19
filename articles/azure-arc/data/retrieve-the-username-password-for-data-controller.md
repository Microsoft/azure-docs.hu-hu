---
title: Felhasználónév és jelszó lekérése az Arc-adatkezelőhöz való kapcsolódáshoz
description: Felhasználónév és jelszó lekérése az Arc-adatkezelőhöz való kapcsolódáshoz
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 8cabb48e3620f1a17d1bb9b87e1646ce2793143b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91761702"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>Felhasználónév és jelszó lekérése az Arc-adatkezelőhöz való kapcsolódáshoz

Előfordulhat, hogy az adatvezérlő felhasználónevét és jelszavát le kell kérnie. A futtatásakor a következő parancsokat kell megadnia. 

```console
azdata login
```

Ha Ön a fürt Kubernetes rendszergazdája. Ennek megfelelően a Kubernetes titkos kulcsokból lekért parancsok futtatásához szükséges jogosultságokat az Azure arc által megőrzött információk tárolják.

> [!NOTE]
>  Ha más nevet használt a névtérhez, amelyben az adatvezérlőt létrehozták, akkor az `-n arc` alábbi parancsokban található paramétert úgy kell módosítani, hogy annak a névtérnek a nevét használja, amelyhez az adatvezérlőt létrehozta.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

Futtassa a következő parancsot a Felhasználónév lekéréséhez:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

Futtassa a következő parancsot a jelszó lekéréséhez:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

Futtassa a következő parancsot a Felhasználónév lekéréséhez:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

Futtassa a következő parancsot a jelszó lekéréséhez:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>Következő lépések

Más [forgatókönyvek](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory-domain-services/scenarios.md) kipróbálása
