<template>
  <div id="wrapper">
    <h1>ToDoリスト</h1>
    <div id="memos_content" v-cloak>
      <ul>
        <li v-for="memo in memos" :key="memo.id">
          <a id="memo_list" href="#" @click="editItem(memo.id)">{{ memo.body.split('\n')[0] }}</a>
          <!-- <button @click="deleteItem(memo.id)">削除</button> -->
          <!-- <button @click="editItem(memo.id)">編集</button> -->
        </li>
      </ul>
        <form @submit.prevent="addItem" v-show="isActive">
          <textarea v-model="item" ref="editor" class="form-control" id="" cols="30" rows="10"></textarea>
          <button @click="setItems" type="submit">{{changeButtonText}}</button>
          <div v-show="deleteActive">
            <button @click="deleteItem" type="submit" v-show="isActive">削除</button>
          </div>
        </form>
    </div>
    <div id="add_button" @click="changeShow">&plus;</div>
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
  // watch: {
  //   memos: {
  //     handler () {
  //       localStorage.setItem('memos', JSON.stringify(this.memos))
  //     },
  //     deep: true
  //   }
  // },
  mounted () {
    this.$nextTick(() => {
      this.memos = JSON.parse(localStorage.getItem('memos')) || []
    })
  },
  methods: {
    addItem () {
      if (!this.item) return
      const item = {
        id: uuidv4(),
        body: this.item
      }
      this.memos.push(item)
      this.saveItem()
      this.item = ''
      this.isActive = false
    },
    setItems () {
      if (!this.item) return
      if (this.editId === null) {
        const item = {
          id: uuidv4(),
          body: this.item
        }
        this.memos.push(item)
        this.saveItem()
        this.isActive = false
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
    cancel () {
      this.item = ''
      this.editId = null
    },
    deleteItem () {
      if (!this.item) return
      if (this.editId === null) {
        const item = {
          id: uuidv4(),
          body: this.item
        }
        this.memos.push(item)
        this.saveItem()
        this.isActive = false
      } else {
        this.memos.splice(this.editId, 1)
        this.saveItem()
        this.isActive = false
      }
      this.cancel()
    },
    editItem (targetId) {
      this.editId = this.memos.findIndex(({ id }) => id === targetId)
      this.item = this.memos[this.editId].body
      this.$refs.editor.focus()
      this.isActive = true
      this.deleteActive = true
    },
    changeShow () {
      this.isActive = !this.isActive
      this.deleteActive = false
    },
    saveItem () {
      localStorage.setItem('memos', JSON.stringify(this.memos))
    }
  }
}
</script>

<style>
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
  }

  h1, form, ul {
    width: 600px;
  }

  h1 {
    margin: 30px auto 40px;
    font-size: 30px;
  }

  #add_button{
    font-size: 30px;
    font-weight: bold;
    color: #fff;
    width: 40px;
    height: 40px;
    background: #ffb499;
    border-radius: 50%;

  }

  form {
    margin: 20px auto 40px;
  }

  form input{
    width: 534px;
    height: 30px;
    border-radius: 6px;
    border: 2px solid rgb(130, 130, 130);
  }

  ul{
    margin: 0 auto;
  }

  li {
    list-style: none;
    font-size: 20px;
    line-height: 52px;
  }

  li #memo_list {
    display: inline-block;
    width: 482px;
  }

  button {
    border-radius: 7px;
    border: 2px solid rgb(130, 130, 130);
    background: #fff;
    color: rgb(130, 130, 130);
    font-weight: bold;
    font-size: 14px;
    padding: 6px 10px;
  }

  button:hover {
    border: 2px solid #ffb499;
    color: #ffb499;
  }

  #flex{
    display: flex;
  }
</style>
