---
title: Az Azure Key Vault integrálása az Azure Policyval
description: Ismerje meg, hogyan integrálhatja a Azure Key Vaultt Azure Policy
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 10/15/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 6ac4d0e0744bfc82a686671234e013b2dd717146
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92927753"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Az Azure Key Vault integrálása az Azure Policyval

A [Azure Policy](../../governance/policy/index.yml) egy irányítási eszköz, amely lehetővé teszi a felhasználók számára az Azure-környezetek nagy léptékű naplózását és kezelését. A Azure Policy lehetővé teszi az Azure-erőforrások guardrails elhelyezését annak biztosítása érdekében, hogy azok megfeleljenek a hozzárendelt szabályzati szabályoknak. Lehetővé teszi a felhasználók számára az Azure-környezet naplózásának, valós idejű kényszerítésének és szervizelésének elvégzését. A szabályzat által végrehajtott ellenőrzések eredményei elérhetők lesznek a felhasználók számára a megfelelőségi irányítópulton, ahol láthatják, hogy mely erőforrások és összetevők megfelelőek, és melyek nem.  További információ: a [Azure Policy szolgáltatás áttekintése](../../governance/policy/overview.md).

Példa használati forgatókönyvekre:

- Szeretné javítani a vállalata biztonsági állapotát azáltal, hogy bevezeti a vállalat kulcstartójában a minimális kulcs méretére és a tanúsítványok maximális érvényességi idejére vonatkozó követelményeket, de nem tudja, hogy mely csapatok lesznek megfelelőek, és melyek nem.
- Jelenleg nincs megoldás a szervezeten belüli ellenőrzés elvégzésére, vagy a környezet kézi naplózására, ha a szervezeten belül egyéni csapatokat kér a megfelelőségük jelentéséhez. Ennek a feladatnak az automatizálására keres rá, valós időben végezheti el a naplózást, és garantálhatja a naplózás pontosságát.
- Szeretné kényszeríteni a vállalati biztonsági házirendeket, és állítsa le a magánszemélyeket önaláírt tanúsítványok létrehozásához, de nem rendelkezik automatikus módszerrel a létrehozásuk blokkolásához. 
- Szeretné kipróbálni néhány követelményt a tesztelési csapatoknak, de az éles környezetben is szigorú szabályozást szeretne fenntartani. Az erőforrások kényszerítésének elkülönítéséhez egyszerű automatizált módszerre van szükség.
- Biztos lehet benne, hogy az új szabályzatok érvényesítését egy élő helyen lévő probléma esetén szeretné visszaállítani. A szabályzat kényszerítésének kikapcsolásához egykattintásos megoldásra van szükség. 
- Egy külső gyártótól származó megoldásra támaszkodik a környezet naplózására, és belső Microsoft-ajánlatot szeretne használni.

## <a name="types-of-policy-effects-and-guidance"></a>A házirend hatásának és útmutatásának típusai

**Naplózás**: Ha egy házirend hatása naplózásra van beállítva, a házirend nem okoz változást a környezetében. Csak olyan összetevőkre figyelmeztet, amelyek nem felelnek meg a szabályzat-definícióknak egy adott hatókörön belül, ha ezeket az összetevőket nem megfelelőként jelöli meg a szabályzat megfelelőségi irányítópultján. A naplózás alapértelmezett, ha nincs kiválasztva házirend-effektus.

**Megtagadás**: Ha egy házirend hatására a Megtagadás érték van beállítva, a házirend letiltja az új összetevők, például a tanúsítványok létrehozását, valamint a meglévő összetevők olyan új verzióinak letiltását, amelyek nem felelnek meg a házirend-definíciónak. A kulcstartón belüli meglévő, nem megfelelő erőforrások nem érintettek. A "naplózás" funkció továbbra is működni fog.

## <a name="available-built-in-policy-definitions"></a>Elérhető "beépített" szabályzat-definíciók

Key Vault létrehozott egy szabályzatot, amely a kulcs, a tanúsítvány és a titkos objektumok kezelésére használható. Ezek a szabályzatok beépítettek, ami azt jelenti, hogy nincs szükség egyéni JSON-írásra, hogy azok elérhetők legyenek, és a Azure Portal a hozzárendeléshez. Továbbra is testreszabhatja bizonyos paramétereket, hogy illeszkedjenek a szervezet igényeihez.

# <a name="certificate-policies"></a>[Tanúsítvány-házirendek](#tab/certificates)

### <a name="certificates-should-have-the-specified-maximum-validity-period-preview"></a>A tanúsítványoknak a megadott maximális érvényességi időtartammal kell rendelkezniük (előzetes verzió)

Ezzel a szabályzattal kezelheti a Key vaultban tárolt tanúsítványok maximális érvényességi időtartamát. Jó biztonsági gyakorlat a tanúsítványok maximális érvényességi időtartamának korlátozására. Ha a tanúsítvány titkos kulcsát az észlelés nélkül feltörték, a rövid életű tanúsítványok segítségével kis mértékben csökkentheti a folyamatos károsodások időbeli keretét, és csökkenti a tanúsítvány értékét egy támadó számára.

### <a name="certificates-should-use-allowed-key-types-preview"></a>A tanúsítványoknak engedélyezett típusú kulcsokat kell használniuk (előzetes verzió)

Ez a szabályzat lehetővé teszi a kulcstartóban lévő tanúsítványok típusának korlátozását. Ennek a szabályzatnak a használatával meggyőződhet arról, hogy a tanúsítvány titkos kulcsa RSA, ECC vagy HSM-biztonsági másolat. A következő listából választhat, hogy milyen típusú tanúsítványok engedélyezettek.

- RSA
- RSA – HSM
- ECC
- ECC – HSM

### <a name="certificates-should-have-the-specified-lifetime-action-triggers-preview"></a>A tanúsítványoknak meg kell adni a megadott élettartam műveleti eseményindítókat (előzetes verzió)

Ez a szabályzat lehetővé teszi, hogy kezelje a lejáratuk meghatározott számú napjain vagy a felhasználható élettartamának bizonyos százalékában megadott élettartam-műveletet.

### <a name="certificates-should-be-issued-by-the-specified-integrated-certificate-authority-preview"></a>A tanúsítványokat a megadott integrált hitelesítésszolgáltató (előzetes verzió) alapján kell kiállítani.

Ha Key Vault integrált hitelesítésszolgáltatót (Digicert vagy GlobalSign) használ, és azt szeretné, hogy a felhasználók egy vagy több szolgáltatót használjanak, akkor ezt a házirendet használhatja a kijelölés naplózásához vagy érvényesítéséhez. Ez a szabályzat az önaláírt tanúsítványok a Key vaultban való létrehozásának naplózására és megtagadására is használható.

### <a name="certificates-should-be-issued-by-the-specified-non-integrated-certificate-authority-preview"></a>A tanúsítványokat a megadott nem integrált hitelesítésszolgáltatótól kell kibocsátani (előzetes verzió)

Ha belső hitelesítésszolgáltatót vagy a Key Vault szolgáltatással nem integrált hitelesítésszolgáltatót használ, és azt szeretné, hogy a felhasználók a megadott listából hitelesítő szolgáltatót használjanak, ezt a házirendet használhatja a hitelesítésszolgáltatók engedélyezési listájának létrehozásához a kiállító neve alapján. Ez a szabályzat az önaláírt tanúsítványok a Key vaultban való létrehozásának naplózására és megtagadására is használható.

### <a name="certificates-using-elliptic-curve-cryptography-should-have-allowed-curve-names-preview"></a>Az elliptikus görbe titkosítását használó tanúsítványoknak engedélyezett görbe-névvel kell rendelkezniük (előzetes verzió)

Ha elliptikus görbe típusú titkosítást vagy ECC-tanúsítványokat használ, az alábbi listából testreszabhatja a görbe neveinek engedélyezett listáját. Az alapértelmezett beállítás lehetővé teszi az összes alábbi görbe nevét.

- P-256
- P – 256K
- P-384
- P-521

## <a name="certificates-using-rsa-cryptography-manage-minimum-key-size-for-rsa-certificates-preview"></a>Az RSA-titkosítást használó tanúsítványok kezelik az RSA-tanúsítványok minimális kulcsának méretét (előzetes verzió)

Ha RSA-tanúsítványokat használ, kiválaszthatja, hogy a tanúsítványoknak milyen minimális méretűnek kell lennie. Az alábbi listából választhatja ki az egyik lehetőséget.

- 2048 bit
- 3072 bit
- 4096 bit

## <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>A megadott számú napon belül lévő tanúsítványok kezelése (előzetes verzió)

A szolgáltatás leállást tapasztalhat, ha egy nem megfelelően figyelt tanúsítvány nem kerül elforgatásra a lejárat előtt. Ez a szabályzat kritikus fontosságú annak biztosításához, hogy a Key vaultban tárolt tanúsítványok figyelése megtörténjen. Azt javasoljuk, hogy a szabályzatot többször alkalmazza különböző lejárati küszöbértékekkel, például 180, 90, 60 és 30 napos küszöbértékekkel. Ez a szabályzat a tanúsítvány lejáratának figyelésére és osztályozására használható a szervezetben.

# <a name="key-policies"></a>[Kulcsfontosságú házirendek](#tab/keys)

### <a name="keys-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>A kulcsok nem lehetnek aktívak a megadott számú napnál hosszabb ideig (előzetes verzió)

Ha azt szeretné, hogy a kulcsok a megadott számú napnál hosszabb ideig ne legyenek aktívak, akkor ezzel a házirenddel naplózhatja, hogy a kulcs mennyi ideig aktív.

**Ha a kulcs aktiválási dátummal van beállítva**, akkor ez a szabályzat a kulcs **aktiválási dátumával** eltelt napok számát számítja ki az aktuális dátumra. Ha a napok száma meghaladja a beállított küszöbértéket, a kulcs a szabályzatnak nem megfelelőként lesz megjelölve.

**Ha a kulcs nem rendelkezik aktiválási dátummal beállítva**, akkor ez a szabályzat a kulcs **létrehozási dátumától** számított napok számát számítja ki az aktuális dátumra. Ha a napok száma meghaladja a beállított küszöbértéket, a kulcs a szabályzatnak nem megfelelőként lesz megjelölve.

### <a name="keys-should-be-the-specified-cryptographic-type-rsa-or-ec-preview"></a>A kulcsnak a megadott RSA vagy EC titkosítási típusnak kell lennie (előzetes verzió)

Ez a szabályzat lehetővé teszi a Key vaultban található kulcsok típusának korlátozását. Ezzel a szabályzattal meggyőződhet arról, hogy a kulcsok RSA-, ECC-vagy HSM-biztonsági mentés alatt állnak. A következő listából választhat, hogy milyen típusú tanúsítványok engedélyezettek.

- RSA
- RSA – HSM
- ECC
- ECC – HSM

### <a name="keys-using-elliptic-curve-cryptography-should-have-the-specified-curve-names-preview"></a>Az elliptikus görbe titkosítást használó kulcsoknak a megadott görbe-nevekkel kell rendelkezniük (előzetes verzió)

Ha elliptikus görbe típusú titkosítást vagy ECC-kulcsot használ, az alábbi listából testreszabhatja a görbe neveinek engedélyezett listáját. Az alapértelmezett beállítás lehetővé teszi az összes alábbi görbe nevét.

- P-256
- P – 256K
- P-384
- P-521

### <a name="keys-should-have-expirations-dates-set-preview"></a>A kulcsoknak be kell állítani a lejárat dátumát (előzetes verzió)

Ez a házirend naplózza a kulcstartók összes kulcsát, és a nem megfelelőként beállított lejárati dátummal rendelkező kulcsokat. Ezt a házirendet használhatja a lejárati dátummal nem rendelkező kulcsok létrehozásának blokkolására is.

### <a name="keys-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>A kulcsoknak többnek kell lenniük a lejárat előtt megadott számú nappal (előzetes verzió)

Ha egy kulcs túl le van zárva a lejárathoz, akkor a kulcs elforgatására szolgáló szervezeti késleltetés kimaradást eredményezhet. A kulcsokat a lejárat előtt megadott számú nappal kell elforgatni, hogy elegendő idő álljon rendelkezésre a hibákra való reagáláshoz. Ez a szabályzat azokat a kulcsokat fogja naplózni, amelyek a lejárati dátumhoz képest túl lezárulnak, és a küszöbértéket napokban állíthatja be. Ezt a házirendet is használhatja, hogy megakadályozza a lejárati dátumhoz tartozó új kulcsok létrehozását.

### <a name="keys-should-be-backed-by-a-hardware-security-module-preview"></a>A kulcsokat hardveres biztonsági modulnak (előzetes verzió) kell támogatnia

A HSM egy hardveres biztonsági modul, amely a kulcsokat tárolja. A HSM fizikai védelmi réteget biztosít a titkosítási kulcsokhoz. A titkosítási kulcs nem hagyhat olyan fizikai HSM-t, amely nagyobb biztonsági szintet biztosít, mint a szoftver kulcsa. Egyes szervezetek megfelelőségi követelményekkel rendelkeznek, amelyek felhatalmazzák a HSM-kulcsok használatát. Ezzel a szabályzattal naplózhatja a kulcstartóban tárolt kulcsokat, amelyek nem HSM-biztonsági mentéssel rendelkeznek. Ezt a házirendet használhatja arra is, hogy letiltsa a HSM-t nem támogató új kulcsok létrehozását. Ez a szabályzat az összes, az RSA és az ECC típusú kulcsra érvényes lesz.

### <a name="keys-using-rsa-cryptography-should-have-a-specified-minimum-key-size-preview"></a>Az RSA-titkosítást használó kulcsoknak meg kell egyezniük a minimális kulcs méretével (előzetes verzió)

A kisebb méretű RSA-kulcsok használata nem biztonságos tervezési gyakorlat. Előfordulhat, hogy olyan naplózási és minősítési szabványokra van szüksége, amely a minimális kulcs méretének használatát bízza meg. A következő házirend lehetővé teszi a Key Vault minimális kulcs méretre vonatkozó követelményének megadását. Olyan kulcsokat is naplózhat, amelyek nem felelnek meg ennek a minimális követelménynek. Ezzel a szabályzattal letiltható az új kulcsok létrehozása, amelyek nem felelnek meg a minimális kulcs méretére vonatkozó követelménynek.

### <a name="keys-should-have-the-specified-maximum-validity-period-preview"></a>A kulcsoknak a megadott maximális érvényességi időtartammal kell rendelkezniük (előzetes verzió)

A szervezeti megfelelőségi követelmények kezeléséhez adja meg azt a maximális időtartamot napokban, ameddig egy kulcs érvényes lehet a kulcstartón belül. A megadott küszöbértéknél hosszabb ideig érvényes kulcsok nem megfelelőként lesznek megjelölve. Ezt a házirendet használhatja arra is, hogy letiltsa a megadott érvényességi időtartamnál hosszabb lejárati dátummal rendelkező új kulcsok létrehozását.

# <a name="secret-policies"></a>[Titkos szabályzatok](#tab/secrets)

### <a name="secrets-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>A titkok nem lehetnek aktívak a megadott számú napnál hosszabb ideig (előzetes verzió)

Ha azt szeretné, hogy a titkos kódok a megadott számú napnál hosszabb ideig ne legyenek aktívak, akkor ezt a házirendet követve naplózhatja, hogy mennyi ideig volt aktív a titka.

**Ha a titkos kulcs aktiválási dátummal van beállítva**, akkor ez a szabályzat a titok **aktiválási dátumával** eltelt napok számát számítja ki az aktuális dátumra. Ha a napok száma meghaladja a beállított küszöbértéket, a titkos kulcs a szabályzatnak nem megfelelőként lesz megjelölve.

**Ha a titka nem rendelkezik aktiválási dátummal**, akkor ez a szabályzat a titok **létrehozásának dátumától** számított napok számát számítja ki az aktuális dátumig. Ha a napok száma meghaladja a beállított küszöbértéket, a titkos kulcs a szabályzatnak nem megfelelőként lesz megjelölve.

### <a name="secrets-should-have-content-type-set-preview"></a>A titkoknak rendelkezniük kell a tartalomtípus-készlettel (előzetes verzió)

Bármely egyszerű szöveges vagy kódolt fájl tárolható kulcstartó-titokként. Előfordulhat azonban, hogy a szervezet különböző rotációs házirendeket és korlátozásokat kíván beállítani a jelszavakra, a kapcsolódási karakterláncokra vagy a kulcsként tárolt tanúsítványokra vonatkozóan. A tartalomtípus címkéje segíthet a felhasználóknak megtekinteni, hogy mit tárolnak a titkos objektumokban a titkos kulcs értékének olvasása nélkül. Ezt a házirendet használhatja olyan titkok naplózására, amelyek nem rendelkeznek tartalomtípus-címkékkel. Ezt a házirendet arra is használhatja, hogy megakadályozza, hogy új titkokat hozzon létre, ha nem rendelkeznek tartalomtípus-címkével.

### <a name="secrets-should-have-expiration-date-set-preview"></a>A titkokat le kell állítani a lejárat dátumánál (előzetes verzió)

Ez a házirend naplózza a kulcstartó összes titkos kulcsát, és megtekinti a nem megfelelőként beállított lejárati dátummal rendelkező titkokat. Ezt a házirendet használhatja a lejárati dátummal nem rendelkező titkos kulcsok létrehozásának letiltásához is.

### <a name="secrets-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>A titkoknak a lejárat előtt a megadott számú nappal hosszabbnak kell lenniük (előzetes verzió)

Ha egy titkos kulcs túl lezárult a lejárathoz, a titkos kód elforgatásának szervezeti késése kimaradást eredményezhet. A titkokat a lejárat előtt a megadott számú nappal kell elforgatni, hogy elegendő idő álljon rendelkezésre a hibákra való reagáláshoz. Ez a szabályzat azokat a titkokat naplózza, amelyek túl leállnak a lejárati dátumhoz, és lehetővé teszik a küszöbérték napokon történő beállítását. Ezt a házirendet is használhatja, hogy megakadályozza a lejárati dátumhoz képest túl új titkos kódok létrehozását.

### <a name="secrets-should-have-the-specified-maximum-validity-period-preview"></a>A titoknak meg kell egyeznie a megadott maximális érvényességi időtartammal (előzetes verzió)

A szervezeti megfelelőségi követelmények kezeléséhez adja meg azt a maximális időtartamot napokban, ameddig a titkos kulcs érvényes lehet a kulcstartón belül. A megadott küszöbértéknél hosszabb ideig érvényes titkos kódok nem megfelelőként lesznek megjelölve. Ezt a házirendet használhatja arra is, hogy letiltsa a megadott érvényességi időtartamnál hosszabb lejárati dátummal rendelkező új titkok létrehozását.

---

## <a name="example-scenario"></a>Példaforgatókönyv

Az 100-es tanúsítványokat tartalmazó több csapat által használt kulcstartót kezelheti, és biztosítania kell, hogy a kulcstartóban lévő egyik tanúsítvány ne legyen két évnél hosszabb ideig érvényes.

1. A tanúsítványok hozzárendeléséhez meg **kell adni a megadott maximális érvényességi időszakra** vonatkozó házirendet, meg kell adnia, hogy a tanúsítvány maximális érvényességi időtartama 24 hónap, és a szabályzat hatása "naplózás" értékre van állítva. 
1. A [megfelelőségi jelentést a Azure Portal](#view-compliance-results)tekintheti meg, és azt is felfedezheti, hogy 20 tanúsítvány nem megfelelő, és > 2 évig érvényes, és a fennmaradó tanúsítványoknak megfelelőek. 
1. Felveszi Önnel a kapcsolatot a tanúsítványok tulajdonosainak, és közli az új biztonsági követelménysel, hogy a tanúsítványok nem lehetnek 2 évnél hosszabb ideig érvényesek. Néhány csapat válaszol, és a tanúsítványok 15-én megújítva a maximális érvényességi időtartam 2 év vagy kevesebb. Más csapatok nem válaszolnak, és továbbra is 5 nem megfelelő tanúsítvány található a kulcstartóban.
1. Megváltoztathatja a "megtagadás" beállításhoz rendelt szabályzat hatásait. Az 5 nem megfelelő tanúsítvány nem vonható vissza, és továbbra is működni fognak. Azonban a két évnél hosszabb érvényességi időtartammal nem újítható meg. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Key Vault szabályzat engedélyezése és kezelése a Azure Portal használatával

### <a name="select-a-policy-definition"></a>Házirend-definíció kiválasztása

1. Jelentkezzen be az Azure Portalra. 
1. Keressen rá a "szabályzat" kifejezésre a keresősáv alatt, és válassza a **házirend** lehetőséget.

    ![A keresősáv megjelenítő képernyőkép.](../media/policy-img1.png)

1. A házirend ablakban válassza a **definíciók** lehetőséget.

    ![Képernyőkép a definíciók beállításról.](../media/policy-img2.png)

1. A kategória szűrőben törölje az **összes kijelölése** lehetőséget, majd válassza a **Key Vault** lehetőséget. 

    ![A Kategória szűrőt és a kiválasztott Key Vault kategóriát megjelenítő képernyőkép.](../media/policy-img3.png)

1. Most meg kell jelennie a nyilvános előzetes verzióhoz elérhető összes házirendnek, Azure Key Vault. Győződjön meg arról, hogy elolvasta és megértette a szabályzattal kapcsolatos útmutatást, és válassza ki azt a szabályzatot, amelyet hozzá szeretne rendelni egy hatókörhöz.  

    ![A nyilvános előzetes verzióhoz elérhető szabályzatokat bemutató képernyőkép.](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Szabályzat társítása hatókörhöz 

1. Válassza ki az alkalmazni kívánt szabályzatot, ebben a példában a **tanúsítvány kezelése érvényességi időszaka** házirend látható. Kattintson a hozzárendelés gombra a bal felső sarokban.

    ![A tanúsítvány érvényességi időtartamának kezelése házirendet megjelenítő képernyőkép.](../media/policy-img5.png)
  
1. Válassza ki azt az előfizetést, amelyre alkalmazni szeretné a szabályzatot. Dönthet úgy, hogy az előfizetésen belül csak egy erőforráscsoporthoz korlátozza a hatókört. Ha a szabályzatot a teljes előfizetésre szeretné alkalmazni, és néhány erőforráscsoportot kizár, akkor a kizárási listát is konfigurálhatja. Állítsa be úgy a házirend-kényszerítési választót, hogy **engedélyezve** legyen, ha azt szeretné, hogy a házirend hatása (naplózás vagy megtagadás) **ki legyen kapcsolva** . 

    ![Képernyőfelvétel: Itt választhatja ki, hogy a hatókör csak egyetlen erőforráscsoport számára legyen korlátozva egy előfizetésen belül.](../media/policy-img6.png)

1. Kattintson a képernyő felső részén található parameters (paraméterek) fülre, hogy megadja a maximális érvényességi időtartamot a kívánt hónapokban. A fenti részekben ismertetett útmutatást követve válassza a **naplózás** vagy a **Megtagadás** lehetőséget a szabályzat hatásához. Ezután válassza a felülvizsgálat + létrehozás gombot. 

    ![A parameters (paraméterek) lapot megjelenítő képernyőkép, amelyen megadhatja a maximális érvényességi időtartamot a kívánt hónapokban.](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Megfelelési eredmények megtekintése

1. Lépjen vissza a szabályzat panelre, és válassza a megfelelőség lapot. Kattintson arra a szabályzat-hozzárendelésre, amelyben meg szeretné tekinteni a megfelelőségi eredményeket.

    ![A megfelelőség lapot megjelenítő képernyőkép, ahol kiválaszthatja azt a szabályzat-hozzárendelést, amelyre vonatkozóan meg szeretné tekinteni a megfelelőségi eredményeket.](../media/policy-img8.png)

1. Ezen a lapon a megfelelő vagy nem megfelelő tárolók alapján szűrheti az eredményeket. Itt láthatja a szabályzat-hozzárendelés hatókörén belül nem megfelelő kulcstartók listáját. A tár nem megfelelőnek minősül, ha a tároló egyik összetevője (tanúsítványa) nem megfelelő. Kiválaszthat egy egyéni tárolót az egyes nem megfelelő összetevők (tanúsítványok) megtekintéséhez. 


    ![Képernyőkép, amely a szabályzat-hozzárendelés hatókörén belüli nem megfelelő kulcstartók listáját jeleníti meg.](../media/policy-img9.png)

1. A nem megfelelő tárolóban található összetevők nevének megtekintése


    ![Képernyőfelvétel: Itt megtekintheti a nem megfelelő tárolóban található összetevők nevét.](../media/policy-img10.png)

1. Ha ellenőriznie kell, hogy a felhasználók megtagadják-e az erőforrások létrehozását a Key vaulton belül, kattintson az **összetevő-események (előzetes verzió)** fülre, és tekintse meg a megtagadott tanúsítvány-műveletek összefoglalását a kérelmező és a kérelmek időbélyegével. 


    ![A Azure Key Vault működésének áttekintése](../media/policy-img11.png)

## <a name="feature-limitations"></a>A szolgáltatás korlátozásai

A "megtagadás" hatású szabályzatok kiosztása akár 30 percig is eltarthat (átlagos eset) és 1 óra (legrosszabb esetben), hogy megtagadja a nem megfelelő erőforrások létrehozását. Egy tár meglévő összetevőinek kiértékelése akár 1 órát (átlagos esetet) és 2 órát (legrosszabb esetben) is igénybe vehet, mielőtt a portál felhasználói felületén megtekintsék a megfelelőségi eredményeket. Ha a megfelelőségi eredmények "nem elindítottként" jelennek meg, akkor az a következő okok miatt fordulhat elő:
- A szabályzat-értékelés még nem fejeződött be. A kezdeti értékelési késés a legrosszabb esetben akár 2 órát is igénybe vehet. 
- Nincsenek kulcstárolók a szabályzat-hozzárendelés hatókörében.
- Nincsenek kulcstárolók a szabályzat-hozzárendelés hatókörében található tanúsítványokkal.

> [!NOTE]
> Azure Policy [erőforrás-szolgáltatói módokat](../../governance/policy/concepts/definition-structure.md#resource-provider-modes), például a Azure Key Vaulthoz tartozókat, adja meg a megfelelőségi adatokat az [összetevő megfelelőségi](../../governance/policy/how-to/get-compliance-data.md#component-compliance) lapján.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Policy szolgáltatásról](../../governance/policy/overview.md)
- Lásd Key Vault példákat: [Key Vault beépített szabályzat-definíciókat](../../governance/policy/samples/built-in-policies.md#key-vault)
