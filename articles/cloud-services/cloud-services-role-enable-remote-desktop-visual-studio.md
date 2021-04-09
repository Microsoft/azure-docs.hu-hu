---
title: A Visual Studio használata, Távoli asztal engedélyezése egy szerepkörhöz (Azure Cloud Services klasszikus)
description: Azure Cloud Service-alkalmazás konfigurálása távoli asztali kapcsolatok engedélyezéséhez
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ad95cefbdf839c28b0979b051e217a1dfec76eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743236"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-visual-studio"></a>Az Azure Cloud Services (klasszikus) szerepkör Távoli asztali kapcsolatának engedélyezése a Visual Studio használatával

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Távoli asztal lehetővé teszi az Azure-ban futó szerepkör asztalának elérését. Az alkalmazással kapcsolatos problémák elhárításához és diagnosztizálásához Távoli asztal-kapcsolatban is használhatja a szolgáltatást.

A Visual Studio által a Cloud Serviceshez nyújtott közzétételi varázsló tartalmaz egy lehetőséget, amellyel a közzétételi folyamat során engedélyezheti Távoli asztal a hitelesítő adatok használatával. A beállítás használata a Visual Studio 2017 15,4-es vagy korábbi verziójának használata esetén megfelelő.

A Visual Studio 2017 15,5-es és újabb verzióiban azonban javasoljuk, hogy ne engedélyezze a Távoli asztal a közzétételi varázslón keresztül, hacsak csak egyetlen fejlesztőként dolgozik. Ha a projektet más fejlesztők is megnyitják, akkor a Távoli asztal az Azure Portalon, a PowerShellen keresztül, vagy egy folyamatos üzembe helyezési munkafolyamatban lévő kiadási folyamaton keresztül engedélyezheti. Ez a javaslat a Visual Studio Távoli asztal a Cloud Service virtuális gépen való kommunikációjának változásának köszönhető, a jelen cikkben ismertetett módon.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Távoli asztal konfigurálása a Visual Studio 2017 15,4-es és korábbi verzióival

A Visual Studio 2017 15,4-es vagy korábbi verziójának használatakor a Közzétételi varázslóban az **összes szerepkör engedélyezése távoli asztal** lehetőséget használhatja. Továbbra is használhatja a varázslót a Visual Studio 2017 15,5-es vagy újabb verziójával, de ne használja a Távoli asztal kapcsolót.

1. A Visual Studióban indítsa el a közzétételi varázslót. ehhez kattintson a jobb gombbal a Cloud Service-projektre Megoldáskezelő és válassza a **Közzététel** lehetőséget.

2. Ha szükséges, jelentkezzen be az Azure-előfizetésbe, és válassza a **tovább** lehetőséget.

3. A **Beállítások** lapon válassza az **összes szerepkör távoli asztal engedélyezése** lehetőséget, majd a **beállítások...** hivatkozásra kattintva nyissa meg a **Távoli asztal konfiguráció** párbeszédpanelt.

4. A párbeszédpanel alján válassza a **További beállítások lehetőséget**. Ez a parancs egy legördülő listát jelenít meg, amelyben létrehoz vagy kiválaszt egy tanúsítványt, hogy Titkosítsa a hitelesítő adatokat a Távoli asztalról való csatlakozáskor.

   > [!Note]
   > A távoli asztali kapcsolathoz szükséges tanúsítványok eltérnek a többi Azure-művelethez használt tanúsítványtól. A távelérési tanúsítványnak rendelkeznie kell titkos kulccsal.

5. Válasszon ki egy tanúsítványt a listából, vagy válassza a **&lt; Létrehozás. &gt; ..** lehetőséget. Ha új tanúsítványt hoz létre, adjon meg egy rövid nevet az új tanúsítványnak, amikor a rendszer kéri, és válassza **az OK** gombot. Az új tanúsítvány megjelenik a legördülő listában.

6. Adja meg a felhasználónevet és a jelszót. Meglévő fiókot nem használhat. Ne használja a "rendszergazda" nevet az új fiókhoz tartozó felhasználónévként.

7. Válassza ki azt a dátumot, amikor a fiók lejár, és azt követően, hogy mely Távoli asztal kapcsolatok lesznek blokkolva.

8. Miután megadta az összes szükséges információt, kattintson **az OK gombra**. A Visual Studio hozzáadja a Távoli asztal beállításait a projekthez `.cscfg` és a `.csdef` fájlokhoz, beleértve a kiválasztott tanúsítvánnyal titkosított jelszót is.

9. A **következő** gomb használatával hajtsa végre a fennmaradó lépéseket, majd válassza a **Közzététel** lehetőséget, amikor készen áll a felhőalapú szolgáltatás közzétételére. Ha nem áll készen a közzétételre, kattintson a **Mégse gombra** , és válassza az **Igen** lehetőséget, amikor a rendszer kéri a módosítások mentésére. A Cloud Service-t később is közzéteheti ezekkel a beállításokkal.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Távoli asztal konfigurálása a Visual Studio 2017 15,5-es vagy újabb verziójának használatakor

A Visual Studio 2017 15,5-es vagy újabb verziójával továbbra is használhatja a közzétételi varázslót a Cloud Service-projekttel. Ha csak egyetlen fejlesztőként dolgozik, használhatja az **összes szerepkör engedélyezése távoli asztal** lehetőséget is.

Ha egy csapat részeként dolgozik, ehelyett engedélyezze a Távoli asztalt az Azure Cloud Service-ben a [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) vagy a [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)használatával.

Ezt a javaslatot a Visual Studio 2017 15,5-es és újabb verziójának változása okozza a Cloud Service virtuális géppel való kommunikáció során. Távoli asztal a közzétételi varázslóval való engedélyezésekor a Visual Studio korábbi verziói kommunikálnak a virtuális géppel az "RDP beépülő modul" néven. A Visual Studio 2017 15,5-es és újabb verziói a biztonságosabb és rugalmasabb RDP-bővítmény használatával kommunikálnak. Ez a módosítás azzal a ténnyel is igazodik, hogy a Azure Portal és a PowerShell-metódusok lehetővé teszik a Távoli asztal az RDP-bővítmény használatát is.

Ha a Visual Studio az RDP-bővítménnyel kommunikál, egyszerű szöveges jelszót továbbít a TLS protokollon keresztül. A projekt konfigurációs fájljai azonban csak titkosított jelszót tárolnak, amely csak az eredeti titkosításhoz használt helyi tanúsítvánnyal lehet visszafejteni egyszerű szöveggé.

Ha a Cloud Service-projektet minden alkalommal ugyanabból a fejlesztői számítógépről telepíti, akkor a helyi tanúsítvány elérhető. Ebben az esetben a Közzétételi varázslóban továbbra is használhatja az **összes szerepkör engedélyezése távoli asztal** lehetőséget.

Ha azonban más fejlesztők is szeretnék telepíteni a Cloud Service-projektet különböző számítógépekről, akkor a többi számítógépnek nem lesz a szükséges tanúsítványa a jelszó visszafejtéséhez. Ennek eredményeképpen a következő hibaüzenet jelenik meg:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

A Cloud Service telepítésekor a jelszót bármikor megváltoztathatja, de a művelet a Távoli asztalt használó összes felhasználó számára nem lesz megfelelő.

Ha a projektet egy csapattal osztja meg, akkor érdemes törölni a beállítást a Közzétételi varázslóban, ehelyett a Távoli asztal közvetlenül a [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) vagy a [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)használatával engedélyezheti.

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Üzembe helyezés egy Build-kiszolgálóról a Visual Studio 2017 15,5-es vagy újabb verziójával

Üzembe helyezhet egy Cloud Service-projektet egy Build-kiszolgálóról (például az Azure DevOps Services használatával), amelyen a Visual Studio 2017 15,5-es vagy újabb verziója telepítve van a Build-ügynökben. Ezzel a megoldással az üzemelő példány ugyanabból a számítógépről történik, amelyen a titkosítási tanúsítvány elérhető.

Az Azure DevOps Services-ből származó RDP-bővítmény használatához adja meg a következő adatokat a Build folyamatában:

1. Adja meg az `/p:ForceRDPExtensionOverPlugin=true` MSBuild argumentumait, hogy a központi telepítés az RDP beépülő modul helyett az RDP-bővítménnyel működjön. Például:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. A Build lépései után adja hozzá az **Azure Cloud Service üzembe helyezési** lépését, és állítsa be a tulajdonságait.

1. Az üzembe helyezési lépés után adjon hozzá egy **Azure PowerShell** -lépést, állítsa a **megjelenítendő név** tulajdonságot "Azure-telepítés: RDP-bővítmény engedélyezése" (vagy egy másik megfelelő név) értékre, és válassza ki a megfelelő Azure-előfizetést.

1. Állítsa a **parancsfájl típusát** "inline" értékre, és illessze be az alábbi kódot a **beágyazott parancsfájl** mezőbe. (Létrehozhat egy `.ps1` fájlt a projektben ezzel a parancsfájllal, beállíthatja a parancsfájl **típusát** "parancsfájl elérési útja" értékre, és beállíthatja a **parancsfájl elérési útját** úgy, hogy a fájlra mutasson.)

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Kapcsolódás Azure-szerepkörhöz Távoli asztal használatával

Miután közzétette a Cloud Service-t az Azure-ban, és engedélyezte a Távoli asztal használatát, a Visual Studio Server Explorer használatával bejelentkezhet a Cloud Service virtuális gépre:

1. A Server Explorerben bontsa ki az **Azure** csomópontot, majd bontsa ki a felhőalapú szolgáltatás és az egyik szerepkör csomópontját a példányok listájának megjelenítéséhez.

2. Kattintson a jobb gombbal egy példány-csomópontra, és válassza a **kapcsolat távoli asztal használatával** lehetőséget.

3. Adja meg a korábban létrehozott felhasználónevet és jelszót. Most bejelentkezett a távoli munkamenetbe.

## <a name="additional-resources"></a>További források

[A Cloud Services Konfigurálása](cloud-services-how-to-configure-portal.md)
