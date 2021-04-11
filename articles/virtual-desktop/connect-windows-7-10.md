---
title: Kapcsolódás a Windows rendszerű virtuális asztali Windows 10 vagy 7-es verzióhoz – Azure
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz a Windows asztali ügyfélprogram használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 09/22/2020
ms.author: helohr
manager: femila
ms.custom: template-how-to
ms.openlocfilehash: 625662a6b67e7d30e6320fe7831e4fa7793b9c30
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447879"
---
# <a name="connect-with-the-windows-desktop-client"></a>Kapcsolódás a Windows asztali ügyféllel

A Windows rendszerű virtuális asztali erőforrásait Windows 10, Windows 10 IoT Enterprise és Windows 7 operációs rendszert használó eszközökön érheti el a Windows asztali ügyfélprogram használatával. 

> [!IMPORTANT]
> Ez nem támogatja a 8. vagy a Windows 8,1.
> 
> Ez csak Azure Resource Manager objektumokat támogat, az objektumok Azure Resource Manager nélküli támogatásához lásd: [Kapcsolódás a Windows asztali (klasszikus) ügyféllel](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md).
> 
> Ez nem támogatja a RemoteApp-és asztali kapcsolat (RADC) ügyfelet vagy a Távoli asztali kapcsolat (MSTSC) ügyfelet.

## <a name="install-the-windows-desktop-client"></a>A Windows asztali ügyfél telepítése

Töltse le az ügyfelet a Windows-verzió alapján:

- [Windows 64 bites](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 bites](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

A telepítés során a hozzáférés meghatározásához válassza a következők egyikét:

- **Telepítés csak az Ön számára**
- **Telepítés a gép összes felhasználója számára** (rendszergazdai jogosultságok szükségesek)

Ha a telepítést követően el szeretné indítani az ügyfelet, használja a **Start** menüt, és keressen rá **Távoli asztal**.

## <a name="subscribe-to-a-workspace"></a>Előfizetés munkaterületre

Egy munkaterületre való előfizetéshez válassza a következőt:

- Munkahelyi vagy iskolai fiók használata, valamint az ügyfél számára elérhető erőforrások felderítése
- Az erőforrás adott URL-címének használata

Ha az erőforrást az előfizetés után szeretné elindítani, lépjen a **kapcsolódási központba** , és kattintson duplán az erőforrásra.

> [!TIP]
> Ha az erőforrást a **Start** menüből szeretné elindítani, keresse meg a mappát a munkaterület nevével, vagy adja meg az erőforrás nevét a keresősávban.

### <a name="use-a-user-account"></a>Felhasználói fiók használata

1. Válassza az **előfizetés** lehetőséget a főoldalon.
1. Ha a rendszer kéri, jelentkezzen be a felhasználói fiókjával.

A munkaterület szerint csoportosított erőforrások megjelennek a **Kapcsolatok központban**.

   > [!NOTE]
   > A Windows-ügyfél automatikusan alapértéke a Windows Virtual Desktop (klasszikus). 
   > 
   > Ha azonban az ügyfél további Azure Resource Manager erőforrásokat észlel, automatikusan hozzáadja azokat, vagy értesíti a felhasználót, hogy elérhetők.

### <a name="use-a-specific-url"></a>Adott URL-cím használata

1. Válassza az **előfizetés URL-cím** alapján lehetőséget a főoldalon.
1. Adja meg a *munkaterület URL* -címét vagy egy *e-mail-címet*:
   - A **munkaterület URL-címéhez** használja a rendszergazda által megadott URL-címet.

   |Rendelkezésre álló erőforrások|URL-cím|
   |-|-|
   |Windows Virtual Desktop (klasszikus)|`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`|
   |Windows virtuális asztal|`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`|
   |Windows rendszerű virtuális asztal (US Gov)|`https://rdweb.wvd.azure.us/api/arm/feeddiscovery`|
   
   - **E-mail**-cím esetén használja az e-mail-címét. 
      
   Az ügyfél megkeresi az e-mailhez társított URL-címet, ha a rendszergazda engedélyezte az [e-mailek észlelését](/windows-server/remote/remote-desktop-services/rds-email-discovery).

1. Kattintson a **Tovább** gombra.
1. Ha a rendszer kéri, jelentkezzen be a felhasználói fiókjával.

A munkaterület szerint csoportosított erőforrások megjelennek a **Kapcsolatok központban**.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az ügyfél használatáról, tekintse meg [a Windows asztali ügyfélprogram használatának első lépései](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)című témakört.

Ha Ön olyan rendszergazda, aki többet szeretne megtudni az ügyfél funkcióinak megismeréséről, tekintse meg a [Windows asztali ügyfélprogramot a rendszergazdák számára](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin).
