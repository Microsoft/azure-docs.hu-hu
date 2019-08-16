---
title: Mi történt a Webjobs-projekttel (Visual Studio Azure Storage Connected Service)? | Microsoft Docs
description: Leírja, hogy mi történt egy Azure Webjobs-projektben, miután kapcsolódott egy Storage-fiókhoz a Visual Studio Connected Services használatával
services: storage
author: ghogen
manager: jillfra
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: c54214e0b919ddaa60403a1c986a144100143577
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510471"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Mi történt a Webjobs-projekttel (Visual Studio Azure Storage Connected Service)?
## <a name="references-added"></a>Hivatkozások hozzáadva
Az Azure Storage NuGet csomagja hozzá lett adva vagy frissítve lett a Visual Studio-projektben.  
Ez a csomag a következő .NET-referenciákat veszi fel:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Az Azure Storage-hoz hozzáadott kapcsolatok karakterlánca
A projekt app. config fájljában a **AzureWebJobsStorage** és a **AzureWebJobsDashboard** bejegyzések frissültek a kiválasztott Storage-fiókhoz tartozó kapcsolatok karakterláncával és kulcsával.

További információ: [Azure WebJobs dokumentációs erőforrások](https://go.microsoft.com/fwlink/?linkid=390226).

