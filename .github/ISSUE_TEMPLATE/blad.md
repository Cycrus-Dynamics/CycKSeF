---
name: Zgłoszenie błędu
about: Coś nie działa w CycKSeF (wersja Darmowa)
title: "[Błąd] "
labels: bug
assignees: ""
---

<!--
UWAGA — TO ZGŁOSZENIE JEST PUBLICZNE.
NIE wklejaj: danych faktur, numerów KSeF, certyfikatów ani tokenów KSeF, haseł,
pliku .env, poswiadczenia-admina.txt, NIP-ów kontrahentów, danych osobowych.
Zamaskuj takie dane w logach PRZED wysłaniem (np. zastąp je "***").

Klienci z pakietem płatnym: hello@cycrusdynamics.com albo https://cycksef.pl/#kontakt
-->

## Wersja CycKSeF

<!-- Panel → Konfiguracja → „Wersja aplikacji". Na Linuksie dodatkowo: `./status.sh` w /opt/cycksef pokazuje wersję obrazów. -->

Wersja aplikacji:

## Środowisko

- System operacyjny i wersja (np. Ubuntu 24.04 / Debian 12 / Rocky 9 / Windows 11):
- Docker: wynik `docker --version` i `docker compose version`:
- Tryb SSL (`selfsigned` / `letsencrypt` / `custom` / `none`):
- Edycja: Darmowa / płatna (bez wklejania klucza):

## Kroki do odtworzenia

1.
2.
3.

## Oczekiwane zachowanie

<!-- Co powinno się stać. -->

## Rzeczywiste zachowanie

<!-- Co się dzieje zamiast tego. Komunikat błędu — jeśli jest, przeklej dosłownie (po zamaskowaniu danych wrażliwych). -->

## Wynik `./doctor.sh`

<!--
Linux: w katalogu /opt/cycksef uruchom `./doctor.sh` i wklej wynik poniżej.
Przy problemie z instalacją: `./doctor.sh --pre`; przy problemie z działającą aplikacją: `./doctor.sh --post`.
Windows: `doctor.sh` nie jest dostępny — wklej zamiast tego wynik `docker compose ps` z katalogu C:\CycKSeF\deploy.
Przed wklejeniem usuń z wyniku domenę/adresy, jeśli nie chcesz ich ujawniać.
-->

```text

```

## Fragment logów (opcjonalnie)

<!--
Linux: `./logs.sh backend --tail 200` albo `./logs.sh celery_worker --tail 200`.
Windows: `docker compose logs backend` / `docker compose logs celery_worker`.
Tylko fragment dotyczący problemu, po zamaskowaniu danych wrażliwych.
-->

```text

```

## Dodatkowe informacje

<!-- Zrzuty ekranu (bez danych faktur), co już próbowałeś/aś, od kiedy występuje. -->
