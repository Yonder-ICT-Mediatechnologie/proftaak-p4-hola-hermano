# ERD — LuxeDrive Database

## Entiteiten

```
┌─────────────────────────────┐        ┌─────────────────────────────┐
│            users            │        │            cars             │
├─────────────────────────────┤        ├─────────────────────────────┤
│ id              BIGINT  PK  │        │ id              BIGINT  PK  │
│ name            VARCHAR     │        │ make            VARCHAR     │
│ email           VARCHAR  UQ │        │ model           VARCHAR     │
│ password        VARCHAR     │        │ year            SMALLINT    │
│ email_verified  TIMESTAMP   │        │ price           BIGINT      │
│ remember_token  VARCHAR     │        │ power           VARCHAR     │
│ created_at      TIMESTAMP   │        │ accel           VARCHAR     │
│ updated_at      TIMESTAMP   │        │ topspeed        VARCHAR     │
└─────────────────────────────┘        │ status          ENUM        │
                                       │                 (available, │
┌─────────────────────────────┐        │                  sold)      │
│   personal_access_tokens    │        │ image           VARCHAR     │
├─────────────────────────────┤        │ description     TEXT        │
│ id              BIGINT  PK  │        │ created_at      TIMESTAMP   │
│ tokenable_type  VARCHAR     │───┐    │ updated_at      TIMESTAMP   │
│ tokenable_id    BIGINT      │   │    └─────────────────────────────┘
│ name            VARCHAR     │   │
│ token           VARCHAR  UQ │   │  (polymorf) tokenable → users.id
│ abilities       TEXT        │   │
│ last_used_at    TIMESTAMP   │   │
│ created_at      TIMESTAMP   │◄──┘
└─────────────────────────────┘
```

## Relaties

- **users (1) ── (n) personal_access_tokens**
  Eén admin-gebruiker kan meerdere login-tokens hebben (Laravel Sanctum).
  De relatie is polymorf via `tokenable_type` + `tokenable_id`.

- **cars** staat los: een onafhankelijke entiteit met de voorraad.
  (In een uitbreiding zou je `cars.created_by → users.id` kunnen toevoegen
  om bij te houden welke admin een auto plaatste.)

## Toelichting velden `cars`

| Veld        | Type      | Uitleg                                  |
|-------------|-----------|-----------------------------------------|
| price       | BIGINT    | Prijs in hele euro's (geen decimalen)   |
| status      | ENUM      | `available` of `sold`                   |
| power/accel | VARCHAR   | Tekstueel ("1000 pk", "2,5 s")          |
| image       | VARCHAR   | URL naar de afbeelding                   |
