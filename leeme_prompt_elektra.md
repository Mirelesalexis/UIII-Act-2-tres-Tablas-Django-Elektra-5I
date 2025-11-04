Perfecto — te doy la **primera parte completa** y lista para ejecutar: pasos, comandos y todo el código (modelos, vistas, URLs, plantillas y configuración) para que el proyecto **Elektra** en **Python / Django** funcione en **VS Code** y arranque en el puerto **8029**. Me ceñí a tus requisitos: **sin `forms.py`**, trabajar por ahora sólo con **Productos**, estructura completa de carpetas/archivos y templates Bootstrap con colores suaves.

---

# 0 — Estructura final que crearemos

```
UIII_Elektra_0644/
├─ .venv/                          # entorno virtual
├─ backend_Elektra/                # proyecto Django
│  ├─ backend_Elektra/
│  │  ├─ __init__.py
│  │  ├─ settings.py
│  │  ├─ urls.py
│  │  └─ wsgi.py
│  ├─ manage.py
├─ app_Elektra/                     # app principal
│  ├─ migrations/
│  ├─ templates/
│  │  ├─ base.html
│  │  ├─ header.html
│  │  ├─ navbar.html
│  │  ├─ footer.html
│  │  ├─ inicio.html
│  │  └─ productos/
│  │     ├─ agregar_producto.html
│  │     ├─ ver_producto.html
│  │     ├─ actualizar_producto.html
│  │     └─ borrar_producto.html
│  ├─ admin.py
│  ├─ apps.py
│  ├─ models.py
│  ├─ views.py
│  ├─ urls.py
│  └─ __init__.py
```

---

# 1. Crear carpeta del proyecto `UIII_Elektra_0644`

En tu terminal (p. ej. PowerShell o bash):

```bash
mkdir UIII_Elektra_0644
cd UIII_Elektra_0644
```

# 2. Abrir VS Code en la carpeta

Desde la misma carpeta:

```bash
code .
```

(O abre VS Code y usa *File > Open Folder...* y selecciona `UIII_Elektra_0644`.)

# 3. Abrir la terminal en VS Code

En VS Code: `Terminal > New Terminal` (o `Ctrl+ñ` / `Ctrl+` `) — se abrirá en la raíz del proyecto.

# 4. Crear entorno virtual `.venv` desde terminal

(compatible Windows / macOS / Linux)

```bash
# Windows / PowerShell or CMD
python -m venv .venv

# macOS / Linux
python3 -m venv .venv
```

# 5. Activar el entorno virtual

* Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
# o si en cmd:
.venv\Scripts\activate
```

* macOS / Linux:

```bash
source .venv/bin/activate
```

Verás `(.venv)` al inicio del prompt.

# 6. Activar intérprete de Python en VS Code

1. En VS Code pulsa `Ctrl+Shift+P`.
2. Escribe `Python: Select Interpreter`.
3. Selecciona el que apunta a `UIII_Elektra_0644/.venv/...` (tu `.venv`).

# 7. Instalar Django

Con el entorno activo:

```bash
pip install django
```

# 8. Crear proyecto `backend_Elektra` sin duplicar carpeta

Para evitar que se cree `backend_Elektra/backend_Elektra`, usa el **punto** al final:

```bash
django-admin startproject backend_Elektra .
```

(Este comando en la carpeta `UIII_Elektra_0644` crea `manage.py` y la carpeta `backend_Elektra/` interna.)

# 9. Ejecutar servidor en el puerto 8029

```bash
python manage.py runserver 8029
```

Servidor accesible en `http://127.0.0.1:8029/`

# 10. Copiar y pegar el link en el navegador

Copia `http://127.0.0.1:8029/` y pégalo en tu navegador. (Si trabajas en red local y quieres acceder desde otra máquina usa `0.0.0.0:8029`: `python manage.py runserver 0.0.0.0:8029`.)

# 11. Crear aplicación `app_Elektra`

Con el entorno activo, en la raíz del proyecto:

```bash
python manage.py startapp app_Elektra
```

Esto crea la carpeta `app_Elektra/`.

---

# 12 — `models.py` corregido y listo (solo trabajar con Productos por ahora)

Crea/edita `app_Elektra/models.py` con este contenido. Observa: **Proveedor** definido antes de **Producto** para evitar referenciar una clase no declarada; import `timezone` para fechas cuando se necesite más adelante. Por ahora dejamos `Venta` presente pero al final trabajarás solo con Producto (puedes comentar Venta si quieres).

```python
from django.db import models
from django.utils import timezone

# MODELO: PROVEEDORES (definido primero para usar en Producto)
class Proveedor(models.Model):
    nombre = models.CharField(max_length=100)
    pais = models.CharField(max_length=50)
    direccion = models.TextField()
    telefono = models.CharField(max_length=20)
    email = models.EmailField(max_length=100, unique=True)
    fecha_registro = models.DateField(auto_now_add=True)
    activo = models.BooleanField(default=True)

    def __str__(self):
        return self.nombre

# MODELO: PRODUCTOS
class Producto(models.Model):
    nombre = models.CharField(max_length=50, unique=True)
    categoria = models.CharField(max_length=50)
    precio = models.DecimalField(max_digits=10, decimal_places=2)
    stock = models.IntegerField()
    descripcion = models.TextField(blank=True, null=True)
    proveedor = models.ForeignKey(Proveedor, on_delete=models.CASCADE)
    sku = models.CharField(max_length=100, blank=True, null=True)

    def __str__(self):
        return self.nombre

# MODELO: VENTAS (puedes dejarlo comentado si no lo usarás ahora)
class Venta(models.Model):
    folio = models.CharField(max_length=50, unique=True)
    fecha_venta = models.DateTimeField(default=timezone.now)
    total = models.DecimalField(max_digits=10, decimal_places=2)
    metodo_pago = models.CharField(max_length=50)
    estado = models.CharField(max_length=50)
    vendedor_id = models.IntegerField()
    id_producto = models.ForeignKey(Producto, on_delete=models.CASCADE)

    def __str__(self):
        return f"Venta {self.folio}"
```

> Nota: si quieres trabajar **solo con Producto** ahora, puedes comentar Proveedor/Venta temporalmente y poner `proveedor = models.CharField(max_length=100, blank=True)` en Producto para evitar FK; pero lo ideal es definir Proveedor como arriba.

---

# 12.5 Procedimiento para migraciones (makemigrations y migrate)

Después de guardar modelos:

```bash
python manage.py makemigrations
python manage.py migrate
```

# 13. Primero trabajamos con MODELO: PRODUCTOS

(Ya listo en `models.py`.)

# 14. `views.py` — funciones para Productos

Edita `app_Elektra/views.py` con esto (usa `render`, `redirect`, `get_object_or_404`). No usamos `forms.py`, manejamos `request.POST` directamente:

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Producto, Proveedor
from django.urls import reverse

def inicio_elektra(request):
    return render(request, 'inicio.html')

def agregar_producto(request):
    if request.method == 'POST':
        # sin validación: tomar campos directos
        nombre = request.POST.get('nombre')
        categoria = request.POST.get('categoria')
        precio = request.POST.get('precio') or 0
        stock = request.POST.get('stock') or 0
        descripcion = request.POST.get('descripcion', '')
        proveedor_id = request.POST.get('proveedor')
        sku = request.POST.get('sku', '')

        proveedor = None
        if proveedor_id:
            try:
                proveedor = Proveedor.objects.get(id=proveedor_id)
            except Proveedor.DoesNotExist:
                proveedor = None

        producto = Producto.objects.create(
            nombre=nombre,
            categoria=categoria,
            precio=precio,
            stock=stock,
            descripcion=descripcion,
            proveedor=proveedor,
            sku=sku
        )
        return redirect('ver_producto')

    # GET: mostrar formulario y lista de proveedores para el select
    proveedores = Proveedor.objects.all()
    return render(request, 'productos/agregar_producto.html', {'proveedores': proveedores})

def ver_producto(request):
    productos = Producto.objects.all().order_by('id')
    return render(request, 'productos/ver_producto.html', {'productos': productos})

def actualizar_producto(request, producto_id):
    producto = get_object_or_404(Producto, id=producto_id)
    proveedores = Proveedor.objects.all()
    return render(request, 'productos/actualizar_producto.html', {'producto': producto, 'proveedores': proveedores})

def realizar_actualizacion_producto(request, producto_id):
    producto = get_object_or_404(Producto, id=producto_id)
    if request.method == 'POST':
        producto.nombre = request.POST.get('nombre')
        producto.categoria = request.POST.get('categoria')
        producto.precio = request.POST.get('precio') or 0
        producto.stock = request.POST.get('stock') or 0
        producto.descripcion = request.POST.get('descripcion', '')
        proveedor_id = request.POST.get('proveedor')
        if proveedor_id:
            try:
                producto.proveedor = Proveedor.objects.get(id=proveedor_id)
            except Proveedor.DoesNotExist:
                producto.proveedor = None
        producto.sku = request.POST.get('sku', '')
        producto.save()
        return redirect('ver_producto')
    return redirect('actualizar_producto', producto_id=producto.id)

def borrar_producto(request, producto_id):
    producto = get_object_or_404(Producto, id=producto_id)
    if request.method == 'POST':
        producto.delete()
        return redirect('ver_producto')
    return render(request, 'productos/borrar_producto.html', {'producto': producto})
```

# 15. Crear carpeta `templates` dentro de `app_Elektra`

En VS Code crea `app_Elektra/templates/` y subcarpetas como en la estructura.

# 16. Archivos HTML: `base.html`, `header.html`, `navbar.html`, `footer.html`, `inicio.html`

A continuación los contenidos esenciales. Todos usan Bootstrap CDN y un CSS simple de "colores suaves".

## `app_Elektra/templates/base.html`

```html
<!doctype html>
<html lang="es">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Elektra - Sistema</title>

    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">

    <!-- Bootstrap Icons -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.5/font/bootstrap-icons.css" rel="stylesheet">

    <style>
      body { background: #f7fafc; color: #222; }
      .soft-card { background: #ffffff; border-radius: 12px; box-shadow: 0 2px 10px rgba(20,20,30,0.04); padding: 1rem; }
      footer.footer-fixed { position: fixed; bottom: 0; width: 100%; background: #f1f5f9; padding: .75rem 0; text-align:center; }
      .brand { font-weight:700; color:#1f2937; }
    </style>
  </head>
  <body>
    {% include 'navbar.html' %}
    <div class="container my-4">
      {% block content %}{% endblock %}
    </div>

    {% include 'footer.html' %}

    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
  </body>
</html>
```

## `app_Elektra/templates/navbar.html`

```html
<nav class="navbar navbar-expand-lg navbar-light" style="background:#e6f2ff;">
  <div class="container">
    <a class="navbar-brand brand" href="{% url 'inicio_elektra' %}">
      <i class="bi bi-bank"></i> Sistema de Administración Elektra
    </a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navMenu">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navMenu">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link" href="{% url 'inicio_elektra' %}"><i class="bi bi-house"></i> Inicio</a></li>

        <!-- Productos con submenu Categorías -->
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown"><i class="bi bi-box-seam"></i> Producto</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'agregar_producto' %}">Agregar producto</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_producto' %}">Ver producto</a></li>
            <li><a class="dropdown-item" href="#">Actualizar producto</a></li>
            <li><a class="dropdown-item" href="#">Borrar producto</a></li>
          </ul>
        </li>

        <!-- Proveedores -->
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown"><i class="bi bi-people"></i> Proveedores</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar proveedores</a></li>
            <li><a class="dropdown-item" href="#">Ver proveedores</a></li>
            <li><a class="dropdown-item" href="#">Actualizar proveedores</a></li>
            <li><a class="dropdown-item" href="#">Borrar proveedores</a></li>
          </ul>
        </li>

        <!-- Ventas -->
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown"><i class="bi bi-cash-stack"></i> Ventas</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar ventas</a></li>
            <li><a class="dropdown-item" href="#">Ver ventas</a></li>
            <li><a class="dropdown-item" href="#">Actualizar ventas</a></li>
            <li><a class="dropdown-item" href="#">Borrar ventas</a></li>
          </ul>
        </li>

      </ul>
    </div>
  </div>
</nav>
```

## `app_Elektra/templates/footer.html`

```html
<footer class="footer-fixed">
  <div class="container">
    <small>© {% now "Y" %} Sistema Elektra — Creado por Ing. Alexis Mireles, Cbtis 128</small>
  </div>
</footer>
```

## `app_Elektra/templates/inicio.html`

```html
{% extends 'base.html' %}
{% block content %}
<div class="soft-card">
  <h2>Bienvenido al Sistema Elektra</h2>
  <p>Administración sencilla de productos, proveedores y ventas.</p>
  <img src="https://images.unsplash.com/photo-1515879218367-8466d910aaa4?auto=format&fit=crop&w=1200&q=60"
       alt="Elektra" class="img-fluid rounded">
</div>
{% endblock %}
```

> La imagen anterior se toma desde la red (Unsplash). Puedes reemplazar la URL por otra.

# 21 y 22 — Carpeta `productos` y plantillas (agregar/ver/actualizar/borrar)

Crea `app_Elektra/templates/productos/` y añade estos archivos:

## `agregar_producto.html`

```html
{% extends 'base.html' %}
{% block content %}
<div class="soft-card">
  <h3>Agregar Producto</h3>
  <form method="post">
    {% csrf_token %}
    <div class="mb-2">
      <label>Nombre</label>
      <input name="nombre" class="form-control" required>
    </div>
    <div class="mb-2">
      <label>Categoria</label>
      <input name="categoria" class="form-control">
    </div>
    <div class="row">
      <div class="col-md-4 mb-2">
        <label>Precio</label>
        <input name="precio" type="number" step="0.01" class="form-control">
      </div>
      <div class="col-md-4 mb-2">
        <label>Stock</label>
        <input name="stock" type="number" class="form-control">
      </div>
      <div class="col-md-4 mb-2">
        <label>SKU</label>
        <input name="sku" class="form-control">
      </div>
    </div>

    <div class="mb-2">
      <label>Proveedor</label>
      <select name="proveedor" class="form-select">
        <option value="">-- Seleccionar --</option>
        {% for p in proveedores %}
          <option value="{{ p.id }}">{{ p.nombre }}</option>
        {% endfor %}
      </select>
    </div>

    <div class="mb-2">
      <label>Descripcion</label>
      <textarea name="descripcion" class="form-control" rows="3"></textarea>
    </div>

    <button class="btn btn-primary">Agregar</button>
    <a href="{% url 'ver_producto' %}" class="btn btn-secondary">Volver</a>
  </form>
</div>
{% endblock %}
```

## `ver_producto.html`

```html
{% extends 'base.html' %}
{% block content %}
<div class="soft-card">
  <h3>Productos</h3>
  <a href="{% url 'agregar_producto' %}" class="btn btn-success mb-3">Agregar producto</a>
  <table class="table table-striped">
    <thead>
      <tr>
        <th>#</th><th>Nombre</th><th>Categoria</th><th>Precio</th><th>Stock</th><th>Proveedor</th><th>Acciones</th>
      </tr>
    </thead>
    <tbody>
      {% for p in productos %}
      <tr>
        <td>{{ forloop.counter }}</td>
        <td>{{ p.nombre }}</td>
        <td>{{ p.categoria }}</td>
        <td>{{ p.precio }}</td>
        <td>{{ p.stock }}</td>
        <td>{{ p.proveedor }}</td>
        <td>
          <a href="{% url 'actualizar_producto' p.id %}" class="btn btn-sm btn-warning">Editar</a>
          <a href="{% url 'borrar_producto' p.id %}" class="btn btn-sm btn-danger">Borrar</a>
        </td>
      </tr>
      {% empty %}
      <tr><td colspan="7">No hay productos</td></tr>
      {% endfor %}
    </tbody>
  </table>
</div>
{% endblock %}
```

## `actualizar_producto.html`

```html
{% extends 'base.html' %}
{% block content %}
<div class="soft-card">
  <h3>Actualizar Producto</h3>
  <form method="post" action="{% url 'realizar_actualizacion_producto' producto.id %}">
    {% csrf_token %}
    <div class="mb-2">
      <label>Nombre</label>
      <input name="nombre" value="{{ producto.nombre }}" class="form-control" required>
    </div>
    <div class="mb-2">
      <label>Categoria</label>
      <input name="categoria" value="{{ producto.categoria }}" class="form-control">
    </div>
    <div class="row">
      <div class="col-md-4 mb-2">
        <label>Precio</label>
        <input name="precio" value="{{ producto.precio }}" type="number" step="0.01" class="form-control">
      </div>
      <div class="col-md-4 mb-2">
        <label>Stock</label>
        <input name="stock" value="{{ producto.stock }}" type="number" class="form-control">
      </div>
      <div class="col-md-4 mb-2">
        <label>SKU</label>
        <input name="sku" value="{{ producto.sku }}" class="form-control">
      </div>
    </div>

    <div class="mb-2">
      <label>Proveedor</label>
      <select name="proveedor" class="form-select">
        <option value="">-- Seleccionar --</option>
        {% for p in proveedores %}
          <option value="{{ p.id }}" {% if producto.proveedor and producto.proveedor.id == p.id %}selected{% endif %}>{{ p.nombre }}</option>
        {% endfor %}
      </select>
    </div>

    <div class="mb-2">
      <label>Descripcion</label>
      <textarea name="descripcion" class="form-control" rows="3">{{ producto.descripcion }}</textarea>
    </div>

    <button class="btn btn-primary">Guardar</button>
    <a href="{% url 'ver_producto' %}" class="btn btn-secondary">Cancelar</a>
  </form>
</div>
{% endblock %}
```

## `borrar_producto.html`

```html
{% extends 'base.html' %}
{% block content %}
<div class="soft-card">
  <h3>Eliminar producto</h3>
  <p>¿Seguro que deseas eliminar <strong>{{ producto.nombre }}</strong>?</p>
  <form method="post">
    {% csrf_token %}
    <button class="btn btn-danger">Sí, borrar</button>
    <a href="{% url 'ver_producto' %}" class="btn btn-secondary">No</a>
  </form>
</div>
{% endblock %}
```

---

# 23. No utilizar `forms.py`

Cumplido: las vistas leen `request.POST` directamente.

# 24. `urls.py` en `app_Elektra` (mapeo CRUD)

Crea `app_Elektra/urls.py`:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_elektra, name='inicio_elektra'),
    path('productos/agregar/', views.agregar_producto, name='agregar_producto'),
    path('productos/', views.ver_producto, name='ver_producto'),
    path('productos/actualizar/<int:producto_id>/', views.actualizar_producto, name='actualizar_producto'),
    path('productos/actualizar/realizar/<int:producto_id>/', views.realizar_actualizacion_producto, name='realizar_actualizacion_producto'),
    path('productos/borrar/<int:producto_id>/', views.borrar_producto, name='borrar_producto'),
]
```

# 25. Agregar `app_Elektra` en `settings.py` de `backend_Elektra`

En `backend_Elektra/settings.py` — busca `INSTALLED_APPS` y añade `'app_Elektra',`:

```python
INSTALLED_APPS = [
    # ...
    'app_Elektra',
]
```

Además, asegurarte que `TEMPLATES` incluya el directorio de templates (opcional si Django encuentra templates por app, pero añadir para mayor seguridad):

```python
import os
BASE_DIR = Path(__file__).resolve().parent.parent

TEMPLATES = [
    {
        # ...
        'DIRS': [os.path.join(BASE_DIR, 'app_Elektra', 'templates')],
        # ...
    },
]
```

# 26. Configurar `backend_Elektra/urls.py` para enlazar `app_Elektra`

Edita `backend_Elektra/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Elektra.urls')),  # rutea la app principal
]
```

# 27. Registrar modelos en `admin.py` y volver a migrar

En `app_Elektra/admin.py`:

```python
from django.contrib import admin
from .models import Producto, Proveedor, Venta

admin.site.register(Producto)
admin.site.register(Proveedor)
# admin.site.register(Venta)  # opcional por ahora
```

Luego:

```bash
python manage.py makemigrations
python manage.py migrate
```

# 28. Estética: colores suaves y código simple

El `base.html` tiene estilos suaves. Puedes ajustar paleta (azul claro, gris suave). Las plantillas usan Bootstrap para apariencia sencilla y moderna.

# 29. Al inicio crear la estructura completa de carpetas y archivos

Ya listado arriba en la estructura. Crea manualmente los archivos con los contenidos que te proporcioné.

# 30. Proyecto totalmente funcional (productos)

Con todo lo anterior implementado deberías poder:

* Agregar productos (`/productos/agregar/`)
* Ver lista de productos (`/productos/`)
* Editar (`/productos/actualizar/<id>/`)
* Borrar (`/productos/borrar/<id>/`)
* Inicio (`/`)

# 31. Finalmente ejecutar servidor en el puerto 8029

```bash
python manage.py runserver 8029
```

---

## Comandos rápidos resumen (para copiar/pegar)

```bash
# crear carpeta y abrir VS Code
mkdir UIII_Elektra_0644 && cd UIII_Elektra_0644
code .

# crear venv y activarlo (ejemplo Windows PowerShell)
python -m venv .venv
.venv\Scripts\Activate.ps1

# instalar django
pip install django

# crear proyecto sin carpeta duplicada
django-admin startproject backend_Elektra .

# crear app
python manage.py startapp app_Elektra

# aplicar migraciones iniciales
python manage.py makemigrations
python manage.py migrate

# correr servidor en el puerto 8029
python manage.py runserver 8029
```

---

## Recomendaciones / notas finales

* Si obtienes error por `Proveedor` vacío al crear un `Producto`, asegúrate de crear al menos un `Proveedor` en el admin ([http://127.0.0.1:8029/admin](http://127.0.0.1:8029/admin)) o modificar temporalmente `proveedor` a `null=True, blank=True` en el `ForeignKey`.
* Si trabajas en Windows PowerShell y no puedes activar el venv por política, usa `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass`.
* En producción cambia `DEBUG=False` y configura `ALLOWED_HOSTS`.
* Dejé `Proveedor` y `Venta` en modelos pero puedes comentarlos si no los vas a usar en este primer paso.

---

Si quieres, inmediatamente te doy:

* los archivos listos para pegar (si quieres que cree un pack con todos los archivos contenidos exactamente listos para pegar aquí),
* o te genero sólo las plantillas HTML completas en un solo bloque para pegarlas en VS Code.

¿Qué prefieres ahora?

