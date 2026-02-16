---
title: "Podstawy LDAP: Co musisz wiedzieć"
author: slav
type: post
date: 2026-02-16T09:42:07+02:00
url: /2026/podstawy-ldap/
description: "Koniec z zarządzaniem wieloma bazami użytkowników. Dowiedz się, jak użyć lldap do centralizacji uwierzytelniania w Nextcloud, Gitea i Grafanie — prosty przewodnik po LDAP."
thumbnail: /images/2026/ldap-practical-guide.webp
categories:
- Blog
---
## Problem

Masz kilka self-hostowanych usług — Nextcloud, Gitea, Grafana, może więcej. Każda z nich ma własną listę użytkowników i haseł. Dochodzi nowa osoba? Zakładasz pięć kont. Ktoś odchodzi? Masz nadzieję, że pamiętasz, gdzie miał dostęp.

LDAP to katalog, który trzyma wszystkich użytkowników i grupy w jednym miejscu. Usługi pytają go „czy ta osoba istnieje?" i „czy ma tu wstęp?" zamiast utrzymywać własne listy użytkowników.

**lldap** to lekki serwer LDAP stworzony dokładnie do tego celu. Bez korporacyjnej złożoności — tylko użytkownicy, grupy i przejrzysty interfejs webowy.

---

## System nazewnictwa

Nazwy w LDAP wyglądają dziwnie na pierwszy rzut oka, ale kryje się za nimi prosty wzorzec.

### Twoja domena staje się korzeniem

Weź swoją nazwę domeny, rozdziel ją po kropkach i dodaj do każdej części prefiks `dc=`:

```
example.com      →  dc=example,dc=com
home.lab         →  dc=home,dc=lab
yak.consulting   →  dc=yak,dc=consulting
```

To się nazywa **Base DN** — korzeń drzewa katalogowego.

### Wszystko jest ścieżką

Każdy wpis w LDAP ma pełny adres zwany **Distinguished Name** (DN). Czyta się go od najbardziej szczegółowego elementu po lewej do najbardziej ogólnego po prawej:

```
uid=slav, ou=people, dc=yak, dc=consulting
│         │          └── domena
│         └── w folderze "people"
└── użytkownik "slav"
```

To jak ścieżka do pliku zapisana od tyłu: zamiast `/yak.consulting/people/slav` dostajesz `uid=slav,ou=people,dc=yak,dc=consulting`.

### Popularne skróty

| Skrót | Znaczenie | Pomyśl o tym jak o |
|---|---|---|
| **dc** | Domain Component | Twoja domena rozbita na części |
| **ou** | Organizational Unit | Folder |
| **uid** | User ID | Nazwa użytkownika |
| **cn** | Common Name | Nazwa grupy |
| **dn** | Distinguished Name | Pełna ścieżka do dowolnego wpisu |

---

## Drzewo

Typowy katalog lldap wygląda tak:

```
dc=yak,dc=consulting           ← korzeń
├── ou=people                   ← tu trafiają użytkownicy
│   ├── uid=slav
│   └── uid=anna
└── ou=groups                   ← tu trafiają grupy
    ├── cn=nextcloud_users
    └── cn=gitea_users
```

lldap tworzy tę strukturę automatycznie. Wystarczy ustawić Base DN i zacząć dodawać użytkowników oraz grupy przez interfejs webowy.

---

## Grupy kontrolują dostęp

To kluczowa koncepcja. Nie dajesz użytkownikowi bezpośredniego dostępu do usługi — dodajesz go do **grupy**, a usługa sprawdza przynależność do grupy.

Chcesz, żeby ktoś miał dostęp do Nextcloud? Dodaj go do `nextcloud_users`.
Chcesz odebrać dostęp? Usuń z grupy.
Chcesz wdrożyć kogoś do wszystkiego? Dodaj do odpowiednich grup w jednym miejscu.

---

## Jak usługi się łączą

Kiedy podłączasz usługę do lldap, potrzebuje ona kilku rzeczy:

- **Gdzie jest LDAP?** — adres serwera i port
- **Jaki jest korzeń?** — Twój Base DN
- **Jak się zalogować, żeby szukać?** — konto serwisowe (tzw. Bind DN), które może wyszukiwać użytkowników
- **Jak znaleźć użytkowników?** — filtr wyszukiwania, np. „znajdź użytkownika pasującego do tej nazwy"
- **Kto ma wstęp?** — filtr grupowy, np. „wpuść tylko członków tej grupy"

Usługa nigdy nie widzi haseł. Po prostu prosi lldap o ich weryfikację.

---

## Model mentalny

Jeśli zapamiętasz tylko tyle, to już wystarczy:

- **Base DN** = Twoja domena w stylu LDAP → korzeń drzewa
- **Użytkownicy** mieszkają w `ou=people` pod korzeniem
- **Grupy** mieszkają w `ou=groups` pod korzeniem
- **Każdy wpis** ma pełną ścieżkę (DN) wynikającą z jego pozycji w drzewie
- **Dostęp** kontrolują grupy, do których użytkownik należy
- **Usługi** łączą się z LDAP kontem tylko do odczytu i sprawdzają użytkowników po grupach