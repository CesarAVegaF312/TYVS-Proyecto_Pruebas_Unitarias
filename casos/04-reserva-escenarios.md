# Caso 04: Reserva de escenarios deportivos

> Caso **ficticio y simplificado** para el curso. Las reglas son inventadas y no describen ninguna entidad real.

## Contexto

El instituto de deportes de una ciudad alquila canchas, coliseos y piscinas por horas. Las reservas para la semana se abren los lunes a las 7:00 y en pocos minutos se agotan las franjas de la tarde.

## Entidad: `Reserva`

| Atributo | Tipo | Descripción |
|---|---|---|
| `documento` | texto | Solo dígitos, entre 8 y 10. |
| `escenario` | enumerado | `CANCHA_FUTBOL`, `COLISEO` o `PISCINA`. |
| `inicio` | fecha y hora | Formato ISO `AAAA-MM-DDTHH:00`; las reservas empiezan en hora exacta. |
| `duracionHoras` | entero | Duración de la reserva. |
| `asistentes` | entero | Número de personas. |

## Reglas de negocio (unidad 3)

Se evalúan **en este orden**; la primera que se cumpla determina el resultado. Si no se cumple ninguna, el resultado es `CONFIRMED`.

| # | Condición de rechazo | Resultado |
|---|---|---|
| R1 | Algún campo obligatorio falta o no cumple su formato. | `INVALID` |
| R2 | La reserva no cabe en el horario de servicio, de 06:00 a 22:00 (debe terminar a más tardar a las 22:00). | `OUT_OF_HOURS` |
| R3 | La duración no es válida: de 1 a 2 horas, salvo `PISCINA`, que es exactamente 1 hora. | `INVALID_DURATION` |
| R4 | La reserva se pide con menos de 2 horas o con más de 14 días de anticipación. | `ADVANCE_NOT_ALLOWED` |
| R5 | Los asistentes superan la capacidad: 22 en `CANCHA_FUTBOL`, 40 en `COLISEO`, 15 en `PISCINA`. | `CAPACITY_EXCEEDED` |
| R6 | La persona tiene una sanción vigente (servicio externo). | `SANCTIONED` |

## Persistencia y unicidad (unidad 4)

Un escenario no puede tener dos reservas cuyas franjas se solapen. Si se intenta, el resultado es `SLOT_TAKEN`.

## Servicio externo (unidad 4)

**Registro de sanciones**, que su sistema consume pero no controla:

```http
GET /sanciones/{documento}
```

Respuesta de ejemplo:

```json
{ "sancionado": true, "hasta": "2026-11-15" }
```

Si el servicio no responde o responde con error, el resultado es `EXTERNAL_UNAVAILABLE`.

## API REST (unidades 4 y 5)

- `POST /reservas`: registra la solicitud y devuelve el resultado.
- `GET /reservas?documento={texto}`: búsqueda por un campo de texto.

Los códigos HTTP son los comunes a todos los casos (ver [el índice](README.md#contrato-común-a-todos-los-casos)).

## SLO de partida (unidad 5)

p95 < 300 ms y tasa de error < 1 % en la apertura del lunes: pico de 250 usuarios compitiendo por las mismas franjas.

Es un punto de partida. El equipo debe justificarlo o ajustarlo con datos, y documentar la decisión.

## Supuestos que el equipo debe resolver

Estas preguntas no tienen respuesta en la ficha **a propósito**: en un proyecto real tampoco la tendrían. Decidan, documenten la decisión en el Wiki y escriban las pruebas que la fijan.

1. Una sanción que vence el mismo día de la reserva, ¿todavía aplica?
2. ¿En qué zona horaria se interpreta `inicio` si el cliente envía la hora sin zona?
