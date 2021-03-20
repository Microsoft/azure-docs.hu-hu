---
title: A Azure Lab Services-beli tantermi Tesztkörnyezet virtuális gépei jelszavának alaphelyzetbe állítása | Microsoft Docs
description: Megtudhatja, hogyan állíthatja alaphelyzetbe a virtuális gépek (VM-EK) jelszavát a Azure Lab Services Labs-ban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1b0b13862ca4620da15606138c0a80adeac8056a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96436810"
---
# <a name="set-or-reset-password-for-virtual-machines-in-labs-students"></a>Virtuális gépek jelszavának beállítása vagy alaphelyzetbe állítása a Labs-ben (diákok)
Ez a cikk bemutatja, hogyan állíthatja be vagy állíthatja alaphelyzetbe a virtuális gépek jelszavát. 

## <a name="enable-resetting-of-passwords"></a>Jelszavak alaphelyzetbe állításának engedélyezése
A tesztkörnyezet létrehozásakor a labor tulajdonosa engedélyezheti vagy letilthatja **ugyanazt a jelszót az összes virtuális géphez**. Ha ez a beállítás engedélyezve van, a tanulók nem állíthatják alaphelyzetbe a jelszót. A Labs összes virtuális gépe ugyanazzal a jelszóval fog rendelkezni, amelyet az oktató állít be. 

Ha ez a beállítás le van tiltva, akkor a felhasználóknak jelszót kell beállítaniuk, amikor első alkalommal próbálnak csatlakozni a virtuális géphez. A tanulók a jelen cikk utolsó szakaszában látható módon bármikor alaphelyzetbe állíthatják a jelszót. 

## <a name="reset-password-for-the-first-time"></a>Jelszó alaphelyzetbe állítása első alkalommal
Ha a **virtuális gépekhez ugyanazt a jelszót használja** , a felhasználók (tanulók) a **saját virtuális gépek** lapon a labor csempén a **Csatlakozás** gombra kattintanak, a felhasználó a következő párbeszédpanelt látja a virtuális gép jelszavának beállításához: 

![A diák jelszavának alaphelyzetbe állítása](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Jelszó alaphelyzetbe állítása később
A tanuló emellett beállíthatja a jelszót úgy, hogy a túlfolyó menüre (a **függőleges három pontra**) kattint a labor csempén, és kiválasztja a **jelszó alaphelyzetbe állítása** lehetőséget. 

![Jelszó alaphelyzetbe állítása később](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Következő lépések
A labor tulajdonosa által konfigurálható más tanulói használati lehetőségekről a következő cikkben talál további információt: [Student-használat konfigurálása](how-to-configure-student-usage.md).
