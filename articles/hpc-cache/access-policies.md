---
title: Hozzáférési szabályzatok használata az Azure HPC cache-ben
description: Egyéni hozzáférési házirendek létrehozása és alkalmazása az Azure HPC cache-ben a tárolási célokhoz való hozzáférés korlátozásához
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: eb9e71cc8ec463077e3b12b8738203a4945a2eab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471775"
---
# <a name="control-client-access"></a>Ügyfél-hozzáférés vezérlése

Ez a cikk bemutatja, hogyan hozhat létre és alkalmazhat egyéni ügyfél-hozzáférési házirendeket a tárolási célokhoz.

Az ügyfél-hozzáférési házirendek határozzák meg, hogy az ügyfelek hogyan csatlakozhatnak a tárolási cél exportálásához. Megadhatja a root squash-és olvasási/írási hozzáférést az ügyfél gazdagépén vagy hálózati szintjén.

A hozzáférési házirendek a névtér elérési útjára lesznek alkalmazva, ami azt jelenti, hogy különböző hozzáférési házirendeket használhat két különböző exportáláshoz egy NFS-tárolási rendszeren.

Ez a szolgáltatás olyan munkafolyamatokhoz használható, ahol meg kell határozni, hogy az ügyfelek különböző csoportjai hogyan férhessenek hozzá a tárolási célokhoz.

Ha nincs szüksége a tárolási cél elérésének részletes szabályozására, használhatja az alapértelmezett házirendet, vagy az alapértelmezett szabályzatot további szabályokkal is testreszabhatja. Ha például engedélyezni szeretné a root squash-t a gyorsítótáron keresztül csatlakozó összes ügyfél számára, akkor az **alapértelmezett** nevű szabályzatot a legfelső szintű squash-beállítás hozzáadásához is módosíthatja.

## <a name="create-a-client-access-policy"></a>Ügyfél-hozzáférési házirend létrehozása

A házirendek létrehozásához és kezeléséhez használja a Azure Portal **ügyfél-hozzáférési házirendek** lapját. <!-- is there AZ CLI for this? -->

[![képernyőkép az ügyfél-hozzáférési házirendek lapról. Több házirend van meghatározva, és néhányat ki kell terjeszteni a szabályok megjelenítésére](media/policies-overview.png)](media/policies-overview.png#lightbox)

Az egyes szabályzatok szabályokból állnak. A szabályokat a rendszer a legkisebb hatókör (gazdagép) és a legnagyobb (alapértelmezett) tartományba tartozó gazdagépekre alkalmazza. A rendszer az első megegyező szabályt alkalmazza, és a későbbi szabályok figyelmen kívül lesznek hagyva.

Új hozzáférési szabályzat létrehozásához kattintson a lista tetején található **+ hozzáférési házirend hozzáadása** gombra. Adja meg az új hozzáférési szabályzat nevét, és adjon meg legalább egy szabályt.

![képernyőkép a hozzáférési szabályzatok szerkesztési paneljéről, amelyekben számos szabály van kitöltve. A szabály mentéséhez kattintson az OK gombra.](media/add-policy.png)

A szakasz további része a szabályokban használható értékeket ismerteti.

### <a name="scope"></a>Hatókör

A hatókör és a Címterület együttesen határozzák meg, hogy mely ügyfelekre van hatással a szabály.

Ezekkel a beállításokkal adhatja meg, hogy a szabály egy adott ügyfélre (gazdagépre), az IP-címek tartományára (hálózat) vagy az összes ügyfélre vonatkozik (alapértelmezés).

Válassza ki a megfelelő **hatókör** -értéket a szabályhoz:

* **Gazdagép** – a szabály egy adott ügyfélre vonatkozik.
* **Hálózat** – a szabály az ügyfelekre vonatkozik az IP-címek egy tartományában.
* **Alapértelmezett** – a szabály az összes ügyfélre vonatkozik.

A szabályzat szabályai ebben a sorrendben vannak kiértékelve. Miután egy ügyfél-csatlakoztatási kérelem megfelel egy szabálynak, a többi figyelmen kívül lesz hagyva.

### <a name="address-filter"></a>Címterület

A **címterület** értéke határozza meg, hogy mely ügyfelek egyeznek a szabállyal.

Ha a hatókört a **gazdagépre** állítja be, csak egy IP-címet adhat meg a szűrőben. A hatókör beállítás **alapértelmezett** értékeként nem adhat meg IP-címeket a **cím szűrő** mezőben, mert az alapértelmezett hatókör minden ügyfélnek megfelel.

A szabályhoz tartozó IP-cím vagy címtartomány megadása. Címtartomány megadásához használja a CIDR-jelölést (például: 0.1.0.0 verziójára/16).

### <a name="access-level"></a>Hozzáférési szint

Állítsa be, hogy milyen jogosultságokat biztosítson a hatókörnek és a szűrőnek megfelelő ügyfelek számára.

A beállítások **olvasási/írási**, **írásvédett** vagy **Nincs hozzáférés**.

### <a name="suid"></a>SUID

Jelölje be a **suid** jelölőnégyzetet, ha engedélyezni szeretné a tárolóban lévő fájlok számára a felhasználói azonosítók beállítását a hozzáféréshez.

A SUID általában a felhasználó jogosultságának ideiglenes növelésére szolgálnak, így a felhasználó elvégezheti az adott fájllal kapcsolatos feladatot.

### <a name="submount-access"></a>Alcsatlakoztatási hozzáférés

Jelölje be ezt a jelölőnégyzetet, ha engedélyezni szeretné, hogy a megadott ügyfelek közvetlenül csatlakoztassa az Exportálás alkönyvtárait.

### <a name="root-squash"></a>Gyökér squash

Adja meg, hogy a legfelső szintű squash legyen-e beállítva a szabálynak megfelelő ügyfelek számára.

Ezzel a beállítással szabályozható, hogy az Azure HPC cache hogyan kezelje az ügyfélszámítógépeken a legfelső szintű felhasználótól érkező kéréseket. Ha a root squash engedélyezve van, a rendszer automatikusan egy nem Kiemelt felhasználóhoz rendeli hozzá a root felhasználókat, amikor az Azure HPC cache-n keresztül küldenek kéréseket. Azt is megakadályozza, hogy az ügyfelek kérései a set-UID engedélyezési biteket használják.

Ha a root squash le van tiltva, a rendszer az ügyfél gyökérszintű felhasználójának (UID 0) kérelmét átadja egy háttérként szolgáló NFS Storage rendszernek. Ez a konfiguráció a nem megfelelő fájlok elérését is lehetővé teheti.

A root squash beállítása az ügyfelek kéréseinek megoldásával kompenzálhatja a szükséges ``no_root_squash`` beállítást a tárolási célként használt NAS-rendszereken. (További információ az [NFS-tárolási cél előfeltételeiről](hpc-cache-prerequisites.md#nfs-storage-requirements).) Emellett az Azure Blob Storage-célokkal való használatkor is javíthatja a biztonságot.

Ha bekapcsolja a bekapcsolást, akkor a névtelen azonosító felhasználói értékét is be kell állítania. A portál 0 és 4294967295 közötti egész értékeket fogad el. (A régi értékek-2 és-1 a visszamenőleges kompatibilitás érdekében támogatottak, de új konfigurációk esetén nem ajánlott.)

Ezek az értékek meghatározott felhasználói értékekre vannak leképezve:

* **-2** vagy **65534** (senki)
* **-1** vagy **65535** (nincs hozzáférés)
* **0** (nem rendszerjogosultságú gyökér)

Előfordulhat, hogy a tárolási rendszere más, speciális jelentésekkel rendelkező értékekkel rendelkezik.

## <a name="update-access-policies"></a>Hozzáférési szabályzatok frissítése

Az **ügyfél-hozzáférési házirendek** lapon lévő táblából szerkesztheti vagy törölheti a hozzáférési házirendeket.

Kattintson a szabályzat nevére a szerkesztéshez való megnyitásához.

Ha törölni szeretne egy házirendet, jelölje be a neve melletti jelölőnégyzetet a listában, majd kattintson a lista tetején található **Törlés** gombra. A "default" nevű szabályzat nem törölhető.

> [!NOTE]
> Nem törölhet olyan hozzáférési szabályzatot, amely használatban van. Távolítsa el a szabályzatot minden olyan névtérbeli elérési útról, amely tartalmazza azt a törlés megkísérlése előtt.

## <a name="next-steps"></a>Következő lépések

* A tárolási célok névterének elérési útjain alkalmazza a hozzáférési házirendeket. Olvassa el [az összesített névtér beállítása](add-namespace-paths.md) című témakört.
