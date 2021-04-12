---
title: A Azure Backup-ügynök frissítése
description: Ez az információ azt ismerteti, hogy miért érdemes frissíteni a Azure Backup ügynököt, és hova kell letölteni a frissítést.
services: backup
cloud: ''
suite: ''
author: v-amallick
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: v-amallick
ms.openlocfilehash: bf77103db93652e1df837f6b1032b5e53bd41e1f
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294164"
---
## <a name="upgrade-the-mars-agent"></a>A MARS-ügynök frissítése

A 2.0.9083.0 alábbi Microsoft Azure Recovery Services (MARS) ügynökének verziói az Azure Access Control szolgáltatástól függenek. A MARS-ügynököt Azure Backup ügynöknek is nevezzük.

A 2018-es verzióban [a Microsoft elavulta az Azure Access Control szolgáltatást](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). 2018. március 19-én kezdődően a MARS-ügynök összes verziója a 2.0.9083.0 alatt biztonsági mentési hibákat fog tapasztalni. A biztonsági mentési hibák elkerülése vagy elhárítása érdekében [frissítse a Mars-ügynököt a legújabb verzióra](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). A MARS-ügynök frissítését igénylő kiszolgálók azonosításához kövesse a Microsoft Azure Recovery Services- [(MARS-) ügynök frissítése](../articles/backup/upgrade-mars-agent.md)című témakör lépéseit.

A MARS-ügynök a fájlok és mappák, valamint a rendszerállapot-információknak az Azure-ba történő biztonsági mentésére szolgál. A System Center DPM és Azure Backup Server a MARS-ügynök használatával készít biztonsági mentést az Azure-ba.
