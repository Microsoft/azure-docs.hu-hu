---
title: Az Azure MFA-kiszolgáló frissítése – Azure Active Directory
description: Lépések és útmutatás az Azure-Multi-Factor Authentication-kiszolgáló újabb verzióra való frissítéséhez.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/12/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbddd2eb52414827561d8896dfc8bc9ff705f41b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97584391"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>A legújabb Azure Multi-Factor Authentication-kiszolgáló frissítése

Ez a cikk végigvezeti az Azure Multi-Factor Authentication (MFA) Server 6.0-s vagy újabb verziójának frissítésének folyamatán. Ha frissítenie kell a PhoneFactor-ügynök egy korábbi verzióját, tekintse meg a [PhoneFactor-ügynök frissítése az Azure multi-Factor Authentication-kiszolgáló](howto-mfaserver-deploy-upgrade-pf.md)-ra című témakört.

Ha v6. x-ről vagy régebbiről a v7. x vagy újabb verzióra frissít, az összes összetevő a .NET 2,0-ről a .NET 4,5-re vált. Az összes összetevőhöz a Microsoft Visual C++ 2015 újraterjeszthető frissítése 1 vagy újabb verzió szükséges. Az MFA-kiszolgáló telepítője az összetevők x86-os és x64-es verzióját is telepíti, ha még nincsenek telepítve. Ha a felhasználói portál és a Mobile App Web Service különálló kiszolgálókon fut, akkor az összetevők frissítése előtt telepítenie kell ezeket a csomagokat. A Microsoft [letöltőközpontból](https://www.microsoft.com/download/)megkeresheti a legújabb Microsoft Visual C++ 2015 terjeszthető frissítést. 

> [!IMPORTANT]
> Az 2019. július 1-től a Microsoft már nem kínál az MFA-kiszolgálót az új üzemelő példányokhoz. Azok az új ügyfelek, amelyek a bejelentkezési események során a többtényezős hitelesítést (MFA) szeretnék megkövetelni, felhőalapú Azure AD-Multi-Factor Authenticationt kell használniuk.
>
> A felhőalapú MFA megismeréséhez tekintse meg az [oktatóanyag: felhasználói bejelentkezési események biztonságossá tétele az Azure ad-vel multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Az MFA-kiszolgálót az 2019. július 1. előtt aktivált meglévő ügyfelek letöltik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon előállítják az aktiválási hitelesítő adatokat.

Frissítés lépéseinek áttekintése:

* Azure MFA-kiszolgálók frissítése (alárendeltek, majd elsődleges)
* A felhasználói portál példányainak frissítése
* A AD FS-adapter példányainak frissítése

## <a name="upgrade-azure-mfa-server"></a>Az Azure MFA-kiszolgáló frissítése

1. Az Azure- [multi-Factor Authentication-kiszolgáló letöltése](howto-mfaserver-deploy.md#download-the-mfa-server) az Azure MFA-kiszolgáló telepítőjének legújabb verziójára című cikkben található utasításokat követve olvasható.
2. Készítsen biztonsági másolatot az MFA-kiszolgáló adatfájljáról, amely a C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (feltéve, hogy az alapértelmezett telepítési hely) az elsődleges MFA-kiszolgálón található.
3. Ha több kiszolgálót is futtat a magas rendelkezésre állás érdekében, módosítsa az MFA-kiszolgálóra hitelesítő ügyfélszoftvereket, hogy ne küldjenek forgalmat a frissített kiszolgálókra. Ha Load balancert használ, távolítsa el az alárendelt MFA-kiszolgálót a terheléselosztó közül, végezze el a frissítést, majd adja hozzá újra a kiszolgálót a farmhoz.
4. Futtassa az új telepítőt az egyes MFA-kiszolgálókon. Először frissítse az alárendelt kiszolgálókat, mert elolvashatják az elsődleges által replikált régi adatfájlt.

   > [!NOTE]
   > A kiszolgálók frissítésekor el kell távolítani a terheléselosztásról vagy más MFA-kiszolgálókkal való forgalom megosztásáról.
   >
   > A telepítő futtatása előtt nem kell eltávolítania az aktuális MFA-kiszolgálót. A telepítő helyben történő frissítést hajt végre. A telepítési útvonal a korábbi telepítésből származó beállításjegyzékből lett kiválasztva, ezért az ugyanarra a helyre települ (például C:\Program Files\Multi-Factor Authentication Server).
  
5. Ha a rendszer kéri, hogy telepítsen egy Microsoft Visual C++ 2015 újraterjeszthető frissítési csomagot, fogadja el a kérést. A csomag x86-os és x64-es verziója is telepítve van.
6. Ha a Web Service SDK-t használja, a rendszer kéri, hogy telepítse az új Web Service SDK-t. Az új Web Service SDK telepítésekor ellenőrizze, hogy a virtuális könyvtár neve megegyezik-e a korábban telepített virtuális könyvtárral (például Phonefactorwebservicesdk).
7. Ismételje meg a lépéseket az összes alárendelt kiszolgálón. Léptesse elő a beosztottak egyikét az új elsődlegesnek, majd frissítse a régi elsődleges kiszolgálót.

## <a name="upgrade-the-user-portal"></a>A felhasználói portál frissítése

A szakaszra való áttérés előtt fejezze be az MFA-kiszolgálók frissítését.

1. Készítsen biztonsági másolatot a felhasználói portál telepítési helyének virtuális könyvtárában található web.config fájlról (például C:\inetpub\wwwroot\MultiFactorAuth). Ha bármilyen változás történt az alapértelmezett témában, készítsen biztonsági másolatot a App_Themes \Default mappáról is. Jobb, ha létrehoz egy másolatot az alapértelmezett mappáról, és új témát hoz létre, mint az alapértelmezett téma módosításához.
2. Ha a felhasználói portál ugyanazon a kiszolgálón fut, mint a többi MFA-kiszolgáló összetevő, az MFA-kiszolgáló telepítése arra kéri, hogy frissítse a felhasználói portált. Fogadja el a parancssort, és telepítse a felhasználói portál frissítését. Győződjön meg arról, hogy a virtuális könyvtár neve megegyezik a korábban telepített virtuális könyvtárral (például MultiFactorAuth).
3. Ha a felhasználói portál a saját kiszolgálóján található, másolja a MultiFactorAuthenticationUserPortalSetup64.msi fájlt az egyik MFA-kiszolgáló telepítési helyére, és helyezze a felhasználói portál webkiszolgálóra. Indítsa el a telepítőt.

   Ha hiba történik, "a Microsoft Visual C++ 2015 újraterjeszthető frissítésének 1. vagy újabb verziója szükséges", töltse le és telepítse a legújabb frissítési csomagot a [Microsoft letöltőközpontból](https://www.microsoft.com/download/). Telepítse az x86-os és x64-es verziókat is.

4. A frissített felhasználói portál szoftver telepítése után hasonlítsa össze az 1. lépésben az új web.config fájllal készített web.config biztonsági mentést. Ha az új web.config nem léteznek új attribútumok, másolja a biztonsági mentési web.config a virtuális könyvtárba, hogy felülírja az újat. Egy másik lehetőség a appSettings-értékek és a webszolgáltatás SDK URL-címének másolása/beillesztése a biztonságimásolat-fájlból az új web.configba.

Ha több kiszolgálón van a felhasználói portál, ismételje meg a telepítést.

## <a name="upgrade-the-mobile-app-web-service"></a>A Mobile App Web Service frissítése

> [!NOTE]
> Az 8,0-nál régebbi Azure MFA-kiszolgáló verzióról 8.0 +-ra való frissítés után a Mobile App Web Service a frissítés után is eltávolítható

## <a name="upgrade-the-ad-fs-adapters"></a>A AD FS adapterek frissítése

Az erre a szakaszra való áttérés előtt fejezze be az MFA-kiszolgálók és a felhasználói portál frissítését.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Ha az MFA különböző kiszolgálókon fut, mint AD FS

Ezek az utasítások csak akkor érvényesek, ha a Multi-Factor Authentication-kiszolgáló külön futtatja a AD FS-kiszolgálókról. Ha mindkét szolgáltatás ugyanazon a kiszolgálón fut, ugorja át ezt a szakaszt, és folytassa a telepítési lépésekkel. 

1. Mentse a AD FSban regisztrált MultiFactorAuthenticationAdfsAdapter.config fájl másolatát, vagy exportálja a konfigurációt a következő PowerShell-parancs használatával: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]` . Az adapter neve "WindowsAzureMultiFactorAuthentication" vagy "AzureMfaServerAuthentication" lehet a korábban telepített verziótól függően.
2. Másolja a következő fájlokat az MFA-kiszolgáló telepítési helyéről a AD FS kiszolgálókra:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Szerkessze a Register-MultiFactorAuthenticationAdfsAdapter.ps1 szkriptet a `-ConfigurationFilePath [path]` parancs végére való hozzáadásával `Register-AdfsAuthenticationProvider` . Cserélje le a *[Path]* fájlt a MultiFactorAuthenticationAdfsAdapter.config fájl teljes elérési útjára, vagy az előző lépésben exportált konfigurációs fájlra.

   Tekintse meg az új MultiFactorAuthenticationAdfsAdapter.config attribútumait, és ellenőrizze, hogy megfelelnek-e a régi konfigurációs fájlnak. Ha az új verzióban bármely attribútum hozzá lett adva vagy el lett távolítva, másolja az attribútum értékeit a régi konfigurációs fájlból az újat, vagy módosítsa a régi konfigurációs fájlt az egyeztetéshez.

### <a name="install-new-ad-fs-adapters"></a>Új AD FS adapterek telepítése

> [!IMPORTANT]
> A felhasználók nem lesznek szükségesek kétlépéses ellenőrzés végrehajtásához a szakasz 3-8. lépésében. Ha több fürtben van AD FS konfigurálva, a leállás elkerülése érdekében a többi fürttől függetlenül eltávolíthatja, frissítheti és visszaállíthatja a farmban lévő összes fürtöt.

1. Távolítson el néhány AD FS kiszolgálót a farmból. Frissítse ezeket a kiszolgálókat, amíg a többiek még futnak.
2. Telepítse az új AD FS-adaptert az AD FS farmból eltávolított összes kiszolgálóra. Ha az MFA-kiszolgáló minden AD FS-kiszolgálóra telepítve van, az MFA-kiszolgáló rendszergazdai UX-en keresztül frissítheti. Ellenkező esetben frissítse MultiFactorAuthenticationAdfsAdapterSetup64.msi futtatásával.

   Ha hiba történik, "a Microsoft Visual C++ 2015 újraterjeszthető frissítésének 1. vagy újabb verziója szükséges", töltse le és telepítse a legújabb frissítési csomagot a [Microsoft letöltőközpontból](https://www.microsoft.com/download/). Telepítse az x86-os és x64-es verziókat is.

3. Nyissa meg a **AD FS**  >  **hitelesítési házirendek**  >  **globális többtényezős hitelesítési házirend szerkesztése** lehetőséget. Törölje a **WindowsAzureMultiFactorAuthentication** vagy a **AzureMFAServerAuthentication** (a telepített aktuális verziótól függően).

   Ha ez a lépés elkészült, a kétlépéses ellenőrzés az MFA-kiszolgálón keresztül nem érhető el ebben a AD FS-fürtben, amíg el nem végzi a 8. lépést.

4. A Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell-parancsfájl futtatásával szüntesse meg a AD FS-adapter korábbi verziójának regisztrációját. Győződjön meg arról, hogy a *-Name* paraméter ("WindowsAzureMultiFactorAuthentication" vagy "AzureMFAServerAuthentication") megegyezik a 3. lépésben megjelenő névvel. Ez az összes olyan kiszolgálóra vonatkozik, amely ugyanabban a AD FS fürtben van, mivel központi konfiguráció van.
5. Regisztrálja az új AD FS adaptert a Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell-parancsfájl futtatásával. Ez az összes olyan kiszolgálóra vonatkozik, amely ugyanabban a AD FS fürtben van, mivel központi konfiguráció van.
6. Indítsa újra a AD FS szolgáltatást minden olyan kiszolgálón, amely el lett távolítva a AD FS farmról.
7. Adja hozzá újra a frissített kiszolgálókat a AD FS farmhoz, és távolítsa el a többi kiszolgálót a farmból.
8. Nyissa meg a **AD FS**  >  **hitelesítési házirendek**  >  **globális többtényezős hitelesítési házirend szerkesztése** lehetőséget. **AzureMfaServerAuthentication**-keresés.
9. Ismételje meg a 2. lépést a AD FS farmból eltávolított kiszolgálók frissítéséhez, majd indítsa újra a AD FS szolgáltatást ezeken a kiszolgálókon.
10. Adja hozzá ezeket a kiszolgálókat a AD FS farmhoz.

## <a name="next-steps"></a>Következő lépések

* Példák [speciális forgatókönyvekre az Azure multi-Factor Authentication és a harmadik féltől származó virtuális magánhálózatok](howto-mfaserver-nps-vpn.md) esetében

* [MFA-kiszolgáló szinkronizálása a Windows Server Active Directory](howto-mfaserver-dir-ad.md)

* [Windows-hitelesítés konfigurálása](howto-mfaserver-windows.md) az alkalmazásokhoz
