---
title: Az Azure desired State Configuration bővítmény korábbi verzióinak használata
description: Ez a cikk az Azure-beli kívánt State Configuration (DSC) bővítmény verziójának előzményeivel kapcsolatos információkat osztja meg.
ms.date: 02/17/2021
keywords: DSC, PowerShell, Azure, bővítmény
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: e51bce6e015ef7367625b010b74e5d2422b35051
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651802"
---
# <a name="work-with-azure-desired-state-configuration-extension-version-history"></a>Az Azure desired State Configuration bővítmény korábbi verzióinak használata

Az Azure desired State Configuration (DSC) virtuálisgép- [bővítmény](../virtual-machines/extensions/dsc-overview.md) az Azure, a Windows Server, valamint a Windows PowerShellt tartalmazó Windows Management FRAMEWORK (WMF) által nyújtott fejlesztések és új képességek támogatásához szükséges.

Ez a cikk információkat nyújt az Azure DSC virtuálisgép-bővítmény minden verziójáról, az általa támogatott környezetekről, valamint az új funkciókkal és változásokkal kapcsolatos megjegyzésekről és megjegyzésekről.

## <a name="latest-version"></a>Legújabb verzió

### <a name="version-283"></a>2,83-es verzió

- **Kiadás dátuma:**
  - 2021. február
- **Operációsrendszer-támogatás**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows-ügyfél 7/8.1/10
  - Nano Server
- **WMF-támogatás:**
  - WMF 5.1
  - WMF 5,0 RTM
  - WMF 4,0 frissítés
  - WMF 4.0
- **Környezet**
  - Azure
  - Azure China Vianet 21
  - Azure Government
- **Megjegyzések:** Ez a kiadás tartalmaz egy javítást az aláíratlan bináris fájlokhoz a Windows VM bővítménnyel.

### <a name="version-280"></a>2,80-es verzió

- **Kiadás dátuma:**
  - Szeptember 26., Sep-2019 (Azure) | Július 6., 2020 (Azure China Vianet 21) | Július 20., 2020 (Azure Government)
- **Operációsrendszer-támogatás**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows-ügyfél 7/8.1/10
  - Nano Server
- **WMF-támogatás:**
  - WMF 5.1
  - WMF 5,0 RTM
  - WMF 4,0 frissítés
  - WMF 4.0
- **Környezet**
  - Azure
  - Azure China Vianet 21
  - Azure Government
- **Megjegyzések:** Ebben a kiadásban nem szerepel új funkció.

### <a name="version-276"></a>2,76-es verzió

- **Kiadás dátuma:**
  - Május 9., 2018 (Azure) | Június 21., 2018 (Azure China Vianet 21, Azure Government)
- **Operációsrendszer-támogatás**
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows-ügyfél 7/8.1/10
  - Nano Server
- **WMF-támogatás:**
  - WMF 5.1
  - WMF 5,0 RTM
  - WMF 4,0 frissítés
  - WMF 4.0
- **Környezet**
  - Azure
  - Azure China Vianet 21
  - Azure Government
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016; más Windows OSs esetén telepíti a [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) -et (a WMF telepítése újraindítást igényel). A nano Server esetében a DSC-szerepkör telepítve van a virtuális gépen.
- **Új funkciók:**
  - Az alállapotok és egyéb kisebb hibajavítások kiterjesztési metaadatainak továbbfejlesztése.

## <a name="supported-versions"></a>Támogatott verziók

> [!WARNING]
> Az 2,4-es és a 2,13-es verziók a WMF 5,0 nyilvános előzetes verzióját használják, amelynek aláíró tanúsítványai a 2016 augusztusában lejárnak.
> A hibával kapcsolatos további információkért tekintse meg a következő [blog cikket](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/).

### <a name="version-275"></a>2,75-es verzió

- **Kiadás dátuma:** Március 5., 2018
- **Operációs rendszer támogatása:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-ügyfél 7/8.1/10, Nano Server
- **WMF-támogatás:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Környezet:** Azure
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016; más Windows OSs esetén telepíti a [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) -et (a WMF telepítése újraindítást igényel). A nano Server esetében a DSC-szerepkör telepítve van a virtuális gépen.
- **Új funkciók:**
  - Miután a GitHub kikényszeríti a TLS 1,2-et, a virtuális gépeket nem lehet beépíteni az Azure Marketplace-en elérhető DIY Resource Manager-sablonok használatával Azure Automation állapot-konfigurációra, vagy a GitHubon futó konfigurációk lekéréséhez a DSC-bővítmény használatával. A bővítmény telepítésekor a következőhöz hasonló hibaüzenet jelenik meg:

    ```json
    {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details": [{
            "code": "Conflict",
            "message": "{
                \"status\": \"Failed\",
                \"error\": {
                    \"code\": \"ResourceDeploymentFailure\",
                    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",
                    \"details\": [ {
                        \"code\": \"VMExtensionProvisioningError\",
                        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'.
                        Error message: \\\"The DSC Extension failed to execute: Error downloading
                        https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip
                        after 29 attempts: The request was aborted: Could not create SSL/TLS secure channel..\\nMore information about the failure can
                        be found in the logs located under 'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.74.0.0' on the VM.\\\".\"
                    } ]
                }
            }"
        }]
    }
    ```

  - Az új bővítmény verziójában a TLS 1,2 már érvényben van. Ha a bővítmény üzembe helyezése során már meg van adva a `AutoUpgradeMinorVersion = true` Resource Manager-sablonban, a bővítmény 2,75-re frissül. Manuális frissítésekhez adja meg a `TypeHandlerVersion = 2.75` Resource Manager-sablonját.

### <a name="version-270---272"></a>Verzió: 2,70 – 2,72

- **Kiadás dátuma:** November 13., 2017
- **Operációs rendszer támogatása:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-ügyfél 7/8.1/10, Nano Server
- **WMF-támogatás:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Környezet:** Azure
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016; más Windows OSs esetén telepíti a [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) -et (a WMF telepítése újraindítást igényel). A nano Server esetében a DSC-szerepkör telepítve van a virtuális gépen.
- **Új funkciók:**
  - A hibajavítások &, amelyek egyszerűbbé teszik a Azure Automation állapot konfigurációjának használatát a Portálon és egy Resource Manager-sablonnal. További információ: az [alapértelmezett konfigurációs parancsfájl](../virtual-machines/extensions/dsc-overview.md) a DSC-bővítmény dokumentációjában.

### <a name="version-226"></a>2,26-es verzió

- **Kiadás dátuma:** 2017. június 9.
- **Operációs rendszer támogatása:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-ügyfél 7/8.1/10, Nano Server
- **WMF-támogatás:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Környezet:** Azure
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016; más Windows OSs esetén telepíti a [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) -et (a WMF telepítése újraindítást igényel). A nano Server esetében a DSC-szerepkör telepítve van a virtuális gépen.
- **Új funkciók:**
  - A telemetria fejlesztése.

### <a name="version-225"></a>2,25-es verzió

- **Kiadás dátuma:** 2017. június 2.
- **Operációs rendszer támogatása:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-ügyfél 7/8.1/10, Nano Server
- **WMF-támogatás:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Környezet:** Azure
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016; más Windows OSs esetén telepíti a [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) -et (a WMF telepítése újraindítást igényel). A nano Server esetében a DSC-szerepkör telepítve van a virtuális gépen.
- **Új funkciók:**
  - Számos hibajavítás és egyéb kisebb javítás lett hozzáadva.

### <a name="version-224"></a>2,24-es verzió

- **Kiadás dátuma:** Április 13., 2017
- **Operációs rendszer támogatása:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **WMF-támogatás:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Környezet:** Azure
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016; más Windows OSs esetén telepíti a [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) -et (a WMF telepítése újraindítást igényel). A nano Server esetében a DSC-szerepkör telepítve van a virtuális gépen.
- **Új funkciók:**
  - A VM UUID & DSC-ügynök AZONOSÍTÓjának közzététele kiterjesztési metaadatokként. További kisebb újítások lettek hozzáadva.

### <a name="version-223"></a>2,23-es verzió

- **Kiadás dátuma:** Március 15., 2017
- **Operációs rendszer támogatása:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **WMF-támogatás:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Környezet:** Azure
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016; más Windows OSs esetén telepíti a [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) -et (a WMF telepítése újraindítást igényel). A nano Server esetében a DSC-szerepkör telepítve van a virtuális gépen.
- **Új funkciók:**
  - Hibajavítások és egyéb javítások lettek hozzáadva.

### <a name="version-222"></a>2,22-es verzió

- **Kiadás dátuma:** Február 8., 2017
- **Operációs rendszer támogatása:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **WMF-támogatás:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Környezet:** Azure
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016; más Windows OSs esetén telepíti a [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) -et (a WMF telepítése újraindítást igényel). A nano Server esetében a DSC-szerepkör telepítve van a virtuális gépen.
- **Új funkciók:**
  - A DSC bővítmény mostantól támogatja a WMF 5,1-et.
  - Másodlagos további tökéletesítések lettek hozzáadva.

### <a name="version-221"></a>2,21-es verzió

- **Kiadás dátuma:** 2016. december 2.
- **Operációs rendszer támogatása:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **WMF-támogatás:** WMF 5,1 előnézet, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Környezet:** Azure
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016; más Windows operációs rendszer esetén a [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) telepítését telepíti (a WMF telepítése újraindítást igényel). A nano Server esetében a DSC-szerepkör telepítve van a virtuális gépen.
- **Új funkciók:**
  - A DSC-bővítmény mostantól elérhető a nano Serveren. Ez a verzió elsősorban a bővítmény Nano Serveren való futtatásához szükséges programkód-módosításokat tartalmazza.
  - Másodlagos további tökéletesítések lettek hozzáadva.

### <a name="version-220"></a>2,20-es verzió

- **Kiadás dátuma:** 2016. augusztus 2.
- **Operációs rendszer támogatása:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-támogatás:** WMF 5,1 előnézet, WMF 5,0 RTM, WMF 4,0 Update, WMF 4,0
- **Környezet:** Azure
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016 Technical Preview részeként; más Windows operációs rendszer esetén a [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) telepítését telepíti (a WMF telepítése újraindítást igényel).
- **Új funkciók:**
  - A WMF 5,1 előzetes verziójának támogatása. Az első közzétételkor ez a verzió egy opcionális frissítés volt, és a WMF 5,1 előzetes verziójának telepítéséhez a Resource Manager-sablonokban meg kellett adni a Wmfversion = ' 5.1 PP '-t.
    A Wmfversion = ' Latest ' továbbra is telepíti a [WMF 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/)-t.
    A WMF 5,1 előzetes verziójával kapcsolatos további információkért tekintse meg [ezt a blogot](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/).
  - Másodlagos egyéb javítások és tökéletesítések lettek hozzáadva.

### <a name="version--219"></a>2,19-es verzió

- **Kiadás dátuma:** 2016. június 3.
- **Operációs rendszer támogatása:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-támogatás:** WMF 5,0 RTM, WMF 4,0 frissítés, WMF 4,0
- **Környezet:** Azure, Azure China Vianet 21, Azure Government
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016 Technical Preview részeként; más Windows operációs rendszer esetén a [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) telepítését telepíti (a WMF telepítése újraindítást igényel).
- **Új funkciók:**
  - A DSC-bővítmény már elérhető az Azure China Vianet 21-ben. Ez a verzió a bővítmény Azure China Vianet 21 rendszeren való futtatásához szükséges javításokat tartalmazza.

### <a name="version-218"></a>2,18-es verzió

- **Kiadás dátuma:** 2016. június 3.
- **Operációs rendszer támogatása:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-támogatás:** WMF 5,0 RTM, WMF 4,0 frissítés, WMF 4,0
- **Környezet:** Azure
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016 Technical Preview részeként; más Windows operációs rendszer esetén a [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) telepítését telepíti (a WMF telepítése újraindítást igényel).
- **Új funkciók:**
  - A telemetria nem blokkolja, ha hiba lép fel a telemetria gyorsjavítás letöltésekor (ismert Azure DNS probléma) vagy a telepítés során.
  - Javítsa ki az időszakos hibát, amelyben a bővítmény leállítja a konfiguráció feldolgozását újraindítás után. Ez azt eredményezte, hogy a DSC-bővítmény "áttérési" állapotban marad.
  - Másodlagos egyéb javítások és tökéletesítések lettek hozzáadva.

### <a name="version-217"></a>2,17-es verzió

- **Kiadás dátuma:** Április 26., 2016
- **Operációs rendszer támogatása:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-támogatás:** WMF 5,0 RTM, WMF 4,0 frissítés, WMF 4,0
- **Környezet:** Azure
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016 Technical Preview részeként; más Windows operációs rendszer esetén a [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) telepítését telepíti (a WMF telepítése újraindítást igényel).
- **Új funkciók:**
  - A WMF 4,0 frissítésének támogatása. A WMF 4,0 frissítésével kapcsolatos további információkért tekintse meg [ezt a blogot](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/).
  - Próbálja megismételni a hibákat a DSC-bővítmény telepítésekor vagy a DSC-konfiguráció a bővítmény telepítése után történő alkalmazása közben. Ennek a változásnak a részeként a bővítmény újra próbálkozik a telepítéssel, ha egy korábbi telepítés meghiúsult, vagy újraindította a korábban meghiúsult DSC-konfigurációt, legfeljebb háromszor, amíg el nem éri a befejezési állapotot (sikeres/hiba), vagy ha új kérés érkezik. Ha a bővítmény érvénytelen felhasználói beállítások vagy felhasználói bevitel miatt sikertelen, nem próbálkozzon újra. Ebben az esetben a bővítményt újra kell hívni egy új kéréssel és helyes felhasználói beállításokkal. 

  > [!NOTE]
   > A DSC-bővítmény az újrapróbálkozásokhoz tartozó Azure virtuálisgép-ügynöktől függ. Az Azure-beli virtuálisgép-ügynök a legutóbbi sikertelen kéréssel meghívja a bővítményt, amíg nem ér el sikeres vagy hibás állapotot.

### <a name="version-216"></a>2,16-es verzió

- **Kiadás dátuma:** 2016. április 21.
- **Operációs rendszer támogatása:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-támogatás:** WMF 5,0 RTM, WMF 4,0
- **Környezet:** Azure
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016 Technical Preview részeként; más Windows operációs rendszer esetén a [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) telepítését telepíti (a WMF telepítése újraindítást igényel).
- **Új funkciók:**
  - A hibakezelés és más kisebb hibajavítások továbbfejlesztése.
  - Új tulajdonság a DSC-bővítmény beállításaiban. `ForcePullAndApply` a AdvancedOptions-ben hozzáadva a DSC-bővítmény engedélyezi a DSC-konfigurációkat, ha a frissítési mód lekéréses (az alapértelmezett leküldéses mód helyett). A DSC-bővítmény beállításaival kapcsolatos további információkért tekintse meg [ezt a blogot](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/).

### <a name="version-215"></a>2,15-es verzió

- **Kiadás dátuma:** Március 14., 2016
- **Operációs rendszer támogatása:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-támogatás:** WMF 5,0 RTM, WMF 4,0
- **Környezet:** Azure
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016 Technical Preview részeként; más Windows operációs rendszer esetén a [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) telepítését telepíti (a WMF telepítése újraindítást igényel).
- **Új funkciók:**
  - A bővítmény 2,14-es verziójában a WMF RTM telepítésének módosításai is megtalálhatók. A bővítmény verziójának 2.13.2.0-ről 2.14.0.0-re való frissítése során észreveheti, hogy egyes DSC-parancsmagok meghiúsulnak, vagy a konfiguráció hibát jelez – "a megadott tulajdonságértékek nem találhatók példányok". További információt a [DSC kibocsátási megjegyzései](/powershell/scripting/wmf/known-issues/known-issues-dsc)című témakörben talál. A problémák megkerülő megoldásai a 2,15-es verzióban lettek hozzáadva. 
  - Ha már telepítette a 2,14-es verziót, és a fenti két probléma egyikén fut, akkor ezeket a lépéseket manuálisan kell végrehajtania. Egy emelt szintű PowerShell-munkamenetben futtassa a következő parancsokat:
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>2,14-es verzió

- **Kiadás dátuma:** Február 25., 2016
- **Operációs rendszer támogatása:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-támogatás:** WMF 5,0 RTM, WMF 4,0
- **Környezet:** Azure
- **Megjegyzések:** Ez a verzió a DSC-t használja a Windows Server 2016 Technical Preview részeként; más Windows operációs rendszer esetén a [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) telepítését telepíti (a WMF telepítése újraindítást igényel).
- **Új funkciók:**
  - A WMF RTM-t használja.
  - Lehetővé teszi az adatgyűjtés használatát a DSC-bővítmény minőségének javítása érdekében. További információt ebben a [blogbejegyzésben](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/)talál.
  - Egy Resource Manager-sablonban frissített beállítási formátumot biztosít a bővítményhez. További információt ebben a [blogbejegyzésben](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/)talál.
  - Hibajavítások és egyéb fejlesztések.

## <a name="next-steps"></a>Következő lépések

- A PowerShell DSC-vel kapcsolatos további információkért lásd: [PowerShell dokumentációs központ](/powershell/scripting/dsc/overview/overview).
- Vizsgálja [meg a DSC-bővítmény Resource Manager-sablonját](../virtual-machines/extensions/dsc-template.md).
- A PowerShell DSC-vel felügyelhető egyéb funkciók és erőforrások esetében tallózzon a [PowerShell-galériában](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- A bizalmas paraméterek konfigurációkba való átadásával kapcsolatos részletekért lásd: [a hitelesítő adatok biztonságos kezelése a DSC-bővítmény kezelőjével](../virtual-machines/extensions/dsc-credentials.md).
