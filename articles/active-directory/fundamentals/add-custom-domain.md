---
title: Egyéni tartomány hozzáadása – Azure Active Directory | Microsoft Docs
description: Útmutató egyéni tartomány hozzáadásához a Azure Active Directory használatával.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28847ece3c08e93d14d381b35fc93fb16dad95a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95973898"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Egyéni tartománynév hozzáadása a Azure Active Directory portál használatával

Minden új Azure AD-bérlő kezdeti tartománynevet, *\<domainname> . onmicrosoft.com* tartalmaz. A kezdeti tartománynév nem módosítható vagy törölhető, de felveheti a szervezet nevét. Az egyéni tartománynevek hozzáadásával a felhasználók számára ismerős felhasználónevek (például *alain \@ contoso.com*) hozhatók létre.

## <a name="before-you-begin"></a>Előkészületek

Egyéni tartománynév hozzáadása előtt hozza létre a tartománynevet egy tartományregisztráló használatával. Egy akkreditált tartományregisztráló esetében tekintse meg a következőt: [ICANN-akkreditált regisztrátorok](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>A címtár létrehozása az Azure AD-ben

A tartománynév beszerzése után létrehozhatja első Azure AD-címtárát. Jelentkezzen be a címtár Azure Portalba egy olyan fiókkal, amely az előfizetés **tulajdonosi** szerepkörét használja.

Az új címtár létrehozásához kövesse az [új bérlő létrehozása a szervezet számára](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization)című témakör lépéseit.

>[!IMPORTANT]
>A bérlőt létrehozó személy automatikusan a bérlő globális rendszergazdája. A globális rendszergazda további rendszergazdákat is hozzáadhat a bérlőhöz.

Az előfizetési szerepkörökkel kapcsolatos további információkért lásd: [Azure-szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).

>[!TIP]
> Ha azt tervezi, hogy a helyszíni Windows Server AD-t az Azure AD-vel szeretné összevonása, akkor be kell jelölnie **ezt a tartományt a helyi Active Directory való egyszeri bejelentkezéshez** , amikor a Azure ad Connect eszközt futtatja a címtárak szinkronizálásához.
>
> Emellett a helyszíni címtárral való összevonáshoz kiválasztott tartománynevet is regisztrálnia kell az **Azure AD-tartomány** lépésnél a varázslóban. Ha szeretné megtekinteni, hogy a telepítő hogyan néz ki, tekintse meg [az összevonás számára kijelölt Azure ad-tartomány ellenőrzése](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation)című témakört. Ha nem rendelkezik a Azure AD Connect eszközzel, [letöltheti itt](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Egyéni tartománynév hozzáadása az Azure AD-hez

A címtár létrehozása után felveheti az egyéni tartománynevet.

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Keresse meg és válassza ki a *Azure Active Directory* bármely oldalon. Ezután válassza az **Egyéni tartománynevek**  >  **egyéni tartomány hozzáadása** elemet.

    ![Custom Domain Names (egyéni tartománynevek) lap, amelyen az egyéni tartomány hozzáadása látható](media/add-custom-domain/add-custom-domain.png)

1. Az **Egyéni tartománynév** mezőben adja meg a szervezet új nevét, ebben a példában a *contoso.com*. Válassza a **Tartomány hozzáadása** lehetőséget.

    ![Egyéni tartománynevek lap az egyéni tartomány hozzáadása oldalon](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >A megfelelő működéshez a *. com*, a *.net* vagy bármely más legfelső szintű bővítményt kell megadni.

    A nem ellenőrzött tartomány hozzá van adva. Megjelenik a **contoso.com** lap, amely a DNS-adatokat jeleníti meg. Mentse ezt az információt. Később szüksége lesz rá egy TXT-rekord létrehozásához a DNS konfigurálásához.

    ![Contoso-lap DNS-bejegyzési információkkal](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>DNS-adatok hozzáadása a tartományregisztrálóhoz

Miután hozzáadta az egyéni tartománynevet az Azure AD-hez, vissza kell térnie a tartományregisztráló, és hozzá kell adnia az Azure AD DNS-adatokat a másolt TXT-fájlból. A TXT-rekordnak a tartományhoz való létrehozása ellenőrzi a tartománynév tulajdonjogát.

Térjen vissza a tartományregisztráló, és hozzon létre egy új TXT-rekordot a tartományhoz a másolt DNS-adatok alapján. Állítsa az élettartamot (TTL) 3600 másodpercre (60 perc), majd mentse a rekordot.

>[!IMPORTANT]
>Tetszőleges számú tartománynevet regisztrálhat. Azonban minden tartomány saját TXT-rekordot kap az Azure AD-ből. Ügyeljen arra, hogy a rendszer a TXT-fájl adatait adja meg a tartományregisztrálónál. Ha hiba esetén helytelen vagy ismétlődő információt ad meg, meg kell várnia, amíg a TTL időtúllépés (60 perc), mielőtt újra próbálkozik.

## <a name="verify-your-custom-domain-name"></a>Egyéni tartománynév ellenőrzése

Az Egyéni tartománynév regisztrálását követően győződjön meg arról, hogy az érvényes az Azure AD-ben. A tartományregisztráló és az Azure AD közötti propagálás lehet azonnali, vagy eltarthat néhány napig a tartományregisztrálótól függően.

Az egyéni tartomány nevének ellenőrzéséhez kövesse az alábbi lépéseket:

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Keresse meg és válassza ki a *Azure Active Directory* bármely lapon, majd válassza az **Egyéni tartománynevek** lehetőséget.

1. Az **Egyéni tartománynevek** területen válassza ki az egyéni tartománynevet. Ebben a példában válassza a **contoso.com** lehetőséget.

    ![Fabrikam – egyéni tartománynevek lap, a contoso kiemelve](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. A **contoso.com** lapon válassza az **ellenőrzés** lehetőséget, hogy ellenőrizze, hogy az egyéni tartomány megfelelően van-e regisztrálva, és hogy érvényes-e az Azure ad-hez.

    ![Contoso-lap DNS-bejegyzési információkkal és az ellenőrzés gombbal](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Miután ellenőrizte az egyéni tartománynevet, törölheti az ellenőrző TXT-vagy MX-fájlt.

## <a name="common-verification-issues"></a>Gyakori ellenőrzési problémák

Ha az Azure AD nem tudja ellenőrizni az egyéni tartománynevet, próbálkozzon az alábbi javaslatokkal:

- **Várjon legalább egy órát, és próbálkozzon újra**. A DNS-rekordokat propagálni kell, mielőtt az Azure AD ellenőrizni tudja a tartományt. A folyamat egy óráig vagy tovább is tarthat.

- **Győződjön meg arról, hogy a DNS-rekord helyes.** Térjen vissza a tartománynév-regisztráló webhelyhez. Győződjön meg arról, hogy a bejegyzés létezik, és hogy az megfelel az Azure AD által biztosított DNS-bejegyzési információknak.

  Ha nem tudja frissíteni a rekordot a regisztrátor webhelyén, ossza meg a bejegyzést olyan személlyel, aki jogosult a bejegyzés hozzáadására, és ellenőrizze, hogy helyes-e.

- **Győződjön meg arról, hogy a tartománynév még nincs használatban egy másik címtárban.** A tartománynév csak egy címtárban ellenőrizhető. Ha a tartománynevet egy másik címtárban ellenőrzi, az nem ellenőrizhető az új könyvtárban is. Az ismétlődési probléma megoldásához törölnie kell a tartománynevet a régi címtárból. További információ a tartománynevek törléséről: [Egyéni tartománynevek kezelése](../enterprise-users/domains-manage.md).

- **Győződjön meg arról, hogy nem rendelkezik nem felügyelt Power BI Bérlővel.** Ha a felhasználók aktiválták Power BI az önkiszolgáló regisztráción keresztül, és létrehoztak egy nem felügyelt bérlőt a szervezet számára, akkor a PowerShell használatával belső vagy külső rendszergazdaként kell átvennie a felügyeletet. További információkért tekintse meg a [nem felügyelt címtár az Azure Active Directoryban rendszergazdaként történő átvételét](../enterprise-users/domains-admin-takeover.md) ismertető cikket.

## <a name="next-steps"></a>Következő lépések

- Adjon hozzá egy másik globális rendszergazdát a címtárhoz. További információ: [szerepkörök és rendszergazdák társítása](active-directory-users-assign-role-azure-portal.md).

- Felhasználók hozzáadása a tartományhoz. További információ: [felhasználók hozzáadása vagy törlése](add-users-azure-active-directory.md).

- A tartománynév-információk kezelése az Azure AD-ben. További információ: [Egyéni tartománynevek kezelése](../enterprise-users/domains-manage.md).

- Ha a Windows Server azon helyszíni verzióit használja, amelyeket a Azure Active Directory mellett szeretne használni, tekintse [meg a helyszíni címtárak integrálása Azure Active Directory](../hybrid/whatis-hybrid-identity.md)használatával című témakört.