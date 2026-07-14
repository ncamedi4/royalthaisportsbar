# Deploy-guide — Royal Thai SportsBar med admin-panel

Denna guide gör tre saker:
1. Flyttar sajten från Netlify Drop till GitHub-baserad deploy (samma sajt, samma domän — behövs för admin-panelen)
2. Aktiverar Netlify Identity + Git Gateway (så kunden kan logga in på `/admin/`)
3. Bjuder in kunden så hen kan redigera evenemang, öppettider och meny

**Räknar 15–20 minuter totalt.** Krav: ett GitHub-konto (gratis, 2 min att skapa om du inte har).

---

## Steg 1 — Skapa GitHub-repo (3 min)

1. Gå till [github.com/new](https://github.com/new)
2. **Repository name:** `royal-thai-sportsbar` (eller vad du vill)
3. **Public** eller **Private** — spelar ingen roll (Netlify läser båda)
4. **Skapa INTE README/gitignore/license** — vi vill ha ett tomt repo
5. Klicka **Create repository**

Kopiera repo-URL:en (t.ex. `https://github.com/anton-nca/royal-thai-sportsbar`).

## Steg 2 — Pusha koden till repot (5 min)

Öppna Terminal, cd:a till `Claude/`-mappen:

```bash
cd "/Users/antonnorrman/Downloads/NCA Media/Kunder/2026/Royal Tai och Sportsbar/Claude"
git init
git add .
git commit -m "Initial commit — Royal Thai SportsBar site + admin"
git branch -M main
git remote add origin https://github.com/DITT-KONTO/royal-thai-sportsbar.git
git push -u origin main
```

Byt ut `DITT-KONTO/royal-thai-sportsbar` mot din faktiska URL. Om det ber om lösenord, använd en Personal Access Token (github.com/settings/tokens → "Generate new token (classic)" → tick `repo`).

## Steg 3 — Koppla Netlify till GitHub-repot (3 min)

Du har redan sajten på Netlify Drop (`royalthaisportsbar.netlify.app`). Vi byter dess deploy-metod:

1. Öppna Netlify → **Project overview** → **Project configuration** → **Build & deploy** → **Continuous deployment**
2. Klicka **Link repository**
3. Välj **GitHub** → auktorisera → välj `royal-thai-sportsbar`-repot
4. **Branch:** `main`
5. **Build command:** (lämna tomt)
6. **Publish directory:** `.` (punkt)
7. Klicka **Deploy site**

Ny deploy tar ~30 sek. Domänen och allt annat behålls.

## Steg 4 — Aktivera Netlify Identity (2 min)

1. Netlify → **Integrations** (eller **Extensions**) → sök **Netlify Identity** → **Enable**
2. **Registration:** ändra från "Open" till **"Invite only"** (så bara du och kunden kan skapa konton)
3. **External providers:** valfritt lägga till Google (bekvämt om kunden har Google-konto)

## Steg 5 — Aktivera Git Gateway (1 min)

Fortfarande i Identity-inställningarna:

1. Scrolla ner till **Services** → **Git Gateway**
2. Klicka **Enable Git Gateway**

Detta gör att Decap kan skriva tillbaka till GitHub via Netlify — utan att kunden själv behöver GitHub-konto.

## Steg 6 — Bjud in dig själv (och testa) (3 min)

1. Netlify → **Identity**-fliken → **Invite users**
2. Ange **din egen** mejladress först
3. Kolla mejlen — klicka länken → sätt lösenord
4. Gå till `royalthaisportsbar.netlify.app/admin/`
5. Klicka **Logga in med Netlify Identity** → använd mejl + lösenord

Du ser nu admin-panelen med **Evenemang / Öppettider / Meny**. Prova redigera ett evenemang och klicka **Publish** — inom ~30 sek är sajten uppdaterad.

## Steg 7 — Bjud in kunden (1 min)

När du verifierat att allt fungerar:

1. Netlify → **Identity** → **Invite users**
2. Ange kundens mejl (`royalthaisportsbar@gmail.com` eller ägarens privata)
3. Skicka länken till kunden med ett kort mejl:

> "Hej! Sajten är live och du kan själv redigera evenemang, öppettider och meny. Gå till royalthaisportsbar.netlify.app/admin/ och klicka 'Logga in med Netlify Identity'. Använd mejlet du precis fick från Netlify för att sätta lösenord. Ändringar publiceras automatiskt inom en minut."

---

## Vad kunden kan (och inte kan) redigera

**Kan redigera själv:**
- ✅ Evenemang — lägga till nya, ändra befintliga, ladda upp bilder, markera vilka som visas på startsidan
- ✅ Öppettider — anpassa för helger/semester
- ✅ Meny — priser, beskrivningar, hela kategorier
- ✅ Bilder (drag-and-drop, går till `/uploads/`)

**Kan INTE redigera själv** (det tar Anton):
- Brand-texter (hero, intro, om oss)
- Layout och design
- Kontaktuppgifter (adress, telefon i footer)
- Boka Bord-formulär

Om kunden vill ändra något i "kan INTE"-listan, säger de bara till dig.

---

## Bonuspoäng: Anslut royalthaisportsbar.com

Så länge Netlify har URL `royalthaisportsbar.netlify.app` känns det inte helt proffs. Fix:

1. Netlify → **Domain management** → **Add custom domain**
2. Skriv `royalthaisportsbar.com`
3. Följ instruktionerna — de säger vilka DNS-records du ska ändra hos nuvarande registrar (om Wix, ändra där; om du köpt domänen separat, ändra hos den)
4. Netlify sätter upp gratis SSL automatiskt (grön hänglås)

Efter DNS-propagation (kan ta upp till 48h men oftast <1h) laddar `royalthaisportsbar.com` sajten och admin-panelen ligger på `royalthaisportsbar.com/admin/`.

---

## Formspree (för Boka Bord-formuläret)

Kom ihåg att aktivera Formspree separat:

1. Skapa gratis konto på [formspree.io](https://formspree.io)
2. Klicka **New Form** → mottagare `royalthaisportsbar@gmail.com`
3. Kopiera form-id (ser ut som `xrbknbjw`)
4. Öppna `index.html`, sök upp `const FORMSPREE_ID="FORMSPREE_ID";`
5. Byt `"FORMSPREE_ID"` mot ditt id, t.ex. `"xrbknbjw"`
6. Commit och push: `git add index.html && git commit -m "Aktivera Formspree" && git push`
7. Netlify deployar automatiskt inom 30 sek

Nu landar bokningar som riktig text direkt i inbox:en — inte mailto-hopp.
