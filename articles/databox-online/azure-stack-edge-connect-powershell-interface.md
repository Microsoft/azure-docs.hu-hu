---
title: Microsoft Azure Stack Edge-eszköz csatlakoztatása és kezelése a Windows PowerShell felületén keresztül | Microsoft Docs
description: Ismerteti, hogyan csatlakozhat a szolgáltatáshoz, majd hogyan felügyelheti Azure Stack Edge-t a Windows PowerShell felületén keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: 973c618b46d1b6be902d9629ca63ee120cae6855
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85313218"
---
# <a name="manage-an-azure-stack-edge-device-via-windows-powershell"></a>Azure Stack Edge-eszköz kezelése a Windows PowerShell használatával

Azure Stack Edge-megoldás lehetővé teszi az adatfeldolgozást és a hálózaton keresztüli küldését az Azure-ba. Ez a cikk az Azure Stack Edge-eszköz konfigurációs és felügyeleti feladatait ismerteti. Az eszköz kezeléséhez használhatja a Azure Portal, a helyi webes felhasználói felületet vagy a Windows PowerShell felületét is.

Ez a cikk a PowerShell-felületet használó feladatokra összpontosít. 

Ez a cikk az alábbi eljárásokat tartalmazza:

- Kapcsolódás a PowerShell felületéhez
- Támogatási csomag létrehozása
- Tanúsítvány feltöltése
- Az eszköz alaphelyzetbe állítása
- Eszköz adatainak megtekintése
- Számítási naplók beolvasása
- Számítási modulok figyelése és hibakeresése

## <a name="connect-to-the-powershell-interface"></a>Kapcsolódás a PowerShell felületéhez

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Támogatási csomag létrehozása

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Tanúsítvány feltöltése

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

IoT Edge tanúsítványokat is feltölthet, hogy biztonságos kapcsolatot lehessen létesíteni a IoT Edge-eszköz és a hozzájuk kapcsolódó alsóbb rétegbeli eszközök között. Három IoT Edge tanúsítvány (*. PEM* formátum) szükséges a telepítéséhez:

- Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány vagy a tulajdonos HITELESÍTÉSSZOLGÁLTATÓja
- Eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa
- Eszköz kulcsának tanúsítványa

A következő példa a parancsmag használatát mutatja be IoT Edge tanúsítványok telepítéséhez:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
A parancsmag futtatásakor a rendszer kérni fogja a hálózati megosztás jelszavának megadását.

A tanúsítványokkal kapcsolatos további információkért nyissa meg a [Azure IoT Edge tanúsítványokat](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) , vagy [telepítse a tanúsítványokat egy átjáróra](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Eszköz adatainak megtekintése
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Eszköz alaphelyzetbe állítása

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Számítási naplók beolvasása

Ha a számítási szerepkör konfigurálva van az eszközön, a számítási naplókat a PowerShell felületén keresztül is lekérheti.

1. [Kapcsolódjon a PowerShell felületéhez](#connect-to-the-powershell-interface).
2. Az `Get-AzureDataBoxEdgeComputeRoleLogs` eszközhöz tartozó számítási naplók beszerzéséhez használja a következőt:.

    A következő példa a parancsmag használatát mutatja be:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Itt látható a parancsmaghoz használt paraméterek leírása:
    - `Path`: Adja meg annak a megosztásnak a hálózati elérési útját, ahol létre szeretné hozni a számítási napló csomagot.
    - `Credential`: Adja meg a hálózati megosztáshoz tartozó felhasználónevet. A parancsmag futtatásakor meg kell adnia a megosztási jelszót.
    - `FullLogCollection`: Ez a paraméter biztosítja, hogy a naplófájl tartalmazza az összes számítási naplót. Alapértelmezés szerint a naplófájl csak a naplók egy részhalmazát tartalmazza.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Számítási modulok figyelése és hibakeresése

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/azure-stack-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Kilépés a távoli munkamenetből

A távoli PowerShell-munkamenetből való kilépéshez zárja be a PowerShell ablakát.

## <a name="next-steps"></a>További lépések

- [Azure stack Edge](azure-stack-edge-deploy-prep.md) üzembe helyezése Azure Portalban.
