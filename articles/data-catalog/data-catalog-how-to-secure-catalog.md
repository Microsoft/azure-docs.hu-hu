---
title: A Azure Data Cataloghoz való hozzáférés biztonságossá tétele
description: Ez a cikk bemutatja, hogyan védheti meg a adatkatalógust és az adategységeit.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: c6c99eb62ba628ffc8c84799a6729540b572c580
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736387"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>A adatforrásokhoz és adateszközökhöz való hozzáférés biztonságossá tétele
> [!IMPORTANT]
> Ez a funkció csak a Azure Data Catalog standard kiadásában érhető el.

Azure Data Catalog lehetővé teszi annak megadását, hogy ki férhet hozzá az adatkatalógushoz, és hogy milyen műveleteket (regisztráció, jegyzet, tulajdonos) végezhet el a katalógus metaadatainak használatával. 

## <a name="catalog-users-and-permissions"></a>Katalógus felhasználói és engedélyei
Az adatkatalógushoz való hozzáférés biztosítása egy felhasználónak vagy csoportnak, illetve engedélyek beállítása:

1. Az [adatkatalógus kezdőlapján](https://www.azuredatacatalog.com)kattintson a **Beállítások** elemre az eszköztáron.

    ![adatkatalógus – beállítások](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)
2. A beállítások lapon bontsa ki a **katalógus felhasználói** szakaszát.
    ![Katalógus felhasználói – Hozzáadás](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)
3. Kattintson a **Hozzáadás**lehetőségre.
4. Adja meg a katalógushoz társított Azure Active Directory (HRE) **biztonsági csoportjának** teljes felhasználónevét vagy nevét. Használjon vesszőt (",") elválasztó, ha egynél több felhasználót vagy csoportot ad hozzá.
    ![Katalógus felhasználói – felhasználók vagy csoportok](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)
5. Nyomja le az **ENTER** vagy a **Tab** billentyűt a szövegmezőből. 
6.  Győződjön meg arról, hogy a felhasználókhoz vagy csoportokhoz tartozó összes engedély (**jegyzet**, **regisztráció**és **tulajdonos**) alapértelmezés szerint hozzá van rendelve ehhez a felhasználóhoz vagy csoporthoz. A felhasználó vagy csoport [regisztrálhatja]( data-catalog-how-to-register.md)az adategységeket, [megjegyzéseket fűzhet]( data-catalog-how-to-annotate.md)az adategységekhez, és az adategységek tulajdonjogát is elvégezheti. []( data-catalog-how-to-manage.md) 
    ![Katalógus felhasználói – alapértelmezett engedélyek](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)
7.  Ha csak olvasási hozzáférést szeretne adni egy felhasználónak vagy csoportnak a katalógushoz, törölje az adott felhasználóhoz vagy csoporthoz tartozó feliratozási beállítást. Ha így tesz, a felhasználó vagy csoport nem láthatja az adategységeket a katalógusban, de megtekintheti őket. 
8.  Ha meg szeretné tagadni egy felhasználó vagy csoport adategységek regisztrálását , törölje az adott felhasználó vagy csoport regisztrálási beállítását.
9.  Ahhoz, hogy egy felhasználó megtagadja egy adategység tulajdonjogának megtagadását, törölje az adott felhasználó vagy csoport **tulajdonjogának** átvétele beállítást. 
10. Ha törölni szeretne egy felhasználót vagy csoportot a katalógus felhasználói közül, kattintson a lista alján található felhasználó/csoport **x** elemére. 
    ![Katalógus felhasználói – felhasználó törlése](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

    > [!IMPORTANT]
    > Javasoljuk, hogy adjon hozzá biztonsági csoportokat a felhasználók katalógusához, ahelyett, hogy közvetlenül hozzáadja a felhasználókat, és nem rendel hozzá engedélyeket. Ezután adja hozzá a felhasználókat a szerepköreiknek megfelelő biztonsági csoportokhoz és a szükséges hozzáféréshez a katalógushoz.

## <a name="special-considerations"></a>Különleges szempontok

- A biztonsági csoportokhoz hozzárendelt engedélyek az adalékanyag. Tegyük fel, hogy a felhasználó két csoportban van. Az egyik csoport Megjegyzés-jogosultságokkal rendelkezik, és az egyéb csoportok nem rendelkeznek megjegyzési engedélyekkel. Ezután a felhasználó jegyzetekkel rendelkezik engedéllyel. 
- A felhasználóhoz explicit módon hozzárendelt engedélyek felülbírálják a felhasználóhoz tartozó csoportokhoz rendelt engedélyeket. Az előző példában tegyük fel, hogy explicit módon felvette a felhasználót a katalógusba a felhasználók számára, és nem rendel hozzá feliratozási engedélyeket. A felhasználó nem láthatja az adategységeket, annak ellenére, hogy a felhasználó egy olyan csoport tagja, amelynek van jegyzetekkel kapcsolatos engedélye.

## <a name="next-steps"></a>További lépések
- [Ismerkedés az Azure Data Catalog szolgáltatással](data-catalog-get-started.md)

