---
title: 'Oktatóanyag: a hálózat konfigurálása egy Azure FXT Edge Filer-fürtben'
description: Hálózati beállítások testreszabása az Azure FXT Edge Filer-fürt létrehozása után
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 4ce7ffc66e0b6164b2e4ca9725b3f26403292a4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220771"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Oktatóanyag: a fürt hálózati beállításainak konfigurálása

Az újonnan létrehozott Azure FXT Edge Filer-fürt használata előtt tekintse át és szabja testre a munkafolyamat több hálózati beállítását.

Ez az oktatóanyag ismerteti azokat a hálózati beállításokat, amelyekre szükség lehet az új fürthöz való alkalmazkodáshoz.

Az oktatóanyagban érintett témák köre:

> [!div class="checklist"]
>
> * Lehet, hogy mely hálózati beállításokat kell frissíteni a fürt létrehozása után
> * Az Azure FXT Edge Filer használati eseteinek AD-kiszolgálót vagy DNS-kiszolgálót kell használniuk
> * Ciklikus multiplexelés DNS-(RRDNS-) konfigurálása a FXT-fürtre irányuló ügyfelek kérelmeinek automatikus elosztásához

A lépések elvégzéséhez szükséges idő attól függ, hogy hány konfigurációs módosításra van szükség a rendszeren:

* Ha csak az oktatóanyagot kell elolvasnia, és néhány beállítást ellenőriznie kell, a 10 – 15 percet is igénybe vehet.
* Ha ciklikusan megjelenő DNS-t kell konfigurálnia, a feladat akár egy órát is igénybe vehet.

## <a name="adjust-network-settings"></a>Hálózati beállítások módosítása

Számos hálózattal kapcsolatos feladat része egy új Azure FXT Edge Filer-fürt beállításának. Válassza ki ezt a listát, és döntse el, hogy mely eszközök vonatkoznak a rendszeren.

Ha többet szeretne megtudni a fürt hálózati beállításairól, olvassa el a [hálózati szolgáltatások konfigurálása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) a fürt konfigurációs útmutatójában című témakört.

* Ciklikus multiplexelés beállítása a DNS-hez az ügyfél felé irányuló hálózat számára (nem kötelező)

  A fürt forgalmának terheléselosztásához konfigurálja a DNS-t a [FXT Edge Filer-fürt DNS konfigurálása](#configure-dns-for-load-balancing)című részében leírtak szerint.

* NTP-beállítások ellenőrzése

* Active Directory és Felhasználónév/csoport nevének letöltésének konfigurálása (ha szükséges)

  Ha a hálózati gazdagépek Active Directory vagy más típusú külső címtárszolgáltatás használatát használják, akkor módosítania kell a fürt címtárszolgáltatások konfigurációját, hogy beállítsa, hogyan töltse le a fürt a felhasználónevet és a csoport adatait. A részletekért tekintse meg a **fürt**  >  **címtár-szolgáltatásait** a fürt konfigurációs útmutatójában.

  Ha az SMB-támogatást szeretné használni, szükség van egy AD-kiszolgálóra. Konfigurálja az AD-t az SMB beállításának megkezdése előtt.

* VLAN-ok definiálása (nem kötelező)
  
  Konfigurálja a fürt vservers és globális névterének meghatározása előtt szükséges további VLAN-okat. További információért olvassa el a VLAN-ok [használata](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) a fürt konfigurációs útmutatójában című témakört.

* Proxykiszolgálók konfigurálása (ha szükséges)

  Ha a fürt proxykiszolgálón keresztül éri el a külső címeket, a következő lépésekkel állíthatja be:

  1. A proxykiszolgáló megadása a **proxy konfigurációs** beállításai lapon
  1. Alkalmazza a proxykiszolgáló konfigurációját a **fürt**  >  **általános beállítása** lapon vagy a **Core Filer részletek** lapon.
  
  További információért olvassa el a következő témakört: [webproxyk használata](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) a fürt konfigurációs útmutatójában.

* A fürt által használandó [titkosítási tanúsítványok](#encryption-certificates) feltöltése (nem kötelező)

### <a name="encryption-certificates"></a>Titkosítási tanúsítványok

Az FXT Edge Filer-fürt X. 509 tanúsítványokat használ ehhez a függvényekhez:

* A fürt felügyeleti forgalmának titkosítása

* Az ügyfél nevében történő hitelesítés harmadik féltől származó KMIP-kiszolgálókra

* A felhőalapú szolgáltatók kiszolgálói tanúsítványainak ellenőrzéséhez

Ha tanúsítványokat kell feltöltenie a fürtre, használja a **fürt**  >  **tanúsítványainak** beállításai lapot. A részletek a fürt konfigurációs útmutatójának [fürt > tanúsítványok](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) lapján találhatók.

A fürt felügyeleti kommunikációjának titkosításához használja a **fürt**  >  **általános telepítési** Beállítások lapját, és válassza ki, hogy melyik tanúsítványt kívánja használni a felügyeleti TLS-hez.

> [!Note]
> A Cloud Service-hozzáférési kulcsok tárolása a **Felhőbeli hitelesítő adatok** konfigurálása lapon történik. A fenti [Core Filer hozzáadása](fxt-add-storage.md#add-a-core-filer) szakasz egy példát mutat be. a részletekért olvassa el a fürt konfigurációs útmutatójának [Felhőbeli hitelesítő adatai](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) című szakaszt.

## <a name="configure-dns-for-load-balancing"></a>A DNS konfigurálása terheléselosztáshoz

Ez a szakasz a ciklikus multiplexelés DNS-(RRDNS-) rendszer konfigurálásának alapjait ismerteti az ügyfelek terhelésének a FXT Edge Filer-fürtön lévő összes ügyfél felé irányuló IP-cím közötti elosztásához.

### <a name="decide-whether-or-not-to-use-dns"></a>Döntse el, hogy a DNS-t használja-e

A terheléselosztás mindig ajánlott, de nem kell mindig a DNS-t használnia. Bizonyos típusú ügyfél-munkafolyamatok esetében például érdemes lehet egy parancsfájl használatával egyenletesen hozzárendelni a fürt IP-címeit az ügyfelek között a fürt csatlakoztatásakor. Néhány módszert a [fürt csatlakoztatása](fxt-mount-clients.md)című témakörben talál.

Tartsa szem előtt ezeket a dolgokat, amikor eldönti, hogy használ-e DNS-kiszolgálót:

* Ha a rendszer csak az NFS-ügyfelek számára érhető el, a DNS nem szükséges. Az összes hálózati címet numerikus IP-címek használatával lehet megadni.

* Ha a rendszer támogatja az SMB (CIFS) elérését, a DNS-t kötelező megadni, mert meg kell adnia egy DNS-tartományt a Active Directory-kiszolgálóhoz.

* A DNS használata kötelező, ha Kerberos-hitelesítést kíván használni.

### <a name="round-robin-dns-configuration-details"></a>Ciklikus multiplexelés – DNS-konfiguráció részletei

Amikor az ügyfelek hozzáférnek a fürthöz, a RRDNS automatikusan kiegyenlíti a kéréseket az összes elérhető felület között.

Az optimális teljesítmény érdekében konfigurálja úgy a DNS-kiszolgálót, hogy az alábbi ábrán látható módon kezelje az ügyfélre irányuló fürt címeit.

A bal oldalon megjelenik egy fürt VServer, és az IP-címek a központban és a jobb oldalon jelennek meg. Konfigurálja az egyes ügyfél-hozzáférési pontokat egy rekordokkal és mutatókkal az ábrán látható módon.

![Fürt ciklikus multiplexelés DNS-diagramja – részletes szöveg: az ALT Text hivatkozás a kép ](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
 [részletes leírását](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

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

### <a name="enable-dns-in-the-cluster"></a>DNS engedélyezése a fürtben

Válassza ki azt a DNS-kiszolgálót, amelyet a fürt használ a **fürt**  >  **felügyeleti hálózati** beállításai lapon. Az oldalon található beállítások a következők:

* DNS-kiszolgáló címe
* DNS-tartománynév
* DNS-keresési tartományok

További részletekért olvassa el a [DNS-beállítások](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) című témakört a fürt konfigurációs útmutatójában.

## <a name="next-steps"></a>Következő lépések

Ez az Azure FXT Edge Filer-fürt utolsó alapszintű konfigurációs lépése.

* Ismerkedjen meg a rendszerled-ekkel és a [hardver állapotának figyelésével](fxt-monitor.md)kapcsolatos egyéb indikátorokkal.
* További információ arról, hogy az ügyfelek hogyan csatlakoztatják a FXT Edge Filer-fürtöt a [fürt csatlakoztatásakor](fxt-mount-clients.md).
* Az FXT Edge Filer-fürtök működtetésével és kezelésével kapcsolatos további információkért tekintse meg a [fürt konfigurációs útmutatóját](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html).
