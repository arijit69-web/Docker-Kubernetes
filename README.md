YAML Notes

What is YAML?
- YAML is a human-readable data format.
- It is used mostly for configuration files (Docker, Kubernetes, CI/CD, etc.).
- YAML originally meant "YAML Ain't Markup Language".
- File extensions: .yml or .yaml (both are valid).

Core idea
- YAML stores data as key-value pairs.
- It also supports lists and nested objects.

Important rules
- Indentation matters. Use spaces, not tabs.
- 2 spaces is common and easy to read.
- Keep naming consistent: use snake_case or kebab-case.

Example: basic key-value pairs
```yaml
chai_type: masala_chai # Inline comment
temperature: hot
servings: 2
brewing_time_minutes: 5
```

Note about "version"
- In many config files, version is the version of that config schema/tool, not YAML language version.
```yaml
version: "3.9"
```

Nested data
```yaml
chai_recipe:
  base: black tea
  milk: whole milk
```

Data types
```yaml
chai_name: Masala Chai
description: "Chai with cardamom and ginger"   # Double quotes
tagline: 'The best chai in town'                # Single quotes

# | = literal block (keeps newlines as-is)
brewing_instructions_literal: |
  Boil water.
  Add tea leaves.
  Add milk.

# > = folded block (joins lines into one paragraph)
brewing_instructions_folded: >
  Boil water.
  Add tea leaves.
  Add milk.

cup_count: 3
daily_total_ml: 350

is_hot: true
add_sugar: true
add_salt: false

sweetener: null
alternative_milk: ~  # ~ also means null

brew_date: 2025-01-15
local_time: "2025-01-15 08:30:01"  # quoted to avoid parser differences
```

Boolean safety tip
- Some YAML parsers treat words like yes/no/on/off as booleans.
- Use true/false for clarity.

Collections (lists)
```yaml
spices:
  - ginger
  - cloves
  - black pepper
  - cardamom

steeping_times_minutes: [3, 2, 1]  # inline list is also valid

chai_categories:
  - name: Traditional
    varieties:
      - Masala Chai
      - Ginger Chai
      - Cardamom Chai
  - name: Modern
    varieties:
      - Vanilla Chai
      - Chocolate Chai
```

Dictionary/object (map)
```yaml
masala_chai:
  ingredients:
    tea: black_tea
    liquid:
      water_ml: 200
      milk_ml: 100
    spices:
      ginger: 1_inch
      cinnamon: 1_stick
  preparation:
    duration_minutes: 5
    method: simmer
```

List of objects
```yaml
chai_menu:
  - name: Masala Chai
    price: 30
    size: regular
  - name: Ginger Chai
    price: 20
    size: regular
```

DRY with anchors and aliases
- `&name` creates an anchor (saves a block for reuse).
- `*name` is an alias (pastes the saved block).
- `<<:` is a merge key (merges the anchor's keys into current object).

```yaml
default_chai_base: &default_base
  tea: black_tea
  water_ml: 200
  brewing_time_minutes: 5

morning_chai:
  <<: *default_base   # merges all keys from default_chai_base here
  milk_ml: 100        # adds or overrides keys

evening_chai:
  <<: *default_base
  milk_ml: 300
```

Type casting (tags)
```yaml
zip_code: !!str 12345
count: !!int "123"

unique_spices: !!set
  ? cardamom
  ? ginger
  ? cloves
```

Quick checklist for writing YAML
- Keep indentation consistent.
- Avoid tabs.
- Prefer true/false over yes/no/on/off.
- Quote dates/times when format is important.
- Use comments to explain non-obvious values.

Real backend YAML reference (Node.js API + PostgreSQL)

This is a practical Docker Compose YAML for a backend app.

```yaml
version: "3.9"

services:
  api:
    image: node:20-alpine
    working_dir: /app
    volumes:
      - ./backend:/app
    command: sh -c "npm ci; npm run dev"
    ports:
      - "3000:3000"
    environment:
      NODE_ENV: development
      PORT: 3000
      DB_HOST: db
      DB_PORT: 5432
      DB_NAME: app_db
      DB_USER: app_user
      DB_PASSWORD: app_password
    depends_on:
      - db

  db:
    image: postgres:16-alpine
    restart: unless-stopped
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: app_db
      POSTGRES_USER: app_user
      POSTGRES_PASSWORD: app_password
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

Simple explanation
- `services` defines containers that should run together.
- `api` is your backend server.
- `db` is your PostgreSQL database.
- `depends_on` starts database before API (but API should still retry DB connection).
- `ports` maps local machine port to container port (`host:container`).
- `environment` holds config values used by your app.
- `volumes` keeps database data safe even if container is removed.

When to use this
- Local development setup for backend + database.
- Team projects where everyone needs the same local environment.

Security note
- For real production, do not hardcode passwords in YAML.
- Use `.env` files or secret managers.

