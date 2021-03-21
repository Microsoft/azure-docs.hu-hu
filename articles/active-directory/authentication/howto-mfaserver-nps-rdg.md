---
title: RDG és Azure MFA-kiszolgáló RADIUS-Azure Active Directory használatával
description: Ez az Azure Multi-Factor Authentication-oldal segítséget nyújt a távoli asztali (RD) átjáró és a RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló telepítéséhez.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff89e8c803e0edf5245a62d625a6367d68de96ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742068"
---
# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló

A Távoli asztal (RD) átjáró gyakran a helyi [hálózati házirend-szolgáltatást (NPS)](/windows-server/networking/core-network-guide/core-network-guide#BKMK_optionalfeatures) használja a felhasználók hitelesítéséhez. Ez a cikk azt ismerteti, hogyan továbbíthatóak a távoli asztali átjáró RADIUS-kérései (a helyi NPS-en keresztül) a Multi-Factor Authentication-kiszolgáló számára. Az Azure MFA és a távoli asztali átjáró kombinációja azt jelenti, hogy a felhasználói bárhonnan elérhetik a munkahelyi környezeteiket, erős hitelesítés mellett.

Mivel a terminálszolgáltatások Windows-hitelesítése nem támogatott Server 2012 R2 esetén, a távoli asztali átjáró és a RADIUS használatával érheti el az MFA-kiszolgálóval való integrációt.

Az Azure Multi-Factor Authentication-kiszolgálót külön kiszolgálóra kell telepíteni, amely a RADIUS-kérést proxykapcsolaton keresztül visszaadja az NPS-nek a távoli asztali átjárókiszolgálón. Miután az NPS hitelesíti a felhasználónevet és a jelszót, választ küld a Multi-Factor Authentication-kiszolgálónak. Az MFA-kiszolgáló ekkor elvégzi a hitelesítés második tényezőjét, és visszaküldi az eredményt az átjárónak.

> [!IMPORTANT]
> Az 2019. július 1-től a Microsoft már nem kínál az MFA-kiszolgálót az új üzemelő példányokhoz. Azok az új ügyfelek, amelyek a bejelentkezési események során a többtényezős hitelesítést (MFA) szeretnék megkövetelni, felhőalapú Azure AD-Multi-Factor Authenticationt kell használniuk.
>
> A felhőalapú MFA megismeréséhez tekintse meg az [oktatóanyag: felhasználói bejelentkezési események biztonságossá tétele az Azure ad-vel multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Ha felhőalapú MFA-t használ, tekintse meg az [Azure-beli RADIUS-hitelesítés integrálásával multi-Factor Authentication](howto-mfa-nps-extension.md).
>
> Az MFA-kiszolgálót az 2019. július 1. előtt aktivált meglévő ügyfelek letöltik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon előállítják az aktiválási hitelesítő adatokat.

## <a name="prerequisites"></a>Előfeltételek

- Tartományhoz csatlakoztatott Azure MFA-kiszolgáló. Ha még nincs telepítve, kövesse az [Azure Multi-Factor Authentication-kiszolgáló – első lépések](howto-mfaserver-deploy.md) című szakasz lépéseit.
- Egy meglévő konfigurált NPS-kiszolgáló.
- A hitelesítést az NPS szolgáltatásokkal végző távoli asztali átjáró.

> [!NOTE]
> Ezt a cikket csak az MFA-kiszolgáló üzemelő példányait kell használni, nem az Azure MFA-t (felhőalapú).

## <a name="configure-the-remote-desktop-gateway"></a>A távoli asztali átjáró konfigurálása

Konfigurálja úgy a távoli asztali átjárót, hogy a RADIUS-hitelesítést egy Azure Multi-Factor Authentication-kiszolgálónak küldje el.

1. A Távoli asztali átjárókezelőben kattintson a jobb gombbal a kiszolgáló nevére, és válassza a **Tulajdonságok** lehetőséget.
2. Lépjen a **Távoli asztali kapcsolat engedélyezési házirendjeinek tárolója** lapra, és válassza az **NPS-t futtató központi kiszolgáló** lehetőséget.
3. Adjon hozzá egy vagy több Azure Multi-Factor Authentication-kiszolgálót RADIUS-kiszolgálóként a kiszolgálók nevének és IP-címének megadásával.
4. Hozzon létre egy közös titkos kulcsot mindegyik kiszolgálóhoz.

## <a name="configure-nps"></a>Az NPS konfigurálása

A távoli asztali átjáró NPS használatával küldi a RADIUS-kérést az Azure Multi-Factor Authentication számára. Az NPS konfigurálásához először módosítsa az időtúllépési beállításokat, hogy elkerülhető legyen a távoli asztali átjáró időtúllépése a kétlépéses ellenőrzés befejezése előtt. Ezután frissítse az NPS-t az MFA-kiszolgálótól érkező RADIUS-hitelesítések fogadásához. Az NPS konfigurálásához kövesse az alábbi eljárást:

### <a name="modify-the-timeout-policy"></a>Az időtúllépési házirend módosítása

1. Az NPS-ben nyissa meg a **RADIUS-ügyfelek és -kiszolgálók** menüt a bal oldali oszlopban, és válassza a **Távoli RADIUS-kiszolgálócsoportok** elemet.
2. Válassza a **TS GATEWAY SERVER GROUP** elemet.
3. Lépjen a **Terheléselosztás** lapra.
4. Módosítsa a **Válasz nélküli másodpercek száma a kiszolgáló nem elérhetővé nyilvánítása előtt** és a **Két kérelem között eltelt másodpercek száma a kiszolgáló nem elérhetővé nyilvánítása előtt** beállítást 30 és 60 másodperc közötti értékre. (Ha a kiszolgálón továbbra is időtúllépés történik a hitelesítés során, visszaléphet ide, és növelheti a másodpercek számát.)
5. Lépjen a **Hitelesítés/Fiók** lapra, és győződjön meg arról, hogy a megadott RADIUS-portok egyeznek azokkal a portokkal, amelyeken a Multi-Factor Authentication-kiszolgáló figyel.

### <a name="prepare-nps-to-receive-authentications-from-the-mfa-server"></a>Az NPS előkészítése az MFA-kiszolgálótól érkező hitelesítések fogadására

1. Kattintson a jobb gombbal a bal oldali oszlop RADIUS-ügyfelek és -kiszolgálók területén lévő **RADIUS-ügyfelek** elemre, és válassza az **Új** parancsot.
2. Adja hozzá az Azure Multi-Factor Authentication-kiszolgálót RADIUS-ügyfélként. Válasszon egy rövid nevet, és adjon meg egy közös titkos kulcsot.
3. Nyissa meg a bal oldali oszlopban lévő **Házirendek** menüt, és válassza a **Kapcsolatkérelem-házirendek** elemet. Ekkor meg kell jelennie egy TS GATEWAY AUTHORIZATION POLICY nevű házirendnek, amely a távoli asztali átjáró konfigurálásakor lett létrehozva. Ez a házirend továbbítja a RADIUS-kéréseket a Multi-Factor Authentication-kiszolgálónak.
4. Kattintson a jobb gombbal a **TS GATEWAY AUTHORIZATION POLICY** elemre, és válassza a **Házirend duplikálása** parancsot.
5. Nyissa meg az új házirendet, és lépjen a **Feltételek** lapra.
6. Adjon hozzá egy feltételt, amely egyezteti az ügyfél rövid nevét az Azure Multi-Factor Authentication-kiszolgáló RADIUS-ügyfelének a 2. lépésben beállított rövid nevével.
7. Lépjen a **Beállítások** lapra, és válassza a **Hitelesítés** lehetőséget.
8. Módosítsa a hitelesítésszolgáltatót a **Kérelmek hitelesítése ezen a kiszolgálón** értékre. Ez a házirend biztosítja, hogy amikor az NPS RADIUS-kérést kap az Azure MFA-kiszolgálóról, a hitelesítés helyben történik, ahelyett hogy a rendszer egy RADIUS-kérést küldene vissza az Azure Multi-Factor Authentication-kiszolgálónak, ami hurokállapothoz vezetne.
9. A hurokállapot elkerülése érdekében ellenőrizze, hogy az új házirend az eredeti házirend FÖLÖTT szerepel-e a **Kapcsolatkérelem-házirendek** panelen.

## <a name="configure-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication konfigurálása

Az Azure Multi-Factor Authentication-kiszolgáló RADIUS-proxyként van konfigurálva a távoli asztali átjáró és az NPS között.  A távoli asztali átjárókiszolgálótól eltérő tartományhoz csatlakoztatott kiszolgálóra kell telepíteni. Az Azure Multi-Factor Authentication-kiszolgáló konfigurálásához kövesse az alábbi eljárást.

1. Nyissa meg az Azure Multi-Factor Authentication-kiszolgálót, és válassza a RADIUS-hitelesítés ikont.
2. Jelölje be a **RADIUS-hitelesítés engedélyezése** jelölőnégyzetet.
3. Az Ügyfelek lapon ellenőrizze, hogy a portok egyeznek-e az NPS-ben konfiguráltakkal, és válassza a **Hozzáadás** elemet.
4. Adja hozzá a távoli asztali átjáró kiszolgálójának IP-címét, az alkalmazás nevét (nem kötelező) és a közös titkos kulcsot. A közös titkos kulcsnak azonosnak kell lennie az Azure Multi-Factor Authentication-kiszolgálón és a távoli asztali átjárón.
3. Lépjen a **Cél** lapra, és jelölje be a **RADIUS-kiszolgálók** választógombot.
4. Válassza a **Hozzáadás** lehetőséget, és adja meg az IP-címet, a közös titkos kulcsot és az NPS-kiszolgáló portjait. Ha nem központi NPS-t használ, a RADIUS-ügyfél és a RADIUS-cél azonos. A közös titkos kulcsnak egyeznie kell az NPS-kiszolgáló RADIUS-ügyfél szakaszában beállítottal.

![RADIUS-hitelesítés az MFA-kiszolgálón](./media/howto-mfaserver-nps-rdg/radius.png)

## <a name="next-steps"></a>Következő lépések

- Az Azure MFA és az [IIS-webalkalmazások](howto-mfaserver-iis.md) integrálása

- Válaszokért lásd az [Azure Multi-Factor Authenticationre vonatkozó gyakori kérdéseket](multi-factor-authentication-faq.md)