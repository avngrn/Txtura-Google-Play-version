# Data Safety Form (Google Play) / Obrazac o bezbednosti podataka za Txtura

[English](#english) | [Srpski](#srpski)

**Last updated / Poslednje ažuriranje:** November 19, 2026 / 19. novembar 2026.  
**Document Version / Verzija dokumenta:** 1.1  
**Application / Aplikacija:** Txtura (`com.txtura`)

---

<a id="english"></a>
# Data Safety Form (Google Play) — English

This document provides accurate answers and justifications for completing the **Data safety** section in Google Play Console for the **Txtura** (`com.txtura`) Android application.

---

## Overview

| Google Play Console Question | Answer | Explanation |
| :--- | :---: | :--- |
| **Does your app collect or share any user data?** | **No** | Under official Google Play Data Safety guidelines, data processed **strictly locally on-device** and never transmitted to external developer servers or third parties is **not considered collection or sharing**. |
| **Is all of the user data collected by your app encrypted in transit?** | **Yes** | Any outbound network request (carrier MMSC communication and optional GitHub release version checks) uses encrypted HTTPS/TLS channels. |
| **Do you provide a way for users to request that their data is deleted?** | **Yes** | Users can directly delete individual messages, conversations, trash history, widget bindings, or uninstall the app to remove all locally stored data. |

---

## 1. Detailed Data Breakdown by Category

If declaring categories corresponding to permissions used by a Default SMS Handler:

### A. Messages
* **Data type:** SMS or MMS messages
* **Collected?** **No** – Processed 100% locally on the user's device (including on-device verification code detection, on-device malicious link inspection, and home screen widget summaries).
* **Shared?** **No** – Never shared with third parties (standard carrier SMS/MMS delivery is excluded from third-party sharing under Play policy).
* **Processed ephemerally?** **Yes** – For sending, receiving, and displaying messages.
* **Purpose:** **App functionality** (Default SMS/MMS handler, home screen widgets, on-device security checks).

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

* **Encryption in transit:** All external connections (carrier MMSC transactions and optional app update checks) use secure TLS/HTTPS channels.
* **Encryption at rest:** Local sensitive databases are protected with **SQLCipher (AES-256)**, and user-created local backup files are encrypted with **AES-256-GCM** using salted **PBKDF2** key derivation.
* **100% On-device link & OTP safety checks:** All malicious link detection, safe hyperlink verification, and numeric/alphanumeric verification code (OTP) parsing happen strictly locally on the device without querying external servers.
* **Zero-permission wallpaper adaptation for widgets:** Home screen widgets read system wallpaper color hints via `WallpaperManager.getWallpaperColors(FLAG_SYSTEM)` strictly locally to automatically adapt transparency and contrast without requiring storage permissions.
* **Data deletion mechanism:** Users have complete local control—deleting any message or thread removes it permanently after trash retention (or immediately when emptied), and uninstalling Txtura erases all internal databases, widget states, and caches.
* **No unencrypted cloud backup leakage:** `android:allowBackup="false"` is enforced in the manifest to prevent automatic unencrypted ADB or cloud extraction of SMS/MMS data.

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
| `android.permission.INTERNET` | System | Sending/receiving MMS via carrier MMSC APN and optional GitHub release version check. |
| `android.permission.RECEIVE_BOOT_COMPLETED` | System | Restoring scheduled messages and message/widget sync after device reboot. |

---

<a id="srpski"></a>
# Obrazac o bezbednosti podataka (Google Play Data Safety Form) — Srpski

Ovaj dokument sadrži precizne odgovore i objašnjenja za popunjavanje sekcije **Data safety (Bezbednost podataka)** u Google Play Konzoli (Google Play Console) za aplikaciju **Txtura** (`com.txtura`).

---

## Pregled (Data Safety Overview)

| Pitanje u Google Play konzoli | Odgovor | Objašnjenje |
| :--- | :---: | :--- |
| **Da li vaša aplikacija prikuplja ili deli bilo koje korisničke podatke?** *(Does your app collect or share any user data?)* | **NE (No)** | Po zvaničnim Google Play smernicama, podaci koji se obrađuju **isključivo lokalno na uređaju (on-device processing)** i ne prenose se na eksterne servere niti trećim stranama **ne smatraju se prikupljanjem (Collection)**. |
| **Da li se svi podaci koje aplikacija prikuplja prenose preko šifrovane veze (in transit)?** | **DA (Yes)** | Sva eventualna HTTPS mrežna komunikacija (provera ažuriranja i MMSC komunikacija) zaštićena je TLS/HTTPS protokolom. |
| **Da li omogućavate korisnicima da zatraže brisanje svojih podataka?** | **DA (Yes)** | Korisnik samostalno briše sve poruke, razgovore, podešavanja vidžeta i podatke direktno u aplikaciji, ili deinstalacijom aplikacije. |

---

## 1. Detaljan pregled podataka po kategorijama u Google Play formularu

Ako izaberete deklarisanje stavki na osnovu dozvola koje aplikacija koristi kao podrazumevani SMS klijent:

### A. Poruke (Messages)
* **Tip podatka:** SMS ili MMS poruke
* **Da li se podaci prikupljaju?** **NE (No)** – Podaci se obrađuju 100% lokalno na uređaju (uključujući prepoznavanje verifikacionih kodova, proveru bezbednosti linkova i prikaz u vidžetima na početnom ekranu).
* **Da li se podaci dele?** **NE (No)** – Ne dele se sa trećim stranama (telekomunikacioni prenos poruka putem operatera ne spada u treća lica u smislu Data Safety podele).
* **Da li se obrađuju prolazno (ephemeral processing)?** **DA (Yes)** – Za slanje, prijem i prikaz.
* **Svrha:** **Funkcionalnost aplikacije (App functionality)** – Osnovna svrha aplikacije (podrazumevani SMS/MMS rukovalac, vidžeti i lokalna bezbednosna provera).

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

* **Enkripcija u prenosu (Encryption in transit):** Sva komunikacija koja ide van uređaja (APN MMSC mrežne transakcije i opciona provera verzije) koristi bezbedne šifrovane kanale (HTTPS/TLS).
* **Enkripcija u mirovanju (Encryption at rest):** Lokalna baza podataka zaštićena je putem **SQLCipher (AES-256)** enkripcije, dok su korisnički bekap fajlovi šifrovani industrijskim standardom **AES-256-GCM** uz **PBKDF2** izvođenje ključa sa solju.
* **100% lokalna provera linkova i verifikacionih kodova:** Provera da li je link u poruci zlonameran, pretvaranje bezbednih linkova u klikabilne hiperlinkove i prepoznavanje numeričkih i alfanumeričkih verifikacionih kodova (OTP) vrše se isključivo na uređaju bez slanja sadržaja poruka bilo kom serveru.
* **Automatska transparentnost vidžeta bez dozvole za skladište:** Vidžeti na početnom ekranu čitaju isključivo sistemske informacije o dominantnim bojama pozadine (`WallpaperManager.getWallpaperColors`) lokalno na uređaju radi automatskog prilagođavanja providnosti i kontrasta.
* **Mehanizam brisanja podataka (Data deletion mechanism):** Korisnik ima potpunu kontrolu – brisanjem pojedinačne poruke ili razgovora podaci se nepovratno brišu sa uređaja. Brisanje aplikacije uklanja sve interne baze, podatke vidžeta i keš.
* **Nema pristupa bekapa u oblaku bez saglasnosti:** Aplikacija koristi `android:allowBackup="false"` kako bi sprečila automatsko nezaštićeno skladištenje osetljivih poruka preko ADB-a ili na neproverene cloud servise.

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
| `android.permission.INTERNET` | Sistemski | Prijem i slanje MMS sadržaja putem MMSC APN servera mobilnog operatera i opciona provera verzije. |
| `android.permission.RECEIVE_BOOT_COMPLETED` | Sistemski | Ponovno pokretanje zakazanih poruka i osvežavanja vidžeta nakon restarta uređaja. |
