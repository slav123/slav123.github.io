---
title: "Email Catch: Od weekendowego projektu do alternatywy dla Amazon SES"
author: slav
type: post
date: 2025-07-20T16:42:07+02:00
url: /2025/email-catch/
description: "Wysokowydajny serwis SMTP napisany w Go jako alternatywa dla Amazon SES. Od weekendowego projektu do produkcyjnego narzędzia do odbierania i przetwarzania emaili."
thumbnail: /images/2025/Google_AI_Studio_2025-07-20T15_07_42.166Z.webp
categories:
- Blog
- Go
- DevOps

draft: false
translationKey: "email-catch-2025"
---

# Email Catch: Od weekendowego projektu do alternatywy dla Amazon SES

Jako programista, który lubi eksperymentować w weekendy, postanowiłem zmagać się z problemem, który prawdopodobnie dotyka wielu z nas - jak w prosty sposób odbierać i przetwarzać emaile bez konieczności używania skomplikowanych rozwiązań chmurowych.

## Genesis projektu

Historia Email Catch to typowa droga ewolucji weekendowego projektu. Wszystko zaczęło się od prostej potrzeby: chciałem mieć kontrolę nad tym, jak moja aplikacja odbiera emaile. Pierwszą wersję napisałem w Node.js - szybko, funkcjonalnie, potem pojawił się golang.

Postanowiłem przepisać całość. Wykorzystałem podejście "vibe coding" - poniewa moja znajomość Go była lekko juz zakorzuona.

## Dlaczego nie serverless?

Kiedy pojawił się Amazon SES, naturalnym wyborem wydawało się skorzystanie z rozwiązań serverless i zestawienie wszystkiego na AWS Lambda. I rzeczywiście - przez jakiś czas tak robiłem. Jednak z czasem złożoność całego rozwiązania zaczęła mnie przytłaczać:

- Konfiguracja wielu Lambda function
- Zarządzanie IAM rolami
- Debugowanie rozproszonych procesów
- Vendor lock-in

To wszystko doprowadziło mnie do wniosku, że potrzebuję czegoś prostszego, bardziej kontrolowalnego.

## Co powstało?

Email Catch to wysokowydajny serwis SMTP napisany w Go, który:

**Odbiera emaile** - wystarczy ustawić rekord MX na nasz serwer i wszystkie emaile wysłane na naszą domenę trafiają bezpośrednio do aplikacji.

**Routing na sterydach** - system reguł pozwala na przekierowanie emaili na podstawie domeny i wzorca adresata.

**Elastyczne przechowywanie** - wsparcie dla:
- Zapisu lokalnego (pliki .eml)
- S3-kompatybilnych rozwiązań (AWS S3, MinIO, DigitalOcean Spaces)

**Webhooks** - natychmiastowe powiadomienia z pełną treścią emaila w formacie JSON

## Integracja z automatyzacją

Prawdziwa siła Email Catch ujawnia się w połączeniu z narzędziami automatyzacji jak [https://n8n.io/](n8n). Dzięki webhookom można w prosty sposób:

**Przetwarzać faktury** - automatyczne wyciąganie danych z załączników PDF i przekazywanie do systemu księgowego

**Powiadomienia** - natychmiastowe alerty na Slacku o nowych emailach z określonego domeny i adresata

**Archiwizacja** - inteligentny zapis ważnych emaili z kategoryzacją

## Bezpieczeństwo 

Email Catch został zaprojektowany z myślą o bezpieczeństwie:

- **TLS/SSL** - szyfrowana komunikacja z automatycznymi certyfikatami Let's Encrypt
- **Wieloportowość** - wsparcie dla standardowych portów SMTP (25, 587, 465, 2525)
- **Graceful shutdown** - bezpieczne zamykanie połączeń
- **Rate limiting** - ochrona przed nadużyciami

## Podsumowanie

Czasami najlepsze rozwiązania powstają z frustracji istniejącymi narzędziami. Email Catch pokazuje, że nie zawsze potrzebujemy skomplikowanych rozwiązań chmurowych - czasem prosty, dobrze napisany serwis może być znacznie bardziej efektywny.

Jeśli szukasz alternatywy dla Amazon SES lub po prostu chcesz mieć pełną kontrolę nad odbieraniem emaili w swojej aplikacji, Email Catch może być odpowiedzią na Twoje potrzeby.

---

**Email Catch jest dostępny na GitHub**: https://github.com/slav123/email-catch

*Projekt jest open source na licencji MIT - contributions welcome!*