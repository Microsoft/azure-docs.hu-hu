---
title: Alkalmazás frissítése fürtön a sfctl-ben
description: Service Fabric parancssori felület példaszkriptje – Alkalmazás frissítése egy új verzióval. Ez a példa egy üzembe helyezett alkalmazást is frissít az új elemekkel.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: 34f2ae6f3a2ff3adc35794d6e7dfd682640c646b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75614723"
---
# <a name="update-an-application-using-the-service-fabric-cli"></a>Alkalmazás frissítése a Service Fabric parancssori felület használatával

Ez a példaszkript egy meglévő alkalmazás új verzióját tölti fel, majd frissíti az üzembe helyezett alkalmazást az új elemekkel.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Példaszkript

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Következő lépések

További információ: [A Service Fabric parancssori felületének dokumentációja](../service-fabric-cli.md).

A [Service Fabric parancssori felület példái](../samples-cli.md) között további Service Fabric parancssori felületi példákat talál az Azure Service Fabrichez.
