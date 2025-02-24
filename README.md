import pickle

# ----------------------------
# Clase Producto
# ----------------------------
class Producto:
    def __init__(self, id, nombre, cantidad, precio):
        self.id = id
        self.nombre = nombre
        self.cantidad = cantidad
        self.precio = precio

    # Métodos para obtener los atributos
    def get_id(self):
        return self.id

    def get_nombre(self):
        return self.nombre

    def get_cantidad(self):
        return self.cantidad

    def get_precio(self):
        return self.precio

    # Métodos para actualizar atributos
    def set_cantidad(self, cantidad):
        self.cantidad = cantidad

    def set_precio(self, precio):
        self.precio = precio

    def __str__(self):
        return f"ID: {self.id} | Nombre: {self.nombre} | Cantidad: {self.cantidad} | Precio: ${self.precio:.2f}"


# ----------------------------
# Clase Inventario
# ----------------------------
class Inventario:
    def __init__(self):
        # Utilizamos un diccionario con el ID del producto como clave.
        self.productos = {}

    def agregar_producto(self, producto):
        if producto.get_id() in self.productos:
            print("El producto ya existe en el inventario.")
        else:
            self.productos[producto.get_id()] = producto
            print("Producto agregado exitosamente.")

    def eliminar_producto(self, id_producto):
        if id_producto in self.productos:
            del self.productos[id_producto]
            print("Producto eliminado.")
        else:
            print("Producto no encontrado.")

    def actualizar_producto(self, id_producto, cantidad=None, precio=None):
        if id_producto in self.productos:
            if cantidad is not None:
                self.productos[id_producto].set_cantidad(cantidad)
            if precio is not None:
                self.productos[id_producto].set_precio(precio)
            print("Producto actualizado.")
        else:
            print("Producto no encontrado.")

    def buscar_producto_por_nombre(self, nombre):
        encontrados = [producto for producto in self.productos.values() if producto.get_nombre().lower() == nombre.lower()]
        if encontrados:
            for prod in encontrados:
                print(prod)
        else:
            print("No se encontraron productos con ese nombre.")

    def mostrar_inventario(self):
        if self.productos:
            for producto in self.productos.values():
                print(producto)
        else:
            print("El inventario está vacío.")

    def guardar_inventario(self, archivo):
        try:
            with open(archivo, 'wb') as f:
                pickle.dump(self.productos, f)
            print("Inventario guardado exitosamente.")
        except Exception as e:
            print(f"Error al guardar el inventario: {e}")

    def cargar_inventario(self, archivo):
        try:
            with open(archivo, 'rb') as f:
                self.productos = pickle.load(f)
            print("Inventario cargado exitosamente.")
        except FileNotFoundError:
            print("El archivo no existe. Se creará uno nuevo al guardar.")
        except Exception as e:
            print(f"Error al cargar el inventario: {e}")


# ----------------------------
# Función para cargar productos iniciales de víveres
# ----------------------------
def cargar_productos_iniciales(inventario):
    # Si el inventario está vacío, se cargan productos de ejemplo.
    if not inventario.productos:
        productos_ejemplo = [
            Producto("001", "Arroz", 100, 0.60),
            Producto("002", "Lenteja", 80, 0.80),
            Producto("003", "Azúcar refinada", 70, 2.50),
            Producto("004", "Azúcar integral", 50, 1.00),
            Producto("005", "Aceite", 40, 2.50),
            Producto("006", "Sal", 200, 0.60),
            Producto("007", "Huevos", 60, 3.50),
            Producto("008", "Fideos", 90, 0.60)
        ]
        for prod in productos_ejemplo:
            inventario.agregar_producto(prod)
        print("Productos iniciales cargados al inventario.\n")


# ----------------------------
# Función para el menú interactivo
# ----------------------------
def menu():
    inventario = Inventario()
    archivo = "inventario.pkl"
    
    # Intentamos cargar el inventario guardado previamente.
    inventario.cargar_inventario(archivo)
    
    # Si el inventario está vacío, cargamos productos de ejemplo.
    cargar_productos_iniciales(inventario)
    
    while True:
        print("\n----- MENÚ DE INVENTARIO -----")
        print("1. Agregar producto")
        print("2. Eliminar producto")
        print("3. Actualizar producto")
        print("4. Buscar producto por nombre")
        print("5. Mostrar inventario")
        print("6. Guardar inventario")
        print("7. Cargar inventario")
        print("8. Salir")

        opcion = input("Seleccione una opción: ")

        if opcion == '1':
            try:
                id = input("Ingrese ID del producto: ")
                nombre = input("Ingrese nombre del producto: ")
                cantidad = int(input("Ingrese cantidad: "))
                precio = float(input("Ingrese precio: "))
                producto = Producto(id, nombre, cantidad, precio)
                inventario.agregar_producto(producto)
            except ValueError:
                print("Error en la entrada de datos. Verifique cantidad y precio.")

        elif opcion == '2':
            id = input("Ingrese el ID del producto a eliminar: ")
            inventario.eliminar_producto(id)

        elif opcion == '3':
            id = input("Ingrese el ID del producto a actualizar: ")
            print("Si no desea actualizar un campo, déjelo vacío.")
            cant_input = input("Ingrese nueva cantidad (o presione Enter para omitir): ")
            prec_input = input("Ingrese nuevo precio (o presione Enter para omitir): ")
            cantidad = int(cant_input) if cant_input else None
            precio = float(prec_input) if prec_input else None
            inventario.actualizar_producto(id, cantidad, precio)

        elif opcion == '4':
            nombre = input("Ingrese el nombre del producto a buscar: ")
            inventario.buscar_producto_por_nombre(nombre)

        elif opcion == '5':
            inventario.mostrar_inventario()

        elif opcion == '6':
            inventario.guardar_inventario(archivo)

        elif opcion == '7':
            inventario.cargar_inventario(archivo)

        elif opcion == '8':
            print("Saliendo del programa...")
            break

        else:
            print("Opción no válida. Por favor, intente nuevamente.")


# ----------------------------
# Ejecución principal
# ----------------------------
if __name__ == "__main__":
    menu()
