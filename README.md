# 🪖 Vestland Airsoft Club (VAC) – Nettsidekrav og Teknisk Spesifikasjon

## 📌 Formål
Nettsiden skal tilby en komplett digital plattform for AirSoft-klubben VAC med:
- Påmelding til spill
- Baneoversikt
- Spillmoduser
- Adminpanel
- Autentisering og brukerhåndtering via Supabase

---

## 🌍 Frontend
- **Framework:** React (alternativt Next.js for SSR)
- **UI-bibliotek:** Tailwind CSS eller ShadCN UI
- **Autentisering:** Supabase Auth (JWT + OAuth)
- **Interaktiv kartvisning:** Leaflet.js
- **State Management:** React Query eller Zustand

---

## 🗃️ Backend & Database
- **Database:** Supabase (PostgreSQL)
- **API-tilgang:** Supabase Client SDK
- **Autentisering:** Supabase Auth med RBAC
- **Filopplasting:** Supabase Storage (kart, bilder)

---

## 🧩 Datamodell (Supabase-tables)

### Brukere (`users`)
| Felt       | Type      | Beskrivelse                |
|------------|-----------|----------------------------|
| id         | UUID      | Primærnøkkel (autogen)     |
| email      | TEXT      | Brukerens e-post           |
| full_name  | TEXT      | Fullt navn                 |
| nickname   | TEXT      | Kallenavn                  |
| phone      | TEXT      | Telefonnummer (valgfritt)  |
| role       | TEXT      | admin / mod / crew / player|
| created_at | TIMESTAMP | Registreringstidspunkt     |

### Arrangementer (`events`)
| Felt        | Type     | Beskrivelse                |
|-------------|----------|----------------------------|
| id          | UUID     | Primærnøkkel               |
| title       | TEXT     | Navn på event              |
| description | TEXT     | Beskrivelse                |
| event_date  | DATE     | Dato                       |
| start_time  | TIME     | Starttid                   |
| location    | TEXT     | Sted / bane                |
| mode_id     | UUID     | Spillmodus (referanse)     |

### Påmeldinger (`registrations`)
| Felt      | Type     | Beskrivelse                |
|-----------|----------|----------------------------|
| id        | UUID     | Primærnøkkel               |
| user_id   | UUID     | Referanse til `users`      |
| event_id  | UUID     | Referanse til `events`     |
| role      | TEXT     | crew / player              |
| confirmed | BOOLEAN  | Godkjent av admin/mod      |

### Spillmoduser (`modes`)
| Felt     | Type     | Beskrivelse                |
|----------|----------|----------------------------|
| id       | UUID     | Primærnøkkel               |
| name     | TEXT     | Navn                       |
| rules    | TEXT     | Regler og detaljer         |
| teams    | INT      | Antall lag                 |
| duration | TEXT     | Estimert varighet          |

### Kartsoner (`field_zones`)
| Felt        | Type  | Beskrivelse                      |
|-------------|-------|----------------------------------|
| id          | UUID  | Primærnøkkel                     |
| name        | TEXT  | F.eks. Spawn, Objective etc.     |
| type        | TEXT  | spawn / flag / restricted        |
| coordinates | JSON  | GeoJSON eller polygondata        |

---

## 🧭 Sidekart / Rutestruktur

| Rute               | Beskrivelse                                |
|--------------------|--------------------------------------------|
| `/`                | Forside m/ info og neste spill             |
| `/events`          | Liste over kommende arrangementer          |
| `/events/:id`      | Detaljer + påmelding                       |
| `/modes`           | Oversikt og info om spillmoduser           |
| `/field`           | Interaktivt banekart + kartinfo            |
| `/login`           | Brukerpålogging (via Supabase)             |
| `/admin/dashboard` | Adminpanel                                 |
| `/admin/events`    | Opprett og administrer arrangementer       |
| `/admin/modes`     | Administrer game modes                     |
| `/admin/users`     | Bruker- og rolleadministrasjon             |

---

## 🔐 Rollebasert tilgang (RBAC via Supabase Policy)

| Rolle     | Tilganger                                              |
|-----------|--------------------------------------------------------|
| Admin     | Full tilgang til alt (CRUD på alle moduler)           |
| Mod       | Lese og endre arrangementer og påmeldinger            |
| Crew      | Kun lese deltakerlister                               |
| Player    | Registrere seg, se arrangementer og informasjon       |

**Eksempelpolicy i Supabase:**
```sql
CREATE POLICY "Players can read events"
ON public.events
FOR SELECT
TO authenticated
USING (true);
```

---

## 🖼️ Filopplasting (Supabase Storage)
- **Bucket:** `field_maps` – kart i PNG, JPG eller PDF
- **Bucket:** `event_images` – bilder knyttet til arrangementer

---

## 📦 API-integrasjon via Supabase Client (React)
```ts
import { createClient } from '@supabase/supabase-js';

const supabase = createClient(SUPABASE_URL, SUPABASE_KEY);

const { data: events, error } = await supabase
  .from('events')
  .select('*')
  .order('event_date', { ascending: true });
```

---

## 📤 Eksport og Print
- Printvennlig PDF av deltakerliste
- CSV-eksport av arrangementsdata
- Kart eksportert som PNG/PDF

---

## 📚 Fremtidig utvidelse
- Notifikasjoner via e-post (Supabase Edge Functions / SendGrid)
- Discord-integrasjon (webhooks for spillvarsler)
- Mobilapp (React Native med Supabase backend)