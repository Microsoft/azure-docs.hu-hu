---
title: Altartomány delegálása – Azure DNS
description: Ezzel a képzési útvonallal megkezdheti egy Azure DNS altartomány delegálását.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: add7674771fd19f6029a94c46624006f0cf30f1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011540"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Azure DNS altartomány delegálása

A Azure Portal a DNS-altartomány delegálására is használható. Ha például a contoso.com-tartomány tulajdonosa, akkor a *Engineering* nevű altartományt delegálhatja egy másikra, külön zónára, amelyet a contoso.com-zónától függetlenül kezelhet.

Ha szeretné, az altartomány delegálása [Azure PowerShell](delegate-subdomain-ps.md)használatával végezhető el.

## <a name="prerequisites"></a>Előfeltételek

Azure DNS altartomány delegálásához először delegálnia kell a nyilvános tartományt Azure DNSre. A kiszolgálók delegáláshoz való konfigurálásával kapcsolatos útmutatásért lásd: [tartomány delegálása Azure DNS](./dns-delegate-domain-azure-dns.md) . Miután a tartomány delegálásra került a Azure DNS zónába, delegálhatja az altartományt.

> [!NOTE]
> A Contoso.com a cikk során példaként szolgál. A contoso.com helyére írja be a saját tartománynevét.

## <a name="create-a-zone-for-your-subdomain"></a>Zóna létrehozása az altartományhoz

Először hozza létre a zónát a **mérnöki** altartományhoz.

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.
2. A keresőmezőbe írja be a **DNS** kifejezést, majd válassza a **DNS-zóna** lehetőséget.
3. Válassza a **Létrehozás** lehetőséget.
4. A **DNS-zóna létrehozása** panelen írja be a **Engineering.contoso.com** **nevet a név** szövegmezőbe.
5. Válassza ki a zónához tartozó erőforráscsoportot. Előfordulhat, hogy ugyanazt az erőforráscsoportot szeretné használni, mint a szülő zónát, hogy a hasonló erőforrások együtt maradjanak.
6. Kattintson a **Létrehozás** lehetőségre.
7. Az üzembe helyezés sikeres végrehajtása után lépjen az új zónára.

## <a name="note-the-name-servers"></a>Jegyezze fel a névkiszolgálók nevét

Ezután jegyezze fel a mérnöki altartomány négy névkiszolgálói kiszolgálóját.

A **mérnöki** zóna ablaktáblán jegyezze fel a zóna négy névkiszolgálói kiszolgálóját. Ezeket a névkiszolgálók később fogja használni.

## <a name="create-a-test-record"></a>Teszt rekord létrehozása

Hozzon létre egy **olyan** rekordot, amelyet teszteléshez szeretne használni. Hozzon létre például egy **www** -rekordot, és konfigurálja **10.10.10.10** IP-címmel.

## <a name="create-an-ns-record"></a>NS-rekord létrehozása

Ezután hozzon létre egy névkiszolgálói (NS) rekordot a **mérnöki** zónához.

1. Navigáljon a szülőtartomány zónájához.
2. Válassza a **+ Rekordhalmaz** lehetőséget.
3. A **rekordazonosító hozzáadása** panelen írja be a **mérnöki** **nevet a név** szövegmezőbe.
4. A **Típus mezőben** válassza az **NS** lehetőséget.
5. A **névkiszolgáló** területen adja meg azokat a négy névszervereket, amelyeket korábban a **mérnöki** zónában rögzített.
6. Kattintson az **OK** gombra.

## <a name="test-the-delegation"></a>A delegálás tesztelése

A delegálás teszteléséhez használja az nslookupt.

1. Indítson el egy PowerShell-ablakot.
2. A parancssorba írja be a következőt: `nslookup www.engineering.contoso.com.`
3. Meg kell kapnia egy nem mérvadó választ, amely a **10.10.10.10**.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [konfigurálhat fordított DNS-t az Azure-ban üzemeltetett szolgáltatásokhoz](dns-reverse-dns-for-azure-services.md).