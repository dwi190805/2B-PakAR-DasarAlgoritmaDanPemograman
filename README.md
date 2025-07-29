import json
import os
from datetime import datetime

class Barang:
    def __init__(self, nama, stok, harga):
        self.nama = nama
        self.stok = stok
        self.harga = harga

class Inventori:
    def __init__(self):
        self.data_barang = []
        self.load_data()

    def tambah_barang(self):
        print("\n=== Tambah Barang ===")
        nama = input("Nama barang: ")
        stok = int(input("Stok: "))
        harga = float(input("Harga per unit: "))
        
        self.data_barang.append({
            'nama': nama,
            'stok': stok,
            'harga': harga
        })
        self.save_data()
        print(f"Barang '{nama}' berhasil ditambahkan!")

    def tampilkan_barang(self):
        print("\n=== Daftar Barang ===")
        if not self.data_barang:
            print("Tidak ada barang dalam inventori.")
            return
        
        print(f"{'No.':<5}{'Nama':<20}{'Stok':<10}{'Harga':<15}{'Total Nilai':<15}")
        print("-" * 65)
        for idx, barang in enumerate(self.data_barang, 1):
            total_nilai = barang['stok'] * barang['harga']
            print(f"{idx:<5}{barang['nama']:<20}{barang['stok']:<10}{barang['harga']:<15.2f}{total_nilai:<15.2f}")

    def edit_barang(self):
        self.tampilkan_barang()
        if not self.data_barang:
            return
            
        try:
            no = int(input("\nPilih nomor barang yang akan diedit: ")) - 1
            if 0 <= no < len(self.data_barang):
                barang = self.data_barang[no]
                print(f"\nEdit barang: {barang['nama']}")
                barang['nama'] = input(f"Nama baru ({barang['nama']}): ") or barang['nama']
                barang['stok'] = int(input(f"Stok baru ({barang['stok']}): ") or barang['stok'])
                barang['harga'] = float(input(f"Harga baru ({barang['harga']}): ") or barang['harga'])
                self.save_data()
                print("Barang berhasil diupdate!")
            else:
                print("Nomor tidak valid!")
        except ValueError:
            print("Input harus berupa angka!")

    def hapus_barang(self):
        self.tampilkan_barang()
        if not self.data_barang:
            return
            
        try:
            no = int(input("\nPilih nomor barang yang akan dihapus: ")) - 1
            if 0 <= no < len(self.data_barang):
                barang = self.data_barang.pop(no)
                self.save_data()
                print(f"Barang '{barang['nama']}' berhasil dihapus!")
            else:
                print("Nomor tidak valid!")
        except ValueError:
            print("Input harus berupa angka!")

    def cari_barang(self):
        print("\n=== Cari Barang ===")
        keyword = input("Masukkan nama barang atau bagiannya: ").lower()
        
        hasil = [barang for barang in self.data_barang if keyword in barang['nama'].lower()]
        
        if not hasil:
            print("Tidak ditemukan barang dengan kata kunci tersebut.")
            return
            
        print(f"\nHasil pencarian untuk '{keyword}':")
        print(f"{'No.':<5}{'Nama':<20}{'Stok':<10}{'Harga':<15}{'Total Nilai':<15}")
        print("-" * 65)
        for idx, barang in enumerate(hasil, 1):
            total_nilai = barang['stok'] * barang['harga']
            print(f"{idx:<5}{barang['nama']:<20}{barang['stok']:<10}{barang['harga']:<15.2f}{total_nilai:<15.2f}")

    def laporan_ringkas(self):
        print("\n=== Laporan Ringkas ===")
        total_barang = len(self.data_barang)
        total_stok = sum(barang['stok'] for barang in self.data_barang)
        total_nilai = sum(barang['stok'] * barang['harga'] for barang in self.data_barang)
        
        print(f"Jumlah jenis barang: {total_barang}")
        print(f"Total stok semua barang: {total_stok}")
        print(f"Total nilai inventori: {total_nilai:.2f}")

    def export_to_file(self):
        if not self.data_barang:
            print("Tidak ada data untuk diexport!")
            return
            
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        filename = f"inventori_{timestamp}.txt"
        
        with open(filename, 'w') as file:
            file.write("=== Laporan Inventori ===\n")
            file.write(f"Tanggal: {datetime.now().strftime('%d/%m/%Y %H:%M:%S')}\n\n")
            file.write(f"{'No.':<5}{'Nama':<20}{'Stok':<10}{'Harga':<15}{'Total Nilai':<15}\n")
            file.write("-" * 65 + "\n")
            
            for idx, barang in enumerate(self.data_barang, 1):
                total_nilai = barang['stok'] * barang['harga']
                file.write(f"{idx:<5}{barang['nama']:<20}{barang['stok']:<10}{barang['harga']:<15.2f}{total_nilai:<15.2f}\n")
            
            # Tambahkan ringkasan
            total_barang = len(self.data_barang)
            total_stok = sum(barang['stok'] for barang in self.data_barang)
            total_nilai = sum(barang['stok'] * barang['harga'] for barang in self.data_barang)
            
            file.write("\n=== Ringkasan ===\n")
            file.write(f"Jumlah jenis barang: {total_barang}\n")
            file.write(f"Total stok semua barang: {total_stok}\n")
            file.write(f"Total nilai inventori: {total_nilai:.2f}\n")
        
        print(f"Data berhasil diexport ke file: {filename}")

    def save_data(self):
        with open('inventori.json', 'w') as file:
            json.dump(self.data_barang, file)

    def load_data(self):
        if os.path.exists('inventori.json'):
            with open('inventori.json', 'r') as file:
                self.data_barang = json.load(file)

def main():
    inventori = Inventori()
    
    while True:
        print("\n=== APLIKASI INVENTORI SEDERHANA ===")
        print("1. Tambah Barang")
        print("2. Tampilkan Barang")
        print("3. Edit Barang")
        print("4. Hapus Barang")
        print("5. Cari Barang")
        print("6. Laporan Ringkas")
        print("7. Export ke File")
        print("0. Keluar")
        
        pilihan = input("Pilih menu: ")
        
        if pilihan == '1':
            inventori.tambah_barang()
        elif pilihan == '2':
            inventori.tampilkan_barang()
        elif pilihan == '3':
            inventori.edit_barang()
        elif pilihan == '4':
            inventori.hapus_barang()
        elif pilihan == '5':
            inventori.cari_barang()
        elif pilihan == '6':
            inventori.laporan_ringkas()
        elif pilihan == '7':
            inventori.export_to_file()
        elif pilihan == '0':
            print("Terima kasih telah menggunakan aplikasi inventori!")
            break
        else:
            print("Pilihan tidak valid. Silakan coba lagi.")

if __name__ == "__main__":
    main()
