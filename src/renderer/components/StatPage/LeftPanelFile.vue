<template>
  <div style="height:400px; overflow-y:auto;">
    <table id="stat-left-file-table">
      <tr>
        <th id="stat-left-file-th" class="table-danger"> 数据分析文件</th>
      </tr>
      <tr class="stat-left-file-tr" v-for="(data, index) in xs" v-bind:key='index' v-on:click="loadFile(data, index)" v-bind:class="{'table-danger':flag == index}">
        <td>{{data}}</td>
      </tr>
    </table>
  </div>
</template>

<script>
  import loadFile from '../../utils/LoadFile';
  import { getStat } from '../../utils/StatServerFile'
  export default {
    data() {
      return {
        flag: null
      };
    },
    computed: {
      xs: {
        get() {
          return this.$store.state.Stat.files
        }
      }
    },
    methods: {
      loadFile: function (data, index) {
        this.flag = index
        this.$store.commit('STAT_SET_FILE_INDEX', index);
        if (this.$store.state.Stat.tableType === 'server') {
          if (this.$store.state.System.server === '') {
            const key = Object.keys(global.hitbdata.server)
            const server = global.hitbdata.server[key][0];
            console.log(server);
            getStat(this, ['www.jiankanglaifu.com', '80', data])
          } else {
            getStat(this, [this.$store.state.System.server, this.$store.state.System.port])
          }
        } else {
          loadFile(this, data, 'stat')
          this.$store.commit('STAT_SET_TABLE_TYPE', 'local');
        }
      },
    },
  };
</script>

<style scoped>

</style>
