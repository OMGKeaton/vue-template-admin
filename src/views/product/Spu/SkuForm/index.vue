<template>
  <el-form label-width="100px">
    <el-form-item label="SPU名称"> spu名称 </el-form-item>

    <el-form-item label="SKU名称">
      <el-input v-model="model" placeholder="SKU名称"></el-input>
    </el-form-item>

    <el-form-item label="价格(元)">
      <el-input v-model="model" placeholder="价格(元)" type="number"></el-input>
    </el-form-item>

    <el-form-item label="重量(千克)">
      <el-input
        v-model="model"
        placeholder="重量(千克)"
        type="number"
      ></el-input>
    </el-form-item>

    <el-form-item label="规格描述">
      <el-input
        v-model="model"
        placeholder="规格描述"
        type="textarea"
        rows="4"
      ></el-input>
    </el-form-item>

    <el-form-item label="平台属性">
      <el-form :inline="true" label-width="100px">
        <el-form-item label="产地">
          <el-select value="" placeholder="请选择">
            <el-option label="label" value="value"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
    </el-form-item>

    <el-form-item label="销售属性">
      <el-form :inline="true" label-width="100px">
        <el-form-item label="颜色">
          <el-select value="" placeholder="请选择">
            <el-option label="label" value="value"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
    </el-form-item>

    <el-form-item label="图片列表">
      <el-table border style="width: 100%">
        <el-table-column align="center" type="selection" width="55"> </el-table-column>
        <el-table-column prop="prop" label="图片" width="width">
        </el-table-column>
        <el-table-column prop="prop" label="名称" width="width">
        </el-table-column>
        <el-table-column prop="prop" label="操作" width="width">
        </el-table-column>
      </el-table>
    </el-form-item>

    <el-form-item>
      <el-button type="primary">保存</el-button>
      <el-button @click="$emit('update:isShowSkuForm',false)">取消</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
export default {
  name: "SkuForm",
};
</script>

<style lang="scss" scoped>
</style>
