---
title: Azure Active Directory hozzáférés konfigurálása – Azure Blockchain szolgáltatás
description: Az Azure Blockchain szolgáltatás konfigurálása Azure Active Directory-hozzáféréssel
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 337d01abc51d310d06aeea3427b770132be4824c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85208773"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Az Azure Blockchain Service Azure Active Directory-hozzáférésének konfigurálása

Ebből a cikkből megtudhatja, hogyan biztosíthat hozzáférést, és hogyan csatlakozhat az Azure Blockchain Service-csomópontokhoz Azure Active Directory (Azure AD) felhasználó-, csoport-vagy alkalmazás-azonosítók használatával.

Az Azure AD biztosítja a felhőalapú Identitáskezelés használatát, és lehetővé teszi, hogy egyetlen identitást használjon a teljes vállalati és az Azure-beli alkalmazások eléréséhez. Az Azure Blockchain szolgáltatás integrálva van az Azure AD-vel, és olyan előnyöket kínál, mint az ID-összevonás, az egyszeri bejelentkezés és a többtényezős hitelesítés.

## <a name="prerequisites"></a>Előfeltételek

* [Blockchain-tag létrehozása a Azure Portal használatával](create-member.md)

## <a name="grant-access"></a>Hozzáférés biztosítása

A hozzáférést a tag szintjén és a csomópont szintjén is megadhatja. A hozzáférési jogosultságok megadása a tag szintjén a tag alá tartozó összes csomópont számára biztosít hozzáférést.

### <a name="grant-member-level-access"></a>Tag szintű hozzáférés megadása

Hozzáférési engedély megadása a tag szintjén.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon a **hozzáférés-vezérlés (iam) > adja hozzá > szerepkör-hozzárendelés** hozzáadása lehetőséget.
1. Válassza a **Blockchain (előzetes verzió)** szerepkört, és adja hozzá azt az Azure ad ID-objektumot, amelyhez hozzáférést szeretne biztosítani. Az Azure AD ID-objektum a következőket teheti:

    | Azure AD-objektum | Példa |
    |-----------------|---------|
    | Azure AD-felhasználó   | `kim@contoso.onmicrosoft.com` |
    | Azure AD-csoport  | `sales@contoso.onmicrosoft.com` |
    | Alkalmazásazonosító  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Szerepkör-hozzárendelés hozzáadása](./media/configure-aad/add-role-assignment.png)

1. Kattintson a **Mentés** gombra.

### <a name="grant-node-level-access"></a>Csomópont szintű hozzáférés megadása

A csomópontok biztonsága lehetőségre kattintva megadhatja a hozzáférést, és a csomópont nevére kattinthat, amelyhez hozzáférést szeretne biztosítani.

Válassza a Blockchain (előzetes verzió) szerepkört, és adja hozzá azt az Azure AD ID-objektumot, amelyhez hozzáférést szeretne biztosítani.

További információ: az [Azure Blockchain szolgáltatás tranzakciós csomópontjainak konfigurálása](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Csatlakozás az Azure Blockchain Connector használatával

Töltse le vagy klónozott az [Azure Blockchain-összekötőt a githubról](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

A **readme** (rövid útmutató) című részében találhatja meg az összekötőt a forráskódból.

### <a name="connect-using-an-azure-ad-user-account"></a>Csatlakozási szolgáltatás Azure AD-felhasználói fiókkal

1. Futtassa az alábbi parancsot egy Azure AD-felhasználói fiókkal történő hitelesítéshez. Cserélje le \<myAADDirectory\> egy Azure ad-tartományra. Például: `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Az Azure AD kéri a hitelesítő adatokat.
1. Jelentkezzen be a felhasználónevével és jelszavával.
1. A sikeres hitelesítés után a helyi proxy csatlakozik a blockchain-csomóponthoz. Most már csatlakoztathatja a geth-ügyfelet a helyi végponthoz.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Összekapcsolás alkalmazás-azonosító használatával

Számos alkalmazás az Azure AD-vel való hitelesítést egy Azure AD-felhasználói fiók helyett egy alkalmazás-azonosító használatával végzi.

Ha egy alkalmazás-azonosító használatával szeretne csatlakozni a csomóponthoz, cserélje le a **aadauthcode** és a **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Paraméter | Leírás |
|-----------|-------------|
| Bérlő azonosítója | Azure AD-tartomány, például: `yourdomain.onmicrosoft.com`
| ügyfél-azonosító | A regisztrált alkalmazás ügyfél-azonosítója az Azure AD-ben
| ügyfél – titok | A regisztrált alkalmazás ügyfél-titka az Azure AD-ben

Az alkalmazások Azure AD-ben való regisztrálásával kapcsolatos további információkért lásd [: útmutató: a portál használata Azure ad-alkalmazás és-szolgáltatásnév létrehozásához, amely hozzáférhet az erőforrásokhoz](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Mobileszköz vagy szöveges böngésző csatlakoztatása

Olyan mobileszköz-vagy szöveges böngésző esetén, ahol az Azure AD-hitelesítés előugró ablaka nem lehetséges, az Azure AD egyszeri jelszót hoz létre. A PIN-kódot átmásolhatja, és folytathatja az Azure AD-hitelesítést egy másik környezetben.

A PIN-kód létrehozásához cserélje le a **aadauthcode** a **aaddevice** értékre. Cserélje le \<myAADDirectory\> egy Azure ad-tartományra. Például: `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Következő lépések

Az Azure Blockchain szolgáltatás adatbiztonságával kapcsolatos további információkért lásd: az [Azure Blockchain szolgáltatás biztonsága](data-security.md).