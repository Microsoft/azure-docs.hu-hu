---
title: Windows 10 barangolási beállítások referenciája – Azure Active Directory
description: A Windows 10-es verzióban az ESR-ben barangoló vagy biztonsági mentésre kerülő beállítások
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6048ee9237640799b7bec37083e607fc74ffb8e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85252967"
---
# <a name="windows-10-roaming-settings-reference"></a>Windows 10 roaming beállítások referenciája

A következő lista felsorolja azokat a beállításokat, amelyek a Windows 10 rendszerben barangolnak vagy biztonsági mentést végeznek. 

## <a name="devices-and-endpoints"></a>Eszközök és végpontok

A Windows 10 szinkronizálási, biztonsági mentési és helyreállítási keretrendszere által támogatott eszközök és fióktípus összefoglalását a következő táblázat tartalmazza.

| Fiók típusa és művelet | Asztal | Mobil |
| --- | --- | --- |
| Azure Active Directory: szinkronizálás |Igen |Nem |
| Azure Active Directory: biztonsági mentés/visszaállítás |Nem |Nem |
| Microsoft-fiók: szinkronizálás |Igen |Yes |
| Microsoft-fiók: biztonsági mentés/visszaállítás |Nem |Igen |

## <a name="what-is-backup"></a>Mi az a biztonsági mentés?

A Windows-beállítások általában alapértelmezés szerint szinkronizálhatók, de egyes beállítások csak a biztonsági mentésre kerülnek, például az eszközön telepített alkalmazások listájára. A biztonsági mentés csak a mobileszközök számára lehetséges, és jelenleg nem érhető el Enterprise State Roaming felhasználók számára. A Backup egy Microsoft-fiók használ, és tárolja a beállításokat és az alkalmazásadatok a OneDrive. Ha a felhasználó a beállítások alkalmazás használatával letiltja a szinkronizálást az eszközön, a szokásos módon szinkronizált alkalmazásadatok csak biztonsági mentést készítenek. A biztonsági mentési információ csak a visszaállítási művelettel érhető el az új eszköz első futtatási élménye során. A biztonsági mentések az eszközbeállítások használatával letilthatók, és a felhasználó OneDrive fiókján keresztül kezelhetők és törölhetők.

## <a name="windows-settings-overview"></a>Windows-beállítások áttekintése

A következő beállítási csoportok érhetők el a végfelhasználók számára a beállítások szinkronizálásának engedélyezéséhez vagy letiltásához a Windows 10-es eszközökön.

* Téma: asztali háttér, felhasználói csempe, tálca pozíciója stb. 
* Internet Explorer beállításai: böngészési előzmények, beírt URL-címek, kedvencek stb. 
* Jelszavak: Windows Hitelesítőadat-kezelő, beleértve a Wi-Fi profilokat 
* Nyelvi beállítások: helyesírási szótár, rendszernyelv beállításai 
* Könnyű hozzáférés: Narrátor, képernyő-billentyűzet, nagyító 
* Egyéb Windows-beállítások: lásd a Windows-beállítások részleteit.
* Microsoft Edge böngésző beállítása: Microsoft Edge kedvencek, olvasási lista és egyéb beállítások

![Beállítások szinkronizálása](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

> [!NOTE]
> Ez a cikk a Microsoft Edge korábbi HTML-alapú böngészőre vonatkozik, amely a Windows 10 2015-es verziójában indult el. A cikk nem vonatkozik az új, 2020. január 15-én kiadott Microsoft Edge Chromium-alapú böngészőre. Az új Microsoft Edge szinkronizálási működésével kapcsolatos további információkért tekintse meg a [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync)című cikket.

A Microsoft Edge böngésző beállítási csoportjának (Kedvencek, olvasások listája) szinkronizálása engedélyezhető vagy letiltható a végfelhasználók számára a Microsoft Edge böngésző beállításai menüpontban.

![Fiók](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

A Windows 10 1803-es vagy újabb verziója esetén az Internet Explorer beállítási csoport (Kedvencek, begépelt URL-címek) szinkronizálása engedélyezhető vagy letiltható a végfelhasználók számára az Internet Explorer beállítások menüpontban. 

![Beállítások](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Windows-beállítások részletei

A következő táblázatban a Settings Group (beállítások) csoport oszlopában található egyéb bejegyzések a beállítások > fiókok > a beállítások szinkronizálása > más Windows-beállítások lehetőségre kattintva letilthatók. 

A beállítások csoport oszlop belső bejegyzései olyan beállításokat és alkalmazásokat mutatnak be, amelyek csak az alkalmazáson belüli szinkronizálásból, vagy a teljes eszköz szinkronizálásának a mobileszköz-kezelés (MDM) vagy a Csoportházirend beállítások használatával történő letiltásával érhetők el.
A nem barangoló vagy szinkronizált beállítások nem tartoznak csoporthoz.

| Beállítások | Asztal | Mobil | Group |
| --- | --- | --- | --- |
| **Fiókok**: fiók képe |szinkronizálás |X |Téma |
| **Fiókok**: egyéb Fiókbeállítások |X |X | |
| **Speciális mobil szélessáv**: internetkapcsolat megosztási hálózatának neve (lehetővé teszi a mobil Wi-Fi-pontok automatikus észlelését Bluetooth-kapcsolaton keresztül) |X |X |Jelszavak |
| **Alkalmazásadatok: az** egyes alkalmazások szinkronizálhatja az adatszinkronizálást |biztonsági másolat szinkronizálása |biztonsági másolat szinkronizálása |belső |
| **Alkalmazások listája**: telepített alkalmazások listája |X |biztonsági mentés |Egyéb |
| **Bluetooth**: minden Bluetooth-beállítás |X |X | |
| **Parancssor**: parancssor "Defaults" beállítások |szinkronizálás |X |belső |
| **Hitelesítő adatok**: hitelesítő adatok zárolása |szinkronizálás |szinkronizálás |jelszó |
| **Dátum, idő és régió**: automatikus idő (internetes idő szinkronizálása) |szinkronizálás |szinkronizálás |language |
| **Dátum, idő és régió**: 24 órás óra |szinkronizálás |X |language |
| **Dátum, idő és régió**: dátum és idő |szinkronizálás |X |language |
| **Dátum, idő és régió**: időzóna | |X |language |
| **Dátum, idő és régió**: nyári megtakarítási idő |szinkronizálás |X |language |
| **Dátum, idő és régió**: ország/régió |szinkronizálás |X |language |
| **Dátum, idő és régió**: a hét első napja |szinkronizálás |X |language |
| **Dátum, idő és régió**: régió formátuma (területi beállítás) |szinkronizálás |X |language |
| **Dátum, idő és régió**: rövid dátum |szinkronizálás |X |language |
| **Dátum, idő és régió**: hosszú dátum |szinkronizálás |X |language |
| **Dátum, idő és régió**: rövid idő |szinkronizálás |X |language |
| **Dátum, idő és régió**: hosszú idő |szinkronizálás |X |language |
| **Asztal személyre szabása**: asztali téma (háttér, rendszerszín, alapértelmezett rendszerhangok, képernyőkímélő) |szinkronizálás |X |Téma |
| **Asztal személyre szabása**: diavetítés háttérképe |szinkronizálás |X |Téma |
| **Asztal személyre szabása**: tálca beállításai (pozíció, automatikus elrejtés stb.) |szinkronizálás |X |Téma |
| **Asztal személyre szabása**: kezdőképernyő elrendezése |X |biztonsági mentés | |
| **Eszközök**: megosztott nyomtatók, amelyekhez kapcsolódott |X |X |egyéb |
| **Microsoft Edge böngésző**: olvasások listája |szinkronizálás |szinkronizálás |belső |
| **Microsoft Edge böngésző**: Kedvencek |szinkronizálás |szinkronizálás |belső |
| **Microsoft Edge böngésző**: legnépszerűbb helyek <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |belső |
| **Microsoft Edge böngésző**: beírt URL-címek <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |belső |
| **Microsoft Edge böngésző**: Kedvencek sáv beállításai <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |belső |
| **Microsoft Edge böngésző**: a Kezdőlap gomb <sup> [[1]](#footnote-1) megjelenítése</sup> |szinkronizálás |szinkronizálás |belső |
| **Microsoft Edge böngésző**: előugró ablakok tiltása <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |belső |
| **Microsoft Edge böngésző**: Kérdezzen rá, mi a teendő az egyes letöltésekkel <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |belső |
| **Microsoft Edge böngésző**: a jelszavak mentésére szolgáló ajánlat <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |belső |
| **Microsoft Edge böngésző**: a nem követett kérelmek küldése <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |belső |
| **Microsoft Edge böngésző**: űrlapok bejegyzéseinek mentése <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |belső |
| **Microsoft Edge böngésző**: a keresés és a webhely javaslatainak megjelenítése I Type <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |belső |
| **Microsoft Edge böngésző**: cookie-k preferencia <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |belső |
| **Microsoft Edge böngésző**: lehetővé teszi a webhelyek számára a védett adathordozó-licencek mentését az eszközön <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |belső |
| **Microsoft Edge böngésző**: képernyőolvasó-beállítás <sup> [[1]](#footnote-1)</sup> |szinkronizálás |szinkronizálás |belső |
| **Kontrasztos megjelenítés**: be vagy ki |szinkronizálás |X |könnyű hozzáférés |
| **Kontrasztos**: téma beállításai |szinkronizálás |X |könnyű hozzáférés |
| **Internet Explorer**: lapok megnyitása (URL és cím) |szinkronizálás |szinkronizálás |Internet Explorer |
| **Internet Explorer**: olvasások listája |szinkronizálás |szinkronizálás |Internet Explorer |
| **Internet Explorer**: beírt URL-címek |szinkronizálás |szinkronizálás |Internet Explorer |
| **Internet Explorer**: böngészési előzmények |szinkronizálás |szinkronizálás |Internet Explorer |
| **Internet Explorer**: Kedvencek |szinkronizálás |szinkronizálás |Internet Explorer |
| **Internet Explorer**: kizárt URL-címek |szinkronizálás |szinkronizálás |Internet Explorer |
| **Internet Explorer**: kezdőlapok |szinkronizálás |szinkronizálás |Internet Explorer |
| **Internet Explorer**: tartományi javaslatok |szinkronizálás |szinkronizálás |Internet Explorer |
| **Billentyűzet**: a felhasználók be-és kikapcsolhatják a képernyő-billentyűzetet |szinkronizálás |X |könnyű hozzáférés |
| **Billentyűzet**: Sticky Yes (alapértelmezés szerint kikapcsolva) |szinkronizálás |X |könnyű hozzáférés |
| **Billentyűzet**: szűrési kulcsok bekapcsolása (alapértelmezés szerint kikapcsolva) |szinkronizálás |X |könnyű hozzáférés |
| **Billentyűzet**: a váltási kulcsok bekapcsolása (alapértelmezés szerint kikapcsolva) |szinkronizálás |X |könnyű hozzáférés |
| **Internet Explorer**: tartomány nyelve: kínai (CHS) QWERTY – önálló tanulás engedélyezése |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY – dinamikus jelölt rangsor engedélyezése |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY-char-set egyszerűsített kínai |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY-char-set hagyományos kínai |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY – fuzzy pinjin |szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelv**: CHS QWERTY-fuzzy pár |szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelv**: CHS QWERTY – teljes pinjin |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY – dupla pinjin |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY – automatikus javítás olvasása |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY-C/E kapcsoló kulcsa, eltolás |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS QWERTY-C/E kapcsoló kulcsa, CTRL |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS Wubi – egyetlen karakteres beviteli mód |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS Wubi – a jelölt fennmaradó kódolásának megjelenítése |szinkronizálás |X |Nyelv |
| **Nyelv**: CHS Wubi-Beep if 4 – kódolás érvénytelen |szinkronizálás |X |Nyelv |
| **Nyelv**: CHT bopomofo – include CJK ext-A |szinkronizálás |X |Nyelv |
| **Nyelv**: Japán IME – prediktív gépelés és egyéni szavak |szinkronizálás |szinkronizálás |Nyelv |
| **Nyelv**: koreai (kor) IME |X |X |Nyelv |
| **Nyelv**: kézírás-felismerés |X |X |Nyelv |
| **Nyelv**: nyelvi profil |szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelv**: helyesírás – automatikus javítás és kiemelési hibák |szinkronizálás |biztonsági mentés |Nyelv |
| **Nyelv**: billentyűzetek listája |szinkronizálás |biztonsági mentés |Nyelv |
| **Zárolási képernyő**: az összes zárolási képernyő beállításai |X |X | |
| **Nagyító**: be vagy ki (fő váltógomb) |X |X |Könnyű elérés |
| **Nagyító**: inverziós szín be-vagy kikapcsolása (alapértelmezés szerint kikapcsolva) |szinkronizálás |X |Könnyű elérés |
| **Nagyító**: követés – a billentyűzet fókuszának követése |szinkronizálás |X |Könnyű elérés |
| **Nagyító**: követés – az egérmutató követése |szinkronizálás |X |Könnyű elérés |
| **Nagyító**: indítás felhasználói bejelentkezéskor (alapértelmezés szerint kikapcsolva) |szinkronizálás |X |Könnyű elérés |
| **Egér**: az egérmutató méretének módosítása |szinkronizálás |X |egyéb |
| **Egér**: az egérmutató színének módosítása |szinkronizálás |X |egyéb |
| **Egér**: minden egyéb beállítás |X |X | |
| **Narrátor**: Gyors indítás |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: a felhasználók módosíthatják a Narrátor beszédét |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: a felhasználók be-és kikapcsolhatják a Narrátor beolvasási ajánlatait az általános elemekhez (alapértelmezés szerint). |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: a felhasználók be-vagy kikapcsolhatják, hogy hallják-e a begépelt karaktereket (alapértelmezés szerint) |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: a felhasználók be-és kikapcsolhatják, hogy tudnak-e begépelt szavakat hallani (alapértelmezés szerint) |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: kurzor beszúrása a narrátorba (alapértelmezés szerint) |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: a Narrátor kurzorának vizuális kiemelésének engedélyezése (alapértelmezés szerint) |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: hangjelek lejátszása (alapértelmezés szerint) |szinkronizálás |X |Könnyű elérés |
| **Narrátor**: kulcsok aktiválása az érintési billentyűzeten az ujját kiemelve (alapértelmezés szerint kikapcsolva) |szinkronizálás |X |Könnyű elérés |
| **Egyszerű hozzáférés**: a villogó kurzor vastagságának beállítása |szinkronizálás |X |Könnyű elérés |
| **Könnyű hozzáférés**: háttérképek eltávolítása (alapértelmezés szerint kikapcsolva) |szinkronizálás |X |Könnyű elérés |
| **Energiaellátás és alvó állapot**: minden beállítás |X |X | |
| **Kezdőképernyő személyre szabása**: ékezetes szín (csak telefonos) |X |szinkronizálás |Téma |
| **Gépelés**: helyesírási szótár |szinkronizálás |biztonsági mentés |Nyelv |
| Gépelés: hibásan írt szöveg automatikus **kiírása** |szinkronizálás |biztonsági mentés |Nyelv |
| **Gépelés**: a hibásan írt szavak kiemelése |szinkronizálás |biztonsági mentés |Nyelv |
| **Beírás**: szövegbeli javaslatok megjelenítése típusként |szinkronizálás |biztonsági mentés |Nyelv |
| **Begépelés**: szóköz hozzáadása a szöveges javaslat kiválasztása után |szinkronizálás |biztonsági mentés |Nyelv |
| **Beírás**: adjon meg egy pontot, miután duplán koppint a szóközre. |szinkronizálás |biztonsági mentés |Nyelv |
| **Begépelés**: nagybetűssé tétel az egyes mondatok első betűje |szinkronizálás |biztonsági mentés |Nyelv |
| **Begépelés**: az összes nagybetűs karakterek használata, ha duplán koppintok a SHIFT billentyűre |szinkronizálás |biztonsági mentés |Nyelv |
| Begépelés: lejátszási kulcs hangok **beírása** |szinkronizálás |biztonsági mentés |Nyelv |
| **Beírás**: személyre szabási adatainak érintéses billentyűzethez |szinkronizálás |biztonsági mentés |Nyelv |
| **Wi-Fi**: Wi-Fi profilok (csak WPA) |szinkronizálás |szinkronizálás |Jelszavak |

###### <a name="footnote-1"></a>1. lábjegyzet

A Windows Creators Update minimális támogatott operációsrendszer-verziója (Build 15063). 

## <a name="next-steps"></a>Következő lépések

Az áttekintést lásd: a [nagyvállalati állapot barangolása – áttekintés](enterprise-state-roaming-overview.md).
