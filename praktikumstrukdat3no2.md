### PENJELASAN NOMOR 2 ###
Program ini digunakan untuk mencari jalur terpendek dari satu titik ke titik lain di dalam sebuah graf berarah dan berbobot, menggunakan algoritma Dijkstra. 
Program juga akan menampilkan biaya total dan urutan rute dari titik awal ke titik tujuan.

Program ini digunakan untuk mencari jalur terpendek dari satu titik ke titik lain di dalam sebuah graf berarah dan berbobot, menggunakan algoritma Dijkstra. 
Program juga akan menampilkan biaya total dan urutan rute dari titik awal ke titik tujuan.

Graf direpresentasikan dengan matriks adjacency petaJalan[][], di mana:
Baris ke-i dan kolom ke-j menunjukkan biaya atau jarak dari simpul i ke simpul j.
Jika tidak ada jalur langsung, maka nilai jaraknya dianggap sangat besar (nilaiSangatBesar()).

Algoritma yang digunakan: Dijkstra
Dijkstra adalah algoritma greedy untuk mencari jarak terpendek dari satu titik ke titik-titik lain di graf berbobot.

Rumus utama:
jarak[v] = min(jarak[v], jarak[u] + bobot(u, v))
Di mana u adalah titik yang sedang diproses dan v adalah tetangganya.


###  Struktur Variabel Global ###
```
int jumlahLokasi, jumlahJalan, titikAwal, titikTujuan;
long long petaJalan[100][100];
long long jarakTerdekat[100];
int sudahDikunjungi[100];
int titikSebelumnya[100];
int rute[100];
int panjangRute = 0;
```
jumlahLokasi: jumlah simpul pada graf.
jumlahJalan: jumlah sisi (edges) pada graf.
petaJalan[i][j]: bobot dari titik i ke titik j.
jarakTerdekat[i]: jarak terpendek dari titikAwal ke titik i.
sudahDikunjungi[i]: penanda apakah titik i sudah diproses.
titikSebelumnya[i]: untuk merekonstruksi jalur.
rute[]: menyimpan jalur dari titik awal ke tujuan.
panjangRute: banyaknya simpul dalam jalur.

### Fungsi nilaiSangatBesar() ###
```
long long nilaiSangatBesar() { return 999999999999999LL; }
```
Menghindari penggunaan #define INF, kita buat fungsi yang mengembalikan bilangan besar.
Ini digunakan untuk menyatakan bahwa belum ada jalur antara dua titik.
Mengembalikan nilai besar sebagai pengganti “tak hingga”.
Digunakan untuk menyatakan bahwa dua simpul tidak terhubung langsung.
Tipe long long digunakan karena batas bobot adalah hingga 10^9, dan akumulasi total bisa jauh lebih besar. LL artinya long long literal.\

### Fungsi inisialisasiPeta() ###
```
void inisialisasiPeta() {
    for (int i = 0; i < jumlahLokasi; i++) {
        jarakTerdekat[i] = nilaiSangatBesar();
        sudahDikunjungi[i] = 0;
        titikSebelumnya[i] = -1;
        for (int j = 0; j < jumlahLokasi; j++) {
            petaJalan[i][j] = (i == j) ? 0 : nilaiSangatBesar();
        }
    }
}
```
Mengatur:
jarakTerdekat[i] = nilaiSangatBesar(); → inisialisasi jarak awal sebagai "tak hingga" karena belum dihitung.
sudahDikunjungi[i] = 0; → tandai semua simpul belum dikunjungi.
titikSebelumnya[i] = -1; → belum ada jalur yang diketahui.
petaJalan[i][j] = ... → matriks bobot antar simpul.
Jika i == j, maka bobot dari simpul ke dirinya sendiri adalah 0.
Jika i ≠ j, maka isi dengan nilai sangat besar → artinya belum ada jalur.


### Fungsi inputData() ###
```
void inputData() {
    scanf("%d %d", &jumlahLokasi, &jumlahJalan);
    inisialisasiPeta();
    for (int i = 0; i < jumlahJalan; i++) {
        int asal, tujuan;
        long long jarak;
        scanf("%d %d %lld", &asal, &tujuan, &jarak);
        petaJalan[asal][tujuan] = jarak;
    }
    scanf("%d", &titikAwal);
    scanf("%d", &titikTujuan);
}
```
Membaca jumlah vertex (V) dan edge (N), lalu memanggil inisialisasiPeta.
Melakukan pengisian bobot ke petaJalan[a][b].
Karena graf berarah, tidak perlu mengisi petaJalan[b][a].
Input start (S) dan end (E).

### Fungsi cariJalurTerpendek() ###
```
void cariJalurTerpendek() {
    jarakTerdekat[titikAwal] = 0;

    for (int i = 0; i < jumlahLokasi; i++) {
        int kandidatTerdekat = -1;
        long long jarakMinimum = nilaiSangatBesar();

        for (int j = 0; j < jumlahLokasi; j++) {
            if (!sudahDikunjungi[j] && jarakTerdekat[j] < jarakMinimum) {
                jarakMinimum = jarakTerdekat[j];
                kandidatTerdekat = j;
            }
        }

        if (kandidatTerdekat == -1) return;
        sudahDikunjungi[kandidatTerdekat] = 1;

        for (int j = 0; j < jumlahLokasi; j++) {
            if (!sudahDikunjungi[j] && petaJalan[kandidatTerdekat][j] < nilaiSangatBesar()) {
                long long alternatif = jarakTerdekat[kandidatTerdekat] + petaJalan[kandidatTerdekat][j];
                if (alternatif < jarakTerdekat[j]) {
                    jarakTerdekat[j] = alternatif;
                    titikSebelumnya[j] = kandidatTerdekat;
                }
            }
        }
    }
}
```
jarakTerdekat[titikAwal] = 0; → jarak dari start ke dirinya sendiri adalah 0.
Loop sebanyak jumlah simpul:
Cari simpul dengan jarak terdekat yang belum dikunjungi.
Tandai simpul tersebut sebagai dikunjungi.
Update jarak semua tetangganya jika ditemukan jalur lebih pendek.
Simpan "pendahulu" untuk pelacakan jalur nanti.
Hitung alternatif jalur.
Jika lebih pendek, perbarui jarakTerdekat[] dan titikSebelumnya[].

### Fungsi susunRute() ###
```
void susunRute() {
    panjangRute = 0;
    int sekarang = titikTujuan;
    while (sekarang != -1) {
        rute[panjangRute++] = sekarang;
        sekarang = titikSebelumnya[sekarang];
    }
}
```
Menelusuri kembali jalur dari titikTujuan ke titikAwal menggunakan titikSebelumnya.
Disimpan di array rute (namun urutannya terbalik, dari tujuan ke awal).
rute[panjangRute++] = sekarang; artinya simpan nilai sekarang ke dalam rute, lalu naikkan panjangRute.

### Fungsi tampilkanHasil() ###
```
void tampilkanHasil() {
    if (jarakTerdekat[titikTujuan] == nilaiSangatBesar()) {
        printf("Impossible to find the path\n");
        return;
    }

    susunRute();
    printf("Path found with cost %lld: ", jarakTerdekat[titikTujuan]);
    for (int i = panjangRute - 1; i >= 0; i--) {
        printf("%d", rute[i]);
        if (i > 0) printf(" ");
    }
    printf("\n");
}
```
Jika jaraknya masih sangat besar → tidak ada jalur.
Jika ada jalur:
Susun dan cetak urutan titik dalam rute.
Tampilkan total biaya (jarakTerdekat[titikTujuan]).

#### Fungsi main() ####
```
int main() {
    inputData();
    cariJalurTerpendek();
    tampilkanHasil();
    return 0;
}
```
Memanggil fungsi utama secara urut:
inputData() → baca data input.
cariJalurTerpendek() → jalankan algoritma Dijkstra.
tampilkanHasil() → tampilkan jalur dan total bobot.

