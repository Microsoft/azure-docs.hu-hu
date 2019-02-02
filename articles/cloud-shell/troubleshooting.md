---
title: Az Azure Cloud Shell – hibaelhárítás |} A Microsoft Docs
description: Hibaelhárítás az Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 73f2e7a37e1e51bf215cbac782b454d909f275dc
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568532"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Hibaelhárítási & korlátozások az Azure Cloud Shell

Ismert megoldásuk kapcsolatos hibák elhárítása az Azure Cloud Shellben a következők:

## <a name="general-troubleshooting"></a>Általános hibaelhárítási tippek

### <a name="early-timeouts-in-firefox"></a>Korai időtúllépések, a Firefoxban

- **Részletek**: A cloud Shell egy bemeneti/kimeneti átadása a böngészőben nyissa meg websocket használja. A FireFox, bezárhatja a websocket túl korán okozó korai időtúllépések a Cloud Shellben előre beállított házirendek rendelkezik.
- **Megoldás**: Nyissa meg a FireFox, és navigáljon a "kapcsolatos: config" az URL mezőbe. Keresse meg a "network.websocket.timeout.ping.request", és módosítsa az értéket 0 és 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>A Cloud Shell letiltása zárolt hálózati környezet

- **Részletek**: Előfordulhat, hogy a rendszergazdák szeretne tiltsa le a hozzáférést a Cloud Shellbe a felhasználók számára. A cloud Shell használja a hozzáférést a `ux.console.azure.com` tartományt, amely megtagadható, férjen hozzá a Cloud Shell entrypoints, beleértve a portal.azure.com, a shell.azure.com, a Visual Studio Code az Azure-fiók bővítmény és a docs.microsoft.com leállítása.
- **Megoldás**: Korlátozza a hozzáférést a `ux.console.azure.com` hálózati beállításokat, és a környezetben keresztül. A Cloud Shell ikon a Portal.Azure.com címen továbbra is megmarad, de fog nem sikerült kapcsolódni a szolgáltatáshoz.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Tároló párbeszédpanelen - hiba: 403-as RequestDisallowedByPolicy

- **Részletek**: A Cloud Shell – tárfiók létrehozása esetén a rendszergazda által elhelyezett egy az Azure policy miatt volt sikertelen Hibaüzenet jelenik meg a következőket tartalmazzák: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Megoldás**: Távolítsa el vagy frissítse a tagadja meg a tároló létrehozása az Azure policy az Azure rendszergazdával.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Tároló párbeszédpanelen - hiba: 400 DisallowedOperation

- **Részletek**: Az Azure Active Directory-előfizetés használata esetén nem hozható létre tároló.
- **Megoldás**: Használja a tároló-erőforrásokat hozhat létre Azure-előfizetéssel. Az Azure-erőforrások létrehozása az Azure AD-előfizetések nem jelennek meg.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminálszolgáltatások kimenete – hiba: Nem sikerült kapcsolódni a terminálon: websocket nem hozható létre. Nyomja meg `Enter` az újracsatlakozáshoz.
- **Részletek**: A cloud Shell lehetővé teszi a Cloud Shell infrastruktúra websocket kapcsolatot igényel.
- **Megoldás**: Check you have configured your network settings to enable sending https requests and websocket requests to domains at *.console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Támogatja a TLS 1.2 használatával a Cloud Shell-kapcsolat beállítása
 - **Részletek**: A TLS verzióját a kapcsolat definiálása a Cloud Shellbe, be kell böngésző egyedi beállításait.
 - **Megoldás**: Keresse meg a böngésző biztonsági beállításait, és válassza a "Használja a TLS 1.2-es" jelölőnégyzetét.

## <a name="bash-troubleshooting"></a>Bash-hibaelhárítás

### <a name="cannot-run-the-docker-daemon"></a>A docker-démon nem futtatható.

- **Részletek**: A cloud Shell egy tárolót a gazdagép a rendszerhéj-környezetet használja, ezért fut a démon nem engedélyezett.
- **Megoldás**: Vételéhez [docker-machine](https://docs.docker.com/machine/overview/), amely alapértelmezés szerint az kezelése a docker-tárolók egy távoli Docker gazdagépen települ.

## <a name="powershell-troubleshooting"></a>PowerShell-hibaelhárítás

### <a name="gui-applications-are-not-supported"></a>Grafikus felhasználói Felülettel alkalmazások nem támogatottak.

- **Részletek**: Ha egy felhasználó elindít egy grafikus felhasználói felület alkalmazás, a rendszer nem adja vissza. Például egy klónozza, privát GitHub-tárházba, amely rendelkezik a kéttényezős hitelesítés engedélyezve van, ha egy párbeszédpanel jelenik meg a kéttényezős hitelesítés elvégzése a.
- **Megoldás**: Zárja be és nyissa meg újra a rendszerhéjat.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Hibaelhárítás Azure virtuális gépek távoli felügyelete
> [!NOTE]
> Az Azure virtuális gépek rendelkeznie kell egy nyilvános IP-cím elérhető.

- **Részletek**: A Rendszerfelügyeleti webszolgáltatások a Windows tűzfal alapértelmezett beállításai miatt a felhasználó a következő hiba jelenhet meg: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Megoldás**:  Futtatás `Enable-AzureRmVMPSRemoting` engedélyezése a PowerShell-táveléréssel a célgépen minden aspektusát.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` az eredmény az Azure-meghajtó nem frissül

- **Részletek**: Alapértelmezés szerint az Optimalizálás a felhasználói élmény, eredményeit `dir` tárolja a rendszer Azure-meghajtó.
- **Megoldás**: Miután hoz létre, frissíteni vagy eltávolítani egy Azure-erőforrás, futtassa `dir -force` frissíteni az eredményeket az Azure-meghajtó.

## <a name="general-limitations"></a>Általános korlátozások

Az Azure Cloud Shell a következő ismert korlátozások vonatkoznak:

### <a name="system-state-and-persistence"></a>Rendszerállapot és adatmegőrzés

A gép, amely biztosítja a Cloud Shell-munkamenetek ideiglenes, és legyen újrahasznosítása után a munkamenet a 20 percig inaktív. A cloud Shell Azure-fájlmegosztás csatlakoztatása a szükséges. Az előfizetés ennek eredményeképpen a Cloud Shell eléréséhez a tárolási erőforrások beállításához képesnek kell lennie. Egyéb szempontok közé tartoznak:

- Csatlakoztatott tárolóval, csak a módosítások belül a `clouddrive` könyvtár tárolja. A Bash a `$HOME` is megőrzi a könyvtárban van.
- Azure-fájlmegosztások csak a csatlakoztathatók a [régió hozzárendelt](persisting-shell-storage.md#mount-a-new-clouddrive).
  - Futtassa a Bash, `env` állítja be a régióban található `ACC_LOCATION`.
- Az Azure Files csak helyileg redundáns tárolás és a georedundáns tárolás fiókokat támogatja.

### <a name="browser-support"></a>Böngésző támogatása

A cloud Shell az alábbi böngészők legújabb verzióit támogatja:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari böngészőt privát üzemmódban nem támogatott.

### <a name="copy-and-paste"></a>Másolás és beillesztés

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Egy adott felhasználó csak egy parancshéj lehet aktív.

Csak indíthatják egyfajta rendszerhéj egyszerre, vagy **Bash** vagy **PowerShell**. Előfordulhat azonban, Bash- vagy PowerShell fut egyszerre több példányát. Érvényesítheti a Bash- vagy PowerShell okok Cloud Shell újraindítása, amely befejezi a meglévő munkameneteket között.

### <a name="usage-limits"></a>Használati korlátozások

A cloud Shell interaktív használati esetek szól. Ennek eredményeképpen minden olyan hosszan futó nem interaktív munkamenet befejeződik figyelmeztetés nélkül.

### <a name="user-permissions"></a>Felhasználói engedélyek

Engedélyek beállítása normál felhasználóként sudo hozzáférés nélkül. Minden olyan telepítési kívül a `$Home` directory nincs megőrizve.

## <a name="bash-limitations"></a>Bash-korlátozások

### <a name="editing-bashrc"></a>.Bashrc szerkesztése

Legyen körültekintő elvégzendő .bashrc, így szerkesztési váratlan hibákat eredményezhet a Cloud Shellben.

## <a name="powershell-limitations"></a>PowerShell-korlátozások

### <a name="preview-version-of-azuread-module"></a>Azure ad-modul előzetes verziójában

Jelenleg `AzureAD.Standard.Preview`, a .NET Standard-alapú, a modul előzetes verziója érhető el. Ez a modul adja meg ugyanazokat a funkciókat, mint `AzureAD`.

### <a name="sqlserver-module-functionality"></a>`SqlServer` a modul funkció

A `SqlServer` modul tartalmazza a Cloud Shellben a PowerShell Core csak előzetes támogatással rendelkezik. Különösen `Invoke-SqlCmd` még nem áll rendelkezésre.

### <a name="default-file-location-when-created-from-azure-drive"></a>Fájl alapértelmezett helye az Azure-meghajtó létrehozása

PowerShell-parancsmagok használatával felhasználók az Azure-meghajtó a fájlok nem hozható létre. Amikor a felhasználó más eszközökkel, például vim vagy nano, új fájlok létrehozása a fájlok menti, és a `$HOME` alapértelmezés szerint.

### <a name="commands-that-create-gui-pop-ups-are-not-supported"></a>Grafikus felhasználói Felülettel felugró ablakokat hoznak létre parancsok nem támogatottak.

Ha egy felhasználó futtat egy parancsot kell létrehoznia egy Windows párbeszédablak, mint például `Connect-AzureAD`, `Connect-AzureRmAccount`, vagy `Connect-AzAccount`, például kap egy hibaüzenetet: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="tab-completion-can-throw-psreadline-exception"></a>Kiegészítés nagyvállalat PSReadline kivétel

Ha a felhasználó PSReadline Szerkesztőmódba Emacs értékre van állítva, a felhasználó megpróbálja keresztül kiegészítés, az összes lehetőség megjelenítéséhez és az ablak mérete túl kicsi összes lehetőség megjelenítéséhez, PSReadline kivételt fogja kijelezni nem kezelt kivétel.

### <a name="large-gap-after-displaying-progress-bar"></a>Miután a folyamatjelző sáv megjelenítése nagy közök

Ha egy parancs vagy felhasználói művelet egy folyamatjelző, ezen a lapon az épp közben a `Azure:` meghajtó, akkor lehetséges, hogy a kurzor nincs megfelelően beállítva, és eseményáramlási kimaradást jelenik meg, ahol a folyamatjelző sáv korábban volt.

### <a name="random-characters-appear-inline"></a>Véletlenszerű karakter beágyazott jelennek meg.

A kurzor pozíciója feladatütemezési kódjai, például `5;13R`, a felhasználói bevitel is megjelennek. A karakterek manuálisan távolíthatja el.

## <a name="personal-data-in-cloud-shell"></a>A Cloud Shellben személyes adatok

Az Azure Cloud Shell komolyan veszi a személyes adatokat, a rögzített, és az Azure Cloud Shell szolgáltatás által tárolt adatok alapértelmezett beállításokat biztosítsanak a élményt biztosít, mint például a legutóbb használt shell segítségével, előnyben részesített betűméret, előnyben részesített betűtípus, és a fájlmegosztás részletei felhő, amely vissza a meghajtó. Meg szeretne exportálni, vagy törölje ezeket az adatokat, kövesse az alábbi utasításokat.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportálás
Annak érdekében, hogy **exportálása** a Cloud Shell menti, például a felhasználói beállítások előnyben részesített felület, betűméret és betűtípus, futtassa a következő parancsokat.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell indítása")](https://shell.azure.com)
2. Futtassa az alábbi parancsokat a Bash vagy a PowerShell használatával:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Törlés
Annak érdekében, hogy **törlése** a Cloud Shell menti, például a felhasználói beállítások előnyben részesített felület, betűméret és betűtípus, futtassa a következő parancsokat. A Cloud Shell következő indításakor kell adnia a munkába egy fájlmegosztás újra. 

>[!Note]
> Ha törli a felhasználói beállítások, a tényleges Azure-fájlmegosztást nem törlődnek. Nyissa meg az Azure Files, a művelet végrehajtásához.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell indítása")](https://shell.azure.com)
2. Futtassa az alábbi parancsokat a Bash vagy a PowerShell használatával:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
