<template>
  <div>
    <div v-if="this.$store.state.System.toolbar === 'getServers'">
      <table>
        <tr v-for="(data, index) in file" v-bind:key='index' v-on:click="connect(data, index)" v-bind:class="{'table-danger':flag == index}" class="server-rightpanel-tr">
          <td v-for="(field, index) in data" v-bind:key='index'>{{data[index]}}</td>
        </tr>
      </table>
    </div>
    <!-- 连接服务器状态 -->
    <div v-if="this.$store.state.System.connectInfo == true" >
      <!-- 登录状态 -->
        <!-- 未登录 -->
        <div v-if="this.$store.state.System.user.login == false">
          <div v-if="this.$store.state.System.toolbar === 'getUsers'">
            <form>
                <div class="form-group">
                  <label for="exampleInputEmail1andname">输入用户名或emails</label>
                  <input type="text" class="form-control" placeholder="exampleInputEmail1andname" v-on:blur="logininput()" v-model="emailorname">
                </div>
                <div class="form-group" v-if="this.loginpass == true">
                  <label for="exampleInputPassword1">密码</label>
                  <input type="password" class="form-control" placeholder="Password" v-model="loginpassword">
                </div>
              </form>
                <button type="submit" class="btn btn-primary" v-on:click="login()">提交</button>
          </div>
          <div v-if="this.$store.state.System.toolbar === 'createUsers'">
            <div v-if="this.$store.state.System.registerInfo[2] == true">
              {{this.$store.state.System.registerInfo[1]}}
              <button class="btn btn-primary" v-on:click="newRegister">重新创建用户</button>
            </div>
            <div v-else>
              <h3  v-if="this.$store.state.System.registerInfo[2] == false">{{this.$store.state.System.registerInfo[1]}}, &nbsp; &nbsp; &nbsp;请重新注册</h3>
              <form>
                <div class="form-group">
                  <label for="InputEmail">用户注册的Email地址</label>
                  <input type="email" class="form-control" id="InputEmail" aria-describedby="emailHelp" placeholder="Enter email" v-model="email">
                </div>
                <div class="form-group">
                  <label for="InputPassword">用户密码</label>
                  <input type="password" class="form-control" id="InputPassword" placeholder="Password" v-model="password">
                </div>
                <div class="form-group">
                  <label for="InputOrg">机构</label>
                  <input type="text" class="form-control" id="InputOrg" placeholder="InputOrg" v-model="org">
                </div>
                <div class="form-group">
                  <label for="InputAge">年龄</label>
                  <input type="number" class="form-control" id="InputAge" placeholder="Age" v-model="age">
                </div>
                <div class="form-group">
                  <label for="InputTel">电话</label>
                  <input type="text" class="form-control" id="InputTel" placeholder="Tel" v-model="tel">
                </div>
                <div class="form-group">
                  <label for="InputPersonname">姓名</label>
                  <input type="password" class="form-control" id="InputPersonname" placeholder="Personname" v-model="personname">
                </div>
              </form>
              <button type="submit" class="btn btn-primary" v-on:click="register">注册用户</button>
            </div>
          </div>
        </div>
        <!-- 未登录 -->
        <!-- 已登录 -->
        <div v-else>
          <div v-if="this.$store.state.System.toolbar === 'getUsers'">
            <get-users></get-users>
            <button type="submit" class="btn btn-primary" v-on:click="orgRegister('userinfo')">确认修改</button>
          </div>
          <div v-if="this.$store.state.System.toolbar === 'getOrgs'" class ="orgs">
            <get-orgs></get-orgs>
          </div>
          <div v-if="this.$store.state.System.toolbar === 'createOrgs'">
            <create-orgs></create-orgs>
          </div>
          <div v-if="this.$store.state.System.toolbar === 'createDepartments'">
            <create-departments></create-departments>
          </div>
          <div v-if="this.$store.state.System.toolbar === 'getPersons'">
            <get-persons></get-persons>
          </div>
        </div>
        <!-- 已登录 -->
      <!-- 登录状态 -->
    </div>
    <div v-else>
      请连接服务器
    </div>
    <!-- 连接服务器状态 -->
  </div>
</template>

<script>
  // import { sGetOrg, sCreateOrg, sUpdateOrg, sGetDepart, sCreateDepart, sUpdateDepart, sGetSystemDepart, sUploadDoc, sCheckDoc, sInsertDoc, sStatDoc, sCompDrg, sUpdateUser, sGetStat, sGetStatInfo, sGetStatInfoChart, sdownLoadStatInfo, sSaveDefined, sGetRule, sSearchRule, sGetUser, sLogin, sRegister } from '../../utils/server'
  // import { sGetUser, sLogin, sRegister, sConnect } from '../../utils/Server'
  import GetUsers from './RightPanelServer/GetUsers';
  import GetOrgs from './RightPanelServer/GetOrgs';
  import CreateOrgs from './RightPanelServer/CreateOrgs';
  import CreateDepartments from './RightPanelServer/CreateDepartments';
  import GetPersons from './RightPanelServer/GetPersons';
  import { sLogin, sRegister, sConnect, sUpdateUser } from '../../utils/Server'
  export default {
    components: { GetUsers, GetOrgs, CreateOrgs, CreateDepartments, GetPersons },
    data() {
      return {
        flag: null,
        email: '',
        password: '',
        org: '',
        age: '',
        tel: '',
        personname: '',
        emailorname: '',
        loginpassword: '',
        loginpass: true,
      }
    },
    computed: {
      file: {
        get() {
          const f = []
          let fileLen = this.$store.state.System.file.length;
          switch (this.$store.state.System.toolbar) {
            case 'getServers':
              if (fileLen > 99) { fileLen = 99 }
              for (let i = 0; i < fileLen; i += 1) {
                f.push(this.$store.state.System.file[i].split(','))
              }
              break;
            case 'getUsers':
              break;
            case 'getOrgs':
              break;
            case 'getPersons':
              break;
            case 'getServerFunctions':
              break;
            default:
              break;
          }
          return f
        }
      },
    },
    methods: {
      logininput: function () {
        const reg = /^([0-9A-Za-z\-_.]+)@([0-9a-z]+\.[a-z]{2,3}(\.[a-z]{2})?)$/g
        if (this.emailorname.length === 16) {
          if (reg.test(this.emailorname)) {
            this.loginpass = true
          } else {
            this.loginpass = false
          }
        } else {
          this.loginpass = true
        }
      },
      login: function () {
        const user = { username: this.emailorname, password: this.loginpassword }
        sLogin(this, [this.$store.state.System.server, this.$store.state.System.port, user])
        // if (this.loginpass === false) {
        //   // alert('区块链登录')
        //   // sLogin(this, [this.emailorname, this.loginpassword])
        // } else {
        //   if (this.loginpassword === '') {
        //     // alert('请输入密码')
        //   } else {
        //     const reg = /^([0-9A-Za-z\-_.]+)@([0-9a-z]+\.[a-z]{2,3}(\.[a-z]{2})?)$/g
        //     if (!reg.test(this.emailorname)) {
        //       // alert('用户名必须是邮箱')
        //     } else {
        //       sLogin(this, [this.emailorname, this.loginpassword])
        //     }
        //   }
        //   this.$store.state.System.registerInfo[2] = true
        // }
      },
      connect: function (data, index) {
        this.flag = index
        this.$store.commit('SYSTEM_SET_SERVER', data)
        if (this.$store.state.System.toolbar === 'getServers') {
          sConnect(this, [data[1], data[2], index])
        }
      },
      register: function () {
        const user = { username: this.email, password: this.password, org: this.org, age: this.age, tel: this.tel, email: this.email, name: this.personname, type: 2 }
        sRegister(this, [this.$store.state.System.server, this.$store.state.System.port, user])
      },
      newRegister: function () {
        this.$store.commit('SYSTEM_REGISTER_USER', [{}, '重新创建用户'])
      },
      orgRegister: function (value) {
        if (value === 'userinfo') {
          // const userinfo = { username: this.userinfoName, password: this.userinfoPass }
          const userinfo = {}
          const arr = [this.userinfoName, this.userinfoPass]
          const arr1 = ['username', 'password']
          arr.forEach((n, index) => {
            if (n !== '') {
              userinfo[arr1[index]] = n
            }
          })
          sUpdateUser(this, [this.$store.state.System.server, this.$store.state.System.port, this.$store.state.System.user.id, userinfo]
          )
          // console.log(userinfo)
          this.userinfo.name = false
          this.userinfo.pass = false
        }
      }
    },
  };
</script>

<style scoped>
.orgs{
  margin-top: 0.8em;
}
</style>
