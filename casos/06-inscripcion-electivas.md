# Caso 06: Inscripción a electivas

> Caso **ficticio y simplificado** para el curso. Las reglas son inventadas y no describen ninguna entidad real.

## Contexto

Una universidad abre la inscripción de electivas a la misma hora para todos los estudiantes. Cada semestre el sistema se cae en los primeros minutos y algunos cursos quedan con más inscritos que cupos.

## Entidad: `Inscripcion`

| Atributo | Tipo | Descripción |
|---|---|---|
| `codigoEstudiante` | texto | Formato libre de 6 a 12 caracteres alfanuméricos. |
| `codigoCurso` | texto | Formato `ELE-` seguido de 3 dígitos. |
| `creditosCurso` | entero | Créditos de la electiva, de 1 a 4. |
| `franja` | texto | Día y bloque, por ejemplo `MAR-14` (martes, 14:00). |

## Reglas de negocio (unidad 3)

Se evalúan **en este orden**; la primera que se cumpla determina el resultado. Si no se cumple ninguna, el resultado es `ENROLLED`.

| # | Condición de rechazo | Resultado |
|---|---|---|
| R1 | Algún campo obligatorio falta o no cumple su formato. | `INVALID` |
| R2 | El estudiante tiene un bloqueo financiero (servicio externo). | `FINANCIAL_HOLD` |
| R3 | El estudiante no ha aprobado el prerrequisito del curso (servicio externo). | `MISSING_PREREQUISITE` |
| R4 | Con este curso superaría el máximo de créditos: 20, o 22 si su promedio es 4,0 o más (servicio externo). | `CREDIT_LIMIT` |
| R5 | El curso se cruza con la franja de otra electiva ya inscrita. | `SCHEDULE_CONFLICT` |
| R6 | El curso no tiene cupos disponibles. | `NO_SEATS` |

## Datos de referencia

El sistema guarda una tabla de cursos con `codigoCurso`, `cupo` y `prerrequisito` (opcional, otro `codigoCurso`). Se carga con datos semilla al iniciar; el equipo define al menos 5 cursos, entre ellos uno sin prerrequisito y uno con un solo cupo.

## Persistencia y unicidad (unidad 4)

Un estudiante no puede inscribir dos veces el mismo curso. Si se intenta, el resultado es `DUPLICATED`.

## Servicio externo (unidad 4)

**Sistema académico**, que su sistema consume pero no controla:

```http
GET /estudiantes/{codigoEstudiante}/academico
```

Respuesta de ejemplo:

```json
{ "bloqueoFinanciero": false, "promedio": 4.1, "creditosInscritos": 16, "cursosAprobados": ["ELE-101"] }
```

Si el servicio no responde o responde con error, el resultado es `EXTERNAL_UNAVAILABLE`.

## API REST (unidades 4 y 5)

- `POST /inscripciones`: registra la solicitud y devuelve el resultado.
- `GET /inscripciones?estudiante={texto}`: búsqueda por un campo de texto.

Los códigos HTTP son los comunes a todos los casos (ver [el índice](README.md#contrato-común-a-todos-los-casos)).

## SLO de partida (unidad 5)

p95 < 500 ms y tasa de error < 2 % en la apertura: 500 usuarios en 5 minutos (prueba de estrés). Ningún curso puede terminar con más inscritos que cupos.

Es un punto de partida. El equipo debe justificarlo o ajustarlo con datos, y documentar la decisión.

## Supuestos que el equipo debe resolver

Estas preguntas no tienen respuesta en la ficha **a propósito**: en un proyecto real tampoco la tendrían. Decidan, documenten la decisión en el Wiki y escriban las pruebas que la fijan.

1. Dos estudiantes piden el último cupo en el mismo instante: ¿quién se queda con él y cómo lo garantiza el sistema?
2. Un promedio de 3,995 que el sistema académico muestra como 4,0, ¿habilita los 22 créditos?
