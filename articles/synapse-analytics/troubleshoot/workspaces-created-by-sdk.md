---
title: 'Hibakeresés: az SDK által létrehozott munkaterületek nem tudják elindítani a szinapszis Studio alkalmazást'
description: Az SDK által létrehozott munkaterületek feloldásának lépései nem tudják elindítani a szinapszis Studio alkalmazást
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: workspace
ms.date: 11/24/2020
ms.openlocfilehash: f5d0aae975d476da47510a1f2fd164cbc5f32945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466430"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>Az SDK használatával létrehozott Azure szinapszis Analytics-munkaterületek – problémamegoldás

Ez a cikk hibaelhárítási lépéseket tartalmaz a szinapszis Studiónak a szoftverfejlesztői készlettel (SDK) létrehozott szinapszis-munkaterületről történő elindításához.


## <a name="prerequisites"></a>Előfeltételek

- Az SDK használatával létrehozott szinapszis-munkaterület

## <a name="workaround"></a>Áthidaló megoldás

Ha a szinapszis Studio alkalmazást az SDK által létrehozott munkaterületről szeretné elindítani, hajtsa végre a következő lépéseket: 
  1.    Hozzon létre egy munkaterületet a futtatásával `az synapse workspace create` .
  2.    A felügyelt azonosító AZONOSÍTÓjának kinyerése a futtatásával `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    A Storage blob adatközreműködői szerepkörének megadása a felügyelt Identity Storage-fiókhoz a futtatásával ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.`
  4.    Tűzfalszabály hozzáadása a futtatásával ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Következő lépések

* [Mi az az Azure szinapszis?](../overview-what-is.md)
* [Első lépések](../get-started.md)
