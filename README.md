# Kurs_game_of_life

// Игра Жизнь.cpp : Этот файл содержит функцию "main". Здесь начинается и заканчивается выполнение программы.
//

#include <iostream>
#include <vector>
#include <fstream>
#include <cstdlib>
#include <Windows.h>



using namespace std;

using Field = vector<vector<int>>;


// Посчет живых соседей

int countNeighbors(const Field& field, int r, int c, int rows, int cols)
{
	int count = 0;

	for (int dr = -1; dr <= 1; dr++)
	{
		for (int dc = -1; dc <= 1; dc++)
		{
			if (dr == 0 && dc == 0) continue;

			int nr = r + dr;
			int nc = c + dc;

			if (nr >= 0 && nr < rows && nc >= 0 && nc < cols)
				count += field[nr][nc];
		}
	}
	return count;
}

// Подсчет живых клеток
int countAlive(const Field& field)
{
	int total = 0;
	for (auto& row : field)
		for (auto& cell : row)
			total = total + cell;
	return total;
}

// Проверка равенства полей
bool fieldsEqual(const Field& a, const Field& b)
{
	return a == b;
}

// Вывод поля
void printField(const Field& field, int generation)
{
	int alive = countAlive(field);

	for (auto& row : field)
	{
		for (auto& cell : row)
			cout << (cell ? "*" : "-");
			cout << endl;
	}

	cout << endl;
	cout << "Generation: " << generation
		<< ". Alive cells: " << alive << endl;
}



int main()
{
	setlocale(LC_ALL, "Russian");
	SetConsoleCP(1251);
	SetConsoleOutputCP(1251);

	ifstream file("in.txt");

	if (!file)
	{
		cout << "Ошибка открытия файла\n";
		return 1;
	}

	int rows, cols;
	file >> rows >> cols;

	Field current(rows, vector<int>(cols, 0));
	Field next(rows, vector<int>(cols, 0));

	// Чтение живых клеток

	int r, c;

	while (file >> r >> c)
	{
		if (r >= 0 && r < rows && c >= 0 && c < cols)
			current[r][c] = 1;
	}

	int generation = 1;

	while (true)
	{
		system("cls"); // очистика консоли

		printField(current, generation);

		// Расчет следующего поколения

		for (int i = 0; i < rows; i++)
		{
			for (int j = 0; j < cols; j++)
			{
				int neighbors = countNeighbors(current, i, j, rows, cols);

				if (current[i][j] == 1)
				{
					if (neighbors == 2 || neighbors == 3)
					{
						next[i][j] = 1;
					}
					else
					{
						next[i][j] = 0;
					}
				}
				else
				{
					if (neighbors == 3)
					{
						next[i][j] = 1;
					}
					else
					{
						next[i][j] = 0;
					}
				}
			}

		}


	// Условия завершена

	if (countAlive(next) == 0)
	{
		Sleep(1000);
		system("cls");
		printField(next, generation + 1);
		cout << "Game finished: no alive cells" << endl;
		break;
	}

	if (fieldsEqual(current, next))
	{
		Sleep(1000);
		system("cls");
		printField(next, generation + 1);
		cout << "Game finished: stable cinfiguration" << endl;
		break;
	}

	current = next;
	generation++;

	Sleep(1000);
	}
	return 0;
}

