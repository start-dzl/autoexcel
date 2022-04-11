<template>
  <div>
    <div id="div1">
      <div id="div1">
      <el-select v-model="selectValue" placeholder="请选择">
        <el-option
          v-for="item in options"
          :key="item.pinyin"
          :label="item.name"
          :value="item.pinyin"
          :disabled="item.disabled">
        </el-option>
      </el-select>
      </div>
      <div id="div1">
      <el-input v-model="input" placeholder="请输入内容"></el-input>
      </div>
      <div id="div1">
         <el-button type="primary" @click="init3()" >查询</el-button>
      </div>
    </div>
    <div style="margin-top: 20px">
      <el-table
        :data="tables"
        ref="multipleTable"
        tooltip-effect="dark"
        style="width: 100%"
        @selection-change="selectArInfo"
      >
        <el-table-column type="selection" width="45px"></el-table-column>
        <el-table-column label="序号" width="62px" type="index">
        </el-table-column>
        <template v-for="col in tableData">
          <el-table-column
            sortable
            :show-overflow-tooltip="true"
            :prop="col.pinyin"
            :label="col.name"
            :key="col.pinyin"
            width="124px"
          >
          </el-table-column>
        </template>
        <el-table-column label="操作" width="80" align="center">
          <template slot-scope="scope">
            <el-button size="mini"
              ><i class="iconfont"></i
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>
 
<script>
export default {
  data() {
    return {
      selectValue:null,
      input: null,
      options: [],
      tables: [],
      tableData: [],
    };
  },
  methods: {
    // 获取表格选中时的数据
    selectArInfo(val) {
      this.selectArr = val;
    },
    init3(){
    var url = "http://localhost:8080/excel";
    console.log(this.tables);
    let that = this
    that.axios.get(url,{
     params:{
        'fildName': that.selectValue,
        'fildvalue': that.input
      }
    })
      .then(function (res) {
        if (res.status == 200) {
          console.log(res.data.data);
          that.tables = res.data.data;
          that.tableData = res.data.head;
        }
      })
      .catch(function (error) {
        console.log(error);
      });
    },
    init1(){
    var url = "http://localhost:8080/excel";
    console.log(this.tables);
    let that = this
    that.axios.get(url)
      .then(function (res) {
        if (res.status == 200) {
          console.log(res.data.data);
          that.tables = res.data.data;
          that.tableData = res.data.head;
        }
      })
      .catch(function (error) {
        console.log(error);
      });
    },
    init2(){
    var url = "http://localhost:8080/excel/head";
    let that = this
    that.axios.get(url)
      .then(function (res) {
        if (res.status == 200) {
          console.log(res.data);
          that.options = res.data;
        }
      })
      .catch(function (error) {
        console.log(error);
      });
    }
  },
  created() {
   this.init1();
   this.init2();
  },
};
</script>
<style scoped>

#div1{
    float: left;
    padding: 0px 20px;
}

</style>