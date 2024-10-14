<template>
  <div class="modal">
    <h1>Quản lí công việc</h1>
    <div class="add-task">
      <input
        type="text"
        placeholder="Nhập tên công việc"
        v-model="inputValue"
      />
      <button @click="addTask">Thêm</button>
    </div>
    <div class="nav">
      <button
        @click="
          getTaskValue = '';
          getAlltask();
        "
      >
        Tất cả
      </button>
      <button
        @click="
          getTaskValue = 'complete';
          getAlltask();
        "
      >
        Hoàn thành
      </button>
      <button
        @click="
          getTaskValue = 'unfinished';
          getAlltask();
        "
      >
        Đang thực hiện
      </button>
    </div>

    <div class="task-list">
      <ul>
        <li v-for="task in tasks" :key="task.id">
          <input
            type="checkbox"
            v-model="task.status"
            @change="updateTaskStatus(task.id, task.status)"
          />
          <p :class="{ 'completed-task': task.status }">{{ task.name }}</p>
          <button @click="deleteTask(task.id)">Xóa</button>
          <button @click="openEditModal(task)">Sửa</button>
        </li>
      </ul>
    </div>

    <div class="nav-delete">
      <button @click="deleteTaskComplete">Xóa công việc hoàn thành</button>
      <button @click="deleteAll">Xóa tất cả công việc</button>
    </div>

    <editModal
      v-show="isEditing"
      @updateTaskName="updateTaskName"
      @closeModal="isEditing = false"
    />
  </div>
</template>

<script setup>
import axios from "axios";
import { ref, onMounted } from "vue";
import editModal from "./editModal.vue";

const inputValue = ref("");
const getTaskValue = ref("");
const tasks = ref([]);
const isEditing = ref(false);
const currentTaskId = ref(null);

const getAlltask = async () => {
  if (getTaskValue.value === "unfinished") {
    tasks.value = (
      await axios.get("http://localhost:3000/task?status=false")
    ).data;
  } else if (getTaskValue.value === "complete") {
    tasks.value = (
      await axios.get("http://localhost:3000/task?status=true")
    ).data;
  } else {
    tasks.value = (await axios.get("http://localhost:3000/task")).data;
  }
};

onMounted(() => {
  getAlltask();
});

const addTask = async () => {
  if (inputValue.value.trim() === "") {
    alert("Vui lòng nhập tên công việc");
    return;
  }
  await axios.post("http://localhost:3000/task", {
    name: inputValue.value,
    status: false,
  });
  inputValue.value = "";
  getAlltask();
};

const deleteTask = async (id) => {
  if (confirm("Bạn có chắc chắn muốn xóa không?")) {
    await axios.delete(`http://localhost:3000/task/${id}`);
    getAlltask();
  }
};

const updateTaskStatus = async (id, newStatus) => {
  try {
    await axios.patch(`http://localhost:3000/task/${id}`, {
      status: newStatus,
    });
    getAlltask();
  } catch (error) {
    console.error("Error updating task status:", error);
  }
};

const deleteAll = async () => {
  if (confirm("Bạn có chắc chắn muốn xóa tất cả các công việc?")) {
    const allTasks = (await axios.get("http://localhost:3000/task")).data;
    const deletePromises = allTasks.map((task) =>
      axios.delete(`http://localhost:3000/task/${task.id}`)
    );
    await Promise.all(deletePromises);
    getAlltask();
  }
};

const deleteTaskComplete = async () => {
  if (confirm("Bạn có chắc chắn muốn xóa các công việc đã hoàn thành?")) {
    const completedTasks = (
      await axios.get("http://localhost:3000/task", {
        params: { status: true },
      })
    ).data;
    const deletePromises = completedTasks.map((task) =>
      axios.delete(`http://localhost:3000/task/${task.id}`)
    );
    await Promise.all(deletePromises);
    getAlltask();
  }
};

const openEditModal = (task) => {
  currentTaskId.value = task.id;
  isEditing.value = true;
  console.log(isEditing.value);
};

const updateTaskName = async (newName) => {
  try {
    await axios.patch(`http://localhost:3000/task/${currentTaskId.value}`, {
      name: newName,
    });
    getAlltask();
    isEditing.value = false;
  } catch (error) {
    console.error("Error updating task name:", error);
  }
};
</script>

<style>
.modal {
  width: 100%;
  max-width: 400px;
  height: 600px;
  background-color: #fff;
  margin: 50px auto;
  padding: 20px;
  border-radius: 12px;
  position: relative;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  overflow-y: auto;
}

h1 {
  text-align: center;
  font-size: 24px;
  color: #333;
  margin-bottom: 20px;
}

.add-task {
  margin-top: 10px;
  display: flex;
  gap: 10px;
  align-items: center;
  flex-direction: column;
}

.add-task input {
  flex-grow: 1;
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 6px;
  font-size: 16px;
}

.add-task button {
  width: 100%;
  padding: 10px 15px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s ease;
}

.add-task button:hover {
  background-color: #0056b3;
}

.nav {
  display: flex;
  justify-content: space-between;
}

button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 8px 20px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
  margin: 5px;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #0056b3;
}

.task-list {
  max-height: 280px;
  overflow-y: auto;
  margin-top: 20px;
}

ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

li {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background-color: #f9f9f9;
  margin-bottom: 10px;
  padding: 10px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

li p {
  margin: 0;
  font-size: 16px;
  flex-grow: 1;
  padding: 0 10px;
  color: #333;
}

li input[type="checkbox"] {
  margin-right: 10px;
}

li button {
  background-color: #dc3545;
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s ease;
}

li button:hover {
  background-color: #c82333;
}

li button:nth-child(3) {
  background-color: #ffc107;
  color: white;
}

li button:nth-child(3):hover {
  background-color: #e0a800;
}

.nav-delete {
  display: flex;
  justify-content: space-between;
  position: absolute;
  bottom: 10px;
}

.completed-task {
  text-decoration: line-through;
}

@media screen and (max-width: 600px) {
  .modal {
    width: 90%;
    padding: 15px;
  }

  h1 {
    font-size: 20px;
  }

  .add-task {
    flex-direction: column;
  }

  .add-task input {
    width: 100%;
  }

  .add-task button {
    width: 100%;
  }

  li {
    flex-direction: column;
    align-items: flex-start;
  }

  li button {
    width: 100%;
    margin-top: 5px;
  }

  li p {
    width: 100%;
    padding: 5px 0;
  }
}
</style>
