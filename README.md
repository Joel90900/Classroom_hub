# ⚡ ClassroomHub Pro — Plataforma de Recursos y Gestión Educativa

[![Django](https://img.shields.io/badge/Django-6.1.3-092E20?style=for-the-badge&logo=django&logoColor=white)](https://www.djangoproject.com/)
[![MySQL](https://img.shields.io/badge/Database-MySQL%20%7C%20MariaDB-4479A1?style=for-the-badge&logo=mysql&logoColor=white)](https://www.mysql.com/)
[![Tests](https://img.shields.io/badge/Tests-55%20Passing-success?style=for-the-badge&logo=pytest&logoColor=white)]()
[![Architecture](https://img.shields.io/badge/Architecture-MTV%20Pattern-blueviolet?style=for-the-badge)]()

**ClassroomHub** es un portal web de gestión de materiales didácticos y recursos de aprendizaje desarrollado sobre el framework **Django**. Implementa una arquitectura desacoplada basada en el patrón **MTV (Model-Template-View)**, conectada a un motor de base de datos relacional **MySQL/MariaDB**, con seguridad granular mediante roles, autenticación de usuarios y vistas basadas en clases para operaciones CRUD completas.

---

## 🚀 Características Principales

- **Catálogo Público de Recursos**: Exploración libre de materiales educativos organizados por categorías académicas.
- **Detalle y Ficha Técnica**: Visualización especializada por recurso (`DetailView`) con manejo transparente de errores HTTP 404.
- **Gestión CRUD Integral**: Vistas basadas en clases (`CreateView`, `UpdateView`, `DeleteView`) protegidas por permisos específicos.
- **Validación Avanzada en Servidor**: Sanitización y normalización de entradas mediante `ModelForm` (`clean_title`).
- **Autenticación y Sesiones**: Registro público, inicio de sesión seguro, cierre mediante método POST y seguimiento de visitas en sesión.
- **Seguridad y Control de Acceso (RBAC)**: Grupos `Catalog reviewers` (revisión de solo lectura) y `Catalog editors` (gestión completa de recursos).
- **Cobertura de Pruebas Unitarias**: Suite integral de 55 pruebas automatizadas ejecutadas en base de datos aislada.

---

## 🏗️ Arquitectura y Flujo del Sistema

```
                       ┌─────────────────────────┐
                       │   Cliente Web / HTTP    │
                       └────────────┬────────────┘
                                    │
                                    ▼
                       ┌─────────────────────────┐
                       │    Enrutador urls.py    │
                       └────────────┬────────────┘
                                    │
                  ┌─────────────────┴─────────────────┐
                  ▼                                   ▼
       ┌─────────────────────┐             ┌─────────────────────┐
       │ Vistas Funcionales  │             │ Vistas Basadas en   │
       │       (FBV)         │             │    Clases (CBV)     │
       │ home, signup, dash  │             │ Detail, CRUD Views  │
       └──────────┬──────────┘             └──────────┬──────────┘
                  │                                   │
                  ├─────────────────┬─────────────────┤
                  ▼                 ▼                 ▼
         ┌─────────────────┐ ┌─────────────┐ ┌─────────────────┐
         │ ModelForm / ORM │ │  Plantillas │ │ Autenticación y │
         │   Validation    │ │    (Dバージ)   │ │    Permisos     │
         └────────┬────────┘ └─────────────┘ └─────────────────┘
                  │
                  ▼
         ┌─────────────────┐
         │ MySQL / MariaDB │
         └─────────────────┘
```

---

## 📋 Matriz de Requisitos Funcionales

| Módulo | Clave | Descripción Operativa | Componente Django |
|---|:---:|---|---|
| **Catálogo** | `RF01` | Listado general accesible de manera pública | `pages:resources` (`resources`) |
| **Detalle** | `RF02` | Consulta detallada de información y categoría | `pages:resource-detail` (`ResourceDetailView`) |
| **Formularios** | `RF03` | Validación de negocio y sanitización de títulos | `ResourceForm` (`clean_title`) |
| **Creación** | `RF04` | Alta de recursos restringida con permiso `add` | `pages:resource-create` (`ResourceCreateView`) |
| **Edición** | `RF05` | Modificación sin duplicar clave primaria | `pages:resource-edit` (`ResourceUpdateView`) |
| **Eliminación** | `RF06` | Confirmación previa (GET) y baja efectiva (POST) | `pages:resource-delete` (`ResourceDeleteView`) |
| **Identidad** | `RF07` | Registro de cuentas, Login y Logout seguro | `UserCreationForm`, `LoginView`, `LogoutView` |
| **Sesiones** | `RF08` | Contador de visitas persistente en sesión privada | `pages:dashboard` (`@login_required`) |
| **Revisión** | `RF09` | Panel exclusivo para el grupo de revisores | `pages:resource-review` (`view_resource`) |
| **Edición** | `RF10` | Privilegios de edición completa para editores | Permisos CRUD (`add`, `change`, `delete`) |
| **Control** | `RF11` | Redirección 302 a anónimos y respuesta 403 a no autorizados | `EditorAccessMixin` |
| **Errores** | `RF12` | Respuesta controlada HTTP 404 para registros inexistentes | `django.http.Http404` |
| **Calidad** | `RF13` | Ejecución aislada de la batería de 55 pruebas unitarias | `test_classroom_hub` |

---

## 🗺️ Índice de Rutas de la Aplicación

| URL | Nombre de Ruta | Acceso Requerido | Tipo | Propósito |
|---|---|---|---|---|
| `/` | `pages:home` | Público | FBV | Página de inicio del portal |
| `/resources/` | `pages:resources` | Público | FBV | Catálogo principal de recursos |
| `/resources/<int:pk>/` | `pages:resource-detail` | Público | CBV | Ficha técnica del recurso |
| `/resources/new/` | `pages:resource-create` | Autenticado + `add_resource` | CBV | Formulario para registrar recurso |
| `/resources/<int:pk>/edit/` | `pages:resource-edit` | Autenticado + `change_resource` | CBV | Edición de recurso existente |
| `/resources/<int:pk>/delete/` | `pages:resource-delete` | Autenticado + `delete_resource` | CBV | Confirmación y borrado de registro |
| `/resources/review/` | `pages:resource-review` | Autenticado + `view_resource` | FBV | Panel de revisión de contenido |
| `/about/` | `pages:about` | Público | FBV | Información del sistema |
| `/accounts/signup/` | `pages:signup` | Anónimo | FBV | Registro de nuevos usuarios |
| `/accounts/login/` | `pages:login` | Anónimo | CBV | Inicio de sesión con CSRF |
| `/accounts/logout/` | `pages:logout` | Autenticado (POST) | CBV | Cierre de sesión seguro |
| `/dashboard/` | `pages:dashboard` | Autenticado | FBV | Panel privado con contador |
| `/dashboard/summary/` | `pages:dashboard-summary` | Autenticado | FBV | Métricas agregadas en tiempo real |
| `/admin/` | `admin:index` | Staff | Admin | Panel administrativo de Django |

---

## 👥 Esquema de Roles y Permisos (RBAC)

El proyecto utiliza el sistema de autenticación nativo de Django para modelar la seguridad:

```
               ┌────────────────────────────────┐
               │    Catalog reviewers (Grupo)   │
               └───────────────┬────────────────┘
                               │ Permiso:
                               ▼ pages.view_resource
               ┌────────────────────────────────┐
               │     Acceso a /resources/review/ │
               └────────────────────────────────┘

               ┌────────────────────────────────┐
               │     Catalog editors (Grupo)    │
               └───────────────┬────────────────┘
                               │ Permisos:
                               ▼ pages.view_resource, add_resource,
                                 change_resource, delete_resource
               ┌────────────────────────────────┐
               │ Operaciones CRUD Completas     │
               └────────────────────────────────┘
```

---

## ⚙️ Configuración y Variables de Entorno

Crea un archivo `.env` en la raíz de `classroom-hub` con los parámetros de conexión:

```env
DB_NAME=classroomhub_db
DB_USER=classroomhub_user
DB_PASSWORD=TuPasswordSeguro
DB_HOST=127.0.0.1
DB_PORT=3306
```

---

## 🧪 Ejecución de Pruebas Automatizadas

La suite incluye **55 pruebas unitarias y de integración** divididas en módulos:

1. **Modelos y Persistencia**: Integridad referencial (`PROTECT`), representaciones y ordenamiento.
2. **Autenticación y Sesiones**: Registro, inicio de sesión, CSRF, cierre por POST y contador.
3. **Control de Acceso y Roles**: Respuestas HTTP 403, grupos y permisos de revisor.
4. **Validación de Formularios**: Reglas de negocio sobre longitud, espacios en blanco y categorías.
5. **Operaciones CRUD**: Comportamiento de vistas genéricas (`Detail`, `Create`, `Update`, `Delete`).

Para correr las 55 pruebas:

```bash
python manage.py test pages
```

---

## 💻 Inicio Rápido en Desarrollo

```bash
# 1. Instalar dependencias
pip install -r requirements.txt

# 2. Aplicar migraciones
python manage.py migrate

# 3. Iniciar el servidor local
python manage.py runserver
```

Abre en tu navegador: [http://127.0.0.1:8000/](http://127.0.0.1:8000/)
