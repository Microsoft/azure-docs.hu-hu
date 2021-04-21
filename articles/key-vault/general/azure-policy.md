---
title: Az Azure Key Vault integrálása az Azure Policyval
description: Megtudhatja, hogyan integrálhatja Azure Key Vault a Azure Policy
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: cddc7b931bf59412d4a7ec8e6b0eecfe148f3d5e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749275"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Az Azure Key Vault integrálása az Azure Policyval

[Azure Policy](../../governance/policy/index.yml) egy olyan cégirányítási eszköz, amely lehetővé teszi a felhasználóknak az Azure-környezetük nagy léptékű naplózását és kezelését. Azure Policy lehetővé teszi, hogy védőkorlátokat helyezzen el az Azure-erőforrásokon, hogy azok megfeleljenek a hozzárendelt szabályzatszabályoknak. Lehetővé teszi a felhasználók számára az Azure-környezetük naplózását, valós idejű kényszerítését és szervizelését. A szabályzat által végzett naplózás eredményei a megfelelőségi irányítópulton érhetők el a felhasználók számára, ahol láthatják, hogy mely erőforrások és összetevők megfelelők és melyek nem.  További információkért lásd a Azure Policy [áttekintését.](../../governance/policy/overview.md)

Példa használati forgatókönyvekre:

- Szeretné javítani a vállalat biztonsági biztonságát a minimális kulcsméretekre és a tanúsítványok maximális érvényességi időtartamára vonatkozó követelmények alkalmazásával a vállalat kulcstartóiban, de nem tudja, hogy mely csapatok fognak megfelelő lenni, és melyek nem.
- Jelenleg nincs megoldás a szervezeten belüli naplózás elvégzésére, vagy manuálisan naplóz a környezetben azáltal, hogy megkéri a szervezet egyes csapatait, hogy jelentsék a megfelelőségüket. A feladat automatizálására, a valós idejű naplózásra és a naplózás pontosságának garantálára keres egy módját.
- Szeretné kikényszeríteni a vállalati biztonsági szabályzatokat, és megakadályozni, hogy egyes személyek önaírt tanúsítványokat hoznak létre, de nincs automatikus módja a létrehozásuk blokkolására. 
- A tesztelési csapatokra vonatkozó követelményeket meg szeretné pihentetni, de az éles környezet felett szeretne szigorú ellenőrzést tartani. Egyszerű, automatizált módszerre van szüksége az erőforrások kényszerítési folyamatának elválasztása érdekében.
- Biztos szeretne lenni abban, hogy az új szabályzatok kényszerítési eljárását vissza tudja állitni egy élő webhely problémája esetén. Egykattintásos megoldásra van szüksége a szabályzat kényszerítési érvényének kikapcsolhoz. 
- Egy külső féltől származó megoldásra támaszkodik a környezet naplózása érdekében, és egy belső Microsoft-ajánlatot szeretne használni.

## <a name="types-of-policy-effects-and-guidance"></a>Szabályzathatások típusai és útmutatás

**Naplózás:** Ha egy szabályzat hatása naplózásra van beállítva, a szabályzat nem okoz a környezetben jelentős változásokat. Ez csak a szabályzatmegfelelőségi irányítópulton nem megfelelőként megjelölt összetevőkről küld riasztást, például olyan összetevőkről, amelyek nem felelnek meg a szabályzatdefinícióknak egy adott hatókörben. A naplózás alapértelmezett beállítás, ha nincs házirend-hatás kijelölve.

**Megtagadás:** Ha egy házirend megtagadásra van állítva, a házirend letiltja az új összetevők, például tanúsítványok létrehozását, valamint a szabályzatdefiníciónak nem megfelelő meglévő összetevők új verzióinak létrehozását. A kulcstartóban meglévő nem megfelelő erőforrásokra ez nincs hatással. Az auditálási képességek továbbra is működni fognak.

## <a name="available-built-in-policy-definitions"></a>Elérhető "Beépített" szabályzatdefiníciók

Key Vault létrehozott egy szabályzatkészletet, amely kulcs-, tanúsítvány- és titkosobjektumok kezelésére használható. Ezek a szabályzatok "beépítettek", ami azt jelenti, hogy nem kell egyéni JSON-t írnia az engedélyezésük érdekében, és elérhetők a Azure Portal a hozzárendeléshez. Bizonyos paramétereket továbbra is testreszabhat a szervezet igényeinek megfelelően.

# <a name="certificate-policies"></a>[Tanúsítvány-házirendek](#tab/certificates)

### <a name="certificates-should-have-the-specified-maximum-validity-period-preview"></a>A tanúsítványoknak a megadott maximális érvényességi időtartammal (előzetes verzió) kell lennie

Ez a szabályzat lehetővé teszi a Key Vaultban tárolt tanúsítványok maximális érvényességi időtartamának kezelését. Bevált biztonsági eljárás a tanúsítványok maximális érvényességi idejének korlátozása. Ha a tanúsítvány titkos kulcsát észlelés nélkül feltörik, a rövid életű tanúsítványok használata minimálisra csökkenti a folyamatos károkat, és csökkenti a tanúsítvány értékét a támadók számára.

### <a name="certificates-should-use-allowed-key-types-preview"></a>A tanúsítványoknak engedélyezett kulcstípusokat kell használniuk (előzetes verzió)

Ezzel a szabályzattal korlátozhatja a kulcstartóban található tanúsítványok típusát. Ezzel a szabályzattal győződhet meg arról, hogy a tanúsítvány titkos kulcsai RSA-, ECC-, vagy HSM-háttérbe vannak-e ásva. Az alábbi listából választhatja ki, hogy mely tanúsítványtípusok engedélyezettek.

- RSA
- RSA – HSM
- Ecc
- ECC – HSM

### <a name="certificates-should-have-the-specified-lifetime-action-triggers-preview"></a>A tanúsítványoknak a megadott élettartamműveleti eseményindítóval (előzetes verzió) kell lennie

Ez a szabályzat lehetővé teszi az olyan tanúsítványok élettartam-műveletének kezelését, amelyek a lejáratuktól számított bizonyos számú napon belül vagy a használható élettartamuk egy bizonyos százalékán belül elérték az élettartamukat.

### <a name="certificates-should-be-issued-by-the-specified-integrated-certificate-authority-preview"></a>A tanúsítványokat a megadott integrált hitelesítésszolgáltatónak kell kiadnia (előzetes verzió)

Ha integrált Key Vault hitelesítésszolgáltatót (Digicert vagy GlobalSign) használ, és azt szeretné, hogy a felhasználók ezen szolgáltatók valamelyikét vagy valamelyikét használják, ezzel a szabályzattal naplót is kaphat vagy kényszerítheti a választást. Ezzel a szabályzattal naplózhatja vagy megtagadhatja az önaírt tanúsítványok létrehozását a Key Vaultban.

### <a name="certificates-should-be-issued-by-the-specified-non-integrated-certificate-authority-preview"></a>A tanúsítványokat a megadott nem integrált hitelesítésszolgáltatónak kell kiadnia (előzetes verzió)

Ha belső hitelesítésszolgáltatót vagy a kulcstartóval nem integrált hitelesítésszolgáltatót használ, és azt szeretné, hogy a felhasználók egy Ön által listából származó hitelesítésszolgáltatót használjanak, a házirend segítségével létrehozhatja a hitelesítésszolgáltatók engedélyezett listáját a kiállító neve alapján. Ezzel a szabályzattal naplózhatja vagy megtagadhatja az önaírt tanúsítványok létrehozását a Key Vaultban.

### <a name="certificates-using-elliptic-curve-cryptography-should-have-allowed-curve-names-preview"></a>A három ponttal görbét használó titkosítást használó tanúsítványoknak engedélyezettnek kell lennie a görbeneveknek (előzetes verzió)

Ha elliptikus görbe-titkosítást vagy ECC-tanúsítványokat használ, az alábbi listából testre szabhatja a görbenevek engedélyezett listáját. Az alapértelmezett beállítás az alábbi görbenevek mindegyikét engedélyezi.

- P-256
- P-256K
- P-384
- P-521

## <a name="certificates-using-rsa-cryptography-manage-minimum-key-size-for-rsa-certificates-preview"></a>RSA-titkosítást használó tanúsítványok Az RSA-tanúsítványok minimális kulcsméretének kezelése (előzetes verzió)

RSA-tanúsítványok használata esetén megadhatja a tanúsítványokhoz szükséges minimális kulcsméretet. Az alábbi listából választhat egyet.

- 2048 bites
- 3072 bites
- 4096 bites

## <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Meghatározott számú lejárati napon belüli tanúsítványok kezelése (előzetes verzió)

A szolgáltatás kimaradást tapasztalhat, ha a lejárata előtt nem váltják le megfelelően a figyelt tanúsítványokat. Ez a szabályzat kritikus fontosságú annak érdekében, hogy a rendszer figyelni tudja a Key Vaultban tárolt tanúsítványokat. Javasoljuk, hogy ezt a szabályzatot többször is alkalmazza különböző lejárati küszöbértékekkel, például 180, 90, 60 és 30 napos küszöbértékekkel. Ez a szabályzat a tanúsítvány lejáratának figyelére és osztályozásére használható a szervezetben.

# <a name="key-policies"></a>[Fő szabályzatok](#tab/keys)

### <a name="keys-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>A kulcsok nem lehet aktívak a megadott számú napnál hosszabb ideig (előzetes verzió)

Ha meg szeretne győződni arról, hogy a kulcsok a megadott számú napnál tovább nem voltak aktívak, ezzel a szabályzatmal naplót kaphat arról, hogy a kulcs mennyi ideig aktív.

**Ha a kulcshoz be** van állítva aktiválási dátum, ez a szabályzat  a kulcs aktiválási dátumtól az aktuális dátumig eltelt napok számát számítja ki. Ha a napok száma meghaladja a beállított küszöbértéket, a kulcs nem megfelelőként lesz megjelölve a szabályzatnak.

**Ha a kulcshoz** nincs beállítva aktiválási dátum, akkor ez a szabályzat a  kulcs létrehozásának dátumtól az aktuális dátumig eltelt napok számát számítja ki. Ha a napok száma meghaladja a beállított küszöbértéket, a kulcs nem megfelelőként lesz megjelölve a szabályzatnak.

### <a name="keys-should-be-the-specified-cryptographic-type-rsa-or-ec-preview"></a>A kulcsoknak a megadott RSA vagy EC titkosítási típusnak kell lennie (előzetes verzió)

Ez a szabályzat lehetővé teszi a kulcstartóban található kulcsok típusának korlátozását. Ezzel a szabályzatmal arról is győződhet meg, hogy a kulcsok RSA- vagy ECC-, illetve HSM-háttérként vannak-e használva. Az alábbi listából választhatja ki, hogy mely tanúsítványtípusok engedélyezettek.

- RSA
- RSA – HSM
- Ecc
- ECC – HSM

### <a name="keys-using-elliptic-curve-cryptography-should-have-the-specified-curve-names-preview"></a>A három ponttal görbét használó titkosítást használó kulcsoknak meg kell adniuk a megadott görbeneveket (előzetes verzió)

Ha elliptikus görbe-titkosítást vagy ECC-kulcsokat használ, az alábbi listából testreszabhatja a görbenevek engedélyezett listáját. Az alapértelmezett beállítás az alábbi görbenevek mindegyikét engedélyezi.

- P-256
- P-256K
- P-384
- P-521

### <a name="keys-should-have-expirations-dates-set-preview"></a>A kulcsok lejárati dátumait be kell állítani (előzetes verzió)

Ez a szabályzat naplót végez a kulcstartókban található összes kulcson, és megjelöli a nem megfelelőként beállított lejárati dátummal nem rendelkező kulcsokat. Ezzel a szabályzatmal letilthatja a lejárati dátummal nem rendelkező kulcsok létrehozását.

### <a name="keys-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>A kulcsok lejárata előtt a megadott számú napnál többnek kell lennie (előzetes verzió)

Ha egy kulcs túl közel van a lejárathoz, a kulcs elforgatása szervezeti késéssel is eredményezhet kimaradást. A kulcsokat a lejárat előtt meghatározott számú napon kell elforgatni, hogy elegendő időt biztosítson a hibákra való reagáláshoz. Ez a szabályzat napló fogja naplóni a lejárati dátumukhoz túl közeli kulcsokat, és lehetővé teszi ennek a küszöbértéknek a napokban való beállítását. Ezzel a szabályzatmal megakadályozhatja az olyan új kulcsok létrehozását, amelyek túl közel vannak a lejárati dátumukhoz.

### <a name="keys-should-be-backed-by-a-hardware-security-module-preview"></a>A kulcsok biztonsági kulcsait hardveres biztonsági modulnak kell mögötte (előzetes verzió)

A HSM egy hardveres biztonsági modul, amely kulcsokat tárol. A HSM fizikai védelmi réteget biztosít a titkosítási kulcsok számára. A titkosítási kulcs nem hagyhatja el a fizikai HSM-et, amely a szoftverkulcsnál magasabb szintű biztonságot nyújt. Egyes szervezetek megfelelőségi követelményekkel szabályznak a HSM-kulcsok használatát. Ezzel a szabályzatkal naplózható a kulcstartóban tárolt, nem HSM-háttérbeli kulcsok naplózása. Ezzel a szabályzatmal letilthatja a HSM-háttéren nem található új kulcsok létrehozását. Ez a szabályzat minden kulcstípusra érvényes lesz, az RSA-t és az ECC-t is.

### <a name="keys-using-rsa-cryptography-should-have-a-specified-minimum-key-size-preview"></a>Az RSA-titkosítást használó kulcsok minimális kulcsmérete (előzetes verzió)

A kisebb kulcsméretű RSA-kulcsok használata nem biztonságos tervezési gyakorlat. A minimális kulcsméret használatát kötelezően előírt naplózási és tanúsítási szabványok vonatkozhatnak Önre. Az alábbi szabályzat lehetővé teszi a kulcstartó minimális kulcsméret-követelményének beállítását. Olyan kulcsokat is naplóolhat, amelyek nem teljesítik ezt a minimális követelményt. Ezzel a házirendtel blokkolható az olyan új kulcsok létrehozása is, amelyek nem felelnie meg a minimális kulcsméretre vonatkozó követelménynek.

### <a name="keys-should-have-the-specified-maximum-validity-period-preview"></a>A kulcsoknak a megadott maximális érvényességi időtartammal (előzetes verzió) kell lennie

A szervezeti megfelelőségi követelmények kezeléséhez adja meg a kulcstartóban érvényes kulcs legfeljebb napokban megadott maximális idejét. A beállított küszöbértéknél hosszabb ideig érvényes kulcsok nem megfelelőként lesznek megjelölve. Ezzel a szabályzatmal letilthatja az olyan új kulcsok létrehozását, amelyek lejárati dátuma hosszabb a megadott maximális érvényességi időtartamnál.

# <a name="secret-policies"></a>[Titkos szabályzatok](#tab/secrets)

### <a name="secrets-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>A titkos kulcsok nem lehet aktívak a megadott számú napnál hosszabb ideig (előzetes verzió)

Ha meg szeretne győződni arról, hogy a titkos kulcsok a megadott számú napnál tovább nem voltak aktívak, ezzel a szabályzatmal naplót kaphat arról, hogy a titkos kulcsok mennyi ideig voltak aktívak.

**Ha a titkos kód aktiválási dátuma** be van állítva, ez a  szabályzat kiszámítja, hogy hány nap eltelt a titkos kód aktiválási dátumtól az aktuális dátumig. Ha a napok száma meghaladja a beállított küszöbértéket, a titkos secret nem megfelelőként lesz megjelölve a szabályzatnak.

**Ha a titkos kódhoz** nincs beállítva aktiválási dátum, ez a szabályzat kiszámítja, hogy hány nap eltelt a titkos kód létrehozásától az aktuális dátumig.  Ha a napok száma meghaladja a beállított küszöbértéket, a titkos secret nem megfelelőként lesz megjelölve a szabályzatnak.

### <a name="secrets-should-have-content-type-set-preview"></a>A titkos kulcsokhoz tartalomtípust kell beállítani (előzetes verzió)

Bármilyen egyszerű szöveg vagy kódolt fájl tárolható kulcstartókulcsként. Előfordulhat azonban, hogy a szervezet különböző rotációs szabályzatokat és korlátozásokat szeretne beállítani a jelszavakra, kapcsolati sztringekra vagy kulcsként tárolt tanúsítványokra. A tartalomtípuscímkék segítségével a felhasználók a titkos kód értékének beolvasása nélkül láthatják, hogy mi van tárolva egy titkos objektumban. Ezzel a szabályzatkal naplózhatja a tartalomtípuscímkével nem beállított titkos kódokat. Ezzel a szabályzatot arra is használhatja, hogy megakadályozza az új titkos kulcsok létrejöttét, ha nincs beállítva tartalomtípus-címke.

### <a name="secrets-should-have-expiration-date-set-preview"></a>A titkos kulcsok lejárati dátumának (előzetes verzió) kell lennie

Ez a szabályzat naplót végez a key vaultban tárolt összes titkos kulcson, és megjelöli a nem megfelelőként beállított lejárati dátummal nem rendelkező titkos kulcsokat. Ezzel a szabályzatmal letilthatja a lejárati dátummal nem rendelkező titkos kulcsok létrehozását.

### <a name="secrets-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>A titkos kulcsok lejárata előtt a megadott számú napnál többnek kell lennie (előzetes verzió)

Ha egy titkos kulcs túl közel van a lejárathoz, a titkos kulcs szervezeti késleltetése kimaradáshoz vezethet. A titkos kulcsokat a lejárat előtt meghatározott számú napon kell elforgatni, hogy elegendő időt biztosítson a hibákra való reagáláshoz. Ez a szabályzat naplója a lejárati dátumukhoz túl közeli titkos kulcsokat, és lehetővé teszi a küszöbérték napokban való beállítását. Ezzel a szabályzatmal megakadályozhatja olyan új titkos kulcsok létrehozását, amelyek túl közel vannak a lejárati dátumukhoz.

### <a name="secrets-should-have-the-specified-maximum-validity-period-preview"></a>A titkos kulcsoknak a megadott maximális érvényességi időtartammal (előzetes verzió) kell lennie

A szervezeti megfelelőségi követelmények kezeléséhez adja meg a titkos kulcs kulcstartóban való érvényességének maximális idejét napokban. A beállított küszöbértéknél hosszabb ideig érvényes titkos kulcsok nem megfelelőként lesznek megjelölve. Ezzel a szabályzatmal letilthatja az olyan új titkos kulcsok létrehozását, amelyek lejárati dátuma hosszabb a megadott maximális érvényességi időtartamnál.

---

## <a name="example-scenario"></a>Példaforgatókönyv

Ön több, 100 tanúsítványt tartalmazó csapat által használt kulcstartót kezel, és meg szeretne győződni arról, hogy a kulcstartóban található tanúsítványok egyike sem érvényes 2 évnél hosszabb ideig.

1. A Tanúsítványok  hozzárendelése a megadott maximális érvényességi időtartamra vonatkozó házirendet kell, megadhatja, hogy a tanúsítvány érvényességi időtartama legfeljebb 24 hónap legyen, és a házirend hatását állítsa "naplózásra". 
1. A megfelelőségi [](#view-compliance-results)jelentést a Azure Portal, és felderíti, hogy 20 tanúsítvány nem megfelelő és > 2 évig érvényes, a többi tanúsítvány pedig megfelelő. 
1. Kapcsolatba lép a tanúsítványok tulajdonosaival, és közli az új biztonsági követelményt, amely szerint a tanúsítványok nem lehetnek 2 évnél hosszabb ideig érvényesek. Egyes csapatok válaszolnak, és 15 tanúsítványt újítottak meg legfeljebb 2 éves maximális érvényességi időtartammal. Más csapatok nem válaszolnak, és továbbra is 5 nem megfelelő tanúsítvány van a kulcstartóban.
1. Módosíthatja a "megtagadás" szabályzat hatását. Az 5 nem megfelelő tanúsítványt a rendszer nem vonja vissza, és továbbra is működni fognak. Azonban nem újíthatók meg 2 évnél hosszabb érvényességi időtartammal. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Új házirend engedélyezése Key Vault és kezelése a Azure Portal

### <a name="select-a-policy-definition"></a>Szabályzatdefiníció kiválasztása

1. Jelentkezzen be az Azure Portalra. 
1. Keressen rá a "Szabályzat" kifejezésre a keresősávban, és válassza a **Szabályzat lehetőséget.**

    ![Képernyőkép a keresősávról.](../media/policy-img1.png)

1. A Szabályzat ablakban válassza a **Definíciók lehetőséget.**

    ![Képernyőkép a Definíciók lehetőség kiemelésről.](../media/policy-img2.png)

1. A Kategóriaszűrőben törölje az Összes **kijelölése** lehetőséget, majd válassza **a** Key Vault. 

    ![Képernyőkép a Kategóriaszűrőről és a kiválasztott Key Vault kategóriáról.](../media/policy-img3.png)

1. Most már látnia kell a nyilvános előzetes verzióhoz elérhető összes szabályzatot a Azure Key Vault. Győződjön meg arról, hogy elolvasta és megértette a fenti szabályzati útmutató szakaszt, és válasszon ki egy hatókört hozzárendelni kívánt szabályzatot.  

    ![A nyilvános előzetes verzióhoz elérhető szabályzatokat bemutató képernyőkép.](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Szabályzat hozzárendelése hatókörökhez 

1. Válasszon ki egy alkalmazni kívánt szabályzatot. Ebben a példában a **Tanúsítvány** érvényességi idejének kezelése szabályzat jelenik meg. Kattintson a hozzárendelés gombra a bal felső sarokban.

    ![A Tanúsítvány érvényességi idejének kezelése szabályzatot bemutató képernyőkép.](../media/policy-img5.png)
  
1. Válassza ki azt az előfizetést, amelyben alkalmazni szeretné a szabályzatot. Dönthet úgy, hogy a hatókört egy előfizetésen belül csak egyetlen erőforráscsoportra korlátozza. Ha a szabályzatot a teljes előfizetésre szeretné alkalmazni, és kizárni néhány erőforráscsoportot, kizárási listát is konfigurálhat. Állítsa a házirend-kényszerítési választót Engedélyezve beállításra, ha szeretné, hogy  a házirend hatása (naplózás vagy megtagadás) bekövetkezjön, vagy letiltva kapcsolja ki a hatást (naplózás vagy megtagadás).  

    ![Képernyőkép arról, hogy hol korlátozhatja a hatókört egyetlen erőforráscsoportra az előfizetésen belül.](../media/policy-img6.png)

1. Kattintson a paraméterek lapfülre a képernyő tetején a kívánt hónapokban megadott maximális érvényességi időtartam megadásához. Válassza **a naplózás** vagy megtagadás lehetőséget a szabályzat hatásának ellenőrzéshez a fenti szakaszok útmutatása alapján.  Ezután válassza a felülvizsgálat + létrehozás gombot. 

    ![Képernyőkép a Paraméterek lapról, ahol megadhatja a maximális érvényességi időszakot a kívánt hónapokban.](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Megfelelőségi eredmények megtekintése

1. Vissza a Szabályzat panelre, és válassza a Megfelelőség lapot. Kattintson arra a szabályzat-hozzárendelésre, amely megfelelőségi eredményeit meg szeretné tekinteni.

    ![Képernyőkép a Megfelelőség lapról, ahol kiválaszthatja azt a szabályzat-hozzárendelést, amely számára meg szeretné tekinteni a megfelelőségi eredményeket.](../media/policy-img8.png)

1. Ezen az oldalon szűrheti az eredményeket megfelelő vagy nem megfelelő tárolók szerint. Itt láthatja a nem megfelelő kulcstartók listáját a szabályzat-hozzárendelés hatókörében. A tároló nem megfelelőnek minősül, ha a tárolóban található összetevők (tanúsítványok) bármelyike nem megfelelő. Az egyes nem megfelelő összetevők (tanúsítványok) megtekintéséhez kiválaszthat egy adott tárolót. 


    ![Képernyőkép a szabályzat-hozzárendelés hatókörében található nem megfelelő kulcstartók listájáról.](../media/policy-img9.png)

1. A nem megfelelő tárolóban található összetevők nevének megtekintése


    ![Képernyőkép a nem megfelelő tárolóban található összetevők nevének megtekintéséről.](../media/policy-img10.png)

1. Ha ellenőriznie kell, hogy a rendszer megtagadja-e a felhasználók számára az erőforrások kulcstartón belüli létrehozására vonatkozó képességet, az Összetevőesemények **(előzetes verzió)** lapra kattintva megtekintheti a megtagadott tanúsítványműveleteket a kérelmezővel, valamint a kérelmek időbélyegeit. 


    ![A Azure Key Vault áttekintése](../media/policy-img11.png)

## <a name="feature-limitations"></a>A szolgáltatás korlátozásai

A "megtagadás" hatású szabályzatok hozzárendelése akár 30 percet (átlagos eset) és 1 órát is vehet (legrosszabb esetben) a nem megfelelő erőforrások létrehozásának megtagadása. A tároló meglévő összetevőinek szabályzatértékelése akár 1 órát (átlagos eset) és 2 órát (legrosszabb esetben) is igénybe vehet, mielőtt a megfelelőségi eredmények megtekinthetők a portál felhasználói felületén. Ha a megfelelőségi eredmények "Nincs elindítva" eredményt mutatnak, annak az alábbi okai lehetnek:
- A szabályzat értékelése még nem fejeződött be. A kezdeti kiértékelési késés a legrosszabb esetben akár 2 órát is igénybe vehet. 
- A szabályzat-hozzárendelés hatókörében nincsenek kulcstartók.
- A szabályzat-hozzárendelés hatókörébe nem tartoznak tanúsítványokkal tartozó kulcstartók.

> [!NOTE]
> Azure Policy szolgáltatói [](../../governance/policy/concepts/definition-structure.md#resource-provider-modes)módok (például az Azure Key Vault esetén) a Megfelelőséggel kapcsolatos információkat az Összetevő [megfelelősége lapon biztosítanak.](../../governance/policy/how-to/get-compliance-data.md#component-compliance)

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Policy szolgáltatásról](../../governance/policy/overview.md)
- Lásd Key Vault: [Key Vault szabályzatdefiníciók](../../governance/policy/samples/built-in-policies.md#key-vault)
