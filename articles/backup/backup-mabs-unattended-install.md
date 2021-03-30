---
title: Azure Backup Server v2 csendes telepítése
description: Használjon PowerShell-szkriptet a Azure Backup Server v2 csendes telepítéséhez. Ezt a telepítési típust felügyelet nélküli telepítésnek is nevezik.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74172246"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Azure Backup Server felügyelet nélküli telepítésének futtatása

Megtudhatja, hogyan futtathatja Azure Backup Server felügyelet nélküli telepítését.

Ezek a lépések nem érvényesek, ha Azure Backup Server v1-es verzióját telepíti.

## <a name="install-backup-server"></a>A biztonsági mentési kiszolgáló telepítése

1. A Azure Backup Server v2 vagy újabb verziót futtató kiszolgálón hozzon létre egy szövegfájlt. (A fájlt a Jegyzettömbben vagy egy másik szövegszerkesztőben is létrehozhatja.) Mentse a fájlt MABSSetup.iniként.

2. Illessze be a következő kódot a MABSSetup.ini fájlba. Cserélje le a zárójelben () lévő szöveget a \< \> környezetének megfelelő értékekre. A következő szöveg egy példa:

   ```text
   [OPTIONS]
   UserName=administrator
   CompanyName=<Microsoft Corporation>
   SQLMachineName=localhost
   SQLInstanceName=<SQL instance name>
   SQLMachineUserName=administrator
   SQLMachinePassword=<admin password>
   SQLMachineDomainName=<machine domain>
   ReportingMachineName=localhost
   ReportingInstanceName=<reporting instance name>
   SqlAccountPassword=<admin password>
   ReportingMachineUserName=<username>
   ReportingMachinePassword=<reporting admin password>
   ReportingMachineDomainName=<domain>
   VaultCredentialFilePath=<vault credential full path and complete name>
   SecurityPassphrase=<passphrase>
   PassphraseSaveLocation=<passphrase save location>
   UseExistingSQL=<1/0 use or do not use existing SQL>
   ```

3. Mentse a fájlt. Ezután a telepítési kiszolgálón egy rendszergazda jogú parancssorba írja be a következő parancsot:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Az alábbi jelzőket használhatja a telepítéshez:</br>
**/f**:. ini fájl elérési útja</br>
**/l**: naplófájl elérési útja</br>
**/i**: telepítési útvonal</br>
**/x**: eltávolítási útvonal</br>

## <a name="next-steps"></a>Következő lépések

A Backup Server telepítése után megtudhatja, hogyan készítheti elő a kiszolgálót, vagy megkezdheti a munkaterhelések védelmét.

- [A biztonsági mentési kiszolgáló munkaterhelésének előkészítése](backup-azure-microsoft-azure-backup.md)
- [VMware-kiszolgáló biztonsági mentése a Backup Server használatával](backup-azure-backup-server-vmware.md)
- [Az SQL Server biztonsági mentése a Backup Server használatával](backup-azure-sql-mabs.md)
- [Modern biztonsági mentési hely hozzáadása a Backup Serverhez](backup-mabs-add-storage.md)
