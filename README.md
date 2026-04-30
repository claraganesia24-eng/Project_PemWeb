## KASUS 2

## Deskripsi
Program ini merupakan implementasi **Genetic Algorithm (GA)** untuk menyelesaikan **Knapsack Problem**, yaitu memilih kombinasi barang dengan nilai maksimum tanpa melebihi kapasitas tas.
Setiap solusi direpresentasikan dalam bentuk kromosom biner:
* `1` = barang diambil
* `0` = barang tidak diambil

## Data Barang
| Barang    | Berat | Nilai |
| --------- | ----- | ----- |
| Laptop    | 3     | 200   |
| HP        | 1     | 100   |
| Buku      | 2     | 60    |
| Jaket     | 2     | 80    |
| Kamera    | 1     | 150   |
| Sepatu    | 2     | 70    |
| Powerbank | 1     | 40    |

**Kapasitas maksimum tas = 7**

## Parameter Genetic Algorithm

| Parameter     | Nilai |
| ------------- | ----- |
| POP_SIZE      | 50    |
| GENERATIONS   | 100   |
| MUTATION_RATE | 0.2   |
| ELITE_SIZE    | 2     |

## 🧬 Cara Kerja Genetic Algorithm
1. **Inisialisasi** → Membuat populasi awal secara acak
2. **Fitness** → Menghitung nilai total (jika tidak melebihi kapasitas)
3. **Selection** → Memilih individu terbaik (Tournament Selection)
4. **Crossover** → Menggabungkan dua parent
5. **Mutation** → Mengubah gen secara acak
6. **Elitism** → Menyimpan individu terbaik
7. **Iterasi** → Diulang sampai generasi selesai

## Fitness Function
* Jika berat ≤ kapasitas → fitness = total nilai
* Jika berat > kapasitas → fitness = 0 (penalti)

## Hasil Akhir
Contoh output program:
===== HASIL AKHIR =====
Barang terpilih : ['Laptop', 'HP', 'Jaket', 'Kamera']
Total berat     : 7
Total nilai     : 530
Penjelasan: Solusi valid dan optimal.

### Analisis
* Total berat = 7 (sesuai kapasitas)
* Total nilai = 530 (maksimal)
* Solusi dinyatakan **optimal**
  
## Visualisasi Perkembangan
Grafik berikut menunjukkan perkembangan nilai fitness:
* 🔵 **Best** → nilai terbaik tiap generasi
* 🟠 **Average** → rata-rata populasi
<img width="862" height="681" alt="image" src="https://github.com/user-attachments/assets/2b111cc5-779c-453d-94b4-e37a33d0bc07" />

## Kode Program (Dengan Penjelasan Per Baris)
import random                     # import library untuk membuat angka acak
import matplotlib.pyplot as plt  # import library untuk membuat grafik

# =========================================================
# DATA BARANG
# =========================================================
items = [                         # list berisi data barang
    ("Laptop", 3, 200),          # nama: Laptop, berat: 3, nilai: 200
    ("HP", 1, 100),              # nama: HP, berat: 1, nilai: 100
    ("Buku", 2, 60),             # nama: Buku, berat: 2, nilai: 60
    ("Jaket", 2, 80),            # nama: Jaket, berat: 2, nilai: 80
    ("Kamera", 1, 150),          # nama: Kamera, berat: 1, nilai: 150
    ("Sepatu", 2, 70),           # nama: Sepatu, berat: 2, nilai: 70
    ("Powerbank", 1, 40),        # nama: Powerbank, berat: 1, nilai: 40
]

MAX_WEIGHT = 7                   # kapasitas maksimum tas adalah 7

POP_SIZE = 50                   # jumlah individu dalam populasi (50 solusi)
GENERATIONS = 100               # jumlah iterasi evolusi (100 generasi)
MUTATION_RATE = 0.2             # peluang mutasi sebesar 20%
ELITE_SIZE = 2                  # jumlah individu terbaik yang dipertahankan

# =========================================================
# INIT
# =========================================================
def create_individual():                     # fungsi untuk membuat 1 individu
    return [random.randint(0,1) for _ in items]  # membuat list 0/1 sepanjang jumlah barang

def init_population():                      # fungsi untuk membuat populasi awal
    return [create_individual() for _ in range(POP_SIZE)]  # membuat 50 individu acak

# =========================================================
# FITNESS
# =========================================================
def fitness(individual):                   # fungsi untuk menghitung nilai fitness
    total_weight = 0                      # variabel untuk menyimpan total berat
    total_value = 0                       # variabel untuk menyimpan total nilai

    for gene, (name, w, v) in zip(individual, items):  # looping gen dan data barang
        if gene == 1:                     # jika gen = 1 (barang dipilih)
            total_weight += w            # tambahkan berat barang
            total_value += v             # tambahkan nilai barang

    # penalti jika overweight
    if total_weight > MAX_WEIGHT:        # jika berat melebihi kapasitas
        return 0                         # fitness = 0 (solusi tidak valid)

    return total_value                  # jika valid, fitness = total nilai

# =========================================================
# SELECTION
# =========================================================
def selection(pop):                     # fungsi untuk memilih parent
    return max(random.sample(pop, 3), key=fitness)  
    # ambil 3 individu acak lalu pilih yang fitness-nya paling tinggi

# =========================================================
# CROSSOVER
# =========================================================
def crossover(p1, p2):                 # fungsi untuk menggabungkan 2 parent
    point = random.randint(1, len(p1)-1)  # menentukan titik potong secara acak
    return p1[:point] + p2[point:]     # gabungkan bagian kiri p1 dan kanan p2

# =========================================================
# MUTATION
# =========================================================
def mutate(ind):                       # fungsi mutasi
    for i in range(len(ind)):          # looping setiap gen
        if random.random() < MUTATION_RATE:  # jika angka random < 0.2
            ind[i] = 1 - ind[i]        # ubah gen (0 jadi 1, 1 jadi 0)
    return ind                         # kembalikan individu hasil mutasi

# =========================================================
# ANALISIS
# =========================================================
def decode(ind):                       # fungsi untuk menerjemahkan kromosom
    selected = []                      # list barang terpilih
    total_weight = 0                  # total berat
    total_value = 0                   # total nilai

    for gene, (name, w, v) in zip(ind, items):  # looping gen dan barang
        if gene == 1:                 # jika gen aktif
            selected.append(name)     # tambahkan nama barang ke list
            total_weight += w         # tambahkan berat
            total_value += v          # tambahkan nilai

    return selected, total_weight, total_value  # kembalikan hasil decode

# =========================================================
# GA
# =========================================================
def GA():                             # fungsi utama Genetic Algorithm
    pop = init_population()           # membuat populasi awal

    best_hist = []                    # list untuk menyimpan fitness terbaik tiap generasi
    avg_hist = []                     # list untuk menyimpan rata-rata fitness

    print("\n===== PROSES EVOLUSI (KNAPSACK) =====\n")  # tampilkan judul

    for gen in range(GENERATIONS):    # looping sebanyak 100 generasi

        pop = sorted(pop, key=fitness, reverse=True)  
        # urutkan populasi berdasarkan fitness tertinggi

        best = pop[0]                 # ambil individu terbaik
        best_fit = fitness(best)      # hitung fitness terbaik
        avg_fit = sum(fitness(p) for p in pop) / POP_SIZE  
        # hitung rata-rata fitness

        best_hist.append(best_fit)    # simpan fitness terbaik ke list
        avg_hist.append(avg_fit)      # simpan rata-rata ke list

        if gen % 5 == 0:              # setiap 5 generasi
            print(f"Gen {gen:3d} | Best Value: {best_fit} | Avg: {avg_fit:.2f}")  
            # tampilkan info perkembangan

        new_pop = pop[:ELITE_SIZE]    # ambil 2 individu terbaik (elitism)

        while len(new_pop) < POP_SIZE:  # selama populasi baru belum penuh
            p1 = selection(pop)       # pilih parent 1
            p2 = selection(pop)       # pilih parent 2

            child = crossover(p1, p2) # lakukan crossover
            child = mutate(child)     # lakukan mutasi

            new_pop.append(child)     # tambahkan anak ke populasi baru

        pop = new_pop                 # ganti populasi lama dengan yang baru

    # =====================================================
    # HASIL
    # =====================================================
    best = sorted(pop, key=fitness, reverse=True)[0]  # ambil solusi terbaik akhir
    selected, weight, value = decode(best)           # decode solusi

    print("\n===== HASIL AKHIR =====")               # tampilkan hasil
    print("Barang terpilih :", selected)            # tampilkan barang yang dipilih
    print("Total berat     :", weight)              # tampilkan total berat
    print("Total nilai     :", value)               # tampilkan total nilai

    if weight <= MAX_WEIGHT:                        # cek apakah valid
        print("Penjelasan: Solusi valid dan optimal.")  
    else:
        print("Penjelasan: Melebihi kapasitas (tidak valid).")

    # =====================================================
    # VISUAL
    # =====================================================
    plt.figure()                   # membuat figure baru untuk grafik
    plt.plot(best_hist)            # plot garis fitness terbaik
    plt.plot(avg_hist)             # plot garis rata-rata fitness
    plt.title("Perkembangan Nilai (Fitness)")  # judul grafik
    plt.xlabel("Generasi")         # label sumbu x
    plt.ylabel("Value")            # label sumbu y
    plt.legend(["Best", "Average"])  # legenda grafik
    plt.grid()                     # tampilkan grid
    plt.show()                     # tampilkan grafik

# RUN
GA()                               # menjalankan program Genetic Algorithm
