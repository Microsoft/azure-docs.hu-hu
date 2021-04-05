---
title: Avere vFXT DNS – Azure
description: DNS-kiszolgáló konfigurálása ciklikus időszeleteléses terheléselosztáshoz az Azure-hoz készült avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009024"
---
# <a name="avere-cluster-dns-configuration"></a>Avere-fürt DNS-konfigurációja

Ez a szakasz ismerteti a DNS-rendszer konfigurálásának alapjait a avere vFXT-fürt terheléselosztásához.

Ez a dokumentum *nem tartalmaz* utasításokat a DNS-kiszolgáló Azure-környezetben történő beállításához és kezeléséhez.

Az Azure-beli vFXT-fürtök terheléselosztása helyett használjon kézi metódusokat, hogy az ügyfelek között egyenletesen rendeljen IP-címeket a csatlakoztatásuk során. [A avere-fürt csatlakoztatása](avere-vfxt-mount-clients.md)számos módszert ismertet.

Tartsa szem előtt ezeket a dolgokat, amikor eldönti, hogy használ-e DNS-kiszolgálót:

* Ha a rendszer csak az NFS-ügyfelek számára érhető el, a DNS használata nem kötelező – az összes hálózati címet numerikus IP-címek használatával lehet megadni.

* Ha a rendszer támogatja az SMB (CIFS) elérését, a DNS-t kötelező megadni, mert meg kell adnia egy DNS-tartományt a Active Directory-kiszolgálóhoz.

* A DNS használata kötelező, ha Kerberos-hitelesítést kíván használni.

## <a name="load-balancing"></a>Terheléselosztás

A teljes terhelés elosztásához konfigurálja a DNS-tartományt úgy, hogy az ügyfél által elérhető IP-címekhez ciklikusan megjelenő elosztott terhelési eloszlást használjon.

## <a name="configuration-details"></a>Konfiguráció részletei

Amikor az ügyfelek hozzáférnek a fürthöz, a RRDNS automatikusan kiegyenlíti a kéréseket az összes elérhető felület között.

Az optimális teljesítmény érdekében konfigurálja úgy a DNS-kiszolgálót, hogy az alábbi ábrán látható módon kezelje az ügyfélre irányuló fürt címeit.

A bal oldalon megjelenik egy fürt VServer, és az IP-címek a központban és a jobb oldalon jelennek meg. Konfigurálja az egyes ügyfél-hozzáférési pontokat egy rekordokkal és mutatókkal az ábrán látható módon.

![Avere-fürt ciklikus multiplexelés DNS-diagramja](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Minden ügyfél felé irányuló IP-címnek egyedi névvel kell rendelkeznie a fürt belső használatára. (Ebben a diagramban az ügyfél IP-címeinek neve vs1-Client-IP-*, az éles környezetben azonban érdemes lehet valami tömörebb, például az ügyfél * esetében használni.)

Az ügyfelek a VServer nevével csatlakoztatják a fürtöt a kiszolgálói argumentumként.

A DNS-kiszolgáló ``named.conf`` fájljának módosításával ciklikus sorrendet állíthat be a VServer való lekérdezésekhez. Ezzel a beállítással biztosíthatja, hogy az összes elérhető érték a következőn keresztül történjen:. Adjon hozzá egy, a következőhöz hasonló utasítást:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

A következő ``nsupdate`` parancsok példát nyújtanak a DNS helyes konfigurálására:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>Fürt DNS-beállításai

Itt adhatja meg azt a DNS-kiszolgálót, amelyet a vFXT-fürt használ a **fürt**  >  **felügyeleti hálózati** beállításai lapon. Az oldalon található beállítások a következők:

* DNS-kiszolgáló címe
* DNS-tartománynév
* DNS-keresési tartományok

A lap használatával kapcsolatos további részletekért olvassa el a [DNS-beállításokat](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) a avere-fürt konfigurációs útmutatójában.
