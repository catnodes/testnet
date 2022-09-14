<p align="center">
  <img height="100" height="auto" src="https://user-images.githubusercontent.com/50621007/171398816-7e0432f4-4d39-42ad-a72e-cd8dd008028f.png">
</p>

# Subspace node setup untuk Gemini 2 Incentivized Testnet

# Informasi
Tanggal: 20 September 2022 \
Waktu  : 3pm UTC \
Instruksi resmi: https://docs.subspace.network/ 
Informasi lebih lanjut tentang testnet: https://forum.subspace.network/t/gemini-ii-incentivized-testnet-will-be-live-on-sep-20/675

Gemini II Ringkasan Konfigurasi
- Sejarah awal akan diatur ke 5 GB
- Ukuran plot maksimum untuk farmering adalah 100 GB untuk setiap node farmer
- Tambahan 50 GB diperlukan untuk memperhitungkan pertumbuhan riwayat rantai

Dokumentasi resmi:
- Panduan resmi: https://github.com/subspace/subspace/blob/main/docs/farming.md
- Telemetry: https://telemetry.subspace.network/#list/0x43d10ffd50990380ffe6c9392145431d630ae67e89dbc9c014cac2a417759101
- Block explorer: https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Feu-1.gemini-2a.subspace.network%2Fws#/explorer

## Rekomendasi perangkat keras
- CPU: 4 CPU
- Memory: 8 GB RAM
- Disk: 160 GB SSD Penyimpanan

## Port yang diperlukan
Saat ini, port TCP `30333` perlu diekspos agar node berfungsi dengan baik.
Jika Anda memiliki server tanpa firewall, tidak ada yang perlu dilakukan, tetapi pastikan untuk membuka port TCP `30333` untuk koneksi masuk.

## Membuat dompet Polkadot.js
Tutorial membuat dompet Polkadot:
1. Download dan install [Browser Extension](https://polkadot.js.org/extension/)
2. Buka [Subspace Explorer](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Feu-1.gemini-2a.subspace.network%2Fws#/accounts) dan tekan tombol `Add account`
3. Simpan `mnemonic` dan buat dompet
4. Ini akan menghasilkan alamat dompet yang harus Anda gunakan nanti. Contoh alamat dompet: `st7QseTESMmUYcT5aftRJZ3jg357MsaAa93CFQL5UKsyGEk53`

## Siapkan full node Subspace Anda
Full node tidak menyimpan riwayat dan status seluruh blockchain, hanya bertahan 1024 blok. Anda dapat mengatur full node Subspace Anda dalam beberapa menit dengan menggunakan skrip otomatis di bawah ini:
```
wget -O subspace.sh https://raw.githubusercontent.com/catnodes/testnet/main/subspace/subspace.sh && chmod +x subspace.sh && ./subspace.sh
```

## Periksa node anda di telemetri
Setelah Anda selesai mengatur node dan farmer:
1. Buka [Subspace Gemini 2 Telemetry](https://telemetry.subspace.network/#list/0x43d10ffd50990380ffe6c9392145431d630ae67e89dbc9c014cac2a417759101)
2. Dan mulailah mengetik nama node yang Anda berikan sebelumnya
3. Anda akan melihat diri Anda dalam daftar seperti pada gambar di bawah ini

![image](https://www.dropbox.com/s/w3jtcdqcwevkd9f/CleanShot%202022-09-14%20at%2019.19.40%402x.jpg)

## Periksa sinkronisasi simpul Anda
Jika output `false`, berarti node Anda sudah sinkron
```
curl -s -X POST http://localhost:9933 -H "Content-Type: application/json" --data '{"id":1, "jsonrpc":"2.0", "method": "system_health", "params":[]}' | jq .result.isSyncing
```

## Pembaharuan node
Untuk memutakhirkan node Anda ke binari baru, jalankan perintah di bawah ini:
```
cd $HOME && rm -rf subspace-*
APP_VERSION=$(curl -s https://api.github.com/repos/subspace/subspace/releases/latest | jq -r ".tag_name" | sed "s/runtime-/""/g")
wget -O subspace-node https://github.com/subspace/subspace/releases/download/${APP_VERSION}/subspace-node-ubuntu-x86_64-${APP_VERSION}
wget -O subspace-farmer https://github.com/subspace/subspace/releases/download/${APP_VERSION}/subspace-farmer-ubuntu-x86_64-${APP_VERSION}
chmod +x subspace-*
mv subspace-* /usr/local/bin/
systemctl restart subspaced
sleep 30
systemctl restart subspaced-farmer
```

## Perbaikian kesalahan saat memuat perpustakaan bersama
Jika anda menemukan kesalahan: `error while loading shared libraries: libOpenCL.so.1: cannot open shared object file: No such file or directory`
```
sudo apt update && sudo apt install ocl-icd-opencl-dev libopencl-clang-dev libgomp1 -y
```

## Muat ulang node
Jika Anda menjalankan node sebelumnya, dan ingin beralih ke snapshot baru, lakukan langkah-langkah berikut, lalu ikuti panduan lagi:
```
subspace-farmer wipe
subspace-node purge-chain --chain gemini-2a -y
systemctl restart subspaced
sleep 30
systemctl restart subspaced-farmer
```

##Perintah yang berguna
Cek node dan versi farmer
```
subspace-farmer --version && subspace-node --version
```

Cek status node
```
service subspaced status
```

Cek status farmer
```
service subspaced-farmer status
```

Cek log node
```
journalctl -u subspaced -f -o cat
```

Cek log farmer
```
journalctl -u subspaced-farmer -f -o cat
```

Anda akan melihat sesuatu yang serupa di log:
```
2022-02-03 10:52:23 Subspace
2022-02-03 10:52:23 ✌️  version 0.1.0-35cf6f5-x86_64-ubuntu
2022-02-03 10:52:23 ❤️  by Subspace Labs <https://subspace.network>, 2021-2022
2022-02-03 10:52:23 📋 Chain specification: Subspace Gemini 2
2022-02-03 10:52:23 🏷  Node name: YOUR_FANCY_NAME
2022-02-03 10:52:23 👤 Role: AUTHORITY
2022-02-03 10:52:23 💾 Database: RocksDb at /home/X/.local/share/subspace-node-x86_64-ubuntu-20.04-snapshot-2022-jan-05/chains/subspace_test/db/full
2022-02-03 10:52:23 ⛓  Native runtime: subspace-100 (subspace-1.tx1.au1)
2022-02-03 10:52:23 🔨 Initializing Genesis block/state (state: 0x22a5…17ea, header-hash: 0x6ada…0d38)
2022-02-03 10:52:24 ⏱  Loaded block-time = 1s from block 0x6ada0792ea62bf3501abc87d92e1ce0e78ddefba66f02973de54144d12ed0d38
2022-02-03 10:52:24 Starting archiving from genesis
2022-02-03 10:52:24 Archiving already produced blocks 0..=0
2022-02-03 10:52:24 🏷  Local node identity is: 12D3KooWBgKtea7MVvraeNyxdPF935pToq1x9VjR1rDeNH1qecXu
2022-02-03 10:52:24 🧑‍🌾 Starting Subspace Authorship worker
2022-02-03 10:52:24 📦 Highest known block at #0
2022-02-03 10:52:24 〽️ Prometheus exporter started at 127.0.0.1:9615
2022-02-03 10:52:24 Listening for new connections on 0.0.0.0:9944.
2022-02-03 10:52:26 🔍 Discovered new external address for our node: /ip4/XXX.XXX.XXX.XXX/tcp/30333/p2p/12D3KooWBgKtea7MVvraeNyxdPF935pToq1x9VjR1rDeNH1qecXu
2022-02-03 10:52:29 ⚙️  Syncing, target=#215883 (2 peers), best: #55 (0xafc7…bccf), finalized #0 (0x6ada…0d38), ⬇ 850.1kiB/s ⬆ 1.5kiB/s
```

Untuk menghapus node
```
sudo systemctl stop subspaced subspaced-farmer
sudo systemctl disable subspaced subspaced-farmer
rm -rf ~/.local/share/subspace*
rm -rf /etc/systemd/system/subspaced*
rm -rf /usr/local/bin/subspace*
```
