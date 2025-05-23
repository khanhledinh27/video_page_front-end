<template>

  <div>
    <!-- Navbar -->
    <nav class="navbar navbar-expand-lg navbar-light custom-navbar">
      <div class="container">
        <router-link to="/" @click="reloadPage" class="navbar-brand">
          <img src="/src/assets/logo_plt.png" class="navbar-logo" alt="PLT Solutions Logo">
        </router-link>
        <button
          class="navbar-toggler"
          type="button"
          data-bs-toggle="collapse"
          data-bs-target="#navbarSupportedContent"
          aria-controls="navbarSupportedContent"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav me-auto mb-2 mb-lg-0">
            <li class="nav-item">
                <router-link to="/" class="nav-link " aria-current="page">Trang chủ</router-link>
              </li>
              <li class="nav-item">
                <router-link to="/course" class="nav-link active">Bài giảng</router-link>
              </li>
              <li class="nav-item">
                <router-link to="/courselist" class="nav-link">Khóa học</router-link>
              </li>
              <li class="nav-item">
                <router-link to="/contact" class="nav-link">Liên hệ</router-link>
              </li>
          </ul>
        </div>
      </div>
    </nav>

    <div class="category-filter">
      <button v-for="category in categories" :key="category" @click="filterByCategory(category)"
        :class="selectedCategory === category ? 'btn-info' : 'btn-outline-secondary'">
        {{ category }}
      </button>
      <button @click="resetFilter()" class="btn btn-sm btn-outline-dark mx-1" v-if="selectedCategory">
        Tất cả
      </button>
    </div>

    <!-- Search Input -->
    <div class="search-box">
      <input type="text" v-model="searchQuery" placeholder="Search lectures..." class="form-control" />
    </div>

    <div class="card-body">
      <div class="table-responsive">
        <div class="table-bordered">
          <div v-if="filteredLectures.length > 0">
            <div class="hpage-total_videos">

              <ul v-for="(data, index) in filteredLectures" :key="index" class="lecture-item">
                <li class="frame-total_videos">
                  <div class="video-wrapper">
                    <router-link :to="'/videodetail/' + data.id">
                      <img :src="getThumbnailUrl(data.video_url)" alt="Video Thumbnail" class="video-thumbnail">
                    </router-link>
                  </div>
                </li>
                <div class="title-videos-all">
                  <li class="frame-total_videos desc_videos">
                    <router-link :to="'/videodetail/' + data.id" class="link_pagevideos">
                      <h4><strong>{{ data.title }}</strong></h4>
                    </router-link>
                  </li>
                  <li class="frame-total_videos desc_videos">
                    <p>{{ data.description }}</p>
                  </li>
                </div>



              </ul>

            </div>
          </div>
          <div v-else>
            <p class="text-center">{{ isLoading ? 'Loading...' : 'Không tìm thấy video' }}</p>
          </div>
        </div>
      </div>
    </div>
    <!-- Footer Section -->
    <footer class="text-center text-white">
        <div class="container p-4 pb-0">
          <section class="">
            <p class="d-flex justify-content-center align-items-center">
              <span class="me-3">Chào mừng đến với PLT Solutions, nơi sẽ không làm các bạn thất vọng!!!</span>
            </p>
          </section>
        </div>
        <div class="text-center p-3">
          © 2025 Copyright:
          <router-link class="text-white" to="/">PLT Solutions</router-link>
        </div>

      </footer>
  </div>
</template>

<script>
import axios from 'axios'
import '@/assets/homeview_videos.css'

export default {
  name: "Lectures",
  data() {
    return {
      lectures: [],
      categories: [],
      selectedCategory: null,
      searchQuery: '', // Add searchQuery data property
      isLoading: true
    };
  },
  computed: {
    filteredLectures() {
      let filtered = this.lectures;
      if (this.selectedCategory) {
        filtered = filtered.filter(lecture => lecture.category === this.selectedCategory);
      }
      if (this.searchQuery) {
        filtered = filtered.filter(lecture =>
          lecture.title.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
          lecture.description.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
      }
      return filtered;
    }
  },
  mounted() {
    this.getLectures();
  },
  methods: {
    filterByCategory(category) {
      this.selectedCategory = category;
    },
    resetFilter() {
      this.selectedCategory = null;
    },
    getLectures() {
      this.isLoading = true;
      axios.get(`${import.meta.env.VITE_API_URL}/api/lectures`)
        .then(res => {
          this.lectures = res.data.data;
          // Lấy danh sách các category duy nhất
          this.categories = [...new Set(this.lectures.map(lecture => lecture.category))];
          this.isLoading = false;
        })
        .catch(error => {
          console.log(error);
          this.isLoading = false;
        });
    },
    reloadPage(event) {
      event.preventDefault(); // Ngăn chặn điều hướng mặc định
      window.location.reload(); // Tải lại trang
    },
    extractYouTubeId(url) {
      const regExp = /(?:youtu\.be\/|youtube\.com\/(?:.*v=|.*\/v\/|.*embed\/|.*youtu\.be\/|v=))([^&?\/]+)/;
      const match = url.match(regExp);
      return match && match[1] ? match[1] : null;
    },
    getThumbnailUrl(url) {
      const videoId = this.extractYouTubeId(url);
      return videoId ? `https://img.youtube.com/vi/${videoId}/hqdefault.jpg` : '';
    }
  }
};
</script>

<style scoped>
/* Navbar Styles */
.custom-navbar {
  background-color: #ffffff;
  border-bottom: 2px solid #324ee9;
  padding: 10px 0;
}

.navbar-logo {
  height: 60px;
  width: auto;
  margin-right: 20px;
}

.navbar-nav {
  display: flex;
  align-items: center;
}

.navbar-nav .nav-item {
  margin-right: 20px;
  font-size: 16px;
  font-weight: bold;
}

.navbar-nav .nav-link {
  color: #000000;
  text-transform: capitalize;
  transition: color 0.3s ease;
}

.navbar-nav .nav-link:hover {
  color: #324ee9;
}

.navbar-nav .nav-link.active {
  color: #324ee9;
}
.search-form {
  display: flex;
  align-items: center;
}

.search-input {
  border: 1px solid #ccc;
  border-radius: 20px;
  padding: 5px 15px;
  width: 250px;
  transition: border-color 0.3s ease;
}

.search-input:focus {
  border-color: #324ee9;
  outline: none;
}

@media (max-width: 768px) {
  .navbar-nav .nav-item {
    margin-right: 10px;
  }

  .search-input {
    width: 150px;
  }
}

/* COURSES PAGE */
.video-thumbnail {
  width: 100%;
  height: auto;
  cursor: pointer;
  border-radius: 8px;
  transition: transform 0.3s ease;
}

.video-thumbnail:hover {
  transform: scale(1.05);
}

.link_pagevideos {
  text-decoration: none;
  color: inherit;
  font-weight: bold;
}

.link_pagevideos:hover {
  text-decoration: none;
  color: inherit;
}

.category-filter {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin: 20px;
  justify-content: center;
  padding: 15px;
}

.category-filter button {
  padding: 8px 16px;
  border-radius: 20px;
  border: 1px solid #ddd;
  background-color: white;
  color: #333;
  font-size: 0.9rem;
  transition: all 0.3s ease;
  cursor: pointer;
}

.category-filter button:hover {
  background-color: #f8f9fa;
  transform: translateY(-2px);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.category-filter .btn-info {
  background-color: #17a2b8;
  color: white;
  border: none;
}

.category-filter .btn-outline-secondary {
  background-color: transparent;
  border: 1px solid #6c757d;
}

.category-filter .btn-outline-dark {
  border-color: #343a40;
}

.search-box {
  margin: 20px auto;
  position: relative;
  display: flex;
  justify-content: center;
  max-width: 500px;
  padding: 0 20px;
}

.search-box input {
  width: 100%;
  padding: 12px 20px 12px 45px;
  border-radius: 25px;
  border: 1px solid #ddd;
  font-size: 1rem;
  transition: all 0.3s ease;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

.search-box input:focus {
  border-color: #007bff;
  box-shadow: 0 0 0 0.2rem rgba(0, 123, 255, 0.25);
  outline: none;
}

.search-box i.search-icon {
  position: absolute;
  left: 35px;
  top: 50%;
  transform: translateY(-50%);
  color: #6c757d;
}
/* Footer Section */
footer{
  background-color:#3a3f5f;
  position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    z-index: 1000;
}
</style>