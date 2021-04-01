---
title: Windows 7 rendszerű virtuális gépek Windows Virtual Desktop (klasszikus) – Azure
description: Windows 7 rendszerű virtuális gépek (VM-EK) problémáinak megoldása Windowsos virtuális asztali (klasszikus) környezetben.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1d71b4472b16372927db56b20eca4fcbde0cc625
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88005414"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop-classic"></a>Windows 7 rendszerű virtuális gépek hibáinak megoldása a Windows Virtual Desktopban (klasszikus)

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat.

Ez a cikk a Windows rendszerű virtuális asztali munkamenetgazda virtuális gépek (VM-EK) konfigurálásakor felmerülő problémák elhárítására használható.

## <a name="known-issues"></a>Ismert problémák

A Windows rendszerű virtuális asztali számítógépeken a Windows 7 nem támogatja a következő funkciókat:

- Virtualizált alkalmazások (RemoteApp)
- Időzóna-átirányítás
- Automatikus DPI-méretezés

A Windows virtuális asztal csak a Windows 7 teljes asztalait tudja virtualizálni.

Habár az automatikus DPI-méretezés nem támogatott, manuálisan módosíthatja a virtuális gép felbontását úgy, hogy a jobb gombbal az Távoli asztal-ügyfél ikonjára kattint, és kiválasztja a **feloldás** elemet.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Hiba: nem érhető el a Távoli asztal felhasználói csoport

Ha a Windows rendszerű virtuális asztal nem találja Önt vagy a felhasználók hitelesítő adatait a Távoli asztal felhasználói csoportban, a következő hibaüzenetek egyike jelenhet meg:

- "Ez a felhasználó nem tagja a Távoli asztal felhasználói csoportnak"
- "Engedélyeket kell adni a bejelentkezéshez Távoli asztali szolgáltatások"

A hiba elhárításához vegye fel a felhasználót a Távoli asztal felhasználói csoportba:

1. Nyissa meg az Azure Portalt.
2. Válassza ki azt a virtuális gépet, amelyen a hibaüzenet jelenik meg.
3. Válassza **a parancs futtatása** lehetőséget.
4. Futtassa a következő parancsot, és a `<username>` helyére írja be a hozzáadni kívánt felhasználó nevét:

   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```