---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre Azure Container instance-erőforrásokat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 24f6052c436b73d0075371fa74160d21826e2209
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97866138"
---
## <a name="create-an-azure-container-instance-resource-using-the-azure-portal"></a>Azure Container instance-erőforrás létrehozása a Azure Portal használatával

1. Lépjen a Container Instances [Létrehozás](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) lapjára.

2. Az **alapvető beállítások** lapon adja meg a következő adatokat:

    |Beállítás|Érték|
    |--|--|
    |Előfizetés|Válassza ki előfizetését.|
    |Erőforráscsoport|Válassza ki a rendelkezésre álló erőforráscsoportot, vagy hozzon létre egy újat, például: `cognitive-services` .|
    |Tárolónév|Adjon meg egy nevet, például: `cognitive-container-instance` . A névnek alsó sapkában kell lennie.|
    |Hely|Válasszon régiót az üzembe helyezéshez.|
    |Lemezkép típusa|Ha a tároló rendszerképét olyan tároló-beállításjegyzékben tárolja, amely nem igényel hitelesítő adatokat, válassza a lehetőséget `Public` . Ha a tároló rendszerképének eléréséhez hitelesítő adatokra van szükség, válassza a lehetőséget `Private` . További részletekért tekintse meg a [Container-Tárházak és-lemezképek](../container-image-tags.md) című témakört, amelyből megtudhatja, hogy a tároló képe `Public` vagy sem `Private` (nyilvános előzetes verzió). |
    |Rendszerkép neve|Adja meg a Cognitive Services tároló helyét. A hely a parancs argumentumként használt helye `docker pull` . Tekintse meg a [tároló adattárait és lemezképeit](../container-image-tags.md) a rendelkezésre álló képnevekhez és a hozzájuk tartozó adattárhoz.<br><br>A rendszerkép nevének teljesen minősítettnek kell lennie három rész megadásával. Először a Container Registry, majd a tárház, végül a rendszerkép neve: `<container-registry>/<repository>/<image-name>` .<br><br>Íme egy példa, amely a `mcr.microsoft.com/azure-cognitive-services/keyphrase` Microsoft Container Registry kulcsszókeresés rendszerképét jelöli az Azure Cognitive Services adattár alatt. Egy másik példa a, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` amely a tároló előzetes tárolójának beállításjegyzékének Microsoft-tárházában a szöveges kép beszédét jelképezi. |
    |Operációs rendszer típusa|`Linux`|
    |Méret|Az adott kognitív szolgáltatási tárolóra vonatkozó javasolt javaslatok méretének módosítása:<br>2 CPU-mag<br>4 GB

3. A **hálózatkezelés** lapon adja meg a következő adatokat:

    |Beállítás|Érték|
    |--|--|
    |Portok|Állítsa be a TCP-portot a következőre: `5000` . A tároló elérhetővé tétele a 5000-as porton.|

4. A **speciális** lapon adja meg a szükséges **környezeti változókat** az Azure Container instance-erőforrás tároló számlázási beállításainál:

    | Kulcs | Érték |
    |--|--|
    |`ApiKey`|Az erőforrás **kulcsainak és végpontjának** lapjáról lett másolva. Egy 32 alfanumerikus karakterből álló karakterlánc, szóköz vagy kötőjel nélkül `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|
    |`Billing`| A végpont URL-címe az erőforrás **kulcsok és végpont** lapján lett másolva.|
    |`Eula`|`accept`|

5. Kattintson **a felülvizsgálat és létrehozás** gombra.
6. Az ellenőrzés után kattintson a **Létrehozás** gombra a létrehozási folyamat befejezéséhez.
7. Az erőforrás sikeres üzembe helyezése után készen áll
