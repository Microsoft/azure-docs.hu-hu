---
title: Log Analytics-ügynök telepítése Windows rendszerű számítógépeken
description: Ez a cikk azt ismerteti, hogyan csatlakoztathatók a más felhőkben vagy a helyszínen üzemeltetett Windows-számítógépek a Windows Log Analytics-ügynökével Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/03/2020
ms.openlocfilehash: 50bfed0ee4346955a9e478e7567f00610d8184f1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708662"
---
# <a name="install-log-analytics-agent-on-windows-computers"></a>Log Analytics-ügynök telepítése Windows rendszerű számítógépeken
Ez a cikk a Log Analytics ügynök Windows rendszerű számítógépekre történő telepítésének részleteit ismerteti a következő módszerekkel:

* Manuális telepítés a [telepítővarázsló](#install-agent-using-setup-wizard) vagy a [parancssor](#install-agent-using-command-line)használatával.
* [Azure Automation kívánt állapot-konfiguráció (DSC)](#install-agent-using-dsc-in-azure-automation). 

>[!IMPORTANT]
> A cikkben ismertetett telepítési módszerek általában a helyszíni vagy más felhőkben található virtuális gépekhez használatosak. Az Azure Virtual Machines szolgáltatáshoz használható hatékonyabb beállításokért lásd a [telepítési beállításokat](./log-analytics-agent.md#installation-options) .

> [!NOTE]
> Ha úgy kell beállítania az ügynököt, hogy egynél több munkaterületre jelentsen, ez a kezdeti beállítás során nem hajtható végre, csak ezt követően, ha a Vezérlőpult vagy a PowerShell beállításait a [munkaterület hozzáadása vagy eltávolítása](agent-manage.md#adding-or-removing-a-workspace)című témakörben leírtak szerint frissíti.  

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Log Analytics-ügynök által támogatott Windows-verziók listáját [Azure monitor ügynökök áttekintése](agents-overview.md#supported-operating-systems) című témakörben tekintheti meg.

### <a name="sha-2-code-signing-support-requirement"></a>Az SHA-2 kód aláírásának támogatási követelménye 
A Windows-ügynök a 2020-as augusztus 17-én kizárólag az SHA-2 aláírást fogja használni. Ez a változás hatással lesz az ügyfelekre az Azure-szolgáltatások (Azure Monitor, Azure Automation, Azure Update Management, Azure Change Tracking, Azure Security Center, Azure Sentinel, Windows Defender ATP) részeként egy örökölt operációs rendszert használó Log Analytics ügynökkel. A módosítás nem követeli meg az ügyfelek beavatkozását, kivéve, ha az ügynököt örökölt operációsrendszer-verzióra (Windows 7, Windows Server 2008 R2 és Windows Server 2008) futtatja. Az örökölt operációsrendszer-verzión futó ügyfeleknek a következő műveleteket kell végrehajtaniuk a gépen, mielőtt augusztus 17-én, 2020-es vagy az ügynökük leállítja az adatok küldését az Log Analytics munkaterületekre:

1. Telepítse az operációs rendszerének legújabb szervizcsomagját. A szervizcsomag szükséges verziói a következők:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Telepítse az operációs rendszer SHA-2 aláírására vonatkozó Windows-frissítéseket az [2019-es SHA-2 kód-aláírás támogatási követelménye a Windows és a WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus) szolgáltatáshoz.
3. Frissítsen a Windows-ügynök legújabb verziójára (10.20.18029-verzió).
4. Javasoljuk, hogy az ügynököt a [TLS 1,2 használatára](agent-windows.md#configure-agent-to-use-tls-12)konfigurálja. 

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények
Lásd: [log Analytics ügynök áttekintése](./log-analytics-agent.md#network-requirements) a Windows-ügynök hálózati követelményeiről.


   
## <a name="configure-agent-to-use-tls-12"></a>Az ügynök konfigurálása a TLS 1,2 használatára
A [TLS 1,2](/windows-server/security/tls/tls-registry-settings#tls-12) protokoll biztosítja a Windows-ügynök és a log Analytics szolgáltatás közötti kommunikációhoz szükséges adatforgalom biztonságát. Ha alapértelmezés szerint a [tls 1,2 nélküli operációs rendszerre](../logs/data-security.md#sending-data-securely-using-tls-12)telepíti a szolgáltatást, akkor az alábbi lépésekkel kell konfigurálnia a TLS 1,2-es verzióit.

1. Keresse meg a következő beállításkulcsot: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Hozzon létre egy alkulcsot a **protokollok** alatt a TLS 1,2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1,2**
3. Hozzon létre egy **ügyfél** -alkulcsot a korábban létrehozott TLS 1,2 protokoll verziójának alkulcsában. Például: **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Client**.
4. Hozza létre a következő DWORD-értékeket a **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ ügyfél** területen:

    * **Engedélyezve** [érték = 1]
    * **DisabledByDefault** [érték = 0]  

Konfigurálja a .NET-keretrendszer 4,6-es vagy újabb verzióját a biztonságos titkosítás támogatásához, ahogy az alapértelmezés szerint le van tiltva. Az [erős titkosítás](/dotnet/framework/network-programming/tls#schusestrongcrypto) biztonságosabb hálózati protokollokat használ, mint például a TLS 1,2, és blokkolja a nem biztonságos protokollokat. 

1. Keresse meg a beállításjegyzék következő alkulcsát: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\ . NETFramework\v4.0.30319**.  
2. Hozza létre az **1** értékkel rendelkező **alatt** DWORD értéket.  
3. Keresse meg a beállításjegyzék következő alkulcsát: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\ . NETFramework\v4.0.30319**.  
4. Hozza létre az **1** értékkel rendelkező **alatt** DWORD értéket. 
5. A beállítások érvénybe léptetéséhez indítsa újra a rendszert. 

## <a name="install-agent-using-setup-wizard"></a>Ügynök telepítése a telepítővarázsló használatával
A következő lépésekkel telepítheti és konfigurálhatja az Log Analytics-ügynököt az Azure-ban, és Azure Government a felhőt a számítógépén található ügynök telepítővarázslója segítségével. Ha szeretné megismerni, hogyan konfigurálhatja az ügynököt egy System Center Operations Manager felügyeleti csoportnak való jelentésre is, tekintse meg [a Operations Manager ügynök telepítése az ügynök telepítése varázslóval](/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard)című témakört.

1. A Log Analytics munkaterületen, a **Windows-kiszolgálók** lapon, amelyről korábban navigált, válassza ki a Windows- **ügynök** megfelelő verzióját a letöltéshez a Windows operációs rendszer processzor-architektúrája alapján.   
2. Futtassa a telepítőt, és telepítse az ügynököt a számítógépre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
3. A **Licencfeltételek** oldalon olvassa el és fogadja el a licencet, majd kattintson az **Elfogadom** gombra.
4. A **Célmappa** lapon fogadja el az alapértelmezett telepítési mappát, vagy adjon meg egy másikat, majd kattintson a **Tovább** gombra.
5. **Az ügynök telepítésének beállításai** lapon csatlakoztassa az ügynököt az Azure Log Analyticshez, majd kattintson a **Tovább** gombra.   
6. Az **Azure Log Analytics** lapon végezze el a következőket:
   1. Illessze be az előzőleg kimásolt **Munkaterület-azonosítót** és **Munkaterületkulcsot (Elsődleges kulcs)**.  Ha a gépnek egy Azure Government-felhőbeli Log Analytics-munkaterületnek kell jelentenie, válassza az **Azure US Government** lehetőséget az **Azure Cloud** legördülő listából.  
   2. Ha a számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, kattintson a **Speciális** gombra, majd adja meg a proxykiszolgáló URL-címét és portszámát.  Ha a proxykiszolgáló hitelesítést igényel, írja be a felhasználónevet és jelszót a proxykiszolgálóval való hitelesítéshez, majd kattintson a **Tovább** gombra.  
7. A szükséges konfigurációs beállítások megadása után kattintson a **Tovább** gombra.<br><br> ![illessze be a Munkaterület-azonosítót és az Elsődleges kulcsot](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. A **Telepítésre kész** oldalon ellenőrizze a beállításokat, majd kattintson a **Telepítés** elemre.
9. **A konfigurálás sikeresen befejeződött** lapon kattintson a **Befejezés** gombra.

Ennek végeztével a **Microsoft Monitoring Agent** megjelenik a **Vezérlőpulton**. Annak megerősítéséhez, hogy a jelentés Log Analytics, tekintse át az [ügynök kapcsolatának ellenőrzése log Analytics](#verify-agent-connectivity-to-azure-monitor). 

## <a name="install-agent-using-command-line"></a>Ügynök telepítése parancssor használatával
Az ügynök letöltött fájlja egy önálló telepítési csomag.  Az ügynök és a támogató fájlok telepítőprogramja a csomagban található, és a megfelelő telepítéséhez a következő példákban bemutatott parancssor használatával kell kinyerni.    

>[!NOTE]
>Ha frissíteni szeretne egy ügynököt, akkor a Log Analytics Scripting API-t kell használnia. További információkért tekintse meg a [Windows és a Linux log Analytics ügynökének kezelése és karbantartása](agent-manage.md) című témakört.

Az alábbi táblázat az ügynök számára a telepítő által támogatott paramétereket mutatja be, beleértve a Automation DSC használatával történő üzembe helyezést is.

|MMA-specifikus beállítások                   |Jegyzetek         |
|---------------------------------------|--------------|
| NOAPM=1                               | Nem kötelező megadni. Telepíti az ügynököt a .NET-alkalmazás teljesítményének figyelése nélkül.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = az ügynök konfigurálása munkaterületre való jelentéshez                |
|OPINSIGHTS_WORKSPACE_ID                | Munkaterület-azonosító (GUID) a hozzáadandó munkaterülethez                    |
|OPINSIGHTS_WORKSPACE_KEY               | A munkaterülettel való első hitelesítéshez használt munkaterület-kulcs |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | A munkaterület helyét tartalmazó felhőalapú környezet meghatározása <br> 0 = Azure kereskedelmi felhő (alapértelmezett) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | A használni kívánt proxy URI azonosítója |
|OPINSIGHTS_PROXY_USERNAME               | A hitelesített proxy eléréséhez használt Felhasználónév |
|OPINSIGHTS_PROXY_PASSWORD               | Jelszó a hitelesített proxy eléréséhez |

1. Az ügynök telepítési fájljainak kibontásához emelt szintű parancssorból futtassa a parancsot, `MMASetup-<platform>.exe /c` és kérni fogja a fájlok kinyerésének elérési útját.  Azt is megteheti, hogy az argumentumok átadásával megadhatja az elérési utat `MMASetup-<platform>.exe /c /t:<Full Path>` .  
2. Ha csendesen szeretné telepíteni az ügynököt, és úgy konfigurálja, hogy az Azure kereskedelmi felhőben lévő munkaterületre jelentsen, a telepítési fájlokat a következő típusra kibontott mappából állítsa be: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   Ha az ügynököt úgy szeretné beállítani, hogy az Azure US government Cloud-nak jelentsen, írja be a következőt: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Az *OPINSIGHTS_WORKSPACE_ID* és *OPINSIGHTS_WORKSPACE_KEY* paraméterekhez tartozó karakterlánc-értékeket dupla idézőjelek közé kell ágyazni, hogy a rendszer interprit a csomaghoz érvényes beállításokként Windows Installer. 

## <a name="install-agent-using-dsc-in-azure-automation"></a>Ügynök telepítése a DSC használatával Azure Automation

A következő parancsfájl-példa használatával telepítheti az ügynököt Azure Automation DSC használatával.   Ha nem rendelkezik Automation-fiókkal, tekintse meg a [Azure Automation](../../automation/index.yml) használatának első lépéseit ismertető témakört, amely a Automation DSC használata előtt szükséges Automation-fiók létrehozásának követelményeit és lépéseit ismerteti.  Ha nem ismeri a Automation DSCt, tekintse át [a Automation DSC első lépéseivel foglalkozó](../../automation/automation-dsc-getting-started.md)oktatóanyagot.

Az alábbi példa a 64 bites ügynököt telepíti, amelyet az `URI` érték azonosít. Az 32 bites verziót is használhatja az URI értékének lecserélésével. Mindkét verzió URI-je a következő:

- Windows 64 bites ügynök – https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32 bites ügynök – https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Az eljárás és a parancsfájl például nem támogatja a Windows rendszerű számítógépekre már telepített ügynök frissítését.

Az ügynök csomagjához tartozó 32-bites és 64-bites verziók különböző termékkódok és új verziók is egyedi értékkel rendelkeznek.  A termékkód egy olyan GUID, amely egy alkalmazás vagy termék rendszerbiztonsági azonosítóját jelöli, és a Windows Installer **ProductCode** tulajdonság képviseli.  A `ProductId` **MMAgent.ps1** parancsfájlban szereplő értéknek meg kell egyeznie a 32 bites vagy a 64 bites ügynök telepítőcsomag kódjával.

Ha közvetlenül az ügynök telepítési csomagjából szeretné lekérni a termékkód beolvasását, akkor a Orca.exet a [Windows SDK Windows Installer összetevőkből](/windows/win32/msi/platform-sdk-components-for-windows-installer-developers) is használhatja, amelyek a Windows szoftverfejlesztői készlet összetevője, vagy a PowerShell használatával egy Microsoft értékes szakember (MVP) által írt [parancsfájlt](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/)  követve.  Mindkét módszer esetében először ki kell bontania a **MOMagent.msi** fájlt a MMASetup telepítési csomagjából.  Ez a következő szakaszban látható az [ügynök telepítése parancssor használatával](#install-agent-using-command-line)című szakasz első lépésében.  

1. Importálja a xPSDesiredStateConfiguration DSC-modult [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) Azure Automation-ba.  
2.    Hozzon létre Azure Automation változó eszközöket *OPSINSIGHTS_WS_ID* és *OPSINSIGHTS_WS_KEYhoz*. Állítsa be *OPSINSIGHTS_WS_ID* a log Analytics munkaterület-azonosítóra, és állítsa *OPSINSIGHTS_WS_KEY* a munkaterület elsődleges kulcsára.
3.    Másolja a szkriptet, és mentse MMAgent.ps1ként.

```powershell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

    Import-DscResource -ModuleName xPSDesiredStateConfiguration
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}

```

4. Frissítse a `ProductId` parancsfájlban szereplő értéket az ügynök telepítési csomagjának legújabb verziójából kinyert kóddal a korábban javasolt módszerek használatával. 
5. [Importálja az MMAgent.ps1 konfigurációs parancsfájlt](../../automation/automation-dsc-getting-started.md#import-a-configuration-into-azure-automation) az Automation-fiókjába. 
6. [Rendeljen Windows-számítógépet vagy-csomópontot](../../automation/automation-dsc-getting-started.md#enable-an-azure-resource-manager-vm-for-management-with-state-configuration) a konfigurációhoz. A csomópont 15 percen belül ellenőrzi a konfigurációját, és az ügynököt leküldi a csomópontra.

## <a name="verify-agent-connectivity-to-azure-monitor"></a>Ügynök kapcsolatának ellenőrzése Azure Monitor

Az ügynök telepítésének befejezését követően ellenőrizze, hogy sikeresen csatlakozott-e, és hogy a jelentéskészítés két módon hajtható végre.  

A számítógép **Vezérlőpultjában** keresse meg a **Microsoft Monitoring Agent** elemet.  Jelölje ki, és az **Azure log Analytics** lapon az ügynöknek meg kell jelennie a következő üzenetnek: **a Microsoft monitoring Agent sikeresen csatlakozott a Microsoft Operations Management Suite szolgáltatáshoz.**<br><br> ![MMA kapcsolati állapota a Log Analytics felé](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Egy egyszerű napló-lekérdezést is végrehajthat a Azure Portal.  

1. A Azure Portal keresse meg és válassza a **figyelő** elemet.
1. A menüben válassza a **naplók** lehetőséget.
1. A **naplók** ablaktábla lekérdezés mezőjébe írja be a következőt:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Az eredményül kapott keresési eredmények között meg kell jelennie a szívverési rekordoknak, amely jelzi, hogy a számítógép csatlakoztatva van, és jelentést küld a szolgáltatásnak.

## <a name="cache-information"></a>Gyorsítótár-információk

A Log Analytics ügynökből származó adatok a helyi gépen vannak gyorsítótárazva, a *C:\Program Files\Microsoft monitoring Agent\Agent\Health szolgáltatás állapota* előtt, mielőtt elküldi őket a Azure monitornak. Az ügynök 20 másodpercenként próbálkozik a feltöltéssel. Ha ez nem sikerül, a rendszer az idő múlásával egy exponenciálisan növekvő időtartamot vár. 30 másodpercet vár a második kísérlet előtt, 60 másodpercig a következő, 120 másodperc és így tovább, amíg az újrapróbálkozások száma az újrapróbálkozások között legfeljebb 8,5 óráig tart. Ez a várakozási idő kis mértékben véletlenszerű, így elkerülhető, hogy az összes ügynök egyszerre kísérelje meg a kapcsolódást. A rendszer elveti a legrégebbi adatvesztést, ha eléri a maximális puffert.

Az alapértelmezett gyorsítótár mérete 50 MB, de konfigurálható legalább 5 MB és legfeljebb 1,5 GB között. A *HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Persistence Cache Maximum* beállításkulcs tárolja. Az érték a lapok számát jelöli 8 KB/oldal alapján.


## <a name="next-steps"></a>Következő lépések

- Tekintse át a [Windows és Linux rendszerhez készült log Analytics ügynök felügyeletét és karbantartását](agent-manage.md) , hogy megtudja, hogyan lehet újrakonfigurálni, frissíteni vagy eltávolítani az ügynököt a virtuális gépről.

- Ha problémába ütközik az ügynök telepítésekor vagy felügyeletekor, tekintse át [a Windows-ügynök hibaelhárítását ismertető témakört](agent-windows-troubleshoot.md) .