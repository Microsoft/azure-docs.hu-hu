---
title: A Data Lake Storage Gen1 hozzáférés-vezérlésének áttekintése | Microsoft Docs
description: Ismerkedjen meg a HDFS-ből származó Azure Data Lake Storage Gen1 hozzáférés-vezérlési modelljének alapjaival.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: aa0da5721c577957b101ac8a2d9346c0536f0a88
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424138"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Hozzáférés-vezérlés az Azure Data Lake Storage Gen1-ben

Azure Data Lake Storage Gen1 a HDFS származtatott hozzáférés-vezérlési modellt valósít meg, amely a POSIX hozzáférés-vezérlési modelljéből származik. Ez a cikk a Data Lake Storage Gen1 hozzáférés-vezérlési modelljének alapjait összegzi. 

## <a name="access-control-lists-on-files-and-folders"></a>Fájlokra és mappákra vonatkozó hozzáférés-vezérlési listák

Kétféle hozzáférés-vezérlési lista (ACL) létezik – a **hozzáférési ACL** és az **alapértelmezett ACL**.

* **Hozzáférési ACL-ek**: Egy objektum hozzáféréseit vezérlik. A fájlok és mappák egyaránt rendelkeznek hozzáférési ACL-lel.

* **Alapértelmezett ACL**: A mappához társított ACL „sablon”, amely meghatározza az adott mappában létrehozott gyermekelemek hozzáférési ACL-jeit. A fájlok nem rendelkeznek alapértelmezett ACL-ekkel.


A hozzáférési ACL-ek és alapértelmezett ACL-ek ugyanazzal a struktúrával rendelkeznek.

> [!NOTE]
> Ha megváltoztatja az alapértelmezett ACL-eket egy szülő objektumon, akkor az nem módosítja a már létező gyermekelemek hozzáférési ACL-jeit és alapértelmezett ACL-jeit.
>
>

## <a name="permissions"></a>Engedélyek

A fájlrendszer objektumaira vonatkozó engedélyek a következők: **Olvasás**, **Írás**, és **Végrehajtás**. Ezek az engedélyek használhatók fájlok és mappák esetében a következő táblázatban látható módon:

|            |    Fájl     |   Mappa |
|------------|-------------|----------|
| **Olvasás (R)** | Olvashatja a fájl tartalmát | **Olvasás** és **végrehajtás** szükséges a mappa tartalmának listázásához.|
| **Írás (W)** | Írhatja a fájlt vagy hozzáfűzhet a fájlhoz | **Írás** és **Végrehajtás** szükséges gyermekelemek létrehozásához a mappában |
| **Végrehajtás (X)** | Nem jelent semmit a Data Lake Storage Gen1 kontextusában | Szükséges egy mappa gyermekelemeinek bejárásához |

### <a name="short-forms-for-permissions"></a>Az engedélyek rövid alakjai

**RWX** az **Olvasás + Írás + Végrehajtás** engedélyt jelöli. Egy tömörebb, numerikus formátum is létezik, amelyben **Olvasás=4**, **Írás=2** és **Végrehajtás=1**, és az összegük jelöli az engedélyeket. Az alábbiakban néhány példa látható.

| Numerikus alak | Rövid alak |      Jelentés     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Olvasás + Írás + Végrehajtás |
| 5            | `R-X`        | Olvasás + Végrehajtás         |
| 4            | `R--`        | Olvasás                   |
| 0            | `---`        | Nincs engedély         |


### <a name="permissions-do-not-inherit"></a>Az engedélyek nem öröklődnek

Az Data Lake Storage Gen1 által használt POSIX stílusú modellben az elemhez tartozó engedélyeket maga az elem tárolja. Vagyis egy elem az engedélyeit nem örökölheti a szülőelemektől.

## <a name="common-scenarios-related-to-permissions"></a>Az engedélyekhez kapcsolódó gyakori helyzetek

Az alábbiakban néhány gyakori forgatókönyv segít megérteni, hogy mely engedélyek szükségesek bizonyos műveletek végrehajtásához egy Data Lake Storage Gen1 fiókon.

| Művelet | Objektum              |    /      | Seattle   | Portland   | Data.txt       |
|-----------|---------------------|-----------|------------|-------------|----------------|
| Olvasás      | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Hozzáfűzés a következőhöz | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `-W-`          |
| Törlés    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Létrehozás    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista      | /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista      | Seattle           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista      | /Seattle/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> A fájlra vonatkozó írási engedélyek nem szükségesek a fájl törléséhez, amíg az előbbi két feltétel teljesül.
>
>


## <a name="users-and-identities"></a>Felhasználók és identitások

Minden fájl és mappa külön engedélyekkel rendelkezik az alábbi identitásokhoz:

* A tulajdonos felhasználó
* A tulajdonoscsoport
* Nevesített felhasználók
* Nevesített csoportok
* Minden egyéb felhasználó

A felhasználók és csoportok identitása Azure Active Directory- (Azure AD-) indentitás. Tehát ha a "felhasználó" a Data Lake Storage Gen1 kontextusában másként nincs jelezve, az egy Azure AD-felhasználó vagy egy Azure AD-biztonsági csoport lehet.

### <a name="the-super-user"></a>A felügyelő

A felügyelők a Data Lake Storage Gen1 fiók összes felhasználójának a legtöbb jogosultsággal rendelkeznek. A felügyelő:

* RWX-engedéllyel (olvasás, írás és végrehajtás) rendelkezik az **összes** fájlhoz és mappához.
* Bármely fájl vagy mappa engedélyeit megváltoztathatja.
* Bármely fájl vagy mappa tulajdonosát vagy tulajdonoscsoportját megváltoztathatja.

A Data Lake Storage Gen1 fiók **tulajdonosi** szerepköréhez tartozó összes felhasználó automatikusan egy felügyelő.

### <a name="the-owning-user"></a>A tulajdonos felhasználó

Automatikusan az elem tulajdonosa lesz az a felhasználó, aki létrehozta az elemet. A tulajdonos felhasználó:

* Megváltoztathatja a tulajdonában lévő fájl engedélyeit.
* megváltoztathatja a tulajdonában lévő fájl tulajdonos csoportját, ha a tulajdonos felhasználó szintén tagja ennek a csoportnak.

> [!NOTE]
> A tulajdonos *nem* változtathatja meg egy fájl vagy mappa tulajdonos felhasználóját. Csak a felügyelők változtathatják meg egy fájl vagy mappa tulajdonosát vagy tulajdonoscsoportját.
>
>

### <a name="the-owning-group"></a>A tulajdonoscsoport

**Háttér**

A POSIX ACL-ekben minden felhasználó társítva van egy „elsődleges csoporttal”. Például az „alice” nevű felhasználó a „finance” csoportba tartozhat. Alice több csoporthoz is tartozhat, de egy csoport mindig ki van jelölve elsődleges csoportjaként. A POSIX-ben ha Alice létrehoz egy fájlt, a fájl tulajdonoscsoportja Alice elsődleges csoportja lesz, ami ebben az esetben a „finance”. A tulajdonos csoport egyéb esetben egyéb felhasználókhoz/csoportokhoz hozzárendelt engedélyekhez hasonlóan viselkedik.

Mivel nincs "elsődleges csoport" társítva a Data Lake Storage Gen1 felhasználókhoz, a tulajdonos csoport az alábbiak szerint lesz hozzárendelve.

**A tulajdonos csoport kiosztása új fájlhoz vagy mappához**

* **1. eset**: A gyökérmappa „/”. Ez a mappa jön létre Data Lake Storage Gen1 fiók létrehozásakor. Ebben az esetben a tulajdonos csoport egy all-Zero GUID azonosítóra van beállítva.  Ez az érték nem engedélyezi a hozzáférést.  Ez a helyőrző egészen addig, amíg egy csoportot hozzá nem rendelnek.
* **2. eset** (minden egyéb eset): Egy új elem létrehozásakor a tulajdonoscsoport a szülőmappából másolódik át.

**A tulajdonos csoport módosítása**

A tulajdonoscsoportot megváltoztathatja:
* Bármely felügyelő.
* a tulajdonos, ha szintén tagja ennek a csoportnak.

> [!NOTE]
> A tulajdonoscsoport *nem* változtathatja meg egy fájl vagy mappa ACL-eit.
>
> Az 2018-on vagy azt megelőzően létrehozott fiókokhoz a tulajdonos csoportot arra a felhasználóra állították be, aki létrehozta a fiókot az **1. eset** esetén a gyökérmappa esetében.  Egyetlen felhasználói fiók nem érvényes a tulajdonos csoporton keresztüli engedélyek biztosításához, így ez az alapértelmezett beállítás nem biztosít engedélyeket. Ezt az engedélyt egy érvényes felhasználói csoporthoz rendelheti hozzá.


## <a name="access-check-algorithm"></a>Hozzáférés-ellenőrzési algoritmus

A következő pseudocode a Data Lake Storage Gen1-fiókok hozzáférés-ellenőrzési algoritmusát jelöli.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or folder
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask IS NOT used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & entry.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permmissions & mask) == desired_perms)

  # Handle named groups and owning group
  member_count = 0
  perms = 0
  entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
      member_count += 1
      perms | =  entry.permissions
  if (member_count>0) :
    return ((desired_perms & perms & mask ) == desired_perms)
 
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>A maszk

Ahogy az a hozzáférés-ellenőrzési algoritmusban is látható, a maszk korlátozza a **megnevezett felhasználók**, a **tulajdonos csoport** és a **nevesített csoportok** hozzáférését.  

> [!NOTE]
> Új Data Lake Storage Gen1 fiók esetén a gyökérmappa ("/") hozzáférési ACL-jéhez tartozó maszk alapértelmezés szerint RWX.
>
>

### <a name="the-sticky-bit"></a>Ragadós bit

A ragadós (sticky) bit a POSIX-fájlrendszer egy speciális funkciója. Data Lake Storage Gen1 kontextusában nem valószínű, hogy a Sticky bit szükséges. Összefoglalva, ha a Sticky bit engedélyezve van egy mappában, a gyermek elem csak törölhető vagy átnevezhető az alárendelt elem tulajdonos felhasználója számára.

A ragadós bit nem látható az Azure Portalon.

## <a name="default-permissions-on-new-files-and-folders"></a>Új fájlok és mappák alapértelmezett engedélyei

Az új fájlok vagy mappák meglévő mappában történő létrehozásakor a szülőmappára vonatkozó alapértelmezett ACL a következőket határozza meg:

- Gyermekmappa alapértelmezett ACL-jei és hozzáférési ACL-jei.
- Gyermekfájl hozzáférési ACL-je (a fájloknak nincs alapértelmezett ACL-je).

### <a name="umask"></a>umask

Fájl vagy mappa létrehozásakor a rendszer a umask használatával módosítja az alapértelmezett ACL-ek beállítását az alárendelt elemen. a umask egy 9 bites érték a szülő mappákban, amelyek RWX értéket tartalmaznak a **tulajdonos felhasználó**, a **tulajdonos csoport** és a **többi** számára.

A Azure Data Lake Storage Gen1 umask egy állandó érték, amely a 007-re van állítva. Ez az érték a következőre van lefordítva

| umask-összetevő     | Numerikus alak | Rövid alak | Értelmezés |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | A tulajdonos felhasználó számára másolja a szülő alapértelmezett ACL-t a gyermek hozzáférési ACL-jéhez. | 
| umask.owning_group  |    0         |   `---`      | A tulajdonos csoport esetében másolja a szülő alapértelmezett ACL-t a gyermek hozzáférési ACL-jéhez. | 
| umask. other         |    7         |   `RWX`      | Egyéb esetben távolítsa el az összes engedélyt a gyermek hozzáférési ACL-jéhez |

Az Azure Data Lake Storage Gen1 által használt umask-érték azt jelenti, hogy a többi soha nem kerül be alapértelmezés szerint az új gyermekekre – függetlenül attól, hogy mit jelez az alapértelmezett ACL. 

Az alábbi pseudocode azt szemlélteti, hogyan történik a umask alkalmazása az ACL-ek egy alárendelt tételhez való létrehozásakor.

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>Gyakori kérdések a Data Lake Storage Gen1 ACL-ekkel kapcsolatban

### <a name="do-i-have-to-enable-support-for-acls"></a>Engedélyeznem kell az ACL támogatását?

Nem. Az ACL-eken keresztüli hozzáférés-vezérlés mindig Data Lake Storage Gen1 fiókhoz van kapcsolva.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Milyen engedélyek szükségesek egy mappa és tartalma rekurzív törléséhez?

* A szülőmappának rendelkeznie kell **Írás + Végrehajtás** engedélyekkel.
* A törölni kívánt mappához és a benne található összes mappához **Olvasás + Írás + Végrehajtás** engedélyre van szükség.

> [!NOTE]
> A mappákban található fájlok törléséhez nem szükséges írási engedély. Emellett a "/" gyökérmappa **soha nem** törölhető.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>Ki a fájl vagy mappa tulajdonosa?

A fájl vagy mappa létrehozója lesz a tulajdonos.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Melyik csoport lett a fájl vagy mappa tulajdonoscsoportjaként beállítva a létrehozáskor?

A tulajdonoscsoport annak a szülőmappának a tulajdonoscsoportjából lesz kimásolva, amelyben az új fájlt vagy mappát létrehozzák.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Én vagyok egy fájl tulajdonosa, de nem rendelkezem a szükséges RWX-engedéllyel. Mit tegyek?

A tulajdonos módosíthatja a fájlhoz tartozó engedélyeket, így bármilyen szükséges RWX-engedélyt megadhat saját magának.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Amikor megtekintem az ACL-eket az Azure Portalon, felhasználóneveket látok, az API-kon keresztül azonban GUID azonosítókat. Ez miért van?

Az ACL-ek bejegyzéseinek tárolása olyan GUID azonosítókként történik, amelyek az Azure AD-ban található felhasználóknak felelnek meg. Az API-k az aktuális állapotukban adják vissza a GUID azonosítókat. Az Azure Portal megpróbálja könnyebbé tenni az ACL-ek használatát a GUID azonosítóknak rövid nevekre történő lefordításával, amikor csak lehetséges.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Miért látok néha GUID azonosítókat az ACL-eken, amikor az Azure Portalt használom?

Ha a felhasználó már nem létezik az Azure AD-ben, egy GUID lesz látható. Ez általában akkor történik, ha a felhasználó elhagyta a vállalatot, vagy törölve lett a fiókja az Azure AD-ben. Győződjön meg arról is, hogy a megfelelő azonosítót használja az ACL-ek beállításához (az alábbi részleteket részletezve).

### <a name="when-using-service-principal-what-id-should-i-use-to-set-acls"></a>Az egyszerű szolgáltatásnév használata esetén milyen azonosítót kell használnia az ACL-ek beállításához?

Az Azure Portalon lépjen a **Azure Active Directory-> vállalati alkalmazások** elemre, és válassza ki az alkalmazást. Az **Áttekintés** lapon meg kell jelennie az objektum azonosítójának, és ezt kell használni, ha ACL-eket adnak hozzá az adateléréshez (és nem az alkalmazás-azonosítóhoz).

### <a name="does-data-lake-storage-gen1-support-inheritance-of-acls"></a>Támogatja Data Lake Storage Gen1 az ACL-ek öröklését?

Nem, az alapértelmezett ACL-ek azonban használhatók a szülő mappán belül újonnan létrehozott gyermekfájlok és mappák ACL-jeinek beállításához.

### <a name="what-are-the-limits-for-acl-entries-on-files-and-folders"></a>Mik a fájlok és mappák ACL-bejegyzéseinek korlátai?

32 ACL-ek állíthatók be fájlonként és könyvtárként. Hozzáférés és alapértelmezett ACL-ek mindegyike rendelkezik saját 32 ACL-bejegyzési korláttal. Ha lehetséges, használjon biztonsági csoportokat ACL-hozzárendelésekhez. A csoportok használatával kevésbé valószínű, hogy a fájlok vagy könyvtárak ACL-bejegyzéseinek maximális száma nagyobb.

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Hol tudhatok meg többet a POSIX hozzáférés-vezérlési modellről?

* [POSIX hozzáférés-vezérlési listák Linux rendszeren](https://www.linux.com/news/posix-acls-linux)
* [HDFS-engedély útmutatója](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX – gyakori kérdések](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL Ubuntu rendszeren](https://help.ubuntu.com/community/FilePermissionsACLs)
* [Hozzáférés-vezérlési listákat használó ACL Linux rendszeren](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Lásd még

* [A Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
