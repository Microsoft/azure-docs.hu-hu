---
title: Azure AD Connect – a userCertificate attribútum által okozott LargeObject hibák | Microsoft Docs
description: Ez a témakör a userCertificate attribútum által okozott hibák LargeObject kapcsolatos szervizelési lépéseket ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b419e0f24201d661ad0f5f1373022ea6e9e9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861748"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect Sync: a userCertificate attribútum által okozott LargeObject hibák kezelésére

Az Azure AD a **userCertificate** attribútumhoz legfeljebb **15** tanúsítvány értékét kényszeríti ki. Ha Azure AD Connect több mint 15 értékkel rendelkező objektumot exportál az Azure AD-be, az Azure AD **LargeObject** hibaüzenetet ad vissza a következő üzenettel:

>*"A kiépített objektum túl nagy. Az objektum attribútumai számának levágása. A rendszer újrapróbálkozik a művelettel a következő szinkronizálási ciklusban... "*

A LargeObject hibát más AD-attribútumok is okozhatják. Annak megerősítéséhez, hogy valóban a userCertificate attribútum okozza, ellenőriznie kell az objektumot a helyszíni AD-ben vagy a [synchronization Service Manager metaverse-keresésben](./how-to-connect-sync-service-manager-ui-mvsearch.md).

Az alábbi módszerek egyikével szerezheti be a bérlőben található objektumok listáját LargeObject-hibákkal:

 * Ha a bérlő Azure AD Connect Health szinkronizálásra van engedélyezve, tekintse meg a [szinkronizálási hibajelentési jelentést](./how-to-connect-health-sync.md) .
 
 * Az egyes szinkronizálási ciklusok végén küldött, címtár-szinkronizálási hibákkal kapcsolatos értesítő e-mailek a LargeObject-hibákat tartalmazó objektumok listáját tartalmazza. 
 * Az [synchronization Service Manager Operations (műveletek) lapon](./how-to-connect-sync-service-manager-ui-operations.md) a LargeObject hibákkal rendelkező objektumok listája jelenik meg, ha rákattint a legutóbbi Exportálás az Azure ad-ba műveletre.
 
## <a name="mitigation-options"></a>Kockázatcsökkentő beállítások
Amíg a LargeObject hiba fel nem oldódik, az ugyanazon objektumra irányuló egyéb attribútumok nem exportálhatók az Azure AD-be. A hiba elhárításához a következő lehetőségek közül választhat:

 * Frissítse Azure AD Connect a 1.1.524.0 vagy újabb verzióra. Azure AD Connect build 1.1.524.0 a beépített szinkronizálási szabályok frissültek, hogy ne exportálják az attribútumok userCertificate és userSMIMECertificate, ha az attribútumok több mint 15 értékkel rendelkeznek. A Azure AD Connect frissítésével kapcsolatos további információkért tekintse meg a [Azure ad Connect: frissítés korábbi verzióról a legújabbra](./how-to-upgrade-previous-version.md)című cikket.

 * Hozzon létre egy **kimenő szinkronizálási szabályt** a Azure ad Connectban, amely **Null értéket exportál az olyan objektumok tényleges értékei helyett, amelyek több mint 15 tanúsítvány értékkel rendelkeznek**. Ez a beállítás akkor lehet megfelelő, ha nem követeli meg az Azure AD-ba exportálni kívánt összes tanúsítvány értékét a több mint 15 értékkel rendelkező objektumok számára. A szinkronizálási szabály megvalósításával kapcsolatos részletekért tekintse meg a következő szakaszt a [szinkronizálási szabály megvalósítása a userCertificate attribútum exportálásának korlátozására](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute)című szakaszban.

 * Csökkentse a tanúsítvány-értékek számát a helyszíni AD-objektumon (15 vagy kevesebb) a szervezet által már nem használt értékek eltávolításával. Ez akkor megfelelő, ha a duzzadt attribútumot lejárt vagy fel nem használt tanúsítványok okozzák. Az [itt elérhető PowerShell-szkript](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) segítségével megkeresheti, biztonsági másolatot készíthet, és törölheti a lejárt tanúsítványokat a helyszíni ad-ben. A tanúsítványok törlése előtt javasoljuk, hogy ellenőrizze a szervezetben a nyilvános kulcsú infrastruktúra rendszergazdáit.

 * Konfigurálja Azure AD Connect úgy, hogy kizárják a userCertificate attribútumot az Azure AD-be való exportáláshoz. Általánosságban elmondható, hogy ezt a beállítást nem ajánlott használni, mivel az attribútumot a Microsoft Online Services is használhatja bizonyos forgatókönyvek engedélyezéséhez. Elsősorban:
    * A felhasználói objektum userCertificate attribútumát az Exchange Online és az Outlook-ügyfelek használják az üzenetek aláírására és titkosítására. A szolgáltatással kapcsolatos további tudnivalókért tekintse meg az [üzenetek aláírására és titkosítására vonatkozó, az S/MIME](/microsoft-365/security/office-365-security/s-mime-for-message-signing-and-encryption)-t ismertető cikket.

    * Az Azure AD az userCertificate attribútumot használja a számítógép-objektumon, hogy a Windows 10 helyszíni tartományhoz csatlakoztatott eszközei csatlakozzanak az Azure AD-hoz. A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure ad-hez Windows 10-es](../devices/hybrid-azuread-join-plan.md)környezetben című cikket.

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>A userCertificate attribútum exportálásának korlátozására szolgáló szinkronizálási szabály implementálása
A userCertificate attribútum által okozott LargeObject-hiba megoldásához létrehozhat egy kimenő szinkronizálási szabályt a Azure AD Connectban, amely **Null értéket exportál a tényleges értékek helyett a több mint 15 tanúsítvány értékkel rendelkező objektumok esetében**. Ez a szakasz a **felhasználói** objektumok szinkronizálási szabályának megvalósításához szükséges lépéseket ismerteti. A lépések a **kapcsolattartási** és a **számítógép** -objektumok esetében módosíthatók.

> [!IMPORTANT]
> A Null érték exportálása eltávolítja a korábban az Azure AD-be sikeresen exportált tanúsítvány-értékeket.

A lépések a következőképpen foglalhatók össze:

1. Tiltsa le a szinkronizálási ütemező szolgáltatást, és ellenőrizze, hogy nincs-e folyamatban szinkronizálás.
3. Keresse meg a userCertificate attribútumhoz tartozó meglévő kimenő szinkronizálási szabályt.
4. Hozza létre a szükséges kimenő szinkronizálási szabályt.
5. Ellenőrizze az új szinkronizálási szabályt egy meglévő objektumon LargeObject hibával.
6. Alkalmazza az új szinkronizálási szabályt a LargeObject-hibával hátralévő objektumokra.
7. Ellenőrizze, hogy nincsenek-e váratlan módosítások az Azure AD-ba való exportálásra.
8. Exportálja a módosításokat az Azure AD-be.
9. Engedélyezze újra a szinkronizálási ütemező funkciót.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. lépés Tiltsa le a szinkronizálási ütemező szolgáltatást, és ellenőrizze, hogy nincs-e folyamatban szinkronizálás
Győződjön meg arról, hogy az új szinkronizálási szabály megvalósítása során nincs szükség szinkronizálásra, hogy elkerülje az Azure AD-ba való nem kívánt módosítások elkerülését. A beépített szinkronizálási ütemező letiltása:
1. Indítsa el a PowerShell-munkamenetet a Azure AD Connect kiszolgálón.

2. Ütemezett szinkronizálás letiltása a (z) parancsmag futtatásával: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Az előző lépések csak a beépített Feladatütemezővel rendelkező Azure AD Connect újabb verzióira (1.1. xxx. x) érvényesek. Ha a Windows Feladatütemezőt használó Azure AD Connect régebbi verzióit (1.0. xxx. x) használja, vagy a saját egyéni ütemező (nem gyakori) használatával indítja el az időszakos szinkronizálást, ezeket ennek megfelelően kell letiltania.

1. A **synchronization Service Manager** elindításához nyissa meg a Start → szinkronizációs szolgáltatást.

1. Lépjen az **Operations (műveletek** ) lapra, és ellenőrizze, hogy nincs *-e "folyamatban"* állapotú művelet.

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>2. lépés A userCertificate attribútum meglévő kimenő szinkronizálási szabályának megkeresése
Egy meglévő szinkronizálási szabálynak kell lennie, amely engedélyezve van, és úgy van konfigurálva, hogy a felhasználói objektumok userCertificate attribútumát exportálja az Azure AD-be. Keresse meg ezt a szinkronizálási szabályt, hogy megtalálja a **sorrendjét** és a **hatóköri szűrő** konfigurációját:

1. Indítsa el a **szinkronizálási szabályok szerkesztőjét** a Start → szinkronizációs szabályok szerkesztőjével.

2. Konfigurálja a keresési szűrőket a következő értékekkel:

    | Attribútum | Érték |
    | --- | --- |
    | Irány |**Kimenő** |
    | MV-objektum típusa |**Személy** |
    | Összekötő |*Az Azure AD-összekötő neve* |
    | Összekötő objektum típusa |**felhasználói** |
    | MV-attribútum |**userCertificate** |

3. Ha OOB (beépített) szinkronizálási szabályokat használ az Azure AD connectorhoz a felhasználói objektumok userCertficiate-attribútumának exportálásához, akkor vissza kell kérnie az *"out to HRE – User ExchangeOnline"* szabályt.
4. Jegyezze fel a szinkronizálási szabály **prioritási** értékét.
5. Válassza ki a szinkronizálási szabályt, és kattintson a **Szerkesztés** gombra.
6. A *"fenntartott szabály jóváhagyásának szerkesztése"* előugró ablakban kattintson a **nem** gombra. (Ne aggódjon, nem teszünk semmilyen változást a szinkronizálási szabályban).
7. A Szerkesztés képernyőn válassza a hatókör- **szűrő** lapot.
8. Jegyezze fel a hatókör-szűrő konfigurációját. Ha a OOB szinkronizálási szabályt használja, akkor pontosan **egy két záradékot tartalmazó hatókör-szűrő csoportnak** kell lennie, beleértve a következőket:

    | Attribútum | Operátor | Érték |
    | --- | --- | --- |
    | sourceObjectType | EGYENLŐ | User |
    | cloudMastered | NOTEQUAL | Igaz |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>3. lépés A szükséges kimenő szinkronizálási szabály létrehozása
Az új szinkronizálási szabálynak ugyanazzal a **hatókör-szűrővel** és **magasabb prioritással** kell rendelkeznie, mint a meglévő szinkronizálási szabálynak. Ez biztosítja, hogy az új szinkronizálási szabály ugyanarra az objektumra vonatkozzon, mint a meglévő szinkronizálási szabály, és felülbírálja a userCertificate attribútum meglévő szinkronizálási szabályát. A szinkronizálási szabály létrehozása:
1. A szinkronizálási szabályok szerkesztőben kattintson az **új szabály hozzáadása** gombra.
2. A **Leírás lapon** adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | --- | --- | --- |
    | Name | *Adjon meg egy nevet* | Például *: "out to HRE – egyéni felülbírálás a userCertificate"* |
    | Description | *Adja meg a leírást* | Például: *"Ha a userCertificate attribútum több mint 15 értékkel rendelkezik, akkor a null értéket exportálja."* |
    | Csatlakoztatott rendszerek | *Válassza ki az Azure AD-összekötőt* |
    | Csatlakoztatott rendszerobjektum típusa | **felhasználói** | |
    | Metaverse objektum típusa | **személy** | |
    | Hivatkozás típusa | **Csatlakozás** | |
    | Precedencia | *1-99 közötti számot választott* | A választott számot nem szabad egyetlen meglévő szinkronizálási szabály sem használni, és alacsonyabb értékkel (és így magasabb prioritással) kell rendelkeznie, mint a meglévő szinkronizálási szabály. |

3. Lépjen a **hatókör-szűrő** lapra, és implementálja ugyanazt a hatókör-szűrőt, amelyet a meglévő szinkronizálási szabály használ.
4. Ugorja át az **illesztési szabályok** lapot.
5. Ugrás az **átalakítások** lapra új átalakítás hozzáadásához a következő konfiguráció használatával:

    | Attribútum | Érték |
    | --- | --- |
    | Tördelés típusa |**Expression** |
    | Cél attribútum |**userCertificate** |
    | Forrás attribútum |*Használja a következő kifejezést*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. A szinkronizálási szabály létrehozásához kattintson a **Hozzáadás** gombra.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>4. lépés: Az új szinkronizálási szabály ellenőrzése LargeObject hibával rendelkező meglévő objektumon
Ezzel a megoldással ellenőrizheti, hogy a létrehozott szinkronizálási szabály megfelelően működik-e egy meglévő AD-objektumon, LargeObject-hiba esetén, mielőtt alkalmazza azt más objektumokra:
1. Lépjen a Synchronization Service Manager **Operations (műveletek** ) lapjára.
2. Válassza ki a legutóbbi Exportálás az Azure AD-ba műveletet, és kattintson az egyik objektumra LargeObject-hibákkal.
3.  Az összekötő terület objektum tulajdonságai előugró ablakban kattintson az **előnézet** gombra.
4. Az előugró ablakban válassza a **teljes szinkronizálás** lehetőséget, majd kattintson a **véglegesítés várható eredménye** elemre.
5. Az előnézet képernyő és az összekötő terület objektum tulajdonságai képernyő bezárásához.
6. Nyissa meg a Synchronization Service Manager **Összekötők** lapját.
7. Kattintson a jobb gombbal az **Azure ad** -összekötőre, és válassza a **Futtatás...** lehetőséget.
8. Az összekötő futtatása előugró ablakban válassza az **Exportálás** lépés lehetőséget, majd kattintson **az OK** gombra.
9. Várjon, amíg az Azure AD-ba való exportálás befejeződik, és ellenőrizze, hogy nincs-e több LargeObject hiba az adott objektumra vonatkozóan.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>5. lépés Az új szinkronizálási szabály alkalmazása a LargeObject hibával hátralévő objektumokra
A szinkronizálási szabály hozzáadása után futtatnia kell egy teljes szinkronizálási lépést az AD-összekötőn:
1. Nyissa meg a Synchronization Service Manager **Összekötők** lapját.
2. Kattintson a jobb gombbal az **ad** -összekötőre, és válassza a **Futtatás...** lehetőséget.
3. Az összekötő futtatása előugró ablakban válassza a **teljes szinkronizálás** lépést, és kattintson **az OK** gombra.
4. Várjon, amíg befejeződik a teljes szinkronizálási lépés.
5. Ha több AD-összekötővel rendelkezik, ismételje meg a fenti lépéseket a fennmaradó AD-összekötők esetében. Általában több összekötőre van szükség, ha több helyszíni címtárral rendelkezik.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>6. lépés Ellenőrizze, hogy az Azure AD-ba való exportálásra váró váratlan módosítások nem vártak-e
1. Nyissa meg a Synchronization Service Manager **Összekötők** lapját.
2. Kattintson a jobb gombbal az **Azure ad** -összekötőre, és válassza az **összekötő terület keresése** lehetőséget.
3. A keresési összekötő területének előugró ablakában:
    1. Hatókör beállítása **függőben lévő exportálásra**.
    2. Jelölje be a 3 jelölőnégyzetet, beleértve a **Hozzáadás**, a **módosítás** és a **Törlés** jelölőnégyzetet.
    3. Kattintson a **Keresés** gombra az összes olyan objektum visszaküldéséhez, amelynek módosításai az Azure ad-be való exportálásra várnak.
    4. Ellenőrizze, hogy nincsenek-e váratlan módosítások. Egy adott objektum változásainak vizsgálatához kattintson duplán az objektumra.

### <a name="step-7-export-the-changes-to-azure-ad"></a>7. lépés A módosítások exportálása az Azure AD-be
A módosítások exportálása az Azure AD-be:
1. Nyissa meg a Synchronization Service Manager **Összekötők** lapját.
2. Kattintson a jobb gombbal az **Azure ad** -összekötőre, és válassza a **Futtatás...** lehetőséget.
4. Az összekötő futtatása előugró ablakban válassza az **Exportálás** lépés lehetőséget, majd kattintson **az OK** gombra.
5. Várjon, amíg az Azure AD-ba való exportálás befejeződik, és ellenőrizze, hogy nincs-e több LargeObject hiba.

### <a name="step-8-re-enable-sync-scheduler"></a>8. lépés A szinkronizálási ütemező újbóli engedélyezése
Most, hogy a probléma megoldódott, engedélyezze újra a beépített szinkronizálási ütemező funkciót:
1. Indítsa el a PowerShell-munkamenetet.
2. Engedélyezze újra az ütemezett szinkronizálást a (z) parancsmag futtatásával: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Az előző lépések csak a beépített Feladatütemezővel rendelkező Azure AD Connect újabb verzióira (1.1. xxx. x) érvényesek. Ha a Windows Feladatütemezőt használó Azure AD Connect régebbi verzióit (1.0. xxx. x) használja, vagy a saját egyéni ütemező (nem gyakori) használatával indítja el az időszakos szinkronizálást, ezeket ennek megfelelően kell letiltania.

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
