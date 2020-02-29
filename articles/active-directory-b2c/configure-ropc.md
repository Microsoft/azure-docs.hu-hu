---
title: Az erőforrás-tulajdonos jelszava hitelesítő adatainak konfigurálása
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan konfigurálhatja a ROPC folyamatot a Azure AD B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1d17f9af5700df5458cc4373dfc5cd8fb7774f91
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912399"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-ad-b2c"></a>Az erőforrás-tulajdonos jelszava hitelesítő adatainak konfigurálása Azure AD B2C

Az erőforrás-tulajdonosi jelszó hitelesítő adatai (ROPC) a OAuth standard hitelesítési folyamata, ahol az alkalmazás, más néven a függő entitás, az érvényes hitelesítő adatokat (például felhasználóazonosító és jelszó) az azonosító token, a hozzáférési jogkivonat és a frissítési jogkivonat esetében cseréli.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

[!INCLUDE [active-directory-b2c-ropc-notes](../../includes/active-directory-b2c-ropc-notes.md)]

##  <a name="create-a-resource-owner-user-flow"></a>Erőforrás-tulajdonos felhasználói folyamat létrehozása

1. Jelentkezzen be a Azure Portalba a Azure AD B2C bérlő globális rendszergazdájaként.
2. A Azure AD B2C bérlőre való váltáshoz válassza a B2C könyvtárat a portál jobb felső sarkában.
3. Kattintson a **felhasználói folyamatok**elemre, majd válassza az **új felhasználói folyamat**elemet.
4. Kattintson az **összes** fülre, és válassza a **Bejelentkezés a ROPC használatával**lehetőséget.
5. Adja meg a felhasználói folyamat nevét, például *ROPC_Auth*.
6. Az **alkalmazás jogcímei**területen kattintson a **továbbiak megjelenítése**lehetőségre.
7. Válassza ki az alkalmazáshoz szükséges jogcímeket, például a megjelenítendő nevet, az e-mail-címet és az identitás-szolgáltatót.
8. Kattintson az **OK**, majd a **Létrehozás** gombra.
9. Kattintson a **felhasználói folyamat futtatása**elemre.

   Ekkor megjelenik egy végpont, például a következő példa:

   `https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_ROPC_Auth`


## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

Egy API-hívás létrehozásához használja kedvenc API-fejlesztési alkalmazását, és tekintse át a felhasználói folyamat hibakeresésére szolgáló választ. A következő táblázatban szereplő információk alapján állítson össze egy hívást a post kérelem törzse:
- Cserélje le *\<yourtenant. onmicrosoft. com >* a B2C-bérlő nevére.
- Cserélje le a *\<B2C_1A_ROPC_Auth >* az erőforrás-tulajdonosi jelszó hitelesítő adatainak teljes nevére.
- Cserélje le a *\<bef2222d56-552f-4a5b-b90a-1988a7d634c3 >* a regisztrációhoz tartozó alkalmazás-azonosítóra.

`https://yourtenant.b2clogin.com/<yourtenant.onmicrosoft.com>/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

| Paraméter | Érték |
| --- | ----- |
| felhasználónév | leadiocl@outlook.com |
| jelszó | Passxword1 |
| grant_type | jelszó |
| scope | OpenID \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > offline_access |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| response_type | token id_token |

*Client_id* az az érték, amelyet korábban alkalmazás-azonosítóként észlelt. A *Offline_access* megadása nem kötelező, ha frissítési tokent szeretne kapni. A használt felhasználónévnek és jelszónak a Azure AD B2C bérlő egy meglévő felhasználójának hitelesítő adatainak kell lennie.

A tényleges POST-kérelem a következőhöz hasonlóan néz ki:

```
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Az offline-hozzáférés sikeres válasza a következő példához hasonlóan néz ki:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg"
}
```

## <a name="redeem-a-refresh-token"></a>Frissítési jogkivonat beváltása

Az alábbi táblázatban szereplő információk alapján készítse el a kérés törzsét:

`https://yourtenant.b2clogin.com/<yourtenant.onmicrosoft.com>/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

| Paraméter | Érték |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| resource | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

A *Client_id* és az *erőforrás* az alkalmazás-azonosítóként korábban megjegyzett értékek. *Refresh_token* a korábban megemlített hitelesítési hívásban kapott jogkivonat.

A sikeres válasz a következő példához hasonlít:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhTmsifQ.eyJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNTE2ZmMwNjUtZmYzNi00YjkzLWFhNWEtZDZlZWRhN2NiYWM4L3YyLjAvIiwiZXhwIjoxNTMzNjc2NTkwLCJuYmYiOjE1MzM2NzI5OTAsImF1ZCI6IjljNTA2MThjLWY5NTEtNDlhNS1iZmU1LWQ3ODA4NTEyMWMzYSIsImlkcCI6IkxvY2FsQWNjb3VudCIsInN1YiI6ImJmZDgwODBjLTBjNDAtNDNjYS05ZTI3LTUyZTAyNzIyNWYyMSIsIm5hbWUiOiJEYXZpZE11IiwiZW1haWxzIjpbImRhdmlkd20xMDMwQGhvdG1haWwuY29tIl0sInRmcCI6IkIyQ18xX1JPUENfQXV0aCIsImF6cCI6IjljNTA2MThjLWY5NTEtNDlhNS1iZmU1LWQ3ODA4NTEyMWMzYSIsInZlciI6IjEuMCIsImlhdCI6MTUzMzY3Mjk5MH0.RULWeBR8--s5cCGG6XOi8m-AGyCaASx9W5B3tNUQjbVkHnGdo2_OUrnVoOZ1PTcrc1b0PQM2kVWi7NpYn57ifnqL_feTJPDbj9FJ8BmyxULdoECWxSM6KHsOPWZOIg5y1lNwN_IQ2HNF6UaDyYf1ZIM-jHr-uSfUnQXyWRnGDwNKX7TQbFmFk4oFMbPxTE7ioWAmxSnroiiB4__P9D0rUM1vf_qfzemf2ErIWSF9rGtCNBG-BvJlr3ZMCxIhRiIWNM2bVY0i3Nprzj0V8_FM6q8U19bvg9yDEzUcbe_1PMqzP3IrXW9N1XvQHupsOj8Keb7SmpgY1GG091X6wBCypw",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhTmsifQ.eyJleHAiOjE1MzM2NzY1OTAsIm5iZiI6MTUzMzY3Mjk5MCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzUxNmZjMDY1LWZmMzYtNGI5My1hYTVhLWQ2ZWVkYTdjYmFjOC92Mi4wLyIsInN1YiI6ImJmZDgwODBjLTBjNDAtNDNjYS05ZTI3LTUyZTAyNzIyNWYyMSIsImF1ZCI6IjljNTA2MThjLWY5NTEtNDlhNS1iZmU1LWQ3ODA4NTEyMWMzYSIsImlhdCI6MTUzMzY3Mjk5MCwiYXV0aF90aW1lIjoxNTMzNjcyOTkwLCJpZHAiOiJMb2NhbEFjY291bnQiLCJuYW1lIjoiRGF2aWRNdSIsImVtYWlscyI6WyJkYXZpZHdtMTAzMEBob3RtYWlsLmNvbSJdLCJ0ZnAiOiJCMkNfMV9ST1BDX0F1dGgiLCJhdF9oYXNoIjoiYW5hZ3QtX1NveUtBQV9UNFBLaHN4dyJ9.bPzpUFh94XFHXC_yR6qH_Unf6_hN-9-BjDXOzrdb1AuoU6-owQ3fWDxNBUbYEPALid3sgm4qhJ6BROFKryD8aWfrNyaErnYZwZ6rliHk4foa3JsbDgM3yNGPL0hzOFpC4Y9QhUjNgQOxvnQLtqbHVNonSvBc7VVPAjBDza44GowmvLORfJ1qkTjdrFM75HlLVeQch8cUNf-Ova77JdG5WHgYgqRhAq1OhV68YgEpQkARyz77zbAz9zZEHZZlgsli8UV6C-CPcmoHbwS-85mLzF9nLxhzjgIXJwckB6I7lvTpfuRtaqZIb3pMYeHZJaxaNLDvq9Qe4N-danXABg1B2w",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAifQ..8oC4Q6aKdr35yMWm.p43lns-cfWNFbtmrhvtssQXCItb3E9aSLafZJ6nKnnpXGQ-ZapOOyH7hPK7AN_RT7NMsQwNdy0Fyv_hOMrFbMPZNvHSa91RsQIvBZ73-CVy0HNF0grSezjCATg4NVHfricuQVegEmZKFOoNP6TaMC2kIlEi3rhrrO8VE3ZFQ3Jjo6j91BJaE9ybb02HWOoKqlzHiazwQyUHujw_R0TyXaQCI_gtLARr5QUXm7hlAfHhxR9uewQKlRbeuMH8nCMLSMASCJyzfeSJTjXmA0F0VrXozrqzOJdyy0EETPR7oA48MJ9l6C2sy2ZELkqpOM3xhbhV-Re7nM09b8DeWuCw7VNTcQc9DKnIHDR-H5U2Tc-lMJQadgUNZv7KGSRGTyprWb7wF7FEPnRNID5PCDV_N_yoQpI7VvJO_NotXEgHFo7OHs5Gsgwpl5mrDtymYzIMM7onTflOlu46em_qltji7xcWNOuHq4AeOlcY9ZythZgJH7livljReTwyX8QuUwpomXVEUGDc5pAnvgSozxnUbM7AlwfUeJZRT45P7L7683RSqChdNxiQk0sXUECqxnFxMAz4VUzld2yFe-pzvxFF4_feQjBEmSCAvekpvJUrEticEs4QzByV5UZ2ZCKccijFTg4doACiCo_z13JTm47mxm-5jUhXOQqiL69oxztk.KqI-z2LlC77lvwqmeFtdGQ",
    "refresh_token_expires_in": 1209600
}
```
> [!NOTE]
> Ha Graph APIon keresztül hoz létre felhasználókat, az alkalmazásnak az "OpenID", a "offline_access" és a "profil" engedélyekkel kell rendelkeznie a Microsoft Graph.

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>Implementálja az előnyben részesített natív SDK-val, vagy használja az App-Autht

A Azure AD B2C implementációja megfelel a OAuth 2,0 szabványoknak a nyilvános ügyfél erőforrás-tulajdonos jelszavának hitelesítő adataihoz, és kompatibilisnek kell lennie a legtöbb ügyféloldali SDK-val. Az Androidhoz készült iOS-es és AppAuth-alapú AppAuth esetében alaposan teszteltük ezt a folyamatot, éles környezetben. A legfrissebb információkért tekintse meg a [OAuth 2,0-hez készült natív app SDK-t és az OpenID connectet a modern ajánlott eljárások megvalósításához](https://appauth.io/).

Töltse le a GitHubról, [az Android](https://aka.ms/aadb2cappauthropc) és [az iOS](https://aka.ms/aadb2ciosappauthropc)rendszerhez készült Azure ad B2C használatára konfigurált munkamintákat.




