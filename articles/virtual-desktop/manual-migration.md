---
title: Migrálás manuálisan a Windows virtuális asztalról (klasszikus) – Azure
description: A Windows virtuális asztalról (klasszikus) a Windows rendszerű virtuális asztalra való manuális Migrálás.
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd292fa6b4f613e0a5f5a80e0cd87675f529baf5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516171"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>Migrálás manuálisan a Windows virtuális asztalról (klasszikus)

A Windows virtuális asztal (klasszikus) PowerShell-parancsmagokkal, REST API-kkal és szolgáltatási objektumokkal hozza létre a szolgáltatási környezetét. A Windows rendszerű virtuális asztali szolgáltatások környezetében a "Object" egy olyan dolog, amelyet a Windows rendszerű virtuális asztal hoz létre. A szolgáltatási objektumok közé tartoznak a bérlők, a gazdagépek, az alkalmazások csoportjai és a munkamenet-gazdagépek.

A Windows rendszerű virtuális asztal (klasszikus) azonban nincs integrálva az Azure-ba. Az Azure-integráció nélkül a létrehozott objektumokat nem kezeli automatikusan a Azure Portal, mert nem kapcsolódnak az Azure-előfizetéshez.

A Windows rendszerű virtuális asztali szolgáltatások legújabb jelentős frissítése a teljes Azure-integráció irányába mutató váltást jelöl a szolgáltatásban. A Windows rendszerű virtuális asztalon létrehozott objektumokat a Azure Portal automatikusan kezeli.

Ebből a cikkből megtudhatja, miért érdemes megfontolni a Windows rendszerű virtuális asztal legújabb verziójának áttelepítését. Ezt követően megtudhatja, hogyan lehet manuálisan áttelepíteni a Windows rendszerű virtuális asztalról (klasszikus) a Windows virtuális asztal legújabb frissítésére.

## <a name="why-migrate"></a>Miért érdemes az áttelepítés?

A főbb frissítések nem megfelelőek, különösen azokat manuálisan kell elvégeznie. Van azonban néhány ok, amiért nem lehet automatikusan áttelepíteni:

- A klasszikus kiadással létrehozott meglévő szolgáltatási objektumok nem rendelkeznek képviselettel az Azure-ban. A hatókörük nem terjed ki a Windows rendszerű virtuális asztali szolgáltatáson kívülre.
- A legújabb frissítéssel a szolgáltatás alkalmazás-azonosítója úgy lett módosítva, hogy eltávolítsa az alkalmazások beleegyezikét a Windows Virtual Desktop (klasszikus) eszközön. Nem hozhat létre új Azure-objektumokat a Windows virtuális asztallal, hacsak nem hitelesítik őket az új alkalmazás-AZONOSÍTÓval.

A probléma ellenére továbbra is fontos az áttelepítés a klasszikus verzióról. A következő műveleteket végezheti el az áttelepítés után:

- A Windows virtuális asztal kezelése a Azure Portal használatával.
- Azure Active Directory (AD) felhasználói csoportok társítása az alkalmazási csoportokhoz.
- A továbbfejlesztett Log Analytics funkció használatával elháríthatja az üzemelő példányt.
- A rendszergazdai hozzáférés kezeléséhez használja az Azure-natív szerepköralapú hozzáférés-vezérlést (Azure RBAC).

## <a name="when-should-i-migrate"></a>Mikor érdemes migrálni?

Ha az áttelepítést követően megkérdezi magát, az üzemelő példány aktuális és jövőbeli helyzetét is figyelembe kell vennie.

Van néhány forgatókönyv, különösen, ha azt javasoljuk, hogy manuálisan telepítse a következőket:

- Egy tesztelési gazdagép készletének beállítása kis számú felhasználóval rendelkezik.
- Van egy kis mennyiségű felhasználóval rendelkező gazdagép-készlet beállítása, de végül akár több száz felhasználó számára is megtervezhető.
- Van egy egyszerű beállítása, amely könnyen replikálható. Ha például a virtuális gépek katalógus-rendszerképet használnak.

> [!IMPORTANT]
> Ha olyan speciális konfigurációt használ, amely hosszú időt vett igénybe, vagy sok felhasználóval rendelkezik, a rendszer nem javasolja manuálisan az áttelepítést.

## <a name="prepare-for-migration"></a>Előkészületek a migráláshoz

Mielőtt elkezdené, meg kell győződnie arról, hogy a környezet készen áll az áttelepítésre.

Az áttelepítési folyamat elindításához a következők szükségesek:

- Azure-előfizetés, amely új Azure-szolgáltatási objektumokat hoz létre.
- Győződjön meg arról, hogy a következő szerepkörökhöz van rendelve:
    
    - Közreműködő
    - Felhasználói hozzáférés rendszergazdája
    
    A közreműködő szerepkör lehetővé teszi, hogy Azure-objektumokat hozzon létre az előfizetésében, és a felhasználói hozzáférés rendszergazdai szerepkör lehetővé teszi a felhasználók alkalmazás-csoportokhoz rendelését.

## <a name="how-to-migrate-manually"></a>Manuális Migrálás

Most, hogy előkészítette az áttelepítési folyamatot, itt az ideje, hogy ténylegesen áttelepítse.

A Windows rendszerű virtuális asztali környezetből (klasszikus) a Windows virtuális asztalra való áttelepítéshez:

1. Az összes magas szintű objektum a Azure Portal való létrehozásához kövesse a következő témakör utasításait: [Host Pool létrehozása a Azure Portal](create-host-pools-azure-marketplace.md) használatával.
2. Ha át szeretné adni a már használt virtuális gépeket, kövesse a [virtuális gépek regisztrálása a Windows rendszerű virtuális asztali gazdaszámítógépre](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) című témakör útmutatását, hogy manuálisan regisztrálja őket az 1. lépésben létrehozott új gazdagép-készletbe.
3. Új RemoteApp-alkalmazás csoportok létrehozása.
4. Felhasználók vagy felhasználói csoportok közzététele az új asztali és RemoteApp-alkalmazások csoportjaiban.
5. A feltételes hozzáférési szabályzat frissítésével engedélyezze az új objektumokat a [többtényezős hitelesítés beállítása](set-up-mfa.md)című részben leírtak szerint.

Az állásidő megelőzése érdekében először regisztrálja a meglévő munkamenet-gazdagépeket a Azure Resource Manager integrált gazdagép-készletekbe egyszerre kis csoportokban. Ezt követően lassan felveszi a felhasználókat az új Azure Resource Manager integrált alkalmazás-csoportokba.

## <a name="next-steps"></a>Következő lépések

Az áttelepítés után megismerheti, hogyan működik a Windows rendszerű virtuális asztal, ha megtekinti [az oktatóanyagokat](create-host-pools-azure-marketplace.md). Ismerkedjen meg a fejlett felügyeleti képességekkel a [meglévő címkészlet kibontásával és az](expand-existing-host-pool.md) [RDP-tulajdonságok testreszabásával](customize-rdp-properties.md).

A Service Objects szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Windows rendszerű virtuális asztali környezet](environment-setup.md)című témakört.
