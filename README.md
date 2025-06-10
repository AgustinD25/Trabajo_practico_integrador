# Trabajo_practico_integrador
Repositorio que contiene una simulación de una base de datos de usuarios en Python. Se implementan algoritmos de búsqueda (lineal y binaria) y ordenamiento (Bubble Sort, Quick Sort, Merge Sort) para comparar su rendimiento. Incluye pruebas de eficiencia, documentación de metodología y resultados.
Integrantes: Magdalena Darchez, comision 7, Agustin Diaz, comision 7
Nuestro objetivo con este trabajo es comprender la importancia de la eficiencia en el desarrollo de software y demuestrae como elegir el método adecuado puede mejorar significativamente el rendimiento en procesamiento de datos.

Codigo fuente explicado con comentarios: 
#TRABAJO PRACTICO INTEGRADOR DE PROGRAMACION 1
import random
import time
import matplotlib.pyplot as plt

# ------------------------------------------------------------------
# Generación de la base de datos ficticia
# ------------------------------------------------------------------
def generar_base_datos(n):
    """
    Genera una lista de 'n' registros de usuarios.
    Cada registro es un diccionario con los siguientes campos:
      - ID: valor único (1, 2, 3, …, n)
      - Nombre: Cadena 'Usuario' seguida del número de registro
      - Edad: Número aleatorio entre 18 y 60
      - Email: Correo simulado en base al ID
    """
    return [
        {
            "ID": i,
            "Nombre": f"Usuario{i}",
            "Edad": random.randint(18, 60),
            "Email": f"user{i}@correo.com"
        }
        for i in range(1, n + 1)
    ]

# ------------------------------------------------------------------
# Algoritmos de Búsqueda
# ------------------------------------------------------------------
def busqueda_lineal(registros, objetivo):
    """
    Recorre la lista 'registros' secuencialmente hasta encontrar aquel
    cuyo 'ID' sea igual a 'objetivo'.
    """
    for registro in registros:
        if registro["ID"] == objetivo:
            return registro
    return None

def busqueda_binaria(registros, objetivo):
    """
    Aplica búsqueda binaria en 'registros' suponiendo que la lista se encuentra ordenada
    por 'ID'. Se reduce el rango de búsqueda a la mitad en cada iteración.
    """
    inicio = 0
    fin = len(registros) - 1
    while inicio <= fin:
        medio = (inicio + fin) // 2
        if registros[medio]["ID"] == objetivo:
            return registros[medio]
        elif registros[medio]["ID"] < objetivo:
            inicio = medio + 1
        else:
            fin = medio - 1
    return None

# ------------------------------------------------------------------
# Algoritmos de Ordenamiento
# ------------------------------------------------------------------
def bubble_sort(registros):
    """
    Ordena la lista 'registros' usando Bubble Sort.
    Es un algoritmo sencillo, aunque poco eficiente para grandes conjuntos.
    Se realiza sobre una copia para no modificar la lista original.
    """
    data = registros.copy()
    n = len(data)
    for i in range(n):
        for j in range(0, n - i - 1):
            if data[j]["ID"] > data[j+1]["ID"]:
                data[j], data[j+1] = data[j+1], data[j]
    return data

def quick_sort(registros):
    """
    Ordena la lista 'registros' usando Quick Sort.
    Método 'divide y vencerás' que selecciona un pivote (en este caso, el ID central)
    y separa la lista en sublistas de elementos menores, iguales y mayores al pivote.
    """
    if len(registros) <= 1:
        return registros
    pivote = registros[len(registros) // 2]["ID"]
    izquierda = [x for x in registros if x["ID"] < pivote]
    medio = [x for x in registros if x["ID"] == pivote]
    derecha = [x for x in registros if x["ID"] > pivote]
    return quick_sort(izquierda) + medio + quick_sort(derecha)

def merge_sort(registros):
    """
    Ordena la lista 'registros' usando Merge Sort.
    Divide la lista en dos mitades, las ordena recursivamente y luego fusiona ambas.
    """
    if len(registros) <= 1:
        return registros
    mid = len(registros) // 2
    izquierda = merge_sort(registros[:mid])
    derecha = merge_sort(registros[mid:])
    return merge(izquierda, derecha)

def merge(izquierda, derecha):
    """
    Fusiona dos listas ordenadas ('izquierda' y 'derecha') en una sola lista ordenada.
    """
    resultado = []
    i = j = 0
    while i < len(izquierda) and j < len(derecha):
        if izquierda[i]["ID"] < derecha[j]["ID"]:
            resultado.append(izquierda[i])
            i += 1
        else:
            resultado.append(derecha[j])
            j += 1
    resultado.extend(izquierda[i:])
    resultado.extend(derecha[j:])
    return resultado

# ------------------------------------------------------------------
# Pruebas de Búsqueda
# ------------------------------------------------------------------
def test_busquedas():
    # Generamos base de datos de 100 registros
    data = generar_base_datos(100)
    # Seleccionamos un ID al azar para buscar
    objetivo = random.randint(1, 100)
    print(f"\n=== Búsqueda del usuario con ID: {objetivo} ===")
    
    # Búsqueda Lineal
    inicio = time.time()
    resultado_lineal = busqueda_lineal(data, objetivo)
    tiempo_lineal = time.time() - inicio

    # Para la búsqueda binaria, se ordena la lista (usamos Bubble Sort para mantener la simplicidad)
    data_ordenada = bubble_sort(data)
    inicio = time.time()
    resultado_binaria = busqueda_binaria(data_ordenada, objetivo)
    tiempo_binaria = time.time() - inicio

    print("\nBúsqueda Lineal:")
    print("  Resultado:", resultado_lineal)
    print("  Tiempo:", f"{tiempo_lineal:.6f} segundos")

    print("\nBúsqueda Binaria (en datos ordenados):")
    print("  Resultado:", resultado_binaria)
    print("  Tiempo:", f"{tiempo_binaria:.6f} segundos")

# ------------------------------------------------------------------
# Comparación y Visualización de Tiempos de Ordenamiento
# ------------------------------------------------------------------
def test_ordenamientos_y_grafico():
    # Definimos distintos tamaños de base de datos para evaluar el rendimiento.
    n_values = [50, 100, 150, 200, 250, 300]
    bubble_times = []
    quick_times  = []
    merge_times  = []
    
    for n in n_values:
        data = generar_base_datos(n)
        
        # Medir Bubble Sort (recordá que es ineficiente y crece con O(n^2))
        inicio = time.time()
        bubble_sort(data)
        bubble_times.append(time.time() - inicio)
        
        # Medir Quick Sort
        inicio = time.time()
        quick_sort(data)
        quick_times.append(time.time() - inicio)
        
        # Medir Merge Sort
        inicio = time.time()
        merge_sort(data)
        merge_times.append(time.time() - inicio)
    
    # Mostrar los tiempos en consola
    print("\nComparación de tiempos de ordenamiento:")
    for i, n in enumerate(n_values):
        print(f"Registros: {n:3d} | Bubble: {bubble_times[i]:.6f}s | Quick: {quick_times[i]:.6f}s | Merge: {merge_times[i]:.6f}s")
    
    # Crear un gráfico para visualizar el rendimiento
    plt.figure(figsize=(10, 6))
    plt.plot(n_values, bubble_times, marker='o', label="Bubble Sort")
    plt.plot(n_values, quick_times, marker='s', label="Quick Sort")
    plt.plot(n_values, merge_times, marker='^', label="Merge Sort")
    plt.xlabel("Cantidad de Registros")
    plt.ylabel("Tiempo de Ordenamiento (segundos)")
    plt.title("Comparación de algoritmos de ordenamiento")
    plt.legend()
    plt.grid(True)
    plt.show()

# ------------------------------------------------------------------
# Función principal para ejecutar las pruebas
# ------------------------------------------------------------------
if __name__ == "__main__":
    # Ejecutar pruebas de búsqueda
    test_busquedas()
    # Ejecutar comparación de tiempos y graficación
    test_ordenamientos_y_grafico()

