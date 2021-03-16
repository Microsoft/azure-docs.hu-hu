---
title: Kiterjesztett csoportok használata az Azure HPC cache-ben
description: Címtárszolgáltatások konfigurálása a tárolási célokhoz való hozzáféréshez az Azure HPC cache-ben
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: fd5dce0760953bf19c72e1a1062a9c03ffe861e7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563372"
---
# <a name="configure-directory-services"></a>Címtárszolgáltatások konfigurálása

A **Címtárszolgáltatások** beállításai lehetővé teszik, hogy az Azure HPC-gyorsítótár külső forrás használatával hitelesítse a felhasználókat a háttérbeli tárolók eléréséhez.

Előfordulhat, hogy engedélyeznie kell a **kibővített csoportokat** , ha a munkafolyamat olyan NFS-tárolási célokat és ügyfeleket tartalmaz, amelyek több mint 16 csoport tagjai.

Miután rákattintott a gombra a kiterjesztett csoportok engedélyezéséhez, ki kell választania azt a forrást, amelyet az Azure HPC cache a felhasználó és a csoport hitelesítő adatainak beolvasásához használ majd.

* [Active Directory](#configure-active-directory) – hitelesítő adatok beolvasása egy külső Active Directory-kiszolgálóról. Ehhez a feladathoz nem használható Azure Active Directory.
* [Lapos fájl](#configure-file-download) – `/etc/group` egy hálózati helyről tölthető le és tölthet le `/etc/passwd` fájlokat.
* [LDAP](#configure-ldap) – hitelesítő adatok beolvasása egy Lightweight Directory Access Protocol-(LDAP-) kompatibilis forrásból.

> [!NOTE]
> Győződjön meg arról, hogy a gyorsítótár a biztonságos alhálózatán belülről fér hozzá a csoport adatforrásához.<!-- + details/examples -->

A **felhasználó által letöltött Felhasználónév** mező megjeleníti a legutóbbi csoport adatainak letöltésének állapotát.

![képernyőkép a címtár-szolgáltatási oldal beállítások oldaláról a portálon, a kiterjesztett csoportok esetében pedig a Yes (igen) lehetőség van kiválasztva, és a legördülő menü a letöltés forrásának megnyitása](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>Az Active Directory konfigurálása

Ez a szakasz azt ismerteti, hogyan állíthatja be a gyorsítótárat a felhasználói és csoportos hitelesítő adatok beolvasásához egy külső Active Directory (AD) kiszolgálóról.

Adja meg az **Active Directory adatai** területen a következő értékeket:

* **Elsődleges DNS** – adja meg egy olyan tartománynév-kiszolgáló IP-címét, amelyet a gyorsítótár HASZNÁLHAT az ad-tartománynév feloldásához.

* **Másodlagos DNS** (nem kötelező) – Itt adhatja meg annak a kiszolgálónak a nevét, amelyet használni szeretne, ha az elsődleges kiszolgáló nem érhető el.

* **Ad DNS-tartománynév** – adja meg annak az ad-kiszolgálónak a teljes tartománynevét, amelyhez a gyorsítótár csatlakozni fog a hitelesítő adatok beszerzéséhez.

* **Gyorsítótár-kiszolgáló neve (számítógépfiók)** – a HPC-gyorsítótárhoz hozzárendelni kívánt név beállítása az ad-tartományhoz való csatlakozáskor. Olyan nevet adjon meg, amely könnyen felismerhető a gyorsítótárként. A név legfeljebb 15 karakter hosszúságú lehet, és tartalmazhat nagybetűket, számokat és kötőjeleket (-).

A **hitelesítő adatok** szakaszban adjon meg egy ad-rendszergazdai felhasználónevet és jelszót, amelyet az Azure HPC gyorsítótára HASZNÁLHAT az ad-kiszolgáló eléréséhez. Ezeket az információkat a rendszer a tároláskor titkosítja, és nem kérdezheti le.

Mentse a beállításokat az oldal tetején található gombra kattintva.

![képernyőfelvétel a részletekről szakasz a Active Directory kitöltött értékekkel](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>Fájl letöltésének konfigurálása

Ezek az értékek akkor szükségesek, ha le szeretné tölteni a fájlokat a felhasználói és a csoport adataival. A fájloknak a szabványos Linux/UNIX `/etc/group` és formátumban kell lenniük `/etc/passwrd` .

* **Felhasználói fájl URI-ja** – adja meg a fájl teljes URI-ját `/etc/passwrd` .
* **Csoport fájljának URI-ja** – adja meg a fájl teljes URI-ját `/etc/group` .

![képernyőkép a letöltés részleteiről szakasz, amely egy egyszerű fájl letöltését tartalmazza](media/group-download-details-file.png)

## <a name="configure-ldap"></a>Az LDAP konfigurálása

Ha nem AD LDAP-forrást szeretne használni a felhasználók és a csoportok hitelesítő adatainak beolvasásához, adja meg ezeket az értékeket. Ha segítségre van szüksége ezekkel az értékekkel, forduljon az LDAP-rendszergazdához.

* **LDAP-kiszolgáló** – adja meg a használni kívánt LDAP-kiszolgáló teljes tartománynevét vagy IP-címét. <!-- only one, not up to 3 -->

* **LDAP Base DN** – a megkülönböztető nevet adja meg az LDAP-tartományhoz DN formátumban. Kérdezze meg az LDAP-rendszergazdát, ha nem ismeri az Alap DN-t.

A kiszolgáló és az Alap DN az egyetlen szükséges beállítás az LDAP-működéshez, de a további beállítások biztonságosabbá teszik a kapcsolatokat.

![képernyőfelvétel a címtárszolgáltatások oldalának beállítások oldalának LDAP-konfigurációs területéről](media/group-download-details-ldap.png)

A **biztonságos hozzáférés** szakaszban engedélyezheti az LDAP-kapcsolat titkosítását és tanúsítvány-ellenőrzését. Miután az **Igen** gombra kattintott a titkosítás engedélyezéséhez, a következő lehetőségek közül választhat:

* **Tanúsítvány érvényesítése** – ha be van állítva, az LDAP-kiszolgáló tanúsítványát a rendszer az alábbi URI-mezőben ellenőrzi a hitelesítésszolgáltatótól.

* **Hitelesítésszolgáltatói tanúsítvány URI-ja** – megadja a mérvadó tanúsítvány elérési útját. Ez lehet egy HITELESÍTÉSSZOLGÁLTATÓ által hitelesített tanúsítványra vagy egy önaláírt tanúsítványra mutató hivatkozás. Ez a mező a külsőleg érvényesített tanúsítványok beállításának használatához szükséges.

* **Tanúsítvány automatikus letöltése** – válassza az **Igen** lehetőséget, ha a beállítások elküldésekor azonnal le szeretné tölteni a tanúsítványt.

Adja meg a **hitelesítő adatok** szakaszt, ha statikus hitelesítő adatokat szeretne használni az LDAP-biztonsághoz. Ezeket az információkat a rendszer a tároláskor titkosítja, és nem kérdezheti le.

* **Kötési DN** – adja meg az LDAP-kiszolgáló hitelesítéséhez használandó kötési megkülönböztető nevet. (DN formátum használata.)
* **Kötési jelszó** – adja meg a kötési DN jelszavát.

## <a name="next-steps"></a>Következő lépések

* További információ az [Azure HPC cache csatlakoztatásával](hpc-cache-mount.md) kapcsolatos ügyfél-hozzáférésről
* Ha nem megfelelően tölti le a hitelesítő adatait, a hitelesítő adatok forrásának rendszergazdájával forduljon a rendszergazdához. Ha szükséges, nyisson meg egy [támogatási jegyet](hpc-cache-support-ticket.md) .
