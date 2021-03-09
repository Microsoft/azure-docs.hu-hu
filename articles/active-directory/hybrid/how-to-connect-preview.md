---
title: 'Azure AD Connect: előzetes verzióban elérhető funkciók | Microsoft Docs'
description: Ez a témakör részletesen ismerteti a Azure AD Connect előzetes verziójában elérhető funkciókat.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d48cd8f95ecaf3e537a5221d766af150a51aa31
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517684"
---
# <a name="more-details-about-features-in-preview"></a>További információ az előzetes verzió szolgáltatásairól
Ez a témakör a jelenleg előzetes verzióban elérhető szolgáltatások használatát ismerteti.

## <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Azure AD Connect Sync v2 végpont API

Üzembe helyezettünk egy új végpontot (API-t) a Azure AD Connect számára, amely a szinkronizációs szolgáltatás működésének teljesítményét javítja Azure Active Directoryre. Az új v2-végpont kihasználása az Azure AD-ba való exportálás és importálás során észrevehető teljesítménybeli nyereséget tapasztal. Ez az új végpont a legfeljebb 250k taggal rendelkező csoportok szinkronizálását is támogatja. Ennek a végpontnak a használata lehetővé teszi Microsoft 365 egyesített csoportok visszaírását, maximális tagsági korlát nélkül, a helyszíni Active Directory, ha a csoport visszaírási engedélyezve van. További információ: [Azure ad Connect Sync v2 ENDPOINT API](how-to-connect-sync-endpoint-api-v2.md).

## <a name="user-writeback"></a>Felhasználói visszaírási
> [!IMPORTANT]
> A felhasználói visszaírási előzetes verziójának funkcióját a rendszer a 2015-es augusztusi frissítésből törölte Azure AD Connect. Ha engedélyezte, tiltsa le ezt a funkciót.
>
>

## <a name="next-steps"></a>Következő lépések
Folytassa [Azure ad Connect egyéni telepítését](how-to-connect-install-custom.md).

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
