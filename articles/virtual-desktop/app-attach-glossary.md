---
title: Windows rendszerű virtuális asztali MSIX-alkalmazás szószedetének csatolása – Azure
description: A MSIX-alkalmazások glosszáriuma a feltételeket és fogalmakat csatolja.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7132eae073f3d53a104536076ae801ec9ff93e5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96518668"
---
# <a name="msix-app-attach-glossary"></a>MSIX-alkalmazás szószedetének csatolása

Ez a cikk a MSIX-alkalmazás csatolásával kapcsolatos legfontosabb feltételek és fogalmak definícióit sorolja fel.

## <a name="msix-container"></a>MSIX-tároló

Az MSIX-tárolók a MSIX-alkalmazások futtatására szolgálnak. További információ: MSIX- [tárolók](/windows/msix/msix-container).

## <a name="msix-application"></a>MSIX-alkalmazás 

Egy alkalmazásban tárolt alkalmazás. MSIX-fájl.

## <a name="msix-package"></a>MSIX-csomag 

Az MSIX-csomag egy MSIX-fájl vagy-alkalmazás.

## <a name="msix-share"></a>MSIX-megosztás

Az MSIX-megosztás egy olyan hálózati megosztás, amely kibővített MSIX-csomagokat tart fenn. A MSIX-megosztások támogatják az SMB 3 vagy újabb verzióját. Az alkalmazások ettől a MSIX-megosztástól kezdve nem kell az alkalmazás fájljait áthelyezni a rendszermeghajtóra.

## <a name="msix-image"></a>MSIX-rendszerkép

Az MSIX-rendszerkép olyan VHD-, VHDx-vagy CIM-fájl, amely egy vagy több MSIX csomagolt alkalmazást tartalmaz. Minden alkalmazást a MSIX-rendszerképben továbbítanak a MSIXMGR eszközzel.

## <a name="repackage"></a>Átcsomagolni

Az újracsomagolás egy nem MSIX alkalmazást használ, és átalakítja a MSIX a MSIX Packaging Tool (MPT) használatával. További információ: MSIX- [csomagoló eszköz – áttekintés](/windows/msix/packaging-tool/tool-overview).

## <a name="expand-an-msix-package"></a>MSIX-csomag kibontása

A MSIX-csomagok kiterjesztése egy többlépéses folyamat. A bővítés a MSIX-fájlt veszi át, és a tartalmát egy VHD (x) vagy CIM fájlba helyezi. 

MSIX-csomag kibontása:

1. MSIX-csomag (MSIX-fájl) beszerzése.
2. Nevezze át a MSIX-fájlt egy. zip-fájlba.
3. Bontsa ki az eredményül kapott. zip fájlt egy mappában.
4. Hozzon létre egy virtuális merevlemezt, amely a mappával megegyező méretű.
5. Csatlakoztassa a virtuális merevlemezt.
6. Lemez inicializálása.
7. Hozzon létre egy partíciót.
8. Formázza a partíciót.
9. Másolja ki a kibontott tartalmat a VHD-be.
10. A MSIXMGR eszköz használatával ACL-eket alkalmazhat a VHD tartalmára.
11. Válassza le a VHD-t (x) vagy a [CIM](#cim)-t.

## <a name="upload-an-msix-package"></a>MSIX-csomag feltöltése 

A MSIX-csomagok feltöltése magában foglalja a kibontott MSIX csomagot tartalmazó VHD (x) vagy [CIM](#cim) feltöltését a MSIX-megosztásra.

A Windows rendszerű virtuális asztali gépeken a feltöltések MSIX-megosztáson keresztül történnek. A csomagok feltöltése után az azonos előfizetésben lévő összes gazdagép hivatkozhat rá.

## <a name="add-an-msix-package"></a>MSIX-csomag hozzáadása

A Windows rendszerű virtuális asztali gépeken egy MSIX-csomag hozzáadása egy gazdagép-készlethez kapcsolódik.

## <a name="publish-an-msix-package"></a>MSIX-csomag közzététele 

A Windows rendszerű virtuális asztali számítógépeken a közzétett MSIX-csomagokat hozzá kell rendelni egy Active Directory-tartomány szolgáltatáshoz (AD DS) vagy Azure Active Directory (Azure AD) felhasználóhoz vagy felhasználói csoporthoz.

## <a name="staging"></a>Előkészítés

Az előkészítés két dolgot foglal magába:

- A VHD (x) vagy a [CIM](#cim) csatlakoztatása a virtuális géphez.
- Értesítés az operációs rendszerről, hogy a MSIX-csomag elérhető a regisztrációhoz.

## <a name="registration"></a>Regisztráció

A regisztráció egy előkészített MSIX csomagot tesz elérhetővé a felhasználók számára. A regisztráció felhasználónkénti alapon történik. Ha nem adott meg explicit módon egy alkalmazást az adott felhasználó számára, nem fogja tudni futtatni az alkalmazást.

A regisztrációnak két típusa van: rendszeres és késleltetett.

### <a name="regular-registration"></a>Normál regisztráció

Normál regisztráció esetén minden felhasználóhoz rendelt alkalmazás teljesen regisztrálva van. A regisztráció akkor történik meg, amikor a felhasználó bejelentkezik a munkamenetbe, ami hatással lehet a Windows virtuális asztal használatának megkezdéséhez szükséges időre.

### <a name="delayed-registration"></a>Késleltetett regisztráció

A késleltetett regisztráció során a felhasználóhoz rendelt összes alkalmazás csak részben van regisztrálva. A részleges regisztráció azt jelenti, hogy a Start menü csempe és a fájltársítások dupla kattintással regisztrálva van. A regisztráció akkor történik meg, amikor a felhasználó bejelentkezik a munkamenetbe, így minimális hatással van a Windows virtuális asztal használatának megkezdéséhez szükséges időre. A regisztráció csak akkor fejeződik be, ha a felhasználó a MSIX csomagban futtatja az alkalmazást.

A késleltetett regisztráció jelenleg a MSIX-alkalmazás csatolásának alapértelmezett konfigurációja.

## <a name="deregistration"></a>Legalább

A regisztráció eltávolítja a regisztrált, de nem futó MSIX-csomagot a felhasználó számára. A regisztráció akkor történik meg, amikor a felhasználó kijelentkezik a munkamenetből. A regisztráció során a MSIX alkalmazás csatolja a felhasználóra jellemző, a helyi felhasználói profilhoz tartozó alkalmazásadatok küldését.

## <a name="destage"></a>Kilépés

A leválasztás értesíti az operációs rendszert arról, hogy egy jelenleg nem futó MSIX-csomag vagy alkalmazás nem választható le. Ezzel eltávolítja az operációs rendszer összes hivatkozását.

## <a name="cim"></a>CIM

. A CIM a kompozit képfájlok rendszeréhez (CimFS) társított új fájlkiterjesztés. A CIM-fájlok csatlakoztatása és leválasztása gyorsabb a VHD-fájlokban. A CIM a VHD-nél kevesebb PROCESSZORt és memóriát is felhasznál.

A CIM-fájl egy fájl. CIM-bővítmény, amely metaadatokat és legalább hat további, tényleges adatokat tartalmazó fájlt tartalmaz. A CIM-fájlban található fájlok nem rendelkeznek bővítményekkel. A következő táblázat felsorolja a CIM-ban található példákat:

| Fájlnév | Mellék | Méret |
|-----------|-----------|------|
| VSC | CIM | 1 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 27 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 20 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 42 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 428 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 217 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 264 132 KB |

A következő táblázat a VHD-k és a CimFS közötti teljesítmény-összehasonlítás. Ezek a számok egy DSv4-gépen futtatott, 500 300 MB-os fájlokkal futtatott teszt eredményei.

|  Jellemzők                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| Csatlakoztatás átlagos ideje     | 356 MS                     | 255 MS      |
| Leválasztási idő átlagos ideje   | 1615 MS                    | 36 MS       |
| Memóriahasználat | 6% (8 GB)                      | 2% (8 GB)       |
| PROCESSZOR (szám-tüske)          | Többszöri maxed | Nincs hatás |

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az MSIX alkalmazáshoz, tekintse át az [áttekintést](what-is-app-attach.md) és a [gyakori kérdéseket](app-attach-faq.md). Ellenkező esetben Ismerkedjen [meg az alkalmazások csatlakoztatásának beállítása](app-attach.md)című lépéssel.
