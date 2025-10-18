## README - Taller: Relaciones en JPA con Spring Data - Tienda Online

**Creador:** Jefferson Andrés Rueda

### 1. Objetivo del Taller

Modelar y probar relaciones `@OneToOne`, `@OneToMany`/`@ManyToOne` y `@ManyToMany` (con y sin entidad intermedia). El proyecto debe usar Spring Boot + Spring Data JPA, poniendo especial atención en la configuración de cascadas, `orphanRemoval`, la estrategia de *fetch* y la serialización JSON.

El dominio de este ejercicio es una **Tienda Online**.

### 2. Dominio y Requisitos de Relaciones

El modelo propuesto se basa en las siguientes entidades:

| Entidad | Relaciones | Requisitos de JPA y Mapeo |
| :--- | :--- | :--- |
| **Cliente** | 1:1 con `Direccion`, N:1 con `Pedido` | En `Cliente`, mapear `Direccion` como `@OneToOne(mappedBy=...)`. |
| **Direccion** | 1:1 con `Cliente` | `Direccion` posee la FK (`cliente_id`). Configuración: `cascade=ALL`, `orphanRemoval=true`, `fetch=LAZY`. Debe permitir crear `Cliente` con su `Direccion` en una sola operación. |
| **Pedido** | N:1 con `Cliente` | `Pedido` tiene FK `cliente_id`. En `Cliente`, usar `@OneToMany(mappedBy='cliente', cascade=ALL, orphanRemoval=true, fetch=LAZY)`. Al eliminar un `Cliente`, deben eliminarse sus `Pedidos` (`orphan Removal`). |
| **Producto** | N:M con `Categoria` (puro) | N:M con `Categoria`: Usar `@Many ToMany` con `JoinTable` (`producto_categoria`). Evitar `EAGER` por defecto. |
| **ItemPedido** | Entidad intermedia N:M entre `Pedido` y `Producto` | Tiene `@ManyToOne` a `Pedido` y `@ManyToOne` a `Producto`. Incluye atributos propios (`cantidad`, `precio Unitario`). Definir `unique constraint` (`pedido_id`, `producto_id`). |
| **Categoria** | N:M con `Producto` (puro) | Poner *unique* en `Categoria.nombre`. |

**Serialización JSON:** Usar DTOs o `@EntityGraph` para evitar acceder a *proxies* `LAZY`. Evitar ciclos usando DTOs o `@Jsonlgnore` en colecciones.

### 3. Reglas de Negocio (Servicio) - Ejemplos

* **Crear Pedido:**
    * Validar *stock* de cada `Producto`.
    * Decrementar *stock*.
    * Calcular `total` sumando `items.cantidad * items.precio Unitario`.
    * Evitar ítems duplicados en un mismo pedido (usando la *constraint* `uk_pedido_producto`).
* **Cancelar Pedido:** Revertir el *stock* si el estado lo permite (p.ej., `NUEVO` → `CANCELADO`).

### 4. Repositorios y Consultas Sugeridas

* **Producto Repository:** `findByCategorias_Nombre(String nombre)
  ### 6. Cómo Correr el Proyecto (con H2 por defecto)

1.  **Clonar el Repositorio:** [Aquí se indicarían los comandos de Git]
2.  **Configuración de Spring Boot:** El proyecto está configurado para usar la base de datos en memoria **H2** por defecto, lo que facilita el inicio rápido.
3.  **Ejecutar la Aplicación:** Ejecutar la clase principal de Spring Boot. Por ejemplo, usando Maven o Gradle:
    ```bash
    # Con Maven
    ./mvnw spring-boot:run
    
    # Con Gradle
    ./gradlew bootRun
    ```
