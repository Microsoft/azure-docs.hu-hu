---
title: Alkalmazások listázása fürtön sfctl-ben
description: Service Fabric parancssori felületi példaszkript – Service Fabric-fürtön lévő alkalmazások listázása.
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
ms.date: 04/13/2018
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: 41685e53eb0915f54bdc2d678191e5b767990dde
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610267"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Service Fabric-fürtön futó alkalmazások listázása

Ez a példaszkript egy Service Fabric-fürthöz csatlakozik, és az összes üzembe helyezett alkalmazást felsorolja.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Példaszkript

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>További lépések

További információ: [A Service Fabric parancssori felületének dokumentációja](../service-fabric-cli.md).

A [Service Fabric parancssori felület példái](../samples-cli.md) között további Service Fabric parancssori felületi példákat talál az Azure Service Fabrichez.
