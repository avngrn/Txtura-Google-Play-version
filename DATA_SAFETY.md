# Data Safety Form (Google Play) / Obrazac o bezbednosti podataka za Txtura

[English](#english) | [Srpski](#srpski)

**Last updated / Poslednje ažuriranje:** November 23, 2026 / 23. novembar 2026.  
**Document Version / Verzija dokumenta:** 1.2  
**Application / Aplikacija:** Txtura (`com.txtura`)

---

<a id="english"></a>
# Data Safety Form (Google Play) — English

This document provides accurate answers and justifications for completing the **Data safety** section in Google Play Console for the **Txtura** (`com.txtura`) Android application.

---

## Overview

| Google Play Console Question | Answer | Explanation |
| :--- | :---: | :--- |
| **Does your app collect or share any of the required user data types?** | **Yes** *(Optional features only)* | By default, Txtura processes all SMS, MMS, contacts, photos, audio, and calendar data **100% locally on-device** with zero off-device transmission. Two strictly optional, disabled-by-default features transmit user-initiated data over HTTPS: (1) *"Identify malicious websites in messages"* (gated behind an in-app Prominent Disclosure dialog) sends extracted URLs/domains to Cloudflare Security DNS (`security.cloudflare-dns.com`) and destination servers for ephemeral phishing/malware inspection; and (2) *"Google Drive Cloud Backup"* (gated behind explicit Google OAuth 2.0 consent for the restricted `drive.appdata` scope) uploads a client-side **AES-256-GCM + PBKDF2** encrypted backup directly to the user's own private Google Drive app folder. The developer operates no backend servers and never receives or can decrypt this data. |
| **Is all of the user data collected by your app encrypted in transit?** | **Yes** | All outbound network requests (carrier MMSC communication, optional HTTPS URL verification, and optional Google Drive API v3 `appDataFolder` backup uploads/downloads) use encrypted HTTPS/TLS channels; Google Drive backups are additionally end-to-end encrypted with AES-256-GCM before upload. |
| **Do you provide a way for users to request that their data is deleted?** | **No developer account / In-app deletion** | Txtura has no developer user accounts and retains zero server-side data. Optional URL verification is processed strictly ephemerally without storage. Users can permanently delete their encrypted Google Drive backup file and disconnect Google Drive directly in *Settings → Data & Storage*, and can delete all local messages, trash history, and widget data in-app or by uninstalling. |

---

## 1. Detailed Data Breakdown by Category

If declaring categories corresponding to permissions used by a Default SMS Handler:

### A. Messages
* **Data type:** SMS or MMS messages
* **Collected?** **No** by default – Processed 100% locally on the user's device (including on-device verification code detection, on-device malicious link heuristics, and home screen widget summaries). Only if the user explicitly opts in to:
  1. *"Identify malicious websites in messages"* (disabled by default, gated behind an in-app Prominent Disclosure dialog) — only extracted URLs/domains are sent over HTTPS for ephemeral malware/phishing verification (*Fraud prevention, security, and compliance*, not linked to user identity, never stored by developer); or
  2. *"Google Drive Cloud Backup"* (disabled by default, requires explicit Google OAuth 2.0 `drive.appdata` authorization) — an end-to-end **AES-256-GCM + PBKDF2** password-encrypted backup archive of messages and app settings is uploaded directly to the user's own hidden Google Drive `appDataFolder` (*App functionality*; developer has zero access to the file or encryption password).
* **Shared?** **No** – Never shared with third parties for advertising or analytics (standard carrier SMS/MMS delivery and user-directed encrypted storage in the user's own Google Drive account are excluded from third-party sharing under Play policy).
* **Processed ephemerally?** **Yes** – For sending, receiving, displaying messages, and optional opt-in URL safety checks (user-initiated encrypted Google Drive backups remain in the user's own Drive until the user deletes them in-app).
* **Purpose:** **App functionality** and optional **Fraud prevention, security, and compliance**.

### B. Personal Info
* **Data type:** Contacts (names, phone numbers, and contact photos)
* **Collected?** **No** – Read-only on-device access to display sender names and profile photos inside conversations and on the Single Contact home screen widget.
* **Shared?** **No**.
* **Purpose:** **App functionality**.

### C. Photos and Videos
* **Data type:** Photos
* **Collected?** **No** – Processed locally only when the user explicitly selects or captures a photo to send via MMS (EXIF metadata such as GPS coordinates is automatically stripped prior to sending).
* **Shared?** **No**.
* **Purpose:** **App functionality**.

### D. Audio Files
* **Data type:** Voice or sound recordings
* **Collected?** **No** – Recorded locally strictly on user request to send as an MMS voice message attachment.
* **Shared?** **No**.
* **Purpose:** **App functionality**.

### E. Calendar
* **Data type:** Calendar events
* **Collected?** **No** – Accessed locally only when the user explicitly chooses to share a calendar event in a message.
* **Shared?** **No**.
* **Purpose:** **App functionality**.

### F. Device or Other IDs / SIM Slot State
* **Data type:** Active SIM slot metadata (`SubscriptionManager` display name, carrier label, slot index)
* **Collected?** **No** – Accessed strictly on-demand when the user enables Dual SIM / Multi-SIM support, strictly on-device for choosing `SIM 1` / `SIM 2` when sending and labeling message bubbles. Never logged, exported, or transmitted off-device.
* **Shared?** **No**.
* **Purpose:** **App functionality** (Dual SIM / Multi-SIM messaging).

---

## 2. Security Practices

* **Encryption in transit:** All external connections (carrier MMSC transactions, optional HTTPS URL verification, and optional Google Drive API v3 backup transfers) use secure TLS/HTTPS channels. App updates are distributed exclusively via Google Play.
* **Encryption at rest & end-to-end cloud backup:** Local sensitive databases are protected with **SQLCipher (AES-256)**, and both local and optional Google Drive (`appDataFolder`) backup files are encrypted client-side with **AES-256-GCM** using salted **PBKDF2-HMAC-SHA256** (100,000 iterations) key derivation. For optional scheduled background Google Drive backups (`WorkManager`), the user's backup password is stored strictly locally in `noBackupFilesDir` wrapped by an **Android Keystore AES-256-GCM** key.
* **On-device link & OTP safety checks (optional online verification):** By default, all malicious link heuristics, safe hyperlink verification, and numeric/alphanumeric verification code (OTP) parsing happen strictly locally on the device with zero external requests. Users may optionally opt in (disabled by default, gated behind an explicit in-app disclosure dialog) to verify extracted URLs over HTTPS via Cloudflare Security DNS (`security.cloudflare-dns.com`) and redirect/TLS inspection.
* **Zero-permission wallpaper adaptation for widgets:** Home screen widgets read system wallpaper color hints via `WallpaperManager.getWallpaperColors(FLAG_SYSTEM)` strictly locally to automatically adapt transparency and contrast without requiring storage permissions.
* **Data deletion mechanism:** Users have complete control—deleting any message or thread removes it permanently after trash retention (or immediately when emptied), the remote encrypted Google Drive copy can be deleted or disconnected in one tap inside *Settings → Data & Storage*, and uninstalling Txtura erases all internal databases, widget states, Keystore-wrapped secrets, and caches.
* **No unencrypted cloud backup leakage:** `android:allowBackup="false"` is enforced in the manifest to prevent automatic unencrypted ADB or system cloud extraction of SMS/MMS data.

---

## 3. Permissions Declaration & Justification Matrix

| Permission | Request Timing | Play Policy Justification |
| :--- | :---: | :--- |
| `android.permission.READ_SMS` | First launch | **Core feature:** Required for Default SMS App to read and display SMS/MMS message history and widgets. |
| `android.permission.SEND_SMS` | First launch | **Core feature:** Required to send SMS messages requested by the user (including Dual SIM routing). |
| `android.permission.RECEIVE_SMS` | First launch | **Core feature:** Real-time reception of incoming SMS messages. |
| `android.permission.RECEIVE_MMS` | First launch | **Core feature:** Real-time reception of incoming MMS multimedia messages. |
| `android.permission.RECEIVE_WAP_PUSH` | First launch | **Core feature:** Receiving carrier MMS WAP push notifications. |
| `android.permission.READ_CONTACTS` | First launch | **Core UX:** Resolving contact names and photos for conversations and the Single Contact home screen widget. |
| `android.permission.POST_NOTIFICATIONS` | First launch (Android 13+) | **Notifications:** Displaying system notifications for incoming messages and delivery alerts. |
| `android.permission.READ_PHONE_STATE` | **On-demand only** | **Dual SIM / Multi-SIM:** Never requested on startup; requested only when the user enables Dual SIM to detect active SIM cards (`SIM 1` / `SIM 2`), display SIM badges on bubbles, and switch sending SIM with one tap. |
| `android.permission.RECORD_AUDIO` | **On-demand only** | Recording MMS voice messages strictly when initiated by the user (automatically revoked via `revokeSelfPermissionOnKill` on Android 13+ if auto-granted by `ROLE_SMS`). |
| `android.permission.READ_CALENDAR` | **On-demand only** | Sharing a calendar event inside a message strictly when tapped in the attachment menu. |
| `android.permission.USE_BIOMETRIC` | Optional | Local biometric/PIN app lock to protect physical privacy. |
| `android.permission.INTERNET` | System | Sending/receiving MMS via carrier MMSC APN, optional user-initiated URL safety verification, and optional end-to-end encrypted Google Drive (`appDataFolder`) backup/restore. |
| `android.permission.RECEIVE_BOOT_COMPLETED` | System | Restoring scheduled messages, optional Google Drive periodic backup schedule, and message/widget sync after device reboot. |

---

<a id="srpski"></a>
# Obrazac o bezbednosti podataka (Google Play Data Safety Form) — Srpski

Ovaj dokument sadrži precizne odgovore i objašnjenja za popunjavanje sekcije **Data safety (Bezbednost podataka)** u Google Play Konzoli (Google Play Console) za aplikaciju **Txtura** (`com.txtura`).

---

## Pregled (Data Safety Overview)

| Pitanje u Google Play konzoli | Odgovor | Objašnjenje |
| :--- | :---: | :--- |
| **Da li vaša aplikacija prikuplja ili deli bilo koje zahtevane tipove korisničkih podataka?** *(Does your app collect or share any of the required user data types?)* | **DA (Yes)** *(Samo za opcione funkcije)* | Podrazumevano, Txtura obrađuje sve SMS, MMS, kontakte, fotografije, audio i kalendarske podatke **100% lokalno na uređaju** bez slanja van uređaja. Dve strogo opcione, podrazumevano isključene funkcije koriste HTTPS vezu tek na zahtev korisnika: (1) *„Identifikacija zlonamernih veb-sajtova u porukama“* (uz dijalog za saglasnost u aplikaciji) šalje izdvojene URL-ove/domene na Cloudflare Security DNS (`security.cloudflare-dns.com`) i odredišne servere radi prolazne phishing/malware provere; i (2) *„Google Drive rezervna kopija“* (uz izričitu Google OAuth 2.0 dozvolu za ograničeni opseg `drive.appdata`) šalje **AES-256-GCM + PBKDF2** šifrovanu kopiju direktno u skrivenu fasciklu na korisnikovom sopstvenom Google Drive nalogu. Programer nema servere i nikada nema pristup podacima niti lozinci šifrovanja. |
| **Da li se svi podaci koje aplikacija prikuplja prenose preko šifrovane veze (in transit)?** | **DA (Yes)** | Sva odlazna mrežna komunikacija (MMSC preko operatera, opciona HTTPS provera linkova i opciona Google Drive API v3 sinhronizacija bekapa) zaštićena je TLS/HTTPS protokolom, a Google Drive kopije su dodatno end-to-end šifrovane pomoću AES-256-GCM pre slanja. |
| **Da li omogućavate korisnicima da zatraže brisanje svojih podataka?** | **Nema naloga programera / Brisanje u aplikaciji** | Txtura nema naloge na serverima programera i ne čuva podatke na eksternim serverima. Opciona provera URL-ova je isključivo prolazna (ephemeral). Korisnik u svakom trenutku može jednim dodirom da trajno obriše svoju šifrovanu Google Drive kopiju i prekine vezu sa Drive-om u *Podešavanja → Podaci i skladište*, kao i da obriše sve lokalne poruke, korpu i podešavanja u aplikaciji ili deinstalacijom. |

---

## 1. Detaljan pregled podataka po kategorijama u Google Play formularu

Ako izaberete deklarisanje stavki na osnovu dozvola koje aplikacija koristi kao podrazumevani SMS klijent:

### A. Poruke (Messages)
* **Tip podatka:** SMS ili MMS poruke
* **Da li se podaci prikupljaju?** **NE (No)** podrazumevano – Podaci se obrađuju 100% lokalno na uređaju (uključujući prepoznavanje verifikacionih kodova, lokalnu heurističku proveru linkova i prikaz u vidžetima). Isključivo ukoliko korisnik izričito uključi:
  1. opcionu, podrazumevano isključenu opciju *„Identifikacija zlonamernih veb-sajtova u porukama“* (uz dijalog za pristanak u aplikaciji) — izdvojeni URL-ovi/domeni se prolazno proveravaju putem HTTPS-a u svrhu zaštite od prevara i bezbednosti (nisu povezani sa identitetom korisnika i programer ih ne skladišti); ili
  2. opcionu funkciju *„Google Drive rezervna kopija“* (podrazumevano isključeno, uz Google OAuth 2.0 dozvolu za ograničeni opseg `drive.appdata`) — šifrovana arhiva (**AES-256-GCM + PBKDF2**) poruka i podešavanja čuva se direktno u korisnikovoj privatnoj skrivenoj fascikli na sopstvenom Google Drive nalogu (*Funkcionalnost aplikacije*; programer nema pristup fajlu ni lozinci).
* **Da li se podaci dele?** **NE (No)** – Ne dele se sa trećim stranama u svrhe oglašavanja ili analitike (telekomunikacioni prenos poruka putem operatera i šifrovano čuvanje na sopstvenom Google Drive nalogu korisnika ne spadaju u deljenje sa trećim licima u smislu Data Safety pravila).
* **Da li se obrađuju prolazno (ephemeral processing)?** **DA (Yes)** – Za slanje, prijem, prikaz i opcionu opt-in proveru bezbednosti linkova (korisnički inicirane šifrovane Google Drive kopije ostaju na korisnikovom Drive-u dok ih sam ne obriše iz aplikacije).
* **Svrha:** **Funkcionalnost aplikacije (App functionality)** i opciono **Sprečavanje prevara, bezbednost i usklađenost (Fraud prevention, security, and compliance)**.

### B. Lični podaci (Personal info)
* **Tip podatka:** Kontakti (imena, brojevi telefona i fotografije kontakata)
* **Da li se podaci prikupljaju?** **NE (No)** – Pristup u režimu čitanja isključivo na uređaju radi prepoznavanja imena i fotografija pošiljaoca u razgovorima i na vidžetu za pojedinačni kontakt.
* **Da li se dele?** **NE (No)**.
* **Svrha:** **Funkcionalnost aplikacije (App functionality)**.

### C. Fotografije i video snimci (Photos and videos)
* **Tip podatka:** Fotografije (Photos)
* **Da li se podaci prikupljaju?** **NE (No)** – Obrađuju se lokalno samo kada korisnik priloži sliku u MMS (EXIF metapodaci poput GPS lokacije automatski se uklanjaju pre slanja).
* **Da li se dele?** **NE (No)**.
* **Svrha:** **Funkcionalnost aplikacije (App functionality)**.

### D. Audio fajlovi (Audio files)
* **Tip podatka:** Glasovni snimci (Voice or sound recordings)
* **Da li se podaci prikupljaju?** **NE (No)** – Snimanje glasa na zahtev korisnika za slanje kao MMS audio prilog.
* **Da li se dele?** **NE (No)**.
* **Svrha:** **Funkcionalnost aplikacije (App functionality)**.

### E. Kalendar (Calendar)
* **Tip podatka:** Kalendarski događaji (Calendar events)
* **Da li se podaci prikupljaju?** **NE (No)** – Koristi se lokalno samo kada korisnik odabere da podeli događaj u poruci.
* **Da li se dele?** **NE (No)**.
* **Svrha:** **Funkcionalnost aplikacije (App functionality)**.

### F. Stanje SIM slotova (Dual SIM / Multi-SIM)
* **Tip podatka:** Osnovni podaci o aktivnim SIM slotovima (`SubscriptionManager` – naziv operatera, broj slota)
* **Da li se podaci prikupljaju?** **NE (No)** – Pristupa se isključivo na zahtev korisnika i isključivo lokalno radi prikaza `SIM 1` / `SIM 2` oznaka i izbora kartice za slanje poruke. Nikada se ne šalje van uređaja.
* **Da li se dele?** **NE (No)**.
* **Svrha:** **Funkcionalnost aplikacije (App functionality)**.

---

## 2. Bezbednosne prakse (Security Practices)

* **Enkripcija u prenosu (Encryption in transit):** Sva komunikacija koja ide van uređaja (APN MMSC mrežne transakcije, opciona HTTPS provera linkova i opciona Google Drive API v3 sinhronizacija bekapa) koristi bezbedne šifrovane kanale (HTTPS/TLS). Ažuriranja aplikacije se isporučuju isključivo putem Google Play prodavnice.
* **Enkripcija u mirovanju i end-to-end cloud bekap:** Lokalna baza podataka zaštićena je putem **SQLCipher (AES-256)** enkripcije, dok su i lokalni i opcioni Google Drive (`appDataFolder`) bekap fajlovi šifrovani na samom uređaju industrijskim standardom **AES-256-GCM** uz **PBKDF2-HMAC-SHA256** (100.000 iteracija) izvođenje ključa sa solju. Za opcioni automatski zakazani Google Drive bekap (`WorkManager`), korisnička lozinka bekapa čuva se isključivo lokalno u `noBackupFilesDir`, dodatno šifrovana **Android Keystore AES-256-GCM** ključem.
* **Lokalna provera linkova i verifikacionih kodova (opciona online provera):** Podrazumevano se provera bezbednosti linkova i prepoznavanje numeričkih i alfanumeričkih verifikacionih kodova (OTP) vrše isključivo lokalno na uređaju bez spoljnih zahteva. Korisnik može opciono da uključi (podrazumevano isključeno, uz prethodni dijalog za saglasnost u aplikaciji) online proveru izdvojenih URL-ova putem HTTPS-a preko Cloudflare Security DNS-a (`security.cloudflare-dns.com`) i provere preusmerenja/TLS sertifikata.
* **Automatska transparentnost vidžeta bez dozvole za skladište:** Vidžeti na početnom ekranu čitaju isključivo sistemske informacije o dominantnim bojama pozadine (`WallpaperManager.getWallpaperColors`) lokalno na uređaju radi automatskog prilagođavanja providnosti i kontrasta.
* **Mehanizam brisanja podataka (Data deletion mechanism):** Korisnik ima potpunu kontrolu – brisanjem pojedinačne poruke ili razgovora podaci se nepovratno brišu sa uređaja, šifrovanu Google Drive kopiju korisnik može obrisati ili prekinuti vezu jednim dodirom u *Podešavanja → Podaci i skladište*, a deinstalacija aplikacije uklanja sve interne baze, podatke vidžeta, Keystore tajne i keš.
* **Nema nezaštićenog bekapa u oblaku:** Aplikacija koristi `android:allowBackup="false"` kako bi sprečila automatsko neenkriptovano izvlačenje osetljivih poruka preko ADB-a ili sistemskog bekapa.

---

## 3. Matrica dozvola (Permissions Declaration & Justification)

| Dozvola (Permission) | Tip traženja | Opravdanje prema Google Play politici (Justification) |
| :--- | :---: | :--- |
| `android.permission.READ_SMS` | Prvi start | **Osnovna funkcionalnost (Core feature):** Neophodno za Default SMS aplikaciju radi čitanja i prikaza istorije poruka i vidžeta. |
| `android.permission.SEND_SMS` | Prvi start | **Osnovna funkcionalnost:** Slanje SMS poruka na zahtev korisnika (uključujući izbor SIM kartice). |
| `android.permission.RECEIVE_SMS` | Prvi start | **Osnovna funkcionalnost:** Prijem dolaznih SMS poruka u realnom vremenu. |
| `android.permission.RECEIVE_MMS` | Prvi start | **Osnovna funkcionalnost:** Prijem dolaznih multimedijalnih poruka. |
| `android.permission.RECEIVE_WAP_PUSH` | Prvi start | **Osnovna funkcionalnost:** Prijem MMS WAP push notifikacija preko mobilne mreže. |
| `android.permission.READ_CONTACTS` | Prvi start | **Korisničko iskustvo:** Prikaz imena i profilnih slika kontakata u razgovorima i na vidžetu za pojedinačni kontakt. |
| `android.permission.POST_NOTIFICATIONS` | Prvi start (Android 13+) | **Obaveštenja:** Prikaz sistemskih obaveštenja o novim porukama i statusu dostave. |
| `android.permission.READ_PHONE_STATE` | **Na zahtev (On-demand)** | **Dual SIM / Multi-SIM:** Ne traži se pri prvom pokretanju; traži se isključivo kada korisnik aktivira podršku za dve SIM kartice radi detekcije `SIM 1` / `SIM 2` kartica, prikaza oznaka na porukama i promene kartice za slanje jednim dodirom. |
| `android.permission.RECORD_AUDIO` | **Na zahtev (On-demand)** | Snimanje glasovne poruke za slanje (traži se eksplicitno tek kada korisnik pokrene snimanje; automatski opoziv na Android 13+ ako sistem dodeli preko `ROLE_SMS`). |
| `android.permission.READ_CALENDAR` | **Na zahtev (On-demand)** | Deljenje događaja iz kalendara u poruci (traži se samo na klik u meniju priloga). |
| `android.permission.USE_BIOMETRIC` | Opciono | Lokalno zaključavanje aplikacije otiskom prsta/licem radi zaštite privatnosti. |
| `android.permission.INTERNET` | Sistemski | Prijem i slanje MMS sadržaja putem MMSC APN servera mobilnog operatera, opciona provera linkova na zahtev korisnika i opcioni end-to-end šifrovani Google Drive (`appDataFolder`) bekap i vraćanje. |
| `android.permission.RECEIVE_BOOT_COMPLETED` | Sistemski | Ponovno pokretanje zakazanih poruka, opcionog rasporeda automatskog Google Drive bekapa i osvežavanja vidžeta nakon restarta uređaja. |
