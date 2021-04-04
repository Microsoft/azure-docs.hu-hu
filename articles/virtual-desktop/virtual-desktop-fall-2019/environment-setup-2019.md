---
title: Windows rendszerű virtuális asztali (klasszikus) környezet – Azure
description: A Windows rendszerű virtuális asztali környezet (klasszikus) alapvető elemei.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4fa281f6435013e9feb6808aab04ffa69c5f96c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008610"
---
# <a name="windows-virtual-desktop-classic-environment"></a>Windows rendszerű virtuális asztali (klasszikus) környezet

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../environment-setup.md).

A Windows Virtual Desktop egy olyan szolgáltatás, amely egyszerű és biztonságos hozzáférést biztosít a felhasználóknak a virtualizált asztali számítógépekhez és a RemoteAppokhoz. Ez a témakör részletesen ismerteti a Windows rendszerű virtuális asztali környezet általános szerkezetét.

## <a name="tenants"></a>Bérlők

A Windows rendszerű virtuális asztali bérlő a Windows rendszerű virtuális asztali környezet felügyeletének elsődleges felülete. Minden Windowsos virtuális asztali bérlőhöz társítani kell a környezetbe bejelentkező felhasználókat tartalmazó Azure Active Directory. A Windows rendszerű virtuális asztali bérlőből megkezdheti a gazdagép-készletek létrehozását a felhasználók munkaterhelésének futtatásához.

## <a name="host-pools"></a>Gazdagépek készletei

A gazdagépek olyan Azure-beli virtuális gépek gyűjteményei, amelyek a Windows rendszerű virtuális asztali ügynök futtatásakor a Windows Virtual Desktop szolgáltatásban futnak. A gazdagép-készletben lévő összes munkamenet-gazda virtuális gépet ugyanabból a rendszerképből kell származnia, amely konzisztens felhasználói élményt nyújt.

A gazdagépek két típus egyike lehet:

- Személyes, ahol minden egyes munkamenet-gazdagép az egyes felhasználókhoz van rendelve.
- Készletezett, ahol a munkamenet-gazdagépek fogadhatnak kapcsolatokat bármely olyan felhasználótól, aki a gazdagépen belül egy alkalmazáscsoport számára van engedélyezve.

A gazdagépen további tulajdonságokat is megadhat a terheléselosztási viselkedés megváltoztatásához, az egyes munkamenet-állomások által elvégezhető munkamenetek számának megadásához, valamint arról, hogy a felhasználó milyen műveleteket végezhet a gazdagépen a gazdaszámítógépen a Windows rendszerű virtuális asztali munkamenetbe való bejelentkezéskor. A felhasználók számára közzétett erőforrásokat az alkalmazás-csoportok segítségével szabályozhatja.

## <a name="app-groups"></a>Alkalmazáscsoportok

Az alkalmazáscsoport a gazdagépen lévő munkamenet-gazdagépekre telepített alkalmazások logikai csoportosítása. Az alkalmazások csoportja a két típus egyike lehet:

- RemoteApp, ahol a felhasználók az egyénileg kiválasztott és az alkalmazás csoportba való közzétételhez hozzáférnek a RemoteApp-hoz
- Asztal, ahol a felhasználók a teljes asztalt érik el

Alapértelmezés szerint a rendszer automatikusan létrehoz egy asztali alkalmazást ("asztali alkalmazáscsoport"), amikor létrehoz egy gazdagép-készletet. Az alkalmazáscsoport bármikor eltávolítható. Azonban nem hozhat létre másik asztali alkalmazást a gazdagépen, amíg létezik egy asztali alkalmazáscsoport. A RemoteApp-alkalmazások közzétételéhez létre kell hoznia egy RemoteApp-alkalmazás csoportot. Több RemoteApp-alkalmazáscsoport is létrehozható a különböző munkavégző forgatókönyvek kielégítése érdekében. A különböző RemoteApp-alkalmazások többek között átfedésben lévő RemoteAppkat is tartalmazhatnak.

Az erőforrások felhasználók számára való közzétételéhez hozzá kell rendelnie azokat az alkalmazás-csoportokhoz. Amikor felhasználókat rendel az alkalmazás-csoportokhoz, vegye figyelembe a következőket:

- Egy felhasználó nem rendelhető hozzá egyszerre egy asztali alkalmazáscsoport és egy RemoteApp-alkalmazáscsoport is ugyanabban a gazdagép-készletben.
- Egy felhasználó több, ugyanazon a gazdagépen belüli alkalmazás-csoporthoz is hozzárendelhető, és a hírcsatorna mindkét alkalmazáscsoport összegyűjtését eredményezi.

## <a name="tenant-groups"></a>Bérlői csoportok

A Windows virtuális asztal szolgáltatásban a Windows rendszerű virtuális asztali bérlő az a hely, ahol a telepítés és a konfigurálás nagy része történik. A Windows rendszerű virtuális asztali bérlő tartalmazza a gazdagépeket, az alkalmazás-csoportokat és az alkalmazáscsoport felhasználói hozzárendeléseit. Bizonyos esetekben azonban előfordulhat, hogy egyszerre több Windows rendszerű virtuális asztali bérlőt kell kezelnie, különösen akkor, ha Ön egy felhőalapú szolgáltató (CSP) vagy egy szolgáltatói partner. Ezekben az esetekben használhat egy egyéni Windowsos virtuális asztali bérlői csoportot az ügyfelek Windows rendszerű virtuális asztali bérlői és központilag felügyelt hozzáférésének elhelyezésére. Ha azonban csak egyetlen Windowsos virtuális asztali bérlőt kezel, a bérlői csoport fogalma nem érvényes, és továbbra is használhatja és kezelheti a bérlőt, amely az alapértelmezett bérlői csoportban található.

## <a name="end-users"></a>Végfelhasználók

Miután hozzárendelte a felhasználókat az alkalmazás csoportjaihoz, csatlakozhatnak a Windows rendszerű virtuális asztali környezethez a Windows rendszerű virtuális asztali ügyfelek bármelyikével.

## <a name="next-steps"></a>Következő lépések

További információ a delegált hozzáférésről és a szerepkörök felhasználókhoz való hozzárendeléséről a [Windows Virtual Desktopban](delegated-access-virtual-desktop-2019.md).

A Windows rendszerű virtuális asztali bérlő beállításával kapcsolatos további információkért lásd: [bérlő létrehozása a Windows rendszerű virtuális asztalon](tenant-setup-azure-active-directory.md).

A következő cikkekből megtudhatja, hogyan csatlakozhat a Windows rendszerű virtuális asztalhoz:

- [Csatlakozás Windows 10 vagy Windows Server 7 rendszerről](connect-windows-7-10-2019.md)
- [Csatlakozás webböngészőről](connect-web-2019.md)
