<template>
  <div>
    <table>
      <tr v-for="(data, index) in file" v-bind:key='index' v-on:click="onClick(data, index)" v-bind:class="{'table-danger':flag == index}" class="server-drg-rightpanel-tr">
        <td v-for="(field, index) in data" v-bind:key='index' class="server-drg-rightpanel-td">{{data[index]}}</td>
      </tr>
    </table>
    <div v-if="this.$store.state.System.toolbar === 'getLocalData'">
      <!-- {{this.$store.state.System.wt4LocalRow}} -->
      <table class="table">
        <thead>
          <tr>
            <th>年龄age</th>
            <th>性别gender</th>
            <th>出院转归sf0108</th>
            <th>主要诊断disease_code</th>
            <th>其他诊断diags_code</th>
            <th>手术操作opers_code</th>
            <th>新生儿出生天数sf0100</th>
            <th>新生儿入院体重sf0102</th>
            <th>呼吸机使用小时数sf0104</th>
            <th>总费用total_expense</th>
            <th>住院天数acctual_days</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(value, index) in this.$store.state.System.wt4Table"  v-bind:key="index" v-on:click="getWt4LocalRecord(index)" v-bind:class="{'table-danger': localHightLight.includes(index)}">
            <td>{{value.age}}</td>
            <td>{{value.gender}}</td>
            <td>{{value.sf0108}}</td>
            <td>{{value.disease_code}}</td>
            <td>{{value.diags_code}}</td>
            <td>{{value.opers_code}}</td>
            <td>{{value.sf0100}}</td>
            <td>{{value.sf0102}}</td>
            <td>{{value.sf0104}}</td>
            <td>{{value.total_expense}}</td>
            <td>{{value.acctual_days}}</td>
          </tr>
        </tbody>
      </table>
      <div class="row">
        <div class="col-5" />
        <nav aria-label="Page navigation example">
          <ul class="pagination">
            <li class="page-item" v-for="(value, index) in this.$store.state.System.wt4.page_list" v-bind:class="{'disabled': value.page === page}" v-on:click="wt4Page(value.page)">
              <a class="page-link" href="#">{{value.num}}</a>
            </li>
          </ul>
        </nav>
      </div>
    </div>
    <div v-if="this.$store.state.System.toolbar === 'getServerData'">
      <table class="table">
        <thead>
          <tr>
            <th>年龄age</th>
            <th>性别gender</th>
            <th>出院转归sf0108</th>
            <th>主要诊断disease_code</th>
            <th>其他诊断diags_code</th>
            <th>手术操作opers_code</th>
            <th>新生儿出生天数sf0100</th>
            <th>新生儿入院体重sf0102</th>
            <th>呼吸机使用小时数sf0104</th>
            <th>总费用total_expense</th>
            <th>住院天数acctual_days</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(value, index) in this.$store.state.System.wt4.data"  v-bind:key="index" v-on:click="getWt4Record(index)" v-bind:class="{'table-danger': highLight.includes(index)}">
            <td>{{value.age}}</td>
            <td>{{value.gender}}</td>
            <td>{{value.sf0108}}</td>
            <td>{{value.disease_code}}</td>
            <td>{{value.diags_code.join(',')}}</td>
            <td>{{value.opers_code.join(',')}}</td>
            <td>{{value.sf0100}}</td>
            <td>{{value.sf0102}}</td>
            <td>{{value.sf0104}}</td>
            <td>{{value.total_expense}}</td>
            <td>{{value.acctual_days}}</td>
          </tr>
        </tbody>
      </table>
      <div class="row">
        <div class="col-5" />
        <nav aria-label="Page navigation example">
          <ul class="pagination">
            <li class="page-item" v-for="(value, index) in this.$store.state.System.wt4.page_list" v-bind:class="{'disabled': value.page === page}" v-on:click="wt4Page(value.page)">
              <a class="page-link" href="#">{{value.num}}</a>
            </li>
          </ul>
        </nav>
      </div>
    </div>
    <div v-if="this.$store.state.System.toolbar === 'drgCompute'">
      <table class="table">
        <thead>
          <tr>
            <th>病案号</th>
            <th>DRG</th>
            <th>分组日志</th>
            <th>结果</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(value, index) in this.$store.state.System.wt4Comp"  v-bind:key="index" v-on:click="drgRule(value[0].drg)">
            <td>{{value[0].B_WT4_V1_ID}}</td>
            <td>{{value[0].drg}}</td>
            <td>{{value[0].log}}</td>
            <td>{{value[2]}}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div v-if="this.$store.state.System.toolbar === 'drgRule'">
      <drg-rule></drg-rule>
    </div>
  </div>
</template>

<script>
  import DrgRule from './RightPanelDrg/DrgRule';
  import { sGetWt4, sGetCompRule } from '../../utils/Server'
  export default {
    components: { DrgRule },
    data() {
      return {
        flag: [],
        highLight: [],
        page: this.$store.state.System.wt4.page_num,
        localHightLight: []
      }
    },
    computed: {
      file: {
        get() {
          const f = []
          let fileLen = this.$store.state.System.file.length;
          switch (this.$store.state.Home.toolbar) {
            case 'files':
              if (fileLen > 99) { fileLen = 99 }
              for (let i = 0; i < fileLen; i += 1) {
                f.push(this.$store.state.System.file[i].split(','))
              }
              break;
            default:
              break;
          }
          return f
        }
      },
    },
    methods: {
      onClick: function (data, index) {
        this.flag = index
        this.$store.commit('SYSTEM_GET_FIELD', data);
        this.$store.commit('SYSTEM_GET_FIELD_INDEX', index);
      },
      wt4Page: function (value) {
        this.page = value
        sGetWt4(this, [this.$store.state.System.server, this.$store.state.System.port, this.page])
      },
      getWt4Record: function (value) {
        if (this.highLight.includes(value)) {
          this.highLight.splice(this.highLight.findIndex(v => v === value), 1)
        } else {
          this.highLight = [...this.highLight, value]
        }
        this.$store.commit('SYSTEM_GET_WT4ROW', value);
        // console.log(this.$store.state.System.wt4.data[value]);
      },
      getWt4LocalRecord: function (value) {
        if (this.localHightLight.includes(value)) {
          this.localHightLight.splice(this.localHightLight.findIndex(v => v === value), 1)
        } else {
          this.localHightLight = [...this.localHightLight, value]
        }
        this.$store.commit('SYSTEM_GET_WT4_LOCAL_ROW', value);
      },
      drgRule: function (value) {
        this.$store.commit('SYSTEM_SET_TOOLBAR', 'drgRule');
        sGetCompRule(this, [this.$store.state.System.server, this.$store.state.System.port, 'drg', { code: value }])
      }
    },
  };
</script>

<style scoped>
</style>
