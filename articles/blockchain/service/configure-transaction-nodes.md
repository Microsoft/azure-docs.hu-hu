---
title: Az Azure Blockchain szolgáltatás tranzakciós csomópontjainak konfigurálása
description: Az Azure Blockchain szolgáltatás tranzakciós csomópontjainak konfigurálása
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 0b659fa9f15cd41e9e55de526747d470046cafa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091256"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Az Azure Blockchain szolgáltatás tranzakciós csomópontjainak konfigurálása

A tranzakciós csomópontok egy nyilvános végponton keresztül blockchain-tranzakciók küldésére szolgálnak az Azure Blockchain szolgáltatásba. Az alapértelmezett tranzakciós csomópont tartalmazza a blockchain regisztrált Ethereum-fiók titkos kulcsát, és nem törölhető.

Az alapértelmezett tranzakciós csomópont részleteinek megtekintése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon az Azure Blockchain-szolgáltatás tagjához. Válassza a **tranzakciós csomópontok** lehetőséget.

    ![Alapértelmezett tranzakciós csomópont kiválasztása](./media/configure-transaction-nodes/nodes.png)

    Az Áttekintés részletei közé tartozik a nyilvános végpont címei és a nyilvános kulcs.

## <a name="create-transaction-node"></a>Tranzakciós csomópont létrehozása

Legfeljebb kilenc további tranzakciós csomópontot adhat hozzá a blockchain-taghoz, összesen 10 tranzakciós csomópontra. A tranzakciós csomópontok hozzáadásával növelheti a méretezhetőséget vagy terjesztheti a terhelést. Rendelkezhet például egy tranzakciós csomópont végponttal különböző ügyfélalkalmazások számára.

Tranzakciós csomópont hozzáadása:

1. A Azure Portal navigáljon az Azure Blockchain-szolgáltatás tagjához, és válassza a **tranzakciós csomópontok > Hozzáadás** lehetőséget.
1. Fejezze be az új tranzakciós csomópont beállításait.

    ![Tranzakciós csomópont hozzáadása](./media/configure-transaction-nodes/add-node.png)

    | Beállítás | Leírás |
    |---------|-------------|
    | Név | Tranzakciós csomópont neve. A név a tranzakciós csomópont végpontjának DNS-címeinek létrehozására szolgál. Például: `newnode-myblockchainmember.blockchain.azure.com`. A csomópont neve nem módosítható a létrehozása után. |
    | Jelszó | Állítson be egy erős jelszót. Használja a jelszót a tranzakciós csomópont végpontjának egyszerű hitelesítéssel való eléréséhez.

1. Válassza a **Létrehozás** lehetőséget.

    Az új tranzakciós csomópont kiépítés körülbelül 10 percet vesz igénybe. A további tranzakciós csomópontok költségekkel járnak. A költségekkel kapcsolatos további információkért lásd: az [Azure díjszabása](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Végpontok

A tranzakciós csomópontok egyedi DNS-névvel és nyilvános végpontokkal rendelkeznek.

A tranzakciós csomópont végpontjának részleteinek megtekintése:

1. A Azure Portal navigáljon az egyik Azure Blockchain-szolgáltatási tag tranzakciós csomópontjára, és válassza az **Áttekintés** lehetőséget.

    ![A képernyőfelvételek egy blockchain-tag tranzakciós csomópontjainak áttekintését jelenítik meg.](./media/configure-transaction-nodes/endpoints.png)

A tranzakciós csomópont végpontok biztonságosak és hitelesítés szükségesek. A tranzakciós végponthoz Azure AD-hitelesítéssel, HTTPS alapszintű hitelesítéssel és HTTPS-kapcsolaton keresztüli hozzáférési kulccsal vagy a TLS protokollon keresztül érheti el a csatlakozást.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory hozzáférés-vezérlés

Az Azure Blockchain szolgáltatás tranzakciós csomópontjának végpontok támogatják Azure Active Directory (Azure AD) hitelesítést. Megadhatja az Azure AD-beli felhasználói, csoport-és szolgáltatásnév hozzáférését a végponthoz.

Az Azure AD hozzáférés-vezérlésének engedélyezése a végpontnak:

1. A Azure Portal navigáljon az Azure Blockchain-szolgáltatás tagjához, és válassza a **tranzakciós csomópontok > hozzáférés-vezérlés (iam) > hozzáadás > szerepkör-hozzárendelés hozzáadása** elemet.
1. Új szerepkör-hozzárendelés létrehozása egy felhasználóhoz, csoporthoz vagy egyszerű szolgáltatáshoz (alkalmazás szerepkörei).

    ![IAM szerepkör hozzáadása](./media/configure-transaction-nodes/add-role.png)

    | Beállítás | Művelet |
    |---------|-------------|
    | Szerepkör | Válassza a **tulajdonos**, **közreműködő** vagy **olvasó** lehetőséget.
    | Hozzáférés hozzárendelése ehhez | Válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév** lehetőséget.
    | Válassza ezt: | Keresse meg azt a felhasználót, csoportot vagy szolgáltatásnevet, amelyet hozzá szeretne adni.

1. A szerepkör-hozzárendelés hozzáadásához válassza a **Mentés** lehetőséget.

Az Azure AD hozzáférés-vezérléssel kapcsolatos további információkért lásd: [Azure-szerepkörök kiosztása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md)

További információ az Azure AD-hitelesítés használatával történő kapcsolódásról: [Kapcsolódás a csomóponthoz a HRE-hitelesítés használatával](configure-aad.md).

### <a name="basic-authentication"></a>Alapszintű hitelesítés

A HTTPS alapszintű hitelesítés esetében a Felhasználónév és jelszó hitelesítő adatait a rendszer a kérelem HTTPS-fejlécében adja át a végpontnak.

A tranzakciós csomópontok alapszintű hitelesítési végpontjának részleteit a Azure Portal tekintheti meg. Navigáljon az egyik Azure Blockchain-szolgáltatási tag tranzakciós csomóponthoz, és válassza az **alapszintű hitelesítés** lehetőséget a beállításokban.

![Alapszintű hitelesítés](./media/configure-transaction-nodes/basic.png)

A Felhasználónév a csomópont neve, és nem módosítható.

Az URL-cím használatához cserélje le a \<password\> jelszót a csomópont kiosztásakor beállított jelszóra. A jelszót a **jelszó alaphelyzetbe állítása** lehetőség kiválasztásával frissítheti.

### <a name="access-keys"></a>Elérési kulcs

A hozzáférési kulcs hitelesítéséhez a hozzáférési kulcs szerepel a végpont URL-címében. A tranzakciós csomópont kiosztásakor két hozzáférési kulcs jön létre. A hitelesítéshez hozzáférési kulcs is használható. A két kulcs lehetővé teszi a kulcsok módosítását és elforgatását.

Megtekintheti a tranzakciós csomópont hozzáférési kulcsának részleteit, és a hozzáférési kulcsokat tartalmazó végponti címeket is átmásolhatja. Navigáljon az egyik Azure Blockchain-szolgáltatási tag tranzakciós csomóponthoz, és válassza a **hozzáférési kulcsok** lehetőséget a beállítások területen.

### <a name="firewall-rules"></a>Tűzfalszabályok

A tűzfalszabályok lehetővé teszik a tranzakciós csomóponton hitelesíteni kívánt IP-címek korlátozását.  Ha nincs tűzfalszabályok konfigurálva a tranzakciós csomóponthoz, akkor bármely fél nem fér hozzá.  

A tranzakciós csomópont tűzfalszabályok megtekintéséhez navigáljon az egyik Azure Blockchain-szolgáltatási tag tranzakciós csomóponthoz, és válassza a **Tűzfalszabályok** lehetőséget a beállítások területen.

Tűzfalszabályok hozzáadásához írja be a szabály nevét, a kezdő IP-címet és egy záró IP-címet a **Tűzfalszabályok** rácsba.

![Tűzfalszabályok](./media/configure-transaction-nodes/firewall-rules.png)

Engedélyezés:

* **Egyetlen IP-cím:** Adja meg ugyanazt az IP-címet a kezdő és a záró IP-címekhez.
* **IP-címtartomány:** Adja meg a kezdő és a záró IP-címtartományt. Például a 10.221.34.0 és a 10.221.34.255 végződésű tartomány a teljes 10.221.34.xxx-alhálózatot engedélyezheti.
* **Összes IP-cím engedélyezése:** Konfigurálja a kezdő IP-címet a 0.0.0.0 értékre és a záró IP-címet a 255.255.255.255.

## <a name="connection-strings"></a>Kapcsolati sztringek

A tranzakciós csomóponthoz tartozó kapcsolati karakterlánc szintaxisa az alapszintű hitelesítéshez vagy a hozzáférési kulcsok használatával van megadva. Kapcsolati karakterláncok, beleértve a hozzáférési kulcsokat a HTTPS-en és a websocketeken keresztül.

Megtekintheti a tranzakciós csomópontok kapcsolatainak karakterláncait és másolja a végponti címeket. Navigáljon az egyik Azure Blockchain-szolgáltatási tag tranzakciós csomóponthoz, és válassza a beállítások között a **kapcsolódási karakterláncok** lehetőséget.

![Kapcsolati sztringek](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Mintakód

A Web3, a Nethereum, a Web3js és a szarvasgomba használatával gyorsan engedélyezheti a tranzakciós csomóponthoz való csatlakozást.

Megtekintheti a tranzakciós csomópont minta-kapcsolódási kódját, és átmásolhatja a népszerű fejlesztői eszközökkel való használatra. Nyissa meg az egyik Azure Blockchain-szolgáltatási tag tranzakciós csomópontját, és válassza a **mintakód** lehetőséget a beállítások területen.

Válassza a Web3, a Nethereum, a szarvasgomba vagy a Web3j fület a használni kívánt mintakód megtekintéséhez.

![Mintakód](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tranzakciós csomópontok konfigurálása az Azure CLI-vel](manage-cli.md)
