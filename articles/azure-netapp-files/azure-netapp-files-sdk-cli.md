---
title: SDK-k és CLI-eszközök Azure NetApp Files | Microsoft Docs
description: 'Ismerje meg a Azure NetApp Files támogatott SDK-kat és azok közzétett helyét a GitHubon, valamint a támogatott parancssori eszközöket: Azure CLI és PowerShell.'
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: b-juche
ms.openlocfilehash: e2f7328a0acc8e5206abc600a4052f83828a87f6
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174223"
---
# <a name="azure-netapp-files-sdks-cli-tools-and-arm-templates"></a>SDK-k, CLI-eszközök és ARM-sablonok Azure NetApp Files

Ez a cikk a Azure NetApp Files által támogatott SDK-kat, parancssori (CLI) eszközöket és Azure Resource Manager (ARM-sablonokat) sorolja fel.

## <a name="supported-sdks"></a>Támogatott SDK-k 

Az alábbi táblázat felsorolja a támogatott SDK-kat.  A támogatott SDK-k részletei a GitHubon közzétett helyükön találhatók.  

|    Nyelv    |    SDK közzétett helye a GitHubon    |
|------------------|--------------------------------------------------------------|
|    .NET  |    [Azure/Azure-SDK-for-net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/netapp)    |
|    Python  |  [Azure/Azure-SDK-for-Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/netapp)    |
|    Go    |    [Azure/Azure-SDK-for-go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/netapp)       |
|    Java |     [Azure/Azure-SDK-for-Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/netapp) |
|    JavaScript    |    [Azure/Azure-SDK-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/netapp/arm-netapp)    |
|    Ruby   |    [Azure/Azure-SDK-for-Ruby](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_netapp)    |

## <a name="cli-tools"></a>CLI-eszközök

Az alábbi táblázat felsorolja a támogatott CLI-eszközöket és azok parancs-hivatkozását.   

|    Eszköz    |    A parancs leírása    |
|------------------|--------------------------------------------|
|    Azure CLI  |    [az netappfiles](/cli/azure/netappfiles)    |
|    PowerShell   |    [Azure NetApp Files Azure PowerShell](/powershell/module/az.netappfiles/?view=azps-2.5.0#netapp_files&preserve-view=true)    |

## <a name="code-samples"></a>Kódminták

Lásd: [Azure NetApp Files-kód mintái](/samples/browse/?filter-products=netapp&products=azure-netapp-files).

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok  

A Azure Resource Manager (ARM) lehetővé teszi, hogy az alkalmazásai deklaratív sablonnal legyenek kiépítve. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont újra és újra, az alkalmazás életciklusának minden fázisában felhasználhatja az alkalmazás üzembe helyezéséhez.   

Tekintse [meg a Azure NetApp Files elérhető ARM-sablonokat](https://azure.microsoft.com/resources/templates/?term=anf).

## <a name="next-steps"></a>Következő lépések   
 [Azure SDK-k letöltése](https://azure.microsoft.com/downloads/)
