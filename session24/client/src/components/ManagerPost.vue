<template>
  <div>
    <div>
      <input type="text" placeholder="Nhập từ khóa tìm kiếm" />
      <select>
        <option value="gannhat">Gần nhất</option>
        <option value="xanhat">Xa nhất</option>
      </select>
      <button @click="showForm = true">Thêm mới bài viết</button>
    </div>

    <div class="table-container">
      <table>
        <thead>
          <tr>
            <th>ID</th>
            <th>Tiêu đề bài viết</th>
            <th>Hình ảnh</th>
            <th>Ngày viết</th>
            <th>Trạng thái</th>
            <th>Chức năng</th>
          </tr>
        </thead>
        <tbody v-show="!isLoading">
          <tr v-for="post in posts" :key="post.id">
            <td>{{ post.id }}</td>
            <td>{{ post.title }}</td>
            <td><img :src="post.image" alt="image" /></td>
            <td>{{ post.date }}</td>
            <td>{{ post.status ? "Đang xuất bản" : "Ngừng xuất bản" }}</td>
            <td>
              <button @click="deletePost(post.id)">Xóa</button>
              <button @click="updatePost(post.id)">Sửa</button>
              <button @click="blockPost(post.id)">
                {{ post.status ? "Chặn" : "Bỏ chặn" }}
              </button>
            </td>
          </tr>
        </tbody>
      </table>
      <div v-if="isLoading" class="loading">Đang tải dữ liệu...</div>
    </div>

    <NewPost v-if="showForm" @closeForm="closeForm" @refreshData="getPost" />
  </div>
</template>
  
  <script setup>
import axios from "axios";
import { ref } from "vue";
import NewPost from "./NewPost.vue";

const posts = ref([]);
const isLoading = ref(false);
const showForm = ref(false);

const closeForm = () => {
  showForm.value = false; 
};

const getPost = async () => {
  isLoading.value = true;
  posts.value = (await axios.get("http://localhost:3000/posts")).data;
  setTimeout(() => {
    isLoading.value = false;
  }, 500);
};

getPost();

const blockPost = async (id) => {
  if (confirm("Bạn có chắc chắn muốn chặn bài viết này?")) {
    const postToUpdate = posts.value.find((post) => post.id === id);
    const updatedStatus = !postToUpdate.status;
    await axios.put(`http://localhost:3000/posts/${id}`, {
      ...postToUpdate,
      status: updatedStatus,
    });
    getPost();
  }
};
</script>
  
  <style>
.table-container {
  width: 100%;
}

table {
  width: 100%;
  border-collapse: collapse;
}

tbody {
  display: block;
  max-height: 230px;
  overflow-y: auto;
}

tr {
  display: table;
  table-layout: fixed;
  width: 100%;
}

th,
td {
  padding: 10px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

img {
  width: 50px;
  height: 50px;
  object-fit: cover;
}

.loading {
  position: fixed;
  top: 50%;
  left: 50%;
  text-align: center;
  padding: 20px;
  color: #007bff;
  font-weight: bold;
}
</style>
  