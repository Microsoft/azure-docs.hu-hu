---
title: Üzenetküldési szabályzat
titleSuffix: An Azure Communication Services concept document
description: További információ az SMS üzenetküldési szabályzatokról.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/19/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bb9765c2620f45d67bf888f8bfe8a4dee450cfd6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646762"
---
# <a name="azure-communication-services-messaging-policy"></a>Azure kommunikációs szolgáltatások üzenetkezelési szabályzata

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Az Azure kommunikációs szolgáltatások olyan gazdag, egyéni kommunikációs élmények kialakításával alakítják át az ügyfeleket ügyfeleiknek, akik a Microsoft csapatainak és a Skype-nak köszönhetően azonos nagyvállalati szintű szolgáltatásokat dolgoznak ki. Az SMS üzenetkezelés funkcióit integrálhatja kommunikációs megoldásaiba, így bármikor és bárhol elérheti ügyfeleit. Csak néhány üzenetküldési követelményt kell szem előtt tartania az első lépésekhez.

Tudjuk, hogy az üzenetkezelési követelmények ijesztőek lehetnek a tanuláshoz, de olyan egyszerűek, mint a "COMS" Megjegyzés:

- C – beleegyezett
- O-Opt-Out
- M-üzenet tartalma
- S – hamisítás

Ezt az üzenetkezelési szabályzatot fejlesztettük ki a szabályozási követelmények kielégítése és a javasolt ajánlott eljárások összehangolása érdekében. 

[!INCLUDE [Notice](../../includes/messaging-policy-include.md)]

## <a name="consent"></a>Hozzájárulás 

### <a name="what-is-consent"></a>Mi a beleegyezik?

A hozzájárulás az Ön és az üzenet címzettje közötti megállapodás, amely lehetővé teszi, hogy automatizált üzeneteket küldjön nekik. Az első üzenet elküldése előtt jóvá kell hagynia a beleegyezését, és egyértelművé kell tennie a címzettnek, hogy fogadja az üzeneteket. Ez az eljárás "előzetes kifejezett beleegyezés" néven ismert.

Az elküldött üzeneteknek ugyanolyan típusú üzeneteknek kell lenniük, mint amelyeket a címzett megfogadott, és csak a címzett által megadott számra kell elküldeni. Ha tájékoztató üzeneteket szeretne küldeni, például a kinevezési emlékeztetőket vagy a riasztásokat, akkor a beleegyező engedély írható vagy szóbeli lehet. Ha olyan promóciós üzeneteket szeretne küldeni, mint például az értékesítés vagy a marketing üzenet, amely egy terméket vagy szolgáltatást reklámoz, akkor a beleegyező nevet kell írni.

### <a name="how-do-you-obtain-consent"></a>Hogyan szerezhet be hozzájárulásukat?

A beleegyezikség többféle módon is beszerezhető, például:

- Ha a felhasználó telefonszámát egy webhelyre írja be, 
- Ha a felhasználó szöveges üzenetváltást kezdeményez, vagy
- Amikor a felhasználó a telefonszámra küld egy regisztrációs kulcsszót. 
 
Függetlenül attól, hogy a hozzájárulás Hogyan szerezhető be, Önnek és ügyfeleinek biztosítaniuk kell, hogy a hozzájárulás egyértelmű legyen. A beleegyezikség hatókörét törölni kell a címzett számára.


### <a name="consent-requirements"></a>Beleegyező követelmények:

- A beleegyező művelet beszerzése előtt adja meg a "cselekvés meghívása" lehetőséget. Önnek és ügyfeleinek a "hívás a művelethez" üzenetet kell adniuk a potenciális üzenet címzettjeinek, amely meghívja őket az üzenetkezelési programba való bejelentkezésre. A művelet meghívásához legalább a következőknek kell szerepelniük: (1) az üzenet feladójának identitása, (2) Clear opt-in utasítások, (3) letiltási utasítások és (4) minden kapcsolódó üzenetküldési díj.
- A beleegyezikség nem átruházható vagy nem rendelhető hozzá. A magánszemélyek által biztosított bármely beleegyezikés nem ruházható fel és nem értékesíthető független harmadik fél számára. Ha egy harmadik fél beleegyezik, akkor egyértelműen azonosítania kell a harmadik féltől származó személyt. Azt is meg kell határoznia, hogy az Ön által beszerzett beleegyezikés csak a harmadik féltől származó kommunikációra vonatkozik.
- A belefoglalt engedély korlátozott. Az a személy, aki egy adott célra megadja a számát, hozzájárul ahhoz, hogy csak az adott célra és az adott üzenet küldőjének kapjon kommunikációt. A beleegyezikés megkezdése előtt egyértelműen értesíteni kell a kívánt üzenetet a címzettről, ha ismétlődő üzeneteket vagy üzeneteket küld egy partnertől.

### <a name="consent-best-practices"></a>Ajánlott eljárások:

A fent ismertetett üzenetküldési követelmények mellett érdemes lehet több gyakori ajánlott eljárást is megvalósítani, például a következőket: 

- Részletes "a művelet meghívása" információ. A megfelelő engedély beszerzésének biztosításához adja meg a következőt
  - az üzenetkezelő program vagy termék neve vagy leírása
  - azon szám (ok), amelyből a címzett üzeneteket fog kapni, és 
  - bármely vonatkozó feltétel és kikötés, mielőtt egy egyéni felhasználó befogadja az üzeneteket.
- Pontos beleegyező rekordok. Meg kell őriznie azokat a rekordokat, amelyeket egy adott személy legalább négy évig biztosít Önnek. A belefoglalási rekordok a következők lehetnek:
  - időbélyegei
  - a médium, amellyel a beleegyezik
  - az a konkrét kampány, amelyhez beleegyezik a beleegyezett
  - Képernyőfelvételek
  - a hozzájárulást küldő személy munkamenet-azonosítója vagy IP-címe.
- Adatvédelmi és biztonsági szabályzatok. A fejlesztőknek javasoljuk, hogy egyértelmű adatvédelmi szabályzatokat nyújtsanak, amelyeket az üzenet címzettjei megtekinthetnek a hozzájárulásukat a beszerzésük előtt. Javasoljuk továbbá a proaktív biztonsági ellenőrzések fenntartását az egyéni személyes információk védelme érdekében.


## <a name="double-opt-in-consent"></a>Dupla opt-in jóváhagyás:

Az Azure kommunikációs szolgáltatás azt javasolja, hogy az összes üzenetküldési kampányhoz használjon kettős jóváhagyási jóváhagyást. A kettős opt-in jóváhagyás egy kétlépéses folyamat, amelyben az egyes első beleegyezik, hogy bizonyos típusú üzeneteket kapjon. Ezután elküldheti a jóváhagyást követő utólagos üzenetet. Csak akkor küldjön több üzenetet, ha az üzenet címzettjei jóváhagyják a hozzájárulásukat.

Az elküldött kezdeti megerősítő üzenetnek tartalmaznia kell az identitását, a jövőbeli üzenetek letiltásának lehetőségét (például a "Leállítás" parancs használatát), a díjmentes számot vagy a "Súgó" parancsot a további információkért, az értesítéseket, amelyekkel az adott személy regisztrálva van egy ismétlődő üzenetben, a program rövid leírását, valamint azt a gyakoriságot, amellyel ismétlődő üzeneteket szeretne küldeni és minden kapcsolódó díj. 

### <a name="does-azure-communication-services-ever-require-double-opt-in-consent"></a>Szükség van-e az Azure kommunikációs szolgáltatásokra dupla opt-jóváhagyásra?
Igen, míg az Azure kommunikációs szolgáltatások esetében mindig ajánlott a kettős opt-in jóváhagyás, ha az adathalászat-sémákban való gyakori használatuk, illetve a fogyasztói panaszokat okozó tendencia miatt a kettős opt-in használatát igényli. Ezek a kampányok a következők:
- Automatikus jótállási üzenetek
- Rövid távú egészségbiztosítási csomagok
- Adósság-újrafinanszírozás vagy kamatláb-csökkentési üzenetek, ha nem pénzügyi intézmény
- Ólom-létrehozási üzenetek
- Nyereményjátékok, versenyek és Giveaway
- Work-From-Home ajánlatok

Az Azure kommunikációs szolgáltatások belátása szerint változhatnak azok a kampányok, amelyekhez szükség van a kettős jóváhagyásra.

### <a name="exceptions-to-traditional-consent-rules"></a>A hagyományos engedélyezési szabályok alóli kivételek:
Habár a korábbi kifejezett beleegyezést általában az üzenet elküldése előtt kell megadnia, a rendszer két olyan helyzetet is felvesz, amelyekben a rendszer beleegyezik az üzenetbe egy egyedi

- A címzett kommunikációt kezdeményez. Ha egy személy egy üzenetet küld Önnek, akkor az üzenetben megadott lekérdezésre vagy kérelemre adott válaszként megadhatja a megfelelő információkat. Azonban a hallgatólagos beleegyezik, hogy a megadott személy kizárólag az adott személy által kezdeményezett beszélgetésre korlátozódik, hacsak nem kap beleegyezett a további kommunikációhoz.
- Bizonyos szolgáltatások alóli kivételek. Több konkrét szolgáltatás is van, amelyekhez hallgatólagosan beleegyezik egy üzenet elindítására. A leggyakoribb ilyenek a következők: 
- csomag kézbesítési üzenetei
- az időérzékeny témákat (például a lehetséges csalárd tranzakciókat vagy az adatok megszegését) érintő pénzügyi intézmények üzenetei
- az egészségügyi szolgáltatók üzenetei, amelyek időérzékeny információkat és kezelési célokat tartalmaznak (például a kinevezési vagy vizsga-emlékeztetők, a labor eredményei és a vényköteles értesítések). 
 
Ezen üzenetek egyike sem tartalmazhat kérelmeket vagy hirdetményeket.


## <a name="opt-out"></a>Letiltás

Az üzenet címzettjei visszavonják a beleegyezést, és a jövőbeli üzenetek fogadását bármilyen ésszerű módon eldönthetik. Nem jelölhet ki kizárólagos módon az üzenet címzettjeinek, hogy visszavonják a beleegyezést. 

### <a name="opt-out-requirements"></a>Letiltási követelmények:

Győződjön meg arról, hogy az üzenet címzettjei bármikor lefoglalhatják a jövőbeli üzeneteket. Több letiltási lehetőség is elérhető. Ha egy üzenet címzettje kilép, akkor ne küldjön további üzeneteket, kivéve, ha az egyén megújított beleegyezett.

Az egyik leggyakoribb letiltási módszer az, hogy a "Leállítás" kulcsszót minden új beszélgetés kezdeti üzenetében tartalmazza. Készüljön fel arra, hogy eltávolítsa azokat az ügyfeleket, akik egy kisbetűs "Leállítás" vagy más gyakori kulcsszavakat (például "leiratkozás" vagy "Mégse") választanak. Egy egyéni visszavonás után távolítsa el őket az összes ismétlődő üzenetküldési kampányból, kivéve, ha kifejezetten megválasztják, hogy egy adott programból is fogadják az üzeneteket.

### <a name="opt-out-best-practices"></a>Ajánlott eljárások:

A kulcsszavakon kívül más gyakori letiltási mechanizmusok is magukban foglalják az ügyfelek számára a kijelölt letiltási e-mail-címet, az ügyfélszolgálati munkatárs telefonszámát, vagy egy, a weblapon való lemondásra mutató hivatkozást. 


### <a name="how-we-handle-opt-out-requests"></a>A lekéréses kérelmek kezelése:

Ha az Azure-beli kommunikációs szolgáltatások díjmentes számának letiltására irányuló egyéni kérések nem állnak fenn, akkor az ettől a számtól érkező további forgalom automatikusan le lesz állítva. Azonban továbbra is gondoskodnia kell arról, hogy ne küldjön további üzeneteket az adott üzenetküldési kampányhoz új vagy különböző számokból. Ha külön kapott kifejezett beleegyezett egy másik üzenetküldési kampányhoz, akkor továbbra is más számú üzenetet küldhet az adott kampánynak. Tekintse meg a gyakori kérdések szakaszt, ha többet szeretne megtudni a [letiltások kezeléséről](https://github.com/Prakulka/azure-docs-pr/blob/master/articles/communication-services/concepts/telephony-sms/sms-faq.md#how-can-i-receive-messages-using-azure-communication-services)

## <a name="message-content"></a>Üzenet tartalma

### <a name="adult-content"></a>Felnőtt tartalom:

A Sex, a Hate, az alkohol, a lőfegyverek, a dohány, a szerencsejátékok, a nyereményjátékok és a versenyek elemeit tartalmazó üzenetek további követelményeket is kiválthatnak. Ezt a tartalmat kifejezetten tiltja bizonyos joghatóságok. Ha olyan üzenetet küld, amely tartalmazza ezt a tartalmat, akkor az Ön kötelessége, hogy betartsa az összes, a kommunikációt fogadó joghatóság hatálya alá tartozó törvényt. A bűnüldöző szervek vagy az Azure kommunikációs szolgáltatások kérésére elő kell készítenie, hogy igazolja a betartást a felnőtt tartalmat szabályozó helyi jogszabályokkal.

Még akkor is, ha az ilyen tartalom nem törvénytelen, egy kor-ellenőrzési mechanizmust is be kell vonnia a felnőtt tartalomból a kívánt üzenetbe. A Egyesült Államok további jogi követelmények vonatkoznak a 13 évnél fiatalabb gyermekekre irányuló marketing-kommunikációra. 

### <a name="prohibited-content"></a>Tiltott tartalom:

Az Azure kommunikációs szolgáltatás a beleegyezéstől függetlenül tiltja bizonyos üzenetek tartalmát. A tiltott tartalom a következőket tartalmazza:
- A jogellenes tevékenységeket előmozdító tartalom (például adókijátszás vagy állati kegyetlenség a Egyesült Államok)
- Gyűlöletkeltő beszéd, becsületsértő beszéd, zaklatás vagy más beszéd, amely szabadalmi offenzíva
- Pornográf tartalom
- Obszcén vagy vulgáris tartalom
- Megfélemlítés és fenyegetések
- Olyan tartalom, amely félrevezető, megtévesztő, kárt okoz, vagy jogosulatlanul megszerezhet bármilyen értéket 
- Ártalmas, diszkriminációs vagy erőszakos tartalmú tartalom
- Kártevőket átterjedő tartalom
- A kapuzás követelményeinek megkerülésére szolgáló tartalom

Fenntartjuk a jogot arra, hogy bármikor módosítsák a tiltott üzenetekre vonatkozó tartalom listáját.

## <a name="spoofing"></a>Identitáshamisítás

A hamisítás azt eredményezi, hogy egy félrevezető vagy pontatlan, az üzenet címzettjeinek megjelenített számát okozza. Határozottan visszatartjuk Önnek és minden olyan szolgáltatót, amelyet a hamisított üzenetek küldésekor használ. A hamisítás megvédi az üzenet feladójának identitását, és megakadályozza, hogy az üzenetek címzettjei könnyedén letiltsák a nemkívánatos kommunikációt. Azt is megköveteli, hogy az összes vonatkozó hamisítási törvényt betartsa.

## <a name="final-thoughts"></a>Végső gondolatok

### <a name="legal-responsibility"></a>Jogi felelősség:

Ez az üzenetkezelési szabályzat nem jelent jogi tanácsot, és fenntartjuk a jogot arra, hogy bármikor módosíthassa a szabályzatot. Az Azure kommunikációs szolgáltatásai nem felelősek annak biztosításáért, hogy az ügyfél üzeneteinek tartalma, ütemezése vagy címzettjei megfelelnek az összes vonatkozó jogi követelménynek. 

Ügyfeleink felelősek az összes üzenetküldési követelményért. Ha olyan platformot vagy szoftvergyártót használ, amely az Azure kommunikációs szolgáltatásokat használja üzenetküldési célokra, akkor meg kell követelnie, hogy az ügyfelek az ebben az üzenetküldési szabályzatban tárgyalt összes követelménynek is eleget tegyenek. További útmutatásért a CTIA hasznos [üzenetkezelési elveket és ajánlott eljárásokat](https://api.ctia.org/wp-content/uploads/2019/07/190719-CTIA-Messaging-Principles-and-Best-Practices-FINAL.pdf)biztosít.

### <a name="penalties"></a>Következményekkel járhat

Javasoljuk ügyfeleinknek, hogy olyan szabályzatokat és eljárásokat fejlesszenek ki és vezessenek be, amelyek biztosítják az összes üzenetkezelési követelménynek való megfelelést. Az üzenetküldési követelmények megsértése jelentős pénzbírságot eredményezhet, amely gyorsan elvégezhető. Érdemes megtanulnia és betartania az összes vonatkozó üzenetkezelési követelményt, és hatékony kockázatcsökkentő védelmet kell kidolgoznia, hogy a rendszer a terjesztés előtt is tartalmazzon és elhárítson megsértéseket. Ha megszegi az üzenetkezelési szabályzatot vagy más jogi követelményt, akkor a jövőben a megfelelőség biztosítása érdekében együttműködünk Önnel. Fenntartjuk azonban a jogot arra, hogy az Azure kommunikációs szolgáltatások platformról távolítson el minden ügyfelet, aki az üzenetkezelési szabályzattal vagy a jogi követelményekkel való megfelelőségi mintát mutatja be.
