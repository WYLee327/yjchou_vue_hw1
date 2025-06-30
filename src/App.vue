<script setup>
import { ref, onMounted } from 'vue';
const bookmarks = ref([]);
const urlInput = ref('');
const titleInput = ref('');
const localStorageName = 'bookmarks';

// 掛載完成後載入資料
onMounted(() => {
  const storage = localStorage.getItem(localStorageName);
  if (storage != null) {
    const result = JSON.parse(storage);
    bookmarks.value = result;
  }
});

const addItem = () => {
  if (urlInput.value.trim() != '') {
    const newItem = {
      id: crypto.randomUUID(),
      url: urlInput.value,
      title: titleInput.value !== '' ? titleInput.value : 'N/A',
    };
    bookmarks.value.unshift(newItem);
    saveToLocal();
    clearInput();
  }
};

const delItem = (itemId) => {
  const updateBookmarks = bookmarks.value.filter(
    (bookmark) => bookmark.id !== itemId
  );
  bookmarks.value = updateBookmarks;
  saveToLocal();
};

const delAll = () => {
  bookmarks.value = [];
  saveToLocal();
};

const clearInput = () => {
  urlInput.value = '';
  titleInput.value = '';
};

const saveToLocal = () => {
  localStorage.setItem(localStorageName, JSON.stringify(bookmarks.value));
};
</script>

<template>
  <div
    style="
      width: 100%;
      display: flex;
      flex-direction: column;
      align-items: center;
    "
  >
    <h1 class="text-3xl" style="margin-top: 30px; margin-bottom: 50px">
      收藏網址⼩⼯具
    </h1>
    <div
      style="
        width: 500px;
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 5px;
      "
    >
      <div class="input-area" style="width: 100%">
        <label for="url-input">網址：</label>
        <input
          v-model="urlInput"
          type="text"
          class="input"
          id="url-input"
          required
        />
      </div>
      <div class="input-area" style="width: 100%">
        <label for="title-input">標題 (選填)：</label>
        <input
          v-model="titleInput"
          type="text"
          class="input"
          id="title-input"
        />
      </div>
      <button @click="addItem" class="btn">新增</button>
    </div>

    <div  style="width: 80%">
    <div
      style="
        width: 100%;
        display: flex;
        justify-content: flex-end;
        margin-top: 50px;
      "
    >
      <button @click="delAll" class="btn btn-error btn-sm">刪除全部</button>
    </div>
    <table id="show-list" style="margin-top: 3px; width: 100%;">
      <thead>
        <tr>
          <th colspan="3">收藏清單</th>
        </tr>
        <tr>
          <th class="url-column">網址</th>
          <th class="title-column">標題</th>
          <th class="del-column">刪除</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="bookmark in bookmarks" :key="bookmark.id">
          <td class="url-column">
            <a
              :href="bookmark.url"
              class="text-blue-600 visited:text-purple-600 ..."
              >{{ bookmark.url }}</a
            >
          </td>
          <td class="title-column">{{ bookmark.title }}</td>
          <td class="del-column">
            <button
              @click="delItem(bookmark.id)"
              class="btn btn-warning btn-sm"
              style="margin: 3px"
            >
              刪除
            </button>
          </td>
        </tr>
      </tbody>
    </table>
    </div>
  </div>
</template>

<style scoped>
.input {
  border: 1px solid black;
}

.input-area {
  display: flex;
  justify-content: space-between;
}

th,
td {
  border: 1px solid black;
  padding-left: 10px;
}


.del-column {
  width: 100px;
  text-align: center;
}
</style>
