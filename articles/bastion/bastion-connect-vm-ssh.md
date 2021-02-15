---
title: Kapcsolódás Linux rendszerű virtuális géphez az Azure Bastion használatával
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat Linux rendszerű virtuális géphez az Azure Bastion használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: 5d61c2a1a0f5d7b26809621af6dfa88cf5080320
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518181"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Kapcsolódás az SSH-val egy Linux rendszerű virtuális géphez az Azure Bastion használatával

Ebből a cikkből megtudhatja, hogyan teheti meg biztonságosan és zökkenőmentesen az SSH-t egy Azure-beli virtuális hálózatban lévő linuxos virtuális gépeken. Közvetlenül a Azure Portal tud csatlakozni a virtuális géphez. Az Azure Bastion használata esetén a virtuális gépeknek nincs szükségük ügyfélre, ügynökre vagy további szoftverre. További információ az Azure Bastion-ről: [Áttekintés](bastion-overview.md).

Az Azure Bastion használatával Linux rendszerű virtuális gépekhez csatlakozhat SSH-val. A hitelesítéshez felhasználónevet/jelszót és SSH-kulcsokat is használhat. Az SSH-kulcsokkal csatlakozhat a virtuális géphez az alábbiak használatával:

* Egy manuálisan megadott titkos kulcs
* A titkos kulcs adatait tartalmazó fájl

Az SSH titkos kulcsának olyan formátumúnak kell lennie, amely a-val kezdődik  `"-----BEGIN RSA PRIVATE KEY-----"` és végződik `"-----END RSA PRIVATE KEY-----"` .

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy beállította az Azure-beli megerősített gazdagépet ahhoz a virtuális hálózathoz, amelyben a virtuális gép található. További információ: [Azure Bastion-gazdagép létrehozása](./tutorial-create-host-portal.md). A megerősített szolgáltatás a virtuális hálózatban való üzembe helyezése és telepítése után a használatával csatlakozhat a virtuális hálózat bármely virtuális GÉPHEZ. 

Amikor a Bastion használatával csatlakozik, feltételezi, hogy az RDP-t használja egy Windows rendszerű virtuális géphez való csatlakozáshoz, és SSH-t a linuxos virtuális gépekhez való csatlakozáshoz. További információ a Windows rendszerű virtuális gépekhez való csatlakozásról: [Csatlakozás virtuális géphez – Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Szükséges szerepkörök

A kapcsolatok létrehozásához a következő szerepkörök szükségesek:

* Olvasó szerepkör a virtuális gépen
* Olvasó szerepkör a NIC-ben a virtuális gép privát IP-címével
* Olvasó szerepkör az Azure Bastion-erőforrásban

### <a name="ports"></a>Portok

Ha SSH-n keresztül szeretne csatlakozni a Linux rendszerű virtuális géphez, a következő portokat kell megnyitnia a virtuális gépen:

* Bejövő port: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Kapcsolat: Felhasználónév és jelszó használata

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd kattintson a **Kapcsolódás** elemre, és válassza a **kilépési pont lehetőséget** .

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Képernyőfelvétel: a Azure Portal található virtuális gép áttekintése a kiválasztott kapcsolattal":::
1. A kiválasztást követően egy oldalsó sáv jelenik meg, amelynek három lapja van – RDP, SSH és Bastion. Ha a megerősített szolgáltatás a virtuális hálózathoz lett kiépítve, a megerősített lap alapértelmezés szerint aktív. Ha nem hozott létre a virtuális hálózatra vonatkozó kiépítést, olvassa el a következő témakört: a [Bastion konfigurálása](./tutorial-create-host-portal.md).

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="Képernyőfelvétel – a virtuális gép kapcsolódása párbeszédpanel, amelyen a BASTION van kiválasztva":::
1. Adja meg az SSH felhasználónevét és jelszavát a virtuális géphez.
1. A kulcs beírása után kattintson a **kapcsolat** gombra.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Kapcsolat: manuálisan adjon meg egy titkos kulcsot

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd kattintson a **Kapcsolódás** elemre, és válassza a **kilépési pont lehetőséget** .

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Képernyőfelvétel: a Azure Portal található virtuális gép áttekintése a kiválasztott kapcsolattal":::
1. A kiválasztást követően egy oldalsó sáv jelenik meg, amelynek három lapja van – RDP, SSH és Bastion. Ha a megerősített szolgáltatás a virtuális hálózathoz lett kiépítve, a megerősített lap alapértelmezés szerint aktív. Ha nem hozott létre a virtuális hálózatra vonatkozó kiépítést, olvassa el a következő témakört: a [Bastion konfigurálása](./tutorial-create-host-portal.md).

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="Kapcsolódás a virtuális géphez párbeszédpanelhez.":::
1. Adja meg a felhasználónevet, és válassza az **SSH titkos kulcs** lehetőséget.
1. Adja meg a titkos kulcsot a szövegmezőben található **SSH titkos kulcsban** (vagy illessze be közvetlenül a vágólapra).
1. A kulcs beírása után kattintson a **kapcsolat** gombra.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Összekapcsolás: titkos kulcsfájl használata

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd kattintson a **Kapcsolódás** elemre, és válassza a **kilépési pont lehetőséget** .

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Kijelöltek összekapcsolása":::
1. A kiválasztást követően egy oldalsó sáv jelenik meg, amelynek három lapja van – RDP, SSH és Bastion. Ha a megerősített szolgáltatás a virtuális hálózathoz lett kiépítve, a megerősített lap alapértelmezés szerint aktív. Ha nem hozott létre a virtuális hálózatra vonatkozó kiépítést, olvassa el a következő témakört: a [Bastion konfigurálása](./tutorial-create-host-portal.md).

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="MEGERŐSÍTETT kiválasztva.":::
1. Adja meg a felhasználónevet, és válassza az **SSH titkos kulcs lehetőséget a helyi fájlból**.
1. Kattintson a **Tallózás** gombra (a mappa ikon a helyi fájlban).
1. Tallózással keresse meg a fájlt, majd kattintson a **Megnyitás** gombra.
1. A virtuális géphez való kapcsolódáshoz válassza a **Kapcsolódás** lehetőséget. Ha a kapcsolódás gombra kattint, az SSH-t a virtuális géphez közvetlenül a Azure Portal fogja megnyitni. Ez a kapcsolat a HTML5-en keresztül a 443-es portot használja a virtuális gép magánhálózati IP-címén keresztül.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>Kapcsolat: Azure Key Vaultban tárolt titkos kulcs használata

>[!NOTE]
>A szolgáltatáshoz tartozó portál frissítése jelenleg régiókban zajlik.
>

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd kattintson a **Kapcsolódás** elemre, és válassza a **kilépési pont lehetőséget** .
1. A kiválasztást követően egy oldalsó sáv jelenik meg, amelynek három lapja van – RDP, SSH és Bastion. Ha a megerősített szolgáltatás a virtuális hálózathoz lett kiépítve, a megerősített lap alapértelmezés szerint aktív. Ha nem hozott létre a virtuális hálózatra vonatkozó kiépítést, olvassa el a következő témakört: a [Bastion konfigurálása](bastion-create-host-portal.md).

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="Megerősített lap":::
1. Adja meg a felhasználónevet, és válassza az **SSH titkos kulcs lehetőséget a Azure Key Vault**.
1. Válassza ki a **Azure Key Vault** legördülő menüt, és válassza ki azt az erőforrást, amelyben az SSH titkos kulcsát tárolja. Ha nem állított be Azure Key Vault erőforrást, tekintse meg a [Key Vault létrehozása](../key-vault/general/quick-create-portal.md) és az SSH titkos kulcs tárolása új Key Vault titok értékeként című témakört.

   :::image type="content" source="./media/bastion-connect-vm-ssh/key-vault.png" alt-text="Azure Key Vault":::

Győződjön meg arról, hogy rendelkezik a **listával** **, és hozzáférhet** a Key Vault erőforrásban tárolt titkos kulcsokhoz. A Key Vault erőforráshoz tartozó hozzáférési házirendek hozzárendeléséhez és módosításához tekintse meg [a Key Vault hozzáférési szabályzatának hozzárendelését](../key-vault/general/assign-access-policy-portal.md)ismertető témakört.

1. Válassza ki a **Azure Key Vault titkos** legördülő menüt, és válassza ki az SSH titkos kulcs értékét tartalmazó Key Vault titkot.
1. A virtuális géphez való kapcsolódáshoz válassza a **Kapcsolódás** lehetőséget. Ha a kapcsolódás gombra kattint, az SSH-t a virtuális géphez közvetlenül a Azure Portal fogja megnyitni. Ez a kapcsolat a HTML5-en keresztül a 443-es portot használja a virtuális gép magánhálózati IP-címén keresztül.

## <a name="next-steps"></a>Következő lépések

A [megerősített GYIK](bastion-faq.md) áttekintése
