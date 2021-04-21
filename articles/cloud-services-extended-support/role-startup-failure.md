---
title: Szerepkörpéldány indítási hibája a Azure Cloud Services (kiterjesztett támogatás)
description: Hárítsa el a szerepkörpéldány indítási hibáit a Azure Cloud Services (kiterjesztett támogatás).
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f4892fe50c1832628181a11a5166c8cb705f79aa
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748944"
---
# <a name="troubleshoot-azure-cloud-services-extended-support-roles-that-fail-to-start"></a>Sikertelen Azure Cloud Services (kiterjesztett támogatás) szerepkörök hibaelhárítása

Íme néhány gyakori probléma és megoldás, amelyek Azure Cloud Services (kiterjesztett támogatás) szerepkörrel kapcsolatosak, amelyek nem indulnak el.

## <a name="cloud-service-operation-fails-with-roleinstancestartuptimeouterror"></a>A felhőszolgáltatás-művelet a RoleInstanceStartupTimeoutError művelettel meghiúsul

Előfordulhat, hogy a Azure Cloud Services (kiterjesztett támogatás) egy vagy több szerepkörpéldánya lassan indul el, vagy újrahasznosítással jár, és a szerepkörpéldány meghibásodik. Megjelenik a szerepköralkalmazás `RoleInstanceStartupTimeoutError` hibája.

A szerepkör-alkalmazás két részből áll, amelyek a szerepkör újrahasznosítását *okozhatják:* indítási feladatok és szerepkörkód *(a RoleEntryPoint implementációja).* 

Ha a szerepkör leáll, a szolgáltatáskéntplatform- (PaaS-) ügynök újraindítja a szerepkört.

A szerepkörpéldány aktuális állapotát és részleteit a Hibák diagnosztizálásához a PowerShell vagy a következő Azure Portal:

* **PowerShell:** A [Get-AzCloudServiceRoleInstanceView](/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) parancsmag használatával lekért információ a szerepkörpéldány futásidejű állapotáról:

    ```powershell
    Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
     
    Statuses           PlatformFaultDomain PlatformUpdateDomain
    --------           ------------------- --------------------
    {RoleStateStarting} 0                   0
    ```

* **Azure Portal:** A portálon menjen a felhőszolgáltatás-példányhoz. Az állapot részleteinek megtekintéséhez válassza a **Szerepkörök és példányok** lehetőséget, majd válassza ki a szerepkörpéldányt.

  :::image type="content" source="media/role-startup-failure-portal.png" alt-text="Képernyőkép a szerepkör indítási sikertelenségről a Azure Portal.":::

## <a name="missing-dlls-or-dependencies"></a>Hiányzó DLL-ek vagy függőségek

A nem válaszoló szerepköröket és az államok közötti ciklust hiányzó DLL-ek vagy szerelvények okozhatják.

Íme a hiányzó DLL-ek vagy szerelvények néhány tünete:

* A szerepkörpéldány a inicializálási, foglalt és leállítási **inicializálási (Inicializálás),** **Busy**(Foglalt) és Stopping **(Leállítás) között ciklusokat is végigvesz.**
* A szerepkörpéldány Kész  állapotba lépett, de ha a webalkalmazáshoz megy, a lap nem lesz látható.


A webes szerepkörben üzembe helyezett és DLL-t nem kezelő webhelyeken a következő kiszolgáló-futásidejű hiba jelenhet meg:

  :::image type="content" source="media/role-startup-failure-runtime-error.png" alt-text="A szerepkör indítási hibája utáni futásidejű hibát bemutató képernyőkép.":::

### <a name="resolve-missing-dlls-and-assemblies"></a>Hiányzó DLL-ek és szerelvények feloldása

Hiányzó DLL-ek és szerelvények hibáinak elhárítása:

1. A Visual Studio nyissa meg a megoldást.
2. A Megoldáskezelő nyissa meg a *References (Hivatkozások)* mappát.
3. Válassza ki a hibában azonosított szerelvényt.
4. A **Tulajdonságok között** állítsa a Helyi másolás **tulajdonságot** True **(Igaz) értékre.**
5. A felhőszolgáltatás ismételt üzembe állása.

Miután meggyőződik arról, hogy a hibák már nem jelennek meg, újra üzembe kell őket ásni. Az üzembe helyezés beállításakor ne jelölje be az **IntelliTrace engedélyezése a .NET 4-szerepkörökhöz jelölőnégyzetet.**

## <a name="diagnose-role-instance-errors"></a>Szerepkörpéldány-hibák diagnosztizálása

Válasszon az alábbi lehetőségek közül a szerepkörpéldányokkal kapcsolatos problémák diagnosztizálásához.

### <a name="turn-off-custom-errors"></a>Egyéni hibák kikapcsolása

A hiba teljes információinak megtekintéséhez a *webesweb.config* fájljában állítsa az egyéni hibamódot a következőre, majd újra üzembe kell állítania a `off` szolgáltatást:

1. A Visual Studio nyissa meg a megoldást.
2. A Megoldáskezelő nyissa meg a *web.config* fájlt.
3. A szakaszban `system.web` adja hozzá a következő kódot:

   ```xml
   <customErrors mode="Off" />
   ```

4. Mentse a fájlt.
5. Csomagolja újra és újra üzembe a szolgáltatást.

A szolgáltatás újratelepítésekor egy hibaüzenet tartalmazza a hiányzó szerelvények vagy DLL-ek nevét.

### <a name="use-remote-desktop"></a>A Távoli asztal használata

A Távoli asztal a szerepkör eléréséhez és a teljes hibainformációk megtekintéséhez használja a következőt:

1. [Adja hozzá a Távoli asztal bővítményt Azure Cloud Services (kiterjesztett támogatás)](enable-rdp.md).
2. A Azure Portal amikor **a** felhőszolgáltatás-példány Ready (Kész) állapotot mutat, a Távoli asztal jelentkezzen be a felhőszolgáltatásba. További információ: [Csatlakozás szerepkörpéldányokhoz a Távoli asztal.](enable-rdp.md#connect-to-role-instances-with-remote-desktop-enabled)
3. Jelentkezzen be a virtuális gépre a virtuális gép beállításhoz használt hitelesítő Távoli asztal.
4. Nyisson meg egy parancssori ablakot.
5. A parancssorba írja be az **ipconfig parancsot.** Jegyezze fel az IPv4-cím visszaadott értékét.
6. Nyissa meg a Microsoft Internet Explorer.
7. A címsorba írja be az IPv4-címet, majd egy perjelet, valamint a webalkalmazás alapértelmezett fájlját. Például: `http://<IPv4 address>/default.aspx`.

Ha most a webhelyre menve hibaüzeneteket kap, amelyek további információkat tartalmaznak. Bemutatunk egy példát:

:::image type="content" source="media/role-startup-failure-error-message.png" alt-text="A hibaüzenetre példát bemutató képernyőkép.":::
  
### <a name="use-the-compute-emulator"></a>A compute emulátor használata

Az Azure Compute Emulator használatával diagnosztizálhatja és elháríthatja  a hiányzó függőségek és aweb.confighibáit. Ha ezzel a módszerrel diagnosztizálja a problémákat, a legjobb eredmény érdekében használjon olyan számítógépet vagy virtuális gépet, amely tiszta Windows-telepítéssel rendelkezik.

Problémák diagnosztizálása az Azure Compute Emulator használatával:

1. Telepítse az [Azure SDK-t.](https://azure.microsoft.com/downloads/)
2. A fejlesztői számítógépen építse fel a felhőszolgáltatás-projektet.
3. A Fájlkezelő a cloud service projektben a *bin\debug mappára.*
4. Másolja a *.csx* mappát és a *.cscfg* fájlt a hibakereséshez használt számítógépre.
5. A tiszta gépen nyisson meg egy Azure SDK parancssori ablakot.
6. A parancssorba írja be a **következőt:csrun.exe /devstore:start**.
7. Ezután írja be a **csrun \<path to .csx folder\> \<path to .cscfg file\> /launchBrowser futtatását.**
8. A szerepkör indításakor a Internet Explorer részletes hibainformációt jelenít meg.

Ha további diagnosztikai adatokra van szükség, használhatja a szabványos Windows hibaelhárító eszközöket.

### <a name="use-intellitrace"></a>Az IntelliTrace használata

A 4. .NET-keretrendszer feldolgozói és webes szerepkörökhöz használhatja az [IntelliTrace-t.](/visualstudio/debugger/intellitrace) Az IntelliTrace a Visual Studio érhető el.

A felhőszolgáltatás üzembe helyezése bekapcsolt IntelliTrace-sel:

1. Győződjön meg arról, hogy az Azure SDK 1.3-as vagy újabb verziója telepítve van.
2. A Visual Studio telepítse a megoldást. Az üzembe helyezés beállításakor jelölje be az **IntelliTrace engedélyezése a .NET 4-szerepkörökhöz** jelölőnégyzetet.
3. A szerepkörpéldány elindulás után nyissa meg a Kiszolgálókezelőt.
4. Bontsa ki **az Azure\Cloud Services** csomópontot.
5. Bontsa ki az üzemelő példányt a szerepkörpéldányok listához. Kattintson a jobb gombbal egy szerepkörpéldányra.
6. Válassza **az IntelliTrace-naplók megtekintése lehetőséget.**
7. Az **IntelliTrace összegzése lap** **Kivételadatok lapját.**
8. **Bontsa ki a Kivételadatokat,** és keresse meg a `System.IO.FileNotFoundException` hibát:

   :::image type="content" source="media/role-startup-failure-exception-data.png" alt-text="Képernyőkép a szerepkör indítási hibája esetén történt kivételadatokról." lightbox="media/role-startup-failure-exception-data.png":::

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan háríthatja el a felhőszolgáltatás szerepkörével kapcsolatos [problémákat az Azure PaaS számítógép-diagnosztikai adataival.](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)
