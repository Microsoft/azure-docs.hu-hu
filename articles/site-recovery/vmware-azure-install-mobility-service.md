---
title: Forrásoldali gépek előkészítése a mobilitási szolgáltatás a VMware virtuális gépek és fizikai kiszolgálók Azure-ba való vész-helyreállítására szolgáló leküldéses telepítéssel történő telepítésére | Microsoft Docs
description: Megtudhatja, hogyan készítheti elő a kiszolgálót a mobilitási ügynök telepítésére a VMware virtuális gépek és a fizikai kiszolgálók Azure-beli vész-helyreállítására a Azure Site Recovery szolgáltatás használatával történő leküldéses telepítéssel.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 78fddb5b4512883f8e78d6ed53f6e3dbbeba0e4f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90524997"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>A forrásoldali gép előkészítése a mobilitási ügynök leküldéses telepítéséhez

Ha a [Azure site Recovery](site-recovery-overview.md)használatával állítja be a VMWare virtuális gépek és a fizikai kiszolgálók vész-helyreállítását, akkor a [site Recovery mobilitási szolgáltatást](vmware-physical-mobility-service-overview.md) minden helyszíni VMWare virtuális gépen és fizikai kiszolgálón telepítenie kell.  A mobilitási szolgáltatás rögzíti az adatírásokat a gépen, és továbbítja azokat a Site Recovery Process Servernek.

## <a name="install-on-windows-machine"></a>Telepítés Windows rendszerű gépen

Minden olyan Windows-gépen, amelyet védelemmel szeretne ellátni, tegye a következőket:

1. Ellenőrizze, hogy van-e hálózati kapcsolat a számítógép és a folyamat-kiszolgáló között. Ha nem állított be külön folyamat-kiszolgálót, akkor alapértelmezés szerint a konfigurációs kiszolgálón fut.
1. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a számítógéphez. A fióknak rendszergazdai jogosultságokkal kell rendelkeznie a helyi vagy a tartományhoz. Ezt a fiókot csak a leküldéses telepítéshez és az ügynök frissítéseihez használhatja.
2. Ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlést a helyi számítógépen a következő módon:
    - A HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System beállításkulcs területen adjon hozzá egy új DWORD: **LocalAccountTokenFilterPolicy**. Állítsa az értéket **1-re**.
    -  Ehhez futtassa a következő parancsot a parancssorban:
    
       ```
       REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f
       ```

3. A védelemmel ellátni kívánt gépen a Windows tűzfalon válassza az **alkalmazás vagy szolgáltatás engedélyezése a tűzfalon keresztül** lehetőséget. Engedélyezze a **fájl-és nyomtatómegosztás** , valamint a **Windows Management Instrumentation (WMI)** szolgáltatást. Tartományhoz tartozó számítógépek esetén Csoportházirend objektum (GPO) használatával konfigurálhatja a tűzfalbeállítások beállításait.

   ![Tűzfalbeállítások](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Adja hozzá a CSPSConfigtool eszközben létrehozott fiókot. Ehhez jelentkezzen be a konfigurációs kiszolgálóra.
5. Nyissa meg a következőt: **cspsconfigtool.exe**. Parancsikonként érhető el az asztalon és a%ProgramData%\ASR\home\svsystems\bin mappában.
6. A **fiókok kezelése** lapon válassza a **fiók hozzáadása** lehetőséget.
7. Adja hozzá a létrehozott fiókot.
8. Adja meg a számítógépek replikációjának engedélyezése során használt hitelesítő adatokat.

## <a name="install-on-linux-machine"></a>Telepítés Linux rendszerű gépen

Minden védelemmel ellátni kívánt Linux-gépen tegye a következőket:

1. Győződjön meg arról, hogy van hálózati kapcsolat a Linux-gép és a folyamat-kiszolgáló között.
2. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a számítógéphez. A fióknak **gyökér** szintű felhasználónak kell lennie a forrás Linux-kiszolgálón. Ezt a fiókot csak a leküldéses telepítéshez és a frissítésekhez használhatja.
3. Ellenőrizze, hogy a forrás Linux-kiszolgálón található /etc/hosts fájl tartalmaz-e olyan bejegyzéseket, amelyek a helyi gazdanevet az összes hálózati adapterhez társított IP-címekké képezik le.
4. Telepítse a legfrissebb openssh, openssh-server és openssl csomagokat a replikálni kívánt számítógépen.
5. Ügyeljen arra, hogy a Secure Shell (SSH) engedélyezve legyen, és a 22-es porton fusson.
4. Engedélyezze az SFTP alrendszer és a jelszó-hitelesítés használatát a sshd_config fájlban. Ehhez jelentkezzen be **root**-ként.
5. A **/etc/ssh/sshd_config** fájlban keresse meg a sort, amely a **PasswordAuthentication** kezdődik.
6. Állítsa vissza a sort, és módosítsa az értéket **Igen** értékre.
7. Keresse meg az **alrendszer** kezdetű sorát, és a sor megjegyzését.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Indítsa újra az **sshd** szolgáltatást.
9. Adja hozzá a CSPSConfigtool eszközben létrehozott fiókot. Ehhez jelentkezzen be a konfigurációs kiszolgálóra.
10. Nyissa meg a következőt: **cspsconfigtool.exe**. Parancsikonként érhető el az asztalon és a%ProgramData%\home\svsystems\bin mappában.
11. A **fiókok kezelése** lapon válassza a **fiók hozzáadása** lehetőséget.
12. Adja hozzá a létrehozott fiókot.
13. Adja meg a számítógépek replikációjának engedélyezése során használt hitelesítő adatokat.
1. További lépés a SUSE Linux Enterprise Server 11 SP3 vagy RHEL 5 vagy CentOS 5 vagy Debian 7 rendszerű gépek frissítéséhez és védelméhez. [Győződjön meg arról, hogy a legújabb verzió elérhető a konfigurációs kiszolgálón](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server).

## <a name="anti-virus-on-replicated-machines"></a>Vírusirtó a replikált gépeken

Ha a replikálni kívánt gépeken aktív víruskereső szoftver fut, akkor győződjön meg róla, hogy kizárja a mobilitási szolgáltatás telepítési mappáját a víruskereső műveleteiből (*C:\ProgramData\ASR\agent*). Ez biztosítja, hogy a replikáció a várt módon működjön.

## <a name="next-steps"></a>Következő lépések

A mobilitási szolgáltatás telepítése után a Azure Portal válassza a **+ replikálás** lehetőséget a virtuális gépek védelmének megkezdéséhez. További információ a [VMWare virtuális gépek](vmware-azure-enable-replication.md) és [fizikai kiszolgálók](physical-azure-disaster-recovery.md#enable-replication)replikálásának engedélyezéséről.


