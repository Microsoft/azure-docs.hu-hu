---
title: Ajánlott eljárások felhasználók Face-szolgáltatáshoz való hozzáadásához
titleSuffix: Azure Cognitive Services
description: A felhasználók arcfelismerési szolgáltatásban való regisztrálásának folyamata.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: e710771dd14b389e856e752b6587e2a76ad9d85b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505071"
---
# <a name="best-practices-for-adding-users-to-a-face-service"></a>Ajánlott eljárások felhasználók Face-szolgáltatáshoz való hozzáadásához

Ha arcellenőrzéshez vagy azonosításhoz Cognitive Services Face API-t, arcokat kell regisztrálnia a **LargePersonGroup csoportba.** Ez a behatóan bemutatja az ajánlott eljárásokat, amelyek segítségével értelmes hozzájárulásokat gyűjthet a felhasználóktól, valamint olyan példalogikát, amely kiváló minőségű regisztrációkat hoz létre, amelyek optimalizálják a felismerés pontosságát.  

## <a name="meaningful-consent"></a>Jelentéssel bíró hozzájárulás 

Egy arcfelismerési regisztrációs alkalmazás egyik fő célja, hogy lehetőséget biztosít a felhasználóknak arra, hogy engedélyt adjanak az arcképeik adott célra, például egy worksite-hoz való hozzáférésre. Mivel az arcfelismerési technológiák érzékeny személyes adatok gyűjtésének tekintik őket, különösen fontos, hogy a hozzájárulásukat átlátható és tiszteletben látó módon kérje. A hozzájárulás akkor értelmezhető a felhasználók számára, ha lehetővé teszi számukra, hogy meghozják a nekik legmegfelelőbb döntést.   

A Microsoft felhasználói kutatásai, a Microsoft Felelősségteljes AI-alapelvei és külső kutatásai alapján úgy találtuk, hogy a hozzájárulás értelmes, ha a következőket kínálja a technológiában regisztrált felhasználók számára: [](ftp://ftp.cs.washington.edu/tr/2000/12/UW-CSE-00-12-02.pdf)

* Tudatosság: A felhasználóknak kétségtelenül meg kell adniuk arcsablonjukat vagy beléptetési fényképeiket. 
* Megértés: A felhasználóknak képesnek kell lennie pontosan leírni a saját szavaikban, hogy mire, kire, milyen célra és milyen biztosítékokkal kérték őket. 
* Szabad választás: A felhasználóknak nem szabad úgy éreznünk, hogy megkeserülnek vagy manipulálnak, amikor eldöntik, hogy beleegyeznek-e, és regisztrálnak-e arcfelismerésre. 
* Vezérlés: A felhasználók bármikor visszavonhatják a hozzájárulásukat, és törölhetik az adataikat. 

Ez a szakasz egy arcfelismerési regisztrációs alkalmazás fejlesztésével kapcsolatos útmutatást nyújt. Ez az útmutató a Microsoft olyan felhasználói kutatásai alapján lett kifejlesztve, amelyekben arcfelismerést végeznek az épületbe való belépéshez. Ezért előfordulhat, hogy ezek a javaslatok nem vonatkoznak az összes arcfelismerési megoldásra. A Face API felelős használata erősen függ attól a konkrét környezettől, amelyben integrálva van, ezért a javaslatok rangsorolását és alkalmazását az adott forgatókönyvhöz kell igazítani. 

> [!NOTE]
> Az Ön felelőssége, hogy a regisztrációs alkalmazást a helyi jogszabályoknak megfelelő jogi követelményekhez igazítsa, és pontosan tükrözze az összes adatgyűjtési és -feldolgozási gyakorlatot.

## <a name="application-development"></a>Alkalmazásfejlesztés 

A regisztrációs folyamat megtervezése előtt gondolja át, hogy a most felhozott alkalmazás hogyan tudja megvédeni a felhasználók számára az adataik védelmével kapcsolatos ígéreteket. Az alábbi javaslatok segítségével olyan regisztrációs élményt építhet ki, amely felelős megközelítéseket tartalmaz a személyes adatok védelméhez, a felhasználók adatainak kezeléséhez és annak biztosításához, hogy az alkalmazás minden felhasználó számára elérhető legyen.  

|Kategória | Javaslatok |
|---|---|
|Hardver | Vegye figyelembe a regisztrációs eszköz kameraminőségét. |
|Ajánlott regisztrációs funkciók | Tartalmaz egy bejelentkezési lépést többtényezős hitelesítéssel.</br></br>A Face API-ból származó arcsablon-azonosítóval (más néven személyazonosítóval) csatolja a felhasználói adatokat, például egy aliast vagy egy azonosítót. Ez a leképezés szükséges egy felhasználó regisztrációja lekéréséhez és kezeléséhez. Megjegyzés: a személyazonosítót titkos kódként kell kezelni az alkalmazásban.</br></br>Állítson be egy automatizált folyamatot az összes regisztrációs adat törléséhez, beleértve az arcsablonokat és a regisztrációs fényképeket, amelyeken olyan személyek is vannak, akik már nem arcfelismerési technológiát alkalmaznak( például korábbi alkalmazottak).</br></br>Kerülje az automatikus regisztrációt, mivel ez nem teszi a felhasználó számára a hozzájárulás megszerzéséhez javasolt tudatosságot, megértést, választási lehetőséget vagy irányítást. </br></br>Kérjen engedélyt a felhasználóktól a regisztrációhoz használt rendszerképek mentésére. Ez akkor lehet hasznos, ha modellfrissítés történik, mivel körülbelül 10 hónaponként új regisztrációs fényképekre lesz szükség az új modellben való újraregisztráláshoz. Ha az eredeti rendszerképek nincsenek mentve, a felhasználóknak a kezdetektől végig kell menniük a regisztrációs folyamaton.</br></br>A fényképek rendszeren való tárolásának lemondása a felhasználók számára. A választás egyértelműbb legyen, ha hozzáad egy második jóváhagyási kérelem képernyőt a regisztrációs fényképek mentésére. </br></br>Ha a rendszer menti a fényképeket, hozzon létre egy automatizált folyamatot, amely minden felhasználót újra beléptet a modell frissítése során. Azoknak, akik elmentették a regisztrációs fényképeiket, nem kell újra regisztrálniuk magukat. </br></br>Hozzon létre egy alkalmazás-funkciót, amely lehetővé teszi a kijelölt rendszergazdák számára bizonyos minőségi szűrők felülbírálást, ha egy felhasználó regisztrációja problémát okoz. |
|Biztonság | Cognitive Services az [ajánlott eljárásokat](../cognitive-services-virtual-networks.md?tabs=portal) a felhasználói adatok titkosításához az adatok átvitele és az átvitel során. A következő további eljárások segíthetnek a felhasználók számára a regisztrációs folyamat során tett biztonsági ígéretek betartatásában. </br></br>Biztonsági intézkedéseket kell tenni annak érdekében, hogy a regisztráció során senki ne fér hozzá a személyazonosítóhoz. Megjegyzés: A PersonID-t titkosként kell kezelni a regisztrációs rendszerben. </br></br>Szerepköralapú [hozzáférés-vezérlés használata Cognitive Services.](../../role-based-access-control/overview.md) </br></br>Jogkivonat-alapú hitelesítés és/vagy közös hozzáférésű jogosultságok (SAS) használata kulcsokon és titkos kulcsokon keresztül az olyan erőforrások eléréséhez, mint az adatbázisok. A kérelem- vagy SAS-jogkivonatok használatával korlátozott hozzáférést adhat az adatokhoz a fiókkulcsok veszélyeztetése nélkül, és lejárati időt is megadhat a jogkivonathoz. </br></br>Soha ne tárolj titkos kulcsokat, kulcsokat vagy jelszavakat az alkalmazásban. |
|Felhasználói adatvédelem |Különböző regisztrációs lehetőségeket biztosít az adatvédelmi problémák különböző szintjeinek kezelése érdekében. Ne íratja be, hogy a felhasználók személyes eszközeiket használják arcfelismerési rendszerbe való regisztráláshoz. </br></br>Lehetővé teszi, hogy a felhasználók bármikor és bármilyen okból újra regisztrálják, visszavonják a hozzájárulásukat, és töröljön adatokat a regisztrációs alkalmazásból. |
|Akadálymentesség |Kövesse az akadálymentességgel kapcsolatos szabványokat (például [ADA](https://www.ada.gov/regs2010/2010ADAStandards/2010ADAstandards.htm) vagy [W3C)](https://www.w3.org/TR/WCAG21/)annak érdekében, hogy az alkalmazást a mozgássérült vagy látássérült személyek is használhatók legyen. |

## <a name="next-steps"></a>Következő lépések  

A minta [regisztrációs alkalmazással való](build-enrollment-app.md) első lépésekhez kövesse a Regisztrációs alkalmazás összeállítása útmutatót. Ezután szabja testre, vagy írja meg a saját alkalmazását a termék igényeinek megfelelően.