<template>
  <div>
    <el-card>
      <CategorySelector @changeCategory="changeCategory" :isShowList="isShowList"></CategorySelector>
    </el-card>
    <el-card style="margin-top:20px" v-show="isShowList">
      <div>
        <el-button
        type="primary"
        @click="addAttributes"
        icon="el-icon-plus"
        >添加属性</el-button>
        <el-table
        style="width:100%"
        :data="attributesList"
        border
        >
        <el-table-column type="index" align="center" label="序号" width="80">
          </el-table-column>
          <el-table-column prop="attrName" label="属性名称" width="150">
          </el-table-column>
          <el-table-column
           prop="attrValueList"
            label="属性值列表"
            width="width">
            <template #default="{row}">
              <el-tag 
              type="primary"
              v-for="(item,index) in row.attrValueList"
              :key="item.id"
              >{{item.valueName}}</el-tag>
            </template>
          </el-table-column>
          <el-table-column  prop="prop" label="操作" width="width">
            <template #default="{row}">
              <el-button 
              type="warning"
              icon="el-icon-edit"
              size="mini"
              >编辑</el-button>
              <el-button 
              type="danger"
              icon="el-icon-delete"
              size="mini"
              @click="deleteAttr(row)"
              >删除</el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
    </el-card>
    <el-card v-show="!isShowList" style="margin-top:20px">
      <el-form :inline="true" :modele="attrFrom">
        <el-form-item label="attrName">
          <el-input
          placeholder="please input your want"
          v-model="attrFrom.arrtName"
          ></el-input>
        </el-form-item>
      </el-form>
      <el-button
      type="primary"
      @click="addItemAttr"
      size="default"
      icon="el-icon-plus"
      :disabled="!attrFrom.arrtName"
      >
      addAttributesValue 
      </el-button>
      <el-button 
      @click="cancelAttributesValue"
      >cancel</el-button>
      <el-table
        style="width:100%"
        :data="attrFrom.attrValueList"
        border
      >
      <el-table-column type="index" align="center" label="序号" width="80">
          </el-table-column>
          <el-table-column prop="prop" label="属性值名称" width="width">
            <template #default="{row}">
              <div>{{row}}</div>
              <el-input
              ref="index"
              size="mini"
              v-if="row.isEdit"
              v-model="row.valueName"
              placeholder="请输入属性值名称"
              @blur="toLook(row)"
              @keyup.enter.native="toLook(row)"
              ></el-input>
              <span 
              v-else
              @click="toEdit(row, $index)"
              >
             {{ row.valueName}}
              </span>
            </template>
      </el-table-column>
    </el-table>
    </el-card>
    </div>
</template>

<script>
export default {
  name: 'Sku',
  data(){
    return {
      isShowList: true,
      attributesList: [],
      category1Id: "",
      category2Id: "",
      category3Id: "",
      attrForm: {
        arrtName: '',
        attrValueList: []
      }
    };
  },
  methods: {
    changeCategory({categoryId, level}){
      if (level === 1) {
        this.category2Id = "";
        this.category3Id = "";
        this.attributesList = [];
        this.category1Id = categoryId;
      } else if (level === 2) {
        this.category3Id = "";
        this.attributesList = [];
        this.category2Id = categoryId;
      } else {
        this.category3Id = categoryId;
        this.getAttrList();
      }
    },
    async getAttrList() {
      let { category1Id, category2Id, category3Id } = this;
      const result = await this.$API.attr.getList(
        category1Id,
        category2Id,
        category3Id,
      );
      if (result.code === 200 || result.code === 20000) {
        this.attributesList = result.data;
      }
    },
    // 添加属性
    addAttributes(){
      this.isShowList = false
    },
    // 删除功能
    deleteAttr(row){
      console.log(row)
    },
    // 取消添加属性值
    cancelAttributesValue(){
      this.isShowList = true
    },
    // 添加一个属性值
    addItemAttr(row){
      this.attrFrom.attrValueList.push({
        isEdit: true,
        valueName: ''
      })
    },
    // iput失去焦点
    toLook(row){
      console.log(row)
      let attrValuesRules = row.valueName;
      console.log(attrValuesRules, '1')
      if(attrValuesRules.trim() === ''){
        alert('cannot is trim')
        retrun 
      }
      this.attrForm.attrValueList.filter((item)=>{
        // retrun item.valueName === row.valueName
        console.log(item, 'item')
      })
    },
    // 点击span变成input
    toEdit(row,index){
      console.log(row,index)
     
    }
    }
}
</script>

<style lang="scss" scoped>

</style>
