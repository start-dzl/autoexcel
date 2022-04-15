<template>
  <div>
    <div style="margin-top: 30px">
      <div style="float: left">
        <div id="div1">
          <el-button size="small" type="primary" @click="addHead()"
            >新表头</el-button
          >
        </div>
        <div id="div1">
          <el-upload
            class="upload-demo"
            action="http://localhost:8080/excel"
            :on-preview="handlePreview"
            :on-remove="handleRemove"
            :before-remove="beforeRemove"
            multiple
            :limit="3"
            :on-exceed="handleExceed"
            :file-list="fileList"
          >
            <el-button size="small" type="primary">点击上传</el-button>
          </el-upload>
        </div>
      </div>
      <div style="margin-top: 20px">
        <template>
          <el-table :data="tableData" border style="width: 100%">
            <el-table-column fixed prop="id" label="id" width="300">
            </el-table-column>
            <el-table-column prop="name" label="表名" width="220">
              <template slot-scope="scope">
                <el-input
                  v-model="scope.row.name"
                  placeholder="请输入内容"
                ></el-input>
              </template>
            </el-table-column>
            <el-table-column prop="pinyin" label="拼音" width="120">
            </el-table-column>
            <el-table-column prop="expressions" label="表达式" width="500">
              <template slot-scope="scope">
                <el-breadcrumb separator="">
                  <el-breadcrumb-item
                    v-for="item in scope.row.expresses"
                    :key="item"
                    >{{ item.name }}</el-breadcrumb-item
                  >
                </el-breadcrumb>
                <!-- <el-input v-model="scope.row.expressions" placeholder="请输入内容"></el-input> -->
              </template>
            </el-table-column>
            <el-table-column prop="order" label="排序号" width="120">
              <template slot-scope="scope">
                <el-input
                  v-model="scope.row.order"
                  placeholder="请输入内容"
                ></el-input>
              </template>
            </el-table-column>
            <el-table-column fixed="right" label="操作" width="100">
              <template slot-scope="scope">
                <el-button
                  @click="handleEdit(scope.$index, scope.row)"
                  type="text"
                  size="small"
                  >编辑</el-button
                >
              </template>
            </el-table-column>
          </el-table>
        </template>
      </div>
    </div>

    <div id="div1" style="margin-top: 20px">
      <div id="div1">
        <el-select v-model="selectValue" placeholder="请选择">
          <el-option
            v-for="item in options"
            :key="item.pinyin"
            :label="item.name"
            :value="item.pinyin"
          >
          </el-option>
        </el-select>
      </div>
      <div id="div1">
        <el-input v-model="input" placeholder="请输入内容"></el-input>
      </div>
      <div id="div1">
        <el-button type="primary" @click="init3()">查询</el-button>
      </div>
      <div id="div1">
          <el-upload
            class="upload-demo"
            action="http://localhost:8080/excel/update"
            :on-preview="handlePreview"
            :on-remove="handleRemove"
            :before-remove="beforeRemove"
            multiple
            :limit="3"
            :on-exceed="handleExceed"
            :file-list="fileList"
          >
            <el-button size="small" type="primary">点击上传</el-button>
          </el-upload>
        </div>
        <div id="div1">
        <el-button type="primary" @click="init5()">导出</el-button>
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
            <el-button size="mini"><i class="iconfont"></i></el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <el-dialog
      title="表头修改"
      :visible.sync="centerDialogVisible"
      width="50%"
      center
    >
      <el-form ref="form" :model="headEx" label-width="80px">
        <el-form-item label="名称">
          <el-input v-model="headEx.name"></el-input>
        </el-form-item>
        <el-form-item label="拼音">
          <el-input disabled="false" v-model="headEx.pinyin"></el-input>
        </el-form-item>
        <el-form-item label="排序号">
          <el-input v-model="headEx.order"></el-input>
        </el-form-item>
        <el-form-item label="表达式">
          <el-breadcrumb separator="">
            <el-breadcrumb-item v-for="item in headEx.expresses" :key="item">{{
              item.name
            }}</el-breadcrumb-item>
          </el-breadcrumb>
        </el-form-item>
        <el-form-item label="工具">
          <el-select v-model="value1" placeholder="请选择" @change="change">
            <el-option
              v-for="item in options"
              :key="item.pinyin"
              :label="item.name"
              :value="item.pinyin"
            >
            </el-option>
          </el-select>
          <el-select v-model="value2" placeholder="请选择" @change="change1">
            <el-option
              v-for="item in tablesEx"
              :key="item.name"
              :label="item.express"
              :value="item.name"
            >
            </el-option>
          </el-select>
          <el-input style="margin-top: 20px" v-model="numEx"></el-input>
          <el-button style="margin-top: 20px" @click="onNumEx"> 添加</el-button>
        </el-form-item>
        <el-form-item label="">
          <el-button type="primary" @click="onBacking">回退</el-button>
          <el-button type="primary" @click="onclean"> 清空</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="onSubmit">立即创建</el-button>
          <el-button @click="centerDialogVisible = false">取 消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>
 
<script>
export default {
  data() {
    return {
      centerDialogVisible: false,
      selectValue: null,
      input: null,
      options: [],
      tables: [],
      tableData: [],
      headEx: {},
      value1: "",
      value2: "",
      tablesEx: [],
      numEx: 0,
      fileList: [],
    };
  },
  methods: {
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    handlePreview(file) {
      console.log(file);
    },
    handleExceed(files, fileList) {
      this.$message.warning(
        `当前限制选择 3 个文件，本次选择了 ${files.length} 个文件，共选择了 ${
          files.length + fileList.length
        } 个文件`
      );
    },
    beforeRemove(file, fileList) {
      return this.$confirm(`确定移除 ${file.name}？`);
    },
    onSubmit() {
      var url = "http://localhost:8080/head";

      this.$http
        .post(url, this.headEx)
        .then((res) => {
          console.log(res);
        })
        .catch((err) => {
          console.log(err);
        });
      this.centerDialogVisible = false;
    },
    onBacking() {
      this.headEx.expresses.pop();
    },
    onclean() {
      this.headEx.expresses = [];
    },
    onNumEx() {
      let ex1 = {};
      ex1.pinyin = this.numEx;
      ex1.name = this.numEx;
      if (!this.headEx.expresses) {
        let hn = [];
        this.headEx.expresses = hn;
      }
      this.headEx.expresses.push(ex1);
      this.numEx = null;
    },
    change(val) {
      let ex1 = {};
      let ps = this.options;
      console.log("============");
      console.log(ps);
      for (var va in ps) {
        if (ps[va].pinyin == val) {
          ex1 = ps[va];
        }
      }

      if (!this.headEx.expresses) {
        let hn = [];
        this.headEx.expresses = hn;
      }
      this.headEx.expresses.push(ex1);
      console.log(val);
      this.value1 = null;
    },
    change1(val) {
      let ex1 = {};
      for (var va in this.tablesEx) {
        if (this.tablesEx[va].name == val) {
          ex1.expressEnum = this.tablesEx[va].name;
          ex1.name = this.tablesEx[va].express;
        }
      }

      if (!this.headEx.expresses) {
        let hn = [];
        this.headEx.expresses = hn;
      }
      this.headEx.expresses.push(ex1);
      console.log(val);
      this.value2 = null;
    },
    addHead() {
      let headNew = {
        name: null,
      };
      this.tableData.push(headNew);
    },
    handleEdit(index, row) {
      this.centerDialogVisible = true;
      console.log(row);
      this.headEx = row;
    },
    selectArInfo(val) {
      this.selectArr = val;
    },
    init3() {
      var url = "http://localhost:8080/excel";
      console.log(this.tables);
      let that = this;
      that.axios
        .get(url, {
          params: {
            fildName: that.selectValue,
            fildvalue: that.input,
          },
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
    init1() {
      var url = "http://localhost:8080/excel";
      console.log(this.tables);
      let that = this;
      that.axios
        .get(url)
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
    init2() {
      var url = "http://localhost:8080/excel/head";
      let that = this;
      that.axios
        .get(url)
        .then(function (res) {
          if (res.status == 200) {
            console.log(res.data);
            that.options = res.data;
          }
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    init4() {
      var url = "http://localhost:8080/expressEnum";
      let that = this;
      that.axios
        .get(url)
        .then(function (res) {
          if (res.status == 200) {
            console.log(res.data);
            that.tablesEx = res.data;
          }
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    init5() {
      window.open("http://localhost:8080/excel/output","_blank")
      },
  },
  created() {
    this.init1();
    this.init2();
    this.init4();
  },
};
</script>
<style scoped>
#div1 {
  float: left;
  padding: 0px 20px;
}
</style>