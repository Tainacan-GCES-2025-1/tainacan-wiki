# Arquitectura Técnica

Este documento consolida la arquitectura técnica de Tainacan, detallando su estructura como un plugin de WordPress.

Los temas cubren todo, desde el funcionamiento interno del plugin (entidades, repositorios, controladores y lógica de negocio), pasando por la REST API pública y sus funcionalidades, hasta la modelación y manipulación de datos en la base de datos relacional. También se describen soluciones para importación/exportación de datos y procesos asíncronos.

## 1. Backend del Plugin Tainacan

Tainacan se desarrolla como un plugin de WordPress, aprovechando su estructura nativa y extendiéndola con entidades personalizadas para colecciones digitales. El backend se divide en capas:

* **Entidades**: Representaciones de objetos de negocio (colecciones, ítems, metadatos, etc.).
* **Repositorios**: Responsables de abstraer el acceso a la base de datos.
* **Controladores**: Manejan las solicitudes recibidas vía REST API.

Hay otras partes del código que utilizan funcionalidades relacionadas con el consumo y manipulación de datos, pero estas tres son las más relevantes.

### 1.1 Entidades y Repositorios Principales

La siguiente tabla muestra las principales entidades del sistema, sus respectivos repositorios y la ubicación de los archivos de implementación. Cada entidad tiene un repositorio dedicado que gestiona sus operaciones de persistencia y recuperación.

| Entidad                            | Repositorio                                | Ubicación del Código                      |
| ---------------------------------- | ------------------------------------------ | ----------------------------------------- |
| Collection (`tainacan-collection`) | `Tainacan\Repositories\Collections`        | `class-tainacan-collections.php`          |
| Item (`tainacan-item`)             | `Tainacan\Repositories\Items`              | `class-tainacan-items.php`                |
| Metadatum (`tainacan-metadatum`)   | `Tainacan\Repositories\Metadata`           | `class-tainacan-metadata.php`             |
| Metadata Section                   | `Tainacan\Repositories\Metadata_Sections`  | `class-tainacan-metadata-sections.php`    |
| Filter                             | `Tainacan\Repositories\Filters`            | `class-tainacan-filters.php`              |
| Taxonomy                           | `Tainacan\Repositories\Taxonomies`         | `class-tainacan-taxonomies.php`           |
| Term                               | `Tainacan\Repositories\Terms`              | `class-tainacan-terms.php`                |

### 1.2 Ubicaciones Detalladas del Código de Entidades

| Entidad           | Archivo (Ruta)                                                |
| ----------------- | ------------------------------------------------------------- |
| Collection        | `src/classes/entities/class-tainacan-collection.php`          |
| Item              | `src/classes/entities/class-tainacan-item.php`                |
| Metadatum         | `src/classes/entities/class-tainacan-metadatum.php`           |
| Metadata Section  | `src/classes/entities/class-tainacan-metadata-section.php`    |
| Filter            | `src/classes/entities/class-tainacan-filter.php`              |
| Taxonomy          | `src/classes/entities/class-tainacan-taxonomy.php`            |
| Term              | `src/classes/entities/class-tainacan-term.php`                |

## 2. REST API

La REST API de Tainacan ofrece un conjunto completo de endpoints para gestionar las entidades del sistema. Está construida sobre el estándar REST de WordPress, extendiéndolo con controladores personalizados para cada recurso.

### 2.1 Recursos de la API

* CRUD para colecciones, ítems, metadatos, filtros, taxonomías
* Operaciones en lote y edición múltiple de ítems
* Sesiones de importación/exportación
* Búsqueda facetada y recuperación de facetas

### 2.2 Documentación Detallada

La especificación completa de la REST API con todos los endpoints, parámetros y ejemplos está disponible en:

📄 [Documentación REST API](https://redocly.github.io/redoc/?url=https://github.com/tainacan/tainacan-wiki/raw/master/dev/openapi.json#tag/items)

### 2.3 Controladores REST Específicos

La REST API de Tainacan está estructurada a través de controladores PHP especializados, cada uno responsable por un recurso. Extienden una clase base llamada `REST_Controller` y definen sus propios endpoints. Ejemplos:

* `REST_Collections_Controller`: Gestiona colecciones (`/collections`)
* `REST_Items_Controller`: Gestiona ítems (`/items`)
* `REST_Exposers_Controller`: Gestiona formatos de exposición (`/exposers`)
* `REST_Background_Processes_Controller`: Monitorea procesos asíncronos (`/bg-processes`)
* `REST_BulkEdit_Controller`: Maneja edición en lote de ítems (`/collection/{id}/bulk-edit`)

### 2.4 Autenticación

La autenticación en la REST API de Tainacan sigue los estándares de WordPress. En entornos autenticados (como el panel de administración), se utilizan cookies de sesión. Para aplicaciones externas, se recomienda utilizar los [métodos de autenticación REST API de WordPress](https://developer.wordpress.org/rest-api/using-the-rest-api/authentication/#basic-authentication-with-application-passwords).

### 2.5 Ejemplo de Uso de la API

Listar ítems de la colección con ID 1:

```bash
curl -X GET "https://yourdomain.com/wp-json/tainacan/v2/collection/1/items"
```

## 3. Integración Frontend ⇄ Backend ⇄ Base de Datos

El panel administrativo de Tainacan está desarrollado principalmente como una Single Page Application, donde toda la obtención y manipulación de datos se realiza a través de la REST API. Ciclos de obtención similares también ocurren dentro de los Bloques Gutenberg, como la Búsqueda Facetada y el Formulario de Envío de Ítem.

### 3.1 Ciclo de Solicitud

1. El frontend (SPA en Vue.js) envía solicitudes HTTP mediante `Axios`.
2. Los controladores REST reciben y validan los datos.
3. Los datos se convierten en entidades de dominio.
4. Los repositorios gestionan la persistencia.
5. Se devuelve una respuesta JSON formateada al frontend.

## 4. Arquitectura de Datos

La arquitectura de datos de Tainacan se basa en la infraestructura de WordPress, utilizando y extendiendo su modelo relacional. El sistema incorpora Custom Post Types, Custom Taxonomies, Custom Post Meta y tablas adicionales para operaciones asíncronas.

### 4.1 Estructura de Almacenamiento

| Componente                   | Tabla(s) de WordPress              | Notas Técnicas                   |
| --------------------------- | ---------------------------------- | -------------------------------- |
| Colecciones, Ítems, Metadatos | `wp_posts`                        | Identificados por `post_type`   |
| Valores de metadatos y términos | `wp_postmeta`, `wp_termmeta`       | Pares clave-valor para datos adicionales |
| Taxonomías y Términos       | `wp_terms`, `wp_term_taxonomy`, `wp_term_relationships` | Usados para categorización       |
| Procesos Asíncronos         | `wp_tnc_bg_process`                | Importación, exportación, edición masiva, etc. |

### 4.2 Relaciones de Dominio

* Una colección contiene múltiples metadatos y secciones.
* Un ítem pertenece a una única colección y puede clasificarse por términos de diferentes taxonomías.
* Los metadatos de tipo taxonomía actúan como puente hacia los términos.
* Los usuarios tienen permisos diferenciados mediante roles y capacidades de WordPress.

### 4.3 Manipulación de Datos y Acceso a la Base de Datos

La manipulación de datos en el plugin Tainacan se realiza mediante una combinación de funciones nativas de WordPress y acceso directo a la base de datos cuando es necesario.

#### Funciones Nativas de WordPress

Para la mayoría de las operaciones estándar, Tainacan utiliza funciones nativas de WordPress como:

- `wp_insert_post()`
- `get_post_meta()`
- `update_post_meta()`
- `wp_insert_term()`
- Entre otras.

#### Acceso Directo a la Base con `$wpdb`

Para escenarios más complejos o al trabajar con tablas personalizadas (por ejemplo, procesos asíncronos), Tainacan usa acceso SQL directo mediante el objeto `$wpdb`:

- Tablas de ejemplo: `wp_tnc_bg_process`, `wp_tnc_log`
- Métodos de ejemplo: `$wpdb->insert()`, `$wpdb->get_results()`, `$wpdb->query()`

## 5. Sistema de Importación y Exportación

Tainacan cuenta con una estructura flexible para la importación y exportación de datos. Las operaciones se ejecutan mediante sesiones y son ejecutadas de forma asíncrona.

### 5.1 Importación

* Importadores disponibles: CSV, Flickr, YouTube, etc.
* Sesiones creadas vía `/importers/session`
* Subida de archivos, mapeo de metadatos, ejecución en segundo plano
* Clase base: `class-tainacan-importer.php`
* Gestor: `class-tainacan-importer-handler.php`

### 5.2 Exportación

* Exportadores: CSV, CSV de vocabulario.
* Sesiones personalizables
* Clase base: `class-tainacan-exporter.php`
* Ejecución asíncrona con acceso a logs/resultados vía endpoint
* Gestor: `class-tainacan-exporter-handler.php`

## 6. Arquitectura del Frontend

El frontend de Tainacan es una aplicación Vue.js 3 que proporciona una interfaz para la gestión de repositorios digitales. Está implementado como una Single Page Application (SPA), utilizando Vue.js 3, con enrutamiento gestionado por Vue Router y gestión de estado mediante Vuex. Se comunica con el backend a través de la REST API de Tainacan.

Una característica clave del frontend es la búsqueda facetada, que permite a los usuarios explorar intuitivamente la colección combinando múltiples filtros mediante componentes interactivos. Se implementa mediante una interfaz con **paneles de filtros interactivos**, que ofrecen tipos de control como casillas de verificación, campos de texto y selectores de fecha.

![Initialization and Routing Flowchart](/es-mx/dev/_assets/initialization_routing_front.png)

### 6.1 Ubicaciones de Archivos

- `/src/views/admin/` – Interfaz administrativa principal
- `/src/views/gutenberg-blocks/` – Bloques para el editor Gutenberg

### 6.2 Componentes Principales

#### 6.2.1 Página Principal

`src/views/admin/admin.vue` inicializa la app Vue y define el layout:

- Menú lateral
- Encabezado
- Área de contenido principal
- Gestión de rutas

#### 6.2.2 Sistema de Enrutamiento

Definido en `src/views/admin/js/router.js`, con Vue Router:

**Rutas principales**:

- `/home` – Inicio
- `/collections` – Colecciones
- `/items` – Ítems
- `/metadata` – Metadatos
- `/filters` – Filtros
- `/taxonomies` – Taxonomías
- `/activities` – Actividades
- `/capabilities` – Permisos
- `/importers` – Importadores
- `/exporters` – Exportadores

### 6.3 Organización

- `/src/views/admin/pages/home-page.vue`
- `/src/views/admin/pages/lists/`
- `/src/views/admin/pages/singles/`
- Componentes en `/src/views/admin/components/`:

  - `/navigation/`
  - `/edition/`
  - `/search/`
  - `/other/`

### 6.4 Gestión de Estado

Vuex se utiliza para manejar el estado:

- **Collection**
- **Item**
- **Search**
- **Filter**
- **Metadata**

## 7. Referencias y Contribución

* 📚 **Wiki Oficial**: [https://tainacan.github.io/tainacan-wiki/](https://tainacan.github.io/tainacan-wiki/)
* 💻 **Código Fuente**: [https://github.com/tainacan/tainacan](https://github.com/tainacan/tainacan)
* 💬 **Foro de la Comunidad**: [https://tainacan.discourse.group/](https://tainacan.discourse.group/)
* ✉️ **Lista de Correo**: [https://groups.google.com/g/tainacan](https://groups.google.com/g/tainacan)
