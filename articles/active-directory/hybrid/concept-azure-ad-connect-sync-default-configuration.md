---
title: 'Azure AD Connect Sync: az alapértelmezett konfiguráció ismertetése | Microsoft Docs'
description: Ez a cikk a Azure AD Connect Sync alapértelmezett konfigurációját ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e55526e0a63a0c603e2b62ccb3ac0efed911cff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996627"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Az Azure AD Connect szinkronizálása: az alapértelmezett konfiguráció ismertetése
Ez a cikk a beépített konfigurációs szabályokat ismerteti. Dokumentálja a szabályokat, és hogy ezek a szabályok milyen hatással vannak a konfigurációra. Emellett Azure AD Connect Sync alapértelmezett konfigurációját is ismerteti. A cél az, hogy az olvasó tudomásul veszi, hogyan működik a deklaratív kiépítés nevű konfigurációs modell egy valós példában. Ez a cikk azt feltételezi, hogy már telepítette és konfigurálta Azure AD Connect szinkronizálást a telepítővarázsló használatával.

A konfigurációs modell részleteinek megismeréséhez olvassa el a [deklaratív kiépítés ismertetése](concept-azure-ad-connect-sync-declarative-provisioning.md)című témakört.

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Beépített szabályok a helyszínen az Azure AD-be
A következő kifejezések a beépített konfigurációban találhatók.

### <a name="user-out-of-box-rules"></a>Felhasználói használatra kész szabályok
Ezek a szabályok az iNetOrgPerson objektum típusára is érvényesek.

A szinkronizáláshoz a felhasználói objektumnak meg kell felelnie a következőknek:

* SourceAnchor kell rendelkeznie.
* Miután létrejött az objektum az Azure AD-ben, a sourceAnchor nem módosítható. Ha az érték a helyszínen módosul, az objektum szinkronizálása leáll, amíg a sourceAnchor vissza nem változik az előző értékre.
* A accountEnabled (userAccountControl) attribútumot fel kell tölteni. Helyszíni Active Directory esetén ez az attribútum mindig jelen van és fel van töltve.

A következő felhasználói objektumok nincsenek **szinkronizálva** az Azure ad-vel:

* `IsPresent([isCriticalSystemObject])`. Győződjön meg arról, hogy a Active Directory számos beépített objektum, például a beépített rendszergazdai fiók nincs szinkronizálva.
* `IsPresent([sAMAccountName]) = False`. Győződjön meg arról, hogy a sAMAccountName attribútum nélküli felhasználói objektumok nincsenek szinkronizálva. Ez az eset csak gyakorlatilag az NT4-ről frissített tartományokban fordul elő.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Ne szinkronizálja Azure AD Connect Sync és a korábbi verziói által használt szolgáltatásfiókot.
* Ne szinkronizálja az Exchange Online-ban nem működő Exchange-fiókokat.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Ne szinkronizálja azokat az objektumokat, amelyek nem működnek az Exchange Online-ban.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Ez a bitmaszk (&H21C07000) a következő objektumokat szűri ki:
  * Levelezési nyilvános mappa (az előzetes verzióban a 1.1.524.0 verziója)
  * Rendszerszintű postaláda
  * Postaláda-adatbázis postaládája (rendszerpostaláda)
  * Univerzális biztonsági csoport (nem lesz érvényes a felhasználóra, de az örökölt okok miatt)
  * Nem univerzális csoport (nem érvényes a felhasználóra, de a korábbi okok miatt)
  * Postaláda-csomag
  * Felderítési postaláda
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne szinkronizáljon egyetlen replikációs sértett objektumot sem.

A következő attribútum-szabályok érvényesek:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. A sourceAnchor attribútum nem járul hozzá egy csatolt postaládából. Feltételezzük, hogy ha található csatolt postaláda, a tényleges fiók később csatlakozik.
* Az Exchange-hez kapcsolódó attribútumok csak akkor lesznek szinkronizálva, ha a **mailNickName** attribútum értéke.
* Ha több erdő van, akkor az attribútumok a következő sorrendben lesznek felhasználva:
  1. A bejelentkezéshez kapcsolódó attribútumok (például a userPrincipalName) az erdőből egy engedélyezett fiókkal járulnak hozzá.
  2. Az Exchange-GAL (globális címlistában) található attribútumok az erdőhöz kapcsolódnak Exchange-postaládával.
  3. Ha nem található postaláda, ezek az attribútumok bármely erdőből származhatnak.
  4. Az Exchange-hez kapcsolódó attribútumok (a GAL-ben nem látható technikai attribútumok) az erdőből származnak `mailNickname ISNOTNULL` .
  5. Ha több olyan erdő van, amely megfelel ezeknek a szabályoknak, akkor az összekötők (erdők) létrehozási sorrendje (dátum/idő) alapján határozható meg, hogy melyik erdő járul hozzá az attribútumokhoz. Az első csatlakoztatott erdő lesz az első szinkronizálandó erdő. 

### <a name="contact-out-of-box-rules"></a>Kapcsolaton kívüli szabályok
A kapcsolattartási objektumnak meg kell felelnie a következő szinkronizálásának:

* A partnernek levelezésre engedélyezettnek kell lennie. A rendszer a következő szabályokkal ellenőrzi:
  * `IsPresent([proxyAddresses]) = True)`. A proxyAddresses attribútumot fel kell tölteni.
  * Az elsődleges e-mail-cím a proxyAddresses attribútumban vagy a mail attribútumban található. A jelenléte \@ annak ellenőrzésére szolgál, hogy a tartalom egy e-mail-cím. Ennek a két szabálynak az egyikét igaz értékre kell kiértékelni.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Létezik "SMTP:" bejegyzés, és ha van ilyen, \@ a karakterláncban található?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. A mail attribútum ki van töltve, és ha igen, megtalálható \@ a karakterláncban?

A következő kapcsolattartási objektumok **nincsenek szinkronizálva** az Azure ad-vel:

* `IsPresent([isCriticalSystemObject])`. Győződjön meg arról, hogy nincs a kritikusként megjelölt kapcsolattartási objektum szinkronizálva. Nem lehet alapértelmezett konfiguráció.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Ezek az objektumok nem működnek az Exchange Online-ban.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne szinkronizáljon egyetlen replikációs sértett objektumot sem.

### <a name="group-out-of-box-rules"></a>Csoporton kívüli szabályok
A csoport objektumnak meg kell felelnie a következő szinkronizálásának:

* 50 000-nál kevesebb taggal kell rendelkezniük. Ez a szám a helyszíni csoport tagjainak száma.
  * Ha a szinkronizálás első indításakor több tagot is tartalmaz, a rendszer nem szinkronizálja a csoportot.
  * Ha a tagok száma az első létrehozásakor nő, akkor amikor eléri a 50 000 tagot, a rendszer leállítja a szinkronizálást, amíg a tagság száma nem éri el a 50 000 értéket.
  * Megjegyzés: a 50 000 tagságot az Azure AD is kikényszeríti. Nem lehet szinkronizálni a csoportokat több taggal, még akkor is, ha módosítja vagy eltávolítja ezt a szabályt.
* Ha a csoport **terjesztési csoport**, akkor a levelezést is engedélyezni kell. Az ehhez a szabályhoz tartozó, nem kötelező használatú [szabályok](#contact-out-of-box-rules) érvényesítése.

A következő csoportosítási objektumok **nincsenek szinkronizálva** az Azure ad-vel:

* `IsPresent([isCriticalSystemObject])`. Győződjön meg arról, hogy a Active Directory számos beépített objektum, például a beépített rendszergazdák csoport nincs szinkronizálva.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Az inrsync által használt örökölt csoport.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Szerepkör-csoport.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne szinkronizáljon egyetlen replikációs sértett objektumot sem.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal szabályok
A FSP a metaverse "any" ( \* ) objektumához vannak csatlakoztatva. A valóságban ez a csatlakozás csak a felhasználók és a biztonsági csoportok esetében fordul elő. Ez a konfiguráció biztosítja, hogy az erdők közötti tagságok megoldódik és megfelelően megjelenjenek az Azure AD-ben.

### <a name="computer-out-of-box-rules"></a>Számítógépeken kívüli szabályok
A számítógép-objektumoknak meg kell felelniük a következő szinkronizálásának:

* `userCertificate ISNOTNULL`. Ezt az attribútumot csak a Windows 10 rendszerű számítógépek töltik fel. Az attribútumban található értékkel rendelkező összes számítógép-objektum szinkronizálva van.

## <a name="understanding-the-out-of-box-rules-scenario"></a>A beépített szabályok ismertetése
Ebben a példában egy központi telepítést használunk egy fiók erdővel (A), egy erőforrás-erdővel (R) és egy Azure AD-címtárral.

![A forgatókönyv leírását tartalmazó kép](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

Ebben a konfigurációban feltételezhető, hogy a fiók erdőben van egy engedélyezett fiók, valamint egy letiltott fiók az erőforrás-erdőben egy csatolt postaládával.

Célunk az alapértelmezett konfiguráció:

* A bejelentkezéshez kapcsolódó attribútumok szinkronizálása az erdőből az engedélyezett fiókkal történik.
* A GAL-ban (globális címlistában) található attribútumok szinkronizálása az erdőből a postaládával történik. Ha nem található postaláda, akkor a rendszer minden más erdőt használ.
* Ha a rendszer csatolt postaládát talál, a csatolt engedélyezett fióknak az Azure AD-ba exportálandó objektumhoz kell tartoznia.

### <a name="synchronization-rule-editor"></a>Szinkronizációs szabály szerkesztője
A konfiguráció megtekinthető és módosítható az eszköz szinkronizálási szabályainak szerkesztőjével (SRE), és a Start menüben is megtalálható a parancsikon.

![Szinkronizációs szabályok szerkesztő ikonja](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

A SRE egy Resource Kit-eszköz, amely Azure AD Connect Sync szolgáltatással van telepítve. Ahhoz, hogy el tudja indítani, a ADSyncAdmins csoport tagjának kell lennie. Indításakor a következőhöz hasonló jelenik meg:

![Bejövő szinkronizálási szabályok](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Ezen az ablaktáblán láthatja a konfigurációhoz létrehozott összes szinkronizálási szabályt. A tábla minden sora egyetlen szinkronizálási szabály. A szabály típusainak bal oldalán a két különböző típus szerepel a következőben: bejövő és kimenő. A bejövő és a kimenő érték a metaverse nézete. Elsősorban az áttekintésben szereplő bejövő szabályokra fog összpontosítani. A szinkronizálási szabályok tényleges listája az AD-ben észlelt sémától függ. A fenti képen a fiók-erdő (fabrikamonline.com) nem rendelkezik olyan szolgáltatásokkal, mint az Exchange és a Lync, és ezekhez a szolgáltatásokhoz nincsenek szinkronizálási szabályok létrehozva. Az erőforrás-erdőben (res.fabrikamonline.com) azonban megtalálhatja a szolgáltatások szinkronizálási szabályait. A szabályok tartalma eltérő a észlelt verziótól függően. Például egy Exchange 2013-es üzemelő példányban több, az Exchange 2010/2007-ben konfigurált attribútum-folyamat van konfigurálva.

### <a name="synchronization-rule"></a>Szinkronizálási szabály
A szinkronizálási szabály egy olyan konfigurációs objektum, amely egy adott feltétel teljesülése esetén áramlik az attribútumok egy halmazával. Azt is felhasználjuk, hogy az összekötő terület egy objektuma a metaverse egyik objektumához **kapcsolódjon** , más néven JOIN vagy **Match**. A szinkronizálási szabályok elsőbbségi értékkel rendelkeznek, amely azt jelzi, hogy azok hogyan kapcsolódnak egymáshoz. Az alacsonyabb numerikus értékkel rendelkező szinkronizálási szabályok magasabb prioritással rendelkeznek, és egy attribútum-adatfolyam ütközik, a magasabb prioritású WINS megnyeri az ütközés feloldását.

Példaként tekintse át az **ad – User AccountEnabled** szinkronizálási szabályát. Jelölje ki ezt a sort a SRE, és válassza a **Szerkesztés** lehetőséget.

Mivel ez a szabály egy beépített szabály, a szabály megnyitásakor figyelmeztetés jelenik meg. Nem kell [módosítania](how-to-connect-sync-best-practices-changing-default-configuration.md)a beépített szabályokat, ezért a rendszer megkérdezi, hogy mit tesz a szándéka. Ebben az esetben csak a szabályt szeretné megtekinteni. Válassza a **Nem** lehetőséget.

![Szinkronizálási szabályok figyelmeztetése](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

A szinkronizálási szabály négy konfigurációs szakaszt tartalmaz: Leírás, hatókör-szűrő, csatlakozási szabályok és átalakítások.

#### <a name="description"></a>Description
Az első szakasz olyan alapvető információkat tartalmaz, mint például a név és a leírás.

![Leírás lap a szinkronizálási szabály szerkesztőjében](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Azt is megtudhatja, hogy melyik csatlakoztatott rendszerhez kapcsolódik ez a szabály, hogy melyik objektumtípus szerepel a csatlakoztatott rendszeren, valamint a metaverse-objektum típusa. A metaverse objektumtípus mindig személy, függetlenül attól, hogy a Forrásobjektum típusa felhasználó, iNetOrgPerson vagy kapcsolattartó. A metaverse objektumtípus soha nem változik, így általános típusként jön létre. A kapcsolat típusa beállítható a csatlakozáshoz, a StickyJoin vagy a létesítéshez. Ez a beállítás a JOIN Rules (csatlakoztatási szabályok) szakasszal együtt működik.

Azt is láthatja, hogy ez a szinkronizálási szabály a jelszó-szinkronizáláshoz használatos. Ha egy felhasználó a szinkronizálási szabály hatókörében van, a rendszer szinkronizálja a jelszót a helyszínről a felhőbe (feltéve, hogy engedélyezte a jelszó-szinkronizálási funkciót).

#### <a name="scoping-filter"></a>Hatókör-szűrő
A hatóköri szűrő szakasz használatával konfigurálhatja, hogy mikor kell alkalmazni a szinkronizálási szabályt. Mivel a megtekintett szinkronizálási szabály neve azt jelzi, hogy csak az engedélyezett felhasználókra vonatkozik, a hatókör úgy van konfigurálva, hogy a **USERACCOUNTCONTROL** ad-attribútumnak ne legyen a 2. bit-készlete. Ha a Szinkronizáló motor megtalál egy felhasználót az AD-ben, akkor alkalmazza ezt a szinkronizálási szabályt, ha a **UserAccountControl** a 512 decimális értékre van beállítva (a normál felhasználó engedélyezve van). Nem alkalmazza a szabályt, ha a felhasználó **UserAccountControl** beállítása 514 (letiltva a normál felhasználó).

![A "bejövő szinkronizálási szabály szerkesztése" ablak "hatókör szűrő" szakaszát bemutató képernyőkép.](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

A hatókör-szűrő olyan csoportokat és záradékokat tartalmaz, amelyek beágyazva lehetnek. Egy csoporton belüli összes záradéknak meg kell felelnie egy szinkronizálási szabály alkalmazásának. Ha több csoport van definiálva, akkor legalább egy csoportnak teljesülnie kell a szabály alkalmazásához. Ez a logikai vagy a csoportok és logikai, valamint egy csoporton belüli kiértékelése. Erre a konfigurációra például a kimenő szinkronizálási szabály a **HRE – csoportos csatlakozás** lehetőségnél talál példát. Több szinkronizálási szűrő is létezik, például egy a biztonsági csoportok ( `securityEnabled EQUAL True` ) és egy a terjesztési csoportok ( `securityEnabled EQUAL False` ) számára.

![Hatókör lap a szinkronizálási szabály szerkesztőjében](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Ez a szabály annak meghatározására szolgál, hogy mely csoportokat kell kiépíteni az Azure AD-be. A terjesztési csoportoknak engedélyezni kell a levelezést az Azure AD-vel való szinkronizáláshoz, de a biztonsági csoportok esetében nincs szükség e-mailekre.

#### <a name="join-rules"></a>Csatlakozási szabályok
A harmadik szakasz annak konfigurálására szolgál, hogy az összekötő terület objektumai hogyan kapcsolódnak a metaverse objektumaihoz. A korábban megtekintett szabály nem rendelkezik konfigurációval az összekapcsolási szabályokhoz, így helyette az **ad – User JOIN szolgáltatásban** fog megjelenni.

![Csatlakoztatási szabályok lap a szinkronizálási szabály szerkesztőjében](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

Az illesztési szabály tartalma a telepítési varázslóban kiválasztott egyeztetési beállítástól függ. Bejövő szabályok esetén a kiértékelés a forrás-összekötő területének egy objektumával kezdődik, és az illesztési szabályokban szereplő egyes csoportok kiértékelése a sorban történik. Ha egy forrásoldali objektum úgy van kiértékelve, hogy a metaverse pontosan egy objektumát használja az illesztési szabályok valamelyikével, akkor az objektumok csatlakozva lesznek. Ha az összes szabály kiértékelése megtörtént, és nincs egyezés, akkor a Leírás lapon a hivatkozás típusa lesz használatban. Ha ez a konfiguráció kiépítésre van **beállítva, akkor** létrejön egy új objektum a célhelyen, a metaverse, ha az illesztési feltételekben legalább egy attribútum létezik (értéke). Ha új objektumot szeretne kiépíteni a metaverse-nek, azt is nevezik, hogy egy objektumot **terveznek** a metaverse-nek.

Az illesztési szabályok csak egyszer lesznek kiértékelve. Ha egy összekötő Space objektum és egy metaverse-objektum csatlakoztatva van, akkor a rendszer addig csatlakozik, amíg a szinkronizálási szabály hatóköre továbbra is teljesül.

A szinkronizálási szabályok kiértékelése során csak egy olyan szinkronizálási szabálynak kell szerepelnie, amelyhez illesztési szabályok tartoznak. Ha egy objektumhoz több szinkronizálási szabály is található, akkor a rendszer hibát jelez. Ezért az ajánlott eljárás az, hogy csak egy szinkronizálási szabályt kell összekapcsolni, ha egy objektum hatókörében több szinkronizálási szabály van megadva. A Azure AD Connect szinkronizálására szolgáló beépített konfigurációban ezek a szabályok megtalálhatók a név alapján, és megkereshetik a név végén található **JOIN** szót. A megadott illesztési szabályok nélküli szinkronizálási szabály alkalmazza az attribútumot, amikor egy másik szinkronizálási szabály összekapcsolta az objektumokat, vagy egy új objektumot épít ki a célhelyen.

Ha megtekinti a fenti képen láthatót, láthatja, hogy a szabály megpróbál csatlakozni a **objectSID** -hez a **msExchMasterAccountSid** (Exchange) és a **msRTCSIP-OriginatorSid** (Lync) használatával, ami a fiók-erőforrás erdő topológiájában várható. Ugyanezt a szabályt minden erdőben megtalálhatja. Feltételezi, hogy minden erdő lehet fiók vagy erőforrás-erdő. Ez a konfiguráció akkor is működik, ha olyan fiókokkal rendelkezik, amelyek egyetlen erdőben vannak, és nem kell csatlakozniuk.

#### <a name="transformations"></a>Átalakítások
Az átalakítás szakasz a célobjektum összes olyan attribútumát definiálja, amely az objektumok csatlakoztatásakor a célként megadott objektumra vonatkozik, és a hatókör-szűrő teljesül. Visszatérve az **ad – User AccountEnabled** szinkronizációs szabályba, a következő átalakításokat fogja találni:

![A szinkronizálási szabály szerkesztőjének átalakítások lapja](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Ha ezt a konfigurációt kontextusban szeretné elhelyezni, egy Account-Resource erdőben üzemelő példányban, az Exchange-és Lync-beállításokkal rendelkező erőforrás-erdőben a fiók erdő és egy letiltott fiók található. A megtekintett szinkronizálási szabály tartalmazza a bejelentkezéshez szükséges attribútumokat, és ezek az attribútumok olyan erdőből származnak, ahol engedélyezve van a fiók. Ezek az attribútumok egyetlen szinkronizálási szabályba vannak helyezve.

Az átalakítás különböző típusokkal rendelkezhet: állandó, közvetlen és kifejezés.

* Egy állandó folyamat mindig hardcoded értéket hoz folyamatba. A fenti esetben mindig az **igaz** értéket állítja be a **accountEnabled** nevű metaverse attribútumban.
* A közvetlen folyamat a forrásban lévő attribútum értékét mindig a célként megadott attribútumra irányítja.
* A harmadik folyamat típusa kifejezés, és lehetővé teszi a fejlettebb konfigurációk használatát.

A kifejezés nyelve a VBA (Visual Basic for Applications), így a Microsoft Office vagy a VBScript felhasználói felülete felismeri a formátumot. Az attribútumok szögletes zárójelben vannak, [attributeName]. Az attribútumok nevei és a függvények nevei megkülönböztetik a kis-és nagybetűket, de a szinkronizálási szabályok szerkesztője kiértékeli a kifejezéseket, és figyelmeztetést jelenít meg, ha a kifejezés érvénytelen. Az összes kifejezés egyetlen sorban, beágyazott függvényekkel van kifejezve. A konfigurációs nyelv hatékonyságának megjelenítéséhez itt látható a folyamat a pwdLastSet, de további megjegyzésekkel egészül ki:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .NET datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

A következő témakörben talál további információt az attribútum-adatforgalom kifejezés nyelvéről: a [deklaratív kiépítési kifejezések ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) .

### <a name="precedence"></a>Precedencia
Most már megvizsgálta az egyes szinkronizálási szabályokat, de a szabályok együtt működnek a konfigurációban. Bizonyos esetekben az attribútumérték több szinkronizálási szabályból is hozzájárul ugyanahhoz a TARGET attribútumhoz. Ebben az esetben az attribútum prioritása határozza meg, hogy a WINS melyik attribútumot használja. Példaként tekintse meg a sourceAnchor attribútumot. Ez az attribútum fontos attribútum az Azure AD-be való bejelentkezéshez. Ehhez az attribútumhoz két különböző szinkronizálási szabályban talál attribútum-adatfolyamot, **az ad – User AccountEnabled** és a **from ad – Common**. A szinkronizálási szabály elsőbbsége miatt a sourceAnchor attribútum az erdőből származik, amikor a metaverse objektumhoz több objektum is csatlakozik. Ha nincsenek engedélyezett fiókok, akkor a szinkronizálási motor az **ad – User Common** a catch-all szinkronizálási szabályt használja Ez a konfiguráció biztosítja, hogy még a letiltott fiókok esetében is legyen sourceAnchor.

![Bejövő szinkronizálási szabályok](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

A szinkronizálási szabályok elsőbbségi sorrendjét a telepítővarázsló csoportok szerint állítja be. A csoport összes szabályának azonos a neve, de különböző csatlakoztatott címtárakhoz vannak csatlakoztatva. A telepítővarázsló megadja a szabályt az **ad-től – a felhasználó** a legmagasabb prioritással csatlakozik, és az összes csatlakoztatott ad-címtárban megismétli. Ezután egy előre meghatározott sorrendben folytatja a szabályok következő csoportjaival. Egy csoporton belül a szabályok az összekötők a varázslóban való hozzáadásának sorrendjében lesznek hozzáadva. Ha egy másik összekötőt adnak hozzá a varázslóhoz, a szinkronizálási szabályokat a rendszer átrendezi, és az új összekötő szabályait a rendszer az összes csoportban utoljára beszúrja.

### <a name="putting-it-all-together"></a>Végső összeállítás
Most már tudjuk, hogy a szinkronizálási szabályokkal tisztában lehet azzal, hogy a konfiguráció hogyan működik a különböző szinkronizálási szabályokkal. Ha megtekint egy felhasználót és a metaverse-hoz hozzájáruló attribútumokat, a szabályok a következő sorrendben lesznek alkalmazva:

| Name | Megjegyzés |
|:--- |:--- |
| A from AD – felhasználói csatlakozás |Szabály az összekötő terület objektumainak metaverse-vel való csatlakoztatásához. |
| A from AD – felhasználóifiók engedélyezve |Az Azure AD-be és Microsoft 365ba való bejelentkezéshez szükséges attribútumok. Ezeket az attribútumokat az engedélyezett fiókból szeretnénk használni. |
| A from AD – Common from Exchange |A globális címlistában található attribútumok. Feltételezzük, hogy az adatminőség a legjobb abban az erdőben, ahol a felhasználó postaládáját megtalálták. |
| A from AD – Common |A globális címlistában található attribútumok. Ha nem találtunk egy postaládát, a többi csatlakoztatott objektum is hozzájárulhat az attribútum értékéhez. |
| A from AD – felhasználói Exchange |Csak akkor létezik, ha az Exchange észlelve lett. Az összes infrastruktúra-Exchange-attribútumot átfolyik. |
| A from AD – felhasználó Lync |Csak akkor létezik, ha a Lync észlelve lett. Az összes infrastruktúra Lync-attribútumot átfolyik. |

## <a name="next-steps"></a>Következő lépések
* További információ a konfigurációs modellről a [deklaratív kiépítés ismertetése](concept-azure-ad-connect-sync-declarative-provisioning.md)című cikkből.
* További információ a kifejezés nyelvéről a [deklaratív kiépítési kifejezések ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)című cikkből.
* Továbbra is olvashatja, hogyan működik a beépített konfiguráció a [felhasználók és a névjegyek megismeréséhez](concept-azure-ad-connect-sync-user-and-contacts.md)
* Tekintse meg, hogyan lehet [módosítani az alapértelmezett konfigurációt](how-to-connect-sync-change-the-configuration.md)a deklaratív kiépítés használatával.

**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)

