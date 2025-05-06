<template>
  <div class="page">
    <header class="header">
      <div class="brand">
        <h2>UTBK Panel</h2>
      </div>
      <div class="date">{{ tanggalHariIni }}</div>
    </header>

    <main class="container">
      <h1>Daftar Peserta UTBK 2025</h1>

      <div class="stats">
        <p>Total: <strong>{{ daftarPeserta.length }}</strong></p>
        <p>Hadir: <strong>{{ pesertaHadir }}</strong></p>
      </div>

      <div class="input-section">
        <input
          v-model="newPeserta"
          @keyup.enter="tambahPeserta"
          placeholder="Masukkan nama peserta..."
        />
        <button class="add-btn" @click="tambahPeserta">Tambah</button>
      </div>

      <div class="filter">
        <label>
          <input type="checkbox" v-model="filterBelumHadir" />
          Tampilkan yang belum hadir
        </label>
      </div>

      <ul class="list">
        <transition-group name="fade" tag="div">
          <li v-for="(peserta, index) in filteredPeserta" :key="peserta.nama + index">
            <input type="checkbox" v-model="peserta.hadir" />
            <span :class="{ hadir: peserta.hadir }">{{ peserta.nama }}</span>
            <button @click="hapusPeserta(index)">❌</button>
          </li>
        </transition-group>
      </ul>

      <button v-if="daftarPeserta.length" class="clear-btn" @click="konfirmasiHapusSemua">
        Hapus Semua Peserta
      </button>
    </main>

    <footer class="footer">
      <p>&copy; 2025 UTBK Management System</p>
    </footer>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newPeserta: '',
      daftarPeserta: [],
      filterBelumHadir: false,
    }
  },
  computed: {
    filteredPeserta() {
      return this.filterBelumHadir
        ? this.daftarPeserta.filter(p => !p.hadir)
        : this.daftarPeserta
    },
    pesertaHadir() {
      return this.daftarPeserta.filter(p => p.hadir).length
    },
    tanggalHariIni() {
      return new Date().toLocaleDateString('id-ID', {
        weekday: 'long',
        day: 'numeric',
        month: 'long',
        year: 'numeric',
      })
    },
  },
  methods: {
    tambahPeserta() {
      const nama = this.newPeserta.trim()
      if (!nama) return
      const duplikat = this.daftarPeserta.some(
        p => p.nama.toLowerCase() === nama.toLowerCase()
      )
      if (!duplikat) {
        this.daftarPeserta.push({ nama, hadir: false })
        this.newPeserta = ''
      } else {
        alert('Nama sudah ada di daftar.')
      }
    },
    hapusPeserta(index) {
      this.daftarPeserta.splice(index, 1)
    },
    konfirmasiHapusSemua() {
      if (confirm('Yakin ingin menghapus semua peserta?')) {
        this.daftarPeserta = []
      }
    },
  },
  mounted() {
    const saved = localStorage.getItem('pesertaUTBK')
    if (saved) {
      this.daftarPeserta = JSON.parse(saved)
    }
  },
  watch: {
    daftarPeserta: {
      handler(val) {
        localStorage.setItem('pesertaUTBK', JSON.stringify(val))
      },
      deep: true,
    },
  },
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap');

.page {
  font-family: 'Poppins', sans-serif;
  color: #2e7d32;
  min-height: 100vh;
  background-color: #f0f4f1;
  display: flex;
  flex-direction: column;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.2rem 2rem;
  background-color: rgba(255, 255, 255, 0.9);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}
.brand {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}
.date {
  font-size: 0.9rem;
  font-weight: 500;
}

.container {
  background: rgba(255, 255, 255, 0.92);
  margin: 2rem auto;
  padding: 2rem;
  border-radius: 16px;
  width: 90%;
  max-width: 700px;
  box-shadow: 0 10px 30px rgba(0, 64, 0, 0.2);
}

h1 {
  text-align: center;
  margin-bottom: 1.2rem;
  font-weight: 600;
}

.stats {
  display: flex;
  justify-content: space-between;
  margin-bottom: 1rem;
  font-size: 0.95rem;
}

.input-section {
  display: flex;
  gap: 10px;
  margin-bottom: 1rem;
}
.input-section input {
  flex: 1;
  padding: 10px;
  border: 2px solid #66bb6a;
  border-radius: 8px;
}
.add-btn {
  padding: 10px 16px;
  background-color: #2e7d32;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

.filter {
  margin-bottom: 1rem;
}

.list {
  list-style: none;
  padding: 0;
}
.list li {
  background: #f1f8e9;
  padding: 10px 15px;
  border-radius: 8px;
  margin-bottom: 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.list li span.hadir {
  text-decoration: line-through;
  color: #999;
}
.clear-btn {
  margin-top: 1rem;
  background-color: #d32f2f;
  color: white;
  padding: 10px;
  border: none;
  border-radius: 10px;
  width: 100%;
  cursor: pointer;
}

.footer {
  text-align: center;
  padding: 1rem;
  background-color: rgba(255, 255, 255, 0.85);
  font-size: 0.85rem;
  margin-top: auto;
}

.fade-enter-active,
.fade-leave-active {
  transition: all 0.3s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateY(10px);
}
</style>
