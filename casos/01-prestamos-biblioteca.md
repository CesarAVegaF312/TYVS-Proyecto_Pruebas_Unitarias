# Caso 01: Préstamos de la biblioteca

> Caso **ficticio y simplificado** para el curso. Las reglas son inventadas y no describen ninguna entidad real.

## Contexto

La biblioteca de una universidad quiere automatizar la aprobación de préstamos en el mostrador. Hoy la persona de turno revisa a mano si el usuario tiene multas, cuántos libros tiene prestados y si el ejemplar se puede sacar de la sala.

## Entidad: `Prestamo`

| Atributo | Tipo | Descripción |
|---|---|---|
| `idUsuario` | texto | Documento del usuario: solo dígitos, entre 8 y 10. |
| `tipoUsuario` | enumerado | `ESTUDIANTE` o `DOCENTE`. |
| `codigoEjemplar` | texto | Formato `EJ-` seguido de 6 dígitos, por ejemplo `EJ-004512`. |
| `tipoEjemplar` | enumerado | `GENERAL` o `REFERENCIA`. |
| `diasSolicitados` | entero | Días de préstamo pedidos. |

## Reglas de negocio (unidad 3)

Se evalúan **en este orden**; la primera que se cumpla determina el resultado. Si no se cumple ninguna, el resultado es `APPROVED`.

| # | Condición de rechazo | Resultado |
|---|---|---|
| R1 | Algún campo obligatorio falta o no cumple su formato. | `INVALID` |
| R2 | El ejemplar es de tipo `REFERENCIA` (solo consulta en sala). | `NOT_LENDABLE` |
| R3 | El usuario tiene una multa pendiente mayor que cero (servicio externo). | `BLOCKED` |
| R4 | El usuario ya tiene el máximo de préstamos activos: 3 si es estudiante, 5 si es docente (servicio externo). | `LIMIT_REACHED` |
| R5 | Los días solicitados están fuera del rango permitido: 1 a 15 para estudiantes, 1 a 30 para docentes. | `INVALID_PERIOD` |

## Persistencia y unicidad (unidad 4)

Un ejemplar no puede tener dos préstamos activos al mismo tiempo. Si se intenta, el resultado es `ALREADY_LENT`.

## Servicio externo (unidad 4)

**Paz y salvo de la biblioteca**, que su sistema consume pero no controla:

```http
GET /usuarios/{idUsuario}/estado
```

Respuesta de ejemplo:

```json
{ "multaPendiente": 0.0, "prestamosActivos": 2 }
```

Si el servicio no responde o responde con error, el resultado es `EXTERNAL_UNAVAILABLE`.

## API REST (unidades 4 y 5)

- `POST /prestamos`: registra la solicitud y devuelve el resultado.
- `GET /prestamos?usuario={texto}`: búsqueda por un campo de texto.

Los códigos HTTP son los comunes a todos los casos (ver [el índice](README.md#contrato-común-a-todos-los-casos)).

## SLO de partida (unidad 5)

p95 < 250 ms y tasa de error < 1 % con 50 usuarios concurrentes (inicio de semestre).

Es un punto de partida. El equipo debe justificarlo o ajustarlo con datos, y documentar la decisión.

## Supuestos que el equipo debe resolver

Estas preguntas no tienen respuesta en la ficha **a propósito**: en un proyecto real tampoco la tendrían. Decidan, documenten la decisión en el Wiki y escriban las pruebas que la fijan.

1. ¿El límite de préstamos activos cuenta el préstamo que se está solicitando, o solo los anteriores?
2. Una multa de $0,40 que el sistema contable redondea a $0, ¿bloquea o no?
