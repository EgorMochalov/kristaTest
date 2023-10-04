<template>
  <div class="table-item table-head">
    <div>Название</div>
    <div>Описание</div>
    <div>Опубликована</div>
    <div></div>
  </div>
  <form v-on:submit="addItem" class="table-item add-item">
    <div>
      <input name="title" required v-model="newItem.title" placeholder="Введите название">
    </div>
    <div>
      <textarea required v-model="newItem.description" placeholder="Введите описание"></textarea>
    </div>
    <div>
      <input v-model="newItem.published" type="checkbox">
    </div>
    <div>
      <button type="submit" class="add-button">Добавить</button>
    </div>
  </form>
  <ul class="table">
    <li class="table-item" v-for="book in data" :key="book.id">
      <div v-if="book.edit">
        <input v-model="book.title">
      </div>
      <div v-if="book.edit">
        <input v-model="book.description">
      </div>
      <div v-if="book.edit">
        <input type="checkbox" v-model="book.published">
      </div>
      <div v-if="!book.edit">{{ book.title }}</div>
      <div v-if="!book.edit">{{ book.description }}</div>
      <div v-if="!book.edit">{{ book.published == false ? "Нет" : "Да" }}</div>
      <div class="controls">
        <button v-if="book.edit" v-on:click="() => { saveRow(book) }"><svg xmlns="http://www.w3.org/2000/svg" width="20px"
            height="20px" viewBox="0 0 24 24" fill="none">
            <path fill-rule="evenodd" clip-rule="evenodd"
              d="M18.1716 1C18.702 1 19.2107 1.21071 19.5858 1.58579L22.4142 4.41421C22.7893 4.78929 23 5.29799 23 5.82843V20C23 21.6569 21.6569 23 20 23H4C2.34315 23 1 21.6569 1 20V4C1 2.34315 2.34315 1 4 1H18.1716ZM4 3C3.44772 3 3 3.44772 3 4V20C3 20.5523 3.44772 21 4 21L5 21L5 15C5 13.3431 6.34315 12 8 12L16 12C17.6569 12 19 13.3431 19 15V21H20C20.5523 21 21 20.5523 21 20V6.82843C21 6.29799 20.7893 5.78929 20.4142 5.41421L18.5858 3.58579C18.2107 3.21071 17.702 3 17.1716 3H17V5C17 6.65685 15.6569 8 14 8H10C8.34315 8 7 6.65685 7 5V3H4ZM17 21V15C17 14.4477 16.5523 14 16 14L8 14C7.44772 14 7 14.4477 7 15L7 21L17 21ZM9 3H15V5C15 5.55228 14.5523 6 14 6H10C9.44772 6 9 5.55228 9 5V3Z"
              fill="#0F0F0F" />
          </svg></button>
        <button v-if="!book.edit" v-on:click="book.edit = true"><svg xmlns="http://www.w3.org/2000/svg" width="20px"
            height="20px" viewBox="0 0 24 24" fill="none">
            <path fill-rule="evenodd" clip-rule="evenodd"
              d="M20.8477 1.87868C19.6761 0.707109 17.7766 0.707105 16.605 1.87868L2.44744 16.0363C2.02864 16.4551 1.74317 16.9885 1.62702 17.5692L1.03995 20.5046C0.760062 21.904 1.9939 23.1379 3.39334 22.858L6.32868 22.2709C6.90945 22.1548 7.44285 21.8693 7.86165 21.4505L22.0192 7.29289C23.1908 6.12132 23.1908 4.22183 22.0192 3.05025L20.8477 1.87868ZM18.0192 3.29289C18.4098 2.90237 19.0429 2.90237 19.4335 3.29289L20.605 4.46447C20.9956 4.85499 20.9956 5.48815 20.605 5.87868L17.9334 8.55027L15.3477 5.96448L18.0192 3.29289ZM13.9334 7.3787L3.86165 17.4505C3.72205 17.5901 3.6269 17.7679 3.58818 17.9615L3.00111 20.8968L5.93645 20.3097C6.13004 20.271 6.30784 20.1759 6.44744 20.0363L16.5192 9.96448L13.9334 7.3787Z"
              fill="#0F0F0F" />
          </svg></button>
        <button class="del" v-on:click="() => { deliteRow(book.id) }"><svg xmlns="http://www.w3.org/2000/svg" width="20px"
            height="20px" viewBox="0 0 24 24" fill="none">
            <path d="M4 7H20" stroke="#000000" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
            <path
              d="M6 10L7.70141 19.3578C7.87432 20.3088 8.70258 21 9.66915 21H14.3308C15.2974 21 16.1257 20.3087 16.2986 19.3578L18 10"
              stroke="#000000" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
            <path d="M9 5C9 3.89543 9.89543 3 11 3H13C14.1046 3 15 3.89543 15 5V7H9V5Z" stroke="#000000" stroke-width="2"
              stroke-linecap="round" stroke-linejoin="round" />
          </svg></button>
      </div>
    </li>
  </ul>
</template>

<script>
export default {
  name: 'TableComponent',
  data() {
    return {
      data: [],
      newItem: {
        title: "",
        description: "",
        published: false
      }
    }
  },
  mounted() {
    this.refreshItems()
  },
  methods: {
    deliteRow(id) {
      fetch("http://localhost:8080/api/" + id, {
        method: 'DELETE',
      }).then(res => {
        if (res.status == 200) {
          this.data = this.data.filter(x => {
            return x.id != id;
          })
        }
      })
    },
    addItem(e) {
      e.preventDefault()
      fetch("http://localhost:8080/api", {
        method: 'POST',
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          "title": this.newItem.title,
          "description": this.newItem.description,
          "published": this.newItem.published
        })
      }).then((res) => {
        if (res.status == 200) {
          this.newItem.title = ""
          this.newItem.description = ""
          this.newItem.published = false
          this.refreshItems()
        }
      })
    },
    saveRow(updateRow) {
      fetch("http://localhost:8080/api/" + updateRow.id, {
        method: 'PUT',
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          "title": updateRow.title,
          "description": updateRow.description,
          "published": updateRow.published
        })
      }).then((res) => {
        if (res.status == 200) {
          delete updateRow.edit
        }
      })
    },
    refreshItems() {
      fetch("http://localhost:8080/api").then(res => res.json()).then(data => this.data = data)
    }
  }
}
</script>

<style scoped>
.table {
  border: 1px solid black;
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
}

.table .table-item:not(:last-child) {
  border-bottom: 1px solid black;
}

.table-item {
  display: flex;
}

.table-item div {
  display: flex;
  align-items: center;
  font-size: 17px;
  font-weight: 500;
  padding: 10px;
  flex: 1;
  text-align: start;
}

form.table-item div {
  padding: 5px;
}

.table-item div:last-child {
  display: flex;
  justify-content: flex-end;
}

.table-head div {
  font-size: 18px;
  font-weight: 600;
}

.add-item {
  border: 2px dashed black;
  margin: 15px 0;
  display: flex;
  justify-content: space-between;
}

.add-item input {
  align-self: flex-start;
  box-sizing: border-box;
  height: 100%;
  padding: 0px 5px;
  font-size: 13px;
}

.add-item textarea {
  resize: none;
  font-size: 13px;
  padding: 5px;
}

.add-item input::placeholder,
.add-item textarea::placeholder {
  font-size: 15px;
  font-family: 'Courier New', Courier, monospace;

}

.add-item input[type="checkbox"] {
  width: 40px;
  height: 40px;
  cursor: pointer;
}

.add-button {
  background: none;
  padding: 10px;
  border: 1px solid black;
  outline: none;
  cursor: pointer;
  border-radius: 5px;
  font-size: 15px;
}

.add-item div {
  display: flex;
  gap: 15px;
}

.controls {
  display: flex;
  gap: 5px;
}

.controls button {
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  border-radius: 100%;
  padding: 5px;
  width: 50px;
  height: 50px;
  outline: none;
  border: none;
}

.del {
  background-color: rgba(255, 0, 0, 0.8);
}
</style>
