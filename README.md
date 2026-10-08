# RCP System

Sistema de órdenes de trabajo para reparación de celulares y portátiles.
Stack: FastAPI + SQLAlchemy 2 (async) + PostgreSQL.

## Arranque

```bash
cp .env.example .env        # completa SECRET_KEY y FERNET_KEY
docker compose up -d        # PostgreSQL con db/schema.sql ya aplicado
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python -m app.cli crear-usuario asesor@rcp.com "Nombre Asesor" asesor
uvicorn app.main:app --reload
```

Documentación interactiva: http://localhost:8000/docs

## Estructura

```
app/
  main.py            # app FastAPI y routers
  config.py          # variables de entorno
  db.py              # engine y sesión async
  enums.py           # estados, roles, tipos (+ CCA/COM)
  models.py          # tablas (SQLAlchemy)
  schemas.py         # validación de entrada/salida (Pydantic)
  security.py        # login, JWT, cifrado de clave/patrón
  deps.py            # usuario actual y permisos por rol
  pdf.py             # PDF de la OT (cláusulas solo al imprimir)
  routers/
    auth.py          # POST /auth/login, GET /auth/me
    recepcion.py     # crear OT, checklist, PDF
    seguimiento.py   # tabla de casos con buscador
  templates/clausulas.txt   # edita aquí las cláusulas reales
db/schema.sql        # esquema PostgreSQL
```

## Endpoints disponibles

| Método | Ruta | Rol |
|---|---|---|
| POST | /auth/login | todos |
| GET | /recepcion/checklist-items?categoria=CCA\|COM | todos |
| GET | /recepcion/clientes/buscar?cedula_ruc= | todos |
| POST | /recepcion/ots | asesor, gerencia |
| GET | /recepcion/ots/{id}/pdf | asesor, back office, gerencia |
| GET | /seguimiento?q= | todos |

## Siguientes pasos
1. Taller: panel del técnico, asignación y cambios de estado con alertas.
2. Chat global y notificaciones (WebSockets).
3. Proformas, facturación, inventario y Back Office.
4. Migraciones con Alembic (hoy el esquema se aplica con db/schema.sql).
