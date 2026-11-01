# Catálogo de casos del proyecto

El proyecto del curso se desarrolla sobre **un caso asignado a cada equipo**. Un solo sistema que crece unidad a unidad: se construye con TDD en la unidad 3, se integra en la 4 y se somete a carga en la 5.

Los talleres enseñan cada técnica sobre la **Registraduría**, que es el ejemplo resuelto. El proyecto aplica esas técnicas a un caso que nadie ha resuelto por usted.

## Por qué casos distintos con la misma forma

Todos los casos tienen **las mismas piezas** que la Registraduría. Por eso cada guía se aplica tal cual a cualquier caso, y por eso la solución de un equipo no le sirve a otro: los límites, los valores frontera y las reglas son diferentes.

| Pieza | En la Registraduría | Unidad y taller donde se usa |
|---|---|---|
| Entidad con 4 a 6 atributos | `Person` | 3, Pruebas Unitarias |
| 5 o 6 reglas ordenadas que devuelven un resultado enumerado | `VALID`, `UNDERAGE`, `DEAD`… | 3, Pruebas Unitarias (TDD, clases de equivalencia) |
| Restricción de unicidad persistida | Documento duplicado | 4, Pruebas de Integración (H2, Testcontainers) |
| Servicio externo del que depende | Certificados | 4, Pruebas de Integración (dobles de prueba, Pact) |
| Endpoint de búsqueda por texto | `/buscar` | 4, Pruebas de Seguridad (casos de abuso, SAST) |
| SLO de negocio | p95 < 300 ms | 5, Pruebas de Carga |

## Casos

| # | Caso | Entidad | Reglas | Servicio externo |
|---|---|---|---|---|
| 01 | [Préstamos de la biblioteca](01-prestamos-biblioteca.md) | `Prestamo` | 5 + unicidad | Paz y salvo de la biblioteca |
| 02 | [Citas de vacunación](02-citas-vacunacion.md) | `Cita` | 6 + unicidad | Historial de vacunación |
| 03 | [Subsidio de transporte estudiantil](03-subsidio-transporte.md) | `Solicitud` | 5 + unicidad | Verificación de matrícula |
| 04 | [Reserva de escenarios deportivos](04-reserva-escenarios.md) | `Reserva` | 6 + unicidad | Registro de sanciones |
| 05 | [Donación de sangre](05-donacion-sangre.md) | `Donacion` | 6 + unicidad | Historial de donantes |
| 06 | [Inscripción a electivas](06-inscripcion-electivas.md) | `Inscripcion` | 6 + unicidad | Sistema académico |
| 07 | [Solicitud de licencia de conducción](07-licencia-conduccion.md) | `SolicitudLicencia` | 6 + unicidad | Registro de conductores |
| 08 | [Pedidos de mercado a domicilio](08-pedidos-mercado.md) | `Pedido` | 6 + unicidad | Inventario |

## Asignación

- El profesor asigna **un caso por equipo**. Dos equipos de la misma cohorte no comparten caso.
- Las reglas de la ficha **no se cambian**. Si encuentran una ambigüedad, no la resuelvan en silencio: es exactamente la diferencia entre *verificar* (¿lo construimos bien?) y *validar* (¿construimos lo correcto?). Documenten el supuesto en el Wiki y escriban la prueba que lo fija. Cada ficha trae dos supuestos abiertos a propósito.

## Contrato común a todos los casos

Para que las pruebas de sistema y de carga se diseñen igual en todos los casos:

| Situación | Código HTTP | Cuerpo |
|---|---|---|
| Aprobado (`APPROVED`, `SCHEDULED`, `ELIGIBLE`…) | `201 Created` | `{ "resultado": "<RESULTADO>" }` |
| Datos inválidos (`INVALID`) | `400 Bad Request` | `{ "resultado": "INVALID", "detalle": "..." }` |
| Rechazo por una regla de negocio | `422 Unprocessable Entity` | `{ "resultado": "<RESULTADO>" }` |
| Unicidad violada | `409 Conflict` | `{ "resultado": "<RESULTADO>" }` |
| Servicio externo caído | `503 Service Unavailable` | `{ "resultado": "EXTERNAL_UNAVAILABLE" }` |

Ninguna entrada, por malformada que sea, puede producir un `500`.

## Qué se entrega con el caso en cada unidad

| Unidad | Entrega del proyecto | Guía |
|---|---|---|
| 1 | Caso asignado, hoja de ruta y enfoque (ágil o cascada) | Secuencia didáctica |
| 3 | Dominio y reglas con TDD | [Proyecto de Pruebas Unitarias](../README.md) |
| 4 | Persistencia, API REST, servicio externo y pipeline de CI | [Proyecto de Pruebas de Integración](https://github.com/CesarAVegaF312/TYVS-Proyecto_Pruebas_Integracion) |
| 5 | Escenarios de carga y SLO | [Proyecto de Pruebas de Carga y Rendimiento](https://github.com/CesarAVegaF312/TYVS-Proyecto_Pruebas_carga_y_rendimiento) |

Todas las entregas se hacen **sobre el mismo repositorio** del equipo.

## El servicio externo en cada unidad

- **Unidad 3:** el dominio debe ser puro, sin HTTP ni base de datos. El servicio externo es una **interfaz** (un puerto) que el dominio recibe; las pruebas la reemplazan por un doble que devuelve lo que cada escenario necesita.
- **Unidad 4:** se implementa el adaptador HTTP real de esa interfaz, y se prueba su contrato.
