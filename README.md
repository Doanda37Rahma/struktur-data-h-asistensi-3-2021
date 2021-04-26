# struktur-data-h-asistensi-3-2021

## Struktur Data Trie

"Trie" diambil dari kata "retrieval" yang artinya pengambilan. Trie disebut juga digital tree atau prefix tree. Trie adalah data structure berbasis tree yang umumnya digunakan untuk menyimpan suatu set string. 

## Karakteristik Trie untuk string:
  1. Node root dari trie selalu merepresentasikan node null.
  2. Setiap child dari node diurutkan secara alfabet.
  3. Setiap node mempunyai maksimum 26 node anak (a-z).
  4. Setiap node (kecuali root) menyimpan satu huruf dari alfabet.

## Visualisasi Trie
Diagram trie dibawah ini menggambarkan representasi trie untuk kata: 'bell', 'bear', 'boar', 'bat', 'ball', 'stop', 'stock', dan 'stack'.

![BUKTI](https://github.com/Doanda37Rahma/struktur-data-h-asistensi-3-2021/blob/main/img/trie1.png)

Berikut representasi lainnya:

![BUKTI](https://github.com/Doanda37Rahma/struktur-data-h-asistensi-3-2021/blob/main/img/trie2.png)
![BUKTI](https://github.com/Doanda37Rahma/struktur-data-h-asistensi-3-2021/blob/main/img/trie3.png)

## Aplikasi Trie:
1. Spell Checker

Di sini trie tidak hanya memudahkan untuk mencari kata dalam kamus, tetapi juga memudahkan dalam membuat algoritme untuk menampilkan kata yang relevan/direkomendasikan.

2. Auto-complete

Sistem auto-complete menyediakan cara simpel untuk mencari kata alternatif atau menyelesaikan suatu kata dari semua kemungkinan keyword.

3. Sejarah Browser
	
Trie juga dapat digunakan untuk menyimpan URL setiap website yang dikunjungi untuk akses cepat.
	
## Keuntungan / Kerugian
Keuntungan trie adalah dapat melakukan insert, search, dan delete string key lebih cepat daripada BST dan hash table, dengan kompleksitas waktu O(N), N = Panjang key.
Kelemahan trie terletak pada penggunaan memori yang boros, dengan kompleksitas memori O(NxMxC), N=panjang key, M=panjang maks string, C=ukuran alfabet.

## Implementasi dalam C++
Sebuah node trie mengandung bool yang menandakan leaf `isLeaf` dan array of pointer to node yang memiliki index 0-25 (26 alfabet). 
```
// Data structure to store a Trie node
struct Trie
{
    int isLeaf;             // 1 when the node is a leaf node
    struct Trie* character[CHAR_SIZE];
};
```
Dan diinisialisasikan dengan pointer head:
```
   struct Trie* head = getNewTrieNode();
```

### Fungsi `insert()`
Proses memasukkan key kedalam trie yaitu memanjangkan node baru untuk setiap suffix dalam string yang tidak ada dalam trie.
```
// Iterative function to insert a string into a Trie
void insert(struct Trie *head, char* str)
{
    // start from the root node
    struct Trie* curr = head;
    while (*str)
    {
        // create a new node if the path doesn't exist
        if (curr->character[*str - 'a'] == NULL) {
            curr->character[*str - 'a'] = getNewTrieNode();
        }
 
        // go to the next node
        curr = curr->character[*str - 'a'];
 
        // move to the next character
        str++;
    }
 
    // mark the current node as a leaf
    curr->isLeaf = 1;
}
```
### Fungsi `search()`
Proses search hampir sama dengan insertion yaitu menelusuri suatu cabang dalam trie sesuai dengan string yang dicari dan mengembalikan `false` jika string tidak ada (menemui null sebelum string habis/ huruf terakhir bukan node leaf).
Bool `isLeaf` menandakan akhir dari suatu kata. Misal dalam suatu cabang trie 'tendangan', apabila kata 'tendang' dan 'tendangan' dimasukkan, maka 'g' dan 'n' adalah node leaf, tetapi 'a' bukan node leaf sehingga jika dicari kata 'tenda' tidak akan ditemukan.
```
// Iterative function to search a string in a Trie. It returns 1
// if the string is found in the Trie; otherwise, it returns 0.
int search(struct Trie* head, char* str)
{
    // return 0 if Trie is empty
    if (head == NULL) {
        return 0;
    }
 
    struct Trie* curr = head;
    while (*str)
    {
        // go to the next node
        curr = curr->character[*str - 'a'];
 
        // if the string is invalid (reached end of a path in the Trie)
        if (curr == NULL) {
            return 0;
        }
 
        // move to the next character
        str++;
    }
 
    // return 1 if the current node is a leaf and the
    // end of the string is reached
    return curr->isLeaf;
}
```
### Fungsi `delete()`
Untuk proses deletion intinya adalah menghapus setiap huruf key dari bawah ke atas menggunakan rekursi, tetapi berhati-hati dan hanya menghapus node tertentu saja karena key tersebut (contoh 'tendang') bisa saja merupakan prefix key lainnya ('tendang'an), atau prefix dari key tersebut bisa menjadi key baru ('tenda'ng).
```
// Recursive function to delete a string from a Trie
int deletion(struct Trie **curr, char* str)
{
    // return 0 if Trie is empty
    if (*curr == NULL) {
        return 0;
    }
 
    // if the end of the string is not reached
    if (*str)
    {
        // recur for the node corresponding to the next character in
        // the string and if it returns 1, delete the current node
        // (if it is non-leaf)
        if (*curr != NULL && (*curr)->character[*str - 'a'] != NULL &&
            deletion(&((*curr)->character[*str - 'a']), str + 1) &&
            (*curr)->isLeaf == 0)
        {
            if (!hasChildren(*curr))
            {
                free(*curr);
                (*curr) = NULL;
                return 1;
            }
            else {
                return 0;
            }
        }
    }
 
    // if the end of the string is reached
    if (*str == '\0' && (*curr)->isLeaf)
    {
        // if the current node is a leaf node and doesn't have any children
        if (!hasChildren(*curr))
        {
            free(*curr);    // delete the current node
            (*curr) = NULL;
            return 1;       // delete the non-leaf parent nodes
        }
 
        // if the current node is a leaf node and has children
        else {
            // mark the current node as a non-leaf node (DON'T DELETE IT)
            (*curr)->isLeaf = 0;
            return 0;       // don't delete its parent nodes
        }
    }
 
    return 0;
}
```
