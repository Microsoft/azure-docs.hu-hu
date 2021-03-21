---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9e4d60e501ffc5b61c87a80b8dd13698cca28737
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934534"
---
A Key Vault legfeljebb 1024 hozzáférési szabályzatot támogat, és mindegyik bejegyzés külön engedélyeket ad meg egy adott rendszerbiztonsági tag számára. Ennek a korlátozásnak a miatt javasoljuk, hogy a hozzáférési szabályzatokat felhasználók csoportjaihoz, ahol lehetséges, az egyes felhasználók helyett. A csoportok használata sokkal egyszerűbbé teszi a szervezeten belüli több személy engedélyeinek kezelését. További információ: [alkalmazás-és erőforrás-hozzáférés kezelése Azure Active Directory csoportok használatával](../articles/active-directory/fundamentals/active-directory-manage-groups.md)

A Key Vault hozzáférés-vezérléssel kapcsolatos részletes információkért lásd [: Azure Key Vault biztonság: identitás-és hozzáférés-kezelés](../articles/key-vault/general/security-overview.md#identity-management).