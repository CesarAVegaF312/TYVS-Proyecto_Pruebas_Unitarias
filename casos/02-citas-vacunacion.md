# Caso 02: Citas de vacunación

> Caso **ficticio y simplificado** para el curso. Las reglas son inventadas y no describen ninguna entidad real.

## Contexto

Una secretaría de salud municipal abre agenda en línea para tres vacunas. Necesita que el sistema rechace las citas que el personal de enfermería tendría que cancelar el mismo día.

## Entidad: `Cita`

| Atributo | Tipo | Descripción |
|---|---|---|
| `documento` | texto | Solo dígitos, entre 6 y 10. |
| `fechaNacimiento` | fecha | Formato ISO `AAAA-MM-DD`. |
| `vacuna` | enumerado | `INFLUENZA`, `VPH` o `FIEBRE_AMARILLA`. |
| `fechaCita` | fecha | Formato ISO `AAAA-MM-DD`. |

## Reglas de negocio (unidad 3)

Se evalúan **en este orden**; la primera que se cumpla determina el resultado. Si no se cumple ninguna, el resultado es `SCHEDULED`.

| # | Condición de rechazo | Resultado |
|---|---|---|
| R1 | Algún campo obligatorio falta o no cumple su formato. | `INVALID` |
| R2 | La fecha de la cita es anterior a hoy. | `PAST_DATE` |
| R3 | La edad no corresponde a la vacuna: `INFLUENZA` desde los 6 meses, `VPH` de 9 a 17 años, `FIEBRE_AMARILLA` de 1 a 59 años. | `AGE_NOT_ALLOWED` |
| R4 | La persona ya tiene la dosis única de `FIEBRE_AMARILLA` (servicio externo). | `ALREADY_IMMUNIZED` |
| R5 | No ha pasado el intervalo mínimo desde la dosis anterior de la misma vacuna: 365 días para `INFLUENZA`, 180 días para `VPH` (servicio externo). | `TOO_SOON` |
| R6 | Ya hay 60 citas agendadas para esa fecha. | `FULL` |

## Persistencia y unicidad (unidad 4)

Una persona no puede tener dos citas el mismo día. Si se intenta, el resultado es `DUPLICATED`.

## Servicio externo (unidad 4)

**Historial de vacunación**, que su sistema consume pero no controla:

```http
GET /historial/{documento}?vacuna={vacuna}
```

Respuesta de ejemplo:

```json
{ "dosis": ["2025-11-03", "2026-05-10"] }
```

Si el servicio no responde o responde con error, el resultado es `EXTERNAL_UNAVAILABLE`.

## API REST (unidades 4 y 5)

- `POST /citas`: registra la solicitud y devuelve el resultado.
- `GET /citas?documento={texto}`: búsqueda por un campo de texto.

Los códigos HTTP son los comunes a todos los casos (ver [el índice](README.md#contrato-común-a-todos-los-casos)).

## SLO de partida (unidad 5)

p95 < 300 ms y tasa de error < 1 % durante la apertura de agenda: pico de 200 usuarios en el primer minuto.

Es un punto de partida. El equipo debe justificarlo o ajustarlo con datos, y documentar la decisión.

## Supuestos que el equipo debe resolver

Estas preguntas no tienen respuesta en la ficha **a propósito**: en un proyecto real tampoco la tendrían. Decidan, documenten la decisión en el Wiki y escriban las pruebas que la fijan.

1. ¿La edad se calcula a la fecha de hoy o a la fecha de la cita?
2. "De 9 a 17 años": ¿una persona de 17 años y 11 meses todavía puede?
