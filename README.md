import requests
import time
from tqdm import tqdm
from colorama import Fore, Style, init

# Inisialisasi colorama untuk sistem Windows
init(autoreset=True)

# Fungsi untuk melakukan reverse IP lookup
def reverse_ip_lookup(ip_address):
    try:
        api_url = f"http://api.hackertarget.com/reverseiplookup/?q={ip_address}"
        response = requests.get(api_url)

        if response.status_code == 200:
            domains = response.text.splitlines()
            if domains:
                return domains
            else:
                return [f"{Fore.YELLOW}Tidak ada domain yang terkait dengan IP {ip_address}"]
        else:
            return [f"{Fore.RED}Error: {response.status_code} - Tidak dapat mengambil data untuk {ip_address}"]
    except Exception as e:
        return [f"{Fore.RED}Error: {str(e)} untuk {ip_address}"]

# Fungsi untuk membaca daftar IP dari file dan menampilkan progress dengan warna
def process_ips_from_file(input_file, output_file):
    try:
        with open(input_file, 'r') as f:
            ips = f.read().splitlines()

        with open(output_file, 'w') as f:
            for ip in tqdm(ips, desc=f"{Fore.CYAN}Proses Reverse IP", unit=" IP", colour="green"):
                domains = reverse_ip_lookup(ip)
                
                print(f"{Fore.BLUE}IP Address: {ip}")
                f.write(f"IP Address: {ip}\n")
                print(f"{Fore.GREEN}Domains terkait:")
                f.write("Domains terkait:\n")
                for domain in domains:
                    print(f"{Fore.WHITE}  - {domain}")
                    f.write(f"  - {domain}\n")
                f.write("\n")
                print("\n")
                time.sleep(1)  # Tambahan jeda untuk visualisasi progress

        print(f"\n{Fore.GREEN}Hasil berhasil disimpan di {output_file}")
        print(f"{Fore.MAGENTA}Script dibuat oleh: hydra07")
    except Exception as e:
        print(f"{Fore.RED}Error: {str(e)}")

if __name__ == "__main__":
    # Nama file input dan output
    input_file = "ips.txt"
    output_file = "output_domains.txt"
    
    # Proses file IP dan lakukan reverse lookup dengan tampilan progress berwarna
    process_ips_from_file(input_file, output_file)
