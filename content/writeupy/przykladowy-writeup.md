---
title: "[Przykład] Nazwa Maszyny — HackTheBox"
date: 2026-08-01
draft: false
tags: ["hackthebox", "linux", "przyklad"]
categories: ["writeupy"]
summary: "Szablon writeupu — pokazuje strukturę i formatowanie używane we wszystkich wpisach na tym blogu."
ShowToc: true
TocOpen: true
---

> **To jest wpis-placeholder.** Pokazuje format i strukturę, której używam
> we wszystkich writeupach na tym blogu. Podmień go na pierwszy prawdziwy
> writeup albo skopiuj jako punkt startowy dla nowego wpisu (zobacz
> `README.md` w repo bloga, sekcja "Dodawanie nowego wpisu").

## Info

| | |
|---|---|
| **Platforma** | HackTheBox |
| **Trudność** | Easy |
| **System** | Linux |
| **Status** | Retired |
| **Kluczowe techniki** | np. CVE-XXXX, misconfiguration, SUID abuse |

## TL;DR

Krótkie, 3-4 zdaniowe podsumowanie całego łańcucha ataku — od pierwszego
skanu do roota. Ktoś, kto przeczyta tylko ten akapit, powinien wiedzieć,
"co było grane" na tej maszynie.

## Rekonesans

```bash
nmap -sC -sV -p- <TARGET_IP>
```

Opis tego, co wyszło ze skanu i dlaczego akurat ten port/usługa był
następnym krokiem. Zrzuty ekranu wstawiaj tak:

```markdown
![opis zrzutu](images/01-nmap.png)
```

## Foothold / pierwszy dostęp

Tu opisujesz konkretną podatność i sposób jej wykorzystania — **dlaczego**
to zadziałało, nie tylko commendy do wklejenia.

```bash
przykladowa-komenda --exploit
```

## Eskalacja uprawnień

Analogicznie: co znalazłeś podczas enumeracji (SUID, sudo -l, cron, itd.)
i jak to złożyło się w pełną eskalację do roota.

```bash
sudo -l
```

## Wnioski

- Co było nieoczywiste / czego się nauczyłeś na tej maszynie.
- Wzorzec, który prawdopodobnie powtórzy się na innych boxach.

## Rekomendacje (remediation)

- Jak realnie zamknąć tę podatność w produkcyjnym środowisku.

---

**Maszyna:** [HackTheBox — Nazwa](https://www.hackthebox.com/machines/nazwa)
**Zobacz też:** [pełne writeupy w moim portfolio](https://github.com/debasjan/security-portfolio)
