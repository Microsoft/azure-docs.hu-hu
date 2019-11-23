---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Learn how to create an Azure container instance resource.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 1679862b1660d3c8b2505c6e0c54f203f5d4665d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383514"
---
## <a name="create-an-azure-container-instance-resource"></a>Create an Azure Container Instance resource

1. Go to the [Create](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) page for Container Instances.

2. On the **Basics** tab, enter the following details:

    |Beállítás|Value (Díj)|
    |--|--|
    |Előfizetés|Válassza ki előfizetését.|
    |Erőforráscsoport|Select the available resource group or create a new one such as `cognitive-services`.|
    |Tárolónév|Enter a name such as `cognitive-container-instance`. The name must be in lower caps.|
    |Földrajzi egység|Select a region for deployment.|
    |Image type|If your container image is stored in a container registry that doesn’t require credentials, choose `Public`. If accessing your container image requires credentials, choose `Private`. Refer to [container repositories and images](../../cognitive-services-container-support.md#container-repositories-and-images) for details on whether or not the container image is `Public` or `Private` ("Public Preview"). |
    |Rendszerkép neve|Enter the Cognitive Services container location. The location is what's used as an argument to the `docker pull` command. Refer to the [container repositories and images](../../cognitive-services-container-support.md#container-repositories-and-images) for the available image names and their corresponding repository.<br><br>The image name must be fully qualified specifying three parts. First, the container registry, then the repository, finally the image name: `<container-registry>/<repository>/<image-name>`.<br><br>Here is an example, `mcr.microsoft.com/azure-cognitive-services/keyphrase` would represent the Key Phrase Extraction image in the Microsoft Container Registry under the Azure Cognitive Services repository. Another example is, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` which would represent the Speech to Text image in the Microsoft repository of the Container Preview container registry. |
    |OS type|`Linux`|
    |Méret|Change size to the suggested recommendations for your specific Cognitive Service container:<br>2 CPU cores<br>4 GB

3. On the **Networking** tab, enter the following details:

    |Beállítás|Value (Díj)|
    |--|--|
    |Portok|Set the TCP port to `5000`. Exposes the container on port 5000.|

4. On the **Advanced** tab, enter the required **Environment Variables** for the container billing settings of the Azure Container Instance resource:

    | Jelmagyarázat | Value (Díj) |
    |--|--|
    |`apikey`|Copied from the **Keys** page of the resource. It is a 32 alphanumeric-character string with no spaces or dashes, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copied from the **Overview** page of the resource.|
    |`eula`|`accept`|

1. Click **Review and Create**
1. After validation passes, click **Create** to finish the creation process
1. When the resource is successfully deployed, it's ready