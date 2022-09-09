<img src="https://i.ibb.co/qBq6PGh/og-massa-tg-1.png" style="max-width: 100%;">

Official documentation:
>- https://docs.massa.net/en/latest/testnet/install.html

## Persyaratan Minimum Perangkat Keras

- 4 CORE
- 8 GB RAM
- 200 GB Disk


## Instalasi Node

**Step 1. Install Dependencies**

```
sudo su
apt update && apt upgrade -y
apt-get install libclang-dev
apt install screen
apt install pkg-config curl git build-essential libssl-dev
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source $HOME/.cargo/env
rustup toolchcdain install nightly
rustup default nightly
git clone --branch testnet https://github.com/massalabs/massa.git
```

**Step 2. Jalankan Node**

```
cd $HOME/massa/massa-node/; screen -R massa_node; cd
RUST_BACKTRACE=full cargo run --release -- -p [YOUR_PASSWORD] |& tee logs.txt
```

>Selanjutnya detach dengan `ctrl A + D` (jangan lakukan `ctrl + C` nanti nodenya mati)

**Step 3. Jalankan Client**

```
cd $HOME/massa/massa-client/; screen -R massa_client; cd
cargo run --release -- -p [YOUR_PASSWORD]
````

>Detach lagi dengan `ctrl A + D`


## Buat Wallet Baru, Routing, dan Hitung Insentif

Untuk membuat wallet baru teman-teman bisa menggunakan perintah berikut

```
wallet_generate_secret_key
wallet_info
```

### Claim faucet, buy rolls, dan staking rolls

Jangan lupa untuk menyimpan dengan aman _private_key_ dan salin alamat walletnya. Langkah berikutnya klaim faucet di discord massa di room #testnet-faucet

<img src="https://miro.medium.com/max/2400/0*dpvGhS4tAZmf44Ic.jpeg" style="max-width: 100%;">

Cek faucet sudah masuk apa belum

```
wallet_info
```

Jika balance sudah masuk lakukan pembelian rolls

```
buy_rolls YOUR_ADDRESS 1 0
```

Angka 1 diatas mewakili 100 balance. Jika memiliki 500 balance dan ingin membelikan semua bisa diganti dengan 5 0.

Langkah selanjutnya staking rolls tadi kedalam node agar bisa berjalan. Tunggu sekitar 1 jam 45 menit sampai node aktif

```
node_add_staking_secret_keys YOUR_STAKING_KEYS
```

>Jangan lupa kalukan detach lagi dengan tekan tombol `ctrl A + D`

Untuk kembali ke screen sebelumnya (screen massa_node / massa_client) masukkan perintah ini di terminal

```
screen -Rd massa_node

atau

screen -Rd massa_client
```

### Routing Node

Agar node bisa terhubung dengan node lainnya maka kamu harus melakukan routing dan membuka port

```
nano $HOME/massa/massa-node/config/config.toml

[network]
routable_ip = "AAA.BBB.CCC.DDD"
```

Selanjutnya buka portnya
```
ufw allow ssh && ufw allow 31244/tcp && ufw allow 31245/tcp && ufw allow 33035/tcp && ufw reload
```

### Hitung Insentif

Untuk memvalidasi partisipasi teman-teman dalam program hadiah massa testnet, teman-teman wajib terdaftar di akun Discord

```
screen -Rd massa_client
node_testnet_rewards_program_ownership_proof STAKING_ADDRESS_KAMU ID_DISCORD_KAMU
```

## Buat file service

Guna mengantisipasi hal-hal yang tidak diinginkan seperti node macet/trouble maka kita perlu membuat service agar node bisa berjalan otomatis

```
sudo tee /etc/systemd/system/massad.service > /dev/null <<EOF
[Unit]
Description=Massa Node
After=network-online.target

[Service]
User=$USER
WorkingDirectory=$HOME/massa/massa-node
ExecStart=$HOME/massa/target/release/massa-node -p [YOUR_PASSWORD]
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

Selanjutnya aktifkan program dengan perintah ini
```
systemctl daemon-reload
systemctl enable massad     --> _mengaktifkan service_
systemctl restart massad    --> _merestart node_
systemctl status massad     --> _untuk cek status_
```