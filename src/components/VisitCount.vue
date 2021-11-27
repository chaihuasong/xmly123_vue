<template>
  <div class="visitCount">
    <el-container style="height: 100%; border: 1px solid #eee">
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
          <el-table :data="tableData" id="out-table"
                    :header-cell-style="{'text-align':'center'}"
                    :cell-style="{'text-align':'center'}">
            <el-table-column prop="date" label="日期" width="100">
            </el-table-column>
            <el-table-column prop="other" label="主封面" width="80">
            </el-table-column>
            <el-table-column prop="daodu" label="导读" width="80">
            </el-table-column>
            <el-table-column prop="zhibo" label="直播" width="80">
            </el-table-column>
            <el-table-column prop="gwjj" label="《格物九讲》" width="110">
            </el-table-column>
            <el-table-column prop="yj" label="《易经》" width="80">
            </el-table-column>
            <el-table-column prop="zz" label="《庄子》" width="80">
            </el-table-column>
            <el-table-column prop="ddj" label="《道德经》" width="90">
            </el-table-column>
            <el-table-column prop="xj" label="《心经》" width="80">
            </el-table-column>
            <el-table-column prop="jgj" label="《金刚经》" width="90">
            </el-table-column>
            <el-table-column prop="ly" label="《论语》" width="80">
            </el-table-column>
            <el-table-column prop="cxl" label="《传习录》" width="90">
            </el-table-column>
            <el-table-column prop="lztj" label="《六祖坛经》" width="110">
            </el-table-column>
            <el-table-column prop="zzwndl" label="《朱子晚年定论》" width="140">
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
      originData: [],
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
        this.originData = res.data
        let map = {};
        let pre = null;
        this.originData.filter((item) => {
          if (pre == null || pre === item.date) {
            map["date"] = item.date
            map[item.type] = item.count
            console.log(map.date + " : " + item.type + " => " + map[item.type])
          } else {
            this.tableData.push(map)
            console.log("item.date:" + item.date + "--->" + map)
            map = {}
            map["date"] = item.date
            map[item.type] = item.count
          }
          pre = item.date
        })
      })
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
