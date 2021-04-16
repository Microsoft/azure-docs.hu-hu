---
title: Nyelvi csomagok telepítése Windows 10 virtuális gépeken a Windows Virtual Desktop – Azure
description: Nyelvi csomagok telepítése több Windows 10 virtuális gépekhez a Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: db1ac3f5de507a5cfdbfec7216afea9a0f4ac541
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515039"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Nyelvi csomagok hozzáadása Windows 10 több munkamenetből álló rendszerképhez

Windows Virtual Desktop egy olyan szolgáltatás, amely a felhasználók számára bármikor, bárhonnan üzembe helyezhető. Ezért fontos, hogy a felhasználók testre szabják a több munkamenetes rendszerkép Windows 10 Enterprise nyelvét.

A felhasználók nyelvi igényeinek két módon tud igazodni:

- Dedikált gazdagépkészletek összeállítása egyéni rendszerképekkel minden nyelvhez.
- A különböző nyelvi és honosítási követelményekkel rendelkezik ugyanannak a gazdagépkészletnek a felhasználói, de a lemezképek testreszabásával biztosíthatja, hogy a kívánt nyelvet választják ki.

Az utóbbi módszer sokkal hatékonyabb és költséghatékonyabb. Ön döntheti el azonban, hogy melyik módszer a legmegfelelőbb az igényeinek. Ez a cikk bemutatja, hogyan szabhatja testre a képek nyelvét.

## <a name="prerequisites"></a>Előfeltételek

A több munkamenetből álló rendszerképek testreszabásához a következőkre Windows 10 Enterprise több nyelv hozzáadásához:

- Azure-beli virtuális gép (VM) Windows 10 Enterprise több munkamenetes, 1903-as vagy újabb verzióval

- A lemezkép által használt operációsrendszer-verzió nyelvi ISO-fájlja, igény szerinti szolgáltatás (FOD) 1. lemeze és Beérkezett alkalmazások ISO-verziója. Ezeket innen töltheti le:
     
     - Nyelvi ISO:
        - [Windows 10 1903-as vagy 1909-es nyelvi csomag ISO-verziója](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10 2004-es vagy 20H2-es nyelvi csomag ISO-verziója](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - FOD Disk 1 ISO:
        - [Windows 10 1903-as vagy 1909-es FOD Disk 1 ISO-verziója](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10 2004-es vagy 20H2-es FOD Disk 1 ISO-verziója](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - Inbox Apps ISO:
        - [Windows 10, 1903-as vagy 1909-es beérkezett üzenetekhez való alkalmazások ISO-verziója](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, 2004-es verzióJú Beérkezett alkalmazások ISO-fájlja](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, 20H2-es verzióJú Beérkezett üzenetek alkalmazásai ISO-fájl](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Ha a Felhasználói felület nyelvi csomagja (LXP) ISO-fájljaival honosítja a képeket, a legjobb nyelvi élmény érdekében le kell töltenie a megfelelő LXP ISO-fájlt is
        - Ha 1903-as Windows 10 1909-es verziót használ:
          - [Windows 10 1903-as vagy 1909-es LXP ISO-verzió](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Ha a Windows 10 2004-es vagy 20H2-es verzióját használja [Windows 10,](/windows-hardware/manufacture/desktop/language-packs-known-issue) használja a Nyelvek hozzáadása a következőben: ismert problémák az alábbi LXP ISO-k közül melyik a megfelelő az Ön számára:
          - [Windows 10 2004-es vagy 20H2 **9B-es** LXP ISO-verzió](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10 2004-es vagy 20H2 **9C** LXP ISO-verzió](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10 2004-es vagy 20H2 **10C** LXP ISO-verzió](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10 2004-es vagy 20H2 **11C** LXP ISO-verzió](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10 2004-es vagy 20H2 **1C** LXP ISO-verzió](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [Windows 10 2004-es vagy 20H2 **2C** LXP ISO-verzió](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)
          - [Windows 10 2004-es vagy 20H2 **3C** LXP ISO-verzió](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2103C.iso)

- Egy Azure Files vagy fájlmegosztás egy Windows fájlkiszolgáló virtuális gépen

>[!NOTE]
>A fájlmegosztásnak (adattárnak) elérhetőnek kell lennie az egyéni rendszerkép létrehozásához használni kívánt Azure-beli virtuális gépről.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Tartalomtár létrehozása igény szerinti nyelvi csomagokhoz és szolgáltatásokhoz

Tartalomtár létrehozása nyelvi csomagokhoz és FOD-khoz, valamint egy adattár a Beérkezett üzenetek alkalmazáscsomagjaihoz:

1. Azure-beli virtuális gépen töltse le Windows 10 Enterprise Windows 10 több munkamenetes, 1903/1909-es és 2004-es verziójú Windows 10 Enterprise-es verziójú és 2004-es rendszerképeket az Előfeltételek hivatkozásai [közül.](#prerequisites)

2. Nyissa meg és csatlakoztassa az ISO-fájlokat a virtuális gépen.

3. A nyelvi csomag ISO-fájljában másolja ki a **LocalExperiencePacks** és **az x64 \\ langpacks** mappában található tartalmat, majd illessze be a tartalmat a fájlmegosztásba.

4. A **FOD** ISO-fájlba másolja az összes tartalmát, majd illessze be a fájlmegosztásba.
5. Az Inbox Apps ISO-fájlban található **amd64fre** mappában másolja ki az előkészített beérkezett üzenetek alkalmazásait tartalmazó tárház tartalmát.

     >[!NOTE]
     > Ha korlátozott tárterületen dolgozik, csak a felhasználók által használt nyelvekhez másolja a fájlokat. A fájlokat a fájlnevükön lévő nyelvkódok alapján lehet szétszedni. A francia fájl neve például az "fr-FR" kódot tartalmazza. Az összes elérhető nyelv nyelvkódja teljes listájáért lásd: Elérhető nyelvi [csomagok Windowshoz.](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)

     >[!IMPORTANT]
     > Egyes nyelvekhez további betűtípusok is szükségesek, amelyek a műholdas csomagokban különböző elnevezési konvenciókat követnek. A japán betűkészletek neve például a "Jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![Példa a japán nyelvi csomagokra, amelyek fájlnevében a "Jpan" nyelvi címke található.](media/language-pack-example.png)

6. Állítsa be az engedélyeket a nyelvi tartalomtár-megosztáson, hogy olvasási hozzáféréssel rendelkezik az egyéni rendszerkép létrehozásához használt virtuális gépről.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Egyéni Windows 10 Enterprise több munkamenetből álló rendszerkép manuális létrehozása

Egyéni több munkamenetes rendszerkép Windows 10 Enterprise létrehozása:

1. Telepítsen egy Azure-beli virtuális gépet, majd az Azure Galleryben válassza ki Windows 10 Enterprise használt több munkamenet aktuális verzióját.
2. A virtuális gép üzembe helyezése után csatlakozzon hozzá RDP-kapcsolaton keresztül helyi rendszergazdaként.
3. Ellenőrizze, hogy a virtuális gép rendelkezik-e a legújabb Windows-frissítésekkel. Töltse le a frissítéseket, és szükség esetén indítsa újra a virtuális gépet.
4. Csatlakozzon a nyelvi csomag, a FOD és az Inbox Apps fájlmegosztási adattárhoz, és csatlakoztassa egy betűjeles meghajtóhoz (például az E meghajtóhoz).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Egyéni Windows 10 Enterprise több munkamenetből álló rendszerkép automatikus létrehozása

Ha inkább automatizált folyamattal telepít nyelveket, beállíthat egy szkriptet a PowerShellben. A következő szkriptminta segítségével telepítheti Windows 10 Enterprise 2004-es, spanyol (Spanyolország), francia (Franciaország) és kínai (SATELLITE) nyelvi csomagokat és műholdas csomagokat. A szkript integrálja a nyelvi felületi csomagot és az összes szükséges műholdas csomagot a képbe. A parancsfájlt azonban módosíthatja úgy is, hogy más nyelveket telepítsen. Csak futtassa a szkriptet egy emelt szintű PowerShell-munkamenetből, különben nem fog működni.

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

A szkript telepítése a telepítenünk szükséges nyelvek számától függ.

Ha a szkript futtatása befejeződött, ellenőrizze, hogy a nyelvi csomagok megfelelően vannak-e telepítve a **Start** Settings Time (Beállítások kezdési ideje) & Language Language (Nyelv nyelv)  >    >  **lapon.**  >   Ha a nyelvi fájlok vannak, akkor készen is van.

Miután további nyelveket adott hozzá a Windows-rendszerképhez, a beérkezett üzenetek alkalmazásait is frissíteni kell a hozzáadott nyelvek támogatásához. Ehhez frissíteni kell az előre telepített alkalmazásokat a beérkező alkalmazások ISO-fájljából származó tartalommal. A frissítés leválasztott környezetben (a virtuális gépről való internet-hozzáférés nélkül) végrehajtásához használhatja a következő PowerShell-példaszkvprogramot a folyamat automatizálásához.

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
>Az ISO-fájlban található beérkezett alkalmazások nem az előre telepített Windows-alkalmazások legújabb verziói. Az összes alkalmazás legújabb verziójának végrehajtásához frissítenie kell az alkalmazásokat a Windows Áruházbeli alkalmazással, és manuálisan kell keresnie a frissítéseket, miután telepítette a további nyelveket.

Ha végzett, mindenképpen válassza le a megosztást.

## <a name="finish-customizing-your-image"></a>A rendszerkép testreszabásának befejezése

A nyelvi csomagok telepítése után telepítheti a testreszabott lemezképhez hozzáadni kívánt egyéb szoftvereket.

Miután befejezte a rendszerkép testreszabását, futtatnia kell a rendszer-előkészítő eszközt (sysprep).

A sysprep futtatása:

1. Nyisson meg egy rendszergazda jogú parancssort, és futtassa a következő parancsot a lemezkép általánosítására:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Állítsa le a virtuális gépet, majd rögzítse egy felügyelt rendszerképben a Felügyelt rendszerkép létrehozása általánosított virtuális gépről az [Azure-ban útmutatását követve.](../virtual-machines/windows/capture-image-resource.md)

3. Most már használhatja a testreszabott rendszerképet egy gazdagépkészlet Windows Virtual Desktop telepítéséhez. A gazdagépkészletek üzembe helyezésének elsajátításért lásd: [Oktatóanyag:](create-host-pools-azure-marketplace.md)Gazdagépkészlet létrehozása a Azure Portal.

## <a name="enable-languages-in-windows-settings-app"></a>Nyelvek engedélyezése a Windows beállítási alkalmazásban

Végül a gazdagépkészlet üzembe helyezése után hozzá kell adni a nyelvet az egyes felhasználók nyelvi listájához, hogy kiválasztják az előnyben részesített nyelvet a Beállítások menüben.

Annak biztosításához, hogy a felhasználók kiválasztják a telepített nyelveket, jelentkezzen be felhasználóként, majd futtassa a következő PowerShell-parancsmagot, hogy hozzáadja a telepített nyelvi csomagokat a Nyelvek menühöz. Ezt a szkriptet automatikus feladatként vagy bejelentkezési szkriptként is beállíthatja, amely akkor aktiválódik, amikor a felhasználó bejelentkezik a munkamenetbe.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Miután a felhasználó módosítja a nyelvi beállításokat, ki kell jelentkeznie a Windows Virtual Desktop munkamenetből, és újra be kell jelentkeznie, hogy a módosítások életbe lépnek. 

## <a name="next-steps"></a>Következő lépések

Ha kíváncsi a nyelvi csomagok ismert problémáira, tekintse meg a nyelvi csomagok hozzáadását a [Windows 10 1803-as](/windows-hardware/manufacture/desktop/language-packs-known-issue)és újabb verzióiban: Ismert problémák.

Ha további kérdései vannak a Windows 10 Enterprise munkamenetről, tekintse meg a gyakori [kérdéseket.](windows-10-multisession-faq.yml)
