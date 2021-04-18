---
title: Felhasználók és szerepkörök kezelése Azure IoT Central alkalmazások | Microsoft Docs
description: Rendszergazdaként a felhasználók és szerepkörök kezelése a Azure IoT Central alkalmazásban
author: vishwam
ms.author: vishwams
ms.date: 04/16/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 928a9fbad7bde7fe7f1cfaf181bff2a7b9ce458b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599060"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Felhasználók és szerepkörök kezelése az IoT Central-alkalmazásban

Ez a cikk azt ismerteti, hogy rendszergazdaként hogyan adhat hozzá, szerkeszthet és törölhet felhasználókat a Azure IoT Central alkalmazásban. A cikk azt is bemutatja, hogyan kezelheti az alkalmazás szerepköreit.

## <a name="add-users"></a>Felhasználók hozzáadása

Minden felhasználónak felhasználói fiókkal kell lennie, mielőtt bejelentkezhet és hozzáférhet egy alkalmazáshoz. IoT Central jelenleg támogatja a Microsoft-fiókokat és Azure Active Directory fiókokat, de a Azure Active Directory nem.

További információkért lásd a Microsoft-fiók [gyorsútmutató: Új](../../active-directory/fundamentals/add-users-azure-active-directory.md)felhasználók hozzáadása a Azure Active Directory. [](https://support.microsoft.com/products/microsoft-account?category=manage-account)

1. Ha hozzá szeretne adni egy felhasználót egy IoT Central alkalmazáshoz, az Adminisztráció **szakasz Felhasználók** **lapjára kell** majd átmenni.
    
    > [!div class="mx-imgBorder"]
    >![Felhasználók kezelése](media/howto-manage-users-roles/manage-users-pnp.png)

1. Felhasználó hozzáadásához a Felhasználók **lapon** válassza a **+ Felhasználó hozzáadása lehetőséget.**

1. Válasszon egy szerepkört a felhasználó számára a **Szerepkör** legördülő menüből. A szerepkörökről további információt a cikk [Szerepkörök](#manage-roles) kezelése szakaszában talál.

    > [!div class="mx-imgBorder"]
    >![Felhasználó hozzáadása és szerepkör kiválasztása](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Azok a felhasználók, akik olyan egyéni szerepkörben vannak, amely engedélyt ad nekik más felhasználók hozzáadására, csak a saját szerepkörükhöz azonos vagy kevesebb engedéllyel rendelkező szerepkörhöz adhatnak hozzá felhasználókat.
    > 
    > Ha egy felhasználót törölnek a Azure Active Directory, majd újra hozzáadják, nem fog tudni automatikusan bejelentkezni az IoT Central alkalmazásba. A hozzáférés újra engedélyezéséhez az alkalmazás rendszergazdájának törölnie kell, majd újra hozzá kell adni a felhasználót az alkalmazásban.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>A felhasználókhoz rendelt szerepkörök szerkesztése

A szerepköröket a hozzárendelésük után nem lehet módosítani. A felhasználóhoz rendelt szerepkört úgy módosíthatja, hogy törli a felhasználót, majd ismét hozzáadja egy másik szerepkörhöz.

> [!NOTE]
> A hozzárendelt szerepkörök a IoT Central alkalmazáshoz vannak rendelve, és nem kezelhetők az Azure Portalról.

## <a name="delete-users"></a>Felhasználók törlése

Felhasználók törléséhez jelöljön be egy vagy több jelölőnégyzetet a **Felhasználók** lapon. Ezután válassza a **Törlés** elemet.

## <a name="manage-roles"></a>Szerepkörök kezelése

A szerepkörökkel szabályozhatja, hogy a szervezeten belül ki számára engedélyezett a különböző feladatok elvégzése a IoT Central. Három beépített szerepkört rendelhet hozzá az alkalmazás felhasználóihoz. Ha [finomabb szabályozásra](#create-a-custom-role) van szüksége, egyéni szerepköröket is létrehozhat.

> [!div class="mx-imgBorder"]
> ![Szerepkörök kiválasztása](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Rendszergazda

A Rendszergazda **szerepkörben a** felhasználók kezelhetik és vezérelheti az alkalmazás minden részét, beleértve a számlázást is.

Az alkalmazást létrehozó felhasználó automatikusan hozzá lesz rendelve a **Rendszergazda szerepkörhöz.** A Rendszergazda szerepkörben mindig legalább egy **felhasználónak kell** lennie.

### <a name="builder"></a>Szerkesztő

A Szerkesztő **szerepkörben** a felhasználók az alkalmazás minden részét kezelhetik, de nem módosíthatnak az Adminisztráció vagy a Folyamatos adatexportáció lapokon.

### <a name="operator"></a>Operátor

Az Operátor **szerepkörben a** felhasználók figyelhetik az eszközök állapotát. Az eszközsablonok módosítása és az alkalmazás felügyelete nem engedélyezett. A kezelők eszközöket adhatnak hozzá és törölhet, eszközkészleteket kezelnek, valamint elemzéseket és feladatokat futtatnak. 

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Ha a megoldáshoz finomabb hozzáférés-vezérlésre van szükség, egyéni engedélykészletekkel is létrehozhat szerepköröket. Egyéni szerepkör létrehozásához lépjen  az alkalmazás  Felügyelet szakaszában található Szerepkörök lapra. Ezután válassza **az + Új szerepkör lehetőséget,** és adja meg a szerepkör nevét és leírását. Válassza ki a szerepkörhöz szükséges engedélyeket, majd válassza a **Mentés lehetőséget.**

A felhasználókat ugyanúgy használhatja az egyéni szerepkörhöz, mint a beépített szerepkörhöz.

> [!div class="mx-imgBorder"]
> ![Egyéni szerepkör létrehozása](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Egyéni szerepkör-beállítások

Amikor egyéni szerepkört határoz meg, kiválaszthatja azokat az engedélyeket, amelyekhez a felhasználó a szerepkör tagja lesz. Egyes engedélyek másoktól függenek. Ha például hozzáadja  az Alkalmazás irányítópultjának frissítése engedélyt egy szerepkörhöz, akkor az Alkalmazás-irányítópultok megtekintése engedélyre **is szüksége** lesz. Az alábbi táblázatok összefoglalják az egyéni szerepkörök létrehozásakor használható elérhető engedélyeket és azok függőségeit.

#### <a name="managing-devices"></a>Eszközök kezelése

**Eszközsablon engedélyei**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek     |
| Kezelés | Nézet <br/> Egyéb függőségek: Eszközpéldányok megtekintése  |
| Teljes hozzáférés | Megtekintés, kezelés <br/> Egyéb függőségek: Eszközpéldányok megtekintése |

**Eszközpéldány engedélyei**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek <br/> Egyéb függőségek: Eszközsablonok és eszközcsoportok megtekintése |
| Frissítés | Nézet <br/> Egyéb függőségek: Eszközsablonok és eszközcsoportok megtekintése  |
| Létrehozás | Nézet <br/> Egyéb függőségek: Eszközsablonok és eszközcsoportok megtekintése  |
| Törlés | Nézet <br/> Egyéb függőségek: Eszközsablonok és eszközcsoportok megtekintése  |
| Parancsok végrehajtása | Frissítés, Megtekintés <br/> Egyéb függőségek: Eszközsablonok és eszközcsoportok megtekintése  |
| Nyers adatok megtekintése | Nézet <br/> Egyéb függőségek: Eszközsablonok és eszközcsoportok megtekintése  |
| Teljes hozzáférés | View, Update, Create, Delete, Execute commands, View raw data <br/> Egyéb függőségek: Eszközsablonok és eszközcsoportok megtekintése  |

**Eszközcsoportok engedélyei**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek <br/> Egyéb függőségek: Eszközsablonok és eszközpéldányok megtekintése |
| Frissítés | Nézet <br/> Egyéb függőségek: Eszközsablonok és eszközpéldányok megtekintése   |
| Létrehozás | Megtekintés, frissítés <br/> Egyéb függőségek: Eszközsablonok és eszközpéldányok megtekintése   |
| Törlés | Nézet <br/> Egyéb függőségek: Eszközsablonok és eszközpéldányok megtekintése  |
| Teljes hozzáférés | Megtekintés, frissítés, létrehozás, törlés <br/> Egyéb függőségek: Eszközsablonok és eszközpéldányok megtekintése |

**Eszközkapcsolat-kezelési engedélyek**

| Name | Függőségek |
| ---- | -------- |
| Olvasási példány | Nincsenek <br/> Egyéb függőségek: Eszközsablonok, eszközcsoportok, eszközpéldányok megtekintése |
| Példány kezelése | Olvasási példány <br /> Egyéb függőségek: Eszközsablonok, eszközcsoportok, eszközpéldányok megtekintése |
| Globális olvasás | Nincsenek   |
| Globális kezelés | Globális olvasás |
| Teljes hozzáférés | Read instance, Manage instance, Read global, Manage global <br/> Egyéb függőségek: Eszközsablonok, eszközcsoportok, eszközpéldányok megtekintése |

**Feladatok engedélyei**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok és eszközcsoportok megtekintése |
| Frissítés | Nézet <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok és eszközcsoportok megtekintése |
| Létrehozás | Megtekintés, frissítés <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok és eszközcsoportok megtekintése |
| Törlés | Nézet <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok és eszközcsoportok megtekintése |
| Végrehajtás | Nézet <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok és eszközcsoportok megtekintése; Eszközpéldányok frissítése; Parancsok végrehajtása eszközpéldányon |
| Teljes hozzáférés | Megtekintés, frissítés, létrehozás, törlés, végrehajtás <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok és eszközcsoportok megtekintése; Eszközpéldányok frissítése; Parancsok végrehajtása eszközpéldányon |

**Szabályok engedélyei**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek <br/> Egyéb függőségek: Eszközsablonok megtekintése |
| Frissítés | Nézet <br/> Egyéb függőségek: Eszközsablonok megtekintése |
| Létrehozás | Megtekintés, frissítés <br/> Egyéb függőségek: Eszközsablonok megtekintése |
| Törlés | Nézet <br/> Egyéb függőségek: Eszközsablonok megtekintése |
| Teljes hozzáférés | Megtekintés, Frissítés, Létrehozás, Törlés <br/> Egyéb függőségek: Eszközsablonok megtekintése |

#### <a name="managing-the-app"></a>Az alkalmazás kezelése

**Alkalmazásbeállítások engedélyei**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek     |
| Frissítés | Nézet   |
| Másolás | Nézet <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok, eszközcsoportok, irányítópultok, adatexport, védjegyezés, súgóhivatkozások, egyéni szerepkörök, szabályok megtekintése |
| Törlés | Nézet   |
| Teljes hozzáférés | Megtekintés, Frissítés, Másolás, Törlés <br/> Egyéb függőségek: Eszközsablonok, eszközcsoportok, alkalmazás-irányítópultok, adatexportáció, védjegyezés, súgóhivatkozások, egyéni szerepkörök, szabályok megtekintése |

**Alkalmazássablon exportálási engedélyei**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek     |
| Exportálás | Nézet <br/> Egyéb függőségek: Eszközsablonok, eszközpéldányok, eszközcsoportok, irányítópultok, adatexport, védjegyezés, súgóhivatkozások, egyéni szerepkörök, szabályok megtekintése |
| Teljes hozzáférés | Megtekintés, Exportálás <br/> Egyéb függőségek: Eszközsablonok, eszközcsoportok, alkalmazás-irányítópultok, adatexportáció, védjegyezés, súgóhivatkozások, egyéni szerepkörök, szabályok megtekintése |

**Eszközfájl feltöltési engedélyei**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek     |
| Kezelés | Nézet   |
| Teljes hozzáférés | Megtekintés, kezelés |

**Számlázási engedélyek**

| Name | Függőségek |
| ---- | -------- |
| Kezelés | Nincsenek     |
| Teljes hozzáférés | Kezelés |

#### <a name="managing-users-and-roles"></a>Felhasználók és szerepkörök kezelése

**Egyéni szerepkörök engedélyei**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek |
| Frissítés | Nézet |
| Létrehozás | Megtekintés, frissítés |
| Törlés | Nézet |
| Teljes hozzáférés | Megtekintés, frissítés, létrehozás, törlés |

**Felhasználókezelési engedélyek**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek <br/> Egyéb függőségek: Egyéni szerepkörök megtekintése |
| Hozzáadás | Nézet <br/> Egyéb függőségek: Egyéni szerepkörök megtekintése |
| Törlés | Nézet <br/> Egyéb függőségek: Egyéni szerepkörök megtekintése |
| Teljes hozzáférés | Megtekintés, Hozzáadás, Törlés <br/> Egyéb függőségek: Egyéni szerepkörök megtekintése |

> [!NOTE]
> Azok a felhasználók, akik olyan egyéni szerepkörben vannak, amely engedélyt ad nekik más felhasználók hozzáadására, csak olyan szerepkörhöz adhatnak hozzá felhasználókat, amelyek a saját szerepkörüknél azonos vagy kevesebb engedéllyel rendelkezik.

#### <a name="customizing-the-app"></a>Az alkalmazás testreszabása

**Alkalmazás-irányítópult engedélyei**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek     |
| Frissítés | Nézet   |
| Létrehozás | Megtekintés, frissítés |
| Törlés | Nézet   |
| Teljes hozzáférés | Megtekintés, Frissítés, Létrehozás, Törlés |

**Személyes irányítópultok engedélyei**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek     |
| Frissítés | Nézet   |
| Létrehozás | Megtekintés, frissítés   |
| Törlés | Nézet   |
| Teljes hozzáférés | Megtekintés, Frissítés, Létrehozás, Törlés |

**Márkajelzési, favicon- és színengedélyek**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek     |
| Frissítés | Nézet   |
| Teljes hozzáférés | Megtekintés, frissítés |

**Súgóhivatkozások engedélyei**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek     |
| Frissítés | Nézet   |
| Teljes hozzáférés | Megtekintés, frissítés |

#### <a name="extending-the-app"></a>Az alkalmazás kiterjesztése

**Adatexport-engedélyek**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek     |
| Frissítés | Nézet   |
| Létrehozás | Megtekintés, frissítés  |
| Törlés | Nézet   |
| Teljes hozzáférés | Megtekintés, Frissítés, Létrehozás, Törlés |

**API-jogkivonat engedélyei**

| Name | Függőségek |
| ---- | -------- |
| Nézet | Nincsenek  <br/> Egyéb függőségek: Egyéni szerepkörök megtekintése |
| Létrehozás | Nézet <br/> Egyéb függőségek: Egyéni szerepkörök megtekintése |
| Törlés | Nézet <br/> Egyéb függőségek: Egyéni szerepkörök megtekintése |
| Teljes hozzáférés | Megtekintés, Létrehozás, Törlés <br/> Egyéb függőségek: Egyéni szerepkörök megtekintése |

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan kezelheti a felhasználókat és a szerepköröket a IoT Central-alkalmazásban, a következő javasolt lépés a számla [kezelése.](howto-view-bill.md)