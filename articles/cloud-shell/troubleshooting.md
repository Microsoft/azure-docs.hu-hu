---
title: Azure Cloud Shell hibaelhárítás | Microsoft Docs
description: Hibaelhárítási Azure Cloud Shell
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
ms.openlocfilehash: eea64520dd5440467c911b6de42d8c8c31fc1bde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87543452"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>A Azure Cloud Shell korlátozásának hibaelhárítása &

A Azure Cloud Shell hibaelhárítási hibáinak ismert megoldásai a következők:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Általános hibaelhárítás

### <a name="error-running-azuread-cmdlets-in-powershell"></a>Hiba a AzureAD-parancsmagok PowerShellben való futtatásakor

- **Részletek**: Ha a AzureAD-parancsmagokat (például `Get-AzureADUser` Cloud Shell) futtatja, a következő hibaüzenet jelenhet meg: `You must call the Connect-AzureAD cmdlet before calling any other cmdlets` . 
- **Megoldás**: futtassa a `Connect-AzureAD` parancsmagot. Korábban Cloud Shell a parancsmagot automatikusan futtatta a PowerShell indításakor. A kezdési idő felgyorsításához a parancsmag már nem fut automatikusan. Az előző viselkedés visszaállítását úgy is elvégezheti, ha hozzáadja `Connect-AzureAD` a $profile fájlhoz a PowerShellben.

### <a name="early-timeouts-in-firefox"></a>Korai időtúllépés a Firefoxban

- **Részletek**: a Cloud shell egy nyitott WebSocket használatával továbbítja a bemenetet/kimenetet a böngészőjébe. A FireFox előre beállított szabályzatokkal rendelkezik, amelyek a WebSocket idő előtt lezárhatók, ami korai időtúllépéseket okoz a Cloud Shellban.
- **Megoldás**: Nyissa meg a Firefoxot, és navigáljon a "about: config" kifejezésre az URL-cím mezőben. Keressen rá a "Network. WebSocket. timeout. ping. Request" kifejezésre, és módosítsa a 0 és 10 közötti értéket.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Cloud Shell letiltása zárolt hálózati környezetben

- **Részletek**: a rendszergazdák letilthatják a Cloud Shell hozzáférését a felhasználók számára. Cloud Shell hozzáfér a `ux.console.azure.com` tartományhoz, amely megtagadható, leállíthatja a Cloud Shell entrypoints való hozzáférést, beleértve a Portal.Azure.com, a shell.Azure.com, a Visual Studio Code Azure-fiók bővítményét és a docs.microsoft.com. Az Egyesült államokbeli kormányzati felhőben a BelépésiPont a `ux.console.azure.us` megfelelő shell.Azure.us.
- **Megoldás**: `ux.console.azure.com` `ux.console.azure.us` a hálózati beállításokon keresztüli hozzáférés korlátozása a környezethez. A Cloud Shell ikon továbbra is szerepel a Azure Portalban, de nem fog sikeresen csatlakozni a szolgáltatáshoz.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Tárolási párbeszédpanel – hiba: 403 RequestDisallowedByPolicy

- **Részletek**: Storage-fiók Cloud Shellon keresztüli létrehozásakor a rendszergazda által elhelyezett Azure Policy-hozzárendelés miatt sikertelen. A következő hibaüzenetet fogja tartalmazni: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Megoldás**: vegye fel a kapcsolatot az Azure rendszergazdájával, és távolítsa el vagy frissítse a tároló létrehozását megtagadó Azure Policy-hozzárendelést.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Tárolási párbeszédpanel – hiba: 400 DisallowedOperation

- **Részletek**: Azure Active Directory-előfizetés használata esetén nem hozható létre tároló.
- **Megoldás**: tárolási erőforrások létrehozására alkalmas Azure-előfizetés használata. Az Azure AD-előfizetések nem tudnak Azure-erőforrásokat létrehozni.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminál kimenete – hiba: nem sikerült csatlakozni a terminálhoz: a WebSocket nem hozható meg. Kattintson `Enter` a gombra az újrakapcsolódáshoz.
- **Részletek**: a Cloud Shell lehetővé teszi WebSocket-kapcsolat létesítését Cloud Shell-infrastruktúrához.
- **Megoldás**: Győződjön meg arról, hogy konfigurálta a hálózati beállításokat, hogy engedélyezze a HTTPS-kérések és WebSocket-kérelmek küldését a következő helyen: *. Console.Azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>A Cloud Shell-kapcsolatok beállítása a TLS 1,2-támogatáshoz
 - **Részletek**: Ha meg szeretné határozni a TLS-verziót a Cloud Shellhoz való kapcsolathoz, meg kell adnia a böngészőre vonatkozó beállításokat.
 - **Megoldás**: navigáljon a böngésző biztonsági beállításaihoz, és jelölje be a "TLS 1,2 használata" melletti jelölőnégyzetet.

## <a name="bash-troubleshooting"></a>Bash-hibaelhárítás

### <a name="cannot-run-the-docker-daemon"></a>Nem futtatható a Docker-démon

- **Részletek**: Cloud shell egy tárolót használ a rendszerhéj-környezet üzemeltetéséhez, mert a démon futtatása nem engedélyezett.
- **Megoldás**: használja a [Docker-Machine-](https://docs.docker.com/machine/overview/)et, amely alapértelmezés szerint telepítve van a Docker-tárolók távoli Docker-gazdagépről való kezeléséhez.

## <a name="powershell-troubleshooting"></a>PowerShell-hibaelhárítás

### <a name="gui-applications-are-not-supported"></a>A GUI-alkalmazások nem támogatottak

- **Részletek**: Ha a felhasználó grafikus felhasználói felületű alkalmazást indít, a kérés nem tér vissza. Ha például egy klón olyan privát GitHub-tárházat tartalmaz, amelynek két faktoros hitelesítése engedélyezve van, megjelenik egy párbeszédpanel a kétfaktoros hitelesítés végrehajtásához.
- **Megoldás**: zárjuk be és nyissa meg újra a rendszerhéjat.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Azure-beli virtuális gépek távoli felügyeletének hibaelhárítása
> [!NOTE]
> Az Azure-beli virtuális gépeknek nyilvános IP-címmel kell rendelkezniük.

- **Részletek**: a Rendszerfelügyeleti webszolgáltatások alapértelmezett Windows tűzfal-beállításai miatt a felhasználó a következő hibát tapasztalhatja: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Megoldás**: futtassa a parancsot a `Enable-AzVMPSRemoting` PowerShell távoli eljáráshívás összes aspektusának engedélyezéséhez a célszámítógépen.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` nem frissíti az eredményt az Azure Drive-ban

- **Részletek**: alapértelmezés szerint a felhasználói élmény optimalizálása érdekében a `dir` rendszer gyorsítótárazza az eredményeket az Azure Drive-ban.
- **Megoldás**: egy Azure-erőforrás létrehozása, frissítése vagy eltávolítása után futtassa a parancsot az `dir -force` eredmények Azure-meghajtón való frissítéséhez.

## <a name="general-limitations"></a>Általános korlátozások

A Azure Cloud Shell a következő ismert korlátozásokkal rendelkezik:

### <a name="quota-limitations"></a>Kvóta korlátai

A Azure Cloud Shell régiónként legfeljebb 20 egyidejű felhasználó lehet. Ha a korlátnál több egyidejű munkamenetet próbál megnyitni, a "bérlői felhasználó kvóta" hibaüzenet jelenik meg. Ha a vállalatnak több munkamenetet kell megnyitnia, mint amennyit (például a betanítási munkamenetek esetében), akkor a várható használat előtt forduljon az ügyfélszolgálathoz, és kérje meg a kvóta növelését.

Cloud Shell ingyenes szolgáltatásként érhető el, és az Azure-környezet konfigurálására szolgál, nem pedig általános célú számítástechnikai platformként. A túlzott automatikus használat az Azure használati feltételeinek megszegése miatt megsérthető, és Cloud Shell hozzáférés blokkolásához vezethet.

### <a name="system-state-and-persistence"></a>Rendszerállapot és adatmegőrzés

A Cloud Shell-munkamenetet biztosító számítógép ideiglenes, és újraindul, miután a munkamenet 20 percig inaktív. Cloud Shell szükség van egy Azure-fájlmegosztás csatlakoztatására. Ennek eredményeképpen az előfizetésnek képesnek kell lennie a tárolási erőforrások beállítására a Cloud Shell eléréséhez. További szempontok a következők:

- A csatlakoztatott tárolóval csak a könyvtárban lévő módosítások `clouddrive` maradnak meg. A bash-ben a `$HOME` könyvtárat is megőrzi a rendszer.
- Az Azure-fájlmegosztást csak a [hozzárendelt régióból](persisting-shell-storage.md#mount-a-new-clouddrive)lehet csatlakoztatni.
  - A Bashben futtassa a parancsot, `env` hogy megkeresse a régióját `ACC_LOCATION` .
- A Azure Files csak a helyileg redundáns tárolást és a földrajzilag redundáns tárolási fiókokat támogatja.

### <a name="browser-support"></a>Böngészőtámogatás

A Cloud Shell a következő böngészők legújabb verzióit támogatja:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - A Safari privát módban nem támogatott.

### <a name="copy-and-paste"></a>Másolás és beillesztés

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Használati korlátok

A Cloud Shell interaktív használati esetekhez készült. Ennek eredményeképpen a hosszan futó, nem interaktív munkamenetek figyelmeztetés nélkül megszűnnek.

### <a name="user-permissions"></a>Felhasználói engedélyek

Az engedélyek a sudo-hozzáférés nélküli normál felhasználóként vannak beállítva. A címtáron kívüli telepítések `$Home` nem maradnak meg.

## <a name="bash-limitations"></a>Bash-korlátozások

### <a name="editing-bashrc"></a>Szerkesztés. bashrc

Legyen körültekintő, ha szerkeszti a. bashrc, így váratlan hibákat okozhat a Cloud Shellban.

## <a name="powershell-limitations"></a>PowerShell-korlátozások

### <a name="preview-version-of-azuread-module"></a>A AzureAD modul előzetes verziója

Jelenleg `AzureAD.Standard.Preview` a .NET Standard-alapú modul előzetes verziója érhető el. Ez a modul ugyanazokat a funkciókat biztosítja, mint a `AzureAD` .

## <a name="personal-data-in-cloud-shell"></a>Személyes adatCloud Shell

Azure Cloud Shell komolyan veszi a személyes adatait, a Azure Cloud Shell szolgáltatás által rögzített és tárolt adatokat, például a legutóbb használt rendszerhéj, az előnyben részesített betűméret, az előnyben részesített betűkészlet típusát és a felhőalapú meghajtóval kapcsolatos fájlmegosztás részleteit. Ha exportálni vagy törölni szeretné ezeket az információkat, kövesse az alábbi utasításokat.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportálás
A felhasználói beállítások **exportálásához** Cloud shell, például az előnyben részesített rendszerhéj, a betűméret és a betűkészlet típusát, futtassa a következő parancsokat.

1. [![A rendszerindító Azure Cloud Shellt jelző gombot ábrázoló kép.](https://shell.azure.com/images/launchcloudshell.png)](https://shell.azure.com)

2. Futtassa a következő parancsokat a bash vagy a PowerShellben:

Bash

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
A felhasználói beállítások **törléséhez** Cloud shell, például az előnyben részesített rendszerhéj, a betűméret és a betűkészlet típusát, futtassa a következő parancsokat. Amikor legközelebb elindítja Cloud Shell a rendszer kérni fogja a fájlmegosztás újbóli bevezetését. 

>[!Note]
> Ha törli a felhasználói beállításokat, a tényleges Azure Files megosztás nem lesz törölve. A művelet végrehajtásához lépjen a Azure Files.

1. [![A rendszerindító Azure Cloud Shellt jelző gombot ábrázoló kép.](https://shell.azure.com/images/launchcloudshell.png)](https://shell.azure.com)

2. Futtassa a következő parancsokat a bash vagy a PowerShellben:

Bash

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Azure Government korlátozások
A Azure Government Azure Cloud Shell csak a Azure Portalon keresztül érhető el.

>[!Note]
> Az Exchange Online-hoz GCC-High vagy kormányzati DoD-Felhőkhöz való csatlakozás jelenleg nem támogatott.
