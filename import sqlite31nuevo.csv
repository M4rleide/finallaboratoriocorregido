import sqlite3
import csv
import pandas as pd
from datetime import datetime

# Conexión a la base de datos
conn = sqlite3.connect("club_gestion.db")
cursor = conn.cursor()

# Crear tablas
def crear_tablas():
    cursor.execute("""
    CREATE TABLE IF NOT EXISTS Personas (
        id_persona INTEGER PRIMARY KEY AUTOINCREMENT,
        nombre TEXT NOT NULL,
        apellido TEXT NOT NULL,
        documento TEXT UNIQUE NOT NULL,
        fecha_nacimiento DATE NOT NULL,
        telefono TEXT NOT NULL
    )
    """)
    cursor.execute("""
    CREATE TABLE IF NOT EXISTS Domicilios (
        id_domicilio INTEGER PRIMARY KEY AUTOINCREMENT,
        id_persona INTEGER NOT NULL,
        domicilio TEXT NOT NULL,
        codigo_postal TEXT NOT NULL,
        ciudad TEXT NOT NULL,
        FOREIGN KEY (id_persona) REFERENCES Personas (id_persona)
    )
    """)
    conn.commit()

# Validaciones
def validar_nombre_apellido(cadena):
    return all(palabra.isalpha() for palabra in cadena.split())

def validar_numerico(cadena):
    return cadena.isdigit()

def validar_fecha(fecha):
    try:
        fecha_nac = datetime.strptime(fecha, "%Y-%m-%d").date()
        return fecha_nac <= datetime.today().date()
    except ValueError:
        return False

# CRUD Personas
def ingresar_datos():
    nombre = input("Nombre: ").strip()
    while not validar_nombre_apellido(nombre):
        print("Error: El nombre solo debe contener letras y espacios.")
        nombre = input("Nombre: ").strip()

    apellido = input("Apellido: ").strip()
    while not validar_nombre_apellido(apellido):
        print("Error: El apellido solo debe contener letras y espacios.")
        apellido = input("Apellido: ").strip()

    documento = input("Número de documento: ").strip()
    while not validar_numerico(documento):
        print("Error: El documento solo debe contener números.")
        documento = input("Número de documento: ").strip()

    fecha_nacimiento = input("Fecha de nacimiento (YYYY-MM-DD): ").strip()
    while not validar_fecha(fecha_nacimiento):
        print("Error: Fecha inválida. Debe ser en el formato YYYY-MM-DD y no mayor a la fecha actual.")
        fecha_nacimiento = input("Fecha de nacimiento (YYYY-MM-DD): ").strip()

    telefono = input("Teléfono: ").strip()
    while not validar_numerico(telefono):
        print("Error: El teléfono solo debe contener números.")
        telefono = input("Teléfono: ").strip()

    cursor.execute("""
    INSERT INTO Personas (nombre, apellido, documento, fecha_nacimiento, telefono)
    VALUES (?, ?, ?, ?, ?)
    """, (nombre, apellido, documento, fecha_nacimiento, telefono))
    conn.commit()
    print("Persona registrada con éxito.")

    id_persona = cursor.lastrowid
    domicilio = input("Domicilio: ").strip()

    codigo_postal = input("Código Postal: ").strip()
    while not validar_numerico(codigo_postal):
        print("Error: El código postal solo debe contener números.")
        codigo_postal = input("Código Postal: ").strip()

    ciudad = input("Ciudad: ").strip()
    cursor.execute("""
    INSERT INTO Domicilios (id_persona, domicilio, codigo_postal, ciudad)
    VALUES (?, ?, ?, ?)
    """, (id_persona, domicilio, codigo_postal, ciudad))
    conn.commit()
    print("Domicilio registrado con éxito.")

def consultar_datos():
    print("\n=== Personas Registradas ===")
    cursor.execute("""
    SELECT p.id_persona, p.nombre, p.apellido, p.documento, p.fecha_nacimiento, p.telefono, 
           d.domicilio, d.codigo_postal, d.ciudad
    FROM Personas p
    LEFT JOIN Domicilios d ON p.id_persona = d.id_persona
    """)
    datos = cursor.fetchall()
    for registro in datos:
        print(registro)

def eliminar_datos():
    documento = input("Ingrese el número de documento de la persona a eliminar: ").strip()
    cursor.execute("""
    DELETE FROM Domicilios WHERE id_persona = (SELECT id_persona FROM Personas WHERE documento = ?)
    """, (documento,))
    cursor.execute("DELETE FROM Personas WHERE documento = ?", (documento,))
    conn.commit()
    print("Persona y domicilios asociados eliminados con éxito.")

def modificar_datos():
    documento = input("Ingrese el número de documento de la persona a modificar: ").strip()
    cursor.execute("SELECT * FROM Personas WHERE documento = ?", (documento,))
    if cursor.fetchone():
        nombre = input("Nuevo nombre: ").strip()
        while not validar_nombre_apellido(nombre):
            print("Error: El nombre solo debe contener letras y espacios.")
            nombre = input("Nuevo nombre: ").strip()

        apellido = input("Nuevo apellido: ").strip()
        while not validar_nombre_apellido(apellido):
            print("Error: El apellido solo debe contener letras y espacios.")
            apellido = input("Nuevo apellido: ").strip()

        fecha_nacimiento = input("Nueva fecha de nacimiento (YYYY-MM-DD): ").strip()
        while not validar_fecha(fecha_nacimiento):
            print("Error: Fecha inválida. Debe ser en el formato YYYY-MM-DD y no mayor a la fecha actual.")
            fecha_nacimiento = input("Nueva fecha de nacimiento (YYYY-MM-DD): ").strip()

        telefono = input("Nuevo teléfono: ").strip()
        while not validar_numerico(telefono):
            print("Error: El teléfono solo debe contener números.")
            telefono = input("Nuevo teléfono: ").strip()

        cursor.execute("""
        UPDATE Personas
        SET nombre = ?, apellido = ?, fecha_nacimiento = ?, telefono = ?
        WHERE documento = ?
        """, (nombre, apellido, fecha_nacimiento, telefono, documento))
        conn.commit()
        print("Datos actualizados con éxito.")
    else:
        print("El documento no existe.")

def exportar_csv():
    cursor.execute("""
    SELECT p.id_persona, p.nombre, p.apellido, p.documento, p.fecha_nacimiento, p.telefono, 
           d.domicilio, d.codigo_postal, d.ciudad
    FROM Personas p
    LEFT JOIN Domicilios d ON p.id_persona = d.id_persona
    """)
    datos = cursor.fetchall()
    with open("club_gestion.csv", "w", newline="", encoding="utf-8") as archivo:
        escritor = csv.writer(archivo)
        escritor.writerow(["ID", "Nombre", "Apellido", "Documento", "Fecha Nacimiento", "Teléfono", 
                           "Domicilio", "Código Postal", "Ciudad"])
        escritor.writerows(datos)
    print("Datos exportados a club_gestion.csv.")

def listar_datos_ordenados():
    print("""
    1. Apellido Ascendente
    2. Apellido Descendente
    3. Orden por ID
    4. Orden por Edad
    """)
    opcion = input("Seleccione un criterio de ordenamiento: ").strip()
    if opcion == "1":
        cursor.execute("SELECT * FROM Personas ORDER BY apellido ASC")
    elif opcion == "2":
        cursor.execute("SELECT * FROM Personas ORDER BY apellido DESC")
    elif opcion == "3":
        cursor.execute("SELECT * FROM Personas ORDER BY id_persona ASC")
    elif opcion == "4":
        cursor.execute("SELECT * FROM Personas ORDER BY fecha_nacimiento ASC")
    else:
        print("Opción inválida.")
        return
    datos = cursor.fetchall()
    for registro in datos:
        print(registro)

# Menú interactivo
def menu():
    crear_tablas()
    while True:
        print("""
        === MENÚ DE ADMINISTRACIÓN DEL CLUB ===
        1. Ingreso de datos
        2. Consulta de datos
        3. Eliminación de datos
        4. Modificación de datos
        5. Exportar a CSV
        6. Listado de datos ordenados
        7. Salir
        """)
        opcion = input("Seleccione una opción: ").strip()
        if opcion == "1":
            ingresar_datos()
        elif opcion == "2":
            consultar_datos()
        elif opcion == "3":
            eliminar_datos()
        elif opcion == "4":
            modificar_datos()
        elif opcion == "5":
            exportar_csv()
        elif opcion == "6":
            listar_datos_ordenados()
        elif opcion == "7":
            print("Saliendo del sistema...")
            break
        else:
            print("Opción inválida. Inténtelo nuevamente.")

# Ejecutar el programa
if __name__ == "__main__":
    menu()
