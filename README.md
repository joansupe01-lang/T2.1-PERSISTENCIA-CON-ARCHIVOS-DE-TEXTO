#include <iostream>
#include <string>
#include <fstream>
#include <iomanip>
#include <stdexcept>
 
using namespace std;
 
// ─────────────────────────────────────────────
// Estructura para registrar la información de cada estudiante
// ─────────────────────────────────────────────
struct Estudiante {
    string nombre;
    int    edad;
    string carrera;
    float  promedio;
};
 
// ─────────────────────────────────────────────
// Validar nombre (solo letras y espacios)
// ─────────────────────────────────────────────
string LeerNombre(const string& mensaje) {
    string nombre;
    while (true) {
        cout << mensaje;
        getline(cin >> ws, nombre);
 
        if (nombre.empty()) {
            cout << "Error: El nombre no puede estar vacio.\n";
            continue;
        }
 
        bool valido = true;
        for (char c : nombre) {
            if (!isalpha(c) && !isspace(c)) {
                valido = false;
                break;
            }
        }
 
        if (!valido)
            cout << "Error: El nombre contiene caracteres invalidos.\n";
        else
            return nombre;
    }
}
 
// ─────────────────────────────────────────────
// Validar edad (entero en rango [min, max])
// ─────────────────────────────────────────────
int LeerEdad(const string& mensaje, int min, int max) {
    string texto;
    while (true) {
        cout << mensaje;
        getline(cin >> ws, texto);
 
        if (texto.empty()) {
            cout << "Error: El campo no puede estar vacio.\n";
            continue;
        }
 
        try {
            int edad = stoi(texto);
            if (edad < min || edad > max) {
                cout << "Error: Ingrese una edad entre " << min << " y " << max << ".\n";
                continue;
            }
            return edad;
        } catch (const invalid_argument&) {
            cout << "Error: Ingrese un valor numerico valido.\n";
        } catch (const out_of_range&) {
            cout << "Error: El valor ingresado es demasiado grande.\n";
        }
    }
}
 
// ─────────────────────────────────────────────
// Validar promedio (flotante en rango [min, max])
// ─────────────────────────────────────────────
float LeerPromedio(const string& mensaje, float min, float max) {
    string texto;
    while (true) {
        cout << mensaje;
        getline(cin >> ws, texto);
 
        if (texto.empty()) {
            cout << "Error: El campo no puede estar vacio.\n";
            continue;
        }
 
        try {
            float promedio = stof(texto);
            if (promedio < min || promedio > max) {
                cout << "Error: Ingrese un promedio entre " << min << " y " << max << ".\n";
                continue;
            }
            return promedio;
        } catch (const invalid_argument&) {
            cout << "Error: Ingrese un valor numerico valido.\n";
        } catch (const out_of_range&) {
            cout << "Error: El valor ingresado es demasiado grande.\n";
        }
    }
}
 
// ─────────────────────────────────────────────
// Validar carrera (solo letras y espacios)
// ─────────────────────────────────────────────
string LeerCarrera(const string& mensaje) {
    string carrera;
    while (true) {
        cout << mensaje;
        getline(cin >> ws, carrera);
 
        if (carrera.empty()) {
            cout << "Error: La carrera no puede estar vacia.\n";
            continue;
        }
 
        bool valido = true;
        for (char c : carrera) {
            if (!isalpha(c) && !isspace(c)) {
                valido = false;
                break;
            }
        }
 
        if (!valido)
            cout << "Error: La carrera contiene caracteres invalidos.\n";
        else
            return carrera;
    }
}
 
// ─────────────────────────────────────────────
// Guardar registro en archivo (modo append)
// Escribe encabezado solo si el archivo es nuevo
// ─────────────────────────────────────────────
void GuardarRegistro(const string& nombre, int edad,
                     const string& carrera, float promedio) {
 
    // Determinar si el archivo ya existe
    bool archivoNuevo = !ifstream("estudiantes.txt").is_open();
 
    ofstream archivo("estudiantes.txt", ios::app);
    if (!archivo.is_open())
        throw runtime_error("Error critico: No se pudo abrir el archivo de persistencia.");
 
    if (archivoNuevo) {
        archivo << "===================================================================\n";
        archivo << "         REPORTE DE ESTUDIANTES REGISTRADOS\n";
        archivo << "===================================================================\n";
        archivo << left
                << setw(20) << "NOMBRE"  << " | "
                << setw(4)  << "EDAD"    << " | "
                << setw(20) << "CARRERA" << " | "
                << "PROMEDIO\n";
        archivo << "-------------------------------------------------------------------\n";
    }
 
    archivo << left
            << setw(20) << nombre  << " | "
            << setw(4)  << edad    << " | "
            << setw(20) << carrera << " | "
            << fixed << setprecision(2) << promedio << "\n";
 
    archivo.close();
    cout << "\n[SISTEMA] Registro guardado con exito.\n";
}
 
// ─────────────────────────────────────────────
// Mostrar reporte completo desde el archivo
// ─────────────────────────────────────────────
void MostrarReporte() {
    ifstream archivo("estudiantes.txt");
    if (!archivo.is_open()) {
        cout << "\n[!] No hay registros guardados todavia.\n";
        return;
    }
 
    cout << "\n";
    string linea;
    while (getline(archivo, linea))
        cout << linea << "\n";
 
    archivo.close();
    cout << "===================================================================\n";
}
 
// ─────────────────────────────────────────────
// Leer datos del estudiante y llamar GuardarRegistro
// ─────────────────────────────────────────────
void EjecutarRegistro() {
    string nombre   = LeerNombre  ("Ingrese el nombre completo: ");
    int    edad     = LeerEdad    ("Ingrese la edad (17-90): ", 17, 90);
    string carrera  = LeerCarrera ("Ingrese la carrera: ");
    float  promedio = LeerPromedio("Ingrese el promedio (0.0 - 10.0): ", 0.0f, 10.0f);
 
    try {
        GuardarRegistro(nombre, edad, carrera, promedio);
    } catch (const exception& e) {
        cout << "\a" << e.what() << "\n";
    }
}
 
// ─────────────────────────────────────────────
// Menú principal
// ─────────────────────────────────────────────
void MostrarMenu() {
    cout << "\n========================================\n";
    cout << "  SISTEMA DE GESTION ACADEMICA - UTA  \n";
    cout << "========================================\n";
    cout << "  1. Registrar Nuevo Estudiante\n";
    cout << "  2. Ver Reporte General\n";
    cout << "  3. Salir\n";
    cout << "----------------------------------------\n";
    cout << "  Seleccione una opcion: ";
}
 
// ─────────────────────────────────────────────
// Punto de entrada
// ─────────────────────────────────────────────
int main() {
    int opcion;
 
    do {
        MostrarMenu();
 
        if (!(cin >> opcion)) {
            cout << "\n[!] Error: Entrada no valida. Ingrese un numero.\n";
            cin.clear();
            cin.ignore(1000, '\n');
            continue;
        }
        cin.ignore(1000, '\n'); // limpiar el '\n' que deja cin >>
 
        switch (opcion) {
            case 1: EjecutarRegistro(); break;
            case 2: MostrarReporte();   break;
            case 3: cout << "\n[INFO] Finalizando programa. Datos asegurados.\n"; break;
            default: cout << "\n[!] Opcion invalida. Elija entre 1 y 3.\n";
        }
 
    } while (opcion != 3);
 
    return 0;
}
