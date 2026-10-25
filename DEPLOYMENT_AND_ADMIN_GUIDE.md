# GUÍA OFICIAL DE ADMINISTRACIÓN Y DESPLIEGUE | NUKLEO TRAINING

Esta guía detalla el funcionamiento, administración y despliegue del proyecto web oficial y panel de administración privado de **NUKLEO TRAINING**.

---

## 1. Acceso al Panel de Administración

* **Ruta de acceso:** `/admin` (ejemplo local: `http://localhost:3000/admin` o en tu dominio: `https://tudominio.com/admin`)
* **Credenciales iniciales (configuradas mediante seed):**
  * **Email:** `admin@nukleotraining.com`
  * **Contraseña inicial:** `NukleoAdmin2025!`
* **Seguridad:**
  * Al ingresar por primera vez, puedes cambiar la contraseña directamente desde **Panel > Cambiar Contraseña** (`/admin/seguridad`).
  * Las contraseñas se almacenan mediante hash seguro `bcryptjs` con salt rounds. Nunca se guardan en texto plano ni se exponen al cliente.
  * La sesión se gestiona con cookies seguras `HTTP-only`, `SameSite` y tokens firmados criptográficamente con `jose`.

---

## 2. Ejecución Local del Proyecto

1. **Requisitos previos:**
   * Node.js v18+ o superior (probado en v24).
   * npm instalado.

2. **Instalación de dependencias:**
   ```bash
   npm install
   ```

3. **Inicializar la base de datos y catálogo oficial (14 productos y tarifas):**
   ```bash
   npx prisma db push
   node scripts/seed.mjs
   ```

4. **Iniciar el servidor de desarrollo:**
   ```bash
   npm run dev
   ```
   Abre [http://localhost:3000](http://localhost:3000) en tu navegador.

5. **Compilar para producción (Build):**
   ```bash
   npm run build
   npm run start
   ```

---

## 3. Matriz Oficial de Productos y Tarifas Iniciales

El catálogo viene precargado con las 14 equipaciones oficiales y sus tarifas exactas por tramos:

| Categoría | Producto | Técnica | 25–39 uds | 40–69 uds | 70+ uds |
| :--- | :--- | :--- | :---: | :---: | :---: |
| **Camisetas** | Camiseta técnica sublimada | Sublimación | 22 € | 20 € | 18 € |
| **Camisetas** | Camiseta técnica DTF | DTF | 20 € | 18 € | 16 € |
| **Camisetas** | Camiseta de algodón bordada | Bordado | 22 € | 20 € | 18 € |
| **Camisetas** | Camiseta técnica económica | Económica | 14 € | 13 € | 12 € |
| **Rashguards** | Rashguard sublimado | Sublimación | 25 € | 22 € | 20 € |
| **Rashguards** | Rashguard de natación sublimado | Sublimación | 25 € | 22 € | 20 € |
| **Sudaderas** | Sudadera de poliéster sublimada | Sublimación | 30 € | 27 € | 25 € |
| **Sudaderas** | Sudadera de poliéster DTF | DTF | 28 € | 25 € | 23 € |
| **Sudaderas** | Sudadera de algodón bordada | Bordado | 32 € | 29 € | 27 € |
| **Sudaderas** | Sudadera de algodón DTF | DTF | 30 € | 27 € | 25 € |
| **Chándales** | Chándal de poliéster sublimado | Sublimación | 50 € | 48 € | 46 € |
| **Chándales** | Chándal de poliéster DTF | DTF | 48 € | 46 € | 44 € |
| **Chándales** | Chándal de algodón bordado | Bordado | 54 € | 52 € | 50 € |
| **Chándales** | Chándal de algodón DTF | DTF | 52 € | 50 € | 48 € |

* **Regla de la calculadora:**
  * Menos de 25 unidades: muestra aviso de que la tarifa oficial publicada comienza en 25 unidades.
  * 25 a 39 unidades: aplica tramo 1.
  * 40 a 69 unidades: aplica tramo 2.
  * 70 o más unidades: aplica tramo 3.
  * Cambio en 39 / 40 uds y 69 / 70 uds verificado.
  * Unica fuente de datos: al modificar cualquier precio en `/admin/precios`, la calculadora pública y la ficha se actualizan automáticamente sin tocar código.

---

## 4. Integración con WhatsApp

* **Teléfono comercial configurado:** `+34 608 451 380`
* **Enlace base:** `https://wa.me/34608451380`
* **Mensajes automáticos:** Generados y codificados en UTF-8 (`encodeURIComponent`).
* **Personalización desde el panel:** En `/admin/whatsapp` puedes cambiar el número y editar las plantillas de mensajes.

---

## 5. Gestión del Logotipo e Identidad Visual

* El logotipo oficial original está ubicado en `/public/images/logo.svg`.
* Puedes sustituirlo en cualquier momento desde **Panel > Logo e Identidad** (`/admin/identidad`) subiendo un nuevo archivo PNG o SVG sin alterar proporciones ni distorsionar la imagen.
* La cabecera, el favicon y el pie de página se actualizan de manera coordinada.

---

## 6. Despliegue en Producción (Vercel + Supabase)

### Paso 1: Crear proyecto en Supabase (Base de datos PostgreSQL)
1. Entra en [supabase.com](https://supabase.com) y crea una cuenta gratuita.
2. Crea un nuevo proyecto llamado `nukleo-training`.
3. Ve a **SQL Editor** en Supabase, copia el contenido del archivo `supabase/schema.sql` que hemos creado en este proyecto y pulsa **Run**.
4. En **Project Settings > Database**, copia la URI de conexión `Connection string` (URI en modo Transaction o Session).

### Paso 2: Desplegar en Vercel
1. Sube este repositorio a tu cuenta de GitHub o GitLab.
2. Entra en [vercel.com](https://vercel.com) y pulsa **Add New > Project**.
3. Importa el repositorio `nukleo-training-web`.
4. En la sección **Environment Variables**, añade:
   * `DATABASE_URL`: Tu URI de conexión de Supabase (o Postgres).
   * `JWT_SECRET`: Una cadena aleatoria segura de al menos 32 caracteres (ejemplo: puedes generar una con `openssl rand -hex 32`).
   * `ADMIN_INITIAL_EMAIL`: `admin@nukleotraining.com`
   * `ADMIN_INITIAL_PASSWORD`: Tu contraseña segura inicial.
   * `NEXT_PUBLIC_SITE_URL`: `https://tudominio.com`
5. Pulsa **Deploy**. Vercel compilará la aplicación y la publicará en minutos con certificado SSL HTTPS automático.

### Paso 3: Conectar tu Dominio Propio
1. En Vercel, entra en tu proyecto y ve a **Settings > Domains**.
2. Escribe tu dominio (por ejemplo `nukleotraining.com` y `www.nukleotraining.com`).
3. Vercel te indicará los registros DNS (CNAME o registro A) que debes añadir en tu proveedor de dominio (GoDaddy, Namecheap, DonDominio, etc.).
4. Una vez propagado el DNS (suele tardar entre 5 y 30 minutos), tu web estará operativa con HTTPS seguro.

---

## 7. Copias de Seguridad (Backups)

* **En desarrollo local con SQLite:** El archivo de base de datos es `dev.db`. Para hacer una copia de seguridad basta con copiar ese archivo:
  ```bash
  cp dev.db dev_backup_$(date +%Y%m%d).db
  ```
* **En producción con Supabase:** Supabase realiza copias de seguridad automáticas diarias. También puedes descargar un dump SQL desde **Database > Backups**.

---

## 8. Aspectos Legales Pendientes Antes de Publicar

En los archivos:
* `src/app/aviso-legal/page.tsx`
* `src/app/privacidad/page.tsx`

Completa los campos marcados como `[COMPLETAR: ...]` con tu razón social o nombre de autónomo, NIF y domicilio fiscal.
