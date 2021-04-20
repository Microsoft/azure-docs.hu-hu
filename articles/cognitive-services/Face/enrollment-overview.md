---
title: Ajánlott eljárások felhasználók Face-szolgáltatáshoz való hozzáadásához
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan regisztrálhat felhasználókat arcfelismerési szolgáltatásban az arcfelismerési szolgáltatásban.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/19/2021
ms.author: pafarley
ms.openlocfilehash: 183983779c09658d8d6f609319a127b1f406452d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728427"
---
# <a name="best-practices-for-adding-users-to-a-face-service"></a>Ajánlott eljárások felhasználók Face-szolgáltatáshoz való hozzáadásához

Ahhoz, hogy a Cognitive Services Face API-t arcellenőrzéshez vagy azonosításhoz használjuk, arcokat kell regisztrálni egy **LargePersonGroup** vagy hasonló adatstruktúrában. Ez a behatóan bemutatja az ajánlott eljárásokat, amelyek segítségével értelmes jóváhagyást gyűjthet a felhasználóktól, és példalogikával olyan kiváló minőségű regisztrációkat hozhat létre, amelyek optimalizálják a felismerés pontosságát. 

## <a name="meaningful-consent"></a>Jelentéssel bíró hozzájárulás 

A regisztrációs alkalmazás arcfelismerésre való egyik fő célja, hogy lehetőséget biztosít a felhasználóknak arra, hogy engedélyt adjanak az arcképeik meghatározott célokra való használatára, például egy munkahelyhez való hozzáférésre. Mivel az arcfelismerési technológiák érzékeny személyes adatok gyűjtésének tekintik őket, különösen fontos, hogy a hozzájárulásukat átlátható és tiszteletben látó módon kérje. A hozzájárulás akkor értelmezhető a felhasználók számára, ha lehetővé teszi számukra, hogy meghozják a nekik legmegfelelőbb döntést.   

A Microsoft felhasználói kutatásai, a Microsoft Felelősségteljes AI-alapelvei és külső kutatásai alapján úgy találtuk, hogy a hozzájárulás értelmes, ha a következőket kínálja a technológiába regisztráló felhasználóknak: [](ftp://ftp.cs.washington.edu/tr/2000/12/UW-CSE-00-12-02.pdf)

* Tudatosság: A felhasználóknak kétségtelenül meg kell adniuk arcsablonjukat vagy regisztrációs fényképeiket. 
* Megértés: A felhasználóknak képesnek kell lennie pontosan leírni a saját szavaikban, hogy mire, kire, milyen célra és milyen garanciára volt képes. 
* Választás szabadsága: A felhasználók nem érezhetik úgy magukat, hogy el vannak kovatálva vagy manipulálva, amikor eldöntik, hogy beleegyeznek-e, és regisztrálnak-e arcfelismerésre. 
* Vezérlés: A felhasználóknak bármikor vissza kell tudni vonniuk a hozzájárulásukat, és törölniük kell az adataikat. 

Ez a szakasz egy arcfelismerési regisztrációs alkalmazás fejlesztésével kapcsolatos útmutatást nyújt. Ez az útmutató a Microsoft olyan felhasználói kutatásai alapján lett kifejlesztve, amelyek az egyének arcfelismeréssel történő regisztrálását fejlesztették ki az épületbe való belépéshez. Ezért előfordulhat, hogy ezek a javaslatok nem vonatkoznak az összes arcfelismerési megoldásra. A Face API felelősségteljes használata erősen függ attól a konkrét környezettől, amelyben integrálva van, ezért ezeknek a javaslatoknak a rangsorolását és alkalmazását az adott forgatókönyvhöz kell igazítani. 

> [!NOTE]
> Az Ön felelőssége, hogy a regisztrációs alkalmazást összehangolja a vonatkozó jogi követelményekkel a saját joghatóságában, és pontosan tükrözze az összes adatgyűjtési és -feldolgozási gyakorlatot.

## <a name="application-development"></a>Alkalmazásfejlesztés 

A regisztrációs folyamat megtervezése előtt gondolja át, hogyan tarthatja be a felhasználók számára az adatok védelmével kapcsolatos ígéreteket a felépítő alkalmazás. Az alábbi javaslatok segítségével olyan regisztrációs élményt építhet ki, amely felelős megközelítéseket tartalmaz a személyes adatok védelméhez, a felhasználók adatainak kezeléséhez és annak biztosításához, hogy az alkalmazás minden felhasználó számára elérhető legyen.  

|Kategória | Javaslatok |
|---|---|
|Hardver | Vegye figyelembe a regisztrációs eszköz kameraminőségét. |
|Ajánlott regisztrációs funkciók | Tartalmaz egy bejelentkezési lépést többtényezős hitelesítéssel. </br></br>Felhasználói adatok, például alias vagy azonosító szám összekapcsolása a Face API-ból származó arcsablon-azonosítóval (más néven személyazonosító). Ez a leképezés szükséges egy felhasználó regisztrációja lekéréséhez és kezeléséhez. Megjegyzés: a személyazonosítót titkos kódként kell kezelni az alkalmazásban.</br></br>Állítson be egy automatizált folyamatot, amely törli az összes regisztrációs adatot, beleértve az arcfelismerési technológiát már nem használó személyek, például a korábbi alkalmazottak arcfelismerési képeit és beléptetési fényképeit. </br></br>Kerülje az automatikus regisztrációt, mivel nem ad a felhasználónak a hozzájárulás megszerzéséhez javasolt tudatosságot, megértést, választáson vagy vezérlést. </br></br>Kérjen engedélyt a felhasználóktól a regisztrációhoz használt lemezképek mentésére. Ez akkor hasznos, ha modellfrissítés történik, mivel körülbelül 10 hónaponként új regisztrációs fényképekre lesz szükség az új modellbe való újraregisztráláshoz. Ha az eredeti rendszerképek nincsenek mentve, a felhasználóknak a kezdetektől végig kell menniük a regisztrációs folyamaton.</br></br>A fényképek rendszeren való tárolásának lemondása a felhasználók számára. A döntés egyértelműsítő érdekében hozzáadhat egy második jóváhagyási kérelem képernyőt a regisztrációs fényképek mentésére. </br></br>Ha a fényképek mentve vannak, hozzon létre egy automatizált folyamatot, amely újra beléptet minden felhasználót a modellfrissítéskor. A regisztrációs fényképeket mentő felhasználóknak nem kell újból regisztrálniuk magukat. </br></br>Hozzon létre egy olyan alkalmazás-funkciót, amely lehetővé teszi a kijelölt rendszergazdák számára bizonyos minőségi szűrők felülbírálást, ha a felhasználónak problémája van a regisztrációval. |
|Biztonság | Cognitive Services az [ajánlott eljárásokat](../cognitive-services-virtual-networks.md?tabs=portal) a felhasználói adatok titkosításához az átvitel közben és az adatok átvitele közben. Az alábbiakban olyan egyéb gyakorlatok is vannak, amelyek segíthetnek a felhasználók számára a regisztrációs folyamat során a felhasználók számára tett biztonsági ígéretek betartatásában. </br></br>Biztonsági intézkedéseket kell tenni annak érdekében, hogy a regisztráció során senki ne fér hozzá a személyazonosítóhoz. Megjegyzés: A PersonID-t titkosként kell kezelni a regisztrációs rendszerben. </br></br>Szerepköralapú [hozzáférés-vezérlés használata Cognitive Services.](../../role-based-access-control/overview.md) </br></br>Jogkivonat-alapú hitelesítés és/vagy közös hozzáférésű jogosultságok (SAS) használata kulcsokon és titkos kulcsokon keresztül az olyan erőforrások eléréséhez, mint az adatbázisok. A kérelem- vagy SAS-jogkivonatok használatával korlátozott hozzáférést adhat az adatokhoz a fiókkulcsok veszélyeztetése nélkül, és lejárati időt is megadhat a jogkivonathoz. </br></br>Soha ne tárolj titkos kulcsokat, kulcsokat vagy jelszavakat az alkalmazásban. |
|Felhasználói adatvédelem |Különböző regisztrációs lehetőségeket biztosít a különböző adatvédelmi problémák kezelése érdekében. Ne szabja meg, hogy a felhasználók személyes eszközeiket használják arcfelismerési rendszerekbe való regisztráláshoz. </br></br>Lehetővé teszi a felhasználók számára, hogy bármikor és bármilyen okból újra regisztrálják, visszavonják a hozzájárulásukat, és töröljön adatokat a regisztrációs alkalmazásból. |
|Akadálymentesség |Kövesse az akadálymentességgel kapcsolatos szabványokat (például [ADA](https://www.ada.gov/regs2010/2010ADAStandards/2010ADAstandards.htm) vagy [W3C)](https://www.w3.org/TR/WCAG21/)annak érdekében, hogy az alkalmazást a mozgássérült vagy látássérült személyek is használhatók legyen. |

## <a name="next-steps"></a>Következő lépések  

A minta [regisztrációs alkalmazással való](build-enrollment-app.md) első lépésekhez kövesse a Regisztrációs alkalmazás összeállítása útmutatót. Ezután szabja testre, vagy írja meg a saját alkalmazását a termék igényeinek megfelelően.