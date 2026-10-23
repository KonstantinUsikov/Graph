# Graph

Библиотека алгоритмов на графах на C# (.NET Framework 4.8) и консольное приложение
для их запуска. Обходы, связность, Эйлеровы и Гамильтоновы циклы, остовные деревья,
кратчайшие пути и перечисление путей — всё собрано в классе `Graph`.

## Структура проекта

| Путь | Описание |
|------|----------|
| `GraphConsole.sln` | Решение Visual Studio (два проекта ниже) |
| `GraphClassLibrary/` | Библиотека: класс `Graph` и вспомогательные структуры данных |
| `GraphConsole/` | Консольное приложение (`Program.cs`) — примеры графов и вызовов алгоритмов |
| `Graph/` | Заготовка проекта `GraphLibrary`, в решение не входит |

Вспомогательные структуры данных в `GraphClassLibrary/`:

- в `Graph.cs` — `QueueRef<T>`, `StackRef<T>`, `ListRef`, `BinaryHeap<T>`, `Deque<T>`
  (используются алгоритмами `Graph`);
- в отдельных файлах — `LinkedList<T>`, `DoublyLinkedList<T>`, `CircularLinkedList<T>`,
  `CircularDoublyLinkedList<T>`, `NodeStack<T>`, `QueuNode<T>`, `NodeDeque<T>`.

## Сборка и запуск

Требуется .NET Framework 4.8 и Visual Studio 2022 (или JetBrains Rider).

1. Откройте `GraphConsole.sln`.
2. Сделайте `GraphConsole` стартовым проектом.
3. Запустите (F5 / Ctrl+F5).

Из командной строки (Developer Command Prompt):

```
msbuild GraphConsole.sln
GraphConsole\bin\Debug\GraphConsole.exe
```

## Представление графа

Вершины нумеруются `0..N-1`. Граф хранится одновременно в двух видах:

- `adjMatrix` — матрица смежности `int[N, N]`, отсутствие ребра обозначается `Graph.INF`;
- `adjList` — список смежности `Dictionary<int, List<Tuple<int, int>>>`:
  вершина → список пар (сосед, вес ребра).

Конструкторы:

- `Graph()` — пустой граф;
- `Graph(int N)` — `N` вершин без рёбер;
- `Graph(int[,] adjMatrix)` — из матрицы смежности (`0` и `INF` означают отсутствие ребра);
- `Graph(int N, Dictionary<int, List<Tuple<int, int>>> adjList)` — из списка смежности.

## Возможности

Большинство методов возвращают `Tuple<…, string>`: первые элементы — результат
(массивы, списки, новый граф), последний — готовый для вывода текст на русском.
Методы обходов возвращают только текст.

**Редактирование графа**
- `AddVertex()`, `DeleteVertex(v)`
- `AddEdge(u, v, len, isDirected = true)`, `DeleteEdge(u, v, isDirected = true)` —
  при `isDirected = false` добавляется/удаляется и обратное ребро

**Представления и свойства**
- `GetAdjMatrixString()`, `GetAdjListString()` — матрица и список смежности в виде текста
- `IncidenceMatrix()` — список рёбер и матрица инцидентности
- `GetVertexPower()` — таблица степеней вершин (всего / выходит / входит)
- `IsDirected()`, `IsUndirected()`
- `GetReverse()` — обратный (транспонированный) граф

**Обходы** (`checkFull = true` — обойти все компоненты связности)
- Поиск в ширину: `BFS`, `BFS_R` (рекурсивный), `BFSAdjMatrix` (по матрице смежности) —
  также выводят минимальное число переходов и пути с минимальным числом рёбер
- Поиск в глубину: `DFS`, `DFS_R` (рекурсивный), `DFSAdjMatrix` (по матрице смежности) —
  также выводят время захода в вершину и выхода из неё

**Связность**
- `SCC()` — сильно связные компоненты

**Циклы**
- Эйлеровы: `EulerCicle(v)` (рекурсивный), `EulerCicle2(v)` (нерекурсивный),
  `EulerCicleFleury(v)` (алгоритм Флёри)
- Фундаментальные: `FS_Cycles(v)`
- Гамильтоновы: `FindHameltonianСyclesDefault(v)` (перебор с возвратом),
  `FindHameltonianСyclesArithmetic(v)` (арифметический метод),
  `FindHameltonianСyclesRobertsFlores(v)` (метод Робертса — Флореса)

**Минимальное остовное дерево**
- `MST_Kruskal()` — алгоритм Крускала
- `PrimMST(v)` — алгоритм Прима (с выводом разрезов на каждом шаге)

**Кратчайшие пути**
- `DijkstraAdjacencyList(v)` — Дейкстра, O(V²)
- `BellmanFord(v)` — Беллман — Форд, O(E·V); при отрицательном цикле возвращает `null`
- `FloydWarshall()` — Флойд — Уоршелл, O(V³), все пары; при отрицательном цикле возвращает `null`
- `Levit(v)` — алгоритм Левита
- `Johnson()` — перевзвешивание рёбер по Джонсону; возвращает новый граф без
  отрицательных весов, к которому затем применяется Дейкстра

**Перечисление путей** (возвращают `List<string>`; необязательные фильтры
`including` / `excluding` — вершины, через которые путь должен / не должен проходить)
- `RoadsFrom(u)`, `RoadsTo(v)`, `RoadsFromTo(u, v)`
- `RoadsLength(len)`, `RoadsFromLength(u, len)`, `RoadsToLength(v, len)`, `RoadsFromToLen(u, v, len)`
- `AllRoads()`

**Прочее**
- `numberofPaths(source, destination)` — число путей в ациклическом графе
- `TopologicSort()` — топологическая сортировка
- `Graph.SumGraphs(graphs)`, `Graph.SumGraphs2(graphs)` — объединение графов

## Пример

Тело метода `Main` в `GraphConsole/Program.cs` (нужен `using GraphClassLibrary;`):

```csharp
Graph g = new Graph(6);
g.AddEdge(0, 1, 1, false);
g.AddEdge(0, 2, 1, false);
g.AddEdge(1, 2, 1, false);
g.AddEdge(1, 3, 1, false);
g.AddEdge(1, 4, 1, false);
g.AddEdge(2, 4, 1, false);
g.AddEdge(2, 5, 1, false);
g.AddEdge(3, 4, 1, false);
g.AddEdge(4, 5, 1, false);

Console.WriteLine(g.GetAdjMatrixString());
Console.WriteLine(g.BFS(0, true));
Console.WriteLine(g.EulerCicle(0).Item2);
Console.WriteLine(g.DijkstraAdjacencyList(0).Item3);
```

В `GraphConsole/Program.cs` есть несколько закомментированных примеров графов
(с отрицательными весами, для поиска сильно связных компонент и др.) и вызовы всех
алгоритмов — раскомментируйте нужные строки и запустите приложение.
