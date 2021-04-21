---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: ffd053b98a54d3e23eec62427f5c3d82df58954d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800297"
---
<!-- List and set subscriptions -->

1. Kérje le az előfizetéseinek a listáját az [az account list](/cli/azure/account#az_account_list) paranccsal:

    ```
    az account list --output table
    ```

2. Használja az `az account set` parancsot azzal az előfizetés-azonosítóval vagy névvel, amelyre váltani szeretne.

    ```
    az account set --subscription "My Demos"
    ```
