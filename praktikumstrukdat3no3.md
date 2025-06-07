### PENJELASAN NOMOR 3 ###

Program ini digunakan untuk menentukan apakah terdapat suatu rute (path) dalam sebuah graf tak berarah dan berbobot, yang memiliki total bobot sama dengan nilai target tertentu. 
Program diuji untuk beberapa test case.
Kode ini menggunakan DFS dengan visited edge untuk eksplorasi path.
DFS berhenti jika:
Bobot sama dengan target → ditemukan → YES.
Bobot lebih dari target → gagal eksplorasi.
Dengan membatasi penggunaan sisi unik dan pruning saat bobot > target, program menghindari TLE.

#### Library Header ####
```
#include <algorithm>
#include <iostream>
#include <map>
#include <queue>
#include <set>
#include <stack>
#include <tuple>
#include <utility>
```
algorithm: untuk operasi dasar seperti min, max.
iostream: untuk input/output (misalnya cin dan cout).
map: untuk peta kunci-nilai (key-value) — digunakan untuk mapping simpul ke indeks.
queue, stack: tidak digunakan di kode ini, tapi bisa untuk BFS atau DFS iteratif.
set: menyimpan data unik, digunakan untuk menghindari pemakaian sisi yang sama dua kali.
tuple dan utility: untuk menyimpan pasangan atau tiga nilai sekaligus (edge: node1, node2, weight).

#### Deklarasi Struct Graf ####
```
struct Graf {
  long jumlahSimpul, jumlahSisi;
  vector<vector<pair<long, long>>> daftarTetangga;
```
Struct ini mendefinisikan representasi graf.
jumlahSimpul dan jumlahSisi: menyimpan jumlah simpul dan sisi.
daftarTetangga: adjacency list, tiap simpul memiliki daftar pasangan (tetangga, bobot).

### Fungsi inisialisasi() ####
```
void inisialisasi(long totalSimpul) {
  jumlahSimpul = totalSimpul;
  jumlahSisi = 0;
  daftarTetangga.clear();
  for (int i = 0; i < jumlahSimpul; i++) {
    daftarTetangga.push_back({});
  }
}
```
Fungsi ini menginisialisasi struktur graf:
Menyimpan jumlah simpul.
Menyetel jumlah sisi jadi 0.
Membersihkan adjacency list, lalu mengisi daftar kosong sebanyak simpul.

#### Fungsi tambahSisi() ####
```
void tambahSisi(long simpul1, long simpul2, long bobot) {
  daftarTetangga[simpul1].push_back(make_pair(simpul2, bobot));
  daftarTetangga[simpul2].push_back(make_pair(simpul1, bobot));
  jumlahSisi++;
}
```
Fungsi untuk menambahkan sisi ke graf.
Karena graf tidak berarah, kita tambahkan dua arah.
Digunakan make_pair untuk menyimpan (simpulTujuan, bobot).

#### Fungsi Rekursif DFS ####
```
bool dfsRekursif(vector<vector<pair<long, long>>> &graf,
                 set<pair<long, long>> sisiDikunjungi,
                 long posisi, long bobotSaatIni, long targetBobot) {
```
Fungsi rekursif DFS mencari apakah ada jalur dari simpul posisi ke manapun dengan total bobot targetBobot.

```
  if (bobotSaatIni == targetBobot)
    return true;
  if (bobotSaatIni > targetBobot)
    return false;
```
Basis rekursi:
Jika bobot sudah sama dengan target, kita kembalikan true.
Jika melebihi target, tidak perlu lanjut — kembalikan false.

```
  for (auto &[tujuan, berat] : daftarTetangga[posisi]) {
    pair<long, long> sisi = {min(tujuan, posisi), max(tujuan, posisi)};
    if (sisiDikunjungi.count(sisi))
      continue;
```
Kita periksa semua tetangga dari simpul saat ini.
Sisi disimpan sebagai pasangan (min, max) supaya konsisten (karena tak berarah).
Jika sisi sudah pernah dikunjungi, kita skip.

```
    sisiDikunjungi.insert(sisi);
    if (dfsRekursif(graf, sisiDikunjungi, tujuan, bobotSaatIni + berat, targetBobot))
      return true;
    sisiDikunjungi.erase(sisi);
  }
```
Kita tandai sisi ini sebagai sudah dikunjungi, lalu lanjut DFS rekursif ke simpul tujuan.
Jika salah satu DFS menemukan solusi, kembalikan true.
Jika tidak berhasil, kita "backtrack" dengan menghapus sisi tersebut dari set.

```
  return false;
}
```
Jika semua jalan sudah dicoba dan tidak menemukan target bobot, return false.

#### Fungsi telusuriDFS() ####
```
void telusuriDFS(vector<long> &hasil, long targetBobot) {
  set<pair<long, long>> sisiDikunjungi;

  for (int simpul = 0; simpul < jumlahSimpul; simpul++) {
    sisiDikunjungi.clear();
    long bobotSaatIni = 0;
```
Kita akan mencoba DFS dari setiap simpul di graf.
sisiDikunjungi dikosongkan tiap kali mulai dari simpul baru.

```
    if (dfsRekursif(daftarTetangga, sisiDikunjungi, simpul, bobotSaatIni, targetBobot)) {
      cout << "YES" << endl;
      return;
    }
  }

  cout << "NO" << endl;
}
```
Jika dari salah satu simpul berhasil menemukan bobot yang sesuai, kita cetak YES.
Jika semua gagal, cetak NO.

#### Fungsi main() ####
```
int main() {
  int jumlahUji;
  cin >> jumlahUji;
```
Input jumlah test case.

```
  for (int i = 0; i < jumlahUji; i++) {
    set<long> simpulUnik;
    vector<tuple<long, long, long>> daftarSisi;
```
Untuk tiap test case, kita buat:
Set untuk menyimpan simpul unik (untuk mapping index).
Vector untuk menyimpan semua sisi (dengan 3 komponen: node1, node2, bobot).

```
    while (true) {
      long a, b, berat;
      cin >> a >> b >> berat;
      if (a == 0 && b == 0 && berat == 0)
        break;
```
Input sisi terus-menerus sampai bertemu 0 0 0.

```
      simpulUnik.insert(a);
      simpulUnik.insert(b);
      daftarSisi.push_back({a, b, berat});
    }
```
Kita simpan simpul ke set dan sisi ke daftarSisi.

```
    long target;
    cin >> target;
```
Input target bobot yang ingin dicapai.

```
    map<long, long> petaSimpul;
    long indeks = 0;
    for (auto simpul : simpulUnik) {
      petaSimpul[simpul] = indeks++;
    }
```
Kita buat mapping dari nomor simpul asli ke index 0-based untuk efisiensi array.

```
    long ukuranGraf = simpulUnik.size();
    Graf g;
    g.inisialisasi(ukuranGraf);
```
Buat objek Graf dan inisialisasi dengan jumlah simpul unik.

```
    for (auto &[x, y, bobot] : daftarSisi) {
      g.tambahSisi(petaSimpul[x], petaSimpul[y], bobot);
    }
```
Untuk tiap sisi, kita tambahkan ke graf dengan index hasil mapping.

```
    vector<long> hasil(g.jumlahSimpul, -1), jarak(g.jumlahSimpul, -1);
    g.telusuriDFS(hasil, target);
  }

  return 0;
}
```
hasil dan jarak tidak dipakai dalam kode ini, tapi disiapkan mungkin untuk ekspansi.
Kita panggil fungsi utama DFS untuk mencari solusi.

#### KESIMPULAN ####  
Struktur dan pendekatan: Kita menggunakan DFS dengan pencatatan edge yang sudah dikunjungi menggunakan set, agar tidak melintasi sisi yang sama dua kali.
Efisiensi waktu dan memori: DFS ini efisien karena berhenti begitu target tercapai (early stopping), dan hanya menyimpan sisi yang sedang ditelusuri.
Kenapa bisa "YES": karena ditemukan jalur dari salah satu simpul yang total bobotnya sama dengan target.
Kenapa bisa "NO": karena semua jalur dari semua simpul sudah dicek dan tidak ada yang cocok.



