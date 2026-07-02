# consulta-mortal
Cuarto Challenge en The Huddle, web scraping + db.
# Consulta Mortal

> *"Infiltrarte en el criminalmente desactualizado sitio Books To Scrape y exponer verdades oscuras."*

Mini pipeline de datos real que simula un flujo completo: **Scraping → Enriquecimiento → Persistencia → Modelado → Performance**.

---

## Descripción

**Consulta Mortal** es un challenge de datos desarrollado como parte del programa de formación **Penguin Academy**. El proyecto consiste en scrapear el sitio [Books to Scrape](http://books.toscrape.com/), enriquecer los datos mediante APIs externas y persistirlos en una base de datos relacional, para luego analizarlos mediante consultas SQL avanzadas.

---

## Tecnologías utilizadas

- **Python**
- **Jupyter Notebook**
- **requests** — Peticiones HTTP
- **BeautifulSoup4** — Parsing de HTML
- **SQLite** — Base de datos relacional
- **Open Library API** — Enriquecimiento de autores
- **Wikipedia REST API** — Obtención de nacionalidades

---

## Diagrama UML

El diagrama entidad-relación de la base de datos se encuentra incluido en el Jupyter Notebook, generado con [DrawDB](https://drawdb.vercel.app/) a partir del DDL del proyecto.

**Tablas:**
- `categories` — Categorías de libros
- `books` — Libros scrapeados con título, precio, rating, stock y descripción
- `authors` — Autores enriquecidos con datos de APIs externas
- `book_author` — Tabla intermedia (relación N:M entre libros y autores)

---

## Pipeline

El pipeline es **idempotente**: puede ejecutarse múltiples veces sin generar duplicados gracias al uso de `INSERT OR IGNORE` y restricciones `UNIQUE` en la base de datos.

```
1. Scraping de todas las categorías y libros (Books to Scrape)
2. Extracción de descripción, stock y UPC por libro
3. Enriquecimiento de autores via Open Library API
4. Obtención de nacionalidades via Wikipedia API
5. Obtención de años de nacimiento via Open Library API
6. Persistencia en base de datos SQLite
```

---

## Consultas SQL

El notebook incluye 5 consultas comentadas:

1. **Libros bien valorados y económicos** — Rating > 3 y precio < £20
2. **Autor con peor promedio de rating** — Entre autores con más de 10 obras conocidas
3. **Categoría con mayor precio promedio**
4. **Top 5 autores más prolíficos** — Usando `total_known_works` de Open Library
5. **País con más libros bien valorados** *(Obligatoria)* — JOIN entre las 4 tablas, filtrando por rating > 3

---

## Indexación y Performance

Se demuestra el impacto de los índices en el rendimiento de las consultas:

| Consulta | Sin índice | Con índice |
|----------|-----------|------------|
| Consulta por país y rating | ~0.006s | ~0.0005s |

**Mejora aproximada: 12x más rápido.**

---

## Instalación y ejecución

### 1. Clonar el repositorio

```bash
git clone git@github.com:CarlosScura/consulta-mortal.git
cd consulta-mortal
```

### 2. Instalar dependencias

```bash
pip install requests beautifulsoup4
```

### 3. Ejecutar el notebook

Abrí `consulta_mortal.ipynb` en Jupyter Notebook y ejecutá las celdas **en orden**:

1. Celda de imports y variables globales
2. Definición de funciones
3. Creación de tablas
4. Funciones de inserción
5. Pipeline principal
6. Actualización de años de nacimiento
7. Actualización de nacionalidades
8. Consultas SQL

> El pipeline puede tardar entre 45 y 90 minutos dependiendo de la disponibilidad de las APIs externas.

---

## Limitaciones conocidas

- **Variabilidad en resultados**: Las consultas que dependen de APIs externas pueden variar ligeramente entre ejecuciones debido a timeouts y disponibilidad de los servidores.
- **País no disponible directamente**: Open Library no provee el campo país en su endpoint de búsqueda. Se obtiene mediante Wikipedia API usando la descripción del autor.
- **Autores no encontrados**: Libros con títulos con caracteres especiales o poco comunes pueden no encontrarse en Open Library, quedando con datos `NULL`.
- **Sun Tzu**: El nombre en caracteres chinos (孙武) dificulta la búsqueda en APIs occidentales.

---

## Autor

**Fede Alarcón Scura** — Estudiante de Penguin Academy  
Challenge desarrollado como parte del programa de formación.