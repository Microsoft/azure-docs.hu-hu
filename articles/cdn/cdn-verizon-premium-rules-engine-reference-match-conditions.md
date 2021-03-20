---
title: Azure CDN a Verizon Premium szabályainak motorjának egyeztetési feltételeiről
description: Az Azure Content Delivery Network a Verizon Premium Rules motor egyeztetési feltételeit ismertető dokumentációja.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: efd6e6a93cd4ca79e6c4b6de69f8514e2d71b252
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84323314"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN a Verizon Premium szabályainak motorjának egyeztetési feltételeiről

Ez a cikk részletes leírást nyújt az Azure Content Delivery Network (CDN) a Verizon Premium [Rules Engine](cdn-verizon-premium-rules-engine.md)-ről elérhető egyezési feltételeiről.

A szabály második része az egyeztetési feltétel. Az egyeztetési feltétel azokat a kérelmeket azonosítja, amelyekhez a rendszer a különböző funkciókat fogja végrehajtani.

Az egyeztetési feltételt például a következőre használhatja:

- A tartalomra vonatkozó kérelmek szűrése egy adott helyen.
- Egy adott IP-címről vagy országból/régióból generált kérelmek szűrése.
- Kérelmek szűrése fejléc-információk alapján.

## <a name="match-conditions"></a><a name="top"></a>Egyezési feltételek

* [Always](#always) (Mindig)
* [Eszköz](#device)
* [Hely](#location)
* [Származási](#origin)
* [Kérés](#request)
* [URL-cím](#url)

### <a name="always"></a><a name="always"></a>Mindig

[Az Always Match feltétel](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm) úgy van kialakítva, hogy az összes kérésre alkalmazza a szolgáltatások alapértelmezett készletét.

### <a name="device"></a><a name="device"></a>Eszköz

Ezek a megfeleltetési feltételek a kérelmek azonosítására szolgálnak az ügyfél felhasználói ügynöke alapján.

| Name       | Cél                                                           |
|------------|-------------------------------------------------------------------|
| Márkanév | Azonosítja a kérelmeket, hogy az eszköz márkanév megfelel-e: <br> **-** Megadott érték ([név literál](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm)) <br> **-** Reguláris kifejezés ([brand name regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm)) <br> **-** Adott minta ([márkanév helyettesítő karakter](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm)) |
| Eszköz operációs rendszere | Azonosítja a kérelmeket, hogy az eszköz operációs rendszere megfelel-e: <br> **-** Adott érték ([eszköz operációs rendszerének literálja](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm)) <br> **-** Reguláris kifejezés ([eszköz operációs rendszerének regexje](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm)) <br> **-** Adott minta ([eszköz operációs rendszer helyettesítője](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm)) |
| Eszköz operációs rendszerének verziója | Azonosítja a kérelmeket, hogy az eszköz operációs rendszerének verziója megfelel-e: <br> **-** Adott érték (az [eszköz operációs rendszerének konstans verziója](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm)) <br> **-** Reguláris kifejezés (az [eszköz operációs rendszerének regex verziója](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm)) <br> **-** Adott minta (az [eszköz operációs rendszerének helyettesítő verziója](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm)) |
| [Kettős tájolás?](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) | Meghatározza a kérelmeket, hogy az eszköz támogatja-e a kettős tájolást. |
| HTML elsődleges DTD | Azokat a kérelmeket azonosítja, amelyekkel az eszköz HTML-alapú DTD-je megfelel a-nek: <br> **-** Megadott érték ([HTML elsődleges DTD-literál](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm)) <br> **-** Reguláris kifejezés ([HTML előnyben részesített DTD-regex](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm)) <br> **-** Adott minta ([HTML elsődleges DTD helyettesítő karakter](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm)) |
| [A rendszerkép inbélés?](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) | Azonosítja a kérelmeket, hogy az eszköz támogatja-e a Base64 kódolású lemezképeket. |
| [Android rendszerű?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) | Azonosítja a kérelmeket, ha az eszköz az Android operációs rendszert használja. |
| [Az alkalmazás?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) | A kérelmeket a natív alkalmazás által kért tartalom alapján azonosítja. |
| [Teljes asztal?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) | Azonosítja a kérelmeket, hogy az eszköz teljes asztali élményt biztosít-e. |
| [IOS?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) | Meghatározza a kérelmeket, hogy az eszköz használ-e iOS-t. |
| [Robot?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) | Azonosítja a kérelmeket, ha az eszköz automatikus HTTP-ügyfélnek számít (például robot lánctalpas). |
| [Intelligens televízió?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) | Azonosítja a kérelmeket, hogy az eszköz intelligens TV-e. |
| [Az okostelefon?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) | Azonosítja a kérelmeket, hogy az eszköz egy okostelefon-e.
| [Tabletta?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) | Azonosítja a kérelmeket, ha az eszköz egy tabletta. |
| [Érintőképernyő?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) | Azonosítja a kérelmeket, ha az eszköz elsődleges mutató eszköze egy érintőképernyő. |
| [Windows Phone-telefon?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) | Azonosítja a kérelmeket, ha az eszköz Windows Mobile 6.5/Windows Phone-telefon 7 vagy újabb. |
| [Vezeték nélküli eszköz?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) | Azonosítja a kérelmeket, ha az eszköz vezeték nélküli. 
| Marketing neve | Azonosítja a kérelmeket, hogy az eszköz marketing neve egyezik-e: <br> **-** Megadott érték (a [marketing neve literálos](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm)) <br> **-** Reguláris kifejezés ([marketing neve regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm)) <br> **-** Konkrét minta ([marketing neve helyettesítő karakter](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm)) |
| Mobil böngésző | Azonosítja a kérelmeket, hogy az eszköz böngészője megfelel-e az alábbiaknak: <br> **-** Megadott érték (a [mobil böngésző literális](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm)) <br> **-** Reguláris kifejezés ([Mobile Browser regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm)) <br> **-** Adott minta ([Mobile Browser helyettesítő karakter](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm)) |
| Mobil böngésző verziója | Azonosítja a kérelmeket, hogy az eszköz böngésző verziója megfelel-e az alábbiaknak: <br> **-** Megadott érték (a [mobil böngésző verziója literál](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm)) <br> **-** Reguláris kifejezés (a [mobil böngésző verziója regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm)) <br> **-** Adott minta (a [mobil böngésző verziója helyettesítő karakter](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm)) |
| Modell neve | Azonosítja a kérelmeket, hogy az eszköz modellének neve megegyezik-e: <br> **-** Adott érték ([modell neve literál](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm)) <br> **-** Reguláris kifejezés ([modell neve regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm)) <br> **-** Adott minta ([modell neve helyettesítő karakter](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm)) |
| [Progresszív letöltés?](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) | Azonosítja a kérelmeket, hogy az eszköz támogatja-e a progresszív letöltést. |
| Kiadás dátuma | Meghatározza a kérelmeket, hogy az eszköz kiadási dátuma egyezik-e: <br> **-** Adott érték (a [kiadási dátum konstansa](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm)) <br> **-** Reguláris kifejezés (a [kiadás dátuma regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm)) <br> **-** Adott minta ([kiadási dátum helyettesítő karakter](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm)) |
| [Felbontás magassága](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) | Azonosítja a kérelmeket az eszköz magassága alapján. |
| [Felbontás szélessége](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) | Azonosítja a kérelmeket az eszköz szélessége alapján. |

**[Vissza a tetejére](#top)**

### <a name="location"></a><a name="location"></a>Hely

Ezek a megfeleltetési feltételek a kérelmek azonosítására szolgálnak a kérelmező helye alapján.

| Name       | Cél                                                           |
|------------|-------------------------------------------------------------------|
| [AS szám](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | Az adott hálózatból származó kérelmeket azonosítja. |
| Város neve | Azonosítja a kérelmeket attól, hogy olyan városból származnak-e, amelynek a neve megegyezik a következővel: <br> **-** Megadott érték ([város neve literál](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm)) <br> **-** Reguláris kifejezés ([város neve regex](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm)) |
| [Kontinens](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | A megadott kontinensekről származó kérelmeket azonosítja. |
| [Ország](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | A megadott országokból származó kérelmeket azonosítja. |
| [DMA-kód](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) | Azokat a kérelmeket azonosítja, amelyek a megadott metróból származnak (a kijelölt piac területéről). |
| [Szélesség](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) | A megadott földrajzi szélességből származó kérelmeket azonosítja. |
| [Hosszúság](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) | A megadott hosszúsági körökből származó kérelmeket azonosítja. |
| [Metro Code](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) | Azokat a kérelmeket azonosítja, amelyek a megadott metróból származnak (a kijelölt piac területéről). |
| [Irányítószám](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | A megadott postai kódokból származó kérelmeket azonosítja. |
| [Régiókód](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) | A megadott régiókból származó kérelmeket azonosítja. |

> [!NOTE]
> **A Metro Code vagy a DMA kódját kell használnia?** <br>
Mindkét egyeztetési feltétel ugyanazokat a képességeket biztosítja. Javasoljuk azonban, hogy a Metro Code Match feltételt használja a DMA-kérelmek azonosítására.

**[Vissza a tetejére](#top)**

### <a name="origin"></a><a name="origin"></a>Származási

Ezek a megfeleltetési feltételek olyan kérelmek azonosítására szolgálnak, amelyek a CDN-tárolóra vagy egy ügyfél-forrás kiszolgálóra mutatnak.

| Name       | Cél                                                           |
|------------|-------------------------------------------------------------------|
| [CDN-forrás](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | A CDN-tárolón tárolt tartalomra vonatkozó kérelmeket azonosítja. |
| [Ügyfél forrása](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | Az adott ügyfél-kiszolgálón tárolt tartalomra vonatkozó kérelmeket azonosítja. |

**[Vissza a tetejére](#top)**

### <a name="request"></a><a name="request"></a>Kérés

Ezek a megfeleltetési feltételek a kérelmeknek a tulajdonságok alapján történő azonosítására szolgálnak.

| Name              | Cél                                                                |
|-------------------|------------------------------------------------------------------------|
| [Ügyfél IP-címe](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | Az adott IP-címről származó kérelmeket azonosítja. |
| Cookie paraméter  | Azonosít egy kérést, hogy tartalmazza-e a megfelelő cookie-t: <br> **-** Adott érték (a [cookie paraméter literál](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm)) <br> **-** Reguláris kifejezés (a [cookie paraméterének regexje](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm) <br> **-** Adott minta ([cookie-paraméter helyettesítő karaktere](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm)) |
| [Edge CNAME](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) | A megadott peremhálózati CNAME-re mutató kérelmeket azonosítja. |
| Hivatkozó tartomány | A kérelmet egy olyan állomásnév alapján azonosítja, amely megfelel a-nek: <br> **-** Megadott érték ([hivatkozó tartományi literál](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm)) <br> **-** Konkrét minta ([hivatkozó domain helyettesítő karakter](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm)) |
| Kérelem fejléce | Azonosít egy kérést, hogy tartalmaz-e egy fejlécet, amely megfelel a: <br> **-** Megadott érték (a [kérelem fejlécének literálja](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm)) <br> **-** Reguláris kifejezés ([kérelem fejlécének regexje](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm)) <br> **-** Adott minta ([kérelem fejlécének helyettesítő karaktere](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm)) |
| [Kérelem metódusa](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | A HTTP-metódussal azonosítja a kérelmeket. |
| [Kérési séma](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | A HTTP protokollal azonosítja a kérelmeket. |

**[Vissza a tetejére](#top)**

### <a name="url"></a><a name="url"></a>URL-cím

| Name              | Cél                                                                |
|-------------------|------------------------------------------------------------------------|
| URL-cím | A kérelmeket a relatív elérési úttal, például a fájlnévvel együtt azonosítja: <br> **-** Adott érték ([URL-cím konstans](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm)) <br> **-** Reguláris kifejezés ([URL-elérési út regex](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm)) <br> **-** Adott minta ([URL-elérési út helyettesítő karaktere](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm)) |
| URL elérési útja könyvtár | Meghatározza a kérelmeket, hogy relatív elérési útja megfelel-e: <br> **-** Adott érték ([URL-cím-könyvtár literál](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm)) <br> **-** Adott minta ([URL-elérésiút-könyvtár helyettesítő karaktere](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm)) |
| URL-elérési út kiterjesztése | Meghatározza a kérelmeket, hogy a fájlkiterjesztés megfelel-e a fájlnak: <br> **-** Adott érték ([URL-elérésiút-kiterjesztés literál](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm)) <br> **-** Adott minta ([URL-elérésiút-bővítmény helyettesítő karaktere](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm)) |
| URL elérési útja fájlnév | Meghatározza a kérelmeket, hogy a fájlnév megfelel-e az alábbiaknak: <br> **-** Adott érték ([URL-cím konstans neve](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm)) <br> **-** Adott minta ([URL-elérési út fájlnév helyettesítő karakter](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm)) |
| URL-lekérdezés | Azokat a kérelmeket azonosítja, amelyeknek a lekérdezési karakterlánca megfelel a: <br> **-** Adott érték ([URL-lekérdezési literál](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm)) <br> **-** Reguláris kifejezés ([URL-lekérdezési regex](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm)) <br> **-** Adott minta ([URL-lekérdezés helyettesítő karaktere](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm)) |
| URL-lekérdezési paraméter | Azokat a kérelmeket azonosítja, amelyeknek a lekérdezési karakterlánc paramétere egy olyan értékre van beállítva, amely megfelel a következőnek: <br> **-** Megadott érték ([URL-lekérdezési paraméter literál](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm)) <br> **-** Adott minta ([URL-lekérdezési paraméter helyettesítő karakter](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm)) |

**[Vissza a tetejére](#top)**

A legutóbbi egyeztetési feltételekről a [Verizon Rules Engine dokumentációjában](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio)talál további információt.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Content Delivery Network áttekintése](cdn-overview.md)
- [Szabályok motor referenciája](cdn-verizon-premium-rules-engine-reference.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Szabálymotor funkciói](cdn-verizon-premium-rules-engine-reference-features.md)
- [Az alapértelmezett HTTP-viselkedés felülbírálása a szabályok motor használatával](cdn-verizon-premium-rules-engine.md)
