---
title: Mi az identitások biztonságos pontszáma? – Azure Active Directory
description: Hogyan használható a személyazonosság biztonságos pontszáma a címtár biztonsági helyzetének javítására
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f75dea2cffbe710bf2778ceab5eacc91ffcca9c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77523108"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Mi az a Azure Active Directory identitások biztonságos pontszáma?

Mennyire biztonságos az Ön Azure AD-bérlője? Ha nem tudja, hogyan válaszolja meg ezt a kérdést, ez a cikk azt ismerteti, hogyan segít a személyazonossággal kapcsolatos biztonsági helyzet monitorozásában és fejlesztésében.

## <a name="what-is-an-identity-secure-score"></a>Mi az az identitásbiztonsági pontszám?

Az identitások biztonságos pontszáma 1 és 223 közötti szám, amely indikátorként működik a Microsoft által a biztonságra vonatkozó ajánlott eljárások alapján. Az identitások biztonságos pontszámának minden fejlesztési művelete az adott konfigurációra van szabva.  

![Biztonsági pontszám](./media/identity-secure-score/identity-secure-score-overview.png)

A pontszám az alábbiakban nyújt segítséget:

- Az identitásbiztonsági rendszer objektív felmérése
- Identitásbiztonsági fejlesztések tervezése
- A fejlesztések hatásának felmérése

A pontszámot és a kapcsolódó információkat az identitásbiztonsági pontszám irányítópultján találhatja meg. Ezen az irányítópulton a következők találhatók:

- Az Ön személyazonosságának biztonságos pontszáma
- Egy összehasonlító gráf, amely bemutatja, hogyan hasonlítja össze az identitása biztonsági pontszámát az azonos iparágban és hasonló méretekben lévő többi Bérlővel
- Egy trend Graph, amely bemutatja, hogyan változott meg az identitása biztonságos pontszáma az idő múlásával
- A lehetséges tökéletesítések listája

A fejlesztési műveletek végrehajtásával a következőket érheti el:

- Javítsa ki biztonsági helyzetét és a pontszámát
- Használja ki az Ön szervezete számára elérhető funkciókat az identitás-befektetések részeként

## <a name="how-do-i-get-my-secure-score"></a>Hogyan tekinthetem meg a saját biztonsági pontszámomat?

Az identitás biztonságos pontszáma az Azure AD minden kiadásában elérhető. A szervezetek a **Azure Portal**  >  **Azure Active Directory**  >  **biztonsági**  >  **identitás biztonságos pontszámával**érhetik el a személyazonosságuk biztonságos pontszámát.

## <a name="how-does-it-work"></a>Hogyan működik?

Az Azure 48 óránként áttekinti az Ön biztonsági beállításait, és összehasonlítja azokat az ajánlott eljárásokkal. A kiértékelés eredménye alapján a rendszer új pontszámot számít ki a címtárhoz. Lehetséges, hogy a biztonsági konfiguráció nincs teljesen összhangban az ajánlott eljárási útmutatással, és a fejlesztési műveletek csak részben teljesülnek. Ezekben a forgatókönyvekben csak a vezérlő számára elérhető maximális pontszám egy részét kapja meg.

A rendszer az Ön Azure AD-konfigurációjától függően tesz javaslatokat. Ha harmadik féltől származó termékeket használ az ajánlott eljárási javaslatok engedélyezéséhez, ezt a konfigurációt egy javító művelet beállításaiban adhatja meg. Lehetősége van arra is, hogy az ajánlásokat figyelmen kívül hagyja, ha nem vonatkoznak a környezetre. A figyelmen kívül hagyott javaslatok nincsenek hatással a pontszám kiszámítására.

![Figyelmen kívül hagyható vagy megjelölhető művelet harmadik fél által lefedett módon](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>Miben nyújt segítséget?

A biztonsági pontszám az alábbiakban nyújt segítséget:

- Az identitásbiztonsági rendszer objektív felmérése
- Identitásbiztonsági fejlesztések tervezése
- A fejlesztések hatásának felmérése

## <a name="what-you-should-know"></a>Alapismeretek

### <a name="who-can-use-the-identity-secure-score"></a>Kik használhatják az identitásbiztonság pontszámot?

Az identitásbiztonság pontszámot a következő szerepkörök használhatják:

- Globális rendszergazda
- Biztonsági rendszergazda
- Biztonsági olvasók

### <a name="how-are-controls-scored"></a>Hogyan történik a vezérlők pontozása?

A vezérlőket kétféleképpen lehet kimutatni. Néhányat bináris módon kell kiszolgálni – a pontszám 100%-a, ha a funkció vagy a beállítás a javaslat alapján van konfigurálva. A többi pontszám a teljes konfiguráció százalékában számítható ki. Ha például a fejlesztési javaslat szerint 30 pontot kell védenie, ha az összes felhasználót az MFA-val védi, és Ön csak 5 100 teljes védett felhasználóval rendelkezik, akkor a részleges pontszám körülbelül 2 pont (5 védett/100 összesen * 30 Max PTS = 2 PTS részleges pontszám).

### <a name="what-does-not-scored-mean"></a>Mit jelent a [Not Scored]?

A [Not Scored] jelzéssel ellátott műveletek olyan műveletek, amelyeket elvégezhet a vállalatában, de a pontszám kiszámításakor a rendszer nem veszi őket figyelembe, mert (még!) nincsenek csatolva az eszközhöz. Így is javíthat a biztonsági beállításain, azonban a rendszer ilyen esetekben egyelőre nem veszi figyelembe ezeket a műveleteket.

### <a name="how-often-is-my-score-updated"></a>Milyen gyakran frissül a pontszám?

A pontszámot a rendszer naponta számítja újra (PST idő szerint kb. 01:00-kor). Ha változtatást eszközöl valamelyik mért műveleten, a pontszám a következő napon automatikusan frissül. Ahhoz, hogy egy változás megjelenjen a pontszámban is, akár 48 órának is el kell telnie.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Megváltozott a pontszámom. Hogyan tudhatom meg, mi ennek az oka?

Látogasson el a [Microsoft 365 Security Center](https://security.microsoft.com/)webhelyére, ahol megtalálhatja a teljes Microsoft biztonságos pontszámot. Az Előzmények lapon található részletes módosítások áttekintésével egyszerűen megtekintheti a biztonságos pontszám összes módosítását.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>A biztonságos pontszám mértékének a megsértését kockáztatom?

Röviden: nem. A biztonságos pontszám nem fejez ki abszolút mértéket arra vonatkozóan, hogy milyen valószínűséggel sérül. Ez a számérték inkább azt mutatja, milyen mértékben tett lépéseket azért, hogy az illetéktelen behatolást megakadályozó funkciókat bevezessen. Egyetlen szolgáltatás sem tudja garantálni, hogy nem fog megsérülni, és a biztonságos pontszám semmilyen módon nem értelmezhető.

### <a name="how-should-i-interpret-my-score"></a>Hogyan értelmezendő a pontszám?

Akkor kap pontokat, ha a javasolt módon konfigurálja a biztonsági funkciókat, vagy a biztonsághoz kötődő műveleteket végez (például jelentéseket olvas). Egyes műveletek részleges végrehajtása is pontot ér – ilyen például a többtényezős hitelesítés (MFA) engedélyezése a felhasználók számára. A biztonságos pontszám közvetlenül a használt Microsoft-biztonsági szolgáltatásokra jellemző. Ne feledje, hogy a biztonságnak egyensúlyban kell lennie a használhatósággal. Minden biztonsági vezérlő hatással van a felhasználókra is. A kis felhasználói hatású vezérlők elenyésző hatással vannak a felhasználók mindennapi műveleteire.

A pontszám előzményeinek megtekintéséhez lépjen a [Microsoft 365 Security Center](https://security.microsoft.com/) webhelyére, és tekintse át a Microsoft biztonsági pontszámának teljes körét. A teljes biztonsági pontszám módosításait az előzmények megtekintése lehetőségre kattintva tekintheti át. Egy adott dátumot kiválasztva megtekintheti, mely vezérlők voltak engedélyezve az adott napon, és hány pontot kapott ezekre.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Hogyan kapcsolódik az identitásbiztonsági pontszám az Office 365 biztonsági pontszámhoz?

A [Microsoft Secure pontszám](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) öt különböző vezérlőelem-és pontszám-kategóriát tartalmaz:

- Identitás
- Adatok
- Eszközök
- Infrastruktúra
- Alkalmazások

Az identitás biztonságos pontszáma a Microsoft biztonságos pontszámának Identity részét jelöli. Ez az átfedés azt jelenti, hogy az identitások biztonságos pontszámára és a Microsoft azonosító pontszámára vonatkozó javaslatok megegyeznek.

## <a name="next-steps"></a>További lépések

[További információ a Microsoft biztonságos pontszámáról](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
