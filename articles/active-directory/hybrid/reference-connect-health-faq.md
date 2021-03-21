---
title: Azure Active Directory Connect Health GYIK – Azure | Microsoft Docs
description: Ez a gyakori kérdések a Azure AD Connect Health kapcsolatos kérdésekre adnak választ. Ez a GYIK a szolgáltatás használatával kapcsolatos kérdéseket fedi le, beleértve a számlázási modell, a képességek, a korlátozások és a támogatás témaköreit.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6185c5408f74b914ce5ad47634999786ba1d7ab6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367996"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health gyakori kérdések
Ez a cikk válaszokat tartalmaz a Azure Active Directory (Azure AD) kapcsolati állapotával kapcsolatos gyakori kérdésekre (GYIK). Ezek a gyakori kérdések a szolgáltatás használatával kapcsolatos kérdéseket foglalják magukban, beleértve a számlázási modellt, a képességeket, a korlátozásokat és a támogatást.

## <a name="general-questions"></a>Általános kérdések
**K: több Azure AD-címtárat is kezelhetek. Hogyan váltson a prémium szintű Azure Active Directoryra?**

A különböző Azure AD-bérlők közötti váltáshoz válassza ki a jelenleg bejelentkezett **felhasználónevet** a jobb felső sarokban, majd válassza ki a megfelelő fiókot. Ha a fiók nem szerepel a listán, válassza a kijelentkezés **lehetőséget, majd** használja a bejelentkezéshez engedélyezett prémium szintű Azure Active Directory (P1 vagy P2) könyvtár globális rendszergazdai hitelesítő adatait.

**K: az Azure AD Connect Health által támogatott identitás-szerepkörök verziója?**

A következő táblázat felsorolja a szerepköröket és az operációs rendszer támogatott verzióit.

|Szerepkör| Operációs rendszer/verzió|
|--|--|
|Active Directory összevonási szolgáltatások (AD FS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|
|Azure AD Connect | 1.0.9125 vagy újabb verzió|
|Active Directory tartományi szolgáltatások (AD DS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|

A Windows Server Core telepítések nem támogatottak.

Vegye figyelembe, hogy a szolgáltatás által biztosított szolgáltatások a szerepkörtől és az operációs rendszertől függően eltérőek lehetnek. Más szóval az összes funkció nem érhető el az operációs rendszer összes verziójához. A részletekért tekintse meg a szolgáltatás leírását.

**K: hány licencre van szükségem az infrastruktúra figyeléséhez?**

* Az első összekapcsolási ügynökhöz legalább egy prémium szintű Azure AD (P1 vagy P2) licenc szükséges.
* Minden további regisztrált ügynökhöz 25 további prémium szintű Azure AD (P1 vagy P2) licenc szükséges.
* Az ügynökök száma megegyezik az összes figyelt szerepkörben (AD FS, Azure AD Connect és/vagy AD DS) regisztrált ügynökök teljes számával.
* A HRE-kapcsolat licencelése nem igényli, hogy a licencet meghatározott felhasználókhoz rendelje. Csak a szükséges számú érvényes licenc szükséges.

A licencelési információk az [Azure ad díjszabási oldalán](https://aka.ms/aadpricing)is megtalálhatók.

Példa:

| Regisztrált ügynökök | Szükséges licencek | Példa figyelési konfigurációra |
| ------ | --------------- | --- |
| 1 | 1 | 1 Azure AD Connect kiszolgáló |
| 2 | 26| 1 Azure AD Connect kiszolgáló és 1 tartományvezérlő |
| 3 | 51 | 1 Active Directory összevonási szolgáltatások (AD FS) (AD FS) kiszolgáló, 1 AD FS proxy és 1 tartományvezérlő |
| 4 | 76 | 1 AD FS kiszolgáló, 1 AD FS proxy és 2 tartományvezérlő |
| 5 | 101 | 1 Azure AD Connect kiszolgáló, 1 AD FS kiszolgáló, 1 AD FS proxy és 2 tartományvezérlő |

**K: Azure AD Connect Health támogatja az Azure Germany Cloud-ot?**

A Azure AD Connect Health a németországi felhőben nem támogatott, kivéve a [szinkronizálási hibák jelentési funkcióját](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Szerepkörök | Funkciók | Támogatott a német felhőben |
| ------ | --------------- | --- |
| Kapcsolat állapota szinkronizáláshoz | Figyelés/betekintés/riasztások/elemzés | No |
|  | Szinkronizálási hibajelentés | Yes |
| Az ADFS kapcsolati állapota | Figyelés/betekintés/riasztások/elemzés | No |
| Összekapcsolási állapot a HOZZÁADÁShoz | Figyelés/betekintés/riasztások/elemzés | No |

Annak érdekében, hogy az ügynök kapcsolatba lépjen a csatlakozás állapota szinkronizáláshoz, a [telepítési követelményt](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) ennek megfelelően konfigurálja.

## <a name="installation-questions"></a>Telepítési kérdések

**K: milyen hatással van a Azure AD Connect Health-ügynök telepítése az egyes kiszolgálókra?**

A Microsoft Azure AD csatlakozási állapotfigyelő ügynök, AD FS, webalkalmazás-proxy kiszolgálók, Azure AD Connect (szinkronizáló) kiszolgálók telepítésének következményei a tartományvezérlők minimálisan vonatkoznak a CPU, a memóriahasználat, a hálózati sávszélesség és a tárterület tekintetében.

A következő számok közelítenek meg:

* CPU-felhasználás: ~ 1-5%-kal nő.
* Memóriahasználat: a teljes rendszermemória 10%-a.

> [!NOTE]
> Ha az ügynök nem tud kommunikálni az Azure-ban, az ügynök helyileg tárolja az adatkészletet a megadott maximális korláthoz. Az ügynök felülírja a "legutóbb kiszolgált" típusú "gyorsítótárazott" adatértékeket.
>
>

* Helyi puffer tároló a Azure AD Connect Health-ügynökökhöz: ~ 20 MB.
* AD FS-kiszolgálók esetében ajánlott egy 1 024 MB (1 GB) lemezterületet kiépíteni a Azure AD Connect Health ügynökök AD FS naplózási csatornája számára, hogy a rendszer felülírja az összes naplózási adat feldolgozását.

**K: újra kell indítanunk a kiszolgálókat a Azure AD Connect Health-ügynökök telepítésekor?**

Nem. Az ügynökök telepítése nem igényli a kiszolgáló újraindítását. Néhány előfeltételként szükséges lépés telepítéséhez azonban szükség lehet a kiszolgáló újraindítására.

Windows Server 2008 R2 rendszeren például a .NET 4,5 Framework telepítése a kiszolgáló újraindítását igényli.

**K: a Azure AD Connect Health egy átmenő HTTP-proxyn keresztül működik?**

Igen. A folyamatban lévő műveletek esetében beállíthatja, hogy az állapotfigyelő ügynök HTTP-proxy használatával továbbítsa a kimenő HTTP-kéréseket.
További információ a [http-proxy állapot-ügynökökhöz való konfigurálásáról](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Ha proxyt kell konfigurálnia az ügynök regisztrálása során, előfordulhat, hogy előzőleg módosítania kell az Internet Explorer-proxy beállításait.

1. Nyissa meg az Internet Explorer > **Beállítások**  >  **Internetbeállítások**  >  **kapcsolatok**  >  **LAN-beállítások** lehetőséget.
2. Válassza **a proxykiszolgáló használata a LAN**-hoz lehetőséget.
3. Válassza a **speciális** lehetőséget, ha különböző proxy portokkal rendelkezik a http és a https/Secure szolgáltatáshoz.

**K: Azure AD Connect Health támogatja az alapszintű hitelesítést a HTTP-proxykkal való csatlakozáskor?**

Nem. Az egyszerű hitelesítéshez használt tetszőleges Felhasználónév és jelszó megadására szolgáló mechanizmus jelenleg nem támogatott.

**K: milyen tűzfal-portokat kell megnyitni a Azure AD Connect Health ügynök működéséhez?**

A tűzfalak és egyéb kapcsolódási követelmények listáját a [követelmények című szakaszban](how-to-connect-health-agent-install.md#requirements) találja.

**K: Miért látok két kiszolgálót ugyanazzal a névvel a Azure AD Connect Health-portálon?**

Ha eltávolít egy ügynököt egy kiszolgálóról, a rendszer nem távolítja el automatikusan a kiszolgálót a Azure AD Connect Health-portálról. Ha manuálisan távolítja el az ügynököt egy kiszolgálóról, vagy magát a kiszolgálót távolítja el, akkor manuálisan kell törölnie a kiszolgálói bejegyzést a Azure AD Connect Health-portálról.

Előfordulhat, hogy egy kiszolgáló rendszerképét alaphelyzetbe állítja, vagy egy új kiszolgálót hoz létre ugyanazzal a részletekkel (például a számítógépnévvel). Ha nem távolítja el a már regisztrált kiszolgálót a Azure AD Connect Health-portálról, és az ügynököt az új kiszolgálóra telepítette, akkor előfordulhat, hogy két azonos nevű bejegyzés jelenik meg.

Ebben az esetben manuálisan törölje a régebbi kiszolgálóhoz tartozó bejegyzést. A kiszolgáló adatának elavultnak kell lennie.

**K: telepíthetem a Azure AD Connect Health Agent ügynököt a Windows Server Core-on?**

Nem.  A Server Core-on való telepítés nem támogatott.

## <a name="health-agent-registration-and-data-freshness"></a>Az állapotadatok regisztrálása és az adatfrissesség

**K: milyen gyakori okai vannak az állapotadatok regisztrálási hibáinak, és hogyan lehet elhárítani a problémákat?**

Az állapotfigyelő ügynök a következő lehetséges okok miatt nem tud regisztrálni:

* Az ügynök nem tud kommunikálni a szükséges végpontokkal, mert a tűzfal blokkolja a forgalmat. Ez különösen gyakori a webalkalmazás-proxy kiszolgálókon. Győződjön meg arról, hogy engedélyezett-e a kimenő kommunikáció a szükséges végpontokkal és portokkal. Részletekért tekintse meg a [követelmények szakaszt](how-to-connect-health-agent-install.md#requirements) .
* A kimenő kommunikációra a hálózati réteg TLS-ellenőrzése vonatkozik. Ez azt eredményezi, hogy az ügynök által használt tanúsítvány lecserélhető az ellenőrzési kiszolgáló/entitás számára, és az ügynök regisztrációjának befejezéséhez szükséges lépések sikertelenek lesznek.
* A felhasználónak nincs hozzáférése az ügynök regisztrációjának elvégzéséhez. A globális rendszergazdák alapértelmezés szerint rendelkeznek hozzáféréssel. Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac) használatával delegálhatja a hozzáférést más felhasználóknak.

**K: értesítést kapok arról, hogy "Állapotfigyelő szolgáltatás adatok nem naprakészek." Hogyan a probléma megoldásához?**

Azure AD Connect Health akkor hozza létre a riasztást, ha nem kapja meg az összes adatpontot a kiszolgálóról az elmúlt két órában. [További információk](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Üzemeltetési kérdések
**K: engedélyezni kell a naplózást a webalkalmazás-proxy kiszolgálókon?**

Nem, a naplózást nem kell engedélyezni a webalkalmazás-proxy kiszolgálókon.

**K: Hogyan oldható fel Azure AD Connect Health-riasztások?**

Azure AD Connect Health riasztások megoldódott a sikeres feltételhez. Azure AD Connect Health ügynökök rendszeresen azonosítják és bejelentik a szolgáltatás sikerességi feltételeit. Néhány riasztás esetén a letiltási idő alapú. Más szóval, ha ugyanazt a hibát a riasztások keletkezésének 72 órán belül nem észlelik, a rendszer automatikusan feloldja a riasztást.

**K: értesítést kapok arról, hogy a "teszt hitelesítési kérelem (szintetikus tranzakció) nem tudott beolvasni egy jogkivonatot." Hogyan a probléma megoldásához?**

A AD FS Azure AD Connect Health akkor hozza létre ezt a riasztást, ha egy AD FS kiszolgálóra telepített állapotfigyelő ügynök nem tud jogkivonatot beszerezni az állapotfigyelő ügynök által kezdeményezett szintetikus tranzakció részeként. Az állapotfigyelő szolgáltatás a helyi rendszerkörnyezetet használja, és megkísérli lekérni egy saját függő entitás jogkivonatát. Ez egy mindenre kiterjedő teszt, amely biztosítja, hogy AD FS jogkivonatok kibocsátásának állapotában legyen.

Ez a teszt általában sikertelen, mert az állapotfigyelő ügynök nem tudja feloldani a AD FS farm nevét. Ez akkor fordulhat elő, ha a AD FS-kiszolgálók hálózati terheléselosztó mögött vannak, és a rendszer a terheléselosztó mögött lévő csomóponttól kezdeményezi a kérést (a terheléselosztó előtt lévő normál ügyfél helyett). Ez a "C:\Windows\System32\drivers\etc" alatt található "hosts" fájl frissítésével oldható meg, amely tartalmazza a AD FS-kiszolgáló IP-címét vagy egy visszacsatolási IP-címet (127.0.0.1) a AD FS Farm neveként (például sts.contoso.com). A gazda fájl hozzáadása a hálózati hívás rövid összekapcsolását teszi lehetővé, így az állapotfigyelő ügynök beolvashatja a tokent.

**K: kaptam egy e-mailt, amely azt jelzi, hogy a gépek nem a legutóbbi ransomware támadásokkal kapcsolatosak. Miért kaptam ezt az e-mailt?**

Azure AD Connect Health szolgáltatás megvizsgálta az összes általa felügyelt gépet a szükséges javítások telepítésének biztosítása érdekében. A rendszer elküldte az e-mailt a bérlői rendszergazdáknak, ha legalább egy gép nem rendelkezik a kritikus javításokkal. Ezt a döntést a következő logikával használták.
1. A számítógépen telepített összes gyorsjavítás megkeresése.
2. Ellenőrizze, hogy létezik-e legalább egy gyorsjavítás a definiált listából.
3. Ha igen, a gép védett. Ha nem, a gép veszélyben van a támadás ellen.

Az alábbi PowerShell-szkripttel manuálisan is végrehajthatja ezt az ellenőrzést. Ez megvalósítja a fenti logikát.

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**K: Miért jelenik meg a <i>Get-MsolDirSyncProvisioningError PowerShell-</i> parancsmag kevesebb szinkronizálási hiba az eredményben?**

A <i>Get-MsolDirSyncProvisioningError</i> csak az rsync kiépítési hibáit fogja visszaadni. Ezen kívül a csatlakozási állapot portálon más szinkronizálási hibák is láthatók, például az exportálási hibák. Ez konzisztens Azure AD Connect különbözeti eredménysel. További információ [Azure ad Connect szinkronizálási hibákról](./tshoot-connect-sync-errors.md).

**K: Miért nem jönnek létre az ADFS-naplózások?**

Használja a <i>Get-AdfsProperties-AuditLevel PowerShell-</i> parancsmagot annak biztosítására, hogy a naplók nem letiltott állapotban legyenek. További információ az [ADFS-naplókkal](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016)kapcsolatban. Figyelje meg, hogy vannak-e olyan speciális naplózási beállítások, amelyeket az ADFS-kiszolgálóra leküldtek, auditpol.exe felülírja a módosításokat (esemény, ha az alkalmazás létrehozva nincs konfigurálva). Ebben az esetben állítsa be a helyi biztonsági házirendet, hogy naplózza az alkalmazás által generált hibákat és sikereket.

**K: Mikor kerül sor az ügynök tanúsítványának automatikus megújítására a lejárat előtt?**
Az ügynök minősítése a lejárat napja előtt **6 hónappal** automatikusan megújul. Ha nem újítják meg, gondoskodjon arról, hogy az ügynök hálózati kapcsolatai stabilak legyenek. A probléma megoldásához indítsa újra az ügynök szolgáltatásait, vagy frissítsen a legújabb verzióra.



## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health ügynök telepítése](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health műveletek](how-to-connect-health-operations.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](how-to-connect-health-adfs.md)
* [Szinkronizálás Azure AD Connect Health használata](how-to-connect-health-sync.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](how-to-connect-health-adds.md)
* [Az Azure AD Connect Health verzióelőzményei](reference-connect-health-version-history.md)
