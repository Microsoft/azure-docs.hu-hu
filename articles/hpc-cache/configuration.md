---
title: Az Azure HPC gyorsítótár beállításainak konfigurálása
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a gyorsítótár további beállításait, például az MTU-t, az egyéni NTP-és DNS-konfigurációt, valamint az expressz Pillanatképek elérését az Azure Blob Storage-tárolókra.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 06feefe3a934d1ee02793fab442852e5ef40899a
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563378"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>További Azure HPC-gyorsítótár beállításainak konfigurálása

A Azure Portal **hálózatkezelés** lapján számos beállítás testreszabására van lehetőség. A legtöbb felhasználónak nem kell módosítania ezeket a beállításokat az alapértelmezett értékükre.

Ez a cikk azt is ismerteti, hogyan használható az Azure Blob Storage-célok pillanatkép-szolgáltatása. A pillanatkép-szolgáltatáshoz nem tartozik konfigurálható beállítás.

A beállítások megjelenítéséhez nyissa meg a gyorsítótár **hálózatkezelés** lapját a Azure Portal.

![képernyőfelvétel a hálózatkezelés lapról Azure Portal](media/networking-page.png)

> [!NOTE]
> A lap egy korábbi verziója tartalmaz egy gyorsítótár-szintű root squash-beállítást, de ez a beállítás át lett helyezve az [ügyfél-hozzáférési házirendbe](access-policies.md).

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>MTU értékének módosítása
<!-- linked from troubleshoot-nas article -->

A gyorsítótár maximális átviteli egységének mérete a legördülő menüben az **MTU-méret** lehetőség kiválasztásával adható meg.

Az alapértelmezett érték 1500 bájt, de módosíthatja azt 1400-ra.

> [!NOTE]
> Ha csökkenti a gyorsítótár MTU-méretét, győződjön meg arról, hogy a gyorsítótárral kommunikáló ügyfelek és tárolók azonos MTU-beállítással vagy alacsonyabb értékkel rendelkeznek.

A gyorsítótár MTU-értékének csökkentése segíthet a csomagok méretének korlátozásában a gyorsítótár hálózatának többi részén. Egyes VPN-EK például nem tudnak teljes méretű, 1500 bájtos csomagokat továbbítani. A VPN-en keresztül küldött csomagok méretének csökkentése feltehetőleg ezt a problémát kizárja. Vegye figyelembe azonban, hogy egy alacsonyabb gyorsítótár-MTU-beállítás azt jelenti, hogy a gyorsítótárral kommunikáló más összetevők, beleértve az ügyfeleket és a tárolási rendszereket is, alacsonyabb MTU-beállítással kell rendelkezniük a kommunikációs problémák elkerüléséhez.

Ha nem szeretné módosítani az MTU-beállításokat más rendszerösszetevőkön, ne csökkentse a gyorsítótár MTU-beállítását. A VPN-csomagok méretére vonatkozó korlátozások más megoldásokkal is használhatók. A probléma diagnosztizálásával és kezelésével kapcsolatos további információkért olvassa el a [VPN-csomagok méretére vonatkozó korlátozásokat](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) a NAS kiszolgáló hibaelhárítási cikkében.

További információ az Azure Virtual Networks MTU-beállításairól: [TCP/IP teljesítmény-hangolás az Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md)-beli virtuális gépekhez.

## <a name="customize-ntp"></a>Az NTP testreszabása

A gyorsítótár alapértelmezés szerint az Azure-alapú időkiszolgáló time.microsoft.com használja. Ha azt szeretné, hogy a gyorsítótár egy másik NTP-kiszolgálót használjon, adja meg azt az **NTP-konfiguráció** szakaszban. Használjon teljesen minősített tartománynevet vagy IP-címet.

## <a name="set-a-custom-dns-configuration"></a>Egyéni DNS-konfiguráció beállítása

> [!CAUTION]
> Ha nem szükséges, ne módosítsa a gyorsítótár DNS-konfigurációját. A konfigurációs hibák végzetes következményekkel járhatnak. Ha a konfiguráció nem tudja feloldani az Azure-szolgáltatások nevét, a HPC-gyorsítótár példánya véglegesen elérhetetlenné válik.

Az Azure HPC cache automatikusan a biztonságos és kényelmes Azure DNS rendszer használatára van konfigurálva. Néhány szokatlan konfiguráció azonban megköveteli, hogy a gyorsítótár egy különálló, helyszíni DNS-rendszer használatát használja az Azure-rendszer helyett. A **hálózatkezelés** lap **DNS-konfiguráció** szakasza az ilyen típusú rendszer megadására szolgál.

Kérjen segítséget az Azure-képviselőkkel, vagy forduljon a Microsoft szolgáltatáshoz és a támogatási szolgálathoz, és állapítsa meg, hogy szükséges-e egyéni gyorsítótár DNS-konfiguráció használata.

Ha saját helyszíni DNS-rendszerét konfigurálja az Azure HPC cache használatára, meg kell győződnie arról, hogy a konfiguráció képes az Azure-szolgáltatásokhoz tartozó Azure-végpontok nevének feloldására. Az egyéni DNS-környezetet úgy kell konfigurálni, hogy bizonyos névfeloldási kérelmeket továbbítson Azure DNS vagy egy másik kiszolgálóra, igény szerint.

Győződjön meg arról, hogy a DNS-konfiguráció sikeresen fel tudja oldani ezeket az elemeket, mielőtt egy Azure HPC-gyorsítótárhoz használja azt:

* ``*.core.windows.net``
* A visszavont tanúsítványok listájának (CRL) letöltése és az online tanúsítványállapot-protokoll (OCSP) ellenőrző szolgáltatása. Az [Azure TLS jelen cikkének](../security/fundamentals/tls-certificate-changes.md)végén található [Tűzfalszabályok elemben](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me) részleges lista szerepel, de az összes követelmény megismeréséhez forduljon a Microsoft technikai képviselőjéhez.
* Az NTP-kiszolgáló teljesen minősített tartományneve (time.microsoft.com vagy egyéni kiszolgáló)

Ha egyéni DNS-kiszolgálót kell beállítania a gyorsítótárhoz, használja a megadott mezőket:

* **DNS-keresési tartomány** (nem kötelező) – adja meg a keresési tartományt, például: ``contoso.com`` . Egyetlen érték megengedett, vagy üresen hagyhatja.
* **DNS-kiszolgáló (k)** – legfeljebb három DNS-kiszolgálót adjon meg. IP-cím szerint határozza meg őket.

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

### <a name="refresh-storage-target-dns"></a>Tárolási cél DNS frissítése

Ha a DNS-kiszolgáló frissíti az IP-címeket, akkor a társított NFS-tárolási célok átmenetileg elérhetetlenné válnak. Olvassa el, hogyan frissítheti az egyéni DNS-rendszer IP-címeit a [tárolási célok szerkesztése](hpc-cache-edit-storage.md#update-ip-address-custom-dns-configurations-only)című témakörben.

## <a name="view-snapshots-for-blob-storage-targets"></a>BLOB Storage-célokhoz tartozó Pillanatképek megtekintése

Az Azure HPC cache automatikusan menti az Azure Blob Storage-célok tárolási pillanatképeit. A pillanatképek gyors hivatkozási pontot biztosítanak a háttérbeli tároló tartalmához.

A pillanatképek nem helyettesítik az adatok biztonsági mentését, és nem tartalmaznak semmilyen információt a gyorsítótárazott adatok állapotáról.

> [!NOTE]
> Ez a pillanatkép-szolgáltatás eltér a NetApp vagy a Isilon Storage szoftverben található pillanatkép-szolgáltatástól. Ezek a pillanatkép-implementációk a pillanatkép elkészítése előtt ürítik a gyorsítótárból a háttérbeli tárolási rendszerre vonatkozó változásokat.
>
> A hatékonyság érdekében az Azure HPC gyorsítótár-pillanatképe nem üríti ki először a módosításokat, és csak a blob-tárolóba írt adatokat rögzíti. Ez a pillanatkép nem tükrözi a gyorsítótárazott adatok állapotát, ezért előfordulhat, hogy nem tartalmazza a legutóbbi módosításokat.

Ez a funkció csak az Azure Blob Storage-célokhoz érhető el, és a konfigurációja nem módosítható.

A pillanatképeket nyolc óránként, UTC 0:00, 08:00 és 16:00 nyelven kell elvégezni.

Az Azure HPC cache napi, heti és havi pillanatképeket tárol, amíg újakat nem helyettesítenek. A korlátok a következők:

* Akár 20 napi pillanatkép
* Legfeljebb 8 heti pillanatkép
* Legfeljebb 3 havi pillanatkép

A pillanatképeket a `.snapshot` blob Storage-tároló névterében lévő könyvtárból érheti el.
