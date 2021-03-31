---
title: Több tartomány Azure AD Connect
description: Ez a dokumentum több legfelső szintű tartomány beállítását és konfigurálását ismerteti Microsoft 365 és az Azure AD-vel.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53a0da5b5db21c9a543d39d1b252b0b4c64e2a56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306361"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Többtartományos támogatás az Azure AD összevonási szolgáltatásához
Az alábbi dokumentáció ismerteti, hogyan használható több legfelső szintű tartomány és altartomány a Microsoft 365-vagy Azure AD-tartományokkal való egyesítő.

## <a name="multiple-top-level-domain-support"></a>Több felső szintű tartományi támogatás
Az Azure AD-ben több, legfelső szintű tartomány egyesítő van szükség további konfigurálásra, amely nem szükséges, ha a egyesítő egyetlen legfelső szintű tartománnyal rendelkezik.

Ha egy tartomány összevonása az Azure AD-vel történik, a tartományhoz több tulajdonság is be van állítva az Azure-ban.  Az egyik fontos szempont a IssuerUri.  Ez a tulajdonság egy olyan URI, amelyet az Azure AD használ annak a tartománynak a azonosítására, amelyhez a jogkivonat társítva van.  Az URI-nak nem kell semmit feloldania, de érvényes URI-nak kell lennie.  Alapértelmezés szerint az Azure AD beállítja az URI-t az összevonási szolgáltatás azonosítójának értékére a helyszíni AD FS konfigurációjában.

> [!NOTE]
> Az összevonási szolgáltatás azonosítója egy URI, amely egyedileg azonosít egy összevonási szolgáltatást.  Az összevonási szolgáltatás olyan AD FS példánya, amely biztonsági jogkivonat-szolgáltatásként működik.
>
>

A IssuerUri a PowerShell-parancs használatával tekintheti meg `Get-MsolDomainFederationSettings -DomainName <your domain>` .

![Képernyőkép, amely a "Get-MsolDomainFederationSettings" parancs a PowerShellben való beírását követően jeleníti meg az eredményeket.](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Probléma merül fel, ha egynél több legfelső szintű tartományt ad hozzá.  Tegyük fel például, hogy beállította az Azure AD és a helyszíni környezet közötti összevonást.  Ehhez a dokumentumhoz a tartományt, a bmcontoso.com használja.  Most egy második, legfelső szintű tartomány, a bmfabrikam.com lett hozzáadva.

![Több legfelső szintű tartományt ábrázoló képernyőkép](./media/how-to-connect-install-multiple-domains/domains.png)

Ha a bmfabrikam.com-tartomány összevonására kísérli meg az átalakítást, hiba történik.  Ennek az az oka, hogy az Azure AD olyan korlátozással rendelkezik, amely nem teszi lehetővé, hogy a IssuerUri tulajdonság egynél több tartományhoz ugyanazt az értéket engedélyezze.  

![A PowerShellben összevonási hibát bemutató képernyőkép.](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain paraméter
A korlátozás megkerüléséhez hozzá kell adnia egy másik IssuerUri, amely a paraméter használatával végezhető el `-SupportMultipleDomain` .  Ez a paraméter a következő parancsmagokkal használható:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Ez a paraméter lehetővé teszi, hogy az Azure AD konfigurálja a IssuerUri, hogy az a tartomány nevén alapuljon.  A IssuerUri egyediek lesznek az Azure AD-ban található címtárak között.  A paraméter használata lehetővé teszi a PowerShell-parancs sikeres befejezését.

![A PowerShell-parancs sikeres befejezését bemutató képernyőkép.](./media/how-to-connect-install-multiple-domains/convert.png)

A bmfabrikam.com tartomány beállításai a következőkben láthatók:

![A "bmfabrikam.com" tartomány beállításait megjelenítő képernyőkép.](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain` a nem módosítja a többi végpontot, amelyek még úgy vannak konfigurálva, hogy az összevonási szolgáltatásra mutassanak a adfs.bmcontoso.com.

Egy másik dolog, ami `-SupportMultipleDomain` azt biztosítja, hogy a AD FS rendszer magában foglalja az Azure ad-hoz kiállított jogkivonatok megfelelő kibocsátói értékét. Ezt az értéket úgy állítja be, hogy a felhasználói UPN tartomány részét képezi, és azt a IssuerUri, azaz a https://{UPN-utótag}/ADFS/Services/Trust. adja meg tartományként.

Így az Azure AD-ben vagy a Microsoft 365-ben végzett hitelesítés során a rendszer a felhasználó jogkivonatában található IssuerUri elemet használja a tartomány megkereséséhez az Azure AD-ben. Ha nem található egyezés, a hitelesítés sikertelen lesz.

Ha például egy felhasználó UPN bsimon@bmcontoso.com -je, a jogkivonat IssuerUri eleme AD FS problémákra lesz állítva `http://bmcontoso.com/adfs/services/trust` . Ez az elem megfelel az Azure AD-konfigurációnak, és a hitelesítés sikeres lesz.

A következő a testre szabott jogcím-szabály, amely megvalósítja ezt a logikát:

```
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));
```


> [!IMPORTANT]
> Ahhoz, hogy a-SupportMultipleDomain kapcsolót az új vagy már meglévő tartományok konvertálásakor próbálja használni, az összevont megbízhatósági kapcsolatnak már be kell állítania, hogy támogassa őket.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>A AD FS és az Azure AD közötti megbízhatósági kapcsolat frissítése
Ha nem állította be az összevont megbízhatósági kapcsolatot AD FS és az Azure AD-példány között, előfordulhat, hogy újra létre kell hoznia ezt a megbízhatósági kapcsolatot.  Ennek az az oka, hogy ha az eredetileg a paraméter nélkül van beállítva `-SupportMultipleDomain` , a IssuerUri az alapértelmezett értékkel van beállítva.  Az alábbi képernyőképen látható, hogy a IssuerUri van beállítva `https://adfs.bmcontoso.com/adfs/services/trust` .

Ha sikeresen hozzáadott egy új tartományt az Azure AD-portálon, majd megpróbálta átalakítani a használatával `Convert-MsolDomaintoFederated -DomainName <your domain>` , a következő hibaüzenet jelenik meg.

![Képernyőkép, amely egy összevonási hibát jelenít meg a PowerShellben, miután új tartományt próbált átalakítani az "Convert-MsolDomaintoFederated" paranccsal.](./media/how-to-connect-install-multiple-domains/trust1.png)

Ha megpróbálja hozzáadni a `-SupportMultipleDomain` kapcsolót, a következő hibaüzenet jelenik meg:

![A "-SupportMultipleDomain" kapcsoló hozzáadását követően az összevonási hibát bemutató képernyőkép.](./media/how-to-connect-install-multiple-domains/trust2.png)

Az eredeti tartományon történő futtatásra tett kísérlet `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` hibát is eredményez.

![Összevonási hiba](./media/how-to-connect-install-multiple-domains/trust3.png)

Az alábbi lépéseket követve további legfelső szintű tartományt adhat hozzá.  Ha már hozzáadott egy tartományt, és nem használta a `-SupportMultipleDomain` paramétert, kezdje az eredeti tartomány eltávolításának és frissítésének lépéseivel.  Ha még nem adott hozzá legfelső szintű tartományt, a Azure AD Connect PowerShell-lel való hozzáadásának lépéseivel kezdheti meg a következő lépéseket:.

A következő lépésekkel távolíthatja el a Microsoft Online megbízhatósági kapcsolatot, és frissítheti az eredeti tartományt.

1. A AD FS összevonási kiszolgálón nyissa meg a **AD FS felügyeletet.**
2. A bal oldalon bontsa ki a **megbízhatósági kapcsolatok** és a **függő entitások megbízhatóságai** elemet.
3. A jobb oldalon törölje a **Microsoft Office 365 Identity platform** bejegyzést.
   ![A Microsoft Online eltávolítása](./media/how-to-connect-install-multiple-domains/trust4.png)
4. Olyan gépen, amelyen telepítve van a [Windows PowerShell Azure Active Directory modulja](/previous-versions/azure/jj151815(v=azure.100)) , az alábbi parancsot futtatja: `$cred=Get-Credential` .  
5. Adja meg a egyesítő Azure AD-tartományhoz tartozó globális rendszergazda felhasználónevét és jelszavát.
6. A PowerShellben adja meg a `Connect-MsolService -Credential $cred`
7. A PowerShellben adja meg a értéket `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain` .  Ez a frissítés az eredeti tartományhoz készült.  A fenti tartományokat a következő módon használhatja:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Az új legfelső szintű tartomány a PowerShell használatával való hozzáadásához kövesse az alábbi lépéseket.

1. Olyan gépen, amelyen telepítve van a [Windows PowerShell Azure Active Directory modulja](/previous-versions/azure/jj151815(v=azure.100)) , az alábbi parancsot futtatja: `$cred=Get-Credential` .  
2. Adja meg a egyesítő Azure AD-tartományhoz tartozó globális rendszergazda felhasználónevét és jelszavát.
3. A PowerShellben adja meg a `Connect-MsolService -Credential $cred`
4. A PowerShellben adja meg a `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

A következő lépésekkel adhatja hozzá az új legfelső szintű tartományt a Azure AD Connect használatával.

1. Azure AD Connect elindítása az asztalról vagy a Start menüből
2. Válassza a "további Azure ad-tartomány hozzáadása" ![ képernyőképet, amely megjeleníti a további feladatok lapot a "további Azure ad-tartomány hozzáadása" lapon.](./media/how-to-connect-install-multiple-domains/add1.png)
3. Adja meg Azure AD-és Active Directory hitelesítő adatait
4. Válassza ki az összevonáshoz konfigurálni kívánt második tartományt.
   ![További Azure AD-tartomány hozzáadása](./media/how-to-connect-install-multiple-domains/add2.png)
5. Kattintson a Telepítés gombra

### <a name="verify-the-new-top-level-domain"></a>Az új felső szintű tartomány ellenőrzése
A PowerShell-parancs használatával `Get-MsolDomainFederationSettings -DomainName <your domain>` megtekintheti a frissített IssuerUri.  Az alábbi képernyőképen látható, hogy az összevonási beállítások frissítve lettek az eredeti tartományban `http://bmcontoso.com/adfs/services/trust`

![Képernyőkép, amely az eredeti tartományon frissített összevonási beállításokat jeleníti meg.](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Az új tartomány IssuerUri pedig a következőre lett beállítva `https://bmfabrikam.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Altartományok támogatása
Ha hozzáad egy altartományt, az Azure AD által kezelt tartományok módja miatt a rendszer örökli a szülő beállításait.  Tehát a IssuerUri meg kell egyeznie a szülőkkel.

Tehát tegyük fel például, hogy bmcontoso.com, majd hozzáadom a corp.bmcontoso.com.  A corp.bmcontoso.com felhasználójának IssuerUri kell lennie **`http://bmcontoso.com/adfs/services/trust`** .  Az Azure AD esetében azonban a fentiekben ismertetett standard szabály a kiállítóként generált jogkivonatot eredményez **`http://corp.bmcontoso.com/adfs/services/trust`** . a tartomány nem felel meg a szükséges értéknek és a hitelesítés sikertelen lesz.

### <a name="how-to-enable-support-for-subdomains"></a>Az altartományok támogatásának engedélyezése
Ennek a viselkedésnek a megkerüléséhez a Microsoft Online AD FS függő entitás megbízhatóságát frissíteni kell.  Ehhez konfigurálnia kell egy egyéni jogcím-szabályt úgy, hogy az a felhasználó UPN-utótagjának összes altartományát kivágja az egyéni kiállítói érték kiépítésekor.

A következő jogcím ezt teszi:

```    
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
```

[!NOTE]
A reguláris kifejezésben szereplő utolsó szám azt határozza meg, hogy hány szülő tartomány van a gyökértartomány tartományában. Itt bmcontoso.com van használatban, így két szülő tartományra van szükség. Ha három szülő tartományt kell megőrizni (például: corp.bmcontoso.com), akkor a szám három volt. Végül egy tartomány is megadható, és a egyezés mindig a maximális tartománynak megfelelően fog történni. " {2,3} " két-három tartományhoz fog illeszkedni (például: bmfabrikam.com és Corp.bmcontoso.com).

A következő lépésekkel adhat hozzá egyéni jogcímet az altartományok támogatásához.

1. AD FS felügyelet megnyitása
2. Kattintson a jobb gombbal a Microsoft Online RP-megbízhatóság elemre, és válassza a jogcím-szabályok szerkesztése lehetőséget.
3. Válassza ki a harmadik jogcím szabályát, és cserélje le az ![ igény szerinti módosítást.](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Az aktuális jogcím cseréje:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
   ```
    nevű és

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
   ```

    ![Jogcím cseréje](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Kattintson az OK gombra.  Kattintson az Alkalmaz gombra.  Kattintson az OK gombra.  Zárja be az AD FS felügyeleti konzolt.

## <a name="next-steps"></a>Következő lépések
Miután az Azure AD Connect telepítése megtörtént, [ellenőrizheti a telepítést, és hozzárendelheti a licenceket](how-to-connect-post-installation.md).

Ismerkedjen meg a következő, a telepítéssel engedélyezett szolgáltatásokkal: az [Automatikus frissítés](how-to-connect-install-automatic-upgrade.md), a [Véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md) és az [Azure AD Connect Health](how-to-connect-health-sync.md).

Ismerje meg részletesebben a következő általános témaköröket: [az ütemező és a szinkronizálási események indítása](how-to-connect-sync-feature-scheduler.md).

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).