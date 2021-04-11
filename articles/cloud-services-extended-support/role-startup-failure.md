---
title: Szerepkör-példány indítási hibája az Azure Cloud Services (bővített támogatás)
description: Az Azure Cloud Services szerepkör-példány indítási hibáinak hibaelhárítása (bővített támogatás)
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: ced7675350c0e0deadf77837cf0f13ba54fffab9
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382355"
---
# <a name="troubleshoot-azure-cloud-service-extended-support-roles-that-fail-to-start"></a>Az Azure Cloud Service (bővített támogatás) nem indítható szerepköreinek hibáinak megoldása
Íme néhány gyakori probléma és megoldás, amely a nem indítható Cloud Services (bővített támogatás) szerepkörökhöz kapcsolódik.

## <a name="cloud-service-operation-failed-with-roleinstancestartuptimeouterror"></a>A Cloud Service-művelet nem sikerült a RoleInstanceStartupTimeoutError
Előfordulhat, hogy a szolgáltatás egy vagy több szerepkör-példánya (bővített támogatás) nem indul el a meghatározott időn belül. Előfordulhat, hogy ezek a szerepkör-példányok elindulnak vagy újrahasznosítást végeznek, és előfordulhat, hogy a szerepkör-példány meghiúsul egy RoleInstanceStartupTimeoutError, mert ez egy szerepkör-alkalmazási hiba. A szerepkör-alkalmazás két fő részből áll: az indítási feladatok és a szerepkör-kód (RoleEntryPoint implementációja), amelyek közül mindkettő a szerepkör újrahasznosítását okozhatja. Ha a szerepkör összeomlott, a Pásti-ügynök mindig újra elindítja azt. 

Ha hiba esetén szeretné lekérni a szerepkör-példányok aktuális állapotát és részleteit, használja a következőt:

* PowerShell: használja a [Get-AzCloudServiceRoleInstanceView](https://docs.microsoft.com/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) parancsmagot a szerepkör-példányok futásidejű állapotáról egy felhőalapú szolgáltatásban.
```powershell
Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
 
Statuses           PlatformFaultDomain PlatformUpdateDomain
--------           ------------------- --------------------
{RoleStateStarting} 0                   0
```

* Azure Portal: Nyissa meg a Cloud Service-t, és válassza a szerepkörök és példányok fület. Kattintson a szerepkör-példányra az állapot részleteinek beszerzéséhez 
 
   :::image type="content" source="media/role-startup-failure-1.png" alt-text="A rendszerkép megjeleníti a szerepkör indítási hibáját a portálon.":::
   
Íme néhány gyakori probléma és megoldás az Azure Cloud Services (bővített támogatás) olyan szerepköreivel kapcsolatban, amelyek nem indulnak el vagy ciklusban az inicializálás, a foglalt és a leállítási állapotok között.

## <a name="missing-dlls-or-dependencies"></a>Hiányzó DLL-ek vagy függőségek
Az inicializálás, a foglalt és a leállítási állapotok közötti kerékpározást nem válaszoló szerepköröket és szerepköröket a hiányzó DLL-ek vagy szerelvények okozzák.
Hiányzó DLL-ek vagy szerelvények tünetei a következőket okozhatják:

* A szerepkör-példány az **inicializálás**, a **foglalt** és a **leállítási** állapotok közötti kerékpározás.
* A szerepkör-példánya **készen áll** , de ha a webalkalmazásra navigál, a lap nem jelenik meg.

A problémák kivizsgálásához számos ajánlott módszer áll rendelkezésre.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Egy webes szerepkör hiányzó DLL-problémáinak diagnosztizálása
Amikor egy webes szerepkörbe telepített webhelyre navigál, és a böngésző a következőhöz hasonló kiszolgálóhiba jelenik meg, az azt jelezheti, hogy hiányzik egy DLL.

:::image type="content" source="media/role-startup-failure-2.png" alt-text="A rendszerkép futásidejű hibát jelez a szerepkör indítási hibája esetén":::

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Problémák diagnosztizálása az egyéni hibák kikapcsolásával
A részletes hibaüzeneteket úgy tekintheti meg, ha a webes szerepkörhöz tartozó web.config konfigurálásával beállítja az egyéni hibaüzenetet a szolgáltatás kikapcsolására és újbóli üzembe helyezésére.
Ha a Távoli asztal használata nélkül szeretné megtekinteni a teljes hibákat:
1.  Nyissa meg a megoldást a Microsoft Visual Studióban.
2.  A Megoldáskezelő keresse meg a web.config fájlt, és nyissa meg.
3.  A web.config fájlban keresse meg a System. Web szakaszt, és adja hozzá a következő sort:
 ```xml
<customErrors mode="Off" />
```
4.  Mentse a fájlt.
5.  A szolgáltatás újracsomagolása és újbóli üzembe helyezése.
A szolgáltatás újratelepítése után egy hibaüzenet jelenik meg a hiányzó szerelvény vagy DLL nevével.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Problémák diagnosztizálása a távoli hiba megtekintésével
A Távoli asztal használatával elérheti a szerepkört, és távolról is megtekintheti a teljes körű hibaüzeneteket. A következő lépésekkel tekintheti meg a hibákat a Távoli asztal használatával:
1.  Távoli asztali bővítmény engedélyezése a Cloud Service-hez (bővített támogatás). További információ: [Távoli asztal bővítmény alkalmazása Cloud Services (bővített támogatás) a Azure Portal](enable-rdp.md)
2.  A Azure Portalon, ha a példány kész állapotot mutat, akkor a példányba való távoli. A távoli asztal Cloud Services (bővített támogatással) való használatáról további információt a következő témakörben talál: [Kapcsolódás szerepkör-példányokhoz a távoli asztal](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-rdp#connect-to-role-instances-with-remote-desktop-enabled)
3.  Jelentkezzen be a virtuális gépre a Távoli asztal konfigurálása során megadott hitelesítő adatok használatával.
4.  Nyisson meg egy parancsablakot.
5.  Írja be az IPconfig parancsot.
6.  Jegyezze fel az IPv4-címek értékét.
7.  Nyissa meg az Internet Explorert.
8.  Írja be a webalkalmazás címe és nevét. Például: http:// <IPV4 Address> /default.aspx.
A webhelyre való navigálás ekkor több explicit hibaüzenetet ad vissza:
* Kiszolgálóhiba a következő alkalmazásban: "/".
* Leírás: kezeletlen kivétel történt az aktuális webes kérelem végrehajtása során. A hibával kapcsolatos további információkért tekintse át a verem nyomkövetését, és hogy honnan származik a kód.
* Kivétel részletei: System. IO. FIleNotFoundException: nem tölthető be a következő fájl vagy szerelvény: "Microsoft. WindowsAzure. StorageClient, Version = 1.1.0.0, Culture = semleges, PublicKeyToken = 31bf856ad364e35" vagy annak valamelyik függősége. A megadott fájl nem található.
Például:

  :::image type="content" source="media/role-startup-failure-3.png" alt-text="A rendszerkép a szerepkör indítási hibájának kivételét jeleníti meg":::
  
## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Problémák diagnosztizálása a Compute Emulator használatával
Az Azure számítási emulátorával diagnosztizálhatja és elháríthatja a hiányzó függőségek és web.config hibák problémáit.
A diagnosztikai módszer használatának legjobb eredményeihez olyan számítógépet vagy virtuális gépet kell használnia, amely a Windows tiszta telepítését használja. 
1.  Az [Azure SDK](https://azure.microsoft.com/downloads/) telepítése 
2.  A fejlesztői gépen hozza létre a Cloud Service-projektet.
3.  A Windows Intézőben navigáljon a Cloud Service-projekt bin\debug mappájához.
4.  Másolja a. CSX mappát és a. cscfg fájlt arra a számítógépre, amelyet a problémák hibakereséséhez használ.
5.  A tiszta gépen nyisson meg egy Azure SDK parancssori ablakot, és írja be csrun.exe/devstore: Start parancsot.
6.  A parancssorba írja be a következőt: csrun <elérési út futtatása. CSX mappába> <elérési útja. cscfg-fájl>/launchBrowser.
7.  A szerepkör indításakor a részletes hibaüzenetek jelennek meg az Internet Explorerben. A probléma további diagnosztizálásához használhatja a szabványos Windows hibaelhárítási eszközöket is.

## <a name="diagnose-issues-by-using-intellitrace"></a>Problémák diagnosztizálása a IntelliTrace használatával
A .NET-keretrendszer 4-es verzióit használó feldolgozó és webes szerepkörök esetében használhatja a [IntelliTrace](https://docs.microsoft.com/visualstudio/debugger/intellitrace), amely a Microsoft Visual Studio Enterprise-ban érhető el.
Az alábbi lépéseket követve telepítheti a szolgáltatást a IntelliTrace engedélyezésével:
1.  Ellenőrizze, hogy telepítve van-e az Azure SDK 1,3-as vagy újabb verziója.
2.  A megoldás üzembe helyezése a Visual Studio használatával. Az üzembe helyezés során jelölje be a IntelliTrace engedélyezése a .NET 4-szerepkörökhöz jelölőnégyzetet.
3.  A példány elindítása után nyissa meg a Server Explorert.
4.  Bontsa ki a Azure\Cloud Services csomópontot, és keresse meg a központi telepítést.
5.  Bontsa ki a központi telepítést, amíg meg nem jelenik a szerepkör példányai. Kattintson a jobb gombbal az egyik példányra.
6.  Válassza a IntelliTrace-naplók megtekintése lehetőséget. Ekkor megnyílik a IntelliTrace összegzése.
7.  Keresse meg az összefoglalás kivételek szakaszát. Ha kivételek vannak, a szakasz a kivételek által jelzett adattípust jelöli.
8.  Bontsa ki a kivételeket, és keresse meg a System. IO. FileNotFoundException az alábbihoz hasonló hibákat:

    :::image type="content" source="media/role-startup-failure-4.png" alt-text="A rendszerkép megjeleníti a szerepkör indítási hibájának kivételeit" lightbox="media/role-startup-failure-4.png":::

## <a name="address-missing-dlls-and-assemblies"></a>Hiányzó dll-EK és szerelvények címe
A DLL-fájl hiányzó és szerelvény-hibáinak elhárításához kövesse az alábbi lépéseket:
1.  Nyissa meg a megoldást a Visual Studióban.
2.  A Megoldáskezelőban nyissa meg a hivatkozások mappát.
3.  Kattintson a hibában azonosított szerelvényre.
4.  A Tulajdonságok ablaktáblán keresse meg a másolás helyi tulajdonságot, és állítsa az értéket igaz értékre.
5.  Telepítse újra a Cloud Service-t.
Miután meggyőződött róla, hogy az összes hibát kijavította, a szolgáltatás központi telepítését a IntelliTrace engedélyezése a .NET 4-szerepkörökhöz jelölőnégyzet bejelölése nélkül végezheti el.

## <a name="next-steps"></a>Következő lépések 
- Ha szeretné megtudni, hogyan lehet elhárítani a Cloud Service szerepkörrel kapcsolatos problémákat az Azure Pásti számítógép-diagnosztikai adataival, tekintse [meg a Kevin Williamson blog-sorozatot](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
