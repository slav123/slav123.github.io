---
title: "Przestań budować strony logowania: Jak działa zewnętrzna autoryzacja"
author: slav
type: post
date: 2026-02-13T16:42:07+02:00
url: /2026/zewnetrzna-autoryzacja/
description: "Zewnętrzna autoryzacja stawia niezależny serwis przed Twoją aplikacją. Obsługuje logowanie, MFA i sesje. Twoja aplikacja tylko odczytuje nagłówki HTTP."
thumbnail: /images/2026/external-authorization.webp
categories:
- Blog
- Go
- DevOps
- Security
---

Masz aplikację webową. Potrzebujesz uwierzytelniania. Intuicja podpowiada zbudowanie systemu logowania — sesje, hashowanie haseł, MFA, cały stos. Na szczęście jest opcja, która usuwa to wszystko z Twojej aplikacji.

Zewnętrzna autoryzacja stawia niezależny serwis przed Twoją aplikacją. Obsługuje przepływ logowania, uwierzytelnianie wieloskładnikowe i zarządzanie sesjami. Twoja aplikacja dostaje tylko wynik: zestaw nagłówków HTTP mówiących, kim jest użytkownik.

## Jak działa ten przepływ

Każde żądanie do Twojej aplikacji najpierw przechodzi przez reverse proxy — Caddy, Nginx, Traefik lub podobny. Zanim cokolwiek trafi do backendu, proxy wysyła boczne zapytanie do serwisu autoryzacyjnego: „Czy ta osoba jest uwierzytelniona?"

Mogą się wydarzyć dwie rzeczy. Jeśli użytkownik ma ważną sesję, serwis auth odpowiada 200 OK i dołącza nagłówki tożsamości — nazwa użytkownika, email, nazwa wyświetlana, przynależność do grup. Proxy wstrzykuje te nagłówki do oryginalnego żądania i przekazuje je do Twojej aplikacji. Jeśli nie ma ważnej sesji, użytkownik zostaje przekierowany do portalu logowania, gdzie się uwierzytelnia (hasło, MFA, passkeys — cokolwiek obsługuje serwis auth). Po udanym logowaniu wraca i przepływ kończy się normalnie.

Twój backend nigdy nie widzi nieuwierzytelnionych żądań. Nigdy nie przechowuje haseł. Nigdy nie renderuje formularza logowania. Po prostu odczytuje kilka nagłówków i dokładnie wie, kto puka.

## Zaufane nagłówki

Konwencja w większości zewnętrznych systemów auth wygląda tak:

| Nagłówek | Co zawiera |
|---|---|
| `Remote-User` | Nazwa użytkownika lub unikalny ID |
| `Remote-Email` | Adres email |
| `Remote-Name` | Nazwa wyświetlana |
| `Remote-Groups` | Lista grup rozdzielona przecinkami |

Niektóre systemy używają nieco innego nazewnictwa (Authentik prefiksuje wszystko `X-Authentik-`), ale koncept jest identyczny. Twoja aplikacja odczytuje nagłówki, wyciąga informacje o użytkowniku i podejmuje decyzje autoryzacyjne na podstawie przynależności do grup lub tożsamości.

## Narzędzia

**Authelia** to lekki, open-source'owy serwer auth napisany w Go. Poniżej 20 MB, poniżej 30 MB RAM w użyciu i szybki. Obsługuje TOTP, WebAuthn, powiadomienia push i OpenID Connect. Został zaprojektowany specjalnie jako towarzysz reverse proxy i działa od razu z Caddy, Traefik, Nginx i HAProxy. Jeśli szukasz czegoś minimalnego, co dobrze robi swoje — to jest to.

**Authentik** to cięższa alternatywa. Przychodzi z pełnym panelem administracyjnym, konsolą zarządzania użytkownikami, wsparciem LDAP i SAML oraz dostawcami logowania społecznościowego. Można o nim myśleć jak o pełnej platformie tożsamości, a nie tylko bramce auth. Świetny, jeśli musisz zarządzać użytkownikami w wielu serwisach lub potrzebujesz funkcji enterprise. Więcej zasobów, więcej możliwości w zamian.

**Vouch Proxy** podchodzi do tematu z innej strony. Zamiast być pełnym dostawcą tożsamości, deleguje uwierzytelnianie do istniejących providerów OAuth/OIDC — Google, GitHub, Keycloak lub czegokolwiek, co mówi tym protokołem. To cienki most, który tłumaczy sesje OAuth na zaufane nagłówki dla Twojego proxy. Przydatny, gdy masz już dostawcę tożsamości i potrzebujesz tylko warstwy integracji z proxy.

**Caddy** zasługuje na osobną wzmiankę po stronie proxy. Jego dyrektywa `forward_auth` sprawia, że zewnętrzna autoryzacja to funkcja pierwszej klasy. Wskazujesz na swój serwis auth, mówisz które nagłówki z odpowiedzi skopiować i gotowe. Żadnych skryptów Lua, żadnej złożonej konfiguracji subrequestów. Kilka linii w Caddyfile i cały przepływ jest podpięty. Nginx i Traefik potrafią to samo, ale Caddy robi to zauważalnie prościej.

## Co Musi Zrobić Twoja Aplikacja

Prawie nic. I o to chodzi.

W Go piszesz middleware, który odczytuje nagłówek `Remote-User` i odrzuca żądania, gdzie jest pusty. Parsujesz nagłówek grup, jeśli potrzebujesz dostępu opartego na rolach. Przechowujesz informacje o użytkowniku w kontekście żądania. Kilkanaście linii kodu, zero zewnętrznych zależności.

W PHP nagłówki trafiają jako zmienne `$_SERVER` (`HTTP_REMOTE_USER`, `HTTP_REMOTE_EMAIL` itp.). Sprawdzasz czy są obecne, wyciągasz wartości i jedziesz dalej. W Laravelu to klasa middleware. W czystym PHP to para funkcji.

Żadnej walidacji tokenów, żadnej weryfikacji kryptograficznej, żadnej biblioteki do zainstalowania. Model zaufania jest w całości oparty na sieci — co prowadzi do najważniejszej części.

## Bezpieczeństwo

Zaufane nagłówki nie mają wbudowanej kontroli integralności. Nie ma podpisu. Nie ma szyfrowania. Jeśli ktoś może dotrzeć do Twojej aplikacji bezpośrednio — omijając proxy — może ustawić `Remote-User: admin` i wejść jak do siebie.

To oznacza, że bezpieczeństwo całego systemu zależy od jednej rzeczy: **Twoja aplikacja musi być nieosiągalna inaczej niż przez proxy.**

W Dockerze to oznacza niepublikowanie portów aplikacji. Na bare metal to oznacza bindowanie do `127.0.0.1` lub prywatnego interfejsu sieciowego. Jeśli żadne z tych rozwiązań nie jest możliwe, Twoja aplikacja powinna walidować, że żądania przychodzą z adresu IP proxy i odrzucać wszystko inne.

Reverse proxy też ma tu swoją robotę. Musi usunąć wszelkie nagłówki `Remote-*` z oryginalnego żądania klienta przed wykonaniem subrequestu auth. W przeciwnym razie złośliwy klient mógłby wysłać ustawione z góry nagłówki, które przetrwają łańcuch proxy. `forward_auth` w Caddy obsługuje to automatycznie. Nginx i Traefik wymagają jawnej konfiguracji, żeby zrobić to poprawnie.

## Zaufane nagłówki vs. OpenID Connect

Zewnętrzne systemy auth zazwyczaj obsługują oba podejścia. Zaufane nagłówki są prostsze — żadnych bibliotek, żadnego cyklu życia tokenów, żadnej logiki odświeżania. Działają idealnie dla aplikacji za jednym proxy, narzędzi wewnętrznych, dashboardów i self-hosted serwisów.

OpenID Connect to właściwy wybór, gdy Twoja aplikacja żyje na wielu domenach, gdy musi wykonywać wywołania API w imieniu użytkownika lub gdy budujesz coś, z czym będą się integrować osoby trzecie. Wymaga więcej pracy przy implementacji, ale niesie własny dowód autentyczności w każdym tokenie.

Dobra wiadomość jest taka, że zarówno Authelia, jak i Authentik obsługują OIDC obok zaufanych nagłówków. Możesz zacząć od nagłówków dla prostoty i przejść na OIDC później, jeśli wymagania urosną — bez zmiany infrastruktury auth.

W większości scenariuszy self-hosted i wewnętrznych zaufane nagłówki to pragmatyczny punkt wyjścia. Proste we wdrożeniu, proste w debugowaniu i jedna rzecz mniej, o którą Twoja aplikacja musi się martwić.