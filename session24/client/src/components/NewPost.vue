<template>
  <div class="new-post-container">
    <h1>Thêm mới bài viết</h1>
    <div>
      <label for="title">Tên bài viết</label>
      <input id="title" type="text" v-model="newPost.title" />

      <label for="content">Nội dung</label>
      <textarea id="content" v-model="newPost.content"></textarea>

      <label for="image">Hình ảnh</label>
      <input id="image" type="text" v-model="newPost.image" />

      <button @click="submitPost">Xuất bản</button>
      <button @click="emitCloseForm">Đóng</button>
    </div>
  </div>
</template>
  
  <script setup>
import { ref } from "vue";
import axios from "axios";

const emit = defineEmits(["closeForm", "refreshData"]);

const newPost = ref({
  title: "",
  content: "",
  image: "",
  date: ""
});

const emitCloseForm = () => {
  emit("closeForm");
};

const submitPost = async () => {
  if (!newPost.value.title || !newPost.value.content || !newPost.value.image) {
    alert("Vui lòng điền đầy đủ thông tin.");
    return;
  }

  const existingPosts = (await axios.get("http://localhost:3000/posts")).data;
  if (existingPosts.some((post) => post.title === newPost.value.title)) {
    alert("Tên bài viết đã tồn tại.");
    return;
  }

  await axios.post("http://localhost:3000/posts", newPost.value);

  emitCloseForm();
  emit("refreshData");
};
</script>
  
  <style scoped>
.new-post-container {
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  padding: 20px;
  max-width: 600px;
  margin: 20px auto;
  font-family: "Arial", sans-serif;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}

h1 {
  font-size: 24px;
  color: #333;
  text-align: center;
  margin-bottom: 20px;
}

label {
  display: block;
  font-size: 16px;
  font-weight: bold;
  color: #555;
  margin-bottom: 8px;
}

input[type="text"],
textarea {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 14px;
  color: #333;
  margin-bottom: 15px;
  box-sizing: border-box;
}

input[type="text"]:focus,
textarea:focus {
  border-color: #007bff;
  outline: none;
  box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
}

button {
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 4px;
  padding: 10px 15px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  margin-right: 10px;
}

button:hover {
  background-color: #0056b3;
}

button:active {
  background-color: #004494;
}

button:focus {
  outline: none;
  box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
}
</style>
  