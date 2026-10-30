# Caso 07: Solicitud de licencia de conducción

> Caso **ficticio y simplificado** para el curso. Las reglas son inventadas y no describen ninguna entidad real.

## Contexto

Un organismo de tránsito ficticio recibe solicitudes de expedición de licencias. Las reglas de esta ficha son inventadas para el curso y **no** corresponden a la normativa colombiana vigente.

## Entidad: `SolicitudLicencia`

| Atributo | Tipo | Descripción |
|---|---|---|
| `documento` | texto | Solo dígitos, entre 8 y 10. |
| `edad` | entero | Años cumplidos. |
| `categoria` | enumerado | `A2` (motocicletas), `B1` (automóviles) o `C1` (servicio público). |
| `diasDesdeExamenMedico` | entero | Días desde el examen médico. |
| `cursoAprobado` | booleano | Si aprobó el curso de conducción de la categoría. |

## Reglas de negocio (unidad 3)

Se evalúan **en este orden**; la primera que se cumpla determina el resultado. Si no se cumple ninguna, el resultado es `APPROVED`.

| # | Condición de rechazo | Resultado |
|---|---|---|
| R1 | Algún campo obligatorio falta o no cumple su formato. | `INVALID` |
| R2 | La edad no alcanza el mínimo de la categoría: 16 para `A2`, 18 para `B1`, 21 para `C1`. | `AGE_NOT_ALLOWED` |
| R3 | El examen médico tiene más de 180 días. | `MEDICAL_EXPIRED` |
| R4 | No aprobó el curso de la categoría. | `COURSE_REQUIRED` |
| R5 | Para `C1`, no tiene licencia `B1` con al menos 2 años de expedida (servicio externo). | `REQUIRES_B1` |
| R6 | Tiene multas de tránsito pendientes (servicio externo). | `PENDING_FINES` |

## Persistencia y unicidad (unidad 4)

Una persona solo puede tener una solicitud en trámite por categoría. Si se intenta, el resultado es `DUPLICATED`.

## Servicio externo (unidad 4)

**Registro de conductores**, que su sistema consume pero no controla:

```http
GET /conductores/{documento}
```

Respuesta de ejemplo:

```json
{ "multasPendientes": 0, "licencias": [{ "categoria": "B1", "expedicion": "2023-04-02" }] }
```

Si el servicio no responde o responde con error, el resultado es `EXTERNAL_UNAVAILABLE`.

## API REST (unidades 4 y 5)

- `POST /solicitudes`: registra la solicitud y devuelve el resultado.
- `GET /solicitudes?documento={texto}`: búsqueda por un campo de texto.

Los códigos HTTP son los comunes a todos los casos (ver [el índice](README.md#contrato-común-a-todos-los-casos)).

## SLO de partida (unidad 5)

p95 < 400 ms y tasa de error < 1 % con 80 usuarios concurrentes en horario de oficina.

Es un punto de partida. El equipo debe justificarlo o ajustarlo con datos, y documentar la decisión.

## Supuestos que el equipo debe resolver

Estas preguntas no tienen respuesta en la ficha **a propósito**: en un proyecto real tampoco la tendrían. Decidan, documenten la decisión en el Wiki y escriban las pruebas que la fijan.

1. "Al menos 2 años de expedida": ¿se cuentan días (730), años calendario o años cumplidos a la fecha?
2. Si el registro de conductores no responde, ¿se rechaza la solicitud o queda en espera?
