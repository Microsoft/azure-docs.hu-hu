---
title: Azure AD Connect – egy AD FS Farm TLS/SSL-tanúsítványának frissítése | Microsoft Docs
description: Ez a dokumentum részletesen ismerteti a AD FS Farm TLS/SSL-tanúsítványának Azure AD Connect használatával történő frissítésének lépéseit.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 451b50e70b98849dfc4654566d09a5a961abe451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279907"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Active Directory összevonási szolgáltatások (AD FS) (AD FS) Farm TLS/SSL-tanúsítványának frissítése

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan frissítheti a Active Directory összevonási szolgáltatások (AD FS) (AD FS) Farm TLS/SSL-tanúsítványát Azure AD Connect használatával. A Azure AD Connect eszköz használatával egyszerűen frissítheti a AD FS Farm TLS/SSL-tanúsítványát akkor is, ha a kiválasztott felhasználói bejelentkezési módszer nem AD FS.

A AD FS Farm TLS/SSL-tanúsítványának teljes műveletét a következő három egyszerű lépésben végezheti el az összes összevonási és webalkalmazás-proxy (WAP) kiszolgálón:

![Három lépés](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Ha többet szeretne megtudni az AD FS által használt tanúsítványokról, tekintse meg a [AD FS által használt tanúsítványok ismertetése](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc730660(v=ws.11))című témakört.

## <a name="prerequisites"></a>Előfeltételek

* **AD FS Farm**: Győződjön meg arról, hogy a AD FS Farm a Windows Server 2012 R2-alapú vagy újabb verziójú.
* **Azure ad Connect**: gondoskodjon arról, hogy a Azure ad Connect verziója 1.1.553.0 vagy magasabb legyen. A feladat **frissítése AD FS SSL-tanúsítványt** használja.

![TLS-feladat frissítése](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>1. lépés: a AD FS Farm adatainak megadása

Azure AD Connect megpróbálja automatikusan beolvasni a AD FS Farm adatait a következővel:
1. A farm adatainak lekérdezése AD FSról (Windows Server 2016 vagy újabb).
2. Az előző futtatásokból származó információkra hivatkozik, amelyeket helyileg tárolnak Azure AD Connect.

A megjelenő kiszolgálók listáját a AD FS Farm aktuális konfigurációjának megfelelően módosíthatja a kiszolgálók hozzáadásával vagy eltávolításával. Amint megadja a kiszolgáló információit, Azure AD Connect megjeleníti a kapcsolatot és az aktuális TLS/SSL-tanúsítvány állapotát.

![AD FS kiszolgáló adatai](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Ha a lista olyan kiszolgálót tartalmaz, amely már nem része a AD FS farmjának, kattintson az **Eltávolítás** gombra a kiszolgáló törléséhez a AD FS farmban található kiszolgálók listájából.

![Offline kiszolgáló a listában](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Ha egy kiszolgálót eltávolít egy AD FS Farm kiszolgálók listájáról, Azure AD Connect egy helyi művelet, és frissíti a helyileg karbantartott AD FS Azure AD Connect Farm információit. A Azure AD Connect nem módosítja a AD FS konfigurációját, hogy tükrözze a változást.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>2. lépés: új TLS/SSL-tanúsítvány megadása

Miután megerősítette a AD FS Farm-kiszolgálókkal kapcsolatos információkat, Azure AD Connect kéri az új TLS/SSL-tanúsítványt. A telepítés folytatásához adjon meg egy jelszóval védett PFX-tanúsítványt.

![TLS/SSL-tanúsítvány](./media/how-to-connect-fed-ssl-update/certificate.png)

A tanúsítvány megadása után Azure AD Connect több előfeltételt mutat be. Győződjön meg arról, hogy a tanúsítvány helyes a AD FS Farm számára:

-   A tanúsítvány tulajdonos neve vagy alternatív tulajdonosának neve megegyezik az összevonási szolgáltatás nevével, vagy helyettesítő tanúsítványként.
-   A tanúsítvány 30 napnál hosszabb ideig érvényes.
-   A tanúsítvány megbízhatósági lánca érvényes.
-   A tanúsítvány jelszavas védelemmel van ellátva.

## <a name="step-3-select-servers-for-the-update"></a>3. lépés: a frissítéshez tartozó kiszolgálók kiválasztása

A következő lépésben válassza ki azokat a kiszolgálókat, amelyekre szükség van a TLS/SSL-tanúsítvány frissítéséhez. Az offline kiszolgálók nem választhatók ki a frissítéshez.

![Frissítendő kiszolgálók kiválasztása](./media/how-to-connect-fed-ssl-update/selectservers.png)

A konfiguráció befejezése után Azure AD Connect megjeleníti a frissítés állapotát jelző üzenetet, és lehetőséget biztosít a AD FS bejelentkezés ellenőrzésére.

![A konfigurálás befejeződött](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Gyakori kérdések

* **Mi legyen a tanúsítvány tulajdonosának neve az új AD FS TLS/SSL-tanúsítványhoz?**

    Azure AD Connect ellenőrzi, hogy a tanúsítvány tulajdonos neve/alternatív tulajdonos neve tartalmazza-e az összevonási szolgáltatás nevét. Ha például az összevonási szolgáltatás neve fs.contoso.com, a tulajdonos neve/alternatív tulajdonos neve fs.contoso.com kell, hogy legyen.  A helyettesítő tanúsítványokat is elfogadjuk.

* **Miért kértem újra a hitelesítő adatokat a WAP-kiszolgáló lapon?**

    Ha a AD FS kiszolgálókhoz való csatlakozáshoz megadott hitelesítő adatok nem rendelkeznek a WAP-kiszolgálók kezeléséhez szükséges jogosultsággal, akkor a Azure AD Connect a WAP-kiszolgálókon rendszergazdai jogosultsággal rendelkező hitelesítő adatokat kér le.

* **A kiszolgáló kapcsolat nélküli üzemmódban jelenik meg. Mit tegyek?**

    Azure AD Connect nem tud műveletet végrehajtani, ha a kiszolgáló offline állapotban van. Ha a kiszolgáló a AD FS Farm részét képezi, ellenőrizze a kapcsolatot a-kiszolgálóval. A probléma megoldását követően kattintson a frissítés ikonra az állapot frissítéséhez a varázslóban. Ha a kiszolgáló korábban már a farm része volt, de már nem létezik, kattintson az **Eltávolítás** gombra a Azure ad Connect által karbantartott kiszolgálók listájáról való törléséhez. Ha eltávolítja a kiszolgálót a listából, Azure AD Connect nem módosítja a AD FS konfigurációt. Ha AD FSt használ a Windows Server 2016-es vagy újabb verzióiban, a kiszolgáló a konfigurációs beállításokban marad, és a feladat következő futtatásakor ismét megjelenik majd.

* **Frissíthetem a farm-kiszolgálók egy részhalmazát az új TLS/SSL-tanúsítvánnyal?**

    Igen. A fennmaradó kiszolgálók frissítéséhez mindig futtassa újra az **SSL-tanúsítvány frissítése** feladatot. Az **SSL-tanúsítvány frissítése lap kiszolgálók kiválasztása** lapján rendezheti a kiszolgálók listáját az **SSL lejárati dátumán** , így egyszerűen elérheti a még nem frissített kiszolgálókat.

* **Eltávolítottam a kiszolgálót az előző futtatásban, de továbbra is offline állapotú jelenik meg, és a AD FS kiszolgálók lapon szerepel. Miért van az offline kiszolgáló még azután is, hogy eltávolítottam?**

    Ha eltávolítja a kiszolgálót a listából, Azure AD Connect nem távolítja el a AD FS-konfigurációban. Azure AD Connect referenciák AD FS (Windows Server 2016 vagy újabb) a farmra vonatkozó bármilyen információhoz. Ha a kiszolgáló továbbra is megtalálható a AD FS konfigurációban, a lista vissza lesz listázva.  

## <a name="next-steps"></a>Következő lépések

- [Azure AD Connect és összevonás](how-to-connect-fed-whatis.md)
- [Active Directory összevonási szolgáltatások (AD FS) felügyelet és testreszabás Azure AD Connect](how-to-connect-fed-management.md)