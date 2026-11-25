# Privacy Policy / Politika privatnosti za Txtura

[English](#english) | [Srpski](#srpski)

**Last updated / Poslednje ažuriranje:** November 25, 2026 / 25. novembar 2026.  
**Document Version / Verzija dokumenta:** 1.4  
**Application / Aplikacija:** Txtura (Package: `com.txtura`)  
**Copyright Holder / Developer / Programer:** Arpad Vengrin  
**Contact / Kontakt:** vengrinarpad@gmail.com

---

<a id="english"></a>
# Privacy Policy — English

## 1. Introduction

This Privacy Policy explains in detail how the mobile application **Txtura** (hereinafter: "Application", "we", "us") accesses, uses, processes, and protects your data.

Txtura is built around **Privacy by Design**. It operates as a Default SMS and MMS messaging application for Android. **By default, all user data is processed and stored exclusively locally on your device.** The Application operates no developer servers and does not transmit, sell, rent, or share your personal data, messages, contacts, SIM metadata, or media with any third parties. Optional local Wi-Fi Phone ↔ Tablet pairing communicates strictly directly between your own two devices on your local network with end-to-end encryption, and optional cloud backup to your own private Google Drive (`appDataFolder`) is strictly opt-in and end-to-end encrypted on your device before upload.

---

## 2. Data We Process and Permissions Used

To provide reliable SMS/MMS messaging and companion features, Txtura requests specific system permissions on your device.

### 2.1. Permissions Requested on First Launch

1. **SMS and MMS Messages (`READ_SMS`, `SEND_SMS`, `RECEIVE_SMS`, `RECEIVE_MMS`, `RECEIVE_WAP_PUSH`):**
   - **Purpose:** Core functionality of a Default SMS/MMS handler—reading existing message history, receiving incoming messages in real time, composing and sending SMS/MMS messages (including optional carrier MMS read reports when enabled), performing 100% on-device verification code (OTP) recognition and malicious link checks, and updating home screen widgets.
   - **Processing:** Performed 100% locally on your device via Android's official Telephony provider and an encrypted local database. Messages are never uploaded to external servers.

2. **Contacts (`READ_CONTACTS`):**
   - **Purpose:** Displaying contact names, initials, and profile photos instead of raw phone numbers in conversations, contact search, and the Single Contact home screen widget.
   - **Processing:** Read-only local access on your device. Your address book is never uploaded, synced, or exported off-device.

3. **Notifications (`POST_NOTIFICATIONS` on Android 13+):**
   - **Purpose:** Showing local system notifications for incoming SMS/MMS messages, quick replies, and delivery statuses.
   - **Processing:** Local Android `NotificationManager`.

4. **Default SMS App Role (`ROLE_SMS`):**
   - **Purpose:** Required by the Android operating system so Txtura can reliably send, receive, mark read, and manage SMS/MMS messages.

---

### 2.2. On-Demand Permissions (Requested Only When You Trigger the Feature)

1. **Dual SIM / Multi-SIM Support (`READ_PHONE_STATE`):**
   - **Purpose:** Detecting active SIM cards (`SIM 1` / `SIM 2`), displaying SIM badges inside message bubbles, allowing instant 1-tap `SIM 1 ↔ SIM 2` switching in the conversation composer, and choosing a default sending SIM in Settings.
   - **When requested:** Never requested on first launch; requested strictly on-demand if you tap the SIM selector or enable Dual SIM in *Messaging & Network* settings.
   - **Processing:** Read locally via `SubscriptionManager` / `TelephonyManager`. No SIM identifiers, phone numbers, or carrier metadata ever leave your device.

2. **Camera (No `CAMERA` Permission Required):**
   - **Purpose:** Capturing a photo for MMS delegates directly to the system camera app (`ACTION_IMAGE_CAPTURE`), flash alerts use the system torch API (`setTorchMode`), and optional Phone ↔ Tablet QR code scanning uses the zero-permission Google Play Services Code Scanner. Txtura **does not declare or request** the `CAMERA` permission.
   - **Processing:** Captured photos are temporarily cached locally for compression and EXIF metadata removal before MMS sending; scanned pairing QR payloads are processed strictly locally in memory.

3. **Microphone & Voice Recording (`RECORD_AUDIO`):**
   - **Purpose:** Recording voice messages to send as MMS audio attachments.
   - **When requested:** Requested explicitly only when you start recording a voice message. On Android 13+, if Android automatically grants this permission due to `ROLE_SMS`, Txtura proactively revokes it (`revokeSelfPermissionOnKill`) until you explicitly use voice recording.
   - **Processing:** Audio is encoded strictly locally on-device for MMS delivery.

4. **Calendar (`READ_CALENDAR`):**
   - **Purpose:** Selecting and sharing calendar event details inside a message.
   - **When requested:** Only when you explicitly tap "Calendar event" in the attachment menu.

---

### 2.3. Home Screen Widgets, On-Device Link Safety, Optional Local Wi-Fi Pairing, Optional Google Drive Backup & Network Permissions

- **Home Screen Widgets (`Txtura Messages`, `Txtura Unread Messages`, `Txtura Contact`, `Txtura Contacts Grid`) & Selectable App Icons:** Widgets display local conversation summaries, unread counts, and optional downsampled contact photos strictly on your device (excluding locked Hidden Chats threads), adapting transparency via `WallpaperManager.getWallpaperColors(FLAG_SYSTEM)` with **no storage permissions**. Switching between Txtura's 12 selectable home screen app icons updates only local Android launcher component preferences (`<activity-alias>`) when you leave the app, with zero extra permissions and zero off-device communication.
- **On-Device Malicious Link Inspection & Optional Online Verification:** By default, links inside messages are checked **100% locally on your device** using heuristic rules (suspicious TLDs, phishing/typosquatting patterns, punycode, shorteners) with zero network requests. You may optionally enable *"Identify malicious websites in messages"* (disabled by default, requiring explicit consent in an in-app disclosure dialog) to verify extracted URLs/domains over HTTPS via Cloudflare Security DNS (`security.cloudflare-dns.com`) and redirect/TLS inspection; sender numbers, contacts, and full message text are never sent. Verified safe links become clickable hyperlinks that prompt you to either open in your default browser or copy to clipboard.
- **Optional Local Wi-Fi Companion Pairing (Phone ↔ Tablet):** You may optionally pair a SIM-less tablet with your phone over your local Wi-Fi network or mobile hotspot to sync conversations and send SMS, MMS, and voice messages through your phone.
  - **Zero Cloud Servers:** All communication stays strictly within your private local network (enforced by private LAN IP validation) using Android Network Service Discovery (`_txtura._tcp.`), `ACCESS_WIFI_STATE`, and a visible foreground service (`FOREGROUND_SERVICE_REMOTE_MESSAGING`) while active.
  - **Authenticated End-to-End Encryption:** Pairing uses an out-of-band 256-bit pre-shared key exchanged only via QR code (or manual paste), ephemeral ECDH P-256 + HKDF-SHA256 forward-secret session keys, mutual HMAC-SHA256 handshake confirmation, directional **AES-256-GCM** encryption with monotonic packet sequence numbers, single-tablet pinning, and local Android Keystore storage.
- **Optional Google Drive Cloud Backup (`drive.appdata`):** You may optionally connect your own Google account inside *Settings → Data & Storage* to back up and restore your messages and app settings.
  - **Restricted Scope:** Txtura requests only the non-sensitive `https://www.googleapis.com/auth/drive.appdata` scope, which grants access exclusively to a hidden, app-specific folder on your Google Drive. Txtura cannot view, read, modify, or delete any of your other Google Drive files.
  - **End-to-End Encryption:** Every backup uploaded to Google Drive is encrypted client-side on your device using **AES-256-GCM** with salted **PBKDF2-HMAC-SHA256** (100,000 iterations) derived from a backup password you choose. Neither the developer nor Google can decrypt your backup without your password.
  - **Optional Automatic Schedule:** If you enable Daily or Weekly automatic backups (with optional Wi-Fi-only and while-charging constraints via Android `WorkManager`), your chosen backup password is stored strictly locally on your device in `noBackupFilesDir`, encrypted by an **Android Keystore AES-256-GCM** hardware-backed key.
  - **Google API Services User Data Policy (Limited Use):** Txtura's use and transfer of information received from Google APIs strictly adheres to the [Google API Services User Data Policy](https://developers.google.com/terms/api-services-user-data-policy), including the Limited Use requirements. Google Drive access is used solely to store, restore, or delete your own encrypted Txtura backup file upon your explicit request.
- **Network & Foreground Service Permissions (`INTERNET`, `ACCESS_NETWORK_STATE`, `ACCESS_WIFI_STATE`, `CHANGE_NETWORK_STATE`, `FOREGROUND_SERVICE`, `FOREGROUND_SERVICE_REMOTE_MESSAGING`):** Used for carrier MMS data transfer (MMSC APN) and, only if explicitly enabled by you, optional local Wi-Fi phone-to-tablet pairing, optional online URL safety verification, and optional encrypted Google Drive backup/restore. App updates are distributed solely through Google Play. Txtura contains **no analytics, no telemetry, and no ad SDKs**.

---

## 3. Sharing Data with Third Parties

Txtura **does NOT share, sell, or rent** your personal data to any third parties, advertisers, or analytics providers.

- **Mobile Carriers:** When you send an SMS or MMS, message content and recipient phone numbers are transmitted through your mobile carrier's infrastructure as part of standard telecom service under your carrier agreement.
- **External Browser:** If you explicitly tap a verified hyperlink and choose *"Open in default browser"*, Android hands that URL to your chosen web browser app.
- **Zero Tracking SDKs:** Txtura does not include Google Analytics, Firebase Analytics, Facebook SDK, ad networks, or any user-tracking libraries.

---

## 4. Data Security & Protection

1. **Encrypted Local Storage:** Local application data is protected with **SQLCipher (AES-256)** encryption.
2. **Hardened Manifest (`allowBackup="false"`):** Prevents unencrypted ADB or system cloud extraction of sensitive SMS/MMS messages.
3. **Encrypted Local & Google Drive Backups:** Both local backups and optional Google Drive (`appDataFolder`) backups are encrypted client-side with **AES-256-GCM** using salted **PBKDF2-HMAC-SHA256** (100,000 iterations) key derivation from your password. When scheduled automatic Google Drive backups are enabled, the backup password is protected locally with an Android Keystore AES-256-GCM key.
4. **EXIF Metadata Minimization:** Photos sent via MMS automatically have sensitive EXIF metadata (such as GPS coordinates and camera model) stripped before transmission.
5. **Sensitive Clipboard & Biometric Lock:** Copied OTP verification codes are flagged as sensitive (`EXTRA_IS_SENSITIVE` on Android 13+), and optional biometric/PIN lock, salted PBKDF2 Hidden Chats PIN hashing, plus screenshot blocking (`FLAG_SECURE`) protect physical access.

---

## 5. Data Retention & Deletion

- **Full User Control:** Deleting any message or conversation permanently removes it from local app storage once emptied from Trash (with configurable auto-delete retention, default 30 days, plus optional 24-hour OTP auto-cleanup).
- **Local Wi-Fi Pairing Removal:** Unpairing a phone or tablet in *Settings → Pair Phone & Tablet* immediately stops the local foreground service, revokes the pairing keys, and clears synced companion conversations stored on the tablet.
- **Google Drive Backup Deletion:** If you use optional Google Drive Cloud Backup, you can permanently delete your remote encrypted backup file or disconnect Google Drive at any time in *Settings → Data & Storage* (which also cancels scheduled backups and deletes the local Keystore-wrapped password).
- **Uninstallation:** Uninstalling Txtura erases all internal databases, widget bindings, preferences, Keystore-wrapped secrets, and cached files from your device.

---

## 6. Children's Privacy

Txtura is not directed to children under 13 (or the minimum age required in your jurisdiction). Because the Application does not collect personal data on servers, we do not knowingly collect or process children's personal data.

---

## 7. Your Rights (GDPR & Privacy Laws)

Under the GDPR and applicable data protection laws, you have the right to access, modify, delete, and export your data. Because Txtura stores all data exclusively on your device and never on central servers, you exercise all of these rights directly inside the Application (including encrypted local backup export and message deletion).

---

## 8. Changes to This Privacy Policy

We may update this Privacy Policy when new features are introduced or to reflect legal requirements. Updates take effect upon publication in the repository or application with an updated revision date.

---

## 9. Contact

If you have any questions or concerns regarding this Privacy Policy:
- **Email:** vengrinarpad@gmail.com
- **Author:** Arpad Vengrin

---

<a id="srpski"></a>
# Politika privatnosti (Privacy Policy) — Srpski

## 1. Uvod

Ova Politika privatnosti detaljno opisuje kako mobilna aplikacija **Txtura** (u daljem tekstu: „Aplikacija“, „mi“, „nas“) pristupa, koristi, obrađuje i štiti korisničke podatke.

Txtura je dizajnirana sa osnovnim principom privatnosti na prvom mestu. Aplikacija funkcioniše kao podrazumevana aplikacija za obradu tekstualnih (SMS) i multimedijalnih (MMS) poruka na Android operativnom sistemu. **Podrazumevano se svi podaci korisnika obrađuju i čuvaju isključivo lokalno na korisnikovom uređaju.** Aplikacija ne koristi servere programera i ne prenosi, ne prodaje niti deli vaše lične podatke, poruke, kontakte, podatke o SIM karticama ili medije sa trećim stranama. Opciono lokalno povezivanje telefona i tableta preko Wi-Fi mreže odvija se isključivo direktno između vaša dva uređaja uz potpuno šifrovanje, a opciona rezervna kopija na vašem sopstvenom Google Drive nalogu (`appDataFolder`) potpuno je izborna i šifrovana na samom uređaju pre slanja.

---

## 2. Podaci koje obrađujemo i dozvole koje aplikacija koristi

Da bi pružila pouzdanu funkcionalnost slanja i prijema poruka i pratećih opcija, Txtura zahteva pristup određenim sistemskim dozvolama na vašem uređaju.

### 2.1. Dozvole koje se traže pri prvom pokretanju aplikacije

1. **SMS i MMS poruke (`READ_SMS`, `SEND_SMS`, `RECEIVE_SMS`, `RECEIVE_MMS`, `RECEIVE_WAP_PUSH`):**
   - **Svrha:** Osnovna funkcionalnost aplikacije kao podrazumevanog SMS/MMS klijenta – čitanje postojećih razgovora, prijem dolaznih poruka u realnom vremenu, sastavljanje i slanje poruka (uključujući opcione potvrde čitanja za MMS kada su uključene), 100% lokalno prepoznavanje jednokratnih sigurnosnih kodova, lokalna provera bezbednosti internet adresa i prikaz u vidžetima.
   - **Način obrade:** Obrada se vrši 100% lokalno na vašem uređaju preko zvaničnog Android Telephony provajdera i šifrovane lokalne baze. Podaci se nikada ne šalju na spoljne servere.

2. **Kontakti (`READ_CONTACTS`):**
   - **Svrha:** Prikaz imena kontakta, profilne fotografije i inicijala umesto samog broja telefona u listi razgovora, pretrazi kontakata i na vidžetu za pojedinačni kontakt.
   - **Način obrade:** Aplikacija pristupa imeniku isključivo u režimu čitanja na samom uređaju. Vaš imenik se ne otprema, ne sinhronizuje niti izvozi van uređaja.

3. **Obaveštenja (`POST_NOTIFICATIONS` na Android 13+):**
   - **Svrha:** Prikaz lokalnih sistemskih obaveštenja o prispeću novih SMS/MMS poruka, brzih odgovora i statusa dostave.
   - **Način obrade:** Lokalni Android `NotificationManager`.

4. **Status podrazumevane SMS aplikacije (Default SMS App Role):**
   - **Svrha:** Sistemski zahtev Android platforme kako bi aplikacija mogla bezbedno i pouzdano slati, primati i upravljati SMS/MMS porukama.

---

### 2.2. Dozvole na zahtev (traže se isključivo ako korisnik samostalno pokrene radnju)

1. **Podrška za dve ili više SIM kartica (`READ_PHONE_STATE`):**
   - **Svrha:** Detekcija aktivnih SIM kartica (`SIM 1` / `SIM 2`), prikaz SIM oznake na oblačićima poruka, trenutno prebacivanje `SIM 1 ↔ SIM 2` jednim dodirom u polju za unos i izbor podrazumevane kartice za slanje u podešavanjima.
   - **Kada se traži:** Nikada se ne traži pri prvom pokretanju; traži se isključivo na zahtev kada korisnik dodirne SIM selektor ili aktivira Dual SIM u podešavanjima *SMS i MMS mreže*.
   - **Način obrade:** Čita se lokalno preko `SubscriptionManager` / `TelephonyManager`. Nikakvi identifikatori SIM kartice niti podaci operatera ne napuštaju uređaj.

2. **Kamera (bez posebne `CAMERA` dozvole):**
   - **Svrha:** Snimanje fotografije za MMS oslanja se direktno na sistemsku aplikaciju kamere (`ACTION_IMAGE_CAPTURE`), svetlosni odziv koristi sistemsku funkciju za blic (`setTorchMode`), a opciono skeniranje QR koda za povezivanje telefona i tableta koristi ugrađeni skener Google Play usluga bez potrebe za dozvolom kamere. Aplikacija **ne zahteva i ne poseduje** `CAMERA` dozvolu u manifestu.
   - **Način obrade:** Fotografija se privremeno čuva u internoj memoriji aplikacije radi smanjenja veličine i uklanjanja podataka o lokaciji (EXIF) pre slanja kao MMS, dok se očitani QR kod obrađuje isključivo lokalno.

3. **Mikrofon i snimanje zvuka (`RECORD_AUDIO`):**
   - **Svrha:** Snimanje glasovnih poruka radi slanja u formi audio MMS priloga.
   - **Kada se traži:** Tek kada korisnik pokrene snimanje glasovne poruke. Na Android 13+ uređajima, ukoliko sistem automatski dodeli ovu dozvolu zbog uloge `ROLE_SMS`, Txtura je automatski opoziva (`revokeSelfPermissionOnKill`) dok korisnik samostalno ne pokrene snimanje.
   - **Način obrade:** Zvučni zapis se enkodira isključivo lokalno na uređaju.

4. **Kalendar (`READ_CALENDAR`):**
   - **Svrha:** Odabir i deljenje detalja o događaju iz kalendara u poruci.
   - **Kada se traži:** Isključivo na eksplicitni klik na opciju „Događaj iz kalendara“ u meniju priloga.

---

### 2.3. Vidžeti na početnom ekranu, lokalna provera linkova, opciono lokalno Wi-Fi povezivanje, opciona Google Drive kopija i mrežne dozvole

- **Vidžeti na početnom ekranu (*Txtura poruke*, *Txtura nepročitane poruke*, *Txtura kontakt*, *Txtura mreža kontakata*) i izborne ikonice aplikacije:** Vidžeti prikazuju sažetke razgovora, broj nepročitanih poruka i umanjene fotografije kontakata isključivo lokalno na uređaju (automatski isključujući zaključane Sakrivene razgovore), a providnost i kontrast automatski prilagođavaju pozadini početnog ekrana pomoću sistemske funkcije `WallpaperManager.getWallpaperColors(FLAG_SYSTEM)` **bez potrebe za dozvolom pristupa skladištu**. Izbor jedne od 12 ikonica aplikacije menja isključivo lokalnu sistemsku postavku ikonice na početnom ekranu pri izlasku iz aplikacije, bez dodatnih dozvola i bez ikakvog slanja podataka.
- **Lokalna provera bezbednosti linkova i opciona internet provera:** Podrazumevano se svi linkovi u porukama proveravaju **100% lokalno na uređaju** (sumnjivi domeni, obrasci prevara, skraćeni linkovi) bez mrežnih zahteva. Opciono možete uključiti *„Identifikacija zlonamernih veb-sajtova u porukama“* (podrazumevano isključeno, uz obaveznu saglasnost u dijalogu u aplikaciji) radi provere izdvojenih internet adresa putem šifrovane HTTPS veze preko Cloudflare Security DNS-a (`security.cloudflare-dns.com`) i provere preusmerenja; brojevi pošiljalaca, kontakti i ostatak teksta poruke nikada se ne šalju. Bezbedni linkovi nude otvaranje u pregledaču ili kopiranje.
- **Opciono lokalno Wi-Fi povezivanje telefona i tableta:** Opciono možete povezati tablet bez SIM kartice sa svojim telefonom preko iste lokalne Wi-Fi mreže ili pristupne tačke radi sinhronizacije razgovora i slanja SMS, MMS i glasovnih poruka preko telefona.
  - **Bez spoljnih servera:** Celokupna razmena ostaje isključivo u vašoj privatnoj lokalnoj mreži (uz proveru privatnih lokalnih adresa), koristeći lokalno pronalaženje uređaja (`_txtura._tcp.`), dozvolu `ACCESS_WIFI_STATE` i vidljivo trajno obaveštenje aktivne usluge (`FOREGROUND_SERVICE_REMOTE_MESSAGING`) dok su uređaji povezani.
  - **Potpuno šifrovana veza:** Povezivanje koristi 256-bitni ključ razmenjen isključivo putem QR koda (ili ručnog unosa), privremenu razmenu ključeva ECDH P-256 i HKDF-SHA256, uzajamnu HMAC-SHA256 potvrdu, zasebne AES-256-GCM ključeve po smeru sa brojačima paketa, vezivanje za jedan tablet i čuvanje u sistemskom Android Keystore skladištu.
- **Opciona Google Drive rezervna kopija (`drive.appdata`):** Opciono možete povezati svoj Google nalog u *Podešavanja → Podaci i skladište* radi čuvanja i vraćanja poruka i podešavanja.
  - **Ograničeni pristup:** Txtura traži isključivo neosetljivi opseg `https://www.googleapis.com/auth/drive.appdata`, koji daje pristup samo skrivenoj fascikli rezervisanoj za aplikaciju. Txtura ne može da vidi, čita, menja niti briše bilo koje druge datoteke na vašem Google Drive nalogu.
  - **Šifrovanje na samom uređaju:** Svaka kopija pre slanja na Google Drive šifruje se lokalno na vašem uređaju pomoću **AES-256-GCM** šifrovanja sa **PBKDF2-HMAC-SHA256** (100.000 iteracija) izvođenjem ključa iz lozinke koju sami odaberete. Ni programer ni Google ne mogu dešifrovati sadržaj kopije bez vaše lozinke.
  - **Opcioni automatski raspored:** Ako uključite dnevno ili nedeljno automatsko čuvanje (sa opcijama samo preko Wi-Fi mreže i samo dok se uređaj puni preko Android `WorkManager`-a), vaša lozinka kopije čuva se isključivo lokalno u `noBackupFilesDir`, šifrovana **Android Keystore AES-256-GCM** ključem.
  - **Pravila ograničene upotrebe Google API usluga (Limited Use):** Korišćenje informacija dobijenih putem Google API-ja u aplikaciji Txtura u potpunosti je usklađeno sa [Google API Services User Data Policy](https://developers.google.com/terms/api-services-user-data-policy), uključujući zahteve ograničene upotrebe. Pristup Google Drive nalogu koristi se isključivo za čuvanje, vraćanje ili brisanje vaše šifrovane Txtura kopije na vaš zahtev.
- **Mrežne dozvole i usluge u prvom planu (`INTERNET`, `ACCESS_NETWORK_STATE`, `ACCESS_WIFI_STATE`, `CHANGE_NETWORK_STATE`, `FOREGROUND_SERVICE`, `FOREGROUND_SERVICE_REMOTE_MESSAGING`):** Koriste se za mobilni prenos podataka prilikom slanja i prijema MMS poruka preko operatera i, isključivo ako korisnik to izričito uključi, za lokalno Wi-Fi povezivanje telefona i tableta, opcionu internet proveru linkova i opcionu šifrovanu Google Drive rezervnu kopiju. Ažuriranja aplikacije se isporučuju isključivo putem Google Play prodavnice. Aplikacija **ne sadrži analitiku, praćenje niti reklamne dodatke**.

---

## 3. Deljenje podataka sa trećim stranama

Txtura **NE deli, NE prodaje i NE iznajmljuje** vaše lične podatke bilo kojim trećim licima, marketinškim agencijama ili analitičkim kompanijama.

- **Telekomunikacioni operateri:** Prilikom slanja SMS i MMS poruka, sadržaj poruke i broj primaoca prenose se putem infrastrukture vašeg mobilnog operatera u skladu sa vašim ugovorom sa operaterom.
- **Podrazumevani pregledač:** Ako eksplicitno dodirnete bezbedan hiperlink u poruci i izaberete *„Otvori u pregledaču“*, Android prosleđuje tu adresu vašem podrazumevanom veb pregledaču.
- **Bez SDK-ova za praćenje:** Aplikacija ne koristi Google Analytics, Firebase Analytics, Facebook SDK niti bilo koje alate za praćenje korisnika.

---

## 4. Zaštita i bezbednost podataka

1. **Šifrovana lokalna baza podataka:** Osetljivi lokalni podaci zaštićeni su pomoću **SQLCipher (AES-256)** enkripcije.
2. **Hardened manifest (`allowBackup="false"`):** Sprečava neovlašćeno izvlačenje SMS/MMS poruka preko ADB-a ili neenkriptovanih sistemskih rezervnih kopija.
3. **Kriptografska zaštita lokalnih i Google Drive rezervnih kopija:** I lokalne i opcione Google Drive (`appDataFolder`) rezervne kopije šifrovane su na samom uređaju pomoću **AES-256-GCM** enkripcije sa **PBKDF2-HMAC-SHA256** (100.000 iteracija) izvođenjem ključa iz vaše lozinke. Kada je uključen automatski Google Drive bekap, lozinka kopije se lokalno štiti Android Keystore AES-256-GCM ključem.
4. **Uklanjanje metapodataka (EXIF):** Prilikom slanja MMS fotografija automatski se uklanjaju osetljivi EXIF podaci (GPS koordinate, model kamere).
5. **Zaštita kopiranih kodova i biometrijsko zaključavanje:** Kopirani verifikacioni kodovi označavaju se kao osetljivi sadržaj (`EXTRA_IS_SENSITIVE` na Android 13+), a aplikacija podržava biometrijsko zaključavanje, PBKDF2 heširanje PIN-a za Skrivene razgovore i zaštitu od skrinšotovanja (`FLAG_SECURE`).

---

## 5. Zadržavanje i brisanje podataka

- **Potpuna kontrola korisnika:** Brisanjem poruke ili razgovora podaci se trajno uklanjaju iz lokalne baze nakon isteka perioda zadržavanja u korpi (podrazumevano 30 dana, uz opciono automatsko brisanje jednokratnih kodova posle 24 sata) ili odmah pražnjenjem korpe.
- **Prekid lokalnog Wi-Fi uparivanja:** Prekidanjem uparivanja u *Podešavanja → Povezivanje telefona i tableta* odmah se zaustavlja lokalna usluga, brišu ključevi povezivanja i uklanjaju sinhronizovani razgovori sa tableta.
- **Brisanje Google Drive kopije:** Ako koristite opcionu Google Drive rezervnu kopiju, u svakom trenutku možete trajno obrisati šifrovanu datoteku sa Google Drive naloga ili prekinuti vezu u *Podešavanja → Podaci i skladište* (čime se otkazuju i zakazane kopije i briše lokalno šifrovana lozinka).
- **Deinstalacija:** Deinstaliranjem aplikacije sa uređaja brišu se sve interne baze, podešavanja vidžeta, konfiguracioni fajlovi, Keystore tajne i keš.

---

## 6. Privatnost dece (Children's Privacy)

Aplikacija Txtura nije namenjena deci mlađoj od 13 godina (niti mlađoj od uzrasta propisanog u vašoj jurisdikciji). Pošto aplikacija ne prikuplja lične podatke na serverima, svesno ne prikupljamo niti obrađujemo podatke o maloletnim licima.

---

## 7. Prava korisnika (GDPR i zakoni o zaštiti podataka o ličnosti)

U skladu sa GDPR-om i važećim zakonima o zaštiti podataka o ličnosti, imate pravo na pristup, izmenu, brisanje i prenosivost svojih podataka. Pošto Txtura čuva podatke isključivo na vašem uređaju, sva ova prava ostvarujete direktno u samoj aplikaciji (uključujući šifrovani izvoz bekapa i brisanje poruka).

---

## 8. Izmene ove Politike privatnosti

Zadržavamo pravo ažuriranja ove Politike privatnosti pri dodavanju novih funkcionalnosti ili usklađivanju sa propisima. Svaka izmena stupa na snagu objavljivanjem sa ažuriranim datumom revizije.

---

## 9. Kontakt

Ukoliko imate pitanja ili sugestije u vezi sa ovom Politikom privatnosti:
- **E-pošta:** vengrinarpad@gmail.com
- **Autor:** Arpad Vengrin
