---
title: Azure Cloud Shell korlátozások | Microsoft Docs
description: A Azure Cloud Shell korlátainak áttekintése
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 092dccab82326bb9983f11ff64fe50aee7b1084d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74951479"
---
# <a name="limitations-of-azure-cloud-shell"></a>Azure Cloud Shell korlátozásai

A Azure Cloud Shell a következő ismert korlátozásokkal rendelkezik:

## <a name="general-limitations"></a>Általános korlátozások

### <a name="system-state-and-persistence"></a>Rendszerállapot és adatmegőrzés

A Cloud Shell-munkamenetet biztosító számítógép ideiglenes, és újraindul, miután a munkamenet 20 percig inaktív. Cloud Shell szükség van egy Azure-fájlmegosztás csatlakoztatására. Ennek eredményeképpen az előfizetésnek képesnek kell lennie a tárolási erőforrások beállítására a Cloud Shell eléréséhez. További szempontok a következők:

* A csatlakoztatott tárolóval csak a könyvtárban lévő módosítások `$Home` maradnak meg.
* Az Azure-fájlmegosztást csak a [hozzárendelt régióból](persisting-shell-storage.md#mount-a-new-clouddrive)lehet csatlakoztatni.
  * A Bashben futtassa a parancsot, `env` hogy megkeresse a régióját `ACC_LOCATION` .

### <a name="browser-support"></a>Böngészőtámogatás

Cloud Shell támogatja a Microsoft Edge, a Microsoft Internet Explorer, a Google Chrome, a Mozilla Firefox és az Apple Safari legújabb verzióit. A Safari privát módban nem támogatott.

### <a name="copy-and-paste"></a>Másolás és beillesztés

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Egy adott felhasználó esetében csak egy rendszerhéj lehet aktív

A felhasználók egyszerre csak egy típusú rendszerhéjat indíthatnak el, vagy **bash** vagy **PowerShell** használatával. Előfordulhat azonban, hogy egyszerre több bash-vagy PowerShell-példány is fut. A bash vagy a PowerShell közötti váltás a menü használatával Cloud Shell újraindítását eredményezi, amely leállítja a meglévő munkameneteket. Azt is megteheti, hogy a bash-t a PowerShellen belül futtatja, és beírja a `bash` PowerShellt a bashbe `pwsh` .

### <a name="usage-limits"></a>Használati korlátok

A Cloud Shell interaktív használati esetekhez készült. Ennek eredményeképpen a hosszan futó, nem interaktív munkamenetek figyelmeztetés nélkül megszűnnek.

## <a name="bash-limitations"></a>Bash-korlátozások

### <a name="user-permissions"></a>Felhasználói engedélyek

Az engedélyek a sudo-hozzáférés nélküli normál felhasználóként vannak beállítva. A címtáron kívüli telepítések `$Home` nem maradnak meg.

### <a name="editing-bashrc-or-profile"></a>. Bashrc vagy $PROFILE szerkesztése

A. bashrc vagy a PowerShell $PROFILE fájljának szerkesztésekor körültekintően járjon el, ami váratlan hibákat okozhat a Cloud Shellban.

## <a name="powershell-limitations"></a>PowerShell-korlátozások

### <a name="azuread-module-name"></a>`AzureAD` modul neve

A `AzureAD` modul neve jelenleg a `AzureAD.Standard.Preview` modulban ugyanazokat a funkciókat biztosítja.

### <a name="sqlserver-module-functionality"></a>`SqlServer` modul funkciói

A `SqlServer` Cloud Shell részét képező modul csak a PowerShell Core támogatásának előzetes kiadását támogatja. Különösen a `Invoke-SqlCmd` még nem érhető el.

### <a name="default-file-location-when-created-from-azure-drive"></a>Alapértelmezett fájl helye az Azure Drive-ból való létrehozáskor:

A PowerShell-parancsmagok használatával a felhasználók nem hozhatnak létre fájlokat az Azure: meghajtón. Ha a felhasználók más eszközökkel, például a Vim vagy a nano eszközzel hoznak létre új fájlokat, a rendszer alapértelmezés szerint menti a fájlokat `$HOME` . 

### <a name="gui-applications-are-not-supported"></a>A GUI-alkalmazások nem támogatottak

Ha a felhasználó olyan parancsot futtat, amely egy Windows-párbeszédpanelt hoz létre, akkor egy hibaüzenet jelenik meg, például: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)` .

### <a name="large-gap-after-displaying-progress-bar"></a>Nagy közök a folyamatjelző sáv megjelenítése után

Ha a felhasználó olyan műveletet hajt végre, amely egy folyamatjelzőt jelenít meg, például egy, a meghajtón eltöltött lapot `Azure:` , akkor lehetséges, hogy a kurzor nincs megfelelően beállítva, és egy hézag jelenik meg, ahol a folyamatjelző sáv korábban volt.

## <a name="next-steps"></a>Következő lépések

[Hibaelhárítási Cloud Shell](troubleshooting.md) <br>
[Rövid útmutató a Bash-hez](quickstart.md) <br>
[Rövid útmutató a PowerShellhez](quickstart-powershell.md)
