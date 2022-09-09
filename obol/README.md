<img src="https://user-images.githubusercontent.com/48665887/180147974-1e7afb58-4996-4ea3-8d81-69d39d2bb07d.png" alt="68747470733a2f2f6f626f6c2e746563682f6f626f6c6e6574776f726b2e706e67" style="max-width: 100%;">

### Charon Distributed Validator Node

**Step 1. Buat dan backup up private key untuk charon**

```
wget -O obol.sh https://github.com/catnodes/testnet/blob/main/obol/obol.sh && chmod +x obol.sh && ./obol.sh
```

nanti akan terlihat seperti ini 

![image](https://user-images.githubusercontent.com/48665887/180239172-6128ce17-906f-48b2-81cb-099aaf05487d.png)

### cara menampilkan ENR:- terbaru
>setiap kali anda melakukan command dibawah ini jangan kaget kalau ENR berubah-ubah karena dia menyesuaikan timestamp
>pastikan anda sudah Backup file privatekey di /root/charon-distributed-validator-node/.charon/ ke PC anda (bisa pakai SFTP)


```
cd $HOME/charon-distributed-validator-node/
sudo chmod a+rwx .charon
docker run --rm -v "$(pwd):/opt/charon" ghcr.io/obolnetwork/charon:v0.10.0 enr
```

### Cara import private key

>Install dulu ini

```
wget -O obol.sh https://github.com/catnodes/testnet/blob/main/obol/obol.sh && chmod +x obol.sh && ./obol.sh
```

>setelah itu replace file private key di folder .charon pakai file yang sudah dibackup pakai SFTP

### command lainnya

*cek state sync*

```
docker logs charon-distributed-validator-node-geth-1 -f
```

*cek beacon*

```
docker logs charon-distributed-validator-node-teku-1 -f
```

*cek log charon*

```
docker logs  charon-distributed-validator-node-charon-1 -f
```
