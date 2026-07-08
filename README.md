## Hi there 👋

✅ 1. Clonezi repo folosind HTTPS
Pe GitHub, apasă pe Code → HTTPS și copiază link-ul.
Apoi în terminal:

git clone https://github.com/user/repo.git

✅ 2. Intri în repo

cd repo

✅ 3. Creezi un branch nou (pentru modificările tale)

git checkout -b nume-branch

✅ 4. Faci modificările + commit

git add .
git commit -m "am adaugat feature"

✅ 5. Push pe GitHub (fără SSH)

git push origin nume-branch


flutter build appbundle --release --dart-define-from-file=env.json

flutter build apk --release --dart-define-from-file=env.json

Pași de activare a CAPTCHA (opțional, când vrei protecția maximă)

Supabase Dashboard → Authentication → Settings → Enable CAPTCHA protection → alege Cloudflare Turnstile, pune Secret Key.

Cloudflare → creează un site Turnstile, ia Site Key.

Build cu cheia: adaugă în env.json linia "TURNSTILE_SITE_KEY": "0x4AAA..." (folosit deja cu --dart-define-from-file=env.json).

(Opțional) înăsprește limitele din Authentication → Rate Limits.

Notă: și fără CAPTCHA, Straturile 1+2 plus rate-limit-urile native Supabase Auth (per IP) oferă protecție imediată.



Faza 1 (pregătiri tehnice) este implementată în cod. Iată ce am făcut și ce urmează.

Ce am modificat acum
build.gradle.kts — semnare release reală: citește android/key.properties; dacă lipsește, cade automat pe cheile de debug (ca să nu se strice build-urile locale). Am scos comentariile TODO.
.gitignore — adăugat android/key.properties, *.jks, *.keystore (secretele nu intră în Git).
key.properties.example — template cu instrucțiuni (îl copiezi în key.properties și completezi).
Verificări
targetSdk = 36 (Flutter 3.38.7) → trece cerința Google ≥35. ✅
applicationId = ro.snppc.snppc_app (definitiv după publicare).
⚠️ Două lucruri pe care trebuie să le faci tu (nu pot eu)
1. Generează keystore-ul (parolele sunt secrete — le introduci tu):
   keytool -genkey -v -keystore $env:USERPROFILE\snppc-upload.jks -keyalg RSA -keysize 2048 -validity 10000 -alias snppc-upload

Apoi copiază key.properties.example → android/key.properties și completează parolele + calea (cu /, nu \). Fă backup la .jks + parole — fără ele nu mai poți publica update-uri.
flutter doctor --android-licenses

Rezultă build/app/outputs/bundle/release/app-release.aab — fișierul pe care îl urci în Play Console.
flutter build appbundle --release --dart-define-from-file=env.json

Următoarele faze din plan (pot ajuta concret la cele marcate 🟢):

Faza 2 — Juridic: 🟢 pot pregăti textul politicii de confidențialitate (din 06_SECURITY_GDPR.md) ca pagină publicabilă; tu trebuie doar să o pui online la un URL.
Faza 3 — Store listing: 🟢 pot scrie titlul, descrierea scurtă și descrierea completă (RO), optimizate.
Faza 4 — Cont demo pentru recenzori (necesar dacă aplicația cere login): tu creezi un user de test în Supabase.
Faza 5 — Lansare: internal testing → producție.

Publică pagina la un URL public pe snppc.ro, de ex. https://snppc.ro/confidentialitate-aplicatie. Acel URL îl pui în Play Console (și opțional în aplicație, la „Despre”).
Ștergerea contului în aplicație — am scris condiționat „Profil → Setări → Șterge contul (dacă este disponibilă)”. Spune-mi dacă funcția există; dacă nu, fie o implementăm, fie scot mențiunea și rămâne doar calea prin e-mail (acceptată de Google).
Adresa poștală — sediul nu apare nici pe site; dacă vrei, o adaug.
Confirmă lista de sub-procesatori (dacă mai folosești și alt serviciu — ex. notificări push — îl adaug).


Ce trebuie să faci tu pentru Faza 3
Publică privacy-policy-app.html la un URL public (ex. snppc.ro/confidentialitate-aplicatie) și pune-l în Play Console.
Pregătește graficele: app icon 512×512, feature graphic 1024×500, minim 2 capturi de ecran.
Copiază textele în Store presence → Main store listing.

Faze rămase
Faza 4 — Play Console: Data Safety (acum poți declara corect „Account deletion = ștergere definitivă în aplicație”), Content rating (IARC), cont demo pentru reviewer (app-ul cere login, altfel e respins), URL politică.
Faza 5 — Testing (Internal → Closed ~12 testeri/14 zile pentru conturi noi) → Production.

Punctele cheie
1. App Access (cont demo) — obligatoriu pentru că app-ul cere login. Fără cont demo, review-ul e respins automat. Trebuie să creezi un cont real de membru de test în Supabase (demo@snppc.ro), aprobat și funcțional.

2. Data Safety — tabel complet cu ce să declari:

Colectezi: nume, email, User ID, CNP/date legitimație (obligatorii), telefon + locație aproximativă (opționale), conținut/mesaje juridice.
„Encrypted in transit" = Da, „Data deletion" = Da + URL.
⚠️ Poza CI nu se declară ca „Photos" — OCR-ul e local, imaginea nu se stochează; declari doar câmpurile rezultate (nume, CNP).
Supabase = procesator → „Shared" = Nu la toate.
3. Content Rating (IARC) → PEGI 3/Everyone. 4. Ads = Nu. 5. Target audience = 18+. 6. Government apps = Nu (sindicat = ONG, nu instituție guvernamentală).

Ce trebuie să faci tu
Creează și testează contul demo în Supabase.
Publică politica de confidențialitate la un URL public.
Parcurge secțiunile din App content până devin toate „Complete".
Trec la Faza 5 — Testing & Release (Internal → Closed testing → Production, plus regula de 12 testeri/14 zile pentru conturi noi de developer)?

Pașii cheie
Prerechizite (o singură dată):

⚠️ Android SDK trebuie instalat pe acest PC (acum lipsește — build-ul eșuează). Apoi flutter doctor --android-licenses.
Generează upload keystore + completează android/key.properties + backup (dacă îl pierzi, nu mai poți face update-uri).
Deploy delete-account + rulează migrațiile.
Build:
flutter clean
flutter pub get
flutter build appbundle --release --dart-define-from-file=env.json

→ build\app\outputs\bundle\release\app-release.aab (cu cheia anon, niciodată service_role).

Lansare (cont nou de developer):
Internal testing → Closed testing (min. 12 testeri, 14 zile consecutive) → Production. Asta e regula Google care întârzie cel mai mult — pornește testarea închisă cât mai devreme.

Plus release notes gata în română și un checklist final (inclusiv: applicationId e permanent, versionCode trebuie incrementat la fiecare upload).

Statusul întregului plan
Faza	Stare
1 — Tehnic (semnare)	✅ cod gata; lipsesc SDK + keystore local
2 — Politică confidențialitate	✅ HTML gata; de publicat la URL
3 — Store listing	✅ texte gata; lipsesc graficele
4 — Play Console config	✅ ghid gata; de completat în consolă
5 — Testing & Release	✅ ghid gata; de executat

Toate cele 5 faze sunt acum documentate și codul/textele sunt pregătite. Următoarele acțiuni sunt pe partea ta (instalare SDK, keystore, grafice, completare în Play Console). Vrei să te ajut cu ceva concret — de ex. să generez textele pentru capturile de ecran, să verific configurația de semnare, sau să te ghidez pas cu pas la generarea keystore-ului?




<!--
**macoveidorin/macoveidorin** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->



Momentan, fiecare slide care așteaptă media are un placeholder marcat cu numele fișierului exact + eticheta „IMAGINE" sau „🎬 VIDEO". Am creat și generatorul build_canva_pptx.py, astfel încât să pot insera automat capturile reale și să regenerez fișierul.

Cum îmi dai media (2 opțiuni)
Opțiunea A — auto-embed (recomandat): pune fișierele într-un folder și dă-mi calea. Ideal, redenumite exact așa (eu le inserez și regenerez .pptx-ul):

Slide	Nume fișier	Slide	Nume fișier
1	snppc_sl01_home-hero.png	13	snppc_sl13_voting.png*
4	snppc_sl04_home-grid.png	14	snppc_sl14_notifications.png
6	snppc_sl06_login.png	15	snppc_sl15_salary.png
7	snppc_sl07_cnp-mask.png	16	snppc_sl16_news.png
8	snppc_sl08_member-card.png	17	snppc_sl17_stats.png
9	snppc_sl09_admin.png	18	snppc_sl18_offers.png
10	snppc_sl10_membership.png*	19	snppc_sl19_profile.png
11	snppc_sl11_documents.png*	20	snppc_sl20_foundation.png
12	snppc_sl12_aid.png		
*Pentru slide-urile cu video (8, 10, 11, 13), dă-mi și un cadru-poster .png (Canva nu importă fidel video din .pptx) — pun poster-ul în slide, iar tu tragi peste el clipul real în Canva.
