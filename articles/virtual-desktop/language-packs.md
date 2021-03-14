---
title: Nyelvi csomagok telepítése Windows 10 rendszerű virtuális gépekre a Windows Virtual Desktopban – Azure
description: Windows 10 rendszerű, többmunkamenetes virtuális gépek nyelvi csomagjainak telepítése a Windows Virtual Desktopban.
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fc7892db2ca11ab7970835f8979360961ee01104
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463340"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Nyelvi csomagok hozzáadása Windows 10 több munkamenetes rendszerképhez

A Windows virtuális asztal egy olyan szolgáltatás, amelyet a felhasználók bármikor, bárhol üzembe helyezhetnek. Ezért fontos, hogy a felhasználók testre tudják szabni, hogy a Windows 10 Enterprise multi-session rendszerkép milyen nyelven jelenjen meg.

Kétféleképpen lehet alkalmazkodni a felhasználók nyelvi igényeihez:

- Dedikált gazdagép-készleteket hozhat létre az egyes nyelvekhez testreszabott képpel.
- Ugyanazon a gazdagépen eltérő nyelvi és honosítási követelményeknek kell megfelelniük a felhasználóknak, de a képek testreszabásával biztosítható, hogy a kívánt nyelv közül melyikre van szükségük.

Az utóbbi módszer sokkal hatékonyabb és költséghatékony. Azonban Ön dönti el, hogy melyik módszer felel meg legjobban az igényeinek. Ez a cikk bemutatja, hogyan szabhatja testre a képek nyelveit.

## <a name="prerequisites"></a>Előfeltételek

Több nyelv hozzáadásához a következő dolgokra van szükség a Windows 10-es nagyvállalati multi-session lemezképek testreszabásához:

- Azure-beli virtuális gép (VM) a Windows 10 Enterprise multi-session, 1903-es vagy újabb verziójával

- Az ISO, az igény szerinti szolgáltatás (Franciaország tengerentúli) 1. lemeze és a beérkezett fájlok mappa ISO-je a lemezkép által használt operációsrendszer-verzióhoz. Innen töltheti le őket:
     
     - Nyelv ISO:
        - [Windows 10, 1903-es vagy 1909-es nyelvi csomag ISO-verziója](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, 2004 verzió vagy 20H2 Language Pack ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - Franciaország tengerentúli lemeze 1 ISO:
        - [Windows 10, 1903-es vagy 1909-es verziójú, 1. lemez ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, 2004-es vagy 20H2-es verzió, 1. lemez ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - Beérkezett alkalmazások ISO:
        - [Windows 10, 1903-es vagy 1909-es bejövő alkalmazások ISO-verziója](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, 2004-es verziójú beérkezett alkalmazások ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, 20H2 beérkezett fájlok alkalmazásának ISO-verziója](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Ha a lemezképek honosításához a helyi Experience Pack (LXP) ISO-fájlokat használja, akkor a legjobb nyelvi élmény érdekében le kell töltenie a megfelelő LXP ISO-t is.
        - Ha a Windows 10 1903-es vagy 1909-es verzióját használja:
          - [Windows 10, 1903-es vagy 1909-es verziójú LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Ha a Windows 10 2004-es vagy 20H2-es verzióját használja, használja a [nyelvek hozzáadása a Windows 10 rendszerben: ismert problémák](/windows-hardware/manufacture/desktop/language-packs-known-issue) , amelyek alapján megállapíthatja, hogy a következő LXP ISO-verziók közül melyik a legmegfelelőbb:
          - [Windows 10, 2004 verzió vagy 20H2 **9b** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10, 2004 verzió vagy 20H2 **9C** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10, 2004 verzió vagy 20H2 **10C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10, 2004 verzió vagy 20H2 **11C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10, 2004-es verzió vagy 20H2 **1c** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [Windows 10, 2004-es verzió vagy 20H2 **2c** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)

- Egy Azure Files megosztás vagy fájlmegosztás egy Windows fájlkiszolgáló virtuális gépen

>[!NOTE]
>A fájlmegosztás (adattár) számára elérhetőnek kell lennie az egyéni rendszerkép létrehozásához használni kívánt Azure-beli virtuális gépről.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>A nyelvi csomagok és szolgáltatások igény szerinti tárházának létrehozása

A nyelvi csomagok és FODs, valamint a beérkezett fájlok mappáinak tárházának létrehozása a következő helyen:

1. Egy Azure-beli virtuális gépen töltse le a Windows 10-es multi-Language ISO, a FODs és a beérkezett fájlok alkalmazásait a Windows 10 Enterprise multi-session, a 1903/1909-es és a 2004-es verzióra az [Előfeltételek](#prerequisites)hivatkozásaiban.

2. Nyissa meg és csatlakoztassa az ISO-fájlokat a virtuális gépen.

3. Nyissa meg a Language Pack ISO-t, és másolja a tartalmat a **LocalExperiencePacks** és az **x64 \\ Langpacks** mappából, majd illessze be a tartalmat a fájlmegosztásba.

4. Nyissa meg az Franciaország-beli **ISO-fájlt**, másolja ki az összes tartalmát, majd illessze be a fájlmegosztást.
5. Nyissa meg a beérkezett fájlok **amd64fre** mappáját, és másolja a tárházban lévő tartalmat az előkészített Beérkezett üzenetek mappájába.

     >[!NOTE]
     > Ha korlátozott tárterülettel dolgozik, csak azokat a nyelveket másolja, amelyekről a felhasználók számára szüksége van. A fájlokat egymástól eldöntheti, ha a fájlneveit a nyelvi kódokat keresi. A francia fájlban például a "fr-FR" kód szerepel a névben. Az összes elérhető nyelvhez tartozó nyelvi kódok teljes listájáért lásd: [elérhető nyelvi csomagok a Windowshoz](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

     >[!IMPORTANT]
     > Bizonyos nyelvekhez további betűkészletek szükségesek a különböző elnevezési konvenciókat követő szatellit csomagokban. A japán betűkészletek nevei például a következők: "Jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![A japán nyelvi csomagok példája a "Jpan" nyelvi címkével a fájlnevekben.](media/language-pack-example.png)

6. Állítsa be az engedélyeket a nyelvi tartalom tárházának megosztására, hogy rendelkezzen olvasási hozzáféréssel az egyéni rendszerkép létrehozásához használni kívánt virtuális gépről.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Egyéni Windows 10 Enterprise több munkamenetből álló rendszerkép manuális létrehozása

Egyéni Windows 10 Enterprise több munkamenetből álló rendszerkép manuális létrehozása:

1. Helyezzen üzembe egy Azure-beli virtuális gépet, majd nyissa meg az Azure-katalógust, és válassza ki a Windows 10 Enterprise-munkamenet aktuális verzióját, amelyet használ.
2. A virtuális gép üzembe helyezése után helyi rendszergazdaként az RDP használatával csatlakozhat hozzá.
3. Győződjön meg arról, hogy a virtuális gép rendelkezik a legújabb Windows-frissítésekkel. Töltse le a frissítéseket, és szükség esetén indítsa újra a virtuális gépet.
4. Kapcsolódjon a nyelvi csomaghoz, Franciaország tengerentúli megyéihez és a beérkezett alkalmazások fájlmegosztás-tárházához, és csatlakoztassa egy betűjelű meghajtóhoz (például az E meghajtóra).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Egyéni Windows 10 Enterprise több munkamenetből álló rendszerkép automatikus létrehozása

Ha inkább egy automatizált folyamaton keresztül szeretné telepíteni a nyelveket, beállíthat egy parancsfájlt a PowerShellben. A következő parancsfájl-minta használatával telepítheti a spanyol (spanyolországi), francia (franciaországi) és kínai (PRC) nyelvi csomagokat és a Satellite csomagokat a Windows 10 Enterprise multi-session, 2004-es verzióra. A parancsfájl integrálja a nyelvi kezelőfelületi csomagot és az összes szükséges műhold-csomagot a rendszerképbe. Ezt a szkriptet azonban módosíthatja más nyelvek telepítéséhez is. Csak futtassa a parancsfájlt egy emelt szintű PowerShell-munkamenetből, vagy más nem fog működni.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

A parancsfájl eltarthat egy ideig a telepítendő nyelvek számától függően.

Ha a parancsfájl futása befejeződött, ellenőrizze, hogy a nyelvi csomagok megfelelően vannak-e telepítve, **majd a**  >  **Beállítások**  >  **időpontja & a nyelv**  >  **nyelve**. Ha a nyelvi fájlok vannak, akkor minden be van állítva.

Miután további nyelveket adott hozzá a Windows-rendszerképhez, a beérkezett fájlok alkalmazásait is frissíteni kell a hozzáadott nyelvek támogatásához. Ezt úgy teheti meg, hogy az előre telepített alkalmazásokat a beérkezett fájlok ISO-ból származó tartalommal együtt frissít. A frissítés leválasztott környezetben való végrehajtásához (nem lehetséges a virtuális gép internet-hozzáférése) az alábbi PowerShell-parancsfájl segítségével automatizálhatja a folyamatot.

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>Az ISO-ben található beérkezett fájlok alkalmazás nem az előre telepített Windows-alkalmazások legújabb verziói. Az összes alkalmazás legújabb verziójának lekéréséhez frissítenie kell az alkalmazásokat a Windows áruházbeli alkalmazással, és a további nyelvek telepítése után manuálisan kell keresnie a frissítéseket.

Ha elkészült, győződjön meg róla, hogy leválasztja a megosztást.

## <a name="finish-customizing-your-image"></a>A rendszerkép testre szabásának befejezése

A nyelvi csomagok telepítése után telepítheti a testreszabott rendszerképbe felvenni kívánt egyéb szoftvereket is.

Miután végzett a rendszerkép testreszabásával, futtatnia kell a rendszer-előkészítő eszközt (Sysprep).

A Sysprep futtatása:

1. Nyisson meg egy rendszergazda jogú parancssort, és futtassa a következő parancsot a rendszerkép általánosítása érdekében:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Állítsa le a virtuális gépet, majd rögzítse azt egy felügyelt lemezképben az [általánosított virtuális gép felügyelt lemezképének létrehozása az Azure-ban](../virtual-machines/windows/capture-image-resource.md)című témakör utasításait követve.

3. Mostantól a testreszabott lemezkép használatával üzembe helyezhet egy Windows rendszerű virtuális asztali címkészletet. A gazdagépek központi telepítésének megismeréséhez tekintse meg [a következő oktatóanyagot: állomáslista létrehozása a Azure Portal](create-host-pools-azure-marketplace.md).

## <a name="enable-languages-in-windows-settings-app"></a>Nyelvek engedélyezése a Windows beállításai alkalmazásban

Végül, miután telepítette a gazdagépet, hozzá kell adnia a nyelvet az egyes felhasználók nyelvi listájához, hogy a beállítások menüben ki tudják választani a kívánt nyelvet.

Annak biztosítása érdekében, hogy a felhasználók kiválaszthatják a telepített nyelveket, jelentkezzen be felhasználóként, majd futtassa a következő PowerShell-parancsmagot a telepített nyelvi csomagok hozzáadásához a nyelvek menühöz. A parancsfájlt beállíthatja automatikus feladat vagy bejelentkezési parancsfájlként is, amely akkor aktiválódik, amikor a felhasználó bejelentkezik a munkamenetbe.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Miután a felhasználó módosítja a nyelvi beállításokat, ki kell jelentkeznie a Windows rendszerű virtuális asztali munkamenetből, és újra be kell jelentkeznie a módosítások érvénybe léptetéséhez. 

## <a name="next-steps"></a>Következő lépések

Ha kíváncsi a nyelvi csomagok ismert problémáira, tekintse meg [a nyelvi csomagok hozzáadása a Windows 10 1803-es és újabb verzióiban: ismert problémák](/windows-hardware/manufacture/desktop/language-packs-known-issue)című témakört.

Ha bármilyen egyéb kérdése van a Windows 10-es nagyvállalati multi-Sessions szolgáltatással kapcsolatban, tekintse meg a [gyakori](windows-10-multisession-faq.md)kérdéseket.
