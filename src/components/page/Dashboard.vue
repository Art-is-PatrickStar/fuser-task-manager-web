<template>
    <div>
        <el-row :gutter="20">
            <el-col :span="8">
                <el-card shadow="hover" class="mgb20" style="height:252px;">
                    <div class="user-info">
                        <img src="../../assets/img/img.jpg" class="user-avator" alt />
                        <div class="user-info-cont">
                            <div class="user-info-name">{{name}}</div>
                            <div>{{role}}</div>
                        </div>
                    </div>
                    <div class="user-info-list">
                        上次登录时间：
                        <span>2019-11-01</span>
                    </div>
                    <div class="user-info-list">
                        座右铭：&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                        <span>Fuck使我快乐</span>
                    </div>
                </el-card>
                <el-card shadow="hover" style="height:252px;">
                    <div slot="header" class="clearfix" style="display: flex">
                        <span>任务详情</span>
                    </div>Vue
                    <el-progress :percentage="71.3" color="#42b983"></el-progress>JavaScript
                    <el-progress :percentage="24.1" color="#f1e05a"></el-progress>CSS
                    <el-progress :percentage="13.7"></el-progress>HTML
                    <el-progress :percentage="5.9" color="#f56c6c"></el-progress>
                </el-card>
            </el-col>
            <el-col :span="16">
                <el-card shadow="hover" style="height:524px;">

                    <div slot="header" class="clearfix" style="align-items:center; display:flex; justify-content:space-between;">
                        <span>待办事项</span>
                      <div>
                        <el-select style="width: 100px; margin-right: 10px;" v-model="value" placeholder="请选择">
                          <el-option
                              v-for="item in options"
                              :key="item.value"
                              :label="item.label"
                              :value="item.value">
                          </el-option>
                        </el-select>
                      <el-button type="primary" @click="dialogFormVisible = true">添加</el-button>
                      </div>
                    </div>
                    <el-table :show-header="false" :data="todoList" style="width:100%;" >
                        <el-table-column width="40">
                            <template slot-scope="scope">
                                <el-checkbox v-model="scope.row.status"></el-checkbox>
                            </template>
                        </el-table-column>
                        <el-table-column>
                            <template slot-scope="scope">
                                <div
                                    v-if=" isEdit != scope.$index"
                                    class="todo-item"
                                    :class="{'todo-item-del': scope.row.status}"
                                >
                                 {{scope.row.title}}
                                 </div>
                              <el-input v-if=" isEdit == scope.$index " v-model="scope.row.title" style="padding: 0px"></el-input>
                            </template>
                        </el-table-column>
                        <el-table-column
                            width="140"
                        >
                          <template slot-scope="scope">
                            <el-button
                                v-if="isEdit == scope.$index"
                                type="text"
                                size="small"
                                icon="el-icon-check"
                                @click="handleEdit(scope.$index, scope.row, 1)"
                            >
                              保存
                            </el-button>
                            <el-button
                                v-if="isEdit != scope.$index"
                                type="text"
                                size="small"
                                icon="el-icon-edit"
                                @click.native.prevent="handleEdit(scope.$index, scope.row,0)"
                            >
                              编辑
                            </el-button>
                            <el-button
                                @click.native.prevent="handleDelete(scope.$index, scope.row)"
                                type="text"
                                size="small"
                                icon="el-icon-delete"
                            >
                              移除
                            </el-button>
                          </template>
                        </el-table-column>
                    </el-table>

                </el-card>
            </el-col>
        </el-row>
      <div>
        <el-dialog title="增加待办" center="true" :visible.sync="dialogFormVisible">
            <el-form :model="task" center="true" >
              <el-row>
                <el-col :span="19">
                  <el-form-item label="待办事项" :label-width="formLabelWidth" :label-position="right">
                    <el-input v-model="task.name" autocomplete="off"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="1">
                  <el-button type="text" visible="false"></el-button>
                </el-col>
                <el-col :span="3">
                  <el-button type="primary" style="" @click="dialogFormVisible = false">确 定</el-button>
                </el-col>
              </el-row>
            </el-form>

        </el-dialog>
      </div>
    </div>

</template>

<script>

import Schart from 'vue-schart';
import bus from '../common/bus';
export default {
    name: 'dashboard',
    data() {
        return {
            isEdit: -99,  //是否是编辑状态
            name: localStorage.getItem('ms_username'),
            todoList: [
                {
                    title: '今天要修复100个bug',
                    status: false
                },
                {
                    title: '今天要修复100个bug',
                    status: false
                },
                {
                    title: '今天要写100行代码加几个bug吧',
                    status: false
                },
                {
                    title: '今天要修复100个bug',
                    status: false
                },
                {
                    title: '今天要修复100个bug',
                    status: true
                },
                {
                    title: '今天要写100行代码加几个bug吧',
                    status: true
                }
            ],
          options: [{
            value: '全部',
            label: '全部'
          },{
            value: '未开发',
            label: '未开发'
          }, {
            value: '未测试',
            label: '未测试'
          }, {
            value: '自定义',
            label: '自定义'
          }],
          value:'',
          dialogFormVisible: false,
          formLabelWidth: '120px',
          task:{
              name:''
          }
        };

    },
    components: {
        Schart
    },
    computed: {
        role() {
            return this.name === 'admin' ? '超级管理员' : '普通用户';
        }
    },
    // created() {
    //     this.handleListener();
    //     this.changeDate();
    // },
    // activated() {
    //     this.handleListener();
    // },
    // deactivated() {
    //     window.removeEventListener('resize', this.renderChart);
    //     bus.$off('collapse', this.handleBus);
    // },
    methods: {
        changeDate() {
            const now = new Date().getTime();
            this.data.forEach((item, index) => {
                const date = new Date(now - (6 - index) * 86400000);
                item.name = `${date.getFullYear()}/${date.getMonth() + 1}/${date.getDate()}`;
            });
        },
      handleEdit(index, row, status) {
        if(!row.status) {
          switch (status) {
            case 0:
              this.isEdit = index;
              break;
            case 1:
              this.isEdit = -99;
              break;
          }
        }else{
          this.$alert('大佬，已完成的待办事项不能编辑', '提示', {
            confirmButtonText: '确定',
            type: 'info'
          });
        }
      },
      handleDelete(index, row) {
        this.$confirm('这将会永久删除该行数据，是否继续?', '警告', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.todoList.splice(index, 1);
          this.$message({
            type: 'success',
            message: '删除成功'
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });
      },
        // handleListener() {
        //     bus.$on('collapse', this.handleBus);
        //     // 调用renderChart方法对图表进行重新渲染
        //     window.addEventListener('resize', this.renderChart);
        // },
        // handleBus(msg) {
        //     setTimeout(() => {
        //         this.renderChart();
        //     }, 200);
        // },
        // renderChart() {
        //     this.$refs.bar.renderChart();
        //     this.$refs.line.renderChart();
        // }
    }
};
</script>


<style scoped>
.el-row {
    margin-bottom: 20px;
}

.grid-content {
    display: flex;
    align-items: center;
    height: 100px;
}

.grid-cont-right {
    flex: 1;
    text-align: center;
    font-size: 14px;
    color: #999;
}

.grid-num {
    font-size: 30px;
    font-weight: bold;
}

.grid-con-icon {
    font-size: 50px;
    width: 100px;
    height: 100px;
    text-align: center;
    line-height: 100px;
    color: #fff;
}

.grid-con-1 .grid-con-icon {
    background: rgb(45, 140, 240);
}

.grid-con-1 .grid-num {
    color: rgb(45, 140, 240);
}

.grid-con-2 .grid-con-icon {
    background: rgb(100, 213, 114);
}

.grid-con-2 .grid-num {
    color: rgb(45, 140, 240);
}

.grid-con-3 .grid-con-icon {
    background: rgb(242, 94, 67);
}

.grid-con-3 .grid-num {
    color: rgb(242, 94, 67);
}

.user-info {
    display: flex;
    align-items: center;
    padding-bottom: 20px;
    border-bottom: 2px solid #ccc;
    margin-bottom: 20px;
}

.user-avator {
    width: 120px;
    height: 120px;
    border-radius: 50%;
}

.user-info-cont {
    padding-left: 50px;
    flex: 1;
    font-size: 14px;
    color: #999;
}

.user-info-cont div:first-child {
    font-size: 30px;
    color: #222;
}

.user-info-list {
    font-size: 14px;
    color: #999;
    line-height: 25px;
}

.user-info-list span {
    margin-left: 70px;
}

.mgb20 {
    margin-bottom: 20px;
}

.todo-item {
    font-size: 14px;
}

.todo-item-del {
    text-decoration: line-through;
    color: #999;
}

.schart {
    width: 100%;
    height: 300px;
}
</style>
