# RedSegura Sincelejo

Parcial 2 de Estructura de Datos II: árboles (ABB y AVL) y grafos aplicados a una red de atención de
emergencias de Sincelejo. Los tiempos son datos didácticos de simulación, no información oficial.

- Autor: _tu nombre_
- Docente: Jaider Enrique Reyes Herazo
- Tecnología: Java 17, Maven, JUnit 5, Apache NetBeans

## Cómo ejecutar
```bash
mvn clean test                 # compila y corre las pruebas
mvn exec:java                  # menú interactivo
mvn exec:java -Dexec.args="--demo"   # corre Q1..Q7 de una vez
```
En NetBeans: abrir el proyecto Maven, clic derecho -> Run (clase principal `RedSeguraApplication`).
Si ves tildes rotas en la consola, agrega `-Dfile.encoding=UTF-8` a las opciones de la JVM.

## Arquitectura
```
RedSeguraApplication (CLI)
        |
  RedSeguraCore (fachada) ---- SampleDataFactory (datos del caso)
     |            |
  AvlTree     MixedGraph --- BreadthFirstSearch, DepthFirstSearch, DijkstraSolver,
  BinarySearchTree           GraphRepresentations
                |
          Vertex, Edge (model)
```
Los módulos solo se comunican por métodos públicos; los campos son privados.

## Decisiones de diseño
- `MixedGraph` guarda lista de adyacencia: una arista aparece en ambos extremos, un arco solo en su origen.
- Los pesos deben ser > 0 (Dijkstra lo exige); se valida en `Edge`.
- Bloquear una conexión (`blockEdge`) la excluye de recorridos, Dijkstra y representaciones.
- Matriz de incidencia: arco = -1 en origen y +1 en destino; arista = +1 en ambos extremos.
- Altura: árbol vacío = 0, hoja = 1. Factor de balance = altura(izq) - altura(der).
- Dijkstra relaja con `<` estricto: ante empates se conserva el primer camino encontrado.

## Complejidad
| Componente | Tiempo | Espacio |
|---|---|---|
| ABB insert/search/delete | O(h): promedio O(log n), peor O(n) | O(n) |
| AVL insert/search/delete | O(log n) siempre | O(n) |
| Rotación | O(1) | O(1) |
| Lista de adyacencia | construir O(V+E) | O(V+E) |
| Matriz de adyacencia | O(V^2 + E) | O(V^2) |
| Matriz de incidencia | O(V*E) | O(V*E) |
| BFS / DFS | O(V+E) | O(V) |
| Dijkstra con PriorityQueue | O((V+E) log V) | O(V) |

## Resultados de control
- Dijkstra S02 -> S08: S02, S01, S07, S08 = 24 min.
- Al bloquear R16: S02, S01, S09, S10, S08 = 24 min (hay un empate; cambia la ruta, no el costo).
- Consulta de vértice inexistente (S99): error controlado, el programa continúa.
