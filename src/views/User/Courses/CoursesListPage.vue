<template>
  <div>
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
                <router-link to="/course" class="nav-link">Bài giảng</router-link>
              </li>
              <li class="nav-item">
                <router-link to="/courselist" class="nav-link active">Khóa học</router-link>
              </li>
              <li class="nav-item">
                <router-link to="/contact" class="nav-link">Liên hệ</router-link>
              </li>
          </ul>
        </div>
      </div>
    </nav>
    <!-- Danh sách khóa học -->
    <div class="container mt-4">
      <h2 class="text-center">Danh Sách Khóa Học</h2>
      <div v-if="courses.length > 0" class="row">
        <div v-for="course in courses" :key="course.id" class="col-md-4 mb-4">
          <div class="card">
            <img v-if="course.thumbnail" :src="getThumbnailUrl(course.thumbnail)" class="card-img-top" alt="Hình ảnh khóa học">
            <div class="card-body">
              <h5 class="card-title">{{ course.name }}</h5>
              <p class="card-text">{{ course.description }}</p>
              <router-link to="/course" class="btn btn-primary">Xem chi tiết</router-link>
            </div>
          </div>
        </div>
      </div>
      <p v-else class="text-center">Không có khóa học nào.</p>
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
import axios from 'axios';

export default {
    name: 'CoursesListPage',
    data() {
        return {
            courses: [] // Danh sách khóa học
        };
    },
    methods: {
        async fetchCourses() {
            try {
                const token = sessionStorage.getItem('token');
                const response = await axios.get(`${import.meta.env.VITE_API_URL}/api/subjects`, {
                    headers: { 'Authorization': `Bearer ${token}` }
                }); // Gọi API từ backend
                this.courses = response.data;
            } catch (error) {
                console.error("Lỗi khi tải danh sách khóa học:", error);
            }
        },
        getThumbnailUrl(path) {
          if (!path) return null;
          if (path.startsWith('http')) {
              // Add timestamp to force cache refresh
              return `${path}?v=${new Date().getTime()}`;
          }
          return `${import.meta.env.VITE_API_URL}/storage/${path}?v=${new Date().getTime()}`;
      },


    },
    
        mounted() {
            this.fetchCourses(); // Gọi hàm khi trang được tải
        }
    }
</script>

<style>
    /* Logo */
.navbar-logo {
  height: 100px; /* Adjust the height as needed */
  width: auto;
}
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
/*Nav item */
  .nav-item{
    margin-right: 15px;
    font-size: 19px;
  }
  .nav-item:hover{
    text-transform: uppercase;
  }
 .nav-item::after {
     content: '';
     display: block;
     width: 0px;
     height: 2px;
     background: #3883e6;
     transition: 0.4s
 }

 .nav-item:hover::after {
     width: 100%
 }

 .navbar-dark .navbar-nav .active>.nav-link,
 .navbar-dark .navbar-nav .nav-link.active,
 .navbar-dark .navbar-nav .nav-link.show,
 .navbar-dark .navbar-nav .show>.nav-link,
 .navbar-dark .navbar-nav .nav-link:focus,
 .navbar-dark .navbar-nav .nav-link:hover {
     color: #324ee9
 }

 .nav-link {
     padding: 5px 5px;
     transition: 0.2s
 }
 /* Cards */
 .card {
  border-radius: 10px;
  overflow: hidden;
  display: flex;
  flex-direction: column; /* Đảm bảo nội dung không bị lệch */
  height: 100%; /* Đồng bộ chiều cao giữa các card */
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease-in-out, box-shadow 0.3s ease-in-out;
}

.card img {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: 10px 10px 0 0;
}

.card-content {
  flex: 1; /* Đảm bảo nội dung mở rộng và đồng bộ chiều cao */
  display: flex;
  flex-direction: column;
  padding: 15px;
}

.card-title {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 10px;
}

.card-text {
  flex: 1; /* Đẩy nút xuống dưới cùng */
  font-size: 14px;
  color: #555;
  max-height: 3.6em; /* Giới hạn hiển thị 2 dòng */
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}

.card-footer {
  margin-top: auto;
  padding-top: 10px;
}

/* Hiệu ứng hover */
.card:hover {
  transform: scale(1.02);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
}

/* Hiệu ứng hover khi rê chuột */
.card img:hover {
  transform: scale(1.05); /* Phóng to nhẹ khi hover */
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2); /* Thêm bóng đổ */
}



/* Responsive cho màn hình nhỏ (tablet, mobile) */
@media (max-width: 1024px) {
  .card img {
    height: 180px; /* Giảm chiều cao trên tablet */
  }
}

@media (max-width: 768px) {
  .card img {
    height: 160px; /* Giảm chiều cao trên mobile */
  }
}

@media (max-width: 480px) {
  .card img {
    height: auto;
    max-height: 150px;
  }
  .card-title {
    font-size: 16px;
  }
  .card-description {
    max-height: 2.7em; /* Giới hạn còn 2 dòng trên màn hình nhỏ */
  }
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
