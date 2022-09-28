<template>
  <div>
    <el-card>
      <CategorySelector :is-show-list="isShowList" @changeCategory="changeCategory" />
    </el-card>
    <el-card v-show="isShowList" style="margin-top:20px">
      <div>
        <el-button
          type="primary"
          icon="el-icon-plus"
          @click="addAttributes"
        >添加属性</el-button>
        <el-table
          style="width:100%"
          :data="attributesList"
          border
        >
          <el-table-column type="index" align="center" label="序号" width="80" />
          <el-table-column prop="attrName" label="属性名称" width="150" />
          <el-table-column
            prop="attrValueList"
            label="属性值列表"
            width="width"
          >
            <template #default="{row}">
              <el-tag
                v-for="(item,index) in row.attrValueList"
                :key="item.id"
                type="primary"
              >{{ item.valueName }}</el-tag>
            </template>
          </el-table-column>
          <el-table-column prop="prop" label="操作" width="width">
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
    <el-card v-show="!isShowList" style="margin-top:21px">
      <el-tabs v-model="activeName" type="card" @tab-click="handleClick">
        <el-tab-pane label="用户管理" name="first">
          <el-form :inline="true" :modele="attrFrom">
            <el-form-item label="attrName">
              <el-input
                v-model="attrFrom.arrtName"
                placeholder="please input your want"
              />
            </el-form-item>
          </el-form>
          <el-button
            type="primary"
            size="default"
            icon="el-icon-plus"
            :disabled="!attrFrom.arrtName"
            @click="addItemAttr"
          >
            addAttributesValue
          </el-button>
          <el-button
            @click="cancelAttributesValue"
          >cancel</el-button>
          <div>{{ attrFrom.attrValueList }}</div>
          <el-table
            style="width:100%"
            :data="attrFrom.attrValueList"
            border
          >
            <el-table-column type="index" align="center" label="序号" width="80" />
            <el-table-column prop="prop" label="属性值名称" width="width">
              <template #default="{row,$index}">
                <!--            {{ row }}-->
                <el-select
                  v-if="row.isEdit"
                  :ref="$index"
                  v-model="row.valueForChoose"
                  placeholder="Select"
                  size="large"
                  @change="toLook(row)"
                >
                  <el-option
                    v-for="item in options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  />
                </el-select>
                <span
                  v-else
                  style="display: block; width: 100%;height: 100%;cursor: pointer"
                  @click="toEdit(row, $index)"
                >
                  {{ row.valueForChoose }}
                </span>
              </template>
            </el-table-column>
            <el-table-column prop="name" label="操作员名称" width="width">
              <template #default="{row,$index}">
                <!--            {{ row }}-->
                <el-input
                  v-if="row.isEdit"
                  :ref="$index"
                  v-model="row.name"
                  size="mini"
                  placeholder="请输入属性值名称"
                  @blur="toLook(row)"
                  @keyup.enter.native="toLook(row)"
                />
                <span
                  v-else
                  style="display: inline-block; width: 100%;height: 100%;cursor: pointer"
                  @click="toEdit(row, $index)"
                >
                  {{ row.name }}
                </span>
              </template>
            </el-table-column>
          </el-table>
        </el-tab-pane>
        <el-tab-pane label="配置管理" name="second">配置管理</el-tab-pane>
        <el-tab-pane label="角色管理" name="third">角色管理</el-tab-pane>
        <el-tab-pane label="定时任务补偿" name="fourth">定时任务补偿</el-tab-pane>
      </el-tabs>
    </el-card>
  </div>
</template>

<script>
export default {
  name: 'Sku',
  data() {
    return {
      isShowList: true,
      attributesList: [],
      category1Id: '',
      category2Id: '',
      category3Id: '',
      attrFrom: {
        arrtName: '',
        attrValueList: [
          { id: 1, valueForChoose: 'Option1', name: 'james' },
          { id: 2, valueForChoose: 'Option3', name: 'kobe' }
        ]
      },
      options: [
        {
          value: 'Option1',
          label: 'Option1'
        },
        {
          value: 'Option2',
          label: 'Option2'
        },
        {
          value: 'Option3',
          label: 'Option3'
        },
        {
          value: 'Option4',
          label: 'Option4'
        },
        {
          value: 'Option5',
          label: 'Option5'
        }
      ],
      activeName: 'first' // el-tabs数据默认展示位置
    }
  },
  methods: {
    changeCategory({ categoryId, level }) {
      if (level === 1) {
        this.category2Id = ''
        this.category3Id = ''
        this.attributesList = []
        this.category1Id = categoryId
      } else if (level === 2) {
        this.category3Id = ''
        this.attributesList = []
        this.category2Id = categoryId
      } else {
        this.category3Id = categoryId
        this.getAttrList()
      }
    },
    async getAttrList() {
      const { category1Id, category2Id, category3Id } = this
      const result = await this.$API.attr.getList(
        category1Id,
        category2Id,
        category3Id
      )
      if (result.code === 200 || result.code === 20000) {
        this.attributesList = result.data
      }
    },
    // 添加属性
    addAttributes() {
      this.isShowList = false
    },
    // 删除功能
    deleteAttr(row) {
      console.log(row)
    },
    // 取消添加属性值
    cancelAttributesValue() {
      this.isShowList = true
    },
    // 添加一个属性值
    addItemAttr(row) {
      this.attrFrom.attrValueList.push({
        isEdit: true,
        valueName: ''
      })
      this.$nextTick((index) => {
        this.$refs[this.attrFrom.attrValueList.length - 1].focus()
        console.log(this.$refs[this.attrFrom.attrValueList.length - 1], 'this.$refs[this.attrFrom.attrValueList.length - 1]')
      })
    },
    // iput失去焦点
    toLook(row) {
      console.log(row, 'row')
      const attrValuesRules = row.value
      if (attrValuesRules === '') {
        this.$message.error('input cannot be blank')
        row.value = ''
        return false
      }
      // 判断是否为已经存在的数据
      // const attrValuesListRules = this.attrFrom.attrValueList.filter((item) => {
      //   if (item !== row) {
      //     return item.valueName === row.valueName
      //   }
      // })
      // console.log(attrValuesListRules, 'attreho')
      // if (attrValuesListRules.length) {
      //   this.$message.error('attributes values already have')
      //   row.valueName = ''
      //   return false
      // }
      row.isEdit = false
    },
    // 点击span变成input
    toEdit(row, index) {
      console.log(row, index, 'row, index')
      // this.attrFrom.attrValueList.forEach((item) =>
      //   this.$set(item, 'isEdit', true)
      // ) // 这样添加isedit就是响应式
      // this.$nextTick(() => {
      //   this.$refs[index].focus()
      //   console.log(this.$refs[index], 'this.$refs[index]')
      // })
      // this.$set(row, 'isEdit', true)
      row.index = index
      this.attrFrom.attrValueList = this.attrFrom.attrValueList.map((item, index) => {
        console.log(index === row.index, '1')
        return ({
          ...item,
          isEdit: index === row.index
        })
      }
      )
    },
    handleClick(tab, event) {
      console.log(tab, event, 'event')
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
