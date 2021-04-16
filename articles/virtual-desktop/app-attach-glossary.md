---
title: Windows Virtual Desktop MSIX-alkalmazás csatolási szószedete – Azure
description: Az MSIX-alkalmazás szószedete kifejezéseket és fogalmakat csatol.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: c5c596735ad91f38d5ba4217135a9373d2856182
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538462"
---
# <a name="msix-app-attach-glossary"></a>MSIX-alkalmazás csatolása – szószedet

Ez a cikk az MSIX-alkalmazás csatolásával kapcsolatos fő kifejezések és fogalmak definícióit tartalmazza.

## <a name="msix-container"></a>MSIX-tároló

Az MSIX-alkalmazások MSIX-tárolóban futnak. További információ: [MSIX-tárolók.](/windows/msix/msix-container)

## <a name="msix-application"></a>MSIX-alkalmazás 

Egy alkalmazásban tárolt alkalmazás. MSIX-fájl.

## <a name="msix-package"></a>MSIX-csomag 

Az MSIX-csomag egy MSIX-fájl vagy -alkalmazás.

## <a name="msix-share"></a>MSIX-megosztás

Az MSIX-megosztás olyan hálózati megosztás, amely kibontott MSIX-csomagokat tartalmaz. Az MSIX-megosztásnak támogatnia kell az SMB 3-as vagy újabb rendszereket. A megosztásnak a gazdagépkészlet rendszerfiók Virtual Machines (VM) számára is elérhetőnek kell lennie. Az MSIX-csomagok az MSIX-megosztásból vannak előléptetve anélkül, hogy alkalmazásfájlokat kell áthelyezni a rendszer meghajtóra. 

## <a name="msix-image"></a>MSIX-rendszerkép

Az MSIX-lemezképek olyan VHD-, VHDx- vagy CIM-fájlok, amelyek egy vagy több MSIX csomagolt alkalmazást tartalmaznak. Minden alkalmazás az MSIX-rendszerképben lesz kézbesítve az MSIXMGR eszközzel.

## <a name="repackage"></a>Újracsomagolás

Az újracsomagolás egy nem MSIX-alkalmazást vesz igénybe, és az MSIX-be konvertálja az MSIX Packaging Tool (MPT) használatával. További információt az [MSIX Packaging Tool áttekintésében talál.](/windows/msix/packaging-tool/tool-overview)

## <a name="expand-an-msix-package"></a>MSIX-csomag kibontása

Az MSIX-csomagok kibontása többlépéses folyamat. A bővítés az MSIX-fájlt egy VHD(x) vagy CIM-fájlba helyezi. 

MSIX-csomag kibontása:

1. MSIX-csomag (MSIX-fájl) lekért fájlja.
2. Nevezze át az MSIX-fájlt egy .zip fájlra.
3. Csomagolja ki az eredményül kapott .zip fájlt egy mappába.
4. Hozzon létre egy, a mappával azonos méretű virtuális merevlemezt.
5. Csatlakoztassa a VHD-t.
6. Lemez inicializálása.
7. Partíció létrehozása.
8. Formázza a partíciót.
9. Másolja a kicsomagolt tartalmat a VHD-be.
10. Az MSIXMGR eszközzel ACL-eket alkalmazhat a VHD tartalmára.
11. A VHD(x) vagy [a CIM leválasztása.](#cim)

## <a name="upload-an-msix-package"></a>MSIX-csomag feltöltése 

Az MSIX-csomagok feltöltése magában foglalja a kibontott MSIX-csomagot tartalmazó VHD(x) vagy [CIM](#cim) feltöltését az MSIX-megosztásba.

A Windows Virtual Desktop MSIX-megosztásonként egyszer történik feltöltés. A csomag feltöltése után az ugyanabban az előfizetésben található összes gazdagépkészlet hivatkozhat rá.

## <a name="add-an-msix-package"></a>MSIX-csomag hozzáadása

A Windows Virtual Desktop MSIX-csomag hozzáadása egy gazdagépkészlethez kapcsolódik.

## <a name="publish-an-msix-package"></a>MSIX-csomag közzététele 

A Windows Virtual Desktop közzétett MSIX-csomagot hozzá kell rendelni egy Active Directory-tartomány-szolgáltatáshoz (AD DS) vagy Azure Active Directory (Azure AD) felhasználóhoz vagy felhasználói csoporthoz.

## <a name="staging"></a>Előkészítés

Az előkészítés két dolgot foglal magában:

- A VHD(x) vagy [a CIM csatlakoztatása](#cim) a virtuális géphez.
- Az operációs rendszer értesítése arról, hogy az MSIX-csomag regisztrálható.

## <a name="registration"></a>Regisztráció

A regisztráció egy szakaszos MSIX-csomagot tesz elérhetővé a felhasználók számára. A regisztráció felhasználónkénti alapon történik. Ha nem regisztrált explicit módon egy alkalmazást az adott felhasználóhoz, nem fogja tudni futtatni az alkalmazást.

A regisztrációnak két típusa van: normál és késleltetett.

### <a name="regular-registration"></a>Rendszeres regisztráció

A rendszeres regisztráció során a felhasználóhoz rendelt összes alkalmazás teljes mértékben regisztrálva van. A regisztráció akkor történik meg, amikor a felhasználó bejelentkezik a munkamenetbe, ami hatással lehet arra az időre, amely alatt használatba veszi a Windows Virtual Desktop.

### <a name="delayed-registration"></a>Késleltetett regisztráció

A késleltetett regisztráció során a felhasználóhoz rendelt összes alkalmazás csak részlegesen van regisztrálva. Részleges regisztráció esetén a Start menü csempe és a dupla kattintásos fájltársítások regisztrálva vannak. A regisztrációra akkor kerül sor, amikor a felhasználó bejelentkezik a munkamenetbe, így az minimális hatással van az alkalmazás használatának Windows Virtual Desktop. A regisztráció csak akkor fejeződik be, ha a felhasználó az MSIX-csomagban futtatja az alkalmazást.

Az MSIX-alkalmazás csatolásának alapértelmezett konfigurációja jelenleg a késleltetett regisztráció.

## <a name="deregistration"></a>Regisztráció deregistrációja

A regisztráció törlése eltávolít egy felhasználó regisztrált, de nem futó MSIX-csomagját. A regisztrációt akkor lehet dereginálni, amikor a felhasználó kiesik a munkamenetből. A regisztráció-regisztráció során az MSIX-alkalmazás csatolja a felhasználóra jellemző alkalmazásadatokat a helyi felhasználói profilba.

## <a name="destage"></a>Destage (Destage)

Az előkészítés arról értesíti az operációs rendszert, hogy egy MSIX-csomag vagy -alkalmazás, amely jelenleg nem fut, és egyik felhasználó számára sem áll elő, leválasztható. Ezzel eltávolítja az összes rá való hivatkozást az operációs rendszerben.

## <a name="cim"></a>Cim

. A CIM az Összetett képfájlok rendszeréhez (CimFS) társított új fájlkiterjesztés. A CIM-fájlok csatlakoztatása és leválasztása gyorsabb a VHD-fájlok esetén. A CIM emellett kevesebb processzort és memóriát használ fel, mint a VHD.

A CIM-fájlok olyan fájlok, amelyek a következővel: . CIM-bővítmény, amely metaadatokat és legalább hat további fájlt tartalmaz, amelyek tényleges adatokat tartalmaznak. A CIM-fájlban lévő fájloknak nincs kiterjesztése. Az alábbi táblázat a CIM-fájlban található példafájlok listáját tartalmazza:

| Fájlnév | Mellék | Méret |
|-----------|-----------|------|
| Vsc | Cim | 1 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 27 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 20 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 42 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 428 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 217 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 264 132 KB |

Az alábbi táblázat a VHD és a CimFS teljesítmény-összehasonlítását tartalmazza. Ezek a számok egy DSv4-gépen futtatott, 500 300 MB-os fájlokkal futtatott teszt eredményei.

|  Jellemzők                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| Átlagos csatlakoztatási idő     | 356 ms                     | 255 ms      |
| Átlagos leválasztási idő   | 1615 ms                    | 36 ms       |
| Memóriahasználat | 6% (8 GB-os)                      | 2% (8 GB-os)       |
| CPU (kiugró szám)          | Többszöri maximális érték | Nincs hatás |

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az MSIX-alkalmazás csatolásáról, tekintse meg az áttekintést [és a](what-is-app-attach.md) gyakori [kérdéseket.](app-attach-faq.md) Ellenkező esetben az Alkalmazás csatolásának [beállítása – első lépések.](app-attach.md)
