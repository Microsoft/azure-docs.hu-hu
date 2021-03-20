---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: ca060e75e50a3e2327fc0516c3cfc9550afbf90f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582184"
---
1. [Kapcsolódjon a PowerShell felületéhez](#connect-to-the-powershell-interface).
2. Az `Get-HcsApplianceInfo` eszközhöz tartozó információk beszerzéséhez használja a következőt:.

    A következő példa a parancsmag használatát mutatja be:

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    Itt látható egy táblázat, amely az eszköz néhány fontos adatát összegzi:

    | Paraméter | Leírás |
    |-----------|-------------|
    | FriendlyName                   | Az eszköz felhasználóbarát neve, amely a helyi webes felületen keresztül van konfigurálva az eszköz telepítése során. Az alapértelmezett felhasználóbarát név az eszköz sorozatszáma.  |
    | Sorozatszám                   | Az eszköz sorozatszáma a gyárban hozzárendelt egyedi szám.                                                                             |
    | Modellezés                          | Az eszköz modellje. A modell virtuális Data Box Gatewayhoz.                   |
    | FriendlySoftwareVersion        | Az eszköz szoftverének verziójának megfelelő felhasználóbarát karakterlánc. Az előzetes verziót futtató rendszerek esetében a felhasználóbarát szoftververzió Data Box Edge 1902. |
    | HcsVersion                     | Az eszközön futó HCS FRISSÍTŐÜGYNÖK szoftver verziója. Például a Data Box Edge 1902-nek megfelelő HCS FRISSÍTŐÜGYNÖK-szoftververzió 1.4.771.324.            |
    | LocalCapacityInMb              | Az eszköz teljes helyi kapacitása a megabitben.                                                                                                        |
    | IsRegistered                   | Ez az érték azt jelzi, hogy az eszköz aktiválva van-e a szolgáltatással.                                                                                         |


