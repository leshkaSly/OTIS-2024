<p align="center"> Министерство образования Республики Беларусь</p>
<p align="center">Учреждение образования</p>
<p align="center">“Брестский Государственный технический университет”</p>
<p align="center">Кафедра ИИТ</p>
<br><br><br><br><br><br><br>
<p align="center">Лабораторная работа №3</p>
<p align="center">По дисциплине “Общая теория интеллектуальных систем”</p>
<p align="center">Тема: “Применение знаний алгоритмов для графов на практике”</p>
<br><br><br><br><br>
<p align="right">Выполнил:</p>
<p align="right">Студент 2 курса</p>
<p align="right">Группы ИИ-25</p>
<p align="right">Максимчук Е.В.</p>
<p align="right">Проверил:</p>
<p align="right">Иванюк Д. С.</p>
<br><br><br><br><br>
<p align="center">Брест 2024</p>

---

# Общее задание #
1. Разработать и реализовать программный продукт позволяющий
редактировать графовые конструкции различных видов и производить над
ними различные действия. Язык программирования - любой.

2. Редактор должен позволять (задания со **[\*]** являются необязательными):  
  a) одновременно работать с несколькими графами (MDI);  
  b) **[\*]** выделение одновременно нескольких элементов графа, копирование
выделенного фрагмента в clipboard и восстановление из него;  
  c) задавать имена графам;  
  d) сохранять и восстанавливать граф во внутреннем формате программы;  
  e) экспортировать и импортировать граф в текстовый формат (описание
см. ниже);  
  f) создавать, удалять, именовать, переименовывать, перемещать узлы;  
  g) создавать ориентированные и неориентированные дуги, удалять дуги;  
  h) добавлять, удалять и редактировать содержимое узла (содержимое в
виде текста и ссылки на файл);  
  i) задавать цвет дуги и узла, образ узла;  
  j) **[\*]** создавать и отображать петли;  
  k) **[\*]** создавать и отображать кратные дуги.

3. Программный продукт должен позволять выполнять следующие операции:  
  a) выводить информацию о графе:

 + количество вершин, дуг;
 + степени для всех вершин и для выбранной вершины;
 + матрицу инцидентности;
 + матрицу смежности;
 + является ли он деревом, полным, связанным, эйлеровым, **[\*]** планарным;

  b) поиск всех путей (маршрутов) между двумя узлами и кратчайших;  
  c) вычисление расстояния между двумя узлами;  
  d) вычисление диаметра, радиуса, центра графа;  
  e) **[\*]** вычисление векторного и декартово произведения двух графов;  
  f) **[\*]** раскраска графа;  
  g) нахождения эйлеровых, [*] гамильтоновых циклов;  
  h) **[\*]** поиск подграфа в графе, со всеми или некоторыми неизвестными
узлами;  
  i) **[\*]** поиск узла по содержимому;  
  j) **[\*]** объединение, пересечение, сочетание и дополнение графов;  
  k) **[\*]** приведение произвольного графа к определенному типу с
минимальными изменениями:

 + бинарное и обычное дерево;
 + полный граф;
 + планарный граф;
 + связанный граф;

4. Формат текстового представления графа:
<ГРАФ> ::= <ИМЯ ГРАФА> : UNORIENT | ORIENT ; <ОПИСАНИЕ УЗЛОВ> ;
<ОПИСАНИЕ СВЯЗЕЙ> .
<ИМЯ ГРАФА> ::= <ИДЕНТИФИКАТОР>
<ОПИСАНИЕ УЗЛОВ> ::= <ИМЯ УЗЛА> [ , <ИМЯ УЗЛА> …]
<ИМЯ УЗЛА> ::= <ИДЕНТИФИКАТОР>
<ОПИСАНИЕ СВЯЗЕЙ> ::= <ИМЯ УЗЛА> -> <ИМЯ УЗЛА> [ , <ИМЯ УЗЛА> …] ;
[<ОПИСАНИЕ СВЯЗЕЙ> …]

5. Написать отчет по выполненной лабораторной работе в .md формате (readme.md). Разместить его в следующем каталоге: **trunk\ii0xxyy\task_03\doc** (где **xx** - номер группы, **yy** - номер студента, например **ii02102**). 

6. Исходный код разработанной программы разместить в каталоге: **trunk\ii0xxyy\task_03\src**.

---

# Выполнение задания #
```С++
#include <iostream>
#include <queue>
#include <vector>

using namespace std;

struct Node {
    int id;
    vector<int> adjacentNodes;
};

class Graph {
public:
    vector<Node> nodes;

    void addNode(int id) {
        nodes.push_back({ id, {} });
    }

    void addEdge(int start, int end) {
        nodes[start].adjacentNodes.push_back(end);
        nodes[end].adjacentNodes.push_back(start);
    }

    void showGraph() {
        for (int i = 0; i < nodes.size(); ++i) {
            cout << "Node " << nodes[i].id << ": ";
            for (int neighbor : nodes[i].adjacentNodes) {
                cout << neighbor << " ";
            }
            cout << endl;
        }
    }

    vector<int> getEulerianCycle() {
        vector<int> cycle;
        if (!isEulerian()) {
            return cycle;
        }
        vector<bool> visitedNodes(nodes.size(), false);
        int startingNode = 0;

        cycle.push_back(startingNode);
        visitedNodes[startingNode] = true;

        int currentNode = startingNode;
        while (!cycle.empty()) {
            bool foundNeighbor = false;
            for (int i = 0; i < nodes[currentNode].adjacentNodes.size(); ++i) {
                int neighbor = nodes[currentNode].adjacentNodes[i];
                if (!visitedNodes[neighbor]) {
                    foundNeighbor = true;
                    visitedNodes[neighbor] = true;
                    currentNode = neighbor;
                    cycle.push_back(currentNode);
                    break;
                }
            }
            if (!foundNeighbor) {
                cycle.pop_back();
                if (!cycle.empty()) {
                    currentNode = cycle.back();
                }
            }
        }
        return cycle;
    }

    vector<int> getHamiltonianCycle() {
        vector<int> cycle;
        vector<bool> visitedNodes(nodes.size(), false);
        visitedNodes[0] = true;
        cycle.push_back(0);

        if (!hasHamiltonianCycle(0, 1, cycle, visitedNodes)) {
            return cycle;
        }
        return cycle;
    }

    Graph getSpanningTree() {
        Graph spanningTree;
        for (int i = 0; i < nodes.size(); ++i) {
            spanningTree.addNode(i);
        }
        vector<bool> visitedNodes(nodes.size(), false);

        visitedNodes[0] = true;
        queue<int> nodeQueue;
        nodeQueue.push(0);

        while (!nodeQueue.empty()) {
            int currentNode = nodeQueue.front();
            nodeQueue.pop();

            for (int neighbor : nodes[currentNode].adjacentNodes) {
                if (!visitedNodes[neighbor]) {
                    spanningTree.addEdge(currentNode, neighbor);
                    visitedNodes[neighbor] = true;
                    nodeQueue.push(neighbor);
                }
            }
        }
        return spanningTree;
    }

private:
    bool isEulerian() {
        for (int i = 0; i < nodes.size(); ++i) {
            if (nodes[i].adjacentNodes.size() % 2 != 0) {
                return false;
            }
        }
        return true;
    }

    bool hasHamiltonianCycle(int currentNode, int depth, vector<int>& cycle, vector<bool>& visitedNodes) {
        if (depth == nodes.size()) {
            if (cycle[0] == cycle[depth - 1]) {
                return true;
            }
            return false;
        }
        for (int i = 0; i < nodes.size(); ++i) {
            if (nodes[currentNode].adjacentNodes[i] == 1 && !visitedNodes[i]) {
                visitedNodes[i] = true;
                cycle[depth] = i;
                if (hasHamiltonianCycle(i, depth + 1, cycle, visitedNodes)) {
                    return true;
                }
                visitedNodes[i] = false;
            }
        }
        return false;
    }
};

int main() {
    Graph graph;

    graph.addNode(0);
    graph.addNode(1);
    graph.addNode(2);
    graph.addNode(3);
    graph.addNode(4);

    graph.addEdge(0, 1);
    graph.addEdge(1, 2);
    graph.addEdge(2, 3);
    graph.addEdge(3, 4);
    graph.addEdge(4, 0);

    graph.showGraph();

    if (vector<int> eulerianCycle = graph.getEulerianCycle(); !eulerianCycle.empty()) {
        cout << "Eulerian cycle: ";
        for (int node : eulerianCycle) {
            cout << node << " ";
        }
        cout << endl;
    }
    else {
        cout << "The graph does not contain an Eulerian cycle." << endl;
    }

    vector<int> hamiltonianCycle = graph.getHamiltonianCycle();
    if (vector<int> newHamiltonianCycle = graph.getHamiltonianCycle(); !newHamiltonianCycle.empty()) {
        cout << "Hamiltonian cycle: ";
        for (int node : hamiltonianCycle) {
            cout << node << " ";
        }
        cout << endl;
    }
    else {
        cout << "The graph does not contain a Hamiltonian cycle." << endl;
    }
    Graph spanningTree = graph.getSpanningTree();
    spanningTree.showGraph();
    return 0;
}
```


![Вывод:](add_adjes.png)


![Вывод:](add_vertex.png)


![Вывод:](change_weight.png)


![Вывод:](eulerian_path.png)
