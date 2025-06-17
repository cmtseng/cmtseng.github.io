<template>
  <div id="app-container">
    <h1>學生資料列表</h1>

    <div class="controls">
      <button @click="addStudent">新增學生</button>
    </div>

    <div class="student-grid-container">
      <div class="grid-header">
        <div>學號</div>
        <div>姓名</div>
        <div>年齡</div>
        <div>操作</div>
      </div>
      <div v-if="students.length === 0" class="no-data">
        目前沒有學生資料。
      </div>
      <div v-else class="grid-row" v-for="student in students" :key="student.id">
        <div>{{ student.id }}</div>
        <div>{{ student.name }}</div>
        <div>{{ student.age }}</div>
        <div>
          <button @click="deleteStudent(student.id)" class="delete-btn">刪除</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

// 使用 ref 創建響應式數據，用於儲存學生列表
const students = ref([
  { id: 1, name: '張小明', age: 18 },
  { id: 2, name: '陳美麗', age: 19 },
  { id: 3, name: '王大華', age: 17 },
]);

// 用於生成新學生 ID 的變數
let nextId = students.value.length > 0 ? Math.max(...students.value.map(s => s.id)) + 1 : 1;

/**
 * 新增學生資料
 */
const addStudent = () => {
  const newStudent = {
    id: nextId++,
    name: `新學生${nextId - 1}`, // 簡單的預設名稱
    age: Math.floor(Math.random() * 5) + 16, // 隨機生成16-20歲的年齡
  };
  students.value.push(newStudent);
};

/**
 * 刪除學生資料
 * @param {number} studentId - 要刪除的學生 ID
 */
const deleteStudent = (studentId) => {
  if (confirm(`確定要刪除學號 ${studentId} 的學生資料嗎？`)) {
    students.value = students.value.filter(student => student.id !== studentId);
  }
};
</script>

<style scoped>
#app-container {
  font-family: Arial, sans-serif;
  max-width: 800px;
  margin: 40px auto;
  padding: 20px;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
  background-color: #fff;
}

h1 {
  text-align: center;
  color: #333;
  margin-bottom: 30px;
}

.controls {
  text-align: right;
  margin-bottom: 20px;
}

button {
  padding: 10px 20px;
  font-size: 16px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  background-color: #4CAF50; /* Add button green */
  color: white;
}

button:hover {
  background-color: #45a049;
}

.delete-btn {
  background-color: #f44336; /* Delete button red */
}

.delete-btn:hover {
  background-color: #da190b;
}

.student-grid-container {
  border: 1px solid #ccc;
  border-radius: 5px;
  overflow: hidden;
}

.grid-header,
.grid-row {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr 1.5fr; /* 定義四個欄位寬度 */
  padding: 12px 15px;
  border-bottom: 1px solid #eee;
  align-items: center;
}

.grid-header {
  background-color: #f2f2f2;
  font-weight: bold;
  color: #555;
  border-bottom: 2px solid #ccc;
}

.grid-row:nth-child(even) {
  background-color: #f9f9f9;
}

.grid-row:last-child {
  border-bottom: none;
}

.grid-row > div {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.no-data {
  text-align: center;
  padding: 30px;
  color: #777;
  font-style: italic;
  background-color: #fdfdfd;
}
</style>

