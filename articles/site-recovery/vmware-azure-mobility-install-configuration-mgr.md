---
title: A mobilitási szolgáltatás automatizálása a Azure Site Recovery telepítésének vész-helyreállításához
description: A mobilitási szolgáltatás automatikus telepítése a VMware/Physical Server rendszerbeli vész-helyreállításra Azure Site Recovery használatával.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: 2159ab8c2639f0f87fd53e8559dad518a3daa663
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92544817"
---
# <a name="automate-mobility-service-installation"></a>A mobilitási szolgáltatás telepítésének automatizálása

Ez a cikk azt ismerteti, hogyan lehet automatizálni a mobilitási szolgáltatás ügynökének telepítését és frissítéseit [Azure site Recovery](site-recovery-overview.md)-ben.

Ha a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-ba történő helyreállításához Site Recovery helyez üzembe, a mobilitási szolgáltatás ügynökét minden replikálni kívánt gépre telepítenie kell. A mobilitási szolgáltatás rögzíti az adatírásokat a gépen, és továbbítja azokat a Site Recovery folyamat-kiszolgálónak a replikáláshoz. A mobilitási szolgáltatást többféleképpen is üzembe helyezheti:

- **Leküldéses telepítés**: site Recovery telepítse a mobilitási szolgáltatás ügynököt, amikor engedélyezi a replikációt a Azure Portal lévő gépeken.
- **Manuális telepítés**: telepítse a mobilitási szolgáltatást manuálisan az egyes gépeken. [További](vmware-physical-mobility-service-overview.md) információ a leküldéses és manuális telepítésről.
- **Automatikus telepítés**: a telepítés automatizálása a szoftver központi telepítési eszközeivel, például a Microsoft Endpoint Configuration Manager vagy más eszközök, például a JetPatch.

Az automatikus telepítés és a frissítés megoldást nyújt, ha:

- A szervezet nem engedélyezi a leküldéses telepítést a védett kiszolgálókon.
- A vállalati szabályzat megköveteli, hogy a jelszavak rendszeres időközönként módosítva legyenek. Meg kell adnia egy jelszót a leküldéses telepítéshez.
- A biztonsági házirend nem engedélyezi a tűzfal-kivételek hozzáadását adott gépekhez.
- Üzemeltetési szolgáltatóként működik, és nem kívánja biztosítani a leküldéses telepítéshez szükséges ügyfél-számítógép hitelesítő adatait Site Recovery.
- Az ügynökök telepítését egyszerre sok kiszolgálóra kell méretezni.
- A tervezett karbantartási időszakokban szeretné ütemezni a telepítéseket és a frissítéseket.

## <a name="prerequisites"></a>Előfeltételek

A telepítés automatizálásához a következő elemek szükségesek:

- Központilag telepített szoftvertelepítési megoldás, például [Configuration Manager](/configmgr/) vagy [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/).
- Az üzembe helyezés előfeltételei az [Azure](tutorial-prepare-azure.md) -ban és [a helyszínen](vmware-azure-tutorial-prepare-on-premises.md) a VMware vész-helyreállításhoz, illetve a [fizikai kiszolgáló](physical-azure-disaster-recovery.md) vész-helyreállításához. Tekintse át a vész-helyreállítási [támogatási követelményeit](vmware-physical-azure-support-matrix.md) .

## <a name="prepare-for-automated-deployment"></a>Felkészülés az automatikus üzembe helyezésre

A következő táblázat összefoglalja a mobilitási szolgáltatás üzembe helyezésének automatizálásához szükséges eszközöket és folyamatokat.

**Eszköz** | **Részletek** | **Utasítások**
--- | --- | ---
**Configuration Manager** | 1. Ellenőrizze, hogy rendelkezik-e a fent felsorolt [előfeltételekkel](#prerequisites) . <br/><br/> 2. Telepítse a vész-helyreállítást a forrás környezet beállításával, beleértve a petesejtek letöltését a Site Recovery konfigurációs kiszolgáló VMware virtuális gépként való üzembe helyezéséhez OVF-sablon használatával.<br/><br/> 3. regisztrálja a konfigurációs kiszolgálót a Site Recovery szolgáltatásban, állítsa be a cél Azure-környezetet, és konfigurálja a replikációs házirendet.<br/><br/> 4. a mobilitási szolgáltatás automatikus üzembe helyezéséhez létre kell hoznia egy hálózati megosztást, amely tartalmazza a konfigurációs kiszolgáló jelszavát és a mobilitási szolgáltatás telepítési fájljait.<br/><br/> 5. létrehoz egy Configuration Manager csomagot, amely tartalmazza a telepítést vagy a frissítéseket, és előkészíti a mobilitási szolgáltatás üzembe helyezését.<br/><br/> 6. Ezután engedélyezheti az Azure-ba való replikálást azokon a gépeken, amelyeken telepítve van a mobilitási szolgáltatás. | [Automatizálás Configuration Manager](#automate-with-configuration-manager)
**JetPatch** | 1. Ellenőrizze, hogy rendelkezik-e a fent felsorolt [előfeltételekkel](#prerequisites) . <br/><br/> 2. Telepítse a vész-helyreállítást a forrás-környezet beállításával, beleértve a JetPatch-ügynök Azure Site Recovery kezelőjének letöltését és üzembe helyezését a Site Recovery környezetben, OVF-sablon használatával.<br/><br/> 3. regisztrálja a konfigurációs kiszolgálót Site Recovery, állítsa be a cél Azure-környezetet, és konfigurálja a replikációs házirendet.<br/><br/> 4. az automatikus telepítéshez inicializálja és fejezze be a JetPatch Agent Manager-konfigurációt.<br/><br/> 5. a JetPatch-ben létrehozhat egy Site Recovery szabályzatot a mobilitási szolgáltatás ügynökének üzembe helyezésének és frissítésének automatizálásához. <br/><br/> 6. Ezután engedélyezheti az Azure-ba való replikálást azokon a gépeken, amelyeken telepítve van a mobilitási szolgáltatás. | [Automatizálás a JetPatch-ügynök kezelőjével](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Az ügynök telepítése a JetPatch-ben – problémamegoldás](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatizálás Configuration Manager

### <a name="prepare-the-installation-files"></a>A telepítési fájlok előkészítése

1. Győződjön meg arról, hogy az előfeltételek teljesülnek.
1. Hozzon létre egy biztonságos hálózati fájlmegosztást (SMB-megosztás), amelyet a konfigurációs kiszolgálót futtató számítógép is elérhet.
1. A Configuration Managerban [kategorizálja azokat a kiszolgálókat](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) , amelyeken telepíteni vagy frissíteni szeretné a mobilitási szolgáltatást. Az egyik gyűjteménynek az összes Windows-kiszolgálót, a többi Linux-kiszolgálót is tartalmaznia kell.
1. A hálózati megosztáson hozzon létre egy mappát:

   - Windows rendszerű gépeken történő telepítéshez hozzon létre egy _MobSvcWindows_ nevű mappát.
   - Linux rendszerű gépeken történő telepítéshez hozzon létre egy _MobSvcLinux_ nevű mappát.

1. Jelentkezzen be a konfigurációs kiszolgáló számítógépére.
1. Nyisson meg egy rendszergazdai parancssort a konfigurációs kiszolgáló számítógépén.
1. A jelmondat-fájl létrehozásához futtassa a következő parancsot:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Másolja a _MobSvc. jelmondat_ fájlt a Windows mappába és a Linux mappába.
1. A telepítési fájlokat tartalmazó mappa tallózásához futtassa a következő parancsot:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Másolja a telepítési fájlokat a hálózati megosztásba:

   - Windows esetén másolja _Microsoft-ASR_UA_version_Windows_GA_date_Release.exet_ a _MobSvcWindows_.
   - Linux esetén másolja a következő fájlokat a _MobSvcLinux_:
     - _Microsoft-ASR_UARHEL6 -64release. tar. gz_
     - _Microsoft-ASR_UARHEL7 -64release. tar. gz_
     - _Microsoft-ASR_UASLES11-SP3-64release. tar. gz_
     - _Microsoft-ASR_UASLES11-SP4-64release. tar. gz_
     - _Microsoft-ASR_UAOL6 -64release. tar. gz_
     - _Microsoft-ASR_UAUBUNTU-14.04 -64release. tar. gz_

1. Az alábbi eljárásokban leírtak szerint másolja a kódot a Windows-vagy Linux-mappákba. Feltételezzük, hogy:

   - A konfigurációs kiszolgáló IP-címe: `192.168.3.121` .
   - A biztonságos hálózati fájlmegosztás `\\ContosoSecureFS\MobilityServiceInstallers` .

### <a name="copy-code-to-the-windows-folder"></a>Kód másolása a Windows mappába

Másolja a következő kódot:

- Mentse a kódot a _MobSvcWindows_ mappába _install.batként_.
- Cserélje le a `[CSIP]` parancsfájlban található helyőrzőket a konfigurációs kiszolgáló IP-címének tényleges értékeire.
- A parancsfájl támogatja a mobilitási szolgáltatás ügynökének új telepítéseit, valamint a már telepített ügynökök frissítéseit.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Kód másolása a Linux mappába

Másolja a következő kódot:

- Mentse a kódot a _MobSvcLinux_ mappába _install_linux. sh_ néven.
- Cserélje le a `[CSIP]` parancsfájlban található helyőrzőket a konfigurációs kiszolgáló IP-címének tényleges értékeire.
- A parancsfájl támogatja a mobilitási szolgáltatás ügynökének új telepítéseit, valamint a már telepített ügynökök frissítéseit.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Csomag létrehozása

1. Jelentkezzen be a Configuration Manager-konzolra, és nyissa meg a **szoftverfrissítési**  >  **alkalmazások felügyeleti**  >  **csomagjait**.
1. Kattintson a jobb gombbal a **csomagok**  >  **Létrehozás csomag** elemre.
1. Adja meg a csomag részleteit, beleértve a nevet, a leírást, a gyártót, a nyelvet és a verziót.
1. Válassza ki **ezt a csomagot tartalmazó forrásfájlokat**.
1. Kattintson a **Tallózás** gombra, és válassza ki a hálózati megosztást és mappát, amely tartalmazza a megfelelő telepítőt (_MobSvcWindows_ vagy _MobSvcLinux_). Ezután válassza a **tovább** lehetőséget.

   ![A csomag és program létrehozása varázsló képernyőképe](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. A **válassza ki a létrehozni kívánt program típusát** lapon válassza a **normál program**  >  **tovább** lehetőséget.

   ![A csomag és program létrehozása varázsló képernyőképe, amely a normál program lehetőséget mutatja.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. A **standard program adatainak meghatározása** lapon a következő értékeket kell megadnia:

    **Paraméter** | **Windows-érték** | **Linux-érték**
    --- | --- | ---
    **Név** | Microsoft Azure mobilitási szolgáltatás telepítése (Windows) | Telepítse a Microsoft Azure mobilitási szolgáltatást (Linux).
    **Parancssor** | install.bat | ./install_linux. sh
    **A program futtatható** | Annak megadása, hogy van-e bejelentkezett felhasználó | Annak megadása, hogy van-e bejelentkezett felhasználó
    **Egyéb paraméterek** | Alapértelmezett beállítás használata | Alapértelmezett beállítás használata

   ![A standard programhoz megadható adatokat bemutató képernyőkép.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. A **standard program követelményeinek megadása** területen hajtsa végre a következő feladatokat:

   - Windows rendszerű gépek esetén válassza **ezt a programot csak a megadott platformokon lehet futtatni**. Ezután válassza ki a [támogatott Windows operációs rendszereket](vmware-physical-azure-support-matrix.md#replicated-machines) , és kattintson a **Tovább gombra**.
   - Linux rendszerű gépek esetén válassza **a program bármely platformon futtatható** lehetőséget. Ezután kattintson a **Tovább** gombra.

1. Fejezze be a varázsló futtatását.

### <a name="deploy-the-package"></a>A csomag üzembe helyezése

1. A Configuration Manager-konzolon kattintson a jobb gombbal a csomagra, és válassza a **Tartalom terjesztése** lehetőséget.

   ![Képernyőkép a Configuration Manager-konzolról](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Válassza ki azokat a terjesztési pontokat, amelyeken a csomagokat át szeretné másolni. [További információ](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Végezze el a varázsló lépéseit. A csomag ezután elindítja a replikálást a megadott terjesztési pontokra.
1. A csomag terjesztésének befejeződése után kattintson a jobb gombbal a csomagra > az **üzembe helyezés** elemre.

   ![Képernyőfelvétel a Configuration Manager-konzolról, amely megjeleníti a központi telepítés menüpontot.](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Válassza ki a korábban létrehozott Windows-vagy Linux-eszköz gyűjteményét.
1. A **tartalom célhelyének megadása** lapon válassza a **terjesztési pontok** lehetőséget.
1. A **beállítások megadása a szoftver központi telepítésének vezérléséhez** lapon adja meg a **kívánt** **célt** .

   ![A szoftver központi telepítése varázsló képernyőképe](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. A **központi telepítéshez tartozó ütemterv megadása lapon** állítsa be az ütemtervet. [További információ](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - A mobilitási szolgáltatás a megadott ütemtervnek megfelelően települ.
   - A szükségtelen újraindítások elkerülése érdekében ütemezze a csomag telepítését a havi karbantartási időszak vagy a szoftverfrissítések ablakában.

1. A **terjesztési pontok** lapon konfigurálja a beállításokat, majd fejezze be a varázslót.
1. A telepítési folyamat figyelése a Configuration Manager-konzolon. Ugrás a **figyelési**  >  **központi telepítésekre**  >  _\<your package name\>_ .

### <a name="uninstall-the-mobility-service"></a>A mobilitási szolgáltatás eltávolítása

Configuration Manager csomagokat a mobilitási szolgáltatás eltávolításához hozhat létre. A következő parancsfájl például eltávolítja a mobilitási szolgáltatást:

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>Következő lépések

[Engedélyezze a replikációt](vmware-azure-enable-replication.md) a virtuális gépek számára.
