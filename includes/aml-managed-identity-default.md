---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: aa8aea66c5481454f0d7d4d118934f5fbf34a911
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89147471"
---
Az **alapértelmezett felügyelt identitás** a rendszer által hozzárendelt felügyelt identitás vagy az első felhasználó által hozzárendelt felügyelt identitás.

A Futtatás során két identitás létezik:

1. A rendszer identitás használatával állítja be a felhasználó Storage-csatlakoztatásait, a tároló-beállításjegyzéket és az adattárakat.

    * Ebben az esetben a rendszer az alapértelmezetten felügyelt identitást fogja használni.

1. A felhasználó identitást alkalmaz a kód egy beküldött futtatáshoz való eléréséhez.

    * Ebben az esetben adja meg a hitelesítő adatok lekéréséhez használni kívánt felügyelt identitásnak megfelelő *client_id* .
    * Azt is megteheti, hogy beolvassa a felhasználó által hozzárendelt identitás ügyfél-AZONOSÍTÓját a *DEFAULT_IDENTITY_CLIENT_ID* környezeti változón keresztül.

    Ha például az adattárhoz tartozó jogkivonatot az alapértelmezetten felügyelt identitással szeretné lekérni:

    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')
    ```