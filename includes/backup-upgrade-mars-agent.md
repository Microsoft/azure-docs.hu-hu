---
title: A Azure Backup-ügynök frissítése
description: Ez az információ azt ismerteti, hogy miért érdemes frissíteni a Azure Backup ügynököt, és hova kell letölteni a frissítést.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673191"
---
## <a name="upgrade-the-mars-agent"></a>A MARS-ügynök frissítése

A 2.0.9083.0 alábbi Microsoft Azure Recovery Services (MARS) ügynökének verziói az Azure Access Control szolgáltatástól függenek. A MARS-ügynököt Azure Backup ügynöknek is nevezzük.

A 2018-es verzióban [a Microsoft elavulta az Azure Access Control szolgáltatást](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). 2018. március 19-én kezdődően a MARS-ügynök összes verziója a 2.0.9083.0 alatt biztonsági mentési hibákat fog tapasztalni. A biztonsági mentési hibák elkerülése vagy elhárítása érdekében [frissítse a Mars-ügynököt a legújabb verzióra](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). A MARS-ügynök frissítését igénylő kiszolgálók azonosításához kövesse a Microsoft Azure Recovery Services- [(MARS-) ügynök frissítése](../articles/backup/upgrade-mars-agent.md)című témakör lépéseit.

A MARS-ügynök a fájlok és mappák, valamint a rendszerállapot-információknak az Azure-ba történő biztonsági mentésére szolgál. A System Center DPM és Azure Backup Server a MARS-ügynök használatával készít biztonsági mentést az Azure-ba.
