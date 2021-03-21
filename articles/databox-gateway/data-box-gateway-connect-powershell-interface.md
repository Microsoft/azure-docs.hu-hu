---
title: Azure Data Box Gateway eszköz csatlakoztatása és kezelése a Windows PowerShell használatával
description: Ismerteti, hogyan csatlakozhat a szolgáltatáshoz, majd hogyan felügyelheti Data Box Gateway a Windows PowerShell felületén keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: f3b93bfc9af9bce50c301c10bd372567360d7223
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582169"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Azure Data Box Gateway-eszköz kezelése a Windows PowerShell használatával

Azure Data Box Gateway megoldás lehetővé teszi az adatküldés hálózaton keresztül az Azure-ba. Ez a cikk a Data Box Gateway eszköz konfigurációs és felügyeleti feladatait ismerteti. Az eszköz kezeléséhez használhatja a Azure Portal, a helyi webes felhasználói felületet vagy a Windows PowerShell felületét is.

Ez a cikk a PowerShell-felületet használó feladatokra összpontosít.

Ez a cikk az alábbi eljárásokat tartalmazza:

- Csatlakozás a PowerShell-felülethez
- Támogatási csomag létrehozása
- Tanúsítvány feltöltése
- Rendszerindítás nem DHCP-környezetben
- Eszköz adatainak megtekintése

## <a name="connect-to-the-powershell-interface"></a>Csatlakozás a PowerShell-felülethez

[!INCLUDE [Connect to admin runspace](../../includes/data-box-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Támogatási csomag létrehozása

[!INCLUDE [Create a support package](../../includes/data-box-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Tanúsítvány feltöltése

[!INCLUDE [Upload certificate](../../includes/data-box-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Rendszerindítás nem DHCP-környezetben

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Eszköz adatainak megtekintése

[!INCLUDE [View device information](../../includes/data-box-gateway-view-device-info.md)]

## <a name="next-steps"></a>Következő lépések

- Az [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) üzembe helyezése az Azure Portalon.
