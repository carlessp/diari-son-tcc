# Diari de Son · TCC-I

Webapp per omplir des del mòbil el **Diari de son per a la teràpia conductual-cognitiva de l'insomni**
(Hospital Universitari Vall d'Hebron · Servei de Neurofisiologia Clínica).

**🌐 App en línia:** https://carlessp.github.io/diari-son-tcc/ — instal·lable al mòbil com una app (PWA).

![Icona](icons/icon-192.png)

És un **únic fitxer `index.html`** sense cap dependència externa: funciona sense internet i desa
les dades al navegador (`localStorage`). Opcionalment es poden exportar/importar com a **JSON**.

---

## Què fa

### 1. Introduir
- Navegació per dies (‹ ›), botó **Avui** i **↓ Copiar dia anterior**.
- **Nit**: hora d'anar al llit i hora d'adormir-se → `HH:MM`.
- **Dia següent**: hora de despertar-se i hora de llevar-se → `HH:MM`.
- **Durant la nit**: número de desperts i de vegades que s'ha aixecat del llit (comptadors −/+),
  hores en què es torna a dormir (una entrada per despertar) i total d'hores dormides (decimal).
  Botó **≈ Calcular automàticament** per estimar el total a partir de l'hora d'adormir-se i la de despertar-se.
- **Medicament (nom i dosi)**: files dinàmiques. El nom s'autocompleta amb el catàleg de medicaments
  ja fets servir, i hi ha «xips» dels **més recents** per afegir-los d'un toc. No cal reintroduir-los cada dia.
- **Valoració**: qualitat del son **0-10** amb botons grans i camp «Com m'he sentit durant el dia» + notes.
- Tot es **desa sol** (autosave) amb un indicador «✓ desat / sense dades».

### 2. Visualitzar
- **Resum**: dies registrats, mitjana dormida, qualitat mitjana, eficiència del son (%), latència mitjana i desperts/nit.
- **Evolució** dels últims dies (hores dormides i qualitat).
- **Taula** completa amb el mateix ordre de columnes que el full original.
- **🖨 PDF / Imprimir**: genera una vista idèntica al formulari oficial (A4 apaïsat) i obre el diàleg
  d'impressió del mòbil → tria *«Desa com a PDF»*.
- **⬇ CSV**: descàrrega totes les dades per obrir-les al full de càlcul (separador `;`, amb BOM per a Excel).

### 3. Dades
- **Fitxa del pacient**: nom, SAP, data de naixement, nº pacient, NASS, nº cas, servei i data d'entrega
  (surten a la capçalera del PDF).
- **Còpia de seguretat**: `⬇ Descarregar JSON` i `⬆ Carregar JSON` per guardar-ho o traspassar-ho
  a un altre dispositiu. També `⬇ Descarregar CSV` i `Esborrar tot`.
- **Medicaments recordats**: llista editable del catàleg (toca per oblidar-ne un).
- Botó de **tema clar/fosc** a la capçalera (útil per omplir-ho de nit).

---

## Com obrir-la al mòbil

**Opció A — directament (la més ràpida).** Passa el fitxer `index.html` al mòbil i obre'l amb el
navegador. Afegeix-lo a la pantalla d'inici (mira l'apartat *Add to Home screen*). En alguns navegadors
mòbils `localStorage` pot estar limitat en fitxers locals; en aquest cas usa l'Opció B.

**Opció B — servidor local per WiFi** (recomanada per provar-ho des del mòbil):

```bash
# a l'ordinador, dins d'aquesta carpeta:
python3 -m http.server 8765 --bind 0.0.0.0
```

Esbrina la IP de l'ordinador (`ip a` o `hostname -I`) i obre al mòbil:
`http://IP-DEL-ORDINADOR:8765/` . Afegeix-la a la pantalla d'inici.

**Opció C — penjar-la en un hosting estàtic** (GitHub Pages, Netlify, Cloudflare Pages…): puja
`index.html` i tindràs una URL pròpia, instal·lable com una app. Les metaeetiquetes *Apple/Android
web-app* ja hi són perquè quedi bé a la pantalla d'inici.

> Les dades són **locals al dispositiu**: si canvies de mòbil o neteges el navegador, cal fer servir
> **Descarregar JSON** abans. Guardo una còpia periòdica de seguretat.

---

## Estructura de les dades (JSON)

```jsonc
{
  "version": 1,
  "meta": { "nom": "", "sap": "", "naix": "", "pacient": "", "nass": "", "cas": "", "servei": "", "entrega": "" },
  "meds": [ { "nom": "Melatonina", "dosi": "2 mg" } ],   // catàleg/recordatori
  "dies": {
    "2026-04-14": {
      "llit": "23:15",           // hora que es fica al llit (HH:MM)
      "adorm": "23:50",          // hora que s'adorm (HH:MM)
      "desperta": "06:45",       // hora que es desperta (HH:MM)
      "lleva": "07:20",          // hora que es lleva (HH:MM)
      "despertars": 2,           // número de vegades despertat (enter)
      "aixecars": 1,             // cops aixecat del llit (enter)
      "torns": ["03:10"],        // hores en què es torna a dormir (HH:MM, pot haver-n'hi diverses)
      "totalDormit": 6.5,        // total d'hores dormides (decimal) — opcional
      "meds": [ { "nom": "Melatonina", "dosi": "2 mg" } ],
      "qualitat": 7,             // 0-10 o null
      "sentiments": "cansat però millor",
      "notes": ""
    }
  },
  "ui": { "theme": "light" }
}
```

Mètriques calculades automàticament: **latència** (`adorm − llit`), **temps al llit** (`lleva − llit`),
**eficiència del son** (`totalDormit ÷ temps al llit`). Tots els càlculs d'hores gestionen el canvi de dia
(mitjanit).

---

## Privacitat

Tot es processa i s'emmagatzema **al teu dispositiu**. No hi ha cap servidor, cap analítica ni cap
petició a internet. L'única còpia fora del navegador és la que tu descarreguis (JSON/CSV/PDF).
