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

