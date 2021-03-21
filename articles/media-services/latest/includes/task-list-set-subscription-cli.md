---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: aa76f7b85302651f6874747610c3355f0572a7ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94885213"
---
<!-- List and set subscriptions -->

1. Kérje le az előfizetéseinek a listáját az [az account list](/cli/azure/account#az-account-list) paranccsal:

    ```
    az account list --output table
    ```

2. Használja az `az account set` parancsot azzal az előfizetés-azonosítóval vagy névvel, amelyre váltani szeretne.

    ```
    az account set --subscription "My Demos"
    ```
