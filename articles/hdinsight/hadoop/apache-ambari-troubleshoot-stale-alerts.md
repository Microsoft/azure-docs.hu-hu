---
title: Az Apache Ambari elavult riasztások az Azure HDInsight
description: A HDInsight-ben az Apache Ambari elavult riasztások lehetséges okainak és megoldásainak megvitatása és elemzése.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: e31e5d5a5b27f4dcb267905eae6329666fc3292c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946725"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Forgatókönyv: az Apache Ambari elavult riasztások az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache Ambari felhasználói felületén a következőhöz hasonló riasztás jelenhet meg:

![Apache Ambari elavult riasztási példa](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Ok

A Ambari-ügynökök folyamatosan figyelik számos erőforrás állapotát. A *riasztások* úgy konfigurálhatók, hogy értesítést kapjon arról, hogy adott fürt tulajdonságai az előre meghatározott küszöbértékeken belül vannak-e. Ha a riasztási feltétel teljesül, az egyes erőforrás-ellenőrzések futtatása után a Ambari-ügynökök visszaküldik az állapotot a Ambari-kiszolgálónak, és riasztást váltanak ki. Ha egy riasztás nincs bejelölve a riasztási profiljában lévő intervallumnak megfelelően, a kiszolgáló elindít egy *elavult riasztási* riasztást a Ambari-kiszolgálón.

Számos oka lehet annak, hogy az állapot-ellenőrzések miért nem futnak a megadott időintervallumban:

* A gazdagépek nagy terhelés alatt vannak (nagy CPU-használat), így a Ambari-ügynök nem tud elegendő rendszererőforrást beolvasni a riasztások időben történő futtatásához.

* A fürt nagy terhelés esetén számos feladatot vagy szolgáltatást futtat.

* A fürt kis számú gazdagépe számos összetevőt üzemeltet, ezért számos riasztás futtatásához szükséges. Ha az összetevők száma nagy, akkor a riasztási feladatok lemaradnak az ütemezett időközöktől.

## <a name="resolution"></a>Feloldás

A Ambari elavult riasztásokkal kapcsolatos problémák megoldásához próbálkozzon a következő módszerekkel.

### <a name="increase-the-alert-interval-time"></a>A riasztási Intervallum időtartamának megemelése

Az egyéni riasztási időköz értékét a fürt válaszideje és a terhelés alapján növelheti:

1. Az Apache Ambari felhasználói felületén válassza a **riasztások** fület.
1. Válassza ki a kívánt riasztási definíció nevét.
1. A definícióban válassza a **Szerkesztés** lehetőséget.
1. Növelje az **ellenőrzési időköz** értékét, majd kattintson a **Mentés** gombra.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Ambari-kiszolgálói riasztások riasztási időközének megnövelt időtartama

1. Az Apache Ambari felhasználói felületén válassza a **riasztások** fület.
1. A **csoportok** legördülő listában válassza a **AMBARI alapértelmezett** lehetőséget.
1. Válassza ki a **Ambari-kiszolgáló riasztásai** riasztást.
1. A definícióban válassza a **Szerkesztés** lehetőséget.
1. Növelje az **ellenőrzési intervallum** értékét.
1. Növelje az **intervallum szorzó** értékét, majd kattintson a **Mentés** gombra.

### <a name="disable-and-reenable-the-alert"></a>A riasztás letiltása és újraengedélyezése

Az elavult riasztások elvetéséhez tiltsa le, majd engedélyezze újra a következőt:

1. Az Apache Ambari felhasználói felületén válassza a **riasztások** fület.
1. Válassza ki a kívánt riasztási definíció nevét.
1. A definícióban válassza az **engedélyezve** lehetőséget a felhasználói felület jobb szélen lévő részén.
1. A **megerősítő** előugró ablakban válassza a **Letiltás megerősítése** lehetőséget.
1. Várjon néhány másodpercet az oldalon látható összes riasztási "példány" törléséhez.
1. A definícióban válassza a **Letiltva** lehetőséget a felhasználói felület jobb szélen lévő részén.
1. A **megerősítő** előugró ablakban válassza az **Engedélyezés megerősítése** lehetőséget.

### <a name="increase-the-alert-grace-period"></a>A riasztási türelmi időszak javítása

Van egy türelmi időszak, mielőtt egy Ambari-ügynök jelentést készít arról, hogy egy konfigurált riasztás kihagyta az ütemtervét. Ha a riasztás kihagyta az ütemezett időpontot, de a türelmi időszakon belül futott, az elavult riasztás nem jön létre.

Az alapértelmezett `alert_grace_period` érték 5 másodperc. Ezt a beállítást a/etc/ambari-Agent/conf/ambari-agent.ini is konfigurálhatja. Azokon a gazdagépeken, amelyeken az elavult riasztások rendszeres időközönként történnek, próbálja meg növelni az értéket 10-re. Ezután indítsa újra a Ambari-ügynököt.

## <a name="next-steps"></a>Következő lépések

Ha a probléma nem szerepel itt, vagy nem tudja megoldani a problémát, látogasson el a következő csatornák egyikére, további támogatásért:

* Választ kaphat az Azure-szakértőktől az [Azure közösségi támogatási szolgálatában](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a Twitteren. Ez a hivatalos Microsoft Azure fiók a felhasználói élmény javításához. Összekapcsolja az Azure-Közösséget a megfelelő erőforrásokkal: válaszokkal, támogatással és szakértőkkel.

* Ha további segítségre van szüksége, nyújtson be egy támogatási kérést a [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Ide kattintva válassza a Súgó (**?**) lehetőséget a portál menüjében, vagy nyissa meg a **Súgó + támogatás** ablaktáblát. További információ: [Azure-támogatási kérelem létrehozása](../../azure-portal/supportability/how-to-create-azure-support-request.md). 

  Az előfizetés-kezelés és a számlázás támogatását a Microsoft Azure-előfizetés tartalmazza. A technikai támogatás az Azure- [támogatási csomagokon](https://azure.microsoft.com/support/plans/)keresztül érhető el.