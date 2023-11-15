# 前言

> 今天继续带来vue2中关于表格的处理

#  安装

```
npm i xlsx
```

# 安装过程
![在这里插入图片描述](https://img-blog.csdnimg.cn/953cc18d42a64d7f8c6acd16878d1586.png)

# 页面增加一个路由

```
import Vue from 'vue'
import VueRouter from 'vue-router'
import HomeView from '../views/HomeView.vue'

Vue.use(VueRouter)

const routes = [
  {
    path: '/',
    name: 'home',
    component: HomeView
  },
  {
    path: '/about',
    name: 'about',
    component: () => import('../views/AboutView.vue')
  },
  {
    path: '/excel',
    name: 'excel',
    component: () => import('../views/ExcelParse.vue')
  }
]

const router = new VueRouter({
  mode: 'history',
  base: process.env.BASE_URL,
  routes
})

export default router

```

# 增加一个主要文件

```
<template>
  <div>
    <el-upload
      action="https://jsonplaceholder.typicode.com/posts/"
      :on-success="handleChange"
      :file-list="fileList"
      class="el-upload"
      >上传文件</el-upload
    >
    <el-table :data="tableData[0]" style="width: 100%">
      <el-table-column prop="name" label="姓名" width="180"> </el-table-column>
      <el-table-column prop="age" label="年龄" width="180"> </el-table-column>
    </el-table>
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
          console.log( this.tableData[0], "data is");
          // 重写数据
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
  
  
```

# 制作表格 上传
![在这里插入图片描述](https://img-blog.csdnimg.cn/f539fbb6be204e7fadaecee03635aaf1.png)

# 运行结果
![在这里插入图片描述](https://img-blog.csdnimg.cn/a7e0f74723ce4b7b814ed9e5d17e4a8d.png)
