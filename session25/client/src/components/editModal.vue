<template>
  <div class="edit-modal">
    <h1>Sửa công việc</h1>
    <input
      type="text"
      v-model="editValue"
      placeholder="Nhập tên mới của công việc"
    />
    <div>
      <button @click="updateTask">Cập nhật</button>
      <button @click="closeModal">Hủy</button>
    </div>
  </div>
</template>

<script setup>
import { ref, defineEmits } from "vue";

const editValue = ref("");

const emit = defineEmits(["updateTaskName", "closeModal"]);

const updateTask = () => {
  if (editValue.value.trim()) {
    emit("updateTaskName", editValue.value);
  } else {
    alert("Vui lòng nhập tên công việc!");
  }
};

const closeModal = () => {
  emit("closeModal");
  editValue.value = "";
};

const openModal = (taskName) => {
  editValue.value = taskName;
};
</script>

<style>
.edit-modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 400px;
  max-width: 90%;
  background-color: white;
  padding: 25px;
  border-radius: 12px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
  z-index: 1000;
  transition: all 0.3s ease-in-out;
  animation: fadeIn 0.3s ease;
}

.edit-modal h1 {
  font-size: 22px;
  margin-bottom: 15px;
  text-align: center;
  color: #333;
}

.edit-modal input {
  width: 100%;
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 16px;
  margin-bottom: 15px;
  transition: border-color 0.3s ease;
}

.edit-modal input:focus {
  border-color: #007bff;
  outline: none;
}

.edit-modal button {
  width: 48%;
  padding: 12px;
  font-size: 16px;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.edit-modal button:first-child {
  background-color: #007bff;
}

.edit-modal button:first-child:hover {
  background-color: #0056b3;
}

.edit-modal button:last-child {
  background-color: #dc3545;
}

.edit-modal button:last-child:hover {
  background-color: #c82333;
}

.edit-modal div {
  display: flex;
  justify-content: space-between;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translate(-50%, -55%);
  }
  to {
    opacity: 1;
    transform: translate(-50%, -50%);
  }
}

@media screen and (max-width: 480px) {
  .edit-modal {
    width: 90%;
    padding: 15px;
  }

  .edit-modal h1 {
    font-size: 20px;
  }

  .edit-modal button {
    width: 100%;
    margin-bottom: 10px;
  }

  .edit-modal div {
    flex-direction: column;
  }
}
</style>
