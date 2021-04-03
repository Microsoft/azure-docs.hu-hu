---
title: Azure AD-szerepkörök hozzáférési felülvizsgálatának befejezése a PIM-ben – Azure AD | Microsoft Docs
description: Ismerje meg, hogyan végezheti el a Azure AD Privileged Identity Management (PIM) Azure AD szerepköreinek hozzáférési felülvizsgálatát, és megtekintheti az eredményeket
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa7e8089fbbf2ee653100a05383fdbdc877ffda4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84742216"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure AD-szerepkörök hozzáférési felülvizsgálatának befejezése

A Kiemelt szerepkörű rendszergazdák a [hozzáférési felülvizsgálat elindítása](pim-how-to-start-security-review.md)után ellenőrizhetik a Kiemelt jogosultságú hozzáférést.  Privileged Identity Management (PIM) automatikusan elküld egy e-mailt az Azure Active Directory (Azure AD-) szervezet felhasználói számára a hozzáférésük áttekintéséhez. Ha a felhasználó nem kapott e-mailt, elküldheti a [hozzáférési felülvizsgálat végrehajtásának](pim-how-to-perform-security-review.md)utasításait.

Miután a hozzáférési felülvizsgálati időszak véget ért, vagy az összes felhasználó befejezte az önellenőrzést, kövesse a jelen cikkben ismertetett lépéseket a felülvizsgálat kezeléséhez és az eredmények megtekintéséhez.

## <a name="manage-access-reviews"></a>Hozzáférési felülvizsgálatok kezelése

1. Nyissa meg a [Azure Portal](https://portal.azure.com/) , és válassza ki a **Azure ad Privileged Identity Management** szolgáltatást az irányítópulton.
1. Válassza ki az irányítópult **hozzáférési felülvizsgálatok** szakaszát.
1. Válassza ki a felügyelni kívánt hozzáférési felülvizsgálatot.

A hozzáférési felülvizsgálat részletei panelen számos lehetőség áll rendelkezésre a felülvizsgálat kezelésére.

![Privileged Identity Management hozzáférés-felülvizsgálati gombok – képernyőfelvétel](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Emlékeztesse

Ha a hozzáférési felülvizsgálat úgy van beállítva, hogy a felhasználók saját maguk is felülvizsgálják magukat, az **emlékeztető** gomb értesítést küld.

### <a name="stop"></a>Leállítás

Az összes hozzáférési felülvizsgálat befejező dátummal rendelkezik, de a **Leállítás** gomb használatával is befejezheti korán. Ha a felhasználó nem tekinti át ezt az időt, nem fogja tudni leállítani a felülvizsgálatot. Az ellenőrzés leállítása után nem indítható újra.

### <a name="apply"></a>Alkalmaz

A hozzáférési felülvizsgálat befejezése után vagy azért, mert elérte a befejezési dátumot, vagy manuálisan leállította, az **Apply (alkalmaz** ) gomb végrehajtja a felülvizsgálat eredményét. Ha egy felhasználó hozzáférése meg lett tagadva a felülvizsgálat során, akkor ez az a lépés, amely eltávolítja a szerepkör-hozzárendelését.  

### <a name="export"></a>Exportálás

Ha a hozzáférési felülvizsgálat eredményeit manuálisan szeretné alkalmazni, exportálhatja a felülvizsgálatot. Az **Exportálás** gomb megkezdi a CSV-fájl letöltését. Az eredményeket az Excelben vagy más, CSV-fájlokat megnyitó programok segítségével kezelheti.

### <a name="delete"></a>Törlés

Ha még nem érdekli a felülvizsgálat, törölje azt. A **Törlés** gomb eltávolítja a felülvizsgálatot a Privileged Identity Management szolgáltatásból.

> [!IMPORTANT]
> Ezt a pusztító változást nem szükséges megerősíteni, ezért győződjön meg arról, hogy törölni kívánja ezt a felülvizsgálatot.

## <a name="next-steps"></a>Következő lépések

- [Azure AD-szerepkörök hozzáférési felülvizsgálatának elindítása Privileged Identity Management](pim-how-to-start-security-review.md)
- [Azure AD-szerepkörök hozzáférési felülvizsgálatának elvégzése Privileged Identity Management](pim-how-to-perform-security-review.md)
