<template>
  <div id="app">
    <div class="col-8 offset-2">
      <!-- <el-container>
        <el-header style="font-size: 20px">学生管理系统</el-header>
        <el-container>
          <el-aside width="200px">Aside</el-aside>
          <el-main>Main</el-main>
        </el-container>
      </el-container> -->
      <table class="table caption-top table-hover">
        <caption class="text-center">
          <h1>学生管理系统</h1>
          <el-button type="primary" @click="getStudents">获取学生信息</el-button>
          <el-button type="warning" @click="dialogVisible = true">添加学生信息</el-button>
          <el-input type="text" placeholder="请输入学生姓名" class="w-25" v-model="serchname"></el-input>
          <el-button type="success" @click="serchStudent">搜 索</el-button>

          <el-dialog title="提示" :visible.sync="dialogVisible" width="30%" :before-close="handleClose">
            <div>添加学生信息</div>
            <span slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible = false">取 消</el-button>
              <el-button type="primary" @click="addStudent">添 加</el-button>
            </span>
            <div>
              <span>id：</span><input v-model="newStudent.id"/>
            </div>
            <div>
              <span>学号：</span><input v-model="newStudent.number"/>
            </div>
            <div>
              <span>姓名：</span><input v-model="newStudent.name"/>
            </div>
            <div>
              <span>年龄：</span><input v-model.number="newStudent.age"/>
            </div>
            <div>
              <span>语文：</span><input v-model.number="newStudent.chinese"/>
            </div>
            <div>
              <span>数学：</span><input v-model.number="newStudent.math"/>
            </div>
            <div>
              <span>英语：</span><input v-model.number="newStudent.english"/>
            </div>
          </el-dialog>

          <div class="col-8 offset-2">
          <el-button type="text" @click="dialogVisible_login = true">登 录</el-button>
          <el-dialog title="提示" :visible.sync="dialogVisible_login" width="30%" :before-close="handleClose">
            <div>登 录</div>
            <span slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible_login = false">取 消</el-button>
              <el-button type="primary" @click="loginStudent">登 录</el-button>
            </span>
            <div>
              <span>用户名：</span><input type="text" v-model="userinfo.username"/>
            </div>
            <div>
              <span>密  码：</span><input type="password" v-model="userinfo.password"/>
            </div>
          </el-dialog>
          

          <el-button type="text" @click="dialogVisible_register = true">注 册</el-button>
          <el-dialog title="提示" :visible.sync="dialogVisible_register" width="30%" :before-close="handleClose">
            <div>注 册</div>
            <span slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible_register = false">取 消</el-button>
              <el-button type="primary" @click="registerStudent">注 册</el-button>
            </span>
            <div>
              <span>用户名：</span><input type="text" v-model="registerinfo.username"/>
            </div>
            <div>
              <span>密  码：</span><input type="password" v-model="registerinfo.password"/>
            </div>
          </el-dialog>
          </div>
        </caption>
        <thead>
          <tr>
            <th scope="col">学号</th>
            <th scope="col">姓名</th>
            <th scope="col">年龄</th>
            <th scope="col">语文</th>
            <th scope="col">数学</th>
            <th scope="col">英语</th>
            <th scope="col">操作</th>
          </tr>
        </thead>
        <tbody>
          <Student v-for="stu in students_for_page" :key="stu.id" :student="stu"></Student>
        </tbody>
      </table>
      <div class="text-center">
        <el-button-group>
        <el-button type="primary" icon="el-icon-arrow-left" @click="last_page">上一页</el-button>
        <el-button type="primary" @click="next_page">下一页<i class="el-icon-arrow-right el-icon--right"></i></el-button>
        </el-button-group>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import Student from "./components/Student"
export default {
  name: 'App',
  components: {
    Student
  },
  data(){
    return{
      page:1,
      students:[],
      dialogVisible: false,
      dialogVisible_login: false,
      dialogVisible_register: false,
      newStudent:{
        id:"",
        number:"",
        name:"",
        age:"",
        chinese:"",
        math:"",
        english:""
      },
      userinfo:{
        username:"",
        password:""
      },
      registerinfo:{
        username:"",
        password:""
      },
      serchname:""
    }
  },
  methods:{
    handleClose(done) {
        this.$confirm('确认关闭？')
          // eslint-disable-next-line no-unused-vars
          .then(_ => {
            done();
          })
          // eslint-disable-next-line no-unused-vars
          .catch(_ => {});
      },
    getStudents(){
      if (sessionStorage.getItem("isLogined") == "true"){
        axios({
        url:"http://localhost:8081/students",
        method:'GET',
      }).then(res =>{
        console.log(res.data)
        this.students = res.data;
      })
      }
      else{
        this.$alert("请先登录");
      }
    },
    addStudent(){
      if(sessionStorage.getItem("isLogined") == "true"){
        axios({
        url:"http://localhost:8081/add",
        method:'POST',
        data:this.newStudent
      })
      this.dialogVisible=false;
      this.newStudent = {
        id:"",
        number:"",
        name:"",
        age:"",
        chinese:"",
        math:"",
        english:""
      }
      location.reload()
      }
      else{
        this.$alert("请先登录");
      }
    },
    loginStudent(){
      if (sessionStorage.getItem("isLogined") == "true"){
        this.$alert("您已登录");
      }
      else{
        axios({
          url:"http://localhost:8081/login",
          method:'POST',
          data:this.userinfo
        }).then(res =>{
          console.log(res.data)
          if (res.data == "1"){
          sessionStorage.setItem("isLogined","true");
        }
        else{
          this.$alert("用户名或密码错误");
        }
        })
        this.userinfo = {
          username:"",
          password:""
        }
        this.dialogVisible_login=false;
        // location.reload()
      }
    },
    registerStudent(){
      axios({
        url:"http://localhost:8081/register",
        method:"POST",
        data:this.registerinfo
      })
      this.dialogVisible_register = false;
      this.$alert("注册成功");
      this.registerinfo = {
          username:"",
          password:""
        }
    },
    serchStudent(){
      if (sessionStorage.getItem("isLogined") == "true"){
        axios({
        url:"http://localhost:8081/serch",
        method:"POST",
        data:{
          name:this.serchname
        }
      }).then(res=>{
        this.students = res.data;
      })
      }
      else{
        this.$alert("请先登录哦");
      }
      this.serchname = ""
    },
    next_page(){
      this.page += 1;
    },
    last_page(){
      this.page -= 1;
    }
  },
  computed:{
    students_for_page(){
      return this.students.slice(this.page*10-10,this.page*10)
    }
  }
}
</script>

<style>
  .el-header {
    background-color: #B3C0D1;
    color: #333;
    text-align: center;
    line-height: 60px;
  }
  
  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: center;
    line-height: 200px;
  }
  
  .el-main {
    background-color: #E9EEF3;
    color: #333;
    text-align: center;
    line-height: 160px;
  }

</style>
