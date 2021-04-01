---
title: Kapcsolódás a Windows rendszerű virtuális asztalhoz (klasszikus) Windows 10 vagy 7 – Azure
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz (klasszikus) a Windows asztali ügyfélprogram használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 07/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 420c507361e3e2437366e6ccf2d46a8b78684e59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008357"
---
# <a name="connect-with-the-windows-desktop-classic-client"></a>Kapcsolódjon a Windows asztali (klasszikus) ügyfélprogrammal

> A következőkre vonatkozik: Windows 7, Windows 10 és Windows 10 IoT Enterprise

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../connect-windows-7-10.md).

A Windows rendszerű virtuális asztali erőforrásokat Windows 7, Windows 10 és Windows 10 IoT Enterprise rendszerű eszközökön érheti el a Windows asztali ügyfélprogram használatával. Az ügyfél nem támogatja a Windows 8 vagy a Windows 8,1 rendszert.

>[!NOTE]
>A Windows-ügyfél automatikusan alapértéke a Windows Virtual Desktop (klasszikus). Ha azonban az ügyfél észleli, hogy a felhasználó Azure Resource Manager erőforrásokkal is rendelkezik, automatikusan hozzáadja az erőforrásokat, vagy értesíti a felhasználót, hogy elérhetők.

> [!IMPORTANT]
> A Windows virtuális asztal nem támogatja a RemoteApp-és asztali kapcsolatok (RADC) ügyfelet vagy a Távoli asztali kapcsolat (MSTSC) ügyfelet.

> [!IMPORTANT]
> A Windows virtuális asztal jelenleg nem támogatja a Távoli asztal ügyfelet a Windows áruházból.

## <a name="install-the-windows-desktop-client"></a>A Windows asztali ügyfél telepítése

Válassza ki azt az ügyfelet, amely megfelel a Windows-verziójának:

- [Windows 64 bites](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 bites](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Telepítheti az aktuális felhasználóhoz tartozó ügyfelet, amely nem követeli meg a rendszergazdai jogosultságokat, vagy a rendszergazda telepítheti és konfigurálhatja az ügyfelet, hogy az eszköz összes felhasználója hozzáférhessen.

A telepítést követően a-ügyfél a Start menüből indítható el **Távoli asztal** keresésével.

## <a name="subscribe-to-a-workspace"></a>Előfizetés munkaterületre

A munkaterületekre kétféleképpen lehet előfizetni. Az ügyfél megpróbálhatja felderíteni a munkahelyi vagy iskolai fiókja által elérhető erőforrásokat, vagy közvetlenül megadhatja az URL-címet, ahol az erőforrások olyan esetekre vonatkoznak, amikor az ügyfél nem találja őket. Miután előfizetett egy munkaterületre, az alábbi módszerek egyikével indíthatja el az erőforrásokat:

- Nyissa meg a kapcsolódási központot, majd kattintson duplán egy erőforrásra az elindításához.
- Nyissa meg a Start menüt, és keressen egy mappát a munkaterület nevével, vagy adja meg az erőforrás nevét a keresősáv alatt.

### <a name="subscribe-with-a-user-account"></a>Előfizetés felhasználói fiókkal

1. Az ügyfél főoldalán válassza az **előfizetés** lehetőséget.
2. Ha a rendszer kéri, jelentkezzen be a felhasználói fiókjával.
3. Az erőforrások megjelennek a kapcsolatok központjában, és a munkaterület szerint vannak csoportosítva.

### <a name="subscribe-with-a-url"></a>Előfizetés URL-címmel

1. Az ügyfél főoldalán válassza az **előfizetés URL-lel** lehetőséget.
2. Adja meg a munkaterület URL-címét vagy e-mail-címét:
   - Ha a **munkaterület URL-címét** használja, használja a rendszergazda által megadott címet. Ha a Windows virtuális asztalról fér hozzá az erőforrásokhoz, a következő URL-címek egyikét használhatja:
     - Windowsos virtuális asztal (klasszikus): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Windows rendszerű virtuális asztal: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
   - Ha ehelyett az **e-mail** mezőt használja, adja meg az e-mail-címét. Ez azt jelzi, hogy az ügyfél megkeresi az e-mail-címéhez tartozó URL-címet, ha a rendszergazda beállította az [e-mail felderítését](/windows-server/remote/remote-desktop-services/rds-email-discovery).
3. Kattintson a **Tovább** gombra.
4. Ha a rendszer kéri, jelentkezzen be a felhasználói fiókjával.
5. Az erőforrásoknak a kapcsolatok központjában kell megjelenniük, munkaterület szerint csoportosítva.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Windows asztali ügyfél használatáról, tekintse meg [a Windows asztali ügyfélprogram használatának első lépéseivel foglalkozó](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)témakört.
