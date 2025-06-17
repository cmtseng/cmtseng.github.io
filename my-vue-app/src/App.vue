<template>
  <div id="app">
    <h1>學生資料管理</h1>

    <div class="add-student-section">
      <h2>新增學生</h2>
      <input v-model="newStudentName" placeholder="學生姓名" />
      <input v-model.number="newStudentAge" type="number" placeholder="學生年齡" />
      <button @click="addStudent" :disabled="!newStudentName || !newStudentAge">新增學生</button>
      <p v-if="addError" class="error-message">{{ addError }}</p>
    </div>

    <hr />

    <div class="student-list-section">
      <h2>學生列表</h2>
      <p v-if="students.length === 0" class="no-data-message">目前沒有學生資料。</p>
      <table v-else class="student-table">
        <thead>
          <tr>
            <th>ID</th>
            <th>姓名</th>
            <th>年齡</th>
            <th>操作</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="student in students" :key="student.id">
            <td>{{ student.id }}</td>
            <td>{{ student.name }}</td>
            <td>{{ student.age }}</td>
            <td>
              <button @click="deleteStudent(student.id)" class="delete-button">刪除</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

// 用於儲存學生資料的響應式陣列
const students = ref([]);

// 下一個學生 ID 的計數器
const nextStudentId = ref(1);

// 用於新增學生資料的輸入框綁定
const newStudentName = ref('');
const newStudentAge = ref('');
const addError = ref('');

/**
 * 新增學生資料
 */
const addStudent = () => {
  addError.value = ''; // 清除之前的錯誤訊息

  if (!newStudentName.value.trim()) {
    addError.value = '學生姓名不能為空。';
    return;
  }
  if (!newStudentAge.value || newStudentAge.value <= 0) {
    addError.value = '學生年齡必須是有效的正數。';
    return;
  }

  students.value.push({
    id: nextStudentId.value++,
    name: newStudentName.value.trim(),
    age: newStudentAge.value
  });

  // 清空輸入框
  newStudentName.value = '';
  newStudentAge.value = '';
};

/**
 * 刪除學生資料
 * @param {number} id - 要刪除的學生 ID
 */
const deleteStudent = (id) => {
  if (confirm('確定要刪除這位學生嗎？')) {
    students.value = students.value.filter(student => student.id !== id);
  }
};
</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  max-width: 800px;
  margin-left: auto;
  margin-right: auto;
  padding: 20px;
  border: 1px solid #eee;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

h1, h2 {
  color: #34495e;
}

hr {
  margin: 40px 0;
  border: none;
  border-top: 1px dashed #ccc;
}

/* 新增學生區塊樣式 */
.add-student-section {
  margin-bottom: 30px;
  padding: 20px;
  border: 1px solid #e0e0e0;
  border-radius: 5px;
  background-color: #f9f9f9;
}

.add-student-section input {
  padding: 10px;
  margin-right: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1rem;
}

.add-student-section button {
  padding: 10px 20px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.3s ease;
}

.add-student-section button:hover:not(:disabled) {
  background-color: #368a63;
}

.add-student-section button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

.error-message {
  color: red;
  margin-top: 10px;
  font-size: 0.9em;
}

/* 學生列表區塊樣式 */
.student-list-section {
  margin-top: 30px;
}

.no-data-message {
  color: #777;
  font-style: italic;
  margin-top: 20px;
}

.student-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

.student-table th,
.student-table td {
  border: 1px solid #ddd;
  padding: 12px;
  text-align: left;
}

.student-table th {
  background-color: #f2f2f2;
  font-weight: bold;
}

.student-table tr:nth-child(even) {
  background-color: #f9f9f9;
}

.student-table tr:hover {
  background-color: #eef;
}

.delete-button {
  padding: 8px 15px;
  background-color: #e74c3c;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.9rem;
  transition: background-color 0.3s ease;
}

.delete-button:hover {
  background-color: #c0392b;
}
</style>
