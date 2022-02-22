<template>
  <div class="wrapper">
    <h1>ToDoリスト</h1>
    <div id="memos-content" v-cloak>
      <div>
        <ul>
          <li v-for="memo in memos" :key="memo.id">
            <a id="memo-list" href="#" @click="openMemo(memo.id)">{{ memo.body.split('\n')[0] }}</a>
          </li>
        </ul>
        <div id="add-button" @click="openNewContent">&plus;</div>
      </div>
      <form @submit.prevent="addMemo" v-show="contentIsEnabled">
        <textarea v-model="body"></textarea><br>
        <div>
          <button @click="editMemo" type="submit" >{{buttonText}}</button>
          <button @click="deleteMemo" type="submit" v-show="!memoIsEditing">削除</button>
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
      body: '',
      editId: null,
      memos: [],
      contentIsEnabled: false
    }
  },
  computed: {
    buttonText () {
      return this.memoIsEditing ? '追加' : '完了'
    },
    memoIsEditing () {
      return this.editId === null
    }
  },
  mounted () {
    this.memos = JSON.parse(localStorage.getItem('memos')) || []
  },
  methods: {
    openNewContent () {
      this.contentIsEnabled = !this.contentIsEnabled
    },
    createMemo () {
      const memo = {
        id: uuidv4(),
        body: this.body
      }
      this.memos.push(memo)
      this.saveMemo()
      this.body = ''
      this.contentIsEnabled = false
    },
    addMemo () {
      if (!this.body) return
      this.createMemo()
    },
    editMemo () {
      this.memos.splice(this.editId, 1, {
        id: this.memos[this.editId].id,
        body: this.body
      })
      this.reset()
    },
    deleteMemo () {
      this.memos.splice(this.editId, 1)
      this.reset()
    },
    reset () {
      this.saveMemo()
      this.contentIsEnabled = false
      this.body = ''
      this.editId = null
    },
    openMemo (targetId) {
      this.editId = this.memos.findIndex(({ id }) => id === targetId)
      this.body = this.memos[this.editId].body
      this.contentIsEnabled = true
    },
    saveMemo () {
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

  .wrapper {
    width: 1000px;
    margin: 0 auto;
  }

  #memos-content{
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

  #add-button{
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

  li #memo-list {
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
