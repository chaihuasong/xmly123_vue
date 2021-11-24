<template>
  <div class="visitCount">
    <el-container style="height: 500px; border: 1px solid #eee">
      <el-aside width="200px" style="background-color: rgb(238, 241, 246)">
        <el-menu :default-openeds="['1', '3']">
          <el-submenu index="1">
            <template slot="title"><i class="el-icon-message"></i>访问后台</template>
            <el-submenu index="1-4">
              <template slot="title">访问量</template>
            </el-submenu>
          </el-submenu>
        </el-menu>
      </el-aside>

      <el-container>
        <el-header style="text-align: center; font-size: 12px">
          <span style="font-size: 20px;font-weight: bold;color: #086bec">123活动计数统计后台</span>
          <el-button type="primary" icon="el-icon-download" @click="outTab" style="float: right;margin-right: 20px; text-align: center">导出</el-button>
        </el-header>

        <el-main>
          <el-table :data="tableData" id="out-table">
            <el-table-column prop="id" label="id" width="140">
            </el-table-column>
            <el-table-column prop="date" label="日期" width="120" :formatter="dateFormatter">
            </el-table-column>
            <el-table-column prop="type" label="类别" width="150" :formatter="typeFormatter">
            </el-table-column>
            <el-table-column prop="count" label="访问数">
            </el-table-column>
          </el-table>
        </el-main>
      </el-container>
    </el-container>

  </div>
</template>

<script>
import axios from "axios"
import XLSX from "xlsx";
import FileSaver from 'file-saver'
export default {
  name: 'VisitCount',
  props: {
    msg: String
  },
  data() {
    return {
      tableData: [],
    }
  },
  mounted() {
    this.getData()
  },
  methods:{
    getData() {
      axios({
        method: "GET",
        url: "http://121.36.132.237:5000/getVisitCount",
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      }).then((res) => {
        this.tableData = res.data
      })
    },
    dateFormatter(row) {
      if(row.date == null) return null;
      if(row.date.toString().indexOf(",") > 0) return row.date.toString().split(",")[0];
      return row.date;
    },
    typeFormatter(row) {
      if(row.type == "yj") {
        return "《易经》"
      } else if(row.type == "zz") {
        return "《庄子》"
      } else if(row.type == "ddj") {
        return "《道德经》"
      } else if(row.type == "xj") {
        return "《心经》"
      } else if(row.type == "jgj") {
        return "《金刚经》"
      } else if(row.type == "ly") {
        return "《论语》"
      } else if(row.type == "cxl") {
        return "《传习录》"
      } else if(row.type == "lztj") {
        return "《六祖坛经》"
      } else if(row.type == "zzwndl") {
        return "《朱子晚年定论》"
      } else if(row.type == "gwjj") {
        return "《格物九讲》"
      } else if(row.type == "daodu" || row.type.toString().indexOf(",") > 0) {
        return "导读"
      } else {
        return "主封面"
      }
    },
    outTab () {
      let xlsxParam = {raw: true}
      /* generate workbook object from table */
      let wb = XLSX.utils.table_to_book(document.querySelector("#out-table"), xlsxParam)
      /* get binary string as output */
      let wbout = XLSX.write(wb, { bookType: 'xlsx', bookSST: true, type: 'array' })
      let time = new Date(Date.now())
      let month = (time.getMonth() + 1) < 10 ? '0' + (time.getMonth() + 1) : (time.getMonth() + 1)
      let day = time.getDate() < 10 ? '0' + time.getDate() : time.getDate()
      let hours = time.getHours() < 10 ? '0' + time.getHours() : time.getHours()
      let minutes = time.getMinutes() < 10 ? '0' + time.getMinutes() : time.getMinutes()
      let fileStr = time.getFullYear() + '' + month + '' + day + '' + hours + '' + minutes + '_' + this.currentPage
      try {
        FileSaver.saveAs(new Blob([wbout], { type: 'application/octet-stream' }), 'Xmly123_VisitCount_' + fileStr + '.xlsx')
      } catch (e) { if (typeof console !== 'undefined') console.log(e, wbout) }
      return wbout
    },
  }
};
</script>

<style>
.el-header {
  background-color: #B3C0D1;
  color: #333;
  line-height: 60px;
}

.el-aside {
  color: #333;
}
</style>
