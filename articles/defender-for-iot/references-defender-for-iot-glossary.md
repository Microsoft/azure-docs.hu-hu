---
title: A Defender for IoT szószedete
description: Ez a Szószedet a fontos Defender rövid leírását tartalmazza a IoT platform használati feltételeit és fogalmait illetően.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/09/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: d983369db3fbd03c41c248439a5c1aabec14ea55
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100535365"
---
# <a name="defender-for-iot-glossary"></a>A Defender for IoT szószedete

Ez a Szószedet röviden ismerteti az Azure Defender for IoT platform fontos használati feltételeit és fogalmait. Kattintson a **További tudnivalók** hivatkozásra, hogy a kapcsolódó kifejezésekhez lépjen a szószedetben. Ez segít a termék-eszközök gyors megismerésében és használatában.

> [!Note]
> Bármely kifejezés, amelynek a `(DB)` neve szerepel, ügynök-alapú eszköz-építő kifejezés. 

<a name="glossary-a"></a>

## <a name="a"></a>A

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Hozzáférési csoport** | A hozzáférési csoport szabályainak létrehozásával támogatja a nagyméretű szervezetek felhasználói hozzáférési követelményeit.<br /><br />A szabályok segítségével szabályozhatja a Defender IoT-hozzáférését a helyszíni felügyeleti konzolhoz adott felhasználói szerepkörökhöz a megfelelő üzleti egységekben, régiókban, helyeken és zónákban.<br /><br />Tegyük fel például, hogy a biztonsági elemzők egy Active Directory csoportból érik el a Nyugat-európai autóipari adatok elérését, de megakadályozzák az adatok elérését Afrikában. | **[Helyszíni felügyeleti konzol](#o)** <br /><br />**[Üzleti egység](#b)** |
| **Hozzáférési jogkivonatok** | Hozzáférési jogkivonatok előállítása a Defender IoT REST APIhoz való hozzáféréséhez. | **[API](#glossary-a)** |
| **Riasztási esemény nyugtázása** | Utasítsa a Defendert a IoT, hogy az észlelt eseményhez egyszer elrejtse a riasztást. A riasztás újra aktiválódik, ha az eseményt újra észleli a rendszer. | **[](#glossary-a) <br /> Riasztás <br /> [Riasztási esemény](#l) <br /> <br /> megismerése [Riasztási esemény némítása](#m)** |
| **Riasztás** | Üzenet arról, hogy a IoT-kezelő Defender a hitelesítő hálózati viselkedéstől, a hálózati rendellenességektől, illetve a gyanús hálózati tevékenységtől és forgalomtól való eltérésekkel kapcsolatos. | **[Továbbítási szabály](#f) <br /> <br /> [Kizárási szabály](#e) <br /> <br /> [Rendszerértesítések](#s)** |
| **Riasztási Megjegyzés** | Megjegyzések, amelyeket a biztonsági elemzők és a rendszergazdák a riasztási üzenetekben tesznek elérhetővé. Egy riasztási Megjegyzés például megadhatja az eseményre vonatkozó kockázatcsökkentő műveletek, illetve az eseménnyel kapcsolatban álló személyek nevét.<br /><br />A riasztásokat véleményező felhasználók kiválaszthatják az esemény állapotát legjobban tükröző észrevételeket vagy megjegyzéseket, illetve a riasztás kivizsgálásához szükséges lépéseket. | **[Riasztás](#glossary-a)** |
| **Anomália motor** | A IoT motorhoz tartozó Defender, amely észleli a gépek közötti szokatlan kommunikációt és viselkedést. Előfordulhat például, hogy a motor túlzott SMB-bejelentkezési kísérleteket érzékel. Az anomáliák riasztása akkor aktiválódik, ha ezek az események észlelhetők. | **[Defender a IoT-motorokhoz](#d)** |
| **API** | Lehetővé teszi, hogy a külső rendszerek hozzáférhessenek a Defender által felderített adatokhoz a IoT számára, és műveleteket hajtsanak végre az SSL-kapcsolatok külső REST API használatával. | **[Hozzáférési jogkivonatok](#glossary-a)** |
| **Támadási vektor jelentés** | A kihasználható végpontok sebezhetőségi láncának valós idejű grafikus ábrázolása.<br /><br />A jelentések lehetővé teszik a kockázatcsökkentő tevékenységek hatásának kiértékelését a támadási sorozatban. Kiértékelheti például, hogy a rendszerfrissítés megszakítja-e a támadó elérési útját a támadási lánc megszakadásával, vagy ha egy másik támadási útvonal marad. Ez rangsorolja a Szervizelési és a kockázatcsökkentő tevékenységeket. | **[Kockázatértékelési jelentés](#r)** |

## <a name="b"></a>B

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Üzleti egység** | Az üzleti tevékenység logikai szervezete adott iparágak szerint.<br /><br />Egy üveg-és műanyag-gyárakat tartalmazó globális vállalat például két különböző üzleti egységként kezelhető. A Defender hozzáférését a IoT-felhasználók meghatározott üzleti egységekre is szabályozhatja. | **Helyszíni [felügyeleti konzol](#o) <br /> <br /> [Hozzáférési csoport](#glossary-a) <br /> <br /> [](#s) <br /> Webhely <br /> [Zóna](#z)** |
| **Alapkonfiguráció** | Jóváhagyott hálózati forgalom, protokollok, parancsok és eszközök. A IoT Defender a hálózati alaptervtől való eltéréseket azonosítja. Az adatbányászati jelentések létrehozásával megtekintheti a jóváhagyott alapterv-forgalmat. | **[Adatbányászat](#d) <br /> <br /> [Tanulási mód](#l)** |

## <a name="c"></a>C

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **CLI-parancsok** | Parancssori felület (CLI) beállításai a Defender számára a IoT-rendszergazda felhasználók számára. A CLI-parancsok olyan funkciókhoz érhetők el, amelyek nem érhetők el a Defender for IoT Consoles szolgáltatásban. | - |


## <a name="d"></a>T

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Adatbányászat** | Átfogó és részletes jelentések készítése a hálózati eszközökről:<br /><br />- **SoC incidens válasza**: a valós idejű jelentések segítenek az incidensek azonnali megválaszolásában. Egy jelentés például listázhat olyan eszközöket, amelyekhez szükség lehet a javításra.<br /><br />- **Kriminalisztika**: jelentések a korábbi adatelemzési jelentések alapján.<br /><br />- **Informatikai hálózat integritása**: jelentések, amelyek segítenek a teljes hálózati biztonság javításában. Egy jelentés például a gyenge hitelesítési hitelesítő adatokkal rendelkező eszközöket listázhatja.<br /><br />- **láthatóság**: az összes lekérdezési elemre kiterjedő jelentések a hálózat összes alapkonfigurációjának megtekintéséhez.<br /><br />Mentse az adatbányászati jelentéseket a csak olvasási jogosultsággal rendelkező felhasználók megtekintéséhez. | **[](#b) <br /> Alapterv <br /> [Jelentések](#r)** |
| **Defender a IoT-motorokhoz** | Az önfejlesztő elemzési motorok a IoT Defenderban nem szükségesek az aláírások frissítéséhez vagy a szabályok definiálásához. A motorok az IC-specifikus viselkedés-elemzést és adatelemzést használják a rendellenességek, kártevők, működési problémák, protokollok megsértése és a kiindulási hálózati tevékenységtől való eltérések folyamatos elemzéséhez.<br /><br />Ha egy motor eltérést észlel, riasztást vált ki. A riasztásokat a **riasztások** képernyőről vagy egy Siem-ből lehet megtekinteni és kezelni. | **[Riasztás](#glossary-a)** |
| **Defender a IoT platformhoz** | A Defender for IoT megoldás a IoT érzékelőkre és a helyszíni felügyeleti konzolra van telepítve. | **[](#s) <br /> Érzékelő <br /> Helyszíni [felügyeleti konzol](#o)** |
| **Eszköz térképe** | A IoT Defender által észlelt hálózati eszközök grafikus ábrázolása. Megjeleníti az eszközök közötti kapcsolatokat és az egyes eszközökre vonatkozó információkat. A térképen a következőt használhatja:<br /><br />– A kritikus eszközök adatainak beolvasása és vezérlése.<br /><br />– Hálózati szeletek elemzése.<br /><br />– Az eszköz adatainak és összegzésének exportálása. | **[Purdue-réteg csoport](#p)** |
| **Eszköz leltározása – érzékelő** | Az eszközök leltára a Defender által a IoT számára észlelt eszközök széles körét jeleníti meg. A következő lehetőségek érhetők el:<br /><br />– Megjelenített adatok szűrése.<br /><br />– Exportálja ezt az információt egy CSV-fájlba.<br /><br />– Importálja a Windows beállításjegyzékének részleteit. | **[Group](#g)** <br /><br />**[Eszközök leltározása – helyszíni felügyeleti konzol](#d)** |
| **Eszközök leltározása – helyszíni felügyeleti konzol** | A csatlakoztatott érzékelőkről származó információkat a helyszíni felügyeleti konzolról lehet megtekinteni az eszközök leltárában. Ez lehetővé teszi a helyszíni felügyeleti konzol számára az összes hálózati információ átfogó áttekintését. | **[Eszköz leltározása – érzékelő](#d) <br /> <br /> [Eszközök leltározása – adatintegrátor](#d)** |
| **Eszközök leltározása – adatintegrátor** | A helyszíni felügyeleti konzol adatintegrációs funkciói lehetővé teszik, hogy az eszközök leltárában lévő adatokat más vállalati erőforrásokból származó adatokkal fejlessze. Az erőforrások például CMDBs, DNS, tűzfalak és webes API-k. | **[Eszközök leltározása – helyszíni felügyeleti konzol](#d)** |
| **Eszköz ikrek**`(DB)` | Az ikrek olyan JSON-dokumentumok, amelyek az eszköz állapotával kapcsolatos információkat tárolnak, beleértve a metaadatokat, a konfigurációkat és a feltételeket. | [Modul Twin](#m) <br /> <br />[Biztonsági modul Twin](#s) |

## <a name="e"></a>E

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Vállalati nézet** | Egy globális Térkép, amely olyan üzleti egységeket, webhelyeket és zónákat mutat be, ahol a IoT érzékelők védelmezői vannak telepítve. Megtekintheti a kártékony riasztások földrajzi elhelyezkedését, az operatív riasztásokat és egyebeket. | **[Üzleti egység](#b) <br /> <br /> [](#s) <br /> Webhely <br /> [Zóna](#z)** |
| **Esemény idővonala** | A hálózaton észlelt tevékenység ütemezése, beleértve a következőket:<br /><br />– Aktivált riasztások.<br /><br />– Hálózati események (tájékoztató).<br /><br />– Olyan felhasználói műveletek, mint például a bejelentkezés, a felhasználó törlése és a felhasználók létrehozása, valamint a riasztások kezelési műveletei, például az Elnémítás, a tanulás és a nyugtázás. Elérhető az érzékelők konzolján. | - |
| **Kizárási szabály** | Utasítsa a Defendert a IoT, hogy figyelmen kívül hagyja a riasztási eseményindítókat az időszak, az eszköz címe és a riasztás neve vagy egy adott érzékelő alapján.<br /><br />Ha például biztos lehet abban, hogy egy adott érzékelő által figyelt összes eszközön a 6:30 és a 10:15 közötti karbantartási eljárás fut, akkor beállíthat egy kizárási szabályt, amely azt jelzi, hogy az érzékelő nem küld riasztást az előre meghatározott időszakban. | **[](#glossary-a) <br /> Riasztás <br /> [Riasztási esemény némítása](#m)** |

## <a name="f"></a>F

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Továbbítási szabály** | A továbbítási szabályok utasítja a Defendert, hogy a IoT küldjön riasztási információkat a partner-szállítók vagy rendszerek számára.<br /><br />Küldhet például riasztási adatokat egy splunk-kiszolgálónak vagy egy syslog-kiszolgálónak. | **[Riasztás](#glossary-a)** |

## <a name="g"></a>G

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Csoport** | Meghatározott attribútumokat (például a programozási tevékenységet vagy egy adott alhálózaton található eszközöket) tartalmazó eszközök előre definiált vagy egyedi csoportjai. Csoportok segítségével megtekintheti az eszközöket, és elemezheti az eszközöket a Defenderben a IoT.<br /><br />A csoportok megtekinthetők és létrehozhatók az eszköz-hozzárendelésből és az eszközök leltározásával. | **[Eszköz térképe](#d) <br /> <br /> [Eszköz leltározása](#d)** |

## <a name="h"></a>H

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Horizonton nyitott fejlesztési környezet** | Biztonságos IoT és ICS-eszközök, amelyek olyan tulajdonosi és egyéni protokollokat vagy protokollokat futtatnak, amelyek bármely standardtól eltérnek. A Horizon Open Development Environment (ODE) SDK használatával olyan nem ágazati beépülő modulokat fejleszthet, amelyek a megadott protokollok alapján dekódolják a hálózati forgalmat. A IoT Services védelmezői elemzik a forgalmat, hogy teljes körű monitorozást, riasztást és jelentéskészítést nyújtsanak.<br /><br />Horizont használata:<br /><br />- **Kiterjesztheti** a láthatóságot és a vezérlést anélkül, hogy frissítenie kell a Defendert a IoT platform verzióira.<br /><br />- **Gondoskodjon** arról, hogy a helyszínen külső beépülő modulként fejlessze a védett adatokat.<br /><br />- A riasztások, események és protokollok paramétereinek **honosítása** .<br /><br />Részletekért forduljon az ügyfél sikerességi képviselőjéhez. | **[Protokollok támogatása](#p) <br /> <br /> [Honosítás](#l)** |
| **Horizon egyéni riasztás** | A riasztások kezelése a vállalaton belül egyéni riasztások kiváltásával bármely protokollhoz (a Horizon-keretrendszer forgalmának megléte alapján).<br /><br />Ezek a riasztások a következő információk közlésére használhatók:<br /><br />– A forgalom észlelése protokollok és alapul szolgáló protokollok alapján egy saját horizontú beépülő modulban.<br /><br />-A protokoll mezőinek kombinációja az összes protokoll rétegből. | **[Protokolltámogatás](#p)** |

## <a name="i"></a>I

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **IoT Hub** `(DB)` | A felhőben üzemeltetett felügyelt szolgáltatás, amely központi üzenetsorként működik a IoT-alkalmazás és az általa felügyelt eszközök közötti kétirányú kommunikációhoz.  |   |
| **Integráció** | Bontsa ki a Defender for IoT képességeit, ha megosztja az eszköz adatait a partneri rendszerekkel. A szervezetek a korábban silózott biztonsági, NAC-, incidens-felügyeleti és eszközkezelés-megoldásokkal tudják felgyorsítani a rendszerszintű válaszokat, és gyorsabban csökkentik a kockázatokat. | **[Továbbítási szabály](#f)** |
| **Belső alhálózat** | A Defender által a IoT számára meghatározott alhálózati konfigurációk. Bizonyos esetekben, például belső tartományként nyilvános tartományokat használó környezetekben a Defender a IoT az összes alhálózat belső alhálózatként való feloldására is utasíthatja. Az alhálózatok a térképen és a IoT különböző Defender-jelentésekben jelennek meg. | **[Alhálózatok](#s)** |

## <a name="l"></a>L

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Riasztási esemény megismerése** | Utasítsa a Defendert a IoT, hogy engedélyezze a riasztási eseményekben észlelt forgalmat. | **[](#glossary-a) <br /> Riasztás <br /> [Riasztási esemény nyugtázása](#glossary-a) <br /> <br /> [Riasztási esemény némítása](#m)** |
| **Tanulási mód** | Az a mód, amelyet a IoT Defender a hálózati tevékenység megismerésére használ. Ez a tevékenység lesz a hálózati alapterv. A IoT Defender a telepítés után előre meghatározott időszakban marad a módban. Az a tevékenység, amely eltér a megtanult tevékenységtől, miután ez az időszak elindítja a Defender IoT-riasztásokat. | **[Intelligens informatikai tanulás](#s) <br /> <br /> [Alapterv](#b)** |
| **Honosítás** | A riasztások, események és protokoll paramétereinek honosítása a horizont által fejlesztett, nem ágazati beépülő modulok esetében. | **[Horizonton nyitott fejlesztési környezet](#h)** |

## <a name="m"></a>M


| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Micro Agent**`(DB)` | Részletes biztonsági képességeket biztosít a IoT-eszközökhöz, beleértve a biztonsági helyzeteket és a fenyegetések észlelését. | |
| **Modul Twin**`(DB)` | Az ikermodulok JSON-dokumentumok, amelyek a modulok állapotinformációit, például a metaadatokat, konfigurációkat és állapotokat tárolják. | [Ikereszközök](#d) <br /> <br />[Biztonsági modul Twin](#s) |
| **Riasztási esemény némítása** | Utasítsa a Defendert a IoT, hogy folyamatosan figyelmen kívül hagyja a tevékenységeket azonos eszközökkel és hasonló adatforgalommal. | **[](#glossary-a) <br /> Riasztás <br /> [Kizárási szabály](#e) <br /> <br /> [Riasztási esemény nyugtázása](#glossary-a) <br /> <br /> [Riasztási esemény megismerése](#l)** |

## <a name="n"></a>N

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Értesítések** | Információk a hálózati változásokról vagy a megoldatlan eszközök tulajdonságairól. Lehetőség van az eszköz-és a hálózati adatok frissítésére az észlelt új adatokkal. Az értesítésekre adott válaszok gazdagítják az eszközök leltárát, térképét és különböző jelentéseit. Elérhető az érzékelők konzolján. | **[](#glossary-a) <br /> Riasztás <br /> [Rendszerértesítések](#s)** |

## <a name="o"></a>O

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Helyszíni felügyeleti konzol** | A helyszíni felügyeleti konzol az eszközök és fenyegetések központi megtekintését és kezelését teszi lehetővé, amelyek a IoT-érzékelők üzembe helyezését a szervezeten belül észlelték. | **[Defender a IoT platformhoz](#d) <br /> <br /> [Érzékelő](#s)** |
| **Működési riasztás** | Az operatív hálózati problémákkal foglalkozó riasztások, például egy gyanús eszköz, amely a hálózatról leválasztható. | **[](#glossary-a) <br /> Riasztás <br /> [Biztonsági riasztás](#s)** |

## <a name="p"></a>P

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Purdue-réteg** | Megjeleníti a térképen egy tipikus internetkapcsolat fő összetevőinek összekapcsolását és függőségeit. |  |
| **Protokolltámogatás** | A beágyazott protokoll támogatásán kívül a IoT és az INTERNETKAPCSOLATtal rendelkező, saját és egyéni protokollokat futtató eszközök, illetve bármely standardtól eltérő protokollok is biztonságossá tehetők a Horizon Open Development Environment SDK használatával. | **[Horizonton nyitott fejlesztési környezet](#h)** |

## <a name="r"></a>R

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Régió** | Egy globális szervezet logikai részlege földrajzi régiókba. Ilyenek például a következők: Észak-Amerika, Nyugat-Európa és Kelet-Európa.<br /><br />Előfordulhat, hogy a Észak-Amerika különböző üzleti egységekből származó gyárakat tartalmaz. | **[Hozzáférési csoport](#glossary-a) <br /> <br /> [Üzleti egység](#b) <br /> <br /> Helyszíni [felügyeleti konzol](#o) <br /> <br /> [](#s) <br /> Webhely <br /> [Zóna](#z)** |
| **Jelentések** | A jelentések az adatbányászati lekérdezés eredményei által generált információkat tükrözik. Ez magában foglalja az adatbányászat alapértelmezett eredményeit is, amelyek a **jelentések** nézetben érhetők el. A rendszergazdák és a biztonsági elemzők egyéni adatbányászati lekérdezéseket is létrehozhatnak, és jelentésekként menthetik őket. Ezek a jelentések a csak olvasási jogosultsággal rendelkező felhasználók számára is elérhetők lesznek. | **[Adatbányászat](#d)** |
| **Kockázatértékelési jelentés** | A kockázatértékelési jelentéskészítés lehetővé teszi, hogy az egyes hálózati eszközök biztonsági pontszámát, valamint egy általános hálózati biztonsági pontszámot készítsen. A teljes pontszám a 100 százalékos biztonság százalékos arányát jelöli. A jelentés olyan kockázatcsökkentő javaslatokat tartalmaz, amelyek segítségével javíthatja a jelenlegi biztonsági pontszámát. | - |

## <a name="s"></a>S

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Biztonsági riasztás** | A biztonsági problémákkal foglalkozó riasztások, például a túlzott SMB-bejelentkezési kísérletek vagy a kártevők észlelése. | **[](#glossary-a) <br /> Riasztás <br /> [Működési riasztás](#o)** |
| **Biztonsági modul Twin**`(DB)` | A biztonsági modul külön tartalmazza az eszköz biztonságával kapcsolatos összes információt a megoldás minden egyes eszközén. | [Ikereszközök](#d) <br /> <br />[Modul Twin](#m)  |
| **Szelektív szondázás** | A IoT Defender passzívan vizsgálja meg az IT-t és az OT, és észleli az eszközökre, azok attribútumaira, viselkedésére és egyéb adataira vonatkozó információkat. Bizonyos esetekben előfordulhat, hogy egyes információk nem láthatók a passzív hálózati elemzésekben.<br /><br />Ha ez történik, a IoT-ben a biztonságos, részletesen megjelenő szondázás-eszközöket használhatja a korábban nem elérhető eszközökkel kapcsolatos fontos információk megtalálásához. | - |
| **Érzékelő** | Az a fizikai vagy virtuális gép, amelyen a Defender for IoT platform telepítve van. | **[Helyszíni felügyeleti konzol](#o)** |
| **Hely** | Egy gyár vagy más entitás helye. A helynek tartalmaznia kell egy zónát vagy több olyan zónát, amelyben az érzékelő telepítve van. | **[Zóna](#z)** |
| **Hely kezelése** | A helyszíni felügyeleti konzol lehetőséget, amely lehetővé teszi a vállalati érzékelők kezelését. | - |
| **Intelligens informatikai tanulás** | A tanulási időszak befejezése és a tanulási mód letiltását követően a Defender a IoT esetében szokatlanul magas szintű alapszintű változásokat érzékel, amelyek a normál informatikai tevékenységek, például a DNS és a HTTP-kérések eredménye. Előfordulhat, hogy ez a forgalom szükségtelen házirend-megsértési riasztásokat és rendszerértesítéseket vált ki. A riasztások és értesítések csökkentése érdekében engedélyezheti az intelligens IT-tanulást. | **[Tanulási mód](#l) <br /> <br /> [Alapterv](#b)** |
| **Alhálózatok** | Ha engedélyezni szeretné a fókuszt az OT-eszközökön, az IT-eszközöket a rendszer automatikusan összesíti az eszköz térképén lévő alhálózat alapján. Mindegyik alhálózat egyetlen entitásként jelenik meg a térképen, beleértve egy interaktív összecsukási vagy bővítési képességet, amely az IT-alhálózatra és a háttérre koncentrál. | **[Eszköz térképe](#d)** |
| **Rendszerértesítések** | A helyszíni felügyeleti konzolról érkező értesítések újraosztályozása:<br /><br />– Érzékelő csatlakoztatási állapota.<br /><br />– Távoli biztonsági mentés hibái. | **[](#n) <br /> Értesítések <br /> [Riasztás](#glossary-a)** |

## <a name="z"></a>Z

| Időszak | Leírás | Tudjon meg többet |
|--|--|--|
| **Zóna** | Egy hely egy olyan helyen belül, amelyben az érzékelő vagy az érzékelők telepítve vannak. | **[](#s) <br /> Webhely <br /> [Üzleti egység](#b) <br /> <br /> [Régió](#r)** |
