### PENJELASAN NOMOR 1 ###

Soal memberikan:
Sebuah graf tak berarah dan tak berbobot.
Terdiri dari N node dan M edge.
Sebuah node S sebagai titik awal pencarian.
Sebuah bilangan L sebagai target jarak.

Tujuannya adalah:
Mencetak semua node yang berada tepat pada jarak L dari node S, menggunakan pendekatan Breadth-First Search (BFS).

Kalau tidak ada node yang berjarak L dari S, maka output-nya adalah:
Not Found

Kenapa BFS, bukan DFS atau lainnya?
Karena BFS secara alami mencari jarak minimum dari node start ke node lain dalam graf tak berbobot.
BFS berjalan berlapis (layered): level 0, level 1, level 2, dst.
Jadi, kita bisa tahu kapan sebuah node berada pada jarak L dari S hanya dengan menghitung lapisan BFS-nya.
Dalam graf tak berbobot, setiap edge bernilai 1, maka BFS akan menjelajahi node di jarak 1, lalu 2, dst — sangat cocok untuk kasus ini.

Untuk menyelesaikan soal ini secara efisien:
Representasi graf: Adjacency List, karena jumlah node dan edge bisa sangat besar (hingga 10⁵).
Traversal: BFS dari node S
Simpan jarak tiap node dari S dalam array dist[].
Setelah BFS selesai:
Cetak semua node i dengan dist[i] == L
Jika tidak ada, cetak "Not Found"

Kenapa Pendekatan Ini Efisien?
Kompleksitas waktu: O(N + M)
Karena BFS mengunjungi setiap node dan edge maksimal satu kali.

Kompleksitas memori juga efisien karena kita hanya simpan:
Adjacency list
Derajat (jumlah tetangga)
Array jarak (dist[])
Queue untuk BFS
Setelah penjelasan ini, kamu bisa la

#### Bagian Header ####
```
#include <stdio.h>
#include <stdlib.h>
```
#include <stdio.h> digunakan untuk fungsi input/output seperti scanf dan printf.
#include <stdlib.h> dibutuhkan untuk fungsi alokasi memori seperti malloc, calloc, dan free.

#### Fungsi buildAdjList ####
```
void buildAdjList(int** adj, int* deg, int m) {
    for (int i = 0; i < m; i++) {
        int u, v;
        scanf("%d %d", &u, &v);
        adj[u][deg[u]++] = v;
        adj[v][deg[v]++] = u;
    }
}
```
Tujuan: 
Membangun adjacency list dari input edge yang diberikan. Karena graf tidak berarah, maka kita simpan dua arah: u → v dan v → u.
Penjelasan logika:
Loop for sebanyak m kali, karena jumlah edge adalah m.
Tiap baris input dibaca sebagai pasangan (u, v).
deg[u]++ artinya kita simpan node v di list tetangga u, dan tingkatkan derajat u.
Begitu juga sebaliknya untuk v.

#### Fungsi bfs ####
```void bfs(int** adj, int* deg, int* dist, int n, int start) {
    int* q = malloc(n * sizeof(int));
    int front = 0, rear = 0;

    for (int i = 0; i < n; i++) dist[i] = -1;

    dist[start] = 0;
    q[rear++] = start;

    while (front < rear) {
        int u = q[front++];
        for (int i = 0; i < deg[u]; i++) {
            int v = adj[u][i];
            if (dist[v] == -1) {
                dist[v] = dist[u] + 1;
                q[rear++] = v;
            }
        }
    }

    free(q);
}
```
Tujuan:
Melakukan BFS dari node start untuk menghitung jarak minimum ke setiap node.
Penjelasan logika:
Kita pakai queue manual dengan array.
Semua jarak di-inisialisasi -1, yang artinya belum dikunjungi.
Jarak dari start ke dirinya sendiri adalah 0.
Selama queue masih ada node, kita eksplorasi tetangganya.
Jika tetangga belum dikunjungi, kita hitung jaraknya (+1 dari node saat ini) dan masukkan ke queue.

#### Fungsi printAtLevel ####
```
void printAtLevel(int* dist, int n, int L) {
    int found = 0;
    for (int i = 0; i < n; i++) {
        if (dist[i] == L) {
            printf("%d\n", i);
            found = 1;
        }
    }
    if (!found) {
        printf("Not Found\n");
    }
}
```
Tujuan:
Menampilkan semua node yang berada tepat di jarak L dari node start.
Penjelasan logika:
Cek semua node, apakah dist[i] == L.
Jika iya, tampilkan node tersebut.
Jika tidak ada satupun, maka cetak "Not Found".

#### Fungsi main() – Bagian Inti Program ####
```
int main() {
    int n, m, L;
    scanf("%d %d %d", &n, &m, &L);
```
Membaca jumlah node n, jumlah edge m, dan target level L.

```
    int S;
    scanf("%d", &S);
```
Membaca node awal S untuk pencarian BFS.

#### Inisialisasi struktur data ####
```
    int* deg = calloc(n, sizeof(int));
    int* degTemp = calloc(n, sizeof(int));
    int* uList = malloc(m * sizeof(int));
    int* vList = malloc(m * sizeof(int));
```
deg menyimpan berapa banyak tetangga tiap node (derajat).
degTemp untuk bantu tracking saat pengisian adjacency list.

#### Penghitungan derajat node ####
```
    for (int i = 0; i < m; i++) {
        scanf("%d %d", &uList[i], &vList[i]);
        deg[uList[i]]++;
        deg[vList[i]]++;
    }
```
Membaca semua edge, sambil menghitung derajat tiap node untuk alokasi memori yang pas.

#### Alokasi adjacency list ####
```    
int** adj = malloc(n * sizeof(int*));
    for (int i = 0; i < n; i++) {
        adj[i] = malloc(deg[i] * sizeof(int));
        degTemp[i] = 0;
    }
```
Alokasi memori sesuai derajat setiap node agar efisien.
degTemp akan digunakan sebagai indeks pengisian.

#### Pengisian adjacency list ####
```
    for (int i = 0; i < m; i++) {
        int u = uList[i], v = vList[i];
        adj[u][degTemp[u]++] = v;
        adj[v][degTemp[v]++] = u;
    }
```
Sekarang kita isi list tetangga berdasarkan data yang sudah disiapkan sebelumnya.

#### Dealokasi sementara ####
```    
free(uList);
free(vList);
free(degTemp);
```
Bebaskan memori yang sudah tidak dibutuhkan.

#### BFS dan Output ####
```
    int* dist = malloc(n * sizeof(int));
    bfs(adj, deg, dist, n, S);
    printAtLevel(dist, n, L);
```
Lakukan BFS dari node S untuk mengisi array dist.
Cetak node-node yang tepat berada pada level L.

#### Dealokasi akhir ####
```
    for (int i = 0; i < n; i++) {
        free(adj[i]);
    }
    free(adj);
    free(dist);
    free(deg);
```
Membersihkan seluruh memori dinamis untuk menghindari memory leak.
