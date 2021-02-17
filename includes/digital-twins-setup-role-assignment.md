---
author: baanders
description: fájl belefoglalása a hozzáférési engedélyek lépéshez az Azure digitális Twins beállítása beállításban
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: a905bb3b4effb0381facfbfaa37c8ea412b81287
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551801"
---
Az Azure Digital Twins [Azure Active Directory (Azure ad)](../articles/active-directory/fundamentals/active-directory-whatis.md) használatával szerepköralapú hozzáférés-vezérlést (RBAC) használ. Ez azt jelenti, hogy ahhoz, hogy egy felhasználó adatsíkon hívásokat hajtson végre az Azure-beli digitális Twins-példányon, a felhasználónak hozzá kell rendelnie egy, a megfelelő engedélyekkel rendelkező szerepkört.

Az Azure Digital Twins esetében ez a szerepkör az _**Azure digitális Twins-adattulajdonos**_. További információ a szerepkörökről és a biztonságról [*: az Azure Digital Twins-megoldások biztonsága*](../articles/digital-twins/concepts-security.md).

Ebből a szakaszból megtudhatja, hogyan hozhat létre szerepkör-hozzárendelést egy felhasználó számára az Azure Digital Twins-példányban, az adott felhasználó e-mail-címét használva az Azure AD-bérlőn az Azure-előfizetésében. A szervezete szerepköreitől függően saját maga is beállíthatja ezt az engedélyt, vagy beállíthat egy olyan személy nevében, aki az Azure Digital Twins-példányt fogja kezelni.

### <a name="assign-the-role"></a>A szerepkör kiosztása

Ahhoz, hogy a felhasználó engedélyeket nyújtson egy Azure digitális Twins-példány kezeléséhez, hozzá kell rendelnie őket az _**Azure Digital Twins adattulajdonosi**_ szerepkörhöz a példányon belül.

> [!NOTE]
> Vegye figyelembe, hogy ez a szerepkör eltér az Azure AD *tulajdonosi* szerepkörtől, amely az Azure Digital Twins-példány hatókörében is kiosztható. Ez két különböző felügyeleti szerepkör, és az Azure AD- *tulajdonos* nem biztosít hozzáférést az *Azure Digital Twins-adattulajdonossal* biztosított adatközpont-funkciókhoz.