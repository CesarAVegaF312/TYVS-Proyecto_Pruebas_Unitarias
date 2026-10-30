# Caso 05: Donación de sangre

> Caso **ficticio y simplificado** para el curso. Las reglas son inventadas y no describen ninguna entidad real.

## Contexto

Un banco de sangre organiza jornadas de donación en universidades. Quiere que el registro previo descarte en el momento a quien no puede donar, para no hacerle esperar la entrevista.

## Entidad: `Donacion`

| Atributo | Tipo | Descripción |
|---|---|---|
| `documento` | texto | Solo dígitos, entre 6 y 10. |
| `edad` | entero | Años cumplidos. |
| `pesoKg` | decimal | Peso en kilogramos, con un decimal. |
| `sexo` | enumerado | `F` o `M` (solo para los umbrales clínicos de esta ficha). |
| `hemoglobina` | decimal | g/dL, con un decimal. |
| `mesesDesdeUltimoTatuaje` | entero | Opcional: vacío si nunca se ha tatuado. |

## Reglas de negocio (unidad 3)

Se evalúan **en este orden**; la primera que se cumpla determina el resultado. Si no se cumple ninguna, el resultado es `ACCEPTED`.

| # | Condición de rechazo | Resultado |
|---|---|---|
| R1 | Algún campo obligatorio falta o no cumple su formato. | `INVALID` |
| R2 | La edad está fuera del rango de 18 a 65 años. | `AGE_NOT_ALLOWED` |
| R3 | El peso es menor que 50 kg. | `UNDERWEIGHT` |
| R4 | La hemoglobina está por debajo del mínimo: 12,5 g/dL para `F`, 13,5 g/dL para `M`. | `LOW_HEMOGLOBIN` |
| R5 | Se tatuó hace menos de 12 meses. | `DEFERRED` |
| R6 | No ha pasado el intervalo desde la última donación: 120 días para `F`, 90 días para `M` (servicio externo). | `TOO_SOON` |

## Persistencia y unicidad (unidad 4)

Una persona no puede registrar dos donaciones el mismo día. Si se intenta, el resultado es `DUPLICATED`.

## Servicio externo (unidad 4)

**Historial de donantes**, que su sistema consume pero no controla:

```http
GET /donantes/{documento}/ultima-donacion
```

Respuesta de ejemplo:

```json
{ "fecha": "2026-07-20" }
```

Si el servicio no responde o responde con error, el resultado es `EXTERNAL_UNAVAILABLE`.

## API REST (unidades 4 y 5)

- `POST /donaciones`: registra la solicitud y devuelve el resultado.
- `GET /donaciones?documento={texto}`: búsqueda por un campo de texto.

Los códigos HTTP son los comunes a todos los casos (ver [el índice](README.md#contrato-común-a-todos-los-casos)).

## SLO de partida (unidad 5)

p95 < 300 ms y tasa de error < 1 % en una jornada: 100 usuarios concurrentes durante 2 horas (prueba de resistencia).

Es un punto de partida. El equipo debe justificarlo o ajustarlo con datos, y documentar la decisión.

## Supuestos que el equipo debe resolver

Estas preguntas no tienen respuesta en la ficha **a propósito**: en un proyecto real tampoco la tendrían. Decidan, documenten la decisión en el Wiki y escriban las pruebas que la fijan.

1. Una hemoglobina medida con dos decimales (13,49), ¿se redondea o se rechaza por formato?
2. Si el historial no tiene ninguna donación, ¿responde `fecha: null`, un 404 o una lista vacía? ¿Cómo lo maneja el sistema?
