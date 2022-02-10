<template>
  <div id="wrapper">
    <h1>ToDoリスト</h1>
    <div id="memos_content" v-cloak>
      <div>
        <ul>
          <li v-for="memo in memos" :key="memo.id">
            <a id="memo_list" href="#" @click="editTargetItem(memo.id)">{{ memo.body.split('\n')[0] }}</a>
          </li>
        </ul>
        <div id="add_button" @click="inputContentShow">&plus;</div>
      </div>
      <form @submit.prevent="addItem" v-show="isActive">
        <textarea v-model="item"></textarea><br>
        <div id="submit_button">
          <button @click="editItem" type="submit">{{changeButtonText}}</button>
          <span v-show="deleteActive">
            <button @click="deleteItem" type="submit" v-show="isActive">削除</button>
          </span>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import { v4 as uuidv4 } from 'uuid'
export default {
  name: 'ToDoList',
  data () {
    return {
      item: '',
      editId: null,
      memos: [],
      isActive: false,
      deleteActive: false
    }
  },
  computed: {
    changeButtonText () {
      return this.editId === null ? '追加' : '完了'
    }
  },
  mounted () {
    this.$nextTick(() => {
      this.memos = JSON.parse(localStorage.getItem('memos')) || []
    })
  },
  methods: {
    inputContentShow () {
      this.isActive = !this.isActive
      this.deleteActive = false
    },
    createItemModule () {
      const item = {
        id: uuidv4(),
        body: this.item
      }
      this.memos.push(item)
      this.saveItem()
      this.item = ''
      this.isActive = false
    },
    addItem () {
      if (!this.item) return
      this.createItemModule()
    },
    editItem () {
      if (!this.item) return
      if (this.editId === null) {
        this.createItemModule()
      } else {
        this.memos.splice(this.editId, 1, {
          id: this.memos[this.editId].id,
          body: this.item
        })
        this.saveItem()
        this.isActive = false
      }
      this.cancel()
    },
    deleteItem () {
      if (!this.item) return
      if (this.editId === null) {
        this.createItemModule()
      } else {
        this.memos.splice(this.editId, 1)
        this.saveItem()
        this.isActive = false
      }
      this.cancel()
    },
    cancel () {
      this.item = ''
      this.editId = null
    },
    editTargetItem (targetId) {
      this.editId = this.memos.findIndex(({ id }) => id === targetId)
      this.item = this.memos[this.editId].body
      this.isActive = true
      this.deleteActive = true
    },
    saveItem () {
      localStorage.setItem('memos', JSON.stringify(this.memos))
    }
  }
}
</script>

<style scoped>
  [v-cloak] {
    display: none;
  }

  body,
  div,
  h1,
  ul,
  li {
    margin: 0;
    padding: 0;
  }

  #wrapper {
    width: 1000px;
    margin: 0 auto;
  }

  #memos_content{
    display: flex;
    align-items: top;
    justify-content: space-between;
  }

  h1, textarea, a {
    color: #444;
  }

  h1 {
    font-size: 30px;
    margin: 40px 0 30px;
  }

  #add_button{
    font-size: 30px;
    font-weight: bold;
    color: #fff;
    width: 40px;
    height: 40px;
    background: #ffb499;
    border-radius: 50%;
    text-align: center;
    margin-top: 4px;
    cursor : pointer;
  }

  form {
    margin-left: 20px;
    width: 500px;
    text-align: center;
  }

  li,textarea {
    font-size: 20px;
    line-height: 40px;
    text-align: left;
  }

  textarea {
    width: 480px;
    height: 500px;
    border-radius: 6px;
    border: 2px solid rgb(150, 150, 150);
    padding: 10px 0 0 10px;
  }

  a {
    text-decoration: none;
  }

  a:hover {
    text-decoration: underline;
    color: #ffb499;
  }

  ul {
    width: 480px;
    height: 500px;
    padding: 10px 0 0 10px;
    border: 2px dotted rgb(130, 130, 130);
    border-radius: 6px;
    overflow-y: scroll;
  }

  li {
    list-style: none;
  }

  li #memo_list {
    display: inline-block;
    width: 482px;
  }

  button {
    border-radius: 7px;
    border: 2px solid rgb(150, 150, 150);
    background: #fff;
    color: rgb(130, 130, 130);
    font-weight: bold;
    font-size: 14px;
    padding: 6px 10px;
    margin-left: 6px;
    width: 240px;
  }

button:hover {
    border: 2px solid #ffb499;
    color: #ffb499;
  }
</style>
