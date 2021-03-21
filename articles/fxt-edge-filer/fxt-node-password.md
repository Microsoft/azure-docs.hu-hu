---
title: 'Oktatóanyag: hardver inicializálása – Azure FXT Edge Filer'
description: Ismerje meg, hogyan csatlakozhat a hardveres csomóponthoz, és hogyan állítható be a kezdeti jelszó az Azure FXT Edge Filer-csomópontokon.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 79d908077d3bd66a3ed86759e37574383358881b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218850"
---
# <a name="tutorial-set-hardware-passwords"></a>Oktatóanyag: hardveres jelszavak beállítása

Amikor első alkalommal kapcsol be egy Azure FXT Edge Filer-csomópontot, be kell állítania egy rendszergazdai jelszót. A hardveres csomópontok nem rendelkeznek alapértelmezett jelszóval.

A hálózati portok mindaddig le vannak tiltva, amíg a jelszót be nem állította, és a root felhasználó bejelentkezik.

Ezt a lépést a csomópont telepítése és kábelezése után végezze el, de a fürt létrehozásának megkísérlése előtt.

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a hardver-csomóponthoz, és hogyan állíthatja be a jelszót. Azt is leírja, hogyan adhat hozzá BIOS-telepítési jelszót a csomópont biztonságossá tételéhez.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
>
> * Billentyűzet és figyelő összekapcsolása a csomóponttal és bekapcsolás
> * BIOS-telepítési jelszó beállítása
> * A iDRAC-port és a gyökérszintű felhasználó jelszavának beállítása ezen a csomóponton
> * Bejelentkezés root-ként

Ismételje meg ezeket a lépéseket minden olyan csomópont esetében, amelyet a fürtben használni fog.

Az oktatóanyag elvégzése körülbelül 15 percet vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt végezze el a következő lépéseket:

* [Telepítse](fxt-install.md) az összes Azure FXT Edge Filer-csomópontot egy berendezési állványba, és csatlakoztassa az áramellátási kábeleket és a hálózati hozzáférést a [korábbi oktatóanyagban](fxt-network-power.md)leírtak szerint.
* Keressen egy USB-kapcsolattal rendelkező billentyűzetet és egy VGA-csatlakoztatott figyelőt, amelyet csatlakoztathat a hardveres csomópontokhoz. (A csomópont soros portja inaktív a jelszó beállítása előtt.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Billentyűzet és figyelő összekötése a csomóponttal

A monitor és a billentyűzet fizikailag csatlakoztatható az Azure FXT Edge Filer-csomóponthoz.

* Kapcsolja össze a figyelőt a VGA-porttal.
* Kapcsolja össze a billentyűzetet az egyik USB-porttal.

Ez a hivatkozási diagram a ház hátoldalán található portok megkeresésére használható.

> [!NOTE]
> A soros port csak a jelszó beállítása után inaktív.

![Az Azure FXT Edge Filer hátoldalának ábrája, amely soros, VGA és USB portokkal van ellátva](media/fxt-back-serial-vga-usb.png)

Ha több csomópontot szeretne ugyanahhoz a perifériához csatlakoztatni, a KVM-kapcsolót is használhatja.

Kapcsolja be a csomópontot a főkapcsoló gomb megnyomásával.

![Az Azure FXT Edge Filer-kör előtti Power gombjának ábrája a jobb felső sarokban látható.](media/fxt-front-annotated.png)

## <a name="create-a-bios-setup-password"></a>BIOS-telepítési jelszó létrehozása

A BIOS telepítési jelszava megvédi a csomópont BIOS-beállításait a véletlen vagy jogosulatlan módosításoktól. Ez a jelszó nem szükséges a fürt létrehozásához, de erősen ajánlott a fürt biztonsági stratégiájának részeként.

BIOS-telepítési jelszó létrehozása:

1. Kapcsolja be vagy indítsa újra a csomópontot, és azonnal nyomja le az F2 billentyűt a rendszerbeállítási segédprogram megnyitásához.

1. A **rendszertelepítő főmenüje** képernyőn válassza a **rendszer BIOS**-  >  **rendszerbiztonság** lehetőséget.

1. Győződjön meg arról, hogy a **jelszó állapota** beállítás fel van **oldva**.

1. A jelszó beállításához használja a **telepítési jelszó** mezőt. (A képernyőről a rendszer BIOS-jelszavát is beállíthatja, ha egyet szeretne használni.)

1. Az ESC billentyű lenyomásával térjen vissza a **rendszer BIOS** képernyőjére, majd nyomja le ismét az ESC billentyűt. Egy üzenet arra kéri, hogy mentse a módosításokat. Ha a rendszer nem indul el automatikusan, indítsa újra a rendszert, hogy beolvassa a normál indítási képernyőt.<!-- how to exit this mode/do you need to reboot to get to the initial setup screen? -->

## <a name="set-initial-passwords"></a>Kezdeti jelszavak beállítása

Az Azure FXT Edge Filer-csomópontja a rendszerindításkor különböző üzeneteket fog kinyomtatni a figyelőbe. Néhány pillanat múlva a következőhöz hasonló kezdeti beállítási képernyő jelenik meg:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set the iDRAC and temporary root password.
Minimum password length is 8.

NOTE: This does not set a BIOS setup password. For security,
Microsoft recommends using a BIOS setup password, restricting
physical access to the node, and other measures. Learn more at
https://aka.ms/fxt-security.

Enter new password:

```

A beírt jelszó két dolgot használ:

* Ez az Azure FXT Edge Filer-csomópontjának ideiglenes legfelső szintű jelszava.

  Ez a jelszó akkor változik, amikor létrehoz egy fürtöt a csomópont használatával, vagy ha hozzáadja ezt a csomópontot a fürthöz. A fürt felügyeleti jelszava (a felhasználóhoz társítva ``admin`` ) a fürt összes csomópontjának legfelső szintű jelszava is.

* Ez a iDRAC/IPMI hardveres felügyeleti port hosszú távú jelszava.

  Ügyeljen arra, hogy jegyezze fel a jelszót arra az esetre, ha később be kell jelentkeznie a IPMI a hardveres probléma elhárításához.

Adja meg és erősítse meg a jelszót:

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

A jelszó megadása után a rendszer folytatja a rendszerindítást. Amikor befejeződik, a rendszer egy ``login:`` kérést ad.

## <a name="sign-in-as-root"></a>Bejelentkezés root-ként

Jelentkezzen be ``root`` az imént beállított jelszóval.

```
login: root
Password:**********
```

Miután bejelentkezett a gyökérként, a hálózati portok aktívak, és az IP-címekhez felveszik a kapcsolatot a DHCP-kiszolgálóval.

## <a name="next-steps"></a>Következő lépések

A csomópont készen áll a fürt egy részének beosztására. Ezzel létrehozhatja az Azure FXT Edge Filer-fürtöt, vagy [hozzáadhatja egy meglévő fürthöz](fxt-add-nodes.md).

> [!div class="nextstepaction"]
> [Fürt létrehozása](fxt-cluster-create.md)
