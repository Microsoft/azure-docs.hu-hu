---
title: Windows rendszerű virtuális asztali MSIX alkalmazás – GYIK – Azure
description: Gyakran ismételt kérdések a Windows rendszerű virtuális asztali MSIX alkalmazással kapcsolatban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 395c274630131c2ae5f451443913e1e69c7c422a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738701"
---
# <a name="msix-app-attach-faq"></a>MSIX-alkalmazás csatolása – GYIK

Ez a cikk a Windows rendszerű virtuális asztali MSIX alkalmazással kapcsolatos gyakori kérdésekre ad választ.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Mi a különbség a MSIX és a MSIX alkalmazás között?

A MSIX az alkalmazások csomagolási formátuma, míg a MSIX-alkalmazás csatolása az a szolgáltatás, amely MSIX-csomagokat biztosít a telepítéshez.

## <a name="does-msix-app-attach-use-fslogix"></a>A MSIX-alkalmazás csatolja az FSLogix-t?

A MSIX-alkalmazás csatolása nem használja a FSLogix. A MSIX és a FSLogix együttes használata azonban zökkenőmentes felhasználói élmény biztosítására szolgál.

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>Használhatom a MSIX alkalmazást a Windows rendszerű virtuális asztalon kívül?

A Power MSIX-alkalmazás által csatolt API-k elérhetők a Windows 10 Enterprise rendszerhez. Ezek az API-k a Windows rendszerű virtuális asztali gépeken kívül is használhatók. Azonban nincs olyan felügyeleti sík a MSIX alkalmazáshoz, amely a Windows rendszerű virtuális asztalon kívülre van csatolva.

## <a name="how-do-i-get-an-msix-package"></a>Hogyan lekérni egy MSIX-csomagot?

A szoftver gyártójától MSIX-csomagot fog adni. A nem MSIX csomagok MSIX is átalakíthatók. További információ a [meglévő telepítők MSIX való áthelyezéséről](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Mely operációs rendszerek támogatják a MSIX-alkalmazás csatolását?

Windows 10 Enterprise és Windows 10 Enterprise multi-session, 2004-es vagy újabb verzió.

## <a name="is-msix-app-attach-currently-generally-available"></a>Jelenleg általánosan elérhető a MSIX-alkalmazás csatolása?

A MSIX-alkalmazás csatolása a Windows 10 Enterprise és a Windows 10 Enterprise multi-session, 2004-es vagy újabb verziójának része. Mindkét operációs rendszer jelenleg általánosan elérhető. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Használhatom a MSIX alkalmazást a Windows rendszerű virtuális asztali gépeken kívül?

A MSIX és a MSIX app Attach API-k a Windows 10 Enterprise és a Windows 10 Enterprise multi-session, a 2004-es és újabb verziókban találhatók. Jelenleg nem biztosítunk felügyeleti szoftvert a MSIX alkalmazáshoz a Windows rendszerű virtuális asztalon kívül.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>Futtathatok egyszerre két különböző verziót ugyanazon alkalmazáson?

Ugyanannak a MSIX-alkalmazásnak két verziójának párhuzamos futtatásához a appxmanifest.xml fájlban definiált MSIX-csomag családnak minden alkalmazás esetében eltérőnek kell lennie.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>Le kell tiltani az automatikus frissítést a MSIX-alkalmazás csatolásakor?

Igen. A MSIX alkalmazás csatolása nem támogatja az MSIX-alkalmazások automatikus frissítését.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Hogyan működnek az engedélyek a MSIX-alkalmazáshoz való csatolással?

Az MSIX-alkalmazást használó gazdagépeken található összes virtuális gép (VM) olvasási engedéllyel kell rendelkeznie a MSIX lemezképeit tároló fájlmegosztás számára. Ha Azure Files is használ, akkor a szerepköralapú hozzáférés-vezérlést (RBAC) és az új Technology File System (NTFS) engedélyeket is meg kell adni.

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>Használhatom a HTTP-vagy HTTPs-alapú MSIX-alkalmazást?

A MSIX-alkalmazás HTTP-n vagy HTTPs-en keresztüli csatlakoztatása jelenleg nem támogatott.

## <a name="can-i-restage-the-same-msix-application"></a>Megpihenhető-e a MSIX-alkalmazás is?

Igen. Megadhatja a már újraelőkészített alkalmazásokat, és ez nem okoz hibát.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>Az MSIX alkalmazás csatolja az önaláírt tanúsítványokat?

Igen. Telepítenie kell az önaláírt tanúsítványt az összes olyan munkamenet-gazda virtuális gépre, ahol a MSIX-alkalmazás csatolva van az önaláírt alkalmazás üzemeltetéséhez.


## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az MSIX-alkalmazás csatlakoztatásáról, tekintse át az Áttekintés és a [Szószedet](app-attach-glossary.md) [témakört](what-is-app-attach.md) . Ellenkező esetben Ismerkedjen [meg az alkalmazások csatlakoztatásának beállítása](app-attach.md)című lépéssel.
