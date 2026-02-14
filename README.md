# Evidence Balíků — návrh all‑in‑one business platformy

Tento dokument popisuje návrh modulární aplikace (Flutter + API server), která sjednotí provoz firmy do jednoho systému: POS, sklad, inventura, logistika, servis, galerie, CRM, finance i integrace na externí aplikace.

## 1) Vize produktu

**Cíl:** jeden systém pro denní provoz firmy od prodeje přes sklad až po doručení a servis.

**Typy firem:** retail, e‑shop, velkoobchod, servisní firmy, rozvozové firmy, menší výroba.

**Princip:**
- jádro platformy + zapínatelné moduly,
- jednotná databáze,
- role/oprávnění,
- API‑first přístup pro napojení externích nástrojů.

---

## 2) Modulová mapa (all‑in‑one)

### A. POS / Pokladna
- rychlý prodej (dotykové UI pro tablet),
- sken čárových/QR kódů,
- více plateb (hotovost, karta, převod, kombinace),
- slevy, kupóny, věrnostní body,
- vratky a storna,
- účtenky (tisk/PDF/e‑mail),
- směny pokladních a uzávěrky.

### B. Produkty & katalog
- SKU, EAN, varianty (barva, velikost),
- kategorie, značky, atributy,
- cenotvorba (nákup/prodej/marže),
- více ceníků (B2B/B2C/akce),
- produktová galerie (fotky, dokumentace, návody),
- balíčky/sety produktů.

### C. Skladové hospodářství
- více skladů a lokací,
- příjemky, výdejky, převodky,
- rezervace zboží pro objednávky,
- minimální zásoba a automatické doobjednání,
- šarže, expirace, seriová čísla,
- audit trail pohybů.

### D. Inventura
- plánované i průběžné inventury,
- inventura přes mobilní skener,
- porovnání „systém vs realita",
- vyrovnání rozdílů (manka/přebytky),
- inventurní protokoly a exporty.

### E. Objednávky
- objednávky přijaté i vydané,
- workflow stavů (nová, zpracovává se, expedováno, dokončeno),
- vazba na sklad, fakturaci a logistiku,
- split objednávek podle dostupnosti,
- B2B poptávky a schvalování.

### F. Balíky & expedice
- tvorba zásilek ze zakázek,
- tisk štítků,
- párování balíků k dopravci,
- sledování zásilek (tracking),
- hromadná expedice,
- reklamace přepravy.

### G. Dodávky & logistika
- plánování tras a rozvozů,
- přiřazení řidiče/vozidla,
- nakládka/vykládka,
- potvrzení doručení (POD podpis/foto/GPS),
- ETA a monitoring stavu doručení,
- interní i externí doprava.

### H. Servis / opravárenský modul
- evidence servisních zakázek,
- příjem zařízení (diagnostika, závada),
- kalkulace opravy + schválení zákazníkem,
- evidence náhradních dílů,
- plán práce techniků,
- servisní protokol + fotogalerie před/po.

### I. CRM & zákazníci
- zákazníci, firmy, kontakty,
- historie objednávek, komunikace, servisů,
- segmentace a štítky,
- obchodní příležitosti (pipeline),
- úkoly a follow‑up notifikace.

### J. Finance & fakturace
- faktury, dobropisy, zálohy,
- párování plateb,
- pokladna/bankovní přehled,
- DPH režimy, export pro účetní,
- základní P&L přehled a cashflow.

### K. Dokumenty & galerie
- dokumenty k zakázce/produktu (PDF, smlouvy, manuály),
- galerie fotek (produkty, škody, servis),
- verzování a audit,
- sdílení přes odkaz s oprávněním.

### L. Reporting & BI
- dashboard KPI (tržby, marže, obrátka skladu),
- top produkty/pobočky/kurýři,
- SLA doručení a úspěšnost servisu,
- vlastní filtry a export (CSV/XLSX/PDF),
- plán vs realita.

### M. HR & docházka (volitelně)
- uživatelé a role,
- směny a docházka,
- výkonové metriky (pokladní, skladník, technik).

### N. Notifikace & komunikace
- in‑app notifikace,
- e‑mail/SMS/push,
- systémové alerty (dochází zboží, zpoždění, SLA risk).

### O. Integrace hub
- e‑shop platformy,
- dopravci,
- účetní systémy,
- platební brány,
- IoT (tiskárny, skenery, váhy).

---

## 3) Vestavěný API server (API‑first)

## 3.1 Cíle API
- komunikace s mobilní Flutter aplikací,
- napojení třetích stran (e‑shop, ERP, účetnictví),
- webhooks pro realtime události,
- bezpečný přenos dat mezi systémy.

## 3.2 API schopnosti
- REST API (v1) + volitelně GraphQL read endpoint,
- CRUD pro všechny klíčové entity,
- bulk endpointy (import/export),
- webhooky (order.created, shipment.delivered, stock.low),
- idempotency klíče pro bezpečné opakované volání,
- audit log endpointy.

## 3.3 Bezpečnost
- OAuth2/JWT,
- API klíče pro systém‑to‑systém,
- RBAC/ABAC oprávnění,
- rate limiting, IP allowlist,
- šifrování dat (TLS + sensitive at‑rest),
- audit všech kritických změn.

## 3.4 Integrace
- konektorový framework (dopravci, účetnictví, e‑shop),
- plánovač úloh (sync každých X minut),
- dead‑letter fronta pro chybové eventy,
- monitoring integrací (retry, alerty, health status).

---

## 4) Doporučená architektura

### Frontend
- Flutter (Android/iOS/tablet, později web),
- role‑based UI,
- offline‑first pro POS/sklad/logistiku.

### Backend
- API server (NestJS/Laravel/FastAPI),
- PostgreSQL,
- Redis (cache + queue),
- objektové úložiště pro galerie/dokumenty,
- message broker (RabbitMQ/Kafka) pro eventy.

### Offline a synchronizace
- lokální DB v mobilu (Isar/SQLite),
- sync engine: pull/push + conflict resolution,
- režim „degradovaný provoz“ bez internetu.

### Multi‑tenant design
- oddělení dat podle firmy/účtu,
- pobočky jako sub‑entity,
- izolace dat a per‑tenant konfigurace.

---

## 5) Role a oprávnění

Příklad rolí:
- Owner/Admin,
- Manažer pobočky,
- Pokladní,
- Skladník,
- Dispečer logistiky,
- Řidič/Kurýr,
- Servisní technik,
- Účetní,
- Externí API integrace (strojový účet).

Každá role má přesně definované akce (view/create/update/approve/export).

---

## 6) Návrh datových domén (high‑level)

- **Identity:** users, roles, permissions, tenants
- **Catalog:** products, variants, pricing, media
- **Inventory:** warehouses, stock_items, stock_moves, counts
- **Sales:** pos_orders, order_items, payments, receipts
- **Procurement:** purchase_orders, suppliers, goods_receipts
- **Logistics:** shipments, labels, routes, delivery_events
- **Service:** repair_orders, diagnostics, parts, labor, outcomes
- **CRM:** customers, contacts, activities, notes
- **Finance:** invoices, credit_notes, transactions, tax_records
- **Integration:** api_clients, webhooks, sync_jobs, event_logs
- **Documents:** files, galleries, attachments, signatures

---

## 7) Fázování vývoje (roadmapa)

### Fáze 1 — Core MVP
- POS + produkty + sklad + objednávky,
- základní API,
- role a přihlášení,
- report tržeb a zásob.

### Fáze 2 — Logistika
- balíky, štítky, tracking,
- trasy a doručení,
- integrace min. 1 dopravce.

### Fáze 3 — Servis a galerie
- opravárenský modul,
- fotodokumentace a protokoly,
- notifikace zákazníkům.

### Fáze 4 — Pokročilé integrace
- účetnictví, e‑shop konektory,
- pokročilé BI dashboardy,
- automatizace workflow.

---

## 8) Co doporučuji vybrat jako první implementaci

Pokud chceš rychlý start, začni tímto setem:
1. POS
2. Sklad + inventura
3. Objednávky
4. Balíky + základ logistika
5. API server v1

Tím pokryješ hlavní provozní scénáře a můžeš postupně zapínat další moduly.
