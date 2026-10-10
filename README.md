# ⚡ Monad Blitz 2 - Full-Stack Starter Framework

> Framework starter full-stack produksi siap pakai untuk membangun dApp generasi baru di **Monad EVM**. Menggabungkan kecepatan **Next.js 14 (App Router)**, **Viem & Wagmi v2**, **RainbowKit**, dan **Foundry Solidity** dalam arsitektur **Turborepo Monorepo** yang modern.

---

## 🚀 Fitur Utama

- **Monorepo Berbasis Turborepo**: Manajemen workspace terintegrasi (`apps/web` dan `packages/contracts`) dengan caching pipa build yang cepat dan modular.
- **Monad EVM Cancun Ready**: Smart contract dikonfigurasi menggunakan Solidity `0.8.24` dengan spesifikasi `evm_version = "cancun"`, dioptimalkan untuk performa tinggi Monad.
- **MonadStarter.sol Multifitur**:
  - **ERC-20 Token (`MBLITZ`)**: Implementasi standar token dengan decoupled balance model.
  - **Built-in Faucet**: Faucet instan dengan cooldown 60 detik (`FAUCET_AMOUNT = 100 MBLITZ`) untuk kemudahan demo dan onboarding user.
  - **Global State Counter & Purpose Message**: State read/write interaktif real-time untuk pengujian transaksi instan.
  - **Parallel-Execution Friendly**: Struktur storage dirancang untuk meminimalkan lock contention pada mesin eksekusi paralel Monad.
- **Otomatisasi Sinkronisasi ABI (Auto-sync ABI)**:
  - Eksekusi `npm run build` di package kontrak langsung mengompilasi Solidity, menghasilkan TypeScript definitions, dan otomatis menyinkronkan ABI serta alamat kontrak ke `apps/web/src/contracts/deployedContracts.ts`.
- **Dukungan Multi-Environment**:
  - **Monad Testnet** (Chain ID: `10143`) - Default environment
  - **Monad Mainnet** (Chain ID: `143`)
  - **Anvil Localhost** (Chain ID: `31337`)
- **Modern Dark Theme UI**:
  - Tampilan elegan bernuansa cyberpunk/neon dengan Tailwind CSS.
  - RainbowKit v2 + Wagmi v2 + TanStack Query v5 untuk interaksi wallet yang mulus.
  - Dashboard interaktif lengkap dengan wallet connection, kartu saldo MBLITZ, klaim faucet, transfer token, pembacaan counter, dan pengubahan pesan status on-chain.

---

## 📂 Struktur Direktori

```text
Monad Blitz 2/
├── .agents/                      # Skills, referensi arsitektur, dan panduan Monad
│   └── skills/
│       ├── addresses/            # Kontrak dan alamat resmi Monad
│       ├── concepts/             # Dokumentasi parallel execution, block states, EIP-7702
│       ├── gas/                  # Mekanisme gas & reserve balance Monad
│       ├── tooling-and-infra/    # Konfigurasi RPC, Foundry, & Viem
│       └── wallet-integration/   # Integrasi wallet & embedded providers
├── apps/
│   └── web/                      # Frontend Next.js 14 (App Router)
│       ├── src/
│       │   ├── app/              # Halaman utama, providers, layout, styling
│       │   ├── config/           # Definisi chain (chains.ts) dan Wagmi (wagmi.ts)
│       │   └── contracts/        # deployedContracts.ts (Auto-synced ABI & alamat)
│       ├── tailwind.config.ts    # Desain tema gelap kustom Monad
│       └── package.json
├── packages/
│   └── contracts/                # Workspace Smart Contract (Solidity & Foundry)
│       ├── foundry.toml          # Konfigurasi solc 0.8.24 Cancun & RPC Monad
│       ├── script/
│       │   └── Deploy.s.sol      # Foundry deployment broadcast script
│       ├── scripts/
│       │   ├── export-artifacts.js # Kompilasi solc & auto-sync ABI ke frontend
│       │   └── test-contracts.js   # Script pengujian validasi unit contract
│       ├── src/
│       │   └── MonadStarter.sol  # Kontrak utama MonadStarter
│       └── package.json
├── .env.example                  # Template variabel lingkungan
├── package.json                  # Root package.json (Turborepo orchestration)
└── turbo.json                    # Konfigurasi pipeline build & dev Turborepo
```

---

## 🏁 Quickstart Guide

### 1. Prasyarat
- **Node.js** >= `18.18.0` atau `20.x`
- **npm** >= `9.x`
- **Foundry** (opsional, disarankan jika deploy langsung via Forge CLI: `curl -L https://foundry.paradigm.xyz | bash && foundryup`)

### 2. Instalasi Dependensi
Jalankan perintah berikut di root folder workspace:

```bash
npm install
```

### 3. Konfigurasi Environment (`.env.local`)
Salin file template `.env.example` ke file `.env.local` di folder `apps/web`:

```bash
# Windows PowerShell:
Copy-Item .env.example apps/web/.env.local

# Linux / macOS:
cp .env.example apps/web/.env.local
```

Sesuaikan parameter di `apps/web/.env.local`:
```env
# Chain default yang aktif: 10143 (Testnet), 143 (Mainnet), atau 31337 (Anvil)
NEXT_PUBLIC_DEFAULT_CHAIN_ID=10143

# RPC Endpoints
NEXT_PUBLIC_LOCAL_RPC=http://127.0.0.1:8545
NEXT_PUBLIC_MONAD_TESTNET_RPC=https://testnet-rpc.monad.xyz
NEXT_PUBLIC_MONAD_MAINNET_RPC=https://rpc.monad.xyz

# WalletConnect Cloud Project ID (Dapatkan gratis di https://cloud.walletconnect.com)
NEXT_PUBLIC_WALLET_CONNECT_PROJECT_ID=demo_project_id

# Private Key untuk script deployment Foundry (Hanya untuk deploy, JANGAN COMMIT kunci asli)
PRIVATE_KEY=
```

### 4. Menjalankan Aplikasi
Anda dapat menjalankan seluruh stack dari root menggunakan Turborepo:

```bash
# Menjalankan frontend dev server dari root:
npm run dev
```

Atau masuk langsung ke direktori frontend:

```bash
cd apps/web
npm run dev
```

Buka peramban di [http://localhost:3000](http://localhost:3000) untuk mulai berinteraksi dengan dApp.

---

## 🛠️ Alur Kerja Smart Contracts

Semua pengembangan smart contract berpusat di folder `packages/contracts`.

### 1. Kompilasi & Auto-Sync ABI
Kompilasi kontrak Solidity dan sinkronkan ABI serta bytecode terbaru langsung ke frontend:

```bash
# Dari root:
npm run build --workspace=@monad-blitz/contracts

# Atau dari dalam packages/contracts:
cd packages/contracts
npm run build
```

> **Catatan**: Script `export-artifacts.js` akan membuat file `packages/contracts/dist/index.ts` dan secara otomatis memperbarui `apps/web/src/contracts/deployedContracts.ts` tanpa perlu copy-paste manual.

### 2. Menjalankan Pengujian (Testing)
Validasi integritas fungsi dan ABI kontrak:

```bash
# Dari root:
npm run test --workspace=@monad-blitz/contracts

# Atau dari packages/contracts:
cd packages/contracts
npm run test
```

### 3. Deploy Kontrak via Foundry Forge Script

#### A. Deploy ke Local Anvil
```bash
# 1. Jalankan anvil di terminal terpisah
anvil

# 2. Eksekusi script deploy ke anvil
cd packages/contracts
forge script script/Deploy.s.sol --rpc-url http://127.0.0.1:8545 --broadcast --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
```

#### B. Deploy ke Monad Testnet (Chain ID 10143)
Pastikan deployer wallet memiliki saldo testnet MON untuk gas:

```bash
cd packages/contracts
forge script script/Deploy.s.sol \
  --rpc-url https://testnet-rpc.monad.xyz \
  --broadcast \
  --private-key $PRIVATE_KEY \
  --legacy
```

#### C. Deploy ke Monad Mainnet (Chain ID 143)
```bash
cd packages/contracts
forge script script/Deploy.s.sol \
  --rpc-url https://rpc.monad.xyz \
  --broadcast \
  --private-key $PRIVATE_KEY \
  --legacy
```

*Setelah deployment sukses, perbarui alamat kontrak baru pada mapping `MONAD_STARTER_ADDRESSES` di `packages/contracts/scripts/export-artifacts.js` lalu jalankan `npm run build` kembali untuk menyinkronkan ke web.*

---

## 💡 Monad Best Practices

Membangun dApp di Monad memerlukan pemahaman karakteristik arsitektur konsensus dan eksekusi khususnya:

1. **Desain Ramah Eksekusi Paralel (Parallel Execution Friendly)**:
   - Monad mengeksekusi transaksi secara paralel menggunakan pendekatan optimistik (*pipelined execution*).
   - **Hindari State Hotspots**: Jangan gunakan satu storage slot global yang ditulis oleh ratusan user dalam satu waktu secara bersamaan jika tidak diperlukan (misalnya: counter global raksasa pada setiap interaksi).
   - **Gunakan Pola Penyimpanan Terisolasi**: Pisahkan state ke mapping berbasis pengguna (`mapping(address => UserData)`), sharded counters, atau batch withdrawal pattern agar transaksi antar-user dapat dieksekusi simultan tanpa invalidasi cache.

2. **Granularitas Timestamp ~300ms**:
   - Monad menghasilkan block/slot time sub-detik (~300ms), jauh lebih cepat daripada Ethereum (12s).
   - Jangan membuat logika kontrak dengan asumsi interval blok adalah belasan detik.
   - Gunakan `block.timestamp` dengan pemahaman bahwa interval antar-blok sangat rapat.

3. **Maksimum Ukuran Kontrak (Max Contract Size) 128 KB**:
   - Di Ethereum (EIP-170), batas ukuran bytecode kontrak adalah 24.576 byte (24 KB).
   - **Monad mendukung batas ukuran kontrak hingga 128 KB**, memberikan fleksibilitas tinggi bagi developer untuk menyertakan logika kompleks tanpa harus memecah kontrak menjadi pola diamond proxy yang rumit kecuali diperlukan.

4. **Reserve Balance & Gas Management**:
   - Transaksi di Monad memerlukan saldo cadangan gas (*reserve balance*) untuk memastikan slot eksekusi tetap efisien. Gunakan flag `--legacy` jika diperlukan saat berinteraksi dengan tool deployment standar EVM.

---

## 🌐 Referensi Jaringan Resmi Monad

| Parameter | Monad Testnet | Monad Mainnet | Local Anvil |
| :--- | :--- | :--- | :--- |
| **Chain ID** | `10143` | `143` | `31337` |
| **Network Name** | Monad Testnet | Monad Mainnet | Anvil Localhost |
| **Currency** | MON (18 desimal) | MON (18 desimal) | ETH (18 desimal) |
| **Public RPC** | `https://testnet-rpc.monad.xyz` | `https://rpc.monad.xyz` | `http://127.0.0.1:8545` |
| **Block Explorer** | [testnet.monadexplorer.com](https://testnet.monadexplorer.com) | [monadvision.com](https://monadvision.com) / [monadscan.com](https://monadscan.com) | N/A |
| **EVM Spec** | Cancun (0.8.24) | Cancun (0.8.24) | Cancun / Shanghai |

### Tautan Penting:
- 📖 **Dokumentasi Resmi Monad**: [https://docs.monad.xyz](https://docs.monad.xyz)
- 🚀 **Aplikasi & Portal Monad**: [https://app.monad.xyz](https://app.monad.xyz)
- 🚰 **Testnet Faucet**: Kunjungi [testnet.monad.xyz](https://testnet.monad.xyz) atau bot faucet resmi Monad Discord.

---

## 📄 Lisensi
Didistribusikan di bawah lisensi MIT. Silakan gunakan template ini untuk hackathon, riset, maupun produksi di Monad!
