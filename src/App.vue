<template>
  <div id="app">
    <div class="container pt-5">
      <div class="card">
        <h1>{{ title }}</h1>
        <div class="form-control">
          <input v-model="inputValue" type="text" placeholder="Введите название задания" @keyup.enter="addNewNote">
          <button class="btn primary" @click="addNewNote">Добавить</button>
        </div>
        <hr />
        <ul class="list" v-if="notes.length">
          <li
            class="list-item"
            v-for="note of notes"
            :key="note.id"
          >
            <div>
              <input type="checkbox" v-model="note.checked" @click="checkNote(note.id)" class="list-checkbox">
              №{{ note.id }}: {{ note.text }}
            </div>
            <button class="btn danger" @click="deleteNote(note.id)">Удалить</button>
          </li>
        </ul>
        <p v-else>Задач пока нет</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'App',
  data() {
    return {
      title: 'Задания',
      inputValue: '',
      notes: [],
      url: 'http://localhost:3000/todoItems'
    }
  },
  async created() {
    await axios.get(this.url)
      .then(res => {
        this.notes = res.data
      })
      .catch(e => {
        console.log(e)
        throw e
      })
  },
  methods: {
    async addNewNote() {
      await axios.post(this.url, {
        id: this.id,
        text: this.inputValue,
        checked: false
      })
        .then(res => {
          this.notes.push(res.data)
          this.inputValue = ''
        })
        .catch(e => {
          console.log(e)
          throw e
        })
    },
    async deleteNote(id) {
      const idx = this.notes.find(n => n.id === id)
      await axios.delete(`http://localhost:3000/todoItems/${idx.id}`)
        .then(this.notes.splice(idx.id, 1))
        .catch(e => {
          console.log(e);
          throw e
        })
    },
    async checkNote(id) {
      const idx = this.notes.find(n => n.id === id)
      await axios.put(`http://localhost:3000/todoItems/${idx.id}`, {
        id: this.id,
        text: idx.text,
        checked: true
      })
        .then()
        .catch(e => {
          console.log(e);
          throw e
        })
    }
  }
}
</script>

<style>
  .form-control {
    display: flex;
  }
  .form-control .btn {
    margin-left: 30px;
  }
  .list-checkbox {
    margin-right: 15px;
  }
</style>
