---
title: 'VPN Gateway: a VPN-ügyfél hibáinak megoldása – Azure AD-hitelesítés'
description: Az Azure AD hitelesítési ügyfeleivel VPN Gateway P2S hibáinak megoldása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 56a8514fc2531ba0b18925427814e5bfef7d64bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84988099"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Azure AD-hitelesítéssel rendelkező VPN-ügyfél hibáinak megoldása

Ebből a cikkből megtudhatja, hogyan oldhatja fel a VPN-ügyfelet a virtuális hálózathoz pont – hely VPN és Azure Active Directory hitelesítés használatával való csatlakozáshoz.

## <a name="view-status-log"></a><a name="status"></a>Állapotüzenetek megtekintése

A hibaüzenetek állapotára vonatkozó napló megtekintése.

![naplók](./media/troubleshoot-ad-vpn-client/1.png)

1. Az **állapotüzenetek** megjelenítéséhez kattintson az ügyfél ablakának jobb alsó sarkában található nyilak ikonra.
2. Keresse meg a hibát jelző hibákat a naplókban.
3. A hibaüzenetek piros színnel jelennek meg.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Bejelentkezési adatok törlése

Törölje a bejelentkezési adatokat.

![bejelentkezés](./media/troubleshoot-ad-vpn-client/2.png)

1. Válassza a... a megoldani kívánt profil mellett. Válassza a **Konfigurálás – > a mentett fiók törlése** lehetőséget.
2. Kattintson a **Mentés** gombra.
3. Próbáljon meg csatlakozni.
4. Ha a kapcsolat továbbra is sikertelen, folytassa a következő szakasszal.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Diagnosztika futtatása

Futtasson diagnosztikát a VPN-ügyfélen.

![diagnosztika](./media/troubleshoot-ad-vpn-client/3.png)

1. Kattintson a **.** .. azon profil mellett, amelyen diagnosztikát kíván futtatni. Válassza a **Diagnosztizálás – > futtatási diagnosztika** lehetőséget.
2. Az ügyfél több tesztet fog futtatni, és megjeleníti a teszt eredményét.

   * Internet-hozzáférés – ellenőrzi, hogy az ügyfél rendelkezik-e internetkapcsolattal
   * Ügyfél hitelesítő adatai – ellenőrizze, hogy a Azure Active Directory hitelesítési végpont elérhető-e
   * Kiszolgáló feloldható – Kapcsolatfelvétel a DNS-kiszolgálóval a konfigurált VPN-kiszolgáló IP-címének feloldásához
   * A kiszolgáló érhető el – ellenőrzi, hogy a VPN-kiszolgáló válaszol-e vagy sem
3. Ha a tesztek bármelyike meghiúsul, a probléma megoldásához forduljon a hálózati rendszergazdához.
4. A következő szakasz bemutatja, hogyan gyűjtheti össze a naplókat, ha szükséges.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Ügyfél-naplófájlok összegyűjtése

Gyűjtse össze a VPN-ügyfél naplófájljait. A naplófájlok a támogatás/rendszergazda számára a választott módszer használatával küldhetők el. Például: e-mail.

1. Kattintson a "..." azon profil mellett, amelyen diagnosztikát kíván futtatni. Válassza a **Diagnosztizálás – > naplók megjelenítése könyvtárat**.

   ![naplók megjelenítése](./media/troubleshoot-ad-vpn-client/4.png)
2. A Windows Intéző megnyílik a naplófájlokat tartalmazó mappához.

   ![fájl megtekintése](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Következő lépések

További információ: [Azure Active Directory-bérlő létrehozása az Azure ad-hitelesítést használó P2S nyitott VPN-kapcsolatokhoz](openvpn-azure-ad-tenant.md).