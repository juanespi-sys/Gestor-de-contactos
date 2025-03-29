# Gestor-de-contactos
Proyecto C++ de la clase de programación ll 


#include <iostream>  
#include <fstream>   // Librería para manipular archivos
#include <string>    // Librería para manejar cadenas de texto
using namespace std;

// Clase Contacto
class Contacto {
public:
    string nombre;    // Nombre del contacto
    string telefono;  // Número de teléfono del contacto
    string correo;    // Correo electrónico del contacto
    Contacto* siguiente; // Puntero al siguiente contacto en la lista

    // Constructor para inicializar un nuevo contacto
    Contacto(string n, string t, string c) {
        nombre = n;
        telefono = t;
        correo = c;
        siguiente = nullptr; // Inicialmente no apunta a ningún otro contacto
    }
};

// Clase ListaContactos
class ListaContactos {
private:
    Contacto* cabeza; // Puntero al primer contacto de la lista

public:
    // Constructor: Inicializa la lista vacía
    ListaContactos() : cabeza(nullptr) {} 

    // Método para agregar un contacto evitando duplicados
    void agregarContacto(string nombre, string telefono, string correo) {
        // Verifica si ya existe un contacto con el mismo número
        if (buscarContacto(telefono) != nullptr) {
            cout << "El contacto con el numero " << telefono << " ya existe." << std::endl;
            return;
        }

        // Crea un nuevo contacto
        Contacto* nuevo = new Contacto(nombre, telefono, correo);

        // Si la lista está vacía o el nuevo contacto debe ir primero (ordenado por nombre)
        if (!cabeza || cabeza->nombre > nombre) {
            nuevo->siguiente = cabeza; // El nuevo contacto apunta al anterior primer contacto
            cabeza = nuevo; // Ahora la cabeza de la lista es el nuevo contacto
        }
        else {
            // Buscar la posición correcta para insertar el contacto ordenadamente
            Contacto* actual = cabeza;
            while (actual->siguiente && actual->siguiente->nombre < nombre) {
                actual = actual->siguiente; // Avanza en la lista
            }
            nuevo->siguiente = actual->siguiente; // Enlaza el nuevo contacto al siguiente
            actual->siguiente = nuevo; // Inserta el nuevo contacto en la posición correcta
        }
        cout << "Contacto agregado exitosamente.\n";
    }
    Contacto* buscarContacto(string clave) {
        Contacto* actual = cabeza;
        while (actual != nullptr) {
            if (actual->nombre == clave || actual->telefono == clave) {
                return actual;
            }
            actual = actual->siguiente;
        }
        return nullptr;
    }

    // Método para eliminar un contacto por nombre o teléfono
    void eliminarContacto(string clave) {
        if (!cabeza) { // Verifica si la lista está vacía
            cout << "Lista vacia.\n";
            return;
        }
        Contacto* actual = cabeza; // Inicia desde la cabeza de la lista
        Contacto* anterior = nullptr; // Puntero para rastrear el contacto anterior

        // Busca el contacto con el nombre o teléfono indicado
        while (actual && actual->nombre != clave && actual->telefono != clave) {
            anterior = actual;
            actual = actual->siguiente;
        }

        // Si no se encontró el contacto
        if (!actual) {
            cout << "Contacto no encontrado.\n";
            return;
        }

        // Si el contacto a eliminar es el primero de la lista
        if (!anterior) cabeza = actual->siguiente;
        else anterior->siguiente = actual->siguiente;

        delete actual; // Libera la memoria del contacto eliminado
        cout << "Contacto eliminado.\n";
    }

    // Método para mostrar todos los contactos
    void mostrarContactos() {
        if (!cabeza) { // Verifica si la lista está vacía
            cout << "No hay contactos.\n";
            return;
        }
        Contacto* actual = cabeza; // Inicia desde la cabeza de la lista
        while (actual) { // Recorre la lista
            cout << "Nombre: " << actual->nombre
                << ", Tel: " << actual->telefono
                << ", Email: " << actual->correo << endl;
            actual = actual->siguiente; // Avanza al siguiente contacto
        }
    }

    // Método para guardar los contactos en un archivo
    void guardarcontacto() {
        ofstream archivo("contactos.txt"); // Abre el archivo en modo escritura
        Contacto* actual = cabeza; // Inicia desde la cabeza de la lista
        while (actual) { // Recorre la lista
            archivo << actual->nombre << endl;   // Guarda el nombre
            archivo << actual->telefono << endl; // Guarda el teléfono
            archivo << actual->correo << endl;   // Guarda el correo
            archivo << endl; // Línea en blanco para separar contactos
            actual = actual->siguiente; // Avanza al siguiente contacto
        }
        archivo.close(); // Cierra el archivo
        cout << "Contactos guardados en archivo.\n";
    }

    // Método para cargar los contactos desde un archivo
    void cargarDesdeArchivo(const string & filename) { 
        ifstream archivo("contactos.txt"); // Abre el archivo en modo lectura
        string nombre, telefono, correo;

        // Lee los contactos línea por línea
        while (getline(archivo, nombre) && getline(archivo, telefono) && getline(archivo, correo)) {
            agregarContacto(nombre, telefono, correo); // Agrega cada contacto a la lista
            string lineaVacia; // Leer la línea en blanco que separa los contactos
            getline(archivo, lineaVacia);
        }

        archivo.close(); // Cierra el archivo
        cout << "Contactos cargados desde archivo.\n";
    }

    // Método para modificar un contacto
    void modificarContacto(string telefono) {
        Contacto* c = buscarContacto(telefono); // Busca el contacto por su número
        if (!c) { // Si no se encuentra, muestra un mensaje
            cout << "Contacto no encontrado.\n";
            return;
        }
        cout << "Nuevo numero: ";
        cin >> c->telefono; // Permite ingresar un nuevo número de teléfono
        cout << "Nuevo correo: ";
        cin >> c->correo; // Permite ingresar un nuevo correo electrónico
        cout << "Contacto modificado.\n";
    }
}; 
void menu() {
   
    cout << "Que deseas realizar?" << endl;
    cout << "Agregar un contacto (1)" << endl;
    cout << "Buscar un contacto (2)" << endl;
    cout << "Eliminar un contacto (3)" << endl;
    cout << "Mostrar todos los contactos (4)" << endl;
    cout << "Editar contacto (5)" << endl;
    cout << "Guardar contactos en archivo (6)" << endl; 
    cout << "salir del programa (7)" << endl;
}
int main() {
    cout << "Bienvenido a tu gestor de contactos!" << endl; 
    ListaContactos agenda;
    agenda.cargarDesdeArchivo("contactos.txt");
    int opcion;
    string nombre, telefono, correo;
    //Iniciamos el ciclo do-while antes de la llamada del menu para que el programa se repita hasta que el usuario elija sair del programa
    do { 
        menu();
        cin >>opcion;
        cin.ignore();  // Limpiar el buffer 

        switch (opcion) {
        case 1: //Agregar contacto
            cout << "Okey agregaremos un nuevo contacto" <<endl; 
            cout << "Nombre: ";
            getline(cin, nombre);
            cout << "Telefono: ";
            getline(cin, telefono);
            cout << "Correo: ";
            getline(cin, correo);
            agenda.agregarContacto(nombre, telefono, correo);
            break;
        case 2://buscar un contacto
            cout << "Que contacto buscas?" << endl;
            cout << "Escribe el nombre o telefono: ";
            getline(cin, telefono);
            Contacto* encontrado;
            encontrado = agenda.buscarContacto(telefono);
            if (encontrado) {
                cout << "Contacto encontrado: " << encontrado->nombre
                    << ", Telefono: " << encontrado->telefono
                    << ", Correo: " << encontrado->correo << endl;
            }
            else {
                cout << "Contacto no encontrado." << endl;
            }
            break;
        case 3://eliminar un contacto
            cout << "Que contacto deseas eliminar?" << endl;
            cout << "Escribe el nombre o telefono: ";
            getline(cin, telefono);
            agenda.eliminarContacto(telefono);
            break;
        case 4://mostrar todos los contactos
            cout << "Mostrando contactos..." << endl; 
            agenda.mostrarContactos();
            break;
        case 5: //Editar contacto
            cout << "Que contacto deseas modificar?" << endl;
            cout << "Escribe el nombre o telefono:";
            getline(cin, telefono);
            agenda.modificarContacto(telefono); 
            break;
        case 6://Cargar Contactos en archivo
            cout << "Cargando contactos...." << endl;
            agenda.guardarcontacto();  
            break;

        case 7://Salir del programa
            cout << "Saliendo del programa..............." << endl;
            break;
        default: cout << "No es ninguna opcion disponible" << endl;


        }

    } while (opcion != 7);

        return 0;

} 
