---
title: A Windows rendszerű virtuális asztali hálózati kapcsolat ismertetése
titleSuffix: Azure
description: Tudnivalók a Windows rendszerű virtuális asztali hálózati kapcsolatról
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: e4149864e16196b695d38a8c46ab5af835453412
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221210"
---
# <a name="understanding-windows-virtual-desktop-network-connectivity"></a>A Windows rendszerű virtuális asztali hálózati kapcsolat ismertetése

A Windows virtuális asztal lehetővé teszi az ügyfél-munkamenetek üzemeltetését az Azure-on futó munkamenet-gazdagépeken. A Microsoft kezeli a szolgáltatások részeit az ügyfél nevében, és biztonságos végpontokat biztosít az ügyfelek és a munkamenet-gazdagépek csatlakoztatásához. Az alábbi ábra áttekintést nyújt a Windows virtuális asztal által használt hálózati kapcsolatokról

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="A Windows rendszerű virtuális asztali hálózati kapcsolatok diagramja" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>Munkamenet-kapcsolat

A Windows virtuális asztal RDP protokoll (RDP) használatával biztosítja a távoli megjelenítési és bemeneti képességeket a hálózati kapcsolatokon keresztül. Az RDP kezdetben a Windows NT 4,0 Terminal Server Edition kiadásban jelent meg, és folyamatosan fejlődik a Microsoft Windows és a Windows Server minden kiadásával. A kezdetektől fogva az RDP úgy lett kifejlesztve, hogy független legyen a mögöttes szállítási veremtől, és jelenleg több típusú átvitelt is támogat.

## <a name="reverse-connect-transport"></a>Fordított kapcsolat átvitele

A Windows virtuális asztal fordított csatlakozási átvitelt használ a távoli munkamenet létrehozásához és az RDP-forgalom végrehajtásához. A helyszíni Távoli asztali szolgáltatások üzemelő példányokkal ellentétben a fordított kapcsolat átvitele nem használ TCP-figyelőt a bejövő RDP-kapcsolatok fogadására. Ehelyett a HTTPS-kapcsolaton keresztül kimenő kapcsolatot használ a Windows rendszerű virtuális asztali infrastruktúrához.

## <a name="session-host-communication-channel"></a>Munkamenet-gazdagép kommunikációs csatornája

A Windows rendszerű virtuális asztali munkamenetgazda szolgáltatás indításakor a Távoli asztal ügynök betöltő szolgáltatása létrehozza a Windows Virtual Desktop Broker állandó kommunikációs csatornáját. Ez a kommunikációs csatorna egy biztonságos Transport Layer Security (TLS) kapcsolaton van, és buszként szolgál a munkamenet-gazdagép és a Windows rendszerű virtuális asztali infrastruktúra közötti üzenetváltáshoz.

## <a name="client-connection-sequence"></a>Ügyfélkapcsolati folyamat

Az ügyfélkapcsolati folyamat alább látható:

1. A Windows rendszerű virtuális asztali ügyfél támogatott felhasználói előfizetések használata a Windows rendszerű virtuális asztali munkaterületre
2. Azure Active Directory hitelesíti a felhasználót, és visszaadja a felhasználó számára elérhető erőforrások enumerálásához használt jogkivonatot.
3. Az ügyfél átadja a tokent a Windows rendszerű virtuális asztali hírcsatorna-előfizetés szolgáltatásnak
4. A Windows rendszerű virtuális asztali hírcsatorna-előfizetési szolgáltatás ellenőrzi a jogkivonatot.
5. A Windows rendszerű virtuális asztali hírcsatorna-előfizetés szolgáltatás digitálisan aláírt kapcsolati konfiguráció formájában továbbítja az elérhető asztali számítógépek és RemoteApp-alkalmazások listáját az ügyfélnek.
6. Az ügyfél az összes rendelkezésre álló erőforrás kapcsolati konfigurációját tárolja. rdp-fájlok készletében
7. Amikor a felhasználó kiválasztja az erőforrást a csatlakozáshoz, az ügyfél a társított. rdp fájlt használja, és létrehozza a biztonságos TLS 1,2-kapcsolatot a legközelebbi Windowsos virtuális asztali átjáró-példánnyal, és átadja a kapcsolati adatokat.
8. A Windows rendszerű virtuális asztali átjáró érvényesíti a kérést, és megkéri a Windows virtuális asztali közvetítőt a kapcsolat összehangolása érdekében.
9. A Windows Virtual Desktop Broker azonosítja a munkamenet-gazdagépet, és a korábban kialakított állandó kommunikációs csatornát használja a kapcsolat inicializálásához.
10. Távoli asztal stack kezdeményezi a TLS 1,2-kapcsolat használatát ugyanahhoz a Windows Virtual Desktop Gateway-példányhoz, amelyet az ügyfél használ.
11. Miután az ügyfél és a munkamenet-állomás csatlakoztatva lett az átjáróhoz, az átjáró megkezdi a nyers adatok továbbítását mindkét végpont között, ez létrehozza az alapszintű fordított kapcsolat átvitelét az RDP számára.
12. Az alapszintű átvitel beállítása után az ügyfél elindítja az RDP-kézfogást.

## <a name="connection-security"></a>Kapcsolatbiztonság

A TLS 1,2 az ügyfelek és a munkamenet-gazdagépek által a Windows rendszerű virtuális asztali infrastruktúra összetevőihez kezdeményezett összes kapcsolathoz használatos. A Windows virtuális asztal ugyanazt a TLS 1,2 titkosítási algoritmust használja, mint az [Azure bejárati ajtó](../frontdoor/front-door-faq.md#what-are-the-current-cipher-suites-supported-by-azure-front-door). Fontos annak biztosítása, hogy az ügyfélszámítógépek és a munkamenet-gazdagépek is használhatják ezeket a titkosítási modulokat.
A fordított kapcsolat átviteléhez az ügyfél és a munkamenet-állomás is csatlakozik a Windows rendszerű virtuális asztali átjáróhoz. A TCP-kapcsolat létrehozása után az ügyfél vagy a munkamenet-állomás érvényesíti a Windows rendszerű virtuális asztali átjáró tanúsítványát.
Az alapszintű átvitel létrehozása után az RDP egy beágyazott TLS-kapcsolatot hoz létre az ügyfél és a munkamenet-állomás között a munkamenet-gazdagép tanúsítványainak használatával. Alapértelmezés szerint az RDP-titkosításhoz használt tanúsítványt az operációs rendszer saját maga hozza létre a telepítés során. Ha kívánja, az ügyfelek központilag felügyelt tanúsítványokat telepíthetnek a vállalati hitelesítésszolgáltató által kiadott tanúsítványok alapján. A tanúsítványok konfigurálásával kapcsolatos további információkért lásd a [Windows Server dokumentációját](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="next-steps"></a>Következő lépések

* A Windows rendszerű virtuális asztali sávszélességre vonatkozó követelmények megismeréséhez tekintse meg [a Windows rendszerű virtuális asztalok RDP protokoll (RDP) sávszélesség-követelményeit](rdp-bandwidth.md)ismertető témakört.
* A Windows rendszerű virtuális asztali szolgáltatások szolgáltatásminőség (QoS) szolgáltatásának megkezdéséhez lásd: [a Windows rendszerű virtuális asztali szolgáltatás szolgáltatásminőség (QoS) implementálása](rdp-quality-of-service-qos.md).
