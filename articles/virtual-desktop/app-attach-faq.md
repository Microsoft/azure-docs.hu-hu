---
title: Windows Virtual Desktop MSIX-alkalmazás csatolása – GYIK – Azure
description: Gyakori kérdések az MSIX-alkalmazás csatolása Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 6057f4a76f274e34b036ea352a3691b34d24b3a1
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835361"
---
# <a name="msix-app-attach-faq"></a>MSIX-alkalmazás csatolása – GYIK

Ez a cikk az MSIX alkalmazás-csatolással kapcsolatos gyakori kérdésekre ad Windows Virtual Desktop.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Mi a különbség az MSIX és az MSIX alkalmazás csatolása között?

Az MSIX az alkalmazások csomagolási formátuma, míg az MSIX app attach az a szolgáltatás, amely MSIX-csomagokat kézbesít az üzemelő példánynak.

## <a name="does-msix-app-attach-use-fslogix"></a>Az MSIX-alkalmazás csatolása FSLogixot használ?

Az MSIX-alkalmazás csatolása nem használja az FSLogixot. Az MSIX-alkalmazás csatolása és az FSLogix azonban úgy lett kialakítva, hogy együttműködjön a zökkenőmentes felhasználói élmény érdekében.

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>Használhatom az MSIX-alkalmazás csatolását a Windows Virtual Desktop?

Az MSIX-alkalmazáshoz csatolható API-k elérhetők a Windows 10 Enterprise. Ezek az API-k a külső Windows Virtual Desktop. Az MSIX-alkalmazások csatolása azonban nem rendelkezik felügyeleti síkgal a Windows Virtual Desktop.

## <a name="how-do-i-get-an-msix-package"></a>Hogyan MSIX-csomagot?

A szoftverszállító egy MSIX-csomagot fog Önnek adni. A nem MSIX-csomagokat MSIX-csomagokká is konvertálhatja. További [információ: Meglévő telepítők áthelyezése AZ MSIX-be.](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)

## <a name="which-operating-systems-support-msix-app-attach"></a>Mely operációs rendszerek támogatják az MSIX-alkalmazás csatolását?

Windows 10 Enterprise a Windows 10 Enterprise 2004-es vagy újabb verzióját.

## <a name="is-msix-app-attach-currently-generally-available"></a>Általánosan elérhető az MSIX-alkalmazás csatolása?

Az MSIX-alkalmazás csatolása a Windows 10 Enterprise és Windows 10 Enterprise több munkamenetes, 2004-es vagy újabb verziójának része. Jelenleg mindkét operációs rendszer általánosan elérhető. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Használhatom az MSIX-alkalmazás csatolását a Windows Virtual Desktop?

Az MSIX és az MSIX alkalmazáshoz csatoló API-k a Windows 10 Enterprise és Windows 10 Enterprise több munkamenetes, 2004-es és újabb verzióinak részei. Jelenleg nem biztosítunk felügyeleti szoftvert az MSIX-alkalmazáshoz való csatoláshoz a Windows Virtual Desktop.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>Futtathatok egyszerre két verziót ugyanannak az alkalmazásnak?

Ahhoz, hogy ugyanazon MSIX-alkalmazások két verziója egyidejűleg fusson, az appxmanifest.xml fájlban meghatározott MSIX-csomag családnak minden alkalmazás esetében eltérőnek kell lennie.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>Le kell tiltanom az automatikus frissítést az MSIX-alkalmazás csatolásának használata esetén?

Igen. Az MSIX-alkalmazás csatolása nem támogatja az MSIX-alkalmazások automatikus frissítését.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Hogyan működnek az engedélyek az MSIX-alkalmazás csatolásával?

Az MSIX-alkalmazás csatolását használó gazdagépkészletben lévő összes virtuális gépnek olvasási engedéllyel kell rendelkeznie az MSIX-lemezképeket tároló fájlmegosztáson. Ha a szolgáltatás Azure Files is használ, szerepköralapú hozzáférés-vezérlési (RBAC) és új technológiai fájlrendszer (NTFS) engedélyeket is biztosítani kell számára.

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>Használhatom az MSIX-alkalmazás csatolását HTTP- vagy HTTPs-hez?

Az MSIX-alkalmazás csatolása HTTP vagy HTTPs protokollon keresztül jelenleg nem támogatott.

## <a name="can-i-restage-the-same-msix-application"></a>Át tudom restenni ugyanazt az MSIX-alkalmazást?

Igen. A már át restagedelt alkalmazásokat is át lehet restanálni, és ez nem okozhat hibákat.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>Támogatja az MSIX-alkalmazás csatolása az önaírt tanúsítványokat?

Igen. Az önaírt tanúsítványt minden olyan munkamenetgazda virtuális gépre telepítenie kell, ahol az MSIX-alkalmazás csatolása az önaírt alkalmazás gazdagépe.

## <a name="what-applications-can-i-repackage-to-msix"></a>Milyen alkalmazásokat csomagolok újra az MSIX-be?

Minden alkalmazás az operációs rendszer, a programozási nyelvek és a keretrendszerek különböző funkcióit használja. Az alkalmazás újracsomagolásához kövesse a meglévő telepítők [MSIX-be való áthelyezését lehetővé tevő](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)utasításokat. Az alkalmazások újracsomagolásához szükséges dolgok listáját a Prepare to package a desktop application (Asztali alkalmazás csomagolásának előkészítése) között [találja.](/windows/msix/desktop/desktop-to-uwp-prepare) 

Bizonyos alkalmazások nem rétegzheti az alkalmazásokat, ami azt jelenti, hogy nem csomagolhatóak újra MSIX-fájlba. Az alábbi lista azokat az alkalmazásokat tartalmazza, amelyek nem csomagolhatóak újra:

- Illesztőprogramok 
- Active-X vagy Silverlight
- VPN-ügyfelek
- Víruskereső programok

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az MSIX-alkalmazás csatolásról, tekintse meg az áttekintést [és](what-is-app-attach.md) a [szószedetet.](app-attach-glossary.md) Ellenkező esetben az Alkalmazás csatolásának [beállítása – első lépések.](app-attach.md)
