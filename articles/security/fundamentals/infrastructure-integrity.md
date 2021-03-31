---
title: Azure-infrastruktúra integritása
description: Ismerje meg az Azure-infrastruktúra integritását, valamint a Microsoft által a szoftver-összetevőkre vonatkozó, a szoftverek védelméhez szükséges lépéseket.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: b0df14dfcf66f53e01989a27a95afa5e19fcb2fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87543639"
---
# <a name="azure-infrastructure-integrity"></a>Azure-infrastruktúra integritása

## <a name="software-installation"></a>Szoftvertelepítés
Az Azure-környezetbe telepített összes összetevő a Microsoft biztonsági fejlesztési életciklus (SDL) folyamatát követően egyedien épül fel. Az összes szoftver-összetevő, beleértve az operációs rendszer (OS) lemezképeit és a SQL Database is, a változások kezelése és a kiadás-felügyeleti folyamat részeként települnek. Az összes csomóponton futó operációs rendszer a Windows Server 2008 vagy a Windows Server 2012 testreszabott verziója. A pontos verziót a Fabric-vezérlő (FC) választja, az operációs rendszer által lejátszani kívánt szerepkörnek megfelelően. Emellett a gazdagép operációs rendszere nem engedélyezi a jogosulatlan szoftveres összetevők telepítését.

Néhány Azure-összetevő Azure-ügyfélként van üzembe helyezve egy vendég operációs rendszeren futó vendég virtuális gépen.

## <a name="virus-scans-on-builds"></a>Vírusos vizsgálatok buildeken
Az Azure szoftver-összetevőnek (beleértve az operációs rendszert) is át kell esnie a Endpoint Protection víruskereső eszközt használó víruskeresési eszközön. Mindegyik víruskeresés létrehoz egy naplót a kapcsolódó összeállítási címtárban, amely részletezi, hogy mi történt a vizsgálat és a vizsgálat eredményei között. A víruskeresés része az Azure-ban található összes összetevő Build forráskódjának. A kód nem helyezhető át éles környezetbe anélkül, hogy tiszta és sikeres víruskeresést végez. Ha bármilyen probléma merül fel, a Build befagyott, majd a Microsoft Security szolgáltatásban található biztonsági csapatokra kerül annak azonosításához, hogy a "Rogue" kód hol adta meg a buildet.

## <a name="closed-and-locked-environment"></a>Zárt és zárolt környezet
Alapértelmezés szerint az Azure infrastruktúra-csomópontok és a vendég virtuális gépek nem rendelkeznek a rajtuk létrehozott felhasználói fiókokkal. Emellett az alapértelmezett Windows rendszergazdai fiókok is le vannak tiltva. Az Azure Live-támogatással rendelkező rendszergazdák a megfelelő hitelesítéssel bejelentkezhetnek a gépekre, és az Azure éles környezetben felügyelhetik a vészhelyzeti javításokat.

## <a name="azure-sql-database-authentication"></a>Az Azure SQL Database hitelesítése
A SQL Server bármely megvalósításához hasonlóan a felhasználói fiókok felügyeletének szigorúan szabályozva kell lennie. A Azure SQL Database csak SQL Server hitelesítést támogatja. Az ügyfél adatbiztonsági modelljének kiegészítéséhez az erős jelszavakkal rendelkező felhasználói fiókokat és az adott jogokkal konfigurált felhasználókat is fel kell használni.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>ACL-ek és tűzfalak a Microsoft vállalati hálózat és egy Azure-fürt között
Hozzáférés-vezérlési listák (ACL-ek) és tűzfalak a szolgáltatási platform és a Microsoft vállalati hálózat között a jogosulatlan bennfentes hozzáféréstől SQL Database példányok védik. Emellett csak a Microsoft vállalati hálózat IP-címeinek felhasználói érhetik el a Windows Fabric platform-Management végpontot.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>ACL-ek és tűzfalak egy SQL Database-fürt csomópontjai között
További védelemként – a védelmi részletes stratégia részeként – az ACL-eket és a tűzfalat egy SQL Database fürt csomópontjai között implementálták. A Windows Fabric platform fürtjén belüli összes kommunikáció, valamint az összes futó kód megbízható.

## <a name="custom-monitoring-agents"></a>Egyéni figyelési ügynökök
A SQL Database a SQL Database-fürt állapotának figyelésére egyéni monitorozási ügynököket (MAs), más néven watchdogokat alkalmaz.

## <a name="web-protocols"></a>Webes protokollok

### <a name="role-instance-monitoring-and-restart"></a>Szerepkör-példány figyelése és újraindítása
Az Azure biztosítja, hogy az összes üzembe helyezett, futó szerepkör (internetes webes vagy háttérrendszer-feldolgozó szerepkör) fenntartható állapot-ellenőrzés alá kerüljön, hogy azok hatékonyan és hatékonyan biztosítsák azokat a szolgáltatásokat, amelyeknek kiépítése megtörtént. Ha egy szerepkör nem Kifogástalan állapotba kerül, a futtatott alkalmazás kritikus hibája vagy a szerepkör-példányon belüli konfigurációs probléma miatt az FC észleli a problémát a szerepkör-példányon belül, és kijavító állapotot indít el.

### <a name="compute-connectivity"></a>Számítási kapcsolat
Az Azure biztosítja, hogy a központilag telepített alkalmazás vagy szolgáltatás elérhető legyen a szabványos webalapú protokollokon keresztül. Az internetes webes szerepkörök virtuális példányai külső internetkapcsolattal rendelkeznek, és közvetlenül a webes felhasználók érhetik el. Azon műveletek érzékenységének és integritásának védelme érdekében, amelyeket a feldolgozói szerepkörök a nyilvánosan elérhető webes szerepkörű virtuális példányok nevében végeznek el, a háttér-feldolgozási feldolgozói szerepkörök virtuális példányai külső internetkapcsolattal rendelkeznek, de nem érhetők el közvetlenül a külső webes felhasználók.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni arról, hogy a Microsoft Hogyan védi az Azure-infrastruktúrát, olvassa el a következő témakört:

- [Azure-létesítmények,-telephelyek és fizikai biztonság](physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Azure Information System-összetevők és-határok](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure-beli üzemi hálózat](production-network.md)
- [Azure SQL Database biztonsági funkciók](infrastructure-sql.md)
- [Azure-beli üzemi műveletek és felügyelet](infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Azure Customer-adatvédelem](protection-customer-data.md)
