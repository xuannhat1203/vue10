<template>
  <div>
    <div class="w-[80%] m-auto mt-4 h-[100vh]">
      <main class="main">
        <header class="d-flex justify-content-between mb-3">
          <h3>Nhân viên</h3>
          <button @click="handleOpenAdd" class="btn btn-primary">Thêm mới nhân viên</button>
        </header>
        <div class="d-flex align-items-center justify-content-end gap-2 mb-3">
          <input
            style="width: 350px"
            type="text"
            class="form-control"
            placeholder="Tìm kiếm theo email"
          />
          <i class="fa-solid fa-arrows-rotate" title="Refresh"></i>
        </div>
        <!-- Danh sách nhân viên -->
        <table class="table table-bordered table-hover table-striped">
          <thead>
            <tr>
              <th>STT</th>
              <th>Họ và tên</th>
              <th>Ngày sinh</th>
              <th>Email</th>
              <th>Địa chỉ</th>
              <th>Trạng thái</th>
              <th colspan="2">Chức năng</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(user, index) in listUser" :key="index">
              <td>{{ index + 1 }}</td>
              <td>{{ user.name }}</td>
              <td>{{ user.dateOfBirth }}</td>
              <td>{{ user.email }}</td>
              <td>{{ user.address }}</td>
              <td>
                <div style="display: flex; align-items: center; gap: 8px">
                  <div :class="['status', user.active ? 'status-active' : 'status-stop']"></div>
                  <span> {{ user.active ? 'Đang hoạt động' : 'Ngừng hoạt động' }}</span>
                </div>
              </td>
              <td>
                <span @click="toggleBlockUser(user)" class="button button-block">{{ user.active ? 'Chặn' : 'Bỏ chặn' }}</span>
              </td>
              <td>
                <span class="button button-edit">Sửa</span>
              </td>
              <td  @click="handleDelete(user.id)"><span class="button button-delete">Xóa</span></td>
            </tr>
          </tbody>
        </table>
        <footer class="d-flex justify-content-end align-items-center gap-3">
          <select class="form-select">
            <option selected>Hiển thị 10 bản ghi trên trang</option>
            <option>Hiển thị 20 bản ghi trên trang</option>
            <option>Hiển thị 50 bản ghi trên trang</option>
            <option>Hiển thị 100 bản ghi trên trang</option>
          </select>
          <ul class="pagination">
            <li class="page-item">
              <a class="page-link" href="#">Previous</a>
            </li>
            <li class="page-item"><a class="page-link" href="#">1</a></li>
            <li class="page-item"><a class="page-link" href="#">2</a></li>
            <li class="page-item"><a class="page-link" href="#">3</a></li>
            <li class="page-item">
              <a class="page-link" href="#">Next</a>
            </li>
          </ul>
        </footer>
      </main>
    </div>
    <!-- Form thêm mới nhân viên -->
    <div v-if="openModelAdd" class="overlay">
      <form class="form" @submit.prevent="handleAddUser">
        <div class="d-flex justify-content-between align-items-center">
          <h4>Thêm mới nhân viên</h4>
          <i @click="handleCloseAdd" class="fa-solid fa-xmark"></i>
        </div>
        <div>
          <label class="form-label" for="userName">Họ và tên</label>
          <input v-model="newUser.name" id="userName" type="text" class="form-control" required />
        </div>
        <div>
          <label class="form-label" for="dateOfBirth">Ngày sinh</label>
          <input v-model="newUser.dateOfBirth" id="dateOfBirth" type="date" class="form-control" required />
        </div>
        <div>
          <label class="form-label" for="email">Email</label>
          <input v-model="newUser.email" id="email" type="email" class="form-control" required />
        </div>
        <div>
          <label class="form-label" for="address">Địa chỉ</label>
          <textarea v-model="newUser.address" class="form-control" id="address" rows="3" required></textarea>
        </div>
        <div>
          <button type="submit" class="w-100 btn btn-primary">Thêm mới</button>
        </div>
      </form>
    </div>
    <!-- xóa -->
    <div v-if="openModelDelete" class="overlay">
      <div class="modal-custom">
        <div class="modal-title">
          <h4>Cảnh báo</h4>
          <i  @click="cancelDelete" class="fa-solid fa-xmark"></i>
        </div>
        <div class="modal-body-custom">
          <span>Bạn có chắc chắn muốn xóa tài khoản này?</span>
        </div>
        <div class="modal-footer-custom">
          <button @click="cancelDelete" class="btn btn-light">Hủy</button>
          <button @click="comfirmDelete" class="btn btn-danger">Xác nhận</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const openModelAdd = ref(false);
const openModelDelete = ref(false);
const idUser = ref(null);
const handleDelete = (id) => {
  openModelDelete.value = true;
  idUser.value = id;
}
const cancelDelete = () => {
  openModelDelete.value = false;

}
const listUser = ref(JSON.parse(localStorage.getItem("listUsers")) || []);
const newUser = ref({
  name: "",
  dateOfBirth: "",
  email: "",
  address: "",
});
const comfirmDelete = () => {
  const find = listUser.value.filter((user) => user.id !== idUser.value);
  localStorage.setItem("listUsers", JSON.stringify(find));
  listUser.value = find; 
  openModelDelete.value = false;
};

const handleOpenAdd = () => {
  openModelAdd.value = true;
};

const handleCloseAdd = () => {
  openModelAdd.value = false;
  resetNewUser(); 
};

const handleAddUser = () => {
  const userExists = listUser.value.find(user => user.email === newUser.value.email);
  if (!userExists) {
    const user = {
      id: listUser.value.length + 1,
      ...newUser.value,
      active: true, 
    };
    listUser.value.push(user);
    localStorage.setItem("listUsers", JSON.stringify(listUser.value));
    handleCloseAdd(); 
  } else {
    alert("Email đã tồn tại.");
  }
};

const resetNewUser = () => {
  newUser.value = {
    name: "",
    dateOfBirth: "",
    email: "",
    address: "",
  };
};
const toggleBlockUser = (user) => {
  user.active = !user.active;
  localStorage.setItem("listUsers", JSON.stringify(listUser.value));
};
</script>

<style>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

body {
  font-size: 14px;
  font-family: sans-serif;
}

.main {
  /* height: 100vh; */
  margin: 50px 100px;
}

.fa-arrows-rotate {
  font-size: 20px;
  cursor: pointer;
  color: gray;
}

.fa-arrows-rotate:hover {
  color: rgb(184, 180, 180);
  transform: rotate(20deg);
  transition: all 0.5s linear;
}

.button {
  padding: 4px 12px;
  line-height: 30px;
  border-radius: 4px;
  color: #fff;
  cursor: pointer;
}

.button-edit {
  background-color: orange;
}

.button-delete {
  background-color: red;
}

.button-block {
  background-color: green;
}

td:first-child,
td:nth-child(6),
td:nth-child(7) {
  text-align: center;
}

.form-select {
  width: 270px !important;
}

.overlay {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}

.form {
  background-color: #fff;
  width: 500px;
  padding: 20px 24px;
  border-radius: 4px;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.form-label {
  font-weight: 600;
  margin-bottom: 8px;
}

.fa-xmark {
  font-size: 20px;
  cursor: pointer;
}

.error {
  color: red !important;
}

.status-container {
  border: 1px solid #dadada;
  padding: 4px 8px;
  border-radius: 4px;
}

.status {
  height: 10px;
  width: 10px;
  border-radius: 50%;
  border: 1px solid transparent;
}

.status-active {
  background-color: green;
}

.status-stop {
  background-color: red;
}

.modal-custom {
  background-color: #fff;
  padding: 20px 24px;
  border-radius: 4px;
  width: 400px;
}

.modal-title {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.modal-body-custom {
  padding: 20px 0;
}

.modal-footer-custom {
  display: flex;
  justify-content: end;
  gap: 8px;
}

.pagination {
  margin-bottom: 0;
}
</style>
