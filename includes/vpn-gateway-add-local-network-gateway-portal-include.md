---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5358bbbca716f5152a943c90cb7a5f735ae12047
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92479616"
---
1. A [Azure Portal](https://portal.azure.com)a **keresési erőforrások, szolgáltatások és dokumentumok (G +/)** mezőbe írja be a **helyi hálózati átjárót**. Keresse meg a **helyi hálózati átjárót** a **piactér** területen a keresési eredmények között, és jelölje ki. Ekkor megnyílik a **helyi hálózati átjáró létrehozása** lap.
1. A **Helyi hálózati átjáró létrehozása** lapon adja meg a helyi hálózati átjáró értékeit.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-ip.png" alt-text="Helyi hálózati átjáró létrehozása IP-címmel":::

   * **Név:** Adja meg a helyi hálózati átjáróobjektum nevét.
   * **Végpont:** Válassza ki a helyszíni VPN-eszköz végpontjának típusát – **IP-cím** vagy **FQDN (teljes tartománynév)**.
      * **IP-cím**: Ha a VPN-eszköz internetszolgáltatója számára lefoglalt statikus nyilvános IP-címmel rendelkezik, válassza az IP-cím lehetőséget, majd adja meg az IP-címet a példában látható módon. Ez annak a VPN-eszköznek a nyilvános IP-címe, amelyhez az Azure VPN Gateway csatlakozni kíván. Ha most még nem rendelkezik az IP-címmel, használhatja a példában látható értékeket, később azonban vissza kell térnie ide, és lecserélni a helyőrző IP-címet a VPN-eszköz nyilvános IP-címére. Ellenkező esetben az Azure nem fog tudni csatlakozni.
      * **FQDN**: Ha olyan dinamikus nyilvános IP-címmel rendelkezik, amely bizonyos idő elteltével változhat, amelyet általában az internetszolgáltató határoz meg, akkor a dinamikus DNS-szolgáltatással rendelkező állandó DNS-nevet használhatja a VPN-eszköz aktuális nyilvános IP-címére. Az Azure VPN Gateway feloldja a teljes tartománynevet, hogy meghatározza a csatlakozáshoz használandó nyilvános IP-címet. 
   * A **Címtartomány** annak a hálózatnak a címtartományára utal, amelyet a helyi hálózat képvisel. Több címtartományt is felvehet. Ügyeljen arra, hogy az itt megadott címtartományok ne legyenek átfedésben olyan egyéb hálózatok címtartományaival, amelyekhez csatlakozni kíván. Az Azure a helyszíni VPN-eszköz IP-címéhez irányítja át a megadott címtartományt. *A saját értékeit használja itt a helyszíni hellyel való kapcsolódáshoz, ne a példában látható értékeket*.
   * **BGP beállítások konfigurálása:** Csak BGP konfigurálásakor használja. Más esetben ne jelölje be ezt a jelölőnégyzetet.
   * **Előfizetés:** Ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg.
   * **Erőforráscsoport:** Válassza ki a használni kívánt erőforráscsoportot. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy korábban létrehozottat.
   * **Hely:** A hely megegyezik a más beállításokban található **régióval** . Válassza ki azt a helyet, ahol az objektumot létre kívánja hozni. Érdemes ugyanazt a helyet kiválasztani, ahol a virtuális hálózat is található, de ez nem kötelező.

   > [!NOTE]
   >
   > * Az Azure VPN minden FQDN esetében csak egy IPv4-címeket támogat. Ha a tartománynév több IP-címre lett feloldva, az Azure VPN Gateway a DNS-kiszolgálók által visszaadott első IP-címet fogja használni. A bizonytalanság megszüntetéséhez azt javasoljuk, hogy a teljes tartománynevet mindig egyetlen IPv4-címen oldja fel a rendszer. Az IPv6 nem támogatott.
   > * Az Azure VPN Gateway 5 percenként frissíti a DNS-gyorsítótárat. Az átjáró csak a leválasztott alagutak teljes tartománynevét próbálja meg feloldani. Az átjáró alaphelyzetbe állítása a teljes tartománynév feloldását is elindítja.
   >

1. Ha végzett az értékek megadásával, kattintson a lap alján található **Létrehozás** gombra a helyi hálózati átjáró létrehozásához.
