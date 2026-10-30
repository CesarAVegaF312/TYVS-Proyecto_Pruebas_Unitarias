# Caso 03: Subsidio de transporte estudiantil

> Caso **ficticio y simplificado** para el curso. Las reglas son inventadas y no describen ninguna entidad real.

## Contexto

Una alcaldía otorga un subsidio de transporte a estudiantes de bajos ingresos. La convocatoria dura una semana y casi todas las solicitudes llegan el último día.

## Entidad: `Solicitud`

| Atributo | Tipo | Descripción |
|---|---|---|
| `documento` | texto | Solo dígitos, entre 8 y 10. |
| `codigoEstudiante` | texto | Formato libre de 6 a 12 caracteres alfanuméricos. |
| `edad` | entero | Años cumplidos. |
| `estrato` | entero | Estrato socioeconómico, de 1 a 6. |
| `ingresoHogarSmmlv` | decimal | Ingreso mensual del hogar expresado en salarios mínimos, con dos decimales. |

## Reglas de negocio (unidad 3)

Se evalúan **en este orden**; la primera que se cumpla determina el resultado. Si no se cumple ninguna, el resultado es `ELIGIBLE`.

| # | Condición de rechazo | Resultado |
|---|---|---|
| R1 | Algún campo obligatorio falta o no cumple su formato. | `INVALID` |
| R2 | La edad está fuera del rango de 16 a 28 años. | `AGE_OUT_OF_RANGE` |
| R3 | El estrato es mayor que 3. | `STRATUM_NOT_ELIGIBLE` |
| R4 | El ingreso supera el tope: 4 salarios mínimos en estratos 1 y 2, 3 salarios mínimos en estrato 3. | `OVER_INCOME` |
| R5 | La matrícula del estudiante no está vigente (servicio externo). | `NOT_ENROLLED` |

## Persistencia y unicidad (unidad 4)

Una persona solo puede tener una solicitud por convocatoria. Si se intenta, el resultado es `DUPLICATED`.

## Servicio externo (unidad 4)

**Verificación de matrícula**, que su sistema consume pero no controla:

```http
GET /matriculas/{codigoEstudiante}
```

Respuesta de ejemplo:

```json
{ "vigente": true, "periodo": "2026-2" }
```

Si el servicio no responde o responde con error, el resultado es `EXTERNAL_UNAVAILABLE`.

## API REST (unidades 4 y 5)

- `POST /solicitudes`: registra la solicitud y devuelve el resultado.
- `GET /solicitudes?documento={texto}`: búsqueda por un campo de texto.

Los códigos HTTP son los comunes a todos los casos (ver [el índice](README.md#contrato-común-a-todos-los-casos)).

## SLO de partida (unidad 5)

p95 < 400 ms y tasa de error < 1 % el día de cierre: carga sostenida de 300 usuarios durante 30 minutos.

Es un punto de partida. El equipo debe justificarlo o ajustarlo con datos, y documentar la decisión.

## Supuestos que el equipo debe resolver

Estas preguntas no tienen respuesta en la ficha **a propósito**: en un proyecto real tampoco la tendrían. Decidan, documenten la decisión en el Wiki y escriban las pruebas que la fijan.

1. Un ingreso de exactamente 4,00 salarios mínimos en estrato 2, ¿supera el tope?
2. Si el servicio de matrícula responde `vigente: true` para otro periodo, ¿la matrícula está vigente?
