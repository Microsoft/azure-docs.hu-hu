---
title: Azure Active Directory - gyakori kérdések | Microsoft Docs
description: Gyakori kérdések a jelentések Azure Active Directory kapcsolatban.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 05/12/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4da0083a236900037b388798d825515e94613c20
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533701"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Gyakori kérdések a jelentések Azure Active Directory kapcsolatban

Ez a cikk a jelentéskészítéssel (Azure AD Azure Active Directory kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza. További információk: [Jelentéskészítés az Azure Active Directoryban](overview-reports.md). 

## <a name="getting-started"></a>Első lépések 

**K: Jelenleg végponti API-kat használok az Azure AD audit- és integrált alkalmazáshasználati jelentéseinek programozott módon történő `https://graph.windows.net/<tenant-name>/reports/` leküldése a jelentéskészítő rendszerekbe. Mire váltsak?**

**A:** Keresse meg az [API-referenciát,](https://developer.microsoft.com/graph/) és nézze meg, hogyan férhet hozzá a [tevékenységjelentések API-jának használatával.](concept-reporting-api.md) Ez a végpont két jelentésből áll **(naplózás** és bejelentkezések), amelyek a régi **API-végponton** kapott összes adatot biztosítják. Ez az új végpont egy prémium szintű Azure AD-licenccel rendelkező bejelentkezési jelentéssel is rendelkezik, amely az alkalmazáshasználatra, az eszközhasználatra és a felhasználók bejelentkezési információira használható.

---

**K: Jelenleg végponti API-kat használok az Azure AD biztonsági jelentések (adott típusú észlelések, például kiszivárgott hitelesítő adatok vagy névtelen IP-címekről történő bejelentkezések) programozott módon történő lekért jelentéskészítő `https://graph.windows.net/<tenant-name>/reports/` rendszerekbe. Mire váltsak?**

**A:** Az Identity [Protection kockázatészlelési API-jának használatával](../identity-protection/howto-identity-protection-graph-api.md) hozzáférhet a biztonsági észleléshez a Microsoft Graph. Ez az új formátum nagyobb rugalmasságot biztosít az adatok lekérdezése terén, speciális szűréssel, mezőválasztással és sok más funkcióval, és egyetlen típusba szabványosítja a kockázatészleléseket a SIEM-be és más adatgyűjtési eszközökbe való egyszerűbb integráció érdekében. Mivel az adatok eltérő formátumúak, nem helyettesíthet új lekérdezéseket a régi lekérdezésekkel. Az új [API azonban a Microsoft Graph,](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true)amely a Microsoft szabványa az olyan API-khoz, mint Microsoft 365 Azure AD. A szükséges munka tehát kiterjesztheti a jelenlegi Microsoft Graph, vagy segíthet az új standard platformra való áttérés megkezdésében.

---

**K: Hogyan kap prémium szintű licencet?**

**A:** A [kiadás frissítését](../fundamentals/active-directory-get-started-premium.md) prémium szintű Azure Active Directory első Azure Active Directory lásd: Getting started with prémium szintű Azure Active Directory (Ismerkedés a Azure Active Directory kiadással.

---

**K: Milyen hamar kell látni a tevékenységek adatait a prémium szintű licenc lekért licencek után?**

**A:** Ha már rendelkezik tevékenységadatokkal ingyenes licencként, azonnal láthatja. Ha nincsenek adatai, akkor akár 3 nap is el fog tetsni, hogy az adatok meg jelen tudjanak a jelentésekben.

---

**K: Láthatom a múlt havi adatokat, miután prémium szintű Azure AD-licencet kapok?**

**A:** Ha nemrég vált prémium verzióra (beleértve a próbaverziót is), kezdetben legfeljebb 7 napig láthatja az adatokat. Ha az adatok összegyűlnek, az elmúlt 30 nap adatait láthatja.

---

**K: Globális rendszergazdának kell lennie a tevékenységbe való bejelentkezések Azure Portal vagy az API-n keresztüli adatbehozáshoz?**

**A:** Nem, a jelentési adatokat a portálon vagy az API-n keresztül is elérheti, ha **Ön** a bérlő biztonsági **olvasója** vagy biztonsági rendszergazdája. Természetesen a globális **rendszergazdák** is hozzáférhetnek ezekhez az adatokhoz.

---


## <a name="activity-logs"></a>Tevékenységnaplók


**K: Mi a tevékenységnaplók (naplózás és bejelentkezések) adatmegőrzése a Azure Portal?** 

**A:** További információ: [Adatmegőrzési szabályzatok Azure AD-jelentésekhez.](reference-reports-data-retention.md)

---

**K: Mennyi ideig tart, amíg látom a tevékenységadatokat a feladat befejezése után?**

**A:** Az auditnaplók késése 15 perc és egy óra között lehet. Egyes rekordok bejelentkezési tevékenységnaplói 15 perctől akár 2 óráig is eltarthatnak.

---

**K: Le tudom Microsoft 365 a tevékenységnapló adatait a Azure Portal?**

**A:** Bár Microsoft 365 Microsoft 365 tevékenységnaplók és az Azure AD-tevékenységnaplók sok címtárerőforrást tartalmaznak, ha teljes körű nézetet szeretne az Microsoft 365-tevékenységnaplókról, lépjen az [Microsoft 365 Felügyeleti központ-ba](https://admin.microsoft.com) az Office 365 tevékenységnapló-információinak lekért megtekintéséhez.

---

**K: Milyen API-kat használok a tevékenységnaplók Microsoft 365 le?**

**A:** A [Microsoft 365 Management API-k használatával](/office/office-365-management-api/office-365-management-apis-overview) api Microsoft 365 keresztül férhet hozzá a tevékenységnaplókhoz.

---

**K: Hány rekordot tölthetek le a Azure Portal?**

**A:** Legfeljebb 5000 rekordot tölthet le a Azure Portal. A rekordok a legújabb *szerint* vannak rendezve, és alapértelmezés szerint a legutóbbi 5000 rekordot kapják meg.

---

## <a name="risky-sign-ins"></a>Kockázatos bejelentkezések

**K: Az Identity Protection kockázatészlelést észlel, de nem látom a megfelelő bejelentkezéseket a bejelentkezési jelentésben. Ez várható?**

**A:** Igen, az Identity Protection minden hitelesítési folyamat kockázatait kiértékeli, legyen az interaktív vagy nem interaktív. Azonban minden bejelentkezés csak az interaktív bejelentkezéseket jeleníti meg.

---

**K: Hogyan tudja, miért lett kockázatosként megjelölve egy bejelentkezés vagy egy felhasználó a Azure Portal?**

**A:** Ha van egy **prémium szintű Azure AD-előfizetése,** a **mögöttes** kockázatészlelésről úgy  ismerkedhet meg, ha kiválasztja a felhasználót a Kockázatosként megjelölt felhasználók között, vagy kiválaszt egy rekordot a Kockázatos bejelentkezések jelentésben. Ha ingyenes **vagy**  alapszintű előfizetéssel rendelkezik, megtekintheti a veszélyeztetett felhasználókat és a kockázatos bejelentkezési jelentéseket, de a mögöttes kockázatészlelési információkat nem látja.

---

**K: Hogyan történik az IP-címek kiszámítása a bejelentkezések és a kockázatos bejelentkezések jelentésben?**

**A:** Az IP-címek úgy vannak kibocsátva, hogy nincs határozott kapcsolat az IP-cím és az adott címmel fizikailag megtalálható számítógép között. Az IP-címek leképezését tovább bonyolítják olyan tényezők, mint a mobilszolgáltatók és a központi készletekből IP-címeket kiállító VPN-ek, amelyek gyakran nagyon távol esnek az ügyféleszköz használatának helyéhez. Jelenleg az Azure AD-jelentésekben az IP-címek fizikai helyre konvertálása a legjobb megoldás nyomkövetések, beállításjegyzék-adatok, fordított névkeresések és egyéb információk alapján. 

---

**K: Mit jelent a kockázatészlelés "Bejelentkezés további kockázat észlelve" jele?**

**A:** Annak érdekében, hogy betekintést nyújtsunk a környezetbe történő összes kockázatos bejelentkezésbe, a "Bejelentkezés további kockázat észlelve" helyőrzőként működik az olyan észlelések esetén, amelyek kizárólag Azure AD Identity Protection előfizetők számára.

---

## <a name="conditional-access"></a>Feltételes hozzáférés

**K: A funkció újdonsága**

**A:** Az ügyfelek mostantól az összes bejelentkezési jelentésben hibaelhárítást kaphatnak a feltételes hozzáférési szabályzatokkal. Az ügyfelek áttekinthetik a feltételes hozzáférés állapotát, és részletesen áttekintheti a bejelentkezésre alkalmazott szabályzatokat és az egyes szabályzatok eredményét.

**K: Hogyan első lépések?**

**A:** Első lépések:

* Nyissa meg a bejelentkezési jelentést a [Azure Portal.](https://portal.azure.com)
* Kattintson a hibaelhárításhoz kívánt bejelentkezésre.
* Lépjen a **Feltételes hozzáférés lapra.** Itt megtekintheti az összes olyan szabályzatot, amely hatással volt a bejelentkezésre és az egyes szabályzatok eredményére. 
    
**K: Mik a feltételes hozzáférés állapotának lehetséges értékei?**

**A:** A feltételes hozzáférés állapota a következő értékeket tartalmazhatja:

* **Nincs alkalmazva:** Ez azt jelenti, hogy a felhasználó és az alkalmazás hatókörében nem volt feltételes hozzáférési szabályzat. 
* **Sikeres:** Ez azt jelenti, hogy feltételes hozzáférési szabályzat volt elérhető a hatókörbe tartozó felhasználóval és alkalmazással, és a feltételes hozzáférési szabályzatok sikeresen teljesültek. 
* **Hiba:** A bejelentkezés legalább egy feltételes hozzáférési szabályzat felhasználó- és alkalmazás-feltételének teljesült, és a vezérlők nem teljesültek, vagy a hozzáférés letiltása érdekében be vannak állítva.
    
**K: Mik a feltételes hozzáférési szabályzat eredményének lehetséges értékei?**

**A:** A feltételes hozzáférési szabályzatok az alábbi eredményeket kaphatják:

* **Sikeres:** A szabályzat sikeresen teljesült.
* **Hiba:** A szabályzat nem teljesült.
* **Nincs alkalmazva:** Ennek az lehet az oka, hogy a szabályzat feltételei nem teljesülnek.
* **Nincs engedélyezve:** Ennek oka, hogy a szabályzat letiltott állapotban van. 
    
**K: Az összes bejelentkezési jelentésben a szabályzat neve nem egyezik a CA-ban a szabályzat nevével. Miért?**

**A:** Az összes bejelentkezési jelentésben a szabályzat neve a feltételes hozzáférési szabályzat nevére épül a bejelentkezéskor. Ez inkonzisztens lehet a ca házirend nevével, ha később frissítette a szabályzat nevét, azaz a bejelentkezés után.

**K: A bejelentkezést egy feltételes hozzáférési szabályzat blokkolta, de a bejelentkezési tevékenységről szóló jelentés szerint a bejelentkezés sikeres volt. miért?**

**A:** A bejelentkezési jelentés jelenleg nem biztos, hogy pontos eredményeket mutat a feltételes hozzáférés alkalmazásakor az Exchange ActiveSync-forgatókönyvek esetében. Előfordulhatnak olyan esetek, amikor a jelentés bejelentkezési eredménye sikeres bejelentkezést mutat, de a bejelentkezés egy feltételes hozzáférési szabályzat miatt valójában sikertelen volt.
