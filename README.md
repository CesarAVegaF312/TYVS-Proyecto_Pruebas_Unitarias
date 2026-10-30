# Proyecto Final - Pruebas Unitarias

Este proyecto corresponde al trabajo práctico sobre las **pruebas unitarias** como parte final del curso **Testing y Validación de Software**.
Cada equipo trabaja sobre el **caso asignado** del [catálogo de casos](casos/README.md) y aplica las estrategias vistas en el **Taller de Pruebas Unitarias** para diseñar, implementar y validar el módulo de negocio de ese caso mediante **pruebas automatizadas**.

## El caso del proyecto

El proyecto del curso es **un solo sistema por equipo**, construido sobre su caso, que crece unidad a unidad: aquí se construye el dominio con TDD; en el [Proyecto de Pruebas de Integración](https://github.com/CesarAVegaF312/TYVS-Proyecto_Pruebas_Integracion) se le agregan persistencia, API REST y el servicio externo; y en el [Proyecto de Pruebas de Carga y Rendimiento](https://github.com/CesarAVegaF312/TYVS-Proyecto_Pruebas_carga_y_rendimiento) se somete a carga. Todo en el **mismo repositorio**.

En esta entrega se implementan las **reglas de negocio de la ficha**, en el orden en que la ficha las define. El servicio externo es una interfaz que las pruebas reemplazan por un doble: la implementación real llega en la unidad 4.

Los talleres usan la Registraduría como ejemplo resuelto; el caso es lo que usted resuelve. Las fichas comparten la estructura de la Registraduría, así que el taller se aplica paso a paso a cualquier caso.

---

## Objetivos

- Aplicar los principios del **Desarrollo Guiado por Pruebas (TDD)** sobre las reglas del caso asignado.
- Diseñar pruebas unitarias que validen **reglas de negocio puras** (sin dependencias de bases de datos, HTTP o interfaces gráficas).
- Implementar las pruebas siguiendo el patrón **AAA (Arrange – Act – Assert)**.
- Definir y documentar **Clases de Equivalencia** y **Valores Límite**.
- Expresar los comportamientos esperados mediante **BDD (Given – When – Then)**.
- Documentar todo el proceso en un **Wiki dentro del repositorio**, siguiendo el modelo del *Taller de Pruebas Unitarias*.

---

### Estructura propuesta (monomódulo por paquetes)

- Proyecto con estructura estándar de **arquitectura limpia**:

```gherkin
  src/
   ├─ main/java/edu/unisabana/proyecto/
   │   ├─ domain/              # Reglas de negocio
   │   │   ├─ model/           # Entidades y Value Objects
   │   │   ├─ service/         # Casos de uso (lógica central)
   └─ test/java/edu/unisabana/proyecto/
       └─ domain/              # Pruebas unitarias del dominio
```

- Debe compilarse y ejecutarse con:

```bash
mvn clean test
mvn jacoco:report
```

- Las pruebas deben ejecutarse **de manera automática** sin pasos adicionales.

---

> **Nota sobre el lenguaje de implementación**
>
> El **Taller de Pruebas Unitarias** se desarrolla en **Java con Maven** como lenguaje y entorno de referencia para ilustrar los conceptos de TDD, AAA, Clases de Equivalencia, BDD y generación de cobertura con JaCoCo.
>
> No obstante, pueden implementar su **proyecto final en otro lenguaje de programación**, siempre que cumplan las siguientes condiciones:
>
> - Se mantenga la **metodología del taller**, aplicando:
>   - El ciclo **TDD (Red → Green → Refactor)**.
>   - El patrón **AAA (Arrange – Act – Assert)**.
>   - El diseño de **Clases de Equivalencia y Valores Límite**.
>   - La descripción de **escenarios BDD (Given – When – Then)**.
> - Se use una **herramienta de pruebas automatizadas** equivalente (por ejemplo: Jest/Mocha en JavaScript, PyTest en Python, NUnit en C#, etc.).
> - Se genere un **reporte de cobertura automatizada** (por ejemplo: coverage.py, Istanbul, o similar).
> - Todo el proceso se documente en el **Wiki del repositorio**, siguiendo la estructura del taller.
>
> El cambio de lenguaje **no exime** la aplicación de los conceptos ni la presentación de evidencias del proceso TDD.
> La evaluación se basará en la **metodología aplicada**, no en el lenguaje, sin embargo debe ser claro en la documentación sobre como se deben ejecutar las pruebas.

---

## PARA ENTREGAR

### 1. Repositorio principal

- Código fuente del proyecto con sus pruebas unitarias.
- Archivo `.gitignore` (excluir `target`, archivos del IDE, etc.).
- Archivo `integrantes.txt` o sección en el README con los nombres completos.
- Ejecución reproducible (Dependiendo del lenguaje):

```bash
mvn clean test
mvn jacoco:report
```

- URL pública del repositorio (GitHub, GitLab u otro).

---

### 2. Wiki del proyecto (documentación obligatoria)

> El **Wiki** será el documento oficial de entrega.
> Se recomienda seguir la estructura del **Taller de Pruebas Unitarias**.

#### Estructura sugerida del Wiki

1. **Inicio**
   - Caso asignado (enlace a su ficha) y propósito del sistema.
   - **Supuestos resueltos**: cada ambigüedad de la ficha, la decisión tomada y la prueba que la fija.
   - Integrantes del equipo.

2. **TDD (Red → Green → Refactor)**
   - Descripción del ciclo TDD aplicado.
   - Evidencias de al menos **tres iteraciones** (Rojo, Verde, Refactor).
   - Capturas, commits o breves explicaciones.

3. **Patrón AAA (Arrange – Act – Assert)**
   - Ejemplo de test con estructura AAA y breve explicación.
   - Cómo se asegura la legibilidad de las pruebas.

4. **Clases de Equivalencia y Valores Límite**
   - Tabla de casos representativos.
   - Justificación de bordes elegidos y cobertura esperada.

5. **BDD (Behavior Driven Development)**
   - Escenarios en formato **Given – When – Then**.
   - Correspondencia con las pruebas unitarias.

6. **Cobertura y Resultados**
   - Captura del **reporte JaCoCo** (o herramienta equivalente).
   - Mínimo **80% cobertura global** y **80% en el paquete de dominio**.
   - Comentario sobre líneas no cubiertas o casos no incluidos.

7. **Conclusiones y Reflexión Final**
   - Principales aprendizajes del proceso TDD.
   - Dificultades y cómo se resolvieron.
   - Beneficios de aplicar AAA y BDD.

---

## Métricas de calidad

| Métrica | Requisito mínimo |
|----------|------------------|
| **Cobertura global (JaCoCo o equivalente)** | ≥ 80% |
| **Cobertura en paquete de dominio** | ≥ 80% |
| **Número mínimo de clases de prueba** | 5 |
| **Estilo de nomenclatura** | `should...When...()` |
| **Buenas prácticas** | Código limpio, sin duplicaciones, constantes extraídas, nombres expresivos. |

---

## Reflexión esperada en el Wiki

- ¿Qué reglas de negocio fueron más desafiantes de validar con pruebas?
- ¿Cómo influyó el enfoque **TDD** en el diseño del código?
- ¿Qué aportó el patrón **AAA** a la comprensión de los tests?
- ¿Qué utilidad encontró en las **Clases de Equivalencia** y los **escenarios BDD**?
- ¿Cómo aseguraría la mantenibilidad de sus pruebas a futuro?

---

## Rúbrica de evaluación

| **Criterios de evaluación** | **Indicadores de cumplimiento** | **Excelente (5 pts)** | **Bueno (4 pts)** | **Necesita mejorar (3.5 pts)** | **Deficiente (2.5 pts)** | **No cumple (0 pts)** |
|------------------------------|---------------------------------|------------------------|--------------------|---------------------------------|---------------------------|------------------------|
| **Modelado del caso** | Fidelidad a las reglas de la ficha y tratamiento de sus ambigüedades. | Todas las reglas implementadas en el orden de la ficha, con los supuestos documentados y fijados por pruebas. | Reglas completas, supuestos documentados sin pruebas que los fijen. | Faltan reglas o se alteró su orden sin justificación. | Reglas inventadas o distintas de la ficha. | No implementa el caso asignado. |
| **Estructura y organización del código** | Implementa arquitectura limpia y buenas prácticas. | Estructura ordenada, modular y coherente. | Cumple la mayoría de principios con leves inconsistencias. | Organización parcial o sin separación clara de capas. | Estructura confusa o con dependencias innecesarias. | No presenta estructura funcional. |
| **Aplicación del ciclo TDD (Red → Green → Refactor)** | Evidencia de desarrollo iterativo basado en pruebas. | Documenta al menos 3 iteraciones claras con resultados. | Se evidencian ciclos parciales o incompletos. | TDD se menciona pero no se demuestra claramente. | Sin evidencia práctica de TDD. | No aplica TDD. |
| **Patrón AAA (Arrange–Act–Assert)** | Claridad y consistencia en los tests. | Todos los tests aplican AAA correctamente. | Mayoría de los tests con AAA consistente. | Estructura irregular o confusa. | Solo algunos tests aplican AAA. | No se aplica AAA. |
| **Clases de equivalencia y valores límite** | Definición y aplicación de casos representativos. | Tabla completa y justificada, reflejada en los tests. | Casos correctos pero sin justificación detallada. | Tabla parcial o confusa. | Casos incompletos o erróneos. | No se aplica la técnica. |
| **Escenarios BDD (Given–When–Then)** | Coherencia entre escenarios narrativos y pruebas. | Escenarios claros, completos y coherentes. | Escenarios bien planteados pero con faltantes menores. | Redacción ambigua o poco estructurada. | Escenarios mal formulados o inconsistentes. | No aplica BDD. |
| **Cobertura de código (JaCoCo o equivalente)** | Porcentaje de código probado. | ≥ 80% global y en dominio. | Entre 70% y 79%. | Entre 60% y 69%. | Menor a 60%. | No presenta cobertura. |
| **Documentación en Wiki** | Wiki como documento oficial de entrega. | Completo, estructurado y con evidencias claras. | Incluye secciones clave pero sin suficiente detalle. | Incompleto o poco organizado. | Parcial o sin evidencia visual. | No presenta Wiki. |
| **Reflexión técnica y conclusiones** | Análisis del proceso y aprendizajes. | Reflexión profunda y analítica sobre TDD, AAA y BDD. | Reflexión general con ejemplos. | Comentarios superficiales o incompletos. | Reflexión mínima o incoherente. | Sin reflexión. |
| **Calidad general y mantenibilidad** | Código, documentación y presentación global. | Excelente claridad, orden y consistencia técnica. | Buen nivel general con leves omisiones. | Correcto pero sin cohesión entre partes. | Deficiente o sin conexión entre código y documentación. | Proyecto incompleto o inejecutable. |

| Rango de puntaje | Desempeño                                                |
| ---------------- | -------------------------------------------------------- |
| 45 – 50          | Excelente dominio técnico y metodológico.                |
| 35 – 44          | Buen trabajo con documentación o cobertura parcial.      |
| 30 – 34          | Cumple con lo básico pero sin profundidad.               |
| < 30             | No cumple con los criterios mínimos del taller/proyecto. |

---

## Referencias

- Myers, G. J., *The Art of Software Testing* (3rd ed.).
- Koskela, L., *Effective Unit Testing*.
- Martin, R. C., *Clean Architecture*.
- Documentación oficial: *JUnit 5*, *JaCoCo*, *BDD Gherkin Syntax*.

---

> **Nota final:**
> Este proyecto es una **extensión práctica del Taller de Pruebas Unitarias**.
> Se espera que los estudiantes **repliquen la metodología** aplicada en el taller sobre su caso asignado.
> Documentar todo el proceso en el **Wiki** del repositorio y demostrando el uso correcto de **TDD, AAA, Clases de Equivalencia y BDD**.

---

## Créditos y uso académico

**Autor:** César Augusto Vega Fernández
**Curso:** Testing y Validación de Software
**Programa:** Maestría en Ingeniería de Software – Universidad de La Sabana
**Año:** 2025

Este taller y su contenido fueron diseñados por el profesor **César Augusto Vega Fernández** como material académico para el curso *Testing y Validación de Software*, impartido en la **Maestría en Ingeniería de Software de la Universidad de La Sabana**.

Su propósito es exclusivamente educativo y está orientado a fortalecer las competencias de los estudiantes en **TDD, AAA, Clases de Equivalencia, BDD** y validación de software en contextos de arquitectura limpia.

---

### Licencia de uso

Este material se distribuye bajo la licencia [Creative Commons Atribución-NoComercial-CompartirIgual 4.0 Internacional (CC BY-NC-SA 4.0)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es).

Puedes **usar, adaptar o compartir** este contenido con fines educativos, siempre que:

1. Se reconozca la autoría del profesor **César Augusto Vega Fernández**.
2. No se utilice con fines comerciales.
3. Las obras derivadas se distribuyan bajo la misma licencia.

---

© Universidad de La Sabana – Facultad de Ingeniería
Maestría en Ingeniería de Software – 2025
