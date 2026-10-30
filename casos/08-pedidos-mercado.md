# Caso 08: Pedidos de mercado a domicilio

> Caso **ficticio y simplificado** para el curso. Las reglas son inventadas y no describen ninguna entidad real.

## Contexto

Una cadena de supermercados de barrio recibe pedidos por una aplicación. Los viernes de quincena el volumen se triplica y la aplicación de los clientes reintenta los pedidos que no recibieron respuesta.

## Entidad: `Pedido`

| Atributo | Tipo | Descripción |
|---|---|---|
| `idPedido` | texto | UUID generado por la aplicación cliente; sirve como clave de idempotencia. |
| `cliente` | texto | Documento: solo dígitos, entre 8 y 10. |
| `zona` | texto | Código de zona de entrega, por ejemplo `Z-07`. |
| `items` | lista | Pares `{ sku, cantidad }`; al menos uno. |
| `hora` | hora | Hora local del pedido, `HH:MM`. |

## Reglas de negocio (unidad 3)

Se evalúan **en este orden**; la primera que se cumpla determina el resultado. Si no se cumple ninguna, el resultado es `ACCEPTED`.

| # | Condición de rechazo | Resultado |
|---|---|---|
| R1 | Algún campo obligatorio falta o no cumple su formato. | `INVALID` |
| R2 | La zona no está entre las cubiertas: `Z-01` a `Z-12`. | `OUT_OF_COVERAGE` |
| R3 | El pedido llega fuera del horario de 07:00 a 21:00. | `CLOSED` |
| R4 | El valor del pedido, calculado por el servidor con los precios del inventario, es menor que $30.000. | `BELOW_MINIMUM` |
| R5 | Algún producto supera las 10 unidades. | `MAX_UNITS` |
| R6 | El inventario no tiene unidades suficientes de algún producto (servicio externo). | `OUT_OF_STOCK` |

## Persistencia y unicidad (unidad 4)

Un mismo `idPedido` no puede registrarse dos veces. Si se intenta, el resultado es `DUPLICATED`.

## Servicio externo (unidad 4)

**Inventario**, que su sistema consume pero no controla:

```http
POST /inventario/consultas
```

Respuesta de ejemplo:

```json
{ "items": [{ "sku": "LEC-001", "disponible": 12, "precio": 4200 }] }
```

Si el servicio no responde o responde con error, el resultado es `EXTERNAL_UNAVAILABLE`.

## API REST (unidades 4 y 5)

- `POST /pedidos`: registra la solicitud y devuelve el resultado.
- `GET /pedidos?cliente={texto}`: búsqueda por un campo de texto.

Los códigos HTTP son los comunes a todos los casos (ver [el índice](README.md#contrato-común-a-todos-los-casos)).

## SLO de partida (unidad 5)

p95 < 350 ms y tasa de error < 1 % un viernes de quincena: el triple de la carga normal (100 usuarios) durante 1 hora.

Es un punto de partida. El equipo debe justificarlo o ajustarlo con datos, y documentar la decisión.

## Supuestos que el equipo debe resolver

Estas preguntas no tienen respuesta en la ficha **a propósito**: en un proyecto real tampoco la tendrían. Decidan, documenten la decisión en el Wiki y escriban las pruebas que la fijan.

1. La aplicación reintenta con el mismo `idPedido` porque no recibió respuesta: ¿se responde `DUPLICATED` (409) o se devuelve el resultado del pedido original?
2. Un pedido que llega a las 21:00:30, ¿está dentro del horario?
