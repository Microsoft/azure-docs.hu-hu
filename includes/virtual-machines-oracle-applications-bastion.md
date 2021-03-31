---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "68361543"
---
### <a name="bastion-tier"></a>Megerősített szintek

A megerősített gazdagép egy opcionális összetevő, amelyet Jump Serverként használhat az alkalmazás-és adatbázis-példányok eléréséhez. A megerősített gazdagép virtuális gépe rendelkezhet nyilvános IP-címmel, bár a javaslat egy ExpressRoute vagy helyek közötti VPN-kapcsolat beállítása a helyszíni hálózattal a biztonságos hozzáférés érdekében. Emellett csak az SSH (port 22, Linux) vagy az RDP (port 3389, Windows Server) nyitható meg a bejövő forgalom számára. A magas rendelkezésre állás érdekében helyezzen üzembe egy megerősített gazdagépet két rendelkezésre állási zónában vagy egyetlen rendelkezésre állási csoporton belül.

Az SSH-ügynök továbbítása is engedélyezhető a virtuális gépeken, ami lehetővé teszi más virtuális gépek elérését a virtuális hálózatban a hitelesítő adatoknak a megerősített gazdagépről való továbbításával. Vagy használja az SSH-bújtatást más példányokhoz való hozzáféréshez.

Íme egy példa az ügynök továbbítására:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Ez a parancs csatlakozik a megerősített szolgáltatáshoz, majd azonnal `ssh` újra fut, így a rendszer egy terminált kap a cél példányon. Előfordulhat, hogy a célként megadott példányon kívül más felhasználót kell megadnia, ha a fürt másképp van konfigurálva. Az `-A` argumentum továbbítja az ügynöki kapcsolatokat, így a helyi gépen lévő titkos kulcsot automatikusan használja a rendszer. Vegye figyelembe, hogy az ügynök továbbítása egy lánc, ezért a második `ssh` parancs azt is tartalmazza, `-A` hogy a célként megadott példányon kezdeményezett további SSH-kapcsolatok a helyi titkos kulcsot is használják.