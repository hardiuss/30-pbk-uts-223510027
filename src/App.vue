<template>
  <div class="container">
    <!-- Header dengan menu Post dan Todos -->
    <header class="header">
      <nav>
        <ul>
          <li @click="toggleMenu('todos')" :class="{ active: isTodosActive }">Todos</li>
          <li @click="toggleMenu('post')" :class="{ active: !isTodosActive }">Post</li>
        </ul>
      </nav>
    </header>

    <!-- Konten sesuai dengan menu yang dipilih -->
    <div v-if="isTodosActive">
      <h2>Daftar Kegiatan</h2>
      <ul class="kegiatan-list">
        <li v-for="kegiatan in kegiatanList" :key="kegiatan.id">
          <div class="kegiatan-item">
            <input type="checkbox" v-model="kegiatan.isDone" />
            <span :class="{ 'text-decoration-line-through': kegiatan.isDone }">{{ kegiatan.nama }}</span>
            <button @click="deleteKegiatan(kegiatan)" class="button-delete">Hapus</button>
          </div>
        </li>
      </ul>
      <form @submit.prevent="addKegiatan">
        <input type="text" v-model="newKegiatan.nama" placeholder="Tambah Kegiatan Baru" class="input-new-kegiatan" />
        <button type="submit" class="button-add-kegiatan">Tambah</button>
      </form>
    </div>

    <div v-else>
      <h2>Daftar Postingan</h2>
      <label for="userSelect">Pilih Pengguna:</label>
      <select id="userSelect" v-model="selectedUser" @change="getPosts">
        <option v-for="user in userList" :key="user.id" :value="user.id">{{ user.name }}</option>
      </select>
      <div v-if="posts.length > 0">
        <div v-for="post in posts" :key="post.id" class="post-item">
          <h3>{{ post.title }}</h3>
          <p>{{ post.body }}</p>
        </div>
      </div>
      <p v-else>Tidak ada postingan.</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      kegiatanList: [
        { id: 1, nama: 'Belajar Vue.js', isDone: false },
        { id: 2, nama: 'Meeting dengan Tim', isDone: false }
      ],
      newKegiatan: { nama: '' },
      isTodosActive: true, // Menandakan apakah menu Todos aktif
      userList: [], // Menyimpan daftar pengguna
      selectedUser: null, // Menyimpan id pengguna yang dipilih
      posts: [] // Menyimpan daftar postingan
    };
  },
  created() {
    this.fetchUsers(); // Memuat daftar pengguna saat komponen dibuat
  },
  methods: {
    addKegiatan() {
      this.kegiatanList.push({ id: this.kegiatanList.length + 1, nama: this.newKegiatan.nama, isDone: false });
      this.newKegiatan.nama = '';
    },
    deleteKegiatan(kegiatan) {
      this.kegiatanList = this.kegiatanList.filter(k => k.id !== kegiatan.id);
    },
    toggleMenu(menu) {
      // Method untuk menuju ke menu Todos atau Post
      this.isTodosActive = menu === 'todos';
      if (!this.isTodosActive) {
        this.getPosts(); // Memuat postingan saat beralih ke menu Post
      }
    },
    fetchUsers() {
      // Mengambil daftar pengguna dari API
      fetch('https://jsonplaceholder.typicode.com/users')
        .then(response => response.json())
        .then(data => {
          this.userList = data;
          if (data.length > 0) {
            this.selectedUser = data[0].id; // Mengatur pengguna pertama sebagai default
            this.getPosts(); // Memuat postingan pertama saat komponen dibuat
          }
        })
        .catch(error => console.error('Error fetching users:', error));
    },
    getPosts() {
      // Mengambil postingan sesuai dengan pengguna yang dipilih
      if (this.selectedUser) {
        fetch(`https://jsonplaceholder.typicode.com/posts?userId=${this.selectedUser}`)
          .then(response => response.json())
          .then(data => {
            this.posts = data;
          })
          .catch(error => console.error('Error fetching posts:', error));
      } else {
        this.posts = []; // Jika tidak ada pengguna yang dipilih, kosongkan daftar postingan
      }
    }
  }
};
</script>

<style scoped>
/* CSS yang sama seperti sebelumnya */

/* Menandai menu yang aktif */
.header nav ul li.active {
  font-weight: bold;
}

/* Gaya untuk postingan */
.post-item {
  margin-bottom: 20px;
}
</style>