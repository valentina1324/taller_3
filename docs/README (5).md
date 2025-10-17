# Descripción del negocio

Mi tienda virtual “Glow Beauty” es un e-commerce especializado en productos de maquillaje de alta calidad. La idea central del negocio es brindar una experiencia de compra cómoda, segura y confiable desde casa, dirigida a personas que desean adquirir maquillaje sin perder tiempo y con la seguridad de recibir productos originales.

Ofrecemos productos como bases, correctores, rubores, contornos, polvos y labiales, y cada producto está acompañado de imágenes, descripciones detalladas y precios actualizados.
#### La plataforma busca facilitar que los clientes:

Busquen y comparen productos rápidamente.

Añadan productos a un carrito virtual y los modifiquen según sus necesidades.

Finalicen su compra de manera segura mediante un proceso de checkout confiable.

Consulten su historial de pedidos y gestionen sus direcciones de envío.

Por otro lado, los administradores pueden:

Crear, editar o eliminar productos del catálogo.

Supervisar todos los pedidos realizados.

Gestionar los usuarios registrados.

El propósito de Glow Beauty es integrar tecnología y comercio electrónico de manera intuitiva, cuidando tanto la experiencia del cliente como la gestión interna del negocio.

### Rutas del cliente (User)

GET /productos: Ver todos los productos disponibles.

GET /productos/:id: Ver los detalles de un producto específico.

POST /carrito: Agregar un producto al carrito.

PUT /carrito/:id: Editar la cantidad de un producto en el carrito.

DELETE /carrito/:id: Eliminar un producto del carrito.

POST /checkout: Finalizar el pedido (pagar).

GET /pedidos: Ver historial de pedidos del usuario.

GET /perfil: Ver la información del perfil del usuario.

### Rutas del administrador (Admin)

POST /admin/login: Iniciar sesión como administrador.

POST /admin/productos: Crear un nuevo producto.

PUT /admin/productos/:id: Editar un producto existente.

DELETE /admin/productos/:id: Eliminar un producto del catálogo.

GET /admin/pedidos: Ver todos los pedidos realizados por los clientes.

GET /admin/usuarios: Ver los usuarios registrados.

## Entidades / Base de datos

### User

id: UUID / integer, identificador único del usuario (PK).

email: string, único y obligatorio.

passwordHash: string, contraseña encriptada.

name: string, nombre completo o display name.

role: enum('user','admin'), define permisos.

createdAt: timestamp, fecha de creación.

updatedAt: timestamp, fecha última modificación.

### Product

id: UUID / integer, ID del producto.

name: string, nombre del producto (ej. Base líquida).

description: text, descripción detallada.

price: decimal, precio del producto.

sku: string, código de inventario (opcional).

type: enum('base','corrector','rubor','contorno','polvo','labial'), categoría del producto.

stock: integer, cantidad disponible.

createdAt: timestamp, fecha de creación.

updatedAt: timestamp, fecha de actualización.

### ProductImage

id: UUID / integer, ID de la imagen.

productId: UUID / integer, producto al cual pertenece la imagen.

url: string, ruta o URL de la imagen.

altText: string, texto alternativo para accesibilidad.

### Cart

id: UUID / integer, ID del carrito.

userId: UUID / integer, propietario del carrito.

createdAt: timestamp, fecha de creación.

updatedAt: timestamp, última actualización.

### CartItem

id: UUID / integer, ID del item del carrito.

cartId: UUID / integer, carrito al que pertenece.

productId: UUID / integer, producto agregado.

quantity: integer, cantidad solicitada.

priceSnapshot: decimal, precio almacenado al agregar al carrito.

### Order

id: UUID / integer, ID del pedido.

userId: UUID / integer, usuario que hizo el pedido.

total: decimal, suma total del pedido.

status: enum('pending','paid','shipped','cancelled'), estado del pedido.

shippingAddressId: UUID / integer, dirección de envío.

createdAt: timestamp, fecha de creación.

updatedAt: timestamp, fecha última actualización.

### OrderItem

id: UUID / integer, ID del item del pedido.

orderId: UUID / integer, pedido asociado.

productId: UUID / integer, producto comprado.

quantity: integer, cantidad comprada.

priceSnapshot: decimal, precio por unidad al momento del pedido.

### Address

id: UUID / integer, ID de la dirección.

userId: UUID / integer, propietario de la dirección.

line1: string, dirección (calle, número).

city: string, ciudad.

postalCode: string, código postal.

country: string, país.

## Relaciones

User a Cart 1:1 (Cada usuario tiene un carrito activo unico donde agrega productos)

Cart a CartItem 1:N (Un carrito puede contener muchos productos distintos)

CartItem a Product : 1:1 (Cada item del carrito corresponde a un solo producto específico)

Order a OrderItem : 1:N (Un pedido puede incluir muchos items)

OrderItem a Product : 1:1 (Cada item del pedido se refiere a un producto específico)

Product a ProductImage : 1:N (Un producto puede tener muchas imágenes)

User a Address : 1:N (Un usuario puede tener muchas direcciones de envio)

User a Order : 1:N (Un usuario puede hacer muchos pedidos)

### Reglas de negocio

Antes de agregar un producto al carrito: verificar stock > 0.

Al crear un pedido: por cada OrderItem verificar stock, disminuir Product.stock y guardar priceSnapshot.

Solo usuarios con role = 'admin' pueden acceder a rutas /admin/*.

El email en User debe ser único.

Usar priceSnapshot en CartItem u OrderItem para preservar precios al momento de la compra.
