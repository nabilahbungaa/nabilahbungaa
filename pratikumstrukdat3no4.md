### PENJELASAN NOMOR 4 ####

Soal meminta:
“Diberikan beberapa graf tak berarah, cari apakah ada cycle (siklus) atau tidak. Jika ada, cetak node-node yang membentuk siklus, diurut dari kecil ke besar.”

STRATEGI PEMROGRAMAN YANG SAYA GUNAKAN
Soalnya menyebutkan bahwa graf hanya punya 1 siklus maksimal
→ Saya cukup cek sekali lewat DFS, dan langsung keluar (return) kalau sudah ketemu.
Graf-nya undirected dan bisa besar (hingga 100.000 simpul)
→ Saya pilih pakai DFS iteratif pakai stack, bukan rekursif, supaya aman dari stack overflow.
Output-nya harus urut dari kecil ke besar
→ Saya pakai fungsi sort() sebelum mencetak node-node siklus.

```
#include <iostream>
```
Baris ini untuk mengaktifkan input dan output standar, kayak cin dan cout. Jadi supaya kita bisa baca dan tulis data dari terminal.

```
#include <vector>
```
Baris ini untuk pakai vector, yaitu struktur data seperti array tapi bisa dinamis (nambah sendiri ukurannya).

```
#include <stack>
```
Baris ini buat ngambil struktur data stack, yang berfungsi kayak tumpukan. Ini nanti kita pakai buat DFS (Depth First Search) yang pakai stack.

```
#include <algorithm>
```
Baris ini buat ngakses fungsi sort(), nanti kita pakai buat ngurutkan hasil simpul-simpul yang membentuk cycle.

```
using namespace std;
```
Baris ini supaya kita nggak usah nulis std::cout, std::vector terus-menerus, jadi lebih singkat.

```

#### CLASS PENDETEKSI ####
```
class PendeteksiSiklus {
```
Baris ini bikin class atau cetakan objek yang isinya fungsi-fungsi untuk deteksi siklus.

```
private:
    int jumlahNode, jumlahSisi;
    vector<vector<int>> daftarRelasi;
```
Di sini kita buat variabel dalam class yang cuma bisa diakses oleh class itu sendiri, yaitu:
jumlahNode: menyimpan jumlah simpul.
jumlahSisi: jumlah sisi.
daftarRelasi: menyimpan tetangga tiap simpul dalam bentuk list/list (adjacency list).

```
public:
    PendeteksiSiklus() : jumlahNode(0), jumlahSisi(0) {}
```
Baris ini adalah constructor. Fungsinya dijalankan otomatis pas objek class dibuat. Kita isi nilai awal simpul dan sisi jadi 0.

```
    void atur(int totalNode) {
        jumlahNode = totalNode; jumlahSisi = 0;
        daftarRelasi.assign(jumlahNode, vector<int>());
    }
```
Fungsi ini untuk mengatur ulang jumlah simpul.
assign(...) digunakan untuk memastikan bahwa kita punya list kosong sebanyak jumlah simpul.
Jadi, daftar tetangganya di-reset semua.

```
    void hubungkan(int node1, int node2) {
        daftarRelasi[node1].push_back(node2); daftarRelasi[node2].push_back(node1);
        ++jumlahSisi;
    }
```
Fungsi ini menambahkan sisi antara dua node:
Karena graf tidak berarah, jadi kita sambungkan dua arah: node1 ke node2 dan node2 ke node1.
push_back() artinya nambahin data ke akhir list.
++jumlahSisi menambah total sisi 1 setiap kali kita sambung.

```
    void telusuriSiklus(vector<int> &urutan, int awal) {
```
Fungsi utama untuk mencari siklus mulai dari simpul awal.
urutan dipakai untuk nyimpan urutan simpul yang dikunjungi (nggak terlalu dipakai sebenarnya di sini).

```
        vector<bool> sudahDikunjungi(jumlahNode, false);
```
Ini bikin list untuk nandai simpul mana aja yang udah dikunjungi. Awalnya semuanya false.

```
        vector<int> induk(jumlahNode, -1);
```
Ini buat nyimpan induk (parent) dari tiap node. Ini penting banget buat lacak jalur balik kalau ketemu siklus.

```
        stack<pair<int, int>> tumpukan;
```
Ini kita bikin stack (tumpukan) buat DFS. pair<int,int> artinya satu data isinya dua angka: simpul sekarang, dan simpul asal (induknya).

```
        tumpukan.push({awal, -1});
```
Kita mulai DFS dari node awal, dan asalnya kita kasih -1 karena dia belum punya induk.

#### PROSES DFS ####
```
        while (!tumpukan.empty()) {
```
Selama stack masih ada isinya, terusin DFS.

```
            pair<int, int> sekarang = tumpukan.top();
            tumpukan.pop();
```
Ambil data paling atas dari stack. Ini adalah simpul yang mau kita proses sekarang.

```
            int node = sekarang.first;
            int asal = sekarang.second;
```
Pisahkan data pair tadi ke dua variabel: node itu simpul sekarang, asal itu simpul sebelumnya.

#### KUNJUNGI NODE BARU ####
```
            if (!sudahDikunjungi[node]) {
                sudahDikunjungi[node] = true;
                urutan.push_back(node);
```
Kalau simpul ini belum pernah dikunjungi:
Tandai sudah dikunjungi.
Tambahkan ke list urutan (walau nggak dipakai lagi nanti).

#### TELUSURI SEMUA TETANGGA ####
```
                for (int relasi : daftarRelasi[node]) {
```
Cek semua simpul yang bertetangga dengan node.

```
                    if (!sudahDikunjungi[relasi]) {
                        induk[relasi] = node;
                        tumpukan.push({relasi, node});
```
Kalau simpul tetangga (relasi) belum pernah dikunjungi:
Simpan siapa induknya.
Masukkan ke stack untuk diproses nanti.

#### CEK SIKLUS DITEMUKAN ####
```
                    } else if (relasi != asal) {
```
Kalau relasi sudah dikunjungi, dan bukan induknya sendiri, berarti ada jalan bolak-balik → siklus ditemukan.

#### CETAK HASIL SIKLUS ####
```
                        cout << "VOCALOID CYCLE PROGRAM (VCP) : CYCLE DETECTED ";
```
Cetak pesan bahwa siklus ditemukan.

```
                        vector<int> siklus;
                        int sementara = node;
                        siklus.push_back(sementara);
```
Kita mulai susun siklus dengan node yang sekarang, masukin ke siklus.

#### LACAK JALUR BALIK ####
```
                        while (sementara != relasi) {
                            sementara = induk[sementara];
                            siklus.push_back(sementara);
                        }
```
Kita balik terus ke atas lewat induk[] sampai ketemu simpul awal dari siklus. Semua node di jalur ini masuk siklus.

#### URUTKAN DAN CETAK ####
```
                        sort(siklus.begin(), siklus.end());
```
Kita urutkan agar outputnya sesuai permintaan soal (dari kecil ke besar).

```
                        for (int i = 0; i < siklus.size(); ++i) {
                            cout << siklus[i];
                            if (i + 1 != siklus.size()) cout << " ";
                            else cout << endl;
                        }
                        return;
```
Cetak node-nya satu-satu, pakai spasi, dan baris baru di akhir. Habis itu langsung return, karena kita cuma cari satu siklus saja.

#### TIDAK ADA SIKLUS ####
```
        cout << "VOCALOID CYCLE PROGRAM (VCP) : NO CYCLE DETECTED" << endl;
```
Kalau tidak ketemu siklus selama proses DFS, maka cetak tidak ditemukan.

#### FUNGSI MAIN ####
```
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
```
Ini optimasi agar cin dan cout lebih cepat diproses.

```
    int jumlahUji;
    cin >> jumlahUji;
```
Baca jumlah test case yang akan diuji.

#### ULANGI SETIAP TEST CASE ####
```
    while (jumlahUji--) {
        int node, sisi;
        cin >> node >> sisi;
```
Baca jumlah simpul dan sisi untuk satu graf.

```
        PendeteksiSiklus graf;
        graf.atur(node);
```
Buat objek graf baru, lalu panggil atur() untuk set jumlah simpul.

```
        for (int i = 0; i < sisi; ++i) {
            int dari, ke;
            cin >> dari >> ke;
            graf.hubungkan(dari, ke);
        }
```
Baca semua sisi satu per satu, dan sambungkan dua simpulnya.

```
        vector<int> hasil;
        graf.telusuriSiklus(hasil, 0);
    }
    return 0;
}
```
Buat vector hasil (meskipun tidak terlalu dipakai), lalu panggil fungsi telusuri siklus mulai dari node ke-0.







