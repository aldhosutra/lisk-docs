---
title: ... menggunakan Foundry
slug: /building-on-lisk/deploying-smart-contract/with-Foundry
description: "Panduan untuk mendeploy smart contract di jaringan test Lisk menggunakan Foundry. Termasuk instruksi untuk mengatur environment, mengompilasi, dan mendeploy smart contract."
keywords:
  [
    "Foundry",
    "smart contract",
    "ERC-20",
    "Lisk",
    "Lisk test network",
    "Lisk testnet",
    "Lisk Sepolia",
    "testing smart contract",
    "Solidity",
    "smart contract deployment",
    "deploy a smart contract",
    "deploying smart contracts",
    "build on lisk",
    "write smart contract",
    "smart contract development",
  ]
toc_max_heading_level: 4
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Mendeploy Smart Contract dengan Foundry

Panduan ini membahas dasar-dasar _toolchain_ [Foundry](https://book.getfoundry.sh/) dan menjelaskan cara membuat serta mendeploy _smart contract_ dengan Foundry ke testnet **Lisk Sepolia**.

:::note
Anda dapat mendeploy kontrak di mainnet **Lisk** dengan menggunakan proses yang sama.  
Untuk mendeploy ke mainnet, pastikan _wallet_ Anda memiliki cukup ETH.

Teks berikut mencakup perintah untuk Lisk dan Lisk Sepolia demi kemudahan Anda.  
Untuk informasi lebih lanjut, lihat [jaringan Lisk yang tersedia](/network-info) dan [cara menghubungkan wallet ke jaringan tersebut](/user/connecting-to-a-wallet).
:::

Foundry adalah rangkaian _tools_ yang mumpuni untuk mengembangkan, menguji, dan men-_debug_ _smart contract_ Anda.  
Foundry terdiri dari beberapa _tools_ individu seperti:

- [`forge`](https://book.getfoundry.sh/forge/): _tools_ berbasis _command-line_ yang disertakan dengan Foundry. _Forge_ digunakan untuk menguji, membangun, dan mendeploy _smart contract_ Anda.
- [`cast`](https://book.getfoundry.sh/cast/): _tools_ berbasis _command-line_ untuk melakukan panggilan RPC, seperti berinteraksi dengan kontrak, mengirim transaksi, dan mendapatkan data on-chain.
- [`anvil`](https://book.getfoundry.sh/anvil/): node testnet lokal, dirancang untuk menguji perilaku kontrak dari antarmuka depan atau melalui RPC dalam _environment_ pengembangan lokal.
- [`chisel`](https://book.getfoundry.sh/chisel/): REPL Solidity ([Read–Eval–Print Loop](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop)) untuk mencoba potongan kode Solidity pada jaringan lokal atau jaringan yang di-_fork_.

## Prasyarat

Untuk membangun dengan Foundry, Anda memerlukan:

- Pemahaman dasar tentang [Solidity](https://soliditylang.org/).
- Foundry terinstal pada sistem Anda.  
  Untuk menginstalnya, lakukan langkah-langkah berikut:
  - Dari command line, jalankan:
    ```bash
    curl -L https://foundry.paradigm.xyz | bash
    ```
  - Setelah itu, untuk menginstal versi terbaru (nightly) dari Foundry, jalankan:
    ```bash
    foundryup
    ```
- Untuk informasi lebih lanjut, lihat [Panduan Instalasi](https://book.getfoundry.sh/getting-started/installation) di Foundry Book.

### Dana _Wallet_

**Mendeploy kontrak** ke blockchain memerlukan **biaya gas**.  
Oleh karena itu, Anda perlu mendanai _wallet_ Anda dengan ETH untuk menutupi biaya gas tersebut.

Dalam panduan ini, Anda akan mendeploy kontrak ke Lisk Sepolia Testnet.

Anda dapat men-_deposit_ token yang diperlukan menggunakan [Lisk Sepolia Bridge](https://sepolia-bridge.lisk.com/bridge/lisk-sepolia-testnet).

Jika dompet Anda tidak memiliki cukup `SepoliaETH`, gunakan salah satu faucet yang tersedia untuk Ethereum Sepolia Testnet seperti [https://sepoliafaucet.com](https://sepoliafaucet.com/) untuk menerima ETH Testnet secara gratis.  
Kemudian, gunakan Lisk Bridge yang disebutkan sebelumnya untuk mengirim token dari **Ethereum Sepolia Testnet** ke **Lisk Sepolia Testnet**.

## Membuat Proyek

Langkah pertama dalam mendeploy _smart contract_ ke Lisk adalah menyiapkan _environment_ pengembangan Anda dengan membuat proyek Foundry.

Anda dapat membuat direktori baru secara terpisah lalu menginisialisasi (_initialize_) proyek Foundry, atau Anda dapat membiarkan Foundry membuat direktori dan menginisialisasi proyek Foundry dengan menjalankan perintah berikut:

```bash
forge init foundry_app && cd foundry_app
```

Perintah ini akan membuat folder bernama `foundry_app` dan sekaligus mengubah direktori kerja (_working directory_) terminal ke folder tersebut.

<details>
<summary>Log Eksekusi Perintah `forge init`</summary>
```text
Initializing /XYZ/L2/25/foundry_app/foundry_app...
Installing forge-std in /XYZ/L2/25/foundry_app/foundry_app/lib/forge-std (url: Some("https://github.com/foundry-rs/forge-std"), tag: None)
Cloning into '/XYZ/L2/25/foundry_app/foundry_app/lib/forge-std'...
remote: Enumerating objects: 2181, done.
remote: Counting objects: 100% (2177/2177), done.
remote: Compressing objects: 100% (737/737), done.
remote: Total 2181 (delta 1446), reused 2066 (delta 1373), pack-reused 4
Receiving objects: 100% (2181/2181), 614.11 KiB | 766.00 KiB/s, done.
Resolving deltas: 100% (1446/1446), done.
Submodule 'lib/ds-test' (https://github.com/dapphub/ds-test) registered for path 'lib/ds-test'
Cloning into '/XYZ/L2/25/foundry_app/foundry_app/lib/forge-std/lib/ds-test'...
remote: Enumerating objects: 313, done.        
remote: Counting objects: 100% (171/171), done.        
remote: Compressing objects: 100% (79/79), done.        
remote: Total 313 (delta 91), reused 132 (delta 83), pack-reused 142        
Receiving objects: 100% (313/313), 71.35 KiB | 521.00 KiB/s, done.
Resolving deltas: 100% (130/130), done.
    Installed forge-std v1.7.6
    Initialized forge project
```
</details>

Secara default, setiap aplikasi yang dibuat dengan Foundry akan memiliki struktur direktori yang serupa seperti berikut ini:

```bash
.
├── .github
├── lib
├── script
│   └── Counter.s.sol
├── src
│   └── Counter.sol
├── test
│   └── Counter.t.sol
├── .gitignore
├── .gitmodules
├── foundry.toml
└── README.md
```

Untuk saat ini, hapus file yang ada di `script/Counter.s.sol`, `src/Counter.sol`, dan `test/Counter.t.sol`, karena kita akan membuat kontrak dan kode pengujian sendiri dalam panduan berikutnya.

### **Membuat** Smart Contract

Untuk kemudahan dan keamanan, kita akan menggunakan kontrak `ERC721` yang disediakan oleh [OpenZeppelin Contracts library](https://docs.openzeppelin.com/contracts/5.x/erc721) untuk membuat _smart contract_ ERC-721 sederhana.  
Dengan OpenZeppelin, kita tidak perlu menulis keseluruhan kontrak ERC-721.  
Sebaliknya, kita dapat mengimpor kontrak dari library dan langsung menggunakan fungsinya.

Untuk menginstal OpenZeppelin Contracts library ke proyek Anda, jalankan perintah berikut:

```bash
forge install openzeppelin/openzeppelin-contracts
```

Di dalam folder `src`, buat sebuah smart contract bernama `NFT.sol` dan tambahkan kode berikut ke dalam file yang baru dibuat.

```sol title="src/NFT.sol"
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.23;

import "openzeppelin-contracts/contracts/token/ERC721/ERC721.sol";

contract NFT is ERC721 {
    uint256 public currentTokenId;

    // The following will create an ERC721 Token called Lisk.
    constructor() ERC721("Lisk", "LSK") {}

    // For simplicity, we will only implement the mint function of the Lisk token.
    function mint(address recipient) public payable returns (uint256) {
        uint256 newItemId = ++currentTokenId;
        _safeMint(recipient, newItemId);
        return newItemId;
    }
}
```

### **Meng-_compile_** Smart Contract

Setelah kode _smart contract_ siap, Anda harus mengompilasinya menggunakan Foundry.  
Untuk melakukannya, cukup jalankan perintah berikut:

```bash
forge build
```

Jika _smart contract_ tidak memiliki _error_, Anda akan melihat output berikut di terminal:

```text
[⠢] Compiling...
[⠰] Compiling 1 files with 0.8.24
[⠔] Solc 0.8.24 finished in 40.36ms
Compiler run successful!
```

### **Menge-_test_** Smart Contract

Dengan melakukan _testing_ pada _smart contract_, Anda dapat memastikan bahwa _smart contract_ berperilaku sesuai harapan dan tidak ada _bug_ sebelum mendeploy-nya ke Lisk.

Foundry menyediakan banyak _framework testing_ untuk mendukung Anda dalam menulis _tests_ untuk _smart contract_.  
Lihat [Tests - Foundry Book](https://book.getfoundry.sh/forge/tests) untuk contoh dan referensi terkait _framework testing_.

Untuk melakukan _testing_ pada _smart contract_ `NFT`, buat file baru bernama `NFT.t.sol` di dalam direktori `test/`, dan tambahkan konten berikut:

```solidity title="foundry_app/test/NFT.t.sol"
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.20;

import {Test, console} from "forge-std/Test.sol";
import {NFT} from "../src/NFT.sol";

contract NFTTest is Test {

    NFT public lsk;

    // Create dummy addresses for alice and bob
    address alice = makeAddr("alice");
    address bob = makeAddr("bob");

    // Initialize the NFT contract's object
    function setUp() public{
        lsk = new NFT();
    }

    // Pass the address of alice and bob to see whether the mint function successfully passes
    function testMintPass() public {
        lsk.mint(alice);
        lsk.mint(bob);
    }

    // To intentionally create a failing test, let's compare the addresses of alice and bob
    function testMintFail() public {
        assertEq(alice, bob);
    }
}
```

Untuk menjalankan _test_, eksekusi perintah berikut. _Flag_ `-vv` akan menghasilkan informasi detail tentang _test_ yang dijalankan menggunakan perintah berikut:

```bash
forge test -vv
```

Outputnya seharusnya terlihat seperti ini:

```text
[⠢] Compiling...
No files changed, compilation skipped

Ran 2 tests for test/NFT.t.sol:NFTTest
[FAIL. Reason: assertion failed] testMintFail() (gas: 147160)
Logs:
// highlight-start
  Error: a == b not satisfied [address]
        Left: 0x328809Bc894f92807417D2dAD6b7C998c1aFdac6
       Right: 0x1D96F2f6BeF1202E4Ce1Ff6Dad0c2CB002861d3e
// highlight-end

[PASS] testMintPass() (gas: 132327)
Test result: FAILED. 1 passed; 1 failed; 0 skipped; finished in 1.13ms

Ran 1 test suite in 1.13ms: 1 tests passed, 1 failed, 0 skipped (2 total tests)

Failing tests:
Encountered 1 failing test in test/NFT.t.sol:NFTTest
[FAIL. Reason: assertion failed] testMintFail() (gas: 147160)

Encountered a total of 1 failing tests, 1 tests succeeded
```

_Test_ pertama: `testMintPass` berhasil dijalankan karena kriteria untuk fungsi `mint()` terpenuhi.  
Kita memberikan alamat penerima ke fungsi `mint()` sebagaimana yang diminta, sehingga _test_ berhasil.

_Test_ kedua: `testMintFail` gagal karena kita mengasumsikan bahwa alamat `alice` dan `bob` adalah sama.  
_Log output_ yang disorot diatas menjelaskan mengapa asumsi tersebut salah.

### **Mendeploy** Smart Contract

Setelah berhasil membangun _smart contract_, Anda sekarang dapat mendeploy-nya ke jaringan Lisk.  
Untuk contoh ini, kita akan menggunakan jaringan Lisk Sepolia untuk mendeploy kontrak `NFT`.

Tambahkan flag `--verify`, `--verifier`, `--verifier-url`, dan `--private-key` dari akun pengirim ke perintah `forge create` untuk langsung memverifikasi _smart contract_ di BlockScout.

<Tabs>
  <TabItem value="mainnet" label="Lisk" >
    ```bash
    forge create --rpc-url https://rpc.api.lisk.com \
    --etherscan-api-key 123 \
    --verify \
    --verifier blockscout \
    --verifier-url https://blockscout.lisk.com/api \
    --private-key <PRIVATE_KEY> \
    src/NFT.sol:NFT
    ```
  </TabItem>
  <TabItem value="testnet" label="Lisk Sepolia" default>
    ```bash
    forge create --rpc-url https://rpc.sepolia-api.lisk.com \
    --etherscan-api-key 123 \
    --verify \
    --verifier blockscout \
    --verifier-url https://sepolia-blockscout.lisk.com/api \
    --private-key <PRIVATE_KEY> \
    src/NFT.sol:NFT
    ```
  </TabItem>
</Tabs>

Jika deployment berhasil, outputnya akan terlihat seperti berikut:

```text
# Perintah yang disebutkan di atas akan terlebih dahulu mendeploy kontrak dan menampilkan output berikut:

// highlight-start
[⠒] Compiling...
No files changed, compilation skipped
Deployer: 0x5e1A92F84cA1CE280B3Cb29d79C3368f45b41EBB
Deployed to: 0x108872F713A27bc22ca1db8CEefCAC8CbeDdF9E5
Transaction hash: 0xf465528f43e5cbc9b5206e46048feba0b920179813c3eb8c3bdbccbfd13d731e
// highlight-end

# Setelah kontrak berhasil dideploy, perintah yang disebutkan di atas akan langsung memverifikasi kontrak tersebut!

// highlight-start
Starting contract verification...
Waiting for blockscout to detect contract deployment...
Start verifying contract `0x108872F713A27bc22ca1db8CEefCAC8CbeDdF9E5` deployed on 4202

Submitting verification for [src/NFT.sol:NFT] 0x108872F713A27bc22ca1db8CEefCAC8CbeDdF9E5.
Submitted contract for verification:
        Response: `OK`
        GUID: `108872f713a27bc22ca1db8ceefcac8cbeddf9e565e71790`
        URL: https://sepolia-blockscout.lisk.com/address/0x108872f713a27bc22ca1db8ceefcac8cbeddf9e5
Contract verification status:
Response: `OK`
Details: `Pending in queue`
Contract verification status:
Response: `OK`
Details: `Pass - Verified`
Contract successfully verified
// highlight-end
```

Setelah _smart contract_ berhasil dideploy dan diverifikasi, Anda dapat berinteraksi dengan memanggil fungsi-fungsi publiknya.

### **Memverifikasi** Smart Contract

Setiap kontrak yang dideploy harus diverifikasi agar pengguna dan developer lainnya dapat memeriksa _source code_-nya dan memastikan bahwa _source code_ tersebut sesuai dengan _bytecode_ yang dideploy di blockchain.

Selain itu, jika Anda ingin orang lain dapat berinteraksi dengan kontrak Anda melalui _block explorer_ seperti _interface_ [Read Contract](https://sepolia-blockscout.lisk.com/address/0x108872F713A27bc22ca1db8CEefCAC8CbeDdF9E5?tab=read_contract) dan [Write Contract](https://sepolia-blockscout.lisk.com/address/0x108872F713A27bc22ca1db8CEefCAC8CbeDdF9E5?tab=write_contract) di Blockscout, kontrak tersebut harus diverifikasi terlebih dahulu.

Kontrak di atas **sudah diverifikasi**, sehingga Anda seharusnya dapat melihat versi Anda di _block explorer_, tetapi kami tetap akan menjelaskan langkah-langkah untuk memverifikasi kontrak di Lisk Sepolia testnet.

:::info
Anda tidak dapat memverifikasi ulang kontrak yang identik dengan yang sudah diverifikasi. Jika Anda mencoba melakukannya, seperti memverifikasi kontrak di atas, Anda akan mendapatkan pesan error seperti:

```text
Start verifying contract `0x108872F713A27bc22ca1db8CEefCAC8CbeDdF9E5` deployed on 4202

Contract [src/NFT.sol:NFT] "0x108872F713A27bc22ca1db8CEefCAC8CbeDdF9E5" is already verified. Skipping verification.
```

:::

Jika _smart contract_ Anda belum diverifikasi, ambil alamat kontrak yang sudah dideploy dan jalankan:

<Tabs>
  <TabItem value="mainnet" label="Lisk" >
    ```bash
    forge verify-contract <CONTRACT_ADDRESS> \
    ./src/<CONTRACT_FILE>.sol:<CONTRACT_NAME> \
    --chain 1135 \
    --watch \
    --verifier blockscout \
    --verifier-url https://blockscout.lisk.com/api
    ```
  </TabItem>
  <TabItem value="testnet" label="Lisk Sepolia" default>
    ```bash
    forge verify-contract <CONTRACT_ADDRESS> \
    ./src/<CONTRACT_FILE>.sol:<CONTRACT_NAME> \
    --chain 4202 \
    --watch \
    --verifier blockscout \
    --verifier-url https://sepolia-blockscout.lisk.com/api
    ```
  </TabItem>
</Tabs>

Anda akan melihat output yang serupa dengan berikut:

```
Starting contract verification...
Waiting for blockscout to detect contract deployment...
Start verifying contract `0xcCaA1C3eb8FEb5b09a5Eac1359BC4c70E18e29d9` deployed on 4202

Submitting verification for [src/NFT.sol:NFT] 0xcCaA1C3eb8FEb5b09a5Eac1359BC4c70E18e29d9.
Submitted contract for verification:
       Response: `OK`
       GUID: `ccaa1c3eb8feb5b09a5eac1359bc4c70e18e29d965e5c95a`
       URL: https://sepolia-blockscout.lisk.com/address/0xccaa1c3eb8feb5b09a5eac1359bc4c70e18e29d9
Contract verification status:
Response: `OK`
Details: `Pending in queue`
Contract verification status:
Response: `OK`
Details: `Pass - Verified`
Contract successfully verified
```

Gunakan alamat kontrak, misalnya `0xcCaA1C3eb8FEb5b09a5Eac1359BC4c70E18e29d9`, untuk mencari kontrak Anda di [Blockscout](https://sepolia-blockscout.lisk.com/) dan memastikan bahwa kontrak tersebut telah diverifikasi.

## Berinteraksi dengan Smart Contract

Seperti yang disebutkan sebelumnya, jika Anda telah memverifikasi _smart contract_ di Blockscout, Anda dapat menggunakan bagian `Read contract` dan `Write contract` di bawah tab `Contract` untuk berinteraksi dengan kontrak yang telah dideploy.

Tab `Read contract` dapat digunakan tanpa menghubungkan _wallet_, namun, untuk menggunakan tab `Write contract`, Anda harus terlebih dahulu menghubungkan _wallet_ Anda.  
Anda dapat melakukannya dengan mengklik tombol `Connect wallet`.

### Menggunakan **cast** untuk Berinteraksi

Dengan _tool_ command-line dari Foundry: [`cast`](https://book.getfoundry.sh/cast/), Anda dapat berinteraksi dengan kontrak yang telah dideploy, baik untuk membaca maupun menulis data di blockchain.  
Mari kita lakukan panggilan tanpa mempublikasikan transaksi (_read_), lalu menandatangani (_sign_) dan mempublikasikan transaksi (_write_) ke kontrak yang telah dideploy.

#### Melakukan Panggilan (_call_)

Sebagai salah satu komponen utama dari toolkit Foundry, `cast` memungkinkan kita untuk berinteraksi dengan kontrak, mengirim transaksi, dan mendapatkan data on-chain menggunakan panggilan RPC Ethereum.  
Pertama, kita akan melakukan panggilan dari sebuah akun tanpa mempublikasikan transaksi.

Isi `<PLACEHOLDERS>` berikut, lalu jalankan perintah:

<Tabs>
  <TabItem value="mainnet" label="Lisk" >
    ```bash
    cast call <DEPLOYED_CONTRACT_ADDRESS> --rpc-url https://rpc.api.lisk.com "balanceOf(address)" <YOUR_ACCOUNT_ADDRESS_HERE>
    ```
  </TabItem>
  <TabItem value="testnet" label="Lisk Sepolia" default>
    ```bash
    cast call <DEPLOYED_CONTRACT_ADDRESS> --rpc-url https://rpc.sepolia-api.lisk.com "balanceOf(address)" <YOUR_ACCOUNT_ADDRESS_HERE>
    ```
  </TabItem>
</Tabs>

Anda akan menerima respons berupa `0x0000000000000000000000000000000000000000000000000000000000000000`, yang setara dengan `0` dalam format heksadesimal.  
Hal ini masuk akal karena Anda baru saja mendeploy kontrak NFT, namun belum ada NFT yang dicetak (_minted_), sehingga saldo akun Anda adalah nol.

#### Menandatangani (_signing_) dan Mengirim Transaksi

Sekarang, mari kita tandatangani dan kirim transaksi dengan memanggil fungsi `mint(address)` pada kontrak `NFT` yang baru saja kita deploy.

Isi `<PLACEHOLDERS>` berikut, lalu jalankan perintah:

<Tabs>
  <TabItem value="mainnet" label="Lisk" >
    ```bash
    cast send <DEPLOYED_CONTRACT_ADDRESS> --rpc-url https://rpc.api.lisk.com "mint(address)" <RECIPIENT_ADDRESS_HERE> --private-key <SENDER_PRIVATE_KEY>
    ```
  </TabItem>
  <TabItem value="testnet" label="Lisk Sepolia" default>
    ```bash
    cast send <DEPLOYED_CONTRACT_ADDRESS> --rpc-url https://rpc.sepolia-api.lisk.com "mint(address)" <RECIPIENT_ADDRESS_HERE> --private-key <SENDER_PRIVATE_KEY>
    ```
  </TabItem>
</Tabs>

:::info

Karena perintah `cast send` menulis data ke blockchain, maka diperlukan _private key_ akun pengirim yang harus diberikan ke flag `--private-key`.  
Transaksi akan berhasil dikirim jika akun pengirim memiliki dana yang cukup.

Hal ini tidak diperlukan untuk perintah `cast call`, karena perintah tersebut hanya mengambil data yang sudah dipublikasikan dari _smart contract_.

:::

Jika eksekusi transaksi berhasil, Foundry akan memberikan respons berisi informasi tentang transaksi, termasuk `blockNumber`, `gasUsed`, `transactionHash`, dan banyak lagi.

```text
blockHash               0xfa9d32794b0fc9c1a10d39c5289613dfe80b55f8ead06475ca877a389e088e67
// highlight-next-line
blockNumber             2165375
contractAddress
cumulativeGasUsed       137472
effectiveGasPrice       3000000253
from                    0x5e1A92F84cA1CE280B3Cb29d79C3368f45b41EBB
// highlight-next-line
gasUsed                 93597
logs                    [{"address":"0x108872f713a27bc22ca1db8ceefcac8cbeddf9e5","topics":["0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef","0x0000000000000000000000000000000000000000000000000000000000000000","0x000000000000000000000000488ba3c013020bd1712ed6a1997c4212d9711954","0x0000000000000000000000000000000000000000000000000000000000000001"],"data":"0x","blockHash":"0xfa9d32794b0fc9c1a10d39c5289613dfe80b55f8ead06475ca877a389e088e67","blockNumber":"0x210a7f","transactionHash":"0x76750ee1aaeed89c8f165d6f547002eb3bb833a142f73d63c1c3c9980fce8796","transactionIndex":"0x1","logIndex":"0x0","removed":false}]
logsBloom               0x00000000000000000000000000040000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000040000000000000000200000000008000000000000000000040000000000000000000000000000020000000000000000080800000000000000000000000010000000000100000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002000001000000000000400000000000000000000000000000000060000000000000000000000000000000000000000000000000000000000000000000
root
status                  1
// highlight-next-line
transactionHash         0x76750ee1aaeed89c8f165d6f547002eb3bb833a142f73d63c1c3c9980fce8796
transactionIndex        1
type                    2
to                      0x1088…f9e5
l1Fee             "0x30fb62bfb0c"
l1GasPrice             "0x6d49929"
l1GasUsed             "0x8a4"
```

Terakhir, Anda dapat mengonfirmasi proses minting dengan [melakukan panggilan](#melakukan-panggilan-call) lagi.  
Anda seharusnya melihat saldo Anda meningkat dari `0` menjadi `1`.

<Tabs>
  <TabItem value="mainnet" label="Lisk" >
    ```bash
    cast call <DEPLOYED_CONTRACT_ADDRESS> --rpc-url https://rpc.api.lisk.com "balanceOf(address)" <YOUR_ACCOUNT_ADDRESS_HERE>
    ```
  </TabItem>
  <TabItem value="testnet" label="Lisk Sepolia" default>
    ```bash
    cast call <DEPLOYED_CONTRACT_ADDRESS> --rpc-url https://rpc.sepolia-api.lisk.com "balanceOf(address)" <YOUR_ACCOUNT_ADDRESS_HERE>
    ```
  </TabItem>
</Tabs>

Dan responsnya: `0x0000000000000000000000000000000000000000000000000000000000000001` (`1` dalam format heksadesimal) — selamat, Anda berhasil mendeploy kontrak dan mencetak NFT dengan Foundry!

Lihat token yang telah dicetak dalam panduan ini di [Blockscout explorer](https://sepolia-blockscout.lisk.com/token/0x108872F713A27bc22ca1db8CEefCAC8CbeDdF9E5).

Itu dia! Meskipun ini hanya permulaan, masih banyak hal yang dapat dipelajari tentang Foundry.  
Untuk semua hal tentang Foundry, kunjungi [Foundry book](https://book.getfoundry.sh/), atau bergabunglah dengan [dev chat](https://t.me/foundry_rs) atau [support chat](https://t.me/foundry_support) resmi di Telegram.