---
title: A AD FS tanúsítványok vészhelyzeti rotációja | Microsoft Docs
description: Ez a cikk az AD FS tanúsítványok azonnali visszavonását és frissítését ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 9035c0a91bbbd7493437c692540fcbb3136a094e
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613048"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>A AD FS tanúsítványok vészhelyzeti elforgatása
Abban az esetben, ha azonnal el kell forgatni az AD FS-tanúsítványokat, kövesse az ebben a szakaszban ismertetett lépéseket.

> [!IMPORTANT]
> A AD FS környezetben az alábbi lépések végrehajtásával azonnal visszavonja a régi tanúsítványokat.  Mivel ez azonnal megtörténik, az összevonási partnerei számára általában engedélyezett a szokásos idő az új tanúsítvány felhasználására. Előfordulhat, hogy az új tanúsítványok használatához a megbízhatósági kapcsolat frissül.  Ez akkor oldható fel, ha az összes összevonási partner rendelkezik az új tanúsítvánnyal.

> [!NOTE]
> A Microsoft nyomatékosan javasolja a hardveres biztonsági modul (HSM) használatát a tanúsítványok védelméhez és biztonságossá tételéhez.
> További információ: a [hardveres biztonsági modul](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm) a AD FS biztonságossá tételéhez ajánlott eljárások alatt.

## <a name="determine-your-token-signing-certificate-thumbprint"></a>A jogkivonat-aláíró tanúsítvány ujjlenyomatának meghatározása
Azon régi jogkivonat-aláíró tanúsítvány visszavonásához, amelyet AD FS jelenleg használ, meg kell határoznia a jogkivonat-sigining tanúsítvány ujjlenyomatát.  Ehhez kövesse az alábbi lépéseket:

 1. Kapcsolódás a Microsoft Online szolgáltatáshoz `PS C:\>Connect-MsolService`
 2. Dokumentálja mind a helyszíni, mind a Felhőbeli jogkivonat aláíró tanúsítványának ujjlenyomatát és lejárati dátumát.
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  Másolja le az ujjlenyomatot.  A későbbiekben a meglévő tanúsítványok eltávolítására lesz szükség.

Azt is megteheti, hogy az ujjlenyomatot AD FS felügyelettel, a szolgáltatás/tanúsítványok lehetőséggel navigál, kattintson a jobb gombbal a tanúsítványra, válassza a tanúsítvány megtekintése, majd a részletek lehetőséget. 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>Annak megállapítása, hogy AD FS-e automatikusan megújítja a tanúsítványokat
Alapértelmezés szerint a AD FS úgy van konfigurálva, hogy jogkivonat-aláírási és jogkivonat-visszafejtési tanúsítványokat állítson be automatikusan, mind a kezdeti konfigurációs időpontban, mind a tanúsítványok lejárati dátumának megközelítve.

Futtassa a következő Windows PowerShell-parancsot: `PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*` .

A Autocertificaterollover beállítást tulajdonság azt ismerteti, hogy AD FS konfigurálva van-e a jogkivonat-aláírás megújításához és a tokenek automatikus visszafejtéséhez.  Ha a Autocertificaterollover beállítást értéke TRUE (igaz), kövesse az alábbi, [új önaláírt tanúsítvány létrehozása, ha a Autocertificaterollover beállítást értéke TRUE] című részben leírt utasításokat.  Ha a Autocertificaterollover beállítást hamis értékre van állítva, kövesse az alábbi témakörben ismertetett utasításokat: [új tanúsítványok létrehozása manuálisan, ha a Autocertificaterollover beállítást hamis értékre van állítva]


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>Új önaláírt tanúsítvány generálása, ha a Autocertificaterollover beállítást értéke TRUE (igaz)
Ebben a szakaszban **két** jogkivonat-aláíró tanúsítványt fog létrehozni.  Az első a **-sürgős** jelzőt fogja használni, amely azonnal lecseréli az aktuális elsődleges tanúsítványt.  A második a másodlagos tanúsítványhoz lesz használva.  

>[!IMPORTANT]
>A két tanúsítvány létrehozása azért van így, mert az Azure az előző tanúsítvánnyal kapcsolatos információkat tartalmazza.  Egy második létrehozásával arra kényszerítjük az Azure-t, hogy bocsásson ki a régi tanúsítvánnyal kapcsolatos információkat, és cserélje le a második tanúsítványra vonatkozó információkkal.
>
>Ha nem hozza létre a második tanúsítványt, és frissíti az Azure-t, lehetséges, hogy a régi jogkivonat-aláíró tanúsítvány a felhasználók hitelesítésére szolgál.

Az új jogkivonat-aláíró tanúsítványok létrehozásához a következő lépéseket használhatja.

 1. Győződjön meg arról, hogy be van jelentkezve az elsődleges AD FS kiszolgálóra.
 2. Nyissa meg a Windows PowerShellt rendszergazdaként. 
 3. Győződjön meg arról, hogy a Autocertificaterollover beállítást értéke TRUE (igaz).
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. Új jogkivonat-aláíró tanúsítvány létrehozása: `Update-ADFSCertificate –CertificateType token-signing -Urgent` .
 5. Ellenőrizze a frissítést a következő parancs futtatásával: `Get-ADFSCertificate –CertificateType token-signing`
 6. Most létrehozza a második jogkivonat-aláíró tanúsítványt: `Update-ADFSCertificate –CertificateType token-signing` .
 7. A frissítés ellenőrzéséhez futtassa újra a következő parancsot: `Get-ADFSCertificate –CertificateType token-signing`


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>Új tanúsítványok manuális generálása, ha a Autocertificaterollover beállítást hamis értékre van állítva
Ha nem az alapértelmezett automatikusan generált, önaláírt jogkivonat-aláírási és jogkivonat-visszafejtési tanúsítványokat használja, manuálisan kell megújítania és konfigurálnia ezeket a tanúsítványokat.  Ez magában foglalja két új jogkivonat-aláíró tanúsítvány létrehozását és importálását.  Ezután előléptetheti az egyiket elsődlegesként, visszavonhatja a régi tanúsítványt, és konfigurálhatja a második tanúsítványt másodlagos tanúsítványként.

Először be kell szereznie egy két új tanúsítványt a hitelesítésszolgáltatótól, és importálnia kell őket a helyi számítógép személyes tanúsítványtárolójába az egyes összevonási kiszolgálókon. Útmutatásért tekintse meg a [tanúsítvány importálása](https://technet.microsoft.com/library/cc754489.aspx) című cikket.

>[!IMPORTANT]
>A két tanúsítvány létrehozása azért van így, mert az Azure az előző tanúsítvánnyal kapcsolatos információkat tartalmazza.  Egy második létrehozásával arra kényszerítjük az Azure-t, hogy bocsásson ki a régi tanúsítvánnyal kapcsolatos információkat, és cserélje le a második tanúsítványra vonatkozó információkkal.
>
>Ha nem hozza létre a második tanúsítványt, és frissíti az Azure-t, lehetséges, hogy a régi jogkivonat-aláíró tanúsítvány a felhasználók hitelesítésére szolgál.

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>Új tanúsítvány beállítása másodlagos tanúsítványként
Ezt követően be kell állítania egy tanúsítványt a másodlagos AD FS jogkivonat-aláírási vagy visszafejtési tanúsítványként, majd előléptetheti az elsődleges

1. A tanúsítvány importálása után. Nyissa meg a **AD FS felügyeleti** konzolt.
2. Bontsa ki a **szolgáltatás** elemet, majd válassza a **tanúsítványok** lehetőséget.
3. A Műveletek ablaktáblán kattintson a **Token-Signing tanúsítvány hozzáadása** lehetőségre.
4. Válassza ki az új tanúsítványt a megjelenített tanúsítványok listájából, majd kattintson az OK gombra.

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>Az új tanúsítvány előléptetése másodlagosról elsődlegesre
Most, hogy az új tanúsítvány be lett importálva és konfigurálva van AD FSban, elsődleges tanúsítványként kell beállítania.
1. Nyissa meg a **AD FS felügyeleti** konzolt.
2. Bontsa ki a **szolgáltatás** elemet, majd válassza a **tanúsítványok** lehetőséget.
3. Kattintson a másodlagos jogkivonat-aláíró tanúsítványra.
4. A **műveletek** ablaktáblán kattintson a **beállítás elsődlegesként** lehetőségre. A megerősítő üzenetben kattintson az Igen gombra.
5. Miután előléptette az új tanúsítványt elsődleges tanúsítványként, távolítsa el a régi tanúsítványt, mert továbbra is használható. Lásd a [régi tanúsítványok eltávolítása](#remove-your-old-certificates) szakaszt alább.  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>A második tanúsítvány beállítása másodlagos tanúsítványként
Most, hogy hozzáadta az első tanúsítványt, és elkészítette elsődlegesként, és eltávolította a régit, importálja a második tanúsítványt.  Ezt követően konfigurálnia kell a tanúsítványt másodlagos AD FS jogkivonat-aláíró tanúsítványként

1. A tanúsítvány importálása után. Nyissa meg a **AD FS felügyeleti** konzolt.
2. Bontsa ki a **szolgáltatás** elemet, majd válassza a **tanúsítványok** lehetőséget.
3. A Műveletek ablaktáblán kattintson a **Token-Signing tanúsítvány hozzáadása** lehetőségre.
4. Válassza ki az új tanúsítványt a megjelenített tanúsítványok listájából, majd kattintson az OK gombra.

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>Az Azure AD frissítése az új jogkivonat-aláíró tanúsítvánnyal
Nyissa meg a Microsoft Azure Active Directory modult a Windows PowerShellhez. Másik lehetőségként nyissa meg a Windows PowerShellt, majd futtassa a parancsot. `Import-Module msonline`

Kapcsolódjon az Azure AD-hoz a következő parancs futtatásával: `Connect-MsolService` , majd adja meg a globális rendszergazdai hitelesítő adatait.

>[!Note]
> Ha ezeket a parancsokat olyan számítógépen futtatja, amely nem az elsődleges összevonási kiszolgáló, akkor először írja be a következő parancsot: `Set-MsolADFSContext –Computer <servername>` . Cserélje le a <servername> nevet a AD FS-kiszolgáló nevére. Ezt követően adja meg a AD FS-kiszolgáló rendszergazdai hitelesítő adatait, amikor a rendszer kéri.

Ha szeretné, ellenőrizze, hogy van-e szükség frissítésre az aktuális tanúsítvány információinak ellenőrzéséhez az Azure AD-ben. Ehhez futtassa a következő parancsot: `Get-MsolFederationProperty` . Ha a rendszer kéri, adja meg az összevont tartomány nevét.

A tanúsítvány adatainak az Azure AD-ben való frissítéséhez futtassa a következő parancsot: `Update-MsolFederatedDomain` , és ha a rendszer kéri, adja meg a tartománynevet.

>[!Note]
> Ha a parancs futtatásakor hibaüzenet jelenik meg, futtassa a következő parancsot: `Update-MsolFederatedDomain –SupportMultipleDomain` , majd amikor a rendszer kéri, adja meg a tartománynevet.

## <a name="replace-ssl-certificates"></a>SSL-tanúsítványok cseréje
Abban az esetben, ha a jogkivonat-aláíró tanúsítványt egy sérülés miatt szeretné lecserélni, a AD FS és a WAP-kiszolgálók SSL-tanúsítványait is vissza kell vonnia és cserélnie kell.  

Az SSL-tanúsítványok visszavonását a tanúsítványt kiállító hitelesítésszolgáltatótól (CA) kell elvégezni.  Ezeket a tanúsítványokat gyakran harmadik féltől származó szolgáltatók, például a GoDaddy bocsátják ki.  Példa: (tanúsítvány visszavonása | SSL-tanúsítványok – a GoDaddy segít nekünk.  További információ: a [tanúsítványok visszavonásának működése](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10)?redirectedfrom=MSDN).

Miután visszavonta a régi SSL-tanúsítványt, és egy újat adott ki, lecserélheti az SSL-tanúsítványokat. További információ: [AD FS SSL-tanúsítványának cseréje](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs).


## <a name="remove-your-old-certificates"></a>A régi tanúsítványok eltávolítása
A régi tanúsítványok cseréje után távolítsa el a régi tanúsítványt, mert továbbra is használható. . Ehhez kövesse az alábbi lépéseket:.  Ehhez kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy be van jelentkezve az elsődleges AD FS kiszolgálóra.
2. Nyissa meg a Windows PowerShellt rendszergazdaként. 
4. A régi jogkivonat-aláíró tanúsítvány eltávolítása: `Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>` .

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>Összevonási metaadatokat felhasználó összevonási partnerek frissítése
Ha megújította és konfigurálta az új jogkivonat-aláírási vagy jogkivonat-visszafejtő tanúsítványt, meg kell győződnie arról, hogy az összes összevonási partner (erőforrás-szervezeti vagy fiókpartner-partner, amelyet a AD FS a függő entitások megbízhatósága és a jogcím-szolgáltatói Megbízhatóságok) megkapta az új tanúsítványokat.

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>Összevonási metaadatokat nem használó összevonási partnerek frissítése
Ha az összevonási partnerek nem használhatják fel az összevonási metaadatokat, manuálisan kell elküldeni az új jogkivonat-aláíró/jogkivonat-Visszafejtési tanúsítvány nyilvános kulcsát. Küldje el az új tanúsítvány nyilvános kulcsát (. cer fájlt vagy. p7b, ha a teljes láncot szeretné felvenni) az összes erőforrás-szervezeti vagy fiókpartner-partner számára (amelyet a AD FS a függő entitások megbízhatóságának és a jogcím-szolgáltatói megbízhatóságoknak megfelelően képvisel). A partnerek módosításokat hajtanak végre a saját oldalán, hogy megbízzanak az új tanúsítványokban.



## <a name="revoke-refresh-tokens-via-powershell"></a>Frissítési tokenek visszavonása a PowerShell használatával
Most szeretnénk visszavonni a frissítési jogkivonatokat azon felhasználók számára, akik számára lehetséges, hogy újra be kell jelentkezniük, és új jogkivonatokat kell kapniuk.  Ez a szolgáltatás a felhasználókat a telefonról, a jelenlegi webmail-munkamenetekről, valamint a jogkivonatokat és a frissítési tokeneket használó egyéb elemekről is naplózza.  [Itt](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true) talál információt, és azt is megtudhatja, hogyan [vonhatja vissza Azure Active Directory felhasználói hozzáférését](../../active-directory/enterprise-users/users-revoke-access.md).

## <a name="next-steps"></a>Következő lépések

- [SSL-tanúsítványok kezelése a AD FS és a WAP-ban Windows Server 2016](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [Jogkivonat-aláírási és jogkivonat-visszafejtési tanúsítványok beszerzése és konfigurálása AD FS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [Microsoft 365 és Azure Active Directory összevonási tanúsítványainak megújítása](how-to-connect-fed-o365-certs.md)



















