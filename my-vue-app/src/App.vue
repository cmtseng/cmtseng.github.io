<template>
  <div id="app">
    <h1>學生資料管理系統</h1>

    <!-- 新增學生區塊 -->
    <div class="add-student-section">
      <h2>新增學生</h2>
      <div class="input-group">
        <input v-model="newStudentName" placeholder="學生姓名" aria-label="學生姓名" />
        <input v-model.number="newStudentAge" type="number" placeholder="學生年齡" aria-label="學生年齡" min="1" />
        <button @click="addStudent" :disabled="!newStudentName || !newStudentAge">新增</button>
      </div>
      <p v-if="addError" class="error-message" role="alert">{{ addError }}</p>
    </div>

    <hr />

    <!-- 學生列表區塊 (Grid) -->
    <div class="student-list-section">
      <h2>學生列表</h2>
      <p v-if="students.length === 0" class="no-data-message">目前沒有學生資料。</p>
      <table v-else class="student-table" aria-live="polite">
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
              <button @click="confirmDelete(student.id)" class="delete-button" aria-label="刪除學生">刪除</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- 自定義確認對話框 -->
    <div v-if="showConfirmDialog" class="confirm-dialog-overlay" role="dialog" aria-modal="true" aria-labelledby="confirm-dialog-title">
      <div class="confirm-dialog-content">
        <h3 id="confirm-dialog-title">確認刪除</h3>
        <p>確定要刪除這位學生嗎？</p>
        <div class="dialog-buttons">
          <button @click="deleteConfirmed" class="dialog-confirm-button">確定</button>
          <button @click="cancelDelete" class="dialog-cancel-button">取消</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

// 用於儲存學生資料的響應式陣列
const students = ref([]);

// 下一個學生 ID 的計數器，確保 ID 唯一
const nextStudentId = ref(1);

// 用於新增學生資料的輸入框綁定
const newStudentName = ref('');
const newStudentAge = ref('');
// 用於顯示新增學生時的錯誤訊息
const addError = ref('');

// 自定義確認對話框的狀態
const showConfirmDialog = ref(false);
const studentIdToDelete = ref(null); // 儲存要刪除的學生 ID

/**
 * 新增學生資料
 * 執行輸入驗證並將新學生添加到列表中
 */
const addStudent = () => {
  addError.value = ''; // 清除之前的錯誤訊息

  // 驗證學生姓名不能為空或只包含空白字元
  if (!newStudentName.value.trim()) {
    addError.value = '學生姓名不能為空。';
    return;
  }
  // 驗證學生年齡必須是有效的正數
  if (!newStudentAge.value || newStudentAge.value <= 0 || isNaN(newStudentAge.value)) {
    addError.value = '學生年齡必須是有效的正數。';
    return;
  }

  // 將新學生添加到 students 陣列中
  students.value.push({
    id: nextStudentId.value++, // 使用並遞增 ID
    name: newStudentName.value.trim(),
    age: newStudentAge.value
  });

  // 清空輸入框以便下一次新增
  newStudentName.value = '';
  newStudentAge.value = '';
};

/**
 * 顯示自定義確認對話框
 * @param {number} id - 要刪除的學生 ID
 */
const confirmDelete = (id) => {
  studentIdToDelete.value = id;
  showConfirmDialog.value = true;
};

/**
 * 確認刪除操作並執行刪除
 */
const deleteConfirmed = () => {
  if (studentIdToDelete.value !== null) {
    students.value = students.value.filter(student => student.id !== studentIdToDelete.value);
    studentIdToDelete.value = null; // 清除待刪除 ID
  }
  showConfirmDialog.value = false; // 關閉對話框
};

/**
 * 取消刪除操作
 */
const cancelDelete = () => {
  studentIdToDelete.value = null; // 清除待刪除 ID
  showConfirmDialog.value = false; // 關閉對話框
};
</script>

<style scoped>
/* 頁面整體佈局和基本樣式 */
#app {
  font-family: 'Inter', Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  max-width: 800px;
  margin-left: auto;
  margin-right: auto;
  padding: 30px;
  background-color: #ffffff;
  border-radius: 12px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
}

h1, h2 {
  color: #34495e;
  margin-bottom: 20px;
}

hr {
  margin: 40px 0;
  border: none;
  border-top: 2px dashed #e0e0e0;
}

/* 新增學生區塊樣式 */
.add-student-section {
  margin-bottom: 40px;
  padding: 25px;
  border: 1px solid #e0e0e0;
  border-radius: 10px;
  background-color: #fcfcfc;
  box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.05);
}

.input-group {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 15px; /* 輸入框之間的間距 */
  flex-wrap: wrap; /* 讓輸入框在小螢幕上換行 */
}

.add-student-section input {
  padding: 12px 15px;
  border: 1px solid #c8d6e5;
  border-radius: 8px;
  font-size: 1.05rem;
  width: calc(50% - 25px); /* 讓兩個輸入框並排 */
  max-width: 200px; /* 控制最大寬度 */
  box-sizing: border-box; /* 包含 padding 和 border 在寬度內 */
}

/* 按鈕基本樣式 */
button {
  padding: 12px 25px;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1.05rem;
  font-weight: 600;
  transition: all 0.3s ease;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

/* 新增按鈕特有樣式 */
.add-student-section button {
  background-color: #42b983; /* Vue 綠 */
}

.add-student-section button:hover:not(:disabled) {
  background-color: #368a63;
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
}

.add-student-section button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
  box-shadow: none;
}

.error-message {
  color: #e74c3c; /* 紅色錯誤訊息 */
  margin-top: 15px;
  font-size: 0.95em;
  font-weight: 500;
}

/* 學生列表區塊樣式 */
.student-list-section {
  margin-top: 40px;
}

.no-data-message {
  color: #777;
  font-style: italic;
  margin-top: 25px;
  font-size: 1.1em;
}

.student-table {
  width: 100%;
  border-collapse: separate; /* 使用 separate 讓 border-radius 生效 */
  border-spacing: 0;
  margin-top: 25px;
  border-radius: 10px; /* 整個表格圓角 */
  overflow: hidden; /* 確保內容不會溢出圓角 */
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.08);
}

.student-table th,
.student-table td {
  border: 1px solid #e0e0e0;
  padding: 15px;
  text-align: left;
}

.student-table th {
  background-color: #f5f5f5;
  font-weight: bold;
  color: #555;
  text-transform: uppercase;
  font-size: 0.9em;
}

.student-table tbody tr:nth-child(even) {
  background-color: #fdfdfd;
}

.student-table tbody tr:hover {
  background-color: #eef7ff;
  transform: scale(1.01);
  transition: all 0.2s ease-in-out;
}

/* 刪除按鈕特有樣式 */
.delete-button {
  background-color: #e74c3c; /* 紅色 */
  padding: 8px 18px;
  font-size: 0.9rem;
}

.delete-button:hover {
  background-color: #c0392b;
  transform: translateY(-1px);
  box-shadow: 0 3px 6px rgba(0, 0, 0, 0.12);
}

/* 自定義確認對話框樣式 */
.confirm-dialog-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.confirm-dialog-content {
  background-color: white;
  padding: 30px;
  border-radius: 12px;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
  text-align: center;
  max-width: 400px;
  width: 90%;
}

.confirm-dialog-content h3 {
  color: #34495e;
  margin-bottom: 20px;
  font-size: 1.5em;
}

.confirm-dialog-content p {
  margin-bottom: 30px;
  font-size: 1.1em;
  color: #555;
}

.dialog-buttons {
  display: flex;
  justify-content: center;
  gap: 20px;
}

.dialog-confirm-button {
  background-color: #e74c3c;
}

.dialog-cancel-button {
  background-color: #95a5a6; /* 灰色 */
}

.dialog-confirm-button:hover {
  background-color: #c0392b;
}

.dialog-cancel-button:hover {
  background-color: #7f8c8d;
}

/* 響應式調整 */
@media (max-width: 600px) {
  .input-group {
    flex-direction: column;
    gap: 10px;
  }

  .add-student-section input {
    width: 100%; /* 小螢幕上輸入框佔滿寬度 */
    max-width: none;
  }

  .student-table, .add-student-section, .confirm-dialog-content {
    padding: 20px;
  }
  
  .student-table th, .student-table td {
    padding: 10px;
  }
}

