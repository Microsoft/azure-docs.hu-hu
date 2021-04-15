---
title: Csatlakozás Windows Virtual Desktop Azure Sentinel | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztathatja Windows Virtual Desktop adatait a Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2021
ms.author: bagol
ms.openlocfilehash: a835ea7b5e79ecc9b2d26dc6955984d0d0ff2906
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107380292"
---
# <a name="connect-windows-virtual-desktop-data-to-azure-sentinel"></a>Adatok Windows Virtual Desktop csatlakoztatása a Azure Sentinel

Ez a cikk azt ismerteti, hogyan figyelheti a Windows Virtual Desktop (WVD) környezeteket a Azure Sentinel.

A virtuális Windows Virtual Desktop figyelése például lehetővé teszi, hogy több távoli munkát biztosítson virtualizált asztalok használatával, miközben fenntartja a szervezet biztonsági biztonságát.

## <a name="windows-virtual-desktop-data-in-azure-sentinel"></a>Windows Virtual Desktop adatok Azure Sentinel

Windows Virtual Desktop adatok a Azure Sentinel a következő típusokat tartalmazzák:


|Adatok  |Leírás  |
|---------|---------|
|**Windows-eseménynaplók**     |  A WVD-környezetből származó Windows-eseménynaplók ugyanúgy streamelnek egy Azure Sentinel-kompatibilis Log Analytics-munkaterületre, mint a WVD-környezeten kívüli windowsos gépekről származó Windows-eseménynaplók. <br><br>Telepítse a Log Analytics-ügynököt a Windows rendszerű gépre, és konfigurálja a Log Analytics-munkaterületre küldenünk szükséges Windows-eseménynaplókat.<br><br>További információkért lásd:<br>- [Log Analytics-ügynök telepítése Windows rendszerű számítógépekre](/azure/azure-monitor/agents/agent-windows)<br>- [Windows-eseménynapló-adatforrások gyűjtése a Log Analytics-ügynökkel](/azure/azure-monitor/agents/data-sources-windows-events)<br>- [A Windows biztonsági eseményeinek csatlakoztatása](connect-windows-security-events.md)       |
|**Microsoft Defender végponthoz (MDE) vonatkozó riasztások**     |  Ha az MDE-t Windows Virtual Desktop konfigurálni, használja ugyanazt az eljárást, mint bármely más Windows-végpontnál. <br><br>További információkért lásd: <br>- [A Microsoft Defender beállítása végpontok üzembe helyezéséhez](/windows/security/threat-protection/microsoft-defender-atp/production-deployment)<br>- [Adatok csatlakoztatása a Microsoft 365 Defenderből a Azure Sentinel](connect-microsoft-365-defender.md)       |
|**Windows Virtual Desktop diagnosztika**     | Windows Virtual Desktop diagnosztika a Windows Virtual Desktop PaaS szolgáltatás egyik funkciója, amely naplózza az adatokat, ha valaki hozzárendelt Windows Virtual Desktop használja a szolgáltatást. <br><br>Minden napló tartalmaz információkat arról, Windows Virtual Desktop szerepkör mely szerepkört érintette a tevékenységben, valamint a munkamenet során megjelenő hibaüzeneteket, a bérlői információkat és a felhasználói adatokat. <br><br>A diagnosztikai funkció tevékenységnaplókat hoz létre mind a felhasználói, mind a felügyeleti műveletekhez. <br><br>További információ: A Log Analytics használata a diagnosztikai [funkcióhoz a Windows Virtual Desktop.](/azure/virtual-desktop/virtual-desktop-fall-2019/diagnostics-log-analytics-2019)        |
|     |         |

## <a name="connect-windows-virtual-desktop-data"></a>Csatlakozás Windows Virtual Desktop adatokhoz

Az adatok Windows Virtual Desktop adatok Azure Sentinel az alábbi dokumentációban található Windows Virtual Desktop el.

További információ: [Adatok leküldése Windows Virtual Desktop Log Analytics-munkaterületre.](/azure/virtual-desktop/diagnostics-log-analytics)

## <a name="find-your-data"></a>Az adatok megkeresása

A sikeres kapcsolat létrejötte után futtason lekérdezéseket a Azure Sentinel Log Analytics-adatokon.

Tekintse meg például a mintalekérdezések a Windows Virtual Desktop [dokumentációjában.](/azure/virtual-desktop/diagnostics-log-analytics)


Azure Sentinel beépített lekérdezéseket is biztosít az Általános naplók  >    >  **WINDOWS VIRTUAL DESKTOP területen:**

[![Windows Virtual Desktop beépített lekérdezéseket a Azure Sentinel. ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png)](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png#lightbox)

## <a name="next-steps"></a>Következő lépések


További információ: Azure Monitor [Windows Virtual Desktop szószedet.](/azure/virtual-desktop/azure-monitor-glossary)
