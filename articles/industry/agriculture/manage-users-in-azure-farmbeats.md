---
title: Felhasználók kezelése az Azure FarmBeats
description: Ez a cikk bemutatja, hogyan kezelheti a felhasználókat az Azure FarmBeats-ben.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-ummehabiba
ms.openlocfilehash: de9a11cac9856417422bd72579cbca44233ab422
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179901"
---
# <a name="manage-users"></a>Felhasználók kezelése

Az Azure FarmBeats felhasználói felügyeletet tartalmaz a Azure Active Directory (Azure AD) példányának részét képező felhasználók számára. Hozzáadhat felhasználókat az Azure FarmBeats-példányhoz az API-k eléréséhez, megtekintheti a generált térképeket, és elérheti az érzékelő telemetria a farmról.

## <a name="prerequisites"></a>Előfeltételek

- Az Azure FarmBeats telepítése szükséges. További információ: az [Azure FarmBeats telepítése](install-azure-farmbeats.md).
- Azon felhasználók e-mail-azonosítói, amelyeket hozzá szeretne adni, vagy amelyekről el szeretné távolítani az Azure FarmBeats-példányát.

## <a name="manage-azure-farmbeats-users"></a>Azure FarmBeats-felhasználók kezelése

Az Azure FarmBeats az Azure AD-t használja a hitelesítéshez, a hozzáférés-vezérléshez és a szerepkörökhöz. Az Azure AD-bérlőben felhasználókat adhat hozzá az Azure FarmBeats.

> [!NOTE]
> Ha a felhasználó nem Azure AD-bérlői felhasználó, kövesse az **Azure ad-felhasználók hozzáadása** szakasz utasításait a telepítés befejezéséhez.

Az Azure FarmBeats két típusú felhasználói szerepkört támogat:

 - **Rendszergazda**: teljes hozzáférés az Azure FarmBeats Datahub API-khoz. Az ebben a szerepkörben lévő felhasználók lekérhetik az összes Azure FarmBeats Datahub objektumot, és az FarmBeats-gyorsító összes műveletét elvégezheti.
 - **Írásvédett**: csak olvasási hozzáférés a FarmBeats Datahub API-khoz. A felhasználók megtekinthetik a Datahub API-kat, a gyorsító irányítópultokat és a térképeket. A csak olvasási hozzáféréssel rendelkező felhasználók nem hajthatnak végre olyan műveleteket, mint például a térképek létrehozása, az eszközök társítása vagy a farmok létrehozása.

## <a name="add-users-to-azure-farmbeats"></a>Felhasználók hozzáadása az Azure FarmBeats

Felhasználók hozzáadása az Azure FarmBeats:

1. Jelentkezzen be a Gyorssegédbe, majd kattintson a **Beállítások** ikonra.
2. Válassza a **Access Control** lehetőséget.

    ![A farmok beállításai panel](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Adja meg annak a felhasználónak az e-mail-AZONOSÍTÓját, amelyhez hozzáférést szeretne biztosítani.
4. Válassza ki a kívánt szerepkört, **rendszergazda** vagy **csak olvasható** lehetőséget.
5. Válassza a **szerepkör hozzáadása** elemet.

A hozzáadott felhasználó mostantól elérheti az Azure FarmBeats (a Datahub és a gyorsító is).

## <a name="delete-users-from-azure-farmbeats"></a>Felhasználók törlése az Azure FarmBeats

Felhasználók eltávolítása az Azure FarmBeats rendszerből:

1. Jelentkezzen be a Gyorssegédbe, majd kattintson a **Beállítások** ikonra.
2. Válassza a **Access Control** lehetőséget.
3. Válassza a **Törlés** elemet.

   A felhasználó törlődik a rendszerből. A következő megerősítő üzenetet kapja:

   ![Azure FarmBeats-megerősítő üzenet](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Azure AD-felhasználók hozzáadása

> [!NOTE]
> Az Azure FarmBeats-felhasználóknak az Azure AD-bérlőben kell léteznie ahhoz, hogy alkalmazásokat és szerepköröket rendeljenek hozzájuk. Ha a felhasználó nem létezik az Azure AD-bérlőben, kövesse az ebben a szakaszban található utasításokat. Ha a felhasználó már létezik az Azure AD-bérlőben, hagyja ki az utasításokat.

A következő lépésekkel adhat hozzá felhasználókat az Azure AD-hoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A jobb felső sarokban válassza ki a fiókját, majd váltson arra az Azure AD-bérlőre, amely a FarmBeats-hez van társítva.
3. Válassza ki **Azure Active Directory**  >  **felhasználókat**.

    Megjelenik az Azure AD-felhasználók listája.

4. Ha felhasználót szeretne hozzáadni a címtárhoz, válassza az **új felhasználó** lehetőséget. Külső felhasználó hozzáadásához válassza az **új vendég felhasználó** lehetőséget.

    ![A "minden felhasználó" panel](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Válassza ki az új felhasználó nevét, majd hajtsa végre a szükséges mezőket az adott felhasználó számára.
6. Válassza a **Létrehozás** lehetőséget.

Az Azure AD-felhasználók felügyeletével kapcsolatos információkért lásd: [felhasználók hozzáadása vagy törlése az Azure ad-ben](../../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="next-steps"></a>Következő lépések

Sikeresen hozzáadta a felhasználókat az Azure FarmBeats-példányhoz. Most Ismerkedjen meg a [farmok létrehozásával és kezelésével](manage-farms-in-azure-farmbeats.md#create-farms).