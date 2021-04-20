---
title: Azure DNS delegálás áttekintése
description: Ismerje meg, hogyan módosíthatja a tartományok delegálását és használhatja tartományszolgáltatóként az Azure DNS-névkiszolgálóit.
services: dns
author: rohinkoul
ms.service: dns
ms.date: 04/19/2021
ms.author: rohink
ms.topic: conceptual
ms.openlocfilehash: 4753b07cc2f3ccd998c26a3392eb08c8761dd6f7
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738845"
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>DNS-zónák delegálása az Azure DNS-sel

Az Azure DNS használatával DNS-zónákat üzemeltethet, és kezelheti a tartomány DNS-rekordjait az Azure felületén. Egy tartomány DNS-lekérdezései csak akkor érik el az Azure DNS-t, ha a tartomány delegálva van az Azure DNS-be a szülőtartományból. Ne feledje, Azure DNS nem a tartományregisztráló. Ez a cikk ismerteti a tartománydelegálás működését és a tartományok Azure DNS-be való delegálását.

## <a name="how-dns-delegation-works"></a>A DNS-delegálás működése

### <a name="domains-and-zones"></a>Tartományok és zónák

A tartománynévrendszer tartományok hierarchiájából áll. A hierarchia első eleme a „gyökértartomány”, amelynek neve egyszerűen „**.**”.  Ez alatt találhatók a legfelső szintű tartományok, mint a „com”, a „net”, az „org”, az „uk” vagy a „jp”.  A legfelső szintű tartományok alatt találhatók a másodlagos szintű tartományok, mint az „org.uk” vagy a „co.jp”.  És így tovább. A DNS-hierarchia tartományait különálló DNS-zónák üzemeltetik. A zónák globálisan fel vannak osztva, és a világ különböző pontjain található DNS-névkiszolgálók üzemeltetik őket.

**DNS-zóna** – A tartományok egyedi nevek a tartománynévrendszerben, például „contoso.com”. Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A „contoso.com” tartomány például számos DNS-rekordot tartalmazhat: „mail.contoso.com” (levelezési kiszolgálóhoz) és „www.contoso.com” (webhelyhez).

**Tartományregisztráló** – A tartományregisztráló egy olyan cég, amely internetes tartományneveket biztosít. Ezek a cégek ellenőrzik, hogy a használni kívánt internetes tartomány elérhető-e, és ők engedélyezik azok megvásárlását. A tartománynév regisztrációja után Ön a tartománynév jogi tulajdonosa. Ha már rendelkezik internetes tartománnyal, az aktuális tartományregisztrálóval delegálhat a Azure DNS.

További információ a meghatalmazott tartományregisztrálókról: [ICANN-Registrar Registrars](https://www.icann.org/registrar-reports/accredited-list.html).

### <a name="resolution-and-delegation"></a>Feloldás és delegálás

Kétféle DNS-kiszolgáló létezik:

* A *mérvadó* DNS-kiszolgáló üzemelteti a DNS-zónákat. Csak az ezekben a zónákban található rekordokra irányuló DNS-lekérdezéseket válaszolja meg.
* A *rekurzív DNS-kiszolgálók* nem szolgálnak DNS-zónákkal. Minden DNS-lekérdezést megválaszol a mérvadó DNS-kiszolgálók adatait összegyűjtve.

Az Azure DNS mérvadó DNS szolgáltatást nyújt.  Nem biztosít rekurzív DNS-szolgáltatást. Az Azure felhőszolgáltatásai és virtuális gépei automatikusan egy rekurzív DNS szolgáltatás használatára vannak konfigurálva, amelyet az Azure-infrastruktúra külön biztosít. Ha további információt szeretne kapni ezen DNS-beállítások módosításáról, olvassa el az [Azure-beli névfeloldást](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) ismertető cikket.

A számítógépeken vagy mobileszközökön található DNS-ügyfelek általában rekurzív DNS-kiszolgálót hívnak meg az ügyfélalkalmazások által szükséges DNS-lekérdezések éhez.

Amikor egy rekurzív DNS-kiszolgáló egy DNS-rekordra (például „www.contoso.com”) vonatkozó lekérdezést kap, először meg kell keresnie a „contoso.com” tartomány zónáját üzemeltető névkiszolgálót. A névkiszolgáló megkereséséhez a gyökér-névkiszolgálótól kiindulva megkeresi a „com” zónát üzemeltető névkiszolgálókat. Ezután lekérdezi a „com” névkiszolgálóktól a „contoso.com” zónát üzemeltető névkiszolgálókat.  Végül képes lekérdezni ezeket a névkiszolgálókat a "www.contoso.com".

Ez az eljárás a DNS-név feloldása. Szigorúan véve a DNS-feloldás több lépést is tartalmaz, például a KÖVETKEZŐ CNAME-ket, de ez nem fontos a DNS-delegálás működése megértéséhez.

Hogyan „mutat rá” egy szülőzóna a gyermekzóna névkiszolgálóira? Ezt egy speciális DNS-rekorddal, az úgynevezett névkiszolgálói rekorddal hajtja végre. Például a gyökérzóna tartalmazza a „com” névkiszolgálói rekordjait, és megjeleníti a „com” zóna névkiszolgálóit. A „com” zóna pedig tartalmazza a „contoso.com” névkiszolgálói rekordjait, amelyek a „contoso.com” zóna névkiszolgálóit mutatják. Egy szülőzónán belüli gyermekzóna névkiszolgálói rekordjainak beállítását nevezzük tartománydelegálásnak.

Az alábbi képen egy példa DNS-lekérdezés látható. A contoso.net és a partners.contoso.net Azure DNS-zónák.

![DNS-névkiszolgáló](./media/dns-domain-delegation/image1.png)

1. Az ügyfél lekéri a `www.partners.contoso.net` címet a helyi DNS-kiszolgálóról.
2. A helyi DNS-kiszolgáló nem tudja a rekordot, ezért kérést továbbít a gyökérnév-kiszolgálónak.
3. A gyökérnév-kiszolgáló nem rendelkezik a rekordokkal, de ismeri a névkiszolgáló címét, és ezt a címet biztosítja a `.net` DNS-kiszolgálónak
4. A helyi DNS-kiszolgáló elküldi a kérést a `.net` névkiszolgálónak.
5. A `.net` névkiszolgáló nem rendelkezik a rekordokkal, de ismeri a névkiszolgáló `contoso.net` címét. Ebben az esetben a dns-zóna névkiszolgálójának címével válaszol, amely a Azure DNS.
6. A helyi DNS-kiszolgáló elküldi a kérést a helyi kiszolgálón üzemeltetett zóna `contoso.net` Azure DNS.
7. A zónában nem található meg a rekord, de ismeri a névkiszolgálóját, és `contoso.net` `partners.contoso.net` a címmel válaszol. Ebben az esetben ez egy dns-zóna, amely a Azure DNS.
8. A helyi DNS-kiszolgáló elküldi a kérést a zóna `partners.contoso.net` névkiszolgálójának.
9. A `partners.contoso.net` zóna rendelkezik az A rekordgal, és az IP-címmel válaszol.
10. A helyi DNS-kiszolgáló biztosítja az IP-címet az ügyfélnek
11. Az ügyfél csatlakozik a `www.partners.contoso.net` webhelyhez.

A delegálások a névkiszolgálói rekordok két példányával rendelkeznek: egy a gyermekzónára mutató szülőzónában, egy pedig magában a gyermekzónában található. A „contoso.net” zóna a „net” névkiszolgálói rekordjai mellett a „contoso.net” névkiszolgálói rekordjait is tartalmazza. Ezek a rekordok az úgynevezett mérvadó névkiszolgálói rekordok, és a gyermekzóna tetején találhatók.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [delegálhat tartományokat az Azure DNS-be](dns-delegate-domain-azure-dns.md).
