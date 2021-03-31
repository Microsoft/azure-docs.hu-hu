---
title: 'Azure AD Connect: eszközbeállítások | Microsoft Docs'
description: Ez a dokumentum részletesen ismerteti a Azure AD Connectben elérhető eszközök beállításait
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90d8cc39174f6392b0c532948305fa713535f0c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279227"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: eszközbeállítások

Az alábbi dokumentáció a Azure AD Connectban elérhető különböző eszközökre vonatkozó információkat tartalmazza. A Azure AD Connect a következő két művelet konfigurálására használható: 
* **Hibrid Azure ad-csatlakozás**: Ha a környezet helyszíni ad-lábnyomot használ, és az Azure ad előnyeit szeretné használni, hibrid Azure ad-hez csatlakoztatott eszközöket is alkalmazhat. Ezek az eszközök a helyszíni Active Directoryhoz és a Azure Active Directoryhoz is csatlakoznak.
* **Eszköz visszaírási**: az eszköz visszaírási lehetővé teszi a feltételes hozzáférés engedélyezését az eszközökön alapuló AD FS (2012 R2 vagy újabb) védett eszközökre

## <a name="configure-device-options-in-azure-ad-connect"></a>Eszközbeállítások konfigurálása a Azure AD Connectban

1.  Azure AD Connect futtatása. A **További feladatok** lapon válassza az **eszközbeállítások konfigurálása** lehetőséget.  Kattintson a **Tovább** gombra.
    ![Eszközbeállítások konfigurálása](./media/how-to-connect-device-options/deviceoptions.png) 

    Az **Áttekintés** oldalon megjelennek a részletek.
    ![Áttekintés](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > Az eszköz új konfigurálása lehetőség csak a 1.1.819.0 és újabb verziókban érhető el.

2.  Az Azure AD hitelesítő adatainak megadása után kiválaszthatja az eszköz beállításai lapon végrehajtandó műveletet.
    ![Eszköz műveletei](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Következő lépések

* [Hibrid Azure AD-csatlakozás konfigurálása](../devices/hybrid-azuread-join-plan.md)
* [Eszköz visszaírási konfigurálása/letiltása](how-to-connect-device-writeback.md)