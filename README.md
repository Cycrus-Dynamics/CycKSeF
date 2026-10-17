# CycKSeF

CycKSeF to program self-hosted do obsługi faktur z Krajowego Systemu e-Faktur (KSeF):
sam pobiera faktury z KSeF, klasyfikuje je (towar / koszt), przypisuje do oddziałów
według reguł i pilnuje statusów (nieprzejrzana, w toku, załatwiona, zaakceptowana,
niezasadna). Instalujesz go na własnym serwerze — faktury, kontrahenci i certyfikat
KSeF zostają u Ciebie.

To repozytorium **nie zawiera kodu źródłowego** (CycKSeF jest programem zamkniętym).
Służy jako strona projektu: instrukcja instalacji, warunki wersji Darmowej
i zgłoszenia przez [Issues](https://github.com/Cycrus-Dynamics/CycKSeF/issues).

- Strona produktu i cennik: <https://cycksef.pl/#cennik>
- Demo z przykładowymi danymi: <https://demo.cycksef.pl> (logowanie podane na stronie
  w sekcji „Pytania"; dane fikcyjne, resetowane co noc, bez połączenia z prawdziwym KSeF)
- Regulamin: <https://cycksef.pl/regulamin/>

---

## Wersja Darmowa a pakiety płatne

Wersja Darmowa: **0 zł, bez rejestracji, karty i klucza, bez limitu czasu**.
Instalacja jest ta sama dla wersji Darmowej i płatnej — różni je tylko klucz
licencji wklejony w panelu.

| | Darmowa | Pakiety płatne (Start / Firma / Sieć / Bez limitu) |
|---|---|---|
| Cena | 0 zł, bezterminowo | wg [cennika](https://cycksef.pl/#cennik), rozliczenie miesięczne lub roczne |
| Limit faktur | do 50 / mies. | od 100 / mies. do bez limitu |
| Konta użytkowników | 2 | więcej, zależnie od pakietu |
| Automatyczne pobieranie faktur z KSeF | tak | tak |
| Lista i szczegóły faktur, tagi i statusy | tak | tak |
| Przypisanie do oddziałów + reguły automatyczne | tak | tak |
| Kontrahenci z danymi z GUS, podział towar / koszt | tak | tak |
| Eksport PDF / XML / CSV / Excel, statystyki | tak | tak |
| Role i uprawnienia, logowanie FIDO2 | tak | tak |
| Wystawianie faktur do KSeF | — | tak |
| Wrzutnia (zgłaszanie faktur do akceptacji) | — | tak |
| Raporty Excel i harmonogram e-mail | — | tak |
| Wysyłka dokumentów do oddziałów (SMB / FTP / SFTP / S3) | — | tak |
| API / integracje | — | tak (pakiet Sieć i wyżej) |
| Wsparcie | tylko GitHub Issues, bez gwarancji terminu | e-mail (priorytetowe w wyższych pakietach) |

Przejście na pakiet płatny nie wymaga reinstalacji — klucz wklejasz w panelu,
dane i konfiguracja zostają (patrz [Aktywacja płatnej licencji](#aktywacja-płatnej-licencji)).

---

## Wymagania

### Linux

- **System:** Ubuntu 22.04 / 24.04, Debian 12 / 13 albo Rocky Linux 9 / 10; architektura
  **x86_64** (na innej instalator ostrzeże, ale obrazy są publikowane tylko dla x86_64).
- **Zasoby:** min. 4 GB RAM (zalecane 8 GB), min. 20 GB wolnego miejsca, 2 vCPU.
- **Sieć:** wolne porty panelu (domyślnie 80 i 443; zajęty port instalator wykrywa
  i proponuje zamiennik), działające DNS oraz połączenia wychodzące (443) do:
  - `api-test.ksef.mf.gov.pl` / `api-demo.ksef.mf.gov.pl` / `api.ksef.mf.gov.pl` — zależnie od środowiska KSeF,
  - `wl-api.mf.gov.pl` — biała lista VAT,
  - `ghcr.io` — rejestr obrazów Docker,
  - `cycksef.pl` — pobranie paczki instalacyjnej,
  - `license.cycr.us` — aktywacja licencji w panelu (opcjonalnie),
  - serwera SMTP, jeśli chcesz powiadomienia e-mail.
- **Oprogramowanie:** `curl` z obsługą HTTPS (musi być **przed** uruchomieniem komendy
  instalacji). Docker Engine z wtyczką `docker compose` instalator stawia sam, jeśli ich brak.
- **Uprawnienia:** `root` albo działające `sudo`.
- **Zegar:** zsynchronizowany (NTP) — rozjazd czasu to częsta przyczyna błędów KSeF.
- **Certyfikaty KSeF** z portalu Ministerstwa Finansów (`sign.crt`, `sign.key`,
  `auth.crt`, `auth.key`) albo token KSeF — można je dodać po instalacji, w panelu.

Klucz FIDO2 **nie jest wymagany** — domyślnie logujesz się e-mailem i hasłem
wygenerowanym przez instalator.

### Windows

- Windows 10 / 11 64-bit albo Windows Server 2019+ (32-bit nieobsługiwany).
- 8 GB RAM, 20 GB wolnego miejsca.
- WSL2 i Docker Desktop (najnowszy, z `docker compose` v2); wirtualizacja (Intel VT-x / AMD-V)
  włączona w BIOS/UEFI.
- Połączenie internetowe (port 443 wychodzący).

Pełna instrukcja dla Windows: <https://cycksef.pl/windows/>

---

## Instalacja

Obrazy Docker są publiczne (`ghcr.io/cycrus-dynamics/cycksef/backend`
i `ghcr.io/cycrus-dynamics/cycksef/frontend`) — instalator pobiera je sam, bez logowania
do rejestru.

### Linux — jedna komenda

```bash
curl -fsSL https://cycksef.pl/install.sh | sudo bash
```

Skrypt sprawdza system, dogrywa `tar`/`coreutils` jeśli ich brak, pobiera paczkę
wdrożeniową, **weryfikuje jej sumę SHA256**, rozpakowuje do `/opt/cycksef` i uruchamia
kreator konfiguracji.

**Wolisz najpierw przejrzeć, co uruchamiasz?**

```bash
curl -fsSLO https://cycksef.pl/install.sh
less install.sh                                    # przeczytaj przed uruchomieniem jako root
curl -fsSLO https://cycksef.pl/cycksef-deploy-latest.tar.gz
curl -fsSLO https://cycksef.pl/cycksef-deploy-latest.tar.gz.sha256
sha256sum -c cycksef-deploy-latest.tar.gz.sha256   # zweryfikuj sam, niezależnie od install.sh
sudo bash install.sh --package ./cycksef-deploy-latest.tar.gz
```

**Podgląd bez zmian** (co instalator by zrobił) — z katalogu `/opt/cycksef`:

```bash
sudo ./install.sh --dry-run
```

#### O co pyta kreator

Kreator przechodzi 9 kroków; pytania są tylko w trzech:

1. **Docker i sieć** — domena albo adres IP panelu, tryb SSL (`selfsigned` domyślnie;
   `letsencrypt` wymaga publicznej domeny i portu 80; `custom` — własny certyfikat;
   `none` tylko do testu z samego serwera, bo logowanie spoza serwera wtedy nie działa),
   porty HTTP/HTTPS.
2. **Rejestr obrazów** — domyślnie `ghcr.io/cycrus-dynamics`, bez pytań o login i hasło.
3. **Konfiguracja** — dane KSeF (środowisko + certyfikaty albo token; można pominąć
   i dokończyć w panelu), dane sprzedawcy, SMTP (opcjonalnie), strefa czasowa,
   autoaktualizacje (domyślnie wyłączone).

Kreator **nie pyta** o klucz licencji (wklejasz go później w panelu) ani o login/hasło
administratora (generuje je sam).

### Windows — PowerShell jako Administrator

Zainstaluj i uruchom Docker Desktop (ikona wieloryba w zasobniku ma przestać się ruszać),
a potem w PowerShellu uruchomionym **jako Administrator**:

```powershell
irm https://cycksef.pl/install.ps1 | iex
```

Kreator zapyta o NIP firmy, nazwę firmy, e-mail administratora, port panelu
(domyślnie `8080`), tryb SSL i SMTP. Instalacja ląduje w `C:\CycKSeF\deploy\`.

Wariant „pobierz, przejrzyj, uruchom":

```powershell
# Pobierz instalator i paczke
curl.exe -fsSLO https://cycksef.pl/install.ps1
curl.exe -fsSLO https://cycksef.pl/cycksef-deploy-latest.tar.gz
curl.exe -fsSLO https://cycksef.pl/cycksef-deploy-latest.tar.gz.sha256

# Sprawdz sume SHA256 samodzielnie
Get-FileHash -Algorithm SHA256 .\cycksef-deploy-latest.tar.gz

# Uruchom
powershell -ExecutionPolicy Bypass -File .\install.ps1 -Pakiet .\cycksef-deploy-latest.tar.gz
```

Szczegóły, typowe problemy (WSL2, wirtualizacja, antywirus) i różnice względem Linuksa:
<https://cycksef.pl/windows/>

---

## Pierwsze kroki po instalacji

1. **Zaloguj się.** Na końcu instalacji zobaczysz adres panelu i login administratora
   (domyślnie `admin@<domena>`). Hasło nie trafia do terminala — odczytaj je z pliku:

   ```bash
   sudo cat /opt/cycksef/poswiadczenia-admina.txt
   ```

   (Windows: `C:\CycKSeF\deploy\poswiadczenia-admina.txt`.)

2. **Zmień hasło zaraz po pierwszym logowaniu:** Panel → Użytkownicy → Twoje konto →
   ikona kłódki „Ustaw hasło". Dopóki tego nie zrobisz, plik `poswiadczenia-admina.txt`
   zawiera aktywne hasło w czystym tekście — usuń go po zmianie.

3. **Przejdź kreator „Pierwsza konfiguracja"** (baner po zalogowaniu): dane firmy,
   KSeF (certyfikaty `sign.crt`, `sign.key`, `auth.crt`, `auth.key` albo token
   + środowisko), oddziały i prefiks tagów, SMTP, dodatkowe konta i role. Każdy krok
   zapisuje się osobno — kreator można zamknąć i wznowić, albo pominąć i wrócić do niego
   z menu. Certyfikaty są walidowane i szyfrowane przed zapisem; nie trzeba kopiować
   ich na serwer. Jeśli nie masz jeszcze certyfikatów KSeF, dokończ konfigurację później
   w **Konfiguracja → Połączenie z KSeF** (jest tam test połączenia).

4. **Dodaj użytkowników** (Użytkownicy → Dodaj). W wersji Darmowej: 2 konta.

Logowanie e-mailem i hasłem jest domyślne. Klucz FIDO2 albo drugi składnik
(kod e-mail / TOTP) włącza się zmianą `AUTH_LOGIN_METHODS` w `/opt/cycksef/.env`
(`password`, `password_email`, `password_totp` albo `fido2` — jedno z nich, nie łącz
`password` z `fido2`), potem `docker compose restart backend frontend`.

**Ważne przy backupie i reinstalacji:** zaszyfrowane klucze, token i hasła są powiązane
z `SECRET_KEY` w `.env`. Zachowaj jego dotychczasową wartość.

---

## Aktywacja płatnej licencji

Bez klucza instalacja działa bezterminowo jako wersja Darmowa. Po zakupie pakietu
w [cenniku](https://cycksef.pl/#cennik):

1. Zaloguj się do panelu jako administrator.
2. **Ustawienia → Licencja** → wklej klucz.

Dane, faktury i konfiguracja zostają; nic nie instalujesz od nowa. Klucz **nie jest**
podawany instalatorowi ani wpisywany do pliku konfiguracyjnego. Panel potrzebuje
połączenia z `license.cycr.us` (443); wywołania do serwera licencji nigdy nie blokują
instalacji ani startu.

---

## Aktualizacja

Linux — z katalogu `/opt/cycksef`:

```bash
./upgrade.sh
```

Kroki: obowiązkowy backup → `docker compose pull` → migracje bazy → restart usług →
health-check. Jeśli backend nie odpowie po aktualizacji, skrypt sam przywraca poprzednie
obrazy; jeśli migracja już się wykonała, ostateczny ratunek to `./restore.sh` z backupu
sprzed aktualizacji.

Automatyczne aktualizacje nocne są **domyślnie wyłączone** (świadomy opt-in):

```bash
sudo ./install-auto-updates.sh --enable
sudo ./install-auto-updates.sh --disable
```

Windows — pobierz nową paczkę i uruchom instalator ponownie (nie nadpisze `.env` ani danych):

```powershell
curl.exe -fsSLO https://cycksef.pl/cycksef-deploy-latest.tar.gz
curl.exe -fsSLO https://cycksef.pl/cycksef-deploy-latest.tar.gz.sha256
Get-FileHash -Algorithm SHA256 .\cycksef-deploy-latest.tar.gz
powershell -ExecutionPolicy Bypass -File .\install.ps1 -Pakiet .\cycksef-deploy-latest.tar.gz
```

Automatyczne aktualizacje nie są obsługiwane na Windows.

---

## Kopia zapasowa i odtwarzanie

Linux — z katalogu `/opt/cycksef`:

```bash
./backup.sh                 # backups/cycksef-<data-godzina>.tar.gz
./backup.sh --keep 30        # zachowaj 30 najnowszych zamiast domyślnych 14
```

Archiwum zawiera zrzut bazy (weryfikowany przed zakończeniem), `.env`, `config/*.yaml`,
`certs/ksef/`, `nginx/`, `nginx-certs/`; ma uprawnienia 600. Regularny backup z crona:

```bash
# /etc/cron.d/cycksef-backup
0 3 * * * root cd /opt/cycksef && ./backup.sh >>/var/log/cycksef-backup.log 2>&1
```

Odtwarzanie (domyślnie tylko baza danych, z potwierdzeniem i dodatkowym backupem
bezpieczeństwa bieżącego stanu):

```bash
./restore.sh backups/cycksef-20260910-030000.tar.gz
```

Odtworzenie także `.env`, certyfikatów i konfiguracji (np. przenosiny na nowy serwer):

```bash
./restore.sh --restore-config backups/cycksef-20260910-030000.tar.gz
```

Certyfikat Let's Encrypt nie jest częścią backupu — na nowym serwerze po
`--restore-config` uruchom `./scripts/setup-ssl.sh`.

Windows — backup bazy ręcznie (katalog `C:\CycKSeF\deploy`, backupy w `backups\`):

```powershell
cd C:\CycKSeF\deploy
docker compose exec -T db pg_dump -U ksef ksef > backups\backup-$(Get-Date -Format 'yyyyMMdd-HHmmss').sql
```

Regularne backupy na Windows — Harmonogram zadań (Task Scheduler).

---

## Odinstalowanie

Linux — z katalogu `/opt/cycksef`:

```bash
./uninstall.sh
```

Pyta osobno o: backup przed odinstalowaniem (domyślnie **tak**), usunięcie danych —
bazy, Redis, certyfikatów Let's Encrypt (domyślnie **nie**), usunięcie `.env`
i certyfikatów KSeF z katalogu (domyślnie **nie**). Nic nie usuwa bez pytania.

Windows:

```powershell
# 1. Zatrzymaj i usun kontenery
cd C:\CycKSeF\deploy
docker compose down -v

# 2. Usun katalog instalacji
Remove-Item -Recurse -Force C:\CycKSeF

# 3. (Opcjonalnie) usun obrazy Docker
docker image prune -a
```

---

## Codzienna obsługa (Linux, katalog `/opt/cycksef`)

| Co | Komenda |
|----|---------|
| Diagnostyka / stan systemu | `./doctor.sh` |
| Szybki status (kontenery, wersje, backupy) | `./status.sh` |
| Logi | `./logs.sh [-f] [usługa]` |
| Backup | `./backup.sh` |
| Odtworzenie z backupu | `./restore.sh backups/cycksef-<data>.tar.gz` |
| Aktualizacja | `./upgrade.sh` |
| Włącz/wyłącz aktualizacje nocne | `sudo ./install-auto-updates.sh --enable\|--disable` |
| Odinstalowanie | `./uninstall.sh` |
| Autostart po reboocie (systemd) | `sudo ./install-systemd.sh` |

Szczegóły każdej komendy: `<komenda> --help`. Pełna instrukcja (warianty SSL, rozwiązywanie
problemów, FAQ) jest w zainstalowanej paczce: `/opt/cycksef/docs/INSTALACJA.md`.

Najczęstsze problemy:

| Problem | Pierwszy krok |
|---------|---------------|
| Cokolwiek nie działa | `./doctor.sh` — mówi co i jak naprawić |
| 502 Bad Gateway | `docker compose restart nginx` |
| Nie pobiera faktur | `./logs.sh celery_worker` + `docker compose restart celery_beat`; sprawdź egress do KSeF |
| Nie mogę się zalogować | sprawdź login/hasło w `poswiadczenia-admina.txt` (jeśli jeszcze jest), `SSL_MODE` inny niż `none`, wyczyść cookies |
| Zgubione hasło admina | `printf '%s' 'NOWE_HASLO' \| docker compose exec -T backend python scripts/reset_password.py admin@twoja-domena.pl --password-stdin` |
| Certyfikat SSL wygasł/zły | `./scripts/setup-ssl.sh` |
| Instalacja przerwana na preflightcie | `./doctor.sh --pre`, napraw wskazane punkty, uruchom `sudo ./install.sh` ponownie |

---

## Wsparcie i zgłoszenia

- **Wersja Darmowa:** wyłącznie przez [GitHub Issues](https://github.com/Cycrus-Dynamics/CycKSeF/issues)
  w tym repozytorium, bez gwarancji terminu ani faktu odpowiedzi. Skorzystaj z szablonów
  „Zgłoszenie błędu" i „Pytanie".
- **Pakiety płatne:** wsparcie e-mail — <hello@cycrusdynamics.com> albo
  <https://cycksef.pl/#kontakt>.

**Zgłoszenia są publiczne.** Nie wklejaj w nich danych faktur, certyfikatów ani tokenów KSeF,
haseł, plików `.env`, `poswiadczenia-admina.txt`, NIP-ów kontrahentów ani danych osobowych.
Zamaskuj je w logach przed wysłaniem.

---

## Licencja

CycKSeF jest programem zamkniętym; to repozytorium nie zawiera kodu źródłowego.
Warunki wersji Darmowej: [LICENSE.md](LICENSE.md) (streszczenie) — wiążący jest
regulamin pod <https://cycksef.pl/regulamin/> (§ 8 „Licencja Darmowa").

© Cycrus Dynamics
