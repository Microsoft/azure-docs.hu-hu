---
title: DNS-adatkapcsolatok összekötése az Azure Sentinelben | Microsoft Docs
description: Ismerje meg, hogy a Windows rendszeren futó bármely tartománynév-kiszolgálót (DNS) a DNS-gépen lévő ügynök telepítésével kapcsolódjon az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: abecddb6f5469cb4ef463e65d6c74149bf34dca9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590223"
---
# <a name="connect-your-domain-name-server"></a>A tartománynév-kiszolgáló összekötése

> [!IMPORTANT]
> A DNS-adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Windows rendszeren futó összes DNS-kiszolgálót összekapcsolhatja az Azure Sentinel szolgáltatással. Ezt úgy teheti meg, hogy egy ügynököt telepít a DNS-gépre. A DNS-naplók használatával biztonsági, teljesítménybeli és működés közbeni elemzéseket nyerhet a szervezet DNS-infrastruktúrájában azáltal, hogy összegyűjti, elemzi és korrelálja a DNS-kiszolgálókkal kapcsolatos elemzési és naplózási naplókat és egyéb kapcsolódó adatait.

A DNS-naplózási kapcsolatok engedélyezésekor a következőket teheti:
- Azonosítsa azokat az ügyfeleket, akik kártékony tartományneveket próbálnak megoldani
- Elavult erőforrásrekordok azonosítása
- A gyakran lekérdezett tartománynevek és a beszédes DNS-ügyfelek azonosítása
- Kérések terhelésének megtekintése DNS-kiszolgálókon
- Dinamikus DNS-regisztrálási hibák megtekintése

## <a name="connected-sources"></a>Összekapcsolt források

A következő táblázat ismerteti a megoldás által támogatott csatlakoztatott forrásokat:

| **Csatlakoztatott forrás** | **Támogatás** | **Leírás** |
| --- | --- | --- |
| [Windows-ügynökök](../azure-monitor/agents/agent-windows.md) | Yes | A megoldás DNS-információkat gyűjt a Windows-ügynököktől. |
| [Linux-ügynökök](../azure-monitor/vm/quick-collect-linux-computer.md) | No | A megoldás nem gyűjt DNS-adatokat a közvetlen Linux-ügynököktől. |
| [System Center Operations Manage felügyeleti csoport](../azure-monitor/agents/om-agents.md) | Yes | A megoldás a DNS-információkat a csatlakoztatott Operations Manager felügyeleti csoportban lévő ügynököktől gyűjti. Nem szükséges közvetlen kapcsolódás a Operations Manager ügynöktől a Azure Monitorhoz. Az adatok továbbítása a felügyeleti csoportból a Log Analytics munkaterületre történik. |
| [Azure Storage-fiók](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) | No | A megoldás nem használja az Azure Storage-t. |

### <a name="data-collection-details"></a>Adatgyűjtés részletei

A megoldás a DNS-leltár és a DNS-eseményekkel kapcsolatos adatokat gyűjti azokról a DNS-kiszolgálókról, amelyeken a Log Analytics-ügynök telepítve van. A rendszer a leltárral kapcsolatos adatokat, például a DNS-kiszolgálók, zónák és erőforrásrekordok számát gyűjti össze a DNS PowerShell-parancsmagok futtatásával. Az Adatfrissítés két naponként történik. Az eseményekkel kapcsolatos adatokat a rendszer valós időben gyűjti a továbbfejlesztett DNS-naplózás és diagnosztika által biztosított [analitikai és naplózási naplókból](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) a Windows Server 2012 R2 rendszerben.


## <a name="connect-your-dns-appliance"></a>A DNS-berendezés összekötése

1. Az Azure Sentinel portálon válassza az **adatösszekötők** lehetőséget, és válassza a **DNS (előzetes verzió)** csempét.
1. Ha a DNS-gépek az Azure-ban vannak:
    1. Kattintson **az ügynök telepítése az Azure Windows rendszerű virtuális gépen** elemre.
    1. A **virtuális gépek** listájában válassza ki azt a DNS-gépet, amelyet az Azure sentinelbe szeretne továbbítani. Győződjön meg arról, hogy ez egy Windows rendszerű virtuális gép.
    1. A virtuális gép megnyíló ablakában kattintson a **kapcsolat** elemre.  
    1. A **DNS-összekötő** ablakban kattintson az **Engedélyezés** elemre. 

2. Ha a DNS-számítógép nem Azure-beli virtuális gép:
    1. Kattintson **az ügynök telepítése nem Azure-beli gépekre** elemre.
    1. A **közvetlen ügynök** ablakban válassza a Windows- **ügynök letöltése (64 bites)** vagy a **windows-ügynök letöltése (32 bit)** lehetőséget.
    1. Telepítse az ügynököt a DNS-gépre. Másolja a **munkaterület-azonosítót**, az **elsődleges kulcsot** és a **másodlagos kulcsot** , és használja őket, ha a telepítés során a rendszer kéri.

3. Ha a DNS-naplókhoz a Log Analytics vonatkozó sémát szeretné használni, keresse meg a **DnsEvents**.

## <a name="validate"></a>Érvényesítés 

A Log Analyticsban keresse meg a séma **DnsEvents** , és győződjön meg arról, hogy vannak események.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a keresési lekérdezések nem jelennek meg az Azure Sentinelben, kövesse az alábbi lépéseket, hogy a lekérdezések megfelelően jelenjenek meg:
1. Kapcsolja be a [DNS Analytics naplókat a kiszolgálókon](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Győződjön meg arról, hogy a DNSEvents megjelenik a Log Analytics gyűjtemény listájában.
3. [Azure DNS Analytics](../azure-monitor/insights/dns-analytics.md)bekapcsolása.
4. Azure DNS Analytics szolgáltatásban a **konfiguráció** területen módosítsa a beállításokat, mentse, majd állítsa vissza, ha szükséges, majd mentse újra.
5. Győződjön meg arról, Azure DNS analitika, hogy a lekérdezések most megjelenjenek.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a helyi DNS-készülékek az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).