<template>
  <div>
    <el-card>
      <CategorySelector
        @changeCategory="changeCategory"
        :isShowList="isShowList"
      ></CategorySelector>
    </el-card>

    <el-card style="margin-top: 20px">
      <!-- 这个div代表是spu列表页面 -->
      <div v-show="!isShowSpuForm && !isShowSkuForm">
        <el-button
          type="primary"
          icon="el-icon-plus"
          :disabled="!category3Id"
          @click="showAddSpuForm"
          >添加SPU</el-button
        >
        <el-table :data="spuList" border style="width: 100%">
          <el-table-column type="index" align="center" label="序号" width="80">
          </el-table-column>
          <el-table-column prop="spuName" label="SPU名称" width="width">
          </el-table-column>
          <el-table-column prop="description" label="SPU描述" width="width">
          </el-table-column>
          <el-table-column prop="prop" label="SPU操作" width="width">
            <template slot-scope="{ row, $index }">
              <HintButton
                type="success"
                size="mini"
                icon="el-icon-plus"
                title="添加SKU"
                @click="showAddSkuForm(row)"
              ></HintButton>
              <HintButton
                type="warning"
                size="mini"
                icon="el-icon-edit"
                title="修改SPU"
                @click="showUpdateSpuForm(row)"
              ></HintButton>

              <HintButton
                type="info"
                size="mini"
                icon="el-icon-info"
                title="查看SPU的SKU列表"
              ></HintButton>
              <el-popconfirm :title="`你确定要删除${row.spuName}` " @onConfirm="deleteSpu(row)">
                <HintButton
                  slot="reference"
                  type="danger"
                  size="mini"
                  icon="el-icon-delete"
                  title="删除SPU"
                ></HintButton>
              </el-popconfirm>
            </template>
          </el-table-column>
        </el-table>
        <el-pagination
          style="text-align: center"
          @current-change="getSpuList"
          @size-change="handleSizeChange"
          :current-page="page"
          :page-sizes="[2, 3, 5]"
          :page-size="limit"
          :pager-count="7"
          :total="total"
          layout="prev, pager, next, jumper,->,sizes,total"
        >
        </el-pagination>
      </div>
      <!-- 这个代表添加或者修改spu的页面 -->
      <!-- <div>
        添加或者修改spu的页面
      </div> -->
      <!-- 使用.sync完成点击取消切换回列表的功能 -->
      <!-- <SpuForm
        v-show="isShowSpuForm"
        :isShowSpuForm="isShowSpuForm"
        @update:isShowSpuForm="isShowSpuForm = $event"
      ></SpuForm> -->
      <SpuForm
        v-show="isShowSpuForm"
        :isShowSpuForm.sync="isShowSpuForm"
        @backSuccess="backSuccess"
        ref="spu"
      ></SpuForm>

      <!-- 这个代表添加sku的页面 -->
      <!-- <div>
        添加sku的页面
      </div> -->
      <SkuForm v-show="isShowSkuForm" :isShowSpuForm.sync="isShowSpuForm"></SkuForm>
    </el-card>
  </div>
</template>

<script>
import SkuForm from "./SkuForm";
import SpuForm from "./SpuForm";
export default {
  components: {
    SkuForm,
    SpuForm,
  },
  name: "Spu",
  data() {
    return {
      isShowList: true, //这个数据仅仅是我们要去控制spu相关三级联动的可操作性的，不是控制显示哪个页面
      category1Id: "",
      category2Id: "",
      category3Id: "",

      spuList: [],
      page: 1,
      limit: 2,
      total: 0,

      // 下面两个数据来控制三个页面的切换
      isShowSpuForm: false,
      isShowSkuForm: false,
    };
  },
  methods: {
    changeCategory({ categoryId, level }) {
      if (level === 1) {
        this.category2Id = "";
        this.category3Id = "";
        this.spuList = [];
        this.category1Id = categoryId;
      } else if (level === 2) {
        this.category3Id = "";
        this.spuList = [];
        this.category2Id = categoryId;
      } else {
        this.category3Id = categoryId;
        this.getSpuList();
      }
    },
    // 选中3级分类的时候请求获取spu的列表数据
    async getSpuList(pager = 1) {
      this.page = pager;
      let { page, limit, category3Id } = this;
      const result = await this.$API.spu.getList(page, limit, category3Id);
      if (result.code === 200 || result.code === 20000) {
        this.spuList = result.data.records;
        this.total = result.data.total;
      }
    },



    //修改每页数量的回调
    handleSizeChange(size) {
      this.limit = size;
      this.getSpuList();
    },

    // 点击列表页添加spu按钮切换到SpuForm页面
    showAddSpuForm() {
      this.isShowSpuForm = true;
      this.$refs.spu.getAddSpuFormInitData(this.category3Id)
    },
    // 点击列表页修改spu按钮切换到SpuForm页面
    showUpdateSpuForm(row) {
      this.isShowSpuForm = true;
      this.$refs.spu.getUpdateSpuFormInitData(row,this.category3Id)
    },


    // 点击列表页添加sku按钮切换到SkuForm页面
    showAddSkuForm(row) {
      this.isShowSkuForm = true;
    },

    // 子组件保存成功后通知返回到父组件的回调
    // 父组件如何知道，我是添加成功回来还是修改成功回来的
    backSuccess(spuId){
      if(spuId){
        // 证明是修改回来的
        this.getSpuList(this.page)
      }else{
        // 证明是添加回来的
        this.getSpuList()
      }
    },
    //删除spu的操作
    async deleteSpu(row){
      try {
        const result = await this.$API.spu.remove(row.id)
        if(result.code === 200 || result.code === 20000){
          this.$message.success('删除spu成功')
          this.getSpuList(this.spuList.length>1?this.page:this.page-1)
        }else{
          this.$message.error('删除spu失败')
        }
      } catch (error) {
        this.$message.error('请求删除spu失败')
      }

    }
  },
};
</script>

<style lang="scss" scoped>
</style>
