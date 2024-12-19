---
title: Ikhtisar
slug: /token-development
description: "Pengantar tentang standar token ERC dan pengembangan token di Lisk."
keywords:
  [
    "Lisk",
    "Token development",
    "Deploy token",
    "ERC",
    "EIP",
    "ERC-20",
    "ERC-721",
    "ERC-1155",
    "NFT",
    "Fungible token",
    "Hybrid token",
  ]
---

# Pengembangan Token

Halaman ini ditujukan untuk penerbit token yang ingin membuat kontrak ERC-20 baru di Lisk.
Halaman ini mencakup penjelasan tentang ERC, ringkasan standar token terpenting, dan contoh cara mendeploy token tersebut di Lisk.
Jika Anda sudah memiliki token yang dideploy di jaringan Ethereum dan ingin memindahkan (_bridge_) token tersebut ke Lisk, silakan merujuk ke panduan [Memindahkan Token L1 ke Lisk](../add-token-to-lisk/index.md).

## Standar Token ERC

_Interface_ standar memungkinkan token apa pun di Ethereum untuk digunakan kembali oleh aplikasi lain: mulai dari _wallet_ hingga _decentralized exchange_.
**ERCs** (_Ethereum Request for Comments_) adalah serangkaian standar dan konvensi di tingkat aplikasi, termasuk standar kontrak seperti standar token (ERC-20), registri nama (ERC-137), skema URI, format _library/package_, dan format _wallet_ untuk blockchain Ethereum.

Mengikuti standar token ERC yang paling populer saat membuat token baru memiliki beberapa manfaat:

- **Keamanan meningkat:** Biarkan kontrak Anda mewarisi implementasi standar yang telah diaudit dan ditinjau secara menyeluruh, sehingga kemungkinan _bug_ berkurang secara signifikan.
- **Kompatibilitas aplikasi tinggi:** Sebagian besar aplikasi hanya mendukung standar token ERC yang paling populer. Dengan mengikuti standar ini, Anda memastikan token Anda akan kompatibel dengan sebagian besar aplikasi eksternal seperti _wallet_ atau _decentralized exchange_.
- **Dokumentasi yang baik:** Manfaatkan banyak tutorial dan panduan yang tersedia untuk mengembangkan token yang sesuai dengan ERC.

ERC adalah subkategori dari **EIPs** (_Ethereum Improvement Proposals_).
EIP baru ditambahkan mengikuti proses yang dijelaskan dalam [EIP-1](https://eips.ethereum.org/EIPS/eip-1).

Berikut adalah daftar lengkap [proposal ERC](https://eips.ethereum.org/erc).

Ringkasan beberapa standar token ERC yang menarik dapat ditemukan di bawah ini:

- [ERC-20](#erc-20): standar token yang paling digunakan untuk token yang dapat dipertukarkan (_fungible_), meskipun agak terbatas karena kesederhanaannya.
- [ERC-721](#erc-721): standar token paling populer untuk token yang tidak dapat dipertukarkan (_non-fungible_), sering digunakan untuk koleksi dan permainan.
- [ERC-1155](#erc-1155): standar untuk multi-token, memungkinkan satu kontrak merepresentasikan banyak token yang dapat dipertukarkan maupun tidak, serta mendukung operasi batch untuk efisiensi gas yang lebih baik.

## ERC-20

Standar token yang paling digunakan untuk token yang dapat dipertukarkan (_fungible_).  
Setiap token memiliki nilai yang sama dengan token lainnya; tidak ada token yang memiliki hak atau perilaku khusus.  
Hal ini membuat [ERC-20](https://eips.ethereum.org/EIPS/eip-20) berguna untuk berbagai hal seperti mata uang, media pertukaran, hak suara, _staking_, dan lainnya.

### Panduan

[Bagaimana cara mendeploy token ERC-20 baru di Lisk](deploy-erc-20.md)

### Bacaan lebih lanjut

- [Memahami smart contract token ERC-20](https://ethereum.org/en/developers/tutorials/understand-the-erc-20-token-smart-contract/)
- [ERC-20 EIP](https://eips.ethereum.org/EIPS/eip-20)
- [OpenZeppelin: API ERC-20](https://docs.openzeppelin.com/contracts/3.x/api/token/erc20)
- [OpenZeppelin: Kontrak ERC-20](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol)
- [Solidity by example: ERC-20](https://solidity-by-example.org/app/erc20/)
- [Dokumentasi Ethereum: ERC-20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/)
- [Alchemy: Panduan lengkap tentang ERC-20](https://www.alchemy.com/overviews/erc20-solidity)

---

## ERC-721

[ERC-721](https://eips.ethereum.org/EIPS/eip-721) adalah standar untuk merepresentasikan kepemilikan token yang tidak dapat dipertukarkan (_non-fungible_).  
_Non-fungible tokens_ (NFTs) digunakan untuk merepresentasikan objek unik seperti real estate atau barang koleksi (_collectibles_), di mana beberapa item memiliki nilai lebih tinggi dibandingkan lainnya karena kegunaan, kelangkaan, atau karakteristik individu lainnya.

Untuk merepresentasikan fitur unik ini secara onchain, ERC-721 menyertakan properti metadata yang menyediakan informasi tentang fitur spesifik token, seperti judul, pencipta, dan pratinjau gambar.

### Panduan

[Bagaimana cara mendeploy token ERC-721 baru di Lisk](deploy-erc-721.md)

### Bacaan lebih lanjut

- [Dokumentasi Ethereum: ERC-721](https://ethereum.org/en/developers/docs/standards/tokens/erc-721/)
- [ERC-721 EIP](https://eips.ethereum.org/EIPS/eip-721)
- [OpenZeppelin: API ERC-721](https://docs.openzeppelin.com/contracts/3.x/api/token/erc721)
- [OpenZeppelin: Kontrak ERC-721](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol)
- [Solidity by example: ERC-721](https://solidity-by-example.org/app/erc721/)
- [Bagaimana cara mengimplementasikan pasar ERC-721](https://ethereum.org/en/developers/tutorials/how-to-implement-an-erc721-market/)

## ERC-1155

[ERC-1155](https://eips.ethereum.org/EIPS/eip-1155) adalah _interface_ standar untuk kontrak yang mengelola berbagai jenis token.  
Fitur khas ERC-1155 adalah penggunaan satu kontrak pintar untuk merepresentasikan banyak token sekaligus.  
Satu kontrak yang dideploy dapat mencakup kombinasi token _fungible_, _non-fungible_, atau konfigurasi lainnya (misalnya, token _semi-fungible_).

Karena itu, fungsi `balanceOf` pada ERC-1155 berbeda dari ERC-20:  
fungsi ini memiliki argumen tambahan, yaitu `id`, untuk mengidentifikasi token yang ingin Anda query saldonya.  
Akun ERC-1155 memiliki saldo yang berbeda untuk setiap token id; token non-fungible diimplementasikan dengan hanya mencetak satu token saja.

Pendekatan ini menghasilkan penghematan gas yang signifikan untuk proyek yang memerlukan banyak token.  
Alih-alih mendeploy kontrak baru untuk setiap jenis token, satu kontrak token ERC-1155 dapat menyimpan seluruh _state_ sistem, sehingga mengurangi biaya deployment dan kompleksitas.

### Panduan

[Bagaimana cara mendeploy token ERC-1155 baru di Lisk](deploy-erc-1155.md)

:::warning
Harap diperhatikan bahwa saat ini dukungan ekosistem untuk ERC-1155 masih lebih sedikit dibandingkan ERC-20 atau ERC-721.
:::

### Bacaan lebih lanjut

- [Dokumentasi Ethereum: ERC-1155](https://ethereum.org/en/developers/docs/standards/tokens/erc-1155/)
- [ERC-1155 EIP](https://eips.ethereum.org/EIPS/eip-1155)
- [OpenZeppelin: API ERC-1155](https://docs.openzeppelin.com/contracts/3.x/api/token/erc1155)
- [OpenZeppelin: Kontrak ERC-1155](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC1155/ERC1155.sol)
- [Solidity by example: ERC-1155](https://solidity-by-example.org/app/erc1155/)