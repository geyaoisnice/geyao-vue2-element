<template>
  <div>
    <el-upload
      action="https://jsonplaceholder.typicode.com/posts/"
      :on-success="handleChange"
      :file-list="fileList"
      class="el-upload"
      >上传文件</el-upload
    >
    <template v-if="tableHead.length>0">
      <el-table :data="tableData[0]" style="width: 100%">
        <el-table-column
          v-for="(item, key) in tableHead"
          :prop="item"
          :key="key"
          :label="item"
          width="180"
        >
        </el-table-column>
      </el-table>
    </template>
  </div>
</template>
  <script>
import { read, utils } from "xlsx";
export default {
  name: "Parse",
  data() {
    return {
      fileList: [],
      tableData: [],
      tableHead: [],
    };
  },
  computed: {},
  methods: {
    // 导入成功时执行
    handleChange(res, file, fileList) {
      // 将文件放入
      for (let i = 0; i < fileList.length; i++) {
        if (file.name != fileList[i].name) {
          this.fileList &&
            this.fileList.push({
              name: file.name,
              url: "",
              uid: file.uid,
            });
        }
      }
      const files = { 0: file };
      this.readExcel(files);
    },
    readExcel(file) {
      const fileReader = new FileReader();

      fileReader.onload = (ev) => {
        try {
      
            const data = ev.target.result;
            const workbook = read(data, { type: "binary" });
            const params = [];
            // 取对应表生成json表格内容
            workbook.SheetNames.forEach((item) => {
              this.tableData.push(utils.sheet_to_json(workbook.Sheets[item]));
            });
            // 该算法仅针对表头无合并的情况
            if (this.tableData.length > 0) {
              // 获取excel中第一个表格数据tableData[0][0]，并且将表头提取出来
              for (const key in this.tableData[0][0]) {
                this.tableHead.push(key);
              }
            }
      
        } catch (e) {
          console.log("error:" + e);
          return false;
        }
      };
      fileReader.readAsBinaryString(file[0].raw);
    },
  },
};
</script>
  
  <style lang='scss'>
</style>
  
  
  