# KB_F
## Penugasan Kelas


### List Tugas
* [Tugas 1](#Tugas-1)
    * [8 Puzzle DFS](#8-puzzle-DFS)
    * [8 Puzzle BFS](#8-puzzle-BFS)
    * [8 Puzzle IDS](#8-puzzle-IDS)
    * [8 QUEEN](#8-QUEEN)
* [Tugas 2](#Tugas-2)
   * [8 Puzzle Heuristic 1](#8-Puzzle-Heuristic-1)
   * [8 Puzzle Heuristic 2](#8-Puzzle-Heuristic-2)
   * [8 Queen Hill Climbing](#8-Queen-Hill-Climbing)
* [Tugas 3](#Tugas-3)
   * [Tic Tac Toe](#Tic-Tac-Toe)
* [Tugas 4](#Tugas-4)
   * [4 Queen](#4-Queen)

### Tugas 1

**8 Puzzle DFS**

8 Puzzle adalah sebuah permainan sliding puzzle yang terdiri dari sebuah bingkai
yang berisi kotak kotak angka dalam keadaan bergerak dimana ada sebuah kotak
yang kosong. Tujuan dari game ini adalah dengan memakai gerakan geser yang 
menggunakan kotak kosong untuk mencapai goal state. Dalam hal ini, algoritma
yang digunakan adalah DFS. Pencarian dilakukan pada satu node dalam setiap level
yang paling kiri. Jika pada level yang paling dalam, solusi belum ditemukan,
maka pencarian dilanjutkan pada node sebelah kanan, dan node kiri dapat 
dihapus. Demikian seterusnya sampai menemukan solusi

source code:

```c++
#include <bits/stdc++.h>
using namespace std;
#define N 3

```
Disini saya menggunakan bahasa c++ dan mendefine N=3 diawal
yaitu jumlah matriks yang akan digunakan (3x3).

```c++
struct Node
{
    // menyimpan parent node ke current node
    // membantu mengontrol jejak jalannya ketika jawaban ditemukan
    Node* parent;
    
    // menyimpan matriks
    int mat[N][N];
    
    // menyimpan kotak yang kosong
    int x, y;
    
    // menyimpan angka yang berada di kotak yang salah
    int cost;
    
    // menyimpan angka yang bergerak
    int level;
};
```
Program diatas adalah untuk membuat nodes tree

```c++
int printMatrix(int mat[N][N])
{
    for (int i = 0; i < N; i++)
    {
        for (int j = 0; j < N; j++)
            printf("%d ", mat[i][j]);
        printf("\n");
    }
    return 0;
}
```
Fungsi diatas adalah untuk mencetak matriks NxN

```c++
Node* newNode(int mat[N][N], int x, int y, int newX,
              int newY, int level, Node* parent)
{
    Node* node = new Node;
    
    // set pointer untuk path ke root
    node->parent = parent;
    
    // copy data dari parent node ke current node
    memcpy(node->mat, mat, sizeof node->mat);
    
    // pindahkan kotak
    swap(node->mat[x][y], node->mat[newX][newY]);
    
    // set angka untuk kotak yang salah posisi
    node->cost = INT_MAX;
    
    // set angka perpindahan
    node->level = level;
    
    // update posisi kotak kosong
    node->x = newX;
    node->y = newY;
    
    return node;
}
```

Fungsi diatas untuk menempatkan node baru


```c++
int calculateCost(int initial[N][N], int final[N][N])
{
    int count = 0;
    for (int i = 0; i < N; i++)
        for (int j = 0; j < N; j++)
            if (initial[i][j] && initial[i][j] != final[i][j])
                count++;
    return count;
}
```
Fungsi diatas adalah untuk menghitung angka yang berada
di kotak yang salah


```c++
int isSafe(int x, int y)
{
    return (x >= 0 && x < N && y >= 0 && y < N);
}
```
Fungsi diatas adalah untuk mengecek jika x dan y adalah kordinat yang valid


```c++
void printPath(Node* root)
{
    if (root == NULL)
        return;
    printPath(root->parent);
    printMatrix(root->mat);
    
    printf("\n");
}
```
Mencetak Path dari root node ke node tujuan

```c++
void solve(int initial[N][N], int x, int y,
           int final[N][N])
{
    priority_queue<Node*, std::vector<Node*>, comp> pq;
    Node* root = newNode(initial, x, y, x, y, 0, NULL);
    root->cost = calculateCost(initial, final);
    pq.push(root);
    
    // Finds a live node with least cost,
    // add its childrens to list of live nodes and
    // finally deletes it from the list.
    while (!pq.empty())
    {
        Node* min = pq.top();
        pq.pop();
        
        // if min is an answer node
        if (min->cost == 0)
        {
            // print the path from root to destination;
            printPath(min);
            return;
        }
        
        // do for each child of min
        // max 4 children for a node
        for (int i = 0; i < 4; i++)
        {
            if (isSafe(min->x + row[i], min->y + col[i]))
            {
                // create a child node and calculate
                // its cost
                Node* child = newNode(min->mat, min->x,
                                      min->y, min->x + row[i],
                                      min->y + col[i],
                                      min->level + 1, min);
                child->cost = calculateCost(child->mat, final);
                
                // Add child to list of live nodes
                pq.push(child);
            }
        }
    }
}
```
Solving Function

```c++
int main()
{
    // Initial configuration
    // Value 0 is used for empty space
    int initial[N][N] =
    {
        {2, 8, 3},
        {1, 6, 4},
        {7, 0, 5}
    };
    
    // Solvable Final configuration
    // Value 0 is used for empty space
    int final[N][N] =
    {
        {1, 2, 3},
        {8, 0, 4},
        {7, 6, 5}
    };
    
    // Blank tile coordinates in initial
    // configuration
    int x = 2, y = 1;
    
    solve(initial, x, y, final);
    
    return 0;
}
```

Output
![Screen Shot 2020-03-24 at 12 30 05](https://user-images.githubusercontent.com/62136051/77388625-9becd680-6dcb-11ea-9c5f-827d53934573.png)

**8 Puzzle BFS**

Dalam program ini, melakukan pencarian state yang benar untuk menyelesaikan 8 puzzle dengan metode `(Breadth First Search)`. Setelah elemen dimasukkan pada deque dengan cara `push_back` ,dilakukan pencarian secara bfs.
Pencarian yang dilakukan dengan memperluas jangkauan, maksudnya pencariannya dimulai dari parent kemudian ke child namun ketika sampai di child tidak meneruskan ke child bawahnya tapi ke child sebelahnya.

```
int bfs(){
		deque<Node> toexpand;
		deque<State> expanded;
		
		toexpand.push_back(*this);
		while ( !toexpand.empty() ){
			if ( toexpand.front().s.goal()==1 ){ 
				cout << "------|BFS|------" << endl;
				cout << "Solution found!" << endl;
				toexpand.front().print();
				cost = toexpand.front().cost;
				toexpand.clear();
				return cost;
			}
			else{
				if ( !(toexpand.front().expanded(&expanded)) ){
					toexpand.front().expand(&toexpand);
					expanded.push_front( toexpand.front().s );
					toexpand[1].cost=toexpand[0].cost+1;
				}
				toexpand.pop_front();
			}
		}
		if ( toexpand.empty() ) cout << endl << "Solution NOT found!" << endl;
		return 0;
	}
```

**8 Puzzle IDS**

Dalam program berikut, melakukan pencarian state yang benar untuk menyelesaikan 8 puzzle dengan metode `Iterative Deepening Search`. Metode ini adalah sama seperti metode `Depth First Search` namun dalam implementasinya ditambahkan limit dalam pencarian statenya yang dilakukan bertahap dari iterasi 0 hingga mencapai goal yang ditentukan.
`Iterative Deepening Search` (IDS) merupakan sebuah strategi umum yang biasanya dikombinasikan dengan Depth First tree search, yang akan menemukan berapa depth limit terbaik untuk digunakan. Hal ini dilakukan dengan secara menambah limit secara bertahap, mulai dari 0,1, 2, dan seterusnya sampai goal sudah ditemukan.

```
int dfs(int idsdepth){
		deque<Node> toexpand;
		
		if (idsdepth==-1) idsdepth = sizeof(int);
		
		toexpand.push_back(*this);
		while ( !toexpand.empty() ){
				if (toexpand.back().depth < idsdepth){
					if ( toexpand.back().s.goal()==1 ){ 
						if (idsdepth != sizeof(int))  
							cout << "------|IDS|------" << endl;
						cout << "Solution found!" << endl;
						toexpand.back().print();
						toexpand.clear();
						return cost;
					}
					else{
						Node t;
						t= toexpand.back().copy();
						toexpand.pop_back();
						t.expand(&toexpand);
					}
				}
				else return 0;
		}
		if ( toexpand.empty() ) cout << endl << "Solution NOT found!" << endl;
		return 0;
	}
	
	int ids(){
		for(int i=0;;i++){
			int t = dfs(i);
			if (t!=0) return t; 
		}
	}
```


**8 QUEEN**

8 Queen merupakan suatu bentuk masalah kombinatiorial klasik yang menempatkan 8 bidak
pada papan catur berukuran 8x8. Penjelasan mengenai jalan kerja program sudah tertera pada program.
berikut merupakan outputnya:

![Screen Shot 2020-03-24 at 12 39 34](https://user-images.githubusercontent.com/62136051/77389078-00f4fc00-6dcd-11ea-9f26-dbd1aa240832.png)


### Tugas 3

**Tic Tac Toe**

Tic-tac-toe (juga dikenal sebagai noughts dan crosses atau Xs dan Os) adalah permainan kertas dan pensil untuk dua pemain, 
X dan O, yang bergiliran menandai ruang dalam grid 3 × 3. Pemain yang berhasil menempatkan tiga tanda mereka di baris 
horisontal, vertikal, atau diagonal memenangkan pertandingan. Disini saya menggunakan Algoritma minimax. 
Algoritma Minimax merupakan algoritma yang digunakan untukmenentukan pilihan agar memperkecil kemungkinan kehilangan 
nilaimaksimal. Algoitma ini diterpkan dalam permainan yang melibatkan duapemain seperti tic tac toe, checkers,
go dan permainan yangmenggunakan strategi atau logika lainnya.

```c++
bool isMovesLeft(char board[3][3])
{
    for (int i = 0; i<3; i++)
        for (int j = 0; j<3; j++)
            if (board[i][j]=='_')
                return true;
    return false;
}
```

Fungsi diatas mengembalikan true apabila ada perpindahan yang masih ada didalam papan. Mengembalikan
salah ketika tidak ada perpindahan yang bisa dilakukan lagi.


```c++
int evaluate(char b[3][3])
{
    // Checking for Rows for X or O victory.
    for (int row = 0; row<3; row++)
    {
        if (b[row][0]==b[row][1] &&
            b[row][1]==b[row][2])
        {
            if (b[row][0]==player)
                return +10;
            else if (b[row][0]==opponent)
                return -10;
        }
    }
```

Diatas merupakan fungsi evaluasi

```c++
   for (int col = 0; col<3; col++)
    {
        if (b[0][col]==b[1][col] &&
            b[1][col]==b[2][col])
        {
            if (b[0][col]==player)
                return +10;
            
            else if (b[0][col]==opponent)
                return -10;
        }
    }
```
Fungsi diatas digunakan untuk mengecek kolom x dan o untuk menentukan pemenang

```c
    if (b[0][0]==b[1][1] && b[1][1]==b[2][2])
    {
        if (b[0][0]==player)
            return +10;
        else if (b[0][0]==opponent)
            return -10;
    }
    
    if (b[0][2]==b[1][1] && b[1][1]==b[2][0])
    {
        if (b[0][2]==player)
            return +10;
        else if (b[0][2]==opponent)
            return -10;
    }
```
Fungsi diatas digunakan untuk mengecek diagonal x dan o untuk menentukan pemenang


```c++
int minimax(char board[3][3], int depth, bool isMax)
{
    int score = evaluate(board);
    
    // If Maximizer has won the game return his/her
    // evaluated score
    if (score == 10)
        return score;
    
    // If Minimizer has won the game return his/her
    // evaluated score
    if (score == -10)
        return score;
    
    // If there are no more moves and no winner then
    // it is a tie
    if (isMovesLeft(board)==false)
        return 0;
    
    // If this maximizer's move
    if (isMax)
    {
        int best = -1000;
        
        // Traverse all cells
        for (int i = 0; i<3; i++)
        {
            for (int j = 0; j<3; j++)
            {
                // Check if cell is empty
                if (board[i][j]=='_')
                {
                    // Make the move
                    board[i][j] = player;
                    
                    // Call minimax recursively and choose
                    // the maximum value
                    best = max( best,
                               minimax(board, depth+1, !isMax) );
                    
                    // Undo the move
                    board[i][j] = '_';
                }
            }
        }
        return best;
    }
    
    // If this minimizer's move
    else
    {
        int best = 1000;
        
        // Traverse all cells
        for (int i = 0; i<3; i++)
        {
            for (int j = 0; j<3; j++)
            {
                // Check if cell is empty
                if (board[i][j]=='_')
                {
                    // Make the move
                    board[i][j] = opponent;
                    
                    // Call minimax recursively and choose
                    // the minimum value
                    best = min(best,
                               minimax(board, depth+1, !isMax));
                    
                    // Undo the move
                    board[i][j] = '_';
                }
            }
        }
        return best;
    }
}

```

Fungsi diatas merupakan fungsi minimax. Fungsi ini menganggap semua kemungkinan 
perpindahan yang bisa dilakukan oleh game ini dan me return value untuk board.


```c++
Move findBestMove(char board[3][3])
{
    int bestVal = -1000;
    Move bestMove;
    bestMove.row = -1;
    bestMove.col = -1;
    
    // Traverse all cells, evaluate minimax function for
    // all empty cells. And return the cell with optimal
    // value.
    for (int i = 0; i<3; i++)
    {
        for (int j = 0; j<3; j++)
        {
            // Check if cell is empty
            if (board[i][j]=='_')
            {
                // Make the move
                board[i][j] = player;
                
                // compute evaluation function for this
                // move.
                int moveVal = minimax(board, 0, false);
                
                // Undo the move
                board[i][j] = '_';
                
                // If the value of the current move is
                // more than the best value, then update
                // best/
                if (moveVal > bestVal)
                {
                    bestMove.row = i;
                    bestMove.col = j;
                    bestVal = moveVal;
                }
            }
        }
    }
    
    printf("The value of the best Move is : %d\n\n",
           bestVal);
    
    return bestMove;
}
```

Fungsi diatas berfungsi untuk mencari kemungkinan perpindahan terbaik untuk pemain


Output:


![Screen Shot 2020-03-24 at 12 48 55](https://user-images.githubusercontent.com/62136051/77390065-c50f6600-6dcf-11ea-9d02-49c6585c696b.png)


### Tugas 4

**4 Queen**
Output:

![Screen Shot 2020-03-24 at 13 20 43](https://user-images.githubusercontent.com/62136051/77390896-4e279c80-6dd2-11ea-921d-33725139b3a1.png)
