<template>
  <div id="wrapper">
      <div class="todoApp">
            <div class="header">
                <span class="iconfont icon-guanbi close" @click="closeApp"></span>
                <span class="iconfont icon-zuixiaohua min" @click="miniApp"></span>
            </div>
            <div class="todo">
                <h1>{{title}}</h1>
                <ul class="todo-list">
                    <li
                        class="todo-item"
                        :class="{'done': todo.done}"
                        v-for="todo of showTodos"
                        @click="toggle(todo)"
                        :key="todo.id"
                    >
                        <span
                            class="iconfont checkbox"
                            :class="todo.done?'icon-duoxuanxuanzhong':'icon-duoxuanweixuan'"
                        ></span>
                        <label class="text">{{todo.title}}</label>
                        <span
                            class="iconfont icon-lajitong recycle-bin"
                            @click="remove(todo)"
                        ></span>
                    </li>
                </ul>
            </div>
            <div class="filter">
                <div class="filter-box">
                    <b class="filter-item" :class="{'selected': type=='done'}" @click="choose('done')">DONE</b>
                    <b class="filter-item" :class="{'selected': type=='undone'}" @click="choose('undone')">TODO</b>
                    <b class="filter-item" :class="{'selected': type=='all'}" @click="choose('all')">ALL</b>
                </div>
            </div>
            <div class="add-todo">
                <div>
                    <input class="new-todo" v-model="newTodo" placeholder="新增待办事项" @keydown.enter="addTodo" />
                </div>
            </div>
        </div>
  </div>
</template>

<script>
  import {remote} from 'electron'
  let _id = 0;

  export default {
    name: 'landing-page',
     data() {
       return{
          title: 'TODOS',
          type: 'all',    //all, done, undone
          newTodo: '',
          todos: [
              //{title: '任务标题', done: true}
          ]
       }
     },
    computed: {
      showTodos() {
          return this.todos.filter(todo => {
              switch(this.type) {
                  default:
                  case 'all':
                      return true;
                  case 'done':
                      return todo.done;
                  case 'undone':
                      return !todo.done;
              }
          });
      }
    },
    methods: {
        // 关闭应用
        closeApp() {
            // app对象只能通过主线程调用
            remote.app.exit();
        },
        // 最小化应用窗口
        miniApp() {
            // 通过remote下的一个方法来获取当前窗口对象（BrowserWindow）
            remote.getCurrentWindow().minimize();
        },

        // 添加任务
       addTodo() {
          if(this.newTodo == '') {
            return
          }
            this.todos.unshift({
                id: _id++,
                title: this.newTodo,
                done: false
            });
            this.newTodo = '';
        },
        // 任务状态切换
        toggle(todo) {
            todo.done = !todo.done;
        },

        // 移除任务
        remove(todo) {
            // 保留下来，todos中与传入的todo不一样的任务
            this.todos = this.todos.filter( item => item != todo );
        },

        // 更改查看的任务类型
        choose(type) {
            this.type = type;
        }
    }
  }
</script>
