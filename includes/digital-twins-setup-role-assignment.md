---
author: baanders
description: fájl belefoglalása a hozzáférési engedélyek lépéshez az Azure digitális Twins beállítása beállításban
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: a905bb3b4effb0381facfbfaa37c8ea412b81287
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100551801"
---
Az Azure Digital Twins [Azure Active Directory (Azure ad)](../articles/active-directory/fundamentals/active-directory-whatis.md) használatával szerepköralapú hozzáférés-vezérlést (RBAC) használ. Ez azt jelenti, hogy ahhoz, hogy egy felhasználó adatsíkon hívásokat hajtson végre az Azure-beli digitális Twins-példányon, a felhasználónak hozzá kell rendelnie egy, a megfelelő engedélyekkel rendelkező szerepkört.

Az Azure Digital Twins esetében ez a szerepkör az _**Azure digitális Twins-adattulajdonos**_. További információ a szerepkörökről és a biztonságról [*: az Azure Digital Twins-megoldások biztonsága*](../articles/digital-twins/concepts-security.md).

Ebből a szakaszból megtudhatja, hogyan hozhat létre szerepkör-hozzárendelést egy felhasználó számára az Azure Digital Twins-példányban, az adott felhasználó e-mail-címét használva az Azure AD-bérlőn az Azure-előfizetésében. A szervezete szerepköreitől függően saját maga is beállíthatja ezt az engedélyt, vagy beállíthat egy olyan személy nevében, aki az Azure Digital Twins-példányt fogja kezelni.

### <a name="assign-the-role"></a>A szerepkör kiosztása

Ahhoz, hogy a felhasználó engedélyeket nyújtson egy Azure digitális Twins-példány kezeléséhez, hozzá kell rendelnie őket az _**Azure Digital Twins adattulajdonosi**_ szerepkörhöz a példányon belül.

> [!NOTE]
> Vegye figyelembe, hogy ez a szerepkör eltér az Azure AD *tulajdonosi* szerepkörtől, amely az Azure Digital Twins-példány hatókörében is kiosztható. Ez két különböző felügyeleti szerepkör, és az Azure AD- *tulajdonos* nem biztosít hozzáférést az *Azure Digital Twins-adattulajdonossal* biztosított adatközpont-funkciókhoz.