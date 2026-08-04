# debasjan.github.io

Blog techniczny o pentestingu i security — writeupy z HackTheBox, OSCP i bug
bounty. Zbudowany na [Hugo](https://gohugo.io/) z motywem
[PaperMod](https://github.com/adityatelange/hugo-PaperMod), publikowany
automatycznie na GitHub Pages przy każdym pushu na `main`.

Pełne writeupy i metodologia (dłuższa forma) są w osobnym repo:
[security-portfolio](https://github.com/debasjan/security-portfolio). Ten
blog to krótsza, bardziej "na bieżąco" forma tych samych tematów.

---

## Wymagania

- **Git** — masz już.
- **Hugo (Extended)** — do podglądu lokalnego. GitHub Actions instaluje
  swoją własną kopię przy publikacji, więc lokalny Hugo jest potrzebny
  tylko do podglądu przed pushem.

Instalacja Hugo na Windows:

```powershell
winget install --id Hugo.Hugo.Extended -e
```

(alternatywnie `choco install hugo-extended` albo `scoop install hugo-extended`).
Po instalacji zrestartuj terminal, żeby `hugo` był widoczny w PATH.

Sprawdź wersję:

```bash
hugo version
```

Wersja użyta w tym repo (i w workflow GitHub Actions) to **0.164.0** — jeśli
lokalnie masz inną, zaktualizuj też `HUGO_VERSION` w
`.github/workflows/hugo.yml`, żeby build lokalny i CI się zgadzały.

---

## Podgląd lokalny

```bash
hugo server -D
```

`-D` pokazuje też wpisy oznaczone jako `draft: true` (przydatne, bo nowe
wpisy z archetypu zaczynają jako draft). Strona pojawi się pod
`http://localhost:1313/`. Hugo przeładowuje ją automatycznie przy zapisie
pliku.

---

## Dodawanie nowego wpisu

```bash
hugo new content writeupy/nazwa-maszyny.md
```

To utworzy plik na bazie szablonu z `archetypes/writeupy.md` — gotowa
struktura nagłówków (Info, TL;DR, Rekonesans, Foothold, Eskalacja
uprawnień, Wnioski, Rekomendacje). Zobacz
`content/writeupy/przykladowy-writeup.md` jako w pełni wypełniony przykład
formatowania (zrzuty ekranu, bloki kodu, tabelka Info).

**Nazwa pliku = adres URL wpisu**, więc używaj samych małych liter, myślników
zamiast spacji, bez polskich znaków — np. `resolute-active-directory.md`,
nie `Resolute – Active Directory.md`.

### Front matter — pola, które warto ustawić

```yaml
---
title: "Nazwa maszyny — Platforma"
date: 2026-08-03          # data publikacji — wpisy z przyszłą datą się nie pokażą
draft: false               # true = niewidoczne na produkcji, tylko z `-D`
tags: ["hackthebox", "linux", "active-directory"]
categories: ["writeupy"]
summary: "Jedno zdanie — pokazuje się na liście wpisów i w meta description"
ShowToc: true
TocOpen: true               # spis treści rozwinięty od razu
---
```

### Zrzuty ekranu

Najprościej: wrzuć obrazki do `static/images/<nazwa-wpisu>/` i linkuj
względem `static/`:

```markdown
![opis zrzutu](/images/resolute/01-nmap.png)
```

Alternatywnie możesz użyć **page bundle** (folder zamiast pojedynczego
pliku `.md`) — wtedy obrazki trzymasz obok treści:

```
content/writeupy/resolute/
├── index.md
├── 01-nmap.png
└── 02-bloodhound.png
```

i linkujesz po prostu `![opis](01-nmap.png)`. To wygodniejsze przy wpisach
z dużą liczbą zrzutów, bo wszystko siedzi w jednym folderze.

---

## Publikacja

Publikacja jest **automatyczna**: każdy push na branch `main` uruchamia
workflow `.github/workflows/hugo.yml`, który buduje stronę Hugo i wgrywa ją
na GitHub Pages. Nic ręcznie nie trzeba wdrażać.

```bash
git add content/writeupy/nowy-writeup.md static/images/nowy-writeup/
git commit -m "Dodaj writeup: Nazwa Maszyny"
git push
```

Postęp buildu widać w zakładce **Actions** na GitHubie. Strona aktualizuje
się zwykle w 1-2 minuty po pushu.

> Pamiętaj o `draft: false` w froncie matter — draft nie zostanie
> opublikowany, nawet jeśli go wypushujesz.

---

## Aktualizacja motywu PaperMod

Motyw jest dołączony jako **git submodule**. Żeby zaktualizować do
najnowszej wersji:

```bash
git submodule update --remote --merge themes/PaperMod
git add themes/PaperMod
git commit -m "Aktualizacja motywu PaperMod"
git push
```

Jeśli klonujesz to repo od zera na nowym komputerze, pamiętaj o
`--recursive`, inaczej folder `themes/PaperMod` będzie pusty:

```bash
git clone --recursive https://github.com/debasjan/debasjan.github.io.git
```

(albo `git submodule update --init --recursive` po zwykłym `git clone`).

---

## Struktura repo

```
├── archetypes/writeupy.md       # szablon dla `hugo new content writeupy/...`
├── content/
│   ├── writeupy/                # sekcja z writeupami
│   │   ├── _index.md            # opis sekcji (widoczny na /writeupy/)
│   │   └── przykladowy-writeup.md
│   ├── o-mnie.md                # strona "O mnie"
│   ├── szukaj.md                # strona wyszukiwania (JS, client-side)
│   └── archiwum.md              # lista wszystkich wpisów wg daty
├── static/images/               # obrazki niepowiązane z konkretnym wpisem
├── themes/PaperMod/              # motyw (git submodule)
├── hugo.toml                     # cała konfiguracja: język, SEO, menu, motyw
└── .github/workflows/hugo.yml    # build + deploy na GitHub Pages
```

---

## SEO / techniczne

Włączone domyślnie przez konfigurację w `hugo.toml`:

- **sitemap.xml** — generowany automatycznie przez Hugo (`/sitemap.xml`)
- **RSS** — `/index.xml` (cała strona) + RSS per sekcja
- **robots.txt** — generowany, wskazuje na sitemap
- **JSON index do wyszukiwarki** — `/index.json`, używany przez stronę
  `/szukaj/`
- Meta description / OpenGraph / Twitter Cards — z `summary` wpisu albo
  `params.description` jako fallback

Jeśli chcesz dodać własny obrazek podglądu (Open Graph, pokazuje się przy
udostępnianiu linka w social media), wrzuć plik do `static/images/` i dodaj
w `hugo.toml`:

```toml
[params]
  images = ["images/og-cover.png"]
```
