---
title: Az Azure Automation által használt Azure-adatközpont DNS-rekordjai | Microsoft Docs
description: Ez a cikk a Azure Automation szolgáltatások által igényelt DNS-rekordokat ismerteti, amikor az Automation-fiókot üzemeltető Azure-régióval való kommunikációt korlátozza.
services: automation
ms.subservice: process-automation
ms.date: 11/25/2020
ms.topic: conceptual
ms.openlocfilehash: 8630afa7410aad81a7a3c61540fc74702fc6481c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575996"
---
# <a name="dns-records-for-azure-regions-used-by-azure-automation"></a>Az Azure Automation által használt Azure-régiók DNS-rekordjai

A [Azure Automation](../automation-intro.md) szolgáltatás számos DNS-rekordot használ a szolgáltatásokhoz való kapcsolódáshoz. Ha egy adott régióhoz meghatározott Automation-fiókkal rendelkezik, akkor korlátozhatja a kommunikációt az adott regionális adatközpontra. Előfordulhat, hogy ismernie kell ezeket a rekordokat, hogy a következő automatizálási funkciók működjenek a tűzfal mögötti üzemeltetéskor:

* hibrid runbook-feldolgozó
* Állapotkonfiguráció
* Webhookok

>[!NOTE]
>A Linux hibrid Runbook Worker regisztrációja meghiúsul az új rekordokkal, kivéve, ha az 1.6.10.2 vagy újabb verziójú. A Linux rendszerhez készült [log Analytics-ügynök](../../azure-monitor/agents/agent-linux.md) újabb verziójára kell frissíteni, hogy a gép megkapja a feldolgozói szerepkör frissített verzióját, és ezeket az új rekordokat használja. A meglévő gépek a probléma nélkül továbbra is működőképesek maradnak.  

## <a name="dns-records-per-region"></a>DNS-rekordok régiónként

Az alábbi táblázat az egyes régiók DNS-rekordját tartalmazza.

>[!NOTE]
>Habár az itt megadott Automation DNS-rekordok listáját kivontuk, továbbra is működőképesek maradnak, hogy időt kelljen áttelepíteni a [privát hivatkozás támogatása](#support-for-private-link) részben felsorolt új rekordokra, és megelőzni az automatizálási folyamatokkal kapcsolatos hibákat.

| **Régió** | **DNS-rekord** |
| --- | --- |
| Ausztrália középső régiója |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Kelet-Ausztrália |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Délkelet-Ausztrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Közép-Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Közép-India |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| USA 2. keleti régiója |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Kelet-Japán |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Észak-Európa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| USA déli középső régiója |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Délkelet-Ázsia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Az Egyesült Királyság déli régiója | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| USA-beli államigazgatás – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| USA nyugati középső régiója | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Nyugat-Európa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| USA 2. nyugati régiója |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

### <a name="support-for-private-link"></a>Privát hivatkozás támogatása

A Azure Automation [privát hivatkozásának](../../private-link/private-link-overview.md) támogatásához az összes támogatott ADATKÖZPONT DNS-rekordjai frissítve lettek. A régióra jellemző URL-címek helyett az URL-címek az Automation-fiókra vonatkoznak.

| **Régió** | **DNS-rekord** |
| --- | --- |
| USA nyugati középső régiója |`https://<accountId>.webhook.wcus.azure-automation.net`<br>`https://<accountId>.agentsvc.wcus.azure-automation.net`<br>`https://<accountId>.jrds.wcus.azure-automation.net` |
| USA nyugati régiója |`https://<accountId>.webhook.wus.azure-automation.net`<br>`https://<accountId>.agentsvc.wus.azure-automation.net`<br>`https://<accountId>.jrds.wus.azure-automation.net` |
| USA 2. nyugati régiója |`https://<accountId>.webhook.wus2.azure-automation.net`<br>`https://<accountId>.agentsvc.wus2.azure-automation.net`<br>`https://<accountId>.jrds.wus2.azure-automation.net` |
| Az USA középső régiója |`https://<accountId>.webhook.cus.azure-automation.net`<br>`https://<accountId>.agentsvc.cus.azure-automation.net`<br>`https://<accountId>.jrds.cus.azure-automation.net` |
| USA déli középső régiója |`https://<accountId>.webhook.scus.azure-automation.net`<br>`https://<accountId>.agentsvc.scus.azure-automation.net`<br>`https://<accountId>.jrds.scus.azure-automation.net` |
| USA északi középső régiója |`https://<accountId>.webhook.ncus.azure-automation.net`<br>`https://<accountId>.agentsvc.ncus.azure-automation.net`<br>`https://<accountId>.jrds.ncus.azure-automation.net` |
| USA keleti régiója |`https://<accountId>.webhook.eus.azure-automation.net`<br>`https://<accountId>.agentsvc.eus.azure-automation.net`<br>`https://<accountId>.jrds.eus.azure-automation.net` |
| USA 2. keleti régiója |`https://<accountId>.webhook.eus2.azure-automation.net`<br>`https://<accountId>.agentsvc.eus2.azure-automation.net`<br>`https://<accountId>.jrds.eus2.azure-automation.net` |
| Közép-Kanada |`https://<accountId>.webhook.cc.azure-automation.net`<br>`https://<accountId>.agentsvc.cc.azure-automation.net`<br>`https://<accountId>.jrds.cc.azure-automation.net` |
| Nyugat-Európa |`https://<accountId>.webhook.we.azure-automation.net`<br>`https://<accountId>.agentsvc.we.azure-automation.net`<br>`https://<accountId>.jrds.we.azure-automation.net` |
| Észak-Európa |`https://<accountId>.webhook.ne.azure-automation.net`<br>`https://<accountId>.agentsvc.ne.azure-automation.net`<br>`https://<accountId>.jrds.ne.azure-automation.net` |
| Délkelet-Ázsia |`https://<accountId>.webhook.sea.azure-automation.net`<br>`https://<accountId>.agentsvc.sea.azure-automation.net`<br>`https://<accountId>.jrds.sea.azure-automation.net` |
| Kelet-Ázsia |`https://<accountId>.webhook.ea.azure-automation.net`<br>`https://<accountId>.agentsvc.ea.azure-automation.net`<br>`https://<accountId>.jrds.ea.azure-automation.net` |
| Közép-India |`https://<accountId>.webhook.cid.azure-automation.net`<br>`https://<accountId>.agentsvc.cid.azure-automation.net`<br>`https://<accountId>.jrds.cid.azure-automation.net` |
| Kelet-Japán |`https://<accountId>.webhook.jpe.azure-automation.net`<br>`https://<accountId>.agentsvc.jpe.azure-automation.net`<br>`https://<accountId>.jrds.jpe.azure-automation.net` |
| Dél-Korea középső régiója |`https://<accountId>.webhook.kc.azure-automation.net`<br>`https://<accountId>.agentsvc.kc.azure-automation.net`<br>`https://<accountId>.jrds.kc.azure-automation.net` |
| Délkelet-Ausztrália |`https://<accountId>.webhook.ase.azure-automation.net`<br>`https://<accountId>.agentsvc.ase.azure-automation.net`<br>`https://<accountId>.jrds.ase.azure-automation.net` |
| Kelet-Ausztrália |`https://<accountId>.webhook.ae.azure-automation.net`<br>`https://<accountId>.agentsvc.ae.azure-automation.net`<br>`https://<accountId>.jrds.ae.azure-automation.net` |
| Ausztrália középső régiója |`https://<accountId>.webhook.ac.azure-automation.net`<br>`https://<accountId>.agentsvc.ac.azure-automation.net`<br>`https://<accountId>.jrds.ac.azure-automation.net` |
| Az Egyesült Királyság déli régiója |`https://<accountId>.webhook.uks.azure-automation.net`<br>`https://<accountId>.agentsvc.uks.azure-automation.net`<br>`https://<accountId>.jrds.uks.azure-automation.net` |
| Közép-Franciaország |`https://<accountId>.webhook.fc.azure-automation.net`<br>`https://<accountId>.agentsvc.fc.azure-automation.net`<br>`https://<accountId>.jrds.fc.azure-automation.net` |
| Dél-Afrika északi régiója |`https://<accountId>.webhook.san.azure-automation.net`<br>`https://<accountId>.agentsvc.san.azure-automation.net`<br>`https://<accountId>.jrds.san.azure-automation.net` |
| Dél-Brazília |`https://<accountId>.webhook.brs.azure-automation.net`<br>`https://<accountId>.agentsvc.brs.azure-automation.net`<br>`https://<accountId>.jrds.brs.azure-automation.net` |
| Észak-Kína |`https://<accountId>.webhook.bjb.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjb.azure-automation.cn`<br>`https://<accountId>.jrds.bjb.azure-automation.cn` |
| Észak-Kína 2 |`https://<accountId>.webhook.bjs2.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjs2.azure-automation.cn`<br>`https://<accountId>.jrds.bjs2.azure-automation.cn` |
| Kelet-Kína 2 |`https://<accountId>.webhook.sha2.azure-automation.cn`<br>`https://<accountId>.agentsvc.sha2.azure-automation.cn`<br>`https://<accountId>.jrds.sha2.azure-automation.cn` |
| USA-beli államigazgatás – Virginia |`https://<accountId>.webhook.usge.azure-automation.us`<br>`https://<accountId>.agentsvc.usge.azure-automation.us`<br>`https://<accountId>.jrds.usge.azure-automation.us` |
| USA-beli államigazgatás – Texas |`https://<accountId>.webhook.ussc.azure-automation.us`<br>`https://<accountId>.agentsvc.ussc.azure-automation.us`<br>`https://<accountId>.jrds.ussc.azure-automation.us` |
| USA-beli államigazgatás – Arizona |`https://<accountId>.webhook.phx.azure-automation.us`<br>`https://<accountId>.agentsvc.phx.azure-automation.us`<br>`https://<accountId>.jrds.phx.azure-automation.us` |

Cserélje le a `<accountId>` értéket a DNS-rekordba az Automation-fiók azonosítóját jelképező GUID azonosítójával az érték **URL-címéből**. A **kulcsok** azonosítóját a Azure Portalban található **Fiókbeállítások** területen érheti el.

![Automation-fiók elsődleges kulcsának lapja](./media/automation-region-dns-records/automation-account-keys.png)

Másolja az értéket a *fiókok/* URL- **cím** mező után – `https://<GUID>.agentsvc.<region>.azure-automation.net/accounts/<GUID>`

> [!NOTE]
> Az összes webhook-és agentservice DNS-rekord frissítve lett az új stílusú DNS-rekordokra a privát hivatkozás támogatásához. JRDS DNS-rekordok esetén a régi és az új stílusú DNS-rekordok is támogatottak. Ha nem használ privát hivatkozást, a régi stílusú DNS-rekordok jelennek meg, míg a privát hivatkozásokat használó a DNS-rekordok új stílusát fogják látni.

Javasoljuk, hogy használja a [kivételek](../automation-runbook-execution.md#exceptions)meghatározásakor felsorolt címeket. A régió IP-címeinek listája a régiók nevei helyett a következő felhőalapú környezetek esetén töltse le a JSON-fájlt a Microsoft letöltőközpontból:

* [Azure IP-címtartományok és szolgáltatás-címkék – Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
* [Azure IP-címtartományok és szolgáltatás-címkék – Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
* [Azure IP-címtartományok és szolgáltatás-címkék – Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)
* [Azure IP-címtartományok és szolgáltatás-címkék – Azure China Vianet 21](https://www.microsoft.com/download/details.aspx?id=57062)

Az IP-cím fájl felsorolja az Microsoft Azure adatközpontokban használt IP-címtartományok listáját. Ez magában foglalja a számítási, az SQL-és a tárolási tartományokat, és tükrözi a jelenleg telepített tartományokat és az IP-címtartományok közelgő változásait. A fájlban megjelenő új tartományok legalább egy hétig nem használhatók az adatközpontokban.

Érdemes minden héten letölteni az új IP-címet. Ezután frissítse a webhelyet az Azure-ban futó szolgáltatások megfelelő azonosításához.

> [!NOTE]
> Ha az Azure ExpressRoute-t használja, ne feledje, hogy az IP-fájl az Azure Space Border Gateway Protocol (BGP) hirdetményének az egyes hónapok első hetében való frissítésére szolgál.

## <a name="next-steps"></a>Következő lépések

* A hibrid Runbook-feldolgozók hibaelhárításával kapcsolatos további információkért lásd: [hibrid Runbook-feldolgozói problémák elhárítása](../troubleshoot/hybrid-runbook-worker.md#general).

* Az állapot-konfigurációval kapcsolatos problémák elhárításáról az [állapot-konfigurációval kapcsolatos problémák elhárítása](../troubleshoot/desired-state-configuration.md)című témakörben olvashat bővebben.