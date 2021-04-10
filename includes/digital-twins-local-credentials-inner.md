---
author: baanders
description: fájl belefoglalása a DefaultAzureCredential helyi hitelesítésének beállításához az Azure Digital Twins-mintákban – bevezetés nélkül
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: fb148551db798207a52bd7aef629da79dd3341e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102212814"
---
`DefaultAzureCredential`A-ben a minta a helyi környezetben hitelesítő adatokat keres, például egy Azure-bejelentkezést egy helyi [Azure CLI](/cli/azure/install-azure-cli) -ben, illetve a Visual Studióban vagy a Visual Studio Code-ban. Ebből kifolyólag *helyileg kell bejelentkeznie az Azure* -ba ezen mechanizmusok egyikével a minta hitelesítő adatainak beállításához.

Ha a Visual studiót vagy a Visual Studio Code-ot használja a kód futtatásához, győződjön meg arról, hogy a szerkesztőbe bejelentkezett ugyanazzal az Azure-beli hitelesítő adatokkal, amelyeket használni szeretne az Azure Digital Twins-példányhoz való hozzáféréshez.

Ellenkező esetben [telepítheti a helyi Azure CLI](/cli/azure/install-azure-cli)-t, elindíthat egy parancssort a gépen, és futtathatja a `az login` parancsot az Azure-fiókba való bejelentkezéshez. A bejelentkezést követően a kód mintájának futtatásakor automatikusan be kell jelentkeznie.