---
title: 'Azure AD Connect: az eszköz visszaírási engedélyezése | Microsoft Docs'
description: Ez a dokumentum részletesen ismerteti, hogyan engedélyezhető az eszközök visszaírási az Azure AD Connect használatával
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d526394ac89e2d29b2002004736e8480bb15b954
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95973422"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: az eszköz visszaírási engedélyezése
> [!NOTE]
> Az eszköz visszaírási prémium szintű Azure AD előfizetésre van szükség.
> 
> 

Az alábbi dokumentáció a Azure AD Connect eszköz visszaírási funkciójának engedélyezését ismerteti. Az eszköz visszaírási a következő esetekben használja:

* [A vállalati Windows Hello engedélyezése hibrid tanúsítvány-megbízhatósági telepítés használatával](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration)
* Az eszközökön alapuló feltételes hozzáférés engedélyezése az ADFS (2012 R2 vagy újabb) által védett alkalmazások (függő entitások megbízhatóságai) alapján.

Ez további biztonságot és garanciát nyújt, hogy az alkalmazásokhoz való hozzáférés csak a megbízható eszközök számára legyen elérhető. A feltételes hozzáféréssel kapcsolatos további információkért tekintse meg a [kockázatkezelés feltételes hozzáféréssel való kezelésével](../conditional-access/overview.md) és [a helyszíni feltételes hozzáférés beállítása a Azure Active Directory Eszközregisztráció használatával](../devices/overview.md)című témakört.

> [!IMPORTANT]
> <li>Az eszközöknek a felhasználókkal megegyező erdőben kell lenniük. Mivel az eszközöket egyetlen erdőhöz kell visszaírni, ez a funkció jelenleg nem támogatja a több felhasználói erdővel rendelkező telepítést.</li>
> <li>Csak egy eszköz-regisztrációs konfigurációs objektum adható hozzá a helyszíni Active Directory erdőhöz. Ez a funkció nem kompatibilis olyan topológiával, amelyben a helyszíni Active Directory több Azure AD-címtárhoz van szinkronizálva.</li>

## <a name="part-1-install-azure-ad-connect"></a>1. rész: a Azure AD Connect telepítése
Azure AD Connect telepítése egyéni vagy expressz beállítások használatával. A Microsoft azt javasolja, hogy az eszköz visszaírási engedélyezése előtt az összes felhasználó és csoport szinkronizálása sikeresen megtörténjen.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>2. rész: az eszköz visszaírási engedélyezése Azure AD Connect
1. Futtassa újra a telepítővarázsló utasításait. Válassza az **eszközbeállítások konfigurálása lehetőséget** a további feladatok lapon, és kattintson a **tovább** gombra. 

    ![Eszközbeállítások konfigurálása](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > Az eszköz új konfigurálása lehetőség csak a 1.1.819.0 és újabb verziókban érhető el.

2. Az eszközbeállítások lapon válassza az **eszköz visszaírási konfigurálása** lehetőséget. Az **eszköz visszaírási letiltására** szolgáló lehetőség nem lesz elérhető, amíg az eszköz visszaírási be nem kapcsol. A **tovább** gombra kattintva lépjen a varázsló következő lapjára.
    ![Eszköz műveletének választása](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. A visszaírási lapon a megadott tartományt fogja látni az alapértelmezett visszaírási erdőként.
   ![Egyéni telepítési eszköz visszaírási-cél erdője](./media/how-to-connect-device-writeback/writebackforest.png)

4. Az **eszköz tárolója** lapon az Active Directory előkészítése a két elérhető lehetőség egyikének használatával lehetséges:

    a. **Vállalati rendszergazdai hitelesítő adatok megadása**: Ha a vállalati rendszergazda hitelesítő adatai megtalálhatók az erdőben, ahol az eszközöket vissza kell írni, Azure ad Connect automatikusan előkészíti az erdőt az eszköz visszaírási konfigurálása során.

    b. **Töltse le a PowerShell-szkriptet**: Azure ad Connect automatikusan létrehoz egy PowerShell-parancsfájlt, amely előkészíti az Active Directoryt az eszközök visszaírási. Ha a vállalati rendszergazda hitelesítő adatai nem adhatók meg Azure AD Connectban, a rendszer azt javasolja, hogy töltse le a PowerShell-szkriptet. Adja meg a letöltött PowerShell-parancsfájlt annak az erdőnek a vállalati rendszergazdájához **CreateDeviceContainer.ps1** , amelyben az eszközök vissza lesznek írva.
    ![Az Active Directory-erdő előkészítése](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    Az Active Directory-erdő előkészítéséhez a következő műveletek végezhetők el:
    * Ha még nem léteznek, a létrehozza és konfigurálja az új tárolókat és objektumokat a CN = Device Registration Configuration, CN = Services, CN = Configuration, [Forest-DN] elem alatt.
    * Ha még nem léteznek, a létrehozza és konfigurálja az új tárolókat és objektumokat a CN = RegisteredDevices, [tartomány-DN] alatt. A tárolóban létrejönnek az eszközök objektumai.
    * Beállítja a szükséges engedélyeket az Azure AD Connector-fiókhoz a Active Directory lévő eszközök kezeléséhez.
    * Csak egy erdőben kell futnia, még akkor is, ha Azure AD Connect több erdőre van telepítve.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Ellenőrizze, hogy az eszközök szinkronizálva vannak-e Active Directory
Az eszköz visszaírási megfelelően működnie kell. Vegye figyelembe, hogy az eszközök objektumainak beírása akár 3 óráig is eltarthat.  Annak ellenőrzéséhez, hogy az eszközök megfelelően vannak-e szinkronizálva, tegye a következőket a szinkronizálási szabályok befejezése után:

1. Indítsa el az Active Directory felügyeleti központot.
2. Bontsa ki a RegisteredDevices csomópontot az összevont tartományon belül.

   ![Active Directory felügyeleti központ regisztrált eszközei](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Az aktuálisan regisztrált eszközök itt lesznek felsorolva.

   ![Active Directory felügyeleti központ regisztrált eszközök listája](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Feltételes hozzáférés engedélyezése
A forgatókönyv végrehajtásához szükséges részletes utasítások a helyszíni [feltételes hozzáférés beállítása Azure Active Directory Eszközregisztráció használatával](../devices/overview.md)érhetők el.

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="the-writeback-checkbox-is-still-disabled"></a>A visszaírási jelölőnégyzet továbbra is le van tiltva
Ha az eszköz visszaírási jelölőnégyzete nincs engedélyezve, akkor is, ha követte a fenti lépéseket, a következő lépések végigvezetik a telepítési varázsló ellenőrzésének lépésein, mielőtt a jelölőnégyzet engedélyezve lesz.

Első lépések:

* Ahhoz az erdőhöz, ahol az eszközök találhatók, a Windows 2012 R2 szintre kell frissíteni az erdő sémáját, hogy az eszköz-objektum és a társított attribútumok jelen legyenek.
* Ha a telepítővarázsló már fut, akkor a rendszer nem észleli a módosításokat. Ebben az esetben végezze el a telepítővarázsló befejezését, majd futtassa újra.
* Győződjön meg arról, hogy az inicializálási parancsfájlban megadott fiók valójában a Active Directory-összekötő által használt helyes felhasználó. Ennek ellenőrzéséhez kövesse az alábbi lépéseket:
  * A Start menüben nyissa meg a **szinkronizálási szolgáltatást**.
  * Nyissa meg az **Összekötők** lapot.
  * Keresse meg a Active Directory Domain Services típusú összekötőt, és jelölje ki.
  * A **műveletek** területen válassza a **Tulajdonságok** lehetőséget.
  * Lépjen a **kapcsolódás Active Directory erdőhöz**. Ellenőrizze, hogy az ezen a képernyőn megadott tartomány-és Felhasználónév megegyezik-e a parancsfájlhoz megadott fiókkal.
    ![Összekötő fiók szinkronizált Service Manager](./media/how-to-connect-device-writeback/connectoraccount.png)

Konfiguráció ellenőrzése Active Directoryban:

* Ellenőrizze, hogy az eszköz regisztrációs szolgáltatása az alábbi helyen található-e (CN = Deviceregistrationservice objektuma, CN = eszköz regisztrációs szolgáltatások, CN = eszköz regisztrációjának konfigurációja, CN = Services, CN = Configuration) a konfiguráció névhasználati környezetében.

![Hibakeresés, Deviceregistrationservice objektuma a konfigurációs névtérben](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Ellenőrizze, hogy van-e csak egy konfigurációs objektum a konfigurációs névtérben való kereséssel. Ha egynél több van, törölje a duplikált elemeket.

![Hibakeresés, ismétlődő objektumok keresése](./media/how-to-connect-device-writeback/troubleshoot2.png)

* Győződjön meg arról, hogy az eszköz regisztrációs szolgáltatásának objektumában szerepel az msDS-DeviceLocation attribútum, és rendelkezik értékkel. Ellenőrizze ezt a helyet, és győződjön meg róla, hogy az Objektumtípus msDS-DeviceContainer van-e.

![Hibakeresés, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Problémamegoldás, RegisteredDevices objektumosztály](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Ellenőrizze, hogy az Active Directory-összekötő által használt fiók rendelkezik-e a szükséges engedélyekkel az előző lépésben megtalált regisztrált eszközök tárolóban. Ez a tárolóhoz szükséges engedélyek:

![Hibakeresés, engedélyek ellenőrzése a tárolón](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Ellenőrizze, hogy a Active Directory fiók rendelkezik-e engedélyekkel a CN = eszköz regisztrációjának konfigurációja, CN = szolgáltatások, CN = konfigurációs objektum számára.

![Hibaelhárítás, engedélyek ellenőrzése az eszköz regisztrációs konfigurációjában](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>További információ
* [Kockázatkezelés feltételes hozzáféréssel](../conditional-access/overview.md)
* [Helyszíni feltételes hozzáférés beállítása Azure Active Directory Eszközregisztráció használatával](../devices/overview.md)

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).