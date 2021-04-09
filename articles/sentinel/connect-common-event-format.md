---
title: CEF-adatkapcsolatok összekötése az Azure Sentinel előzetes verziójával | Microsoft Docs
description: Csatlakoztasson egy olyan külső megoldást, amely Common Event Format (CEF) üzeneteket küld az Azure Sentinelnek egy Linux rendszerű géppel, mint egy naplófájl-továbbító.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 54fd6c0c085c0055f3114fde606f8f7d2f2e055e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772059"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>A külső megoldás összekötése a közös esemény formátumával

Ha olyan külső megoldást csatlakoztat, amely CEF-üzeneteket küld, három lépést kell megtennie az Azure Sentinel szolgáltatással való csatlakozáshoz:

1. lépés: [a CEF csatlakoztatása syslog/CEF-továbbító üzembe helyezésével](connect-cef-agent.md) 2. lépés: a [megoldásra vonatkozó lépések végrehajtása](connect-cef-solution-config.md) 3. lépés: a [kapcsolat ellenőrzése](connect-cef-verify.md)

Ez a cikk leírja, hogyan működik a kapcsolat, felsorolja az előfeltételeket, és bemutatja, hogyan lehet üzembe helyezni egy olyan mechanizmust a biztonsági megoldásokhoz, amely a syslog-ra épülő Common Event Format (CEF) üzeneteket küldi el. 

> [!NOTE] 
> Az adattárolást annak a munkaterületnek a földrajzi helye tárolja, amelyen az Azure Sentinel alkalmazást futtatja.

Ahhoz, hogy ez a kapcsolat elérhető legyen, telepítenie kell egy syslog-továbbító kiszolgálót a készülék és az Azure Sentinel közötti kommunikáció támogatásához.  A kiszolgáló egy dedikált linuxos gépről (VM vagy helyszíni) áll, amelyen telepítve van a Linux rendszerhez készült Log Analytics-ügynök. 

Az alábbi ábra az Azure-beli Linux rendszerű virtuális gépek esetén történő telepítést ismerteti:

 ![CEF az Azure-ban](./media/connect-cef/cef-syslog-azure.png)

Másik lehetőségként ez a beállítás akkor is előfordulhat, ha egy másik felhőben vagy egy helyszíni gépen található virtuális gépet használ: 

 ![CEF a helyszínen](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Biztonsági szempontok

Győződjön meg arról, hogy a cég biztonsági szabályzata szerint konfigurálja a gép biztonságát. Konfigurálhatja például a hálózatot úgy, hogy az megfeleljen a vállalati hálózati biztonsági házirendnek, és módosítsa a démon portjait és protokollait úgy, hogy azok megfeleljenek a követelményeinek. A következő útmutatást követve javíthatja a gép biztonsági konfigurációját:  [biztonságos virtuális gép az Azure-ban](../virtual-machines/security-policy.md), [ajánlott eljárások a hálózati biztonsághoz](../security/fundamentals/network-best-practices.md).

Ahhoz, hogy TLS-kommunikációt lehessen használni a syslog-forrás és a syslog-továbbító között, konfigurálnia kell a syslog démont (rsyslog vagy syslog-ng) a TLS-vel való kommunikációhoz: a [syslog-forgalom titkosítása a TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), a [naplózási üzenetek titkosítása a TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)használatával.
 
## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a naplózási továbbítóként használt Linux-gép a következő operációs rendszerek egyikét futtatja:

- 64 bites
  - CentOS 7 és 8, beleértve az alverziókat (nem 6)
  - Amazon Linux 2017,09
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 és 8, beleértve az alverziókat (nem 6)
  - Debian GNU/Linux 8 és 9
  - Ubuntu Linux 14,04 LTS, 16,04 LTS és 18,04 LTS
  - SUSE Linux Enterprise Server 12, 15

- 32 bites
  - CentOS 7 és 8, beleértve az alverziókat (nem 6)
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 és 8, beleértve az alverziókat (nem 6)
  - Debian GNU/Linux 8 és 9
  - Ubuntu Linux 14,04 LTS és 16,04 LTS
 
- Daemon-verziók
  - Syslog-ng: 2,1 – 3.22.1
  - Rsyslog: V8
  
- A syslog RFC-k támogatottak
  - Syslog RFC 3164
  - Syslog RFC 5424
 
Győződjön meg arról, hogy a gép a következő követelményeknek is megfelel: 

- Kapacitás
  - A gépnek legalább **4 CPU-maggal és 8 GB RAM-mal** kell rendelkeznie.

    > [!NOTE]
    > - A **rsyslog** démont használó egyetlen naplózási továbbító számítógép másodpercenként **legfeljebb 8500 eseményt (EPS)** gyűjtött.

- Engedélyek
  - Emelt szintű engedélyekkel (sudo) kell rendelkeznie a gépen. 

- Szoftverkövetelmények
  - Győződjön meg arról, hogy a Python 2,7 vagy a 3 fut a gépen.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogy az Azure Sentinel hogyan gyűjt CEF-naplókat a biztonsági megoldásokból és készülékekről. Ha szeretné megtudni, hogyan csatlakoztatható a megoldás az Azure Sentinel szolgáltatáshoz, tekintse meg a következő cikkeket:

- 1. lépés: [a CEF összekötése syslog/CEF-továbbító üzembe helyezésével](connect-cef-agent.md)
- 2. lépés: a [megoldásra vonatkozó lépések végrehajtása](connect-cef-solution-config.md)
- 3. lépés: a [kapcsolat ellenőrzése](connect-cef-verify.md)

Ha többet szeretne megtudni arról, hogy mi a teendő az Azure Sentinelben összegyűjtött adatokkal, tekintse meg a következő cikkeket:

- További információ a [CEF és a CommonSecurityLog mező-hozzárendelésről](cef-name-mapping.md).
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](./tutorial-detect-threats-built-in.md).